---
title: "Azure-végpont hozzáférés-vezérlési listák kezelése |} PowerShell |} Klasszikus |} Microsoft Docs"
description: "Megtudhatja, hogyan kezelheti a hozzáférés-vezérlési listákat a PowerShell használatával"
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
ms.openlocfilehash: c3476908447380ccd7e8b9c0f1c2a55ae763cc1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>A klasszikus üzembe helyezési modellel powershellel végpont hozzáférés-vezérlési listák kezelése
Hozzon létre, és kezelheti a hálózati hozzáférés-vezérlési listák (ACL) végpontok Azure PowerShell használatával vagy a felügyeleti portálon. Ebben a témakörben található eljárások a hozzáférés-vezérlési lista leggyakoribb feladatokat, amelyek a PowerShell segítségével hajthatja végre. A listán szereplő Azure PowerShell parancsmagok lásd [Azure parancsmagokat](http://go.microsoft.com/fwlink/?LinkId=317721). Hozzáférés-vezérlési listák kapcsolatos további információkért lásd: [Mi az a hálózati hozzáférés-vezérlési lista (ACL)?](virtual-networks-acl.md). Ha azt szeretné, hogy az ACL-ek kezelése a felügyeleti portál használatával kapcsolatos tudnivalókat lásd: [hogyan állítsa be végpontok egy virtuális géphez](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Hálózati hozzáférés-vezérlési listák kezelése az Azure PowerShell használatával
Azure PowerShell-parancsmagok segítségével létrehozása, törlése és beállítása (set) hálózati hozzáférés-vezérlési listák (ACL). Néhány példa a néhány módot is beállíthat egy hozzáférés-vezérlési lista PowerShell-lel azt szerepel.

A hozzáférés-vezérlési lista PowerShell-parancsmagok teljes listájának lekéréséhez az alábbiak bármelyikét használhatja:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Hozzon létre egy hálózati hozzáférés-vezérlési lista szabályokat, amelyek lehetővé teszik a hozzáférést egy távoli alhálózatból
Az alábbi példában látható módon, hogy hozzon létre egy új hozzáférés-vezérlési lista szabályokat tartalmaz egy módja. A hozzáférés-vezérlési lista alkalmazva lesz a virtuális gép végpontja. Az ACL-szabályok a következő példa engedélyezi a hozzáférést a távoli alhálózati. Hozzon létre egy új hálózati hozzáférés-vezérlési lista engedélyezési szabályainak távoli alhálózati, nyissa meg az Azure PowerShell ISE. Másolja és illessze be az alábbi, a parancsfájl konfigurálása a saját értékekkel parancsfájlt, és futtassa a parancsfájlt.

1. Hozza létre az új hálózati hozzáférés-vezérlési lista objektumot.
   
        $acl1 = New-AzureAclConfig
2. Olyan szabály, amely lehetővé teszi a hozzáférést a távoli alhálózati beállítása. Az alábbi példában a szabály beállítása *100* (amely elsőbbséget élveznek szabály 200 és újabb) engedélyezi a távoli alhálózati *10.0.0.0/8* a virtuális gép végpontjának eléréséhez. Cserélje le az értékeket a saját konfigurációs követelményeinek megfelelően. A rövid név, amelyet szeretne hívás Ez a szabály neve "SharePoint ACL-config" cserélni.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. A további szabályok ismételje meg az értékeket a saját konfigurációs követelményeinek, és cserélje le a következő parancsmag. Ügyeljen arra, hogy a szabály módosításához rendelést, hogy tükrözze a sorrendet, amelyben a szabályokat alkalmazni kívánt szám. A szabály kevesebb élvez a nagyobb számot.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. Következő lépésként hozzon létre egy új végpontot (Hozzáadás), vagy állítsa be a hozzáférés-vezérlési listája egy meglévő végpontot (Set). Ebben a példában a rendszer a "web" nevű új virtuális gép végpontjának felvétele és a virtuális gép végpontjának frissítése a hozzáférés-vezérlési lista beállításokkal.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. A következő parancsmagjait, és futtassa a parancsfájlt. Az ebben a példában a kombinált parancsmagok néz ki:
   
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

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Távolítsa el a hálózati hozzáférés-vezérlési lista szabályt, amely lehetővé teszi a hozzáférést egy távoli alhálózatból
Az alábbi példában látható módon egy hálózati ACL-szabályának eltávolítható.  Engedélyezési szabályainak távoli alhálózati hálózati hozzáférés-vezérlési lista szabály eltávolításához nyissa meg az Azure PowerShell ISE. Másolja és illessze be az alábbi, a parancsfájl konfigurálása a saját értékekkel parancsfájlt, és futtassa a parancsfájlt.

1. Első lépés a hálózati hozzáférés-vezérlési lista objektum lekérése a virtuális gép végpontja. Az ACL-szabályának távolítsa el lesz. Ebben az esetben megszüntetjük azt által szabály azonosítója. A szabály azonosítója 0 a hozzáférés-vezérlési lista csak a művelettel eltávolítja. Az ACL objektum nem távolítja el a virtuális gép végpontjának a.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. Ezt követően kell alkalmazni a hálózati hozzáférés-vezérlési lista objektum a virtuális gép végpontjának, és frissítse a virtuális gépet.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Távolítsa el a hálózati hozzáférés-vezérlési lista egy virtuális gép végpontja
Bizonyos esetekben érdemes a hálózati hozzáférés-vezérlési lista objektumok eltávolítása a virtuális gép végpontja. Ehhez nyissa meg az Azure PowerShell ISE. Másolja és illessze be az alábbi, a parancsfájl konfigurálása a saját értékekkel parancsfájlt, és futtassa a parancsfájlt.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Következő lépések
[Mi az a hálózati hozzáférés-vezérlési lista (ACL)?](virtual-networks-acl.md)

