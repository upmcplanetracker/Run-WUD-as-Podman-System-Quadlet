WhatsUpDocker (WUD) Podman Quadlet
==================================

**Disclaimer:** I am not affiliated with, associated, authorized, endorsed by, or in any way officially connected with the WhatsUpDocker project.

This guide sets up **WUD** as a System Quadlet on Ubuntu 25.10 to monitor your other containers for updates and send notifications via SMTP.  You can set this to be either a Podman monitor or a Docker monitor.

**Socket Configuration:** By default, WUD looks for `docker.sock`. Since we are using Podman, this Quadlet maps the native `/run/podman/podman.sock` into the container's `/var/run/docker.sock` so WUD can communicate with the Podman engine.  To change thie back to a Docker monitor, see the .container file and comment out the `podman.sock` and uncomment the `docker.sock`.

System Information
------------------

*   **Tested Environment:** Ubuntu 25.10
*   **Podman Version:** 5.4.x
*   **Socket Path:** `/run/podman/podman.sock`

1\. The Quadlet File (wud.container)
------------------------------------

Save the `wud.container` file. Ensure you have enabled the Podman socket on your host system first (see Step 2B).

2\. Installation & Setup
------------------------

### Step A: Clean up existing containers

Stop current instance of WUD.

### Step B: Enable the Podman Socket

For WUD to work, the Podman system service must be active so the socket exists:

`sudo systemctl enable --now podman.socket`

You may skip this if using WUD to monitor Docker containers.

### Step C: Move and Edit

Move the file to the systemd directory and update your SMTP credentials and store paths:

`sudo mkdir -p /etc/containers/systemd`
`sudo mv wud.container /etc/containers/systemd/`
`sudo nano /etc/containers/systemd/wud.container` to edit the container file.

3\. Activation
--------------

Reload systemd and start WUD:

`sudo systemctl daemon-reload`
`sudo systemctl start wud`

4\. Post-Install Verification
-----------------------------

### Verify Socket Communication

Check the WUD logs to ensure it is successfully talking to the Podman socket:

`sudo journalctl -u wud -f`

You should see messages indicating that the Docker (Podman) watcher has started and is scanning containers.

### Access the Web UI

Open your browser and navigate to: `http://[Your-IP]:3050`

5\. Security Note
-----------------

This Quadlet runs as a system service. Mapping the Podman socket into a container gives that container significant control over your system's containers. Ensure your SMTP and Registry passwords in the `.container` file are kept secure.

_Note: As a system quadlet, WUD will start automatically on boot and begin monitoring your media stack immediately._
