---
title: Adatok másolása egyszerűen a Másolás varázslóval – Azure |} A Microsoft Docs
description: Ismerje meg a Data Factory másolási varázslója használatával adatokat másol a támogatott adatforrások a fogadóként.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: f904972f-cd33-48db-9755-2b3196ae4168
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 90f78428601d7b039d00d39c1ca8339ab3ace9ba
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020353"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Másolja, vagy az adatok áthelyezése egyszerűen az Azure Data Factory másolási varázslója
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [másolási tevékenység oktatóanyagát](../quickstart-create-data-factory-dot-net.md). 


Az Azure Data Factory Copy varázslót, hogy megkönnyítik a fürtjét adatokat, amely általában egy teljes körű adat-integrációs forgatókönyv az első lépés. Alatt áll az Azure Data Factory Copy varázslót, ha nem kell minden olyan JSON-definíciói társított szolgáltatások, adatkészletek és folyamatok ismertetése. Miután a varázsló összes lépését végrehajtotta, az a varázsló automatikusan létrehozza a folyamat adatokat másol a kijelölt adatforrás a kijelölt célhelyen. Emellett a másolás varázsló segítségével ellenőrizhetők a szerzői műveletek, időpontjában folyamatban betöltött adatokat, menti az idő, különösen ha meg vannak tölt be adatot az első alkalommal az adatforrásból. A másolás varázsló elindításához kattintson a **adatmásolás** csempére az adat-előállító kezdőlapja.

![Másolás varázsló](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Egy intuitív varázslóval telepítheti az adatok másolása
Ez a varázsló lehetővé teszi, hogy könnyedén áthelyezheti az adatokat a különféle forrásokból származó célhelyre percek alatt. Miután a varázsló lépéseit, egy másolási tevékenységgel rendelkező folyamatot automatikusan létrejön, a függő Data Factory-entitásokat (társított szolgáltatásokat és adatkészleteket) együtt. Nincsenek további lépések szükségesek a folyamat létrehozásához.   

![Adatforrás kiválasztása](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Lásd: [másolása varázsló az oktatóanyag](data-factory-copy-data-wizard-tutorial.md) cikk részletes utasításokat követve hozzon létre egy mintafolyamatot másolni az adatokat egy Azure-blobból egy Azure SQL-adatbázistáblába. 
> 
> 

A varázsló big Data típusú szem előtt a kezdetektől úgy van kialakítva. Egyszerű és hatékony adat-előállító folyamatok létrehozását, hogy mappákat, fájlokat vagy táblákat, az adatok másolása varázsló segítségével több száz. A varázsló a következő három szolgáltatás támogatja: Automatikus adatelőnézet séma rögzítése és a leképezési és adatok szűrése. 

## <a name="automatic-data-preview"></a>Automatikus adatelőnézet
A Másolás varázslót, hogy megállapítani, hogy a megfelelő adatokat másolni kívánt adatokat-e a kijelölt adatforrás adatainak részét teszi lehetővé. Emellett ha a forrásadatok egy szövegfájlba, a Másolás varázslóval a szövegfájlt a sor- és oszlophatárolókat és séma további automatikusan elemzi. 

![A Fájlformátum beállításai](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Séma rögzítési és -leképezés
A bemeneti adatok sémáját előfordulhat, hogy a kimeneti adatok bizonyos esetekben a séma nem egyezik meg. Ebben a forgatókönyvben a forrás-séma oszlopait a célséma oszlopokat kell. 

A másolás varázsló automatikusan képezi le a forrás sémájának oszlopai a célséma oszlopai. Ön is a leképezések felülírása a legördülő listákkal (vagy) adja meg, hogy kell-e egy oszlopot ki kell hagyni az adatok másolása közben.   

![Séma-hozzárendelés](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Adatok szűrése
A varázsló lehetővé teszi, hogy végezhet szűrést a forrásadatokat, amelyet a cél/fogadó adattárba másolandó adatok kiválasztásához. Szűrés csökkenti az adatmennyiséget a fogadó adattárba másolandó, és ezért növeli az átviteli sebességet, a másolási művelet. Lehetővé teszi rugalmas szűrt adatok egy relációs adatbázisban SQL lekérdezési nyelv (vagy) fájlok használatával egy Azure blob-mappába használatával [Data Factory-funkciók és a változók](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Adatbázis adatainak szűrése
A példában az SQL-lekérdezést használja a `Text.Format` függvény és `WindowStart` változó. 

![Kifejezések ellenőrzése](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Egy Azure blob-mappában lévő adatok szűrése
Adatokat másol egy mappát, amely alapján futásidőben határozzák meg a mappa elérési útját a változók is használhat [rendszerváltozók](data-factory-functions-variables.md#data-factory-system-variables). A támogatott értékek: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}**, és **{egyéni}**. Példa: inputfolder / {year} / {month} / {day}.

Tegyük fel, hogy a bemeneti mappa a következő formátumban:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Kattintson a **Tallózás** gombot **fájl vagy mappa**, tallózással keresse meg az egyik mappát (például 2016 -> 03 -> 01-02 >), kattintson **válasszon**. Megtekintheti az `2016/03/01/02` a szövegmezőben. Most cserélje le **2016** a **{year}**, **03** a **{month}**, **01** a **{day}** , és **02** a **{hour}**, és lenyomja a Tab billentyűt. Válassza ki ezeket a változókat formátumát, legördülő listák kell megjelennie:

![Rendszerváltozók használata](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Ahogy az az alábbi képernyőfelvételen is látható, is használhatja a **egyéni** változót, és bármely [formázási karakterláncokat támogatott](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Válasszon ki egy mappát, a struktúrával, használja a **Tallózás** először gombra. Ezután cserélje le az értéket **{egyéni}**, és nyomja le az lapján megtekintheti, a szövegmezőbe, ahol beírhatja a formázó karakterlánc.     

![Egyéni változóval](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Változatos és objektumtípusok támogatása
A másolás varázsló használatával hatékonyan áthelyezheti a több száz mappákat, fájlokat vagy táblákat.

![Másolhat adatokat tartalmazó táblák kiválasztása](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Ütemezési beállítások
Futtathatja a másolási művelet egyszer vagy ütemterv alapján (óránként, naponta, és így tovább). Mindkét lehetőség használható betekintést az összekötők a helyszíni, felhőbeli és helyi asztali példány között.

Egy egyszeri másolási művelet csak egyszer lehetővé teszi adatok áthelyezése egy forrásból egy célhelyre. Bármilyen méretű és bármely támogatott formátumú adatok vonatkozik. Az ütemezett másolási lehetővé teszi az előírt ismétlődéssel adatok másolása. Gazdag beállítások (például az újrapróbálkozási, időtúllépés és riasztások) segítségével konfigurálhatja a ütemezett példányt.

![Ütemezés tulajdonságai](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>További lépések
Másolási tevékenységgel rendelkező folyamat létrehozása a Data Factory Copy varázslót a gyors bemutató, lásd: [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md).

