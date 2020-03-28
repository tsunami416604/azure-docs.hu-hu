---
title: Oktatóanyag – Szimulált X.509-es eszköz kiépítése az Azure IoT Hubba Java és regisztrációs csoportok használatával
description: Ebben az oktatóanyagban hozzon létre és hozzon létre egy szimulált X.509-es eszközt Java-eszköz és szolgáltatás SDK-val, valamint az IoT Hub-eszközlétesítési szolgáltatás (DPS) regisztrációs csoportjaival.
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: d6cb3af134ff272d79cfc440047a3d90733ee9e8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74976808"
---
# <a name="tutorial-create-and-provision-a-simulated-x509-device-using-java-device-and-service-sdk-and-group-enrollments-for-iot-hub-device-provisioning-service"></a>Oktatóanyag: Szimulált X.509-es eszköz létrehozása és kiépítése Java-eszköz- és szolgáltatásSDK-vel, valamint csoportos regisztrációkkal az IoT Hub-eszközkiépítési szolgáltatáshoz

Ezek a lépések bemutatják, hogyan hozhat létre szimulált X.509-eszközt egy Windows operációs rendszert futtató fejlesztői gépen, és hogyan használhat egy kódmintát, hogy ezt a szimulált eszközt regisztrációs csoportok segítségével összekösse a Device Provisioning Service-szel és az IoT Hubbal. 

A folytatás előtt végezze el az [IoT Hub Device Provisioning Service beállítása az Azure Portallal](./quick-setup-auto-provision.md) című cikk lépéseit.


## <a name="prepare-the-environment"></a>A környezet előkészítése 

