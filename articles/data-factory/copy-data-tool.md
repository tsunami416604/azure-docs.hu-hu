---
title: Adatok másolása az Azure Data Factory eszköz |} Microsoft Docs
description: Információkat nyújt azokról az adatok másolása eszköz Azure Data Factory felhasználói felületen
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: b82ee060ff3f25e7a92c85114d457ecb349159b3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Másolja az adatokat eszközt az Azure Data Factory
Az Azure Data Factory adatok másolása eszköz megkönnyíti a, és optimalizálja a folyamatot, az adatok választásával dolgozhat fel a data lake, amely általában az első lépés egy végpont integrációs forgatókönyvet.  Menti a idő, különösen ha használatával Azure Data Factory először betöltik az adatforrásból származó adatokat. Ez az eszköz használatának előnyei a következők:

- Az Azure Data Factory adatok másolása eszköz használatakor nem kell ismernie az összekapcsolt szolgáltatások, adathalmazokat, adatcsatornákat, tevékenységek és eseményindítók Data Factory-definíciók. 
- Adatok másolása eszköz áramló az adatok betöltését a data lake intuitív. Az eszköz automatikusan létrehozza az összes szükséges adat-előállító erőforrás adatok másolása a kiválasztott forráshely-tárolót a kiválasztott cél/fogadó tárolót. 
- Az adatok másolása eszköz segítségével érvényesíteni az adatokat, amelyek idején szerzői, amely segít elkerülni a lehetséges hibákat maga elején van folyamatban okozhatnak.
- Ha kell valósítania egy olyan adatokat tölthet be data lake bonyolult üzleti logikát, a tevékenység szerinti szerzői Data Factory felhasználói felületének használatával az adatok másolása eszköz által létrehozott Data Factory erőforrások továbbra is módosíthatja. 

A következő táblázat nyújt útmutatást a Data Factory felhasználói felületén szerzői tevékenység és az adatok másolása eszköz használatával: 

| Adatok másolása eszköz | Egy tevékenység (másolási tevékenység) készítése |
| -------------- | -------------------------------------- |
| Könnyen hozhat létre egy Azure Data Factory entitások (társított szolgáltatások, adathalmazokat, adatcsatornákat stb.) megismerését nélkül feladat betöltése adatokat szeretné | Szeretné végrehajtani az adatok betöltését lake összetett és rugalmas logikát. |
| Gyorsan adatösszetevők nagy számú betöltése a data lake szeretné. | Szeretné láncolt soron következő tevékenységek adatok tisztítására vagy feldolgozásra a másolási tevékenység. |

Az adatok másolása eszköz elindításához kattintson a **adatok másolása** csempét a data factory kezdőlapján.

