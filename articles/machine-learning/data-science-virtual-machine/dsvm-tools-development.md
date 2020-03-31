---
title: Fejlesztési eszközök
titleSuffix: Azure Data Science Virtual Machine
description: Ismerje meg az adatelemzési virtuális gépen elérhető eszközöket és integrált fejlesztői környezeteket.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c48cf6a7a82e90d3c9d8dc4c35e37dfb944af99f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282681"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Fejlesztési eszközök az Azure Data Science virtuális gépen

A Data Science Virtuális gép (DSVM) számos népszerű eszközt tartalmaz egy rendkívül termelékeny integrált fejlesztői környezetben (IDE). Íme néhány eszköz, amely a DSVM-en található.

## <a name="visual-studio-community-edition"></a>Visual Studio közösségi kiadás

|    |           |
| ------------- | ------------- |
| Mi ez?   | Általános célú IDE      |
| Támogatott DSVM-verziók      | Windows: Visual Studio 2017, Windows 2019 : Visual Studio 2019      |
| Tipikus felhasználások      | Szoftverfejlesztés    |
| Hogyan van beállítva és telepítve a DSVM?      | Adatelemzési számítási feladatok (Python- és R-eszközök), Azure-beli munkaterhelés (Hadoop, Data Lake), Node.js, SQL Server-eszközök, [Azure Machine Learning visual studio-kód](https://github.com/Microsoft/vs-tools-for-ai)    |
| Hogyan kell használni és futtatni      | Asztali parancsikon`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`( ). Grafikusan nyissa meg a Visual Studio alkalmazást az asztal ikonjával vagy a **Start** menüvel. Programok keresése (Windows billentyű+S), majd a **Visual Studio**. Innen projekteket hozhat létre olyan nyelveken, mint a C#, python, R és Node.js.   |
| Kapcsolódó eszközök a DSVM      |     Visual Studio kód, RStudio, Juno  |

> [!NOTE]
> Előfordulhat, hogy egy üzenet jelenik meg arról, hogy a értékelési időszak lejárt. Adja meg A Microsoft-fiók hitelesítő adatait. Vagy hozzon létre egy új ingyenes fiókot, hogy hozzáférjen a Visual Studio Community-hez.

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Mi ez?   | Általános célú IDE      |
| Támogatott DSVM-verziók      | Windows, Linux     |
| Tipikus felhasználások      | Kódszerkesztő és Git-integráció   |
| Hogyan kell használni és futtatni      | Asztali parancsikon`C:\Program Files (x86)\Microsoft VS Code\Code.exe`( ) Windows, asztali`code`parancsikon vagy terminál ( ) Linux    |
| Kapcsolódó eszközök a DSVM      |     Visual Studio, RStudio, Juno  |

## <a name="rstudio-desktop"></a>RStudio Desktop

|    |           |
| ------------- | ------------- |
| Mi ez?   | Ügyfél IDE az R nyelvhez   |
| Támogatott DSVM-verziók      | Windows, Linux      |
| Tipikus felhasználások      |  R fejlesztés     |
| Hogyan kell használni és futtatni      | Asztali parancsikon`C:\Program Files\RStudio\bin\rstudio.exe`( ) Windows,`/usr/bin/rstudio`asztali parancsikon ( ) Linuxon      |
| Kapcsolódó eszközök a DSVM      |   Visual Studio, Visual Studio kód, Juno      |

## <a name="rstudio-server"></a>RStudio Server

|    |           |
| ------------- | ------------- |
| Mi ez?   | Ügyfél IDE az R nyelvhez   |
| Mi ez?   | Webalapú IDE R-hez    |
| Támogatott DSVM-verziók      | Linux      |
| Tipikus felhasználások      |  R fejlesztés     |
| Hogyan kell használni és futtatni      | Engedélyezze a szolgáltatást a _systemctl engedélyezze rstudio-server_, majd indítsa el a szolgáltatást _systemctl start rstudio-server_. Ezután jelentkezzen be az RStudio Server kiszolgálóra a http:\//your-vm-ip:8787 címen.       |
| Kapcsolódó eszközök a DSVM      |   Visual Studio, Visual Studio kód, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Mi ez?   | Ügyfél IDE Julia nyelv   |
| Támogatott DSVM-verziók      | Windows, Linux      |
| Tipikus felhasználások      |  Julia fejlesztés     |
| Hogyan kell használni és futtatni      | Asztali parancsikon`C:\JuliaPro-0.5.1.1\Juno.bat`( ) Windows,`/opt/JuliaPro-VERSION/Juno`asztali parancsikon ( ) Linuxon      |
| Kapcsolódó eszközök a DSVM      |   Visual Studio, Visual Studio kód, RStudio      |

## <a name="pycharm"></a>Pycharm (Pycharm)

|    |           |
| ------------- | ------------- |
| Mi ez?   | Ügyfél IDE python nyelvhez    |
| Támogatott DSVM-verziók      | Windows 2019, Linux      |
| Tipikus felhasználások      |  Python fejlesztés     |
| Hogyan kell használni és futtatni      | Asztali parancsikon`C:\Program Files\tk`( ) a Windows rendszeren. Asztali parancsikon`/usr/bin/pycharm`( ) Linux on      |
| Kapcsolódó eszközök a DSVM      |   Visual Studio, Visual Studio kód, RStudio      |
