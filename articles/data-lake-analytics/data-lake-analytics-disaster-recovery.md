---
title: Vész-helyreállítási útmutató az Azure Data Lake Analytics-hez
description: Ismerje meg, hogyan tervezheti meg a vészhelyreállítást az Azure Data Lake Analytics-fiókokhoz.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: f9b22e6b806f76189134ec63c83d48f48bf95587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889768"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Vész-helyreállítási útmutató az Azure Data Lake Analytics-hez

Az Azure Data Lake Analytics egy igény szerinti elemzési feladatokat végző szolgáltatás, amely leegyszerűsíti a big data-elemzéseket. Az üzembe helyezés, a konfigurálás és a hardver beállítása helyett lekérdezéseket írhat az adatátalakítás és az értékes információk kinyerése érdekében. Az elemző szolgáltatás bármekkora munkát képes elvégezni, csak be kell állítania, hogy mekkora teljesítményre van szüksége. Költséghatékony megoldás, hiszen csak az elvégzett munkáért kell fizetni. Ez a cikk útmutatást nyújt a feladatok ritka régiószintű kimaradások vagy véletlen törlések elleni védelméhez.

## <a name="disaster-recovery-guidance"></a>Vészhelyreállítási útmutató

Az Azure Data Lake Analytics használatakor fontos, hogy saját vész-helyreállítási tervet készítsen. Ez a cikk segítséget nyújt a vész-helyreállítási terv létrehozásához. További források segíthetnek saját terv létrehozásában:
- [Azure-alkalmazások hibaelhárítása és vészhelyreállítása](/azure/architecture/reliability/disaster-recovery)
- [Műszaki útmutató az Azure rugalmasságáról](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>Bevált gyakorlatok és forgatókönyv-útmutató

Futtathat egy ismétlődő U-SQL-feladatot egy ADLA-fiókban egy olyan régióban, amely u-SQL táblákat, valamint strukturálatlan adatokat olvas és ír.  Készüljön fel a katasztrófa az alábbi lépéseket:

1. Hozzon létre ADLA- és ADLS-fiókokat a másodlagos régióban, amelyeket egy kimaradás során fog használni.

   > [!NOTE]
   > Mivel a fióknevek globálisan egyediek, használjon konzisztens elnevezési sémát, amely jelzi, hogy melyik fiók másodlagos.

2. Strukturálatlan adatok esetén az [Azure Data Lake Storage Gen1-ben található adatokhoz hivatkozzon a vész-helyreállítási útmutatóra1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. Az ADLA-táblákban és adatbázisokban tárolt strukturált adatokhoz hozzon létre másolatot a metaadat-összetevőkről, például adatbázisokról, táblákról, táblaértékű függvényekről és szerelvényekről. Ezeket az összetevőket rendszeresen újra kell szinkronizálnia, amikor a változások éles környezetben történnek. Az újonnan beszúrt adatokat például replikálni kell a másodlagos régióba az adatok másolásával és a másodlagos táblába való beszúrással.

   > [!NOTE]
   > Ezek az objektumnevek hatóköre a másodlagos fiókba terjed ki, és globálisan nem egyediek, így ugyanazokkal a nevekkel rendelkezhetnek, mint az elsődleges termelési fiókban.

Egy kimaradás során frissítenie kell a parancsfájlokat, hogy a bemeneti útvonalak a másodlagos végpontra mutassanak. Ezután a felhasználók elküldik a feladataikat az ADLA-fióknak a másodlagos régióban. A feladat kimenete ezután a másodlagos régió ADLA- és ADLS-fiókjába lesz írva.

## <a name="next-steps"></a>További lépések

[Vész-helyreállítási útmutató az azure Data Lake Storage Gen1-ben tárolt adatokhoz](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
