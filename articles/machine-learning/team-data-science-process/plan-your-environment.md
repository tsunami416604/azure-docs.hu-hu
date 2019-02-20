---
title: Forgatókönyvek meghatározhatja és megtervezheti az analytics-folyamat - csoportos adatelemzési folyamat |} Az Azure Machine Learning
description: Forgatókönyvek azonosítása és a bővített analitika adatfeldolgozása kulcs kérdéssor figyelembe vételével.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a8ca08d93992a6e1bfe8ae24b83354503154ef31
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416156"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Forgatókönyvek azonosítása és a bővített analitika adatfeldolgozása

Milyen erőforrásokra szüksége, hogy létrehoz egy környezetet, amely hajthat végre advanced analytics feldolgozási egy adatkészleten? Ez a cikk, amely segítségével azonosítható, a forgatókönyv feladatok és erőforrások a megfelelő kérdéseket tehet fel és sorozatát javasol.

A prediktív elemzési magas szintű lépéseinek sorrendjét kapcsolatos további információkért lásd: [Mi az a csoportos adatelemzési folyamat (TDSP)](overview.md). Minden lépés az adott forgatókönyvnek megfelelő feladatokat adott erőforrásokat igényel.

Válaszoljon a legfontosabb kérdésekre a szituáció azonosítása, a következő területeken:

* adatlogisztika
* adatjellemzők
* adatkészlet minősége
* előnyben részesített eszközök és nyelvek

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Logisztikai kérdések: adatok helyek és -mozgatás

A logisztikai kérdéseket a következőkre terjed ki:

* Adatforrás helye
* célhely az Azure-ban
* az adatok, többek között az ütemezést, az összeg és az erőforrások áthelyezése követelményei

Szükség lehet az elemzés során többször az adatok áthelyezéséhez. Gyakran előfordul, hogy helyi adatok áthelyezése a storage valamilyen rendszerbe, az Azure-ban és Machine Learning studióba.

### <a name="what-is-your-data-source"></a>Mi az az adatforrás?

Az adatait, helyi vagy a felhőben? Lehetséges helyek a következők:

* a nyilvánosan elérhető HTTP-címe
* egy helyi vagy hálózati helye
* SQL Server-adatbázis
* egy Azure storage-tároló

### <a name="what-is-the-azure-destination"></a>Mi az az Azure-beli cél?

Ha nem az adatok kell feldolgozása vagy modellezési? 

* Azure Blob Storage
* SQL Azure-adatbázisok
* Azure virtuális gépen futó SQL Server
* HDInsight (Hadoop az Azure-ban) vagy a Hive-táblák
* Azure Machine Learning
* Csatlakoztatható az Azure virtuális merevlemezek

### <a name="how-are-you-going-to-move-the-data"></a>Hogyan lesz a helyezi át az adatokat?

Az eljárások és erőforrások betöltését, vagy az adatok betöltése az számos különböző tárolási és feldolgozási környezetek lásd:

