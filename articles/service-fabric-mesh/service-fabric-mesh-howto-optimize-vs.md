---
title: A Visual Studio teljesítményének optimalizálása Azure Service Fabric Mesh-projektekhez | Microsoft Docs
description: Ez a cikk bemutatja, hogyan optimalizálhatja a Visual Studio teljesítményét Service Fabric Mesh-projektekhez, hogy az első hibakeresési Futtatás (F5) sokkal gyorsabb legyen.
services: service-fabric-mesh
keywords: hibakeresési teljesítmény optimalizálása
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: 04aeaa6b008b50789f4380e4bb98beba3957c2e9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663436"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>A Visual Studio teljesítményének optimalizálása Service Fabric Mesh-projektekhez

Ez a cikk bemutatja, hogyan optimalizálhatja a Visual Studio teljesítményét Service Fabric Mesh-projektekhez, hogy az első hibakeresési Futtatás (F5) sokkal gyorsabb legyen.  

## <a name="change-visual-studio-settings"></a>A Visual Studio beállításainak módosítása
 
A Visual Studióban a **tools** > **options**  > **Service Fabric Mesh Tools** > **általános**beállításnál a következő beállításokat módosíthatja:

- A **kötelező Docker-rendszerképek lekérése a projekt megnyitásakor** az első hibakeresési művelet (F5) gyorsabb lesz, ha elindítja a lemezkép letöltési folyamatát, miközben a projekt betöltődik.  
- Az **alkalmazás üzembe helyezése a projekt megnyitásakor** megteheti az első hibakeresési műveletet (F5), ha a projekt megnyitása után elindítja a telepítés folyamatát.  
- **Alkalmazás eltávolítása a Project** szolgáltatásban: zárja be az alkalmazás számára lefoglalt erőforrásokat (CPU, RAM), ha eltávolítja a háló alkalmazást a projekt bezárásakor.  

Ha az Service Fabric eszközök kimeneti ablakában látja az üzeneteket, a Visual Studio "képek húzása", "bemelegítés" vagy "alkalmazás eltávolítása", akkor a fenti beállításokra hivatkozik. Ezeket a beállításokat ki is kapcsolhatja.

## <a name="next-steps"></a>További lépések

[Útmutató a Mesh-alkalmazás hibakereséséhez](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)