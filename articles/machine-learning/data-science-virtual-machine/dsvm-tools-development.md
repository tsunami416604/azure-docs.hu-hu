---
title: Adatok adatelemzési virtuális gép fejlesztői eszközök – Azure |} A Microsoft Docs
description: Ismerje meg az eszközök és az integrált fejlesztői környezeteket, amelyek előre telepített a Data Science virtuális gépen.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 663da31985759f47fff77dc5ad0f985d44559385
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55451580"
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>Fejlesztőeszközök a Data Science virtuális gépen

Az adatelemzési virtuális gép (DSVM) biztosítja, hogy a fejlesztési környezetbe a kötegelés több népszerű eszközeivel és IDE. Íme néhány a DSVM lévő eszközök. 

## <a name="visual-studio-2017"></a>Visual Studio 2017  
|    |           |
| ------------- | ------------- |
| Mi ez?   | Általános célú IDE      |
| A DSVM támogatott verziói      | Windows      |
| Gyakori használati      | A szoftverfejlesztői    |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?      | Adatok tudományos munkaterhelés (Python- és R-eszközök), az Azure-beli számítási (Hadoop, a Data Lake), a Node.js, SQL Server-eszközök [a Visual Studio Code az Azure Machine Learning](https://github.com/Microsoft/vs-tools-for-ai)    |
| Hogyan futtathatja / használata?      | Asztali parancsikon (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`)    |
| A dsvm-hez kapcsolódó eszközök      |     A Visual Studio Code-ban, az RStudio Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 
|    |           |
| ------------- | ------------- |
| Mi ez?   | Általános célú IDE      |
| A DSVM támogatott verziói      | Windows, Linux     |
| Gyakori használati      | A kód és a Git-integráció   |
| Hogyan futtathatja / használata?      | Asztali parancsikon (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) Windows-, asztali parancsikonjára vagy terminálon (`code`) Linux rendszeren    |
| A dsvm-hez kapcsolódó eszközök      |     A Visual Studio 2017, az RStudio, Juno  |

## <a name="rstudio--desktop"></a>Az RStudio Desktop 
|    |           |
| ------------- | ------------- |
| Mi ez?   | Ügyfél R ide-vel    |
| A DSVM támogatott verziói      | Windows, Linux      |
| Gyakori használati      |  R-fejlesztés     |
| Hogyan futtathatja / használata?      | Asztali parancsikon (`C:\Program Files\RStudio\bin\rstudio.exe`) a Windows-, asztali parancsikon (`/usr/bin/rstudio`) Linux rendszeren      |
| A dsvm-hez kapcsolódó eszközök      |   A Visual Studio 2017, a Visual Studio Code-ban Juno      |

## <a name="rstudio--server"></a>RStudio  Server 
|    |           |
| ------------- | ------------- |
| Mi ez?   | Webes IDE az r nyelvhez    |
| A DSVM támogatott verziói      | Linux      |
| Gyakori használati      |  R-fejlesztés     |
| Hogyan futtathatja / használata?      | Engedélyezze a szolgáltatást a _systemctl engedélyezése az rstudio-kiszolgáló_, majd indítsa el a szolgáltatást a _systemctl indítsa el az rstudio-kiszolgálót_. Ezután bejelentkezhet az RStudio Serverhez, http://your-vm-ip:8787.       |
| A dsvm-hez kapcsolódó eszközök      |   A Visual Studio 2017, a Visual Studio Code-ban az RStudio Desktop      |

## <a name="juno"></a>Juno 
|    |           |
| ------------- | ------------- |
| Mi ez?   | Ügyfél IDE a Julia nyelvhez   |
| A DSVM támogatott verziói      | Windows, Linux      |
| Gyakori használati      |  Julia fejlesztés     |
| Hogyan futtathatja / használata?      | Asztali parancsikon (`C:\JuliaPro-0.5.1.1\Juno.bat`) a Windows-, asztali parancsikon (`/opt/JuliaPro-VERSION/Juno`) Linux rendszeren      |
| A dsvm-hez kapcsolódó eszközök      |   A Visual Studio 2017, a Visual Studio Code-ot, az RStudio      |

## <a name="pycharm"></a>Pycharm
|    |           |
| ------------- | ------------- |
| Mi ez?   | Ügyfél IDE Python nyelvhez    |
| A DSVM támogatott verziói      | Linux      |
| Gyakori használati      |  Python-fejlesztés     |
| Hogyan futtathatja / használata?      | Asztali parancsikon (`/usr/bin/pycharm`) Linux rendszeren      |
| A dsvm-hez kapcsolódó eszközök      |   A Visual Studio 2017, a Visual Studio Code-ot, az RStudio      |



## <a name="powerbi-desktop"></a>PowerBI Desktop 
|    |           |
| ------------- | ------------- |
| Mi ez?   | Interaktív adatábrázolás és Üzletiintelligencia-eszköze    |
| A DSVM támogatott verziói      | Windows  |
| Gyakori használati      |  Adatvizualizációk és -irányítópultokat készít   |
| Hogyan futtathatja / használata?      | Asztali parancsikon (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| A dsvm-hez kapcsolódó eszközök      |   A Visual Studio 2017, a Visual Studio Code-ban Juno      |

