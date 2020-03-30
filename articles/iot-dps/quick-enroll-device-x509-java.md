---
title: X.509-es eszközök regisztrálása az Azure-eszközkiépítési szolgáltatásba Java használatával
description: Ez a rövid útmutató csoportos és egyéni regisztrációkat is használ. Ebben a rövid útmutatóban x.509-es eszközöket regisztrál az Azure IoT Hub-eszközlétesítési szolgáltatás (DPS) Java használatával.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 534956a53615cfafeffa611127bc8c3cc4493753
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77604920"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-java"></a>Rövid útmutató: X.509-eszközök regisztrációja a Device Provisioning Service-be a Java használatával

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Ebben a rövid útmutatóban a Java használatával programozott módon regisztrálhatja az X.509 szimulált eszközök egy csoportját az Azure IoT Hub-eszközkiépítési szolgáltatásra. Az eszközök egy regisztrációs csoport vagy egy egyéni regisztráció létrehozásával vannak bejelentve egy kiépítési szolgáltatáspéldányra. Ez a rövid útmutató bemutatja, hogyan hozhat létre mindkét típusú regisztrációk a Java Service SDK és egy minta Java alkalmazás használatával.

## <a name="prerequisites"></a>Előfeltételek

- Az [IoT Hub-eszközkiépítési szolgáltatás beállítása az Azure Portalon.](./quick-setup-auto-provision.md)
- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Java SE Fejlesztői Készlet 8](https://aka.ms/azure-jdks). Ez a rövid útmutató az alábbi [Java Service SDK-t](https://azure.github.io/azure-iot-sdk-java/service/) telepíti. Úgy működik, mind a Windows és a Linux. Ez a rövid útmutató a Windows rendszert használja.
- [Maven 3](https://maven.apache.org/download.cgi).
- [Git](https://git-scm.com/download/).

<a id="javasample"></a>

## <a name="download-and-modify-the-java-sample-code"></a>A Java-mintakód letöltése és módosítása

Ez a szakasz egy önaláírt X.509-tanúsítványt használ. Fontos szem előtt tartani a következőket:

* Az önaláírt tanúsítványok csak tesztelési célokra alkalmasak, és nem javasolt őket éles környezetben alkalmazni.
* Az önaláírt tanúsítványok alapértelmezett lejárati ideje 1 év.

A következő lépések bemutatják, hogyan adhatja hozzá az X.509-eszköz kiépítési adatait a mintakódhoz. 

1. Nyisson meg egy parancssort. Klónozza a GitHub-tárta eszközregisztrációs kódmintáját a [Java Service SDK](https://azure.github.io/azure-iot-sdk-java/service/)használatával:
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. A letöltött forráskódban keresse meg az **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** mintamappát. Nyissa meg az **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** fájlt egy tetszőleges szövegszerkesztővel, és adja hozzá az alábbi részeket:

    1. Adja hozzá a `[Provisioning Connection String]` karakterláncot a kiépítési szolgáltatáshoz a portálról:
        1. Keresse meg a létesítési szolgáltatást az [Azure Portalon.](https://portal.azure.com) 
        2. Nyissa meg a **Megosztott hozzáférési házirendeket**, és válasszon ki egy olyan házirendet, amely rendelkezik a *EnrollmentWrite* engedéllyel.
        3. Másolja ki az **elsődleges kulcs kapcsolati sztringjét**. 

            ![A kiépítési kapcsolati sztring lekérése a portálról](./media/quick-enroll-device-x509-java/provisioning-string.png)  

        4. A **_ServiceEnrollmentGroupSample.java_** mintakódfájlban cserélje le a `[Provisioning Connection String]` sztringet az **elsődleges kulcs kapcsolati sztringjére**.

            ```Java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    2. Adja hozzá az eszközcsoport főtanúsítványát. Ha szüksége van egy minta-főtanúsítványra, használja az _X.509-tanúsítványkészítő_ eszközt az alábbiak szerint:
        1. Egy parancsablakban lépjen az **_azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator_** mappára.
        2. Építse fel az eszközt a következő parancs futtatásával:

                ```cmd\sh
                mvn clean install
                ```

        4. Futtassa az eszközt az alábbi parancsokkal:

                ```cmd\sh
                cd target
                java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
                ```

        5. Amikor a rendszer erre kéri, opcionálisan megadhat egy _köznapi nevet_ a tanúsítvány számára.
        6. Az eszköz létrehoz egy helyi **ügyféltanúsítványt**, az **ügyféltanúsítvány titkos kulcsát**, valamint a **főtanúsítványt**.
        7. Másolja a **főtanúsítványt** a vágólapra a **_-----BEGIN CERTIFICATE-----_** és **_-----END CERTIFICATE-----_** sort is beleértve. 
        8. Rendelje a **főtanúsítvány** értékét a **PUBLIC_KEY_CERTIFICATE_STRING** paraméterhez az alábbiak szerint:

                ```Java
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

        9. Zárja be a parancsablakot, vagy írja be az **n** karaktert, amikor a rendszer az *ellenőrző kódot* kéri. 
 
    3. Ha szeretné, a kiépítési szolgáltatást a mintakódon keresztül is konfigurálhatja:
        - A konfiguráció mintához való hozzáadásához kövesse az alábbi lépéseket:
            1. Az [Azure Portalon](https://portal.azure.com) lépjen a kiépítési szolgáltatáshoz csatolt IoT Hubra. Nyissa meg a hubhoz tartozó **Áttekintés** lapot, és másolja ki az **Eszköznév** értékét. Rendelje hozzá ezt az **Eszköznév** értéket az *IOTHUB_HOST_NAME* paraméterhez.

                ```Java
                private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
                ```
            2. Rendeljen egy könnyen megjegyezhető nevet a *DEVICE_ID* paraméterhez, és a *PROVISIONING_STATUS* paraméternél tartsa meg az alapértelmezett *ENABLED* értéket. 

        - VAGY, ha úgy dönt, hogy nem konfigurálja a kiépítési szolgáltatást, tegye megjegyzésbe vagy törölje a következő utasításokat a _ServiceEnrollmentGroupSample.java_ fájlban:

            ```Java
            enrollmentGroup.setIotHubHostName(IOTHUB_HOST_NAME);                // Optional parameter.
            enrollmentGroup.setProvisioningStatus(ProvisioningStatus.ENABLED);  // Optional parameter.
            ```

    4. Tanulmányozza a mintakódot, A segítségével létrehozhat, frissíthet, lekérdezhet és törölhet egy csoportos X.509-eszközregisztrációt. Ha ellenőrizni szeretné a regisztráció sikerességét a portálon, ideiglenesen tegye megjegyzésbe a következő kódsorokat a _ServiceEnrollmentGroupSample.java_ fájl végén:

        ```Java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    5. Mentse a _ServiceEnrollmentGroupSample.java_ fájlt. 
 

<a id="runjavasample"></a>

## <a name="build-and-run-sample-group-enrollment"></a>Csoportregisztrációs minta létrehozása és futtatása

Az Azure IoT Device Provisioning Service kétféle típusú regisztrációt támogat:

- [Regisztrációs csoportok](concepts-service.md#enrollment-group): Több kapcsolódó eszköz regisztrálásához.
- [Egyéni regisztrációk:](concepts-service.md#individual-enrollment)Egyetlen eszköz regisztrálására használható.

Ez az eljárás regisztrációs csoportot használ. A következő szakasz egyéni regisztrációt használ.

1. Nyisson meg egy parancsablakot, és lépjen az **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** mappára.

2. Hozza létre a mintakódot a következő paranccsal:

    ```cmd\sh
    mvn install -DskipTests
    ```

   Ez a parancs letölti [`com.microsoft.azure.sdk.iot.provisioning.service`](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) a Maven csomagot a gépre. A csomag tartalmazza a Java szolgáltatásoldali SDK bináris fájljait, amelyeket a mintakódnak létre kell hoznia. Ha az előző szakaszban futtatta az _X.509-tanúsítványkészítőt_, a csomag már le van töltve a gépre. 

3. Futtassa a mintát a következő parancsokkal a parancsablakban:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

4. A sikeres regisztráció érdekében kísérje figyelemmel a kimeneti ablakot.

5. Az Azure Portalon lépjen a kiépítési szolgáltatásra. Kattintson a **Regisztrációk kezelése** elemre. Figyelje meg, hogy az X.509-eszközök csoportja megjelenik a **Regisztrációs csoportok** lapon, egy automatikusan generált *CSOPORTNÉV*vel. 

    ![Sikeres X.509-regisztráció ellenőrzése a portálon](./media/quick-enroll-device-x509-java/verify-x509-enrollment.png)  

## <a name="modifications-to-enroll-a-single-x509-device"></a>Egyetlen X.509-eszköz regisztrációja esetén elvégzendő változtatások

Egyetlen X.509-eszköz regisztrációja esetén módosítsa a [TPM-eszköz regisztrációja az IoT Hub Device Provisioning Service-be a Java szolgáltatásoldali SDK-val](quick-enroll-device-tpm-java.md#javasample) című részben található *egyéni regisztráció* mintakódját az alábbiak szerint:

1. Másolja az X.509-ügyféltanúsítvány *köznapi nevét* a vágólapra. Ha az [előző mintakód szakaszában](#javasample) leírtak szerint alkalmazni szeretné az _X.509-tanúsítványkészítőt_, adjon meg egy _köznapi nevet_ a tanúsítvány számára, vagy használja az alapértelmezett **microsoftriotcore** nevet. Használja ezt a **köznapi nevet** a *REGISTRATION_ID* változó értékeként. 

    ```Java
    // Use common name of your X.509 client certificate
    private static final String REGISTRATION_ID = "[RegistrationId]";
    ```

2. Nevezze át a *TPM_ENDORSEMENT_KEY* változót *PUBLIC_KEY_CERTIFICATE_STRING* névre. Másolja be az ügyféltanúsítványt vagy az _X.509-tanúsítványkészítő_ kimenetében szereplő **ügyféltanúsítvány** értékét a *PUBLIC_KEY_CERTIFICATE_STRING* változó értékének helyére. 

    ```Java
    // Rename the variable *TPM_ENDORSEMENT_KEY* as *PUBLIC_KEY_CERTIFICATE_STRING*
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
3. Az X.509-ügyféltanúsítvány használatához a **fő** függvényben cserélje le az `Attestation attestation = new TpmAttestation(TPM_ENDORSEMENT_KEY);` sort a következőre:
    ```Java
    Attestation attestation = X509Attestation.createFromClientCertificates(PUBLIC_KEY_CERTIFICATE_STRING);
    ```

4. Mentse, építse fel és futtassa az *egyes regisztrációs* mintafájlt a Build és run the sample code for [individual enrollment](quick-enroll-device-tpm-java.md#runjavasample)című szakaszlépéseivel.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha azt tervezi, hogy vizsgálja meg a Java szolgáltatás minta, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem tervezi a folytatást, az alábbi lépésekkel törölje az összes olyan erőforrást, amelyet ez a rövid útmutató hozott létre.

1. Zárja be a Java-minta kimeneti ablakát a gépen.
1. Zárja be az _X509-tanúsítványkészítő_ ablakát a gépen.
1. Keresse meg az Eszközkiépítési szolgáltatást az Azure Portalon, válassza a **Regisztrációk kezelése**lehetőséget, *GROUP NAME* majd jelölje be a **Regisztrációs csoportok** lapot. **Delete**  

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban x.509-es eszközök szimulált csoportját regisztrálta az Eszközkiépítési szolgáltatásba. Ha mélyebben szeretné megismerni az eszközkiépítést, folytassa az Azure Portalon az eszközkiépítési szolgáltatás beállításának oktatóanyagával. 

> [!div class="nextstepaction"]
> [Azure IoT Hub eszközkiépítési szolgáltatás oktatóanyagai](./tutorial-set-up-cloud.md)
