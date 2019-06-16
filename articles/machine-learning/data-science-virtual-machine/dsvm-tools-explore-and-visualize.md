---
title: Adatáttekintési és vizualizációs eszközök – Azure |} A Microsoft Docs
description: Adatáttekintési és vizualizációs eszközök esetében az adatelemző virtuális gép.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 693be80e493a0ba259d147f432dc9d6c07ba876d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427515"
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>Adatáttekintési és vizualizációs eszközök a Data Science virtuális gépen

Egy kulcs az adatelemzés lépéseként megérteni az adatokat. Megjelenítési és adatvizsgálati eszközök segítségével felgyorsíthatja az adatok megértését. Íme néhány a dsvm-hez a biztosított eszközök, amelyek megkönnyítik a legfontosabb ebben a lépésben. 

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Mi ez?   | Nyílt forráskódú SQL lekérdezési motorja a Big data    |
| A DSVM támogatott verziói      | Windows, Linux  |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?      |  A telepített `/dsvm/tools/drill*` csak beágyazott módban   |
| Gyakori használati      |  Forrásgyűjteményben adatfeltárás anélkül, hogy az ETL. Különböző adatforrások és formátumokhoz, például a fürt megosztott kötetei szolgáltatás, JSON, relációs tábláival, Hadoop lekérdezése     |
| Hogyan futtathatja / használata?      | Asztali parancsikon  <br/> [Ismerkedés a részletezés 10 perc múlva](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| A dsvm-hez kapcsolódó eszközök      |   Rattle, Weka, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Mi ez?   |  Weka adatbányászati feladatokhoz gépi tanulási algoritmusok gyűjteménye. Az algoritmusok közvetlenül egy adatkészlet a alkalmazni, vagy saját Java kódot meghívva. Weka előtti adatfeldolgozás, besorolás, regressziós, fürtözés, társítási szabályok és megjelenítési eszközöket tartalmaz. |
| Támogatott DSVM-kiadások     | Windows, Linux     |
| Gyakori használati      | Általános gépi Tanulási eszköz     |
| Hogyan futtathatja / használata?      | A Windows, a Start menü Weka keresése. Linuxon, jelentkezzen be X2Go, majd keresse meg az alkalmazások -> fejlesztés Weka ->. |
| A minták mutató hivatkozások      | [Weka minták](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| A dsvm-hez kapcsolódó eszközök      |LightGBM, Rattle, Xgboost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Egy grafikus felhasználói felületet Data Mining az R használata   |
| Támogatott DSVM-kiadások     | Windows, Linux     |
| Gyakori használati      | Általános felhasználói felület Data szintű adatbányászatra eszköz az R    |
| Hogyan futtathatja / használata?      | UI-eszköz. A Windows, indítson el egy parancssort, és futtassa az R, majd futtassa az R belül `rattle()`. Linux rendszeren X2Go összekapcsolása, indítsa el a parancsot egy terminálban, R, majd futtassa az R belül futtassa `rattle()`. |
| A minták mutató hivatkozások      | [Rattle](https://togaware.com/onepager/) |
| A dsvm-hez kapcsolódó eszközök      |LightGBM, Weka, Xgboost   |

## <a name="power-bi-desktop"></a>A Power BI Desktopban 
|    |           |
| ------------- | ------------- |
| Mi ez?   | Interaktív adatábrázolás és Üzletiintelligencia-eszköze    |
| A DSVM támogatott verziói      | Windows  |
| Gyakori használati      |  Adatvizualizációk és -irányítópultokat készít   |
| Hogyan futtathatja / használata?      | Asztali parancsikon (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| A dsvm-hez kapcsolódó eszközök      |   Visual Studio 2019, Visual Studio Code, Juno      |

