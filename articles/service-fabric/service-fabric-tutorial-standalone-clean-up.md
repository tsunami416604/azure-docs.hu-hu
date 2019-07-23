---
title: 'Oktatóanyag: Önálló Service Fabric-fürt törlése – Azure Service Fabric | Microsoft Docs'
description: Ebből az oktatóanyagból megtudhatja, hogyan távolíthatja el a különálló fürtöt
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
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: bebe3a2dc83b651e713ee80d7b11068b13096e04
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385164"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Oktatóanyag: Önálló fürt tisztítása

Az önálló Service Fabric-fürtök lehetővé teszik, hogy kiválassza a saját környezetét, és hogy a Service Fabric „bármely operációs rendszer, bármilyen felhő” módszerével hozzon létre egy fürtöt. Ebben az oktatóanyag-sorozatban létre fog hozni egy AWS-ben vagy az Azure-ban üzemeltetett önálló fürtöt, és telepítenie kell egy alkalmazást.

Ez az oktatóanyag egy sorozat negyedik része. Az oktatóanyag ezen része bemutatja, hogyan távolíthatja el a Service Fabric-fürt üzemeltetéséhez létrehozott AWS-vagy Azure-erőforrásokat.

A sorozat negyedik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Service Fabric-fürt törlése
> * AWS-vagy Azure-erőforrások tisztítása

## <a name="clean-up-service-fabric-cluster"></a>A Service Fabric-fürt törlése

1. Az RDP-t a virtuális géphez, amelyet a Service Fabric telepítéséhez használt.
2. Nyissa meg a PowerShellt.
3. Lépjen a második oktatóanyagban kibontott mappába.
4. A Service Fabric-fürt eltávolításához futtassa a következő parancsot:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Ha `Y` a rendszer kéri, adja meg, hogy sikeres volt-e a kimenet, a saját IP-címei pedig az alábbi módon lesznek helyettesítve:

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

1. Jelentkezzen be az AWS-fiókjába.
2. Lépjen az EC2 konzolra.
3. Válassza ki az oktatóanyag első részében létrehozott csomópontokat.
4. Kattintson a **műveletek** > **példány állapotának** > **megszakítása**elemre.

## <a name="clean-up-azure-resources"></a>Azure-erőforrások karbantartása

1. Jelentkezzen be az Azure portálra.
2. Lépjen a **Virtual Machines** szakaszra.
3. Jelölje be az oktatóanyag első részében létrehozott három csomópont jelölőnégyzeteit.
4. Kattintson a **Törlés**gombra.

## <a name="next-steps"></a>További lépések

A sorozat negyedik részében megismerte, hogyan törölheti az előző lépésekben létrehozott erőforrásokat.

> [!div class="checklist"]
> * Az erőforrások törlése

> [!div class="nextstepaction"]
> [Vissza az elejére](service-fabric-tutorial-standalone-create-infrastructure.md)
