---
title: Üzleti folytonosság – Microsoft Genomics |} Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan támogatja a Microsoft Genomics az üzletmenet folytonossága
keywords: az üzletmenet folytonossága, katasztrófa utáni helyreállítás
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 04/06/2018
ms.openlocfilehash: cb3825cb89aff386c4f7c3f3b0d771d73fe637b1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>A Microsoft Genomics üzleti folytonosság – áttekintés
Ez az Áttekintés a Microsoft Genomics biztosít az üzletmenet folytonossága és vészhelyreállítás képességeit ismerteti. Ismerje meg a helyreállítás zavaró események, például az adatok elvesztését okozhatja az Azure-régió kimaradás beállítások. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Microsoft Genomics szolgáltatásokat, hogy támogatási üzleti folytonosság 
Habár ritkán fordul elő, az Azure-adatközpont rendelkezhet kimaradás, így a üzleti szüneteltetése, előfordulhat, hogy utoljára néhány órára néhány percig. Nem tervezett kimaradás esetén az Adatközpont aktuálisan futó összes feladat sikertelen lesz, és a Microsoft Genomics szolgáltatás nem lesz automatikusan újraküldése feladatok távoli másodlagos régióba. 

* Egy elem felett van az Adatközpont ismét online állapotú lesz, amikor az Adatközpont-szolgáltatáskimaradás vár. Ha a szolgáltatáskimaradás rövid, a Microsoft Genomics szolgáltatás automatikusan észleli a sikertelen feladatok, és a munkafolyamat automatikusan újraindul.

* Egy másik lehetőség egy proaktív módon küldje el a munkafolyamat egy másik adatterületen. Microsoft Genomics telepít több példánya [Azure-régiók](https://azure.microsoft.com/regions/services/), és feltünteti régióspecifikus független. Ha egy Microsoft Genomics példányt régió helyi hiba, más régiókban futó Microsoft Genomics példányát folyamatosan feldolgozza a feladatokat. Egy másik régióban az átvitel bármikor felhasználó és a rendelkezésre álló ellenőrzés alatt áll.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Manuális feladatátvétel Microsoft Genomics munkafolyamatok egy másik régióban
Esetén a regionális adatközpont-meghibásodás után akkor választhatja, hogy küldje el a Microsoft Genomics munkafolyamatok egy másodlagos régióban, az egyes közös joghatóság alá és üzleti folytonossági követelmények alapján. Manuális feladatátvétel Microsoft Genomics munkafolyamatok használja egy másik régióspecifikus. Genomika fiókot, és a megfelelő régióspecifikus genomika és a tárfiók hitelesítő adatainak a feladat elküldéséhez.

Pontosabban szüksége lesz:
* Genomika-fiók létrehozása az Azure portál használatával, a másodlagos régióban. 
* A bemeneti adatok áttelepítése a tárfiók másodlagos régióban, és állítsa be a másodlagos régióban kimeneti mappa.
* Küldje el a munkafolyamat a másodlagos régióban.

Ha helyreállítása az eredeti régió, a Microsoft Genomics szolgáltatás nem az adatok áttelepítését másodlagos régióban vissza az eredeti régió. Akkor választhatja, hogy helyezze át a bemeneti és kimeneti fájlokat a másodlagos régióban biztonsági eredeti régió.  Ha Ön úgy dönt, az adatokat, a kívül a genomika szolgáltatás díjak megfizetése kapcsolódik az adatmozgás lenne a felelős. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Egy lehetséges régióspecifikus leállás előkészítése
Ha a gyorsabb helyreállítás esetén egy adatközpont-meghibásodás után van ahhoz, hogy manuálisan küldje el újra a Microsoft Genomics munkafolyamatok másodlagos régióba szükséges idő csökkentése érdekében is igénybe vehet néhány lépést:

* Egy megfelelő másodlagos régióba azonosítása és aktívan mellett az adott régióban a genomika-fiók létrehozása
* Ismétlődő az adatokat az elsődleges és másodlagos régióban, hogy az adatok azonnal elérhető másodlagos régióban. Ez lehet manuális kész vagy használatával a [georedundáns tárolás](https://docs.microsoft.com/azure/storage/common/storage-redundancy) érhető el az Azure storage szolgáltatás. 

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, az üzletmenet folytonossága és vészhelyreállítás beállításai a Microsoft Genomics szolgáltatás használatakor. Az üzletmenet folytonossága és vészhelyreállítás Azure-ban az általános kapcsolatos további információkért lásd: [Azure rugalmassági műszaki útmutatót.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region) 