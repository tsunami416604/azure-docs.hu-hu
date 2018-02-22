---
title: "Azure hibakeresési lehetőségek a felhőalapú szolgáltatások |} Microsoft Docs"
description: "Hibakeresési Azure cloud services csomag"
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: ghogen
editor: 
ms.assetid: 80755da7-8350-4f5c-97ce-2962beabb36d
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/18/2017
ms.author: mikejo
ms.openlocfilehash: 0ba0871cdc522cb1ffbafa617181931fe6fd5b59
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="learn-the-various-ways-to-debug-an-azure-cloud-service"></a>Ismerje meg a különböző módszereket hibakeresése az Azure-felhőszolgáltatás
Ez a cikk az Azure-felhőszolgáltatás hibakeresését különböző módokon mutató hivatkozásokat tartalmaz. 

## <a name="debugging-an-azure-cloud-service-in-visual-studio"></a>Egy Azure-felhőszolgáltatásban a Visual Studio hibakereső
Időt takaríthat meg, és az Azure használatával pénzt számítási emulátor hibakeresése a felhőalapú szolgáltatás a helyi számítógépen. Által a szolgáltatás telepítése előtt helyileg hibakeresés, akkor is megbízhatóságának és teljesítményének javítása számítási alkalommal fizető nélkül. Azonban néhány hiba is felléphet, csak akkor, ha egy felhőalapú szolgáltatás futtatja az Azure-ban. Csak akkor, ha egy felhőalapú szolgáltatás futtatja az Azure-ban jelentkező hibákról is indítja el, mivel lehetővé teszi a távoli hibakeresés, a szolgáltatás közzétételekor, és a hibakereső majd csatolása a szerepkör példánya. További információkért lásd: [a felhőalapú szolgáltatás a helyi számítógépen Debug](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer).

## <a name="using-intellitrace"></a>Az IntelliTrace 
Használatakor a Visual Studio Enterprise szerepkörök megcélzott .NET-keretrendszer 4.5 írni, engedélyezheti az IntelliTrace telepíteni, a Visual Studióból egy Azure felhőszolgáltatást időpontjában. IntelliTrace biztosít, amely a Visual Studio segítségével az alkalmazás hibakeresését, mintha az Azure-ban futó napló. További információkért lásd: [egy közzétett felhőszolgáltatás IntelliTrace és a Visual Studio hibakeresési](http://go.microsoft.com/fwlink/p/?LinkId=623016).

## <a name="remote-debugging"></a>Távoli hibakeresés 
Engedélyezheti a távoli hibakeresés a felhőalapú szolgáltatások telepítésekor a Visual Studio felhőszolgáltatás időpontjában. Ha a központi telepítés távoli hibakeresésének engedélyezése, távoli hibakeresési szolgáltatásai telepítve vannak az egyes szerepkörpéldányt futó virtuális gépek. Ezek szolgáltatások – például `msvsmon.exe` – nincs hatása a teljesítményre vagy további költségeket eredményez. További információkért lásd: [hibakeresése az Azure-felhőszolgáltatás](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure).

## <a name="next-steps"></a>További lépések
- [Egy Azure-felhőszolgáltatásban vagy a virtuális gép a Visual Studio hibakeresési](./vs-azure-tools-debug-cloud-services-virtual-machines.md) – ismerje meg részletesen ismerteti az Azure felhőszolgáltatások hibakeresését.