---
title: Az Azure Stack 1804 frissítés |} A Microsoft Docs
description: 'További tudnivalók: Mi az az Azure Stack 1804 frissítés integrált rendszerek, az ismert problémák és hová töltse le a frissítést.'
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
ms.date: 08/01/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 0190298cbf6352feeb71e365f5815e174c9e30cc
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413516"
---
# <a name="azure-stack-1804-update"></a>Azure Stack 1804 frissítése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek*

Ez a cikk ismerteti a fejlesztések, és javítja a 1804 frissítési csomag, ismert problémák az ebben a kiadásban, valamint a helyét, a frissítés letöltése. Ismert problémákkal kapcsolatban közvetlenül a frissítési folyamat és a build (telepítés utáni) problémái vannak felosztva.

> [!IMPORTANT]        
> Ez a csomag csak olyan Azure Stack integrált rendszerek. Ez a csomag nem vonatkoznak az Azure Stack Development Kit.

## <a name="build-reference"></a>Hivatkozás létrehozása    
Az Azure Stack 1804 frissítés buildszáma **20180513.1**.   

### <a name="new-features"></a>Új funkciók
Ez a frissítés az Azure Stack a következő fejlesztéseket tartalmazza.

- <!-- 15028744 - IS -->  **Az AD FS leválasztott Azure Stack üzemelő példányok támogatása a Visual Studio**. Most már hozzáadhat az előfizetések és hitelesítéséhez Visual Studión belül az AD FS használatával összevont felhasználói hitelesítő adatokat. 
 
- <!-- 1779474, 1779458 - IS --> **Az Av2 és az F sorozatú virtuális gépek használata**. Az Azure Stack most már használhatja a virtuális gépek, az Av2 és az F-sorozat virtuálisgép-méretek alapján. További információ: [virtuális gépek méretei az Azure Stackben támogatott](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). 

- <!-- 1759172 - IS, ASDK --> **Új felügyeleti előfizetések**. Az 1804 két új előfizetés típusa van a portálon. E új típusú előfizetésessel rendelkező verziójától kezdve az 1804 új Azure Stack telepítése, az az alapértelmezett szolgáltatója előfizetés mellett, és látható. *Ne használja az Azure Stack touto verzí e új típusú előfizetésessel*. A rendelkezésre állási egy következő frissítés be ezen előfizetés tárolótípus tesszük közzé. 

  Ha az Azure Stack 1804-verzióra frissít, a két új előfizetés-típus nem láthatók el. Azonban új telepítések esetén az Azure Stack integrált rendszerek, és az Azure Stack Development Kit 1804 vagy újabb verziójára telepítéseit fér hozzá a minden három előfizetés esetében.  

  Ezek új előfizetés típusú az alapértelmezett szolgáltatója előfizetés biztonságos, és megkönnyíti a megosztott erőforrások, például az SQL futtató kiszolgálók üzembe helyezése nagyobb változás részét képezik. A nagyobb módosítás későbbi frissítésekkel további részeit hozzáadása az Azure Stackhez, e új típusú előfizetésessel alatt üzembe helyezett erőforrások elveszhetnek. 

  Most már látható a három előfizetési típusok a következők:  
  - Szolgáltatói előfizetés alapértelmezett: továbbra is használhatja ezt az előfizetéstípust. 
  - Előfizetés mérési: *ne használja ezt az előfizetéstípust.*
  - Használatalapú előfizetés: *ne használja az előfizetés típusa*

  



## <a name="fixed-issues"></a>Rögzített kapcsolatos problémák

- <!-- IS, ASDK -->  A felügyeleti portálon már nem rendelkezik a frissítés csempe frissítése előtt adatait jeleníti meg.
 
- <!-- 2050709  -->  Most már használhatja a felügyeleti portálon tármetrikák Blob service, a Table service és a Queue szolgáltatás szerkesztése.
 
- <!-- IS, ASDK --> A **hálózatkezelés**, amikor kattint **kapcsolat** egy VPN-kapcsolat beállítása **Site-to-site (IPsec)** jelenleg az egyetlen elérhető beállítás.

