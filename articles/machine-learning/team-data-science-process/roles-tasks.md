---
title: "Vonja össze az adatokat tudományos folyamat szerepköröket és feladatokat - Azure |} Microsoft Docs"
description: "A legfontosabb összetevők, a személyzet szerepkörök és a kapcsolódó feladatok egy adatok tudományos csapatprojekthez áttekintését."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev;
ms.openlocfilehash: b502a586cdb7351d5b22c6b0db966723b8ebb7b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="team-data-science-process-roles-and-tasks"></a>Adatok tudományos folyamat szerepköröket és feladatokat

Az Team tudományos folyamat, amely egy prediktív elemzési megoldások és intelligens alkalmazások készítéséhez hatékonyan strukturált módszert biztosít a Microsoft által kifejlesztett keretrendszer. Ez a cikk ismerteti a kulcsfontosságú személyzet szerepköröket, és a kapcsolódó feladatok egy adattudomány által kezelt csapat szabványosítása a folyamatban. 

Ez a bevezetés oktatóprogramot kínál, amelyek ad útmutatást a TDSP környezetet a teljes tudományos adatcsoport, adatok tudományos csoportok és projektek mutató hivatkozásokat tartalmaz. Segítségével a Visual Studio Team Services (VSTS) az oktatóanyagok a kódot futtató platform és gyors tervezési eszköz team feladatok, hozzáférés szabályozása, valamint kezelheti és a tárolóhelyekkel részletes útmutatást nyújtunk. 

Is kell ezt az információt használhatják a saját kód üzemeltetést és gyors tervezési eszköz TDSP valósítja meg. 

## <a name="structures-of-data-science-groups-and-teams"></a>Tudományos adatcsoport és csoportok
Előfordulhat, hogy gyakran szervezett adatok tudományos funkciók a vállalatok a következő hierarchia:

1. ***Adatok tudományos csoport/s***

2. ***Adatok tudományos team/s belül csoport/s***

Ilyen struktúrában nem lesz a csoport, és vonja össze az érdeklődőket. Általában egy adatok tudományos projektet egy tudományos projekt érdeklődők (a projekt felügyeleti és irányítási tevékenységek) és a adatszakértőkön vagy a mérnökök állhat adatok csapat végezhető el (egyes közreműködők / technikai tanácsadási csoporthoz) hajtja végre, akik a adattudomány és az adatok mérnöki részeit. Végrehajtás, előtt a telepítő és irányítási végezhető el a csoportot, csoport vagy projekt érdeklődők.

## <a name="definition-of-four-tdsp-roles"></a>Négy TDSP szerepkörök meghatározása
A fenti kezdetben meg négy különböző szerepkörök a csapat személyzet számára:

1. ***Csoport kezelőjének***. Csoport kezelőjének a teljes tudományos adategység vállalati felettesét. Egy adategység tudományos rendelkezhet több csapat dolgozik, amelyek mindegyike különböző üzleti referenciaegyenesen az adatok tudományos több projektet. A csoport kezelőjének előfordulhat, hogy delegálása a feladatokat egy helyettesítő, de ne változtassa meg a szerepkörhöz társított feladatok.

2. ***Az érdeklődési csapat***. Egy vállalati adatok tudományos egységben csapata kezel. Egy csoport több adatszakértőkön áll. Az adatok tudományos egység csak egy kis mennyiségű adatszakértőkön a csoport kezelőjének, így a csapat lehet azonos.

3. ***A projekt vezető***. A projekt vezető kezeli az egyes adatszakértőkön egy adott adatok tudományos projekt napi tevékenységeit.

4. ***Egyéni közreműködő projekt***. Adatok tudósok, üzleti elemző, adatok visszafejtés, felelős mérnök, stb. A projekt egyéni közreműködő tudományos adatok projekt hajtja végre. 


**[AZURE.NOTE]**: Attól függően a vállalat struktúra egyetlen személy játszhatnak több szerepkör, vagy előfordulhat, hogy működik-e a szerepkör több személy. Ez gyakran lehet néhány tudományos vállalatukon belüli személyzet kisvállalkozások vagy vállalatok esetében.

## <a name="tasks-to-be-completed-by-four-personnel"></a>A négy személyzet elvégzendő feladatok

