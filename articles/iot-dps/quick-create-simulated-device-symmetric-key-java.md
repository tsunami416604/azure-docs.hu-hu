---
title: Gyorsútmutató – Szimmetrikus kulcs használata szimulált eszköz kiépítése az Azure IoT Hubba Java használatával
description: Ebben a rövid útmutatóban a Java-eszköz SDK-t fogja használni egy szimulált eszköz létrehozásához, amely szimmetrikus kulcsot használ az Azure IoT Hub eszközkiépítési szolgáltatással (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 01/30/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: aaa1a4423363255536db7d53a1f8f8fa9ba686ff
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76941401"
---
# <a name="quickstart-provision-a-simulated-device-with-symmetric-keys"></a>Rövid útmutató: Szimmetrikus kulcs kiosztása szimulált eszköz számára

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és futtathat eszközszimulátort a Windows rendszerű fejlesztői gépeken. Ezt a szimulált eszközt úgy fogja konfigurálni, hogy egy szimmetrikus kulcsot használjon az eszközkiépítési szolgáltatás (DPS) példányával való hitelesítéshez, és egy IoT hubhoz legyen rendelve. A Rendszer a [Microsoft Azure IoT Java SDK-k](https://github.com/Azure/azure-iot-sdk-java) jától származó mintakódot használ a kiépítést kezdeményező eszköz rendszerindítási sorrendjének szimulálására. Az eszköz egy DPS-szolgáltatáspéldánysal való egyéni regisztráció alapján lesz felismerve, és egy IoT-központhoz rendelve.

Bár ez a cikk bemutatja az egyéni beléptetési kiépítést, használhatja a regisztrációs csoportokat. A regisztrációs csoportok használata kor van néhány különbség. Például egy származtatott eszközkulcsot kell használnia, amely egyedi regisztrációs azonosítóval van elhasználva az eszközhöz. Bár a szimmetrikus kulcsot használó regisztrációs csoportok nem csak örökölt eszközök esetében használhatóak, a [Szimmetrikus kulcsok használata örökölt eszközök kiépítéséhez](how-to-legacy-device-symm-key.md) című cikk példája jól szemlélteti a regisztrációs csoportok használatát. További információért lásd: [Csoportos beléptetés használata szimmetrikus kulcsú igazolásnál](concepts-symmetric-key-attestation.md#group-enrollments)

Amennyiben nem ismeri az automatikus kiépítés folyamatát, olvassa el [az automatikus kiépítés alapfogalmait](concepts-auto-provisioning.md) ismertető cikket. 

A rövid útmutató folytatása előtt mindenképpen végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) szakasz lépéseit. Ez a rövid útmutató feltételezi, hogy korábban már létrehozott egy Device Provisioning Service-példányt.

A cikk során egy Windows-alapú munkaállomást fogunk használni. Azonban az eljárások Linux esetében is alkalmazhatóak. Linux-munkaállomást használó példát a [Több bérlős regisztráció](how-to-provision-multitenant.md) című cikkben talál.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

* Győződjön meg arról, hogy a [Java SE Development Kit 8](https://aka.ms/azure-jdks) vagy újabb készlet telepítve van a számítógépen.

* Töltse le és telepítse a [Mavent](https://maven.apache.org/install.html).

* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.

<a id="setupdevbox"></a>

## <a name="prepare-the-java-sdk-environment"></a>A Java SDK-környezet előkészítése 

1. Győződjön meg róla, hogy a Git telepítve van a gépen, és hogy hozzá lett adva a parancsablakból elérhető környezeti változókhoz. A [Software Freedom Conservancy's Git ügyfél eszközeiben](https://git-scm.com/download/) találja a telepíteni kívánt `git` eszközök legújabb verzióját, amely tartalmazza a **Git Bash** eszközt, azt a parancssori alkalmazást, amellyel kommunikálhat a helyi Git-adattárral. 

2. Nyisson meg egy parancssort. Klónozza a GitHub-adattárat az eszközszimuláció kódmintájához:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
3. Keresse meg `azure-iot-sdk-java` a gyökérkönyvtárat, és építse fel a projektet az összes szükséges csomag letöltéséhez.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

## <a name="create-a-device-enrollment"></a>Eszközregisztráció létrehozása

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)válassza a bal oldali menü **Minden erőforrás** gombját, és nyissa meg az Eszközkiépítési szolgáltatás (DPS) példányát.

2. Válassza a **Regisztrációk kezelése** lapot, majd a lista tetején az **Egyéni beléptetés hozzáadása** gombot. 

3. A **Beiktatás hozzáadása** panelen adja meg az alábbi adatokat, és nyomja meg a **Mentés** gombot.

   - **Eljárás**: Identitásazonosító *eljárásnak* válassza a **Szimmetrikus kulcs** lehetőséget.

   - **Billentyűk automatikus generálása**: Jelölje be ezt a jelölőnégyzetet.

   - **Regisztrációs azonosító**: Adja meg a regisztráció azonosításra szolgáló Regisztrációs azonosítót. Csak a kisbetűs alfanumerikus karaktereket és a kötőjel karaktert használhatja. Például **symm-key-java-device-007**.

   - **IoT Hub-eszközazonosító**: Adjon meg egy eszközazonosítót. **Például, java-device-007**.

     ![Egyéni regisztráció hozzáadása szimmetrikus kulcsú igazoláshoz a Portalon](./media/quick-create-simulated-device-symm-key-java/create-individual-enrollment-java.png)

4. Miután mentette a regisztrációt, az **elsődleges kulcs** és a **másodlagos kulcs** jön létre, és hozzáadódik a regisztrációs bejegyzéshez. A szimmetrikus kulcseszköz-regisztráció **symm-key-java-device-007** néven jelenik meg az *Egyéni regisztrációk* lap *Regisztrációs azonosító* oszlopában. 

    Nyissa meg a regisztrációt, és másolja ki a generált **Elsődleges kulcsot**. Ezt a kulcsértéket és a **regisztrációs azonosítót** később fogja használni, amikor frissíti az eszköz Java-kódját.



<a id="firstbootsequence"></a>

## <a name="simulate-device-boot-sequence"></a>Eszközrendszerindítási sorrend szimulálása

Ebben a szakaszban frissíteni fogja az eszköz mintakódját, hogy az eszköz rendszerindítási sorrendje elküldésre kerüljön a DPS-példányba. Ez a rendszerindítási sorozat hatására az eszköz fellesz ismert, hitelesítve lesz, és a DPS-példányhoz kapcsolódó IoT hubhoz lesz hozzárendelve.

1. Az Eszközkiépítési szolgáltatás **menüben** válassza az Áttekintés lehetőséget, és jegyezze fel az _azonosító hatókörét_ és _a kiépítési szolgáltatás globális végpontját._

    ![Szolgáltatás adatai](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Nyissa meg a Java-eszköz mintakódját szerkesztésre. Az eszköz mintakódjának teljes elérési útja a következő:

    `azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningSymmetricKeySampleSample.java`

   - Adja hozzá az _azonosító hatókörés_ _a kiépítési szolgáltatás globális végpontja_ a DPS-példány. Az elsődleges szimmetrikus kulcsot és az egyéni regisztrációhoz kiválasztott regisztrációs azonosítót is tartalmazza. Mentse a módosításokat. 

      ```java
        private static final String SCOPE_ID = "[Your scope ID here]";
        private static final String GLOBAL_ENDPOINT = "[Your Provisioning Service Global Endpoint here]";
        private static final String SYMMETRIC_KEY = "[Enter your Symmetric Key here]";
        private static final String REGISTRATION_ID = "[Enter your Registration ID here]";
      ```

3. Nyisson meg egy parancssort az építéshez. Keresse meg a kiépítési minta projekt mappáját a Java SDK-tárház.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample
    ```

4. Készítse el a `target` mintát, majd keresse meg a mappát a létrehozott .jar fájl végrehajtásához.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-symmetrickey-sample-{version}-with-deps.jar
    ```

5. A várt kimenetnek a következőhöz hasonlóan kell kinéznie:

    ```cmd/sh
      Starting...
      Beginning setup.
      Waiting for Provisioning Service to register
      IotHUb Uri : <Your DPS Service Name>.azure-devices.net
      Device ID : java-device-007
      Sending message from device to IoT Hub...
      Press any key to exit...
      Message received! Response status: OK_EMPTY
    ```

6. Az Azure Portalon lépjen a kiépítési szolgáltatáshoz csatolt IoT hubhoz, és nyissa meg a **Device Explorer** (Eszközkereső) panelt. Miután sikeresen kiépítette a szimulált szimmetrikus kulcseszközt a hubra, az eszközazonosítója megjelenik az **Eszközkezelő** panelen, és a *STATUS is* **engedélyezve van.**  Előfordulhat, hogy meg kell **nyomnia** a frissítés gombot a tetején, ha már kinyitotta a panelt a mintaeszköz-alkalmazás futtatása előtt. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-simulated-device-symm-key-java/hubregistration-java.png) 

> [!NOTE]
> Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát, és vizsgálja meg az eszköz ügyfél minta, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem tervezi a folytatást, az alábbi lépésekkel törölje az összes olyan erőforrást, amelyet ez a rövid útmutató hozott létre.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
1. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** t, majd válassza ki az Eszközkiépítési szolgáltatást. Nyissa **meg a szolgáltatás regisztrációinak kezelése** lehetőséget, majd válassza az Egyéni *REGISTRATION ID* **Delete** **regisztrációk** lapot. 
1. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** lehetőséget, majd válassza ki az IoT-központot. Nyissa meg a hub **IoT-eszközeit,** jelölje be a rövid útmutatóban regisztrált eszköz *ESZKÖZazonosítója* melletti jelölőnégyzetet, majd nyomja meg a **Törlés** gombot az ablaktábla tetején.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy szimulált eszközt a Windows-gépen, és kiépítette azt az IoT hubra szimmetrikus kulccsal az Azure IoT Hub-eszközkiépítési szolgáltatással a portálon. Ha meg szeretné tudni, hogyan regisztrálhatja az eszközt programozott módon, folytassa az X.509-es eszközök programozott regisztrációjának rövid útmutatójával. 

> [!div class="nextstepaction"]
> [Azure-gyorsútmutató – X.509-es eszközök regisztrálása az Azure IoT Hub-eszközkiépítési szolgáltatásra](quick-enroll-device-x509-java.md)
