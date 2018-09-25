---
title: Szimmetrikus kulcsok használata az Azure IoT Hub Device Provisioning Service szolgáltatással örökölt eszközök kiépítése |} A Microsoft Docs
description: A szimmetrikus kulcsok használata a device provisioning service-példány az örökölt eszközök kiépítése
author: wesmc7777
ms.author: wesmc
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 51fea4fa1973fbe92242f1995d892cd5b038a29b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991640"
---
# <a name="how-to-provision-legacy-devices-using-symmetric-keys"></a>A szimmetrikus kulcsok használata örökölt eszközök kiépítése


Örökölt eszközök számos gyakori probléma, hogy milyen gyakran legyen az identitás, amely egy darab információ áll. Ez az azonosító adatokat általában a MAC-címet vagy sorozatszámot. Az örökölt eszközök nem rendelkezhet egy tanúsítványt, a TPM vagy bármely más biztonsági funkció, amely az eszköz biztonságos azonosítására használható. Az IoT hub Device Provisioning Service szimmetrikus kulcsát a kulcsigazoláshoz tartalmazza. Szimmetrikus kulcsát a kulcsigazoláshoz használható, hogy egy eszköz ki további információkat, például a MAC-cím vagy sorozatszám alapján.

Ha egyszerűen telepítheti a [hardveres biztonsági modul (HSM)](concepts-security.md#hardware-security-module) és a egy tanúsítványt, majd, amely lehet, hogy esetleg jobb megközelítés azonosítása, és üzembe helyezheti az eszközök. Mivel azt a módszert is lehetővé teszi, hogy átugorja az összes eszközre telepített a kód frissítése, és azt nem kell egy titkos kulcsot az eszközképre ágyazva.

Ez a cikk azt feltételezi, hogy hardveres biztonsági modul és a egy tanúsítványt nem kivitelezhető lehetőség. Azonban feltételezzük, hogy a frissítési kód a Device Provisioning Service használatával ezek az eszközök kiépítése néhány módszer. 

Ez a cikk azt is feltételezi, hogy az eszköz frissítése a jogosulatlan hozzáférés elkerülése érdekében a fő csoportkulcs vagy származtatott eszközkulcs biztonságos környezetben történik.

Ez a cikk egy Windows-alapú munkaállomás felé objektumorientált. Azonban az ismertetett eljárások elvégzése Linux rendszeren. A Linuxos példa: [több-bérlős módhoz kiépítéséről](how-to-provision-multitenant.md).


## <a name="overview"></a>Áttekintés

Egy egyedi regisztrációs Azonosítót fogja definiálni minden egyes eszközhöz, amely azonosítja az adott eszköz információk alapján. Ha például a MAC-cím vagy sorozatszám.

Egy regisztrációs csoportnak, amely [szimmetrikus kulcsát a kulcsigazoláshoz](concepts-symmetric-key-attestation.md) jön létre a Device Provisioning Service szolgáltatással. A regisztrációs csoport csoport főkulcs tartalmazza. A főkulcs kivonatához minden egyedi regisztrációs azonosító minden eszközhöz egyedi eszközkulcs létrehozásához használható. Az eszköz kulcsot fogja használni a származtatott eszköz egyedi regisztrációs Azonosítóval rendelkező igazolja a Device Provisioning Service szolgáltatással, és a egy IoT hubot kell rendelni.

Ebben a cikkben bemutatott eszközkóddal, ugyanezt a mintát követi a [a rövid útmutató: a szimmetrikus kulcsokat a szimulált eszköz kiépítése](quick-create-simulated-device-symm-key.md). A kód fogja-minta használatával eszköz szimulálása a [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). A szimulált eszközt egy regisztrációs csoport helyett egyéni regisztrációt a bizonyítja, ahogyan az a rövid útmutató is.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

* Befejezése után a [IoT Hub Device Provisioning Service beállítása az Azure Portallal](./quick-setup-auto-provision.md) rövid.
* Visual Studio 2015 vagy [Visual Studio 2017](https://www.visualstudio.com/vs/), amelyben engedélyezve van az [„Asztali fejlesztés C++ használatával”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) számítási feladat.
* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Az Azure IoT C SDK-t fejlesztési környezet előkészítése

Ebben a szakaszban egy segítségével hozhatók létre a fejlesztési környezetet előkészíti az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). 

Az SDK-t magában foglalja a mintakód a szimulált eszközhöz. Ezt a szimulált eszközt megkísérli a kiépítés során az eszköz rendszerindítási során.

1. Töltse le a verzióját 3.11.4 a [CMake buildelési rendszert](https://cmake.org/download/). Ellenőrizze a letöltött bináris fájlt a megfelelő titkosítási kivonat értékének használatával. Az alábbi példa a Windows PowerShell használatával ellenőrizte az x64 MSI disztribúció 3.11.4-es verziójának titkosítási kivonatát:

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    A következő kivonatértékeket verzió 3.11.4 írásának időpontjában a CMake webhelyen felsorolt:

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

    Fontos, hogy a Visual Studio előfeltételei (Visual Studio és az „Asztali fejlesztés C++ használatával” számítási feladat) telepítve legyenek a gépen, **mielőtt** megkezdené a `CMake` telepítését. Ha az előfeltételek telepítve vannak, és ellenőrizte a letöltött fájlt, telepítse a CMake buildelési rendszert.

2. Nyisson meg egy parancssort vagy a Git Bash-felületet. Hajtsa végre a következő parancsot az Azure IoT C SDK GitHub-adattár klónozása:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Az adattár mérete jelenleg körülbelül 220 MB. Ez a művelet várhatóan több percig is eltarthat.


3. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Futtassa a következő parancsot, amely létrehozza egy adott a fejlesztési fejlesztésiügyfél-platformhoz SDK verziója. A szimulált eszközhöz tartozó Visual Studio-megoldás a `cmake` könyvtárban jön létre. 

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```
    
    Ha a `cmake` nem találja a C++ fordítóprogramot, a fenti parancs futtatása esetlegesen fordítási hibákat adhat vissza. Ilyen esetekben futtassa a parancsot a [Visual Studio parancssorából](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    A sikeres létrehozást követően a kimenet utolsó sorai a következőhöz hasonlóan néznek majd ki:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```


## <a name="create-a-symmetric-key-enrollment-group"></a>Hozzon létre egy szimmetrikus kulcs regisztrációs csoportot

1. Jelentkezzen be a [az Azure portal](http://portal.azure.com), és nyissa meg a Device Provisioning Service-példány.

2. Válassza ki a **beléptetések kezelése** fülre, majd a **regisztrációs csoport hozzáadása** gombra a lap tetején. 

3. A **regisztrációs csoport hozzáadásához**, adja meg a következőket, majd kattintson a **mentése** gombra.

    - **Csoport neve**: Adja meg **mylegacydevices**.

    - **Tanúsítvány típusa**: válasszon **szimmetrikus kulcs**.

    - **Kulcsok automatikus létrehozása**: ezt a jelölőnégyzetet.

    - **Válassza ki, hogyan szeretné hozzárendelni az eszközöket hubs**: válasszon **statikus konfiguráció** így hozzárendelhet egy adott hubhoz.

    - **Válassza ki az IoT-központok ennek a csoportnak rendelhetők**: válassza ki a hub egyikét.

    ![Adja hozzá a szimmetrikus kulcsát a kulcsigazoláshoz regisztrációs csoportot](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. A regisztráció, a mentés után a **elsődleges kulcs** és **másodlagos kulcs** generált és a regisztrációs bejegyzés hozzá. Megjelenik a szimmetrikus kulcs regisztrációs csoportot **mylegacydevices** alatt a *csoportnév* oszlopa a *regisztrációs csoportok* fülre. 

    Nyissa meg a regisztrációt, és másolja ki a létrehozott értékét **elsődleges kulcs**. Ezt a kulcsot a fő csoportkulcs.


## <a name="choose-a-unique-registration-id-for-the-device"></a>Válasszon egy egyedi regisztrációs Azonosítót az eszköz

Minden egyes eszköz azonosítására definiálni kell egy egyedi regisztrációs Azonosítót. A MAC-cím, sorozatszám vagy az eszköz egyedi információit is használhatja. 

Ebben a példában egy MAC-cím és alkosson a következő karakterláncot a regisztrációs azonosító sorozatszámot kombinációját használjuk

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Hozzon létre egy egyedi regisztrációs Azonosítót az eszközt. Érvényes karakterek: alfanumerikus kisbetűt és kötőjelet ("-").


## <a name="derive-a-device-key"></a>Származtasson egy eszközkulcs 

Az eszköz kulcs létrehozásához használja a csoport főkulcs számítási egy [HMAC-SHA256 algoritmust](https://wikipedia.org/wiki/HMAC) az eszköz és az eredmény Base64 formátumra alakítható az egyedi regisztrációs ID.

A csoport főkulcs nem tartalmazzák az eszköz-kódjában.


#### <a name="linux-workstations"></a>Linux-munkaállomásokon

Egy Linux munkaállomáson használja, ha openssl használatával hozhatja létre a származtatott eszköz kulcsot a következő példában látható módon.

Értékét cserélje **kulcs** együtt a **elsődleges kulcs** korábban feljegyzett.

Cserélje le a értékét **REG_ID** regisztrációs ID azonosítójával.

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

Ha egy Windows-alapú munkaállomást használ, a PowerShell használatával hozhatja létre a származtatott eszköz kulcsot a következő példában látható módon.

Értékét cserélje **kulcs** együtt a **elsődleges kulcs** korábban feljegyzett.

Cserélje le a értékét **REG_ID** regisztrációs ID azonosítójával.

```PowerShell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```PowerShell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


Az eszköz kulcsot fogja használni a származtatott eszköz az egyedi regisztrációs azonosítóval, hajtsa végre a szimmetrikus kulcs igazolási a regisztrációs csoport a kiépítés során.



## <a name="create-a-device-image-to-provision"></a>Lemezkép létrehozása a eszköz kiépítéséhez

Ebben a szakaszban egy nevű eszközkiépítési minta frissíteni **prov\_fejlesztési\_ügyfél\_minta** korábban állítsa be az Azure IoT C SDK-ban található. 

A mintakód a kiépítési kérést küld a Device Provisioning Service-példány eszköz rendszerindítási sorrend szimulálja. A rendszerindítási sorrend miatt az eszköz és az IoT hubot konfigurált a regisztrációs csoport rendelve.

1. Az Azure Portalon válassza ki az eszközkiépítési szolgáltatás **Áttekintés** lapját, és jegyezze fel az **_Azonosító hatóköre_** értéket.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. A Visual Studióban nyissa meg a **azure_iot_sdks.sln** megoldásfájlt a CMake futó korábban létrehozott. A fájl a következő helyen kell lennie:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. A Visual Studio *Solution Explorer* (Megoldáskezelő) ablakában lépjen a **Provision\_Samples** (Kiépítés > Minták) mappára. Bontsa ki a **prov\_dev\_client\_sample** nevű mintaprojektet. Bontsa ki a **Source Files** (Forrásfájlok) elemet, és nyissa meg a **prov\_dev\_client\_sample.c** nevű forrásfájlt.

4. Keresse meg az `id_scope` állandót, és cserélje le az értékét a korábban kimásolt **Azonosító hatóköre** értékre. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Keresse meg a `main()` függvény definícióját ugyanebben a fájlban. Győződjön meg arról, hogy a `hsm_type` változó értéke `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` alább látható módon:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Kattintson a jobb gombbal a **prov\_dev\_client\_sample** projektre, és válassza a **Beállítás kezdőprojektként** lehetőséget. 

7. A Visual Studióban *Megoldáskezelőben* ablakban keresse meg a **hsm\_biztonsági\_ügyfél** projektre, és bontsa ki azt. Bontsa ki a **forrásfájlok**, és nyissa meg a **hsm\_ügyfél\_key.c**. 

    Keresse meg a nyilatkozat az `REGISTRATION_NAME` és `SYMMETRIC_KEY_VALUE` konstansok. A következő módosításokat a fájlt, és mentse a fájlt.

    Frissítse az értéket, a `REGISTRATION_NAME` az állandó a **egyedi regisztrációs Azonosítót az eszköz**.
    
    Frissítse az értéket, a `SYMMETRIC_KEY_VALUE` az állandó a **eszközkulcs származtatott**.

    ```c
    static const char* const REGISTRATION_NAME = "sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6";
    static const char* const SYMMETRIC_KEY_VALUE = "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=";
    ```

7. A Visual Studio menüjében válassza a **Debug** > **Start without debugging** (Hibakeresés > Indítás hibakeresés nélkül) lehetőséget a megoldás futtatásához. A projekt újraépítésére vonatkozó parancsablakban kattintson az **Igen** gombra a projekt újraépítéséhez a futtatás előtt.

    A következő kimenet a szimulált eszköz sikeresen másolatából, és hogyan csatlakozhat hozzá kell rendelni egy IoT hubot a kiépítési szolgáltatás példánya egy példát:

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

8. A portálon lépjen az IoT hub a szimulált eszköz hozzá lett rendelve, és kattintson a **IoT-eszközök** fülre. Sikeresen kiépíti a szimulált a hubon, az eszköz azonosítója megjelenik a **IoT-eszközök** panelen a *állapot* , **engedélyezve**. Előfordulhat, hogy kell kattintania a **frissítése** gombra az oldal tetején. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>Biztonsági szempontok

Vegye figyelembe, hogy ennek következtében a származtatott eszköz kulcsát a lemezképet, amely nem ajánlott biztonsági szempontból ajánlott részét. Ez az egyik OK, miért biztonsági és könnyű használatára kompromisszumot. 





## <a name="next-steps"></a>További lépések

* További Reprovisioning kapcsolatban lásd: [IoT Hub Device reprovisoning fogalmak](concepts-device-reprovision.md) 
* [Gyors útmutató: A szimmetrikus kulcsokat a szimulált eszköz kiépítése](quick-create-simulated-device-symm-key.md)
* Megszüntetés további tudnivalókért lásd: [hogyan eszközöket, amelyek korábban automatikus – kiépített megszüntetése ](how-to-unprovision-devices.md) 











