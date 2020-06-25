---
title: Mi a csoportos adatelemzési folyamat?
description: Adatelemzési módszert biztosít a prediktív elemzési megoldások és intelligens alkalmazások továbbításához.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: overview
ms.date: 1/10/2020
ms.author: tdsp
ms.custom: previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ffe00c3df4abd924fca202d2bcdd0ec458dd3eb
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85355335"
---
# <a name="what-is-the-team-data-science-process"></a>Mi a csoportos adatelemzési folyamat?

A csoportos adatelemzési folyamat (TDSP) egy agilis, ismétlődő adatelemzési módszer, amely hatékony prediktív elemzési megoldások és intelligens alkalmazások gyors megvalósítása érdekében. A TDSP segít a csapatmunka és a tanulás fejlesztésében azzal, hogy a csapat szerepkörei hogyan működnek együtt a legjobban. A TDSP a Microsoft és más iparági vezetők által ajánlott eljárásokat és struktúrákat tartalmaz, amelyek segítenek az adatelemzési kezdeményezések sikeres megvalósításában. A cél a vállalatok segítése az elemzési programjuk előnyeinek teljes körű megvalósításában.

Ez a cikk áttekintést nyújt a TDSP és annak fő összetevőiről. Az itt található folyamat általános leírását biztosítjuk, amely különböző típusú eszközökkel valósítható meg. A folyamat életciklusában részt vevő projekttevékenységek és szerepkörök részletesebb leírását további csatolt témakörök tartalmazzák. A TDSP megvalósításával kapcsolatos útmutatást a Microsoft eszközeinek és infrastruktúrájának a TDSP megvalósításához használt meghatározott készletével is meg kell adni.

## <a name="key-components-of-the-tdsp"></a>A TDSP legfontosabb összetevői

A TDSP a következő hads:

- **Adatelemzési életciklus** definíciója
- **Szabványosított projekt szerkezete**
- Adatelemzési projektekhez ajánlott **infrastruktúra és erőforrások**
- A projekt végrehajtásához javasolt **eszközök és segédprogramok**


## <a name="data-science-lifecycle"></a>Adatelemzési életciklus

A csoportos adatelemzési folyamat (TDSP) életciklust biztosít az adatelemzési projektek fejlesztésének struktúrájához. Az életciklus a sikeres projektek követésének teljes lépéseit ismerteti.

