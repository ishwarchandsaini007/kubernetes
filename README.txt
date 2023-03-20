1. To run the whole project with one command:- " kubectl apply -k ./ " The above command will execute the kustomization.yaml file.
2. I have created a NFS with two directories, one is mounted with the MySQL and other is mounted with wordpress with the help of PVC.
3. If you want the change the NFS, then change the server ip and also create the directories before deploying the project. Access Mode for the NFS must be ReadWriteMany.
4. The mysql-secret.yaml file carries the password of MySQL.
5. For PhpMyAdmin :- We need to mention the PhpMyAdmin Url in the phpmyadmin deployment file and then setup the ingress file.
    nginx.ingress.kubernetes.io/rewrite-target: "/$2"
    path: /phpmyadmin(/|$)(.*)

Useful Commands:- 
kubectl get pods
kubectl get pods -o wide
kubectl get nodes
kubectl get services
kubectl get pvc
kubectl get ingress
kubectl exec -it httpd-8987f559f-f26mq bash
kubectl apply -f deployment.yml
kubectl delete -f deployment.yml
kubectl describe pod wordpress-5c466bfbf7-fb2wj
kubectl logs wordpress-5c466bfbf7-fb2wj

Note:- 
1. If Wordpress installation page will not show then you need to comment this line in the ingress file. 
#nginx.ingress.kubernetes.io/rewrite-target: "/$2" 
After installing the wordpress uncomment the line from the ingress otherwise phpmyadmin will not work.

2. Wordpress internal links will only work if the path and the annotations must be same as mentioned.

  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: "/$1"

path: /?(.*

3. PhpMyAdmin will only work if the path and the annotations must be same as mentioned.


  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: "/$2"

path: /phpmyadmin(/|$)(.*)

+++++++++++++
+ IMPORTANT +
+++++++++++++

4. If you are using both wordpess and the phpmyadmin then use the following annotations and the path.

  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: "/$1$2"

path :-

path: /?(.*
path: /phpmyadmin(/|$)(.*)


5. If phpmyadmin is not working then check the URL in the phpmyadmin deployment file. If you running the phpmyadmin directly on the loadbalancer ip then simply comment the URL lines from the deployment file. If you are using the domain name then mention the domain name in the deployment file. If you are using the phpmyadmin on the sub path (example.com/phpmyadmin) then mention the domain with the sub path in the URL section of the deployment file.

Wordpress:- http://kube.linuxhunter.in
PhpMyAdmin:- http://kube.linuxhunter.in/phpmyadmin (will not work, / is require at the end of phpmyadmin)
PhpMyAdmin:- http://kube.linuxhunter.in/phpmyadmin/ (work)


FOR SSL:-
1. Objtain SSL certificate
2. Directly Apply Certificates and update the ingress:- kubectl create secret tls linuxhunter-tls --key private.key --cert certificate.crt
   If you do not want to apply the certificate directly then Convert the SSL into the yaml file:- kubectl create secret tls linuxhunter-tls --key private.key --cert certificate.crt --dry-run=client --output=yaml
3. If you have created the yaml file for the ssl then add the name of the ssl yaml in the kustomization.yaml 
4. Modify the ingress file and apply the ingress
