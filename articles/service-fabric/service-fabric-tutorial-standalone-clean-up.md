---
title: 'Oktatóanyag: Önálló Service Fabric-fürt törlése – Azure Service Fabric | Microsoft Docs'
description: Ez az oktatóanyag ismerteti, hogyan törölheti az önálló fürtöt
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 28cc2bf4794620641fb6af46bd4017d74f87e955
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666993"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Oktatóanyag: Önálló fürt törlése

Az önálló Service Fabric-fürtök lehetővé teszik, hogy kiválassza a saját környezetét, és hogy a Service Fabric „bármely operációs rendszer, bármilyen felhő” módszerével hozzon létre egy fürtöt. Ebben az oktatóanyag-sorozatban egy, az AWS-en futtatott önálló fürtöt hoz létre, majd telepít rá egy alkalmazást.

Ez az oktatóanyag egy sorozat negyedik része. Az oktatóanyag jelen része bemutatja, hogyan törölheti a Service Fabric-fürt futtatásához létrehozott AWS-erőforrásokat.

A sorozat negyedik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Service Fabric-fürt törlése
> * Az AWS-erőforrások törlése

## <a name="clean-up-service-fabric-cluster"></a>A Service Fabric-fürt törlése

* Csatlakozzon RDP-kapcsolaton keresztül a Service Fabric telepítéséhez használt EC2-példányhoz
* A PowerShell megnyitása
* Lépjen a második oktatóanyagban kibontott mappába.
* A Service Fabric-fürt eltávolításához futtassa a következő parancsot:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

* `Y` amikor a rendszer kéri. Ha a művelet sikeres, a kimenet a következőhöz hasonló (az Ön IP-címeivel):

```powershell
Best Practices Analyzer completed successfully.
Removing configuration from machine 172.31.21.141
Removing configuration from machine 172.31.27.1
Removing configuration from machine 172.31.20.163
Configuration removed from machine 172.31.21.141
Configuration removed from machine 172.31.27.1
Configuration removed from machine 172.31.20.163
The cluster is successfully removed.
```

## <a name="clean-up-aws-resources"></a>Az AWS-erőforrások törlése

* Jelentkezzen be az AWS-fiókba
* Lépjen az EC2 konzolra.
* Válassza ki az oktatóanyag első részében létrehozott csomópontokat.
* Kattintson az **Actions** (Műveletek)  >  **Instance State** (Példány állapota)  >  **Terminate** (Leállítás) elemre

## <a name="next-steps"></a>További lépések

A sorozat negyedik részében megismerte, hogyan törölheti az előző lépésekben létrehozott erőforrásokat.

> [!div class="checklist"]
> * Az erőforrások törlése

> [!div class="nextstepaction"]
> [Vissza az elejére](service-fabric-tutorial-standalone-create-infrastructure.md)