Ha más adatelemzési életciklust használ, például a [ropogós DM-](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining)t, a [KDD](https://wikipedia.org/wiki/Data_mining#Process) -t vagy a szervezet saját egyéni folyamatát, akkor továbbra is használhatja a feladat-alapú TDSP a fejlesztési életciklusok kontextusában. Ezek a különböző módszertanok nagy mértékben közösek. 

Ez az életciklus olyan adatelemzési projektekhez lett tervezve, amelyek intelligens alkalmazások részeként szállítanak. Ezek az alkalmazások gépi tanulási vagy mesterséges intelligencia-modelleket helyeznek üzembe a prediktív elemzésekhez. A feltáró adatelemzési projektek vagy az improvizált elemzési projektek is hasznosak lehetnek ennek a folyamatnak a használatával. Ilyen esetekben azonban előfordulhat, hogy az ismertetett lépések némelyike nem szükséges.    

Az életciklus a projektek jellemzően végrehajtandó főbb szakaszait vázolja fel, gyakran iteratív:

* **Üzleti ismeretek**
* **Adatgyűjtés és-megértés**
* **Modellezés**
* **Üzembe helyezés**

Itt látható a **csoportos adatelemzési folyamat életciklusának**vizuális ábrázolása. 

![TDSP – Lifecycle2](./media/overview/tdsp-lifecycle2.png) 

A TDSP-életciklus egyes szakaszaihoz tartozó célok, feladatok és dokumentációs összetevők leírása a [csoportos adatelemzési folyamat életciklusa](lifecycle.md) című témakörben található. Ezek a feladatok és összetevők a projekt szerepköreihez vannak társítva:

- Megoldás-építész
- Projektvezető
- Adatszakértő
- Adatelemző
- Alkalmazás-fejlesztő
- Projektvezető 

Az alábbi ábrán láthatók a (kék) és az összetevők (a vízszintes tengelyen) (a függőleges tengelyen) az életciklus egyes szakaszaihoz tartozó tevékenységek (kék színnel). 

[![TDSP – szerepkörök és feladatok](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Szabványosított projekt szerkezete

Ha minden projekt megoszt egy címtár-struktúrát, és a Project-dokumentumokhoz sablonokat használ, a csapattagok számára megkönnyíti a projektekkel kapcsolatos információk megtalálását. Minden kód és dokumentum egy verziókövető rendszer (VCS), például a git, a TFS vagy a Subversion használatával van tárolva a csapat együttműködésének engedélyezéséhez. A feladatok és funkciók nyomon követése egy agilis projekt-követési rendszeren, például a JIRA, a rally és az Azure DevOps segítségével az egyes funkciókhoz tartozó kódok szorosabb nyomon követését teszik lehetővé. Az ilyen nyomon követés lehetővé teszi a csapatok számára a jobb költségbecslés megszerzését is. A TDSP javasolja, hogy külön tárházat hozzon létre a VCS minden projekthez a verziószámozás, az információbiztonság és az együttműködés érdekében. Az összes projekt szabványosított szerkezete segíti a szervezeten belüli intézményi ismeretek kiépítését.

A mappák felépítéséhez és a szükséges dokumentumokhoz a standard szintű helyen biztosítunk sablonokat. Ez a mappastruktúrát rendszerezi az adatelemzési és-szolgáltatás-kinyerési kódot tartalmazó fájlokat, és rögzíti a modellre vonatkozó iterációkat. Ezek a sablonok megkönnyítik a csapattagok számára a mások által végzett munka megértését, és új tagok hozzáadását a csapatokhoz. A dokumentumok sablonját egyszerűen megtekintheti és frissítheti Markdown formátumban. A sablonok segítségével az egyes projektek legfontosabb kérdéseivel biztosíthatók a feladatlisták, így biztosítható, hogy a probléma megfelelően legyen meghatározva, és hogy a termékek megfeleljenek a várt minőségnek. Példák:

- az üzleti problémát és a projekt hatókörét dokumentáló projekt Charta
- adatjelentések a nyers adatok szerkezetének és statisztikai adatainak dokumentálására
- a származtatott funkciók dokumentálására szolgáló jelentések modellezése
- modell teljesítmény-mérőszámai, például a ROC-görbék vagy az MSE


[![TDSP – címtárak](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

A könyvtár szerkezete a [githubról](https://github.com/Azure/Azure-TDSP-ProjectTemplate)klónozott lehet.

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Az adatelemzési projektek infrastruktúrája és erőforrásai  

A TDSP ajánlásokat nyújt a megosztott elemzési és tárolási infrastruktúra kezeléséhez, például:

- adatkészletek tárolására szolgáló felhőalapú fájlrendszerek 
- adatbázisok
- big data (SQL-vagy Spark-) fürtök 
- Machine learning-szolgáltatás 

Az elemzési és tárolási infrastruktúrát, ahol a nyers és a feldolgozott adatkészletek tárolva vannak, a felhőben vagy a helyszínen lehet. Ez az infrastruktúra lehetővé teszi a reprodukálható elemzést. Emellett elkerüli az ismétlődést, ami inkonzisztenciát és szükségtelen infrastrukturális költségeket eredményezhet. Eszközöket biztosítanak a megosztott erőforrások kiépítéséhez, nyomon követéséhez, valamint az egyes csapattagok biztonságos elérésének engedélyezéséhez. Azt is érdemes elkészíteni, hogy a projekt tagjai konzisztens számítási környezetet hozzanak létre. A csapattagok ezután képesek replikálni és érvényesíteni a kísérleteket.

Íme egy példa arra, hogy egy csapat több projekten dolgozik, és különböző felhőalapú elemzési infrastruktúra-összetevőket oszt meg.

[![TDSP – infrastruktúra](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>A projekt végrehajtásához szükséges eszközök és segédprogramok

A legtöbb szervezet folyamatainak bevezetése kihívást jelent. Az adatelemzési folyamat és az életciklus megvalósításához biztosított eszközök csökkentik a korlátokat, és növelhetik az elfogadásuk következetességét. A TDSP az eszközök és parancsfájlok kezdeti készletét kínálja a TDSP a csapaton belüli bevezetésének megkezdéséhez. Emellett segít automatizálni az adatelemzési életciklus általános feladatait, például az adatfeltárást és a kiindulási modellezést. Egy jól definiált struktúra van megadva, hogy a felhasználók közösen használják a megosztott eszközöket és segédprogramokat a csapatuk megosztott kódjának tárházában. Ezeket az erőforrásokat ezután a csapaton vagy a szervezeten belül más projektek is kihasználhatják. A TDSP azt is tervezi, hogy lehetővé tegye az eszközök és segédprogramok teljes Közösséghez való hozzájárulását. Előfordulhat, hogy a TDSP segédprogramok klónozása a [githubról](https://github.com/Azure/Azure-TDSP-Utilities)lehetséges.


## <a name="next-steps"></a>További lépések

[Csoportos adatelemzési folyamat: szerepkörök és feladatok](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) Felvázolja a kulcsfontosságú személyzeti szerepköröket és azokhoz kapcsolódó feladatait egy adatelemzési csapat számára, amely egységesíti a folyamatot. 
