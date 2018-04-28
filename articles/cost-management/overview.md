---
title: Az Azure Cost Management áttekintése | Microsoft Docs
description: Az Azure Cost Management egy többfelhős költségkezelő megoldás, amely segítséget nyújt az Azure és egyéb felhőerőforrások használatához.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: overview
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 05e53688e1350052fdbbc61451df8a51dc3349cd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-azure-cost-management"></a>Mi az Azure Cost Management?

A Microsoft Cloudyn nevű leányvállalata által licencelt Azure Cost Management használatával követheti az Azure-erőforrások és egyéb felhőszolgáltatók felhőhasználatát és költségeit és, az AWS-t és a Google-t is beleértve. A könnyen értelmezhető irányítópult-jelentések segítenek a költséglefoglalásban és a költséghelyi visszacsatolásban/elszámolásban. A Cost Management segít optimalizálni a felhő költségeit, mivel azonosítja azokat az alacsony kihasználtságú erőforrásokat, amelyeket kezelhet és módosíthat.

Bevezető videó: [Az Azure Cost Management bemutatása](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo).

## <a name="monitor-usage-and-spending"></a>Használat és költségek monitorozása

A használat és a költségek monitorozása kritikus fontosságú a felhőalapú infrastruktúrákhoz, mert a szervezetek fizetnek az általuk használt erőforrásokért. Amikor a használat túllép a szerződés küszöbértékeken, gyorsan váratlan többletköltségek léphetnek fel. Néhány fontos tényező megnehezítheti az alkalmi monitorozást. Először is az átlagos használaton alapuló költségkivetítés feltételezi, hogy a fogyasztása állandó marad egy adott számlázási időszak egészében. Másodszor, amikor a költségek a költségvetés közelében vannak vagy túllépik azt, fontos proaktív módon fogadni az értesítéseket, hogy módosíthassa a kiadásokat. előfordulhat az is, hogy a felhőszolgáltatók nem biztosítják a költségek kivetítését és a küszöbértékeket összehasonlító vagy az időszakokat összehasonlító jelentéseket.

A jelentések segítenek a kiadások monitorozásában a felhőhasználat, a költségek és trendek elemzése és megfigyelése érdekében. Az időalapú jelentésekkel észlelheti a normál trendektől eltérő rendellenességeket. A felhőben üzemelő szolgáltatások kis hatékonysága jól látható az optimalizálási jelentésekben. A költségelemzési jelentésekben is észreveheti a nem hatékony felhasználást.

![Időalapú költségek jelentése](media\overview\cost-over-time-rpt.png)


## <a name="manage-costs"></a>Költségek kezelése

Az előzményadatok segíthetnek a költségek kezelésében, amikor az idő függvényében elemzi a használatot és költségeket a trendek azonosítása érdekében. A trendekkel ezután előre jelezhetők a jövőbeli kiadások. A Cost Management hasznos előrejelzéseket tartalmazó költségjelentéseket is tartalmaz.

A költséglefoglalás a címkézési szabályzata alapján a költségek elemzésével kezeli a költségeket. Az egyéni fiókokon, erőforrásokon és entitásokon címkéket használhat a költséglefoglalás pontosításához. A Category Manager rendszerezi a címkéket a jobb irányítás érdekében. Emellett költséglefoglalást használhat a költséghelyi visszacsatoláshoz/elszámoláshoz is, hogy az erőforrás-használat és a társított költségek bemutatásával befolyásolja a fogyasztási viselkedést, vagy a költségeket a bérlő ügyfelekre terhelje.

A hozzáférés-vezérlés a költségek vezérlésének elősegítése érdekében biztosítja, hogy a felhasználók és a csapatok csak a nekik szükséges költségkezelési adatokat érjék el. Entitásstruktúrát, felhasználófelügyeletet és ütemezett jelentéseket használ a címzettlistákkal a hozzáférés hozzárendelése érdekében.

A riasztások kezelése a költségek felügyeletének elősegítése érdekében automatikusan értesíti a szokatlan kiadások vagy túlköltekezés esetén. A riasztások más résztvevőket is automatikusan értesíthetnek a kiadások rendellenességeiről és a túlköltekezés kockázatairól. Különböző jelentések támogatják a riasztásokat a költségvetés és a költségek küszöbértékei alapján. Ezek a riasztások azonban jelenleg nem támogatottak a CSP-partnerfiókokhoz és -előfizetésekhez.

## <a name="improve-efficiency"></a>A hatékonyság javítása

A Cost Management segítségével meghatározhatja az optimális virtuálisgép-használatot és azonosíthatja a tétlen virtuális gépeket vagy távoli tétlen virtuális gépeket és a leválasztott lemezeket. A méretoptimalizálással és elégtelenségekkel kapcsolatos jelentésekben lévő információkkal létrehozhat egy tervet a tétlen virtuális gépek méretének csökkentéséhez vagy eltávolításához. Az optimalizálási jelentések azonban jelenleg nem támogatottak a CSP-partnerfiókokhoz és -előfizetésekhez.

![méretezési javaslatok](.\media\overview\sizing.png)

Ha AWS lefoglalt példányokat helyezett üzembe, az optimalizálási jelentésekkel javíthatja a lefoglalt példányok kihasználtságát, mert ezekben megtekintheti a vásárlással kapcsolatos javaslatokat, módosíthatja a nem használt lefoglalásokat, és megtervezheti az üzembe helyezést.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a Cost Managementet, a következő lépés a felhőalapú környezet regisztrálása és az adatok megismerése.

- [Egyéni Azure-előfizetés regisztrálása](quick-register-azure-sub.md)
