---
title: Fejlesztési eszközök
titleSuffix: Azure Data Science Virtual Machine
description: Ismerje meg a Data Science Virtual Machine elérhető eszközöket és integrált fejlesztői környezeteket.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: bc1f40760c1602d81da042bf6909e44a540d35de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283757"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Fejlesztői eszközök az Azure Data Science Virtual Machine

A Data Science Virtual Machine (DSVM) számos népszerű eszközt csomagol egy rendkívül hatékony integrált fejlesztési környezetbe (IDE). Íme néhány a DSVM lévő eszközök.

## <a name="visual-studio-community-edition"></a>Visual Studio Community Edition

|    |           |
| ------------- | ------------- |
| Mi ez?   | Általános célú IDE      |
| Támogatott DSVM-verziók      | Windows: Visual Studio 2017, Windows 2019 (előzetes verzió): Visual Studio 2019      |
| Gyakori használati      | Szoftverfejlesztés    |
| Hogyan van konfigurálva és telepítve a DSVM?      | Adatelemzési munkaterhelés (Python és R-eszközök), Azure munkaterhelés (Hadoop, Data Lake), Node. js, SQL Server eszközök, [Azure Machine learning a Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai) -hoz    |
| Használat és Futtatás      | Asztali parancsikon (`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`). Grafikusan nyissa meg a Visual studiót az asztal ikon vagy a **Start** menü használatával. Keressen programokat (Windows billentyű + S), majd a **Visual studiót**. Innen olyan nyelveket hozhat létre, mint C#a, a Python, az R és a Node. js.   |
| A DSVM kapcsolódó eszközök      |     A Visual Studio Code-ban, az RStudio Juno  |

> [!NOTE]
> Előfordulhat, hogy kap egy üzenetet, hogy a próbaidőszak lejárt. Adja meg a Microsoft-fiók hitelesítő adatait. Vagy hozzon létre egy új, hozzáférhet a Visual Studio Community ingyenes fiókot.

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Mi ez?   | Általános célú IDE      |
| Támogatott DSVM-verziók      | Windows, Linux     |
| Gyakori használati      | A kód és a Git-integráció   |
| Használat és Futtatás      | Asztali parancsikon (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) a Windowsban, asztali parancsikon vagy terminál (`code`) a Linux rendszerben    |
| A DSVM kapcsolódó eszközök      |     Visual Studio, RStudio, Juno  |

## <a name="rstudio-desktop"></a>RStudio asztal

|    |           |
| ------------- | ------------- |
| Mi ez?   | Ügyfél IDE az R nyelvhez   |
| Támogatott DSVM-verziók      | Windows, Linux      |
| Gyakori használati      |  R-fejlesztés     |
| Használat és Futtatás      | Asztali parancsikon (`C:\Program Files\RStudio\bin\rstudio.exe`) Windows rendszeren, asztali parancsikon (`/usr/bin/rstudio`) Linuxon      |
| A DSVM kapcsolódó eszközök      |   Visual Studio, Visual Studio Code, Juno      |

## <a name="rstudio-server"></a>RStudio-kiszolgáló

|    |           |
| ------------- | ------------- |
| Mi ez?   | Ügyfél IDE az R nyelvhez   |
| Mi ez?   | Webes IDE az r nyelvhez    |
| Támogatott DSVM-verziók      | Linux      |
| Gyakori használati      |  R-fejlesztés     |
| Használat és Futtatás      | Engedélyezze a szolgáltatást a _systemctl Enable rstudio-Server engedélyezésével_, majd indítsa el a szolgáltatást a _systemctl Start rstudio-Server kiszolgálóval_. Ezután jelentkezzen be a RStudio-kiszolgálóra a http:\//Your-VM-IP: 8787 címen.       |
| A DSVM kapcsolódó eszközök      |   Visual Studio, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Mi ez?   | Ügyfél IDE a Julia nyelvhez   |
| Támogatott DSVM-verziók      | Windows, Linux      |
| Gyakori használati      |  Julia fejlesztés     |
| Használat és Futtatás      | Asztali parancsikon (`C:\JuliaPro-0.5.1.1\Juno.bat`) Windows rendszeren, asztali parancsikon (`/opt/JuliaPro-VERSION/Juno`) Linuxon      |
| A DSVM kapcsolódó eszközök      |   Visual Studio, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| Mi ez?   | Ügyfél IDE Python nyelvhez    |
| Támogatott DSVM-verziók      | Windows 2019 (előzetes verzió), Linux      |
| Gyakori használati      |  Python-fejlesztés     |
| Használat és Futtatás      | Asztali parancsikon (`C:\Program Files\tk`) Windows rendszeren. Asztali parancsikon (`/usr/bin/pycharm`) Linuxon      |
| A DSVM kapcsolódó eszközök      |   Visual Studio, Visual Studio Code, RStudio      |
