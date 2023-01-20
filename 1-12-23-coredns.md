# CoreDNS Issues

##### The Issue
Today, when pushing an update to the NWS API, our CD solution was unable to deploy the update. When I went into Rancher to see why, it was spitting out errors relating to SSL certificates. There were 2 errors: "EE Certificate key too weak" and ": x509: certificate signed by unknown authority."

##### The Diagnosis
To me, this seemed like it was an issue where somehow the CA certificates got deleted. The first thing I did was to go update them on the docker container that is running the cluster that Rancher is hosted on. While this did allow me to `curl https://google.com` from the docker container properly, it didn't do anything for any of the pods running within the cluster.

At this point, I got a shell into one of the k8s pods and attempted to `curl https://google.com`, and got the same errors as the docker container! At this point I began to suspect that this might have nothing to do with CA Certificates, and rather some DNS resolution error. Using `openssl`, I was able to view the certificate that was being sent to me from 'google'. And to my (lack of) surprise, the CA on the certificate was the manufacturer of the router on the network. I also used `curl`'s `-v` (verbose) flag to see that `curl` was resolving google.com to the IP of my router.

#### The Fix
Since other devices on the network that the k8s cluster was being hosted on didn't have any issues (nor did the server itself), it must have been an issue with whatever DNS provider I had the cluster configured to use. I was able to update this DNS provideo from the previous one to Cloudflare's DNS (1.1.1.1/1.0.0.1). This resolved the issue.
