---
title: Csoportos adatelemzési folyamat szerepkörök és feladatok – Azure |} A Microsoft Docs
description: A legfontosabb összetevők, a csoporthoz szerepkörök és a egy data science csoportprojektet a hozzá tartozó tevékenység áttekintését.
author: marktab
manager: cgronlun
editor: cgronlun
services: machine-learning
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 3529160ccfd0a11d3d2077245c1160941027ff88
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442098"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Csapat adatelemzési folyamat szerepkörök és feladatok

A csoportos adatelemzési folyamat egy keretrendszer, amely egy strukturált hozhat létre prediktív elemzési megoldások és intelligens alkalmazások hatékony módszert biztosít a Microsoft által kifejlesztett. Ez a cikk ismerteti a kulcsfontosságú személyzet szerepköröket, és a hozzájuk kapcsolódó részfeladatokat rendszerű adatelemzési által kezelt csapat szabványosításával ezen folyamatról. 

Ez a bevezető oktatóanyagok, amely a TDSP-környezetet, a teljes adatelemzési adatcsoport, azoknak és projektek vonatkozó utasítások mutató hivatkozásokat tartalmaz. Az oktatóanyagok az Azure DevOps használatával részletes útmutatást biztosít. Az Azure DevOps-kódszolgáltatás platform és Agilis tervezési eszköz a csapat feladatait, hozzáférés és az adattárak kezelése biztosít. 

Ezen információk használatával TDSP megvalósítani a saját kód üzemeltetési és Agilis tervezési eszköz. 

## <a name="structures-of-data-science-groups-and-teams"></a>Adatcsoportok adatelemzési és a csapatok struktúrája

Előfordulhat, hogy gyakran szervezett Data science függvények a vállalatok a következő hierarchia:

1. ***Data science csoport/s***

2. ***Adatok adatelemzési csapatával/s belül csoport/s***

Ilyen struktúra nincsenek csoport és a csapat érdeklődők. Általában egy adatelemzési csapatával, amely projekt érdeklődők (a projekt felügyeleti és cégirányítási feladatok) és az adatelemzők vagy mérnökök is állhat egy adatelemzési projektjéhez végezhető el (az egyes közreműködők / technikai csoporthoz) aki végrehajtja a data science és adatok mérnöki részeire a projekthez. A Futtatás előtt a telepítő és a cégirányítási végzi el a csoportot, csoport vagy projekt érdeklődők.

## <a name="definition-of-four-tdsp-roles"></a>Négy TDSP szerepkörök meghatározása
A fenti cégprofil a csapat személyzet négy különböző szerepkörök tartoznak:

1. ***Csoportkezelő***. Csoportkezelő a kezelő a teljes adatelemzési egység a vállalaton belül. Előfordulhat, hogy a data science egység több csapat, amelyek mindegyike több különböző üzleti referenciaegyenesen az adatelemzési projektek dolgozik. A csoport kezelőjének előfordulhat, hogy delegálni a feladatokat a helyettes, de a szerepkörhöz tartozó feladatok ne módosítsa.

2. ***Csapat vezető***. Egy csapat vezető vállalati a data science egységben csapata kezel. Egy csoport több adatszakértők áll. Data science egység csak kis számú adatszakértők a csoport kezelőjének és a Csapatvezető lehet ugyanaz a személy.

3. ***A projekt vezető***. A projektvezető kezeli a napi tevékenységek egyes adatszakértők, az egy adott adatelemzési projektjéhez.

4. ***Egyéni közreműködő projekt***. Adatelemzési szakértő, üzleti elemző, adatok mérnök, mérnök, stb. A projektben egyes közreműködője adatelemzési projektjéhez hajtja végre. 


> [!NOTE]
> Attól függően, a struktúra a vállalaton belül egyetlen személy játszhatnak egynél több szerepkört, vagy előfordulhat, hogy több személy dolgozik egy szerepkört. Ez az eset a kisvállalkozások vagy vállalatok számára a data science cég munkatársai kis számú gyakran lehet.

## <a name="tasks-to-be-completed-by-four-personnel"></a>Négy munkatársak által elvégzendő feladatok

A következő képen látható a legfelső szintű feladatok munkatársak bevezetése, és a csoportos adatelemzési folyamat, a Microsoft conceptualized megvalósítása szerepkör szerint ábrázolja. 