![Get lépéseket ismertető oldal felkereséséhez - adatok másolása eszköz mutató hivatkozás](./media/copy-data-tool/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Az adatok betöltését a data lake intuitív folyamata
Ez az eszköz lehetővé teszi, hogy könnyedén helyezhetik át adatokat különböző forrásokból célhelyekre egy egyszerűen elsajátítható folyamata a percben:  

1. A beállítások konfigurálása a **forrás**.
2. A beállítások konfigurálása a **cél**. 
3. Konfigurálása **speciális beállítások** például oszlopleképezés Teljesítménybeállítások vagy tartalék tűrését a másolási művelet. 
4. Adjon meg egy **ütemezés** a feladat betöltése adatok. 
5. Felülvizsgálati **összefoglaló** az adat-előállító entitások létrehozni. 
6. **Szerkesztés** frissíteni a másolási tevékenység-beállításokat, szükség esetén a folyamat. 

 Az eszközt a big Data típusú adatok szem előtt, támogatja a különböző adatok és az Objektumtípusok Start tervezték. Helyezze át a mappát, a fájlok vagy a táblák több száz azt használhatja. Az eszköz automatikus adatelőnézet, séma rögzítési és automatikus leképezés és az adatok szűrése, valamint támogatja.

![Adatok másolása eszköz](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Automatikus megtekintés
Megtekintheti a kiválasztott forráshely adattárból, amely lehetővé teszi a adatokat másolással érvényesítéséhez adatok egy részét. Ezenkívül ha az adatok a fájlt, az adatok másolása eszköz kijelölt szöveg automatikus észlelése a sor- és elválasztókat és séma.

![Beállítások](./media/copy-data-tool/file-format-settings.png)

Miután a észlelése:

![Észlelt beállítások és az előzetes verzió](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Séma-készítés és az automatikus leképezés
Az adatforrás sémája nem lehet ugyanaz, mint a adatok cél sok esetben a séma. Ebben a forgatókönyvben kell hozzárendelni a cél séma oszlopok a forrás séma oszlopokat.

Az adatok másolása eszköz figyeli, és megtanulja a viselkedés a forrás- és tárolók közötti oszlopok leképezésekor. Válasszon egy vagy néhány oszlop forrás adattárból, és hozzárendelheti a cél séma, az adatok másolása eszköz elindul, mindkét oldalról kivételezett oszlop párok minta elemzéséhez. Ezt követően vonatkozik, ugyanilyen mintájú oszlopok többi. Ezért láthatja az oszlopok le lett képezve oly módon, ha azt szeretné, néhány kattintással után a cél.  Nem elégedett az adatok másolása eszköz által biztosított oszlopleképezés választás, ha figyelmen kívül hagyhatja, és folytassa az oszlopok kézi leképezése. Ugyanakkor az adatok másolása eszköz folyamatosan megtanulja frissíti a minta és végső soron a megfelelő mintáját megvalósítására oszlopleképezés eléri. 

> [!NOTE]
> Ha a Másolás az SQL Server vagy az Azure SQL Database az Azure SQL Data Warehouse, ha a tábla nem létezik a tárolási célhelye adatok másolása eszköz támogatja a tábla létrehozása automatikusan a forrás-séma segítségével. 

## <a name="filter-data"></a>Adatok szűrése
Szűrheti a forrásadatok csak, amelyet a fogadó adattárba másolni kívánt adatok kiválasztásához. Szűrés csökkenti a fogadó adattárba másolandó adatok mennyiségét, és ezért javítja a teljesítményt, a másolási művelet. Másolás eszköz egy relációs adatbázisban lévő adatok szűrése rugalmas lehetőséget biztosít az Azure blob mappában az SQL lekérdező nyelve, vagy a fájlok használatával. 

### <a name="filter-data-in-a-database"></a>Szűrje az adatokat adatbázisban
Az alábbi képernyőfelvételen látható szűrje az adatokat egy SQL-lekérdezést.

![Szűrje az adatokat adatbázisban](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Szűrje az adatokat az Azure blob mappában
A mappa elérési útja változók segítségével adatok másolása egy mappába. A támogatott értékek: **{year}**, **{month}**, **{day}**, **{óra}**, és **{perc}**. Például: inputfolder / {year} / {month} / {day}. 

Tegyük fel, hogy rendelkezik-e bemeneti mappák a következő formátumban: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Kattintson a **Tallózás** gombra kattint, a **fájl vagy mappa**, keresse meg az egyik mappát (például 2016 -> 03 -> 01 -> 02), és kattintson a **válasszon**. 2016/03/01/02 a szövegmezőben meg kell jelennie. 

Ezután cserélje le **2016** rendelkező **{year}**, **03** rendelkező **{month}**, **01** rendelkező **{day}** , és **02** rendelkező **{óra}**, és nyomja le az ENTER a **lapon** kulcs. Legördülő lista használatával válassza ki a formátumot az alábbi négy változók kell megjelennie:

![A szűrő fájl vagy mappa](./media/copy-data-tool/filter-file-or-folder.png)

Az adatok másolása eszközzel előállított paraméterek kifejezések, Funkciók, és rendszerváltozók használható képviselő {year}, {month}, {day}, {óra} és {percben} folyamat létrehozásakor. További információkért lásd: a [olvasására vagy írására, hogyan particionálva adatok](how-to-read-write-partitioned-data.md) cikk.

## <a name="scheduling-options"></a>Ütemezési beállítások
Futtathatja a másolási művelet egyszer vagy ütemezés (óránként, naponta, és így tovább). Ezek a beállítások az összekötők különböző környezetek, beleértve a helyi, a felhő és a helyi asztali között használható. 

Egy egyszeri másolási művelet lehetővé teszi, hogy egy célra forrásból adatmozgás csak egyszer. Érvényes adatok bármilyen méretű és bármely támogatott formátumra. Az ütemezett másolatát lehetővé teszi, hogy a megadott ismétlődése adatokat másolhat. Gazdag beállításaihoz (például az újra gombra, időtúllépés és riasztások) segítségével konfigurálhatja az ütemezett másolatát.

![Ütemezési beállítások](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>További lépések
Próbálja meg ezek az oktatóanyagok, amely az adatok másolása az eszközzel:

- [Gyors üzembe helyezés: az adatok másolása eszközzel adat-előállító létrehozása](quickstart-create-data-factory-copy-data-tool.md)
- [Oktatóanyag: adatok másolása az Azure-ban az adatok másolása eszköz](tutorial-copy-data-tool.md) 
- [Oktatóanyag: másolás a helyszíni adatok Azure az adatok másolása eszközzel](tutorial-hybrid-copy-data-tool.md)
