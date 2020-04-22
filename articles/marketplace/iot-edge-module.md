---
title: Azure Marketplace IoT peremhálózati modulok
description: Az IoT Edge-modul ajánlat az Azure Piactéren az alkalmazás- és szolgáltatáskiadók számára.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 762d9947046f159e992f09211bfcd76ff8d6712e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684335"
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

- Hozzáférés a Partnerközponthoz. További információ: [Azure Marketplace és AppSource közzétételi útmutató.](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)
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

Az IoT Edge-modul elsődleges technikai követelményeit, hogy minősítést kapjon és közzétehessen az Azure Marketplace-en, az [IoT Edge modul technikai eszközeinek előkészítése](./partner-center-portal/create-iot-edge-module-asset.md)című ismerteti részletesen ismerteti.

## <a name="documentation-and-resources"></a>Dokumentáció és források

[IoT Edge-modulajánlat létrehozása](./partner-center-portal/azure-iot-edge-module-creation.md) – Egy új IoT Edge-modul ajánlat a Partner Centerben való közzétételének lépései.

## <a name="next-steps"></a>További lépések

Ha még nem tette meg,

- [További információ](https://azuremarketplace.microsoft.com/sell) a piactérről.

A Partnerközpontban való regisztrációhoz és új ajánlat létrehozásához vagy egy meglévő

- Jelentkezzen be a [Partnerközpontba](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) az ajánlat létrehozásához vagy teljesítéséhez.
- [Tekintse meg az IoT Edge-modul ajánlat ának létrehozása](./partner-center-portal/azure-iot-edge-module-creation.md) az IoT Edge-modulajánlat közzétételéről.
