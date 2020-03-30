---
title: TPM-eszköz regisztrálása az Azure-eszközkiépítési szolgáltatásba Java használatával
description: Gyorsútmutató – TPM-eszköz regisztrálása az Azure IoT Hub-eszközlétesítési szolgáltatás (DPS) java szolgáltatás SDK használatával. Ez a rövid útmutató egyéni regisztrációkat használ.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: c199d5be4c103c80a6fcc126af70f48367909f64
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241695"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-java-service-sdk"></a>Rövid útmutató: TPM-eszköz igénylése az IoT Hub-eszközkiépítési szolgáltatásba a Java Service SDK használatával

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

Ebben a rövid útmutatóban programozott módon egyéni regisztrációt hozhat létre egy szimulált TPM-eszközhöz az Azure IoT Hub-eszközkiépítési szolgáltatásban a Java Service SDK használatával egy minta Java-alkalmazás segítségével.

## <a name="prerequisites"></a>Előfeltételek

- Az [IoT Hub-eszközkiépítési szolgáltatás beállítása az Azure Portalon.](./quick-setup-auto-provision.md)
- Az [Olvasás kriptográfiai kulcsok befejezése a TPM-eszközről](quick-create-simulated-device.md#simulatetpm).
- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Java SE Fejlesztői Készlet 8](https://aka.ms/azure-jdks). Ez a rövid útmutató az alábbi [Java Service SDK-t](https://azure.github.io/azure-iot-sdk-java/service/) telepíti. Úgy működik, mind a Windows és a Linux. Ez a rövid útmutató a Windows rendszert használja.
- [Maven 3](https://maven.apache.org/download.cgi).
- [Git](https://git-scm.com/download/).

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése 

1. Győződjön meg arról, hogy a [Java SE Development Kit 8](https://aka.ms/azure-jdks) telepítve van a gépén. 

2. Állítson be környezeti változókat a Java-telepítéshez. A `PATH` változónak tartalmaznia kell a *jdk1.8.x\bin* könyvtár teljes elérési útját. Ha ez az első Java-telepítés a számítógépen, hozzon létre egy új, `JAVA_HOME` nevű környezeti változót, amely a *jdk1.8.x* könyvtár teljes elérési útjára mutat. Windows rendszerű gépeken ez a könyvtár a *C:\\Program Files\\Java\\* mappában található. Környezeti változók létrehozásához és szerkesztéséhez keressen rá **A rendszer környezeti változóinak módosítása** kifejezésre a Windows rendszerű gép **Vezérlőpultján**. 

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

1. Nyisson meg egy parancssort. Klónozza a GitHub-tárta eszközregisztrációs kódmintáját a [Java Service SDK](https://azure.github.io/azure-iot-sdk-java/service/)használatával:
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. A letöltött forráskódban lépjen az **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_** mintamappára. Nyissa meg a **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentSample.java_** fájlt egy tetszőleges szövegszerkesztőben, és adja hozzá az alábbi adatokat:

   1. Adja hozzá a `[Provisioning Connection String]` karakterláncot a kiépítési szolgáltatáshoz a portálról:
       1. Keresse meg a létesítési szolgáltatást az [Azure Portalon.](https://portal.azure.com) 
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

   4. Tanulmányozza a mintakódot, Ez a kód egyéni TPM-eszközregisztrációt hoz létre, frissít, kérdez le és töröl. Ha ellenőrizni szeretné a regisztráció sikerességét a portálon, ideiglenesen tegye megjegyzésbe a _ServiceEnrollmentSample.java_ fájl végén található alábbi kódsorokat:
    
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

   Ez a parancs letölti [`com.microsoft.azure.sdk.iot.provisioning.service`](https://www.mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) a Maven csomagot a gépre. Ez a csomag tartalmazza a [Java Service SDK](https://azure.github.io/azure-iot-sdk-java/service/)bináris fájljait, amelyeket a mintakódnak létre kell tennie. 

3. Futtassa a mintát a következő parancsokkal a parancsablakban:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-sample-{version}-with-deps.jar
    ```

4. A sikeres regisztráció érdekében kísérje figyelemmel a kimeneti ablakot. 

5. Az Azure Portalon lépjen a kiépítési szolgáltatásra. Válassza **a Regisztrációk kezelése**lehetőséget, és válassza *Registration ID* az **Egyéni beléptetések** lapot. 

    ![Sikeres TPM-regisztráció ellenőrzése a portálon](./media/quick-enroll-device-tpm-java/verify-tpm-enrollment.png)  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha azt tervezi, hogy vizsgálja meg a Java szolgáltatás minta, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem tervezi a folytatást, az alábbi lépésekkel törölje az összes olyan erőforrást, amelyet ez a rövid útmutató hozott létre.

1. Zárja be a Java-minta kimeneti ablakát a gépen.
1. Zárja be a TPM-szimulátor ablakát, amely a TPM-eszköz szimulálásához jött létre.
1. Keresse meg az Eszközkiépítési szolgáltatást az Azure Portalon, válassza a **Regisztrációk kezelése**lehetőséget, majd *Registration ID* jelölje be az **Egyéni beléptetések** lapot. **Delete**

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban egy szimulált TPM-eszközt regisztrált az eszközkiépítési szolgáltatásra. Ha mélyebben szeretné megismerni az eszközkiépítést, folytassa az Azure Portalon az eszközkiépítési szolgáltatás beállításának oktatóanyagával. 

> [!div class="nextstepaction"]
> [Azure IoT Hub eszközkiépítési szolgáltatás oktatóanyagai](./tutorial-set-up-cloud.md)
