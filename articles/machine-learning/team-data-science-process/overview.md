---
title: Mi a csoportos adatelemzési folyamat? | Microsoft Docs
description: A prediktív elemzési megoldások és intelligens alkalmazások adatok-tudományos módszertannal nyújt.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: deguhath
ms.openlocfilehash: 8479b23f49402962acbf50016226ea0f2eb67b68
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839078"
---
# <a name="what-is-the-team-data-science-process"></a>Mi a csoportos adatelemzési folyamat?

A csapat adatok tudományos folyamat (TDSP) egy gyors, fokozatos adatok tudományos módszertana prediktív elemzési megoldások és intelligens alkalmazások hatékonyan továbbítani. A csoportos adatelemzési folyamat elősegíti a csoportok hatékonyabb együttműködését és tanulását. A Microsofttól és az iparág más vállalataitól származó, olyan ajánlott eljárásokból és szerkezetekből álló válogatást tartalmaz, amelyek az adatelemzési kezdeményezések sikeres megvalósítását segítik elő. A cél a vállalatok segítése az elemzési programjuk előnyeinek teljes körű megvalósításában.

Ez a cikk áttekintése TDSP és fő összetevőit. Számos különböző eszközök nyújtunk általános leírását itt a folyamat, amely végrehajtható legyen. Részletes leírása a tevékenységeket és a folyamat az életciklus részt szerepkörök további csatolt témakörökben találhatók. A Microsoft eszközök és infrastruktúra, amely a TDSP megvalósíthatja a csoportok használatával adott használatával TDSP megvalósításához is útmutatást.

## <a name="key-components-of-the-tdsp"></a>A TDSP fő összetevői

A következő fő összetevőket TDSP foglalja magában:

- A **adatok tudományos életciklus** meghatározása
- A **szerkezetének szabványosított**
- **Infrastruktúra- és erőforrások** tudományos projektekhez
- **Eszközök és segédprogramok** projekt végrehajtásra


## <a name="data-science-lifecycle"></a>Adatok tudományos életciklusa

A csapat adatok tudományos folyamat (TDSP) biztosít egy életciklussal, az adatok tudományos projektek fejlesztésének szerkezetének. Az életciklus kezdetétől a végéig, projektek általában követve azok végrehajtásakor, lépéseit mutatja be.

Ha az adatok egy másik tudományos életciklusát, például a [SZÍNELOSZLÁS-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) vagy a szervezet saját egyéni folyamat ezen fejlesztési életciklusának kontextusában a feladatalapú TDSP továbbra is használhatja. Magas szinten a különböző módszereit, amelyek rendelkeznek nagy közös. 

Életciklus úgy tervezték, adatok tudományos projektek intelligens alkalmazások részét képezi. Ezek az alkalmazások központi telepítése a machine learning vagy mesterséges eszközintelligencia modellek prediktív elemzési. Felderítési adatok tudományos projektek vagy alkalmi analytics projektek is is előnyt az ezt a folyamatot. Azonban ebben az esetben egyes leírt lépések nincs feltétlenül szükség.    

Az életciklus, projektek általában hajtható végre, gyakran ismételt fő szakaszait vázolja fel:

* **Üzleti ismertetése**
* **Adatok megszerzését és ismertetése**
* **Modellezési**
* **Üzembe helyezés**
* **Ügyfelek**

Íme egy vizuális ábrázolását a **Team adatok tudományos folyamat életciklus**. 

![TDSP-Lifecycle2](./media/overview/tdsp-lifecycle2.png) 

A célok, a feladatok és a dokumentáció összetevők minden szakaszra a TDSP életciklus a [Team adatok tudományos folyamat életciklus](lifecycle.md) témakör. Ezekről a feladatokról és az összetevők társított projekt szerepkörök:

- Megoldásokért felelős mérnök
- Projektmenedzsert
- Adatelemző
- Projektvezető 

Az alábbi ábra a (kékkel jelölt) feladatok és ezeket a szerepköröket (a függőleges tengely) (a vízszintes tengely) életciklus minden szakaszhoz tartozó összetevők (zöld) a rács nézetét biztosítja. 

![TDSP-szerepkörök-és-feladatok](./media/overview/tdsp-tasks-by-roles.png)

## <a name="standardized-project-structure"></a>Szabványos projekt struktúra