![Szerepkörök és feladatok áttekintése](./media/roles-tasks/overview-tdsp-top-level.png)

Ez a séma- és az alábbi, részletes áttekintése, amelyek a TDSP az egyes szerepkörökhöz rendelt feladatok kell segítségével eldöntheti, az oktatóanyagokat, az Ön feladatkörei, a szervezet alapján.

> [!NOTE]
> A következőkben megtudhatja lépéseket TDSP-környezet kialakítása, és végezze el az Azure DevOps más adatelemzési feladatokat. Azt adja meg, hogyan végezheti el ezeket a feladatokat az Azure DevOps, mivel az a mi használjuk a Microsoft TDSP megvalósításához. Az Azure DevOps integrálásával a felügyeleti feladatok nyomon követése, munkaelemekből álló csapatmunkát és a egy üzemeltetési szolgáltatás kódot, segédprogramok, megosztására használt verziók rendszerezheti, és adja meg a szerepkör alapú biztonsági. Ön választhat más platformokon, ha szeretné, a TDSP által ismertetett feladatok végrehajtásához. De a platformtól függően néhány szolgáltatás az Azure DevOps ki lehet, hogy nem érhető el. 
>
>Itt leírt utasításokat is használhatja a [adatelemzési virtuális gépet (DSVM)](https://aka.ms/dsvm) az Azure-ban a felhő mint az elemzési asztal számos népszerű beépített adatelemzési eszközzel, előre konfigurált, és a különböző Microsoft-szoftverek és az Azure integrált szolgáltatások. A dsvm-hez vagy bármilyen más fejlesztési környezet használatával TDSP megvalósításához. 


## <a name="group-manager-tasks"></a>Csoportkezelő feladatok

A következő feladatokat végzi a csoport kezelőjének (vagy a kijelölt TDSP-rendszergazda) a TDSP fogad el:

- Hozzon létre egy **csoportfióknak** az üzemeltetési platform (például a Github, a Git, Azure DevOps, és mások) kódot
- Hozzon létre egy **projekt sablontár** a fiók, és a projekt sablontár Microsoft TDSP csapata által fejlesztett kezdőérték. A Microsoft TDSP projekt sablontár 
    - biztosít egy **könyvtárstruktúrát szabványosított** többek között az adatokat, a kód és a dokumentumok, könyvtárak 
    - számos új **dokumentum sablonok szabványosított** egy hatékony adatelemzési folyamat irányításához. 
- Hozzon létre egy **segédprogram tárház**, és ültet be azt a Microsoft TDSP csapata által fejlesztett segédprogram-adattárból. A TDSP segédprogram tárházban, a Microsoft biztosít 
    - hasznos segédprogramok, hogy a munkát meg kell értenie az adatokhoz, hatékonyabb, többek között segédprogramok interaktív adatkutatási, elemzési és jelentéskészítési, valamint a referenciakonfiguráció adatmodellező és -jelentéskészítő készlete.
- Állítsa be a **biztonsági szabályok** ezen két tárházak a csoport-fiókjában.  

A részletes részletes tudnivalókért lásd: [Csoportkezelő feladatokat egy adatelemzési csapatával](group-manager-tasks.md). 


## <a name="team-lead-tasks"></a>Csapat vezető feladatok

A következő feladatokat végzi a Csapatvezető (vagy egy kijelölt projekt adminisztrátorához) a TDSP el:

- Ha a kód üzemeltetési platform verziókezelés és együttműködés az Azure DevOps kijelölt, hozzon létre egy **projekt** meg a csoport az Azure DevOps-szolgáltatásokkal. Ellenkező esetben ez a feladat lehet hagyni.
- Hozzon létre a **projekt sablontár** a projektet, és a csoport projekt sablontár állítsa be a csoport a műveletek irányítója vagy a vezető által kezdőérték alatt. 
- Hozzon létre a **csapat segédprogram tárház**, és adja hozzá a csapat-specifikus parancssori segédeszközöket a tárházba. 
- (Nem kötelező) Hozzon létre **[az Azure file storage](https://azure.microsoft.com/services/storage/files/)** azon adategységeiről, amelyeket a teljes csoport számára hasznos lehet tárolására használható. Más csapattagokat is csatlakoztatása a megosztott felhőalapú fájltároló analytics gépeiken.
- (Nem kötelező) Csatlakoztassa az Azure file storage-a **adatelemző virtuális gép** (DSVM) a csapat vezethet, és adja hozzá az adategységek rajta.
- Állítsa be a **biztonsági ellenőrzést** a csoporttagok felvétele, és konfigurálja a jogosultságait. 

A részletes részletes tudnivalókért lásd: [feladatok csapata egy adatelemzési csapatával](team-lead-tasks.md).  


## <a name="project-lead-tasks"></a>Érdeklődő tevékenységeket

A következő feladatokat végzi el a TDSP projekt vezet:

- Hozzon létre egy **projektadattárat** a projekt keretében és a projekt sablontár ültet be azt. 
- (Nem kötelező) Hozzon létre **az Azure file storage** a projekthez, adategységeket tárolására használható. 
- (Nem kötelező) Csatlakoztassa az Azure file storage-a **adatelemző virtuális gép** (DSVM) a projekt vezethet, és adja hozzá a projekthez, adategységeket rajta.
- Állítsa be a **biztonsági ellenőrzést** a projekt tagok hozzáadása és konfigurálása a jogosultságait. 

A részletes részletes tudnivalókért lásd: [feladatok projekt vezethet az adatelemzési csapatával](project-lead-tasks.md). 

## <a name="project-individual-contributor-tasks"></a>Egyéni közreműködő tevékenységeket

A következő feladatokat végzi egy projekt egyéni közreműködő (általában Adatszakértő), a használatával a TDSP adatelemzési projektjéhez elvégzésére:

- Klónozás a **projektadattárat** a projektvezető által létrehozott. 
- (Nem kötelező) Csatlakoztassa a megosztott **az Azure file storage** a csapat és a projekt azok **adatelemző virtuális gép** (DSVM).
- Hajtsa végre a projektet. 

 
Részletes útmutatásért az előkészítési alakzatot a projekt számára, lásd: [projektben egyes közreműködők az adatelemzési csapatával](project-ic-tasks.md). 


## <a name="data-science-project-execution"></a>Data science projekt végrehajtása
 
Az alábbi utasításokat a megfelelő készletét, az adatszakértők, projektvezető és csapatvezetők hozhat létre munkaelemeket összes feladat és egy projektet szakaszok követése az elejétől vége. A git használatával is elősegíti az adatelemzők közötti együttműködés, és biztosítja, hogy a projekt végrehajtása során létrehozott összetevők verziója, ellenőrzött és az összes projekt tagjai által megosztott.

A megjelenő utasításokat a projekt végrehajtása feltételezzük, hogy mindkét munkaelemeit kapcsolja össze, és a git-tárházak vannak az Azure DevOps project alapján fejlesztettek. Azure DevOps használatával is lehetővé teszi, hogy a Git-ágak az projekt tárházak a munkaelemeket. Ezzel a módszerrel könnyen követheti a munkaelemhez Mi történt meg. 

Az alábbi ábra ezt a munkafolyamatot a projekt végrehajtása TDSP használata vázolja fel.

![Tipikus Data Science projekt végrehajtása](./media/roles-tasks/overview-project-execute.png)

A munkafolyamat lépésekből áll, amely három tevékenységek sorolhatók:

- Sprint (projekt vezethet) tervezése
- Fejlesztés a git-ágak (Adattudós) munkaelemek megoldása összetevők
- A kód áttekintése és ágakat (projekt vezető vagy más tagjai) fő ág egyesítése

Részletes, lépésenkénti útmutatót a projekt végrehajtási munkafolyamat, lásd: [adatelemzési projektek végrehajtásának](project-execution.md).

## <a name="project-structure"></a>Projektstruktúra

Ezzel [projekt sablontár](https://github.com/Azure/Azure-TDSP-ProjectTemplate) hatékony projekt végrehajtása és együttműködés támogatása. Ez a tárház egy szabványosított directory struktúra és a dokumentum sablonok is használhatja a saját TDSP projekt biztosítja.

## <a name="next-steps"></a>További lépések

Ismerje meg a szerepkörök és feladatok határozzák meg a csoportos adatelemzési folyamat részletes leírása:

- [Adatelemzési csapatával csoport Manager-feladatok](group-manager-tasks.md)
- [Csoportos adatelemzési csapatával az érdeklődő feladatok](team-lead-tasks.md)
- [Érdeklődő tevékenységeket az adatelemzési csapatával](project-lead-tasks.md)
- [Projektben egyes közreműködők az adatelemzési csapatával](project-ic-tasks.md)