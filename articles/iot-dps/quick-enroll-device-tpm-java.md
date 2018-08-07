---
title: TPM-eszköz regisztrációja az Azure Device Provisioning Service-be a Java használatával | Microsoft Docs
description: Azure rövid útmutató – TPM-eszköz regisztrációja az Azure IoT Hub Device Provisioning Service-be a Java szolgáltatásoldali SDK-val
author: wesmc7777
ms.author: wesmc
ms.date: 12/20/2017
ms.topic: quickstatrt
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 60b46a4fc55f66e80d40305c03b626c0b5a49b64
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528609"
---
# <a name="enroll-tpm-device-to-iot-hub-device-provisioning-service-using-java-service-sdk"></a>TPM-eszköz regisztrációja az IoT Hub Device Provisioning Service-be a Java szolgáltatásoldali SDK-val

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]


Ezek a lépések bemutatják, hogyan regisztrálhat szimulált TPM-eszközöket programozott módon az Azure IoT Hub Device Provisioning Service-be a [Java szolgáltatásoldali SDK](https://azure.github.io/azure-iot-sdk-java/service/) és egy Java-mintaalkalmazás segítségével. Bár a Java szolgáltatásoldali SDK Windows és Linux rendszerű gépeken is működik, ez a cikk egy Windows rendszerű fejlesztési számítógépet használ a regisztrációs folyamat bemutatására.

A folytatás előtt [állítsa be az IoT Hub Device Provisioning Service-t az Azure Portallal](./quick-setup-auto-provision.md), valamint [szimulálja a TPM-eszközt](quick-create-simulated-device.md#simulatetpm).

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése 

1. Győződjön meg arról, hogy a [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) telepítve van a gépén. 

2. Állítson be környezeti változókat a Java-telepítéshez. A `PATH` változónak tartalmaznia kell a *jdk1.8.x\bin* könyvtár teljes elérési útját. Ha ez az első Java-telepítés a számítógépen, hozzon létre egy új, `JAVA_HOME` nevű környezeti változót, amely a *jdk1.8.x* könyvtár teljes elérési útjára mutat. Windows rendszerű gépeken ez a könyvtár általában a *C:\\Program Files\\Java\\* mappában található. Környezeti változók létrehozásához és szerkesztéséhez keressen rá a **Rendszerkörnyezeti változók szerkesztése** kifejezésre a Windows rendszerű gép **Vezérlőpultján**. 

  Az alábbi parancs parancsablakban való futtatásával ellenőrizheti, hogy sikerült-e beállítani a Javát a számítógépen:

    ```cmd\sh
    java -version
    ```

3. Töltse le a [Maven 3](https://maven.apache.org/download.cgi)-at a számítógépre, és bontsa ki. 

4. Szerkessze a `PATH` környezeti változót, hogy az *apache-maven-3.x.x\\bin* mappára mutasson abban a mappában, amelybe a Mavent kibontotta. Az alábbi parancs parancsablakban való futtatásával ellenőrizheti, hogy sikeres volt-e a Maven telepítése:

    ```cmd\sh
    mvn --version
    ```

5. Győződjön meg arról, hogy a [git](https://git-scm.com/download/) telepítve van a gépen, és hozzá van adva a `PATH` környezeti változóhoz. 


<a id="javasample"></a>

## <a name="download-and-modify-the-java-sample-code"></a>A Java-mintakód letöltése és módosítása

Ez a szakasz bemutatja, hogyan adhatja hozzá a TPM-eszköz kiépítési adatait a mintakódhoz. 

1. Nyisson meg egy parancssort. Klónozza a GitHub-adattárat az eszközregisztráció kódmintájához a Java szolgáltatásoldali SDK használatával:
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. A letöltött forráskódban lépjen az **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_** mintamappára. Nyissa meg a **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentSample.java_** fájlt egy tetszőleges szövegszerkesztőben, és adja hozzá az alábbi adatokat:

    1. Adja hozzá a `[Provisioning Connection String]` karakterláncot a kiépítési szolgáltatáshoz a portálról:
        1. Az [Azure Portalon](https://portal.azure.com) lépjen a kiépítési szolgáltatásra. 
        2. Nyissa meg a **Megosztott elérési szabályzatok** panelt, és válasszon ki egy szabályzatot, amely rendelkezik az *EnrollmentWrite* engedéllyel.
        3. Másolja ki az **elsődleges kulcs kapcsolati sztringjét**. 

            ![A kiépítési kapcsolati sztring lekérése a portálról](./media/quick-enroll-device-tpm-java/provisioning-string.png)  

        4. A **_ServiceEnrollmentSample.java_** mintakódfájlban cserélje le a `[Provisioning Connection String]` sztringet az **elsődleges kulcs kapcsolati sztringjére**.
    
            ```Java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    2. Adja meg a TPM-eszköz adatait:
        1. Kérje le a *Regisztrációs azonosítót* és a *TPM ellenőrzőkulcsát* egy TPM-eszközszimulációhoz a [TPM-eszköz szimulálása](quick-create-simulated-device.md#simulatetpm) című szakaszhoz vezető lépések alapján.
        2. Cserélje le a **_ServiceEnrollmentSample.java_** mintakódfájl `[RegistrationId]` és `[TPM Endorsement Key]` értékét az előző lépés kimenetéből származó **_Regisztrációs azonosító_** és **_Ellenőrzőkulcs_** értékekre:
        
            ```Java
            private static final String REGISTRATION_ID = "[RegistrationId]";
            private static final String TPM_ENDORSEMENT_KEY = "[TPM Endorsement Key]";
            ```

    3. Ha szeretné, a kiépítési szolgáltatást a mintakódon keresztül is konfigurálhatja:
        - A konfiguráció mintához való hozzáadásához kövesse az alábbi lépéseket:
            1. Az [Azure Portalon](https://portal.azure.com) lépjen a kiépítési szolgáltatáshoz csatolt IoT Hubra. Nyissa meg a hubhoz tartozó **Áttekintés** lapot, és másolja ki az **Eszköznév** értékét. Rendelje hozzá ezt az **Eszköznév** értéket az *IOTHUB_HOST_NAME* paraméterhez.
                ```Java
                private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
                ```
            2. Rendeljen egy könnyen megjegyezhető nevet a *DEVICE_ID* paraméterhez, és a *PROVISIONING_STATUS* paraméternél tartsa meg az alapértelmezett *ENABLED* értéket. 
    
        - VAGY, ha úgy dönt, hogy nem konfigurálja a kiépítési szolgáltatást, tegye megjegyzésbe vagy törölje a _ServiceEnrollmentSample.java_ fájl alábbi utasításait:
            ```Java
            // The following parameters are optional. Remove it if you don't need.
            individualEnrollment.setDeviceId(DEVICE_ID);
            individualEnrollment.setIotHubHostName(IOTHUB_HOST_NAME);
            individualEnrollment.setProvisioningStatus(PROVISIONING_STATUS);
            ```

    4. Tanulmányozza a mintakódot, amely egy egyéni TPM-eszközregisztrációt hoz létre, frissít, kérdez le és töröl. Ha ellenőrizni szeretné a regisztráció sikerességét a portálon, ideiglenesen tegye megjegyzésbe a _ServiceEnrollmentSample.java_ fájl végén található alábbi kódsorokat:
    
        ```Java
        // *********************************** Delete info of individualEnrollment ************************************
        System.out.println("\nDelete the individualEnrollment...");
        provisioningServiceClient.deleteIndividualEnrollment(REGISTRATION_ID);
        ```

    5. Mentse a _ServiceEnrollmentSample.java_ fájlt.

<a id="runjavasample"></a>

## <a name="build-and-run-the-java-sample-code"></a>A Java-mintakód létrehozása és futtatása

1. Nyisson meg egy parancsablakot, és lépjen az **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_** mappára.

2. Hozza létre a mintakódot a következő paranccsal:

    ```cmd\sh
    mvn install -DskipTests
    ```

   Ez a parancs letölti a [`com.microsoft.azure.sdk.iot.provisioning.service`](https://www.mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) Maven-csomagot a számítógépre. A csomag tartalmazza a Java szolgáltatásoldali SDK bináris fájljait, amelyeket a mintakódnak létre kell hoznia. 

3. Futtassa a mintát a következő parancsokkal a parancsablakban:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-sample-{version}-with-deps.jar
    ```

4. A sikeres regisztráció érdekében kísérje figyelemmel a kimeneti ablakot. 

5. Az Azure Portalon lépjen a kiépítési szolgáltatásra. Kattintson a **Regisztrációk kezelése** lehetőségre, és válassza az **Egyéni regisztrációk** lapot. Figyelje meg, hogy megjelenik a szimulált TPM-eszköz *Regisztrációs azonosítója*. 

    ![Sikeres TPM-regisztráció ellenőrzése a portálon](./media/quick-enroll-device-tpm-java/verify-tpm-enrollment.png)  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha azt tervezi, hogy részletesebben is áttekinti a Java-szolgáltatásmintát, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti a rövid útmutatóhoz létrehozott összes erőforrást.

1. Zárja be a Java-minta kimeneti ablakát a gépen.
1. Zárja be a TPM-szimulátor ablakát, amely a TPM-eszköz szimulálásához jött létre.
1. Lépjen az eszközkiépítési szolgáltatásra az Azure Portalon, kattintson a **Regisztrációk kezelése** lehetőségre, majd válassza az **Egyéni regisztrációk** lapot. Válassza ki a rövid útmutató segítségével regisztrált eszköz *Regisztrációs azonosítóját*, majd kattintson a panel tetején lévő **Törlés** gombra. 

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban regisztrált egy szimulált TPM-eszközt az eszközkiépítési szolgáltatásba. Ha mélyebben szeretné megismerni az eszközkiépítést, folytassa az Azure Portalon az eszközkiépítési szolgáltatás beállításának oktatóanyagával. 

> [!div class="nextstepaction"]
> [Azure IoT Hub eszközkiépítési szolgáltatás oktatóanyagai](./tutorial-set-up-cloud.md)
