---
title: Forgatókönyvek meghatározhatja és megtervezheti az analytics-folyamat – Azure |} A Microsoft Docs
description: Legfontosabb kérdések figyelembe vételével tervezze meg a fejlett analitikai.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: b61f6c12aaa94fc61063e8d3bd7e339f1548781c
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446418"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Forgatókönyvek azonosítása és a bővített analitika adatfeldolgozása
Milyen erőforrásokat kell azt tervezi, hogy tartalmazzák az ehhez a feldolgozása egy adatkészleten, speciális analitikai környezet beállítása során? Ez a cikk javasol egy sorozatát kérdéseket tehet fel, amelyek segítenek a feladatok és a kapcsolódó erőforrások a szituáció azonosítása. A prediktív elemzési magas szintű lépéseinek sorrendjét szemlélteti az [Mi az a csoportos adatelemzési folyamat (TDSP)?](overview.md). Egyes lépéseket az adott forgatókönyvnek megfelelő feladatokat bizonyos erőforrásokra van szükség. A szituáció azonosítása a legfontosabb kérdések adatlogisztika, jellemzőit, az adatkészleteket és az eszközöket és nyelveket szeretne végezni az elemzés minőségét vonatkoznak.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Logisztikai kérdések: adatok helyek és -mozgatás
A logisztikai kérdéseket érintő helyét a **adatforrás**, a **célhely** Azure, és az adatok áthelyezését a követelményei, például az ütemezést, az összeg és az erőforrások. Az elemzés során többször helyezhető szükség lehet az adatokat. Gyakran előfordul, hogy helyi adatok áthelyezése a storage valamilyen rendszerbe, az Azure-ban és Machine Learning studióba.

1. **Mi az az adatforrás?** Ennyi az egész helyi vagy a felhőben? Példa:
   
   * Az adatok a HTTP-cím nyilvánosan elérhető.
   * Az adatok egy helyi vagy hálózati fájl helyen található.
   * Az adatok a SQL Server-adatbázisban.
   * Az adatok egy Azure storage-tároló tárolva van
2. **Mi az az Azure-beli cél?** Hol kell történő feldolgozása, illetve a modellezési? Példa:
   
   * Azure Blob Storage
   * SQL Azure-adatbázisok
   * Azure virtuális gépen futó SQL Server
   * HDInsight (Hadoop az Azure-ban) vagy a Hive-táblák
   * Azure Machine Learning
   * Csatlakoztatható az Azure virtuális merevlemezeket.
3. **Hogyan lesz a helyezi át az adatokat?** Az eljárások és a betöltési, vagy az adatok betöltése az számos különböző tárolási és feldolgozási környezetek számára elérhető erőforrások az alábbi cikkekben ismertetett:
   
   * [Adatok betöltése a tárolási környezetekbe elemzés céljából](ingest-data.md)
   * [A betanítási adatok importálása az Azure Machine Learning studióba különböző adatforrásokból származó adatokat](../studio/import-data.md).
