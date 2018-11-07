---
title: Azure-végpont hozzáférés-vezérlési listák felügyelete |} PowerShell |} Klasszikus |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti a hozzáférés-vezérlési listák a PowerShell-lel
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: c84e40af-f351-4572-b3f0-d572d46bafe7
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: 1fce5b98d9e12ad373a4ca9d851fb717b3f47045
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250356"
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>PowerShell-lel a klasszikus üzemi modellben végponti hozzáférés-vezérlési listák felügyelete
Hozzon létre, és kezelheti a hálózati hozzáférés-vezérlési listák (ACL) végpontok Azure PowerShell-lel vagy a felügyeleti portálon. Ebben a témakörben eljárásokat találhat a gyakori feladatokhoz ACL, amely a PowerShell használatával is elvégezheti. A lista az Azure PowerShell parancsmagok megtekintéséhez [Azure Management Cmdlets](https://go.microsoft.com/fwlink/?LinkId=317721). ACL-lel kapcsolatos további információkért lásd: [Mi az a hálózati hozzáférés-vezérlési lista (ACL)?](virtual-networks-acl.md). Ha azt szeretné, a hozzáférés-vezérlési listák felügyelete a felügyeleti portál használatával, lásd: [hogyan állítsa be végpontok egy virtuális géphez](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Hálózati hozzáférés-vezérlési listák felügyelete az Azure PowerShell-lel
Azure PowerShell-parancsmagok segítségével hozzon létre, eltávolításához és konfigurálásához (set) hálózati hozzáférés-vezérlési listák (ACL). Néhány példa a több szempontból is beállíthat egy ACL-lel bővítettük.

Az ACL-t a PowerShell-parancsmagok teljes listájának lekéréséhez a következők bármelyikét használhatja:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Hozzon létre egy hálózati ACL szabályok, amelyek engedélyezik a hozzáférést a távoli alhálózatokról
Az alábbi példában látható létrehozhat egy új ACL-t olyan szabályokat tartalmaz. Egy virtuális gép végpontjának rendszer ezután alkalmazza az ACL-t. Az ACL-szabályok az alábbi példában engedélyezi a hozzáférést a távoli alhálózatokról. Hozzon létre egy új hálózati hozzáférés-szabályozási engedélyezési szabályainak távoli alhálózat, nyissa meg az Azure PowerShell ISE-ben. Másolja és illessze be az alábbi, a parancsfájl konfigurálása a saját értékeire parancsfájlt, és futtassa a szkriptet.

1. Hozzon létre új hálózati hozzáférés-szabályozási objektum.
   
        $acl1 = New-AzureAclConfig
2. Állítsa be egy szabályt, amely lehetővé teszi a hozzáférés távoli alhálózatokról. Az alábbi példában a szabály beállítása *100* (amely elsőbbséget élveznek szabály 200 és újabb), hogy a távoli alhálózaton *10.0.0.0/8* a virtuális gép végpontjának a hozzáférést. Cserélje le az értékeket a saját konfigurációs követelményeinek. A neve "SharePoint-ACL-config" kell helyettesíteni szeretné meghívni, ez a szabály valódi név.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. Kiegészítő szabályok esetén ismételje meg a parancsmagot, és cserélje le az értékeket a saját konfigurációs követelményeinek. Ügyeljen arra, hogy a szabály módosításához, ahhoz, hogy a szabályok a alkalmazni kívánt szám. A szabály kevesebb élvez a nagyobb számra.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. Következő lépésként hozzon létre egy új végpontot (Hozzáadás), vagy állítsa be a hozzáférés-Vezérlési egy meglévő végpontot (Set). Ebben a példában azt fogja adjon hozzá egy új virtuális gép végpontjának "web" néven, és frissítse a virtuális gép végpontjának az ACL-beállítások.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. Ezután parancsmagjait kombinálva, és futtassa a szkriptet. Ebben a példában a kombinált parancsmagok következő lenne:
   
        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "Sharepoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Hálózati ACL-szabály, amely lehetővé teszi a távoli alhálózatokról hozzáférés eltávolítása
Az alábbi példa egy hálózati ACL-szabályának eltávolítása úgy mutatja be.  Az engedélyezési szabályainak távoli alhálózati hálózati ACL-szabály eltávolításához nyissa meg az Azure PowerShell ISE-ben. Másolja és illessze be az alábbi, a parancsfájl konfigurálása a saját értékeire parancsfájlt, és futtassa a szkriptet.

1. A hálózati hozzáférés-szabályozási objektum beolvasni a virtuális gép végpontjának első lépéseként. Az ACL-szabályt, majd eltávolítja. Ebben az esetben megszüntetjük, a szabály által A szabály azonosítója 0 az ACL csak a művelettel eltávolítja. Az ACL-objektum nem távolítja el a virtuális gép végpontról.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. Ezután kell a hálózati hozzáférés-szabályozási objektum alkalmazása a virtuális gép végpontjának, és frissítse a virtuális gépet.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Egy hálózati ACL eltávolítása egy virtuális gép végpontja
Bizonyos esetekben érdemes távolítsa el a hálózati hozzáférés-szabályozási objektum egy virtuális gép végpontját. Ehhez nyissa meg az Azure PowerShell ISE-ben. Másolja és illessze be az alábbi, a parancsfájl konfigurálása a saját értékeire parancsfájlt, és futtassa a szkriptet.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>További lépések
[Mi az a hálózati hozzáférés-vezérlési lista (ACL)?](virtual-networks-acl.md)

