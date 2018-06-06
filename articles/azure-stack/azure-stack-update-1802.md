---
title: Frissítés Azure verem 1802 |} Microsoft Docs
description: További tudnivalók az Azure-verem 1802 frissítés Újdonságok integrált rendszerek, az ismert problémák és letöltéséről a frissítést.
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
ms.openlocfilehash: af65ffc088c2beadf415b72ec284ef77f3e4f6d4
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757257"
---
# <a name="azure-stack-1802-update"></a>Az Azure verem 1802 frissítés

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Ez a cikk fejlesztést és 1802 frissítőcsomag, ismert problémái ebben a kiadásban, valamint a frissítés letöltése helyét. Ismert problémák a frissítési folyamat közvetlenül kapcsolatos problémák és a build (telepítés utáni) problémái vannak felosztva.

> [!IMPORTANT]        
> Ez a csomag csak integrált Azure verem rendszerek van. Ez a csomag nem vonatkoznak a Azure verem szoftverfejlesztői készlet.

## <a name="build-reference"></a>Hivatkozás létrehozása    
Az Azure verem 1802 frissítés buildszáma **20180302.1**.  


## <a name="before-you-begin"></a>Előkészületek    
> [!IMPORTANT]    
> Ne próbálja meg a frissítés telepítése során a virtuális gépek létrehozásához. További információ a frissítések kezelése: [kezelheti a frissítéseket az Azure-verem áttekintés](azure-stack-updates.md#plan-for-updates).


### <a name="prerequisites"></a>Előfeltételek
- Telepítse az Azure-verem [1712 frissítése](azure-stack-update-1712.md) az Azure verem 1802 frissítés alkalmazása előtt.    

- Telepítés **AzS gyorsjavítás – 1.0.180312.1-Build 20180222.2** az Azure verem 1802 frissítés alkalmazása előtt. A gyorsjavítás a Windows Defender frissítések, és akkor használható, ha a frissítések letöltése Azure verem.

  Telepítse a gyorsjavítást, hajtsa végre a normál eljárásai [frissítések telepítése Azure verem](azure-stack-apply-updates.md). A frissítés neve jelenik meg **AzS gyorsjavítás – 1.0.180312.1**, és a következő fájlokat tartalmazza: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.XML

  Feltöltése után ezeket a fájlokat egy tárfiók és tároló, a felügyeleti portálon frissítés csempén futtatja a telepítést. 
  
  Azure verem frissítése, ellentétben a frissítés telepítése nincs hatással Azure verem verzióját.  Annak ellenőrzéséhez, hogy a frissítés telepítve van, a lista megtekintése **telepített frissítések**.
 


### <a name="post-update-steps"></a>Frissítés utáni lépések
1802 a telepítés után bármely alkalmazandó gyorsjavításainak telepítéséhez. További információ a következő tudásbáziscikkeiből, megtekintése, valamint a [karbantartása házirend](azure-stack-servicing-policy.md). 
- Az Azure verem gyorsjavítás **1.0.180302.4**. [KB 4131152 - meglévő virtuálisgép-méretezési csoportok esetleg használhatatlanná válik.]( https://support.microsoft.com/help/4131152) 

  Ez a javítás is megoldja a részletes leírást talál [KB 4103348 - hálózati vezérlő API szolgáltatás lefagy, amikor egy Azure verem frissítés telepítésekor](https://support.microsoft.com/help/4103348).


### <a name="new-features-and-fixes"></a>Új szolgáltatásokat és javításokat
A frissítés tartalmazza a következő fejlesztéseket és javításokat Azure verem.

- **Támogatja a következő Azure Storage szolgáltatás API-verziók**:
    - 2017-04-17 
    - 2016-05-31 
    - 2015-12-11 
    - 2015-07-08 
    
    További információkért lásd: [Azure verem Storage: szempontok és a különbségeket](/azure/azure-stack/user/azure-stack-acs-differences).

- **Támogatja a nagyobb [Blokkblobokat](azure-stack-acs-differences.md)**:
    - A maximális megengedett blokkméret nőtt 4 MB 100 MB-ra.
    - A blob maximális mérete 4.75 TB ra 195 GB.  

- **Infrastruktúra biztonsági mentés** most az erőforrás-szolgáltatók csempe jelenik meg, és engedélyezve vannak a biztonsági mentés vonatkozó riasztásokat. Az infrastruktúra biztonsági másolat szolgáltatással kapcsolatos további információkért lásd: [Azure verem és az infrastruktúra biztonsági mentési szolgáltatás biztonsági mentési és az adatok helyreállítását](azure-stack-backup-infrastructure-backup.md).

- **Frissítse a *teszt-AzureStack* parancsmag** tárolási diagnosztika javítása érdekében. Erről a parancsmagról további információk: [Azure verem érvényesítési](azure-stack-diagnostic-test.md).

- **Szerepköralapú hozzáférés-vezérlés (RBAC) fejlesztései** -most már használhatja az RBAC univerzális felhasználói csoportok vonatkozó engedélyek delegálása az Azure-vermet az AD FS telepítésekor. Az RBAC kapcsolatos további információkért lásd: [kezelése RBAC](azure-stack-manage-permissions.md).

- **Támogatja a több tartalék tartományok**.  További információkért lásd: [magas rendelkezésre állású Azure verem](azure-stack-key-features.md#high-availability-for-azure-stack).

- **Fizikai memória frissítések támogatása** -a kezdeti telepítés után most bővítheti az integrált Azure verem rendszer memóriakapacitása. További információkért lásd: [Azure verem fizikai memória kapacitás kezelése](azure-stack-manage-storage-physical-memory-capacity.md).

- **Különböző javítások** teljesítmény, stabilitásának, biztonsági és az Azure-verem által használt operációs rendszer.

<!--
#### New features
-->


<!--
#### Fixes
-->


### <a name="known-issues-with-the-update-process"></a>A frissítési folyamat szolgáltatással kapcsolatos ismert problémák    
*Nincsenek ismert problémák 1802 frissítés telepítéséhez.*


### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)
Az alábbiakban telepítés utáni build kapcsolatos ismert problémák **20180302.1**

#### <a name="portal"></a>Portál
- <!-- 2332636 - IS -->  When you use AD FS for your Azure Stack identity system and update to this version of Azure Stack, the default owner of the default provider subscription is reset to the built-in **CloudAdmin** user.  
  Megkerülő megoldás: A frissítés telepítése után a probléma megoldásához használja a 3. lépés a [konfigurálása eseményindító automatizálási jogcím-szolgáltatói megbízhatósági Azure verem](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) alaphelyzetbe állítani az alapértelmezett szolgáltató előfizetés tulajdonosának eljárást.   

- Lehetővé teszi [új támogatási kérést nyithat a legördülő menüből](azure-stack-manage-portals.md#quick-access-to-help-and-support) a belül a felügyeleti portál nem érhető el. Ehelyett használja a következő hivatkozásra:     
    - Azure verem integrált, rendszereihez https://aka.ms/newsupportrequest.

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.

- Nem lehet a számítási és tárolási erőforrások megtekintése a felügyeleti portálon lehetséges. A probléma oka hiba jelentendő helytelenül sikeres frissítés a frissítés telepítése során. Ha a probléma akkor fordul elő, forduljon a Microsoft ügyfél-támogatási szolgálathoz segítségért.

- A portál üres irányítópult jelenhet meg. Az irányítópult helyreállításához válassza ki a fogaskerék ikonra a portál jobb felső sarokban, majd válassza ki **alapértelmezett beállításainak visszaállítása**.

- Felhasználói előfizetések eredmények az árva erőforrások törlése. A probléma megoldásához először törölnie a felhasználói erőforrásokat és a teljes erőforráscsoport, és törölje a felhasználó előfizetések.

- Az előfizetéshez az Azure-verem portálok engedélyek nem lehet megtekinteni. A probléma megoldásához a PowerShell használatával ellenőrizze az engedélyeket.

- A felügyeleti portál irányítópultján a frissítés csempe nem jelenik meg a frissítésekkel kapcsolatos információk. A probléma megoldásához kattintson a csempére kattintva frissítse azt.

-   A felügyeleti portálon jelenhet meg a kritikus riasztások Microsoft.Update.Admin összetevő. A riasztás nevét, leírását és javítási összes jeleníti meg:  
    - *Hiba – hiányzik a sablon FaultType ResourceProviderTimeout.*

    Ez a riasztás biztonságosan figyelmen kívül hagyhatja. 

- <!-- 2253274 --> In the admin and user portals, the Settings blade for vNet Subnets fails to load. As a workaround, use PowerShell and the [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) cmdlet to view and  manage this information.

- A felügyeleti portál és a felhasználói portálra, mind a áttekintése panel nem töltődik be, amikor kijelöl egy korábbi API-verzióval készült storage-fiókok – áttekintés panel (Példa: 2015-06-15). Ez magában foglalja a rendszer storage-fiókok hasonló **updateadminaccount** javítás és a frissítés során használt. 

  A probléma megoldásához PowerShell használatával futtassa a **Start-ResourceSynchronization.ps1** parancsfájl állítsa vissza a tárfiókadatok való hozzáférést. [A parancsfájl érhető el a Githubról]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts), és a kiemelt végpont szolgáltatás rendszergazdai hitelesítő adatokkal kell futnia. 

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


#### <a name="marketplace"></a>Piactér
- A felhasználók megkeresheti a teljes piactérre előfizetés nélkül, és láthatja például tervek és ajánlatok felügyeleti elemeket. Ezek az elemek nem működőképes a felhasználók számára is.

#### <a name="compute"></a>Compute
- A virtuálisgép-méretezési csoportok skálázási beállításai nem érhetők el a portálon. Áthidaló megoldásként használja [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). PowerShell-verzió különbségek, miatt kell használnia a `-Name` paraméter helyett `-VMScaleSetName`.

- <!-- 2290877  --> You cannot scale up a virtual machine scale set (VMSS) that was created when using Azure Stack prior to version 1802. This is due to the change in support for using availability sets with virtual machine scale sets. This support was added with version 1802.  When you attempt to add additional instances to scale a VMSS that was created prior to this support being added, the action fails with the message *Provisioning state failed*. 

  A probléma megoldásához 1803 verzióban. 1802 verziójához a probléma megoldásához telepítse a Azure verem gyorsjavítást **1.0.180302.4**. További információkért lásd: [KB 4131152: meglévő virtuálisgép-méretezési csoportok használhatatlanná válhat]( https://support.microsoft.com/help/4131152). 

- Az Azure verem támogatja a csak a rögzített típusúvá a VHD-k használatát. Néhány képet, a veremben Azure piactéren keresztül felajánlott használjon dinamikus virtuális merevlemezek, de amelyek el lettek távolítva. Dinamikus lemez nem csatlakoztatható a virtuális gép (VM) átméretezése hagyja el a virtuális gép hibás állapotban.

  A probléma orvoslása érdekében törölje a virtuális gép a virtuális lemez, a tárfiók VHD blob törlése nélkül. Majd a virtuális merevlemez átalakítása dinamikus lemezről rögzített méretű lemezt, és hozza létre a virtuális gép.

- Nyissa meg a portálon beállított rendelkezésre állási létrehozásakor **új** > **számítási** > **rendelkezésre állási csoport**, csak hozhat létre egy rendelkezésre állási csoport egy tartalék tartomány és a frissítési tartomány 1. A probléma megoldásához, amikor egy új virtuális gép létrehozása, hozzon létre a rendelkezésre állási csoportot a powershellel, CLI-t, vagy a portálon.

- Az Azure-verem felhasználói portál virtuális gépek létrehozásakor, a portál megjeleníti, hogy a D sorozat VM csatolhat adatlemezek száma helytelen. Az összes támogatott D sorozat virtuális gépek lehetővé teszi tetszőleges számú adatlemezek Azure beállításként.

- Egy Virtuálisgép-lemezkép nem hozható létre, amikor egy sikertelen elem, amely nem törölhető, előfordulhat, hogy a virtuális gép képek számítási panel hozzáadni.

  A probléma megoldásához hozzon létre egy új Virtuálisgép-lemezkép, amely a Hyper-V segítségével hozhatók létre üres virtuális merevlemez (New-VHD-elérési út C:\dummy.vhd-- SizeBytes rögzített 1 GB-os). Ez a folyamat kell hárítsa el a problémát, amely megakadályozza a sikertelen elem törlése. Ezt követően a dummy lemezkép létrehozása után 15 perc sikeresen törlése.

  Ezután próbálkozzon redownload, amely korábban nem sikerült Virtuálisgép-lemezkép.

-  Ha a kiterjesztést a virtuális gép üzembe helyezéséhez a kiépítés túl sokáig tart, felhasználók hagyja, hogy a létesítési időtúllépési helyett a folyamat felszabadítani, vagy törölje a virtuális gép leállítására tett kísérlet.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  




#### <a name="networking"></a>Hálózat
- Miután egy virtuális Gépet létrehozták, és a társított egy nyilvános IP-cím, IP-címet a virtuális gép nem szüntesse meg. Disassociation úgy tűnik, hogy működik, de a korábban hozzárendelt nyilvános IP-cím marad az eredeti virtuális társítva.

  Jelenleg létrehozhat új virtuális gépek csak új nyilvános IP-címet kell használnia.

  Ez akkor fordul elő, akkor is, ha egy új virtuális géphez az IP-cím ismételt hozzárendelése (más néven a *virtuális IP-címcsere*). Az összes jövőbeni megpróbál a kapcsolaton keresztül a eredetileg társított virtuális Gépet, és nem egy IP-cím eredményén keresztül kapcsolódni.

- Belső Load Balancing (ILB) nem megfelelően kezeli a MAC-címek háttér virtuális gépekhez, emiatt ILB megszünteti a háttér-hálózat használata a Linux-példányok esetén.  ILB helyesen működik a Windows-példányok a háttér-hálózaton.

-   Az IP-továbbítást a szolgáltatás látható a portálon, nincs hatása azonban az IP-továbbítás engedélyezését. Ez a funkció jelenleg nem támogatott.

- Azure verem támogatja egyetlen *helyi hálózati átjáró* / IP-címet. Ez az összes bérlői előfizetések között. Az első helyi hálózati átjáró kapcsolat létrehozására, későbbi helyi hálózati átjáró erőforrás létrehozása a azonos IP-címmel kísérlet után sem.

- A virtuális hálózat DNS-kiszolgáló használatára létrehozott *automatikus*, változó számára egy egyéni DNS-kiszolgáló sikertelen. A frissített beállításokkal nem elküldte azokat a virtuális gépekhez a Vneten belül.

- Az Azure verem nem támogatja a további hálózati adapterek hozzáadása egy Virtuálisgép-példány, a virtuális gép telepítését követően. Ha a virtuális gép több hálózati adapter szükséges, akkor meg kell adni a központi telepítéskor.

-   <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    Az App Service megkerülő megoldás: a távoli asztal a vezérlő példányokra van szüksége, ha módosítja a biztonsági szabályok belül a hálózati biztonsági csoportok a PowerShell használatával.  Az alábbiakban példákat *engedélyezése*, majd állítsa vissza a konfiguráció *megtagadása*:  
    
    - *Engedélyezése:*
 
      ```powershell    
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin
      
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
        
        Login-AzureRMAccount -EnvironmentName AzureStackAdmin
        
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

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers.

> [!NOTE]  
> Az Azure verem 1802 frissítése után továbbra is használja az SQL és MySQL erőforrás-szolgáltató korábban már telepített.  Azt javasoljuk, hogy frissítse az SQL és MySQL amikor elérhetővé válik az új verziót. Azure-vermét, például frissítések alkalmazása az SQL és a MySQL-szolgáltatók egymás után.  Például 1710 verzióját használja, ha először alkalmazza a 1711, majd 1712 verziója, és frissítse a 1802.      
>   
> Frissítés 1802 telepítése nem befolyásolja a felhasználók által MySQL vagy az SQL-erőforrás-szolgáltató aktuális használatát.
> Az erőforrás-szolgáltató használata verziója, függetlenül a felhasználók adatait, a hozzájuk tartozó adatbázisok nincs megszáradásukig, és elérhető marad.    


#### <a name="app-service"></a>App Service
- Ahhoz, hogy az első Azure-függvény létre előfizetést felhasználók regisztrálnia kell a storage erőforrás-szolgáltató.

- Ahhoz, hogy az infrastruktúra kiterjesztése olyan esetekben (munkavállalók, kezelési, előtér-szerepkörök), PowerShell számítás a kibocsátási megjegyzésekben leírt módon kell használnia.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>A Githubból Azure verem eszközök letöltése
- Használatakor a *meghívása webrequest* PowerShell parancsmag használatával töltheti le az Azure-verem eszközök a Githubból, hibaüzenet:     
    -  *Invoke-webrequest: A kérelmet megszakították: SSL/TLS biztonságos csatorna nem hozható létre.*     

  Ez a hiba oka a legutóbbi GitHub támogatási érvénytelenítése Tlsv1 és Tlsv1.1 kriptográfiai szabványok (a PowerShell alapértelmezett). További információkért lásd: [gyenge kriptográfiai szabványok eltávolítási figyelmeztetés](https://githubengineering.com/crypto-removal-notice/).

  A probléma megoldásához vegye fel `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` a PowerShell-konzolon TLSv1.2 használandó, ha a letöltés a GitHub-adattárak kényszerítheti a parancsfájl elején.


## <a name="download-the-update"></a>A frissítés letöltése
Letöltheti az Azure verem 1802 frissítési csomagot [Itt](https://aka.ms/azurestackupdatedownload).


## <a name="more-information"></a>További információ
A Microsoft közzétett segítségével figyelheti, és folytathatja a használatával a privilegizált végpont (EGP) frissítés 1710 telepített frissítések.

- Tekintse meg a [figyelése Azure-készletben a rendszerjogosultságú végpont dokumentációnak frissítések](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update).

## <a name="see-also"></a>Lásd még

- A frissítés-kezelés az Azure-verem áttekintését lásd: [kezelheti a frissítéseket az Azure-verem áttekintés](azure-stack-updates.md).
- Az Azure veremnek megfelelő frissítések alkalmazásával kapcsolatos további információkért lásd: [Azure verem frissítések alkalmazása](azure-stack-apply-updates.md).
