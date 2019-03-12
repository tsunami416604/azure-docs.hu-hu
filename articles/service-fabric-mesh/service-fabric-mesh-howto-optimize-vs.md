---
title: Az Azure Service Fabric-háló projekteket a Visual Studio teljesítményének optimalizálása |} A Microsoft Docs
description: A Visual Studio Azure Service Fabric-háló alkalmazások teljesítményének optimalizálása
services: service-fabric-mesh
keywords: hibakeresési teljesítményének optimalizálása
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: f7a0cb47ad8010bd54a817e9990221b320cde541
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57778926"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Service Fabric-háló projekteket a Visual Studio teljesítményének optimalizálása

Ez a cikk bemutatja, hogyan a Service Fabric-háló projekteket a Visual Studio a teljesítmény optimalizálása, úgy, hogy az első hibakeresés (F5) futtatása sokkal gyorsabb.  

## <a name="change-visual-studio-settings"></a>A Visual Studio beállításainak módosítása
 
A Visual Studióban a **eszközök** > **beállítások**  > **Service Fabric-háló eszközök** > **általános**, módosíthatja a következő beállításokat:

- **A projekt szükséges Docker-rendszerképek nyitott lekéréses** megkönnyíti az első hibakeresés (F5) gyorsabban futnak a letöltési folyamat elindításával, amíg a projekt betöltése.  
- **Nyissa meg a projekt üzembe helyezése** teheti az első hibakeresés (F5) gyorsabban futnak a projekt megnyitása után az üzembe helyezési folyamatnak elindításával.  
- **Zárja be a projekt az alkalmazás eltávolítása** szabadít fel a háló alkalmazás eltávolításával, ha be van zárva a projekt az alkalmazás számára lefoglalt erőforrások (CPU, RAM).  

Amikor megjelenik a Service Fabric-eszközök, amely a Visual Studio kimeneti ablakában üzenetek 'stahují se Image.', "Bemelegedés" vagy "az alkalmazás eltávolítása", a fenti beállítások kontextusában van. Ezek a beállítások kikapcsolhatja.

## <a name="next-steps"></a>További lépések

Olvassa el a [a háló útmutató alkalmazások hibakeresése](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)