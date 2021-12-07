# Bold BI on Google Kubernetes Engine

For fresh installation, continue with the following steps to deploy Bold BI On-Premise in Google Kubernetes Engine (GKE).

1. Download the following files for Bold BI deployment in GKE:

    * [namespace.yaml](https://raw.githubusercontent.com/boldbi/boldbi-kubernetes/v4.2.68/deploy/namespace.yaml)
    * [secrets.yaml](https://raw.githubusercontent.com/boldbi/boldbi-kubernetes/v4.2.68/deploy/secrets.yaml)
    * [log4net_config.yaml](https://raw.githubusercontent.com/boldbi/boldbi-kubernetes/v4.2.68/deploy/log4net_config.yaml)
    * [pvclaim_gke.yaml](https://raw.githubusercontent.com/boldbi/boldbi-kubernetes/v4.2.68/deploy/pvclaim_gke.yaml)
    * [deployment.yaml](https://raw.githubusercontent.com/boldbi/boldbi-kubernetes/v4.2.68/deploy/deployment.yaml)
    * [hpa_gke.yaml](https://raw.githubusercontent.com/boldbi/boldbi-kubernetes/v4.2.68/deploy/hpa_gke.yaml)
    * [service.yaml](https://raw.githubusercontent.com/boldbi/boldbi-kubernetes/v4.2.68/deploy/service.yaml)
    * [ingress.yaml](https://raw.githubusercontent.com/boldbi/boldbi-kubernetes/v4.2.68/deploy/ingress.yaml)

2. Create a Kubernetes cluster in Google Cloud Platform (GCP) to deploy Bold BI.

   https://console.cloud.google.com/kubernetes 

3. Create a Google filestore instance to store the shared folders for application usage.

   https://console.cloud.google.com/filestore 

4. Note the **File share name** and **IP address** after creating filestore instance.

![File Share details](images/gke_file_share_details.png)

5. Open **pvclaim_gke.yaml** file, downloaded in **Step 1**. Replace the **File share name** and **IP address** noted in above step to the `<file_share_name>` and `<file_share_ip_address>` places in the file. You can also change the storage size in the YAML file. Save the file once you replaced the file share name and file share IP address.

![PV Claim](images/gke_pvclaim.png)

6. Connect with your GKE cluster.

   https://cloud.google.com/kubernetes-engine/docs/quickstart 

7. After connecting with your cluster, deploy the latest Nginx ingress controller to your cluster using the following command.

```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.41.2/deploy/static/provider/cloud/deploy.yaml
```

8. Navigate to the folder where the deployment files were downloaded from **Step 1**.

9. Run the following command to create the namespace for deploying Bold BI.

```sh
kubectl apply -f namespace.yaml
```

10. Run the following command to create the secrets.

```sh
kubectl apply -f secrets.yaml
```

11. Run the following command to create the configmap.

```sh
kubectl apply -f log4net_config.yaml
```

12. If you have a DNS to map with the application, then you can continue with the following steps, else skip to **Step 15**. 

13. Open the **ingress.yaml** file. Uncomment the host value and replace your DNS hostname with `example.com` and save the file.

14. If you have the SSL certificate for your DNS and need to configure the site with your SSL certificate, follow the below step or you can skip to **Step 15**.

15. Run the following command to create a TLS secret with your SSL certificate.

```sh
kubectl create secret tls bold-tls -n bold-services --key <key-path> --cert <certificate-path>
```

16. Now, uncomment the `tls` section and replace your DNS hostname with `example.com` in ingress spec and save the file.

![ingress DNS](images/ingress_yaml.png)

17. Run the following command for applying the Bold BI ingress to get the IP address of Nginx ingress.

```sh
kubectl apply -f ingress.yaml
```

18.	Now, run the following command to get the ingress IP address,

```sh
kubectl get ingress -n bold-services
```
Repeat the above command till you get the IP address in ADDRESS tab as shown in the following image.
![Ingress Address](images/ingress_address.png) 

19.	Note the ingress IP address and map it with your DNS, if you have added the DNS in **ingress.yaml** file. If you do not have the DNS and want to use the application, then you can use the ingress IP address.

20. Open the **deployment.yaml** file from the downloaded files in **Step 1**. Replace your DNS or ingress IP address in `<application_base_url>` place.
    
    Ex:  `http://example.com`, `https://example.com`, `http://<ingress_ip_address>`

21. Read the optional client library license agreement from the following link.

    [Consent to deploy client libraries](../docs/consent-to-deploy-client-libraries.md)

22. Note the optional client libraries from the above link as comma separated names and replace it in `<comma_separated_library_names>` place. Save the file after the required values has been replaced.

![deployment.yaml](images/deployment_yaml.png) 

23. If you need to use **Bing Map** widget feature, enter value for `widget_bing_map_enable` environment variable as `true` and API key value for `widget_bing_map_api_key` in the **secrets.yaml** file downloaded in step 1.

24.	Now, run the following commands one by one:

```sh
kubectl apply -f pvclaim_gke.yaml
```

```sh
kubectl apply -f deployment.yaml
```

```sh
kubectl apply -f hpa_gke.yaml
```

```sh
kubectl apply -f service.yaml
```

25.	Wait for some time till the Bold BI On-Premise application deployed to your Google Kubernetes cluster.

26.	Use the following command to get the pods status.

```sh
kubectl get pods -n bold-services
```
![Pod status](images/pod_status.png) 

27. Wait till you see the applications in running state. Then, use your DNS or ingress IP address you got from **Step 18** to access the application in the browser.

28.	Configure the Bold BI On-Premise application startup to use the application. Please refer the following link for more details on configuring the application startup.
    
    https://help.boldbi.com/embedded-bi/application-startup
