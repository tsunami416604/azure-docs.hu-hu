---
title: Az Azure-beli Cloudyn áttekintése | Microsoft Docs
description: A Cloudyn egy többfelhős költségkezelő megoldás, amely segítséget nyújt az Azure és egyéb felhőerőforrások használatához.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/06/2018
ms.topic: overview
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 5c99d638845cf0cd00ddbacf1ed0dcf3da889dd6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53082543"
---
# <a name="what-is-the-cloudyn-service"></a>Mi a Cloudyn szolgáltatás?

A Microsoft leányvállalata, a Cloudyn lehetővé teszi az Azure-erőforrások és egyéb felhőszolgáltatók felhőhasználatának és költségeinek követését, az AWS-t és a Google-t is beleértve. A könnyen értelmezhető irányítópult-jelentések segítenek a költséglefoglalásban és a költséghelyi visszacsatolásban/elszámolásban. A Cloudyn segít optimalizálni a felhő költségeit, mivel azonosítja azokat az alacsony kihasználtságú erőforrásokat, amelyeket kezelhet és módosíthat.

Bevezető videó: [Az Azure Cloudyn bemutatása](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo).

Az Azure Cost Management a Cloudynhez hasonló funkcionalitást kínál. Az Azure Cost Management egy natív Azure költségkezelő megoldás. Segít kezelni a költségvetéseket, exportálni az adatokat, valamint áttekinteni és végrehajtani az optimalizálási javaslatokat pénzmegtakarítás céljából. További információ: [Azure Cost Management](overview-cost-mgt.md).

## <a name="monitor-usage-and-spending"></a>Használat és költségek monitorozása

A használat és a költségek monitorozása kritikus fontosságú a felhőalapú infrastruktúrákhoz, mert a szervezetek fizetnek az általuk használt erőforrásokért. Amikor a használat túllép a szerződés küszöbértékeken, gyorsan váratlan többletköltségek léphetnek fel. Néhány fontos tényező megnehezítheti az alkalmi monitorozást. Először is az átlagos használaton alapuló költségkivetítés feltételezi, hogy a fogyasztása állandó marad egy adott számlázási időszak egészében. Másodszor, amikor a költségek a költségvetés közelében vannak vagy túllépik azt, fontos proaktív módon fogadni az értesítéseket, hogy módosíthassa a kiadásokat. előfordulhat az is, hogy a felhőszolgáltatók nem biztosítják a költségek kivetítését és a küszöbértékeket összehasonlító vagy az időszakokat összehasonlító jelentéseket.

A jelentések segítenek a kiadások monitorozásában a felhőhasználat, a költségek és trendek elemzése és megfigyelése érdekében. Az időalapú jelentésekkel észlelheti a normál trendektől eltérő rendellenességeket. A felhőben üzemelő szolgáltatások kis hatékonysága jól látható az optimalizálási jelentésekben. A költségelemzési jelentésekben is észreveheti a nem hatékony felhasználást.

## <a name="manage-costs"></a>Költségek kezelése

Az előzményadatok segíthetnek a költségek kezelésében, amikor az idő függvényében elemzi a használatot és költségeket a trendek azonosítása érdekében. A trendekkel ezután előre jelezhetők a jövőbeli kiadások. A Cloudyn hasznos előrejelzéseket tartalmazó költségjelentéseket is tartalmaz.

A költséglefoglalás a címkézési szabályzata alapján a költségek elemzésével kezeli a költségeket. Az egyéni fiókokon, erőforrásokon és entitásokon címkéket használhat a költséglefoglalás pontosításához. A Category Manager rendszerezi a címkéket a jobb irányítás érdekében. Emellett költséglefoglalást használhat a költséghelyi visszacsatoláshoz/elszámoláshoz is, hogy az erőforrás-használat és a társított költségek bemutatásával befolyásolja a fogyasztási viselkedést, vagy a költségeket a bérlő ügyfelekre terhelje.

A hozzáférés-vezérlés a költségek vezérlésének elősegítése érdekében biztosítja, hogy a felhasználók és a csapatok csak a nekik szükséges költségkezelési adatokat érjék el. Entitásstruktúrát, felhasználófelügyeletet és ütemezett jelentéseket használ a címzettlistákkal a hozzáférés hozzárendelése érdekében.

A riasztások kezelése a költségek felügyeletének elősegítése érdekében automatikusan értesíti a szokatlan kiadások vagy túlköltekezés esetén. A riasztások más résztvevőket is automatikusan értesíthetnek a kiadások rendellenességeiről és a túlköltekezés kockázatairól. Különböző jelentések támogatják a riasztásokat a költségvetés és a költségek küszöbértékei alapján. Ezek a riasztások azonban jelenleg nem támogatottak a CSP-partnerfiókokhoz és -előfizetésekhez.

## <a name="improve-efficiency"></a>A hatékonyság javítása

A Cloudyn segítségével meghatározhatja az optimális virtuálisgép-használatot és azonosíthatja a tétlen virtuális gépeket vagy távoli tétlen virtuális gépeket és a leválasztott lemezeket. A méretoptimalizálással és elégtelenségekkel kapcsolatos jelentésekben lévő információkkal létrehozhat egy tervet a tétlen virtuális gépek méretének csökkentéséhez vagy eltávolításához. Az optimalizálási jelentések azonban jelenleg nem támogatottak a CSP-partnerfiókokhoz és -előfizetésekhez.

Ha AWS lefoglalt példányokat helyezett üzembe, az optimalizálási jelentésekkel javíthatja a lefoglalt példányok kihasználtságát, mert ezekben megtekintheti a vásárlással kapcsolatos javaslatokat, módosíthatja a nem használt lefoglalásokat, és megtervezheti az üzembe helyezést.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a Cloudynt, a következő lépés a felhőalapú környezet regisztrálása és az adatok megismerése.

- [Egyéni Azure-előfizetés regisztrálása](quick-register-azure-sub.md)
