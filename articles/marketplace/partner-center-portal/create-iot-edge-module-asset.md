---
title: Az IoT Edge-modul technikai eszközeinek előkészítése - Azure Marketplace
description: Ismerje meg a technikai és konfigurációs követelményeket a dolgok internete (IoT) Edge modul technikai eszközök meg kell felelnie, mielőtt közzéteheti őket az Azure Marketplace-en.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 2c0cd47acbd4639ff5eff2af78dcebdfc26270a7
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730699"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Készítse elő az IoT Edge modul technikai eszközeit

> [!IMPORTANT]
> Az IoT Edge modulajánlatok kezelését áthelyezzük a Cloud Partner Portalról a Partner centerbe. Amíg az ajánlatok áttelepítése, kövesse az utasításokat [az IoT Edge modul technikai eszközök cloud](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets) partner portál az ajánlatok kezeléséhez.

Ez a cikk ismerteti azokat a követelményeket, amelyeket az eszközök internetes hálózatának (IoT) Edge-modul technikai eszközeinek meg kell felelniük az Azure Marketplace-en való közzététel előtt.

## <a name="get-started"></a>Bevezetés

Az IoT Edge-modul egy Docker-kompatibilis tároló, amely egy IoT Edge-eszközön fut.

- Ha többet szeretne megtudni az IoT Edge-modulokról, [olvassa el az Azure IoT Edge-modulok megismerése.](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)
- Az IoT Edge-modul fejlesztésének megkezdéséhez olvassa [el a Saját IoT Edge-modulok fejlesztése.](https://docs.microsoft.com/azure/iot-edge/module-development)

## <a name="technical-requirements"></a>Technikai követelmények

Az IoT Edge-modulnak meg kell felelnie az alábbi műszaki követelményeknek az Azure Marketplace-en való minősítéshez és közzétételéhez.

### <a name="platform-support"></a>Platformtámogatás

Az IoT Edge-modulnak az alábbi platformbeállítások egyikét kell támogatnia:

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Az IoT Edge által támogatott 1.

A modulnak támogatnia kell az IoT Edge által támogatott összes Tier 1 platformot (az [Azure IoT Edge-támogatásban rögzítettek szerint).](https://docs.microsoft.com/azure/iot-edge/support) Ezt a lehetőséget azért javasoljuk, mert jobb felhasználói élményt nyújt. Az e kritériumoknak megfelelő modulokbemutatásra kerülnek. A platformopciót használó modulnak:

- Adjon meg egy legújabb címkét és egy verziócímkét (például 1.0.1), amelyek a [GitHub Manifest-eszközzel](https://github.com/estesp/manifest-tool)készített jegyzékcímkék.

- A [PartnerCenter](https://partner.microsoft.com/dashboard/commercial-marketplace) ajánlatlista lapján hivatkozást adhat hozzá az [Azure IoT Edge Certified eszközkatalógus](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/) **hasznos hivatkozások** szakaszában.

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Az IoT Edge által támogatott Tier 1 platformok egy részhalmaza

A modulnak támogatnia kell az IoT Edge által támogatott Tier 1 platformok egy részét (legalább egyet) (az [Azure IoT Edge-támogatásban](https://docs.microsoft.com/azure/iot-edge/support)rögzítettek szerint). A platformopciót használó modulnak:

- Adjon meg egy legújabb címkét és egy verziócímkét (például 1.0.1), amelyek a GitHub [jegyzékfájl-eszközzel](https://github.com/estesp/manifest-tool) készített jegyzékcímkék, ha egynél több platform támogatott. A jegyzékcímkék csak akkor választhatók, ha egy platform támogatott.
- A [PartnerCenter](https://partner.microsoft.com/dashboard/commercial-marketplace) ajánlatlista lapján az [Azure IoT Edge-tanúsítvánnyal rendelkező eszközkatalóguslegalább](https://catalog.azureiotsolutions.com/)egy IoT Edge-eszközhöz mutató hivatkozást adhat hozzá a **Hasznos hivatkozások** szakaszban.

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="Ez a partnerközpont ajánlatlistázási szakaszának képe":::

### <a name="device-dimensions"></a>Eszközméretek

Az IoT Edge-modul méretei (például a CPU, a RAM, a tárolás és a GPU) a célzott IoT Edge-eszközökön az alábbi követelményeknek kell megfelelniük:

- A modulnak legalább egy IoT Edge-eszközzel együtt kell működnie az [Azure IoT Edge certified eszközkatalógusból.](https://catalog.azureiotsolutions.com/)

- A minimális hardverkövetelményeket az ajánlat leírásának utolsó bekezdéseként kell dokumentálni (a [Partnerközpont](https://partner.microsoft.com/dashboard/commercial-marketplace)ajánlatlista lapján). Szükség esetén az ajánlott hardverkövetelményeket is felsorolhatja, ha azok jelentősen eltérnek egymástól. Adja meg például a következő szakaszt az ajánlat leírásának végén:

Másolja a HTML-szöveget, vagy használja a megfelelő rich text függvényeket a szerkesztőablakban.

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>Konfiguráció

A modulnak tartalmaznia kell az alapértelmezett konfigurációs beállításokat, hogy az IoT Edge-eszköz üzembe helyezése a lehető legegyszerűbb legyen. Ez az információ a partnerközpontban található terv **műszaki konfigurációja** [lapján](https://partner.microsoft.com/dashboard/commercial-marketplace)található. A tároló is tartalmazhat az IoT Edge module SDK a peremhálózati hub és az IoT Hub közötti kommunikáció engedélyezéséhez.

#### <a name="default-configuration"></a>Alapértelmezett konfiguráció

Az IoT Edge-moduloknak a partnerközpontban a terv **műszaki konfigurációs** lapján megadott alapértelmezett beállításokkal kell kezdődniük. [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace) A következő alapértelmezett beállítások érhetők el:

- Alapértelmezett **útvonalak**
- Alapértelmezett **modul iker kívánt tulajdonságai**
- Alapértelmezett **környezeti változók**
- Alapértelmezett **tárolólétrehozási beállítások**

Olyan esetben, amikor az alapértelmezett értékhez szükséges paraméternek nincs értelme (például az ügyfél kiszolgálójának IP-címe), adjon hozzá egy paramétert alapértelmezett értékként. Ez az érték nagybetűs, és zárójelek közé van tava. Ebben a példában a következő alapértelmezett környezeti változót állította be:

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Konfigurációs dokumentáció

Az IoT Edge-modul összes konfigurációs beállítását egyértelműen dokumentálni kell. Például dokumentálnia kell, hogyan kell használni az útvonalakat, az iker kívánt tulajdonságokat, a környezeti változókat, a createOptions-t és így tovább. Meg kell adnia egy hivatkozást a dokumentációhoz, vagy az ajánlat vagy a terv leírásának részét kell tennie. Ezt az információt a Partnerközpont **Ajánlatlista** és **Megtervezés oldalán** adhatod [meg.](https://partner.microsoft.com/dashboard/commercial-marketplace)

#### <a name="tags-and-versioning"></a>Címkék és verziószámozás

Az ügyfeleknek képesnek kell lenniük arra, hogy könnyen üzembe helyezzenek egy modult, és automatikusan frissítéseket kapjanak a piactérről (fejlesztői forgatókönyv esetén). Emellett képesnek kell lenniük az általuk tesztelt pontos verzió használatára és fagyasztására (éles környezetben).

Az ügyfelek elvárásainak való megfelelés és a piactéren való közzététel érdekében az IoT Edge-moduloknak meg kell felelniük az alábbi követelményeknek:

- Adjon meg egy olyan legújabb jegyzéklistát, amely az összes támogatott platformon a legújabb verzióra mutat.
- A verziócímkéket X.Y.Z formátumban készítse el, ahol X, Y és Z egész számok.
- Adjon meg egy "verzió" címkét, például az 1.0.1-et, amely az összes támogatott platformon egy adott verzióra mutat.
- Ne frissítse a "verzió" címkéket, például az 1.0.1-et, mert azokat nem szabad módosítani.

> [!NOTE]
> A verziószámozás tartalmazhat "gördülő verzió" címkéket, például 2.0-s és 1.0-s címkéket. Ez több főverzió párhuzamos karbantartását támogatja.

### <a name="telemetry"></a>Telemetria

Az IoT-modul SDK-t használó moduloknak telemetriai célokra be kell állítaniuk az egyedi modulazonosítót PublisherId.OfferId.SkuId-ra. Az egyedi azonosító segítségével az Azure Marketplace-en azonosíthatja a futó modulpéldányok számát.

Az IoT-modul SDK-inak alábbi módszereinek egyikével állítsa be a ProductInfo azonosítót erre az azonosítóra:

- [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

Az IoT-modul SDK-t nem használó modulok esetében a Partnerközponton keresztül kevésbé pontos elemzések érhetők el, például a letöltések száma.

### <a name="security"></a>Biztonság

Az IoT Edge-moduloknak kerülniük kell [a kiemelt modulokat.](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) Ehelyett kérje a lehető legkevésbé kiváltságos hozzáférést a gazdagéphez.

### <a name="module-iot-sdk"></a>IoT SDK modul

Az IoT-modul SDK-jának belefoglalása nem előfeltétele a minősítésnek. Azonban az IoT-modul SDK-val együtt jobb felhasználói élményt nyújthat. Például az útválasztás támogatása vagy üzenetek küldése a felhőbe.

Az IoT-modul SDK-ra van szükség a futó modulpéldányok számának telemetriai adatainak leéséhez.

## <a name="recertification-process"></a>Újraengedélyezési folyamat

A partnerek értesítést kapnak, ha a modulokat érintő törésváltozás történik, például:

- Tier 1 OS/arch támogatási mátrix által támogatott IoT Edge
- IoT-modul SDK
- IoT Edge-futtatókörnyezet
- Az IoT Edge modul tanúsítványának irányelvei

A partnereknek frissíteniük kell és újra jóvá kell hagyniuk ajánlataikat, ha újra közzéteszik őket a [Partnerközpontban.](https://partner.microsoft.com/dashboard/commercial-marketplace)

Az ajánlat is újra certified, ha frissíti, például új képcímkék hozzáadása.

## <a name="host-module-in-azure-container-registry"></a>Gazdamodul az Azure Container Registry szolgáltatásban

Az IoT Edge-modul feltöltéséhez az Azure Marketplace-en először egy Azure Container Registry (ACR) üzemeltetéséhez kell [üzemeltetni.](https://azure.microsoft.com/services/container-registry/) A modulnak tartalmaznia kell a közzétenni kívánt összes címkét, beleértve a jegyzékcímkét hivatkozott képcímkéket is. További információ: Az [Azure-tároló beállításjegyzékének létrehozása és leküldéses tárolórendszerkép.](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-acr)

## <a name="next-steps"></a>További lépések

- [IoT Edge-modulajánlat létrehozása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)