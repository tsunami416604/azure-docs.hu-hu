---
title: PowerShell parancsfájl központi telepítése a Windows HPC-fürt |} Microsoft Docs
description: Az Azure virtuális gépeken a Windows HPC Pack 2012 R2-fürt üzembe PowerShell parancsfájl futtatása
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 286b2be8-2533-40df-b02a-26156b1f1133
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: e05562aeac0ea89ec1c3d80d2967c8f59c68d332
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-windows-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Hozzon létre egy Windows-fürt nagy teljesítményű számítástechnikai rendszerek (HPC) a HPC Pack IaaS telepítési parancsfájl
A HPC Pack IaaS központi telepítés központi telepítése az Azure virtuális gépeken a Windows-munkaterhelések teljes HPC Pack 2012 R2 fürt PowerShell-parancsfájl futtatása. A fürt tartalmaz egy Active Directory-tartományhoz átjárócsomópont Windows Server és a Microsoft HPC Pack fut, és további Windows számítási erőforrásokat, akkor adja meg. Ha szeretné HPC Pack-fürt üzembe helyezése az Azure Linux munkaterhelésekhez, lásd: [hozzon létre egy Linux HPC-fürtöt a HPC Pack IaaS telepítési parancsfájl](../../linux/classic/hpcpack-cluster-powershell-script.md). 

> [!IMPORTANT] 
> A jelen cikkben ismertetett PowerShell-parancsfájl az Azure-ban a klasszikus üzembe helyezési modellel hoz létre a Microsoft HPC Pack 2012 R2-fürt. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.
> A jelen cikkben ismertetett parancsfájl emellett nem támogatja a HPC Pack 2016. Információ HPC Pack 2012 R2 és a HPC Pack 2016 Resource Manager-sablonok, tekintse meg a [HPC Pack fürt telepítési lehetőségek az Azure-ban](../hpcpack-cluster-options.md).

