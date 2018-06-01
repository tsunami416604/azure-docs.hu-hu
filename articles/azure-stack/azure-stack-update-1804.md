---
title: Frissítés Azure verem 1804 |} Microsoft Docs
description: További tudnivalók az Azure-verem 1804 frissítés Újdonságok integrált rendszerek, az ismert problémák és letöltéséről a frissítést.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 2c2813a7f2d909a23c8f5d4f5ac0280b3f932ba6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34700124"
---
# <a name="azure-stack-1804-update"></a>Az Azure verem 1804 frissítés

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Ez a cikk fejlesztést és 1804 frissítőcsomag, ismert problémái ebben a kiadásban, valamint a frissítés letöltése helyét. Ismert problémák a frissítési folyamat közvetlenül kapcsolatos problémák és a build (telepítés utáni) problémái vannak felosztva.

> [!IMPORTANT]        
> Ez a csomag csak integrált Azure verem rendszerek van. Ez a csomag nem vonatkoznak a Azure verem szoftverfejlesztői készlet.

## <a name="build-reference"></a>Hivatkozás létrehozása    
Az Azure verem 1804 frissítés buildszáma **20180513.1**.   

### <a name="new-features"></a>Új funkciók
A frissítés a következő fejlesztéseket Azure verem tartalmazza.

- <!-- 15028744 - IS -->  **Visual Studio support for disconnected Azure Stack deployments using AD FS**. Within Visual Studio you now can add subscriptions and authenticate using AD FS federated User credentials. 
 
- <!-- 1779474, 1779458 - IS --> **Use Av2 and F series virtual machines**. Azure Stack can now use virtual machines based on the Av2-series and F-series virtual machine sizes. For more information see [Virtual machine sizes supported in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). 

- <!-- 1759172 - IS, ASDK --> **New administrative subscriptions**. With 1804 there are two new subscription types available in the portal. These new subscription types are in addition to the Default Provider subscription and visible with new Azure Stack installations beginning with version 1804. *Do not use these new subscription types with this version of Azure Stack*. We will announce the availability to use these subscription types in with a future update. 

  Ha Azure verem 1804 verzióra frissít, a két új előfizetés-típus nem láthatók. Azonban új központi telepítéseknél Azure verem integrált rendszerek, és az Azure verem szoftverfejlesztői készlet 1804 vagy újabb verziója-telepítések fér hozzá a három előfizetéstípusok.  

  Új előfizetés típusaival az alapértelmezett szolgáltató előfizetés védelmét, és könnyebben megosztott erőforrásokhoz, mint a SQL üzemeltető kiszolgálók telepítése nagyobb változás a részét képezik. Hogy a jövőbeli frissítések nagyobb módosítás további részeinek ad Azure verem, az új előfizetés típusaival üzembe helyezett erőforrás elvesztésével járhat. 

  A most látható három előfizetés típusok a következők:  
  - Alapértelmezett szolgáltató előfizetés: továbbra is használhatja ezt az előfizetési típust. 
  - Előfizetés mérési: *ne használja ezt az előfizetési típust.*
  - Felhasználás előfizetés: *ne használjon ennél az előfizetéstípusnál*

  



## <a name="fixed-issues"></a>Javított problémák

- <!-- IS, ASDK -->  In the admin portal, you no longer have to refresh the Update tile before it displays information.
 
- <!-- 2050709  -->  You can now use the admin portal to edit storage metrics for Blob service, Table service, and Queue service.
 
- <!-- IS, ASDK --> Under **Networking**, when you click **Connection** to set up a VPN connection, **Site-to-site (IPsec)** is now the only available option.

- **Különböző javítások** teljesítmény, stabilitásának, biztonsági és az Azure-verem által használt operációs rendszer.

