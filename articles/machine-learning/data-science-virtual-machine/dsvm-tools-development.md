---
title: Fejlesztési eszközök
titleSuffix: Azure Data Science Virtual Machine
description: Ismerje meg a Data Science Virtual Machine elérhető eszközöket és integrált fejlesztői környezeteket.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/3/2019
ms.openlocfilehash: 76a550e95de24bf65b9b6097dd332e535da5b1c4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330715"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Fejlesztői eszközök az Azure Data Science Virtual Machine

A Data Science Virtual Machine (DSVM) számos népszerű eszközt csomagol egy rendkívül hatékony integrált fejlesztési környezetbe (IDE). Íme néhány olyan eszköz, amely a DSVM van megadva.

## <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

|    |           |
| ------------- | ------------- |
| Mi ez?   | Általános célú IDE      |
| Támogatott DSVM-verziók      | Windows      |
| Jellemző felhasználások      | Szoftverfejlesztés    |
| Hogyan van konfigurálva és telepítve a DSVM?      | Adatelemzési munkaterhelés (Python és R-eszközök), Azure munkaterhelés (Hadoop, Data Lake), Node. js, SQL Server eszközök, [Azure Machine learning a Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai) -hoz    |
| Használat és Futtatás      | Asztali parancsikon (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`). Grafikusan nyissa meg a Visual studiót az asztal ikon vagy a **Start** menü használatával. Keressen programokat (Windows billentyű + S), majd a **Visual studiót**. Innen olyan nyelveket hozhat létre, mint C#a, a Python, az R és a Node. js.   |
| A DSVM kapcsolódó eszközök      |     Visual Studio Code, RStudio, Juno  |

> [!NOTE]
> Előfordulhat, hogy a próbaidőszak lejárta után üzenetet kap. Adja meg a Microsoft-fiók hitelesítő adatait. Vagy hozzon létre egy új ingyenes fiókot a Visual Studio Community eléréséhez.

## <a name="visual-studio-code"></a>Visual Studio-kód 

|    |           |
| ------------- | ------------- |
| Mi ez?   | Általános célú IDE      |
| Támogatott DSVM-verziók      | Windows, Linux     |
| Jellemző felhasználások      | Kódszerkesztő és git-integráció   |
| Használat és Futtatás      | Asztali parancsikon (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) a Windowsban, asztali parancsikonban vagy terminálban (`code`) a Linux rendszerben    |
| A DSVM kapcsolódó eszközök      |     Visual Studio 2017, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio asztal 

|    |           |
| ------------- | ------------- |
| Mi ez?   | Ügyfél IDE az R nyelvhez   |
| Támogatott DSVM-verziók      | Windows, Linux      |
| Jellemző felhasználások      |  R-fejlesztés     |
| Használat és Futtatás      | Asztali parancsikon (`C:\Program Files\RStudio\bin\rstudio.exe`) Windowson, asztali parancsikon (`/usr/bin/rstudio`) Linuxon      |
| A DSVM kapcsolódó eszközök      |   Visual Studio 2017, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio-kiszolgáló 

|    |           |
| ------------- | ------------- |
| Mi ez?   | Ügyfél IDE az R nyelvhez   |
| Mi ez?   | Web-alapú IDE az R-hez    |
| Támogatott DSVM-verziók      | Linux      |
| Jellemző felhasználások      |  R-fejlesztés     |
| Használat és Futtatás      | Engedélyezze a szolgáltatást a _systemctl Enable rstudio-Server engedélyezésével_, majd indítsa el a szolgáltatást a _systemctl Start rstudio-Server kiszolgálóval_. Ezután jelentkezzen be a RStudio-kiszolgálóra a http: \//a-VM-IP: 8787.       |
| A DSVM kapcsolódó eszközök      |   Visual Studio 2017, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Mi ez?   | Az ügyfél IDE for Julia nyelve   |
| Támogatott DSVM-verziók      | Windows, Linux      |
| Jellemző felhasználások      |  Julia-fejlesztés     |
| Használat és Futtatás      | Asztali parancsikon (`C:\JuliaPro-0.5.1.1\Juno.bat`) Windowson, asztali parancsikon (`/opt/JuliaPro-VERSION/Juno`) Linuxon      |
| A DSVM kapcsolódó eszközök      |   Visual Studio 2017, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Notebookshoz

|    |           |
| ------------- | ------------- |
| Mi ez?   | A Python nyelvhez készült ügyféloldali IDE    |
| Támogatott DSVM-verziók      | Linux      |
| Jellemző felhasználások      |  Python-fejlesztés     |
| Használat és Futtatás      | Asztali parancsikon (`/usr/bin/pycharm`) Linuxon      |
| A DSVM kapcsolódó eszközök      |   Visual Studio 2017, Visual Studio Code, RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| Mi ez?   | Interaktív adatvizualizáció és BI-eszköz    |
| Támogatott DSVM-verziók      | Windows  |
| Jellemző felhasználások      |  Adatvizualizációk és az irányítópultok fejlesztése   |
| Használat és Futtatás      | Asztali parancsikon (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| A DSVM kapcsolódó eszközök      |   Visual Studio 2017, Visual Studio Code, Juno      |

