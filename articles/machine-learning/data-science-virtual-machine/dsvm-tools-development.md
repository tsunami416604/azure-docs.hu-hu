---
title: "Adatok tudományos virtuális gép Fejlesztőeszközök - Azure |} Microsoft Docs"
description: "Adatok tudományos virtuális gép Fejlesztőeszközök."
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
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 6aef0fbe44e19b515c2bc10c0e7c38fabac10dae
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>Az adatok tudományos virtuális gép Fejlesztőeszközök

A tudományos virtuális gép (DSVM) által számos népszerű eszközök és IDE kötegelése hatékony a fejlesztési környezetet biztosít. Íme néhány a DSVM lévő eszközök. 

## <a name="visual-studio-2017"></a>Visual Studio 2017  
|    |           |
| ------------- | ------------- |
| Mi ez?   | Általános célú IDE      |
| Támogatott DSVM verziók      | Windows      |
| A gyakori felhasználási      | Szoftverfejlesztői    |
| Hogyan van azt konfigurált / a DSVM telepítve?      | Adatok tudományos munkaterhelés (Python és R eszközök), Azure számítási (Hadoop, Data Lake), Node.js, SQL Server-eszközök    |
| Hogyan használja az / futtatni?      | Asztali parancsikonra (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`)    |
| A DSVM a kapcsolódó eszközök      |     A Visual Studio Code, Rstudióból, Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 
|    |           |
| ------------- | ------------- |
| Mi ez?   | Általános célú IDE      |
| Támogatott DSVM verziók      | Windows, Linux     |
| A gyakori felhasználási      | Kód szerkesztése és a Git integráció   |
| Hogyan használja az / futtatni?      | Asztali parancsikonra (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) a Windows, a asztali parancsikonjára vagy a Terminálszolgáltatások (`code`) a Linux    |
| A DSVM a kapcsolódó eszközök      |     Visual Studio 2017, RStudio, Juno  |

## <a name="rstudio--desktop"></a>Rstudióból asztali 
|    |           |
| ------------- | ------------- |
| Mi ez?   | Az R IDE ügyfél    |
| Támogatott DSVM verziók      | Windows, Linux      |
| A gyakori felhasználási      |  R fejlesztési     |
| Hogyan használja az / futtatni?      | Asztali parancsikonra (`C:\Program Files\RStudio\bin\rstudio.exe`) a Windows asztali parancsikonra (`/usr/bin/rstudio`) Linux rendszeren      |
| A DSVM a kapcsolódó eszközök      |   A Visual Studio 2017, a Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio  Server 
|    |           |
| ------------- | ------------- |
| Mi ez?   | Az R webes IDE    |
| Támogatott DSVM verziók      | Linux      |
| A gyakori felhasználási      |  R fejlesztési     |
| Hogyan használja az / futtatni?      | Engedélyezze a szolgáltatást a _systemctl rstudióból-kiszolgáló engedélyezése_, majd indítsa el a szolgáltatást, amely _systemctl rstudióból-kiszolgáló indítása_. Ezután bejelentkezhet Rstudióból kiszolgálóra http://your-vm-ip:8787 címen.       |
| A DSVM a kapcsolódó eszközök      |   A Visual Studio 2017, a Visual Studio Code, Rstudióból asztali      |

## <a name="juno"></a>Juno 
|    |           |
| ------------- | ------------- |
| Mi ez?   | Ágnes nyelvi IDE-ügyfél   |
| Támogatott DSVM verziók      | Windows, Linux      |
| A gyakori felhasználási      |  Ágnes fejlesztési     |
| Hogyan használja az / futtatni?      | Asztali parancsikonra (`C:\JuliaPro-0.5.1.1\Juno.bat`) a Windows asztali parancsikonra (`/opt/JuliaPro-VERSION/Juno`) Linux rendszeren      |
| A DSVM a kapcsolódó eszközök      |   A Visual Studio 2017, a Visual Studio Code, Rstudióból      |

## <a name="pycharm"></a>Pycharm
|    |           |
| ------------- | ------------- |
| Mi ez?   | Ügyfél IDE Python nyelvhez    |
| Támogatott DSVM verziók      | Linux      |
| A gyakori felhasználási      |  R fejlesztési     |
| Hogyan használja az / futtatni?      | Asztali parancsikonra (`/usr/bin/pycharm`) Linux rendszeren      |
| A DSVM a kapcsolódó eszközök      |   A Visual Studio 2017, a Visual Studio Code, Rstudióból      |



## <a name="powerbi-desktop"></a>Power bi Desktop 
|    |           |
| ------------- | ------------- |
| Mi ez?   | Adatok interaktív képi megjelenítés és BI eszköz    |
| Támogatott DSVM verziók      | Windows  |
| A gyakori felhasználási      |  Adatok vizuális és irányítópultok felépítése   |
| Hogyan használja az / futtatni?      | Asztali parancsikonra (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| A DSVM a kapcsolódó eszközök      |   A Visual Studio 2017, a Visual Studio Code, Juno      |

