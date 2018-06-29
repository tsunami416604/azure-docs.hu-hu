---
title: Data Factory Azure másolása varázsló |} Microsoft Docs
description: Ismerje meg az adatokat másolni a támogatott adatforrások mosdók a Data Factory Azure másolása varázsló használatával kapcsolatban.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6330854f4ee9b2db1597d916c815bb6646da6a29
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052749"
---
# <a name="azure-data-factory-copy-wizard"></a>Az Azure Data Factory másolása varázsló
> [!NOTE]
> Ez a cikk a Data Factory 1 verziójára vonatkozik. 

Az Azure Data Factory másolása varázsló megkönnyíti a folyamat választásával dolgozhat fel adatokat, amely általában az első lépés egy végpont integrációs forgatókönyvet. Az Azure Data Factory másolása varázsló áthaladás, ha nem kell bármely JSON-definíciók társított szolgáltatások, adathalmazok és adatcsatornák ismertetése. A varázsló automatikusan létrehozza a kijelölt adatforrás adatainak másolása a kiválasztott cél csővezeték. Ezenkívül másolása varázsló segít a szerzői időpontjában okozhatnak alatt álló adatok érvényesítéséhez. Ezzel időt takaríthat meg különösen ha meg vannak adatok bevitele először az adatforrásból. A varázsló elindításához kattintson a **adatok másolása** csempét a data factory kezdőlapján.

![Másolás varázsló](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>A big data tervezett
Ez a varázsló lehetővé teszi, hogy könnyedén helyezhetik át adatokat különböző forrásokból célhelyekre perc múlva. Után, nyissa meg a varázsló használatával, a másolási tevékenység során a folyamat automatikusan létrejön, függő Data Factory-entitások (társított szolgáltatások és adatkészletek) együtt. Nincsenek további lépéseket kell végrehajtani a folyamatot létrehozni.   

![Adatforrás kiválasztása](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Részletes útmutatás egy minta folyamat adatok másolása az Azure blob-Azure SQL Database táblához, tekintse meg a [másolása varázsló az oktatóanyag](data-factory-copy-data-wizard-tutorial.md).
>
>

A varázsló a big Data típusú adatok szem előtt, támogatja a különböző adatok és az Objektumtípusok Start úgy van kialakítva. Adat-előállító adatcsatornák, helyezze át a mappát, a fájlok vagy a táblák több száz hozhat létre. A varázsló támogatja az automatikus adatelőnézet, séma rögzítési és leképezés és az adatok szűrése.

## <a name="automatic-data-preview"></a>Automatikus megtekintés
Ahhoz, hogy ellenőrizze, hogy az adatokat másolni kívánt megtekintheti a kijelölt adatforrásból származó adatok egy részét. Ezenkívül ha az adatok a fájlt, a varázsló a szövegfájlt a sor- és határolójel és séma automatikusan további elemzi.

![A Fájlformátum beállításai](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Séma rögzítési és -leképezés
A séma, bemeneti adatokat előfordulhat, hogy a kimeneti adatokat egyes esetekben sémája nem egyezik meg. Ebben a forgatókönyvben kell hozzárendelni a cél séma oszlopok a forrás séma oszlopokat.

> [!TIP]
> A Másolás az SQL Server vagy az Azure SQL Database az Azure SQL Data Warehouse, ha a tábla nem létezik a céltár a Data Factory támogatják az adatforrás séma használatával automatikus tábla létrehozásához. A további [helyezze át az adatokat, és az Azure SQL Data Warehouse Azure Data Factory használatával](./data-factory-azure-sql-data-warehouse-connector.md).
>

A legördülő lista használatával olyan oszlopot válasszon ki a cél sémában oszlop leképezése a forrás-séma. A varázsló megpróbálja a mintát oszlopleképezés ismertetése. Érvényes ugyanilyen mintájú a többi oszlop, így nem kell kiválasztania a séma-hozzárendelése egyenként befejezéséhez oszlopokhoz. Ha szeretné, felülbírálhatja a leképezések az oszlopait egyenként a legördülő listák segítségével. A minta még pontosabb további oszlopok leképez válik. A varázsló folyamatosan frissíti a mintát, és végső soron a megfelelő mintáját megvalósítására oszlopleképezés eléri.     

![Séma-hozzárendelése](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Adatok szűrése
Szűrheti a forrásadatok csak, amelyet a fogadó adattárba másolni kívánt adatok kiválasztásához. Szűrés csökkenti a fogadó adattárba másolandó adatok mennyiségét, és ezért javítja a teljesítményt, a másolási művelet. Egy relációs adatbázisban lévő adatok szűrése rugalmas lehetőséget biztosít az SQL-lekérdezési nyelv használatával, vagy az Azure blob mappában a fájlok [adat-előállító funkciók és változók](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Egy adatbázis adatok szűrése
Az alábbi képernyőfelvételen látható egy SQL lekérdezés használata a `Text.Format` függvény és `WindowStart` változó.

![Kifejezések ellenőrzése](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Az adatok Azure blob mappában szűrése
Adatok másolása egy mappába, amely alapján futásidőben határozza meg a mappa elérési változók is használhatja [rendszerváltozók](data-factory-functions-variables.md#data-factory-system-variables). A támogatott értékek: **{year}**, **{month}**, **{day}**, **{óra}**, **{perc}**, és **{egyéni}**. Például: inputfolder / {year} / {month} / {day}.

Tegyük fel, hogy rendelkezik-e bemeneti mappák a következő formátumban:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Kattintson a **Tallózás** gombra kattint, a **fájl vagy mappa**, keresse meg az egyik mappát (például 2016 -> 03 -> 01 -> 02), és kattintson a **válasszon**. Megtekintheti az `2016/03/01/02` a szövegmezőben. Most, cserélje le **2016** rendelkező **{year}**, **03** rendelkező **{month}**, **01** rendelkező **{day}** , és **02** rendelkező **{óra}**, és nyomja le az ENTER a **lapon** kulcs. Legördülő lista használatával válassza ki a formátumot az alábbi négy változók kell megjelennie:

![Rendszer változók használata](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Ahogy az az alábbi képernyőfelvételen, használhatja a **egyéni** változó, és bármilyen [támogatott formázási karakterláncok](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Válasszon egy mappát a struktúra, használja a **Tallózás** először gombra. Ezután cserélje le a értékét **{egyéni}**, és nyomja le az ENTER a **lapon** billentyűt megnyomva megtekintheti a szövegmezőben, ahová beírhatja a Formátum-karakterlánc.     

![Egyéni változóval](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Ütemezési beállítások
Futtathatja a másolási művelet egyszer vagy ütemezés (óránként, naponta, és így tovább). Mindkét lehetőség használható a hardverekről az összekötők környezetek, beleértve a helyszíni, a felhő és a helyi asztali példány között.

Egy egyszeri másolási művelet lehetővé teszi, hogy egy célra forrásból adatmozgás csak egyszer. Érvényes adatok bármilyen méretű és bármely támogatott formátumra. Az ütemezett másolatát lehetővé teszi, hogy az előírt ismétlődése adatokat másolhat. Gazdag beállításaihoz (például az újra gombra, időtúllépés és riasztások) segítségével konfigurálhatja az ütemezett másolatát.

![Ütemezési tulajdonságok](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>További lépések
A Data Factory másolása varázsló segítségével hozzon létre egy folyamatot másolási tevékenység az első útmutatást lásd: [oktatóanyag: hozzon létre egy folyamatot, a másolása varázslóval](data-factory-copy-data-wizard-tutorial.md).
