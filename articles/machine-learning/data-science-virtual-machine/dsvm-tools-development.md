---
title: Fejlesztői eszközök
titleSuffix: Azure Data Science Virtual Machine
description: Ismerje meg a Data Science Virtual Machine elérhető eszközöket és integrált fejlesztői környezeteket.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: 6ff4d92cb3730716c532332bf426132fcbb8e122
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70191951"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Fejlesztői eszközök az Azure Data Science Virtual Machine

A Data Science Virtual Machine (DSVM) számos népszerű eszközt csomagol egy rendkívül hatékony integrált fejlesztési környezetbe (IDE). Íme néhány a DSVM lévő eszközök.

## <a name="visual-studio-2019"></a>Visual Studio 2019  

|    |           |
| ------------- | ------------- |
| Mi ez?   | Általános célú IDE      |
| Támogatott DSVM-verziók      | Windows      |
| Gyakori használati      | Szoftverfejlesztés    |
| Hogyan van konfigurálva és telepítve a DSVM?      | Adatok tudományos munkaterhelés (Python- és R-eszközök), az Azure-beli számítási (Hadoop, a Data Lake), a Node.js, SQL Server-eszközök [a Visual Studio Code az Azure Machine Learning](https://github.com/Microsoft/vs-tools-for-ai)    |
| Használat és Futtatás      | Asztali parancsikon (`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`)    |
| A DSVM kapcsolódó eszközök      |     A Visual Studio Code-ban, az RStudio Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Mi ez?   | Általános célú IDE      |
| Támogatott DSVM-verziók      | Windows, Linux     |
| Gyakori használati      | A kód és a Git-integráció   |
| Használat és Futtatás      | Asztali parancsikon (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) a Windowsban, asztali parancsikon vagy terminál`code`() a Linux rendszerben    |
| A DSVM kapcsolódó eszközök      |     Visual Studio 2019, RStudio, Juno  |

## <a name="rstudio--desktop"></a>Az RStudio Desktop 

|    |           |
| ------------- | ------------- |
| Mi ez?   | Ügyfél IDE az R nyelvhez   |
| Támogatott DSVM-verziók      | Windows, Linux      |
| Gyakori használati      |  R-fejlesztés     |
| Használat és Futtatás      | Asztali parancsikon (`C:\Program Files\RStudio\bin\rstudio.exe`) Windows rendszeren, asztali parancsikon (`/usr/bin/rstudio`) Linuxon      |
| A DSVM kapcsolódó eszközök      |   Visual Studio 2019, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio  Server 

|    |           |
| ------------- | ------------- |
| Mi ez?   | Ügyfél IDE az R nyelvhez   |
| Mi ez?   | Webes IDE az r nyelvhez    |
| Támogatott DSVM-verziók      | Linux      |
| Gyakori használati      |  R-fejlesztés     |
| Használat és Futtatás      | Engedélyezze a szolgáltatást a _systemctl Enable rstudio-Server engedélyezésével_, majd indítsa el a szolgáltatást a _systemctl Start rstudio-Server kiszolgálóval_. Ezután jelentkezzen be a RStudio-kiszolgálóra a\/http:/Your-VM-IP: 8787 címen.       |
| A DSVM kapcsolódó eszközök      |   Visual Studio 2019, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Mi ez?   | Ügyfél IDE a Julia nyelvhez   |
| Támogatott DSVM-verziók      | Windows, Linux      |
| Gyakori használati      |  Julia fejlesztés     |
| Használat és Futtatás      | Asztali parancsikon (`C:\JuliaPro-0.5.1.1\Juno.bat`) Windows rendszeren, asztali parancsikon (`/opt/JuliaPro-VERSION/Juno`) Linuxon      |
| A DSVM kapcsolódó eszközök      |   Visual Studio 2019, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| Mi ez?   | Ügyfél IDE Python nyelvhez    |
| Támogatott DSVM-verziók      | Linux      |
| Gyakori használati      |  Python-fejlesztés     |
| Használat és Futtatás      | Asztali parancsikon (`/usr/bin/pycharm`) Linuxon      |
| A DSVM kapcsolódó eszközök      |   Visual Studio 2019, Visual Studio Code, RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| Mi ez?   | Interaktív adatvizualizáció és BI-eszköz    |
| Támogatott DSVM-verziók      | Windows  |
| Gyakori használati      |  Adatvizualizációk és az irányítópultok fejlesztése   |
| Használat és Futtatás      | Asztali parancsikon (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| A DSVM kapcsolódó eszközök      |   Visual Studio 2019, Visual Studio Code, Juno      |

