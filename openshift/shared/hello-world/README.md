Hello World: Red Hat Data Grid for OpenShift
============================================
**Authors:** Galder Zamarreno, Don Naro  
**Technologies:** Infinispan, Red Hat Data Grid, Red Hat OpenShift  
**Summary:** Quickly verify Data Grid service deployments.  
**Target Product:** Data Grid for OpenShift  
**Product Versions:** RHDG 7.3 or later

About This Quickstart
---------------------
This quickstart provides a lightweight Java application that connects to Data Grid for OpenShift services and stores, retrieves, then verifies a sample key/value pair.

You can use this quickstart with both `cache-service` and `datagrid-service` deployments.

**Before You Begin:** Complete the steps in the [OpenShift Quickstart README](../../README.md) to set up an OpenShift cluster and create Data Grid for OpenShift services.

Configuring Authentication
--------------------------
The quickstart application must authenticate with Data Grid services.

Building the Hello World Quickstart
-----------------------------------

1. Create a new source build on OpenShift.
```bash
$ oc new-build fabric8/s2i-java:2.3~https://github.com/jeanNyil/jboss-jdg-quickstarts.git#test-dg-7.3 \
--name=hello-world \
--strategy=source \
--context-dir="/openshift/shared/hello-world"
```

2. Follow the build on Openshift
```bash
oc logs bc/hello-world
```

Running the Hello World Quickstart
----------------------------------
1. Invoke cache operations with the quickstart application.
```bash
$ oc run hello-world \
--image=`oc get is hello-world -o jsonpath="{.status.dockerImageRepository}"` \
--replicas=1 \
--restart=OnFailure \
--env APP_NAME=${appName} \
--env SVC_DNS_NAME=${appName} \
--env JAVA_OPTIONS=-ea \
--env DG_USERNAME=${datagrid_username} \
--env DG_PASSWORD=${datagrid_password}
```
  Where:
  -  `${appName}` matches the application name that you specified when you created `cache-service` or `datagrid-service`.
  - `${datagrid_username}` is the username to connect to the datagrid service
  - `${datagrid_username}` is the password to connect to the datagrid service

2. Verify the cache operations completed successfully.
```
$ oc logs hello-world-${id} --tail=50
--- Connect to datagrid-service ---
...
--- Store key='hello'/value='world' pair ---
...
--- Retrieve key='hello' ---
--- Value is 'world' ---
```
  Where `${id}` is the unique ID for the pod. **TIP:** Use `oc get pods` to find the pod name.

  The preceding log messages show the Hello World quickstart connected to the Data Grid for OpenShift service and stored a `hello/world` key/value pair. The quickstart application also performs an assertion to ensure that the returned value is `world`.

  You've successfully completed this tutorial!

  Do one of the following:

  - Delete quickstart resources and continue using the project with RHDG for OpenShift.

    ```bash
    $ oc delete all -lrun=hello-world || true
    $ oc delete all -lbuild=hello-world || true
    ```

  - Delete the project to remove all resources, for example:

    ```bash
    $ oc delete project my_project
    ```
