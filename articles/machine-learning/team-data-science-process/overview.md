---
title: Mi a csoportos adatelemzési folyamat?
description: Itt olyan adatelemzési módszer, prediktív elemzési megoldások és intelligens alkalmazásokat.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 10/20/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 94728bbc74d1864c76aa801628fcbfb133b97abd
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53435990"
---
# <a name="what-is-the-team-data-science-process"></a>Mi a csoportos adatelemzési folyamat?

A csoportos adatelemzési folyamat (TDSP) egy rugalmas és iteratív adatelemzési módszer, amelynek célja prediktív elemzési megoldások és intelligens alkalmazások hatékony. A csoportos adatelemzési folyamat elősegíti a csoportok hatékonyabb együttműködését és tanulását. A Microsofttól és az iparág más vállalataitól származó, olyan ajánlott eljárásokból és szerkezetekből álló válogatást tartalmaz, amelyek az adatelemzési kezdeményezések sikeres megvalósítását segítik elő. A cél a vállalatok segítése az elemzési programjuk előnyeinek teljes körű megvalósításában.

Ez a cikk áttekintést TDSP és fő összetevőit. Microsoft-számos eszközt biztosít egy általános leírása itt a folyamat, amely az alkalmazásokon és szolgáltatásokon futtatható. További csatolt témakörökben található egy részletes leírását a tevékenységeket és a szerepkörök részt vesz a folyamat élettartama biztosítunk. -A használatával egy adott Microsoft-eszközök és infrastruktúra, a teams szolgáltatásban a TDSP megvalósításához használt TDSP megvalósításához is útmutatást.

## <a name="key-components-of-the-tdsp"></a>A TDSP fő összetevői

TDSP a következő fő összetevőből áll:

- A **adattudományi életciklus** definíciója
- A **szabványosított projektstruktúra**
- **Infrastruktúra és az erőforrások** az adatelemzési projektek
- **Eszközök és segédprogramok** a projekt végrehajtása


## <a name="data-science-lifecycle"></a>Adatelemzési életciklus

A csoportos adatelemzési folyamat (TDSP) biztosít egy életciklussal, építse fel az adatelemzési projektek fejlesztését. Az életciklus elejétől a végéig, projektek általában követve azok végrehajtásakor, lépéseit ismerteti.

