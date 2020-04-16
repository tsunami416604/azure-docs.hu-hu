---
title: Adatok másolása eszköz Az Azure Data Factory
description: Az Azure Data Factory felhasználói felületének adatok másolása eszközéről nyújt tájékoztatást
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: yexu
ms.openlocfilehash: df078673aed60086a88961ff64f9bfa596d96346
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414086"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Adatok másolása eszköz az Azure Data Factoryben
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az Azure Data Factory Copy Data eszköz megkönnyíti és optimalizálja az adatok adattóba történő betöltésének folyamatát, ami általában az első lépés egy teljes körű adatintegrációs forgatókönyvben.  Időt takarít meg, különösen akkor, ha az Azure Data Factory segítségével adatokat kell beadni a adatforrásból az első alkalommal. Az eszköz használatának néhány előnye:

- Az Azure Data Factory Copy Data eszköz használatakor nem kell megértenie a Data Factory definícióit a csatolt szolgáltatásokhoz, adatkészletekhez, folyamatokhoz, tevékenységekhez és eseményindítókhoz. 
- Az Adatok másolása eszköz folyamata intuitív az adatok adattóba való betöltéséhez. Az eszköz automatikusan létrehozza az összes szükséges Data Factory erőforrást a kiválasztott forrásadattárból a kiválasztott cél-/fogadóadattárba történő másolásához. 
- Az Adatok másolása eszköz segít a szerzői feldolgozás során bevitt adatok érvényesítésében, így az első vizsgálat során elkerülheti az esetleges hibákat.
- Ha összetett üzleti logikát kell megvalósítania az adatok adattóba való betöltéséhez, továbbra is szerkesztheti az Adatok másolása eszköz által létrehozott Data Factory-erőforrásokat a Data Factory felhasználói felületén végzett tevékenységenkénti szerzői használatával. 

Az alábbi táblázat útmutatást nyújt az adatok másolása eszköz és a tevékenységenkénti szerzői tevékenység használatához a Data Factory felhasználói felületén: 

| Adatok másolása eszköz | Tevékenységenként (Másolási tevékenység) szerzői |
| -------------- | -------------------------------------- |
| Egyszerűen szeretne adatbetöltési feladatot készíteni anélkül, hogy megismerne az Azure Data Factory entitásait (összekapcsolt szolgáltatások, adatkészletek, folyamatok stb.) | Összetett és rugalmas logikát szeretne megvalósítani az adatok tóba való betöltéséhez. |
| Azt szeretné, hogy gyorsan betölteni egy nagy számú adatösszetevők egy adattóba. | A másolási tevékenységet az adatok tisztítására vagy feldolgozására szolgáló későbbi tevékenységekkel szeretné láncolni. |

Az Adatok másolása eszköz elindításához kattintson az adat-előállító kezdőlapján található **Adatok másolása** csempére.

