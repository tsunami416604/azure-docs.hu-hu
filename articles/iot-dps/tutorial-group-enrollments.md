---
title: "Azure IoT Hub Java és a beléptetési csoportok használatával szimulált X.509 az eszköz kiépítése |} Microsoft Docs"
description: "Az oktatóanyag az Azure - létrehozásának és kiosztásának egy szimulált X.509 eszközt Java eszköz és a szolgáltatás SDK-t és a beléptetési csoportok IoT Hub eszköz kiépítése szolgáltatáshoz"
services: iot-dps
keywords: 
author: msebolt
ms.author: v-masebo
ms.date: 01/04/2018
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 14e5e7613fd5df650625cf8997d569b754ceb689
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/20/2018
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-and-service-sdk-and-group-enrollments-for-iot-hub-device-provisioning-service"></a>Hozzon létre, és helyezze üzembe a szimulált X.509-eszközt Java eszköz és az SDK szolgáltatás és a csoport regisztrációkat IoT Hub eszköz kiépítése szolgáltatáshoz

Ezeket a lépéseket egy X.509-eszközt a fejlesztési számítógépén fut a Windows operációs rendszer szimulálása megjelenítése, és egy kódminta segítségével a szimulált eszköz csatlakoztatása az eszköz kiépítése szolgáltatáshoz és az IoT hub beléptetési csoportok használatával. 

A folytatás előtt végezze el az [IoT Hub Device Provisioning Service beállítása az Azure Portallal](./quick-setup-auto-provision.md) című cikk lépéseit.


## <a name="prepare-the-environment"></a>A környezet előkészítése 

1. Győződjön meg arról, hogy a [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) telepítve van a gépén.

