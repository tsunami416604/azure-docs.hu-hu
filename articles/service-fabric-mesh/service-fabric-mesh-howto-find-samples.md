---
title: Azure Service Fabric Mesh-minták keresése | Microsoft Docs
description: Itt látható az elérhető Service Fabric Mesh minta-alkalmazások indexe. A példákban szereplő forráskód azt mutatja be, hogyan lehet elérni egy adott forgatókönyvet a Service Fabric erőforrás-modell használatával.
services: service-fabric-mesh
keywords: ''
author: athinanthny
ms.author: atsenthi
ms.date: 12/03/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chackdan
ms.openlocfilehash: a300c06a6a18bf2e986b3736b78957daf3d7efd6
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718808"
---
# <a name="find-service-fabric-mesh-samples"></a>Service Fabric Mesh-minták keresése

Ez a táblázat az elérhető Service Fabric Mesh-minták alkalmazásait ismerteti. A példákban szereplő forráskód azt mutatja be, hogyan lehet elérni egy adott forgatókönyvet a Service Fabric erőforrás-modell használatával.

A sablonok Azure-beli üzembe helyezésével kapcsolatos további információkért tekintse meg a [sablon GitHub-lapját.](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md)

|Minta sablon|Forgatókönyv leírása|Forráskód|Fejlesztői eszközök|
|------------|--------------------|----------|----------------------|
| ["Helló világ!" alkalmazás alkalmazás](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Tárolóban üzemeltetett statikus weblap. Linux rendszeren Nginx-et használ a Windows IIS-hez | [Forráskód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Nincsenek követelmények |
| [Számláló alkalmazás Azure-fájl köteteihez](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | Az állapotot Azure Files-alapú kötetnek a tárolón belüli csatlakoztatásával tárolja. <br><br> **Megjegyzés:** Ehhez a sablonhoz egy Azure Files fájlmegosztást kell kiépíteni. [](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) | [Forráskód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh-eszközök |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Hozzon létre egy alkalmazást egy olyan előtér-és háttér-szolgáltatással, amely DNS-alapú feloldást használ. [Oktatóanyagként](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore) használatos | [Forráskód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Visual Studio Mesh-eszközök |
| [Vizualizációs objektumok alkalmazás](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Egy alkalmazáson belül méretezhető és frissíthető a szolgáltatások. | [Forráskód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Visual Studio Mesh-eszközök |
| [Szavazó alkalmazás](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Olyan alkalmazás létrehozása, amely a DNS-alapú feloldást használó előtér-és háttér-szolgáltatással rendelkezik | [Forráskód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | A Visual Studio Mesh-eszközök a Windows-verzióhoz, a VS Code/DotNet CLI használható a Linux-verzióhoz |
| [Service Fabric megbízható kötetek számláló alkalmazás](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| Az állapot tárolása Service Fabric megbízható lemez-alapú kötetnek a tárolón belüli csatlakoztatásával.| [Forráskód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh-eszközök |
