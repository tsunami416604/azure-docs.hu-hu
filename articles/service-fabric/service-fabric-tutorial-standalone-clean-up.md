---
title: Önálló fürt karbantartása
description: Ebben az oktatóanyagban megtudhatja, hogyan tisztíthatja meg az AWS vagy az Azure-erőforrásokat az önálló Service Fabric-fürtben.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: bfb23ca5f5eb9540491fbd05efdfd6997db15e6b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75639020"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Oktatóanyag: Az önálló fürt törlése

Az önálló Service Fabric-fürtök lehetővé teszik, hogy kiválassza a saját környezetét, és hogy a Service Fabric „bármely operációs rendszer, bármilyen felhő” módszerével hozzon létre egy fürtöt. Ebben az oktatóanyag-sorozatban hozzon létre egy önálló fürtöt az AWS vagy az Azure-ban, és telepítsen egy alkalmazást.

Ez az oktatóanyag egy sorozat negyedik része. Az oktatóanyag ez a része bemutatja, hogyan lehet megtisztítani a Service Fabric-fürt üzemeltetéséhez létrehozott AWS vagy Azure-erőforrásokat.

A sorozat negyedik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Service Fabric-fürt törlése
> * Az AWS- vagy az Azure-erőforrások karbantartása

## <a name="clean-up-service-fabric-cluster"></a>A Service Fabric-fürt törlése

1. RDP a szolgáltatásfabric telepítéséhez használt virtuális gépbe.
2. Nyissa meg a PowerShellt.
3. Lépjen a második oktatóanyagban kibontott mappába.
4. A Service Fabric-fürt eltávolításához futtassa a következő parancsot:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Adja `Y` meg, ha a rendszer kéri, ha sikeres volt a kimenet a következőkre néz ki, a saját IP-címeket helyettesítve:

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

1. Jelentkezzen be AWS-fiókjába.
2. Lépjen az EC2 konzolra.
3. Válassza ki az oktatóanyag első részében létrehozott csomópontokat.
4. Kattintson a Actions Instance State Terminate **(Műveletek** > **példányállapotának** > **leállítása) elemre.**

## <a name="clean-up-azure-resources"></a>Azure-erőforrások karbantartása

1. Jelentkezzen be az Azure portálra.
2. Nyissa meg a **Virtuális gépek szakaszt.**
3. Jelölje be az oktatóanyag első részében létrehozott három csomópont jelölőnégyzetét.
4. Kattintson a **Törlés gombra.**

## <a name="next-steps"></a>További lépések

A sorozat negyedik részében megismerte, hogyan törölheti az előző lépésekben létrehozott erőforrásokat.

> [!div class="checklist"]
> * Az erőforrások törlése

> [!div class="nextstepaction"]
> [Vissza az elejére](service-fabric-tutorial-standalone-create-infrastructure.md)
