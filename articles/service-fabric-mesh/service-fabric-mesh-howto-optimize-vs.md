---
title: A Visual Studio for Azure Service Fabric Mesh optimalizálása
description: Ez a cikk bemutatja, hogyan optimalizálhatja a Visual Studio teljesítményét Service Fabric Mesh-projektekhez, hogy az első hibakeresési Futtatás (F5) sokkal gyorsabb legyen.
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 40c4e9972a3d83a5dd8247bacac12e9d67a15f66
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75497979"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>A Visual Studio teljesítményének optimalizálása Service Fabric Mesh-projektekhez

Ez a cikk bemutatja, hogyan optimalizálhatja a Visual Studio teljesítményét Service Fabric Mesh-projektekhez, hogy az első hibakeresési Futtatás (F5) sokkal gyorsabb legyen.  

## <a name="change-visual-studio-settings"></a>A Visual Studio beállításainak módosítása
 
A Visual Studióban az **eszközök** > **Beállítások**  > **Service Fabric Mesh Tools** > **általános**területen a következő beállításokat módosíthatja:

- A **kötelező Docker-rendszerképek lekérése a projekt megnyitásakor** az első hibakeresési művelet (F5) gyorsabb lesz, ha elindítja a lemezkép letöltési folyamatát, miközben a projekt betöltődik.  
- Az **alkalmazás üzembe helyezése a projekt megnyitásakor** megteheti az első hibakeresési műveletet (F5), ha a projekt megnyitása után elindítja a telepítés folyamatát.  
- **Alkalmazás eltávolítása a Project** szolgáltatásban: zárja be az alkalmazás számára lefoglalt erőforrásokat (CPU, RAM), ha eltávolítja a háló alkalmazást a projekt bezárásakor.  

Ha az Service Fabric eszközök kimeneti ablakában látja az üzeneteket, a Visual Studio "képek húzása", "bemelegítés" vagy "alkalmazás eltávolítása", akkor a fenti beállításokra hivatkozik. Ezeket a beállításokat ki is kapcsolhatja.

## <a name="next-steps"></a>További lépések

[Útmutató a Mesh-alkalmazás hibakereséséhez](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)