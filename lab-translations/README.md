# Intro on Translating labs



E.g instead of using Nano editor to replace the below h1 header, Add text like this, annd replace YOUR_NAME with your name

```
sudo nano /var/www/html/index.nginx-debian.html
```

The code version would look like, using regex expressions to match the above HTML page

```
sudo sed 's/<h1>.*<\/h1>/\n Hi from Peter/' /var/www/html/index.nginx-debian.html

```


## To ping an IP

We can use curl 

```bash
ping my-vm-1
```
Using Gcloud Shell

```
# Read the name of the IP address by executing  the following command
gcloud compute instances list
```


## More examples

In the Cloud Shell create an environment variable that contains the full email address of Username 2.

```
export USER2=[USERNAME_2_EMAIL]
```

Example (Do not copy):

```
export USER2=gcpstaginguser68_student@qwiklabs.net
```

In the Cloud Shell use sed to replace the placeholder in the file with the value of the environment variable.

```
sed -i "s/\[USERNAME_2_EMAIL\]/${USER2}/" username2-editor-binding.yaml
```

In the Cloud Shell, execute the following command to confirm that the correct change has been made:

```
cat username2-editor-binding.yaml
```

The subjects section should now look similar to the following.

Example (Do not copy):
```
subjects:
- kind: User
  name: gcpstaginguser68_student@qwiklabs.net
  apiGroup: rbac.authorization.k8s.io
```

You will apply this RoleBinding later.          Create a Dockerfile under the microservices-demo directory and add the configuration to the file. Copy the given codes from the lab page to the following snippet, and then run the commands in the Cloud Shell.
```
cd microservices-demo

cat > Dockerfile <<EOF
   
// COPY TO HERE
   
EOF
``` Build the image with the following command:

docker build -t microservices-demo:v0.0.1 .
Run docker images to look at the images you built.

Before clicking Check my progress in the lab page, don’t forget to run the following commands to execute the marking script:

cd ~/marking
./step1.shDownload and install skaffold:

curl -Lo skaffold https://storage.googleapis.com/skaffold/releases/v0.36.0/skaffold-linux-amd64 && chmod +x skaffold && sudo mv skaffold /usr/local/bin

Get the external IP of the application:

export EXTERNAL_IP=$(kubectl get service frontend-external | awk 'BEGIN { cnt=0; } { cnt+=1; if (cnt > 1) print $4; }')
Finally, confirm that the app is up and running:

curl -o /dev/null -s -w "%{http_code}\n"  http://$EXTERNAL_IP 