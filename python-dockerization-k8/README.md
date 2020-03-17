
Network:

Containers in a Pod are accessible via "localhost", they use the same network namespace. For containers, the observable host name is a Pod’s name. Since containers share the same IP address and port space, you should use different ports in containers for incoming connections. Because of this, applications in a Pod must coordinate their usage of ports.


In the following example, we will create a multi-container Pod, where nginx in one container works as a reverse proxy for a simple web 

application running in the second container.

Step 1. Create a ConfigMap with nginx configuration file. Incoming HTTP requests to the port 80 will be forwarded to the port 2020 on 

localhost:

Step 2. Create a multi-container Pod with simple web app and nginx in separate containers. Note that for the Pod, we define only nginx port 

80. Port 2020 will not be accessible outside of the Pod.

Step 3. Expose the Pod using the NodePort service:

$ kubectl expose pod mc3 --type=NodePort --port=80

service "mc3" exposed

Step 4. Identify port on the node that is forwarded to the Pod:

$ kubectl describe service mc3

...

NodePort:		<unset>	31418/TCP

...

Now you can use your browser (or curl) to navigate to your node’s port to access the web application through reverse proxy.
