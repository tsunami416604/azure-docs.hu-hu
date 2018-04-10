---
title: Azure útmutató – Különböző hardverbiztonsági modulok használata az eszközkiépítési szolgáltatás ügyfél-SDK-jával
description: Azure útmutató – Különböző hardverbiztonsági modulok használata az eszközkiépítési szolgáltatás ügyfél-SDK-jával
services: iot-dps
keywords: ''
author: yzhong94
ms.author: yizhon
ms.date: 03/28/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: ''
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 0d392f4a8d935cb37b6f4cfcd69826de58b33880
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-use-different-hardware-security-modules-with-device-provisioning-service-client-sdk-for-c"></a>Eltérő hardveres biztonsági modulok használata a Device Provisioning Service C-hez készült ügyféloldali SDK-jával

Ez a cikk bemutatja, hogyan használhat különböző [hardveres biztonsági modulokat (HSM-eket)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) a Device Provisioning Service C-hez készült ügyféloldali SDK-jával. Fizikai eszközt vagy szimulátort használhat. A kiépítési szolgáltatás két igazolásimechanizmus-típus esetében támogatja a hitelesítést: az X**.**509 és a platformmegbízhatósági modul (TPM) esetében.

## <a name="prerequisites"></a>Előfeltételek

A [szimulált eszköz létrehozását és üzembe helyezését](./quick-create-simulated-device.md) ismertető útmutató a fejlesztési környezet előkészítéséről szóló szakaszában foglaltaknak megfelelően előkészített fejlesztési környezet.

### <a name="choose-a-hardware-security-module"></a>Hardveres biztonsági modul kiválasztása

Eszközgyártóként először az egyik támogatott típuson alapuló hardveres biztonsági modulokat (vagy HSM-eket) kell kiválasztania. A [Device Provisioning Service C-hez készült ügyféloldali SDK-ja](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client) jelenleg a következő HSM-eket támogatja: 