![Első lépések lap – hivatkozás az Adatok másolása eszközre](./media/copy-data-tool/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Intuitív áramlás az adatok adattóba való betöltéséhez
Ez az eszköz lehetővé teszi, hogy könnyedén átaz adatokat a legkülönbözőbb forrásokból a célpontok percek alatt egy intuitív áramlás:  

1. Adja meg a **forrás**beállításait.
2. Adja meg a **cél**beállításait. 
3. Adja meg a másolási művelet **speciális beállításait,** például az oszlopleképezést, a teljesítménybeállításokat és a hibatűrési beállításokat. 
4. Adja meg az adatbetöltési feladat **ütemezését.** 
5. Tekintse át a létrehozandó adatfeldolgozó entitások **összegzését.** 
6. A folyamat **szerkesztésével** szükség szerint frissítheti a másolási tevékenység beállításait. 

   Az eszköz célja a big data szem előtt tartva a kezdetektől fogva, támogatja a különböző adatok és objektumtípusok. Több száz mappa, fájl vagy tábla áthelyezésére használhatja. Az eszköz támogatja az automatikus adatmegtekintést, a sémarögzítést és az automatikus leképezést, valamint az adatszűrést is.

![Adatok másolása eszköz](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Automatikus adatelőnézet
Az adatok egy részét megtekintheti a kijelölt forrásadattárból, amely lehetővé teszi a másolt adatok érvényesítését. Ha a forrásadatok szövegfájlban vannak, az Adatok másolása eszköz elemzi a szövegfájlt, hogy automatikusan észlelje a sor- és oszlophatárolókat és a sémát.

![Fájlbeállítások](./media/copy-data-tool/file-format-settings.png)

Az észlelés után:

![Fájlbeállítások észlelése és előnézete](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Sémarögzítés és automatikus hozzárendelés
Az adatforrás sémája sok esetben nem egyezik meg az adatcél sémájával. Ebben az esetben a forrásséma oszlopait a célséma oszlopaihoz kell leképeznie.

Az Adatok másolása eszköz figyeli és megtanulja a viselkedését, amikor oszlopokat térképez le a forrás- és a céltárolók között. Miután kiválasztott egy vagy néhány oszlopot a forrásadattárból, és leképezte őket a célsémára, az Adatok másolása eszköz elkezdi elemezni a mindkét oldalról kiválasztott oszloppárok mintáját. Ezután ugyanazt a mintát alkalmazza a többi oszlopra. Ezért láthatja, hogy az összes oszlop le van képezve a célhoz úgy, ahogy azt csak néhány kattintás után szeretné.  Ha nem elégedett az Adatok másolása eszköz által biztosított oszlopleképezéssel, figyelmen kívül hagyhatja azt, és folytathatja az oszlopok manuális leképezését. Eközben az Adatok másolása eszköz folyamatosan megtanulja és frissíti a mintát, és végül eléri a megfelelő mintát az elérni kívánt oszlopleképezéshez. 

> [!NOTE]
> Adatok másolásakor az SQL Server vagy az Azure SQL Database az Azure SQL Data Warehouse, ha a tábla nem létezik a céltárolóban, adatok másolása eszköz támogatja a tábla automatikus létrehozását a forrásséma használatával. 

## <a name="filter-data"></a>Adatok szűrése
A forrásadatok szűrésével csak azokat az adatokat jelölheti ki, amelyeket a fogadó adattárba kell másolni. A szűrés csökkenti a fogadó adattárba másolandó adatok mennyiségét, és ezáltal növeli a másolási művelet átviteli erejét. Az Adatok másolása eszköz rugalmas módot biztosít a relációs adatbázis adatainak szűrésére az SQL lekérdezési nyelv vagy egy Azure blob mappában lévő fájlok használatával. 

### <a name="filter-data-in-a-database"></a>Adatbázis adatainak szűrése
A következő képernyőképen egy SQL-lekérdezés látható az adatok szűréséhez.

![Adatbázis adatainak szűrése](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Adatok szűrése Azure blob mappában
A mappa elérési útján lévő változók segítségével adatokat másolhat egy mappából. A támogatott változók a következők: **{year}**, **{month}**, **{day}**, **{hour}** és **{minute}**. Például: inputfolder/{year}/{month}/{day}. 

Tegyük fel, hogy a bemeneti mappák a következő formátumban vannak: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Kattintson a **Tallózás gombra** **a Fájl vagy mappa**területen, keresse meg a mappák egyikét (például 2016->03->01->02), majd kattintson a Választás **gombra.** A szövegmezőben a 2016/03/01/02 jelenik meg. 

Ezután cserélje le **a 2016-os** értéket **a következőre: {year}**, 03 **a ({month}**, **01** **és {day}** és **02** **{hour}**– helyett, majd nyomja le a **Tab** billentyűt. **03** A négy változó formátumának kiválasztásához megjelenjen a legördülő listák:

![Fájl vagy mappa szűrése](./media/copy-data-tool/filter-file-or-folder.png)

Az Adatok másolása eszköz olyan paramétereket hoz létre kifejezésekkel, függvényekkel és rendszerváltozókkal, amelyek a folyamat létrehozásakor a(z) {year}, {month}, {day}, {hour} és {minute} kifejezéseket, függvényeket és rendszerváltozókat ábrázolhatnak.

## <a name="scheduling-options"></a>Ütemezési beállítások
A másolási műveletet futtathatja egyszer vagy ütemezés szerint (óránként, naponta és így tovább). Ezek a beállítások különböző környezetekben, például a helyszíni, a felhőben és a helyi asztalon is használhatók az összekötőkhöz. 

Az egyszeri másolási művelet csak egyszer teszi lehetővé az adatok átszállítását a forrásból a célhelyre. Ez vonatkozik az adatok bármilyen méretű és támogatott formátumban. Az ütemezett másolat lehetővé teszi a megadott ismétlődésadatainak másolását. Az ütemezett példány konfigurálásához gazdag beállításokat (például újrapróbálkozási, időhosszabbítási és riasztásokat) használhat.

![Ütemezési beállítások](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>További lépések
Próbálkozzon az alábbi oktatóanyagokkal, amelyek az Adatok másolása eszközt használják:

- [Rövid útmutató: adatgyár létrehozása az Adatok másolása eszközzel](quickstart-create-data-factory-copy-data-tool.md)
- [Oktatóanyag: adatok másolása az Azure-ban az Adatok másolása eszközzel](tutorial-copy-data-tool.md) 
- [Oktatóanyag: a helyszíni adatok másolása az Azure-ba az Adatok másolása eszközzel](tutorial-hybrid-copy-data-tool.md)
