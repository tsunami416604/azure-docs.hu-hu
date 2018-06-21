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
ms.date: 06/20/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: bbd9bb0d56dd61fd0a32531ac425a1dbc1aa8923
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295788"
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

- <!-- 2297790 - IS, ASDK --> **Az Azure verem most már tartalmaz egy *Syslog* ügyfél** , egy *előzetes verziójú funkciók*. Ez az ügyfél lehetővé teszi, hogy a naplózás és a biztonsági naplók az Azure-verem infrastruktúrát a Syslog server vagy a biztonsági információk és az esemény (SIEM) kezelőszoftvere Azure verem külső továbbítását. Jelenleg a Syslog-ügyfél csak-példányokhoz nem hitelesített UDP 514 alapértelmezett porton keresztül. A tartalom minden Syslog-üzenet közös esemény formátum (CEF) van formázva. 

  A Syslog-ügyfél konfigurálásához használja a **Set-SyslogServer** teszik ki, a kiemelt végpont parancsmag. 

  Ebben az előzetes jelenhet meg a következő három riasztásokat. Amikor Azure verem, ezek a riasztások tartalmazzák *leírások* és *szervizelési* útmutatást. 
  - CÍM: Kódintegritás kikapcsolása  
  - CÍM: Kódintegritás a rendszervizsgálati módban 
  - CÍM: Felhasználói fiók létrehozása

  Amíg ez a funkció jelenleg előzetes verzióban érhető, azt kell nem érvényesíthetők éles környezetben.   


### <a name="fixed-issues"></a>Javított problémák
- Jelenleg blokkolt rögzített a probléma [új támogatási kérelem megnyitása a legördülő menüből](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) a a felügyeleti portálon találja meg. Most ezt a beállítást működését. 

- **Különböző javítások** teljesítmény, stabilitásának, biztonsági és az Azure-verem által használt operációs rendszer


<!-- ### Changes  --> 


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Ismert problémák
 
#### <a name="portal"></a>Portál
- <!-- 2551834 - IS, ASDK --> Ha bejelöli **áttekintése** a tárfiókon, az a rendszergazda vagy a felhasználói portálon, a adatait a *Essentials* ablaktáblában nem jelenik meg.  Az Essentials ablaktábláján jelennek meg például a fiókkal kapcsolatos információk a *erőforráscsoport*, *hely*, és *előfizetés-azonosító*.  Áttekintés is elérhető, például a *szolgáltatások* és *figyelés*, is, megadhatja, hogy *Explorerben nyissa meg* vagy *a tárfiók törlése* .  

  A nem érhető el információ megtekintéséhez használja a [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) PowerShell-parancsmagot. 

- <!-- 2551834 - IS, ASDK --> Ha bejelöli **címkék** egy tárfiók a rendszergazda vagy a felhasználói portálon, az adatokat nem töltődik be, és nem jeleníti meg.  

  A nem érhető el információ megtekintéséhez használja a [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) PowerShell-parancsmagot.

- <!-- TBD - IS ASDK --> Ne használja az új felügyeleti előfizetés típusú *előfizetés mérési*, és *fogyasztás előfizetés*. Új előfizetés típusaival 1804 verziójával lett bevezetve, de még nem állnak készen áll a használatra. Ön továbbra is használja a *alapértelmezett szolgáltató* előfizetés típusa.  

