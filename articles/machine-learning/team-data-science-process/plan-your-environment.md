---
title: A forgatókönyvek azonosítása és az elemzési folyamat megtervezése - Team Data Science Process | Azure gépi tanulás
description: Azonosítsa a forgatókönyveket, és tervezze meg a fejlett elemzési adatfeldolgozást egy sor kulcsfontosságú kérdés mérlegelésével.
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
ms.openlocfilehash: b0b811a2b7ed432b7fc5015886b28337ca33424e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76710326"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Forgatókönyvek azonosítása és a bővített analitika adatfeldolgozása

Milyen erőforrásokszükségesek egy olyan környezet létrehozásához, amely speciális elemzési feldolgozást végezhet egy adatkészleten? Ez a cikk egy sor kérdést javasol, amelyek segíthetnek a forgatókönyvhöz kapcsolódó tevékenységek és erőforrások azonosításában.

A prediktív elemzések magas szintű lépéseinek sorrendjéről a [Mi a csapatadat-elemzési folyamat (TDSP).](overview.md) Minden lépéshez speciális erőforrásokra van szükség az adott forgatókönyvhöz tartozó tevékenységekhez.

A forgatókönyv azonosításához válaszoljon a következő területek legfontosabb kérdéseire:

* adatlogisztika
* adatjellemzők
* adatkészlet minősége
* előnyben részesített eszközök és nyelvek

## <a name="logistic-questions-data-locations-and-movement"></a>Logisztikai kérdések: adathelyek és mozgás

A logisztikai kérdések a következő elemekre vonatkoznak:

* adatforrás helye
* célhely az Azure-ban
* az adatok áthelyezésére vonatkozó követelmények, beleértve az ütemezést, az összeget és az

Előfordulhat, hogy az elemzési folyamat során többször is át kell helyeznie az adatokat. Gyakori forgatókönyv a helyi adatok áthelyezése valamilyen formában a tárolás az Azure-ban, majd a Machine Learning Studio.

### <a name="what-is-your-data-source"></a>Mi az adatforrás?

Az adatok helyiek vagy a felhőben vannak? A lehetséges helyszínek a következők:

* nyilvánosan elérhető HTTP-cím
* helyi vagy hálózati fájl helye
* SQL Server-adatbázis
* egy Azure Storage-tároló

### <a name="what-is-the-azure-destination"></a>Mi az Azure-cél?

Hol kell az adatokfeldolgozáshoz vagy modellezéshez? 

* Azure Blob Storage
* SQL Azure-adatbázisok
* Azure virtuális gépen futó SQL Server
* HDInsight (Hadoop az Azure-ban) vagy Hive-táblák
* Azure Machine Learning
* Csatlakoztatható Azure virtuális merevlemezek

### <a name="how-are-you-going-to-move-the-data"></a>Hogy fogod áthelyezni az adatokat?

Az adatok különböző tárolási és feldolgozási környezetekbe történő betöltésére vagy betöltésére szolgáló eljárásokat és erőforrásokat a következő témakörökben tlátjuk el:

