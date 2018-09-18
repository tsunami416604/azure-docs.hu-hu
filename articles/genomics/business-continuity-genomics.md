---
title: Üzleti folytonosság – a Microsoft Genomics |} A Microsoft Docs
titleSuffix: Azure
description: Ez az Áttekintés a Microsoft Genomics biztosítja az üzletmenet-folytonosság és vészhelyreállítás funkcióit írja le. Ismerje meg azokat a káros eseményeket, például az Azure-régióban leállás, amelyek az adatvesztést okozhatnak a beállításokat.
keywords: üzleti folytonosság, vészhelyreállítás
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: article
ms.date: 04/06/2018
ms.openlocfilehash: be678648ab93fcbdfd0a0baa1b01dcb273060ce2
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729452"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>A Microsoft Genomics üzletmenet-folytonossági funkcióinak áttekintése
Ez az Áttekintés a Microsoft Genomics biztosítja az üzletmenet-folytonosság és vészhelyreállítás funkcióit írja le. Ismerje meg azokat a káros eseményeket, például az Azure-régióban leállás, amelyek az adatvesztést okozhatnak a beállításokat. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>A Microsoft Genomics a támogatási üzletmenet-folytonossági funkciói 
Habár ritkán fordul elő, valamely Azure adatközpontnak a kimaradás, így előfordulhat, hogy az elmúlt néhány óra alatt pár percet üzletmenet is rendelkezhet. Egy kimaradás során, az adatközpontban futó összes feladat sikertelen lesz, és a Microsoft Genomics szolgáltatás nem fogja automatikusan küldje el újra a feladatokat egy másodlagos régióba. 

* Az egyik lehetőség, várja meg, az Adatközpont ismét online állapotú lesz, amikor az Adatközpont-kimaradás felett van. Ha a szolgáltatáskimaradás elhárítása után rövid, a Microsoft Genomics szolgáltatással automatikusan észleli a sikertelen feladatokat, és a munkafolyamat automatikusan újraindul.

* Egy másik lehetőség, hogy proaktív módon elküldeni az adatokat egy másik régióban található a munkafolyamatot. A Microsoft Genomics üzembe helyezi a példányokat több [Azure-régiók](https://azure.microsoft.com/regions/services/), és minden régióspecifikus példány független. Ha a Microsoft Genomics-példányok egyik régió helyi hiba, a Microsoft Genomics példányait régiók továbbra is feladatok feldolgozásához. Az átadás egy másik régióba való bármikor a felhasználó és a rendelkezésre álló ellenőrzés alatt áll.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Manuális feladatátvétel a Microsoft Genomics munkafolyamatok egy másik régióba
Esetén a regionális adatközpontjának leállása esetén előfordulhat, hogy használatba vétele küldje el a Microsoft Genomics-munkafolyamatok egy másodlagos régióban, az egyes elkülönítése és az üzleti folytonossági követelmények alapján. Manuális feladatátvétel a Microsoft Genomics munkafolyamatokat akkor egy másik régióspecifikus érdemes használnia. Genomics-fiók, és küldje el a feladatot a megfelelő régióspecifikus Genomics és a tárfiók hitelesítő adatait.

Pontosabban a kell:
* Genomics-fiók létrehozása az Azure portal használatával, a másodlagos régióban. 
* A bemeneti adatokat át egy a másodlagos régióban lévő tárfiókba, és állítsa be a másodlagos régió kimeneti mappa.
* Küldje el a munkafolyamat a másodlagos régióban.

Helyreállítása az eredeti régió, a Microsoft Genomics szolgáltatásban nem telepíti át az adatokat a másodlagos régióból az eredeti régióba. Belátása szerint dönthet úgy, hogy helyezze át a bemeneti és kimeneti fájlok a másodlagos régióból biztonsági eredeti régióba.  Ha úgy dönt, hogy helyezze át az adatokat, ez kívül a Genomics szolgáltatás és a kapcsolódó minden költséget az adatok áthelyezése a felhasználó felelőssége lesz. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Egy lehetséges régióspecifikus szolgáltatáskimaradás előkészítése
Ha a gyorsabb helyreállítás esetén egy adatközpont-meghibásodás után néhány lépésből áll, hogy manuálisan küldje el újra a Microsoft Genomics-munkafolyamatok egy másodlagos régióba szükséges idő csökkentése érdekében:

* Egy megfelelő másodlagos régió azonosításához, és aktívan Pro-Direct az adott régióban a Genomics-fiók létrehozása
* Ismétlődő az elsődleges és másodlagos régióban, úgy, hogy az adatok azonnal elérhetővé válik a másodlagos régióban. Ez lehet manuális kész vagy a a [georedundáns tárolás](https://docs.microsoft.com/azure/storage/common/storage-redundancy) funkció elérhető az Azure storage-ban. 

## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerkedett az üzletmenet-folytonossági és vészhelyreállítási lehetőségeit a Microsoft Genomics szolgáltatás használatakor. Üzleti folytonosság biztosítása és vészhelyreállítás Azure-ban általában kapcsolatos további információkért lásd: [műszaki útmutatást az Azure rugalmassága.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region) 