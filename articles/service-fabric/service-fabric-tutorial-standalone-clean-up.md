---
title: 'Oktatóanyag: Önálló Service Fabric-fürt törlése – Azure Service Fabric | Microsoft Docs'
description: Ebben az oktatóanyagban elsajátíthatja, hogyan önálló fürt törlése
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
ms.openlocfilehash: 9127ad1fe148f85779913454adf6578a9a8a1055
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274095"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Oktatóanyag: Önálló fürt törlése

Az önálló Service Fabric-fürtök lehetővé teszik, hogy kiválassza a saját környezetét, és hogy a Service Fabric „bármely operációs rendszer, bármilyen felhő” módszerével hozzon létre egy fürtöt. Oktatóanyag-sorozat AWS vagy Azure tárolt önálló fürt létrehozása és a egy alkalmazás telepítése bele.

Ez az oktatóanyag egy sorozat negyedik része. Ez a rész az oktatóanyag bemutatja, hogyan karbantartása az AWS vagy Azure-erőforrások, amelyek a Service Fabric-fürt üzemeltetésére létrehozott.

A sorozat negyedik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Service Fabric-fürt törlése
> * Az AWS vagy Azure-erőforrások törlése

## <a name="clean-up-service-fabric-cluster"></a>A Service Fabric-fürt törlése

1. RDP-Kapcsolatot létesíthessen a virtuális gép által használt telepítve van a Service Fabric.
2. Nyissa meg a PowerShellt.
3. Lépjen a második oktatóanyagban kibontott mappába.
4. A Service Fabric-fürt eltávolításához futtassa a következő parancsot:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Adja meg `Y` amikor a rendszer kéri, ha sikeres volt, akkor a kimenetben a következő, a saját IP-címmel a helyettesített fog kinézni:

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

1. Jelentkezzen be az AWS-fiókjára.
2. Lépjen az EC2 konzolra.
3. Válassza ki az oktatóanyag első részében létrehozott csomópontokat.
4. Kattintson a **műveletek** > **példány állapota** > **leállítása**.

## <a name="clean-up-azure-resources"></a>Az Azure-erőforrások törlése

1. Jelentkezzen be az Azure portálra.
2. Nyissa meg a **virtuális gépek** szakaszban.
3. Válassza ki a három csomóponttal, amely egy, az oktatóanyag első részében létrehozott jelölőnégyzeteit.
4. Kattintson a **törlése**.

## <a name="next-steps"></a>További lépések

A sorozat negyedik részében megismerte, hogyan törölheti az előző lépésekben létrehozott erőforrásokat.

> [!div class="checklist"]
> * Az erőforrások törlése

> [!div class="nextstepaction"]
> [Vissza az elejére](service-fabric-tutorial-standalone-create-infrastructure.md)
