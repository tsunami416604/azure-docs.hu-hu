---
title: Örökölt eszközök kiépítése szimmetrikus kulcsokkal – Azure IoT Hub-eszközkiépítési szolgáltatás
description: Szimmetrikus kulcsok használata örökölt eszközök kiépítéséhez az eszközkiépítési szolgáltatás (DPS) példányával
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 4d1a92f3ebf32d2270eb77ec9c79fe860ba090e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75434714"
---
# <a name="how-to-provision-legacy-devices-using-symmetric-keys"></a>Az örökölt eszközök kiépítése szimmetrikus billentyűkkel

A sok örökölt eszközzel gyakori probléma, hogy gyakran egyetlen információból álló identitással rendelkeznek. Ezek az azonosító adatok általában MAC-cím vagy sorozatszám. Előfordulhat, hogy az örökölt eszközök nem rendelkeznek tanúsítvánnyal, TPM-mel vagy bármely más olyan biztonsági funkcióval, amely az eszköz biztonságos azonosítására használható. Az IT hub eszközkiépítési szolgáltatása szimmetrikus kulcsigazolást tartalmaz. A szimmetrikus kulcsigazolás sal azonosíthatja az eszköz alapú adatokat, például a MAC-címet vagy a sorozatszámot.

Ha könnyen telepíthet egy [hardveres biztonsági modult (HSM)](concepts-security.md#hardware-security-module) és egy tanúsítványt, akkor ez jobb megközelítés lehet az eszközök azonosításához és kiépítéséhez. Mivel ez a megközelítés lehetővé teheti, hogy megkerülje az összes eszközére telepített kód frissítését, és nem rendelkezik az eszközképbe ágyazott titkos kulcs.

Ez a cikk feltételezi, hogy sem a HSM, sem a tanúsítvány nem járható út. Azonban feltételezhető, hogy rendelkezik valamilyen módszerrel az eszközkód frissítéséhez az eszközkiépítési szolgáltatás használatához ezeknek az eszközöknek a kiépítéséhez. 

Ez a cikk azt is feltételezi, hogy az eszközfrissítés biztonságos környezetben történik, hogy megakadályozza a főcsoportkulcshoz vagy a származtatott eszközkulcshoz való jogosulatlan hozzáférést.

A cikk során egy Windows-alapú munkaállomást fogunk használni. Azonban az eljárások Linux esetében is alkalmazhatóak. Linux-munkaállomást használó példát a [Több bérlős regisztráció](how-to-provision-multitenant.md) című cikkben talál.

> [!NOTE]
> Az ebben a cikkben használt minta C. Van is egy [C# eszköz kiépítése szimmetrikus kulcs minta](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) áll rendelkezésre. A minta használatához töltse le vagy klónozza az [azure-iot-samples-csharp](https://github.com/Azure-Samples/azure-iot-samples-csharp) repository-t, és kövesse a mintakódban található in-line utasításokat. Az ebben a cikkben található utasításokat követve szimmetrikus kulcsregisztrációs csoportot hozhat létre a portál használatával, és megkeresheti a minta futtatásához szükséges azonosító hatókört és regisztrációs csoport elsődleges és másodlagos kulcsokat. Egyéni regisztrációkat is létrehozhat a minta használatával.

## <a name="overview"></a>Áttekintés

Minden eszközhöz egyedi regisztrációs azonosító t határoz meg az adott eszközt azonosító adatok alapján. Például a MAC-cím vagy egy sorozatszám.

Az eszközkiépítési szolgáltatással [szimmetrikus kulcsigazolást](concepts-symmetric-key-attestation.md) használó regisztrációs csoport jön létre. A beléptetési csoport tartalmazni fog egy csoportfőkulcsot. Ezt a főkulcsot használjuk az egyes egyedi regisztrációs azonosítók kivonatolására, hogy minden eszközhöz egyedi eszközkulcsot készítsen. Az eszköz fogja használni, hogy a származtatott eszközkulcs az egyedi regisztrációs azonosítót, hogy tanúsítsa az eszköz kiépítési szolgáltatás, és egy IoT hub hoz rendelt.

Az ebben a cikkben bemutatott eszközkód ugyanazt a mintát követi, mint a [Rövid útmutató: Szimulált eszköz kiépítése szimmetrikus kulcsokkal.](quick-create-simulated-device-symm-key.md) A kód szimulálja az eszközt az [Azure IoT C SDK-ból](https://github.com/Azure/azure-iot-sdk-c)vett minta használatával. A szimulált eszköz tanúsítja, hogy egy regisztrációs csoport helyett egy egyéni regisztráció, amint azt a rövid útmutató mutatja.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

* Az [IoT Hub-eszközkiépítési szolgáltatás beállítása az Azure Portal rövid útmutatójával.](./quick-setup-auto-provision.md)

A következő előfeltételek a Windows fejlesztői környezetben. Linux vagy macOS esetén tekintse meg a megfelelő szakaszt [a Fejlesztői környezet előkészítése az](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) SDK dokumentációban című témakörben.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 az ["Asztali fejlesztés C++"-os munkaterheléssel.](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) A Visual Studio 2015 és a Visual Studio 2017 is támogatott.

* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK fejlesztői környezet előkészítése

Ebben a szakaszban előkészítjük az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) build készítésére szolgáló fejlesztőkörnyezetet. 

Az SDK tartalmazza a szimulált eszköz mintakódját. A szimulált eszköz a beléptetést az rendszerindítási során fogja megkísérelni.

1. Töltse le a [CMake build rendszert](https://cmake.org/download/).

    Fontos, hogy a Visual Studio előfeltételei (Visual Studio és az „Asztali fejlesztés C++ használatával” számítási feladat) telepítve legyenek a gépen, **mielőtt** megkezdené a `CMake` telepítését. Ha az előfeltételek telepítve vannak, és ellenőrizte a letöltött fájlt, telepítse a CMake buildelési rendszert.

2. Keresse meg az SDK [legújabb kiadásának](https://github.com/Azure/azure-iot-sdk-c/releases/latest) címkenevét.

3. Nyisson meg egy parancssort vagy a Git Bash-felületet. Futtassa a következő parancsokat az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-tárház legújabb kiadásának klónozásához. Használja az előző lépésben található címkét `-b` a paraméter értékeként:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ez a művelet várhatóan több percig is eltarthat.

4. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. Futtassa a `azure-iot-sdk-c` következő parancsokat a könyvtárból:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Futtassa az alábbi parancsot, amely létrehozza az SDK fejlesztői ügyfélplatformra szabott verzióját. A szimulált eszközhöz tartozó Visual Studio-megoldás a `cmake` könyvtárban jön létre. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    Ha a `cmake` nem találja a C++ fordítóprogramot, a fenti parancs futtatása esetlegesen fordítási hibákat adhat vissza. Ilyen esetekben futtassa a parancsot a [Visual Studio parancssorából](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    A sikeres létrehozást követően a kimenet utolsó sorai a következőhöz hasonlóan néznek majd ki:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```


## <a name="create-a-symmetric-key-enrollment-group"></a>Szimmetrikus kulcsregisztrációs csoport létrehozása

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg az eszközkiépítési szolgáltatás példányát.

2. Válassza az **Igénylések kezelése** lapot, majd kattintson a lap tetején található **Beléptetési csoport hozzáadása** gombra. 

3. A **Beiktatási csoport hozzáadása mezőbe**írja be a következő adatokat, majd kattintson a **Mentés** gombra.

   - **Csoportnév**: Adja meg **a sajátlegacydevices**-

   - **Tanúsítvány típusa**: Válassza a **Szimmetrikus kulcs lehetőséget.**

   - **Kulcsok automatikus létrehozása**: Jelölje be ezt a jelölőnégyzetet.

   - **Válassza ki, hogyan szeretné hozzárendelni az eszközöket az elosztókhoz:** Válassza a **Statikus konfiguráció** lehetőséget, hogy egy adott elosztóhoz hozzárendelhető legyen.

   - **Válassza ki azokat az IoT-központokat, amelyekhez a csoport hozzárendelhető:** Válasszon ki egyet a hubok közül.

     ![Regisztrációs csoport hozzáadása szimmetrikus kulcstanúsítványhoz](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. Ha mentette a regisztrációt, a rendszer létrehozza az **Elsődleges kulcsot** és a **Másodlagos kulcsot**, majd hozzáadja őket a regisztrációs bejegyzéshez. A szimmetrikus kulcsregisztrációs csoport **mylegacydevices** ként jelenik meg a *Regisztráció csoportok* lap *Csoportnév* oszlopában. 

    Nyissa meg a regisztrációt, és másolja ki a generált **Elsődleges kulcsot**. Ez a kulcs a fő csoportkulcs.


## <a name="choose-a-unique-registration-id-for-the-device"></a>Egyedi regisztrációs azonosító kiválasztása az eszközhöz

Az egyes eszközök azonosításához egyedi regisztrációs azonosítót kell definiálni. Használhatja a MAC-címet, a sorozatszámot vagy az eszközbármely egyedi információját. 

Ebben a példában egy MAC-cím és egy sorozatszám kombinációját használjuk, amely a következő karakterláncot képezi a regisztrációs azonosítóhoz.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Hozzon létre egy egyedi regisztrációs azonosítót az eszközhöz. Az érvényes karakterek a kisbetűk alfanumerikus és a kötőjel ('-').


## <a name="derive-a-device-key"></a>Eszközkulcs leszármaztatása 

Az eszközkulcs létrehozásához használja a csoportfőkulcsot az eszköz egyedi regisztrációs azonosítójának [HMAC-SHA256-os](https://wikipedia.org/wiki/HMAC) számának kiszámításához és az eredmény Base64 formátumba történő konvertálásához.

Ne adja meg a csoportfőkulcsot az eszközkódban.


#### <a name="linux-workstations"></a>Linux munkaállomások

Ha Linux munkaállomást használ, az openssl segítségével létrehozhatja a származtatott eszközkulcsot, ahogy az a következő példában látható.

Cserélje le a **KEY** értékét a korábban feljegyzett **elsődleges kulcsra.**

Cserélje le a **REG_ID** értékét a regisztrációs azonosítóra.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


#### <a name="windows-based-workstations"></a>Windows-alapú munkaállomások

Ha Windows-alapú munkaállomást használ, a PowerShell segítségével létrehozhatja a származtatott eszközkulcsot, ahogy az a következő példában látható.

Cserélje le a **KEY** értékét a korábban feljegyzett **elsődleges kulcsra.**

Cserélje le a **REG_ID** értékét a regisztrációs azonosítóra.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


Az eszköz a származtatott eszközkulcsot fogja használni az egyedi regisztrációs azonosítóval, hogy szimmetrikus kulcsigazolást hajtson végre a regisztrációs csoporttal a kiépítés során.



## <a name="create-a-device-image-to-provision"></a>Eszközkép létrehozása a kiépítéshez

Ebben a szakaszban frissíteni fogja a **\_prov\_\_dev-ügyfél minta** nevű kiépítési minta az Azure IoT C SDK korábban beállított. 

Ez a mintakód egy eszköz indítási sorozatát szimulálja, amely elküldi a létesítési kérelmet az eszközkiépítési szolgáltatás példányának. A rendszerindítási sorrend hatására az eszköz fellesz ismerve, és hozzárendeli a regisztrációs csoportban konfigurált IoT hubhoz.

1. Az Azure Portalon válassza ki az eszközkiépítési szolgáltatás **Áttekintés** lapját, és jegyezze fel az **_Azonosító hatóköre_** értéket.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. A Visual Studióban nyissa meg a **Azure_iot_sdks.sln** megoldásfájlt, amelyet a CMake korábbi futtatása hozott létre. A megoldásfájlnak a következő helyen kell lennie:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. A Visual Studio *Solution Explorer* (Megoldáskezelő) ablakában lépjen a **Provision\_Samples** (Kiépítés > Minták) mappára. Bontsa ki a **prov\_dev\_client\_sample** nevű mintaprojektet. Bontsa ki a **Source Files** (Forrásfájlok) elemet, és nyissa meg a **prov\_dev\_client\_sample.c** nevű forrásfájlt.

4. Keresse meg az `id_scope` állandót, és cserélje le az értékét a korábban kimásolt **Azonosító hatóköre** értékre. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Keresse meg a `main()` függvény definícióját ugyanebben a fájlban. Győződjön meg róla, hogy a `hsm_type` változó értéke `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, mint az alább is látható:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Keresse meg `prov_dev_set_symmetric_key_info()` a hívást, hogy a **prov\_dev\_\_ügyfél sample.c,** amely kommentálta ki.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Ne fűzzön megjegyzést a függvényhíváshoz, és cserélje le a helyőrző értékeket (beleértve a szögletes zárójeleket is) az eszköz egyedi regisztrációs azonosítójával és a létrehozott származtatott eszközkulccsal.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6", "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=");
    ```
   
    Mentse a fájlt.

7. Kattintson a jobb gombbal a **prov\_dev\_client\_sample** projektre, és válassza a **Beállítás kezdőprojektként** lehetőséget. 

8. A Visual Studio menüjében válassza a **Hibakeresés** > **indítása hibakeresés nélkül** lehetőséget a megoldás futtatásához. A projekt újraépítésére vonatkozó parancsablakban kattintson az **Igen** gombra a projekt újraépítéséhez a futtatás előtt.

    Az alábbi példakimeneten látható, hogy a szimulált eszköz sikeresen elindul és csatlakozik a regisztrációs szolgáltatáspéldányhoz, hogy az hozzárendelhesse egy IoT-központhoz:

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

    Press enter key to exit:
    ```

9. A portálon keresse meg azt az IoT-központot, amelyhez a szimulált eszközt hozzárendelte, és kattintson az **IoT-eszközök** fülre. A szimulált hubra való sikeres kiépítésekor az eszközazonosítója megjelenik az **IoT-eszközök** panelen, és a *STATUS* **engedélyezve van.** Lehet, hogy rá kell kattintania fent a **Frissítés** gombra. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>Biztonsági aggályok

Ne feledje, hogy ez elhagyja a származtatott eszközkulcs részeként a lemezkép, ami nem ajánlott biztonsági ajánlott eljárás. Ez az egyik oka annak, hogy a biztonság és a könnyű használat kompromisszumok. 





## <a name="next-steps"></a>További lépések

* További újraépítés, lásd: [IoT Hub-eszköz újrakiépítése fogalmak](concepts-device-reprovision.md) 
* [Rövid útmutató: Szimmetrikus kulcs kiosztása szimulált eszköz számára](quick-create-simulated-device-symm-key.md)
* További kiépítés: A [korábban automatikusan kiépített eszközök kiirtása](how-to-unprovision-devices.md) 











