# CoreDNS Issues

Today, when pushing an update to the NWS API, our CD solution was unable to deploy the update. When I went into Rancher to see why, it was spitting out errors relating to SSL certificates. There were 2 errors: "EE Certificate key too weak" and ": x509: certificate signed by unknown authority."

To me, this seemed like it was an issue where somehow the CA certificates got deleted. The first thing I did was to go update them on the docker container that is running the cluster that Rancher is hosted on. While this did allow me to `curl https://google.com` from the docker container properly, it didn't do anything for any of the pods running within the cluster.

At this point, I got a shell into one of 
