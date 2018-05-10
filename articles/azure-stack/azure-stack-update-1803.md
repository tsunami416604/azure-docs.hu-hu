---
title: Frissítés Azure verem 1803 |} Microsoft Docs
description: További tudnivalók az Azure-verem 1803 frissítés Újdonságok integrált rendszerek, az ismert problémák és letöltéséről a frissítést.
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
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 36d4cd910f841a323dfada49d65f7acb4bdf3138
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="azure-stack-1803-update"></a>Az Azure verem 1803 frissítés

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Ez a cikk fejlesztést és 1803 frissítőcsomag, ismert problémái ebben a kiadásban, valamint a frissítés letöltése helyét. Ismert problémák a frissítési folyamat közvetlenül kapcsolatos problémák és a build (telepítés utáni) problémái vannak felosztva.

> [!IMPORTANT]        
> Ez a csomag csak integrált Azure verem rendszerek van. Ez a csomag nem vonatkoznak a Azure verem szoftverfejlesztői készlet.

## <a name="build-reference"></a>Hivatkozás létrehozása    
Az Azure verem 1803 frissítés buildszáma **20180329.1**.


## <a name="before-you-begin"></a>Előkészületek    
> [!IMPORTANT]    
> Ne próbálja meg a frissítés telepítése során a virtuális gépek létrehozásához. További információ a frissítések kezelése: [kezelheti a frissítéseket az Azure-verem áttekintés](azure-stack-updates.md#plan-for-updates).


### <a name="prerequisites"></a>Előfeltételek
- Telepítse az Azure-verem [1802 frissítése](azure-stack-update-1802.md) az Azure verem 1803 frissítés alkalmazása előtt.   

- Telepítés **AzS gyorsjavítás – 1.0.180312.1-Build 20180222.2** az Azure verem 1803 frissítés alkalmazása előtt. A gyorsjavítás a Windows Defender frissítések, és akkor használható, ha a frissítések letöltése Azure verem.

  Telepítse a gyorsjavítást, hajtsa végre a normál eljárásai [frissítések telepítése Azure verem](azure-stack-apply-updates.md). A frissítés neve jelenik meg **AzS gyorsjavítás – 1.0.180312.1**, és a következő fájlokat tartalmazza: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.XML

  Feltöltése után ezeket a fájlokat egy tárfiók és tároló, a felügyeleti portálon frissítés csempén futtatja a telepítést. 
  
  Azure verem frissítése, ellentétben a frissítés telepítése nincs hatással Azure verem verzióját. Annak ellenőrzéséhez, hogy a frissítés telepítve van, a lista megtekintése **telepített frissítések**.

### <a name="post-update-steps"></a>Frissítés utáni lépések
- 1803 a telepítés után bármely alkalmazandó gyorsjavításainak telepítéséhez. További információ a következő tudásbáziscikkeiből, megtekintése, valamint a [karbantartása házirend](azure-stack-servicing-policy.md).

  - [KB 4103348 - az hálózati vezérlő API szolgáltatás leáll, ha egy Azure verem frissítés telepítésekor](https://support.microsoft.com/en-us/help/4103348)

- A frissítés telepítése után tekintse át a tűzfal konfigurációját, és győződjön meg arról [szükséges portok](azure-stack-integrate-endpoints.md) nyitva. A frissítés például vezet be az Azure figyelő, köztük a naplók tevékenység naplókban is megváltoztatását. A módosítás port 13012 most szolgál, és meg van nyitva.  

### <a name="new-features"></a>Új funkciók 
A frissítés tartalmazza a következő fejlesztéseket és javításokat Azure verem.

- **Azure verem titkos kulcsok frissítése** - (fiókok és tanúsítványok). Titkos kulcsok kezelésével kapcsolatos további információkért lásd: [forgassa el a titkos kulcsok Azure verem](azure-stack-rotate-secrets.md). 

- <!-- 1914853 --> **Automatic redirect to HTTPS** when you use HTTP to access the administrator and user portals. This improvement was made based on [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) feedback for Azure Stack. 

- <!-- 2202621  --> **Access the Marketplace** – You can now open the Azure Stack Marketplace by using the [+New](https://ms.portal.azure.com/#create/hub) option from within the admin and user portals the same way you do in the Azure portals.
 
- <!-- 2202621 --> **Azure Monitor** - Azure Stack adds [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) to the admin and user portals. This includes new explorers for metrics and activity logs. To access this Azure Monitor from external networks, port **13012** must be open in firewall configurations. For more information about ports required by Azure Stack, see [Azure Stack datacenter integration - Publish endpoints](azure-stack-integrate-endpoints.md).

   Ennek részeként is módosíthatja, a **további szolgáltatások**, *naplók* csomópontként jelenik meg, mint a *tevékenységi naplóit*. A funkció mostantól konzisztensek legyenek az Azure-portálon. 

- <!-- 1664791 --> **Sparse files** -  When you add a New image to Azure Stack, or add an image through marketplace syndication, the image is converted to a sparse file. Images that were added prior to using Azure Stack version 1803 cannot be converted. Instead, you must use marketplace syndication to resubmit those images to take advantage of this feature. 
 
   Ritka fájlok egy hatékony fájlformátum tárolóhely lemezterület-használat csökkentésére, és az i/o javítására használjuk.  További információkért lásd: [ritka Fsutil](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) a Windows Server. 

### <a name="fixed-issues"></a>Javított problémák

- <!-- 1739988 --> Internal Load Balancing (ILB) now properly handles MAC addresses for back-end VMs, which causes ILB to drop packets to the back-end network when using Linux instances on the back-end network. ILB works fine with Windows instances on the back-end network. 

- <!-- 1805496 --> An issue where VPN Connections between Azure Stack would become disconnected due to Azure Stack using different settings for the IKE policy than Azure.  The values now match the values in Azure. 

- <!-- 2209262 --> The IP issue where VPN Connections was previously visible in the portal; however enabling or toggling IP Forwarding has no effect. The feature is turned on by default and the ability to change this not yet supported.  The control has been removed from the portal. 

- <!-- 1766332 --> Azure Stack does not support Policy Based VPN Gateways, even though the option appears in the Portal.  The option has been removed from the Portal. 

- <!-- 1868283 --> Azure Stack now prevents resizing of a virtual machine that is created with dynamic disks. 

- <!-- 1756324 --> Usage data for virtual machines is now separated at hourly intervals. This is consistent with Azure. 

- <!--  2253274 --> The issue where in the admin and user portals, the Settings blade for vNet Subnets fails to load. As a workaround, use PowerShell and the [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) cmdlet to view and manage this information.

- A virtuális gép, az üzenet létrehozásakor *nem lehet megjeleníteni a díjszabási* többé nem jelenik meg a Virtuálisgép-méretet a mérete kiválasztásakor.

- Különböző javítja a teljesítményt, stabilitását, biztonsági és az Azure-verem által használt operációs rendszer.


### <a name="changes"></a>Változások
- A módszer egy újonnan létrehozott ajánlatot állapotának módosítása *titkos* való *nyilvános* vagy *leszerelt* megváltozott. További információkért lásd: [hozzon létre egy ajánlatot](azure-stack-create-offer.md).


### <a name="known-issues-with-the-update-process"></a>A frissítési folyamat szolgáltatással kapcsolatos ismert problémák    
<!-- 2328416 --> During installation of the 1803 update, there can be downtime of the blob service and internal services that use blob service. This includes some virtual machine operations. This down time can cause failures of tenant operations or alerts from services that can’t access data. This issue resolves itself when the update completes installation. 


### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)
Az alábbiakban telepítés utáni build kapcsolatos ismert problémák **20180323.2**.

#### <a name="portal"></a>Portál
- Lehetővé teszi [új támogatási kérést nyithat a legördülő menüből](azure-stack-manage-portals.md#quick-access-to-help-and-support) a belül a felügyeleti portál nem érhető el. Ehelyett használja a következő hivatkozásra:     
    - Azure verem integrált, rendszereihez https://aka.ms/newsupportrequest.

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.

- Nem lehet a számítási és tárolási erőforrások megtekintése a felügyeleti portálon lehetséges. A probléma oka hiba jelentendő helytelenül sikeres frissítés a frissítés telepítése során. Ha a probléma akkor fordul elő, forduljon a Microsoft ügyfél-támogatási szolgálathoz segítségért.

- A portál üres irányítópult jelenhet meg. Az irányítópult helyreállításához válassza ki a fogaskerék ikonra a portál jobb felső sarokban, majd válassza ki **alapértelmezett beállításainak visszaállítása**.

- Egy erőforrás vagy egy erőforráscsoport, tulajdonságainak megtekintésekor a **áthelyezése** gomb le van tiltva. Ez az elvárt viselkedés. Erőforráscsoportok vagy előfizetések között erőforrások vagy erőforráscsoportok áthelyezése jelenleg nem támogatott.

- Felhasználói előfizetések eredmények az árva erőforrások törlése. A probléma megoldásához először törölnie a felhasználói erőforrásokat és a teljes erőforráscsoport, és törölje a felhasználó előfizetések.

- Az előfizetéshez az Azure-verem portálok engedélyek nem lehet megtekinteni. A probléma megoldásához a PowerShell használatával ellenőrizze az engedélyeket.

- A felügyeleti portál irányítópultján a frissítés csempe nem jelenik meg a frissítésekkel kapcsolatos információk. A probléma megoldásához kattintson a csempére kattintva frissítse azt.

- A felügyeleti portálon vonatkozó kritikus riasztás láthatja a *Microsoft.Update.Admin* összetevő. A riasztás nevét, leírását és javítási összes jeleníti meg:  
    - *Hiba – hiányzik a sablon FaultType ResourceProviderTimeout.*

  Ez a riasztás biztonságosan figyelmen kívül hagyhatja. 


<!-- #### Health and monitoring --> 

#### <a name="marketplace"></a>Piactér
- A felhasználók megkeresheti a teljes piactérre előfizetés nélkül, és láthatja például tervek és ajánlatok felügyeleti elemeket. Ezek az elemek nem működőképes a felhasználók számára is.



#### <a name="compute"></a>Compute
- A virtuálisgép-méretezési csoportok skálázási beállításai nem érhetők el a portálon. Áthidaló megoldásként használja [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). PowerShell-verzió különbségek, miatt kell használnia a `-Name` paraméter helyett `-VMScaleSetName`.

- Nyissa meg a portálon beállított rendelkezésre állási létrehozásakor **új** > **számítási** > **rendelkezésre állási csoport**, csak hozhat létre egy rendelkezésre állási csoport egy tartalék tartomány és a frissítési tartomány 1. A probléma megoldásához, amikor egy új virtuális gép létrehozása, hozzon létre a rendelkezésre állási csoportot a powershellel, CLI-t, vagy a portálon.

- Az Azure-verem felhasználói portál virtuális gépek létrehozásakor, a portál megjeleníti, hogy a DS-ben több virtuális gép csatolhat adatlemezek száma helytelen. DS adatsorozat virtuális gépek lehetővé teszi tetszőleges számú adatlemezek Azure beállításként.

- Egy Virtuálisgép-lemezkép nem hozható létre, amikor egy sikertelen elem, amely nem törölhető, előfordulhat, hogy a virtuális gép képek számítási panel hozzáadni.

  A probléma megoldásához hozzon létre egy új Virtuálisgép-lemezkép, amely a Hyper-V segítségével hozhatók létre üres virtuális merevlemez (New-VHD-elérési út C:\dummy.vhd-- SizeBytes rögzített 1 GB-os). Ez a folyamat kell hárítsa el a problémát, amely megakadályozza a sikertelen elem törlése. Ezt követően a dummy lemezkép létrehozása után 15 perc sikeresen törlése.

  Ezután próbálkozzon redownload, amely korábban nem sikerült Virtuálisgép-lemezkép.

-  Ha a kiterjesztést a virtuális gép üzembe helyezéséhez a kiépítés túl sokáig tart, felhasználók hagyja, hogy a létesítési időtúllépési helyett a folyamat felszabadítani, vagy törölje a virtuális gép leállítására tett kísérlet.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  


#### <a name="networking"></a>Hálózat
- Miután egy virtuális Gépet létrehozták, és a társított egy nyilvános IP-cím, IP-címet a virtuális gép nem szüntesse meg. Disassociation úgy tűnik, hogy működik, de a korábban hozzárendelt nyilvános IP-cím marad az eredeti virtuális társítva.

  Jelenleg létrehozhat új virtuális gépek csak új nyilvános IP-címet kell használnia.

  Ez akkor fordul elő, akkor is, ha egy új virtuális géphez az IP-cím ismételt hozzárendelése (más néven a *virtuális IP-címcsere*). Az összes jövőbeni megpróbál a kapcsolaton keresztül a eredetileg társított virtuális Gépet, és nem egy IP-cím eredményén keresztül kapcsolódni.



- Azure verem támogatja egyetlen *helyi hálózati átjáró* / IP-címet. Ez az összes bérlői előfizetések között. Az első helyi hálózati átjáró kapcsolat létrehozására, későbbi helyi hálózati átjáró erőforrás létrehozása a azonos IP-címmel kísérlet után sem.

- A virtuális hálózat DNS-kiszolgáló használatára létrehozott *automatikus*, változó számára egy egyéni DNS-kiszolgáló sikertelen. A frissített beállításokkal nem elküldte azokat a virtuális gépekhez a Vneten belül.

- Az Azure verem nem támogatja a további hálózati adapterek hozzáadása egy Virtuálisgép-példány, a virtuális gép telepítését követően. Ha a virtuális gép több hálózati adapter szükséges, akkor meg kell adni a központi telepítéskor.

- <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    Az App Service megkerülő megoldás: a távoli asztal a vezérlő példányokra van szüksége, ha módosítja a biztonsági szabályok belül a hálózati biztonsági csoportok a PowerShell használatával.  Az alábbiakban példákat *engedélyezése*, majd állítsa vissza a konfiguráció *megtagadása*:  
    
    - *Engedélyezése:*
 
      ```powershell    
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin
      
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
        
        Add-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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
- A folytatás előtt tekintse át a fontos Megjegyzés: a [megkezdése előtt](#before-you-begin) közelében elindítása ezek a kibocsátási megjegyzéseket.

- Akár egy óraba felhasználók adatbázisokat hozhat létre egy új MySQL- vagy SQL-környezetben is igénybe vehet.

- Az erőforrás-szolgáltató, hogy a gazdagép SQL vagy MySQL elemek létrehozásához kiszolgálókon támogatott. A gazdagép-kiszolgálón létrehozott elemek, nem az erőforrás-szolgáltató által létrehozott okozhatja hibás állapotban.  


> [!NOTE]  
> Miután frissítette az Azure verem 1803, továbbra is használja az SQL és MySQL erőforrás-szolgáltató korábban már telepített.  Azt javasoljuk, hogy frissítse az SQL és MySQL amikor elérhetővé válik az új verziót. Azure-vermét, például frissítések alkalmazása az SQL és a MySQL-szolgáltatók egymás után.  Például 1711 verzióját használja, ha először alkalmazza a 1712, majd 1802 verziója, és frissítse a 1803.      
>   
> Frissítés 1803 telepítése nem befolyásolja a felhasználók által MySQL vagy az SQL-erőforrás-szolgáltató aktuális használatát.
> Az erőforrás-szolgáltató használata verziója, függetlenül a felhasználók adatait, a hozzájuk tartozó adatbázisok nincs megszáradásukig, és elérhető marad.    



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


## <a name="download-the-update"></a>A frissítés letöltése
Letöltheti az Azure verem 1803 frissítési csomagot [Itt](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Lásd még
- A privilegizált végpont (EGP) segítségével figyelheti, és folytassa a frissítést, lásd: [figyelése Azure verem használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).
- A frissítés-kezelés az Azure-verem áttekintését lásd: [kezelheti a frissítéseket az Azure-verem áttekintés](azure-stack-updates.md).
- Az Azure veremnek megfelelő frissítések alkalmazásával kapcsolatos további információkért lásd: [Azure verem frissítések alkalmazása](azure-stack-apply-updates.md).