- **Különböző javításokat** teljesítményét, stabilitását, biztonsági és az Azure Stack által használt operációs rendszer.

## <a name="additional-releases-timed-with-this-update"></a>Ez a frissítés túllépte az időkorlátot további kiadások  
A következő érhető el, azonban nem igénylik az Azure Stack frissítés 1804.
- **A Microsoft Azure Stack a System Center Operations Manager figyelési csomag frissítése**. Egy új verziója (1.0.3.0) a Microsoft System Center Operations Manager figyelési csomag az Azure Stack érhető el a [letöltése](https://www.microsoft.com/download/details.aspx?id=55184). Ebben a verzióban az egyszerű szolgáltatások a csatlakoztatott Azure Stack üzemelő példányához hozzáadásakor is használhatja. Ebben a verzióban is lehetővé teszi, hogy a szervizelési műveletek végrehajtása közvetlenül a belül az Operations Manager Update Management élményt funkciókat. Új irányítópultok megjelenítése az erőforrás-szolgáltatók, egységig bővíthető és egység csomópontok méretezése is vannak.

- **Új Azure Stack rendszergazdai PowerShell-verzió 1.3.0**.  Az Azure Stack PowerShell 1.3.0 már elérhető a telepítéshez. Ez a verzió minden rendszergazdai erőforrás-szolgáltatók kezelése az Azure Stack parancsokat biztosít.  Ezzel a kiadással bizonyos tartalmakat érvénytelenné válnak az Azure Stack eszközök githubról [tárház](https://github.com/Azure/AzureStack-Tools). 

   A telepítés részleteit, hajtsa végre a [utasításokat](azure-stack-powershell-install.md) vagy a [súgó](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) az Azure Stack-modul 1.3.0 tartalmának. 

- **Az első Azure Stack API Rest-referencia kiadása**. A [összes Azure Stack-rendszergazdai erőforrás-szolgáltató API-referencia](https://docs.microsoft.com/rest/api/azure-stack/) most már közzé van téve. 


## <a name="before-you-begin"></a>Előkészületek    

### <a name="prerequisites"></a>Előfeltételek
- Az Azure Stack telepítése [1803 frissítése](azure-stack-update-1803.md) az Azure Stack 1804 frissítés alkalmazása előtt.  
  
- Telepítse a legújabb elérhető [frissítést vagy gyorsjavítást verzió 1803](azure-stack-update-1803.md#post-update-steps). 


### <a name="known-issues-with-the-update-process"></a>A frissítési folyamat ismert problémái   
- Az 1804-frissítés telepítése során láthatja a címmel riasztások *hiba – a sablon typu FaultType UserAccounts.New hiányzik.*  Ezek a riasztások nyugodtan figyelmen kívül hagyhatja. Ezek a riasztások automatikusan 1804, a frissítés befejezése után bezárul.   
 
- <!-- TBD - IS --> Ne kísérelje meg a frissítés telepítése során a virtuális gépek létrehozása. Frissítések kezelésével kapcsolatos további információkért lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Frissítés utáni lépések
1804 a telepítés után bármely alkalmazandó gyorsjavítások telepítéséről. További információt a következő cikkeket, megtekintése, valamint a [karbantartási szabályzat](azure-stack-servicing-policy.md).  
 - [KB 4344114 – az Azure Stack gyorsjavítás 1.0.180527.15](https://support.microsoft.com/help/4344114).




### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)
Az alábbiakban a telepítés utáni ismert problémái build **20180513.1**.

#### <a name="portal"></a>Portál
- <!-- TBD - IS ASDK --> Illesztőprogram-frissítések nem alkalmazhat egy OEM-kiterjesztési csomag segítségével az Azure Stack jelen verziójában.  Nem áll megkerülő megoldás a probléma.

- <!-- 1272111 - IS --> Az Azure Stack erre a verzióra frissítése vagy telepítése után nem feltétlenül tudja megtekinteni az Azure Stack skálázási egységek a felügyeleti portálon.  
  Megkerülő megoldás: Használja a Powershellt skálázási egységek kapcsolatos információk megtekintéséhez. További információkért lásd: a [súgó](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) az Azure Stack-modul 1.3.0 tartalmának. 

- <!-- 2332636 - IS -->  Az AD FS használata az Azure Stack identitásrendszer és a frissítés erre a verzióra, az Azure Stack, az alapértelmezett szolgáltatója előfizetés alapértelmezett tulajdonosa áll vissza a beépített **CloudAdmin** felhasználói.  
  Megkerülő megoldás: A frissítés telepítése után a probléma megoldásához használja a 3. lépés a [eseményindító automation konfigurálása jogcím-szolgáltatói megbízhatósági az Azure Stackben](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) eljárás alaphelyzetbe állítani az alapértelmezett szolgáltatója előfizetés tulajdonosa.   

- <!-- TBD - IS ASDK --> Bizonyos felügyeleti előfizetés-típusok nem érhető el.  Azure Stack erre a verzióra frissít, ha a két előfizetés, amely-típusokat [verziójú 1804-es verzióban bevezetett](#new-features) nem láthatók a konzolon. Ez a várható eredmény. A nem érhető el előfizetés-típusok a következők *előfizetés mérési*, és *Használatalapú előfizetés*. Ezek a típusok előfizetés új Azure Stack-környezetek verziójától kezdve az 1804 láthatók, de még nem használatra kész. Ön továbbra is használja a *alapértelmezett szolgáltató* előfizetés-típus.  


- <!-- TBD -  IS ASDK -->Lehetővé teszi [, nyisson egy új támogatási kérelmet a legördülő listából](azure-stack-manage-portals.md#quick-access-to-help-and-support) a belül a felügyeleti portálon nem érhető el. Ehelyett használja a következő hivatkozásra:     
    - Az Azure Stack integrált rendszerek használata https://aka.ms/newsupportrequest.

- <!-- 2403291 - IS ASDK --> Lehet, hogy nincs használatát a vízszintes görgetősáv a rendszergazdai és felhasználói portál alján. Ha nem éri el a vízszintes görgetősáv, az útkövetés, keresse meg a portálon egy előző panelen válassza a panel neve, a tetején található navigációs listában megtekinteni kívánt használati a portál bal oldali.
  ![Webhely-navigációs](media/azure-stack-update-1804/breadcrumb.png) 

- <!-- TBD - IS --> Ez nem lehetséges számítási és tárolási erőforrások megtekintése a felügyeleti portálon. A probléma oka hiba történt a frissítés sikeres helytelenül jelenti a frissítés telepítése során. Ha a probléma akkor fordul elő, forduljon segítségért a Microsoft ügyfél-támogatási szolgálathoz.

- <!-- TBD - IS --> Előfordulhat, hogy megjelenik egy üres irányítópult, a portálon. Szeretné használni az irányítópultot, kattintson a fogaskerék ikonra a portál jobb felső sarokban, és válassza **alapértelmezett beállítások visszaállítása**.

- <!-- TBD - IS ASDK --> Felhasználói előfizetések eredmények az árva erőforrások törlése. Áthidaló megoldásként először törölje a felhasználó vagy a teljes erőforráscsoportot, és törölje a felhasználói előfizetések.

- <!-- TBD - IS ASDK --> Engedélyek nem tekintheti meg az Azure Stack portálok használata az előfizetéshez. Áthidaló megoldásként a PowerShell segítségével ellenőrizze az engedélyeket.

- <!-- TBD - IS ASDK --> A felügyeleti portálon, megjelenhet egy kritikus riasztása a következőhöz a *Microsoft.Update.Admin* összetevő. A riasztás nevét, leírását és szervizelési összes állapotúként jelennek meg:  
    - *Hiba – hiányzik a sablon typu FaultType ResourceProviderTimeout.*

  Ez a riasztás biztonságosan figyelmen kívül hagyhatja. 


#### <a name="health-and-monitoring"></a>Állapot és figyelés
- <!-- 1264761 - IS ASDK -->  A riasztásokat a *egészségügyi vezérlő* összetevő, amely rendelkezik a következő adatokat:  

   #1 riasztás:
   - NAME: Infrastruktúra-szerepkör nem megfelelő állapotú
   - SÚLYOSSÁG: figyelmeztetés
   - ÖSSZETEVŐ: Állapotfigyelő vezérlő
   - Leírás: A health vezérlő szívverési képolvasó nem érhető el. Ez érinthet rendszerállapot-jelentések és metrikákat.  

  Riasztási #2:
   - NAME: Infrastruktúra-szerepkör nem megfelelő állapotú
   - SÚLYOSSÁG: figyelmeztetés
   - ÖSSZETEVŐ: Állapotfigyelő vezérlő
   - Leírás: A health vezérlő tartalék képolvasó nem érhető el. Ez érinthet rendszerállapot-jelentések és metrikákat.

  Mindkét riasztás biztonságosan figyelmen kívül hagyhatja. Ezek automatikusan megszűnik idővel.  
 

#### <a name="compute"></a>Compute
- <!-- TBD - IS --> Amikor kiválasztja a virtuális gép üzembe helyezéséhez virtuálisgép-méretet, néhány F-sorozat Virtuálisgép-méretek nem láthatók a virtuális gép létrehozásakor a méret választó részeként. Az alábbi virtuális gépeinek-választójában jelenítse nem jelennek meg: *F8s_v2*, *F16s_v2*, *F32s_v2*, és *F64s_v2*.  
  Áthidaló megoldásként használja a következő módszerek egyikét virtuális gép üzembe helyezése. Az egyes módszerek kell adja meg a használni kívánt virtuális gép méretét.

  - **Az Azure Resource Manager-sablon:** egy sablont használ, amikor a *vmSize* egyenlő a kívánt VM-méret a sablonban. Ha például a következő központi telepítéséhez használandó használó virtuális gép a *F32s_v2* mérete:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Az Azure CLI:** használhatja a [az virtuális gép létrehozása](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) parancsot, és adja meg a virtuális gép méretét a paramétert, hasonló `--size "Standard_F32s_v2"`.

  - **PowerShell:** a PowerShell segítségével használható [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) a paraméterrel, amely meghatározza a virtuális gép méretét, hasonló `-VMSize "Standard_F32s_v2"`.


- <!-- TBD - IS ASDK --> A virtual machine scale sets méretezési beállításait a portálon nem érhetők el. Áthidaló megoldásként használhatja [Azure PowerShell-lel](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). PowerShell-verzió különbségek, miatt kell használnia a `-Name` paraméter helyett `-VMScaleSetName`.

- <!-- TBD - IS --> Amikor létrehoz egy rendelkezésre állási csoportot a portálon a **új** > **számítási** > **rendelkezésre állási csoport**, csak hozhat létre egy rendelkezésre állási csoport egy tartalék tartomány és a frissítési tartomány 1. Áthidaló megoldásként egy új virtuális gép létrehozásakor, a rendelkezésre állási csoportot használja a Powershellt, CLI-t, vagy létrehozása a portálon.

- <!-- TBD - IS ASDK --> Az Azure Stack felhasználói portál virtuális gépek létrehozásakor a portál megjeleníti, amelyek a D sorozatú virtuális gépek csatlakoztathat adatlemezek száma helytelen. Az összes támogatott D sorozatú virtuális gépek, az Azure konfigurációs lehetővé teszi tetszőleges számú adatlemezeket.

- <!-- TBD - IS ASDK --> Egy Virtuálisgép-lemezkép nem hozható létre, amikor egy sikertelen elem, amely nem törölhető, előfordulhat, hogy lehet hozzáadni a VM-lemezképek számítás panel.

  Áthidaló megoldásként hozzon létre egy új Virtuálisgép-lemezkép hozható létre a Hyper-V keresztül próba VHD-vel (New-VHD-elérési út C:\dummy.vhd – rögzített - SizeBytes 1 GB-os). Ez a folyamat kell kijavítani a problémát, amely megakadályozza, hogy a sikertelen elem törlése. Ezt követően a helyőrző rendszerkép létrehozása után 15 percig sikeresen törölheti.

  Ezután próbálkozzon töltse a Virtuálisgép-rendszerképet, amely korábban nem sikerült.

- <!-- TBD - IS ASDK --> Ha egy virtuális gép üzembe helyezése a bővítmény kiépítési túl hosszú időt vesz igénybe, felhasználók hagyja, hogy a kiépítési időtúllépési helyett a folyamat fel vagy töröljön a virtuális gép leállítása.  

- <!-- 1662991 IS ASDK --> Linux rendszerű virtuális gép diagnosztikai nem támogatott az Azure Stackben. A Virtuálisgép-diagnosztika engedélyezve van egy Linux rendszerű virtuális Gépet helyez üzembe, a központi telepítés sikertelen lesz. Az üzembe helyezés akkor is sikertelen, ha engedélyezi a Linux rendszerű virtuális gép alapvető metrikák diagnosztikai beállításokon keresztül.  


#### <a name="networking"></a>Hálózat
- <!-- 1766332 - IS ASDK --> A **hálózatkezelés**, ha rákattint **VPN-átjáró létrehozása** egy VPN-kapcsolat beállítása **csoportházirend-alapú** jelenik meg a VPN-típust. Válassza ezt a beállítást. Csak a **útválasztó alapú** beállítás támogatott az Azure Stackben.

- <!-- 2388980 - IS ASDK --> Miután egy virtuális Gépet létrehozni, és társított nyilvános IP-címet, IP-címet a virtuális gép nem szüntesse meg. Társításának úgy tűnik, hogy működik, de az előzőleg hozzárendelt nyilvános IP-cím marad az eredeti virtuális gép társítva.

  Jelenleg csak új nyilvános IP-címeket kell használnia az új virtuális gépek létrehozása.

  Ez akkor fordul elő, akkor is, ha új virtuális gép IP-cím ismételt hozzárendelése (más néven egy *virtuális IP-címcsere*). Az összes jövőbeli próbál meg ezt az IP cím eredményt kapcsolaton keresztül a eredetileg kapcsolódó virtuális gép, és a egy újra csatlakozhat.

- <!-- 2292271 - IS ASDK --> Ha megemeli egy kvótát egy hálózati erőforrás-ajánlat és a egy bérlői előfizetéshez társított csomag részét képező, a rendszer nem alkalmazza az új határértéket előfizetéshez. Azonban az új határértéket nő, a kvóta növelése után létrehozott új előfizetések a alkalmazni. 

  A probléma megkerüléséhez használja egy kiegészítő csomagot egy hálózati kvóta növeléséhez, amikor a csomag már társítva van egy előfizetést. További információkért lásd: hogyan [egy kiegészítő csomag elérhetővé](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> DNS-zóna erőforrások vagy társítva útvonaltábla erőforrás rendelkező előfizetés nem törölhető. Sikerült törölni az előfizetést, először törölnie kell DNS-zóna és az útvonaltábla erőforrásokhoz a bérlő előfizetésből. 
  

- <!-- 1902460 - IS ASDK --> Az Azure Stack is támogatja egyetlen *helyi hálózati átjáró* IP-címenként. Ez az összes bérlői előfizetések között. Az első helyi hálózati átjáró kapcsolatának, ezt követő létrehozását, hozzon létre egy helyi hálózati átjáró erőforrás azonos IP-címmel rendelkező kísérlet után le lesznek tiltva.

- <!-- 16309153 - IS ASDK --> A DNS-kiszolgáló beállítását a létrehozott virtuális hálózat *automatikus*, változó, egy egyéni DNS-kiszolgáló meghibásodik. A frissített beállításokat nem kerülnek az adott virtuális hálózatban lévő virtuális gépen.

- <!-- TBD - IS ASDK --> Az Azure Stack nem támogatja a virtuális gép üzembe helyezése után további hálózati adapterek hozzáadása egy Virtuálisgép-példánnyal. Ha a virtuális gép több hálózati adaptert igényel, akkor meg kell határozni központi telepítéskor.

- <!-- 2096388 IS --> A felügyeleti portálon egy hálózati biztonsági csoportra vonatkozó szabályainak frissítése nem használhat. 

    Megkerülő megoldás az App Service: a tartományvezérlő-példányok távoli asztal kell, ha módosítja a biztonsági szabályok belül a hálózati biztonsági csoportok, a PowerShell használatával.  Az alábbiakban példákat *engedélyezése*, majd állítsa vissza a konfigurációt a *megtagadása*:  
    
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

#### <a name="sql-and-mysql"></a>SQL- és MySQL

- <!-- TBD - IS --> Konfigurációelemek létrehozása a kiszolgálókon, a fogadó SQL vagy MySQL csak az erőforrás-szolgáltató támogatott. A gazdagép-kiszolgálón létrehozott elemek nem az erőforrás-szolgáltató által létrehozott egy nem megfelelő állapot eredményezhet.  

- <!-- IS, ASDK --> Különleges karaktereket, szóközöket és időszakok, többek között nem támogatottak a **termékcsalád** vagy **szint** SQL- és MySQL erőforrás-szolgáltatókat létrehozásakor a Termékváltozat-neveket.


> [!NOTE]  
> <!-- TBD - IS --> Miután frissítette az Azure Stack 1804, továbbra is használhatja az SQL- és MySQL erőforrás-szolgáltatókat korábban már telepített.  Azt javasoljuk, hogy frissítse az SQL és a MySQL amikor új kiadása elérhetővé válik. Azure Stack, például frissítések alkalmazása az SQL- és MySQL erőforrás-szolgáltatók egymás után.  Például 1802-es verzióját használja, ha először a alkalmazni 1803 verziót, és frissítse az 1804.      
>   
> Az 1804-es frissítés telepítése nem befolyásolja a felhasználók által SQL vagy MySQL típusú erőforrás-szolgáltató jelenlegi használatát.
> Az erőforrás-szolgáltatókat használ verziójától függetlenül adatbázisaikat a felhasználók adatait nem megszáradásukig, és elérhető marad.    



#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Felhasználók, az első Azure-függvény létrehozása az előfizetés előtt regisztrálnia kell a tárolásierőforrás-szolgáltató.

- <!-- TBD - IS ASDK --> Annak érdekében, hogy a horizontális felskálázás infrastruktúra (feldolgozók, felügyeleti, előtér-szerepkörök), PowerShell számítási a kibocsátási megjegyzésekben leírtak szerint kell használnia.

- <!-- TBD - IS ASDK --> App Service-ben csak lesz üzembe helyezve a **szolgáltatói előfizetés alapértelmezett** jelenleg.  Egy következő frissítés App Service-ben fog helyezze üzembe az Azure Stack 1804-ben bevezetett új szoftverhasználat-mérő előfizetés, és minden meglévő üzemelő példányt átkerülnek az új előfizetés is.

#### <a name="usage"></a>Használat  
- <!-- TBD - IS ASDK --> Használati nyilvános IP-használati mérőszámok adatait jeleníti meg, azonos *EventDateTime* érték helyett minden egyes rekordja esetében a *TimeDate* , amely megjeleníti a rekord létrehozásának stamp. Ezeket az adatokat jelenleg nem használható nyilvános IP-címek használata pontos nyilvántartása végrehajtásához.


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>A frissítés letöltése
Letöltheti az Azure Stack 1804 frissítési csomagot [Itt](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Lásd még
- Az emelt szintű végpontját (EGP) használatával figyelheti, és folytathatja a frissítéseket, lásd: [monitorozása az Azure Stack használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).
- A frissítéskezelés az Azure Stackben áttekintését lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md).
- Az Azure Stack frissítések alkalmazásával kapcsolatos további információkért lásd: [alkalmazza a frissítéseket az Azure Stackben](azure-stack-apply-updates.md).
