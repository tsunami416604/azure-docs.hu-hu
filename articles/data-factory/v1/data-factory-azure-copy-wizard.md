---
title: A Data Factory Azure másolási varázslója |} A Microsoft Docs
description: Ismerje meg a Data Factory Azure másolása varázsló használatával adatokat másol a támogatott adatforrások a fogadóként.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d13e304b0d10e8bd34d306426f1f9164bcc6be94
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014250"
---
# <a name="azure-data-factory-copy-wizard"></a>Az Azure Data Factory másolási varázslója
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. 

Az Azure Data Factory másolás varázslójával megkönnyíti az adatok feldolgozására ez általában egy teljes körű adat-integrációs forgatókönyv az első lépés a folyamat. Alatt áll az Azure Data Factory Copy varázslót, ha nem kell minden olyan JSON-definíciói társított szolgáltatások, adatkészletek és folyamatok ismertetése. A varázsló automatikusan létrehoz egy folyamat adatokat másol a kijelölt adatforrás a kijelölt célhelyen. Emellett másolása varázsló segít érvényesíteni a szerzői időpontjában folyamatban betöltött adatokat. Ezzel időt takaríthat meg, különösen ha meg vannak tölt be adatot az első alkalommal az adatforrásból. A másolás varázsló elindításához kattintson a **adatmásolás** csempére az adat-előállító kezdőlapja.

![Másolás varázsló](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Big Data típusú adatok tervezve
Ez a varázsló lehetővé teszi, hogy könnyedén áthelyezheti az adatokat a különféle forrásokból származó célhelyre percek alatt. Után nyissa meg a varázsló lépéseit, egy másolási tevékenységgel rendelkező folyamat automatikusan létrejön, a függő Data Factory-entitásokat (társított szolgáltatásokat és adatkészleteket) együtt. Nincsenek további lépések szükségesek a folyamat létrehozásához.   

![Adatforrás kiválasztása](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> A részletes utasításokat követve hozzon létre egy mintafolyamatot az adatok másolása Azure blobból egy Azure SQL Database adatbázistábla, tekintse meg a [másolása varázsló az oktatóanyag](data-factory-copy-data-wizard-tutorial.md).
>
>

A varázsló úgy van kialakítva, big Data típusú szem előtt a kezdetektől a változatos és -típusokat támogatja. Data Factory-folyamatok, hogy több száz mappákat, fájlokat vagy táblákat hozhat létre. A varázsló támogatja az automatikus adatelőnézet, séma rögzítési és leképezési és az adatok szűrése.

## <a name="automatic-data-preview"></a>Automatikus adatelőnézet
Annak érdekében, hogy ellenőrizze, hogy az adatokat másolni kívánt megtekintheti az adatokat a kijelölt adatforrás része. Ezenkívül ha a forrásadatok egy szövegfájlba, a Másolás varázslót a szövegfájlt a sor- és elválasztó karakterek és séma további automatikusan elemzi.

![A Fájlformátum beállításai](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Séma rögzítési és -leképezés
A bemeneti adatok sémáját előfordulhat, hogy a kimeneti adatok bizonyos esetekben a séma nem egyezik meg. Ebben a forgatókönyvben a forrás-séma oszlopait a célséma oszlopokat kell.

> [!TIP]
> Amikor az adatok másolása az SQL Server- vagy Azure SQL Database az Azure SQL Data Warehouse, ha a tábla nem létezik a cél tárolójában, a Data Factory támogatja az adatforrás séma használatával automatikus tábla létrehozásához. További információkat talál a [adatok importálására és az Azure Data Factory használatával az Azure SQL Data Warehouse](./data-factory-azure-sql-data-warehouse-connector.md).
>

Egy legördülő lista használatával válasszon ki egy oszlopot a forrás-séma a célséma oszlop leképezése. A varázsló megpróbálja oszlopleképezéshez a minta ismertetése. Ugyanezt a mintát vonatkozik a többi oszlop, így nem kell az oszlopokat a séma-hozzárendelés végrehajtásához külön-külön jelölje ki. Igény szerint a legördülő listák segítségével hozzárendelheti az oszlopokat egyenként felülbírálhatja ezeket a leképezéseket. A minta akkor pontosabb, mivel további oszlopok leképezésére. A Másolás varázslóval a mintázat folyamatosan frissíti, és végső soron eléri a megfelelő mintájának használatával kíván elérni az oszlopleképezés.     

![Séma-hozzárendelés](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Adatok szűrése
Szűrhet a forrásadatok csak kell másolni a fogadó adattárba adatok kiválasztásához. Szűrés csökkenti az adatmennyiséget a fogadó adattárba másolandó, és ezért növeli az átviteli sebességet, a másolási művelet. Adatok szűrése egy relációs adatbázisban rugalmas módot biztosít az SQL-lekérdezési nyelv használatával, vagy segítségével egy Azure blob-mappában található fájlok [Data Factory-funkciók és a változók](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Adatbázis adatainak szűrése
Az alábbi képernyőfelvételen egy SQL query használatával a `Text.Format` függvény és `WindowStart` változó.

![Kifejezések ellenőrzése](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Egy Azure blob-mappában lévő adatok szűrése
Adatokat másol egy mappát, amely alapján futásidőben határozzák meg a mappa elérési útját a változók is használhat [rendszerváltozók](data-factory-functions-variables.md#data-factory-system-variables). A támogatott értékek: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}**, és **{egyéni}**. Például: inputfolder / {year} / {month} / {day}.

Tegyük fel, hogy a bemeneti mappa a következő formátumban:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Kattintson a **Tallózás** gombot **fájl vagy mappa**, tallózással keresse meg az egyik mappát (például 2016 -> 03 -> 01-02 >), kattintson **válasszon**. Megtekintheti az `2016/03/01/02` a szövegmezőben. Most cserélje le **2016** a **{year}**, **03** a **{month}**, **01** a **{day}** , és **02** a **{hour}**, és nyomja le a **lapon** kulcs. Válassza ki ezeket a változókat formátumát, legördülő listák kell megjelennie:

![Rendszerváltozók használata](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Ahogy az az alábbi képernyőfelvételen is látható, is használhatja a **egyéni** változót, és bármely [formázási karakterláncokat támogatott](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Válasszon ki egy mappát, a struktúrával, használja a **Tallózás** először gombra. Ezután cserélje le az értéket **{egyéni}**, és nyomja le a **lapon** billentyűt megnyomva megtekintheti a szövegmezőbe, ahol beírhatja a formázó karakterlánc.     

![Egyéni változóval](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Ütemezési beállítások
Futtathatja a másolási művelet egyszer vagy ütemterv alapján (óránként, naponta, és így tovább). Az összekötők a különböző környezetekben, beleértve a helyszíni, felhőbeli és helyi asztali példány bővítheti a mindkét lehetőség is használható.

Egy egyszeri másolási művelet csak egyszer lehetővé teszi adatok áthelyezése egy forrásból egy célhelyre. Bármilyen méretű és bármely támogatott formátumú adatok vonatkozik. Az ütemezett másolási lehetővé teszi az előírt ismétlődéssel adatok másolása. Gazdag beállítások (például az újrapróbálkozási, időtúllépés és riasztások) segítségével konfigurálhatja a ütemezett példányt.

![Ütemezés tulajdonságai](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>További lépések
Másolási tevékenységgel rendelkező folyamat létrehozása a Data Factory Copy varázslót a gyors bemutató, lásd: [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md).
