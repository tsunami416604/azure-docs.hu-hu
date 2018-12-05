---
title: Az Azure Service Fabric-háló projekteket a Visual Studio teljesítményének optimalizálása |} A Microsoft Docs
description: A Visual Studio Azure Service Fabric-háló alkalmazások teljesítményének optimalizálása
services: service-fabric-mesh
keywords: hibakeresési teljesítményének optimalizálása
author: tylermsft
ms.author: twhitney
ms.date: 11/29/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: 72e900e6e48d18a721be7d2991428f81a81d1303
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52893513"
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