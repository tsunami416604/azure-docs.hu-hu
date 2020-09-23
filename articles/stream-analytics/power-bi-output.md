---
title: Azure Stream Analytics Power BI kimenete
description: Ez a cikk azt ismerteti, hogyan lehet adatokat kiadni a Azure Stream Analyticsból a Power BIba.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: d398cfe063dbbb2bc87a3debf1669afa6a16b43e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90891997"
---
# <a name="power-bi-output-from-azure-stream-analytics"></a>Azure Stream Analytics Power BI kimenete

A Stream Analytics feladatokhoz [Power bi](https://powerbi.microsoft.com/) kimenetként használhatja az elemzési eredmények gazdag vizualizációs élményének biztosításához. Ezt a funkciót használhatja az operatív irányítópultokhoz, a jelentéskészítéshez és a metrikus alapú jelentéskészítéshez.

Stream Analytics Power BI kimenete jelenleg nem érhető el az Azure China 21Vianet és az Azure Germany (T-Systems International) régióiban.

## <a name="output-configuration"></a>Kimeneti konfiguráció

A következő táblázat felsorolja a tulajdonságok nevét és leírásait a Power BI kimenetének konfigurálásához.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias |Adjon meg egy rövid nevet, amelyet a lekérdezésekben használ a lekérdezés kimenetének a Power BI kimenetre való irányításához. |
| Csoport munkaterülete |Ha az adatmegosztást más Power BI felhasználókkal szeretné engedélyezni, válassza ki a csoportokat a Power BI-fiókban, vagy válassza **a saját munkaterület** lehetőséget, ha nem szeretne írni egy csoportba. Egy meglévő csoport frissítéséhez a Power BI hitelesítés megújítása szükséges. |
| Adatkészlet neve |Adja meg azt az adatkészlet-nevet, amelyet a Power BI kimenetének használni szeretne. |
| Tábla neve |Adja meg a tábla nevét a Power BI kimenetének adatkészletében. Jelenleg Power BI Stream Analytics feladatokból származó kimenetnek csak egy táblája lehet egy adatkészletben. |
| Kapcsolat engedélyezése | A kimeneti beállítások konfigurálásához engedélyeznie kell a Power BI. Miután engedélyezte ezt a kimenetet a Power BI-irányítópulthoz, visszavonhatja a hozzáférést a felhasználói fiók jelszavának módosításával, a feladatok kimenetének törlésével vagy a Stream Analytics feladatok törlésével. | 

A Power BI kimenetének és irányítópultjának konfigurálásához lásd: [Azure stream Analytics és Power bi](stream-analytics-power-bi-dashboard.md) oktatóanyag.

> [!NOTE]
> Ne explicit módon hozza létre az adatkészletet és a táblát a Power BI irányítópulton. Az adatkészlet és a tábla automatikusan fel lesz töltve a feladatok elindításakor, és a feladattal Power BIba kerül a bepumpálás. Ha a feladatütemezés nem hoz létre eredményeket, az adatkészlet és a tábla nem jön létre. Ha Power BI már van olyan adatkészlet és tábla, amelynek a neve megegyezik az ebben a Stream Analytics feladatban megadott névvel, a rendszer felülírja a meglévő adatokat.
>

### <a name="create-a-schema"></a>Séma létrehozása

Azure Stream Analytics létrehoz egy Power BI adatkészletet és egy tábla sémát a felhasználó számára, ha még nem léteznek. Minden más esetben a tábla új értékekkel frissül. Jelenleg csak egy tábla létezhet egy adatkészleten belül. 

A Power BI az első, az első kimenő (FIFO) adatmegőrzési szabályt használja. Az adatok egy táblában lesznek gyűjtve, amíg 200 000 sort nem üt.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Adattípus átalakítása Stream Analyticsból Power BI

Azure Stream Analytics az adatmodellt dinamikusan frissíti futásidőben, ha a kimeneti séma megváltozik. Az oszlop neve megváltozik, az oszlop típusa megváltozik, az oszlopok hozzáadása vagy eltávolítása pedig nyomon követhető.

Ez a táblázat a [stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) adattípusok adattípusra való átalakítását ismerteti Power bi [Entity EDM-típusokra](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model), ha Power bi adatkészlet és tábla nem létezik.

Stream Analytics | Power BI
-----|-----
bigint | Int64
nvarchar (max.) | Sztring
dátum/idő | Datetime
float | Dupla
Tömb rögzítése | Karakterlánc típusa, konstans érték: "IRecord" vagy "IArray"

### <a name="update-the-schema"></a>A séma frissítése

Stream Analytics kikövetkezteti az adatmodell-sémát a kimenetben szereplő első események alapján. Később, ha szükséges, az adatmodell-séma úgy frissül, hogy befogadja a bejövő eseményeket, amelyek esetleg nem férnek hozzá az eredeti sémához.

Kerülje a `SELECT *` lekérdezésben, hogy megakadályozza a dinamikus séma frissítését a sorok között. A lehetséges teljesítmény-kihatások mellett előfordulhat, hogy az eredményekhez szükséges idő bizonytalan. Válassza ki azokat a pontos mezőket, amelyeket meg kell jeleníteni a Power BI irányítópulton. Emellett az adatértékeknek meg kell felelniük a választott adattípusnak.

Előző/aktuális | Int64 | Sztring | Datetime | Dupla
-----------------|-------|--------|----------|-------
Int64 | Int64 | Sztring | Sztring | Dupla
Dupla | Dupla | Sztring | Sztring | Dupla
Sztring | Sztring | Sztring | Sztring | Sztring 
Datetime | Sztring | Sztring |  Datetime | Sztring

## <a name="output-batch-size"></a>Kimeneti köteg mérete

A kimeneti köteg mérete: [Power bi REST API-korlátok](https://msdn.microsoft.com/library/dn950053.aspx).

## <a name="next-steps"></a>Következő lépések

* [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)
* [Gyors útmutató: Azure Stream Analytics-feladatok létrehozása az Azure CLI használatával](quick-create-azure-cli.md)
* [Gyors útmutató: Azure Stream Analytics-feladatok létrehozása ARM-sablon használatával](quick-create-azure-resource-manager.md)
* [Gyors útmutató: Stream Analytics-feladatok létrehozása Azure PowerShell használatával](stream-analytics-quick-create-powershell.md)
* [Rövid útmutató: Azure Stream Analytics-feladatok létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [Rövid útmutató: Azure Stream Analytics-feladatok létrehozása a Visual Studio Code-ban](quick-create-visual-studio-code.md)
