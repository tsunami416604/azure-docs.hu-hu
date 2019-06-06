---
title: Az Azure Data Lake Analytics vészhelyreállítási útmutató
description: Ismerje meg, hogyan vész-helyreállítási terv az Azure Data Lake Analytics-fiókok esetében.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ea1d4020aa9be23b4839690ae0b386d35bce8a23
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66498889"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Az Azure Data Lake Analytics vészhelyreállítási útmutató

Az Azure Data Lake Analytics egy igény szerinti elemzési feladatokat végző szolgáltatás, amely leegyszerűsíti a big data-elemzéseket. Az üzembe helyezés, a konfigurálás és a hardver beállítása helyett lekérdezéseket írhat az adatátalakítás és az értékes információk kinyerése érdekében. Az elemző szolgáltatás bármekkora munkát képes elvégezni, csak be kell állítania, hogy mekkora teljesítményre van szüksége. Költséghatékony megoldás, hiszen csak az elvégzett munkáért kell fizetni. Ez a cikk nyújt útmutatást a feladatok védelme a ritka régió kiterjedő ritka kimaradásokkal és véletlen törlésekkel szemben.

## <a name="disaster-recovery-guidance"></a>Vészhelyreállítási útmutató

Az Azure Data Lake Analytics használata esetén fontos, hogy a saját vész-helyreállítási terv előkészítése. Ez a cikk segít végigvezetik egy vész-helyreállítási terv létrehozásához. Nincsenek további erőforrások, amelyek segítségével a saját terve létrehozásában:
- [Azure-alkalmazások hibaelhárítása és vészhelyreállítása](/azure/architecture/reliability/disaster-recovery)
- [Műszaki útmutató az Azure rugalmasságáról](/azure/architecture/reliability)

## <a name="best-practices-and-scenario-guidance"></a>Ajánlott eljárások és útmutató forgatókönyv

Ismétlődő U-SQL-feladat egy ADLA-fiók, amely olvasási és írási U-SQL-táblák, valamint a teljes strukturálatlan adatmennyiséget tárolni egy régióban is futtathatja.  Ezek a lépések végrehajtásával katasztrófa előkészítése:

1. ADLA és ADLS-fiókok létrehozása során használt a másodlagos régióban.

   > [!NOTE]
   > Mivel a fiók nevének globálisan egyedi, használjon egy egységes elnevezési sémát, amely azt jelzi, hogy melyik fiók másodlagos.

2. Strukturálatlan adatok hivatkozhat [adatokat az Azure Data Lake Storage Gen1 vészhelyreállítási útmutató](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. Az ADLA táblákat és adatbázisok tárolt strukturált adatok például adatbázisok, a táblákat, a táblázat értékű függvények és a szerelvények a metaadatok összetevők másolatokat készíteni. Rendszeres időközönként szinkronizálja újra ezeket az összetevőket, amikor változások történhetnek éles környezetben kell. Például a újonnan behelyezett adatokat, a másodlagos régióba replikálja az adatok másolásának és a másodlagos táblába történő beszúrásához.

   > [!NOTE]
   > Ezen objektumok nevét a másodlagos fiók hatóköre és nem globálisan egyedi, így ugyanazokat a neveket, mint az elsődleges éles fiókja is rendelkeznek.

Egy kimaradás során frissítse a parancsfájlokat, hogy a bemeneti elérési utak pontot a másodlagos végpontra kell. A felhasználók ezután a feladatokat a másodlagos régióban ADLA-fiókjába küldje el. A feladat kimenetei a ADLA ADLS fiókot és a másodlagos régióba, majd lesz írva.

## <a name="next-steps"></a>További lépések

[Az Azure Data Lake Storage Gen1 adatok vészhelyreállítási útmutató](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
