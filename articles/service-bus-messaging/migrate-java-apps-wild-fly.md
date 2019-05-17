---
title: Áttelepíteni a Java Enterprise Edition alkalmazások az Azure-bA |} A Microsoft Docs
description: Ismerje meg, körülbelül egyik módja a Java Enterprise Edition (EE) alkalmazások áttelepíthetők az Azure-bA
services: service-bus-messaging
documentationcenter: ''
author: selvasingh
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: asirveda
ms.openlocfilehash: 2392d0ef9d6257b93b76f13a5354eeba57d78ddd
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800280"
---
# <a name="migrate-java-enterprise-edition-ee-apps-to-azure"></a>A Java enterprise edition (EE) alkalmazások migrálása az Azure-bA
Ez a cikk végigvezeti a folyamat áttelepítése meglévő Java EE-alapú számítási feladatok Azure-bA:
 
- A Java-nagyvállalati alkalmazásokat (üzenet-központú nagyvállalati bab) [Linuxon futó Azure App Service](../app-service/containers/app-service-linux-intro.md)
- Alkalmazás Azure Service Bus üzenetkezelő alrendszer
- Használja a Web Sockets interaktív Java számítási feladatokhoz 

## <a name="in-this-article"></a>Ebben a cikkben

* [Mi az áttelepítést végzi a felhőbe](#what-you-will-migrate-to-cloud)
* [Előfeltételek](#prerequisites)
* [Első lépések](#get-started)
    * [A Git-tárház és előkészítő klónozása](#clone-and-prepare-the-git-repository)
* [A minta archívum létrehozása](#build-the-sample-archive)
* [Konzolalkalmazás - üzenetek küldése és fogadása Service Bus Java Message Service (JMS) használatával](#build-and-run-console-app-to-send-and-receive-messages)
    * [Létrehozása és konfigurálása az Azure Service Bus](#create-and-configure-azure-service-bus)
    * [Hozhat létre, és a Konzolalkalmazás futtatása](#build-and-run-the-console-app)
* [Egy üzenet-központú nagyvállalati bean migrálása az Azure-bA](#migrate-a-message-driven-enterprise-bean-to-azure)
    * [Környezet előkészítése](#prepare-environment)
    * [Linux-alapú App Service-alkalmazás üzembe helyezése](#deploy-an-app-to-app-service-linux)
    * [Konfigurálja a JMS erőforrás-adaptert (JMS RA)](#configure-the-jms-resource-adapter-jms-ra)
    * [Megtudhatja, hogyan WildFly konfigurálása](#understand-how-to-configure-wildfly)
    * [Indítási és a bináris összetevők feltöltése az alkalmazás FTP használatával](#upload-startup-and-binary-artifacts-to-app-through-ftp)
        * [FTP telepítési hitelesítő adatainak lekérése](#get-ftp-deployment-credentials)
        * [Indítási és a bináris összetevők feltöltése az alkalmazás FTP használatával](#upload-startup-and-binary-artifacts-to-app-through-ftp)
    * [A JBoss/WildFly indítási parancsfájlt és a CLI-parancsok használatával JMS RA konfigurálása tesztelése](#test-the-jbosswildfly-startup-script-and-cli-commands-to-configure-jms-ra)
        * [A startup.sh parancsprogram teszteléséhez](#test-the-startupsh-script)
    * [A távoli WildFly alkalmazás kiszolgáló újraindítása](#restart-the-remote-wildfly-app-server)
    * [Stream naplók WildFly/JBoss egy fejlesztési gépen](#stream-wildflyjboss-logs-to-a-dev-machine)
    * [Nyissa meg az üzenet-központú nagyvállalati bean az Azure-ban](#open-the-message-driven-enterprise-bean-on-azure)
    * [További információ](#additional-information)
* [A Java Enterprise-alkalmazást, amely használja a websockets protokoll áttelepítése](#migrate-java-ee-app-that-uses-websockets)
    * [Linux-alapú App Service-alkalmazás üzembe helyezése](#deploy-an-app-to-app-service-linux)
    * [Nyissa meg a migrált alkalmazást az App Service Linux rendszeren](#open-the-migrated-app-on-app-service-linux)
* [Következő lépések](#next-steps)

## <a name="what-you-will-migrate-to-cloud"></a>Mi az áttelepítést végzi a felhőbe
Az Azure-bA át fogjuk WildFly/JBoss mintaalkalmazások. Használja ezeket az alkalmazásokat:

- Java Standard Edition (SE) 8
- Java Enterprise Edition (EE) 7
- [Java-specifikáció kérelem (JSR) 343 Java használó üzenetkezelési szolgáltatás (JMS) 2.0](https://jcp.org/en/jsr/detail?id=343)
- [Java specifikáció kérelem (JSR) 356 Java API websocket](https://jcp.org/en/jsr/detail?id=356)

Az áttelepítés után az Azure Service Bus használatával alkalmazásokat fogja futtatni.

## <a name="prerequisites"></a>Előfeltételek
Egy Java webes alkalmazás üzembe helyezése az Azure-ba, az Azure-előfizetés szükséges. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/en-us/free/).

Emellett szüksége lesz a következő előfeltételek vonatkoznak:

- [Azure CLI](/cli/azure/get-started-with-azure-cli) 
- [Java 8](https://www.azul.com/downloads/azure-only/zulu/) 
- [Maven 3](http://maven.apache.org/) 
- [Git](https://github.com/)

## <a name="get-started"></a>Bevezetés
Kezdés előzmények és az egyes lépéseinek befejezéséhez, vagy, elkerülheti, hogy már ismeri a az alapszintű lépéseket. Mindkét esetben fognak megjelenni működő kóddal.

### <a name="clone-and-prepare-the-git-repository"></a>Klónozza, és a Git-tárház előkészítése 

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/migrate-java-ee-app-to-azure-2
cd migrate-Java-EE-app-to-azure-2
yes | cp -rf .prep/* .
```

## <a name="build-the-sample-archive"></a>A minta archívum létrehozása
A minta archívumot a Maven használatával szeretné felépíteni. Ez a lépés néhány percet vesz igénybe.

```bash
cd quickstart
mvn clean install
```

Itt látható a minta kimenete: 

```bash
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Build Order:
[INFO] 
[INFO] Quickstart: Parent
[INFO] Quickstart: app-client
[INFO] Quickstart: app-client - ejb
[INFO] Quickstart: app-client - client-simple
[INFO] Quickstart: app-client - ear
...
...
[INFO] Installing /Users/selvasingh/migrate-java-ee-app-to-azure-2/quickstart/wsba-participant-completion-simple/target/wsba-participant-completion-simple-sources.jar to /Users/selvasingh/.m2/repository/org/wildfly/quickstarts/wsba-participant-completion-simple/14.0.1.Final/wsba-participant-completion-simple-14.0.1.Final-sources.jar
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary:
[INFO] 
[INFO] Quickstart: Parent ................................. SUCCESS [  1.697 s]
[INFO] Quickstart: app-client ............................. SUCCESS [  0.015 s]
[INFO] Quickstart: app-client - ejb ....................... SUCCESS [  2.177 s]
[INFO] Quickstart: app-client - client-simple ............. SUCCESS [  0.646 s]
[INFO] Quickstart: app-client - ear ....................... SUCCESS [  0.723 s]
[INFO] Quickstart: batch-processing ....................... SUCCESS [  1.736 s]
[INFO] Quickstart: bean-validation ........................ SUCCESS [  0.482 s]
[INFO] Quickstart: bean-validation-custom-constraint ...... SUCCESS [  0.295 s]
[INFO] Quickstart: bmt .................................... SUCCESS [  0.291 s]
[INFO] Quickstart: cmt .................................... SUCCESS [  1.642 s]
[INFO] Quickstart: contacts-jquerymobile .................. SUCCESS [  1.351 s]
[INFO] Quickstart: deltaspike-authorization ............... SUCCESS [  0.265 s]
[INFO] Quickstart: deltaspike-beanbuilder ................. SUCCESS [  0.387 s]
[INFO] Quickstart: deltaspike-projectstage ................ SUCCESS [  0.150 s]
[INFO] Quickstart: ejb-asynchronous ....................... SUCCESS [  0.011 s]
[INFO] Quickstart: ejb-asynchronous - ejb ................. SUCCESS [  0.161 s]
[INFO] Quickstart: ejb-asynchronous - client .............. SUCCESS [  0.157 s]
[INFO] Quickstart: ejb-in-ear ............................. SUCCESS [  0.010 s]
[INFO] Quickstart: ejb-in-ear - ejb ....................... SUCCESS [  0.133 s]
[INFO] Quickstart: ejb-in-ear - web ....................... SUCCESS [  0.143 s]
[INFO] Quickstart: ejb-in-ear - ear ....................... SUCCESS [  0.216 s]
[INFO] Quickstart: ejb-in-war ............................. SUCCESS [  0.224 s]
[INFO] Quickstart: ejb-multi-server ....................... SUCCESS [  0.014 s]
[INFO] Quickstart: ejb-multi-server - app-one ............. SUCCESS [  0.012 s]
[INFO] Quickstart: ejb-multi-server - app-one - ejb ....... SUCCESS [  0.148 s]
[INFO] Quickstart: ejb-multi-server - app-one - ear ....... SUCCESS [  0.030 s]
[INFO] Quickstart: ejb-multi-server - app-two ............. SUCCESS [  0.014 s]
[INFO] Quickstart: ejb-multi-server - app-two - ejb ....... SUCCESS [  0.207 s]
[INFO] Quickstart: ejb-multi-server - app-two - ear ....... SUCCESS [  0.031 s]
[INFO] Quickstart: ejb-multi-server - app-main ............ SUCCESS [  0.015 s]
[INFO] Quickstart: ejb-multi-server - app-main - ejb ...... SUCCESS [  0.222 s]
[INFO] Quickstart: ejb-multi-server - app-main - web ...... SUCCESS [  0.252 s]
[INFO] Quickstart: ejb-multi-server - app-main - ear ...... SUCCESS [  0.078 s]
[INFO] Quickstart: ejb-multi-server - app-web ............. SUCCESS [  0.343 s]
[INFO] Quickstart: ejb-multi-server - client .............. SUCCESS [  0.277 s]
[INFO] Quickstart: ejb-security ........................... SUCCESS [  0.302 s]
[INFO] Quickstart: ejb-security-context-propagation ....... SUCCESS [  0.227 s]
[INFO] Quickstart: ejb-security-jaas ...................... SUCCESS [  0.368 s]
[INFO] Quickstart: ejb-security-programmatic-auth ......... SUCCESS [  0.155 s]
[INFO] Quickstart: ejb-throws-exception ................... SUCCESS [  0.009 s]
[INFO] Quickstart: ejb-throws-exception - ejb-api ......... SUCCESS [  0.115 s]
[INFO] Quickstart: ejb-throws-exception - ejb ............. SUCCESS [  0.108 s]
[INFO] Quickstart: ejb-throws-exception - web ............. SUCCESS [  0.145 s]
[INFO] Quickstart: ejb-throws-exception - ear ............. SUCCESS [  0.030 s]
[INFO] Quickstart: ejb-timer .............................. SUCCESS [  0.136 s]
[INFO] Quickstart: greeter ................................ SUCCESS [  0.187 s]
[INFO] Quickstart: HA Singleton Deployment ................ SUCCESS [  0.114 s]
[INFO] Quickstart: HA Singleton Service (parent) .......... SUCCESS [  0.038 s]
[INFO] Quickstart: HA Singleton Service - primary-only .... SUCCESS [  0.160 s]
[INFO] Quickstart: HA Singleton Service - with backups .... SUCCESS [  0.133 s]
[INFO] Quickstart: helloworld ............................. SUCCESS [  0.139 s]
[INFO] Quickstart: Hello World ClassFileTransformers ...... SUCCESS [  0.251 s]
[INFO] Quickstart: helloworld-html5 ....................... SUCCESS [  0.127 s]
[INFO] Quickstart: helloworld-jms ......................... SUCCESS [ 24.354 s]
[INFO] Quickstart: helloworld-mbean ....................... SUCCESS [  0.017 s]
[INFO] Quickstart: helloworld-mbean - helloworld-mbean-webapp SUCCESS [  0.271 s]
[INFO] Quickstart: helloworld-mbean - helloworld-mbean-service SUCCESS [  1.730 s]
[INFO] Quickstart: helloworld-mdb ......................... SUCCESS [  1.260 s]
[INFO] Quickstart: helloworld-mdb-propertysubstitution .... SUCCESS [  0.147 s]
[INFO] Quickstart: helloworld-mutual-ssl .................. SUCCESS [  0.343 s]
[INFO] Quickstart: helloworld-mutual-ssl-secured .......... SUCCESS [  0.331 s]
[INFO] Quickstart: helloworld-rf .......................... SUCCESS [  0.546 s]
[INFO] Quickstart: helloworld-rs .......................... SUCCESS [  0.106 s]
[INFO] Quickstart: helloworld-singleton ................... SUCCESS [  0.121 s]
[INFO] Quickstart: helloworld-ssl ......................... SUCCESS [  0.109 s]
[INFO] Quickstart: helloworld-ws .......................... SUCCESS [  0.827 s]
[INFO] Quickstart: hibernate4 ............................. SUCCESS [  0.481 s]
[INFO] Quickstart: hibernate .............................. SUCCESS [  0.229 s]
[INFO] Quickstart: http-custom-mechanism .................. SUCCESS [  0.012 s]
[INFO] Quickstart: http-custom-mechanism - webapp ......... SUCCESS [  0.196 s]
[INFO] Quickstart: inter-app .............................. SUCCESS [  0.009 s]
[INFO] Quickstart: inter-app - shared ..................... SUCCESS [  0.094 s]
[INFO] Quickstart: inter-app - appA ....................... SUCCESS [  0.111 s]
[INFO] Quickstart: inter-app - appB ....................... SUCCESS [  0.123 s]
[INFO] Quickstart: jaxrs-client ........................... SUCCESS [  0.286 s]
[INFO] Quickstart: jaxrs-jwt .............................. SUCCESS [  0.011 s]
[INFO] Quickstart: jaxrs-jwt - client ..................... SUCCESS [  0.135 s]
[INFO] Quickstart: jaxrs-jwt - service .................... SUCCESS [  0.513 s]
[INFO] Quickstart: jaxws-addressing ....................... SUCCESS [  0.010 s]
[INFO] Quickstart: jaxws-addressing - service ............. SUCCESS [  0.115 s]
[INFO] Quickstart: jaxws-addressing - client .............. SUCCESS [  0.693 s]
[INFO] Quickstart: jaxws-ejb .............................. SUCCESS [  0.009 s]
[INFO] Quickstart: jaxws-ejb - service .................... SUCCESS [  0.133 s]
[INFO] Quickstart: jaxws-ejb - client ..................... SUCCESS [  0.147 s]
[INFO] Quickstart: jaxws-pojo ............................. SUCCESS [  0.010 s]
[INFO] Quickstart: jaxws-pojo - service ................... SUCCESS [  0.125 s]
[INFO] Quickstart: jaxws-pojo - client .................... SUCCESS [  0.191 s]
[INFO] Quickstart: jaxws-retail ........................... SUCCESS [  0.011 s]
[INFO] Quickstart: jaxws-retail - service ................. SUCCESS [  2.587 s]
[INFO] Quickstart: jaxws-retail - client .................. SUCCESS [  0.153 s]
[INFO] Quickstart: jsonp .................................. SUCCESS [  0.136 s]
[INFO] Quickstart: kitchensink ............................ SUCCESS [  0.758 s]
[INFO] Quickstart: kitchensink-angularjs .................. SUCCESS [  0.806 s]
[INFO] Quickstart: kitchensink-ear ........................ SUCCESS [  0.009 s]
[INFO] Quickstart: kitchensink-ear - ejb .................. SUCCESS [  0.181 s]
[INFO] Quickstart: kitchensink-ear - web .................. SUCCESS [  0.166 s]
[INFO] Quickstart: kitchensink-ear - ear .................. SUCCESS [  0.032 s]
[INFO] Quickstart: kitchensink-jsp ........................ SUCCESS [  0.669 s]
[INFO] Quickstart: kitchensink-ml ......................... SUCCESS [  0.901 s]
[INFO] Quickstart: Kitchensink with Undertow.JS and AngularJS SUCCESS [  0.230 s]
[INFO] Quickstart: Kitchensink with Undertow.JS and Mustach SUCCESS [  0.031 s]
[INFO] Quickstart: logging ................................ SUCCESS [  0.092 s]
[INFO] Quickstart: logging-tools .......................... SUCCESS [  0.778 s]
[INFO] Quickstart: mail ................................... SUCCESS [  0.163 s]
[INFO] Quickstart: managed-executor-service ............... SUCCESS [  0.201 s]
[INFO] Quickstart: messaging-clustering-singleton ......... SUCCESS [  0.114 s]
[INFO] Quickstart: numberguess ............................ SUCCESS [  0.131 s]
[INFO] Quickstart: payment-cdi-event ...................... SUCCESS [  0.162 s]
[INFO] Quickstart: resteasy-jaxrs-client .................. SUCCESS [  0.094 s]
[INFO] Quickstart: security-domain-to-domain .............. SUCCESS [  0.007 s]
[INFO] Quickstart: security-domain-to-domain - ejb ........ SUCCESS [  0.088 s]
[INFO] Quickstart: security-domain-to-domain - web ........ SUCCESS [  0.122 s]
[INFO] Quickstart: security-domain-to-domain - ear ........ SUCCESS [  0.025 s]
[INFO] Quickstart: servlet-async .......................... SUCCESS [  0.133 s]
[INFO] Quickstart: servlet-filterlistener ................. SUCCESS [  0.125 s]
[INFO] Quickstart: servlet-security ....................... SUCCESS [  0.125 s]
[INFO] Quickstart: shopping-cart .......................... SUCCESS [  0.008 s]
[INFO] Quickstart: shopping-cart - server ................. SUCCESS [  0.095 s]
[INFO] Quickstart: shopping-cart - client ................. SUCCESS [  0.090 s]
[INFO] Quickstart: spring-greeter ......................... SUCCESS [  0.313 s]
[INFO] Quickstart: spring-kitchensink-basic ............... SUCCESS [  3.836 s]
[INFO] Quickstart: spring-kitchensink-springmvctest ....... SUCCESS [  4.767 s]
[INFO] Quickstart: spring-resteasy ........................ SUCCESS [  0.316 s]
[INFO] Quickstart: tasks-jsf .............................. SUCCESS [  0.826 s]
[INFO] Quickstart: tasks-rs ............................... SUCCESS [  0.194 s]
[INFO] Quickstart: temperature-converter .................. SUCCESS [  0.116 s]
[INFO] Quickstart: thread-racing .......................... SUCCESS [  0.305 s]
[INFO] Quickstart: websocket-client ....................... SUCCESS [  0.242 s]
[INFO] Quickstart: websocket-endpoint ..................... SUCCESS [  0.215 s]
[INFO] Quickstart: websocket-hello ........................ SUCCESS [  0.110 s]
[INFO] Quickstart: wicket-ear ............................. SUCCESS [  0.009 s]
[INFO] Quickstart: wicket-ear - ejb ....................... SUCCESS [  0.093 s]
[INFO] Quickstart: wicket-ear - war ....................... SUCCESS [  0.303 s]
[INFO] Quickstart: wicket-ear - ear ....................... SUCCESS [  0.149 s]
[INFO] Quickstart: wicket-war ............................. SUCCESS [  0.260 s]
[INFO] Quickstart: xml-jaxp ............................... SUCCESS [  0.175 s]
[INFO] Quickstart: jts .................................... SUCCESS [  0.008 s]
[INFO] Quickstart: jts - application-component-2 .......... SUCCESS [  0.146 s]
[INFO] Quickstart: jts - application-component-1 .......... SUCCESS [  0.114 s]
[INFO] Quickstart: ejb-remote ............................. SUCCESS [  0.011 s]
[INFO] Quickstart: ejb-remote - server-side ............... SUCCESS [  0.115 s]
[INFO] Quickstart: ejb-remote - client .................... SUCCESS [  0.109 s]
[INFO] Quickstart: jta-crash-rec .......................... SUCCESS [  0.163 s]
[INFO] Quickstart: wsat-simple ............................ SUCCESS [  0.240 s]
[INFO] Quickstart: wsba-coordinator-completion-simple ..... SUCCESS [  0.232 s]
[INFO] Quickstart: wsba-participant-completion-simple ..... SUCCESS [  0.225 s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 01:16 min
[INFO] Finished at: 2019-02-09T11:01:57-08:00
[INFO] Final Memory: 203M/660M
[INFO] ------------------------------------------------------------------------

```

## <a name="build-and-run-console-app-to-send-and-receive-messages"></a>Hozhat létre és futtassa a konzolalkalmazást az üzenetek küldése és fogadása

### <a name="create-and-configure-azure-service-bus"></a>Létrehozása és konfigurálása az Azure Service Bus

1. Jelentkezzen be az Azure CLI használatával.

    ```bash
    az login
    ```
2. Környezeti változókat a titkos kulcsok kötelező futásidőben, különösen az Azure erőforráscsoport-nevet, és az Azure Service Bus-adatok. Is exportálhatja őket a helyi környezetben, tegyük fel, hogy a megadott Bash rendszerhéj parancsfájl-sablon használatával.

    ```bash
    cd helloworld-jms
    mkdir .scripts
    cp set-env-variables-template.sh .scripts/set-env-variables.sh
    ```
3. Módosítsa `.scripts/set-env-variables.sh` és állítsa be az Azure erőforráscsoport-nevet, és az Azure Service Bus-adatok. Ezután állítson be környezeti változókat:
 
    ```bash
    source .scripts/set-env-variables.sh
    ```
4. Az Azure Service Bus hozza létre:

    ```bash
    az group create --name ${RESOURCEGROUP_NAME} \
        --location ${REGION}
    
    az servicebus namespace create \
        --name  ${DEFAULT_SBNAMESPACE} \
        --resource-group ${RESOURCEGROUP_NAME}
    
    az servicebus queue create \
        --name ${SB_QUEUE} \
        --namespace-name ${DEFAULT_SBNAMESPACE} \
        --resource-group ${RESOURCEGROUP_NAME}
    
    az servicebus queue authorization-rule create \
        --name ${SB_SAS_POLICY} \
        --namespace-name ${DEFAULT_SBNAMESPACE} \
        --queue-name ${SB_QUEUE} \
        --resource-group ${RESOURCEGROUP_NAME} \
        --rights Listen Send
    
    az servicebus queue authorization-rule keys list \
        --name ${SB_SAS_POLICY} \
        --namespace-name ${DEFAULT_SBNAMESPACE} \
        --queue-name ${SB_QUEUE} \
        --resource-group ${RESOURCEGROUP_NAME}
                                                
    ```

    Az értékek jelennek meg a kulcsokhoz, kimásolhatja a <b>elsődleges kulcs</b> értéket. Nyissa meg a.scripts/set-env-variables.sh fájlt, és a primaryKey változó SB_SAS_KEY értéket állítja be.
5. Exportálja a környezeti változókat:

    ```bash
    source .scripts/set-env-variables.sh
    ```

### <a name="build-and-run-the-console-app"></a>Hozhat létre, és a Konzolalkalmazás futtatása

A Maven használata a Konzolalkalmazás, és futtassa azt.

```bash
mvn clean compile exec:java -Dexec.cleanupDaemonThreads=false

[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building Quickstart: helloworld-jms 14.0.1.Final
[INFO] ------------------------------------------------------------------------
...
...
[INFO] --- exec-maven-plugin:1.6.0:java (default-cli) @ helloworld-jms ---
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
Feb 10, 2019 9:28:31 AM org.jboss.as.quickstarts.jms.HelloWorldJMSClient main
INFO: Attempting to acquire connection factory "SBCF"
Feb 10, 2019 9:28:31 AM org.jboss.as.quickstarts.jms.HelloWorldJMSClient main
INFO: Found connection factory "SBCF" in JNDI
Feb 10, 2019 9:28:31 AM org.jboss.as.quickstarts.jms.HelloWorldJMSClient main
INFO: Attempting to acquire destination "QUEUE"
Feb 10, 2019 9:28:31 AM org.jboss.as.quickstarts.jms.HelloWorldJMSClient main
INFO: Found destination "QUEUE" in JNDI
Feb 10, 2019 9:28:37 AM org.jboss.as.quickstarts.jms.HelloWorldJMSClient main
INFO: Sending 1 messages with content: Hello, World!
Feb 10, 2019 9:28:37 AM org.jboss.as.quickstarts.jms.HelloWorldJMSClient main
INFO: Received message with content Hello, World!
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 8.763 s
[INFO] Finished at: 2019-02-10T09:28:38-08:00
[INFO] Final Memory: 33M/401M
[INFO] ------------------------------------------------------------------------
```
## <a name="migrate-a-message-driven-enterprise-bean-to-azure"></a>Egy üzenet-központú nagyvállalati bean migrálása az Azure-bA

### <a name="prepare-environment"></a>Környezet előkészítése

1. MDB módosítsa a könyvtárat:

    ```bash
    cd ../helloworld-mdb
    ```
2. Környezeti változókat a titkos kulcsok kötelező futásidőben, különösen az Azure erőforráscsoport-nevet, és az Azure Service Bus-adatok. Is exportálhatja őket a helyi környezetben, tegyük fel, hogy a megadott Bash rendszerhéj parancsfájl-sablon használatával.

    ```bash
    cp set-env-variables-template.sh .scripts/set-env-variables.sh
    ```
3. Módosítsa `.scripts/set-env-variables.sh` és állítsa be az Azure erőforráscsoport-nevet, az Azure Service Bus-adatok és a App Service Linux adataihoz. Ezeket az értékeket a legtöbb másolhatja az előző gyakorlat során szkriptjéről `../helloworld-jms/.scripts/set-env-variables.sh` , és adja hozzá az App Service Linux adatait. 
4. Most állítsa be a környezeti változókat:
 
    ```bash
    source .scripts/set-env-variables.sh
    ```

### <a name="deploy-an-app-to-azure-app-service-on-linux"></a>Linuxon futó Azure App Service-alkalmazás üzembe helyezése

1. Adjon hozzá [Azure App Service-ben készült maven bővítmény](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) konfigurációt a POM.xml és üzembe helyezése az üzenet adatvezérelt bean WildFly a linuxon futó App Service-ben:

    ```xml    
    <plugins> 
    
        <!--*************************************************-->
        <!-- Deploy to WildFly in App Service on Linux          -->
        <!--*************************************************-->
           
        <plugin>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-webapp-maven-plugin</artifactId>
            <version>1.5.3</version>
            <configuration>
        
                <!-- Web App information -->
               <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
               <appServicePlanName>${WEBAPP_PLAN_NAME}</appServicePlanName>
                <appName>${WEBAPP_NAME}</appName>
                <region>${REGION}</region>
        
                <!-- Java Runtime Stack for Web App on Linux-->
                <linuxRuntime>wildfly 14-jre8</linuxRuntime>
                
                <appSettings>
                    <property>
                        <name>DEFAULT_SBNAMESPACE</name>
                        <value>${DEFAULT_SBNAMESPACE}</value>
                    </property>
                    <property>
                        <name>SB_SAS_POLICY</name>
                        <value>${SB_SAS_POLICY}</value>
                    </property>
                    <property>
                        <name>SB_SAS_KEY</name>
                        <value>${SB_SAS_KEY}</value>
                    </property>
                    <property>
                        <name>PROVIDER_URL</name>
                        <value>${PROVIDER_URL}</value>
                    </property>
                    <property>
                        <name>SB_QUEUE</name>
                        <value>${SB_QUEUE}</value>
                    </property>
                </appSettings>
            </configuration>
        </plugin>
        ...
    </plugins>
    ```
2. Az üzenet adatvezérelt bean buildu:

    ```bash
    mvn package
    [INFO] Scanning for projects...
    [INFO] 
    [INFO] ------------------------------------------------------------------------
    [INFO] Building Quickstart: helloworld-mdb 14.0.1.Final
    [INFO] ------------------------------------------------------------------------
    [INFO] 
    ...
    ...
    [INFO] --- maven-war-plugin:3.2.2:war (default-war) @ helloworld-mdb ---
    [INFO] Packaging webapp
    [INFO] Assembling webapp [helloworld-mdb] in [/Users/selvasingh/migrate-java-ee-app-to-azure-2/quickstart/helloworld-mdb/target/helloworld-mdb]
    [INFO] Processing war project
    [INFO] Copying webapp resources [/Users/selvasingh/migrate-java-ee-app-to-azure-2/quickstart/helloworld-mdb/src/main/webapp]
    [INFO] Webapp assembled in [84 msecs]
    [INFO] Building war: /Users/selvasingh/migrate-java-ee-app-to-azure-2/quickstart/helloworld-mdb/target/helloworld-mdb.war
    [INFO] 
    [INFO] --- maven-source-plugin:3.0.1:jar-no-fork (attach-sources) @ helloworld-mdb ---
    [INFO] Building jar: /Users/selvasingh/migrate-java-ee-app-to-azure-2/quickstart/helloworld-mdb/target/helloworld-mdb-sources.jar
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 3.908 s
    [INFO] Finished at: 2019-02-10T11:37:03-08:00
    [INFO] Final Memory: 34M/413M
    [INFO] ------------------------------------------------------------------------
    ```
3. Az üzenet adatvezérelt bean üzembe a linuxon futó App Service-ben
    
    ```bash
    mvn azure-webapp:deploy

    [INFO] 
    [INFO] ------------------------------------------------------------------------
    [INFO] Building Quickstart: helloworld-mdb 14.0.1.Final
    [INFO] ------------------------------------------------------------------------
    [INFO] 
    [INFO] --- azure-webapp-maven-plugin:1.5.3:deploy (default-cli) @ helloworld-mdb ---
    ...
    ...
    [INFO] Authenticate with Azure CLI 2.0
    [INFO] Target Web App doesn't exist. Creating a new one...
    [INFO] Creating App Service Plan 'helloworld-mdb-appservice-plan'...
    [INFO] Successfully created App Service Plan.
    [INFO] Successfully created Web App.
    [INFO] Trying to deploy artifact to helloworld-mdb...
    [INFO] Deploying the war file...
    [INFO] Successfully deployed the artifact to https://helloworld-mdb.azurewebsites.net
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 02:15 min
    [INFO] Finished at: 2019-02-10T11:41:06-08:00
    [INFO] Final Memory: 55M/362M
    [INFO] ------------------------------------------------------------------------

    ```

### <a name="configure-the-jms-resource-adapter-jms-ra"></a>Konfigurálja a JMS erőforrás-adaptert (JMS RA)
Konfigurálja a JMS-RA, amely lehetővé teszi a Java EJBs konfigurálása a távoli JMS kapcsolat és az üzenetsor néhány lépésből áll. Azure Service Bus, mutasson a távoli beállítás használata a [Apache Qpid JMS szolgáltató](https://qpid.apache.org/components/jms/index.html) az AMQP protokollt. 

#### <a name="understand-how-to-configure-wildfly"></a>Megtudhatja, hogyan WildFly konfigurálása

Az App Service-ben egy alkalmazás server mindegyik példányán az állapot nélküli. Ezért minden példány egy, az alkalmazás által igényelt Wildfly konfigurálását támogató indításkor kell konfigurálni. Konfigurálhat egy Bash-szkript, amely meghívja ezt indítási megadásával indításkor [JBoss/WildFly CLI-parancsok](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) adatforrások, üzenetkezelési szolgáltatók és egyéb függőségek beállításához. Hozzon létre egy startup.sh parancsprogramot, és elhelyezheti a `/home` könyvtárat a webalkalmazás. A szkriptet fogja végrehajtani:
 
1. Egy általános JMS WildFly szolgáltató modul telepítése és konfigurálása a JMS RA. A `module.xml` az általános JMS szolgáltató modul ismerteti:

    ```xml
    <module xmlns="urn:jboss:module:1.1" name="org.jboss.genericjms.provider"> 
      <resources> 
          <resource-root path="proton-j-<version>.jar"/> 
          <resource-root path="qpid-jms-client-<version>jar"/>
          <resource-root path="slf4j-log4j12-<version>jar"/>
          <resource-root path="slf4j-api-<version>jar"/>
          <resource-root path="log4j-<version>jar"/>      
          <resource-root path="netty-buffer-<version>.jar" />
          <resource-root path="netty-codec-<version>.jar" />
          <resource-root path="netty-codec-http-<version>.jar" />
          <resource-root path="netty-common-<version>.jar" />
          <resource-root path="netty-handler-<version>.jar" />
          <resource-root path="netty-resolver-<version>.jar" />
          <resource-root path="netty-transport-<version>.jar" />
          <resource-root path="netty-transport-native-epoll-<version>-linux-x86_64.jar" />
          <resource-root path="netty-transport-native-kqueue-<version>-osx-x86_64.jar" />
          <resource-root path="netty-transport-native-unix-common-<version>.jar" /> 
          <resource-root path="qpid-jms-discovery-<version>jar" />
      </resources> 
    
       <dependencies> 
          <module name="javax.api"/> 
          <module name="javax.jms.api"/> 
      </dependencies> 
    </module>
    ```
2. Hozzon létre egy `jndi.properties` fájlt:

    ```bash
    echo "Generating jndi.properties file in /home/site/deployments/tools directory"
    echo "connectionfactory.SBF=amqps://${DEFAULT_SBNAMESPACE}.servicebus.windows.net?amqp.idleTimeout=120000&jms.username=${SB_SAS_POLICY}&jms.password=${SB_SAS_KEY}" > /home/site/deployments/tools/jndi.properties
    echo "queue.jmstestqueue=${SB_QUEUE}" >> /home/site/deployments/tools/jndi.properties
    echo "====== contents of /home/site/deployments/tools/jndi.properties ======"
    cat /home/site/deployments/tools/jndi.properties
    echo "====== EOF /home/site/deployments/tools/jndi.properties ======"
    ```
3. Másolja az összes bináris JAR-fájlok kivételével, a modul fájl és generált jndi.properties WildFly config helyre:

    ```bash
    mkdir /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider
    mkdir /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider/main
    cp  /home/site/deployments/tools/*.jar /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider/main/
    cp /home/site/deployments/tools/module.xml /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider/main/
    cp /home/site/deployments/tools/jndi.properties /opt/jboss/wildfly/standalone/configuration/
    ```
4. Szolgáltató általános JMS modul hozzáadása:

    ```bash
    /subsystem=ee:list-add(name=global-modules, value={"name" => "org.jboss.genericjms.provider", "slot" =>"main"}
    /subsystem=naming/binding="java:global/remoteJMS":add(binding-type=external-context,module=org.jboss.genericjms.provider,class=javax.naming.InitialContext,environment=[java.naming.factory.initial=org.apache.qpid.jms.jndi.JmsInitialContextFactory,org.jboss.as.naming.lookup.by.string=true,java.naming.provider.url=/home/site/deployments/tools/jndi.properties])
    /subsystem=resource-adapters/resource-adapter=generic-ra:add(module=org.jboss.genericjms,transaction-support=XATransaction)
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd:add(class-name=org.jboss.resource.adapter.jms.JmsManagedConnectionFactory, jndi-name=java:/jms/SBF)
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd/config-properties=ConnectionFactory:add(value=SBF)
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd/config-properties=JndiParameters:add(value="java.naming.factory.initial=org.apache.qpid.jms.jndi.JmsInitialContextFactory;java.naming.provider.url=/home/site/deployments/tools/jndi.properties")
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd:write-attribute(name=security-application,value=true)
    /subsystem=ejb3:write-attribute(name=default-resource-adapter-name, value=generic-ra)
    ```
5. Lehet, hogy egy kiszolgáló töltse be újra a módosítások érvénybe lépéséhez szükséges:

    ```bash
    reload --use-current-server-config=true
    ```

Ezeknek a JBoss CLI-parancsok, WildFly/JBoss általános JMS szolgáltató modulleírást (`module.xml`), és a JAR-fájlok kivételével érhetők el a [quickstart/helloworld-mdb/.scripts](https://github.com/Azure-Samples/migrate-Java-EE-app-to-azure-2/tree/master/quickstart/helloworldmdb/.scripts) 

Emellett közvetlenül letöltheti a `Qpid` és `Proton-j` származó kódtárakat [Apache Qpid JMS szolgáltató](https://qpid.apache.org/components/jms/index.html) az AMQP protokollt.

#### <a name="upload-startup-and-binary-artifacts-to-app-through-ftp"></a>Indítási és a bináris összetevők feltöltése az alkalmazás FTP használatával

##### <a name="get-ftp-deployment-credentials"></a>FTP-s üzembe helyezés hitelesítő adatainak lekérése
Azure CLI használatával az FTP telepítési hitelesítő adatainak lekérése:

```bash
az webapp deployment list-publishing-profiles -g ${RESOURCEGROUP_NAME} -n ${WEBAPP_NAME}
...

Here is the sample output: 
...
{
   ...
   ...
    "profileName": "helloworld-mdb - FTP",
    "publishMethod": "FTP",
    "publishUrl": "ftp://<FTP host name>.ftp.azurewebsites.windows.net/site/wwwroot",
    "userName": "helloworld-mdb\\$helloworld-mdb",
    "userPWD": ================ MASKED ======================,
    "webSystem": "WebSites"
}
   
```

FTP-állomás neve, például: Store `<FTP host name>.ftp.azurewebsites.windows.net`, felhasználónév és a felhasználó jelszavának `.scripts/set-env-variables.sh` fájlt.

##### <a name="upload-artifacts-to-app-through-ftp"></a>Összetevők feltöltése az alkalmazás FTP használatával

Nyissa meg az összetevők feltöltése a Linuxon futó App Service egy FTP-kapcsolat:

1. Váltson a `.scripts` mappát. 

    ```bash
    cd .scripts
    ```
2. Nyissa meg az FTP-kapcsolat
    
    ```bash
    ftp
    ftp> open waws-prod-mwh-007.ftp.azurewebsites.windows.net
    Trying 52.183.36.81...
    Connected to waws-prod-mwh-007.drip.azurewebsites.windows.net.
    220 Microsoft FTP Service
    Name (waws-prod-mwh-007.ftp.azurewebsites.windows.net:selvasingh): helloworld-mdb\\$helloworld-mdb
    331 Password required
    Password: 
    230 User logged in.
    Remote system type is Windows_NT.
    ftp> ascii
    200 Type set to A.
    ftp> passive
    Passive mode: off; fallback to active mode: off.
    ```
2. Töltse fel a startup.sh. 

    ```bash
    ftp> put startup.sh
    local: startup.sh remote: startup.sh
    200 EPRT command successful.
    125 Data connection already open; Transfer starting.
    100% |*********************************************|  1291      211.78 KiB/s    --:-- ETA
    226 Transfer complete.
    1291 bytes sent in 00:00 (42.12 KiB/s)
    ```
3. Váltson a-eszközök könyvtárát.

    ```bash
    ftp> cd site/deployments/tools
    250 CWD command successful.
    ```
4. Töltse fel commands.cli és module.xml.

    ```bash
    ftp> put commands.cli
    local: commands.cli remote: commands.cli
    200 EPRT command successful.
    125 Data connection already open; Transfer starting.
    100% |*********************************************|  1477      242.49 KiB/s    --:-- ETA
    226 Transfer complete.
    1477 bytes sent in 00:00 (48.30 KiB/s)
    ftp> put module.xml
    local: module.xml remote: module.xml
    200 EPRT command successful.
    125 Data connection already open; Transfer starting.
    100% |*********************************************|  1280      206.06 KiB/s    --:-- ETA
    226 Transfer complete.
    1280 bytes sent in 00:00 (33.16 KiB/s)
    ```
5. Töltse fel a JAR-fájlok kivételével.

    ```bash
    ftp> binary
    200 Type set to I.
    ftp> mput *.jar
    mput log4j-1.2.17.jar [anpqy?]? y
    229 Entering Extended Passive Mode (|||10103|)
    125 Data connection already open; Transfer starting.
    100% |*********************************************|   478 KiB    1.73 MiB/s    00:00 ETA
    226 Transfer complete.
    489884 bytes sent in 00:00 (1.40 MiB/s)
    mput netty-buffer-4.1.32.Final.jar [anpqy?]? y
    229 Entering Extended Passive Mode (|||10105|)
    125 Data connection already open; Transfer starting.
    100% |*********************************************|   271 KiB  790.62 KiB/s    00:00 ETA
    226 Transfer complete.
    277778 bytes sent in 00:00 (612.65 KiB/s)
    mput netty-codec-4.1.32.Final.jar [anpqy?]? y
    229 Entering Extended Passive Mode (|||10101|)
    125 Data connection already open; Transfer starting.
    100% |*********************************************|   309 KiB    1.42 MiB/s    00:00 ETA
    226 Transfer complete.
    316671 bytes sent in 00:00 (1.20 MiB/s)
    mput netty-codec-http-4.1.32.Final.jar [anpqy?]? y
    229 Entering Extended Passive Mode (|||10106|)
    125 Data connection already open; Transfer starting.
    100% |*********************************************|   550 KiB    1.88 MiB/s    00:00 ETA
    226 Transfer complete.
    563215 bytes sent in 00:00 (1.55 MiB/s)
    mput netty-common-4.1.32.Final.jar [anpqy?]? y
    229 Entering Extended Passive Mode (|||10104|)
    125 Data connection already open; Transfer starting.
    100% |*********************************************|   573 KiB    2.03 MiB/s    00:00 ETA
    226 Transfer complete.
    586829 bytes sent in 00:00 (1.65 MiB/s)
    mput netty-handler-4.1.32.Final.jar [anpqy?]? y
    229 Entering Extended Passive Mode (|||10108|)
    125 Data connection already open; Transfer starting.
    100% |*********************************************|   410 KiB    1.72 MiB/s    00:00 ETA
    226 Transfer complete.
    420485 bytes sent in 00:00 (1.36 MiB/s)
    mput netty-resolver-4.1.32.Final.jar [anpqy?]? y
    229 Entering Extended Passive Mode (|||10107|)
    125 Data connection already open; Transfer starting.
    100% |*********************************************| 32800        5.49 MiB/s    00:00 ETA
    226 Transfer complete.
    32800 bytes sent in 00:00 (325.36 KiB/s)
    mput netty-transport-4.1.32.Final.jar [anpqy?]? y
    229 Entering Extended Passive Mode (|||10109|)
    125 Data connection already open; Transfer starting.
    100% |*********************************************|   452 KiB    1.69 MiB/s    00:00 ETA
    226 Transfer complete.
    463581 bytes sent in 00:00 (1.40 MiB/s)
    mput netty-transport-native-epoll-4.1.32.Final-linux-x86_64.jar [anpqy?]? y
    229 Entering Extended Passive Mode (|||10110|)
    125 Data connection already open; Transfer starting.
    100% |*********************************************|   137 KiB    1.85 MiB/s    00:00 ETA
    226 Transfer complete.
    141017 bytes sent in 00:00 (521.44 KiB/s)
    mput netty-transport-native-kqueue-4.1.32.Final-osx-x86_64.jar [anpqy?]? y
    229 Entering Extended Passive Mode (|||10111|)
    125 Data connection already open; Transfer starting.
    100% |*********************************************|   107 KiB   18.22 MiB/s    00:00 ETA
    226 Transfer complete.
    109800 bytes sent in 00:00 (143.52 KiB/s)
    mput netty-transport-native-unix-common-4.1.32.Final.jar [anpqy?]? y
    229 Entering Extended Passive Mode (|||10112|)
    125 Data connection already open; Transfer starting.
    100% |*********************************************| 33470        5.47 MiB/s    00:00 ETA
    226 Transfer complete.
    33470 bytes sent in 00:00 (366.26 KiB/s)
    mput proton-j-0.31.0.jar [anpqy?]? y
    229 Entering Extended Passive Mode (|||10114|)
    125 Data connection already open; Transfer starting.
    100% |*********************************************|   719 KiB    1.93 MiB/s    00:00 ETA
    226 Transfer complete.
    736444 bytes sent in 00:00 (1.67 MiB/s)
    mput qpid-jms-client-0.40.0.jar [anpqy?]? y
    229 Entering Extended Passive Mode (|||10113|)
    125 Data connection already open; Transfer starting.
    100% |*********************************************|   729 KiB    1.89 MiB/s    00:00 ETA
    226 Transfer complete.
    747044 bytes sent in 00:00 (1.63 MiB/s)
    mput qpid-jms-discovery-0.40.0.jar [anpqy?]? y
    229 Entering Extended Passive Mode (|||10115|)
    125 Data connection already open; Transfer starting.
    100% |*********************************************| 40531       21.01 MiB/s    00:00 ETA
    226 Transfer complete.
    40531 bytes sent in 00:00 (360.08 KiB/s)
    mput slf4j-api-1.7.25.jar [anpqy?]? y
    229 Entering Extended Passive Mode (|||10116|)
    125 Data connection already open; Transfer starting.
    100% |*********************************************| 41203       59.08 MiB/s    00:00 ETA
    226 Transfer complete.
    41203 bytes sent in 00:00 (338.90 KiB/s)
    mput slf4j-log4j12-1.7.25.jar [anpqy?]? y
    229 Entering Extended Passive Mode (|||10118|)
    125 Data connection already open; Transfer starting.
    100% |*********************************************| 12244        2.10 MiB/s    00:00 ETA
    226 Transfer complete.
    12244 bytes sent in 00:00 (169.29 KiB/s)
    ```

#### <a name="test-the-jbosswildfly-startup-script-and-cli-commands-to-configure-jms-ra"></a>A JBoss/WildFly indítási parancsfájlt és a CLI-parancsok használatával JMS RA konfigurálása tesztelése

A Bash-szkript az adatforrás konfigurálása az App Service Linux által futtatásával tesztelheti [nyissa meg az SSH-kapcsolatot a fejlesztői gépről](../app-service/containers/app-service-linux-ssh-support.md#open-ssh-session-from-remote-shell):

1. Az első terminálablakban futtassa a következő parancsot a távoli kapcsolat létrehozása:

    ```bash
    az webapp remote-connection create --resource-group ${RESOURCEGROUP_NAME} --name ${WEBAPP_NAME} &
    [1] 63235
    bash-3.2$ Auto-selecting port: 65428
    SSH is available { username: root, password: Docker! }
    Start your favorite client and connect to port 65428
    Websocket tracing disabled, use --verbose flag to enable
    Successfully connected to local server..
    ```
2. A második terminálablakban futtassa a következő parancsot: 

    ```bash
    ssh root@localhost -p 65428
    The authenticity of host '[localhost]:65428 ([127.0.0.1]:65428)' can't be established.
    ECDSA key fingerprint is SHA256:u/VkSFAFjoO9EkBT4zl1pNoWAzWAUdUeRjaHnsXNXlM.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '[localhost]:65428' (ECDSA) to the list of known hosts.
    root@localhost's password: 
      _____                               
      /  _  \ __________ _________   ____  
     /  /_\  \___   /  |  \_  __ \_/ __ \ 
    /    |    \/    /|  |  /|  | \/\  ___/ 
    \____|__  /_____ \____/ |__|    \___  >
            \/      \/                  \/ 
    A P P   S E R V I C E   O N   L I N U X
    
    Documentation: http://aka.ms/webapp-linux
    
    54cfe2dfa970:/home# ls -al
    total 12
    drwxrwxrwx    2 nobody   nobody        4096 Feb 11 17:54 .
    drwxr-xr-x   49 root     root          4096 Feb 10 19:40 ..
    drwxrwxrwx    2 nobody   nobody           0 Feb 10 19:40 .mono
    drwxrwxrwx    2 nobody   nobody           0 Feb 10 19:41 LogFiles
    drwxrwxrwx    2 nobody   nobody           0 Feb 10 19:40 d43fc68fefbe78c2a087a46f
    drwxrwxrwx    2 nobody   nobody           0 Feb 10 19:41 site
    -rwxrwxrwx    1 nobody   nobody        1291 Feb 11 17:46 startup.sh
    54cfe2dfa970:/home# 
    ```
3. Nyisson meg egy VI ablakot startup.sh szerkesztése

    ```bash
    c315a18b39d2:/home# vi startup.sh    
    ```
    
    Itt látható a minta kimenete a vi ablakban: 

    ```bash
    echo "Generating jndi.properties file in /home/site/deployments/tools directory"^M
    echo "connectionfactory.SBF=amqps://${DEFAULT_SBNAMESPACE}.servicebus.windows.net?amqp.idleTimeout=120000&jms.username=${SB_SAS_POLICY}&jms.password=${SB_SAS_KEY}" > /home/site/deployments/tools/jndi.properties^M
    echo "queue.jmstestqueue=${SB_QUEUE}" >> /home/site/deployments/tools/jndi.properties^M
    echo "====== contents of /home/site/deployments/tools/jndi.properties ======"^M
    cat /home/site/deployments/tools/jndi.properties^M
    echo "====== EOF /home/site/deployments/tools/jndi.properties ======"^M
    mkdir /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider^M
    mkdir /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider/main^M
    cp  /home/site/deployments/tools/*.jar /opt/jboss/wildfly/modules/system/layers/base/org/jboss/gene
    cp /home/site/deployments/tools/module.xml /opt/jboss/wildfly/modules/system/layers/base/org/jboss/
    cp /home/site/deployments/tools/jndi.properties /opt/jboss/wildfly/standalone/configuration/^M
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/home/site/deployments/tools/commands.cli^M
    ```
    Távolítsa el azokat ' ^ M "záró karakterek, és mentse a fájlt. Távolítsa el a záró karakterek alternatív módja van. Lásd: ([Ez a cikk](http://marcelog.github.io/articles/mac_newline_to_unix_eol.html)).

##### <a name="test-the-startupsh-script"></a>A startup.sh parancsprogram teszteléséhez

Az SSH ablakban hajtsa végre `startup.sh`:

```bash
54cfe2dfa970:/home# source startup.sh
Generating jndi.properties file in /home/site/deployments/tools directory
====== contents of /home/site/deployments/tools/jndi.properties ======
connectionfactory.SBF=amqps://jmsservice}.servicebus.windows.net?amqp.idleTimeout=120000&jms.username=ListenAndSend&jms.password=[MASKED]
queue.jmstestqueue=
====== EOF /home/site/deployments/tools/jndi.properties ======
Picked up _JAVA_OPTIONS: -Djava.net.preferIPv4Stack=true
{"outcome" => "success"}
{"outcome" => "success"}
{"outcome" => "success"}
{"outcome" => "success"}
{"outcome" => "success"}
{"outcome" => "success"}
{
    "outcome" => "success",
    "response-headers" => {
        "operation-requires-reload" => true,
        "process-state" => "reload-required"
    }
}
{
    "outcome" => "success",
    "response-headers" => {"process-state" => "reload-required"}
}
```
#### <a name="restart-the-remote-wildfly-app-server"></a>A távoli WildFly alkalmazás kiszolgáló újraindítása
     
 Azure CLI használatával indítsa újra a távoli WildFly alkalmazás kiszolgálót:
    
 ```bash
 az webapp stop -g ${RESOURCEGROUP_NAME} -n ${WEBAPP_NAME}
 az webapp start -g ${RESOURCEGROUP_NAME} -n ${WEBAPP_NAME}
 ```

#### <a name="stream-wildflyjboss-logs-to-a-dev-machine"></a>Stream naplók WildFly/JBoss egy fejlesztési gépen

1. Adja meg az üzembe helyezett Java-webalkalmazás naplóit az App Service Linux:

    ```bash
    az webapp log config --name ${WEBAPP_NAME} \
     --resource-group ${RESOURCEGROUP_NAME} \
      --web-server-logging filesystem
    ```
2. A helyi gépről nyissa meg a Java webes alkalmazás távoli naplófolyam:

    ```bash
    az webapp log tail --name ${WEBAPP_NAME} \
     --resource-group ${RESOURCEGROUP_NAME}
    ```

### <a name="open-the-message-driven-enterprise-bean-on-azure"></a>Nyissa meg az üzenet-központú nagyvállalati bean az Azure-ban

1. Nyissa meg a webalkalmazást az App Service Linux rendszeren:

    ```bash
    https://helloworld-mdb.azurewebsites.net
    ```

    ![Hello World MDB kép](./media/migrate-java-apps-wild-fly/helloworld-mdb.png)
2. Az App Service Linux log streamjét láthatja:

    ```bash
    2019-02-13T03:05:53,821 INFO  [org.apache.qpid.jms.sasl.SaslMechanismFinder] (AmqpProvider :(2):[amqps://jmsservice.servicebus.windows.net:-1]) Best match for SASL auth was: SASL-PLAIN
    2019-02-13T03:05:53,828 INFO  [org.apache.qpid.jms.JmsConnection] (AmqpProvider :(2):[amqps://jmsservice.servicebus.windows.net:-1]) Connection ID:48eae295-9d89-4aa6-85e8-f26a9b43147e:1 connected to remote Broker: amqps://jmsservice.servicebus.windows.net
    2019-02-13T03:05:53.822173661Z 03:05:53,821 INFO  [org.apache.qpid.jms.sasl.SaslMechanismFinder] (AmqpProvider :(2):[amqps://jmsservice.servicebus.windows.net:-1]) Best match for SASL auth was: SASL-PLAIN
    2019-02-13T03:05:53.830453730Z 03:05:53,828 INFO  [org.apache.qpid.jms.JmsConnection] (AmqpProvider :(2):[amqps://jmsservice.servicebus.windows.net:-1]) Connection ID:48eae295-9d89-4aa6-85e8-f26a9b43147e:1 connected to remote Broker: amqps://jmsservice.servicebus.windows.net
    ...
    ...
    2019-02-13T03:05:53.931890422Z 03:05:53,930 INFO  [class org.jboss.as.quickstarts.mdb.HelloWorldQueueMDB] (default-threads - 1) Received Message from queue: This is message 1
    2019-02-13T03:05:53.958784514Z 03:05:53,957 INFO  [class org.jboss.as.quickstarts.mdb.HelloWorldQueueMDB] (default-threads - 2) Received Message from queue: This is message 2
    2019-02-13T03:05:53.977067441Z 03:05:53,976 INFO  [class org.jboss.as.quickstarts.mdb.HelloWorldQueueMDB] (default-threads - 3) Received Message from queue: This is message 3
    2019-02-13T03:05:53.995098869Z 03:05:53,994 INFO  [class org.jboss.as.quickstarts.mdb.HelloWorldQueueMDB] (default-threads - 17) Received Message from queue: This is message 4
    2019-02-13T03:05:54.014198793Z 03:05:54,013 INFO  [class org.jboss.as.quickstarts.mdb.HelloWorldQueueMDB] (default-threads - 18) Received Message from queue: This is message 5
    ```
3. Az SSH-kapcsolatot az alkalmazás az App Service Linux rendszeren való megnyitásával, és a következő parancsok végrehajtása JMS konfigurációs ellenőrizheti:

    ```bash
    0cb1ce311a79:/home# /opt/jboss/wildfly/bin/jboss-cli.sh -c
    Picked up _JAVA_OPTIONS: -Djava.net.preferIPv4Stack=true
    [standalone@localhost:9990 /] :read-config-as-xml > config.xml
    [standalone@localhost:9990 /] quit
    ```

4. SBF konfigurációs meglétének ellenőrzése

    ```bash
    0cb1ce311a79:/home# cat config.xml | grep "SBF"
                        <connection-definition class-name=\"org.jboss.resource.adapter.jms.JmsManagedConnectionFactory\" jndi-name=\"java:/jms/SBF\" pool-name=\"sbf-cd\">
    ```
### <a name="additional-information"></a>További információ

További információkért tekintse meg: 
 
 - [A JBoss/WildFly általános JMS RA Adapter telepítése](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.1/html/configuring_messaging/resource_adapters#deploy_configure_generic_jms_resource_adapter)
 - [A JBoss/WildFly parancssori útmutatója](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface)
 - [Nyissa meg az SSH-munkamenetet a fejlesztői gépről App Service Linux](../app-service/containers/app-service-linux-ssh-support.md#open-ssh-session-from-remote-shell)

## <a name="migrate-java-ee-app-that-uses-websockets"></a>A websockets protokoll használó Java EE-alapú alkalmazások migrálása

1. Módosítsa a könyvtárat a WebSocket alkalmazás könyvtár:

    ```bash
    cd ../websocket-hello
    ```
2. Környezeti változókat a titkos kulcsok kötelező futásidőben, különösen az Azure erőforráscsoport-nevet, és App Service Linux adatait. Is exportálhatja őket a helyi környezetben, tegyük fel, hogy a megadott Bash rendszerhéj parancsfájl-sablon használatával.

    ```bash
    mkdir .scripts
    cp set-env-variables-template.sh .scripts/set-env-variables.sh
    ```
3. Módosítsa `.scripts/set-env-variables.sh` és állítsa be az Azure erőforráscsoport-nevet, és az App Service Linux adatai. Ezután állítson be környezeti változókat:
 
    ```bash
    source .scripts/set-env-variables.sh
    ```

### <a name="deploy-an-app-to-app-service-linux"></a>Linux-alapú App Service-alkalmazás üzembe helyezése

1. Adjon hozzá [Azure App Service-ben készült maven bővítmény](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) konfigurációt a POM.xml és üzembe helyezése az App Service Linux WildFly Message-Driven Bean:

    ```xml    
    <plugins> 
    
        <!--*************************************************-->
        <!-- Deploy to WildFly in App Service Linux          -->
        <!--*************************************************-->
           
        <plugin>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-webapp-maven-plugin</artifactId>
            <version>1.5.3</version>
            <configuration>
        
                <!-- Web App information -->
               <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
               <appServicePlanName>${WEBAPP_PLAN_NAME}</appServicePlanName>
                <appName>${WEBAPP_NAME}</appName>
                <region>${REGION}</region>
        
                <!-- Java Runtime Stack for Web App on Linux-->
                <linuxRuntime>wildfly 14-jre8</linuxRuntime>
                
            </configuration>
        </plugin>
        ...
    </plugins>
    ```
2. Hozhat létre, és üzembe helyezése Message-Driven Bean App Service Linux:

    ```bash
    mvn package
    [INFO] Scanning for projects...
    [INFO] 
    [INFO] ------------------------------------------------------------------------
    [INFO] Building Quickstart: websocket-hello 14.0.1.Final
    [INFO] ------------------------------------------------------------------------
    [INFO] 
    ...
    ...
    [INFO] --- maven-war-plugin:3.2.2:war (default-war) @ websocket-hello ---
    [INFO] Packaging webapp
    [INFO] Assembling webapp [websocket-hello] in [/Users/selvasingh/migrate-java-ee-app-to-azure-2/quickstart/websocket-hello/target/websocket-hello]
    [INFO] Processing war project
    [INFO] Copying webapp resources [/Users/selvasingh/migrate-java-ee-app-to-azure-2/quickstart/websocket-hello/src/main/webapp]
    [INFO] Webapp assembled in [54 msecs]
    [INFO] Building war: /Users/selvasingh/migrate-java-ee-app-to-azure-2/quickstart/websocket-hello/target/websocket-hello.war
    [INFO] 
    [INFO] --- maven-source-plugin:3.0.1:jar-no-fork (attach-sources) @ websocket-hello ---
    [INFO] Building jar: /Users/selvasingh/migrate-java-ee-app-to-azure-2/quickstart/websocket-hello/target/websocket-hello-sources.jar
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 3.444 s
    [INFO] Finished at: 2019-02-11T22:58:35-08:00
    [INFO] Final Memory: 27M/318M
    [INFO] ------------------------------------------------------------------------
    ```
3. Helyezze üzembe a linuxon futó App Service-ben az üzenet adatvezérelt bean: 

    ```bash
    mvn azure-webapp:deploy
    ```

    Itt látható a minta kimenete: 

    ```bash
    [INFO] Scanning for projects...
    [INFO] 
    [INFO] ------------------------------------------------------------------------
    [INFO] Building Quickstart: websocket-hello 14.0.1.Final
    [INFO] ------------------------------------------------------------------------
    [INFO] 
    [INFO] --- azure-webapp-maven-plugin:1.5.3:deploy (default-cli) @ websocket-hello ---
    [INFO] Authenticate with Azure CLI 2.0
    [INFO] Target Web App doesn't exist. Creating a new one...
    [INFO] Creating App Service Plan 'websocket-hello-app-appservice-plan'...
    [INFO] Successfully created App Service Plan.
    [INFO] Successfully created Web App.
    [INFO] Trying to deploy artifact to websocket-hello-app...
    [INFO] Deploying the war file...
    [INFO] Successfully deployed the artifact to https://websocket-hello-app.azurewebsites.net
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 01:41 min
    [INFO] Finished at: 2019-02-11T23:03:56-08:00
    [INFO] Final Memory: 57M/366M
    [INFO] ------------------------------------------------------------------------
    
    ```

### <a name="open-the-migrated-app-on-app-service-linux"></a>Nyissa meg a migrált alkalmazást az App Service Linux rendszeren

```bash
open https://websocket-hello-app.azurewebsites.net
```

![Websocket Hello kép ](./media/migrate-java-apps-wild-fly/websocket-hello.png)


Gratulálunk! Meglévő Java vállalati számítási feladatokat az Azure-bA migrált: alkalmazást az App Service Linux és az alkalmazás az üzenetkezelési rendszer, az Azure Service Bus.

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket: 

- [A linuxon futó App Service-ben Java Enterprise útmutató](../app-service/containers/app-service-java-enterprise.md)
- [Maven beépülő modul az Azure App Service-hez](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme?view=azure-java-stable)
- [A JBoss/WildFly általános JMS RA Adapter telepítése](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.1/html/configuring_messaging/resource_adapters#deploy_configure_generic_jms_resource_adapter)
- [WildFly/JBoss üzenetkezelési konfiguráció](https://docs.jboss.org/author/display/WFLY/Messaging+configuration)
- [A JBoss/WildFly parancssori útmutatója](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface)
- [Nyissa meg az SSH-kapcsolatot a fejlesztői gépről](../app-service/containers/app-service-linux-ssh-support.md#open-ssh-session-from-remote-shell)
- [Azure Java-fejlesztőknek](/java/azure/)
