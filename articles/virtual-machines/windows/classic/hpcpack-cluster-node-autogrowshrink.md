---
title: Automatikus skálázás HPC Pack fürtcsomópontok |} Microsoft Docs
description: Automatikusan nő, és a számítási fürtcsomópontok HPC Pack az Azure-ban számára zsugorítása
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: ''
editor: tysonn
ms.assetid: 38762cd1-f917-464c-ae5d-b02b1eb21e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/08/2016
ms.author: danlep
ms.openlocfilehash: 4a2350183bc0cb9360e9315cd8a351be20b66584
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31603844"
---
# <a name="automatically-grow-and-shrink-the-hpc-pack-cluster-resources-in-azure-according-to-the-cluster-workload"></a>Automatikusan növelhető, vagy az Azure-ban a HPC Pack fürterőforrások csökkenthető a fürtmunkaterhelés szerint
Ha az Azure "kapacitásnövelés" csomópontok HPC Pack fürt központi telepítését, vagy a HPC Pack-fürtöt hoz létre az Azure virtuális gépeken, érdemes lehet egy módszerre, amellyel automatikusan nő, és a fürt erőforrásait, például a csomópontok vagy a fürtön a munkaterhelés szerint magok csökkenhet. Ily módon a fürterőforrásokat skálázás lehetővé teszi az Azure-erőforrások hatékonyabban használja, és azok kapcsolatos költségek szabályozását.

Ez a cikk bemutatja, hogy kétféle módszert biztosít a HPC Pack automatikus skálázásra számítási erőforrások:

* A HPC Pack fürt tulajdonság **AutoGrowShrink**

* A **AzureAutoGrowShrink.ps1** HPC PowerShell-parancsfájl

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Jelenleg akkor csak automatikusan növelhető vagy csökkenthető a Windows Server operációs rendszert futtató HPC Pack számítási csomópontok.


## <a name="set-the-autogrowshrink-cluster-property"></a>A AutoGrowShrink fürt tulajdonsága
### <a name="prerequisites"></a>Előfeltételek

* **HPC Pack 2012 R2 Update 2 vagy újabb rendszerű fürt** -az átjárócsomóponthoz lehet telepítve a helyi vagy egy Azure virtuális gép. Lásd: [HPC Pack hibrid fürt beállítása](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) egy helyszíni átjárócsomópont és az Azure "kapacitásnövelés" csomópontok használatába. Tekintse meg a [HPC Pack IaaS telepítési parancsfájl](hpcpack-cluster-powershell-script.md) való gyorsan HPC Pack-fürt üzembe helyezése az Azure virtuális gépeken.

* **Az Azure (Resource Manager üzembe helyezési modellben) központi csomóponton fürt** - HPC Pack 2016-től kezdődően az Azure Active Directory alkalmazásban tanúsítványhitelesítés szolgál automatikusan növekvő és zsugorítását fürt virtuális gépek használatával telepíthetők. Az Azure Resource Manager. Tanúsítvány konfigurálása az alábbiak szerint:

  1. Fürttelepítésben után egy átjárócsomópontjához által a távoli asztal csatlakozik.

  2. Töltse fel az tanúsítványt (PFX formátumban és a titkos kulcs) minden átjárócsomópont és telepítését a Cert: \LocalMachine\My és a Cert: \LocalMachine\Root.

  3. Indítsa el az Azure Powershellt rendszergazdaként, és egy központi csomópontján a következő parancsokat:

    ```powershell
        cd $env:CCP_HOME\bin

        Connect-AzureRmAccount
    ```
        
    Ha a fiók több mint egy Azure Active Directory-bérlő vagy az Azure-előfizetés, válassza ki a megfelelő bérlői és az előfizetés a következő parancsot futtathatja:
  
    ```powershell
        Connect-AzureRmAccount -TenantId <TenantId> -SubscriptionId <subscriptionId>
    ```     
       
    A következő parancsot a jelenleg kijelölt bérlői és az előfizetés megtekintése:
    
    ```powershell
        Get-AzureRMContext
    ```

  4. Futtassa a következő parancsfájlt

    ```powershell
        .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -CertificateThumbprint "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" -TenantId xxxxxxxx-xxxxx-xxxxx-xxxxx-xxxxxxxxxxxx
    ```

    Ha

    **DisplayName** -Azure Active alkalmazás megjelenítési neve. Ha az alkalmazás nem létezik, az Azure Active Directoryban létrejön.

    **Kezdőlap** – az alkalmazás kezdőlapja. Beállíthatja, hogy egy üres URL-címet, az előző példában látható módon.

    **IdentifierUri** – az alkalmazás azonosítója. Beállíthatja, hogy egy üres URL-címet, az előző példában látható módon.

    **CertificateThumbprint** -1. lépésben a központi csomóponton telepített tanúsítvány ujjlenyomata.

    **A TenantId** -Azonosítót az Azure Active Directory-Bérlőazonosítóra. A bérlő azonosítója lekérheti az Azure Active Directory portálon **tulajdonságok** lap.

    Ha szeretne többet megtudni **ConfigARMAutoGrowShrinkCert.ps1**- ben futtassa `Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed`.