- <!-- 2403291 - IS ASDK --> Lehet, hogy nincs használatát a rendszergazdai és felhasználói portál alján a vízszintes görgetősáv. Ha nem fér hozzá a vízszintes görgetősáv, használja a útkövetését lehetőségre, és navigáljon a portálon egy előző panel; ehhez válassza a panel neve meg szeretné tekinteni a webhely-navigációs listából tetején található a portál bal oldali.
  ![Webhely-navigációs](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Felhasználói előfizetések eredmények az árva erőforrások törlése. A probléma megoldásához először törölnie a felhasználói erőforrásokat és a teljes erőforráscsoport, és törölje a felhasználó előfizetések.

- <!-- TBD -  IS ASDK --> Az előfizetéshez az Azure-verem portálok engedélyek nem lehet megtekinteni. A probléma megoldásához a PowerShell használatával ellenőrizze az engedélyeket.


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

  Mindkét riasztás biztonságosan figyelmen kívül hagyható, és idővel automatikusan bezáródik.  

- <!-- 2392907 – ASDK -->   Megjelenhet egy *kritikus* riasztás **kevés a memória kapacitás**. Ez a riasztás van-e a következő leírása: *a régió már felhasználta több, mint a rendelkezésre álló memória 95,00 %. Virtuális gépek létrehozását a nagy mennyiségű memóriával sikertelenek lehetnek.*

  Ez a riasztás Azure verem helytelenül fiókjainak memória használata az Azure-verem szoftverfejlesztői készlet a lehet létrehozni.  

  Ez a riasztás figyelmen kívül hagyhatja, és a probléma nem befolyásolja a virtuális gépek elhelyezésére. 

- <!-- 2368581 - IS. ASDK --> Azure verem operátor, ha kevés a memória riasztást, és a bérlői virtuális gépek nem tudnak központi telepítés egy *háló Virtuálisgép-létrehozási hiba*, lehetséges, hogy az Azure-verem stamp nincs rendelkezésre álló memória. Használja a [Azure verem kapacitás Planne](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) legjobb megérteni az elérhető kapacitás a munkaterhelések. 


#### <a name="compute"></a>Compute
- <!-- TBD - IS, ASDK --> A virtuális gép méretét, a virtuális gép központi telepítés kiválasztásakor néhány F-sorozat Virtuálisgép-méretek nem láthatók a virtuális gépek létrehozásakor a mérete választó részeként. A választó nem jelennek meg a következő Virtuálisgép-méretek: *F8s_v2*, *F16s_v2*, *F32s_v2*, és *F64s_v2*.  
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


- <!-- TBD -  IS ASDK --> A virtuálisgép-méretezési csoportok skálázási beállításai nem érhetők el a portálon. Áthidaló megoldásként használja [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). PowerShell-verzió különbségek, miatt kell használnia a `-Name` paraméter helyett `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Az Azure-verem felhasználói portál virtuális gépek létrehozásakor, a portál megjeleníti, hogy a D sorozat VM csatolhat adatlemezek száma helytelen. Az összes támogatott D sorozat virtuális gépek lehetővé teszi tetszőleges számú adatlemezek Azure beállításként.

- <!-- TBD -  IS ASDK --> Egy Virtuálisgép-lemezkép nem hozható létre, amikor egy sikertelen elem, amely nem törölhető, előfordulhat, hogy a virtuális gép képek számítási panel hozzáadni.

  A probléma megoldásához hozzon létre egy új Virtuálisgép-lemezkép, amely a Hyper-V segítségével hozhatók létre üres virtuális merevlemez (New-VHD-elérési út C:\dummy.vhd-- SizeBytes rögzített 1 GB-os). Ez a folyamat kell hárítsa el a problémát, amely megakadályozza a sikertelen elem törlése. Ezt követően a dummy lemezkép létrehozása után 15 perc sikeresen törlése.

  Ezután próbálkozzon redownload, amely korábban nem sikerült Virtuálisgép-lemezkép.

- <!-- TBD -  IS ASDK --> Ha a kiterjesztést a virtuális gép üzembe helyezéséhez a kiépítés túl sokáig tart, felhasználók hagyja, hogy a létesítési időtúllépési helyett a folyamat felszabadítani, vagy törölje a virtuális gép leállítására tett kísérlet.  

- <!-- 1662991 - IS ASDK --> Linux Virtuálisgép-diagnosztika Azure verem nem támogatott. Engedélyezett Virtuálisgép-diagnosztika a Linux virtuális gép telepítésekor a központi telepítés sikertelen lesz. A központi telepítés is sikertelen lesz, ha engedélyezi a Linux virtuális gép alapvető metrikák diagnosztikai beállításait. 

#### <a name="networking"></a>Hálózat
- <!-- TBD - IS ASDK --> Nem hozható létre felhasználó által definiált útvonalak vagy a rendszergazda vagy a felhasználói portálon. A probléma megoldásához használja [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

- <!-- 1766332 - IS, ASDK --> A **hálózati**, ha **VPN Gateway létrehozása** egy VPN-kapcsolat beállítása **csoportházirend-alapú** van megadva, a VPN-típus. Válassza ezt a beállítást. Csak a **útválasztó alapú** Azure-verem lehetőség.

- <!-- 2388980 -  IS ASDK --> Miután egy virtuális Gépet létrehozták, és a társított egy nyilvános IP-cím, IP-címet a virtuális gép nem szüntesse meg. Disassociation úgy tűnik, hogy működik, de a korábban hozzárendelt nyilvános IP-cím marad az eredeti virtuális társítva.

  Jelenleg létrehozhat új virtuális gépek csak új nyilvános IP-címet kell használnia.

  Ez akkor fordul elő, akkor is, ha egy új virtuális géphez az IP-cím ismételt hozzárendelése (más néven a *virtuális IP-címcsere*). Az összes jövőbeni megpróbál a kapcsolaton keresztül az eredeti virtuális gép, és nem egy IP-cím eredményén keresztül kapcsolódni.


- <!-- 2292271 - IS ASDK --> Ha egy hálózati erőforráshoz, amely része egy ajánlatot és tervet készíteni, amely a bérlői előfizetéshez kvótát, az új korlát nincs telepítve az előfizetéshez. Azonban az új korlát vonatkozik jobb lesz, a kvóta után létrehozott új előfizetések. 

  Ez a probléma megoldása érdekében egy bővítmény csomag használata a hálózati kvóta növeléséhez, ha a csomag már társítva van egy előfizetést. További információkért lásd: hogyan [bővítmény tervének elérhetővé](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> DNS-zóna erőforrásokat és a vele társított útvonaltábla erőforrások rendelkező előfizetés nem törölhető. Az előfizetés sikeresen törléséhez először törölnie kell DNS-zónát és útválasztási táblázatot erőforrások a bérlői előfizetéshez. 


- <!-- 1902460 -  IS ASDK --> Azure verem támogatja egyetlen *helyi hálózati átjáró* / IP-címet. Ez az összes bérlői előfizetések között. Az első helyi hálózati átjáró kapcsolat létrehozására, későbbi helyi hálózati átjáró erőforrás létrehozása a azonos IP-címmel kísérlet után sem.

- <!-- 16309153 -  IS ASDK --> A virtuális hálózat DNS-kiszolgáló használatára létrehozott *automatikus*, változó számára egy egyéni DNS-kiszolgáló sikertelen. A frissített beállításokkal nem elküldte azokat a virtuális gépekhez a Vneten belül.
 
- <!-- TBD -  IS ASDK --> Az Azure verem nem támogatja a további hálózati adapterek hozzáadása egy Virtuálisgép-példány, a virtuális gép telepítését követően. Ha a virtuális gép több hálózati adapter szükséges, akkor meg kell adni a központi telepítéskor.


#### <a name="sql-and-mysql"></a>Az SQL és MySQL 
- <!-- TBD - ASDK --> Az adatbázis-kiszolgálók üzemeltetési az erőforrás-szolgáltató és a felhasználó munkaterhelés használatra kell kijelölnie. Bármely más fogyasztó, beleértve az App Service szolgáltatások által használt példánya nem használható.

- <!-- IS, ASDK --> Nem támogatottak a különleges karaktereket, szóközöket és időszakok, beleértve a **termékcsalád** neve, amikor az SQL és a MySQL-szolgáltatók hoz létre a Termékváltozat. 

#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Ahhoz, hogy az első Azure-függvény létre előfizetést felhasználók regisztrálnia kell a storage erőforrás-szolgáltató.

- <!-- TBD - IS ASDK --> Ahhoz, hogy az infrastruktúra kiterjesztése olyan esetekben (munkavállalók, kezelési, előtér-szerepkörök), PowerShell számítás a kibocsátási megjegyzésekben leírt módon kell használnia.  

- <!-- TBD - IS ASDK --> App Service csak telepíthető a a *alapértelmezett szolgáltató előfizetés* most. Az egy későbbi kiadásban, az üzembe helyezés az új lesz az App Service *előfizetés mérési* , amely az Azure verem 1804 lett bevezetve. Mérési való használata is támogatott, ha minden meglévő üzemelő példányt telepíti át az új előfizetés típusa.

#### <a name="usage"></a>Használat  
- <!-- TBD -  IS ASDK --> Használati nyilvános IP-használat mérési adatokat jeleníti meg az azonos *EventDateTime* érték az egyes rekordokhoz ahelyett, hogy a *TimeDate* tájékoztat, hogy a rekord létrehozásának stamp. Ezeket az adatokat jelenleg nem használható a nyilvános IP-cím használatának pontos lehetet.

<!-- #### Identity -->



## <a name="build-201805131"></a>Build 20180513.1

### <a name="new-features"></a>Új funkciók 
A build tartalmazza a következő fejlesztéseket és javításokat Azure verem.  

- <!-- 1759172 - IS, ASDK --> **Új felügyeleti előfizetések**. A 1804 két új előfizetés típusa van a portálon elérhető. Új előfizetés típusaival 1804 verziójával kezdve új Azure verem telepítése mellett az alapértelmezett szolgáltató előfizetés, és látható vannak. *Ne használjon új előfizetés típusaival Azure verem ezen verziójában*. Azt fogja értesíthetik ezeket előfizetés használni egy későbbi kiadásában be elérhetőségét. 

  Ezek új előfizetés típusok a következők: látható, de az alapértelmezett szolgáltató előfizetés védelmét, és könnyebb a megosztott erőforrások, például SQL üzemeltető kiszolgálók telepítése nagyobb változás része. 

  A most már hozzáférhető három előfizetés típusok a következők:  
  - Alapértelmezett szolgáltató előfizetés: továbbra is használhatja ezt az előfizetési típust. 
  - Előfizetés mérési: *ne használja ezt az előfizetési típust.*
  - Felhasználás előfizetés: *ne használjon ennél az előfizetéstípusnál*

### <a name="fixed-issues"></a>Javított problémák
- <!-- IS, ASDK -->  A felügyeleti portálon már nem kell a frissítés csempe frissítése előtt adatait jeleníti meg. 

- <!-- 2050709 - IS, ASDK -->  A felügyeleti portál segítségével most már a Blob szolgáltatás, a Table szolgáltatás és a Queue szolgáltatás storage mérőszámainak szerkesztése.

- <!-- IS, ASDK --> A **hálózati**, kattintva **kapcsolat** egy VPN-kapcsolat beállítása **pont-pont (IPsec)** jelenleg az egyetlen elérhető beállítás. 

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
- <!-- TBD - IS ASDK --> Lehetővé teszi [új támogatási kérést nyithat a legördülő menüből](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) a belül a felügyeleti portál nem érhető el. Ehelyett használja a következő hivatkozásra:     
    - Azure verem szoftverfejlesztői készlet használata https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> Lehet, hogy nincs használatát a rendszergazdai és felhasználói portál alján a vízszintes görgetősáv. Ha nem fér hozzá a vízszintes görgetősáv, használja a útkövetését lehetőségre, és navigáljon a portálon egy előző panel; ehhez válassza a panel neve meg szeretné tekinteni a webhely-navigációs listából tetején található a portál bal oldali.
  ![Webhely-navigációs](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Felhasználói előfizetések eredmények az árva erőforrások törlése. A probléma megoldásához először törölnie a felhasználói erőforrásokat és a teljes erőforráscsoport, és törölje a felhasználó előfizetések.

- <!-- TBD -  IS ASDK --> Az előfizetéshez az Azure-verem portálok engedélyek nem lehet megtekinteni. A probléma megoldásához a PowerShell használatával ellenőrizze az engedélyeket.

-   <!-- TBD -  IS ASDK --> A felügyeleti portálon jelenhet meg a kritikus riasztások Microsoft.Update.Admin összetevő. A riasztás nevét, leírását és javítási összes jeleníti meg:  
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

#### <a name="marketplace"></a>Piactér
- Felhasználók megkeresheti a teljes piactérre előfizetés nélkül, és láthatja például tervek és ajánlatok felügyeleti elemeket. Ezek az elemek nem működőképes a felhasználók számára is.
 
#### <a name="compute"></a>Compute
- <!-- TBD -  IS ASDK --> A virtuálisgép-méretezési csoportok skálázási beállításai nem érhetők el a portálon. Áthidaló megoldásként használja [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). PowerShell-verzió különbségek, miatt kell használnia a `-Name` paraméter helyett `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Az Azure-verem felhasználói portál virtuális gépek létrehozásakor, a portál megjeleníti, hogy a DS-ben több virtuális gép csatolhat adatlemezek száma helytelen. DS adatsorozat virtuális gépek lehetővé teszi tetszőleges számú adatlemezek Azure beállításként.

- <!-- TBD -  IS ASDK --> Egy Virtuálisgép-lemezkép nem hozható létre, amikor egy sikertelen elem, amely nem törölhető, előfordulhat, hogy a virtuális gép képek számítási panel hozzáadni.

  A probléma megoldásához hozzon létre egy új Virtuálisgép-lemezkép, amely a Hyper-V segítségével hozhatók létre üres virtuális merevlemez (New-VHD-elérési út C:\dummy.vhd-- SizeBytes rögzített 1 GB-os). Ez a folyamat kell hárítsa el a problémát, amely megakadályozza a sikertelen elem törlése. Ezt követően a dummy lemezkép létrehozása után 15 perc sikeresen törlése.

  Ezután próbálkozzon redownload, amely korábban nem sikerült Virtuálisgép-lemezkép.

- <!-- TBD -  IS ASDK --> Ha a kiterjesztést a virtuális gép üzembe helyezéséhez a kiépítés túl sokáig tart, felhasználók hagyja, hogy a létesítési időtúllépési helyett a folyamat felszabadítani, vagy törölje a virtuális gép leállítására tett kísérlet.  

- <!-- 1662991 - IS ASDK --> Linux Virtuálisgép-diagnosztika Azure verem nem támogatott. Engedélyezett Virtuálisgép-diagnosztika a Linux virtuális gép telepítésekor a központi telepítés sikertelen lesz. A központi telepítés is sikertelen lesz, ha engedélyezi a Linux virtuális gép alapvető metrikák diagnosztikai beállításait. 

#### <a name="networking"></a>Hálózat
- <!-- 1766332 - IS, ASDK --> A **hálózati**, ha **VPN Gateway létrehozása** egy VPN-kapcsolat beállítása **csoportházirend-alapú** van megadva, a VPN-típus. Válassza ezt a beállítást. Csak a **útválasztó alapú** Azure-verem lehetőség.

- <!-- 2388980 -  IS ASDK --> Miután egy virtuális Gépet létrehozták, és a társított egy nyilvános IP-cím, IP-címet a virtuális gép nem szüntesse meg. Disassociation úgy tűnik, hogy működik, de a korábban hozzárendelt nyilvános IP-cím marad az eredeti virtuális társítva.

  Jelenleg létrehozhat új virtuális gépek csak új nyilvános IP-címet kell használnia.

  Ez akkor fordul elő, akkor is, ha egy új virtuális géphez az IP-cím ismételt hozzárendelése (más néven a *virtuális IP-címcsere*). Az összes jövőbeni megpróbál a kapcsolaton keresztül az eredeti virtuális gép, és nem egy IP-cím eredményén keresztül kapcsolódni.

- <!-- 2292271 - IS ASDK --> Ha egy hálózati erőforráshoz, amely része egy ajánlatot és tervet készíteni, amely a bérlői előfizetéshez kvótát, az új korlát nincs telepítve az előfizetéshez. Azonban az új korlát vonatkozik jobb lesz, a kvóta után létrehozott új előfizetések. 

  Ez a probléma megoldása érdekében egy bővítmény csomag használata a hálózati kvóta növeléséhez, ha a csomag már társítva van egy előfizetést. További információkért lásd: hogyan [bővítmény tervének elérhetővé](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> DNS-zóna erőforrásokat és a vele társított útvonaltábla erőforrások rendelkező előfizetés nem törölhető. Az előfizetés sikeresen törléséhez először törölnie kell DNS-zónát és útválasztási táblázatot erőforrások a bérlői előfizetéshez. 


- <!-- 1902460 -  IS ASDK --> Azure verem támogatja egyetlen *helyi hálózati átjáró* / IP-címet. Ez az összes bérlői előfizetések között. Az első helyi hálózati átjáró kapcsolat létrehozására, későbbi helyi hálózati átjáró erőforrás létrehozása a azonos IP-címmel kísérlet után sem.

- <!-- 16309153 -  IS ASDK --> A virtuális hálózat DNS-kiszolgáló használatára létrehozott *automatikus*, változó számára egy egyéni DNS-kiszolgáló sikertelen. A frissített beállításokkal nem elküldte azokat a virtuális gépekhez a Vneten belül.
 
- <!-- TBD -  IS ASDK --> Az Azure verem nem támogatja a további hálózati adapterek hozzáadása egy Virtuálisgép-példány, a virtuális gép telepítését követően. Ha a virtuális gép több hálózati adapter szükséges, akkor meg kell adni a központi telepítéskor.


#### <a name="sql-and-mysql"></a>Az SQL és MySQL 
- <!-- TBD - ASDK --> Az adatbázis-kiszolgálók üzemeltetési az erőforrás-szolgáltató és a felhasználó munkaterhelés használatra kell kijelölnie. Bármely más fogyasztó, beleértve az App Service szolgáltatások által használt példánya nem használható.

- <!-- IS, ASDK --> Nem támogatottak a különleges karaktereket, szóközöket és időszakok, beleértve a **termékcsalád** neve, amikor az SQL és a MySQL-szolgáltatók hoz létre a Termékváltozat. 

#### <a name="app-service"></a>App Service
- <!-- TBD -  IS ASDK --> Ahhoz, hogy az első Azure-függvény létre előfizetést felhasználók regisztrálnia kell a storage erőforrás-szolgáltató.

- <!-- TBD -  IS ASDK --> Ahhoz, hogy az infrastruktúra kiterjesztése olyan esetekben (munkavállalók, kezelési, előtér-szerepkörök), PowerShell számítás a kibocsátási megjegyzésekben leírt módon kell használnia.
 
#### <a name="usage"></a>Használat  
- <!-- TBD -  IS ASDK --> Használati nyilvános IP-használat mérési adatokat jeleníti meg az azonos *EventDateTime* érték az egyes rekordokhoz ahelyett, hogy a *TimeDate* tájékoztat, hogy a rekord létrehozásának stamp. Ezeket az adatokat jelenleg nem használható a nyilvános IP-cím használatának pontos lehetet.

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

- <!-- 2050709 --> A felügyeleti portál nincs lehetőség a Blob, Table szolgáltatás, vagy várólista szolgáltatást storage mérőszámainak szerkesztése. Ugrás a tárolási, és válassza ki a blob, table vagy várólista-szolgáltatás csempe, megnyílik egy új panel, amely az adott szolgáltatásra vonatkozó mérőszámok diagramot jelenít meg. A lista elejéről a metrikák diagram csempe a Szerkesztés majd választásakor a diagram szerkesztése lehetőséget panel nyílik meg, de nem jelennek meg a beállítások szerkesztéséhez metrikákat.  

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

- <!-- 1662991 --> Linux Virtuálisgép-diagnosztika Azure verem nem támogatott. Engedélyezett Virtuálisgép-diagnosztika a Linux virtuális gép telepítésekor a központi telepítés sikertelen lesz. A központi telepítés is sikertelen lesz, ha engedélyezi a Linux virtuális gép alapvető metrikák diagnosztikai beállításait. 


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

- <!-- IS, ASDK --> Nem támogatottak a különleges karaktereket, szóközöket és időszakok, beleértve a **termékcsalád** vagy **réteg** neve, amikor az SQL és a MySQL-szolgáltatók hoz létre a Termékváltozat.

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