1. Töltse le és telepítse a [Mavent](https://maven.apache.org/install.html).

1. Győződjön meg arról, hogy a(z) `git` telepítve van a gépen, és a parancsablakból elérhető környezeti változókhoz van adva. A [Software Freedom Conservancy's Git ügyfél eszközeiben](https://git-scm.com/download/) találja a telepíteni kívánt `git` eszközök legújabb verzióját, amely tartalmazza a **Git Bash** eszközt, azt a parancssori alkalmazást, amellyel kommunikálhat a helyi Git-adattárral. 

1. Használja a következő [tanúsítvány áttekintése](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) a teszt tanúsítványok létrehozásához. Részletesebb meg tanúsítványok létrehozását, talál [PowerShell-parancsfájlokkal kezelheti a hitelesítésszolgáltató által aláírt X.509-tanúsítványokat](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-security-x509-create-certificates).

    > [!NOTE]
    > Ez a lépés szükséges [OpenSSL](https://www.openssl.org/), amely vagy beépített és forrásból telepítve, vagy letölthető, és telepített egy [3. fél](https://wiki.openssl.org/index.php/Binaries) például [ez](https://sourceforge.net/projects/openssl/). Ha már létrehozta a _legfelső szintű_, _köztes_ és _eszköz_ tanúsítványok, előfordulhat, hogy kihagyja ezt a lépést.
    >

1. Hozza létre a regisztrációs adatait:

    1. Lépéseinek **1. lépés** és **2. lépés** létrehozásához a _legfelső szintű_ és _köztes_ tanúsítványokat.

    1. Jelentkezzen be az Azure Portalra, a bal oldali menüben kattintson az **Összes erőforrás** gombra, és nyissa meg az eszközkiépítési szolgáltatását.

        1. Az eszközkiépítési szolgáltatás összefoglaló panelén válassza a **Tanúsítványok** lehetőséget, és kattintson a felül található **Hozzáadás** gombra.

        1. A **Tanúsítvány hozzáadása** területen adja meg a következő információkat:
            - Adjon meg egy egyedi tanúsítványnevet.
            - Válassza ki a  **_RootCA.pem_**  újonnan létrehozott fájlt.
            - Ha végzett, kattintson a **Mentés** gombra.

        ![Tanúsítvány hozzáadása](./media/tutorial-group-enrollments/add-certificate.png)

        1. Válassza ki az újonnan létrehozott tanúsítványt:
            - Kattintson az **Ellenőrző kód létrehozása** lehetőségre. Másolja ki a kapott kódot.
            - Lépéseinek **3. lépés**. Adja meg a _ellenőrzőkódot_ , vagy kattintson a jobb gombbal a futó PowerShell-ablakban illessze be.  Nyomja le az **Enter** billentyűt.
            - Válassza ki az újonnan létrehozott  **_verifyCert4.pem_**  fájl az Azure portálon. Kattintson a **ellenőrizze**.

            ![Tanúsítvány ellenőrzése](./media/tutorial-group-enrollments/validate-certificate.png)

1. Futtassa a Befejezés **4. lépés** és **5. lépés** a eszköztanúsítványok és a karbantartás erőforrások létrehozására.

> [!NOTE]
> Eszköztanúsítványok létrehozásakor ügyeljen arra, hogy a név csak kisbetűket, számok és betűk és kötőjelek használni.
>


## <a name="create-a-device-enrollment-entry"></a>Eszközregisztrációs bejegyzés létrehozása

1. Nyisson meg egy parancssort. A Java SDK-kódmintákat a GitHub-tárház klónozása:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. A letöltött forráskódban keresse meg az **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** mintamappát. Nyissa meg az **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** fájlt egy tetszőleges szövegszerkesztővel, és adja hozzá az alábbi részeket:

    1. Adja hozzá a `[Provisioning Connection String]` karakterláncot a kiépítési szolgáltatáshoz a portálról:

        1. Az [Azure Portalon](https://portal.azure.com) lépjen a kiépítési szolgáltatásra. 

        1. Nyissa meg a **Megosztott elérési szabályzatok** panelt, és válasszon ki egy szabályzatot, amely rendelkezik az *EnrollmentWrite* engedéllyel.
    
        1. Másolja ki az **elsődleges kulcs kapcsolati karakterláncát**. 

            ![A kiépítési kapcsolati karakterlánc lekérése a portálról](./media/tutorial-group-enrollments/provisioning-string.png)  

        1. A **_ServiceEnrollmentGroupSample.java_** mintakódfájlban cserélje le a `[Provisioning Connection String]` karakterláncot az **elsődleges kulcs kapcsolati karakterláncára**.

            ```java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    1. Nyissa meg a  **_RootCA.pem_**  fájlt egy szövegszerkesztőben. Rendelje a **főtanúsítvány** értékét a **PUBLIC_KEY_CERTIFICATE_STRING** paraméterhez az alábbiak szerint:

        ```java
        private static final String PUBLIC_KEY_CERTIFICATE_STRING =
                "-----BEGIN CERTIFICATE-----\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "-----END CERTIFICATE-----\n";
        ```
 
    1. Az [Azure Portalon](https://portal.azure.com) lépjen a kiépítési szolgáltatáshoz csatolt IoT Hubra. Nyissa meg a hubhoz tartozó **Áttekintés** lapot, és másolja ki az **Eszköznév** értékét. Rendelje hozzá ezt az **Eszköznév** értéket az *IOTHUB_HOST_NAME* paraméterhez.

        ```java
        private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
        ```

    1. Tanulmányozza a mintakódot. A segítségével létrehozhat, frissíthet, lekérdezhet és törölhet egy csoportos X.509-eszközregisztrációt. Ha ellenőrizni szeretné a regisztráció sikerességét a portálon, ideiglenesen tegye megjegyzésbe a következő kódsorokat a _ServiceEnrollmentGroupSample.java_ fájl végén:

        ```java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    1. Mentse a _ServiceEnrollmentGroupSample.java_ fájlt. 

1. Nyisson meg egy parancsablakot, és lépjen az **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** mappára.

1. Hozza létre a mintakódot a következő paranccsal:

    ```cmd\sh
    mvn install -DskipTests
    ```

1. Futtassa a mintát a következő parancsokkal a parancsablakban:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

1. A sikeres regisztráció érdekében kísérje figyelemmel a kimeneti ablakot.

    ![sikeres regisztráció](./media/tutorial-group-enrollments/enrollment.png) 

1. Az Azure Portalon lépjen a kiépítési szolgáltatásra. Kattintson a **Regisztrációk kezelése** elemre. Az X.509-eszközök csoportjának a **Regisztrációs csoportok** lapon kell megjelennie egy automatikusan létrehozott *CSOPORTNÉV* alatt. 


## <a name="simulate-the-device"></a>Az eszköz szimulálása

1. Az eszközkiépítési szolgáltatás összefoglaló panelén válassza az **Áttekintés** lehetőséget, majd jegyezze fel az _Azonosító hatóköre_ és a _Kiépítési szolgáltatás globális végpontja_ értékét.

    ![Szolgáltatás adatai](./media/tutorial-group-enrollments/extract-dps-endpoints.png)

1. Nyisson meg egy parancssort. Lépjen a mintaprojekt mappájára.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. Adja meg a regisztrációs adatait a következő módon:

    - Szerkessze az `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` fájlt, hogy az tartalmazza a korábban feljegyzett _Azonosító hatóköre_ és _Kiépítési szolgáltatás globális végpontja_ értékeket. Nyissa meg a  **_{deviceName}-public.pem_**  fájlt, és ezt az értéket tartalmazzák a _ügyféltanúsítványt_. Nyissa meg a  **_{deviceName}-all.pem_**  fájlt, és másolja a szöveg _---BEGIN titkos kulcs---_ való _---vége titkos kulcs---_.  Használja ezt a _ügyfél tanúsítvány titkos kulcsához_.

        ```java
        private static final String idScope = "[Your ID scope here]";
        private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
        private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
        private static final String leafPublicPem = "<Your Public PEM Certificate here>";
        private static final String leafPrivateKey = "<Your Private PEM Key here>";
        ```

        - A tanúsítvány és a kulcs belefoglalásához használja a következő formátumot:
            
            ```java
            private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "-----END CERTIFICATE-----\n";
            private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXX\n" +
                "-----END PRIVATE KEY-----\n";
            ```

1. Hozza létre a mintát. Lépjen a célmappára, és futtassa a létrehozott jar-fájlt.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

    ![Sikeres regisztráció](./media/tutorial-group-enrollments/registration.png)

1. A portálon lépjen a kiépítési szolgáltatáshoz csatolt IoT hubhoz, és nyissa meg a **Device Explorer** (Eszközkereső) panelt. Ha sikeresen kiépíti a szimulált X.509-eszközt a hubon, az eszköz azonosítója megjelenik a **Device Explorer** panelen, a hozzá tartozó *ÁLLAPOT* pedig **engedélyezett** lesz. Ha már a minta eszközalkalmazás futtatása előtt megnyitotta a panelt, akkor lehet, hogy rá kell kattintania a fenti **Frissítés** gombra. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/tutorial-group-enrollments/hub-registration.png) 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja az eszközügyfél minta használatát és megismerését, akkor ne törölje a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti a rövid útmutatóhoz létrehozott összes erőforrást.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az eszközkiépítési szolgáltatást. Nyissa meg a szolgáltatás **Regisztrációk kezelése** paneljét, majd kattintson az **Egyéni regisztrációk** lapra. Válassza ki a rövid útmutatóban regisztrált eszköz *REGISZTRÁCIÓS AZONOSÍTÓJÁT*, majd kattintson a felül található **Törlés** gombra. 
1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az IoT Hubot. Nyissa meg a hub **IoT-eszközök** paneljét, válassza ki a rövid útmutatóban regisztrált eszköz *ESZKÖZAZONOSÍTÓJÁT*, majd kattintson a felül található **Törlés** gombra.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a szimulált eszköz X.509 elkészítette a Windows-számítógépen és létrehozni azt, hogy az IoT hub, az Azure IoT Hub eszköz kiépítése szolgáltatáshoz és a beléptetési csoportok használatával. További információt az X.509 eszköz, továbbra is eszköz fogalmakat. 

> [!div class="nextstepaction"]
> [IoT Hub eszköz kiépítése szolgáltatáshoz eszköz fogalmak](concepts-device.md)
