---
title: A csapatadat-elemzési folyamat adatgyűjtése és megértése
description: Az adatelemzési projektek adatgyűjtési és -megértéséhez használható célok, feladatok és eredmények
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3c299e9ec42d63812804b5ff7e50324a2de94200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720503"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>A csapatadat-elemzési folyamat adatgyűjtési és -megértési szakasza

Ez a cikk ismerteti a célokat, feladatokat és a csapatadat-elemzési folyamat (TDSP) adatgyűjtési és -megértési szakaszához kapcsolódó célokat, feladatokat és eredményeket. Ez a folyamat egy ajánlott életciklust biztosít, amely az adatelemzési projektek strukturálásához használható. Az életciklus felvázolja azokat a főbb szakaszokat, amelyeket a projektek általában végrehajtanak, gyakran ismétlődően:

   1. **Üzleti ismertetés**
   2. **Adatgyűjtés és adatértelmezés**
   3. **Modellezés**
   4. **Környezet**
   5. **Felhasználói elfogadás**

Itt van egy vizuális ábrázolása a TDSP életciklus: 

![TDSP életciklus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Célok
* Hozzon létre egy tiszta, jó minőségű adatkészletet, amelynek a célváltozókkal való kapcsolata érthető. Keresse meg az adatkészletet a megfelelő elemzési környezetben, így készen áll a modellezésre.
* Az adatfolyamat megoldásarchitektúrájának fejlesztése, amely rendszeresen frissíti és pontozza az adatokat.

## <a name="how-to-do-it"></a>Hogyan kell csinálni
Ebben a szakaszban három fő feladatfoglalkozik:

   * **Az adatok betöltése** a cél analitikus környezetbe.
   * **Az adatok feltárása** annak megállapításához, hogy az adatminőség megfelelő-e a kérdés megválaszolására. 
   * **Állítson be egy adatfolyamatot** az új vagy rendszeresen frissített adatok pontozásához.

### <a name="ingest-the-data"></a>Az adatok betöltése
Állítsa be a folyamatot, hogy az adatok at a forrás helyekről a célhelyekre, ahol elemzési műveleteket futtat, például a betanítás és az előrejelzések. Az adatok különböző Azure-adatszolgáltatásokkal való áthelyezésével kapcsolatos technikai részletekért és lehetőségekért olvassa el az [Adatok betöltése tárolási környezetbe elemzésért című témakört.](ingest-data.md) 

### <a name="explore-the-data"></a>Az adatok vizsgálata
A modellek betanítása előtt ki kell fejlesztenie az adatok alapos megértését. A valós adatkészletek gyakran zajosak, hiányoznak az értékek, vagy számos egyéb eltéréssel rendelkeznek. Az adatok összegzése és vizualizációja segítségével naplózhatja az adatok minőségét, és megadhatja az adatok modellezésre való feldolgozásához szükséges információkat. Ez a folyamat gyakran iteratív.

A TDSP egy [iDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils)nevű automatizált segédprogramot biztosít az adatok megjelenítéséhez és az adatgyűjtő jelentések elkészítéséhez. Azt javasoljuk, hogy először az IDEAR-val kezdje az adatok feltárásához, hogy interaktívan, kódolás nélkül fejlessze a kezdeti adatok megértését. Ezután egyéni kódot írhat az adatok feltárásához és megjelenítéséhez. Az adatok tisztítására vonatkozó útmutatásért olvassa el [a Továbbfejlesztett gépi tanulásra vonatkozó adatok előkészítésére szolgáló feladatok című témakört.](prepare-data.md)  

Miután elégedett a megtisztított adatok minőségével, a következő lépés az adatokban rejlő minták jobb megértése. Ez az adatelemzés segít kiválasztani és kifejleszteni a célmegfelelő prediktív modelljét. Keressen bizonyítékot arra, hogy mennyire kapcsolódik az adatok a célponthoz. Ezután határozza meg, hogy elegendő adat áll-e rendelkezésre a következő modellezési lépések hez. Ismét, ez a folyamat gyakran iteratív. Előfordulhat, hogy új adatforrásokat kell találnia pontosabb vagy relevánsabb adatokkal az előző szakaszban eredetileg azonosított adatkészlet bővítéséhez. 

### <a name="set-up-a-data-pipeline"></a>Adatfolyamat beállítása
Az adatok kezdeti betöltése és tisztítása mellett általában be kell állítania egy folyamatot az új adatok pontozásához vagy az adatok rendszeres frissítéséhez egy folyamatban lévő tanulási folyamat részeként. A pontozás adatfolyamattal vagy munkafolyamattal fejezhető be. A [helyszíni SQL Server-példányból az Azure Data Factory cikkével az Adatok áthelyezése az Azure Data Factory cikkével](move-sql-azure-adf.md) egy példa arra, hogyan állíthat be egy folyamatot az [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)val. 

Ebben a szakaszban az adatfolyamat megoldásarchitektúráját fejleszti ki. A folyamat az adatelemzési projekt következő lépésével párhuzamosan fejleszti. Az üzleti igényektől és a meglévő rendszerek korlátaitól függően, amelyekbe ez a megoldás integrálódik, a csővezeték az alábbi lehetőségek egyike lehet: 

   * Kötegalapú
   * Streamelés vagy valós idejű 
   * Egy hibrid 

## <a name="artifacts"></a>Összetevők
Ebben a szakaszban a következő eredmények olvashatók:

   * [Adatminőségi jelentés](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): Ez a jelentés adatösszegzéseket, az egyes attribútumok és cél közötti kapcsolatokat, a változók rangsorolását és egyebeket tartalmazza. A TDSP részeként biztosított [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) eszköz gyorsan létrehozhatja ezt a jelentést bármilyen táblázatos adatkészleten, például egy CSV-fájlon vagy egy relációs táblán. 
   * **Megoldásarchitektúra:** A megoldás architektúra lehet egy diagram vagy az adatfolyamat leírása, amely segítségével a pontozási vagy előrejelzések az új adatok, miután egy modellt. Azt is tartalmazza a folyamat ot a modell új adatok alapján újratanításához. A TDSP könyvtárstruktúra-sablon használatakor tárolja a dokumentumot a [Project](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) könyvtárban.
   * **Ellenőrzőpont-döntés:** A teljes körű tervezés és a modellépítés megkezdése előtt újraértékelheti a projektet annak meghatározásához, hogy a várt érték elegendő-e a folytatáshoz. Előfordulhat például, hogy készen áll a folytatásra, több adatot kell gyűjtenie, vagy fel kell adnia a projektet, mivel az adatok nem léteznek a kérdés megválaszolásához.

## <a name="next-steps"></a>További lépések

A TDSP életciklusának egyes lépéseire mutató hivatkozások:

   1. [Üzleti ismertetés](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és adatértelmezés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Környezet](lifecycle-deployment.md)
   5. [Felhasználói elfogadás](lifecycle-acceptance.md)

Teljes forgatókönyveket biztosítunk, amelyek bemutatják a folyamat összes lépését az adott forgatókönyvekhez. A [Példa forgatókönyvek](walkthroughs.md) cikk hivatkozásokat és miniatűr leírásokat tartalmazó forgatókönyvek listáját tartalmazza. A forgatókönyvek bemutatják, hogyan kombinálhatja a felhőt, a helyszíni eszközöket és szolgáltatásokat egy munkafolyamatban vagy folyamatban egy intelligens alkalmazás létrehozásához. 

Példák az Azure Machine Learning Studio tdsp-kben végrehajtott lépések végrehajtásáról: [Use the TDSP with Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data).
