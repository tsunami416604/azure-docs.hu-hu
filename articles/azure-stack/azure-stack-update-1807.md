---
title: Az Azure Stack 1807 frissítés |} A Microsoft Docs
description: További tudnivalók what's new in 1807 frissítés az Azure Stack integrált rendszerek, beleértve az ismert problémákat és hová töltse le a frissítést.
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
ms.date: 07/24/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: fe37a62d364d53d03a232b6fdc41e38a9ae2e30f
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39228047"
---
# <a name="azure-stack-1807-update"></a>Azure Stack 1807 frissítése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek*

Ez a cikk ismerteti a 1807 csomag tartalmát. A frissítés tartalmazza a fejlesztések, javításokat és ismert problémák az Azure Stack, valamint a helyét, a frissítés letöltése ehhez a verzióhoz. Ismert problémákkal kapcsolatban közvetlenül a frissítési folyamat és a build (telepítés utáni) problémái vannak felosztva.

> [!IMPORTANT]        
> Ez a csomag csak olyan Azure Stack integrált rendszerek. Ez a csomag nem vonatkoznak az Azure Stack Development Kit.

## <a name="build-reference"></a>Hivatkozás létrehozása    
Az Azure Stack 1807 frissítés buildszáma **1.1807.XX.X**.  


### <a name="new-features"></a>Új funkciók
Ez a frissítés az Azure Stack a következő fejlesztéseket tartalmazza.

- <!-- 1658937 | ASDK, IS --> **Indítsa el a biztonsági mentések előre meghatározott ütemezés szerint** -telepíthetőek, mint az Azure Stack is automatikusan aktiválja infrastruktúra biztonsági mentések rendszeres időközönként az emberi beavatkozás kiküszöbölése érdekében. Az Azure Stack biztonsági mentésekhez, régebbi, mint a meghatározott adatmegőrzési időszaka a külső megosztást is automatikusan törölni fogja. 

- <!-- 2496385 | ASDK, IS --> **A hozzáadott adatátviteli időt a teljes biztonsági mentés időpontja.**

-   <!-- 1702130 | ASDK, IS --> **Biztonsági mentési külső kapacitás most a megfelelő kapacitást a külső megosztás jeleníti meg.** (Ez korábban rögzített kód 10 GB-ra.)

- <!-- 2508488 |  IS   -->   Kapacitás által bővítése [kiegészítő skálázási egység csomópontok hozzáadása](azure-stack-add-scale-node.md).



### <a name="fixed-issues"></a>Rögzített kapcsolatos problémák

- <!-- 448955 | IS ASDK --> Most már sikeresen lekérdezheti a Tevékenységnaplók az időzóna UTC + N üzembe helyezett rendszerekhez.    

- <!-- 2319627 |  ASDK, IS --> Biztonsági mentési konfiguráció paraméterei (elérési út/felhasználónév/jelszó/titkosítási kulcs) előzetes ellenőrzése nem megfelelő beállítások már nem a biztonsági mentési konfigurációhoz állítja be. (Korábban a helytelen beállításokkal állított be a biztonsági mentés és a biztonsági mentést szeretne majd sikertelen mikor tirggered.)

- <!-- 2215948 |  ASDK, IS --> A biztonsági mentési listában most frissíti a külső megosztást a manuálisan törölje a biztonsági mentés során.

- <!-- 2332636 - IS -->  Frissítés erre a verzióra már nem állítja vissza az alapértelmezett szolgáltatója előfizetés alapértelmezett tulajdonosa a beépített CloudAdmin felhasználó és az AD FS telepítésekor.

