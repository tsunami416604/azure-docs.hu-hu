---
title: Szimulált X.509-es eszköz kiépítése az Azure IoT Hubba a Node.js használatával
description: Szimulált X.509-es eszközt hozhat létre és létesíthet a Node.js eszköz SDK-val az Azure IoT Hub-eszközkiépítési szolgáltatáshoz (DPS). Ez a rövid útmutató az egyéni regisztrációkat használja.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 746f4adbf616f95c21874d7c1c48881f88c38d34
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77605400"
---
# <a name="quickstart-create-and-provision-an-x509-simulated-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Rövid útmutató: X.509 szimulált eszköz létrehozása és kiépítése a Node.js eszköz SDK-j használatával az IoT Hub-eszközkiépítési szolgáltatáshoz

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Ebben a rövid útmutatóban egy szimulált X.509-es eszközt hoz létre Windows rendszerű számítógépen. Az eszközminta Node.js kód segítségével csatlakoztassa ezt a szimulált eszközt az IoT hub segítségével egyéni regisztráció az eszközkiépítési szolgáltatás (DPS).

## <a name="prerequisites"></a>Előfeltételek

- Az [automatikus kiépítés fogalmainak áttekintése](concepts-auto-provisioning.md).
- Az [IoT Hub-eszközkiépítési szolgáltatás beállítása az Azure Portalon.](./quick-setup-auto-provision.md)
- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Node.js v4.0+](https://nodejs.org).
- [Git](https://git-scm.com/download/).
- [OpenSSL](https://www.openssl.org/).

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>A környezet előkészítése 

1. A folytatás előtt végezze el az [IoT Hub Device Provisioning Service Azure Portallal való beállítását](./quick-setup-auto-provision.md) ismertető cikk lépéseit.

2. Győződjön meg arról, hogy a [Node.js 4.0-s vagy újabb verziója](https://nodejs.org) telepítve van a gépén.

3. Győződjön meg arról, hogy a [Git](https://git-scm.com/download/) telepítve van a számítógépen, és hozzá van adva a parancsablak számára elérhető környezeti változókhoz. 

4. Győződjön meg arról, hogy az [OpenSSL](https://www.openssl.org/) telepítve van a gépen, és a parancsablakból elérhető környezeti változókhoz van adva. Ez a kódtár létrehozható és telepíthető valamilyen forrásból, vagy letölthető és telepíthető [külső féltől](https://wiki.openssl.org/index.php/Binaries), például [innen](https://sourceforge.net/projects/openssl/). 

    > [!NOTE]
    > Ha már létrehozta az X.509 _fő-_, _köztes_, és/vagy _levéltanúsítványát_, kihagyhatja ezt a lépést és a tanúsítvány létrehozásával kapcsolatos további lépéseket.
    >

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Önaláírt X.509-eszköztanúsítvány és egyéni regisztrációs bejegyzés létrehozása

Ebben a szakaszban egy önaláírt X.509-tanúsítványt fogunk használni. Fontos szem előtt tartani a következőket:

* Az önaláírt tanúsítványok csak tesztelési célokra alkalmasak, és nem javasolt őket éles környezetben alkalmazni.
* Az önaláírt tanúsítványok alapértelmezett lejárati ideje 1 év.

Az [Azure IoT SDK for Node.js](https://github.com/Azure/azure-iot-sdk-node.git) mintakódját használja majd a szimulált eszköz egyéni regisztrációs bejegyzéséhez használandó tanúsítvány létrehozásához.

Az Azure IoT Device Provisioning Service kétféle típusú regisztrációt támogat:

- [Regisztrációs csoportok](concepts-service.md#enrollment-group): Több kapcsolódó eszköz regisztrálásához.
- [Egyéni regisztrációk:](concepts-service.md#individual-enrollment)Egyetlen eszköz regisztrálására használható.

Ez a cikk bemutatja az egyes regisztrációk.

1. Nyisson meg egy parancssort. Klónozza a GitHub-adattárat a kódmintákhoz:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```

2. Lépjen a tanúsítványt létrehozó szkripthez, és állítsa össze a projektet. 

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

3. A saját _tanúsítványnevét_ használva hozzon létre egy X.509 _levéltanúsítványt_ a szkript futtatásával. A levéltanúsítvány köznapi neve lesz a [regisztrációs azonosító](https://docs.microsoft.com/azure/iot-dps/concepts-device#registration-id), ezért csak kisbetűket, alfanumerikus karaktereket és kötőjeleket használjon.

    ```cmd/sh
    node create_test_cert.js device {certificate-name}
    ```

4. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)válassza a bal oldali menü **Minden erőforrás** gombját, és nyissa meg az Eszközkiépítési szolgáltatás példányát.

5. Az Eszközkiépítési szolgáltatás menüben válassza **a Regisztrációk kezelése**lehetőséget. Válassza az **Egyéni regisztrációk** lapot, és felül válassza az **Egyéni beléptetés hozzáadása** gombot. 

6. A **Beiktatás hozzáadása** panelen adja meg a következő adatokat:
   - Válassza az **X.509** elemet az identitás igazolási *Mechanizmusaként*.
   - Az *Elsődleges tanúsítvány .pem vagy .cer fájl*ban válassza *a Fájl kijelöléséhez* válassza a **{certificate-name}_cert.pem** tanúsítványfájlt.  
   - Ha kívánja, megadhatja az alábbi információkat is:
     - Válassza ki a kiépítési szolgáltatáshoz kapcsolódó egyik IoT hubot.
     - Adjon meg egy egyedi eszközazonosítót. Ne használjon bizalmas adatokat az eszköz elnevezésekor. 
     - Frissítse az **Eszköz kezdeti ikerállapotát** az eszköz kívánt kezdeti konfigurációjával.
     - Miután elkészült, nyomja meg a **Mentés** gombot. 

     [![Egyéni regisztráció hozzáadása az X.509-es tanúsítványhoz a portálon](./media/quick-create-simulated-device-x509-node/device-enrollment.png)](./media/quick-create-simulated-device-x509-node/device-enrollment.png#lightbox)

     A sikeres igényléskor az X.509-es eszköz **{tanúsítványnév}** néven jelenik meg az *Egyéni regisztrációk* lap *Regisztrációs azonosító* oszlopában.

## <a name="simulate-the-device"></a>Az eszköz szimulálása

Az [Azure IoT Hub Node.js eszközoldali SDK](https://github.com/Azure/azure-iot-sdk-node) segítségével könnyedén szimulálhatóak eszközök. További információkért tekintse meg az [eszközökkel kapcsolatos alapelveket ismertető](https://docs.microsoft.com/azure/iot-dps/concepts-device) témakört.

1. Az Azure Portalon válassza ki az **áttekintő** panelt az eszközkiépítési szolgáltatáshoz, és vegye figyelembe a **_GLobal-eszköz végpont_** és **_az id hatókör értékeit._**

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509-node/extract-dps-endpoints.png) 

2. Másolja a _tanúsítványt_ és a _kulcsot_ a mintamappába.

    ```cmd/sh
    copy .\{certificate-name}_cert.pem ..\device\samples\{certificate-name}_cert.pem
    copy .\{certificate-name}_key.pem ..\device\samples\{certificate-name}_key.pem
    ```

3. Keresse meg az eszköz tesztszkriptjét, és állítsa össze a projektet. 

    ```cmd/sh
    cd ..\device\samples
    npm install
    ```

4. Szerkessze a **register\_x509.js** fájlt. A következő módosítások elvégzése után mentse a fájlt.
    - A `provisioning host` elemet cserélje le a fenti **1. lépésben** feljegyzett**_globális eszközvégpontra_**.
    - Cserélje `id scope` le **_a_** fenti **1.** 
    - Cserélje `registration id` le az előző szakaszban feljegyzett **_regisztrációs azonosítóra._**
    - A `cert filename` és a `key filename` elemet cserélje le a fenti **2. lépésben** másolt fájlokra. 

5. Futtassa a szkriptet, és ellenőrizze, hogy az eszköz sikeresen lett-e kiépítve.

    ```cmd/sh
    node register_x509.js
    ```   

6. A portálon keresse meg a kiépítési szolgáltatáshoz kapcsolódó IoT-központot, és nyissa meg az **IoT-eszközök** panelt. A szimulált X.509-es eszköz hubra történő sikeres kiépítésekor az eszközazonosítója megjelenik az **IoT-eszközök** paneljén, *a STATUS* pedig **engedélyezve**van. Előfordulhat, hogy meg kell **nyomnia** a frissítés gombot a tetején, ha már kinyitotta a panelt a mintaeszköz-alkalmazás futtatása előtt. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-simulated-device-x509-node/hubregistration.png) 

    Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](../iot-hub/iot-hub-devguide-device-twins.md).


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát, és vizsgálja meg az eszköz ügyfél minta, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem tervezi a folytatást, az alábbi lépésekkel törölje az összes olyan erőforrást, amelyet ez a rövid útmutató hozott létre.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
2. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** t, majd válassza ki az Eszközkiépítési szolgáltatást. Nyissa meg a **szolgáltatás Regisztrációk kezelése** panelt, majd válassza az Egyéni **regisztrációk** lapot. *REGISTRATION ID* **Delete** 
3. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** lehetőséget, majd válassza ki az IoT-központot. Nyissa meg az **IoT-eszközök** panelt a hubhoz, jelölje be a rövid útmutatóban regisztrált eszköz *eszközazonosítója* melletti jelölőnégyzetet, majd nyomja meg a **Törlés** gombot az ablaktábla tetején.


## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy szimulált X.509-es eszközt, és kiépítette azt az IoT hubba az Azure IoT Hub-eszközkiépítési szolgáltatás használatával a portálon. Az X.509-es eszköz programozott regisztrálásához folytassa az X.509-es eszközök programozott regisztrációjának rövid útmutatójával. 

> [!div class="nextstepaction"]
> [Azure-gyorsútmutató – X.509-es eszközök regisztrálása az Azure IoT Hub-eszközkiépítési szolgáltatásra](quick-enroll-device-x509-node.md)
