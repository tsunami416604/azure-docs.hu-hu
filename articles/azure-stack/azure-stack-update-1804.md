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
ms.date: 06/22/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 9fc58d971db18db63e4dc76123ff1311b77e0191
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316433"
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

- <!-- 15028744 - IS -->  **A Visual Studio támogatja a leválasztott Azure verem üzembe helyezése az AD FS**. Visual Studio most hozzáadhat előfizetések és hitelesítés az AD FS segítségével összevont felhasználói hitelesítő adatokat. 
 
- <!-- 1779474, 1779458 - IS --> **Használjon Av2 és F adatsorozat virtuális gépeket**. Azure verem használható virtuális gépeket a Av2 sorozatú és az-F-sorozat virtuálisgép-méretek alapján. További információ: [Azure verem használható virtuálisgép-méretek](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). 

- <!-- 1759172 - IS, ASDK --> **Új felügyeleti előfizetések**. A 1804 két új előfizetés típusa van a portálon elérhető. Új előfizetés típusaival 1804 verziójával kezdve új Azure verem telepítése mellett az alapértelmezett szolgáltató előfizetés, és látható vannak. *Ne használjon új előfizetés típusaival Azure verem ezen verziójában*. Azt fogja értesíthetik ezeket előfizetés használni egy későbbi kiadásában be elérhetőségét. 

  Ha Azure verem 1804 verzióra frissít, a két új előfizetés-típus nem láthatók. Azonban új központi telepítéseknél Azure verem integrált rendszerek, és az Azure verem szoftverfejlesztői készlet 1804 vagy újabb verziója-telepítések fér hozzá a három előfizetéstípusok.  

  Új előfizetés típusaival az alapértelmezett szolgáltató előfizetés védelmét, és könnyebben megosztott erőforrásokhoz, mint a SQL üzemeltető kiszolgálók telepítése nagyobb változás a részét képezik. Hogy a jövőbeli frissítések nagyobb módosítás további részeinek ad Azure verem, az új előfizetés típusaival üzembe helyezett erőforrás elvesztésével járhat. 

  A most látható három előfizetés típusok a következők:  
  - Alapértelmezett szolgáltató előfizetés: továbbra is használhatja ezt az előfizetési típust. 
  - Előfizetés mérési: *ne használja ezt az előfizetési típust.*
  - Felhasználás előfizetés: *ne használjon ennél az előfizetéstípusnál*

  



## <a name="fixed-issues"></a>Javított problémák

- <!-- IS, ASDK -->  A felügyeleti portálon már nem kell a frissítés csempe frissítése előtt adatait jeleníti meg.
 
- <!-- 2050709  -->  A felügyeleti portál segítségével most már a Blob szolgáltatás, a Table szolgáltatás és a Queue szolgáltatás storage mérőszámainak szerkesztése.
 
- <!-- IS, ASDK --> A **hálózati**, kattintva **kapcsolat** egy VPN-kapcsolat beállítása **pont-pont (IPsec)** jelenleg az egyetlen elérhető beállítás.

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
 