- <!-- 2360715 |  ASDK, IS -->  Adatközpont integrációja beállításakor, már nem elérhető az AD FS metaadatait tartalmazó fájl egy megosztást. További információkért lásd: [összevonási metaadatait tartalmazó fájl azáltal, hogy az AD FS-integráció beállításával](azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

- <!-- 2388980 | ASDK, IS --> Hogy kijavítva, hogy megakadályozta abban a rendelt felhasználók egy meglévő nyilvános IP-címet, amely korábban korábban hozzárendelve egy hálózati adapter vagy egy új hálózati adapter vagy a terheléselosztó a terheléselosztó.  

-   <!--2292271 | ASDK, IS --> Ahol egy módosított kvótakorlát miatt nem vonatkozik a meglévő előfizetések probléma tudjuk rögzíteni.  Most ha egy kvótát egy hálózati erőforráshoz tartozó ajánlat emeli, és a egy bérlői előfizetéshez tartozó csomag, az új határértéket vonatkozik a már meglévő előfizetéseket, valamint a új előfizetések.

- **Különböző javításokat** teljesítményét, stabilitását, biztonsági és az Azure Stack által használt operációs rendszer.


<!-- ### Additional releases timed with this update    -->



## <a name="before-you-begin"></a>Előkészületek    

### <a name="prerequisites"></a>Előfeltételek
- Az Azure Stack telepítése [1805 frissítése](azure-stack-update-1805.md) az Azure Stack 1807 frissítés alkalmazása előtt.  Hiba történt a frissítés nélkül 1806.  

- 1807 frissítés telepítésének megkezdése előtt futtassa [Test-AzureStack](azure-stack-diagnostic-test.md) állapotát, az Azure Stack érvényesítéséhez és a kapcsolódó működési problémák megoldásához. Emellett tekintse át az aktív riasztások, és oldja meg az esetleges beavatkozást igénylő.

### <a name="known-issues-with-the-update-process"></a>A frissítési folyamat ismert problémái   
- <!-- 2468613 - IS --> Ez a frissítés telepítése során láthatja a címmel riasztások *hiba – a sablon typu FaultType UserAccounts.New hiányzik.*  Ezek a riasztások nyugodtan figyelmen kívül hagyhatja. A frissítés telepítésének befejezése után ezek a riasztások automatikusan bezáródik.   

- <!-- 2489559 - IS --> Ne kísérelje meg a frissítés telepítése során a virtuális gépek létrehozása. Frissítések kezelésével kapcsolatos további információkért lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Frissítés utáni lépések
*Nincsenek nem frissítés 1807 frissítés utáni lépéseket.*

<!-- After the installation of this update, install any applicable Hotfixes. For more information view the following knowledge base articles, as well as our [Servicing Policy](azure-stack-servicing-policy.md).  
 - Link to KB  
 -->

## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)
Az alábbiakban a telepítés utáni ismert hibáit a build-verziószáma.

### <a name="portal"></a>Portál  
- <!-- 2551834 - IS, ASDK --> Ha bejelöli **áttekintése** a rendszergazdai vagy a felhasználói portált, az információkat a storage-fiókok a *Essentials* ablaktáblában nem jelenik meg.  Az alapvető erőforrások panelen például a fiók információit jeleníti meg a *erőforráscsoport*, *hely*, és *előfizetés-azonosító*.  Egyéb lehetőségek áttekintő is elérhető, például *szolgáltatások* és *figyelés*, is, megadhatja, hogy *Megnyitás az Explorerben* vagy *tárfiók törlése* .

  A nem érhető el információ megtekintéséhez használja a [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) PowerShell-parancsmagot.

- <!-- 2551834 - IS, ASDK --> Ha bejelöli **címkék** a rendszergazdai vagy a felhasználói portált a storage-fiókok, az adatokat nem sikerül betölteni, és nem jeleníti meg.  

  A nem érhető el információ megtekintéséhez használja a [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) PowerShell-parancsmagot. 

- <!-- TBD - IS ASDK --> Bizonyos felügyeleti előfizetés-típusok nem érhető el.  Azure Stack erre a verzióra frissít, ha a két előfizetés, amely-típusokat [verziójú 1804-es verzióban bevezetett](azure-stack-update-1804.md#new-features) nem láthatók a konzolon. Ez a várható eredmény. A nem érhető el előfizetés-típusok a következők *előfizetés mérési*, és *Használatalapú előfizetés*. Ezek a típusok előfizetés új Azure Stack-környezetek verziójától kezdve az 1804 láthatók, de még nem használatra kész. Ön továbbra is használja a *alapértelmezett szolgáltató* előfizetés-típus.  

- <!-- 2403291 - IS ASDK --> Lehet, hogy nincs használatát a vízszintes görgetősáv a rendszergazdai és felhasználói portál alján. Ha nem éri el a vízszintes görgetősáv, az útkövetés, keresse meg a portálon egy előző panelen válassza a panel neve, a tetején található navigációs listában megtekinteni kívánt használati a portál bal oldali.
  ![Webhely-navigációs](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> Ez nem lehetséges számítási és tárolási erőforrások megtekintése a felügyeleti portálon. A probléma oka hiba történt a frissítés sikeres helytelenül jelenti a frissítés telepítése során. Ha a probléma akkor fordul elő, forduljon segítségért a Microsoft ügyfél-támogatási szolgálathoz.

- <!-- TBD - IS --> Előfordulhat, hogy megjelenik egy üres irányítópult, a portálon. Szeretné használni az irányítópultot, kattintson a fogaskerék ikonra a portál jobb felső sarokban, és válassza **alapértelmezett beállítások visszaállítása**.

- <!-- TBD - IS ASDK --> Felhasználói előfizetések eredmények az árva erőforrások törlése. Áthidaló megoldásként először törölje a felhasználó vagy a teljes erőforráscsoportot, és törölje a felhasználói előfizetések.

- <!-- TBD - IS ASDK --> Engedélyek nem tekintheti meg az Azure Stack portálok használata az előfizetéshez. Áthidaló megoldásként a PowerShell segítségével ellenőrizze az engedélyeket.


### <a name="health-and-monitoring"></a>Állapot és figyelés
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

  Mindkét riasztás biztonságosan figyelmen kívül hagyható, és automatikusan fog bezárása idővel.  

- <!-- 2368581 - IS. ASDK --> Az Azure Stack operátorait, ha kevés a szabad memória figyelmeztetést, és a bérlői virtuális gépek sikertelen üzembe helyezés egy *Fabric Virtuálisgép-létrehozási hiba*, lehetséges, hogy az Azure Stack-blokk esik a rendelkezésre álló memória. Használja a [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) legjobban megérteni az elérhető kapacitás a számítási feladatokhoz.


### <a name="compute"></a>Compute
- <!-- 2724873 - IS --> A PowerShell-parancsmagok használata esetén **Start-AzsScaleUnitNode** vagy **Stop-AzsScaleunitNode** skálázási egységek kezelése, indítása vagy leállítása a skálázási egység az első kísérlet sikertelen lehet. A parancsmag futtatása sikertelen, az első, futtassa a parancsmagot egy második alkalommal. A második Futtatás sikeres legyen, a művelet végrehajtásához. 

- <!-- 2494144 - IS, ASDK --> Amikor kiválasztja a virtuális gép üzembe helyezéséhez virtuálisgép-méretet, néhány F-sorozat Virtuálisgép-méretek nem láthatók a virtuális gép létrehozásakor a méret választó részeként. Az alábbi virtuális gépeinek-választójában jelenítse nem jelennek meg: *F8s_v2*, *F16s_v2*, *F32s_v2*, és *F64s_v2*.  
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

  A letöltés a Virtuálisgép-rendszerképet, amely korábban nem sikerült, majd próbálja megismételni.

- <!-- TBD - IS ASDK --> Ha egy virtuális gép üzembe helyezése a bővítmény kiépítési túl hosszú időt vesz igénybe, felhasználók hagyja, hogy a kiépítési időtúllépési helyett a folyamat fel vagy töröljön a virtuális gép leállítása.  

- <!-- 1662991 IS ASDK --> Linux rendszerű virtuális gép diagnosztikai nem támogatott az Azure Stackben. A Virtuálisgép-diagnosztika engedélyezve van egy Linux rendszerű virtuális Gépet helyez üzembe, a központi telepítés sikertelen lesz. Az üzembe helyezés akkor is sikertelen, ha engedélyezi a Linux rendszerű virtuális gép alapvető metrikák diagnosztikai beállításokon keresztül.  


### <a name="networking"></a>Hálózat  

- <!-- 1766332 - IS ASDK --> A **hálózatkezelés**, ha rákattint **VPN-átjáró létrehozása** egy VPN-kapcsolat beállítása **csoportházirend-alapú** jelenik meg a VPN-típust. Válassza ezt a beállítást. Csak a **útválasztó alapú** beállítás támogatott az Azure Stackben.

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
> <!-- TBD - IS --> Miután frissítette az Azure Stack ezen verziójára, akkor továbbra is használja az SQL- és MySQL erőforrás-szolgáltatókat korábban már telepített.  Azt javasoljuk, hogy frissítse az SQL és a MySQL amikor új kiadása elérhetővé válik. Azure Stack, például frissítések alkalmazása az SQL- és MySQL erőforrás-szolgáltatók egymás után. Például 1804 verzióját használja, ha először a alkalmazni verzió 1805, és frissítse a 1807.      
>   
> Ez a frissítés telepítésének nincs hatással az aktuális használatát a felhasználók által SQL vagy MySQL típusú erőforrás-szolgáltatók.
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
Letöltheti az Azure Stack 1807 frissítési csomagot [Itt](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Lásd még
- Az emelt szintű végpontját (EGP) használatával figyelheti, és folytathatja a frissítéseket, lásd: [monitorozása az Azure Stack használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).
- A frissítéskezelés az Azure Stackben áttekintését lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md).
- Az Azure Stack frissítések alkalmazásával kapcsolatos további információkért lásd: [alkalmazza a frissítéseket az Azure Stackben](azure-stack-apply-updates.md).
