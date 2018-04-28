---
title: Szimulált X.509-eszköz kiépítése Azure IoT Hubra Java használatával | Microsoft Docs
description: Azure rövid útmutató – Szimulált X.509-eszköz létrehozása és kiépítése az IoT Hub Device Provisioning Service-hez készült Java eszközoldali SDK-val
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-masebo;bryanla
ms.date: 04/09/2018
ms.topic: quickstart
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 72460e19d202b79369844db6fea24f2914c8bbbe
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Szimulált X.509-eszköz létrehozása és kiépítése az IoT Hub Device Provisioning Service-hez készült Java eszközoldali SDK-val
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Ezek a lépések bemutatják, hogyan hozhat létre szimulált X.509-eszközt egy Windows operációs rendszert futtató fejlesztői gépen, és hogyan használhat egy kódmintát, hogy ezt a szimulált eszközt összekösse az eszközkiépítési szolgáltatással és az IoT Hubbal. 

Ha nem ismeri az automatikus kiépítés folyamatát, olvassa el [az automatikus kiépítés alapfogalmait](concepts-auto-provisioning.md) ismertető cikket. Emellett a folytatás előtt végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) című cikk lépéseit. 

## <a name="prepare-the-environment"></a>A környezet előkészítése 

1. Győződjön meg arról, hogy a [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) telepítve van a gépén.

