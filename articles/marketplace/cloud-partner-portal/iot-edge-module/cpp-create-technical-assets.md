---
title: Technikai eszközök Azure IoT Edge-modul létrehozása |} A Microsoft Docs
description: Hozzon létre egy IoT Edge-modul a technikai eszközök.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: d7f9dfee447cddc771e0e88ca5be04018b51dba2
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49431172"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Az IoT Edge-modul technikai eszközök előkészítése

Ez a cikk ismerteti, amely az IoT Edge-modul technikai eszközök meg kell felelniük az Azure Marketplace-en közzétett előtt.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>IoT Edge-modulok és első lépések

Egy IoT Edge-modul Docker-kompatibilis tároló futtatni az IoT Edge-eszköz kapcsolat.

- IoT Edge-modulok kapcsolatos további információkért lásd: [megismerheti az Azure IoT Edge-modulok](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- Ismerkedés az IoT Edge-modul fejlesztését, lásd: [követelményeket és az eszközök IoT Edge-modulok](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Technikai követelmények

A következő műszaki követelményeknek teljesülniük kell ahhoz, hogy az IoT Edge-modul certified és közzététele az Azure Marketplace-en.

### <a name="platform-support"></a>Platformtámogatás

Az IoT Edge-modul támogatnia kell a platform alábbi lehetőségek közül.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>1. rétegbeli rendszerek IoT Edge által támogatott

Támogatja az IoT Edge által támogatott összes 1. rétegbeli rendszerek (feljegyzett [Azure IoT Edge-támogatás](https://docs.microsoft.com/azure/iot-edge/support)). Ez a beállítás azt javasoljuk, mert a felhasználói élményt biztosít. Ezen feltételeknek megfelelő modulok felhőkompetenciáit kell lesz. Egy modul ezt a platform lehetőséget kell:

- Adjon meg egy `latest` címke és a egy verzió címkéje (például `1.0.1`), amelyek a GitHub beépített jegyzékfájl címkék [jegyzékfájl-eszköz](https://github.com/estesp/manifest-tool).
- Használja a [a Marketplace-en lapon](./cpp-marketplace-tab.md) mutató hivatkozás hozzáadása [hitelesített eszközöket kompatibilis IoT Edge](http://aka.ms/iot-edge-certified). Ez a hivatkozás mutat `http://aka.ms/iot-edge-certified`, egy webhely, ahol az ügyfelek tallózása vagy keresése hitelesített eszközöket. Ezen a webhelyen van, más néven a [Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/) eszközkatalógus.

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Egy IoT Edge által támogatott 1. rétegbeli rendszerek részét
  
Egy részhalmazát támogatjuk (legalább) 1. rétegbeli rendszerek IoT Edge által támogatott (feljegyzett [Azure IoT Edge-támogatás](https://docs.microsoft.com/azure/iot-edge/support)). Egy modul ezt a platform lehetőséget kell:

- Adjon meg egy `latest` címke és a egy verzió címkéje (például `1.0.1`), amelyek a GitHub beépített jegyzékfájl címkék [jegyzékfájl-eszköz](https://github.com/estesp/manifest-tool) Ha egynél több platformot támogatja. Jegyzékfájl címkék megadása nem kötelező csak amikor egy platformot támogatja.
- Használja a [piactér lapján](./cpp-marketplace-tab.md) , adjon meg legalább egy, az IoT Edge-eszköz egy hivatkozást a [Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/) eszközkatalógus.

### <a name="device-dimensions"></a>Eszköz-méretek

Megcélzott IoT Edge-eszközökön IoT Edge-modul dimenziók (CPU/RAM/Storage/GPU/stb.) a következő követelményeknek kell megfelelnie:

- A modul kell **munkahelyi legalább egy IoT Edge-minősítéssel rendelkező** az eszköz a [Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/) eszközkatalógus.
- A **minimális hardverkövetelmények** dokumentálni kell, a legutóbbi tartalmi kivonatként az ajánlat leírása (alatt a [piactér lapján](./cpp-marketplace-tab.md)). Igény szerint is listázhatja az ajánlott hardverkövetelményeknek ha jelentősen eltérnek. Például adja hozzá a következő szakasz végén található az ajánlat leírása:

 ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
 ```

### <a name="configuration"></a>Konfiguráció

Alapértelmezett beállításait, hogy az üzembe helyezés az IoT Edge-eszköz szerint lépünk, a lehető is tartalmaz. A tároló is az IoT Edge modul SDK-t a edgeHub és az IoT hubbal való kommunikáció engedélyezése.

#### <a name="default-configuration"></a>Alapértelmezett konfiguráció

IoT Edge-modulok kell lennie a megadott alapértelmezett beállításokkal indul el [Termékváltozat lapján a Cloud Partner portálra](./cpp-skus-tab.md). Az alábbi alapértelmezett beállításokkal érhetők el:

- Alapértelmezett **útvonalak**
- Alapértelmezett **ikereszköz kívánt tulajdonságok**
- Alapértelmezett **környezeti változók**
- Alapértelmezett **createOptions**

Egy forgatókönyv, ahol egy paraméter alapértelmezett értéke szükséges értelmetlen (például egy ügyfél-kiszolgáló IP-címét) ad hozzá egy paraméter alapértelmezett értéket. Ez az érték kapcsos zárójelek közé, és nagybetűvel. Ebben a példában állíthat be az alábbi alapértelmezett környezeti változót:

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Konfigurációs dokumentációt

Az IoT Edge-modul összes konfigurációs beállításainak egyértelműen dokumentálni kell (hogyan használhatja a saját útvonalakat, ikereszköz kívánt tulajdonságait, a környezeti változók, createOptions és stb.) Adjon meg egy hivatkozást a dokumentációt, vagy a dokumentációban kell szerepelniük az ajánlat/sku leírása.

### <a name="tags-and-versioning"></a>Címkék és a verziókezelés

Ügyfelek üzembe helyezhetik a modul, és automatikusan le frissítéseket a Marketplace-en (forgatókönyvekben fejlesztői.) képesnek kell lennie. Akkor is képesnek kell lennie és rögzítése egy pontos verziót, tesztelt (az egy éles forgatókönyvet.)

Ezek vásárlói elvárásokkal és tehetők közzé a piactéren, az IoT Edge-modulok az alábbi követelményeknek kell megfelelnie:

- Tartalmazza a jegyzékfájl `latest` címke, amely a legújabb verziót az összes támogatott platformon.
- Verzió címkéket az űrlap X.Y.Z, ha X, Y és Z egész számoknak kell lennie.
- Közé tartozik például a "verzió" címke `1.0.1`, egy adott verziót az összes támogatott platformon, amely mutat.
- Nincs frissítés "verzió" címke, például `1.0.1`, mert nem módosítható kell lennie.

>[!Note]
>Szükség esetén versioning tartalmazhat "működés közbeni verzió" címke, például `2.0` és `1.0`. Ez támogatja fenntartásával párhuzamosan több főbb verziók.

### <a name="telemetry"></a>Telemetria

Az IoT-modul SDK-val modulok értékre kell állítani a modul egyedi azonosító `PublisherId.OfferId.SkuId` telemetriai célokra. Egyedi azonosító lehetővé teszi, hogy az Azure Marketplace-en futó példányok modul azonosításához.

 Az IoT-modul SDK-k, az alábbi módszerek segítségével állítsa be a `ProductInfo` , ez az azonosító:

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._product_info?view=azure-java-stable)

Az modulokat, amelyek nem használják az IoT-modul SDK-t, a kevésbé pontos insights például a letöltések számát, a Cloud Partner Portalon keresztül érhető el.

### <a name="security"></a>Biztonság

IoT Edge-modulok a gazdagép, a lehető legalacsonyabb jogosultsági szintű hozzáférést kell kérnie. [Az emelt szintű modulok](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) el kell kerülni.

### <a name="module-iot-sdk"></a>A modul IoT SDK-val

Minősítési előfeltétele többek között az IoT-modul SDK-t nem. Azonban például az IoT-modul SDK-val rendelkezhetnek jobb felhasználói élményt. Ha például útválasztást, vagy üzeneteket küld a felhő támogatásához.

Az IoT-modul SDK modul példányai számával kapcsolatos telemetriai adataihoz szükséges.


## <a name="recertification-process"></a>Tanúsításra folyamat

<!-- Add legal time windows--> Partnerek értesítést kap, amely hatással van a modulokat, például a használhatatlanná tévő változást történik:

- IoT Edge által támogatott szint 1 operációs rendszer/arch támogatási mátrix
- IoT-modul SDK-t
- IoT Edge-futtatókörnyezet
- Az IoT Edge-modul hitelesítő irányelvek

Partnerek kell azok-modulok frissítése és a segítségével újból hitelesítheti ezeket a Cloud Partner Portalon eszköz segítségével.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>Az IoT Edge-modul az Azure Container Registry üzemeltetése

Töltse fel az IoT Edge-modul a Cloud Partner portálra, szüksége lesz a üzemeltetni egy [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR). A modul az összes címke, a közzétételhez használni kívánt tartalmaznia kell többek között a jegyzékfájl címke által hivatkozott címkéket.


## <a name="next-steps"></a>További lépések

- [Az IoT Edge-modul ajánlat létrehozása](./cpp-create-offer.md)