Az alábbi képen ábrázol, a legfelső szintű feladatok a személyzet szerepkör bevezetése és az Team tudományos, Microsoft által conceptualized folyamat végrehajtása során. 

![Szerepkörök és feladatok – áttekintés](./media/roles-tasks/overview-tdsp-top-level.png)

A séma és a következő, részletes áttekintése, amelyek a TDSP minden egyes szerepkörhöz hozzárendelt feladatok kell segítségével válassza ki a megfelelő oktatóanyag az Ön feladatkörei a szervezet alapján.

>[AZURE.NOTE] Az alábbi utasítások azt szemlélteti a lépéseket TDSP környezet beállítása és egyéb adatok tudományos feladatokat a Visual Studio Team Services (VSTS). Azt adja meg, hogyan hajthat végre ezeket a feladatokat a VSTS, mivel az a mi használjuk a Microsoftnál TDSP végrehajtásához. VSTS együttműködés elősegíti a felügyeleti feladatok követése munkaelemek integrálásával és segédprogramok, megosztása kód üzemeltetési szolgáltatás rendszerezése verzióit, és adja meg a szerepkör alapú biztonsági. Tudunk más platformokon, válassza ki, ha szeretné, a TDSP által ismertetett feladatok végrehajtásához. De attól függően, hogy a platform azt használja a VSTS néhány funkció nem érhető el. 
>
>Is használhatja a [adatok tudományos virtuális gép (DSVM)](http://aka.ms/dsvm) az Azure-felhő mint az analytics asztal számos népszerű adatok tudományos eszköz előre konfigurálva van, és integrálva van a különböző Microsoft-szoftverek és Azure-szolgáltatásokhoz. Használhatja a DSVM vagy bármely más fejlesztőkörnyezet TDSP végrehajtásához. 


## <a name="group-manager-tasks"></a>Csoport kezelőjének feladatok

A következő műveleteket foglalja a csoport kezelőjének (vagy egy kijelölt TDSP rendszergazda) a TDSP bevezetni kívánt:

- Hozzon létre egy **csoportfiók** egy platform (például a Githubon, Git, VSTS vagy mások) futtató kódja
- Hozzon létre egy **projekt sablon tárház** a csoport fiókját, és azt a projekt sablon tárházból Microsoft TDSP csapata által fejlesztett kezdőérték. Microsoft TDSP projekt sablon tárházat biztosít a **könyvtárstruktúrát szabványosított** is az adatokat, a kód és a dokumentumokat, és számos **dokumentum sablonokszabványosított** egy hatékony adatok tudományos folyamat irányításához. 
- Hozzon létre egy **segédprogram tárház**, és feltöltheti azt a Microsoft TDSP csapata által fejlesztett segédprogram tárházból. A Microsoft segédprogram TDSP összetevőtárházat hatékonyabbá teszi a adatok tudósok munkáját, beleértve az adatok interaktív áttekintését, elemzési és jelentéskészítési, valamint a baseline modellezési és jelentéskészítési segédprogramok hasznos segédprogramokat biztosít.
- Állítsa be a **biztonsági vezérlő házirend** ezek két adattárak csoport fiókja.  

Részletes ismertetését lásd: [csoport kezelőjének feladatokat tudományos adatok csoport](group-manager-tasks.md). 


## <a name="team-lead-tasks"></a>Az érdeklődési feladatainak

A következő műveleteket foglalja a csapat vezethet (vagy egy kijelölt team projekt rendszergazda) a TDSP bevezetni kívánt:

- Ha VSTS kijelölt versioning és együttműködés a kód a gazdaplatform, hozzon létre egy **csapatprojekt** a csoport VSTS-kiszolgálón. Ellenkező esetben ez a feladat kimarad is.
- Hozzon létre a **team projekt sablon tárház** csapatprojekt, és ez a csoport projekt sablon tárházból az a csoport kezelőjének vagy a meghatalmazott, és a kezelő által létrehozott kezdőérték alatt. 
- Hozzon létre a **team segédprogram tárház**, és a csoport-specifikus segédprogramok felvétele a tárházba. 
- (Választható) Hozzon létre  **[az Azure file storage](https://azure.microsoft.com/services/storage/files/)**  adategységeiről, amelyeket a teljes csoport számára hasznos lehet tárolására használható. Más csoport tagjai a felhő megosztott fájltároló lehet csatlakoztatni az analytics Asztalukat.
- (Választható) Az az Azure file storage való csatlakoztatása a **adatok tudományos virtuális gép** (DSVM) a csapat vezethet, és adja hozzá az adategységek rajta.
- Állítsa be a **biztonsági ellenőrzést** a csoport tagjainak hozzáadása és konfigurálása a jogosultságait. 

Részletes ismertetését lásd: [tudományos adatok csoport feladatainak Team vezethet](team-lead-tasks.md).  


## <a name="project-lead-tasks"></a>Az érdeklődési tevékenységeket

A következő műveleteket foglalja által a projekt előfordulhat, hogy elfogadja a TDSP:

- Hozzon létre egy **projekt tárház** tartozó csapatprojekt és kezdőérték alatt, a csapat projekt sablon tárházba. 
- (Választható) Hozzon létre **az Azure file storage** adategységeket a projekt tárolására használható. 
- (Választható) Az az Azure file storage való csatlakoztatása a **adatok tudományos virtuális gép** (DSVM) a projekt vezethet, és adja hozzá a projekt adategységeket rajta.
- Állítsa be a **biztonsági ellenőrzést** a projekt tagok hozzáadása és konfigurálása a jogosultságait. 

Részletes ismertetését lásd: [tudományos adatok csoport feladatainak projekt vezethet](project-lead-tasks.md). 

## <a name="project-individual-contributor-tasks"></a>Egyéni közreműködő tevékenységeket

A következő műveleteket foglalja által a projekt egyéni közreműködő (általában egy adatok tudósok), az adatok tudományos projektet a TDSP elvégzésére:

- Klónozott a **projekt tárház** állítsa be a projekt vezethet. 
- (Választható) Csatlakoztassa a megosztott **az Azure file storage** a csapat és a projekt a **adatok tudományos virtuális gép** (DSVM).
- A projekt hajtható végre. 

 
Részletes lépéseit előkészítésének projekt telepítését, tekintse meg a [Project egyes közreműködők a tudományos adatok csoport](project-ic-tasks.md). 


## <a name="data-science-project-execution"></a>Adatok tudományos projekt végrehajtása
 
Megfelelő készletét utasításokat követve adatelemzők, a projekt vezető és a team érdeklődők hozhat létre a munkaelemek követéséhez összes feladat és, amelyek a projektet az elejétől végéhez. Git használatával is elősegíti a adatszakértőkön közötti együttműködés, és gondoskodik arról, hogy az összetevők projekt végrehajtása során létrehozott ellenőrzött és megosztott összes projekt tag.

A projekt végrehajtási utasításokat fejlesztettek alapján azt feltételezi, hogy mindkét munkaelemek, és a projekt git VSTS tárházak találhatók. A VSTS egyaránt lehetővé teszi, hogy a munkaelemekben a projekt adattárak a Git ággal rendelkező. Ily módon egyszerűen nyomon követheti a munkaelemhez mi lett végrehajtva. 

Az alábbi ábra bemutatja a munkafolyamat a TDSP használatával projekt végrehajtásra.

![Jellemző adatok tudományos projekt végrehajtása](./media/roles-tasks/overview-project-execute.png)

A munkafolyamat lépésekből áll, amely három tevékenységek sorolhatók:

- Súlyadatainak nyomtatása (projekt vezet) tervezése
- Az összetevők git elágazásokhoz a munkaelemek (adatok tudósok) cím fejlesztése
- A kód áttekintése és a fő ágak (projekt okozhat, vagy más csoport tagjai) ágak egyesítése

A projekt végrehajtási munkafolyamat részletes lépéseit lásd: [végrehajtási adatok tudományos projektek](project-execution.md).

## <a name="next-steps"></a>Következő lépések

Az alábbiakban a szerepkörök és az adatok tudományos eljárással meghatározott feladatok részletes leírását mutató hivatkozásokat:

- [Tudományos adatok csoport csoport Manager-feladatok](group-manager-tasks.md)
- [Az érdeklődési feladatainak egy adatok tudományos csoport](team-lead-tasks.md)
- [Az érdeklődési tevékenységeket a tudományos adatok csoport](project-lead-tasks.md)
- [Project egyes közreműködők a tudományos adatok csoport](project-ic-tasks.md)