* [Adatok betöltése a tárolási környezetekbe elemzés céljából](ingest-data.md)
* [A betanítási adatok importálása az Azure Machine Learning Studio -ba (klasszikus) különböző adatforrásokból](../studio/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Az adatokat rendszeresen kell áthelyezni, vagy módosítani kell az áttelepítés során?

Fontolja meg az Azure Data Factory (ADF) használatát, ha az adatokat folyamatosan át kell telepíteni. Az ADF a következő célokra lehet hasznos:

* hibrid forgatókönyv, amely helyszíni és felhőalapú erőforrásokat is magában foglal
* olyan forgatókönyv, amelyben az adatok áttelepítése során az üzleti logika szerint tranzakcióra, módosításra vagy módosításra kerül

További információt az [Adatok áthelyezése egy helyszíni SQL-kiszolgálóról az SQL Azure-ba az Azure Data Factory segítségével című témakörben talál.](move-sql-azure-adf.md)

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Mennyi adatot kell áthelyezni az Azure-ba?

A nagy adatkészletek meghaladhatják bizonyos környezetek tárolókapacitását. Például tekintse meg a machine learning studio (klasszikus) méretkorlátokról szóló témakört a következő szakaszban. Ilyen esetekben az elemzés során az adatok mintáját használhatja. Ha részletesen tudni szeretné, hogyan lehet egy adatkészletet levenni különböző Azure-környezetekben, [olvassa el a Mintaadatok a csapatadat-elemzési folyamat ban című témakört.](sample-data.md)

## <a name="data-characteristics-questions-type-format-and-size"></a>Adatjellemzőkkel kapcsolatos kérdések: típus, formátum és méret

Ezek a kérdések kulcsfontosságúak a tárolási és feldolgozási környezetek megtervezéséhez. Ezek segítenek kiválasztani az adattípusnak megfelelő forgatókönyvet, és megérteni a korlátozásokat.

### <a name="what-are-the-data-types"></a>Mik az adattípusok?

* Numerikus
* Kategorikus
* Sztringek
* Bináris

### <a name="how-is-your-data-formatted"></a>Hogyan történik az adatok formázása?

* Vesszővel tagolt (CSV) vagy tabulátorral tagolt (TSV) lapos fájlok
* Tömörített vagy tömörítetlen
* Azure-blobok
* Hadoop Hive asztalok
* SQL Server-táblák

### <a name="how-large-is-your-data"></a>Mekkoraaz adatok?

* Kicsi: Kevesebb mint 2 GB
* Közepes: 2 GB-nál nagyobb és 10 GB-nál kisebb
* Nagy: 10 GB-nál nagyobb

Vegyük például az Azure Machine Learning Studio (klasszikus) környezetet:

* Az Azure Machine Learning Studio által támogatott adatformátumok és -típusok listáját az [Adatformátumok és a támogatott adattípusok](../studio/import-data.md#supported-data-formats-and-data-types) című szakaszban.
* Az elemzési folyamatban használt egyéb Azure-szolgáltatások korlátairól az [Azure előfizetési és szolgáltatási korlátok, kvóták és korlátozások](../../azure-resource-manager/management/azure-subscription-service-limits.md)című témakörben talál.

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Adatminőséggel kapcsolatos kérdések: feltárás és előfeldolgozás

### <a name="what-do-you-know-about-your-data"></a>Mit tud az adatairól?

Ismerje meg az adatok alapvető jellemzőit:

* Milyen mintákat vagy trendeket mutat
* Mi kiugró van
* Hány érték hiányzik

Ez a lépés fontos, hogy segítsen:

* Határozza meg, hogy mennyi előfeldolgozásra van szükség
* Olyan hipotézisek megfogalmazása, amelyek a legmegfelelőbb jellemzőkre vagy elemzéstípusokra utalnak
* További adatgyűjtési tervek kidolgozása

Az adatellenőrzés hasznos technikái közé tartoznak a leíró statisztikai számítások és a vizualizációs telkek. Az adatkészletek különböző Azure-környezetekben való megismeréséről [a Csapatadat-elemzési folyamat adatainak feltárása](explore-data.md)című témakörben talál részletesen.

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>Az adatok előfeldolgozást vagy tisztítást igényelnek?

Előfordulhat, hogy előkell dolgoznia és meg kell tisztítania az adatokat, mielőtt hatékonyan használhatja az adatkészletet a gépi tanuláshoz. A nyers adatok gyakran zajosak és megbízhatatlanok. Lehet, hogy hiányoznak az értékek. Az ilyen adatok modellezésre való felhasználása félrevezető eredményeket hozhat. A leírásért olvassa [el A továbbfejlesztett gépi tanulás adatainak előkészítése.](prepare-data.md)

## <a name="tools-and-languages-questions"></a>Eszközökkel és nyelvekkel kapcsolatos kérdések

A nyelvek, a fejlesztői környezetek és az eszközök számos lehetőséget kínálnak. Legyen tisztában az Ön igényeivel és preferenciáival.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Milyen nyelveket szeretne használni az elemzéshez?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Milyen eszközöket használjon az adatok elemzéséhez?

* [Microsoft Azure Powershell](/powershell/azure/overview) – az Azure-erőforrások parancsfájlnyelven történő felügyeletére használt parancsfájlnyelv
* [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
* [Forradalom Analitika](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio között](https://www.rstudio.com)
* [Python Tools for Visual Studio](https://aka.ms/ptvsdocs)
* [Anaconda](https://www.continuum.io/why-anaconda)
* [Jupyter-notebookok](https://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>A speciális elemzési forgatókönyv azonosítása

Miután megválaszolta az előző szakaszban szereplő kérdéseket, készen áll annak meghatározására, hogy melyik forgatókönyv felel meg leginkább az ön esetének. A mintaforgatókönyveket az [Azure Machine Learning speciális elemzéseinek forgatókönyvei ismertetik.](plan-sample-scenarios.md)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Mi az a csoportos adatelemzési folyamat (TDSP)?](overview.md)
