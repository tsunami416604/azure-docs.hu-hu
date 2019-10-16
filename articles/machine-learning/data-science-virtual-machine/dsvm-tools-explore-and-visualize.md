---
title: Adatáttekintési és vizualizációs eszközök
titleSuffix: Azure Data Science Virtual Machine
description: Adatfelderítési és vizualizációs eszközök a Data Science Virtual Machinehoz.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: a18c374df0cad7994af855089f1096d52fe30ce2
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330701"
---
# <a name="data-exploration-and-visualization-tools-on-the-azure-data-science-virtual-machine"></a>Adatfelderítési és vizualizációs eszközök az Azure Data Science Virtual Machine

Az adatelemzés során a legfontosabb az, hogy megértse az adatelemzést. A vizualizációk és az adatfeltárási eszközök segítenek felgyorsítani az adatmegismerést. A következő, a Data Science Virtual Machine (DSVM) szolgáltatásban elérhető eszközök megkönnyítik ezt a kulcsot.

## <a name="apache-drill"></a>Apache-részletezés
|    |           |
| ------------- | ------------- |
| Mi ez?   | Nyílt forráskódú SQL-lekérdezési motor big data    |
| Támogatott DSVM-verziók      | Windows, Linux  |
| Hogyan van konfigurálva és telepítve a DSVM?      |  Csak beágyazott módban van telepítve a `/dsvm/tools/drill*` verzióban   |
| Jellemző felhasználások      |  Helyben történő adatfeltáráshoz kinyerési, átalakítási, betöltési (ETL) adatok nélkül. Különböző adatforrások és-formátumok lekérdezése, beleértve a CSV-t, a JSON-t, a rokon táblákat és a Hadoop.     |
| Használat és Futtatás      | Asztali parancsikon  <br/> [Ismerkedés a részletezéssel 10 percen belül](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| A DSVM kapcsolódó eszközök      |   Csörgő, WEKA, SQL Server Management Studio      |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| Mi ez?   | Interaktív adatvizualizáció és BI-eszköz    |
| Támogatott DSVM-verziók      | Windows  |
| Jellemző felhasználások      |  Adatvizualizációk és az irányítópultok fejlesztése   |
| Használat és Futtatás      | Asztali parancsikon (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`), vagy egyszerűen csak a **Start** menüből futtatható.      |
| A DSVM kapcsolódó eszközök      |   Visual Studio 2019, Visual Studio Code, Juno      |

> [!NOTE]
> Power BI eléréséhez Microsoft Office 365-fiókra van szükség.


## <a name="rattle"></a>Csörgő
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Grafikus felhasználói felület (GUI) az R-t használó adatbányászathoz   |
| Támogatott DSVM-kiadások     | Windows, Linux     |
| Jellemző felhasználások      | Általános felhasználói felületi adatbányászati eszköz az R-hez    |
| Használat és Futtatás      | Felhasználói felület eszköz. Windows rendszeren nyisson meg egy parancssort, futtassa az R-t, majd az R-ben futtassa a `rattle()` parancsot. Linux rendszeren kapcsolódjon a X2Go, indítson el egy terminált, futtassa az R-t, majd az R-ben futtassa `rattle()` parancsot. |
| Mintákra mutató hivatkozások      | [Csörgő](https://togaware.com/onepager/) |
| A DSVM kapcsolódó eszközök      |LightGBM, WEKA, Xgboost   |


## <a name="weka"></a>WEKA
|    |           |
| ------------- | ------------- |
| Mi ez?   |  Gépi tanulási algoritmusok gyűjteménye adatbányászati feladatokhoz. Ezeket az algoritmusokat közvetlenül egy adatkészletre lehet alkalmazni, vagy a saját Java-kódjából hívható meg. A WEKA az adatfeldolgozás, a besorolás, a regresszió, a fürtözés, a társítási szabályok és a vizualizáció eszközeit tartalmazza. |
| Támogatott DSVM-kiadások     | Windows, Linux     |
| Jellemző felhasználások      | Általános gépi tanulási eszköz     |
| Használat és Futtatás      | Windows rendszeren keresse meg a WEKA a Start menüben. Linux rendszeren jelentkezzen be a X2Go-be, majd lépjen az alkalmazások > fejlesztési > WEKA. |
| Mintákra mutató hivatkozások      | [WEKA-minták](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| A DSVM kapcsolódó eszközök      |LightGBM, csörgő, Xgboost   |