4. **Az adatok áthelyezését rendszeres ütemezés szerint vagy módosítani a migrálás során nem kell?** Érdemes megfontolni az Azure Data Factory (ADF) használatát, amikor adatokat kell folyamatosan áttelepíteni, különösen akkor, ha egy hibrid forgatókönyvben, amely hozzáfér a helyszíni és felhőbeli erőforrások van szó, vagy amikor az adatok tranzakció van, vagy módosítani, illetve üzleti logikát hozzáadni kell Ez a migrált képviselőinkkel. További információkért lásd: [adatok áthelyezése a helyszíni SQL Serverről az SQL Azure, az Azure Data Factoryvel](move-sql-azure-adf.md)
5. **Mekkora az adatokat, hogy az Azure-bA áthelyezni?** A nagyon nagy méretű adatkészletek előfordulhat, hogy bizonyos környezetekben tárolási kapacitásának túllépésére. Egy vonatkozó példáért tanulmányozza a méretbeli korlátokat a Machine Learning Studio a következő szakaszban. Ezekben az esetekben az elemzés során egy minta az adatok használhatók. Bemutatja, hogyan való egy adatkészletbe különböző Azure-alapú környezetekben a részletekért lásd: [mintát vesz az a csoportos adatelemzési folyamat](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Jellemzők kérdésekre: típusa, formátum és mérete
Ezek a kérdések kulcsfontosságúak a tárhely tervezésével és feldolgozási környezetekben, amelyek mindegyike a különböző típusú adatok megfelelőek és, amelyek mindegyike rendelkezik, bizonyos korlátozásokat.

1. **Mik azok az adattípusokat?** Példa:
   
   * Numerikus
   * Kategorikus
   * Sztringek
   * Bináris
2. **Hogyan van formázva az adatok?** Példa:
   
   * Vesszővel tagolt (CSV) vagy tabulátorral tagolt (TSV) egybesimított fájlok
   * Tömörített és tömörítetlen
   * Azure-blobok
   * Hadoop Hive-táblák
   * Az SQL Server-táblák
3. **Mekkora az adatait?**
   
   * Kisméretű: kevesebb mint 2 GB
   * Közepes: Nagyobb, mint 2 GB és 10 GB-nál kisebb
   * Nagy: 10 GB-nál nagyobb

Vegyük például az Azure Machine Learning Studio-környezetben:

* Az adatformátumok és az Azure Machine Learning Studio által támogatott típusok listáját lásd: [adatformátumok és a támogatott adattípusokat](../studio/import-data.md#data-formats-and-data-types-supported) szakaszban.
* Az adatok Azure Machine Learning Studio korlátozásai információkért lásd: a **mekkora az adatkészlet lehet saját modulok?** szakaszában [importálása és exportálása az adatokat a Machine Learning](../studio/faq.md#machine-learning-studio-questions)

Információk az egyéb Azure-szolgáltatásokat használja az elemzés során vonatkozó korlátozások: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Minőségi kérdésekre: adatáttekintési és előzetes feldolgozása
1. **Mit tud az adataival kapcsolatban?** Fedezze fel az adatokat egy megértése alapvető jellemzőit, így. Milyen mintázatokat vagy trendeket azonban, milyen rendelkezik kiugró adatokat vagy hány érték hiányzik. Ebben a lépésben fontos a szükség esetén, amelyek a leginkább megfelelő funkciók vagy írja be az elemzési feltételezéseket kialakításában, valamint a további adatok gyűjtési terveket kidolgozásában előfeldolgozásához mértékét. Leíró statisztikák kiszámításához és Vizualizációk olyan hasznos technikákat adatok várólistájához. Bemutatja, hogyan fedezheti fel egy adatkészletbe különböző Azure-alapú környezetekben a részletekért lásd: [a csoportos adatelemzési folyamat az adatok megismerése](explore-data.md).
2. **Igényelnek-e az adatokat előzetesen feldolgozni, vagy tisztítás?**
   Előzetesen feldolgozni, és adattisztítást is a fontos feladatok, amelyek általában kell elvégezni, mielőtt adatkészlet használható hatékonyan a machine Learning szolgáltatáshoz. Nyers adatokat gyakran zajos és nem megbízható, és esetleg hiányzik a értékeket. Az ilyen adatok használata a modellezési félrevezető eredményeket hozhat létre. Ismertetését lásd: [feladatok készíti elő az adatok bővített gépi tanulás](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Eszközök és nyelvek kérdések
Sokféle lehetőség érhető el attól függően, milyen nyelven és fejlesztési környezetek vagy eszközök van szüksége, vagy a legtöbb conformable használatával.

1. **Milyen nyelveket szeretné használni az elemzésre?**  
   
   * R
   * Python
   * SQL
2. **Milyen eszközöket használja az adatok elemzésére?**
   
   * [A Microsoft Azure Powershell](/powershell/azure/overview) -parancsfájl nyelvének az Azure-erőforrások felügyeletére szolgáló parancsfájl nyelvének.
   * [Az Azure Machine Learning Studióban](../studio/what-is-ml-studio.md)
   * [A Revolution Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
   * [Az RStudio](http://www.rstudio.com)
   * [Python Tools for Visual Studio](https://aka.ms/ptvsdocs)
   * [Anaconda](https://www.continuum.io/why-anaconda)
   * [Jupyter-notebookok](http://jupyter.org/)
   * [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>A fejlett analitikai szituáció azonosítása
Ha megválaszolta az előző szakaszban szereplő kérdések, készen áll határozza meg, melyik ajánlott módszer az eset. A mintaként használható jelen forgatókönyvek leírt [az Azure Machine Learning speciális elemzési forgatókönyvek](plan-sample-scenarios.md).

