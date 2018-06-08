---
title: A Microsoft Azure verem szoftverfejlesztői készlet kibocsátási megjegyzései |} Microsoft Docs
description: Fejlesztések, javítások és Azure verem szoftverfejlesztői készlet kapcsolatos ismert problémák.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: 185cd6f20cdb19531777ccb58e72be14ea656549
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850120"
---
# <a name="azure-stack-development-kit-release-notes"></a>Az Azure verem szoftverfejlesztői készlet kibocsátási megjegyzései  
A kibocsátási megjegyzések fejlesztései, javítások és Azure verem szoftverfejlesztői készlet ismert problémáira vonatkozó információkat tartalmazzák. Ha nem biztos abban, hogy melyik verzióját futtatja, akkor [a portál használatával ellenőrizze](.\.\azure-stack-updates.md#determine-the-current-version).

> Naprakész legyen what's new in a ASDK előfizet a [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [hírcsatorna](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11805147"></a>Build 1.1805.1.47

> [!TIP]  
> A felhasználói visszajelzések alapján van a Microsoft Azure verem használható verzió séma frissítése. A frissítés, 1805, kezdve az új sémával jobban jelöli felhő verziószámának.  
> 
> A verzió séma már *Version.YearYearMonthMonth.MinorVersion.BuildNumber* ahol a második és harmadik készletek jelzi a verziója és kiadása. Például 1805.1 jelöli a *RTM kiadási* 1805 (RTM) verziójára.  


### <a name="new-features"></a>Új funkciók 
A build tartalmazza a következő fejlesztéseket és javításokat Azure verem.  

- <!-- 2297790 - IS, ASDK --> **Azure Stack now includes a *Syslog* client** as a *preview feature*. This client allows the forwarding of audit and security logs related to the Azure Stack infrastructure to a Syslog server or security information and event management (SIEM) software that is external to Azure Stack. Currently, the Syslog client only supports unauthenticated UDP connections over default port 514. The payload of each Syslog message is formatted in Common Event Format (CEF). 

  A Syslog-ügyfél konfigurálásához használja a **Set-SyslogServer** teszik ki, a kiemelt végpont parancsmag. 

  Ebben az előzetes jelenhet meg a következő három riasztásokat. Amikor Azure verem, ezek a riasztások tartalmazzák *leírások* és *szervizelési* útmutatást. 
  - CÍM: Kódintegritás kikapcsolása  
  - CÍM: Kódintegritás a rendszervizsgálati módban 
  - CÍM: Felhasználói fiók létrehozása

  Amíg ez a funkció jelenleg előzetes verzióban érhető, azt kell nem érvényesíthetők éles környezetben.   


### <a name="fixed-issues"></a>Javított problémák

- **Különböző javítások** teljesítmény, stabilitásának, biztonsági és az Azure-verem által használt operációs rendszer


<!-- ### Changes  --> 


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Ismert problémák
 
#### <a name="portal"></a>Portál
- <!-- 2551834 - IS, ASDK --> When you select **Overview** for a storage account in either the admin or user portals, the information from the *Essentials* pane does not display.  The Essentials pane displays information about the account like its *Resource group*, *Location*, and *Subscription ID*.  Other options for Overview  are accessible, like *Services* and *Monitoring*, as well as options to *Open in Explorer* or to *Delete storage account*.  

  A nem érhető el információ megtekintéséhez használja a [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) PowerShell-parancsmagot. 

- <!-- TBD - IS ASDK --> Do not use the new administrative subscription types of *Metering subscription*, and *Consumption subscription*. These new subscription types were introduced with version 1804 but are not yet ready for use. You should continue to use the *Default Provider* subscription type.  

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![Webhely-navigációs](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD -  IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.


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

  Mindkét riasztás biztonságosan figyelmen kívül hagyható, és idővel automatikusan bezáródik.  

- <!-- 2392907 – ASDK -->   You might see a *critical* alert for **Low memory capacity**. This alert has the following description: *The region has consumed more than 95.00 % of available memory. Creating virtual machines with large amounts of memory may fail.*

  Ez a riasztás Azure verem helytelenül fiókjainak memória használata az Azure-verem szoftverfejlesztői készlet a lehet létrehozni.  

  Ez a riasztás figyelmen kívül hagyhatja, és a probléma nem befolyásolja a virtuális gépek elhelyezésére. 

- <!-- 2368581 - IS. ASDK --> An Azure Stack operator, if you receive a low memory alert and tenant virtual machines fail to deploy with a *Fabric VM creation error*, it is possible that the Azure Stack stamp is out of available memory. Use the [Azure Stack Capacity Planne](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) to best understand the capacity available for your workloads. 


#### <a name="compute"></a>Compute
- <!-- TBD - IS, ASDK --> When selecting a virtual machine size for a virtual machine deployment, some F-Series VM sizes are not visible as part of the size selector when you create a VM. The following VM sizes do not appear in the selector: *F8s_v2*, *F16s_v2*, *F32s_v2*, and *F64s_v2*.  
  Megoldás segítségével a következő módszerek egyikét a virtuális gép üzembe helyezése. Minden esetben meg kell adnia a használni kívánt Virtuálisgép-méretet.

  - **Az Azure Resource Manager-sablon:** amikor sablont használ, állítsa be a *vmSize* a sablonban, hogy egyezzen a használni kívánt Virtuálisgép-méretet. Például a következő bejegyzés központi telepítéséhez használandó virtuális gép által használt a *F32s_v2* mérete:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Az Azure CLI:** használhatja a [az virtuális gép létrehozása](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) parancsot, és adja meg a Virtuálisgép-méretet a paramétert, hasonló `--size "Standard_F32s_v2"`.

  - **PowerShell:** a PowerShell használatával is használhat [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) paraméter meghatározza, hogy a virtuális gép méretét, hasonló az `-VMSize "Standard_F32s_v2"`.


- <!-- TBD -  IS ASDK --> Scaling settings for virtual machine scale sets are not available in the portal. As a workaround, you can use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Because of PowerShell version differences, you must use the `-Name` parameter instead of `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach a D series VM. All supported D series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD -  IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  A probléma megoldásához hozzon létre egy új Virtuálisgép-lemezkép, amely a Hyper-V segítségével hozhatók létre üres virtuális merevlemez (New-VHD-elérési út C:\dummy.vhd-- SizeBytes rögzített 1 GB-os). Ez a folyamat kell hárítsa el a problémát, amely megakadályozza a sikertelen elem törlése. Ezt követően a dummy lemezkép létrehozása után 15 perc sikeresen törlése.

  Ezután próbálkozzon redownload, amely korábban nem sikerült Virtuálisgép-lemezkép.

- <!-- TBD -  IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 - IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 

#### <a name="networking"></a>Hálózat
- <!-- 1766332 - IS, ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 -  IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  Jelenleg létrehozhat új virtuális gépek csak új nyilvános IP-címet kell használnia.

  Ez akkor fordul elő, akkor is, ha egy új virtuális géphez az IP-cím ismételt hozzárendelése (más néven a *virtuális IP-címcsere*). Az összes jövőbeni megpróbál a kapcsolaton keresztül az eredeti virtuális gép, és nem egy IP-cím eredményén keresztül kapcsolódni.


- <!-- 2292271 - IS ASDK --> If you raise a Quota limit for a Network resource that is part of an Offer and Plan that is associated with a tenant subscription, the new limit is not applied to that subscription. However, the new limit does apply to new subscriptions that are created after the quota is increased. 

  Ez a probléma megoldása érdekében egy bővítmény csomag használata a hálózati kvóta növeléséhez, ha a csomag már társítva van egy előfizetést. További információkért lásd: hogyan [bővítmény tervének elérhetővé](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> You cannot delete a subscription that has DNS Zone resources or Route Table resources associated with it. To successfully delete the subscription, you must first delete DNS Zone and Route Table resources from the tenant subscription. 


- <!-- 1902460 -  IS ASDK --> Azure Stack supports a single *local network gateway* per IP address. This is true across all tenant subscriptions. After the creation of the first local network gateway connection, subsequent attempts to create a local network gateway resource with the same IP address are blocked.

- <!-- 16309153 -  IS ASDK --> On a Virtual Network that was created with a DNS Server setting of *Automatic*, changing to a custom DNS Server fails. The updated settings are not pushed to VMs in that Vnet.
 
- <!-- TBD -  IS ASDK --> Azure Stack does not support adding additional network interfaces to a VM instance after the VM is deployed. If the VM requires more than one network interface, they must be defined at deployment time.


#### <a name="sql-and-mysql"></a>Az SQL és MySQL 
- <!-- TBD - ASDK --> The database hosting servers must be dedicated for use by the resource provider and user workloads. You cannot use an instance that is being used by any other consumer, including App Services.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers. 

#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- TBD - IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.  

- <!-- TBD - IS ASDK --> App Service can only be deployed into the *Default Provider subscription* at this time. In a future update, App Service will deploy into the new *Metering subscription* that was introduced in Azure Stack 1804. When Metering is supported for use, all existing deployments will be migrated to this new subscription type.

#### <a name="usage"></a>Használat  
- <!-- TBD -  IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.

<!-- #### Identity -->



## <a name="build-201805131"></a>Build 20180513.1

### <a name="new-features"></a>Új funkciók 
A build tartalmazza a következő fejlesztéseket és javításokat Azure verem.  

- <!-- 1759172 - IS, ASDK --> **New administrative subscriptions**. With 1804 there are two new subscription types available in the portal. These new subscription types are in addition to the Default Provider subscription and visible with new Azure Stack installations beginning with version 1804. *Do not use these new subscription types with this version of Azure Stack*. We will announce the availability to use these subscription types in with a future update. 

  Ezek új előfizetés típusok a következők: látható, de az alapértelmezett szolgáltató előfizetés védelmét, és könnyebb a megosztott erőforrások, például SQL üzemeltető kiszolgálók telepítése nagyobb változás része. 

  A most már hozzáférhető három előfizetés típusok a következők:  
  - Alapértelmezett szolgáltató előfizetés: továbbra is használhatja ezt az előfizetési típust. 
  - Előfizetés mérési: *ne használja ezt az előfizetési típust.*
  - Felhasználás előfizetés: *ne használjon ennél az előfizetéstípusnál*

### <a name="fixed-issues"></a>Javított problémák
- <!-- IS, ASDK -->  In the admin portal, you no longer have to refresh the Update tile before it displays information. 

- <!-- 2050709 - IS, ASDK -->  You can now use the admin portal to edit storage metrics for Blob service, Table service, and Queue service.

- <!-- IS, ASDK --> Under **Networking**, when you click **Connection** to set up a VPN connection, **Site-to-site (IPsec)** is now the only available option. 

- **Különböző javítások** teljesítmény, stabilitásának, biztonsági és az Azure-verem által használt operációs rendszer

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>Időtúllépés történt a frissítés további verziókban  
A következő is elérhető, de nincs szükség Azure verem frissítés 1804.
- **A Microsoft Azure verem a System Center Operations Manager figyelési csomag frissítése**. Egy új verziója (1.0.3.0) a Microsoft System Center Operations Manager figyelési csomagját Azure verem érhető el [letöltése](https://www.microsoft.com/download/details.aspx?id=55184). Ebben a verzióban Szolgáltatásnevekről egy csatlakoztatott Azure Alkalmazásveremben üzembe hozzáadásakor is használhatja. Ebben a verzióban is lehetővé teszi, hogy a javítási művelet közvetlenül belül az Operations Manager felügyelete élményt funkciókat. Új irányítópultok megjelenítése az erőforrás-szolgáltató, skálázási egységek és méretezési egység csomópontok is vannak.

- **Új Azure verem Admin PowerShell-verzió 1.3.0**.  Azure verem PowerShell 1.3.0 már elérhető a telepítéshez. Ebben a verzióban tartalmazza az összes felügyeleti erőforrás-szolgáltatók kezelése az Azure-verem parancsok.  Ezzel a kiadással néhány tartalom az Azure verem eszközök githubból elavulttá válik [tárház](https://github.com/Azure/AzureStack-Tools). 

   A telepítés részleteit, hajtsa végre a [utasításokat](.\.\azure-stack-powershell-install.md) vagy a [súgó](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) Azure verem modul 1.3.0 tartalmát. 

- **Azure verem API Rest-referencia kiadása kezdeti**. A [összes Azure-verem rendszergazdai erőforrás-szolgáltató API-referencia](https://docs.microsoft.com/rest/api/azure-stack/) most már közzé van téve. 

### <a name="known-issues"></a>Ismert problémák
 
#### <a name="portal"></a>Portál
- <!-- TBD - IS ASDK --> The ability [to open a new support request from the dropdown](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) from within the administrator portal isn’t available. Instead, use the following link:     
    - Azure verem szoftverfejlesztői készlet használata https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![Webhely-navigációs](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD -  IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.

-   <!-- TBD -  IS ASDK --> In the admin portal, you might see a critical alert for the Microsoft.Update.Admin component. The Alert name, description, and remediation all display as:  
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

#### <a name="marketplace"></a>Piactér
- Felhasználók megkeresheti a teljes piactérre előfizetés nélkül, és láthatja például tervek és ajánlatok felügyeleti elemeket. Ezek az elemek nem működőképes a felhasználók számára is.
 
#### <a name="compute"></a>Compute
- <!-- TBD -  IS ASDK --> Scaling settings for virtual machine scale sets are not available in the portal. As a workaround, you can use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Because of PowerShell version differences, you must use the `-Name` parameter instead of `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach to a DS series VM. DS series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD -  IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  A probléma megoldásához hozzon létre egy új Virtuálisgép-lemezkép, amely a Hyper-V segítségével hozhatók létre üres virtuális merevlemez (New-VHD-elérési út C:\dummy.vhd-- SizeBytes rögzített 1 GB-os). Ez a folyamat kell hárítsa el a problémát, amely megakadályozza a sikertelen elem törlése. Ezt követően a dummy lemezkép létrehozása után 15 perc sikeresen törlése.

  Ezután próbálkozzon redownload, amely korábban nem sikerült Virtuálisgép-lemezkép.

- <!-- TBD -  IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 - IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 

#### <a name="networking"></a>Hálózat
- <!-- 1766332 - IS, ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 -  IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  Jelenleg létrehozhat új virtuális gépek csak új nyilvános IP-címet kell használnia.

  Ez akkor fordul elő, akkor is, ha egy új virtuális géphez az IP-cím ismételt hozzárendelése (más néven a *virtuális IP-címcsere*). Az összes jövőbeni megpróbál a kapcsolaton keresztül az eredeti virtuális gép, és nem egy IP-cím eredményén keresztül kapcsolódni.

- <!-- 2292271 - IS ASDK --> If you raise a Quota limit for a Network resource that is part of an Offer and Plan that is associated with a tenant subscription, the new limit is not applied to that subscription. However, the new limit does apply to new subscriptions that are created after the quota is increased. 

  Ez a probléma megoldása érdekében egy bővítmény csomag használata a hálózati kvóta növeléséhez, ha a csomag már társítva van egy előfizetést. További információkért lásd: hogyan [bővítmény tervének elérhetővé](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> You cannot delete a subscription that has DNS Zone resources or Route Table resources associated with it. To successfully delete the subscription, you must first delete DNS Zone and Route Table resources from the tenant subscription. 


- <!-- 1902460 -  IS ASDK --> Azure Stack supports a single *local network gateway* per IP address. This is true across all tenant subscriptions. After the creation of the first local network gateway connection, subsequent attempts to create a local network gateway resource with the same IP address are blocked.

- <!-- 16309153 -  IS ASDK --> On a Virtual Network that was created with a DNS Server setting of *Automatic*, changing to a custom DNS Server fails. The updated settings are not pushed to VMs in that Vnet.
 
- <!-- TBD -  IS ASDK --> Azure Stack does not support adding additional network interfaces to a VM instance after the VM is deployed. If the VM requires more than one network interface, they must be defined at deployment time.


#### <a name="sql-and-mysql"></a>Az SQL és MySQL 
- <!-- TBD - ASDK --> The database hosting servers must be dedicated for use by the resource provider and user workloads. You cannot use an instance that is being used by any other consumer, including App Services.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers. 

#### <a name="app-service"></a>App Service
- <!-- TBD -  IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- TBD -  IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.
 
#### <a name="usage"></a>Használat  
- <!-- TBD -  IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>A Githubból Azure verem eszközök letöltése
- Használatakor a *meghívása webrequest* PowerShell parancsmag használatával töltheti le az Azure-verem eszközök a Githubból, hibaüzenet:     
    -  *Invoke-webrequest: A kérelmet megszakították: SSL/TLS biztonságos csatorna nem hozható létre.*     

  Ez a hiba oka a legutóbbi GitHub támogatási érvénytelenítése Tlsv1 és Tlsv1.1 kriptográfiai szabványok (a PowerShell alapértelmezett). További információkért lásd: [gyenge kriptográfiai szabványok eltávolítási figyelmeztetés](https://githubengineering.com/crypto-removal-notice/).

<!-- #### Identity -->



## <a name="build-201803021"></a>Build 20180302.1

### <a name="new-features-and-fixes"></a>Új szolgáltatásokat és javításokat
Az új szolgáltatásokat és Azure verem integrált rendszerek verzió 1803 alkalmazása az Azure verem szoftverfejlesztői készlet, amely a javításokat. Tekintse meg a [új szolgáltatások](.\.\azure-stack-update-1803.md#new-features) és [problémák rögzített](.\.\azure-stack-update-1803.md#fixed-issues) az Azure-verem 1803 szakasza részleteket a kibocsátási megjegyzések frissítése.  
> [!IMPORTANT]    
> A elemek egy része szerepel a **új szolgáltatások** és **problémák rögzített** szakaszok csak integrált Azure verem rendszerek szempontjából.

### <a name="changes"></a>Módosítások
- A módszer egy újonnan létrehozott ajánlatot állapotának módosítása *titkos* való *nyilvános* vagy *leszerelt* megváltozott. További információkért lásd: [hozzon létre egy ajánlatot](.\.\azure-stack-create-offer.md). 


### <a name="known-issues"></a>Ismert problémák
 
#### <a name="portal"></a>Portál
- Lehetővé teszi [új támogatási kérést nyithat a legördülő menüből](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) a belül a felügyeleti portál nem érhető el. Ehelyett használja a következő hivatkozásra:     
    - Azure verem szoftverfejlesztői készlet használata https://aka.ms/azurestackforum.    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- Megjelenik egy **szükséges aktiválási** figyelmeztető riasztás, amely azt ajánlja, hogy regisztrálja az Azure verem szoftverfejlesztői készlet. Ez az elvárt viselkedés.

- Felhasználói előfizetések eredmények az árva erőforrások törlése. A probléma megoldásához először törölnie a felhasználói erőforrásokat és a teljes erőforráscsoport, és törölje a felhasználó előfizetések.

- Az előfizetéshez az Azure-verem portálok engedélyek nem lehet megtekinteni. A probléma megoldásához a PowerShell használatával ellenőrizze az engedélyeket.

- A felügyeleti portál irányítópultján a frissítés csempe nem jelenik meg a frissítésekkel kapcsolatos információk. A probléma megoldásához kattintson a csempére kattintva frissítse azt.

-   A felügyeleti portálon jelenhet meg a kritikus riasztások Microsoft.Update.Admin összetevő. A riasztás nevét, leírását és javítási összes jeleníti meg:  
    - *Hiba – hiányzik a sablon FaultType ResourceProviderTimeout.*

    Ez a riasztás biztonságosan figyelmen kívül hagyhatja. 

- A felügyeleti portál és a felhasználói portálra, mind a áttekintése panel nem töltődik be, amikor kijelöl egy korábbi API-verzióval készült storage-fiókok – áttekintés panel (Példa: 2015-06-15). 

  A probléma megoldásához PowerShell használatával futtassa a **Start-ResourceSynchronization.ps1** parancsfájl állítsa vissza a tárfiókadatok való hozzáférést. [A parancsfájl érhető el a Githubról]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts), és kell futnia, amely szolgáltatás rendszergazdai hitelesítő adatait a development kit gazdagépen a ASDK használatakor.  

- A **szolgáltatásának állapota** panel betöltése sikertelen. Ha a szolgáltatás állapotát panel megnyitásához a rendszergazda vagy a felhasználói portál, Azure verem egy hibaüzenet jelenik meg, és nem betölti az információt. Ez az elvárt működés. Válassza ki, és nyissa meg a szolgáltatás állapotát, de ez a funkció még nem érhető el, de hajtják végre az Azure-verem egy jövőbeli verziójában.


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

- A verem Azure felügyeleti portálon, előfordulhat, hogy a kritikus riasztások nevű látható **függőben lévő külső tanúsítvány lejárta**.  Ezt a figyelmeztetést is nyugodtan figyelmen kívül hagyható, és az Azure verem szoftverfejlesztői készlet működésére hatással. 


#### <a name="marketplace"></a>Piactér
- Felhasználók megkeresheti a teljes piactérre előfizetés nélkül, és láthatja például tervek és ajánlatok felügyeleti elemeket. Ezek az elemek nem működőképes a felhasználók számára is.
 
#### <a name="compute"></a>Compute
- A virtuálisgép-méretezési csoportok skálázási beállításai nem érhetők el a portálon. Áthidaló megoldásként használja [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). PowerShell-verzió különbségek, miatt kell használnia a `-Name` paraméter helyett `-VMScaleSetName`.

- Az Azure-verem felhasználói portál virtuális gépek létrehozásakor, a portál megjeleníti, hogy a DS-ben több virtuális gép csatolhat adatlemezek száma helytelen. DS adatsorozat virtuális gépek lehetővé teszi tetszőleges számú adatlemezek Azure beállításként.

- Egy Virtuálisgép-lemezkép nem hozható létre, amikor egy sikertelen elem, amely nem törölhető, előfordulhat, hogy a virtuális gép képek számítási panel hozzáadni.

  A probléma megoldásához hozzon létre egy új Virtuálisgép-lemezkép, amely a Hyper-V segítségével hozhatók létre üres virtuális merevlemez (New-VHD-elérési út C:\dummy.vhd-- SizeBytes rögzített 1 GB-os). Ez a folyamat kell hárítsa el a problémát, amely megakadályozza a sikertelen elem törlése. Ezt követően a dummy lemezkép létrehozása után 15 perc sikeresen törlése.

  Ezután próbálkozzon redownload, amely korábban nem sikerült Virtuálisgép-lemezkép.

-  Ha a kiterjesztést a virtuális gép üzembe helyezéséhez a kiépítés túl sokáig tart, felhasználók hagyja, hogy a létesítési időtúllépési helyett a folyamat felszabadítani, vagy törölje a virtuális gép leállítására tett kísérlet.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>Hálózat
- A **hálózati**, ha **kapcsolat** egy VPN-kapcsolat beállítása **VNet – VNet** van megadva, a lehetséges kapcsolattípus. Válassza ezt a beállítást. Jelenleg csak a **pont-pont (IPsec)** lehetőség.

- Miután egy virtuális Gépet létrehozták, és a társított egy nyilvános IP-cím, IP-címet a virtuális gép nem szüntesse meg. Disassociation úgy tűnik, hogy működik, de a korábban hozzárendelt nyilvános IP-cím marad az eredeti virtuális társítva.

  Jelenleg létrehozhat új virtuális gépek csak új nyilvános IP-címet kell használnia.

  Ez akkor fordul elő, akkor is, ha egy új virtuális géphez az IP-cím ismételt hozzárendelése (más néven a *virtuális IP-címcsere*). Az összes jövőbeni megpróbál a kapcsolaton keresztül az eredeti virtuális gép, és nem egy IP-cím eredményén keresztül kapcsolódni.



- Azure verem támogatja egyetlen *helyi hálózati átjáró* / IP-címet. Ez az összes bérlői előfizetések között. Az első helyi hálózati átjáró kapcsolat létrehozására, későbbi helyi hálózati átjáró erőforrás létrehozása a azonos IP-címmel kísérlet után sem.

- A virtuális hálózat DNS-kiszolgáló használatára létrehozott *automatikus*, változó számára egy egyéni DNS-kiszolgáló sikertelen. A frissített beállításokkal nem elküldte azokat a virtuális gépekhez a Vneten belül.
 
- Az Azure verem nem támogatja a további hálózati adapterek hozzáadása egy Virtuálisgép-példány, a virtuális gép telepítését követően. Ha a virtuális gép több hálózati adapter szükséges, akkor meg kell adni a központi telepítéskor.



#### <a name="sql-and-mysql"></a>Az SQL és MySQL 
- Akár egy óraba felhasználók adatbázisok létrehozhat egy új SQL- vagy MySQL SKU is igénybe vehet.

- Az adatbázis-kiszolgálók üzemeltetési az erőforrás-szolgáltató és a felhasználó munkaterhelés használatra kell kijelölnie. Bármely más fogyasztó, beleértve az App Service szolgáltatások által használt példánya nem használható.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** or **Tier** names when you create a SKU for the SQL and MySQL resource providers.

#### <a name="app-service"></a>App Service
- Ahhoz, hogy az első Azure-függvény létre előfizetést felhasználók regisztrálnia kell a storage erőforrás-szolgáltató.

- Ahhoz, hogy az infrastruktúra kiterjesztése olyan esetekben (munkavállalók, kezelési, előtér-szerepkörök), PowerShell számítás a kibocsátási megjegyzésekben leírt módon kell használnia.
 
#### <a name="usage"></a>Használat  
- Használati nyilvános IP-használat mérési adatokat jeleníti meg az azonos *EventDateTime* érték az egyes rekordokhoz ahelyett, hogy a *TimeDate* tájékoztat, hogy a rekord létrehozásának stamp. Ezeket az adatokat jelenleg nem használható a nyilvános IP-cím használatának pontos lehetet.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>A Githubból Azure verem eszközök letöltése
- Használatakor a *meghívása webrequest* PowerShell parancsmag használatával töltheti le az Azure-verem eszközök a Githubból, hibaüzenet:     
    -  *Invoke-webrequest: A kérelmet megszakították: SSL/TLS biztonságos csatorna nem hozható létre.*     

  Ez a hiba oka a legutóbbi GitHub támogatási érvénytelenítése Tlsv1 és Tlsv1.1 kriptográfiai szabványok (a PowerShell alapértelmezett). További információkért lásd: [gyenge kriptográfiai szabványok eltávolítási figyelmeztetés](https://githubengineering.com/crypto-removal-notice/).

  A probléma megoldásához vegye fel `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` a PowerShell-konzolon TLSv1.2 használandó, ha a letöltés a GitHub-adattárak kényszerítheti a parancsfájl elején.