* [Adatok betöltése a tárolási környezetekbe elemzés céljából](ingest-data.md)
* [A betanítási adatok importálása az Azure Machine Learning studióba különböző adatforrásokból](../studio/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Az adatok áthelyezését rendszeres ütemezés szerint vagy módosítani a migrálás során nem kell?

Fontolja meg az Azure Data Factory (ADF) használatát, ha folyamatosan áttelepíteni kívánt adatokat. Az ADF hasznos lehet:

* egy hibrid forgatókönyvben, amely magában foglalja, mind a helyszíni és felhőbeli erőforrások
* egy olyan forgatókönyvet, ahol az adatok tranzakció, módosítani vagy üzleti logikát az áttelepítendő képviselőinkkel módosította

További információkért lásd: [adatok áthelyezése a helyszíni SQL Serverről az SQL Azure, az Azure Data Factoryvel](move-sql-azure-adf.md).

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Mekkora az adatokat, hogy az Azure-bA áthelyezni?

Rendkívül nagyméretű adatkészletek esetében előfordulhat, hogy bizonyos környezetekben tárolási kapacitásának túllépésére. Egy vonatkozó példáért tanulmányozza a méretbeli korlátokat a Machine Learning Studio a következő szakaszban. Ezekben az esetekben az elemzés során az adatok mintát használhat. Bemutatja, hogyan való egy adatkészletbe különböző Azure-alapú környezetekben a részletekért lásd: [mintát vesz az a csoportos adatelemzési folyamat](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Jellemzők kérdésekre: típusa, formátum és mérete

Ezek a kérdések kulcsfontosságúak a tárhely tervezésével és környezetek feldolgozása. Ezek segítséget, válassza ki a megfelelő forgatókönyv esetében az adatok típusát és a korlátozások ismertetése.

### <a name="what-are-the-data-types"></a>Mik azok az adattípusokat?

* Numerikus
* Kategorikus
* Sztringek
* Bináris

### <a name="how-is-your-data-formatted"></a>Hogyan van formázva az adatok?

* Vesszővel tagolt (CSV) vagy tabulátorral tagolt (TSV) egybesimított fájlok
* Tömörített és tömörítetlen
* Azure-blobok
* Hadoop Hive-táblák
* Az SQL Server-táblák

### <a name="how-large-is-your-data"></a>Mekkora az adatait?

* Kisméretű: 2 GB-nál kisebb
* Közepes: Nagyobb, mint 2 GB és 10 GB-nál kisebb
* Nagy méretű: 10 GB-nál nagyobb

Vegyük például az Azure Machine Learning Studio-környezetben:

* Az adatformátumok és az Azure Machine Learning Studio által támogatott típusok listáját lásd: [adatformátumok és a támogatott adattípusokat](../studio/import-data.md#supported-data-formats-and-data-types) szakaszban.
* Információk az egyéb Azure-szolgáltatásokat használja az elemzés során vonatkozó korlátozások: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Minőségi kérdésekre: adatáttekintési és előzetes feldolgozása

### <a name="what-do-you-know-about-your-data"></a>Mit tud az adataival kapcsolatban?

Megismerheti az adatokkal kapcsolatos alapvető jellemzői:

* Mi mintáit, és a trendek, kiváló
* Milyen kiugró értékek rendelkezik
* Hány értékek hiányoznak.

Ebben a lépésben fontos segítséget:

* Határozza meg, mennyi előfeldolgozásához van szükség.
* Állítson össze, amelyek a leginkább megfelelő szolgáltatásokat és elemzési típusú feltételezéseket
* További adatok gyűjtési terveket dolgoznak ki

Adatok vizsgálatra hasznos technikái a következők leíró statisztika számítási és vizualizációs látható. Bemutatja, hogyan fedezheti fel egy adatkészletbe különböző Azure-alapú környezetekben a részletekért lásd: [a csoportos adatelemzési folyamat az adatok megismerése](explore-data.md).

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>Igényelnek-e az adatok előfeldolgozása vagy tisztítás?

Szüksége lehet az adatok előfeldolgozása és a gépi tanulás az adatkészlet is hatékonyan használata előtt. Nyers adatok legtöbbször zajos és nem megbízható. Előfordulhat, hogy azt hiányzó értékeket. Az ilyen adatok használata a modellezési félrevezető eredményeket hozhat létre. Ismertetését lásd: [feladatok készíti elő az adatok bővített gépi tanulás](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Eszközök és nyelvek kérdések

Számos módon, fejlesztői környezetek és eszközök számára. Vegye figyelembe az igények és beállítások.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Milyen nyelveket szeretné használni az elemzésre?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Milyen eszközöket használja az adatok elemzésére?

* [A Microsoft Azure Powershell](/powershell/azure/overview) -parancsfájl nyelvének az Azure-erőforrások felügyeletére szolgáló parancsfájl nyelvének
* [Az Azure Machine Learning Studióban](../studio/what-is-ml-studio.md)
* [A Revolution Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
* [Az RStudio](http://www.rstudio.com)
* [Python Tools for Visual Studio](https://aka.ms/ptvsdocs)
* [Anaconda](https://www.continuum.io/why-anaconda)
* [Jupyter-notebookok](http://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>A fejlett analitikai szituáció azonosítása

Miután rendelkezik kérdéseire az előző szakaszban, készen áll határozza meg, melyik ajánlott módszer az esetet. A mintaként használható jelen forgatókönyvek leírt [az Azure Machine Learning speciális elemzési forgatókönyvek](plan-sample-scenarios.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Mi az a csoportos adatelemzési folyamat (TDSP)?](overview.md)