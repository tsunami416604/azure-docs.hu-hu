---
title: "Adatok feltárása és a képi megjelenítés eszközök - Azure |} Microsoft Docs"
description: "Adatok feltárása és a képi megjelenítés eszközök számára az adatok tudományos virtuális gép."
keywords: "adatok tudományos eszközök, adatok tudományos virtuális gép, adattudomány, linux adattudomány eszközei"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: gokuma;bradsev
ms.openlocfilehash: 96f8d1bcf5a7e51823ed55a1b924d1397eba9da5
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>Adatok feltárása és a képi megjelenítés eszközök az adatok tudományos virtuális gép

Adattudomány kulcsfontosságú lépés, hogy adatokat. A képi megjelenítés és adatok feltárása eszközök segítségével az adatok ismertetése érdekében. Feltéve a DSVM adott faciliate meg ezt a kulcsot. lépés: az alábbiakban néhány eszközök. 

## <a name="apache-drill"></a>Apache részletezés
|    |           |
| ------------- | ------------- |
| Mi ez?   | A Big data nyílt forráskódú SQL-lekérdezési motor    |
| Támogatott DSVM verziók      | Windows, Linux  |
| Hogyan van azt konfigurált / a DSVM telepítve?      |  A telepített `/dsvm/tools/drill*` csak beágyazott módban   |
| A gyakori felhasználási      |  Forrásgyűjteményben adatfeltárás anélkül, hogy az ETL. Különböző adatait adatforrásokat, és formázza a JSON, CSV, includign relációs táblák, Hadoop     |
| Hogyan használja az / futtatni?      | Asztali parancsikonjára  <br/> [Ismerkedés a részletezési 10 perc múlva](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| A DSVM a kapcsolódó eszközök      |   Rattle, Weka, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Mi ez?   |  Weka gépi tanulási algoritmusok adatok adatbányászati tevékenységek gyűjteménye. Az algoritmusok kell közvetlenül a DataSet adatkészlet alkalmazni, vagy saját Java kód hívása. Weka előtti adatfeldolgozási, besorolást, regressziós, fürtözés, társítási szabályok és a képi megjelenítés eszközöket tartalmazza. |
| Támogatott DSVM kiadás     | Windows, Linux     |
| A gyakori felhasználási      | Általános ML eszköz     |
| Hogyan használja az / futtatni?      | A Windowsban, keressen rá a Start menüben Weka. Linux, jelentkezzen be X2Go, majd keresse meg az alkalmazások fejlesztése -> -> Weka. |
| Minták mutató hivatkozások      | [Weka minták](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| A DSVM a kapcsolódó eszközök      |LightGBM, Rattle, Xgboost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Az adatbányászati az R nyelv használatát a grafikus felhasználói felület   |
| Támogatott DSVM kiadás     | Windows, Linux     |
| A gyakori felhasználási      | Az R általános UI-adatbányászat eszköz    |
| Hogyan használja az / futtatni?      | Felhasználói felületi eszköz. A Windows, egy parancssor elindításához, futtassa az R, majd futtassa R belül `rattle()`. Linux, X2Go kapcsolattartásnak, indítsa el a Terminálszolgáltatások, futtassa az R, majd futtassa R belül `rattle()`. |
| Minták mutató hivatkozások      | [Rattle](https://togaware.com/onepager/) |
| A DSVM a kapcsolódó eszközök      |LightGBM, Weka, Xgboost   |

## <a name="powerbi-desktop"></a>Power bi Desktop 
|    |           |
| ------------- | ------------- |
| Mi ez?   | Adatok interaktív képi megjelenítés és BI eszköz    |
| Támogatott DSVM verziók      | Windows  |
| A gyakori felhasználási      |  Adatok vizuális és irányítópultok felépítése   |
| Hogyan használja az / futtatni?      | Asztali parancsikonra (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| A DSVM a kapcsolódó eszközök      |   A Visual Studio 2017, a Visual Studio Code, Juno      |