- <!-- TBD - IS --> Ne próbálja meg a frissítés telepítése során a virtuális gépek létrehozásához. További információ a frissítések kezelése: [kezelheti a frissítéseket az Azure-verem áttekintés](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Frissítés utáni lépések
1804 a telepítés után bármely alkalmazandó gyorsjavításainak telepítéséhez. További információ a következő tudásbáziscikkeiből, megtekintése, valamint a [karbantartása házirend](azure-stack-servicing-policy.md).  
 - [KB 4341392 - Azure verem gyorsjavítás 1.0.180523.11](https://support.microsoft.com/en-us/help/4341392).




### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)
Az alábbiakban telepítés utáni build kapcsolatos ismert problémák **20180513.1**.

#### <a name="portal"></a>Portál
- <!-- 1272111 - IS --> Az Azure-verem ezen verziójára való frissítése vagy telepítése után nem feltétlenül Azure verem méretezési egységek megtekintheti a felügyeleti portálon.  
  Megkerülő megoldás: A PowerShell szolgáltatás használatával skálázási egység adatainak megtekintése. További információkért lásd: a [súgó](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) Azure verem modul 1.3.0 tartalmát. 

- <!-- 2332636 - IS -->  Az AD FS használata az Azure-verem azonosítási rendszer és az Azure-verem ezen verziójára való frissítés esetén az alapértelmezett szolgáltató előfizetés alapértelmezett tulajdonosa lesz visszaállítva a beépített **CloudAdmin** felhasználó.  
  Megkerülő megoldás: A frissítés telepítése után a probléma megoldásához használja a 3. lépés a [konfigurálása eseményindító automatizálási jogcím-szolgáltatói megbízhatósági Azure verem](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) alaphelyzetbe állítani az alapértelmezett szolgáltató előfizetés tulajdonosának eljárást.   

- <!-- TBD - IS ASDK --> Néhány felügyeleti előfizetési típust nem érhetők el.  Amikor Azure verem erre a verzióra frissít, a két előfizetéstípusok, amely volt [1804 verziójában bevezetett](#new-features) nem láthatók a konzolon. Ez a várható eredmény. Az előfizetés nem érhető el típusok *előfizetés mérési*, és *fogyasztás előfizetés*. Előfizetés típusaival 1804 verziójával kezdve új Azure verem környezetben láthatók, de még nem állnak készen áll a használatra. Ön továbbra is használja a *alapértelmezett szolgáltató* előfizetés típusa.  


- <!-- TBD -  IS ASDK -->Lehetővé teszi [új támogatási kérést nyithat a legördülő menüből](azure-stack-manage-portals.md#quick-access-to-help-and-support) a belül a felügyeleti portál nem érhető el. Ehelyett használja a következő hivatkozásra:     
    - Azure verem integrált, rendszereihez https://aka.ms/newsupportrequest.

- <!-- 2403291 - IS ASDK --> Lehet, hogy nincs használatát a rendszergazdai és felhasználói portál alján a vízszintes görgetősáv. Ha nem fér hozzá a vízszintes görgetősáv, használja a útkövetését lehetőségre, és navigáljon a portálon egy előző panel; ehhez válassza a panel neve meg szeretné tekinteni a webhely-navigációs listából tetején található a portál bal oldali.
  ![Webhely-navigációs](media/azure-stack-update-1804/breadcrumb.png) 

- <!-- TBD - IS --> Nem lehet a számítási és tárolási erőforrások megtekintése a felügyeleti portálon lehetséges. A probléma oka hiba jelentendő helytelenül sikeres frissítés a frissítés telepítése során. Ha a probléma akkor fordul elő, forduljon a Microsoft ügyfél-támogatási szolgálathoz segítségért.

- <!-- TBD - IS --> A portál üres irányítópult jelenhet meg. Az irányítópult helyreállításához válassza ki a fogaskerék ikonra a portál jobb felső sarokban, majd válassza ki **alapértelmezett beállításainak visszaállítása**.

- <!-- TBD - IS ASDK --> Felhasználói előfizetések eredmények az árva erőforrások törlése. A probléma megoldásához először törölnie a felhasználói erőforrásokat és a teljes erőforráscsoport, és törölje a felhasználó előfizetések.

- <!-- TBD - IS ASDK --> Az előfizetéshez az Azure-verem portálok engedélyek nem lehet megtekinteni. A probléma megoldásához a PowerShell használatával ellenőrizze az engedélyeket.

- <!-- TBD - IS ASDK --> A felügyeleti portálon vonatkozó kritikus riasztás láthatja a *Microsoft.Update.Admin* összetevő. A riasztás nevét, leírását és javítási összes jeleníti meg:  
    - *Hiba – hiányzik a sablon FaultType ResourceProviderTimeout.*

  Ez a riasztás biztonságosan figyelmen kívül hagyhatja. 


#### <a name="health-and-monitoring"></a>Állapot- és figyelés
- <!-- 1264761 - IS ASDK -->  A riasztásokat a *állapotfigyelő vezérlő* komponenst, amely rendelkezik a következő adatokat:  

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
- <!-- TBD - IS --> A virtuális gép méretét, a virtuális gép központi telepítés kiválasztásakor néhány F-sorozat Virtuálisgép-méretek nem láthatók a virtuális gépek létrehozásakor a mérete választó részeként. A választó nem jelennek meg a következő Virtuálisgép-méretek: *F8s_v2*, *F16s_v2*, *F32s_v2*, és *F64s_v2*.  
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


- <!-- TBD - IS ASDK --> A virtuálisgép-méretezési csoportok skálázási beállításai nem érhetők el a portálon. Áthidaló megoldásként használja [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). PowerShell-verzió különbségek, miatt kell használnia a `-Name` paraméter helyett `-VMScaleSetName`.

- <!-- TBD - IS --> Nyissa meg a portálon beállított rendelkezésre állási létrehozásakor **új** > **számítási** > **rendelkezésre állási csoport**, csak hozhat létre egy rendelkezésre állási csoport egy tartalék tartomány és a frissítési tartomány 1. A probléma megoldásához, amikor egy új virtuális gép létrehozása, hozzon létre a rendelkezésre állási csoportot a powershellel, CLI-t, vagy a portálon.

- <!-- TBD - IS ASDK --> Az Azure-verem felhasználói portál virtuális gépek létrehozásakor, a portál megjeleníti, hogy a D sorozat VM csatolhat adatlemezek száma helytelen. Az összes támogatott D sorozat virtuális gépek lehetővé teszi tetszőleges számú adatlemezek Azure beállításként.

- <!-- TBD - IS ASDK --> Egy Virtuálisgép-lemezkép nem hozható létre, amikor egy sikertelen elem, amely nem törölhető, előfordulhat, hogy a virtuális gép képek számítási panel hozzáadni.

  A probléma megoldásához hozzon létre egy új Virtuálisgép-lemezkép, amely a Hyper-V segítségével hozhatók létre üres virtuális merevlemez (New-VHD-elérési út C:\dummy.vhd-- SizeBytes rögzített 1 GB-os). Ez a folyamat kell hárítsa el a problémát, amely megakadályozza a sikertelen elem törlése. Ezt követően a dummy lemezkép létrehozása után 15 perc sikeresen törlése.

  Ezután próbálkozzon redownload, amely korábban nem sikerült Virtuálisgép-lemezkép.

- <!-- TBD - IS ASDK --> Ha a kiterjesztést a virtuális gép üzembe helyezéséhez a kiépítés túl sokáig tart, felhasználók hagyja, hogy a létesítési időtúllépési helyett a folyamat felszabadítani, vagy törölje a virtuális gép leállítására tett kísérlet.  

- <!-- 1662991 IS ASDK --> Linux Virtuálisgép-diagnosztika Azure verem nem támogatott. Engedélyezett Virtuálisgép-diagnosztika a Linux virtuális gép telepítésekor a központi telepítés sikertelen lesz. A központi telepítés is sikertelen lesz, ha engedélyezi a Linux virtuális gép alapvető metrikák diagnosztikai beállításait.  


#### <a name="networking"></a>Hálózat
- <!-- 1766332 - IS ASDK --> A **hálózati**, ha **VPN Gateway létrehozása** egy VPN-kapcsolat beállítása **csoportházirend-alapú** van megadva, a VPN-típus. Válassza ezt a beállítást. Csak a **útválasztó alapú** Azure-verem lehetőség.

- <!-- 2388980 - IS ASDK --> Miután egy virtuális Gépet létrehozták, és a társított egy nyilvános IP-cím, IP-címet a virtuális gép nem szüntesse meg. Disassociation úgy tűnik, hogy működik, de a korábban hozzárendelt nyilvános IP-cím marad az eredeti virtuális társítva.

  Jelenleg létrehozhat új virtuális gépek csak új nyilvános IP-címet kell használnia.

  Ez akkor fordul elő, akkor is, ha egy új virtuális géphez az IP-cím ismételt hozzárendelése (más néven a *virtuális IP-címcsere*). Az összes jövőbeni megpróbál a kapcsolaton keresztül a eredetileg társított virtuális Gépet, és nem egy IP-cím eredményén keresztül kapcsolódni.

- <!-- 2292271 - IS ASDK --> Ha egy hálózati erőforráshoz, amely része egy ajánlatot és tervet készíteni, amely a bérlői előfizetéshez kvótát, az új korlát nincs telepítve az előfizetéshez. Azonban az új korlát vonatkozik jobb lesz, a kvóta után létrehozott új előfizetések. 

  Ez a probléma megoldása érdekében egy bővítmény csomag használata a hálózati kvóta növeléséhez, ha a csomag már társítva van egy előfizetést. További információkért lásd: hogyan [bővítmény tervének elérhetővé](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> DNS-zóna erőforrásokat és a vele társított útvonaltábla erőforrások rendelkező előfizetés nem törölhető. Az előfizetés sikeresen törléséhez először törölnie kell DNS-zónát és útválasztási táblázatot erőforrások a bérlői előfizetéshez. 
  

- <!-- 1902460 - IS ASDK --> Azure verem támogatja egyetlen *helyi hálózati átjáró* / IP-címet. Ez az összes bérlői előfizetések között. Az első helyi hálózati átjáró kapcsolat létrehozására, későbbi helyi hálózati átjáró erőforrás létrehozása a azonos IP-címmel kísérlet után sem.

- <!-- 16309153 - IS ASDK --> A virtuális hálózat DNS-kiszolgáló használatára létrehozott *automatikus*, változó számára egy egyéni DNS-kiszolgáló sikertelen. A frissített beállításokkal nem elküldte azokat a virtuális gépekhez a Vneten belül.

- <!-- TBD - IS ASDK --> Az Azure verem nem támogatja a további hálózati adapterek hozzáadása egy Virtuálisgép-példány, a virtuális gép telepítését követően. Ha a virtuális gép több hálózati adapter szükséges, akkor meg kell adni a központi telepítéskor.

- <!-- 2096388 IS --> A felügyeleti portál használatával nem frissíteni a hálózati biztonsági csoport szabályainak. 

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

- <!-- TBD - IS --> Az erőforrás-szolgáltató, hogy a gazdagép SQL vagy MySQL elemek létrehozásához kiszolgálókon támogatott. A gazdagép-kiszolgálón létrehozott elemek, nem az erőforrás-szolgáltató által létrehozott okozhatja hibás állapotban.  

- <!-- IS, ASDK --> Nem támogatottak a különleges karaktereket, szóközöket és időszakok, beleértve a **termékcsalád** vagy **réteg** neve, amikor az SQL és a MySQL-szolgáltatók hoz létre a Termékváltozat.


> [!NOTE]  
> <!-- TBD - IS --> Az Azure verem 1804 frissítése után továbbra is használja az SQL és MySQL erőforrás-szolgáltató korábban már telepített.  Azt javasoljuk, hogy frissítse az SQL és MySQL amikor elérhetővé válik az új verziót. Azure-vermét, például frissítések alkalmazása az SQL és a MySQL-szolgáltatók egymás után.  Például 1802 verzióját használja, ha először a verzió 1803 vonatkoznak, és 1804 frissíti.      
>   
> Frissítés 1804 telepítése nem befolyásolja a felhasználók által MySQL vagy az SQL-erőforrás-szolgáltató aktuális használatát.
> Az erőforrás-szolgáltató használata verziója, függetlenül a felhasználók adatait, a hozzájuk tartozó adatbázisok nincs megszáradásukig, és elérhető marad.    



#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Ahhoz, hogy az első Azure-függvény létre előfizetést felhasználók regisztrálnia kell a storage erőforrás-szolgáltató.

- <!-- TBD - IS ASDK --> Ahhoz, hogy az infrastruktúra kiterjesztése olyan esetekben (munkavállalók, kezelési, előtér-szerepkörök), PowerShell számítás a kibocsátási megjegyzésekben leírt módon kell használnia.

- <!-- TBD - IS ASDK --> App Service csak telepíthető a a **alapértelmezett szolgáltató előfizetés** most.  Az egy későbbi kiadásban az üzembe helyezés az Azure verem 1804 rendszerben bevezetett új szoftverhasználat-mérő előfizetés lesz az App Service, és minden meglévő üzemelő példányt telepíti át az új előfizetés is.

#### <a name="usage"></a>Használat  
- <!-- TBD - IS ASDK --> Használati nyilvános IP-használat mérési adatokat jeleníti meg az azonos *EventDateTime* érték az egyes rekordokhoz ahelyett, hogy a *TimeDate* tájékoztat, hogy a rekord létrehozásának stamp. Ezeket az adatokat jelenleg nem használható a nyilvános IP-cím használatának pontos lehetet.


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>A frissítés letöltése
Letöltheti az Azure verem 1804 frissítési csomagot [Itt](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Lásd még
- A privilegizált végpont (EGP) segítségével figyelheti, és folytassa a frissítést, lásd: [figyelése Azure verem használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).
- A frissítés-kezelés az Azure-verem áttekintését lásd: [kezelheti a frissítéseket az Azure-verem áttekintés](azure-stack-updates.md).
- Az Azure veremnek megfelelő frissítések alkalmazásával kapcsolatos további információkért lásd: [Azure verem frissítések alkalmazása](azure-stack-apply-updates.md).