[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-files"></a>Példa konfigurációs fájlok
A következő példákban helyettesítse a saját értékeit az előfizetés-azonosítóval vagy és a fiók és a szolgáltatás nevének.

### <a name="example-1"></a>1. példa
A következő konfigurációs fájl egy HPC Pack fürt, amelyen egy helyi adatbázisok átjárócsomópont telepíti, és öt számítási csomópontok a Windows Server 2012 R2 operációs rendszert futtat. A felhőszolgáltatások közvetlenül az USA nyugati régiója hely jönnek létre. Az átjárócsomópont úgy működik, mint a tartomány erdő tartományvezérlőjévé.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2012R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### <a name="example-2"></a>2. példa
A következő konfigurációs fájl telepíti egy meglévő tartomány erdőben HPC Pack fürt. Helyi adatbázisok 1 központi csomóponttal rendelkező fürt, és 12 számítási csomópontjain alkalmazott BGInfo Virtuálisgép-bővítménnyel.
A Windows-frissítések automatikus telepítését a virtuális gép esetében a tartomány erdő le van tiltva. A felhőszolgáltatások közvetlenül a Kelet-Ázsia hely jönnek létre. A számítási csomópontok létrehozott három felhőszolgáltatások és három storage-fiókok: *MyHPCCN-0001* való *MyHPCCN-0005* a *MyHPCCNService01* és *mycnstorage01*; *MyHPCCN-0006* való *MyHPCCN0010* a *MyHPCCNService02* és *mycnstorage02*; és *MyHPCCN-0011* való *MyHPCCN-0012* a *MyHPCCNService03* és *mycnstorage03*). A számítási csomópontok jönnek létre, a számítási csomópont rögzítése meglévő titkos lemezképpel. Az automatikus növelhető vagy csökkenthető az engedélyezve van az alapértelmezett növelhető vagy csökkenthető a időközönként.

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
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### <a name="example-3"></a>3. példa
A következő konfigurációs fájl telepíti egy meglévő tartomány erdőben HPC Pack fürt. A fürt egy átjárócsomópont, egy adatbázis-kiszolgálón, 500 GB adatlemezt, a Windows Server 2012 R2 operációs rendszert futtató két csomópontok és a Windows Server 2012 R2 operációs rendszert futtató öt számítási csomópontokat tartalmazza. A felhőszolgáltatás MyHPCCNService létrejön az affinitáscsoport *MyIBAffinityGroup*, és a felhőszolgáltatásokra létrejönnek az affinitáscsoport *MyAffinityGroup*. A HPC feladat ütemezési REST API-t és a HPC-webportál az átjárócsomópont engedélyezve.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```



### <a name="example-4"></a>4. példa
A következő konfigurációs fájl telepíti egy meglévő tartomány erdőben HPC Pack fürt. Helyi adatbázisok két központi csomóponttal rendelkező fürt, két Azure csomópont sablonok létrehozása és három mérete közepes Azure csomópontot hoz létre Azure csomópontsablonhoz *AzureTemplate1*. Az átjárócsomópont konfigurálása után az átjárócsomópont fut egy parancsfájlt.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## <a name="troubleshooting"></a>Hibaelhárítás
* **"A virtuális hálózat nem létezik" hiba** -a parancsfájl futtatása az Azure-ban egy előfizetéshez tartozó egyidejűleg több fürtök üzembe helyezésekor, ha egy vagy több üzemelő példány a hiba miatt sikertelen lehet "VNet *VNet\_neve* nem létezik ".
  Ha ez a hiba akkor fordul elő, futtassa a parancsfájlt újra a sikertelen központi telepítésnél.
* **Az Internet elérése az Azure virtuális hálózat a probléma** – Ha hoz létre fürtöt az új tartományvezérlő a telepítési parancsfájl használatával vagy manuálisan főkiszolgálóvá előléptetni egy átjárócsomópont VM tartományvezérlőre, csatlakozás problémákat tapasztalhat a Virtuális gépek az internethez. Ez a probléma akkor fordulhat elő, ha továbbító DNS-kiszolgáló automatikusan konfigurálja a tartományvezérlőn, és a továbbító DNS-kiszolgáló nem oldja meg megfelelően.
  
    Ez a probléma, jelentkezzen be a tartományvezérlő, és vagy távolítsa el a továbbító konfigurációs beállítás, vagy egy érvényes továbbító DNS-kiszolgáló konfigurálása. Ez a beállítás konfigurálása a Kiszolgálókezelőben kattintson **eszközök** >
    **DNS** nyissa meg a DNS-kezelőben, és kattintson duplán a **továbbítók**.
* **RDMA hálózati elérésekor számítási igényű virtuális gépek** –, ha ad hozzá a Windows Server számítási csomópont használja az RDMA-kompatibilisek-e virtuális gépek méretezés például A8 és A9, vagy virtuális gépek csatlakoztatása az RDMA-alkalmazás hálózati problémák. Ez a probléma akkor fordul elő, akkor ha a HpcVmDrivers kiterjesztése nincs megfelelően telepítve a virtuális gépek a fürt való hozzáadásakor. A bővítmény például Beragadt telepítése állapotban.
  
    Ez a probléma megoldása érdekében először ellenőrizze a bővítményt a virtuális gépek állapotát. Ha a kiterjesztés nem megfelelően van telepítve, távolítsa el a csomópontok a HPC-fürtből, és majd újból vegye fel a csomópontok. Például az Add-HpcIaaSNode.ps1 parancsfájl futtatásával az átjárócsomópont számítási csomópont virtuális gépek is hozzáadhat.

## <a name="next-steps"></a>További lépések
* Próbálja meg futtatni egy teszt munkaterhelés a fürtön. Egy vonatkozó példáért lásd: a HPC Pack [– első lépések útmutató](https://technet.microsoft.com/library/jj884144).
* Az oktatóanyagnak, amellyel a parancsfájl a fürtöt tartalmazó környezetben, és futtassa a HPC-munkaterhelés, lásd: [Ismerkedés az Azure-, Excel és SOA alkalmazásokat és szolgáltatásokat futtathatnak HPC Pack fürtöt a](../../virtual-machines-windows-excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Próbálja meg a HPC Pack eszközök elindítása, leállítása, adja hozzá, és a számítási csomópontok eltávolítása egy fürtről hoz létre. Lásd: [kezelése számítási csomópontok HPC csomagban fürtön, az Azure-ban](hpcpack-cluster-node-manage.md).
* Első hozzon létre feladatokat küldhet a fürthöz egy helyi számítógépről, tekintse meg a [nyújt HPC feladatok egy helyi számítógépről egy HPC Pack fürtön, az Azure-ban](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