Minden projekt könyvtárstruktúra megosztása és sablonok használata a projekt dokumentumok rendelkező megkönnyíti, hogy a csoport tagjai a projektjeikhez kapcsolatos információk megkereséséhez. A verziókezelő rendszert (virtuális) Git, TFS vagy Subversion Csoportalapú együttműködés engedélyezése kód és a dokumentumok tárolódnak. Feladatok és a szolgáltatások nyomkövetési rendszer például Jira gyors projektben nyomon követése, Rally, a Visual Studio Team Services lehetővé teszi, hogy a kód az egyedi szolgáltatások közelebb követési. Ilyen követési is lehetővé teszi, hogy csapatokkal az beszerzése jobban a becsült költség. TDSP azt javasolja, hogy egy külön tárház minden olyan projekthez létrehozását versioning, információ-biztonság és együttműködés virtuális. Az összes projektet, melyekben szabványosított struktúráját segít intézményi Tudásbázis összeállítása a szervezet.

A szabványos helyekre szükséges dokumentumok és mappastruktúrát nyújtunk sablonok. A gyökérmappa-szerkezetében rendezi a fájlokat, amelyek tartalmazzák az adatok feltárása és a szolgáltatás kivonása kódját, majd, jegyezze fel modell ismétlési. Ezek a sablonok megkönnyítik a csoport tagjai, melyben megismerheti a mások által végzett munka, illetve új tagok hozzáadása csoportokhoz. Könnyen megtekintése és módosítása a dokumentum sablonok markdown formátumban. Adjon meg minden olyan projekthez biztosítását, hogy jól meghatározott-e a problémát, és, termékek esetében megfelel-e a várt minőségi hasonló fontos kérdések ellenőrzőlisták a sablonok használatával. Példák erre vonatkozóan:

- a dokumentum az üzleti probléma és a projekt hatókörének projekt okleveles
- adatot jelentenek a dokumentum a struktúra és a nyers adatok
- a dokumentum a származtatott szolgáltatásai modell jelentések
- modell metrikák: ROC görbék vagy MSE például


![TDSP-könyvtárak](./media/overview/tdsp-dir-structure.png)

A könyvtárstruktúra a klónozható [Github](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastruktúra- és tudományos projektekhez erőforrások  

TDSP megosztott elemzés és a tárolási infrastruktúrák kezelése ajánlásokat:

- felhő fájlrendszereket, olyan adatkészletek tárolása 
- adatbázisok
- big Data típusú adatok (Hadoop vagy külső) fürtök 
- Machine learning szolgáltatás. 

A felhőbeli vagy helyszíni az elemzés és tároló-infrastruktúra lehet. Ez a nyers és feldolgozott adatkészletek tárolására. Ez az infrastruktúra lehetővé teszi, hogy reprodukálható elemzés. Is elkerülhető a másolást, ami inkonzisztenciát és szükségtelen infrastruktúra fenntartási költségeit. Eszközök itt találhatók a megosztott erőforrások kiépítéséhez, nyomon követése, és minden csapattag adott erőforrásokhoz való biztonságos engedélyezése. Akkor is célszerű rendelkezik projekt tagokat hozzon létre egy egységes számítási környezetet. Különböző tagja majd replikálja, és kísérletek ellenőrzése.

Íme egy példa egy csoport több projekten dolgozik, és különböző felhőalapú analytics infrastruktúra összetevőinek megosztása.

![TDSP-infrastruktúra](./media/overview/tdsp-analytics-infra.png)


## <a name="tools-and-utilities-for-project-execution"></a>Eszközöket és segédprogramokat projekt végrehajtása

A legtöbb szervezetben folyamatok bevezetéséről kihívást. Az eszközök valósítja meg az adatok tudományos folyamat és életciklus súgó alacsonyabb korlátainak és növelje a konzisztenciájának azok elfogadását. TDSP tartalmaz egy kezdeti eszközök és parancsfájlok jump-start a csapaton belüli TDSP elfogadását. Emellett segít a adatok tudományos életciklusának például az adatok feltárása és alapterv modellezési gyakori feladatokat automatizálja. Nincs megosztott eszközök és segédprogramok történő a csoport megosztott kód tárházba közre egyéni felhasználók számára előírt jól definiált szerkezetben. A csoport vagy a szervezet más projektek majd is javítható ezeket az erőforrásokat. TDSP terveket is eszközöket és segédprogramokat, ha a teljes közösségi hozzájárulás engedélyezése. A TDSP segédprogramok a klónozható [Github](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>További lépések

[Team tudományos folyamata: Szerepköröket és feladatokat](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) bemutatja azokat a kulcsfontosságú személyzet szerepkörök és a kapcsolódó feladatok a folyamattal szabványosítja adatok tudományos csoport. 
