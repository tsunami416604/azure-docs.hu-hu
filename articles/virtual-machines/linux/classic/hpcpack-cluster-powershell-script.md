---
title: PowerShell parancsfájl központi telepítése Linux HPC-fürt |} Microsoft Docs
description: Egy Azure virtuális gépeken Linux HPC Pack 2012 R2-fürt üzembe PowerShell parancsfájl futtatása
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 73041960-58d3-4ecf-9540-d7e1a612c467
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: 66affb47190ba0c6fccaae8e8267b310682aee46
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841843"
---
# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Hozzon létre egy Linux-fürt nagy teljesítményű számítástechnikai rendszerek (HPC) a HPC Pack IaaS telepítési parancsfájl
A HPC Pack IaaS központi telepítés központi telepítése az Azure virtuális gépeken Linux munkaterhelések teljes HPC Pack 2012 R2 fürt PowerShell-parancsfájl futtatása. A fürt egy Active Directory-tartományhoz átjárócsomópont Windows Server és a Microsoft HPC Pack fut, és a Linux terjesztéseket HPC Pack által támogatott valamelyikét futtató számítási csomópontok áll. Ha szeretné HPC Pack-fürt üzembe helyezése a Azure Windows-munkaterhelések, lásd: [hozzon létre egy Windows HPC-fürtöt a HPC Pack IaaS telepítési parancsfájl](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
> [!IMPORTANT] 
> A jelen cikkben ismertetett PowerShell-parancsfájl az Azure-ban a klasszikus üzembe helyezési modellel hoz létre a Microsoft HPC Pack 2012 R2-fürt. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.
> A jelen cikkben ismertetett parancsfájl emellett nem támogatja a HPC Pack 2016. Információ HPC Pack 2012 R2 és a HPC Pack 2016 Resource Manager-sablonok, tekintse meg a [HPC Pack fürt telepítési lehetőségek az Azure-ban](../hpcpack-cluster-options.md).


[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>Példa konfigurációs fájlt
A következő konfigurációs fájl egy tartományvezérlő és a tartomány erdő hoz létre, és telepíti az HPC Pack fürt, amelynek helyi adatbázisok egy átjárócsomópont és 10 Linux számítási csomópontok. A felhőszolgáltatások közvetlenül a Kelet-Ázsia hely jönnek létre. A Linux számítási csomópontok létrehozott két felhőalapú szolgáltatások és a két storage-fiókok (Ez azt jelenti, hogy *MyLnxCN-0001* való *MyLnxCN-0005* a *MyLnxCNService01* és *mylnxstorage01*, és *MyLnxCN-0006* való *MyLnxCN-0010* a *MyLnxCNService02* és *mylnxstorage02*). A számítási csomópontok OpenLogic CentOS 7.0 verzió Linux lemezképből jönnek létre. 

Helyettesítse a saját értékeit az előfizetés nevét, a fiók és a szolgáltatás neve.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
    </DomainController>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## <a name="troubleshooting"></a>Hibaelhárítás
* **"A virtuális hálózat nem létezik" hiba**. A HPC Pack IaaS telepítési parancsfájl futtatása az Azure-ban egy előfizetéshez tartozó egyidejűleg több fürtök üzembe helyezésekor, ha egy vagy több üzemelő példány a hiba miatt sikertelen lehet "VNet *VNet\_neve* nem létezik".
  Ha ez a hiba akkor fordul elő, futtassa újra a parancsfájlt a sikertelen központi telepítés.
* **Az Internet elérése az Azure virtuális hálózat a probléma**. HPC Pack-fürtöt hoz létre egy új tartományvezérlővel a telepítési parancsfájl használatával, vagy manuálisan főkiszolgálóvá előléptetni egy átjárócsomópont VM tartományvezérlőre, ha a virtuális gépek az Azure virtuális hálózat csatlakozik az internetre problémákat tapasztalhatja. Ez akkor fordulhat elő, ha egy továbbító DNS-kiszolgáló automatikusan konfigurálja a tartományvezérlőn, és a továbbító DNS-kiszolgáló nem oldja meg megfelelően.
  
    Ez a probléma, jelentkezzen be a tartományvezérlő, és vagy távolítsa el a továbbító konfigurációs beállítás, vagy egy érvényes továbbító DNS-kiszolgáló konfigurálása. Ehhez a Kiszolgálókezelőben kattintson **eszközök** > **DNS** nyissa meg a DNS-kezelőben, és kattintson duplán a **továbbítók**.

## <a name="next-steps"></a>További lépések
* Lásd: [Ismerkedés az Azure-ban HPC Pack-fürtben lévő Linux számítási csomópontok](hpcpack-cluster.md) támogatott Linux disztribúciókról kapcsolatos információkért megköveteli az adatok, és a Linux és HPC Pack fürthöz feladatok elküldése számítási csomópontjain.
* Oktatóprogramot kínál, amelyek a parancsfájl segítségével hozzon létre egy fürtöt, és futtassa a Linux HPC munkaterhelés lásd:
  * [A Microsoft HPC Pack NAMD futó Linux számítási csomópontok az Azure-ban](hpcpack-cluster-namd.md)
  * [A Microsoft HPC Pack OpenFOAM futó Linux számítási csomópontok az Azure-ban](hpcpack-cluster-openfoam.md)
  * [Futtassa a csillag-CCM + Microsoft HPC Pack Linux számítási csomópontok az Azure-ban](hpcpack-cluster-starccm.md)

