# JVM

As part of the JMX implementation, please refer to the following and update accordingly for the "web" and "server"

Folder "jmx", contains the following files, please use the same from the shared path  jmx 

1.tomcat :
 
  a. apache-tomcat-9.0.46.tar.gz
 
  b.config.yaml
 
  c.jmx_prometheus_javaagent-0.16.1.jar

2.web :
 
  a.Dockerfile_Admin_java17
 
  b.setenv.sh

3.Update the "deployment.yaml" with the below
    
    annotations:
        prometheus.io/port: '8871'
        prometheus.io/scrape: 'true'
    
    - name: JAVA_AGENT_PORT
              value: '8871'

4.Update the "service.yaml" with the below
 
  - port: 8871
    targetPort: 8871
    name: prom

Note : FYR, please find folder "manifests-ref" in the shared folder path             

INSTALL PROMETHEUS AND GRAFANA USING HELM CHART

First install the prometheus using helm chart,

1. helm repo add prometheus-repo https://prometheus-community.github.io/helm-charts
2. helm repo list
3. helm install [RELEASE_NAME] prometheus-repo/prometheus

ex: helm install prom prometheus-repo/prometheus

It will deploy multiple prometheus resources and You will notice that the Pod is pending from the replica set simply because the SCC constraint is not met, fix it by,

*oc adm policy add-scc-to-user anyuid -z service-account -n namespace
 
  Apply above command to related service accounts, The service account is using the chart’s release name.


  example:

  1.oc adm policy add-scc-to-user anyuid -z prome-kube-state-metrics -n monitoring 
  
  2.oc adm policy add-scc-to-user anyuid -z prome-prometheus-alertmanager -n monitoring
  
  3.oc adm policy add-scc-to-user anyuid -z prome-prometheus-pushgateway -n monitoring                 
  
  4. oc adm policy add-scc-to-user anyuid -z prome-prometheus-server -n monitoring

Another prometheus component deployed as a Daemonset, it will required privilised SCC to run pod
  
  5. oc adm policy add-scc-to-user privileged -z prome-prometheus-node-exporter -n monitoring


For grafana installaton use the helm commands,

1. helm repo add grafana https://grafana.github.io/helm-charts

2. helm repo list

3. helm install [RELEASE_NAME] grafana/grafana

ex: helm install graf grafana/grafana

You will notice that the Pod is pending from the replica set simply because the SCC constraint is not met, fix it by,

*oc adm policy add-scc-to-user anyuid -z service-account -n namespace
 
Apply above command to related service accounts, The service account is using the chart’s release name.

1. oc adm policy add-scc-to-user anyuid -z graf-grafana -n monitoring  

2. oc adm policy add-scc-to-user anyuid -z graf-grafana-test -n monitoring 


![Deploy](https://user-images.githubusercontent.com/105658922/168635125-54db2cd9-38a3-4e2a-af74-8fb7ca845c69.png)



Create rouets to access both prometheus and grafana extrenally by using services prome-prometheus-server and  graf-grafana at port 9090 and 3000.

Now access prometheus endpoint and go to the status --> targets , you can find like below screenshot.

![prometheus](https://user-images.githubusercontent.com/105658922/168636579-c62bf6c3-d24c-4f09-921b-b34899aca957.png)

Once you have all prerequisites configured and deployed, the JVM metrics will be collected and stored in the prometheus. We use Grafana to create a graphical dashboard and measure the performance as shown below.

Click on configuration --> Data sources --> Add datasource and choose Prometheus

![grafana1](https://user-images.githubusercontent.com/105658922/168638159-09382e04-dd6c-498a-a0c4-7d7fc50c9154.png)

After that save & test it will add datasource suucessfully.

Now we will create a New Dashboard for visualizing the metric data for go to + --> import and need to upload the JSON file , it is in above folder

On successfull additon you can see data in grafana.

![grafana2](https://user-images.githubusercontent.com/105658922/168639775-2dfb123c-3cd1-4ad1-a803-3150242e2a1a.png)

![grafana3](https://user-images.githubusercontent.com/105658922/168639812-7d6170b7-1b93-4f3c-ae18-9478dfcf4ed5.png)

Now we can create our own dashboard with the cluster metrics and user workload metrics.







  
  
  

  
  



              
              
              
