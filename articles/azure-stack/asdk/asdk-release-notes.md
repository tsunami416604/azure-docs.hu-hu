---
title: A Microsoft Azure Stack Development Kit kibocsátási megjegyzései |} A Microsoft Docs
description: Fejlesztések, javításokat és az Azure Stack Development Kit kapcsolatos ismert problémák.
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
ms.date: 07/11/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: 86ac1f1b5433104faa89e1f107fa36fc1da5f70e
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989894"
---
# <a name="azure-stack-development-kit-release-notes"></a>Az Azure Stack Development Kit kibocsátási megjegyzései  
A kibocsátási megjegyzések olyan információt tartalmaznak a fejlesztések, javításokat és ismert problémák az Azure Stack Development Kit. Ha nem biztos abban, hogy melyik verziót futtatja, akkor az [ellenőrizhető a portál használatával](.\.\azure-stack-updates.md#determine-the-current-version).

> What's new in a ASDK való feliratkozással vétele a [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [hírcsatorna](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11805147"></a>1.1805.1.47 összeállítása

> [!TIP]  
> Ügyfeleink visszajelzései alapján, a verzió sémát, használja a Microsoft Azure Stackhez készült frissítése nincs. Ezzel a frissítéssel 1805, kezdve az új sémában jobban jelöli az aktuális felhő verzió.  
> 
> A verzió séma már *Version.YearYearMonthMonth.MinorVersion.BuildNumber* , a második és harmadik csoportok verziójú és kiadású jelzi-e. Például 1805.1 jelöli a *RTM verzió kibocsátási* 1805 (RTM) verzióját.  


### <a name="new-features"></a>Új funkciók 
A build tartalmaz a következő fejlesztések és javítások az Azure Stackhez.  

- <!-- 2297790 - IS, ASDK --> **Az Azure Stack már tartalmaz egy *Syslog* ügyfél** , egy *előzetes verziójú funkció*. Ez az ügyfél lehetővé teszi, hogy a Syslog server vagy a biztonsági biztonságiadat- és eseménykezelés (SIEM) felügyeleti szoftverek külső, az Azure Stackhez az Azure Stack-infrastruktúra kapcsolódó naplózási és biztonsági naplók továbbítását. Jelenleg a Syslog-ügyfél csak támogatja az UDP-kapcsolatok nem hitelesített alapértelmezett 514-es porton keresztül. Minden Syslog-üzenet a hasznos a Common Event Format (CEF) van formázva. 

  A Syslog-ügyfél konfigurálásához használja a **Set-SyslogServer** parancsmag között szerepelnek a kiemelt végponthoz. 

  Ebben az előzetes kiadásban a következő három riasztások jelenhet meg. Előadó: Azure Stack, ha ezek a riasztások belefoglalása *leírások* és *szervizelési* útmutatást. 
  - TITLE: A Kódintegritás kikapcsolása  
  - TITLE: A Kódintegritás rendszervizsgálati módban 
  - CÍM: Felhasználói fiók létrehozása

  Bár ez a funkció előzetes verzióban érhető el, akkor érdemes nem lehet hivatkozni az éles környezetben.   


### <a name="fixed-issues"></a>Rögzített kapcsolatos problémák
- Hogy javítva lett a probléma által blokkolt [új támogatási kérést adhat a legördülő listából](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) a a felügyeleti portálon. Most ezt a beállítást helyesen működik. 

- **Különböző javításokat** teljesítményét, stabilitását, biztonsági és az Azure Stack által használt operációs rendszer


<!-- ### Changes  --> 


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Ismert problémák
 
#### <a name="portal"></a>Portál
- <!-- 2551834 - IS, ASDK --> Ha bejelöli **áttekintése** a rendszergazdai vagy a felhasználói portált, az információkat a storage-fiókok a *Essentials* ablaktáblában nem jelenik meg.  Az alapvető erőforrások panelen például a fiók információit jeleníti meg a *erőforráscsoport*, *hely*, és *előfizetés-azonosító*.  Egyéb lehetőségek áttekintő is elérhető, például *szolgáltatások* és *figyelés*, is, megadhatja, hogy *Megnyitás az Explorerben* vagy *tárfiók törlése* .  

  A nem érhető el információ megtekintéséhez használja a [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) PowerShell-parancsmagot. 

- <!-- 2551834 - IS, ASDK --> Ha bejelöli **címkék** a rendszergazdai vagy a felhasználói portált a storage-fiókok, az adatokat nem sikerül betölteni, és nem jeleníti meg.  

  A nem érhető el információ megtekintéséhez használja a [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) PowerShell-parancsmagot.

- <!-- TBD - IS ASDK --> Ne használja az új felügyeleti előfizetés típusú *előfizetés mérési*, és *Használatalapú előfizetés*. E új típusú előfizetésessel jelentek meg az 1804-verzióval, de még nem állnak készen áll a használatra. Ön továbbra is használja a *alapértelmezett szolgáltató* előfizetés-típus.  

- <!-- 2403291 - IS ASDK --> Lehet, hogy nincs használatát a vízszintes görgetősáv a rendszergazdai és felhasználói portál alján. Ha nem éri el a vízszintes görgetősáv, az útkövetés, keresse meg a portálon egy előző panelen válassza a panel neve, a tetején található navigációs listában megtekinteni kívánt használati a portál bal oldali.
  ![Webhely-navigációs](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Felhasználói előfizetések eredmények az árva erőforrások törlése. Áthidaló megoldásként először törölje a felhasználó vagy a teljes erőforráscsoportot, és törölje a felhasználói előfizetések.

- <!-- TBD -  IS ASDK --> Engedélyek nem tekintheti meg az Azure Stack portálok használata az előfizetéshez. Áthidaló megoldásként a PowerShell segítségével ellenőrizze az engedélyeket.


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

  Mindkét riasztás #1. és #2 biztonságosan figyelmen kívül hagyható, és automatikusan fog bezárása idővel. 

  Is megjelenhet a következő riasztás *kapacitás*. A riasztás leírásában azonosított rendelkezésre álló memória százalékos eltérőek lehetnek:  

  Riasztás a #3:
   - NAME: Kevés a memória-kapacitás
   - SÚLYOSSÁG: kritikus
   - ÖSSZETEVŐ: kapacitás
   - Leírás: A régió lefoglalta a rendelkezésre álló memória több mint 80,00 %. Virtuális gépek a nagy mennyiségű memóriát, előfordulhat, hogy nem lehet létrehozni.  

  Ebben a verzióban az Azure Stack ezt a riasztást az helytelenül is aktiválódik. Ha a bérlői virtuális gépek továbbra is sikeresen telepíteni, nyugodtan figyelmen kívül hagyhatja ezt a riasztást. 
  
  Riasztási #3 nem automatikusan zárja be. Ez a riasztás bezárása Azure Stack fog létrehozni ugyanabból a riasztásból 15 percen belül.  

- <!-- 2368581 - IS ASDK --> Az Azure Stack operátorait, ha kevés a szabad memória figyelmeztetést, és a bérlői virtuális gépek sikertelen üzembe helyezés egy *Fabric Virtuálisgép-létrehozási hiba*, lehetséges, hogy az Azure Stack-blokk esik a rendelkezésre álló memória. Használja a [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) legjobban megérteni az elérhető kapacitás a számítási feladatokhoz. 


#### <a name="compute"></a>Compute
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


- <!-- TBD -  IS ASDK --> A virtual machine scale sets méretezési beállításait a portálon nem érhetők el. Áthidaló megoldásként használhatja [Azure PowerShell-lel](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). PowerShell-verzió különbségek, miatt kell használnia a `-Name` paraméter helyett `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Az Azure Stack felhasználói portál virtuális gépek létrehozásakor a portál megjeleníti, amelyek a D sorozatú virtuális gépek csatolhat a szolgáltatáskéréshez adatlemezek száma helytelen. Az összes támogatott D sorozatú virtuális gépek, az Azure konfigurációs lehetővé teszi tetszőleges számú adatlemezeket.

- <!-- TBD -  IS ASDK --> Egy Virtuálisgép-lemezkép nem hozható létre, amikor egy sikertelen elem, amely nem törölhető, előfordulhat, hogy lehet hozzáadni a VM-lemezképek számítás panel.

  Áthidaló megoldásként hozzon létre egy új Virtuálisgép-lemezkép hozható létre a Hyper-V keresztül próba VHD-vel (New-VHD-elérési út C:\dummy.vhd – rögzített - SizeBytes 1 GB-os). Ez a folyamat kell kijavítani a problémát, amely megakadályozza, hogy a sikertelen elem törlése. Ezt követően a helyőrző rendszerkép létrehozása után 15 percig sikeresen törölheti.

  Ezután próbálkozzon töltse a Virtuálisgép-rendszerképet, amely korábban nem sikerült.

- <!-- TBD -  IS ASDK --> Ha egy virtuális gép üzembe helyezése a bővítmény kiépítési túl hosszú időt vesz igénybe, felhasználók hagyja, hogy a kiépítési időtúllépési helyett a folyamat fel vagy töröljön a virtuális gép leállítása.  

- <!-- 1662991 - IS ASDK --> Linux rendszerű virtuális gép diagnosztikai nem támogatott az Azure Stackben. A Virtuálisgép-diagnosztika engedélyezve van egy Linux rendszerű virtuális Gépet helyez üzembe, a központi telepítés sikertelen lesz. Az üzembe helyezés akkor is sikertelen, ha engedélyezi a Linux rendszerű virtuális gép alapvető metrikák diagnosztikai beállításokon keresztül. 

#### <a name="networking"></a>Hálózat
- <!-- TBD - IS ASDK --> Nem hozhat létre felhasználó által megadott útvonalakat vagy a rendszergazda vagy a felhasználói portálon. Áthidaló megoldásként használja [Azure PowerShell-lel](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

- <!-- 1766332 - IS, ASDK --> A **hálózatkezelés**, ha rákattint **VPN-átjáró létrehozása** egy VPN-kapcsolat beállítása **csoportházirend-alapú** jelenik meg a VPN-típust. Válassza ezt a beállítást. Csak a **útválasztó alapú** beállítás támogatott az Azure Stackben.

- <!-- 2388980 -  IS ASDK --> Miután egy virtuális Gépet létrehozni, és társított nyilvános IP-címet, IP-címet a virtuális gép nem szüntesse meg. Társításának úgy tűnik, hogy működik, de az előzőleg hozzárendelt nyilvános IP-cím marad az eredeti virtuális gép társítva.

  Jelenleg csak új nyilvános IP-címeket kell használnia az új virtuális gépek létrehozása.

  Ez akkor fordul elő, akkor is, ha új virtuális gép IP-cím ismételt hozzárendelése (más néven egy *virtuális IP-címcsere*). Az összes jövőbeli kapcsolódni a kapcsolatot az eredeti virtuális gép, és a egy újra IP cím eredményéből.


- <!-- 2292271 - IS ASDK --> Ha megemeli egy kvótát egy hálózati erőforrás-ajánlat és a egy bérlői előfizetéshez társított csomag részét képező, a rendszer nem alkalmazza az új határértéket előfizetéshez. Azonban az új határértéket nő, a kvóta növelése után létrehozott új előfizetések a alkalmazni. 

  A probléma megkerüléséhez használja egy kiegészítő csomagot egy hálózati kvóta növeléséhez, amikor a csomag már társítva van egy előfizetést. További információkért lásd: hogyan [egy kiegészítő csomag elérhetővé](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> DNS-zóna erőforrások vagy társítva útvonaltábla erőforrás rendelkező előfizetés nem törölhető. Sikerült törölni az előfizetést, először törölnie kell DNS-zóna és az útvonaltábla erőforrásokhoz a bérlő előfizetésből. 


- <!-- 1902460 -  IS ASDK --> Az Azure Stack is támogatja egyetlen *helyi hálózati átjáró* IP-címenként. Ez az összes bérlői előfizetések között. Az első helyi hálózati átjáró kapcsolatának, ezt követő létrehozását, hozzon létre egy helyi hálózati átjáró erőforrás azonos IP-címmel rendelkező kísérlet után le lesznek tiltva.

- <!-- 16309153 -  IS ASDK --> A DNS-kiszolgáló beállítását a létrehozott virtuális hálózat *automatikus*, változó, egy egyéni DNS-kiszolgáló meghibásodik. A frissített beállításokat nem kerülnek az adott virtuális hálózatban lévő virtuális gépen.
 
- <!-- TBD -  IS ASDK --> Az Azure Stack nem támogatja a virtuális gép üzembe helyezése után további hálózati adapterek hozzáadása egy Virtuálisgép-példánnyal. Ha a virtuális gép több hálózati adaptert igényel, akkor meg kell határozni központi telepítéskor.


#### <a name="sql-and-mysql"></a>SQL- és MySQL 
- <!-- TBD - ASDK --> Az adatbázist üzemeltető kiszolgálók a erőforrás-szolgáltató és a felhasználó munkaterhelések által használható kell kijelölnie. Bármely egyéb fogyasztói, beleértve az App Services által használt példány nem használhat.

- <!-- IS, ASDK --> Különleges karaktereket, szóközöket és időszakok, többek között nem támogatottak a **termékcsalád** nevezze a Termékváltozat létrehozott SQL- és MySQL erőforrás-szolgáltatókat. 

#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Felhasználók, az első Azure-függvény létrehozása az előfizetés előtt regisztrálnia kell a tárolásierőforrás-szolgáltató.

- <!-- TBD - IS ASDK --> Annak érdekében, hogy a horizontális felskálázás infrastruktúra (feldolgozók, felügyeleti, előtér-szerepkörök), PowerShell számítási a kibocsátási megjegyzésekben leírtak szerint kell használnia.  

- <!-- TBD - IS ASDK --> App Service-ben csak lesz üzembe helyezve a *alapértelmezett szolgáltatói előfizetés* jelenleg. Egy következő frissítés App Service-ben fog helyezze üzembe az új *előfizetés mérési* , amely az Azure Stack 1804-es verzióban jelent meg. Mérés való használata támogatott, ha minden meglévő üzemelő példányt átkerülnek az új előfizetés típusa.

#### <a name="usage"></a>Használat  
- <!-- TBD -  IS ASDK --> Használati nyilvános IP-használati mérőszámok adatait jeleníti meg, azonos *EventDateTime* érték helyett minden egyes rekordja esetében a *TimeDate* , amely megjeleníti a rekord létrehozásának stamp. Ezeket az adatokat jelenleg nem használható nyilvános IP-címek használata pontos nyilvántartása végrehajtásához.

<!-- #### Identity -->



## <a name="build-201805131"></a>20180513.1 összeállítása

### <a name="new-features"></a>Új funkciók 
A build tartalmaz a következő fejlesztések és javítások az Azure Stackhez.  

- <!-- 1759172 - IS, ASDK --> **Új felügyeleti előfizetések**. Az 1804 két új előfizetés típusa van a portálon. E új típusú előfizetésessel rendelkező verziójától kezdve az 1804 új Azure Stack telepítése, az az alapértelmezett szolgáltatója előfizetés mellett, és látható. *Ne használja az Azure Stack touto verzí e új típusú előfizetésessel*. A rendelkezésre állási egy következő frissítés be ezen előfizetés tárolótípus tesszük közzé. 

  Ezek új előfizetés-típusok a következők: látható, de az alapértelmezett szolgáltatója előfizetés biztonságos, és megkönnyíti a megosztott erőforrások, például az SQL futtató kiszolgálók üzembe helyezése nagyobb változás része. 

  Már elérhető a három előfizetési típusok a következők:  
  - Szolgáltatói előfizetés alapértelmezett: továbbra is használhatja ezt az előfizetéstípust. 
  - Előfizetés mérési: *ne használja ezt az előfizetéstípust.*
  - Használatalapú előfizetés: *ne használja az előfizetés típusa*

### <a name="fixed-issues"></a>Rögzített kapcsolatos problémák
- <!-- IS, ASDK -->  A felügyeleti portálon már nem rendelkezik a frissítés csempe frissítése előtt adatait jeleníti meg. 

- <!-- 2050709 - IS, ASDK -->  Most már használhatja a felügyeleti portálon tármetrikák Blob service, a Table service és a Queue szolgáltatás szerkesztése.

- <!-- IS, ASDK --> A **hálózatkezelés**, amikor kattint **kapcsolat** egy VPN-kapcsolat beállítása **Site-to-site (IPsec)** jelenleg az egyetlen elérhető beállítás. 

- **Különböző javításokat** teljesítményét, stabilitását, biztonsági és az Azure Stack által használt operációs rendszer

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>Ez a frissítés túllépte az időkorlátot további kiadások  
A következő érhető el, azonban nem igénylik az Azure Stack frissítés 1804.
- **A Microsoft Azure Stack a System Center Operations Manager figyelési csomag frissítése**. Egy új verziója (1.0.3.0) a Microsoft System Center Operations Manager figyelési csomag az Azure Stack érhető el a [letöltése](https://www.microsoft.com/download/details.aspx?id=55184). Ebben a verzióban az egyszerű szolgáltatások a csatlakoztatott Azure Stack üzemelő példányához hozzáadásakor is használhatja. Ebben a verzióban is lehetővé teszi, hogy a szervizelési műveletek végrehajtása közvetlenül a belül az Operations Manager Update Management élményt funkciókat. Új irányítópultok megjelenítése az erőforrás-szolgáltatók, egységig bővíthető és egység csomópontok méretezése is vannak.

- **Új Azure Stack rendszergazdai PowerShell-verzió 1.3.0**.  Az Azure Stack PowerShell 1.3.0 már elérhető a telepítéshez. Ez a verzió minden rendszergazdai erőforrás-szolgáltatók kezelése az Azure Stack parancsokat biztosít.  Ezzel a kiadással bizonyos tartalmakat érvénytelenné válnak az Azure Stack eszközök githubról [tárház](https://github.com/Azure/AzureStack-Tools). 

   A telepítés részleteit, hajtsa végre a [utasításokat](.\.\azure-stack-powershell-install.md) vagy a [súgó](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) az Azure Stack-modul 1.3.0 tartalmának. 

- **Az első Azure Stack API Rest-referencia kiadása**. A [összes Azure Stack-rendszergazdai erőforrás-szolgáltató API-referencia](https://docs.microsoft.com/rest/api/azure-stack/) most már közzé van téve. 

### <a name="known-issues"></a>Ismert problémák
 
#### <a name="portal"></a>Portál
- <!-- TBD - IS ASDK --> Lehetővé teszi [, nyisson egy új támogatási kérelmet a legördülő listából](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) a belül a felügyeleti portálon nem érhető el. Ehelyett használja a következő hivatkozásra:     
    - Az Azure Stack fejlesztői készletének használata https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> Lehet, hogy nincs használatát a vízszintes görgetősáv a rendszergazdai és felhasználói portál alján. Ha nem éri el a vízszintes görgetősáv, az útkövetés, keresse meg a portálon egy előző panelen válassza a panel neve, a tetején található navigációs listában megtekinteni kívánt használati a portál bal oldali.
  ![Webhely-navigációs](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Felhasználói előfizetések eredmények az árva erőforrások törlése. Áthidaló megoldásként először törölje a felhasználó vagy a teljes erőforráscsoportot, és törölje a felhasználói előfizetések.

- <!-- TBD -  IS ASDK --> Engedélyek nem tekintheti meg az Azure Stack portálok használata az előfizetéshez. Áthidaló megoldásként a PowerShell segítségével ellenőrizze az engedélyeket.

-   <!-- TBD -  IS ASDK --> A felügyeleti portálon láthatja a Microsoft.Update.Admin összetevő kritikus riasztást. A riasztás nevét, leírását és szervizelési összes állapotúként jelennek meg:  
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

#### <a name="marketplace"></a>Piactér
- Felhasználók megkeresheti a teljes piactérre előfizetés nélkül, és megtekintheti a felügyeleti elemek, például a csomagokat és ajánlatokat. Ezek az elemek nem működik a felhasználók számára is.
 
#### <a name="compute"></a>Compute
- <!-- TBD -  IS ASDK --> A virtual machine scale sets méretezési beállításait a portálon nem érhetők el. Áthidaló megoldásként használhatja [Azure PowerShell-lel](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). PowerShell-verzió különbségek, miatt kell használnia a `-Name` paraméter helyett `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Az Azure Stack felhasználói portál virtuális gépek létrehozásakor a portál megjeleníti, hogy a DS sorozatú virtuális gép csatlakoztathat adatlemezek száma helytelen. DS sorozatú virtuális gépek, az Azure-konfiguráció lehetővé teszi tetszőleges számú adatlemezeket.

- <!-- TBD -  IS ASDK --> Egy Virtuálisgép-lemezkép nem hozható létre, amikor egy sikertelen elem, amely nem törölhető, előfordulhat, hogy lehet hozzáadni a VM-lemezképek számítás panel.

  Áthidaló megoldásként hozzon létre egy új Virtuálisgép-lemezkép hozható létre a Hyper-V keresztül próba VHD-vel (New-VHD-elérési út C:\dummy.vhd – rögzített - SizeBytes 1 GB-os). Ez a folyamat kell kijavítani a problémát, amely megakadályozza, hogy a sikertelen elem törlése. Ezt követően a helyőrző rendszerkép létrehozása után 15 percig sikeresen törölheti.

  Ezután próbálkozzon töltse a Virtuálisgép-rendszerképet, amely korábban nem sikerült.

- <!-- TBD -  IS ASDK --> Ha egy virtuális gép üzembe helyezése a bővítmény kiépítési túl hosszú időt vesz igénybe, felhasználók hagyja, hogy a kiépítési időtúllépési helyett a folyamat fel vagy töröljön a virtuális gép leállítása.  

- <!-- 1662991 - IS ASDK --> Linux rendszerű virtuális gép diagnosztikai nem támogatott az Azure Stackben. A Virtuálisgép-diagnosztika engedélyezve van egy Linux rendszerű virtuális Gépet helyez üzembe, a központi telepítés sikertelen lesz. Az üzembe helyezés akkor is sikertelen, ha engedélyezi a Linux rendszerű virtuális gép alapvető metrikák diagnosztikai beállításokon keresztül. 

#### <a name="networking"></a>Hálózat
- <!-- 1766332 - IS, ASDK --> A **hálózatkezelés**, ha rákattint **VPN-átjáró létrehozása** egy VPN-kapcsolat beállítása **csoportházirend-alapú** jelenik meg a VPN-típust. Válassza ezt a beállítást. Csak a **útválasztó alapú** beállítás támogatott az Azure Stackben.

- <!-- 2388980 -  IS ASDK --> Miután egy virtuális Gépet létrehozni, és társított nyilvános IP-címet, IP-címet a virtuális gép nem szüntesse meg. Társításának úgy tűnik, hogy működik, de az előzőleg hozzárendelt nyilvános IP-cím marad az eredeti virtuális gép társítva.

  Jelenleg csak új nyilvános IP-címeket kell használnia az új virtuális gépek létrehozása.

  Ez akkor fordul elő, akkor is, ha új virtuális gép IP-cím ismételt hozzárendelése (más néven egy *virtuális IP-címcsere*). Az összes jövőbeli kapcsolódni a kapcsolatot az eredeti virtuális gép, és a egy újra IP cím eredményéből.

- <!-- 2292271 - IS ASDK --> Ha megemeli egy kvótát egy hálózati erőforrás-ajánlat és a egy bérlői előfizetéshez társított csomag részét képező, a rendszer nem alkalmazza az új határértéket előfizetéshez. Azonban az új határértéket nő, a kvóta növelése után létrehozott új előfizetések a alkalmazni. 

  A probléma megkerüléséhez használja egy kiegészítő csomagot egy hálózati kvóta növeléséhez, amikor a csomag már társítva van egy előfizetést. További információkért lásd: hogyan [egy kiegészítő csomag elérhetővé](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> DNS-zóna erőforrások vagy társítva útvonaltábla erőforrás rendelkező előfizetés nem törölhető. Sikerült törölni az előfizetést, először törölnie kell DNS-zóna és az útvonaltábla erőforrásokhoz a bérlő előfizetésből. 


- <!-- 1902460 -  IS ASDK --> Az Azure Stack is támogatja egyetlen *helyi hálózati átjáró* IP-címenként. Ez az összes bérlői előfizetések között. Az első helyi hálózati átjáró kapcsolatának, ezt követő létrehozását, hozzon létre egy helyi hálózati átjáró erőforrás azonos IP-címmel rendelkező kísérlet után le lesznek tiltva.

- <!-- 16309153 -  IS ASDK --> A DNS-kiszolgáló beállítását a létrehozott virtuális hálózat *automatikus*, változó, egy egyéni DNS-kiszolgáló meghibásodik. A frissített beállításokat nem kerülnek az adott virtuális hálózatban lévő virtuális gépen.
 
- <!-- TBD -  IS ASDK --> Az Azure Stack nem támogatja a virtuális gép üzembe helyezése után további hálózati adapterek hozzáadása egy Virtuálisgép-példánnyal. Ha a virtuális gép több hálózati adaptert igényel, akkor meg kell határozni központi telepítéskor.


#### <a name="sql-and-mysql"></a>SQL- és MySQL 
- <!-- TBD - ASDK --> Az adatbázist üzemeltető kiszolgálók a erőforrás-szolgáltató és a felhasználó munkaterhelések által használható kell kijelölnie. Bármely egyéb fogyasztói, beleértve az App Services által használt példány nem használhat.

- <!-- IS, ASDK --> Különleges karaktereket, szóközöket és időszakok, többek között nem támogatottak a **termékcsalád** nevezze a Termékváltozat létrehozott SQL- és MySQL erőforrás-szolgáltatókat. 

#### <a name="app-service"></a>App Service
- <!-- TBD -  IS ASDK --> Felhasználók, az első Azure-függvény létrehozása az előfizetés előtt regisztrálnia kell a tárolásierőforrás-szolgáltató.

- <!-- TBD -  IS ASDK --> Annak érdekében, hogy a horizontális felskálázás infrastruktúra (feldolgozók, felügyeleti, előtér-szerepkörök), PowerShell számítási a kibocsátási megjegyzésekben leírtak szerint kell használnia.
 
#### <a name="usage"></a>Használat  
- <!-- TBD -  IS ASDK --> Használati nyilvános IP-használati mérőszámok adatait jeleníti meg, azonos *EventDateTime* érték helyett minden egyes rekordja esetében a *TimeDate* , amely megjeleníti a rekord létrehozásának stamp. Ezeket az adatokat jelenleg nem használható nyilvános IP-címek használata pontos nyilvántartása végrehajtásához.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>Az Azure Stack-eszközök letöltése a Githubról
- Használatakor a *invoke-webrequest* PowerShell-parancsmag használatával töltse le az Azure Stack-eszközök a Githubról, hibaüzenetet kap:     
    -  *Invoke-webrequest: A kérés megszakadt: nem sikerült létrehozni az SSL/TLS biztonságos csatornát.*     

  Ez a hiba oka egy legutóbbi GitHub támogatás elavulásának Tlsv1 és Tlsv1.1 titkosítási szabványok (a PowerShell-alapértelmezett). További információkért lásd: [gyenge titkosítási szabványok eltávolítási értesítés](https://githubengineering.com/crypto-removal-notice/).

<!-- #### Identity -->



## <a name="build-201803021"></a>20180302.1 összeállítása

### <a name="new-features-and-fixes"></a>Új funkciókkal és javításokkal
Az új szolgáltatásokat és az Azure Stack integrált rendszerek verzió 1803 érvényesek az Azure Stack Development Kit kiadott javításokat. Tekintse meg a [új funkciók](.\.\azure-stack-update-1803.md#new-features) és [hibákat](.\.\azure-stack-update-1803.md#fixed-issues) az Azure Stack 1803 szakaszait kibocsátási megjegyzések a részletek frissítése.  
> [!IMPORTANT]    
> Egyes szereplő elemek a **új funkciók** és **hibákat** szakaszok csak az Azure Stack integrált rendszerek érvényesek.

### <a name="changes"></a>Módosítások
- A módszer egy újonnan létrehozott ajánlat az állapotváltozáshoz *privát* való *nyilvános* vagy *leszerelt* megváltozott. További információkért lásd: [hozzon létre egy ajánlatot](.\.\azure-stack-create-offer.md). 


### <a name="known-issues"></a>Ismert problémák
 
#### <a name="portal"></a>Portál
- Lehetővé teszi [, nyisson egy új támogatási kérelmet a legördülő listából](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) a belül a felügyeleti portálon nem érhető el. Ehelyett használja a következő hivatkozásra:     
    - Az Azure Stack fejlesztői készletének használata https://aka.ms/azurestackforum.    

- <!-- 2050709 --> A felügyeleti portálon nincs lehetőség a Blob service, Table service vagy Queue szolgáltatás tármetrikák szerkesztése. Nyissa meg a Storage, és válassza ki a blob, tábla vagy üzenetsor-szolgáltatás csempéjét, amikor egy új panel nyílik meg, amely megjeleníti az adott szolgáltatásnak a teljesítménymetrikák diagramja. Majd válassza a felső a metrikák diagram csempe szerkesztése, ha a diagram szerkesztése panel megnyílik, de nem jeleníti meg a beállítások szerkesztéséhez metrikák.  

- Megjelenik egy **aktiválás szükséges** figyelmeztetés, ami arról, hogy kell regisztrálni az Azure Stack Development Kit kapcsolódjanak. Ez a viselkedés várható.

- Felhasználói előfizetések eredmények az árva erőforrások törlése. Áthidaló megoldásként először törölje a felhasználó vagy a teljes erőforráscsoportot, és törölje a felhasználói előfizetések.

- Engedélyek nem tekintheti meg az Azure Stack portálok használata az előfizetéshez. Áthidaló megoldásként a PowerShell segítségével ellenőrizze az engedélyeket.

- A frissítés csempét az irányítópulton, a felügyeleti portálon, nem jelennek meg a frissítésekkel kapcsolatos információk. A probléma megoldásához kattintson a csempére, és frissítse azt.

-   A felügyeleti portálon láthatja a Microsoft.Update.Admin összetevő kritikus riasztást. A riasztás nevét, leírását és szervizelési összes állapotúként jelennek meg:  
    - *Hiba – hiányzik a sablon typu FaultType ResourceProviderTimeout.*

    Ez a riasztás biztonságosan figyelmen kívül hagyhatja. 

- A felügyeleti portálon és a felhasználói portálra, mind az áttekintési panelen nem sikerül betölteni az áttekintési panelen a storage-fiókok egy régebbi API-verzióval készült kijelölésekor (Példa: 2015-06-15). 

  Áthidaló megoldásként PowerShell használatával futtassa a **Start-ResourceSynchronization.ps1** parancsfájl hozzáférés visszaállítása a tárfiók részleteit. [A parancsfájl érhető el a Githubról]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts), és futtatnia kell szolgáltatás rendszergazdai hitelesítő adatait a development kit gazdagépen a ASDK használatakor.  

- A **Service Health** panel nem sikerül betölteni. Ha a Service Health panel megnyitja a rendszergazdai vagy a felhasználói portálon, az Azure Stack egy hibaüzenet jelenik meg, és nem tölt be adatokat. Ez az elvárt működés. Válassza ki, és nyissa meg a Service Health, bár ez a funkció még nem áll rendelkezésre, de hajtják végre az Azure Stack egy jövőbeli verziójában.


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

- Az Azure Stack felügyeleti portálon, megjelenhet egy kritikus riasztás nevű **függőben lévő külső tanúsítvány lejárati**.  Ez a riasztás biztonságosan figyelmen kívül hagyható, és befolyásolja az Azure Stack Development Kit műveleteit. 


#### <a name="marketplace"></a>Piactér
- Felhasználók megkeresheti a teljes piactérre előfizetés nélkül, és megtekintheti a felügyeleti elemek, például a csomagokat és ajánlatokat. Ezek az elemek nem működik a felhasználók számára is.
 
#### <a name="compute"></a>Compute
- A virtual machine scale sets méretezési beállításait a portálon nem érhetők el. Áthidaló megoldásként használhatja [Azure PowerShell-lel](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). PowerShell-verzió különbségek, miatt kell használnia a `-Name` paraméter helyett `-VMScaleSetName`.

- Az Azure Stack felhasználói portál virtuális gépek létrehozásakor a portál megjeleníti, hogy a DS sorozatú virtuális gép csatlakoztathat adatlemezek száma helytelen. DS sorozatú virtuális gépek, az Azure-konfiguráció lehetővé teszi tetszőleges számú adatlemezeket.

- Egy Virtuálisgép-lemezkép nem hozható létre, amikor egy sikertelen elem, amely nem törölhető, előfordulhat, hogy lehet hozzáadni a VM-lemezképek számítás panel.

  Áthidaló megoldásként hozzon létre egy új Virtuálisgép-lemezkép hozható létre a Hyper-V keresztül próba VHD-vel (New-VHD-elérési út C:\dummy.vhd – rögzített - SizeBytes 1 GB-os). Ez a folyamat kell kijavítani a problémát, amely megakadályozza, hogy a sikertelen elem törlése. Ezt követően a helyőrző rendszerkép létrehozása után 15 percig sikeresen törölheti.

  Ezután próbálkozzon töltse a Virtuálisgép-rendszerképet, amely korábban nem sikerült.

-  Ha egy virtuális gép üzembe helyezése a bővítmény kiépítési túl hosszú időt vesz igénybe, felhasználók hagyja, hogy a kiépítési időtúllépési helyett a folyamat fel vagy töröljön a virtuális gép leállítása.  

- <!-- 1662991 --> Linux rendszerű virtuális gép diagnosztikai nem támogatott az Azure Stackben. A Virtuálisgép-diagnosztika engedélyezve van egy Linux rendszerű virtuális Gépet helyez üzembe, a központi telepítés sikertelen lesz. Az üzembe helyezés akkor is sikertelen, ha engedélyezi a Linux rendszerű virtuális gép alapvető metrikák diagnosztikai beállításokon keresztül. 


#### <a name="networking"></a>Hálózat
- A **hálózatkezelés**, ha rákattint **kapcsolat** egy VPN-kapcsolat beállítása **VNet – VNet** szerepel a lehetséges kapcsolattípust. Válassza ezt a beállítást. Jelenleg csak a **Site-to-site (IPsec)** beállítás támogatott.

- Miután egy virtuális Gépet létrehozni, és társított nyilvános IP-címet, IP-címet a virtuális gép nem szüntesse meg. Társításának úgy tűnik, hogy működik, de az előzőleg hozzárendelt nyilvános IP-cím marad az eredeti virtuális gép társítva.

  Jelenleg csak új nyilvános IP-címeket kell használnia az új virtuális gépek létrehozása.

  Ez akkor fordul elő, akkor is, ha új virtuális gép IP-cím ismételt hozzárendelése (más néven egy *virtuális IP-címcsere*). Az összes jövőbeli kapcsolódni a kapcsolatot az eredeti virtuális gép, és a egy újra IP cím eredményéből.



- Az Azure Stack is támogatja egyetlen *helyi hálózati átjáró* IP-címenként. Ez az összes bérlői előfizetések között. Az első helyi hálózati átjáró kapcsolatának, ezt követő létrehozását, hozzon létre egy helyi hálózati átjáró erőforrás azonos IP-címmel rendelkező kísérlet után le lesznek tiltva.

- A DNS-kiszolgáló beállítását a létrehozott virtuális hálózat *automatikus*, változó, egy egyéni DNS-kiszolgáló meghibásodik. A frissített beállításokat nem kerülnek az adott virtuális hálózatban lévő virtuális gépen.
 
- Az Azure Stack nem támogatja a virtuális gép üzembe helyezése után további hálózati adapterek hozzáadása egy Virtuálisgép-példánnyal. Ha a virtuális gép több hálózati adaptert igényel, akkor meg kell határozni központi telepítéskor.



#### <a name="sql-and-mysql"></a>SQL- és MySQL 
- Akár egy óra elteltével a felhasználó adatbázist is létrehozhat egy új SQL vagy MySQL Termékváltozatot is igénybe vehet.

- Az adatbázist üzemeltető kiszolgálók a erőforrás-szolgáltató és a felhasználó munkaterhelések által használható kell kijelölnie. Bármely egyéb fogyasztói, beleértve az App Services által használt példány nem használhat.

- <!-- IS, ASDK --> Különleges karaktereket, szóközöket és időszakok, többek között nem támogatottak a **termékcsalád** vagy **szint** SQL- és MySQL erőforrás-szolgáltatókat létrehozásakor a Termékváltozat-neveket.

#### <a name="app-service"></a>App Service
- Felhasználók, az első Azure-függvény létrehozása az előfizetés előtt regisztrálnia kell a tárolásierőforrás-szolgáltató.

- Annak érdekében, hogy a horizontális felskálázás infrastruktúra (feldolgozók, felügyeleti, előtér-szerepkörök), PowerShell számítási a kibocsátási megjegyzésekben leírtak szerint kell használnia.
 
#### <a name="usage"></a>Használat  
- Használati nyilvános IP-használati mérőszámok adatait jeleníti meg, azonos *EventDateTime* érték helyett minden egyes rekordja esetében a *TimeDate* , amely megjeleníti a rekord létrehozásának stamp. Ezeket az adatokat jelenleg nem használható nyilvános IP-címek használata pontos nyilvántartása végrehajtásához.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Az Azure Stack-eszközök letöltése a Githubról
- Használatakor a *invoke-webrequest* PowerShell-parancsmag használatával töltse le az Azure Stack-eszközök a Githubról, hibaüzenetet kap:     
    -  *Invoke-webrequest: A kérés megszakadt: nem sikerült létrehozni az SSL/TLS biztonságos csatornát.*     

  Ez a hiba oka egy legutóbbi GitHub támogatás elavulásának Tlsv1 és Tlsv1.1 titkosítási szabványok (a PowerShell-alapértelmezett). További információkért lásd: [gyenge titkosítási szabványok eltávolítási értesítés](https://githubengineering.com/crypto-removal-notice/).

  A probléma megoldásához adja hozzá `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` kényszerítése a PowerShell-konzol használatához TLSv1.2 GitHub-adattárak való letöltése esetén a parancsfájl elejére.





