---
title: HPC Pack számítási fürtcsomópontok kezelése |} Microsoft Docs
description: PowerShell parancsfájl eszközei hozzáadásához távolítsa el, indítsa el, és állítsa le a számítási fürtcsomópontok HPC Pack 2012 R2-ben az Azure-ban
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 4193f03b-94e9-4704-a7ad-379abde063a9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: 453f53be15b24b96f183b4935cc45fc97ad058bd
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="manage-the-number-and-availability-of-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Az Azure-beli HPC Pack-fürtökben lévő számítási csomópontok számának és elérhetőségének kezelése
Ha létrehozott egy HPC Pack 2012 R2-fürt az Azure virtuális gépeken, érdemes lehet könnyen hozzáadása, eltávolítása, (kiépíteni) indítása vagy leállítása (deprovision) módjai néhány számítási csomópont virtuális gépek a fürt. Ezek a feladatok elvégzéséhez az átjárócsomóponthoz VM telepített Azure PowerShell-parancsfájlok futtatása. Ezek a parancsfájlok segítségével szabályozhatja, számát és a HPC Pack fürterőforrások rendelkezésre állását, szabályozhatja költségeit.

> [!IMPORTANT] 
> Ez a cikk csak az Azure klasszikus telepítési modellel készült HPC Pack 2012 R2 fürtök vonatkozik. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.
> A jelen cikkben ismertetett PowerShell-parancsfájlok emellett HPC Pack 2016 nem érhetők el.

## <a name="prerequisites"></a>Előfeltételek
* **Az Azure virtuális gépeken HPC Pack 2012 R2-fürt**: egy HPC Pack 2012 R2-fürt létrehozása a klasszikus üzembe helyezési modellben. Például a központi telepítés automatizálhatja az Azure piactéren, és az Azure PowerShell parancsfájlt a HPC Pack 2012 R2 Virtuálisgép-lemezkép használatával. További információkat és előfeltételeket: [HPC-fürt létrehozása a HPC Pack IaaS telepítési parancsfájl](hpcpack-cluster-powershell-script.md).
  
    A központi telepítést követően talál a csomópont felügyeleti parancsfájlok % CCP\_OTTHONI % bin mappájában az átjárócsomópont. Futtassa rendszergazdaként a parancsfájlok mindegyikének.
* **Azure Közzététel beállításai fájl vagy a felügyeleti tanúsítvány**: végre kell hajtani a head csomóponton a következők egyikét:
  
  * **Közzététele beállításfájl importálása az Azure**. Ehhez futtassa a következő Azure PowerShell-parancsmagok az átjárócsomóponthoz:
    
    ```PowerShell
    Get-AzurePublishSettingsFile
    
    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```
  * **Az Azure felügyeleti tanúsítvány konfigurálása az átjárócsomópont**. Ha a .cer fájlt, importálja azt a CurrentUser\My tanúsítványtárolójába, és futtassa a következő Azure PowerShell-parancsmaggal (AzureCloud vagy AzureChinaCloud) az Azure környezetben:
    
    ```PowerShell
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My\<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## <a name="add-compute-node-vms"></a>Virtuális gépek számítási csomópont hozzáadása
A számítási csomópontok hozzáadása a **Add-HpcIaaSNode.ps1** parancsfájl.

### <a name="syntax"></a>Szintaxis
```PowerShell
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### <a name="parameters"></a>Paraméterek
* **Szolgáltatásnév**: a felhőalapú szolgáltatás, amely új számítási csomópont virtuális gépek neve kerülnek.
* **ImageName**: Azure virtuális gép lemezképének nevét, amely a Azure-portálon vagy az Azure PowerShell-parancsmag használatával érhető el **Get-AzureVMImage**. A kép a következő követelményeknek kell megfelelniük:
  
  1. A Windows operációs rendszert kell telepíteni.
  2. A számítási csomópont szerepkör HPC Pack kell telepíteni.
  3. A kép a felhasználói kategória nem egy nyilvános Azure Virtuálisgép-lemezkép titkos képnek kell lennie.
* **Mennyiség**: számítási csomópont lehet hozzáadni a virtuális gépek száma.
* **InstanceSize**: a számítási csomópont virtuális gépek méretét.
* **DomainUserName**: tartományi felhasználó nevét, amely az új virtuális gépek csatlakoztatása a tartományhoz.
* **DomainUserPassword**: a tartományi felhasználó jelszavát.
* **NodeNameSeries** (nem kötelező): a számítási csomópontok elnevezési mintát. A következő formátumban kell megadni &lt; *legfelső szintű\_neve*&gt;&lt;*Start\_szám*&gt;%. Például MyCN % 10 % azt jelenti, hogy egy sor kiindulva MyCN11 számítási csomópont nevét. Ha nincs megadva, a parancsfájl a konfigurált csomópont elnevezési adatsorozat a HPC-fürtben.