## <a name="additional-releases-timed-with-this-update"></a>Időtúllépés történt a frissítés további verziókban  
A következő is elérhető, de nincs szükség Azure verem frissítés 1804.
- **A Microsoft Azure verem a System Center Operations Manager figyelési csomag frissítése**. Egy új verziója (1.0.3.0) a Microsoft System Center Operations Manager figyelési csomagját Azure verem érhető el [letöltése](https://www.microsoft.com/download/details.aspx?id=55184). Ebben a verzióban Szolgáltatásnevekről egy csatlakoztatott Azure Alkalmazásveremben üzembe hozzáadásakor is használhatja. Ebben a verzióban is lehetővé teszi, hogy a javítási művelet közvetlenül belül az Operations Manager felügyelete élményt funkciókat. Új irányítópultok megjelenítése az erőforrás-szolgáltató, skálázási egységek és méretezési egység csomópontok is vannak.

- **Új Azure verem Admin PowerShell-verzió 1.3.0**.  Azure verem PowerShell 1.3.0 már elérhető a telepítéshez. Ebben a verzióban tartalmazza az összes felügyeleti erőforrás-szolgáltatók kezelése az Azure-verem parancsok.  Ezzel a kiadással néhány tartalom az Azure verem eszközök githubból elavulttá válik [tárház](https://github.com/Azure/AzureStack-Tools). 

   A telepítés részleteit, hajtsa végre a [utasításokat](azure-stack-powershell-install.md) vagy a [súgó](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) Azure verem modul 1.3.0 tartalmát. 

- **Azure verem API Rest-referencia kiadása kezdeti**. A [összes Azure-verem rendszergazdai erőforrás-szolgáltató API-referencia](https://docs.microsoft.com/rest/api/azure-stack/) most már közzé van téve. 


## <a name="before-you-begin"></a>Előkészületek    

### <a name="prerequisites"></a>Előfeltételek
- Telepítse az Azure-verem [1803 frissítése](azure-stack-update-1803.md) az Azure verem 1804 frissítés alkalmazása előtt.    

### <a name="known-issues-with-the-update-process"></a>A frissítési folyamat szolgáltatással kapcsolatos ismert problémák   
- A 1804 frissítés telepítése során láthatja a cím riasztások *hiba – a sablont a FaultType UserAccounts.New hiányzik.*  Ezek a riasztások nyugodtan figyelmen kívül hagyhatja. Ezek a riasztások automatikusan 1804 a frissítés befejezése után bezárul.   
 
- <!-- TBD - IS --> Do not attempt to create virtual machines during the installation of this update. For more information about managing updates, see [Manage updates in Azure Stack overview](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Frissítés utáni lépések
*Nincs frissítés utáni lépések frissítés 1804 van.*



### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)
Az alábbiakban telepítés utáni build kapcsolatos ismert problémák **20180513.1**.

#### <a name="portal"></a>Portál
- <!-- 1272111 - IS --> After you install or update to this version of Azure Stack, you might not be able to view Azure Stack scale units in the Admin portal.  
  Megkerülő megoldás: A PowerShell szolgáltatás használatával skálázási egység adatainak megtekintése. További információkért lásd: a [súgó](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) Azure verem modul 1.3.0 tartalmát. 

- <!-- 2332636 - IS -->  When you use AD FS for your Azure Stack identity system and update to this version of Azure Stack, the default owner of the default provider subscription is reset to the built-in **CloudAdmin** user.  
  Megkerülő megoldás: A frissítés telepítése után a probléma megoldásához használja a 3. lépés a [konfigurálása eseményindító automatizálási jogcím-szolgáltatói megbízhatósági Azure verem](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) alaphelyzetbe állítani az alapértelmezett szolgáltató előfizetés tulajdonosának eljárást.   

- <!-- TBD - IS ASDK --> Some administrative subscription types are not available.  When you upgrade Azure Stack to this version, the two subscription types that were [introduced with version 1804](#new-features) are not visible in the console. This is expected. The unavailable subscription types are *Metering subscription*, and *Consumption subscription*. These subscription types are visible in new Azure Stack environments beginning with version 1804 but are not yet ready for use. You should continue to use the *Default Provider* subscription type.  


- <!-- TBD -  IS ASDK -->The ability [to open a new support request from the dropdown](azure-stack-manage-portals.md#quick-access-to-help-and-support) from within the administrator portal isn’t available. Instead, use the following link:     
    - Azure verem integrált, rendszereihez https://aka.ms/newsupportrequest.

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![Webhely-navigációs](media/azure-stack-update-1804/breadcrumb.png) 

- <!-- TBD - IS --> It might not be possible to view compute or storage resources in the administrator portal. The cause of this issue is an error during the installation of the update that causes the update to be incorrectly reported as successful. If this issue occurs, contact Microsoft Customer Support Services for assistance.

- <!-- TBD - IS --> You might see a blank dashboard in the portal. To recover the dashboard, select the gear icon in the upper right corner of the portal, and then select **Restore default settings**.

- <!-- TBD - IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD - IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.

- <!-- TBD - IS ASDK --> In the admin portal, you might see a critical alert for the *Microsoft.Update.Admin* component. The Alert name, description, and remediation all display as:  
    - *Hiba – hiányzik a sablon FaultType ResourceProviderTimeout.*

  Ez a riasztás biztonságosan figyelmen kívül hagyhatja. 


#### <a name="health-and-monitoring"></a>Állapot- és figyelés
- <!-- 1264761 - IS ASDK -->  You might see alerts for the *Health controller* component that have the following details:  

   Riasztási #1:
   - NAME: Infrastruktúra-szerepkör nem kifogástalan
   - SÚLYOSSÁG: figyelmeztetés
   - ÖSSZETEVŐ: Rendszerállapot-vezérlő
   - Leírás: A rendszerállapot-vezérlő szívverés képolvasó nem érhető el. Ez érinthet állapotjelentések és metrikákat.  

  Riasztási #2:
   - NAME: Infrastruktúra-szerepkör nem kifogástalan
   - SÚLYOSSÁG: figyelmeztetés
   - ÖSSZETEVŐ: Rendszerállapot-vezérlő
   - Leírás: A rendszerállapot vezérlő tartalék képolvasó nem érhető el. Ez érinthet állapotjelentések és metrikákat.

  Mindkét riasztás biztonságosan figyelmen kívül hagyhatja. Ezek automatikusan megszűnik adott idő alatt.  
 

#### <a name="compute"></a>Compute
- <!-- TBD - IS --> When selecting a virtual machine size for a virtual machine deployment, some F-Series VM sizes are not visible as part of the size selector when you create a VM. The following VM sizes do not appear in the selector: *F8s_v2*, *F16s_v2*, *F32s_v2*, and *F64s_v2*.  
  Megoldás segítségével a következő módszerek egyikét a virtuális gép üzembe helyezése. Minden esetben meg kell adnia a használni kívánt Virtuálisgép-méretet.

  - **Az Azure Resource Manager-sablon:** amikor sablont használ, állítsa be a *vmSize* a sablonban, hogy egyezzen a kívánt Virtuálisgép-méretet. Például a következő központi telepítéséhez használandó virtuális gép által használt a *F32s_v2* mérete:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Az Azure CLI:** használhatja a [az virtuális gép létrehozása](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) parancsot, és adja meg a Virtuálisgép-méretet a paramétert, hasonló `--size "Standard_F32s_v2"`.

  - **PowerShell:** a PowerShell használatával is használhat [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) paraméter meghatározza, hogy a virtuális gép méretét, hasonló az `-VMSize "Standard_F32s_v2"`.


- <!-- TBD - IS ASDK --> Scaling settings for virtual machine scale sets are not available in the portal. As a workaround, you can use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Because of PowerShell version differences, you must use the `-Name` parameter instead of `-VMScaleSetName`.

- <!-- TBD - IS --> When you create an availability set in the portal by going to **New** > **Compute** > **Availability set**, you can only create an availability set with a fault domain and update domain of 1. As a workaround, when creating a new virtual machine, create the availability set by using PowerShell, CLI, or from within the portal.

- <!-- TBD - IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach to a D series VM. All supported D series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD - IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  A probléma megoldásához hozzon létre egy új Virtuálisgép-lemezkép, amely a Hyper-V segítségével hozhatók létre üres virtuális merevlemez (New-VHD-elérési út C:\dummy.vhd-- SizeBytes rögzített 1 GB-os). Ez a folyamat kell hárítsa el a problémát, amely megakadályozza a sikertelen elem törlése. Ezt követően a dummy lemezkép létrehozása után 15 perc sikeresen törlése.

  Ezután próbálkozzon redownload, amely korábban nem sikerült Virtuálisgép-lemezkép.

- <!-- TBD - IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  


#### <a name="networking"></a>Hálózat
- <!-- 1766332 - IS ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 - IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  Jelenleg létrehozhat új virtuális gépek csak új nyilvános IP-címet kell használnia.

  Ez akkor fordul elő, akkor is, ha egy új virtuális géphez az IP-cím ismételt hozzárendelése (más néven a *virtuális IP-címcsere*). Az összes jövőbeni megpróbál a kapcsolaton keresztül a eredetileg társított virtuális Gépet, és nem egy IP-cím eredményén keresztül kapcsolódni.

- <!-- 2292271 - IS ASDK --> If you raise a Quota limit for a Network resource that is part of an Offer and Plan that is associated with a tenant subscription, the new limit is not applied to that subscription. However, the new limit does apply to new subscriptions that are created after the quota is increased. 

  Ez a probléma megoldása érdekében egy bővítmény csomag használata a hálózati kvóta növeléséhez, ha a csomag már társítva van egy előfizetést. További információkért lásd: hogyan [bővítmény tervének elérhetővé](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> You cannot delete a subscription that has DNS Zone resources or Route Table resources associated with it. To successfully delete the subscription, you must first delete DNS Zone and Route Table resources from the tenant subscription. 
  

- <!-- 1902460 - IS ASDK --> Azure Stack supports a single *local network gateway* per IP address. This is true across all tenant subscriptions. After the creation of the first local network gateway connection, subsequent attempts to create a local network gateway resource with the same IP address are blocked.

- <!-- 16309153 - IS ASDK --> On a Virtual Network that was created with a DNS Server setting of *Automatic*, changing to a custom DNS Server fails. The updated settings are not pushed to VMs in that Vnet.

- <!-- TBD - IS ASDK --> Azure Stack does not support adding additional network interfaces to a VM instance after the VM is deployed. If the VM requires more than one network interface, they must be defined at deployment time.

- <!-- 2096388 IS --> You cannot use the admin portal to update rules for a network security group. 

    Az App Service megkerülő megoldás: a távoli asztal a vezérlő példányokra van szüksége, ha módosítja a biztonsági szabályok belül a hálózati biztonsági csoportok a PowerShell használatával.  Az alábbiakban példákat *engedélyezése*, majd állítsa vissza a konfiguráció *megtagadása*:  
    
    - *Engedélyezése:*
 
      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn’t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
      
      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *Megtagadása:*

        ```powershell
        
        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn’t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
          
        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg 
        ```

#### <a name="sql-and-mysql"></a>Az SQL és MySQL

- <!-- TBD - IS --> Only the resource provider is supported to create items on servers that host SQL or MySQL. Items created on a host server that are not created by the resource provider might result in a mismatched state.  

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** or **Tier** names when you create a SKU for the SQL and MySQL resource providers.


> [!NOTE]  
> <!-- TBD - IS --> After you update to Azure Stack 1804, you can continue to use the SQL and MySQL resource providers that you previously deployed.  We recommend you update SQL and MySQL when a new release becomes available. Like Azure Stack, apply updates to SQL and MySQL resource providers sequentially.  For example, if you use version 1802, first apply version 1803, and then update to 1804.      
>   
> Frissítés 1804 telepítése nem befolyásolja a felhasználók által MySQL vagy az SQL-erőforrás-szolgáltató aktuális használatát.
> Az erőforrás-szolgáltató használata verziója, függetlenül a felhasználók adatait, a hozzájuk tartozó adatbázisok nincs megszáradásukig, és elérhető marad.    



#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- TBD - IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.

- <!-- TBD - IS ASDK --> App Service can only be deployed into the **Default Provider Subscription** at this time.  In a future update App Service will deploy into the new Metering Subscription introduced in Azure Stack 1804 and all existing deployments will be migrated to this new subscription also.

#### <a name="usage"></a>Használat  
- <!-- TBD - IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>A frissítés letöltése
Letöltheti az Azure verem 1804 frissítési csomagot [Itt](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Lásd még
- A privilegizált végpont (EGP) segítségével figyelheti, és folytassa a frissítést, lásd: [figyelése Azure verem használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).
- A frissítés-kezelés az Azure-verem áttekintését lásd: [kezelheti a frissítéseket az Azure-verem áttekintés](azure-stack-updates.md).
- Az Azure veremnek megfelelő frissítések alkalmazásával kapcsolatos további információkért lásd: [Azure verem frissítések alkalmazása](azure-stack-apply-updates.md).