2. Töltse le és telepítse a [Mavent](https://maven.apache.org/install.html).

3. Győződjön meg róla, hogy a Git telepítve van a gépen, és hogy hozzá lett adva a parancsablakból elérhető környezeti változókhoz. A [Software Freedom Conservancy's Git ügyfél eszközeiben](https://git-scm.com/download/) találja a telepíteni kívánt `git` eszközök legújabb verzióját, amely tartalmazza a **Git Bash** eszközt, azt a parancssori alkalmazást, amellyel kommunikálhat a helyi Git-adattárral. 

4. Nyisson meg egy parancssort. Klónozza a GitHub-adattárat az eszközszimuláció kódmintájához:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
5. Lépjen az `azure-iot-sdk-`java` gyökérmappájába, és futtasson buildelést a projekten az összes szükséges csomag letöltéséhez.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```
6. Lépjen a tanúsítványt létrehozó projekthez, és építse ki a projektet. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Önaláírt X.509-eszköztanúsítvány és egyéni regisztrációs bejegyzés létrehozása

1. A korábban megnyitott parancssorablakban navigáljon a `target` mappába, és futtassa az előző lépésben létrehozott JAR-fájlt.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

2. A **Do you want to input common name** (Szeretne megadni köznapi nevet?) kérdésnél adja meg az _N_ értéket. Másolja a vágólapra a `Client Cert` kimenetét a *-----BEGIN CERTIFICATE-----* sortól kezdődően az *-----END CERTIFICATE-----* sorig.

   ![Egyénitanúsítvány-készítő](./media/java-quick-create-simulated-device-x509/individual.png)

3. Hozzon létre egy **_X509individual.pem_** nevű fájlt a Windows rendszerű gépén, nyissa meg egy tetszőleges szövegszerkesztőben, és másolja bele a vágólapra kimásolt szöveget. Mentse a fájlt, és zárja be a szerkesztőt.

4. A parancssorban a **Do you want to input Verification Code** (Szeretne megadni ellenőrző kódot?) kérdésnél adja meg az _N_ értéket, és hagyja megnyitva a program kimenetét, hogy az útmutató későbbi részében hivatkozhasson rá. Később másolnia kell a `Client Cert` és a `Client Cert Private Key` értéket, melyeket a következő szakaszban fog használni.

5. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), a bal oldali menüben kattintson a **Minden erőforrás** gombra, és nyissa meg a Device Provisioning Service-példányt.

6. Az eszközkiépítési szolgáltatás összefoglalás panelén válassza a **Beléptetések kezelése** lehetőséget. Válassza az **Egyéni beléptetések** fület, és kattintson a felül lévő **Hozzáadás** gombra. 

7. A **Beléptetési listabejegyzés hozzáadása** panelen adja meg a következő információkat:
    - Válassza az **X.509** elemet az identitás igazolási *Mechanizmusaként*.
    - Az *Elsődleges tanúsítványfájl (.pem vagy .cer)* területen kattintson a *Fájl kiválasztása* lehetőségre, és válassza ki az előző lépésekben létrehozott **X509individual.pem** tanúsítványfájlt.  
    - Ha kívánja, megadhatja az alábbi információkat is:
      - Válassza ki a kiépítési szolgáltatáshoz kapcsolódó egyik IoT hubot.
      - Adjon meg egy egyedi eszközazonosítót. Ne használjon bizalmas adatokat az eszköz elnevezésekor. 
      - Frissítse az **Eszköz kezdeti ikerállapotát** az eszköz kívánt kezdeti konfigurációjával.
   - Ha végzett, kattintson a **Mentés** gombra. 

    [![X.509-igazolás egyéni beléptetésének hozzáadása a portálon](./media/quick-create-simulated-device-x509-csharp/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

     Sikeres regisztrációt követően az X.509-eszköz **microsoftriotcore** azonosítóval megjelenik a *Regisztrációs azonosító* oszlopban, az *Egyéni regisztrációk* lapon. 



## <a name="simulate-the-device"></a>Az eszköz szimulálása

1. Az eszközkiépítési szolgáltatás összefoglaló panelén válassza az **Áttekintés** lehetőséget, majd jegyezze fel az _Azonosító hatóköre_ és a _Kiépítési szolgáltatás globális végpontja_ értékét.

    ![Szolgáltatás adatai](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Nyisson meg egy parancssort. Lépjen a Java SDK adattárban a mintaprojekt mappájára.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

3. Adja meg a kiépítési szolgáltatás és az X.509-identitás adatait a kódban. Ezt a rendszer a szimulált eszköz igazolásához használja az automatikus kiépítés során, mielőtt regisztrálná az eszközt:

   - Szerkessze az `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` fájlt, hogy az tartalmazza a korábban feljegyzett _Azonosító hatóköre_ és _Kiépítési szolgáltatás globális végpontja_ értékeket. Adja hozzá a _Client Cert_ és a _Client Cert Private Key_ értéket is az előző szakaszban feljegyzett adatok alapján.

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   - A tanúsítvány és a titkos kulcs másolásakor és beillesztésekor használja a következő formátumot:
        
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

4. Hozza létre a mintát. Lépjen a `target` mappába, és futtassa a létrehozott jar-fájlt.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

5. Az Azure Portalon lépjen a kiépítési szolgáltatáshoz csatolt IoT hubhoz, és nyissa meg a **Device Explorer** (Eszközkereső) panelt. Ha sikeresen kiépítette a szimulált X.509-eszközt a hubon, az eszköz azonosítója megjelenik a **Device Explorer** panelen, a hozzá tartozó *ÁLLAPOT* pedig **engedélyezett** lesz.  Ha már a minta eszközalkalmazás futtatása előtt megnyitotta a panelt, akkor lehet, hogy rá kell kattintania a fenti **Frissítés** gombra. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/java-quick-create-simulated-device-x509/hub-registration.png) 

> [!NOTE]
> Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja az eszközügyfél minta használatát és megismerését, akkor ne törölje a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti a rövid útmutatóhoz létrehozott összes erőforrást.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
2. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az eszközkiépítési szolgáltatást. Nyissa meg a szolgáltatás **Regisztrációk kezelése** paneljét, majd kattintson az **Egyéni regisztrációk** lapra. Válassza ki a rövid útmutatóban regisztrált eszköz *REGISZTRÁCIÓS AZONOSÍTÓJÁT*, majd kattintson a felül található **Törlés** gombra. 
3. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az IoT Hubot. Nyissa meg a hub **IoT-eszközök** paneljét, válassza ki a rövid útmutatóban regisztrált eszköz *ESZKÖZAZONOSÍTÓJÁT*, majd kattintson a felül található **Törlés** gombra.


## <a name="next-steps"></a>További lépések

Ebben a gyors útmutatóban létrehozott egy szimulált X.509-eszközt a Windows rendszerű számítógépén. Ezután konfigurálta az eszköz beléptetését az Azure IoT Hub Device Provisioning szolgáltatásban, majd automatikusan kiépítette az eszközt az IoT hubján. Ha szeretné megismerni az X.509-eszköz programozott regisztrációjának folyamatát, lépjen tovább az X.509-eszközök programozott regisztrációjának rövid útmutatójára. 

> [!div class="nextstepaction"]
> [Azure rövid útmutató – X.509-eszközök regisztrációja az Azure IoT Hub Device Provisioning Service-be](quick-enroll-device-x509-java.md)
