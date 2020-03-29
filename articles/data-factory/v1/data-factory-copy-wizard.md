---
title: Adatok másolása egyszerűen a Másolás varázslóval – Azure
description: Megtudhatja, hogy miként másolhatja az adatokat a támogatott adatforrásokból a fogadókba, hogyan másolhatja az adatokat a Data Factory Copy wizardval.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: f904972f-cd33-48db-9755-2b3196ae4168
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8b872cc30ae66e83274f189138dad6d609e3f536
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927051"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Adatok másolása vagy áthelyezése egyszerűen az Azure Data Factory Copy varázslóval
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [másolási tevékenység oktatóanyagát](../quickstart-create-data-factory-dot-net.md). 


Az Azure Data Factory Copy wizard az adatok betöltési folyamatának megkönnyítése, amely általában az első lépés egy teljes körű adatintegrációs forgatókönyvben. Az Azure Data Factory Copy wizard on keresztül, nem kell megérteni a JSON-definíciók csatolt szolgáltatások, adatkészletek és folyamatok. A varázsló összes lépésének elvégzése után azonban a varázsló automatikusan létrehoz egy folyamatot, amely adatokat másol a kijelölt adatforrásból a kijelölt célhelyre. Ezenkívül a Másolás varázsló segít a bevitt adatok ellenőrzésében a szerkesztés időpontjában, ami sok időt takarít meg, különösen akkor, ha először ad be adatokat az adatforrásból. A Másolás varázsló elindításához kattintson az adat-előállító kezdőlapján az **Adatok másolása** csempére.

![Másolás varázsló](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Intuitív varázsló az adatok másolásához
Ez a varázsló lehetővé teszi, hogy az adatokat a legkülönbözőbb forrásokból percek alatt áthelyezheti a célhelyekre. A varázsló átnézése után a rendszer automatikusan létrehoz egy másolási tevékenységet rendelkező folyamatot a függő Data Factory entitásokkal (összekapcsolt szolgáltatásokkal és adatkészletekkel) együtt. A folyamat létrehozásához nincs szükség további lépésekre.   

![Adatforrás kiválasztása](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Lásd: [Másolás varázsló oktatóanyag](data-factory-copy-data-wizard-tutorial.md) cikk lépésenkénti utasításokat hozzon létre egy minta folyamat adatok másolása egy Azure blob egy Azure SQL Database-tábla. 
> 
> 

A varázsló a kezdetektől fogva a big data-adatokat szem előtt tartva lett kialakítva. Egyszerű és hatékony a Data Factory folyamatok létrehozása, amelyek több száz mappát, fájlt vagy táblát mozgatnak át az Adatok másolása varázslóval. A varázsló a következő három szolgáltatást támogatja: Automatikus adatelőnézet, sémarögzítés és -leképezés, valamint az adatok szűrése. 

## <a name="automatic-data-preview"></a>Automatikus adatelőnézet
A Másolás varázsló lehetővé teszi a kijelölt adatforrásból származó adatok egy részének áttekintését annak ellenőrzéséhez, hogy azok az adatok a megfelelő adatok-e, amelyeket másolni szeretne. Ha a forrásadatok szövegfájlban vannak, a másoló varázsló elemzi a szövegfájlt a sor- és oszlophatárolók, valamint a séma automatikus megismeréséhez. 

![A Fájlformátum beállításai](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Séma rögzítése és leképezése
Előfordulhat, hogy a bemeneti adatok sémája bizonyos esetekben nem egyezik meg a kimeneti adatok sémájával. Ebben az esetben a forrásséma oszlopait a célséma oszlopaihoz kell leképeznie. 

A másoló varázsló automatikusan leképezi a forrásséma oszlopait a célséma oszlopaihoz. A leképezéseket felülírhatja a legördülő listák (vagy) segítségével, hogy az adatok másolása közben ki kell-e hagyni egy oszlopot.   

![Sémaleképezés](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Az adatok szűrése
A varázsló lehetővé teszi a forrásadatok szűrését, hogy csak azokat az adatokat jelölje ki, amelyeket a cél-/fogadó adattárba kell másolni. A szűrés csökkenti a fogadó adattárba másolandó adatok mennyiségét, és ezáltal növeli a másolási művelet átviteli erejét. Rugalmas módot biztosít a relációs adatbázisok adatainak szűrésére az Azure blobmappában lévő SQL-lekérdezési nyelv (vagy) fájlok használatával a [Data Factory függvények és változók](data-factory-functions-variables.md)használatával.   

### <a name="filtering-of-data-in-a-database"></a>Adatbázis adatainak szűrése
A példában az SQL-lekérdezés `WindowStart` a függvényt és a `Text.Format` változót használja. 

![Kifejezések ellenőrzése](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Adatok szűrése Azure blob mappában
A mappa elérési útján lévő változók segítségével adatokat másolhat egy olyan mappából, amelyet futásidőben határoznak meg a [rendszerváltozók](data-factory-functions-variables.md#data-factory-system-variables)alapján. A támogatott változók a következők: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** és **{custom}**. Példa: inputfolder/{year}/{month}/{day}.

Tegyük fel, hogy a bemeneti mappák a következő formátumban vannak:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Kattintson a **Tallózás gombra** **a Fájl vagy mappa**területen, keresse meg a mappák egyikét (például 2016->03->01->02), majd kattintson a Választás **gombra.** Látnia `2016/03/01/02` kell a szövegdobozban. Most cserélje le **a 2016-os** értéket **{year} ( {year}**, **03** és **{month}**, **01** **és {day}** és **02** **{hour}** elemre, majd nyomja le a Tab billentyűt. A négy változó formátumának kiválasztásához megjelenjen a legördülő listák:

![Rendszerváltozók használata](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Amint az a következő képernyőképen látható, **egyéni** változót és bármely [támogatott formátumú karakterláncot](https://msdn.microsoft.com/library/8kb3ddd4.aspx)is használhat. Ha ilyen struktúrával rendelkező mappát szeretne kijelölni, először használja a **Tallózás** gombot. Ezután cserélje le az értéket **{custom}** elemre, és a Tab billentyűvel jelenítse meg a szövegmezőt, ahol beírhatja a formázási karakterláncot.     

![Egyéni változó használata](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Különböző adat- és objektumtípusok támogatása
A Másolás varázsló val több száz mappát, fájlt vagy táblát helyezhet át hatékonyan.

![Adatok másolására kijelölt táblák kiválasztása](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Ütemezési beállítások
A másolási műveletet futtathatja egyszer vagy ütemezés szerint (óránként, naponta és így tovább). Mindkét lehetőség használható az összekötők szélessége a helyszíni, felhőbeli és helyi asztali példányban.

Az egyszeri másolási művelet csak egyszer teszi lehetővé az adatok átszállítását a forrásból a célhelyre. Ez vonatkozik az adatok bármilyen méretű és támogatott formátumban. Az ütemezett másolat lehetővé teszi az adatok másolását egy előírt ismétlődésről. Az ütemezett példány konfigurálásához gazdag beállításokat (például újrapróbálkozási, időhosszabbítási és riasztásokat) használhat.

![Ütemezési tulajdonságok](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>További lépések
Ha gyorsútmutatót szeretne használni a Data Factory Copy wizard segítségével a Másolási tevékenységgel rendelkező folyamat létrehozásához, olvassa el [az Oktatóanyag: Folyamat létrehozása a Másolás varázslóval című témakört.](data-factory-copy-data-wizard-tutorial.md)

