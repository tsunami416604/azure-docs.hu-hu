---
title: Mi a csapat adatelemzési folyamata?
description: Adatelemzési módszertant biztosít a prediktív elemzési megoldások és az intelligens alkalmazások biztosításához.
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
ms.openlocfilehash: 10d6e562301e089700940ac5dfb212bcc4e09653
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79088079"
---
# <a name="what-is-the-team-data-science-process"></a>Mi a csapat adatelemzési folyamata?

A Team Data Science Process (TDSP) egy agilis, iteratív adatelemzési módszertan a prediktív elemzési megoldások és az intelligens alkalmazások hatékony biztosítása érdekében. A TDSP segít a csapategyüttműködés és a tanulás javításában azáltal, hogy javasolja a csapatszerepek legjobb együttműködését. A TDSP a Microsoft és más iparági vezetők bevált gyakorlatait és struktúráit tartalmazza az adatelemzési kezdeményezések sikeres megvalósításának elősegítésére. A cél a vállalatok segítése az elemzési programjuk előnyeinek teljes körű megvalósításában.

Ez a cikk áttekintést nyújt a TDSP-ről és fő összetevőiről. Adunk egy általános leírást a folyamat itt, hogy lehet végrehajtani a különböző eszközök. A folyamat életciklusában részt vevő projektfeladatok és szerepkörök részletesebb leírása további kapcsolódó témakörökben található. A TDSP-nek a Csapatainkban való megvalósításához használt Microsoft-eszközök és infrastruktúra egy adott készletének használatával történő megvalósításához is útmutatást nyújtunk.

## <a name="key-components-of-the-tdsp"></a>A TDSP fő összetevői

A TDSP a következő kulcsfontosságú összetevőkből áll:

- **Adatelemzési életciklus-definíció**
- **Szabványosított projektstruktúra**
- **Infrastruktúra és erőforrások** adatelemzési projektekhez
- **Eszközök és segédprogramok** a projekt végrehajtásához


## <a name="data-science-lifecycle"></a>Adatelemzési életciklus

A csapatadat-elemzési folyamat (TDSP) egy életciklust biztosít az adatelemzési projektek fejlesztésének strukturálásához. Az életciklus ismerteti a sikeres projektek által követett összes lépést.