### <a name="example"></a>Példa
A következő példa 20 mérete nagy számítási csomópont virtuális gépeket ad a felhőszolgáltatásban *hpcservice1*a Virtuálisgép-lemezkép alapján *hpccnimage1*.

```PowerShell
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## <a name="remove-compute-node-vms"></a>Távolítsa el a virtuális gépek számítási csomópont
A számítási csomópontok eltávolítása a **Remove-HpcIaaSNode.ps1** parancsfájl.

### <a name="syntax"></a>Szintaxis
```PowerShell
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### <a name="parameters"></a>Paraméterek
* **Név**: eltávolítandó fürtcsomópont nevét. Helyettesítő karakterek használata támogatott. A paraméter nevének neve. Nem adható meg egyszerre a **neve** és **csomópont** paraméterek.
* **Csomópont**: a csomópontok távolítható el, amelyen keresztül a HPC PowerShell-parancsmag érhető el a HpcNode objektum [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). A paraméter nevének a csomópont. Nem adható meg egyszerre a **neve** és **csomópont** paraméterek.
* **DeleteVHD** (nem kötelező): a beállítást, ha a kapcsolódó lemezek törlése a virtuális gépek eltávolításra.
* **Kényszerített** (nem kötelező): kapcsolat nélküli HPC-csomópontok kényszerítése előtt távolítsa el a beállítást.
* **Erősítse meg** (nem kötelező): a parancs végrehajtása előtt kérése.
* **WhatIf**: beállítás leírására, hogy mi történne a parancs hajtja végre ténylegesen a parancs végrehajtása nélkül.

### <a name="example"></a>Példa
Az alábbi példa kényszeríti a kapcsolat nélküli a csomópontok kezdődő nevű *HPCNode-CN -* és azok eltávolítja a csomópontokon és a társított lemezekkel.

```PowerShell
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## <a name="start-compute-node-vms"></a>Indítsa el a virtuális gépek számítási csomópont
Kezdő számítási csomópont a **Start-HpcIaaSNode.ps1** parancsfájl.

### <a name="syntax"></a>Szintaxis
```PowerShell
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### <a name="parameters"></a>Paraméterek
* **Név**: a fürtcsomópont nevét kell elindítani. Helyettesítő karakterek használata támogatott. A paraméter nevének neve. Nem adható meg egyszerre a **neve** és **csomópont** paraméterek.
* **Csomópont**-kell elindítani, a csomópontokat, amelyeknek a HPC PowerShell-parancsmaggal érhető el a HpcNode objektumot [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). A paraméter nevének a csomópont. Nem adható meg egyszerre a **neve** és **csomópont** paraméterek.

### <a name="example"></a>Példa
Az alábbi példa csomópontok kezdődik kezdődő nevek *HPCNode-CN -*.

```PowerShell
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## <a name="stop-compute-node-vms"></a>Állítsa le a virtuális gépek számítási csomópont
Állítsa le a számítási csomópontok a **Stop-HpcIaaSNode.ps1** parancsfájl.

### <a name="syntax"></a>Szintaxis
```PowerShell
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### <a name="parameters"></a>Paraméterek
* **Név**-leállt a fürtcsomópont nevét. Helyettesítő karakterek használata támogatott. A paraméter nevének neve. Nem adható meg egyszerre a **neve** és **csomópont** paraméterek.
* **Csomópont**: a csomópontok leállítását, amelyen keresztül a HPC PowerShell-parancsmag érhető el a HpcNode objektum [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). A paraméter nevének a csomópont. Nem adható meg egyszerre a **neve** és **csomópont** paraméterek.
* **Kényszerített** (nem kötelező): a beállítást, ha a kényszerített offline HPC-csomópontok azokat leállítása előtt.

### <a name="example"></a>Példa
Az alábbi példa arra kényszeríti a kapcsolat nélküli csomópontok kezdődő nevű *HPCNode-CN -* és megszűnik a csomópontok.

```PowerShell
Stop-HPCIaaSNode.ps1 –Name HPCNodeCN-* -Force
```

## <a name="next-steps"></a>További lépések
* Automatikusan nő, és a fürt csomópontjai az aktuális terhelést a feladatok és a feladatokat a fürt megfelelően csökkenhet, lásd: [automatikusan növelhető, vagy az Azure-ban a HPC Pack fürterőforrások csökkenthető a fürtmunkaterhelés megfelelően](hpcpack-cluster-node-autogrowshrink.md).

