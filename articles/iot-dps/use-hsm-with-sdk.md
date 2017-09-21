---
title: "Azure útmutató – Eltérő HSM használata az eszközkiépítési szolgáltatás ügyfél-SDK-jával | Microsoft Docs"
description: "Azure útmutató – Eltérő HSM használata fizikai eszközökkel és szimulátorokkal az eszközkiépítési szolgáltatás ügyfél-SDK-jával"
services: iot-dps
keywords: 
author: yzhong94
ms.author: yizhon
ms.date: 08/28/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: 
ms.devlang: na
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: a5649ab873993d20803cb01a4b0ecc278c3ce16c
ms.contentlocale: hu-hu
ms.lasthandoff: 09/14/2017

---

# <a name="how-to-use-different-hardware-security-modules-with-device-provisioning-service-client-sdk"></a>Eltérő hardveres biztonsági modulok használata az eszközkiépítési szolgáltatás ügyfél-SDK-jával
Ezek a lépések azt mutatják be, hogyan használhat eltérő [hardveres biztonsági modulokat (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) az eszközkiépítési szolgáltatás ügyfél-SDK-jával a C nyelv, valamint fizikai eszköz és szimulátor használatával.  A kiépítési szolgáltatás két hitelesítési módot támogat: az X**.**509 és a Platformmegbízhatósági modul (TPM) technológiát.

## <a name="prerequisites"></a>Előfeltételek

A [Create and provision simulated device] (Szimulált eszköz létrehozása és üzembe helyezése) (./quick-create-simulated-device.md) útmutató „A fejlesztési környezet előkészítése” című szakaszában foglaltaknak megfelelően előkészített fejlesztőkörnyezet.

## <a name="enable-authentication-with-different-hsms"></a>A különböző HSM-ekkel való hitelesítés engedélyezése

A hitelesítési módot (X**.**509 vagy TPM) engedélyezni kell a fizikai eszközön vagy a szimulátoron, mielőtt beléptethetőek lennének az Azure Portalon.  Lépjen az azure-iot-sdk-c gyökérmappájába.  Futtassa a megadott parancsot a választott hitelesítési módnak megfelelően.

### <a name="use-x509-with-simulator"></a>Az X**.**509 használata szimulátorral

A kiépítési szolgáltatás tartalmaz egy Device Identity Composition Engine (DICE) emulátort, amely létrehoz egy X**.**509 tanúsítványt az eszköz hitelesítéséhez.  Futtassa az alábbi parancsot az X**.**509-hitelesítés engedélyezéséhez:

```
cmake -Ddps_auth_type=x509 ..
```

A DICE-szal rendelkező hardverekkel kapcsolatban [itt](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) talál további információkat.

### <a name="use-x509-with-hardware"></a>Az X**.**509 használata hardverrel

Az kiépítési szolgáltatás és az X**.**509 együttes használata más hardvereken is lehetséges.  A kapcsolat kiépítéséhez egy interfész szükséges a hardver és az SDK között.  Az interfészre vonatkozó információkkal kapcsolatban forduljon a HSM gyártójához.

### <a name="use-tpm"></a>TPM használata

A kiépítési szolgáltatás a Windows- és Linux-hardverek TPM-lapkáihoz SAS-token használatával képes csatlakozni.  Futtassa az alábbi parancsot a TPM-hitelesítés engedélyezéséhez:

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>TPM használata szimulátorral

Ha nem rendelkezik TPM-lapkát tartalmazó eszközzel, fejlesztési célokra használhat szimulátort is a Windows operációs rendszeren.  Futtassa az alábbi parancsot a TPM-hitelesítés engedélyezéséhez és a TPM-szimulátor futtatásához:

```
cmake -Ddps_auth_type=tpm_simulator ..
```

## <a name="build-the-sdk"></a>Az SDK felépítése
Az SDK-t az eszközbeléptetés létrehozása előtt építse fel.

### <a name="linux"></a>Linux
- Az SDK felépítése Linux rendszeren:
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake ..
    cmake --build .  # append '-- -j <n>' to run <n> jobs in parallel
    ```
- A Hibakeresési bináris fájlok felépítéséhez egészítse ki a projektlétrehozási parancsot a megfelelő CMake beállítással, például:
    ```
    cmake -DCMAKE_BUILD_TYPE=Debug ..
    ```

- Számos [CMake konfigurációs beállítás](https://cmake.org/cmake/help/v3.6/manual/cmake.1.html) érhető el az SDK felépítéséhez. Például az elérhető protokollvermek egyikének letiltásához egészítse ki a CMake projektlétrehozási parancsot egy argumentummal:
    ```
    cmake -Duse_amqp=OFF ..
    ```
- Egységtesztet is felépíthet és futtathat:
    ```
    cmake -Drun_unittests=ON ..
    cmake --build .
    ctest -C "debug" -V
    ```

### <a name="windows"></a>Windows
- Az SDK felépítéséhez Windows rendszeren hajtsa végre a következő lépéseket a projektfájlok létrehozásához:
    - Nyisson meg egy „VS2015 fejlesztői parancssort”.
    - Futtassa az alábbi CMake parancsokat az adattár gyökérkönyvtárából:
      ```
      cd azure-iot-sdk-c
      mkdir cmake
      cd cmake
      cmake -G "Visual Studio 14 2015" ..
      ```
    Ez a parancs x86 kódtárakat épít fel. Ha x64 adattárakat szeretne felépíteni, módosítsa a cmake generátorargumentumát: 
    ```
    cmake .. -G "Visual Studio 14 2015 Win64"
    ```

- Ha a projekt létrehozása sikeresen befejeződik, egy Visual Studio megoldásfájlt (.sln) kell látnia a(z) `cmake` mappában. Az SDK felépítése:
    - Nyissa meg a **cmake\azure_iot_sdks.sln** fájlt a Visual Studióban és építse fel, **VAGY**
    - Futtassa az alábbi parancsot a parancssori ablakban, amelyet a projektfájlok létrehozásához használt:
      ```
      cmake --build . -- /m /p:Configuration=Release
      ```
- A Hibakeresési bináris fájlok felépítéséhez használja a megfelelő MSBuild argumentumot: 
    ```
    cmake --build . -- /m /p:Configuration=Debug`
    ```
- Számos CMake konfigurációs beállítás érhető el az SDK felépítéséhez. Például az elérhető protokollvermek egyikének letiltásához egészítse ki a CMake projektlétrehozási parancsot egy argumentummal:
    ```
    cmake -G "Visual Studio 14 2015" -Duse_amqp=OFF ..
    ```
- Egységteszteket is felépíthet és futtathat:
    ```
    cmake -G "Visual Studio 14 2015" -Drun_unittests=ON ..
    cmake --build . -- /m /p:Configuration=Debug
    ctest -C "debug" -V
    ```
  
### <a name="libraries-to-include"></a>Az alkalmazandó kódtárak
- Az alábbi kódtárakat kell belevennie az SDK-ba:
    - A kiépítési szolgáltatás: dps_http_transport, dps_client, dps_security_client
    - IoTHub Security: iothub_security_client

## <a name="create-a-device-enrollment-entry-in-dps"></a>Eszközbeléptetési bejegyzés létrehozása a DPS szolgáltatásban

### <a name="tpm"></a>TPM
TPM használata esetén kövesse a [„Szimulált eszköz létrehozása és kiépítése az IoT Hub eszközkiépítési szolgáltatással”](./quick-create-simulated-device.md) szakaszban leírtakat egy eszközbeléptetési bejegyzés létrehozásához a DPS szolgáltatásban, majd az első rendszerindítás szimulálásához.

### <a name="x509"></a>X**.**509
1. Az eszközök a kiépítési szolgáltatásba való beléptetéséhez fel kell jegyeznie az egyes eszközök Ellenőrzőkulcsát és Regisztrációs azonosítóját, amelyek az ügyfél-SDK által biztosított Kiépítési eszközben tekinthetőek meg. Futtassa a következő parancsot a legfelső szintű hitelesítésszolgáltató tanúsítványának (beléptetési csoportok esetén) és az aláíró tanúsítványának (egyéni beléptetés esetén) kinyomtatásához:
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. Jelentkezzen be az Azure Portalra, a bal oldali menüben kattintson az **Összes erőforrás** gombra, és nyissa meg a DPS szolgáltatást.
   - X**.**509 egyéni beléptetés: A kiépítési szolgáltatás összefoglalás panelén válassza a **Beléptetések kezelése** lehetőséget. Válassza az **Egyéni beléptetések** fület, és kattintson a felül lévő **Hozzáadás** gombra. Válassza az **X**.**509** lehetőséget identitásigazolási *mechanizmusként*, és töltse fel a panel által kért aláírói tanúsítványt. Ha végzett, kattintson a **Mentés** gombra. 
   - X**.**509 csoportos beléptetés: A kiépítési szolgáltatás összefoglalás panelén válassza a **Beléptetések kezelése** lehetőséget. Válassza a **Csoportos beléptetések** lapot, és kattintson a felül lévő **Hozzáadás** gombra. Válassza az **X**.**509** lehetőséget identitásigazolási *mechanizmusként*, adja meg a csoport és a tanúsítvány nevét, és töltse fel a panel által kért legfelső szintű hitelesítésszolgáltatói tanúsítványt. Ha végzett, kattintson a **Mentés** gombra. 

## <a name="connecting-to-iot-hub-after-provisioning"></a>Csatlakozás az IoT Hubhoz a kiépítés után

Miután megtörtént az eszköz kiépítése a kiépítési szolgáltatással, ez az API a HSM hitelesítési mód használatával csatlakozik az IoT Hubhoz: 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```

