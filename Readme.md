# Setup apps using Virtualbox, Packer, Kubernetes K3S and Tunneling using Cloudflare


### Prereq
1. Install VirtualBox
2. Install Packer
3. Access into Cloudflare
4. Domain on Cloudflare


### Run Kubernetes using Vagrant

1. Clone The Repository `https://github.com/ryoguritno/vagrant-k3s-cloudflare.git`

2. Change Dir into `vagrantfile`

3. Check the file `Vagrantfile` for the `box/image` files if any update relase `https://portal.cloud.hashicorp.com/vagrant/discover?query=debian12`. 

4. Run vagrant
    ```bash
    vagarant up
    ```

5. Wait after the box is ready to use, and try to SSH
    ```bash
    vagrant ssh
    ```

6. Login Tunnel Cloudflare
    ```bash
    # cloudflared version
    # cloudflared tunnel login
    ```
    It will open your browser and ask you for authorization.

7. Create Tunnel Cloudflare
    ```bash
    # cloudflared tunnel create k3s-tunnel
    ```

8. Upload the Tunnel Credentials to K3S Cluster. 
    ```bash
    # kubectl create namespace cloudflare
    # kubectl create secret generic tunnel-credentials --from-file=credentials.json=/root/.cloudflared/ec559bfb-9084-4497-94d9-043542a5421e.json -n cloudflare
    ```

9. Create DNS Record to associating the tunnel
    ```bash
    # cloudflared tunnel route dns k3s-tunnel test.example.com
    ```

10. Deploy the Apss
    ```bash
    # cd ../manifest
    # kubectl create namespace app-hello
    # kubectl -n app-hello apply -f .
    # kubectl -n app-hello get all
    ```

11. Deploy the Cloudflared
    ```bash
    # cd ../cloudflare
    # kubectl -n cloudflare apply -f .
    # kubectl -n cloudflare get all
    ```

Replace the ingress section in ConfigMap with your desired hostname and the service endpoint to your Kubernetes service.

As it is a list, you can actually add multiple hostname records to expose multiple services. Don’t forget to add their corresponding DNS records.

Now you should be able to connect your Kubernetes Service through Internet by using the hostname. As Cloudflare tunnel supports both HTTP and HTTPS, you can set a force-HTTPS on Cloudflare portal if you want a HTTPS only traffic.