1. Győződjön meg arról, hogy a [Java SE Development Kit 8](https://aka.ms/azure-jdks) telepítve van a gépén.

1. Töltse le és telepítse a [Mavent](https://maven.apache.org/install.html).

1. Győződjön meg arról, hogy a(z) `git` telepítve van a gépen, és a parancsablakból elérhető környezeti változókhoz van adva. A [Software Freedom Conservancy's Git ügyfél eszközeiben](https://git-scm.com/download/) találja a telepíteni kívánt `git` eszközök legújabb verzióját, amely tartalmazza a **Git Bash** eszközt, azt a parancssori alkalmazást, amellyel kommunikálhat a helyi Git-adattárral. 

1. A teszttanúsítványok létrehozásához használja az alábbi [tanúsítványáttekintést.](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)

    > [!NOTE]
    > Ehhez a lépéshez [OpenSSL](https://www.openssl.org/)szükséges , amely forrásból vagy letölthető és telepíthető egy ilyen [harmadik féltől](https://wiki.openssl.org/index.php/Binaries) [.](https://sourceforge.net/projects/openssl/) Ha már létrehozta a _fő-_, _köztes_ és _eszköztanúsítványt_, kihagyhatja ezt a lépést.
    >

    1. A _fő-_ és a _köztes_ tanúsítvány létrehozásához végezze el az első két lépést.

    1. Jelentkezzen be az Azure Portalra, a bal oldali menüben kattintson az **Összes erőforrás** gombra, és nyissa meg az eszközkiépítési szolgáltatását.

        1. Az eszközkiépítési szolgáltatás összefoglaló panelén válassza a **Tanúsítványok** lehetőséget, és kattintson a felül található **Hozzáadás** gombra.

        1. A **Tanúsítvány hozzáadása** területen adja meg a következő információkat:
            - Adjon meg egy egyedi tanúsítványnevet.
            - Jelölje ki a létrehozott **_RootCA.pem_** fájlt.
            - Ha végzett, kattintson a **Mentés** gombra.

           ![Tanúsítvány hozzáadása](./media/tutorial-group-enrollments/add-certificate.png)

        1. Válassza ki az újonnan létrehozott tanúsítványt:
            - Kattintson az **Ellenőrző kód létrehozása** lehetőségre. Másolja ki a kapott kódot.
            - Futtassa az ellenőrzést. Adja meg az _ellenőrző kódot_, vagy kattintson a jobb gombbal, hogy beillessze a PowerShell-ablakba.  Nyomja **le az Enter billentyűt.**
            - Válassza ki az újonnan létrehozott **_verifyCert4.pem_** fájlt az Azure Portalon. Kattintson az **Ellenőrzés** lehetőségre.

              ![Tanúsítvány ellenőrzése](./media/tutorial-group-enrollments/validate-certificate.png)

    1. Végezetül futtassa az eszköztanúsítványok létrehozásához és a felesleges erőforrások törléséhez szükséges műveleteket.

       > [!NOTE]
       > Az eszköztanúsítványok létrehozásakor ügyeljen arra, hogy az eszköz nevében kizárólag kisbetűs alfanumerikus karaktereket, valamint kötőjeleket használjon.
       >


## <a name="create-a-device-enrollment-entry"></a>Eszközregisztrációs bejegyzés létrehozása

1. Nyisson meg egy parancssort. Klónozza a GitHub-adattárat a Java SDK-kódmintákhoz:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. A letöltött forráskódban keresse meg az **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** mintamappát. Nyissa meg az **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** fájlt egy tetszőleges szövegszerkesztővel, és adja hozzá az alábbi részeket:

    1. Adja hozzá a `[Provisioning Connection String]` karakterláncot a kiépítési szolgáltatáshoz a portálról:

        1. Keresse meg a létesítési szolgáltatást az [Azure Portalon.](https://portal.azure.com)

        1. Nyissa meg a **Megosztott elérési szabályzatok** panelt, és válasszon ki egy szabályzatot, amely rendelkezik az *EnrollmentWrite* engedéllyel.

        1. Másolja ki az **elsődleges kulcs kapcsolati sztringjét**.

            ![A kiépítési kapcsolati sztring lekérése a portálról](./media/tutorial-group-enrollments/provisioning-string.png)  

        1. A **_ServiceEnrollmentGroupSample.java_** mintakódfájlban cserélje le a `[Provisioning Connection String]` sztringet az **elsődleges kulcs kapcsolati sztringjére**.

            ```java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    1. Nyissa meg a köztes aláíró tanúsítványfájlt egy szövegszerkesztőben. Frissítse `PUBLIC_KEY_CERTIFICATE_STRING` az értéket a köztes aláíró tanúsítvány értékével.

        Ha az eszköztanúsítványokat Bash rendszerhéjjal hozta létre, *a ./certs/azure-iot-test-only.intermediate.cert.pem* tartalmazza a köztes tanúsítványkulcsot. Ha a tanúsítványok a PowerShell használatával jöttek létre, *a ./Intermediate1.pem* lesz a köztes tanúsítványfájl.

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

    1. Tanulmányozza a mintakódot, A segítségével létrehozhat, frissíthet, lekérdezhet és törölhet egy csoportos X.509-eszközregisztrációt. Ha ellenőrizni szeretné a regisztráció sikerességét a portálon, ideiglenesen tegye megjegyzésbe a következő kódsorokat a _ServiceEnrollmentGroupSample.java_ fájl végén:

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

    ![Sikeres regisztráció](./media/tutorial-group-enrollments/enrollment.png) 

1. Az Azure Portalon lépjen a kiépítési szolgáltatásra. Kattintson a **Regisztrációk kezelése** elemre. Az X.509-eszközök csoportjának a **Regisztrációs csoportok** lapon kell megjelennie egy automatikusan létrehozott *CSOPORTNÉV* alatt.

## <a name="simulate-the-device"></a>Az eszköz szimulálása

1. Az Eszközkiépítési szolgáltatás összefoglaló panelen válassza **az Áttekintés** lehetőséget, és jegyezze fel az _azonosító hatókörét_ és _a kiépítési szolgáltatás globális végpontját._

    ![Szolgáltatás adatai](./media/tutorial-group-enrollments/extract-dps-endpoints.png)

1. Nyisson meg egy parancssort. Lépjen a mintaprojekt mappájára.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. Szerkessze, `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` hogy tartalmazza az _azonosító hatókör_ és a _kiépítési szolgáltatás globális végpont,_ amely korábban megjegyezte.

    ```java
    private static final String idScope = "[Your ID scope here]";
    private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
    private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
    private static final int MAX_TIME_TO_WAIT_FOR_REGISTRATION = 10000; // in milli seconds
    private static final String leafPublicPem = "<Your Public PEM Certificate here>";
    private static final String leafPrivateKey = "<Your Private PEM Key here>";
    ```

1. Frissítse `leafPublicPem` a `leafPrivateKey` változókat a nyilvános és privát eszköztanúsítványokkal.

    Ha az eszköztanúsítványokat a PowerShell segítségével hozta létre, a mydevice* fájlok tartalmazzák az eszköz nyilvános kulcsát, titkos kulcsát és PFX-ét.

    Ha az eszköztanúsítványokat Bash rendszerhéjjal hozta létre, a ./certs/new-device.cert.pem tartalmazza a nyilvános kulcsot. Az eszköz személyes kulcsa a ./private/new-device.key.pem fájlban lesz.

    Nyissa meg a nyilvános `leafPublicPem` kulcsfájlt, és frissítse a változót ezzel az értékkel. Másolja a szöveget _a -----BEGIN PRIVATE KEY-----_ szövegéből _a -----END PRIVATE KEY-----_ mezőbe.

    ```java
    private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

    Nyissa meg a személyes `leafPrivatePem` kulcsfájlt, és frissítse a változót ezzel az értékkel. Másolja a _szöveget a -----BEGIN RSA TITKOS KULCS-----_ szövegéből _a -----END RSA PRIVATE KEY----- mezőbe._

    ```java
    private static final String leafPrivateKey = "-----BEGIN RSA PRIVATE KEY-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END RSA PRIVATE KEY-----\n";
    ```

1. Adjon hozzá egy `leafPrivateKey` új változót közvetlenül a köztes tanúsítványhoz. Nevezze el `intermediateKey`ezt az új változót . Adja meg a köztes aláíró tanúsítvány értékét.

    Ha az eszköztanúsítványokat Bash rendszerhéjjal hozta létre, *a ./certs/azure-iot-test-only.intermediate.cert.pem* tartalmazza a köztes tanúsítványkulcsot. Ha a tanúsítványok a PowerShell használatával jöttek létre, *a ./Intermediate1.pem* lesz a köztes tanúsítványfájl.

    ```java
    private static final String intermediateKey = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

1. A `main` függvényben adja `intermediateKey` hozzá `signerCertificates` a gyűjteményt `securityProviderX509`a inicializálása előtt.

    ```java
    public static void main(String[] args) throws Exception
    {
        ...

        try
        {
            ProvisioningStatus provisioningStatus = new ProvisioningStatus();

            // Add intermediate certificate as part of the certificate key chain.
            signerCertificates.add(intermediateKey);

            SecurityProvider securityProviderX509 = new SecurityProviderX509Cert(leafPublicPem, leafPrivateKey, signerCertificates);
    ```

1. Mentse a módosításokat, és készítse el a mintát. Lépjen a célmappára, és futtassa a létrehozott jar-fájlt.

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
1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az eszközkiépítési szolgáltatást. Nyissa meg a szolgáltatás **regisztrációk kezelése** panelt, majd kattintson *REGISTRATION ID* az **Egyéni regisztrációk** fülre. **Delete** 
1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az IoT Hubot. Nyissa meg a hub **IoT-eszközök** paneljét, válassza ki a rövid útmutatóban regisztrált eszköz *ESZKÖZAZONOSÍTÓJÁT*, majd kattintson a felül található **Törlés** gombra.


## <a name="next-steps"></a>További lépések

Ebben az oktatóprogramban egy szimulált X.509-eszközt hozott létre Windows rendszerű gépén, majd kiépítette az IoT Hubon az Azure IoT Hub Device Provisioning Service-szel és regisztrációs csoportokkal. Ha további információra van szüksége az X.509-eszközzel kapcsolatban, folytassa az eszközzel kapcsolatos alapvető információkat ismertető témakörrel. 

> [!div class="nextstepaction"]
> [Az IoT Hub Device Provisioning Service-re vonatkozó alapvető információk](concepts-device.md)
