---
title: Adatok másolása eszköz az Azure Data Factory |} A Microsoft Docs
description: Ismerteti az Azure Data Factory felhasználói felületén az adatok másolása eszközzel
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: yexu
ms.openlocfilehash: 107687c785433f81870449d1445136b5148a4d2c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101754"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Adatok másolása eszköz az Azure Data Factoryban
Az Azure Data Factory az adatok másolása eszköz megkönnyíti a, és optimalizálja a folyamatot, az adatok feldolgozására, ami általában az első lépés az adatok végpontok közötti integrációs forgatókönyve a data lake-be.  Hogy időt takaríthat meg, különösen akkor használhatja az Azure Data Factory betölteni az adatokat egy adatforrásból az első alkalommal. Ez az eszköz használatának előnyei a következők:

- Az Azure Data Factory az adatok másolása eszköz használatakor nem kell ismernie a Data Factory társított szolgáltatásokat, adatkészleteket, folyamatokat, tevékenységek és eseményindítók definíciói. 
- A folyamat az adatok másolása eszköz az adatok betöltését a data lake intuitív. Az eszköz automatikusan létrehozza a szükséges adat-előállító erőforrásokat adatokat másol a kiválasztott forrásadattár a kiválasztott cél/fogadó adattárba. 
- Az adatok másolása eszközzel segít, időpontjában szerzői műveletek, amelyek elkerülheti a saját maga elején lehetséges hibákat betöltött adatok érvényesítése.
- Az adatok betöltése egy data lake az összetett üzleti logikát kell, ha a Data Factory-erőforrások használatával a Data Factory felhasználói felületén szerzői tevékenység – adatok másolása eszköz által létrehozott továbbra is szerkeszthető. 

A következő táblázat nyújt útmutatást a mikor érdemes használni az adatok másolása eszközzel és a tevékenység szerinti szerzői műveletek a Data Factory felhasználói felületén: 

| Adatok másolása eszköz | Egy (másolási) tevékenység készítése |
| -------------- | -------------------------------------- |
| Azt szeretné, amellyel egyszerűen készíthet egy Adatbetöltési feladat anélkül, hogy megismerkedett az Azure Data Factory-entitásokat (társított szolgáltatásokat, adatkészleteket, folyamatokat stb.) | A data lake való betöltésének összetett és rugalmas logikát szeretné. |
| Szeretné gyorsan nagy mennyiségű adat összetevők betöltése a data lake. | Másolási tevékenységgel ellátott tisztító és feldolgozási adatok egymást követő tevékenységek láncolt szeretné. |

Adatok másolása eszköz elindításához kattintson a **adatok másolása** csempére az adat-előállító kezdőlapja.

