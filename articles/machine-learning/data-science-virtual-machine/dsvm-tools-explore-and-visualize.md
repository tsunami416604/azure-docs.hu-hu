---
title: Adatáttekintési és vizualizációs eszközök
titleSuffix: Azure Data Science Virtual Machine
description: Adatfelderítési és vizualizációs eszközök a Data Science Virtual Machinehoz.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: 14c34a13440d50fcf42c2207f9933dc1de014a14
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70191964"
---
# <a name="data-exploration-and-visualization-tools-on-the-azure-data-science-virtual-machine"></a>Adatfelderítési és vizualizációs eszközök az Azure Data Science Virtual Machine

Az adatelemzés során a legfontosabb az, hogy megértse az adatelemzést. A vizualizációk és az adatfeltárási eszközök segítenek felgyorsítani az adatmegismerést. A következő, a Data Science Virtual Machine (DSVM) szolgáltatásban elérhető eszközök megkönnyítik ezt a kulcsot.

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Mi ez?   | Nyílt forráskódú SQL-lekérdezési motor big data    |
| Támogatott DSVM-verziók      | Windows, Linux  |
| Hogyan van konfigurálva és telepítve a DSVM?      |  `/dsvm/tools/drill*` Csak beágyazott módban van telepítve   |
| Gyakori használati      |  Helyben történő adatfeltáráshoz kinyerési, átalakítási, betöltési (ETL) adatok nélkül. Különböző adatforrások és-formátumok lekérdezése, beleértve a CSV-t, a JSON-t, a rokon táblákat és a Hadoop.     |
| Használat és Futtatás      | Asztali parancsikon  <br/> [Ismerkedés a részletezéssel 10 percen belül](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| A DSVM kapcsolódó eszközök      |   Rattle, Weka, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Mi ez?   |  Gépi tanulási algoritmusok gyűjteménye adatbányászati feladatokhoz. Ezeket az algoritmusokat közvetlenül egy adatkészletre lehet alkalmazni, vagy a saját Java-kódjából hívható meg. A WEKA az adatfeldolgozás, a besorolás, a regresszió, a fürtözés, a társítási szabályok és a vizualizáció eszközeit tartalmazza. |
| Támogatott DSVM-kiadások     | Windows, Linux     |
| Gyakori használati      | Általános gépi tanulási eszköz     |
| Használat és Futtatás      | A Windows, Weka keresése a Start menüben. Linux rendszeren jelentkezzen be a X2Go-be, majd lépjen az alkalmazások > fejlesztési > WEKA. |
| A minták mutató hivatkozások      | [Weka minták](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| A DSVM kapcsolódó eszközök      |LightGBM, csörgő, Xgboost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Grafikus felhasználói felület (GUI) az R-t használó adatbányászathoz   |
| Támogatott DSVM-kiadások     | Windows, Linux     |
| Gyakori használati      | Általános felhasználói felületi adatbányászati eszköz az R-hez    |
| Használat és Futtatás      | UI-eszköz. Windows rendszeren nyisson meg egy parancssort, futtassa az R-t, majd az R `rattle()`-ben futtassa a parancsot. Linux rendszeren kapcsolódjon a X2Go, indítson el egy terminált, futtassa az R parancsot, majd `rattle()`az r-ben futtassa a parancsot. |
| A minták mutató hivatkozások      | [Rattle](https://togaware.com/onepager/) |
| A DSVM kapcsolódó eszközök      |LightGBM, Weka, Xgboost   |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| Mi ez?   | Interaktív adatvizualizáció és BI-eszköz    |
| Támogatott DSVM-verziók      | Windows  |
| Gyakori használati      |  Adatvizualizációk és az irányítópultok fejlesztése   |
| Használat és Futtatás      | Asztali parancsikon (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| A DSVM kapcsolódó eszközök      |   Visual Studio 2019, Visual Studio Code, Juno      |

