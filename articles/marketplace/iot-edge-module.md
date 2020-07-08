---
title: Azure Marketplace IoT Edge modul ajánlatok
description: Tudnivalók az Azure Marketplace-en elérhető IoT Edge-modulok közzétételéről.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 9783eb1dbcbfc3ee01008dfa33ff2429a87cf62a
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963376"
---
# <a name="iot-edge-modules"></a>IoT Edge-modulok

A [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) platformot Microsoft Azure támogatja.  Ez a platform lehetővé teszi a felhasználók számára, hogy közvetlenül a IoT-eszközökön futtassák a Felhőbeli számítási feladatokat.  Az IoT Edge-modulok offline számítási feladatokat futtathatnak, és helyileg is végezhetik az adatelemzést. Ez az ajánlat típusa segít a sávszélesség megtakarításában, a helyi és bizalmas adatok védelmében, és alacsony késésű válaszidőt biztosít.  Ezzel a lehetőséggel kihasználhatja ezeket az előre elkészített számítási feladatokat. Eddig csak néhány, a Microsofttól származó, első féltől származó megoldás érhető el.  Az időt és az erőforrásokat a saját egyéni IoT-megoldásainak kiépítése során kellett befektetni.

[Az Azure Marketplace IoT Edge moduljaival](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)most már egyetlen célhelyet kínálunk a kiadók számára, hogy elérhetővé tegye és eladják megoldásait a IoT-közönség számára. A IoT-fejlesztők végső soron megtalálhatják és megvásárolhatják a megoldás-fejlesztés felgyorsításához szükséges képességeket.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>A IoT Edge-modulok főbb előnyei az Azure Marketplace-en

| **Közzétevők számára**    | **Ügyfelek számára (IoT-fejlesztők)**  |
| :------------------- | :-------------------|
| Több millió fejlesztőt érhet el IoT Edge megoldások létrehozásához és üzembe helyezéséhez.  | Hozzon létre egy IoT Edge megoldást a biztonságos és tesztelt összetevők használatának megbízhatóságával. |
| Tegye közzé egyszer, és futtassa az összes olyan IoT Edge hardvert, amely támogatja a tárolókat. | Csökkentse a piacra kerülési időt azáltal, hogy megkeresi és üzembe helyezi az 1. és a harmadik féltől származó IoT Edge-modulokat. |
| Pénzbevétel rugalmas számlázási lehetőségekkel <ul> <li> Ingyenes és saját licenc használata (BYOL). </li> </ul> | A vásárlásokat a választott számlázási modellekkel teheti meg. <ul> <li> Ingyenes és saját licenc használata (BYOL). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>Mi az IoT Edge modul?

Azure IoT Edge lehetővé teszi az üzleti logikák üzembe helyezését és kezelését modulok formájában. A Azure IoT Edge modulok a IoT Edge által kezelt legkisebb számítási egységek, és tartalmazhatnak Microsoft-szolgáltatásokat (például Azure Stream Analytics), harmadik féltől származó szolgáltatásokat vagy a saját megoldásokra vonatkozó kódokat. IoT Edge modulokról további információt a [Azure IoT Edge modulok ismertetése](../iot-edge/iot-edge-modules.md)című témakörben talál.

**Mi a különbség a Container ajánlat típusa és az IoT Edge modul típusú ajánlat között?**

A IoT Edge modul ajánlat típusa egy IoT Edge eszközön futó adott típusú tároló. A szolgáltatás az alapértelmezett konfigurációs beállításokat használja a IoT Edge környezetben való futtatáshoz, és opcionálisan a IoT Edge modul SDK-val is integrálható a IoT Edge futtatókörnyezettel.

## <a name="publishing-your-iot-edge-module"></a>A IoT Edge-modul közzététele

**A megfelelő kirakat kiválasztása**

IoT Edge modulokat csak az Azure Marketplace-en tesszük közzé, a AppSource nem alkalmazható.  További információ a kirakatok közötti különbségekről és célközönségről: [a megoldás közzétételi lehetőségének meghatározása](determine-your-listing-type.md).
 
**Számlázási beállítások**

A piactér jelenleg **ingyenes** , és **saját licenc (BYOL)** számlázási lehetőségeket biztosít IoT Edge modulokhoz.
 
**Közzétételi beállítások**

IoT Edge moduloknak minden esetben ki kell választaniuk a **Transact** Publishing beállítást.  A közzétételi beállításokkal kapcsolatos további részletekért tekintse meg [a közzétételi lehetőség kiválasztása](determine-your-listing-type.md) című témakört.  

## <a name="eligibility-criteria"></a>Jogosultsági feltételek

A Microsoft Azure Marketplace-szerződések és-szabályzatok minden feltétele IoT Edge modul-ajánlatok esetében érvényes.  Emellett a IoT Edge modulokra vonatkozó előfeltételeket és műszaki követelményeket is megkövetelnek.  

**Előfeltételek**

IoT Edge modul Azure Marketplace-en való közzétételéhez meg kell felelnie a következő előfeltételeknek:

- Hozzáférés a partner központhoz. További információ: az [Azure Marketplace és a AppSource közzétételi útmutatója](marketplace-publishers-guide.md).
- IoT Edge moduljának üzemeltetése egy Azure Container Registry. 
- A IoT Edge modul metaadatainak készen állnak, például (nem teljes lista): 
    - Egy cím
    - Leírás (HTML formátumban)
    - Egy embléma képe (PNG-formátum és rögzített képméret, beleértve az 40x40px, a 90x90px, a 115x115px, a 255x115px)
    - Használati feltételek és adatvédelmi szabályzat
    - Alapértelmezett modul konfigurációja (Route, Twin kívánt tulajdonságok, createOptions, környezeti változók)
    - Dokumentáció
    - Támogatási kapcsolattartók

**Technikai követelmények**

Az IoT Edge modul elsődleges műszaki követelményei, hogy az informatikai támogatás és közzététel az Azure Marketplace-en, részletesen megtalálhatók a [IoT Edge modul előkészítésének technikai eszközein](./partner-center-portal/create-iot-edge-module-asset.md).

## <a name="documentation-and-resources"></a>Dokumentáció és erőforrások

[Hozzon létre egy IoT Edge modul-ajánlatot](./partner-center-portal/azure-iot-edge-module-creation.md) – ez a lépés egy új IoT Edge modul ajánlatának közzétételéhez a partner Centerben.

## <a name="next-steps"></a>További lépések

Ha még nem tette meg,

- [További](https://azuremarketplace.microsoft.com/sell) információ a Piactérről.

A partner Centerben való regisztrációhoz, és egy új ajánlat létrehozásához vagy egy meglévő alkalmazáshoz való munka megkezdéséhez

- Az ajánlat létrehozásához vagy befejezéséhez jelentkezzen be a [partner Centerben](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) .
- A IoT Edge modul ajánlatának közzétételéről a [IoT Edge modul létrehozása](./partner-center-portal/azure-iot-edge-module-creation.md) című témakörben talál további információt.
