---
title: Az Azure Service Fabric Mesh-minták keresése
description: Az elérhető Service Fabric Mesh mintaalkalmazások indexe. A forráskód ezekben a példákban bemutatja, hogyan érhet el egy adott forgatókönyv a Service Fabric erőforrásmodell használatával.
ms.date: 12/03/2018
ms.topic: conceptual
ms.openlocfilehash: c944ddc26f2e2d099cf9552acb8287c363d0c768
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461912"
---
# <a name="find-service-fabric-mesh-samples"></a>Szolgáltatásháló-minták keresése

Ez a táblázat ismerteti a rendelkezésre álló Service Fabric Mesh mintaalkalmazások. A forráskód ezekben a példákban bemutatja, hogyan érhet el egy adott forgatókönyv a Service Fabric erőforrásmodell használatával.

A sablonok közvetlenül az Azure-ba történő üzembe helyezéséről a [GitHub-mintasablon-lapon olvashat bővebben.](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md)

|Mintasablon|Forgatókönyv leírása|Forráskód|Fejlesztői eszközök|
|------------|--------------------|----------|----------------------|
| [Hello World alkalmazás](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Tárolóban tárolt statikus weblap. Linux hoz használ nginx, a Windows IIS | [Forráskód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Nincsenek követelmények |
| [Számlálóalkalmazás az Azure-fájlkötetekhez](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | Az Azure Files alapú kötet tárolón belüli csatlakoztatásával tárolja az állapotot. <br><br> **Megjegyzés:** Ehhez a sablonhoz azure files fájlmegosztásra van szükség a már kiépített [utasításokhoz](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) | [Forráskód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio mesh eszköz |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Hozzon létre egy alkalmazást egy előtér- és háttérszolgáltatással, amely DNS-alapú feloldást használ. Itt [oktatóanyagként](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore) használható | [Forráskód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Visual Studio mesh eszköz |
| [Vizuális objektumok alkalmazás](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Mikroszolgáltatások méretezése és frissítése egy alkalmazáson belül. | [Forráskód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Visual Studio mesh eszköz |
| [Szavazó alkalmazás](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | DNS-alapú felbontást használó előtér- és háttérszolgáltatással rendelkező alkalmazás létrehozása | [Forráskód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Visual Studio Mesh Tooling a Windows verzió, VS Kód / dotnet CLI lehet használni a Linux verzió |
| [Számlálóalkalmazás a szolgáltatásháló megbízható köteteihez](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| Állapot tárolása a Service Fabric reliable disk alapú kötet tárolón belüli csatlakoztatásával.| [Forráskód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio mesh eszköz |
