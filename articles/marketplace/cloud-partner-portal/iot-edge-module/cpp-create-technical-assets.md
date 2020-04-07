---
title: Azure IoT Edge modul technikai eszközeinek létrehozása | Azure Piactér
description: Hozzon létre egy IOt Edge-modul technikai eszközeit.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: cd9cd9f3986ef1944d9f9119296af5512b352d1d
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744322"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Készítse elő az IoT Edge modul technikai eszközeit

>[!Important]
>2020. március 30-tól megkezdjük az IoT Edge modulajánlatok kezelését a Partner Centerbe. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Kövesse az [IoT Edge-modulajánlat létrehozása](https://aka.ms/AzureIoTTechAsset) az áttelepített ajánlatok kezeléséhez kövesse az utasításokat.

Ez a cikk ismerteti azokat a követelményeket, amelyeket az IoT Edge-modul technikai eszközeinek meg kell felelniük az Azure Marketplace-en való közzététel előtt.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>Az IoT Edge-modulok ismertetése és az első lépések

Az IoT Edge-modul egy Docker-kompatibilis tároló, amely ioT Edge-eszközön való futtatásra készült.

- Ha többet szeretne megtudni az IoT Edge-modulokról, [olvassa el az Azure IoT Edge-modulok megismerése.](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)
- Az IoT Edge-modul fejlesztésének megkezdéséhez tekintse meg [az IoT Edge-modulok fejlesztéséhez szükséges követelményeket és eszközöket.](https://docs.microsoft.com/azure/iot-edge/module-development)

## <a name="technical-requirements"></a>Technikai követelmények

Az alábbi műszaki követelményeknek kell megfelelni ahhoz, hogy az IoT Edge-modul minősítést és közzétételt az Azure Marketplace-en.

### <a name="platform-support"></a>Platformtámogatás

Az IoT Edge-modulnak támogatnia kell az alábbi platformbeállítások egyikét.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Az IoT Edge által támogatott 1.

Az IoT Edge által támogatott összes Tier 1 platform támogatása (az [Azure IoT Edge-támogatásban](https://docs.microsoft.com/azure/iot-edge/support)rögzítettek szerint). Ezt a lehetőséget azért javasoljuk, mert jobb felhasználói élményt nyújt. Az e kritériumoknak megfelelő modulokbemutatásra kerülnek. A platformopciót használó modulnak:

- Adjon `latest` meg egy címkét és `1.0.1`egy verziócímkét (például ), amelyek a GitHub [jegyzékfájl-eszközzel](https://github.com/estesp/manifest-tool)készített jegyzékcímkék.
- A [Piactér lapon](./cpp-marketplace-tab.md) csatolhat egy hivatkozást [kompatibilis IoT Edge-tanúsítvánnyal rendelkező eszközökre.](https://aka.ms/iot-edge-certified) Ez a hivatkozás `https://aka.ms/iot-edge-certified`a webhelyen található, ahol az ügyfelek kereshetnek vagy kereshetnek minősített eszközöket. Ez a webhely azure [IoT Edge-tanúsítvánnyal rendelkező](https://catalog.azureiotsolutions.com/) eszközkatalógusként is ismert.

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Az IoT Edge által támogatott Tier 1 platformok egy részhalmaza
  
Támogatja az IoT Edge által támogatott Tier 1 platformok egy részhalmazát (legalább egy) (az [Azure IoT Edge-támogatásban rögzítettek szerint).](https://docs.microsoft.com/azure/iot-edge/support) A platformopciót használó modulnak:

- Adjon `latest` meg egy címkét és `1.0.1`egy verziócímkét (például), amelyek a GitHub [jegyzékfájl-eszközzel](https://github.com/estesp/manifest-tool) készített jegyzékcímkék, ha egynél több platform támogatott. A jegyzékcímkék csak akkor választhatók, ha egy platform támogatott.
- A [Piactér lapon](./cpp-marketplace-tab.md) az [Azure IoT Edge certified](https://catalog.azureiotsolutions.com/) eszközkatalóguslegalább egy IoT Edge-eszközre mutató hivatkozást biztosíthat.

### <a name="device-dimensions"></a>Eszközméretek

Az IoT Edge modul méretei (CPU/RAM/Storage/GPU/etc.) a célzott IoT Edge-eszközökön a következő követelményeknek kell megfelelniük:

- A **modulnak legalább egy IoT Edge-tanúsítvánnyal rendelkező eszközzel** kell működnie az [Azure IoT Edge-tanúsítvánnyal rendelkező](https://catalog.azureiotsolutions.com/) eszközkatalógusban.
- A **minimális hardverkövetelményeket** az ajánlat leírásának utolsó bekezdéseként kell dokumentálni (a [Piactér lapon).](./cpp-marketplace-tab.md) Szükség esetén az ajánlott hardverkövetelményeket is felsorolhatja, ha azok jelentősen eltérnek egymástól. Adja meg például a következő szakaszt az ajánlat leírásának végén:

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>Konfiguráció

Azt is tartalmazza az alapértelmezett konfigurációs beállításokat, hogy a központi telepítés egy IoT Edge-eszköz a lehető legegyenesebben előre. A tároló is tartalmazhat az IoT Edge module SDK az edgeHub és az IoT Hub közötti kommunikáció engedélyezéséhez.

#### <a name="default-configuration"></a>Alapértelmezett konfiguráció

Az IoT Edge-moduloknak a Cloud Partner [portál Termékváltozat lapján](./cpp-skus-tab.md)megadott alapértelmezett beállításokkal kell kezdődniük. A következő alapértelmezett beállítások érhetők el:

- Alapértelmezett **útvonalak**
- Alapértelmezett **iker kívánt tulajdonságok**
- Alapértelmezett **környezeti változók**
- Alapértelmezett **createOptions**

Olyan esetben, amikor az alapértelmezett értékhez szükséges paraméternek nincs értelme (például az ügyfél kiszolgálójának IP-címe), egy paramétert kell hozzáadnia alapértelmezett értékként. Ez az érték zárójelek közé van bezárva, nagybetűvel. Ebben a példában a következő alapértelmezett környezeti változót állította be:

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Konfigurációs dokumentáció

Az IoT Edge-modul összes konfigurációs beállítását egyértelműen dokumentálni kell (hogyan kell használni az útvonalakat, az iker kívánt tulajdonságokat, a környezeti változókat, a createOptions-t és így tovább.) Adjon meg egy hivatkozást a dokumentációhoz, vagy a dokumentációnak az ajánlat/termék leírásának részét kell, hogy tartalmazza.

### <a name="tags-and-versioning"></a>Címkék és verziószámozás

Az ügyfeleknek könnyen telepíteniük kell egy modult, és automatikusan le kell szedniük a frissítéseket a piactérről (fejlesztői forgatókönyv esetén).) Emellett képesnek kell lenniük az általuk tesztelt pontos verzió használatára és fagyasztására (éles környezetben).

Az ügyfelek elvárásainak való megfelelés és a piactéren való közzététel érdekében az IoT Edge-moduloknak a következő követelményeknek kell megfelelniük:

- Adjon meg `latest` egy jegyzékcímkét, amely a legújabb verziót az összes támogatott platformon rámutat.
- A verziócímkéknek X.Y.Z formátumúnak kell lenniük, ahol X, Y és Z egész számok.
- Include a "version" `1.0.1`tag, mint a , hogy pont egy adott verzió minden támogatott platformokon.
- Ne frissítse a "verzió" `1.0.1`címkéket, például a , mert nem módosíthatók.

>[!Note]
>A verziószámozás tartalmazhat "gördülő verzió" címkéket, például `2.0` és `1.0`. Ez több főverzió párhuzamos karbantartását támogatja.

### <a name="telemetry"></a>Telemetria

Az IoT-modul SDK-t használó moduloknak `PublisherId.OfferId.SkuId` telemetriai célokra be kell állítaniuk az egyedi modulazonosítót. Az egyedi azonosító lehetővé teszi, hogy az Azure Marketplace-en azonosítsa a futó modulpéldányok számát.

 Az IoT-modul SDK-inak alábbi `ProductInfo` módszereivel állítsa be ezt az azonosítót:

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

Az IoT-modul SDK-t nem használó modulok esetében kevésbé pontos elemzések érhetők el a Cloud Partner Portalon keresztül, például a letöltések száma.

### <a name="security"></a>Biztonság

Az IoT Edge-moduloknak a lehető legkevésbé kiemelt hozzáférést kell kérniük a gazdagéphez. [A kiemelt modulokat](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) el kell kerülni.

### <a name="module-iot-sdk"></a>IoT SDK modul

Az IoT-modul SDK-jának belefoglalása nem előfeltétele a minősítésnek. Azonban az IoT-modul SDK-val együtt jobb felhasználói élményt nyújthat. Például az útválasztás támogatása vagy üzenetek küldése a felhőbe.

Az IoT-modul SDK-ra van szükség a futó modulpéldányok számának telemetriai adatainak levezetéséhez.


## <a name="recertification-process"></a>Újraengedélyezési folyamat

<!-- Add legal time windows-->
A partnerek értesítést kapnak minden olyan esetben, amikor a moduljukat érintő változás történik, például:

- Tier 1 os/arch támogatási mátrix által támogatott IoT Edge
- IoT-modul SDK
- IoT peremhálózati futásidejű
- Az IoT Edge modul tanúsítási irányelvei

A partnereknek frissíteniük kell a moduljaikat, és újra kell tanúsítaniuk azokat a Cloud Partner Portal eszközzel.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>Az IoT Edge-modul üzemeltetése egy Azure Container Registry-ben

Az IoT Edge-modul feltöltéséhez a felhőpartner-portálra először egy Azure Container Registry (ACR) üzemeltetéséhez kell [üzemeltetnie.](https://azure.microsoft.com/services/container-registry/) A modulnak tartalmaznia kell a közzétenni kívánt összes címkét, beleértve a jegyzékcímkét hivatkozott képcímkéket is.


## <a name="next-steps"></a>További lépések

- [Az IoT Edge modulajánlat létrehozása](./cpp-create-offer.md)
