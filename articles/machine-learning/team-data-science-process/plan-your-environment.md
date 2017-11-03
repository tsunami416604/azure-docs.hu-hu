---
title: "Forgatókönyvek meghatározhatja és megtervezheti az elemzés során - Azure |} Microsoft Docs"
description: "Tervezze meg a speciális elemzés fő kérdések sorát teszi figyelembe véve."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 421520dd-7728-4d29-889c-ebe6a0a6fb07
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: 42d1de8a27f8979db5662094e8bd74a6f5ccac55
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Forgatókönyvek azonosítása és a bővített analitika adatfeldolgozása
Milyen erőforrásokat kell tervezett egy környezet beállításakor ehhez a dataset feldolgozási speciális elemzésekre? Ez a cikk megválaszolandó kérdések azonosításához a feladatok és a kapcsolódó erőforrások adott esetben több javasol. A prediktív elemzés magas szintű lépések sorrendjének szemlélteti az [Mi az a csoport adatok tudományos folyamat (TDSP)?](overview.md). Egy adott erőforráshoz egyes lépéseket igényel az adott forgatókönyvhöz kapcsolódó feladatok. A forgatókönyv azonosításához fontos kérdések adatok logisztikai, jellemzők, ennek az adatkészletek és az eszközök, inkább az elemzést nyelvek minőségének vonatkoznak.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Logisztikai kérdések: adatok helye és a mozgás
A logisztikai kérdéseket vonatkoznak a helyét a **adatforrás**, a **célhelyre** Azure-ban, és helyezze át az adatokat, beleértve az ütemezés követelményei mennyiségét és erőforrások szerepet játszanak. Az elemzés során többször áthelyezni az adatokat kell. Egy gyakori forgatókönyv, hogy a helyi adatok tárolási néhány formába Azure és a Machine Learning Studióhoz.

1. **Mi az az adatforrást?** Ennyi az egész helyi vagy a felhőben? Példa:
   
   * Az adatok nyilvánosan elérhető HTTP-cím.
   * Az adatok egy helyi vagy hálózati fájl helyen található.
   * Az adatokat az SQL Server-adatbázisban.
   * Az adatok tárolását az Azure storage-tárolóban
2. **Mi az az Azure cél?** Ha nem azt kell feldolgozása vagy modellezési? Példa:
   
   * Azure Blob Storage
   * SQL Azure-adatbázisok
   * Azure virtuális gépen futó SQL Server
   * HDInsight (Hadoop az Azure-on) vagy a Hive táblák
   * Azure Machine Learning
   * Csatlakoztatható Azure virtuális merevlemezeket.
3. **Hogyan lesz a helyezi át az adatokat?** Az eljárások és a betöltési vagy az adatok betöltése az számos eltérőek a tárolási és feldolgozási környezetek számára elérhető erőforrások az alábbi témakörökben szereplő eljárásokat.
   
   * [Adatok betöltése az elemzés a tárolási környezetekben](ingest-data.md)
   * [A betanítási adatok importálása az Azure Machine Learning Studio a különféle adatforrásokból származó](../studio/import-data.md).