- [Platformmegbízhatósági modul (TPM)](https://en.wikipedia.org/wiki/Trusted_Platform_Module): A TPM bevett szabvány a legtöbb Windows-alapú eszközplatformhoz, valamint néhány Linux-/Ubuntu-alapú eszközhöz. Eszközgyártóként ezt a HSM-et választhatja, ha az eszközökön ezek az operációs rendszerek futnak, és egy bevett szabványt kíván használni a HSM-ekhez. TPM-lapkák esetén csak külön-külön regisztrálhatja az eszközöket a Device Provisioning Service-ben. A TPM-szimulátort használhatja fejlesztési célokra Windows- vagy Linux-alapú fejlesztői gépeken.

- [X.509](https://cryptography.io/en/latest/x509/): Az X.509-alapú hardveres biztonsági modulok viszonylag új lapkák. A Microsoft dolgozik a RIoT- és a DICE-lapkákon, amelyek az X.509-tanúsítványokat használják. Az X.509-lapkákkal kötegelt eszközregisztrációt végezhet a portálon. Ez egyes nem Windows operációs rendszereket is támogat, például az embedOS rendszert. A Device Provisioning Service ügyféloldali SDK-ja támogatja egy X.509-eszközszimulátor használatát fejlesztési célokra. 

További információkat az [IoT Hub Device Provisioning Service biztonsággal kapcsolatos fogalmait](concepts-security.md) ismertető témakörben talál. 

## <a name="enable-authentication-for-supported-hsms"></a>Támogatott hardveres biztonsági modulok hitelesítésének engedélyezése

A hitelesítési módot (X**.**509 vagy TPM) engedélyezni kell a fizikai eszközön vagy a szimulátoron, mielőtt az regisztrálható lenne az Azure Portalon. Először lépjen az azure-iot-sdk-c gyökérmappájába. Ezután futtassa a megadott parancsot a választott hitelesítési módnak megfelelően:

### <a name="use-x509-with-simulator"></a>Az X**.**509 használata szimulátorral

A kiépítési szolgáltatás tartalmaz egy Device Identity Composition Engine (DICE) emulátort, amely létrehoz egy X**.**509-tanúsítványt az eszköz hitelesítéséhez. Futtassa a következő parancsot az X**.**509-hitelesítés engedélyezéséhez: 

```
cmake -Ddps_auth_type=x509 ..
```

A DICE-szal rendelkező hardverekkel kapcsolatban [itt](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) talál további információkat.

### <a name="use-x509-with-hardware"></a>Az X**.**509 használata hardverrel

Az kiépítési szolgáltatás és az X**.**509 együttes használata más hardvereken is lehetséges. A kapcsolat kiépítéséhez egy interfész szükséges a hardver és az SDK között. Az interfészre vonatkozó információkkal kapcsolatban forduljon a HSM gyártójához.

### <a name="use-tpm"></a>TPM használata

A kiépítési szolgáltatás a Windows- és Linux-hardverek TPM-lapkáihoz SAS-token használatával képes csatlakozni. Futtassa a következő parancsot a TPM-hitelesítés engedélyezéséhez:

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>TPM használata szimulátorral

Ha nem rendelkezik TPM-lapkát tartalmazó eszközzel, fejlesztési célokra használhat szimulátort is a Windows operációs rendszeren. Futtassa a következő parancsot a TPM-hitelesítés engedélyezéséhez és a TPM-szimulátor futtatásához:

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

## <a name="create-a-device-enrollment-entry-in-device-provisioning-services"></a>Eszközregisztrációs bejegyzés létrehozása Device Provisioning Service-ben

### <a name="tpm"></a>TPM
TPM használata esetén kövesse a [szimulált eszköz az IoT Hub Device Provisioning Service használatával történő létrehozását és kiépítését ismertető](./quick-create-simulated-device.md) szakaszban leírtakat eszközregisztrációs bejegyzés létrehozásához a Device Provisioning Service-ben és az első rendszerindítás szimulálásához.

### <a name="x509"></a>X**.**509
1. Az eszközök a kiépítési szolgáltatásba való beléptetéséhez fel kell jegyeznie az egyes eszközök Ellenőrzőkulcsát és Regisztrációs azonosítóját, amelyek az ügyfél-SDK által biztosított Kiépítési eszközben tekinthetőek meg. Futtassa a következő parancsot a legfelső szintű hitelesítésszolgáltató tanúsítványának (beléptetési csoportok esetén) és az aláíró tanúsítványának (egyéni beléptetés esetén) kinyomtatásához:
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. Jelentkezzen be az Azure Portalra, a bal oldali menüben kattintson a **Minden erőforrás** gombra, és nyissa meg a DPS szolgáltatást.
   - X**.**509 egyéni beléptetés: A kiépítési szolgáltatás összefoglalás panelén válassza a **Beléptetések kezelése** lehetőséget. Válassza az **Egyéni beléptetések** fület, és kattintson a felül lévő **Hozzáadás** gombra. Válassza az **X**.**509** lehetőséget identitásigazolási *mechanizmusként*, és töltse fel a panel által kért aláírói tanúsítványt. Ha végzett, kattintson a **Mentés** gombra. 
   - X**.**509 csoportos beléptetés: A kiépítési szolgáltatás összefoglalás panelén válassza a **Beléptetések kezelése** lehetőséget. Válassza a **Csoportos beléptetések** lapot, és kattintson a felül lévő **Hozzáadás** gombra. Válassza az **X**.**509** lehetőséget identitásigazolási *mechanizmusként*, adja meg a csoport és a tanúsítvány nevét, és töltse fel a panel által kért legfelső szintű hitelesítésszolgáltatói tanúsítványt. Ha végzett, kattintson a **Mentés** gombra. 

## <a name="enable-authentication-for-custom-tpm-and-x509-devices-optional"></a>Egyéni TPM- és X.509-eszközök hitelesítésének engedélyezése (nem kötelező)

> [!NOTE]
> Ez a szakasz csak olyan eszközökre vonatkozik, amelyeknek egyéni platform vagy HSM támogatására van szükségük, amelyeket jelenleg nem támogat a Device Provisioning Service C-hez készült ügyféloldali SDK-ja.

Először ki kell fejlesztenie az egyéni HSM-adattárat és -kódtárat:

1. Fejlesszen ki egy kódtárat a HSM eléréséhez. Ennek a projektnek statikus kódtárat kell készítenie az eszközkiépítési SDK-hoz.

2. Implementálja a következő fejlécfájlban meghatározott függvényeket a kódtárában: 

    - Egyéni TPM esetén: implementálja a [HSM TPM API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-tpm-api) alatt meghatározott egyéni HSM-függvényeket.  
    - Egyéni X.509 esetén: implementálja a [HSM X509 API-ban](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-x509-api) meghatározott egyéni HSM-függvényeket. 

Miután a kódtár sikeresen létrejött önállóan, integrálnia kell azt a Device Provisioning Service ügyféloldali SDK-jával a kódtárhoz kapcsolásával. :

1. Adja meg az egyéni HSM GitHub-adattárát, a kódtár útvonalát és nevét a következő `cmake` parancsban:
    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=<path_and_name_of_library> <PATH_TO_AZURE_IOT_SDK>
    ```
   
2. Nyissa meg a CMake (`\azure-iot-sdk-c\cmake\azure_iot_sdks.sln`) által készített Visual Studio-megoldásfájlt, és építse fel. 

    - A felépítési folyamat összeállítja az SDK-kódtárat.
    - Az SDK megpróbál a `cmake` parancsban meghatározott egyéni HSM-hez kapcsolódni.

3. Futtassa a „Provision_Samples” területen (az `\azure-iot-sdk-c\cmake\provisioning_client\samples\prov_dev_client_ll_sample` helyen) lévő „prov_dev_client_ll_sample” mintaalkalmazást a HSM megfelelő implementálásának ellenőrzéséhez.

## <a name="connecting-to-iot-hub-after-provisioning"></a>Csatlakozás az IoT Hubhoz a kiépítés után

Miután megtörtént az eszköz kiépítése a kiépítési szolgáltatással, ez az API a HSM hitelesítési mód használatával csatlakozik az IoT Hubhoz: 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```

