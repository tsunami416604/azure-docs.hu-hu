---
title: Az üzletmenet folytonosságának áttekintése
titleSuffix: Microsoft Genomics
description: Ez az áttekintés ismerteti a Microsoft Genomics által az üzletmenet folytonosságát és a vészhelyreállítást.
keywords: üzletmenet folytonossága, katasztrófa utáni helyreállítás
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 04/06/2018
ms.openlocfilehash: 28a4a53851155c56e8d34981862bf52a3a2cf15b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72249178"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Az üzletmenet folytonosságának áttekintése a Microsoft Genomics-szal
Ez az áttekintés ismerteti a Microsoft Genomics által az üzletmenet folytonosságát és a vészhelyreállítást. Ismerje meg a zavaró események, például egy Azure-régió kimaradás, amely adatvesztést okozhat lehetőségek helyreállítása lehetőségeket. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>A Microsoft Genomics szolgáltatásai, amelyek támogatják az üzletmenet folytonosságát 
Bár ritka, egy Azure-adatközpontban előfordulhat, hogy kimaradás, ami üzleti zavarokat okozhat, amelyek néhány percig vagy néhány óráig tarthatnak. Kimaradás esetén az adatközpontban jelenleg futó összes feladat sikertelen lesz, és a Microsoft Genomics szolgáltatás nem küldi el automatikusan újra a feladatokat egy másodlagos régióba. 

* Az egyik lehetőség az, hogy megvárja, amíg az adatközpont újra online állapotba kerül, amikor az adatközpont kimaradása véget ért. Ha a szolgáltatáskimaradás rövid, a Microsoft Genomics szolgáltatás automatikusan észleli a sikertelen feladatokat, és a munkafolyamat automatikusan újraindul.

* Egy másik lehetőség a munkafolyamat proaktív elküldése egy másik adatrégióban. A Microsoft Genomics több [Azure-régióban](https://azure.microsoft.com/regions/services/)telepíti a példányokat, és minden régióspecifikus példány független. Ha a Microsoft Genomics egyik példánya helyi hibát tapasztal, a Microsoft Genomics más példányait futtató régiók továbbra is feldolgozzák a feladatokat. Ez az alternatív régióba való átvitel a felhasználó ellenőrzése alatt áll, és bármikor elérhető.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Microsoft Genomics-munkafolyamatok manuális átvétele egy másik régióba
Ha egy regionális adatközpont-kimaradás történik, dönthet úgy, hogy a Microsoft Genomics munkafolyamatokat egy másodlagos régióban küldi el, az egyéni adatok szuverenitása és az üzletmenet-folytonossági követelmények alapján. Manuálisfeladat-átvételmicrosoft genomics munkafolyamatok, egy másik régió-specifikus. Genomika fiók és küldje el a munkát a megfelelő régió-specifikus Genomics és tárfiók hitelesítő adatokat.

Pontosabban, meg kell:
* Hozzon létre egy genomikai fiókot a másodlagos régióban az Azure Portal használatával. 
* A bemeneti adatok áttelepítése egy tárfiókba a másodlagos régióban, és hozzon létre egy kimeneti mappát a másodlagos régióban.
* Küldje el a munkafolyamatot a másodlagos régióban.

Az eredeti régió visszaállításakor a Microsoft Genomics szolgáltatás nem telepíti vissza az adatokat a másodlagos régióból az eredeti régióba. Dönthet úgy, hogy a bemeneti és kimeneti fájlokat a másodlagos régióból visszaaz eredeti régióba.  Ha úgy dönt, hogy áthelyezi az adataikat, az kívül esik a Genomics szolgáltatáson, és az adatmozgással kapcsolatos összes díj az Ön felelőssége lenne. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Felkészülés egy lehetséges régióspecifikus kimaradásra
Ha az adatközpontok kimaradása esetén a gyorsabb helyreállítás miatt aggódik, néhány lépéssel csökkentheti a Microsoft Genomics-munkafolyamatok másodlagos régióba való manuális újraelküldéséhez szükséges időt:

* Azonosítsa a megfelelő másodlagos régiót, és hozzon létre pro-aktívan genomikai fiókot az adott régióban
* Az adatok duplikálása az elsődleges és másodlagos régióban, hogy az adatok azonnal elérhetők a másodlagos régióban. Ez történhet manuálisan vagy az Azure [storage-ban elérhető georedundáns tárolási](https://docs.microsoft.com/azure/storage/common/storage-redundancy) funkció használatával. 

## <a name="next-steps"></a>További lépések
Ebben a cikkben a Microsoft Genomics szolgáltatás használatakor megismerkedett az üzletmenet folytonosságának és a vészhelyreállításlehetőségeinek. Az Azure-on belüli üzletmenet-folytonosságról és a vészhelyreállításról az [Azure rugalmassági technikai útmutatójában](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region) talál további információt. 