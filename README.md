# eap-oracle-db

Example of adding adding and using and Oracle JDBC datasource to a Red Hat JBoss EAP 7.1.x container for Openshift.

This example adds an Oracle JDBC driver as a module into the EAP 7.1.x image during an Openshift based source to image (s2i) build.  A datasource is then created at deploy time that uses the Oracle JDBS driver.  There are a few mechanisms used that are provided as facilities in the EAP 7.1.x image for Openshift from Red Hat.

- A `.s2i` directory that includes an `environment` [file](https://github.com/travisrogers05/eap-oracle-db/blob/master/.s2i/environment) is provided in the project's source repository that sets `CUSTOM_INSTALL_DIRECTORIES=extensions`.  This is used by scripts provided in the EAP 7.1.x image to allow for customization to take place at a pods deploy time.
- An `extensions` [directory](https://github.com/travisrogers05/eap-oracle-db/tree/master/extensions) that contains 
  - the necessary [module directory structure and module.xml](https://github.com/travisrogers05/eap-oracle-db/tree/master/extensions/modules/com/oracle/main) file for the Oracle JDBC driver, as well as the Oracle JDBC driver file.
  - A `drivers.env` [file](https://github.com/travisrogers05/eap-oracle-db/blob/master/extensions/drivers.env) that contains driver specific details.  This example includes one driver, but multiple drivers could be included.  Refer to the [JBoss EAP for Openshift documentation](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.1/html-single/red_hat_jboss_enterprise_application_platform_for_openshift/#S2I-Artifacts) for further details about the expected contents of this file.
  - An `install.sh` [file](https://github.com/travisrogers05/eap-oracle-db/blob/master/extensions/install.sh) that is executed during the Openshift s2i build process.  This script takes care of installing the Oracle JDBC driver as a module into the EAP 7.1.x image.  (adding files to the image and updating the standalone-openshift.xml to include the driver config)
- A `configuration` [directory](https://github.com/travisrogers05/eap-oracle-db/blob/master/configuration) that contains
  - A `datasources.env` [file](https://github.com/travisrogers05/eap-oracle-db/blob/master/configuration/datasources.env) is included that provides all the specifics for the datasource.  These settings are incorporated into the EAP 7.1.x configuration at pod deploy time.  Multiple datasources can be provided, although this example uses only one.  Refer to the [JBoss EAP for Openshift documentation](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.1/html-single/red_hat_jboss_enterprise_application_platform_for_openshift/#S2I-Artifacts) for further details about the expected contents of this file.


## How it works

An Openshift build process clones this git repo into a build pod that performs a maven build of the example servlet.  The example servlet artifact and Oracle JDBC driver are copied into the image during the build.  The JBoss EAP standalone-openshift.xml that is provide in the JBoss EAP for Openshift image is updated to include the Oracle JDBC driver configuration.  The Openshift build process produces a container image to be used in application pods.

When the resulting container image is used to produce an application pod, the pod is configured at deploy time to include datasource settings provided by the `datasources.env` [file](https://github.com/travisrogers05/eap-oracle-db/blob/master/configuration/datasources.env).

