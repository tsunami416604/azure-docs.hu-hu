---
title: Az Azure Stack 1805 frissítés |} A Microsoft Docs
description: További tudnivalók what's new in 1805 frissítés az Azure Stack integrált rendszerek, beleértve az ismert problémákat és hová töltse le a frissítést.
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
ms.date: 07/11/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 3ef910cc588b095b0c10f5e0928d7fadf17113f6
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989809"
---
# <a name="azure-stack-1805-update"></a>Azure Stack 1805 frissítése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek*

Ez a cikk ismerteti a fejlesztést, és javítja a 1805 frissítési csomagban, és eltávolításának ismert problémái ezen verziója, valamint a helyét, a frissítés letöltése. Ismert problémákkal kapcsolatban közvetlenül a frissítési folyamat és a build (telepítés utáni) problémái vannak felosztva.

> [!IMPORTANT]        
> Ez a csomag csak olyan Azure Stack integrált rendszerek. Ez a csomag nem vonatkoznak az Azure Stack Development Kit.

## <a name="build-reference"></a>Hivatkozás létrehozása    
Az Azure Stack 1805 frissítés buildszáma **1.1805.1.47**.  

> [!TIP]  
> Ügyfeleink visszajelzései alapján, a verzió sémát, használja a Microsoft Azure Stackhez készült frissítése nincs.  Ezzel a frissítéssel 1805, kezdve az új sémában jobban jelöli az aktuális felhő verzió.  
> 
> A verzió séma már *Version.YearYearMonthMonth.MinorVersion.BuildNumber* , a második és harmadik csoportok verziójú és kiadású jelzi-e. Például 1805.1 jelöli a *RTM verzió kibocsátási* 1805 (RTM) verzióját.  


### <a name="new-features"></a>Új funkciók
Ez a frissítés az Azure Stack a következő fejlesztéseket tartalmazza.
<!-- 2297790 - IS, ASDK --> 
- **Az Azure Stack már tartalmaz egy *Syslog* ügyfél** , egy *előzetes verziójú funkció*. Ez az ügyfél lehetővé teszi, hogy a Syslog server vagy a biztonsági biztonságiadat- és eseménykezelés (SIEM) felügyeleti szoftverek külső, az Azure Stackhez az Azure Stack-infrastruktúra kapcsolódó naplózási és biztonsági naplók továbbítását. Jelenleg a Syslog-ügyfél csak támogatja az UDP-kapcsolatok nem hitelesített alapértelmezett 514-es porton keresztül. Minden Syslog-üzenet a hasznos a Common Event Format (CEF) van formázva. 

  A Syslog-ügyfél konfigurálásához használja a **Set-SyslogServer** parancsmag között szerepelnek a kiemelt végponthoz. 

  Ebben az előzetes kiadásban a következő három riasztások jelenhet meg. Előadó: Azure Stack, ha ezek a riasztások belefoglalása *leírások* és *szervizelési* útmutatást. 
  - TITLE: A Kódintegritás kikapcsolása  
  - TITLE: A Kódintegritás rendszervizsgálati módban 
  - CÍM: Felhasználói fiók létrehozása

  Bár ez a funkció előzetes verzióban érhető el, akkor érdemes nem lehet hivatkozni az éles környezetben.   




### <a name="fixed-issues"></a>Rögzített kapcsolatos problémák