Ha egy másik adatelemzési életciklust használ, például [a CRISP-DM,](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining) [a KDD](https://wikipedia.org/wiki/Data_mining#Process) vagy a szervezet saját egyéni folyamatát, továbbra is használhatja a feladatalapú TDSP-t a fejlesztési életciklusok összefüggésében. Magas szinten ezek a különböző módszerek sok közös vonást jelentenek. 

Ezt az életciklust olyan adatelemzési projektekhez tervezték, amelyek intelligens alkalmazások részeként szállítanak. Ezek az alkalmazások gépi tanulási vagy mesterséges intelligencia modelleket telepítenek a prediktív elemzéshez. Feltáró adatelemzési projektek vagy rögtönzött elemzési projektek is részesülhetnek ezzel a folyamattal. De ilyen esetekben előfordulhat, hogy a leírt lépések némelyikére nincs szükség.    

Az életciklus felvázolja azokat a főbb szakaszokat, amelyeket a projektek általában végrehajtanak, gyakran ismétlődően:

* **Üzleti megértés**
* **Adatgyűjtés és -megértés**
* **Modellezés**
* **Környezet**
* **Vevő immár a bő**

Itt látható a **csapatadat-elemzési folyamat életciklusának**vizuális ábrázolása. 

![TDSP-életciklus2](./media/overview/tdsp-lifecycle2.png) 

A TDSP életciklusának egyes fázisaihoz tartozó célokat, feladatokat és dokumentációs összetevőket a [Csapatadat-feldolgozás](lifecycle.md) életciklus-témakörismerteti. Ezek a tevékenységek és összetevők projektszerepkörökhöz vannak társítva:

- Megoldás építész
- Projektvezető
- Adatelemző
- Projektvezető 

Az alábbi ábra a szerepkörök (a függőleges tengelyen) életciklusának egyes szakaszaihoz (a vízszintes tengelyen) társított feladatok (kék színnel) és (zöld színnel) rácsnézetét tartalmazza. 

[![TDSP-szerepkörök és feladatok](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Szabványos projektstruktúra

Ha az összes projekt megoszt egy könyvtárstruktúrát, és sablonokat használ a projektdokumentumokhoz, a csapattagok könnyen megtalálhatják a projektjeikkel kapcsolatos információkat. Minden kód és dokumentum egy verzió-ellenőrző rendszerben (VCS) (GIT, TFS vagy Subversion) tárolódik a csoportegyüttműködés lehetővé tétele érdekében. A feladatok és funkciók nyomon követése egy agilis projektkövető rendszerben, például a Jira, a Rally és az Azure DevOps lehetővé teszi az egyes funkciók kódjának szorosabb nyomon követését. Az ilyen nyomon követés lehetővé teszi a csapatok számára, hogy jobb költségbecsléseket kapjanak. A TDSP azt javasolja, hogy hozzon létre egy külön adattárat a VCS-en lévő minden projekthez a verziószámozás, az információbiztonság és az együttműködés érdekében. Az összes projekt szabványosított struktúrája segít az intézményi ismeretek szervezeten belüli kiépítésében.

Sablonokat biztosítunk a mappaszerkezethez és a szükséges dokumentumokhoz a szabványos helyeken. Ez a mappastruktúra rendezi az adatok feltárásához és szolgáltatáskinyeréséhez kódot tartalmazó fájlokat, valamint a rekordmodell-ismétléseket. Ezek a sablonok megkönnyítik a csapattagok számára, hogy megértsék a mások által végzett munkát, és új tagokat vegyenek fel a csapatokba. A dokumentumsablonok at markdown formátumban tekintheti meg és frissítheti. A sablonok segítségével az egyes projektekhez kapcsolódó legfontosabb kérdéseket tartalmazó ellenőrzőlisták biztosítják, hogy a probléma jól meghatározott,és hogy a deliverable-ek megfeleljenek a várt minőségnek. Példák erre vonatkozóan:

- a projekt alapokmánya, amely dokumentálja a projekt üzleti problémáját és
- adatjelentéseket a nyers adatok szerkezetének és statisztikáinak dokumentálására
- modelljelentések a származtatott funkciók dokumentálása érdekében
- modellteljesítmény-mérőszámok, például ROC-görbék vagy MSE


[![TDSP-könyvtárak](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

A könyvtárstruktúra klónozható a [GitHubról.](https://github.com/Azure/Azure-TDSP-ProjectTemplate)

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastruktúra és erőforrások adatelemzési projektekhez  

A TDSP javaslatokat tartalmaz a megosztott elemzési és tárolási infrastruktúra kezelésére, például:

- felhőalapú fájlrendszerek adatkészletek tárolására 
- adatbázisok
- big data (Hadoop vagy Spark) fürtök 
- gépi tanulási szolgáltatás 

Az elemzési és tárolási infrastruktúra, ahol a nyers és feldolgozott adatkészletek tárolása, lehet a felhőben vagy a helyszínen. Ez az infrastruktúra reprodukálható elemzést tesz lehetővé. Elkerüli a párhuzamosságokat is, ami következetlenséghez és szükségtelen infrastrukturális költségekhez vezethet. Eszközök állnak rendelkezésre a megosztott erőforrások kiépítéséhez, nyomon követéséhez, és lehetővé teszi, hogy minden csapattag biztonságosan csatlakozzon ezekhez az erőforrásokhoz. Az is jó gyakorlat, hogy a projekt tagjai egységes számítási környezetet hozzanak létre. A különböző csapattagok ezután replikálhatják és ellenőrizhetik a kísérleteket.

Íme egy példa egy csapat dolgozik több projekten, és megosztása a különböző felhőelemzési infrastruktúra-összetevők.

[![TDSP-infrastruktúra](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Eszközök és segédprogramok a projekt végrehajtásához

A folyamatok bevezetése a legtöbb szervezetben kihívást jelent. Az adatelemzési folyamat és életciklus megvalósításához biztosított eszközök segítenek csökkenteni az elfogadásuk akadályait, és növelni azok konzisztenciáját. A TDSP kezdeti eszközöket és parancsfájlokat biztosít a TDSP csapaton belüli bevezetéséhez. Emellett segít automatizálni néhány gyakori feladatok az adatelemzési életciklus, például az adatok feltárása és az alapmodellezés. Van egy jól meghatározott struktúra biztosított az egyének számára, hogy hozzájáruljon a megosztott eszközök és segédprogramok a csapat megosztott kód tárház. Ezeket az erőforrásokat ezután a csapaton vagy a szervezeten belül más projektek is használhatják. A TDSP azt is tervezi, hogy lehetővé teszi az eszközök és segédprogramok hozzájárulását az egész közösség számára. A TDSP segédprogramok klónozhatók a [GitHubról.](https://github.com/Azure/Azure-TDSP-Utilities)


## <a name="next-steps"></a>További lépések

[Csapatadat-elemzési folyamat: Szerepkörök és feladatok](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) Ismerteti a legfontosabb személyzeti szerepkörök és a hozzájuk kapcsolódó feladatok egy adatelemzési csapat, amely egységesíti ezt a folyamatot. 