Ha az egy másik adattudományi életciklus, mint például [SZÍNELOSZLÁS-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [évi Versenysorozatából](https://wikipedia.org/wiki/Data_mining#Process) vagy a szervezet saját egyéni folyamatát a feladatalapú TDSP továbbra is használhatja ezeket a fejlesztési életciklus kontextusában. Magas szinten ezek különböző módszereket rendelkezik nagy részét. 

Ennek az életciklusnak úgy lett kialakítva, amelyek az intelligens alkalmazások része adatelemzési projektek. Ezek az alkalmazások üzembe helyezése a machine learning és a mesterséges intelligenciát használó modellek prediktív elemzőeszközöket. Feltáró adatelemzési projektek vagy alkalmi elemzési projektek előnyeit is kihasználhatják a folyamat használatával. Azonban ezekben az esetekben a leírt lépések nincs szükség.    

Az életciklus a fő szakaszai, projektek általában végrehajtható, iteratív gyakran ismerteti:

* **Az üzleti igények felmérése**
* **Adatgyűjtés és adatértelmezés**
* **Modellezés**
* **Üzembe helyezés**
* **Felhasználói elfogadás**

Íme a vizuális megjelenítését, a **csoportos adatelemzési folyamat életciklusa**. 

![TDSP-Lifecycle2](./media/overview/tdsp-lifecycle2.png) 

A célok, feladatok és az egyes fázisokat a TDSP-életciklus dokumentációs összetevők ismertetett a [csoportos adatelemzési folyamat életciklusa](lifecycle.md) témakör. Ezekről a feladatokról és összetevők-projekt szerepkörök tartoznak:

- Felhőmegoldás-fejlesztő mérnök
- Projektvezető
- Adatelemző
- Projektvezető 

A következő ábra összetevőkről (zöld) (a vízszintes tengely) életciklusának minden egyes fázisa társított ezen szerepkörök (a függőleges tengely) és (a kék) feladatok rács nézetét jeleníti meg. 

[ ![TDSP-szerepkörök – és-feladatok](./media/overview/tdsp-tasks-by-roles.png) ](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Szabványos projektstruktúra

Az összes projekt megosztása a könyvtárstruktúra és sablonok használatával projekt dokumentumok kellene megkönnyíti a projektjeikhez további információt a csoport tagjai. Kód és a dokumentumok a verziókezelő rendszer (virtuális) például a Git, TFS Subversion engedélyezése és együttműködést vannak tárolva. Követési feladatokat és a szolgáltatások a nyomon követési rendszer például a Jira, Rally és az Azure DevOps-projektkezelési lehetővé teszi, hogy közelebb nyomon követését, a kód egyes funkciókra. Az ilyen nyomkövetési is lehetővé teszi a csapatok beszerzése jobban költségekről. TDSP azt javasolja, különálló-tárházat az egyes projektek létrehozása a alapú VC-k verziószámozása, információ-biztonság és együttműködést. A szabványos struktúra az összes projekt segítségével hozhat létre az intézményi ismeretek a szervezeten belül.

Sablonok a mappastruktúrát és a nem szabványos helyekre szükséges dokumentumok biztosítunk. A mappastruktúra rendszerezi a fájlokat, amelyek az adatok feltárása és a szolgáltatás kivonása kódját tartalmazza, és, jegyezze fel a modell az ismétlések. Ezek a sablonok megkönnyítik a csapattagok, melyben megismerheti a mások által végzett munka, illetve új tagokat felvenni a csapatok számára. Könnyű megtekintése és módosítása a dokumentumok sablonjainak markdown formátumban. Sablonok segítségével biztosíthat ellenőrzőlista biztosítása érdekében, hogy a probléma, jól definiált és, hogy teljesíti-e az elvárt minőségi le minden projekt fő kérdést. Példák erre vonatkozóan:

- egy projekt bérlő az üzleti probléma megoldására és a projekt hatókörének dokumentálása
- dokumentum szerkezetét és a nyers adatok statisztikai adatok jelentések
- modell-jelentést a dokumentum a származtatott szolgáltatásai
- például ROC-görbét vagy MSE modell teljesítmény-mérőszámok


[ ![TDSP-könyvtárak](./media/overview/tdsp-dir-structure.png) ](./media/overview/tdsp-dir-structure.png#lightbox)

A könyvtárstruktúra is lehet klónozni a [GitHub](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastruktúra- és adatelemzési projektek források  

Tdsp-vel megosztott analytics és a tárolási infrastruktúra kezelése ajánlásokat:

- felhőbeli fájlrendszerek adatkészletek tárolására. 
- adatbázisok
- a big data (Hadoop és Spark) fürtök 
- Machine learning szolgáltatás 

Az elemzési és tárolási infrastruktúrájában a felhőben vagy a helyszínen is lehet. Ez a nyers és a feldolgozott adatkészletek tárolására. Ez az infrastruktúra lehetővé teszi, hogy reprodukálható elemzése. Azt is elkerülheti duplikációt tartalmaz, ami inkonzisztens állapotát és infrastruktúrával kapcsolatos felesleges költségeket. Eszközök állnak rendelkezésre a megosztott erőforrások kiépítéséhez, azok nyomon követésére és ezen erőforrások biztonságos kapcsolatot minden egyes csapattag engedélyezése. Emellett tanácsos projekt tagjai konzisztens számítási környezet létrehozásához. Másik csapattag ezután replikálja, és kísérletek ellenőrzése.

Íme egy példa egy csoport több projekten dolgozik, és a megosztási különböző felhőalapú elemzési infrastruktúra-összetevőket.

[ ![TDSP-infrastruktúra](./media/overview/tdsp-analytics-infra.png) ](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Eszközöket és segédprogramokat tehet projekt végrehajtása

A szervezetek folyamatok nagyobb kihívást jelent. Megvalósítása adatok adatelemzési folyamat- és életciklus segítségével csökkentheti az akadályok és azok elfogadását konzisztenciájának biztosított eszközökkel. TDSP-eszközök és parancsfájlok TDSP a bevezetést a csapat gyorsan elindíthatja a kezdeti készletét nyújtja. Emellett elősegíti a adattudományi életciklus adatáttekintés és modellezés alapkonfiguráció például a gyakran előforduló feladatok némelyikének automatizálását. Nincs jól definiált szerkezetben vannak meghatározott személyek számára, hogy a megosztott eszközök és segédprogramok csapatuk megosztott kódtárban való közreműködéshez. A csapata vagy a szervezeten belül más projektek majd is javítható ezeket az erőforrásokat. Tdsp-vel is tervek eszközök és segédprogramok használatával a teljes közösségi hozzájárulások engedélyezéséhez. A TDSP segédprogramok is lehet klónozni a [GitHub](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>További lépések

[Csoportos adatelemzési folyamat: Szerepkörök és feladatok](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) a kulcsfontosságú személyzet szerepköröket és a egy adatelemzési csapatával, amely szabványosítja a folyamattal a hozzájuk kapcsolódó részfeladatokat ismerteti. 