![Első lépéseket ismertető oldalon – adatok másolása eszköz mutató hivatkozás](./media/copy-data-tool/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Az adatok betöltését a data lake intuitív folyamat
Ez az eszköz lehetővé teszi, hogy könnyedén áthelyezheti az adatokat a különféle forrásokból származó célhelyre percek alatt egy intuitív folyamat:  

1. A beállítások konfigurálása a **forrás**.
2. A beállítások konfigurálása a **cél**. 
3. Konfigurálása **speciális beállítások** például oszlop-hozzárendelés, finomhangolása és tartalék tűrését másolási művelet. 
4. Adjon meg egy **ütemezés** az adatok betöltése a feladatot. 
5. Felülvizsgálat **összefoglaló** a Data Factory-entitásokat létrehozni. 
6. **Szerkesztés** frissíteni a beállításokat a másolási tevékenység, igény szerint a folyamat. 

   Az eszközt úgy tervezték, big Data típusú szem előtt a kezdetektől a változatos és -típusokat támogatja. Több száz mappákat, fájlokat vagy táblákat áthelyezése használhatja azt. Az eszköz automatikus adatelőnézet, séma rögzítése és automatikus hozzárendelés és az adatok szűrése, valamint támogatja.

![Adatok másolása eszköz](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Automatikus adatelőnézet
Megtekintheti az adatokat a kiválasztott adattár, amely lehetővé teszi, hogy az adatok másolásakor ellenőrzése része. Emellett ha a forrásadatok egy szövegfájlba, az adatok másolása eszköz automatikusan észleli a sor- és elválasztó karakterek és séma a szövegfájl elemzi.

![Fájlbeállítások](./media/copy-data-tool/file-format-settings.png)

Az észlelés: után

![Észlelt beállítások és az előzetes verzió](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Séma rögzítése és automatikus hozzárendelés
Az adatforrás sémája nem lehet ugyanaz, mint az adatokat a rendeltetési sok esetben a séma. Ebben a forgatókönyvben a forrás-séma oszlopait a célséma oszlopokat kell.

Az adatok másolása eszközzel figyeli, és megtanulja a viselkedés, forrás-és cél között oszlopok leképezésekor. Után válasszon ki egy vagy több oszlop forrásadattár, és megfeleltet az a cél-sémát, az adatok másolása eszközzel elindul, a minta mindkét oldalról kiválasztott oszlop párokhoz elemzéséhez. Ezt követően ugyanezt a mintát vonatkozik a többi oszlopot. Ezért látja összes oly módon, ha azt szeretné, hogy csupán néhány kattintás után a cél az oszlopok van leképezve.  Ha nem elégedett a kiválasztott oszlop leképezése az adatok másolása eszköz által biztosított, figyelmen kívül hagyásához, és folytassa az oszlopok leképezése a manuálisan. Ugyanakkor az adatok másolása eszközzel folyamatosan tanul és frissíti a minta, és végső soron eléri az oszlopleképezéshez használatával kíván elérni a megfelelő minta. 

> [!NOTE]
> Ha az adatok másolása az SQL Server- vagy Azure SQL Database az Azure SQL Data Warehouse, ha a tábla nem létezik a cél-tárolóban, az adatok másolása eszköz támogatja a tábla létrehozása automatikusan a forrás-séma használatával. 

## <a name="filter-data"></a>Adatok szűrése
Szűrhet a forrásadatok csak kell másolni a fogadó adattárba adatok kiválasztásához. Szűrés csökkenti az adatmennyiséget a fogadó adattárba másolandó, és ezért növeli az átviteli sebességet, a másolási művelet. Copy Data eszköz egy relációs adatbázisban lévő adatok szűrése egy rugalmas módon biztosít az SQL lekérdezési nyelvet, vagy a fájlok használatával egy Azure blob-mappába. 

### <a name="filter-data-in-a-database"></a>Adatbázis adatainak szűrése
Az alábbi képernyőképen látható szűrje az adatokat egy SQL-lekérdezést.

![Adatbázis adatainak szűrése](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Adatok szűrése egy Azure blob-mappába
A mappa elérési útjának változók használatával adatokat másol egy mappát. A támogatott értékek: **{year}**, **{month}**, **{day}**, **{hour}**, és **{minute}**. Például: inputfolder / {year} / {month} / {day}. 

Tegyük fel, hogy a bemeneti mappa a következő formátumban: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Kattintson a **Tallózás** gombot **fájl vagy mappa**, tallózással keresse meg az egyik mappát (például 2016 -> 03 -> 01-02 >), kattintson **válasszon**. 2016/03/01/02 a szövegmezőben kell megjelennie. 

Ezután cserélje le **2016** a **{year}**, **03** a **{month}**, **01** a **{day}** , és **02** a **{hour}**, és nyomja le a **lapon** kulcs. Válassza ki ezeket a változókat formátumát, legördülő listák kell megjelennie:

![A szűrő fájl vagy mappa](./media/copy-data-tool/filter-file-or-folder.png)

Az adatok másolása eszközzel létrehoz paraméterek kifejezések, funkciók és rendszerváltozók használható, amelyek {year}, {month}, {day}, {hour}, {minute}, folyamat létrehozásakor és. További információkért lásd: a [olvasására vagy írására hogyan particionált adatok](how-to-read-write-partitioned-data.md) cikk.

## <a name="scheduling-options"></a>Ütemezési beállítások
Futtathatja a másolási művelet egyszer vagy ütemterv alapján (óránként, naponta, és így tovább). Ezek a beállítások az összekötők különböző környezetekben, beleértve a helyszíni, felhőbeli és helyi asztali használható. 

Egy egyszeri másolási művelet csak egyszer lehetővé teszi adatok áthelyezése egy forrásból egy célhelyre. Bármilyen méretű és bármely támogatott formátumú adatok vonatkozik. Az ütemezett másolási lehetővé teszi egy Ön által megadott ismétlődési az adatok másolása. Gazdag beállítások (például az újrapróbálkozási, időtúllépés és riasztások) segítségével konfigurálhatja a ütemezett példányt.

![Ütemezési beállítások](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>További lépések
Próbálja meg ezek az oktatóanyagok, amely az adatok másolása eszközzel:

- [Gyors útmutató: hozzon létre egy adat-előállítót az adatok másolása eszközzel](quickstart-create-data-factory-copy-data-tool.md)
- [Oktatóanyag: adatok másolása az Azure-ban az adatok másolása eszközzel](tutorial-copy-data-tool.md) 
- [Oktatóanyag: másolási a helyszíni adatok az Azure-ban az adatok másolása eszközzel](tutorial-hybrid-copy-data-tool.md)