4. **Az adatok rendszeres időközönként át vagy az áttelepítés során módosíthat nem kell?** Érdemes lehet használni az Azure Data Factory (ADF), ha adatokat folyamatosan telepíti át, különösen akkor, ha egy hibrid forgatókönyvben, amely hozzáfér a helyszíni és felhőalapú erőforrások van szó, vagy ha az adatok tranzakcióalapú van vagy módosítható, vagy az üzleti logika áttelepítendő során hozzá kell. További információkért lásd: [tárolt adatok mozgatása egy helyi SQL server az SQL Azure-bA az Azure Data Factoryvel](move-sql-azure-adf.md)
5. **Az adatok mekkora kell helyezni az Azure?** Nagyon nagy adatkészletek haladhatja meg a tárolási kapacitást az egyes környezetekben. Példa tanulmányozza a méretkorlátozásokról a Machine Learning Studio a következő szakaszban. Ebben az esetben az adatok minta az elemzés során is használható. Lefelé-minta egy adatkészlet különböző Azure-alapú környezetekben a részletekért lásd: [az adatokat az adatok tudományos folyamatban](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Adatok jellemzőit kérdések: típusa, formátum és mérete
Ezeket a kérdéseket billentyű lenyomásával, a tárhelyek tervezésével és feldolgozási környezetekben, amelyek mindegyike különböző típusú adatok megfelelő, és amelyek bizonyos korlátozások.

1. **Mik az adattípusok?** Példa:
   
   * Numerikus
   * Kategorikus
   * Karakterláncok
   * Bináris
2. **Az adatok formázását?** Példa:
   
   * Vesszővel tagolt (CSV) vagy (TSV) egybesimított fájlokba tabulátorral tagolt
   * Tömörített és tömörítetlen
   * Azure-blobokat
   * Hadoop Hive táblák
   * SQL Server-táblákra
3. **Mekkora az adatokat?**
   
   * Kis: 2GB-nál kisebb
   * Közepes: Nagyobb, mint 2GB és 10GB-nál kisebb
   * Nagy: 10GB-nál nagyobb

Hajtsa végre az Azure Machine Learning Studio környezet például:

* Az adatok formátumú és az Azure Machine Learning Studio által támogatott eszköztípusok listájáért lásd: [adatok formátumok és a támogatott adattípusok](../studio/import-data.md#data-formats-and-data-types-supported) szakasz.
* Az Azure Machine Learning Studio adatok korlátozásai információkért lásd: a **méretét az adatkészlet lehet a modulok?** szakasza [importálása és exportálása az adatok a Machine Learning szolgáltatáshoz](../studio/faq.md#machine-learning-studio-questions)

Egyéb Azure-szolgáltatásokhoz használja az elemzés során korlátai információkért lásd: [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Minőségi kérdésekre: feltárására és előzetes feldolgozás
1. **Mi ismeri az adatokkal kapcsolatban?** Áttekintheti az adatokat, ha van szüksége ahhoz, hogy egy megértése alapvető jellemzőit. Hány értékek hiányoznak, vagy mi mintákra vagy a trendeket, exhibits, mi kiugró tartozik. Ez a lépés fontos előzetes feldolgozás szükség esetén, amelyek a legmegfelelőbb szolgáltatások javaslat vagy írja be a elemzési feltételezéseket vonatkozókat, és vonatkozókat kapcsolatos további adatok gyűjtése a mértékét. Leíró statisztika kiszámításához és képi megjelenítéseket olyan hasznos technikákat adatok végzett vizsgálat végrehajtásához. Bemutatja, hogyan megismerkedhet a különböző Azure-környezetek dataset részletekért lásd: [adatokba az adatok tudományos folyamatban](explore-data.md).
2. **Igényelnek-e az adatok előzetes feldolgozás, vagy tisztítás?**
   Előzetesen feldolgozni, és az adatok tisztítása, amelyek általában kell elvégzése előtt a DataSet adatkészlet nem használható hatékonyan gépi tanulás fontos feladatokat. Nyers adatok gyakran zajos vagy nem megbízható, és előfordulhat, hogy értékek hiányzik. Ilyen adatok használata a modellezési félrevezető eredményeket hozhat létre. Ismertetését lásd: [készíti elő az adatok továbbfejlesztett feladatok gépi tanulás](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Eszközök és nyelvek vonatkozó kérdések
Az számos beállítások itt attól függően, hogy milyen nyelveket és fejlesztői környezetek vagy eszközök kell, vagy a conformable használja.

1. **Milyen nyelven szeretné a elemzések?**  
   
   * R
   * Python
   * SQL
2. **Milyen eszközöket használja az adatok elemzésére?**
   
   * [A Microsoft Azure Powershell](/powershell/azure/overview) -egy parancsfájl nyelvét, a parancsfájl nyelvének az Azure-erőforrások felügyeletéhez.
   * [Az Azure Machine Learning Studióban](../studio/what-is-ml-studio.md)
   * [Fordulat elemzés](http://www.revolutionanalytics.com/revolution-r-open)
   * [Rstudióból](http://www.rstudio.com)
   * [Python Tools for Visual Studio](http://microsoft.github.io/PTVS/)
   * [Anaconda](https://www.continuum.io/why-anaconda)
   * [Jupyter notebookok](http://jupyter.org/)
   * [Microsoft Power BI](http://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>A speciális elemzés forgatókönyv azonosítása
Ha rendelkezik kérdéseire az előző szakaszban, készen áll határozza meg, melyik a legjobb forgatókönyv megfelel az Ön esetében. A szituáció vázolt [forgatókönyvek az Azure Machine Learning speciális elemzésekre](plan-sample-scenarios.md).