<!-- # - applicability -->
- Hogy javítva lett a probléma által blokkolt [új támogatási kérést adhat a legördülő listából](azure-stack-manage-portals.md#quick-access-to-help-and-support) a a felügyeleti portálon. Most ezt a beállítást helyesen működik. 

- **Különböző javításokat** teljesítményét, stabilitását, biztonsági és az Azure Stack által használt operációs rendszer.


<!-- # Additional releases timed with this update    -->



## <a name="before-you-begin"></a>Előkészületek    

### <a name="prerequisites"></a>Előfeltételek
- Az Azure Stack telepítése [1804 frissítése](azure-stack-update-1804.md) az Azure Stack 1805 frissítés alkalmazása előtt.    
- 1805 frissítés telepítésének megkezdése előtt futtassa [Test-AzureStack](azure-stack-diagnostic-test.md) állapotát, az Azure Stack érvényesítéséhez és a kapcsolódó működési problémák megoldásához. Emellett tekintse át az aktív riasztások, és oldja meg az esetleges beavatkozást igénylő. 

### <a name="known-issues-with-the-update-process"></a>A frissítési folyamat ismert problémái   
- A 1805 frissítés telepítése során láthatja a címmel riasztások *hiba – a sablon typu FaultType UserAccounts.New hiányzik.*  Ezek a riasztások nyugodtan figyelmen kívül hagyhatja. Ezek a riasztások automatikusan 1805, a frissítés befejezése után bezárul.   

- <!-- 2489559 - IS --> Ne kísérelje meg a frissítés telepítése során a virtuális gépek létrehozása. További információ a frissítések, seSe kezelése [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Frissítés utáni lépések
1805 a telepítés után bármely alkalmazandó gyorsjavítások telepítéséről. További információt a következő cikkeket, megtekintése, valamint a [karbantartási szabályzat](azure-stack-servicing-policy.md).  
 - [KB 4344102 – az Azure Stack gyorsjavítás 1.1805.7.57](https://support.microsoft.com/help/4344102).


## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)
Az alábbiakban a telepítés utáni ismert hibáit a build-verziószáma.

### <a name="portal"></a>Portál  
- <!-- 2551834 - IS, ASDK --> Ha bejelöli **áttekintése** a rendszergazdai vagy a felhasználói portált, az információkat a storage-fiókok a *Essentials* ablaktáblában nem jelenik meg.  Az alapvető erőforrások panelen például a fiók információit jeleníti meg a *erőforráscsoport*, *hely*, és *előfizetés-azonosító*.  Egyéb lehetőségek áttekintő is elérhető, például *szolgáltatások* és *figyelés*, is, megadhatja, hogy *Megnyitás az Explorerben* vagy *tárfiók törlése* . 

  A nem érhető el információ megtekintéséhez használja a [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) PowerShell-parancsmagot. 

- <!-- 2551834 - IS, ASDK --> Ha bejelöli **címkék** a rendszergazdai vagy a felhasználói portált a storage-fiókok, az adatokat nem sikerül betölteni, és nem jeleníti meg.  

  A nem érhető el információ megtekintéséhez használja a [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) PowerShell-parancsmagot.


- <!-- 2332636 - IS -->  Az AD FS használata az Azure Stack identitásrendszer és a frissítés erre a verzióra, az Azure Stack, az alapértelmezett szolgáltatója előfizetés alapértelmezett tulajdonosa áll vissza a beépített **CloudAdmin** felhasználói.  
  Megkerülő megoldás: A frissítés telepítése után a probléma megoldásához használja a 3. lépés a [eseményindító automation konfigurálása jogcím-szolgáltatói megbízhatósági az Azure Stackben](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) eljárás alaphelyzetbe állítani az alapértelmezett szolgáltatója előfizetés tulajdonosa.   

- <!-- TBD - IS ASDK --> Bizonyos felügyeleti előfizetés-típusok nem érhető el.  Azure Stack erre a verzióra frissít, ha a két előfizetés, amely-típusokat [verziójú 1804-es verzióban bevezetett](azure-stack-update-1804.md#new-features) nem láthatók a konzolon. Ez a várható eredmény. A nem érhető el előfizetés-típusok a következők *előfizetés mérési*, és *Használatalapú előfizetés*. Ezek a típusok előfizetés új Azure Stack-környezetek verziójától kezdve az 1804 láthatók, de még nem használatra kész. Ön továbbra is használja a *alapértelmezett szolgáltató* előfizetés-típus.  

- <!-- 2403291 - IS ASDK --> Lehet, hogy nincs használatát a vízszintes görgetősáv a rendszergazdai és felhasználói portál alján. Ha nem éri el a vízszintes görgetősáv, az útkövetés, keresse meg a portálon egy előző panelen válassza a panel neve, a tetején található navigációs listában megtekinteni kívánt használati a portál bal oldali.
  ![Webhely-navigációs](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> Ez nem lehetséges számítási és tárolási erőforrások megtekintése a felügyeleti portálon. A probléma oka hiba történt a frissítés sikeres helytelenül jelenti a frissítés telepítése során. Ha a probléma akkor fordul elő, forduljon segítségért a Microsoft ügyfél-támogatási szolgálathoz.

- <!-- TBD - IS --> Előfordulhat, hogy megjelenik egy üres irányítópult, a portálon. Szeretné használni az irányítópultot, kattintson a fogaskerék ikonra a portál jobb felső sarokban, és válassza **alapértelmezett beállítások visszaállítása**.

- <!-- TBD - IS ASDK --> Felhasználói előfizetések eredmények az árva erőforrások törlése. Áthidaló megoldásként először törölje a felhasználó vagy a teljes erőforráscsoportot, és törölje a felhasználói előfizetések.

- <!-- TBD - IS ASDK --> Engedélyek nem tekintheti meg az Azure Stack portálok használata az előfizetéshez. Áthidaló megoldásként a PowerShell segítségével ellenőrizze az engedélyeket.


### <a name="health-and-monitoring"></a>Állapot és figyelés
- <!-- 1264761 - IS ASDK -->  A riasztásokat a *egészségügyi vezérlő* összetevő, amely rendelkezik a következő adatokat:  
- 
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

  Mindkét riasztás #1. és #2 biztonságosan figyelmen kívül hagyható, és automatikusan fog bezárása idővel. 

  Is megjelenhet a következő riasztás *kapacitás*. A riasztás leírásában azonosított rendelkezésre álló memória százalékos eltérőek lehetnek:  

  Riasztás a #3:
   - NAME: Kevés a memória-kapacitás
   - SÚLYOSSÁG: kritikus
   - ÖSSZETEVŐ: kapacitás
   - Leírás: A régió lefoglalta a rendelkezésre álló memória több mint 80,00 %. Virtuális gépek a nagy mennyiségű memóriát, előfordulhat, hogy nem lehet létrehozni.  

  Ebben a verzióban az Azure Stack ezt a riasztást az helytelenül is aktiválódik. Ha a bérlői virtuális gépek továbbra is sikeresen telepíteni, nyugodtan figyelmen kívül hagyhatja ezt a riasztást. 
  
  Riasztási #3 nem automatikusan zárja be. Ez a riasztás bezárása Azure Stack fog létrehozni ugyanabból a riasztásból 15 percen belül.  

- <!-- 2368581 - IS. ASDK --> Ha kevés a szabad memória figyelmeztetést, és a bérlői virtuális gépek sikertelen üzembe helyezés az Azure Stack operátorait szerint egy *Fabric Virtuálisgép-létrehozási hiba*, lehetséges, hogy az Azure Stack-blokk esik a rendelkezésre álló memória. Használja a [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) legjobban megérteni az elérhető kapacitás a számítási feladatokhoz. 


### <a name="compute"></a>Compute
- <!-- TBD - IS, ASDK --> Amikor kiválasztja a virtuális gép üzembe helyezéséhez virtuálisgép-méretet, néhány F-sorozat Virtuálisgép-méretek nem láthatók a virtuális gép létrehozásakor a méret választó részeként. Az alábbi virtuális gépeinek-választójában jelenítse nem jelennek meg: *F8s_v2*, *F16s_v2*, *F32s_v2*, és *F64s_v2*.  
  Áthidaló megoldásként használja a következő módszerek egyikét virtuális gép üzembe helyezése. Az egyes módszerek kell adja meg a használni kívánt virtuális gép méretét.

  - **Az Azure Resource Manager-sablon:** egy sablont használ, amikor a *vmSize* egyenlő a használni kívánt Virtuálisgép-méretet a sablonban. Ha például a következő bejegyzés központi telepítéséhez használandó használó virtuális gép a *F32s_v2* mérete:  

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

- <!-- TBD - IS ASDK --> Az Azure Stack felhasználói portál virtuális gépek létrehozásakor a portál megjeleníti, hogy a DS sorozatú virtuális gép csatlakoztathat adatlemezek száma helytelen. DS sorozatú virtuális gépek, az Azure-konfiguráció lehetővé teszi tetszőleges számú adatlemezeket.

- <!-- TBD - IS ASDK --> Egy Virtuálisgép-lemezkép nem hozható létre, amikor egy sikertelen elem, amely nem törölhető, előfordulhat, hogy lehet hozzáadni a VM-lemezképek számítás panel.

  Áthidaló megoldásként hozzon létre egy új Virtuálisgép-lemezkép hozható létre a Hyper-V keresztül próba VHD-vel (New-VHD-elérési út C:\dummy.vhd – rögzített - SizeBytes 1 GB-os). Ez a folyamat kell kijavítani a problémát, amely megakadályozza, hogy a sikertelen elem törlése. Ezt követően a helyőrző rendszerkép létrehozása után 15 percig sikeresen törölheti.

  Ezután próbálkozzon töltse a Virtuálisgép-rendszerképet, amely korábban nem sikerült.

- <!-- TBD - IS ASDK --> Ha egy virtuális gép üzembe helyezése a bővítmény kiépítési túl hosszú időt vesz igénybe, felhasználók hagyja, hogy a kiépítési időtúllépési helyett a folyamat fel vagy töröljön a virtuális gép leállítása.  

- <!-- 1662991 IS ASDK --> Linux rendszerű virtuális gép diagnosztikai nem támogatott az Azure Stackben. A Virtuálisgép-diagnosztika engedélyezve van egy Linux rendszerű virtuális Gépet helyez üzembe, a központi telepítés sikertelen lesz. Az üzembe helyezés akkor is sikertelen, ha engedélyezi a Linux rendszerű virtuális gép alapvető metrikák diagnosztikai beállításokon keresztül.  


### <a name="networking"></a>Hálózat
- <!-- TBD - IS ASDK --> Nem hozhat létre felhasználó által megadott útvonalakat vagy a rendszergazda vagy a felhasználói portálon. Áthidaló megoldásként használja [Azure PowerShell-lel](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

- <!-- 1766332 - IS ASDK --> A **hálózatkezelés**, ha rákattint **VPN-átjáró létrehozása** egy VPN-kapcsolat beállítása **csoportházirend-alapú** jelenik meg a VPN-típust. Válassza ezt a beállítást. Csak a **útválasztó alapú** beállítás támogatott az Azure Stackben.

- <!-- 2388980 - IS ASDK --> Miután egy virtuális Gépet létrehozni, és társított nyilvános IP-címet, IP-címet a virtuális gép nem szüntesse meg. Társításának úgy tűnik, hogy működik, de az előzőleg hozzárendelt nyilvános IP-cím marad az eredeti virtuális gép társítva.

  Jelenleg csak új nyilvános IP-címeket kell használnia az új virtuális gépek létrehozása.

  Ez akkor fordul elő, akkor is, ha új virtuális gép IP-cím ismételt hozzárendelése (más néven egy *virtuális IP-címcsere*). Az összes jövőbeli kapcsolódni a kapcsolatot az eredeti virtuális gép, és a egy újra IP cím eredményéből.

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


### <a name="sql-and-mysql"></a>SQL- és MySQL

- <!-- TBD - IS --> Konfigurációelemek létrehozása a kiszolgálókon, a fogadó SQL vagy MySQL csak az erőforrás-szolgáltató támogatott. A gazdagép-kiszolgálón létrehozott elemek nem az erőforrás-szolgáltató által létrehozott egy nem megfelelő állapot eredményezhet.  

- <!-- IS, ASDK --> Különleges karaktereket, szóközöket és időszakok, többek között nem támogatottak a **termékcsalád** vagy **szint** SQL- és MySQL erőforrás-szolgáltatókat létrehozásakor a Termékváltozat-neveket.


> [!NOTE]  
> <!-- TBD - IS --> Miután frissítette az Azure Stack 1805, továbbra is használhatja az SQL- és MySQL erőforrás-szolgáltatókat korábban már telepített.  Azt javasoljuk, hogy frissítse az SQL és a MySQL amikor új kiadása elérhetővé válik. Azure Stack, például frissítések alkalmazása az SQL- és MySQL erőforrás-szolgáltatók egymás után. Például 1803 verzióját használja, ha először a alkalmazni az 1804 verziója, és frissítse a 1805.      
>   
> A telepítés, frissítés 1805 nem befolyásolja az aktuális használatát a felhasználók által SQL vagy MySQL típusú erőforrás-szolgáltatók.
> Az erőforrás-szolgáltatókat használ verziójától függetlenül adatbázisaikat a felhasználók adatait nem megszáradásukig, és elérhető marad.    



### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Felhasználók, az első Azure-függvény létrehozása az előfizetés előtt regisztrálnia kell a tárolásierőforrás-szolgáltató.

- <!-- 2489178 - IS ASDK --> Annak érdekében, hogy a horizontális felskálázás infrastruktúra (feldolgozók, felügyeleti, előtér-szerepkörök), PowerShell számítási a kibocsátási megjegyzésekben leírtak szerint kell használnia.

- <!-- TBD - IS ASDK --> App Service-ben csak lesz üzembe helyezve a *alapértelmezett szolgáltatói előfizetés* jelenleg. Egy következő frissítés App Service-ben fog helyezze üzembe az új *előfizetés mérési* , amely az Azure Stack 1804-es verzióban jelent meg. Mérés való használata támogatott, ha minden meglévő üzemelő példányt átkerülnek az új előfizetés típusa.


### <a name="usage"></a>Használat  
- <!-- TBD - IS ASDK --> Használati nyilvános IP-használati mérőszámok adatait jeleníti meg, azonos *EventDateTime* érték helyett minden egyes rekordja esetében a *TimeDate* , amely megjeleníti a rekord létrehozásának stamp. Ezeket az adatokat jelenleg nem használható nyilvános IP-címek használata pontos nyilvántartása végrehajtásához.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>A frissítés letöltése
Letöltheti az Azure Stack 1805 frissítési csomagot [Itt](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Lásd még
- Az emelt szintű végpontját (EGP) használatával figyelheti, és folytathatja a frissítéseket, lásd: [monitorozása az Azure Stack használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).
- A frissítéskezelés az Azure Stackben áttekintését lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md).
- Az Azure Stack frissítések alkalmazásával kapcsolatos további információkért lásd: [alkalmazza a frissítéseket az Azure Stackben](azure-stack-apply-updates.md).
