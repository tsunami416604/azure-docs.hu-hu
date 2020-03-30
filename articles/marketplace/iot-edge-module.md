---
title: Azure IoT peremhálózati modulok
description: Az IoT Edge-modul ajánlat az Azure Piactéren az alkalmazás- és szolgáltatáskiadók számára.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/22/2018
ms.author: dsindona
ms.openlocfilehash: aadbf33914f919e393a5ec88cf6fc0a6103911b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286149"
---
# <a name="iot-edge-modules"></a>IoT Edge-modulok

Az [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) platform ot az Azure Cloud támogatja.  Ez a platform lehetővé teszi a felhasználók számára, hogy a felhőbeli számítási feladatok közvetlenül az IoT-eszközökön fussanak.  Az IoT Edge-modul offline számítási feladatok futtatásához, és az adatok elemzése helyileg. Ez az ajánlattípus segít a sávszélesség megtakarításában, a helyi és bizalmas adatok védelmében, valamint alacsony késleltetésű válaszidőt biztosít.  Most már rendelkezik az előre elkészített számítási feladatok előnyeit. Mostanáig csak néhány belső megoldás állt rendelkezésre a Microsofttól.  Időt és erőforrásokat kellett befektetnie saját egyéni IoT-megoldásai létrehozásába.

Az [IoT Edge-modulok](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)azure piactéren való bevezetésével mostantól egyetlen céllal rendelkezünk a kiadók számára, hogy elérhetővé tévék és értékesítsék megoldásaikat az IoT-közönség nek. Az IoT-fejlesztők végső soron megtalálhatják és megvásárolhatják a megoldásfejlesztés felgyorsítása érdekében szükséges képességeket.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Az IoT Edge-modulok legfontosabb előnyei az Azure Marketplace-en:

| **Kiadók nak**    | **Ügyfeleknek (IoT-fejlesztőknek)**  |
| :------------------- | :-------------------|
| Az IoT Edge-megoldások at fejlesztőinek millióihoz juthat el.  | Az IoT Edge-megoldásokat biztonságos és tesztelt összetevők használatával komponálja. |
| Egyszer tegye közzé, és futtassa az ioT Edge-hardverek, amely támogatja a tárolók. | Csökkentse a piacra jutási időt az első és harmadik féltől származó IoT Edge-modulok keresésével és üzembe helyezésével az egyedi igényekhez. |
| Pénzt keresni a rugalmas számlázási lehetőségekkel <ul> <li> Ingyenes és hozd a saját licenc (BYOL). </li> </ul> | Vásároljon a kiválasztott számlázási modellekkel. <ul> <li> Ingyenes és hozd a saját licenc (BYOL). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>Mi az IoT Edge-modul?

Az Azure IoT Edge lehetővé teszi az üzleti logika üzembe helyezését és kezelését a peremhálózaton modulok formájában. Az Azure IoT Edge-modulok az IoT Edge által kezelt legkisebb számítási egységek, és tartalmazhatnak Microsoft-szolgáltatásokat (például Az Azure Stream Analytics), a harmadik féltől származó szolgáltatásokat vagy a saját megoldásspecifikus kódot. Ha többet szeretne megtudni az IoT Edge-modulokról, [olvassa el az Azure IoT Edge-modulok megismerése.](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)

**Mi a különbség a tárolóajánlat típusa és az IoT Edge-modul ajánlattípusa között?**

Az IoT Edge-modul ajánlattípusa egy adott típusú tároló fut egy IoT Edge-eszközön. Az IoT Edge-környezetben futtatható alapértelmezett konfigurációs beállításokkal rendelkezik, és opcionálisan az IoT Edge-modul SDK-ját használja az IoT Edge futásidőbe való integráláshoz.

## <a name="publishing-your-iot-edge-module"></a>Az IoT Edge-modul közzététele

**A megfelelő kirakat kiválasztása**

Az IoT Edge-modulok csak az Azure Marketplace-en vannak közzétéve, az AppSource nem vonatkozik.  A kirakatokban fennálló különbségekről és célközönségről a [megoldás közzétételi lehetőségének meghatározása](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)című témakörben talál további információt.
 
**Számlázási lehetőségek**

A piactér jelenleg támogatja az **ingyenes** és **hozza el a saját licenc (BYOL)** számlázási lehetőségek IoT Edge modulok.
 
**Közzétételi beállítások**

Minden esetben az IoT Edge-moduloknak ki kell választaniuk a **Transact** közzétételi lehetőséget.  A közzétételi beállításokkal kapcsolatos további részletekért [válasszon közzétételi lehetőséget.](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)  

## <a name="eligibility-criteria"></a>Jogosultsági feltételek

A Microsoft Azure Piactér-szerződések és -szabályzatok összes feltétele az IoT Edge modulajánlatokra vonatkozik.  Emellett az IoT Edge-modulok előfeltételei és műszaki követelményei is vannak.  

**Előfeltételek**

IoT Edge-modul közzététele az Azure Marketplace-en, meg kell felelnie az alábbi előfeltételeknek:

- Hozzáférés a felhőpartneri portálhoz (CPP). További információ: [Azure Marketplace és AppSource közzétételi útmutató.](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)
- Az IoT Edge-modul tanusködése egy Azure Container Registry. 
- Az IoT Edge-modul metaadatai készen állnak, például (nem teljes lista): 
    - A cím
    - Leírás (HTML formátumban)
    - Emblémakép (PNG formátum és rögzített képméretek, beleértve a 40x40px, 90x90px, 115x115px, 255x115px)
    - A használati idő és az adatvédelmi politika
    - Alapértelmezett modulkonfiguráció (útvonal, iker kívánt tulajdonságok, createOptions, környezeti változók)
    - Dokumentáció
    - Támogatási kapcsolattartók

**Műszaki követelmények**

Az IoT Edge-modul elsődleges technikai követelményeit, hogy minősítést kapjon és közzétehessen az Azure Marketplace-en, az [IoT Edge modul technikai eszközeinek előkészítése](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets)című ismerteti részletesen ismerteti.  

## <a name="documentation-and-resources"></a>Dokumentáció és források

[Hozzon létre egy IoT Edge-modul ajánlat](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-offer) – Az új IoT Edge-modul ajánlat a felhőbeli közzétételi portál on közzétételi lépéseket.

## <a name="next-steps"></a>További lépések

Ha még nem tette meg,

- [Regisztráljon](https://azuremarketplace.microsoft.com/sell) a piacon.

Ha regisztrált, és új ajánlatot hoz létre, vagy egy meglévőn dolgozik,

- Jelentkezzen be a [Cloud Partner Portalszolgáltatásba](https://cloudpartner.azure.com/) az ajánlat létrehozásához vagy befejezéséhez.
- Tekintse meg [az IoT Edge-modul közzétételi áttekintését](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts) az IoT Edge-modulajánlat közzétételéről.
