---
title: Lemezek titkosítása egy Windows rendszerű virtuális gépen az Azure-ban | Microsoft Docs
description: Virtuális lemezek titkosítása a Windowsos virtuális GÉPEN fokozott biztonság céljából Azure PowerShell használatával
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 87777d3a6abfeaeac74fd69126cc3e71e11be825
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597858"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Virtuális lemezek titkosítása egy Windows rendszerű virtuális GÉPEN
A virtuális gépek (VM) biztonsága és megfelelősége érdekében az Azure-ban található virtuális lemezek titkosíthatók. A lemezek titkosítása egy Azure Key Vault védett titkosítási kulcsok használatával történik. Ezeket a titkosítási kulcsokat szabályozhatja, és naplózhatja a használatukat. Ez a cikk azt ismerteti, hogyan titkosíthatja a virtuális lemezeket egy Windows rendszerű virtuális GÉPEN Azure PowerShell használatával. Linux rendszerű [virtuális gépek titkosítása](../linux/disk-encryption-overview.md)is végezhető.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="overview-of-disk-encryption"></a>A lemezek titkosításának áttekintése
A Windows rendszerű virtuális gépeken futó virtuális lemezek a BitLocker használatával titkosítva vannak. A virtuális lemezek titkosítása az Azure-ban díjmentes. A titkosítási kulcsokat a szoftveres védelem Azure Key Vault tárolja, vagy importálhatja vagy létrehozhatja a kulcsokat az FIPS 140-2 2. szintű standard minősítésű hardveres biztonsági modulokban (HSM). A titkosítási kulcsok a virtuális GÉPHEZ csatolt virtuális lemezek titkosítására és visszafejtésére szolgálnak. Ezeket a titkosítási kulcsokat továbbra is felügyelheti, és naplózhatja a használatukat. 

A virtuális gépek titkosításának folyamata a következő:

1. Titkosítási kulcs létrehozása Azure Key Vaultban.
1. Konfigurálja a titkosítási kulcsot a lemezek titkosítására való használatra.
1. Engedélyezze a lemez titkosítását a virtuális lemezek számára.
1. A rendszer a szükséges titkosítási kulcsokat Azure Key Vault kéri.
1. A virtuális lemezek titkosítása a megadott titkosítási kulccsal történik.


## <a name="requirements-and-limitations"></a>Követelmények és korlátozások

A lemez titkosításával kapcsolatos támogatott forgatókönyvek és követelmények:

* Az Azure Marketplace-lemezképekről vagy egyéni VHD-lemezképekről származó új Windows-alapú virtuális gépek titkosításának engedélyezése.
* Titkosítás engedélyezése meglévő Windows rendszerű virtuális gépeken az Azure-ban.
* A titkosítás engedélyezése a tárolóhelyek használatával konfigurált Windows rendszerű virtuális gépeken.
* Az operációs rendszer és az adatmeghajtók titkosításának letiltása a Windows rendszerű virtuális gépeken.
* Standard szintű virtuális gépek, például A, D, DS, G és GS sorozatú virtuális gépek.

    > [!NOTE]
    > Az összes erőforrásnak (beleértve a Key Vault, a Storage-fiókot és a virtuális gépet) ugyanabban az Azure-régióban és-előfizetésben kell lennie.

A lemez titkosítása jelenleg nem támogatott a következő esetekben:

* Alapszintű virtuális gépek.
* A klasszikus üzemi modell használatával létrehozott virtuális gépek.
* A titkosítási kulcsok frissítése egy már titkosított virtuális gépen.
* Integráció a helyszíni kulcskezelő szolgáltatással.


## <a name="create-an-azure-key-vault-and-keys"></a>Azure Key Vault és kulcsok létrehozása
Mielőtt elkezdené, győződjön meg arról, hogy a Azure PowerShell modul legújabb verziója telepítve van. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál. Az alábbi parancs példákban cserélje le az összes példa paramétert a saját nevekre, helyekre és kulcsokra, például *myResourceGroup*, *myKeyVault*, *myVM*és így tovább.

Első lépésként létre kell hoznia egy Azure Key Vault a titkosítási kulcsok tárolásához. Az Azure Key Vaultok olyan kulcsokat, titkokat vagy jelszavakat tárolhatnak, amelyek lehetővé teszik az alkalmazások és szolgáltatások biztonságos megvalósítását. Virtuális lemezek titkosításához létre kell hoznia egy Key Vault a virtuális lemezek titkosításához vagy visszafejtéséhez használt titkosítási kulcs tárolásához. 

Engedélyezze a Azure Key Vault szolgáltatót az Azure-előfizetésen belül a [Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider), majd hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). A következő példában létrehozunk egy *myResourceGroup* nevű ERŐFORRÁSCSOPORTOT az *USA keleti* régiójában:

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

A titkosítási kulcsokat és a hozzájuk társított számítási erőforrásokat (például a tárterületet és a virtuális gépet) tároló Azure Key Vault csak ugyanabban a régióban kell lennie. Hozzon létre egy Azure Key Vaultt a [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) , és engedélyezze a Key Vault a lemezes titkosítással való használatra. Adja meg a *keyVaultName* egyedi Key Vault nevét a következőképpen:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

A titkosítási kulcsokat a szoftveres vagy a hardveres biztonsági modell (HSM) elleni védelem használatával is tárolhatja.  A standard Key Vault csak A szoftveres védelemmel ellátott kulcsokat tárolja. A HSM használatával felár ellenében prémium Key Vault szükséges. Prémium szintű Key Vault létrehozásához az előző lépésben adja hozzá a *-SKU "Prémium"* paramétert. A következő példa szoftveres védelemmel ellátott kulcsokat használ a standard Key Vault létrehozása óta. 

Mindkét védelmi modell esetében az Azure platformnak hozzáférést kell biztosítania a titkosítási kulcsok igényléséhez, amikor a virtuális gép elindítja a virtuális lemezek visszafejtését. Hozzon létre egy titkosítási kulcsot a Key Vault az [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey). A következő példa egy *myKey*nevű kulcsot hoz létre:

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
A titkosítási folyamat teszteléséhez hozzon létre egy új virtuális gépet a [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Az alábbi példa egy *myVM* nevű virtuális gépet hoz létre egy *Windows Server 2016 Datacenter* -rendszerkép használatával. Amikor a rendszer kéri a hitelesítő adatokat, adja meg a virtuális géphez használni kívánt felhasználónevet és jelszót:

```azurepowershell-interactive
$cred = Get-Credential

New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-a-virtual-machine"></a>Virtuális gép titkosítása
Titkosítsa a virtuális gépet a [set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) a Azure Key Vault kulcs használatával. Az alábbi példa lekéri az összes fontos információt, majd titkosítja a *myVM*nevű virtuális gépet:

```azurepowershell-interactive
$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Fogadja el a parancssort a virtuális gép titkosításának folytatásához. A virtuális gép újraindul a folyamat során. Miután a titkosítási folyamat befejeződik, és a virtuális gép újraindult, tekintse át a titkosítási állapotot a [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus):

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

A kimenet a következő példához hasonló:

```azurepowershell-interactive
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Következő lépések
* Az Azure Key Vault kezelésével kapcsolatos további információkért lásd: [Key Vault beállítása virtuális gépekhez](key-vault-setup.md).
* További információ a lemezek titkosításáról, például egy titkosított egyéni virtuális gép Azure-ba való feltöltésének előkészítéséről: [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).
