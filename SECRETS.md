1) From Mac terminal issue the following command:
    echo -n 'admin' | base64
   -or- for Windows, you can browse to base64encode.org and enter the text you want to encode.
   
3) Use the resulting base64 encoded username in your mongo-secret.yaml

4) You will also need to encode your password in base64:
   echo -n 'password' | base64

5) Use the resulting base64 encoded username and password in your mongo-secret.yaml. 

6) Add your mongo-secret.yaml to your kubernetes cluster manually through Rancher, or uncomment the code that references "mongo-secret".  Delete the mongo-secret.yaml from your repository.

7) Go in to Rancher to also add in the following code in the kubectl shell >_ (after you replace your docker username, password and email):

   "kubectl create secret docker-registry docker-creds --docker-username=roseaw --docker-password=P@ssw0rd! --docker-email=roseaw@miamioh.edu"

    "kubectl create secret generic mongodb-secret --from-literal=mongo-root-username=admin --from-literal=mongo-root-password=password"
