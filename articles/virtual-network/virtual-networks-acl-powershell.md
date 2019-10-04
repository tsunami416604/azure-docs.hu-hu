---
title: Azure-végpontok hozzáférés-vezérlési jegyzékeinek kezelése | PowerShell | Klasszikus | Microsoft Docs
description: Ismerje meg, hogyan kezelheti az ACL-eket a PowerShell használatával
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.assetid: c84e40af-f351-4572-b3f0-d572d46bafe7
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 4fe8349863c16a15886f9f1d33056f0bbfec31f2
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056769"
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>Végpontok hozzáférés-vezérlési listájainak kezelése a PowerShell használatával a klasszikus üzemi modellben
A végpontok számára a Azure PowerShell vagy a felügyeleti portál használatával hozhat létre és kezelhet hálózati Access Control listákat (ACL-eket). Ebben a témakörben megtalálhatja a PowerShell használatával elvégezhető általános ACL-feladatok műveleteit. Azure PowerShell-parancsmagok listájáért lásd: [Azure felügyeleti parancsmagok](https://go.microsoft.com/fwlink/?LinkId=317721). Az ACL-ekkel kapcsolatos további információkért lásd: [Mi az a hálózati Access Control lista (ACL)?](virtual-networks-acl.md). Ha az ACL-eket a felügyeleti portál használatával szeretné kezelni, tekintse meg a [végpontok virtuális géphez való beállítását](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)ismertető témakört.

## <a name="manage-network-acls-by-using-azure-powershell"></a>Hálózati ACL-ek kezelése Azure PowerShell használatával
Azure PowerShell parancsmagok segítségével hozhatja létre, távolíthatja el és konfigurálhatja (beállíthatja) a hálózati Access Control listákat (ACL-eket). Néhány példát mutatunk be arra, hogyan konfigurálhat egy ACL-t a PowerShell használatával.

Az ACL PowerShell-parancsmagok teljes listájának beolvasásához a következők bármelyikét használhatja:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Hálózati ACL létrehozása olyan szabályokkal, amelyek engedélyezik a hozzáférést egy távoli alhálózatról
Az alábbi példa bemutatja, hogyan hozhat létre olyan új ACL-t, amely szabályokat tartalmaz. Ezt az ACL-t a rendszer egy virtuálisgép-végpontra alkalmazza. Az alábbi példában szereplő ACL-szabályok távoli alhálózatról is engedélyezik a hozzáférést. Ha új hálózati ACL-t szeretne létrehozni egy távoli alhálózat engedélyezési szabályaival, nyisson meg egy Azure PowerShell ISE-t. Másolja és illessze be az alábbi szkriptet, konfigurálja a parancsfájlt a saját értékeivel, majd futtassa a parancsfájlt.

1. Hozza létre az új hálózati ACL-objektumot.
   
        $acl1 = New-AzureAclConfig
2. Olyan szabályt állít be, amely engedélyezi a hozzáférést egy távoli alhálózatról. Az alábbi példában be kell állítania a *100* -as szabályt (amely a 200-es és annál magasabb szintű prioritással rendelkezik) ahhoz, hogy a távoli alhálózat *10.0.0.0/8* hozzáférést kapjon a virtuális gép végpontja számára. Cserélje le az értékeket a saját konfigurációs követelményeire. A "SharePoint ACL-konfiguráció" nevet a szabályt meghívni kívánó rövid névvel kell helyettesíteni.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. További szabályokért ismételje meg a parancsmagot, és cserélje le az értékeket a saját konfigurációs követelményeire. Ügyeljen arra, hogy módosítsa a szabály sorszámát, hogy tükrözze a szabályok alkalmazásának sorrendjét. Az alsó szabály száma elsőbbséget élvez a nagyobb számmal szemben.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. Ezután létrehozhat egy új végpontot (Hozzáadás), vagy megadhatja egy meglévő végpont (set) ACL-t. Ebben a példában egy új, "web" nevű virtuálisgép-végpontot fogunk felvenni, és a virtuális gép végpontját a ACL-beállításokkal frissíti.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. Ezután egyesítse a parancsmagokat, és futtassa a szkriptet. Ebben a példában az egyesített parancsmagok a következőképpen néznek ki:
   
        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Távoli alhálózat elérését lehetővé tevő hálózati ACL-szabály eltávolítása
Az alábbi példa azt szemlélteti, hogyan lehet eltávolítani egy hálózati ACL-szabályt.  Egy távoli alhálózat engedélyezési szabályaival rendelkező hálózati ACL-szabály eltávolításához nyisson meg egy Azure PowerShell ISE-t. Másolja és illessze be az alábbi szkriptet, konfigurálja a parancsfájlt a saját értékeivel, majd futtassa a parancsfájlt.

1. Első lépésként szerezze be a virtuális gép végpontjának hálózati ACL-objektumát. Ezután eltávolítja az ACL-szabályt. Ebben az esetben a szabály azonosítója alapján távolítjuk el. Ez csak a 0. AZONOSÍTÓJÚ szabályt törli az ACL-ből. Nem távolítja el az ACL-objektumot a virtuális gép végpontján.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. Ezután a hálózati ACL-objektumot a virtuálisgép-végpontra kell alkalmaznia, és frissítenie kell a virtuális gépet.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Hálózati hozzáférés-vezérlési lista eltávolítása egy virtuálisgép-végpontból
Bizonyos esetekben előfordulhat, hogy el szeretné távolítani egy hálózati ACL-objektumot egy virtuálisgép-végpontról. Ehhez nyisson meg egy Azure PowerShell ISE-t. Másolja és illessze be az alábbi szkriptet, konfigurálja a parancsfájlt a saját értékeivel, majd futtassa a parancsfájlt.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>További lépések
[Mi az a hálózati Access Control lista (ACL)?](virtual-networks-acl.md)

