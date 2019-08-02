---
title: Adatfelderítési és vizualizációs eszközök – Azure | Microsoft Docs
description: Adatfelderítési és vizualizációs eszközök a Data Science Virtual Machinehoz.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: vijetaj
ms.openlocfilehash: 31b05ec4fa68c3d4804000caee94b62432bdaed9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68557757"
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>Adatfelderítési és vizualizációs eszközök a Data Science Virtual Machine

Az adatelemzés egyik kulcsfontosságú lépése az, hogy megértse az adatelemzést. A vizualizációk és az adatfeltárási eszközök segítenek felgyorsítani az adatmegismerést. Itt talál néhány olyan eszközt a DSVM, amelyek megkönnyítik ezt a kulcsfontosságú lépést. 

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Mi ez?   | Nyílt forráskódú SQL-lekérdezési motor a Big-adatmennyiségen    |
| A DSVM támogatott verziói      | Windows, Linux  |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?      |  `/dsvm/tools/drill*` Csak beágyazott módban van telepítve   |
| Gyakori használati      |  Helyszíni adatelemzés az ETL megkövetelése nélkül. Különböző adatforrások és formátumok lekérdezése, beleértve a CSV-t, a JSON-t, a Hadoop     |
| Hogyan futtathatja / használata?      | Asztali parancsikon  <br/> [Ismerkedés a részletezéssel 10 percen belül](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| A dsvm-hez kapcsolódó eszközök      |   Rattle, Weka, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Mi ez?   |  A WEKA a gépi tanulási algoritmusok gyűjteménye az adatbányászati feladatokhoz. Az algoritmusokat közvetlenül egy adatkészletre lehet alkalmazni, vagy a saját Java-kódjából hívható meg. Weka előtti adatfeldolgozás, besorolás, regressziós, fürtözés, társítási szabályok és megjelenítési eszközöket tartalmaz. |
| Támogatott DSVM-kiadások     | Windows, Linux     |
| Gyakori használati      | Általános ML eszköz     |
| Hogyan futtathatja / használata?      | Windows rendszeren keresse meg a WEKA a Start menüben. Linux rendszeren jelentkezzen be a X2Go, majd navigáljon az Applications-> Development-> WEKA. |
| A minták mutató hivatkozások      | [Weka minták](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| A dsvm-hez kapcsolódó eszközök      |LightGBM, csörgő, Xgboost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Grafikus felhasználói felület az R használatával történő adatbányászathoz   |
| Támogatott DSVM-kiadások     | Windows, Linux     |
| Gyakori használati      | Általános felhasználói felületi adatbányászati eszköz az R-hez    |
| Hogyan futtathatja / használata?      | UI-eszköz. Windows rendszeren indítson el egy parancssort, futtassa az R-t, `rattle()`majd az r Run oldalon. Linux rendszeren kapcsolódjon a X2Go-hez, indítson el egy terminált, futtassa az `rattle()`r parancsot, majd az r Run oldalon. |
| A minták mutató hivatkozások      | [Rattle](https://togaware.com/onepager/) |
| A dsvm-hez kapcsolódó eszközök      |LightGBM, Weka, Xgboost   |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| Mi ez?   | Interaktív adatábrázolás és Üzletiintelligencia-eszköze    |
| A DSVM támogatott verziói      | Windows  |
| Gyakori használati      |  Adatvizualizációk és -irányítópultokat készít   |
| Hogyan futtathatja / használata?      | Asztali parancsikon (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| A dsvm-hez kapcsolódó eszközök      |   Visual Studio 2019, Visual Studio Code, Juno      |