* **Az Azure (klasszikus üzembe helyezési modellel) központi csomóponton fürt** – Ha a HPC Pack IaaS telepítési parancsfájl segítségével a fürt létrehozása a klasszikus üzembe helyezési modellel, engedélyezze a **AutoGrowShrink** tulajdonság által a fürt a AutoGrowShrink beállítást a fürt konfigurációs fájlban. További információkért tekintse meg a kísérő dokumentáció a [parancsfájl letöltése](https://www.microsoft.com/download/details.aspx?id=44949).

    Azt is megteheti, engedélyezi a **AutoGrowShrink** fürt tulajdonság a következő szakaszban leírt HPC PowerShell-parancsok segítségével a fürt telepítése után. Ez az előkészítéséhez végezze el az alábbi lépéseket:

  1. Konfigurálása az Azure felügyeleti tanúsítványt, az átjárócsomópont és az Azure-előfizetés. Teszt üzembe helyezés esetén a Microsoft HPC Azure alapértelmezett önaláírt tanúsítványt használjon, az átjárócsomópont telepít HPC Pack, és a tanúsítvány majd feltölteni az Azure-előfizetéshez. Beállítások és a lépéseket, tekintse meg a [TechNet Library útmutatást](https://technet.microsoft.com/library/gg481759.aspx).

  2. Futtatás **regedit** az átjárócsomóponthoz HKLM\SOFTWARE\Micorsoft\HPC\IaasInfo Ugrás, és adjon hozzá egy karakterláncértéket. Állítsa be a nevet a "Ujjlenyomata", és az érték 1. lépésben a tanúsítvány ujjlenyomatára.

### <a name="hpc-powershell-commands-to-set-the-autogrowshrink-property"></a>HPC PowerShell-parancsok futtatásával AutoGrowShrink tulajdonságának beállítása
Az alábbiakban a HPC PowerShell Példaparancsok beállítása **AutoGrowShrink** és a további paraméterek működését. Lásd: [AutoGrowShrink paraméterek](#AutoGrowShrink-parameters) beállítások teljes listáját az ebben a cikkben később.

Futtassa a következő parancsokat, rendszergazdaként HPC PowerShell kell elindítani a fürt átjárócsomópontjából.

**Ahhoz, hogy a AutoGrowShrink tulajdonság**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 1
```

**A AutoGrowShrink tulajdonság letiltása**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 0
```

**Módosíthatja a igazodás időköz percben**

```powershell
Set-HpcClusterProperty –GrowInterval <interval>
```

**Módosíthatja a zsugorítási időköz percben**

```powershell
Set-HpcClusterProperty –ShrinkInterval <interval>
```

**Az aktuális konfigurációja AutoGrowShrink megtekintése**

```powershell
Get-HpcClusterProperty –AutoGrowShrink
```

**A csoportok csomópont kizárása AutoGrowShrink**

```powershell
Set-HpcClusterProperty –ExcludeNodeGroups <group1,group2,group3>
```

>[!NOTE] 
> Ez a paraméter támogatott HPC Pack 2016 indítása
>

### <a name="autogrowshrink-parameters"></a>AutoGrowShrink paraméterek
Az alábbiakban AutoGrowShrink paraméterek használatával módosítható a **Set-HpcClusterProperty** parancsot.

* **EnableGrowShrink** -kapcsoló engedélyezheti vagy tilthatja le a **AutoGrowShrink** tulajdonság.
* **ParamSweepTasksPerCore** -nő, egy alapvető paraméteres ismétlés feladatok száma. Az alapértelmezett érték egy alapvető feladatonként növelése sikertelen volt.

  > [!NOTE]
  > HPC Pack QFE KB3134307 módosítások **ParamSweepTasksPerCore** való **TasksPerResourceUnit**. A feladat erőforrástípus alapul, és csomópont, szoftvercsatorna vagy core lehet.
  >
  >
* **GrowThreshold** -való automatikus növekedés aszinkron feladatok küszöbértéket. Az alapértelmezett érték 1, ami azt jelenti, hogy ha a sorban álló állapotban, 1 vagy több feladat méretének automatikus növelése csomópontok.
* **GrowInterval** -időköz percben automatikus növekedés indításához. Az alapértelmezett érték 5 perc.
* **ShrinkInterval** -időköz percben automatikus zsugorítását indításához. Az alapértelmezett érték 5 perc. |}
* **ShrinkIdleTimes** -folyamatos ellenőrzések zsugorítása jelzi a csomópontok száma üresjáratban. Az alapértelmezett érték 3-szor. Például ha a **ShrinkInterval** 5 perc, a HPC Pack ellenőrzi, hogy a csomópont tétlen 5 percenként. Ha a csomópontok üresjárati állapotban van, a folyamatos 3 ellenőrzi (15 perc) után, HPC Pack zsugorítja csomóponton.
* **ExtraNodesGrowRatio** -csomópontok nő a Message Passing Interface (MPI) feladatok további százalékát. Az alapértelmezett értéke 1, ami azt jelenti, hogy HPC Pack növekedésének csomópontok MPI-feladatok 1 %.
* **GrowByMin** -kapcsoló annak jelzésére, hogy az automatikus növekedésre házirend a feladathoz szükséges minimális erőforrások alapul. Az alapértelmezett értéke false, ami azt jelenti, hogy a HPC Pack növekedésének csomópontok a feladatok a feladatokhoz szükséges erőforrások alapján.
* **SoaJobGrowThreshold** -küszöbérték bejövő SOA kérelmek indításához az automatikus növelési folyamat. Az alapértelmezett érték: 50000.

  > [!NOTE]
  > Ez a paraméter a HPC Pack 2012 R2 Update 3 verziójától kezdve alkalmazható.
  >
  >
* **SoaRequestsPerCore** -nő, egy alapvető lekérdezések bejövő SOA száma. Az alapértelmezett érték: 20000.

  > [!NOTE]
  > Ez a paraméter a HPC Pack 2012 R2 Update 3 verziójától kezdve alkalmazható.
  >
* **ExcludeNodeGroups** – a megadott csomópont csoportokban lévő csomópontok automatikusan növelhető vagy csökkenthető.
  
  > [!NOTE]
  > Ez a paraméter HPC Pack 2016 verziójától kezdve alkalmazható.
  >

### <a name="mpi-example"></a>MPI – példa
Alapértelmezés szerint a HPC Pack 1 % növekedésének MPI-feladatok extra csomópontok (**ExtraNodesGrowRatio** értéke 1). Oka az, hogy MPI több csomópont lehet szükség, és a feladat csak futtatható, amikor készen áll az összes csomópont. Csomópontok Azure indításakor alkalmanként egy csomópont lehet, hogy több időre van szüksége többinél, más csomópontok üresjáratban lehet, miközben a rendszer az adott csomópont kész állapotba hozásához okozó elindításához. További csomópontokat karcsúsításával HPC Pack csökkenti a erőforrás várakozási időt, és potenciálisan menti a költségek. MPI-feladatok (például 10 %-át) extra csomópontok százaléka növeléséhez hasonló parancs futtatása

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### <a name="soa-example"></a>SOA-példa
Alapértelmezés szerint **SoaJobGrowThreshold** 50000 értékre van állítva és **SoaRequestsPerCore** 20000 értékre van állítva. Ha egy SOA-feladatok 70000 kérések, egy sorban álló feladat és bejövő kérelmek 70000. Ebben az esetben a HPC Pack növekszik a sorban álló feladat, és a bejövő kéréseket 1 mag, nő (70000-50000) / 20000 = 1 alapvető, így a teljes növekszik a SOA-feladatok 2 magos.

## <a name="run-the-azureautogrowshrinkps1-script"></a>A AzureAutoGrowShrink.ps1 parancsprogrammal
### <a name="prerequisites"></a>Előfeltételek

* **HPC Pack 2012 R2 Update 1 vagy újabb rendszerű fürt** – a **AzureAutoGrowShrink.ps1** parancsfájl telepítve van a % CCP_HOME % bin mappában. A fürt átjárócsomópontjába lehet telepítve a helyi vagy egy Azure virtuális gép. Lásd: [HPC Pack hibrid fürt beállítása](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) egy helyszíni átjárócsomópont és az Azure "kapacitásnövelés" csomópontok használatába. Tekintse meg a [HPC Pack IaaS telepítési parancsfájl](hpcpack-cluster-powershell-script.md) gyorsan HPC Pack-fürt üzembe helyezése az Azure virtuális gépeken, vagy használjon egy [Azure gyors üzembe helyezés sablon](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/).
* **Az Azure PowerShell 1.4.0** -a parancsfájl jelenleg ezt a verziót az Azure PowerShell függ.
* **A fürt az Azure-kapacitásnövelés csomópontok** -futtassa a parancsfájlt egy ügyfélszámítógépen, amelyen telepítve van-e az HPC Pack, vagy a head csomóponton. Ha fut az ügyfélszámítógépeken, győződjön meg arról, hogy megadta-e a változó $env: CCP_SCHEDULER a head csomópontra. Az Azure "kapacitásnövelés" csomópontok hozzá kell adni a fürthöz, de nem telepített állapotban lehetnek.
* **Az Azure virtuális gépeken (Resource Manager üzembe helyezési modellben) telepített fürt** -a Resource Manager üzembe helyezési modellben telepített Azure virtuális gépek fürtben, a parancsfájl az Azure authentication két módszert támogat: futtatásához az Azure-fiókjával jelentkezzen be a minden alkalommal parancsfájl (futtatásával `Connect-AzureRmAccount`, vagy konfigurálja a szolgáltatás egyszerű való hitelesítéshez szükséges tanúsítvány. HPC Pack biztosít a parancsfájl **ConfigARMAutoGrowShrinkCert.ps** egyszerű szolgáltatás létrehozása a tanúsítványt. A parancsfájl létrehoz egy Azure Active Directory (Azure AD) alkalmazás és egy egyszerű szolgáltatást, és a közreműködő szerepkört rendel az egyszerű szolgáltatás. A parancsfájl futtatásához indítsa el az Azure Powershellt rendszergazdaként, és futtassa a következő parancsokat:

    ```powershell
    cd $env:CCP_HOME\bin

    Connect-AzureRmAccount

    .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -PfxFile "d:\yourcertificate.pfx"
    ```

    Ha szeretne többet megtudni **ConfigARMAutoGrowShrinkCert.ps1**- ben futtassa `Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed`,

* **Az Azure virtuális gépeken (klasszikus üzembe helyezési modellel) telepített fürt** -futtassa a parancsfájlt az átjárócsomóponthoz virtuális gép, mert előfeltétel a **Start-HpcIaaSNode.ps1** és **Stop-HpcIaaSNode.ps1** parancsfájlok amely telepítve van. A parancsfájlok továbbá egy Azure felügyeleti tanúsítvánnyal kell rendelkezniük, vagy közzététele beállításfájl (lásd: [kezelése számítási csomópontok HPC csomagban fürtön, az Azure-ban](hpcpack-cluster-node-manage.md)). Győződjön meg arról, hogy minden a számítási csomópont virtuális gépek kell már felvette a fürthöz. Azok a leállított állapotban lehet.



### <a name="syntax"></a>Szintaxis
```powershell
AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfActiveQueuedTasksPerNodeToGrow <Single> [-NumOfActiveQueuedTasksToGrowThreshold <Int32>]
    [-NumOfInitialNodesToGrow <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>]
    [-ShrinkCheckIdleTimes <Int32>] [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>]
    [<CommonParameters>]

AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfQueuedJobsPerNodeToGrow <Single> [-NumOfQueuedJobsToGrowThreshold <Int32>] [-NumOfInitialNodesToGrow
    <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>] [-ShrinkCheckIdleTimes <Int32>]
    [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]

AzureAutoGrowShrink.ps1 -UseLastConfigurations [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]
```
### <a name="parameters"></a>Paraméterek
* **NodeTemplates** -növelhető vagy csökkenthető a csomópontok hatókörének meghatározásához a csomópont sablonok neve. Ha nincs megadva (az alapértelmezett érték: @()), minden csomópontja a **AzureNodes** csomópontcsoport, a hatókör mikor **NodeType** AzureNodes, és az összes csomópontjának értéke a **ComputeNodes**csomópont csoport, a hatókör mikor **NodeType** ComputeNodes értéke.
* **JobTemplates** – a feladat sablonok neve a csomópontok nő hatókörének meghatározásához.
* **A NodeType** -csomópont növelhető vagy csökkenthető a típusát. Támogatott értékek a következők:

  * **AzureNodes** – Azure PaaS (kapacitásnövelés) csomópontján egy a helyszíni vagy Azure IaaS-fürt.
  * **ComputeNodes** - a számítási csomópont virtuális gépek csak Azure IaaS-fürtben lévő.

* **NumOfQueuedJobsPerNodeToGrow** -szükséges egy csomópont növelése sikertelen volt az aszinkron feladatok száma.
* **NumOfQueuedJobsToGrowThreshold** -igazodás megkezdéséhez az aszinkron feladatok küszöbérték száma.
* **NumOfActiveQueuedTasksPerNodeToGrow** – az aktív várólistára nő egy csomópont szükséges feladatok. Ha **NumOfQueuedJobsPerNodeToGrow** van megadva 0,-nál nagyobb értéket a rendszer figyelmen kívül hagyja ezt a paramétert.
* **NumOfActiveQueuedTasksToGrowThreshold** -igazodás megkezdéséhez aktív aszinkron feladatok küszöbérték száma.
* **NumOfInitialNodesToGrow** -csomópontok nő, ha a hatókör összes csomópontjának kezdeti minimális száma **nem telepített** vagy **leállítva (Deallocated)**.
* **GrowCheckIntervalMins** -időköze percben nő ellenőrzései között.
* **ShrinkCheckIntervalMins** -a időköz percben zsugorítása ellenőrzések között.
* **ShrinkCheckIdleTimes** -folyamatos száma zsugorítása ellenőrzések (elválasztott **ShrinkCheckIntervalMins**) jelzi, hogy a csomópontok üresjáratban.
* **UseLastConfigurations** -a korábbi konfigurációkat a argumentum fájlba menti.
* **ArgFile**-mentse és frissítse a parancsfájl futtatásához használja a argumentum fájl nevét.
* **LogFilePrefix** -előtag a naplófájl nevét. Egy elérési utat is megadhat. Alapértelmezés szerint a napló írni az aktuális munkakönyvtárban.

### <a name="example-1"></a>1. példa
A következő példa az alapértelmezett AzureNode sablon növelhető vagy csökkenthető automatikusan telepítik az Azure-kapacitásnövelés csomópontok konfigurál. Ha a csomópontok kezdetben a a **nem telepített** állapotba kerül, legalább 3 csomópontok indulnak el. Ha a várólistán lévő feladatok száma meghaladja a 8, a parancsfájl kezdését csomópontok az aszinkron feladatok aránya meghaladja **NumOfQueuedJobsPerNodeToGrow**. Ha egy csomópont tétlen 3 egymást követő üresjárati idő, le van állítva.

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### <a name="example-2"></a>2. példa
A következő példa az Azure számítási csomópont növelhető vagy csökkenthető, automatikusan az alapértelmezett Átjárócsomópontján sablonnal telepített virtuális gépek konfigurál.
A feladat alapértelmezett sablon által konfigurált feladatok a munkaterhelés hatókörének meghatározása a fürtön. Ha a csomópontok kezdetben le van állítva, legalább 5 csomópontok indulnak el. Aktív aszinkron feladatok száma meghaladja a 15, ha a parancsfájl kezdését csomópontokat az aktív aszinkron feladatok aránya meghaladja **NumOfActiveQueuedTasksPerNodeToGrow**. Ha egy csomópont tétlen 10 egymást követő üresjárati idő, le van állítva.

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```
