---
title: A Visual Studio optimalizálása az Azure Service Fabric Mesh szolgáltatáshoz
description: Ez a cikk bemutatja, hogyan optimalizálhatja a Visual Studio teljesítményét service fabric mesh-projektekhez, hogy az első hibakeresés futtatása (F5) sokkal gyorsabb.
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 40c4e9972a3d83a5dd8247bacac12e9d67a15f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75497979"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>A Visual Studio teljesítményének optimalizálása Service Fabric Mesh projektekhez

Ez a cikk bemutatja, hogyan optimalizálhatja a Visual Studio teljesítményét service fabric mesh-projektekhez, hogy az első hibakeresés futtatása (F5) sokkal gyorsabb.  

## <a name="change-visual-studio-settings"></a>A Visual Studio beállításainak módosítása
 
A Visual Studio **Eszközök** > **eszközök**  > **szolgáltatáshálóeszközök** > **általános**csoportjában a következő beállításokat módosíthatja:

- **A szükséges Docker-képek lekérése a projekt megnyitásakor** gyorsabbá teszi az első hibakeresési futtatást (F5) a lemezkép letöltési folyamatának elindításával a projekt betöltése közben.  
- **Az alkalmazás központi telepítése a megnyitott projekten** gyorsabbá teheti az első hibakeresési futtatást (F5) a központi telepítés folyamatának elindításával a projekt megnyitása után.  
- **A project close alkalmazás eltávolítása** újraszerzi az alkalmazáshoz rendelt erőforrásokat (CPU, RAM) a Mesh alkalmazás eltávolításával, amikor a projekt le van zárva.  

Amikor olyan üzeneteket lát a Service Fabric Tools kimeneti ablakban, hogy a Visual Studio "képeket húz", "bemelegítést" vagy "alkalmazás eltávolítását" látja, akkor a fenti beállításokra hivatkozik. Ezeket a beállításokat kikapcsolhatja.

## <a name="next-steps"></a>További lépések

Olvassa el a [Debug a Mesh alkalmazás oktatóanyagát](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)