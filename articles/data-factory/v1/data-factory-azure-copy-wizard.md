---
title: Adatgyári Azure-másolás varázsló
description: Megtudhatja, hogy miként másolhatja az adatokat a támogatott adatforrásokból a fogadókból a Data Factory Azure Copy wizard használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fef9059700e2bd94029c40bb819870a7174e0812
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930122"
---
# <a name="azure-data-factory-copy-wizard"></a>Azure Data Factory Copy varázsló
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. 

Az Azure Data Factory Copy wizard megkönnyíti az adatok betöltésének folyamatát, ami általában az első lépés egy teljes körű adatintegrációs forgatókönyvben. Az Azure Data Factory Copy wizard on keresztül, nem kell megérteni a JSON-definíciók csatolt szolgáltatások, adatkészletek és folyamatok. A varázsló automatikusan létrehoz egy folyamatot, amely adatokat másol a kijelölt adatforrásból a kijelölt célhelyre. Ezenkívül a Másolás varázsló segít a bevitt adatok érvényesítésében a szerzői jog idején. Ez időt takarít meg, különösen akkor, ha először ad be adatokat az adatforrásból. A Másolás varázsló elindításához kattintson az adat-előállító kezdőlapján az **Adatok másolása** csempére.

![Másolás varázsló](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Big data-adatokhoz tervezve
Ez a varázsló lehetővé teszi, hogy az adatokat a legkülönbözőbb forrásokból percek alatt áthelyezheti a célhelyekre. Miután végigment a varázslón, a rendszer automatikusan létrehoz egy másolási tevékenységet, valamint a függő Data Factory entitásokat (csatolt szolgáltatásokat és adatkészleteket). A folyamat létrehozásához nincs szükség további lépésekre.   

![Adatforrás kiválasztása](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Az Azure blobból egy Azure SQL Database-táblába történő adatok másolásához részletes útmutatást a [Másolás varázsló oktatóanyaga](data-factory-copy-data-wizard-tutorial.md)című témakörben talál.
>
>

A varázsló a kezdetektől fogva a big data-adatokat szem előtt tartva készült, és támogatja a különböző adat- és objektumtípusokat. Létrehozhat olyan Data Factory-folyamatokat, amelyek több száz mappát, fájlt vagy táblát mozgatnak át. A varázsló támogatja az automatikus adatmegtekintést, a sémarögzítést és -leképezést, valamint az adatszűrést.

## <a name="automatic-data-preview"></a>Automatikus adatelőnézet
A kijelölt adatforrásból megtekintheti az adatok egy részét annak ellenőrzése érdekében, hogy az adatokat szeretné-e másolni. Ha a forrásadatok szövegfájlban vannak, a Másolás varázsló elemzi a szövegfájlt, hogy automatikusan megtanulja a sor- és oszlophatárolókat és sémát.

![A Fájlformátum beállításai](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Séma rögzítése és leképezése
Előfordulhat, hogy a bemeneti adatok sémája bizonyos esetekben nem egyezik meg a kimeneti adatok sémájával. Ebben az esetben a forrásséma oszlopait a célséma oszlopaihoz kell leképeznie.

> [!TIP]
> Adatok másolásakor az SQL Server vagy az Azure SQL Database az Azure SQL Data Warehouse, ha a tábla nem létezik a céltárolóban, a Data Factory támogatja az automatikus tábla létrehozása a forrás sémája használatával. Tudjon meg többet az [Adatok áthelyezése az Azure SQL Data Warehouse-ba és onnan az Azure Data Factory használatával.](./data-factory-azure-sql-data-warehouse-connector.md)
>

A legördülő lista segítségével válasszon ki egy oszlopot a forrássémából a célséma egyik oszlopához való hozzárendeléséhez. A Másolás varázsló megpróbálja megérteni az oszlopleképezés mintáját. Ugyanazt a mintát alkalmazza a többi oszlopra is, így a sémaleképezés befejezéséhez nem kell egyenként kijelölnie az egyes oszlopokat. Ha szeretné, felülírhatja ezeket a leképezéseket a legördülő listák segítségével az oszlopok egyenkénti leképezéséhez. A minta egyre pontosabb lesz, ahogy több oszlopot térképez fel. A Másolás varázsló folyamatosan frissíti a mintát, és végül eléri az elérni kívánt oszlopleképezés megfelelő mintáját.     

![Sémaleképezés](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Az adatok szűrése
A forrásadatok szűrésével csak azokat az adatokat jelölheti ki, amelyeket a fogadó adattárba kell másolni. A szűrés csökkenti a fogadó adattárba másolandó adatok mennyiségét, és ezáltal növeli a másolási művelet átviteli erejét. Ez egy rugalmas módja annak, hogy az SQL lekérdezési nyelv, vagy fájlok egy Azure blob mappában a [Data Factory függvények és változók](data-factory-functions-variables.md)használatával szűrni az adatokat.   

### <a name="filtering-of-data-in-a-database"></a>Adatbázis adatainak szűrése
A következő képernyőképen egy `Text.Format` SQL-lekérdezés látható a függvény és `WindowStart` a változó használatával.

![Kifejezések ellenőrzése](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Adatok szűrése Azure blob mappában
A mappa elérési útján lévő változók segítségével adatokat másolhat egy olyan mappából, amelyet futásidőben határoznak meg a [rendszerváltozók](data-factory-functions-variables.md#data-factory-system-variables)alapján. A támogatott változók a következők: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** és **{custom}**. Például: inputfolder/{year}/{month}/{day}.

Tegyük fel, hogy a bemeneti mappák a következő formátumban vannak:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Kattintson a **Tallózás gombra** **a Fájl vagy mappa**területen, keresse meg a mappák egyikét (például 2016->03->01->02), majd kattintson a Választás **gombra.** Látnia `2016/03/01/02` kell a szövegdobozban. Most cserélje le **a 2016-os** értéket **{year}**( **03** , **{month}**, **01** **és {day}** és **02** **{hour}** elemre, és nyomja le a **Tab** billentyűt. A négy változó formátumának kiválasztásához megjelenjen a legördülő listák:

![Rendszerváltozók használata](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Amint az a következő képernyőképen látható, **egyéni** változót és bármely [támogatott formátumú karakterláncot](https://msdn.microsoft.com/library/8kb3ddd4.aspx)is használhat. Ha ilyen struktúrával rendelkező mappát szeretne kijelölni, először használja a **Tallózás** gombot. Ezután cserélje le az értéket **{custom}** elemre, és a **Tab** billentyűvel jelenítse meg a szövegmezőt, ahol beírhatja a formázási karakterláncot.     

![Egyéni változó használata](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Ütemezési beállítások
A másolási műveletet futtathatja egyszer vagy ütemezés szerint (óránként, naponta és így tovább). Mindkét beállítás használható az összekötők szélessége a környezetekben, beleértve a helyszíni, felhőbeli és helyi asztali példányt.

Az egyszeri másolási művelet csak egyszer teszi lehetővé az adatok átszállítását a forrásból a célhelyre. Ez vonatkozik az adatok bármilyen méretű és támogatott formátumban. Az ütemezett másolat lehetővé teszi az adatok másolását egy előírt ismétlődésről. Az ütemezett példány konfigurálásához gazdag beállításokat (például újrapróbálkozási, időhosszabbítási és riasztásokat) használhat.

![Ütemezési tulajdonságok](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>További lépések
Ha gyorsútmutatót szeretne használni a Data Factory Copy wizard segítségével a Másolási tevékenységgel rendelkező folyamat létrehozásához, olvassa el [az Oktatóanyag: Folyamat létrehozása a Másolás varázslóval című témakört.](data-factory-copy-data-wizard-tutorial.md)
