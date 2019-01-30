---
title: A Microsoft Azure Stack Development Kit kibocsátási megjegyzései |} A Microsoft Docs
description: Fejlesztések, javításokat és az Azure Stack Development Kit kapcsolatos ismert problémák.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 12/21/2018
ms.openlocfilehash: d3d776def9e031ca2bcc76d1b60a19f67a74b35a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55240344"
---
# <a name="asdk-release-notes"></a>ASDK kibocsátási megjegyzései 
 
Ez a cikk ismerteti fejlesztései, javításokat és ismert problémák az Azure Stack Development Kit (ASDK). Ha nem biztos abban, hogy melyik verziót futtatja, akkor az [ellenőrizhető a portál használatával](../azure-stack-updates.md#determine-the-current-version).

> What's new in a ASDK való feliratkozással vétele a [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [hírcsatorna](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-118110101"></a>1.1811.0.101 összeállítása

### <a name="new-features"></a>Új funkciók

A build az Azure Stack a következő fejlesztések és javítások tartalmazza:  

- Nincs új minimális és ajánlott hardver- és szoftverkövetelményei a ASDK egy készletét. Ezek új specifikáció ajánlott vannak dokumentálva [Azure Stack üzembe helyezési, tervezési megfontolások a](asdk-deploy-considerations.md). Az Azure Stack platform alakult ki, további szolgáltatások már elérhetők és további erőforrások lehet szükség. A nagyobb specifikáció átdolgozott javaslatokról tükrözik.

### <a name="fixed-issues"></a>Hibák kijavítva:

<!-- TBD - IS ASDK --> 
- Kijavítva, amelyben a nyilvános IP-használatának mérési adatait mutatott azonos **EventDateTime** érték helyett minden egyes rekordja esetében a **TimeDate** , amely megjeleníti a rekord létrehozásának stamp. Ezek az adatok most már használhatja a nyilvános IP-címek használata pontos nyilvántartása végrehajtásához.

<!-- 3099544 – IS, ASDK --> 
- Kijavítva a hiba akkor történt, amikor egy új virtuális gép (VM) az Azure Stack portal használatával hoz létre. A virtuális gép méretének kiválasztásával okozott megjelenítendő USD/hónap oszlop egy **nem érhető el** üzenet. Már nem jelenik meg ebben az oszlopban; megjelenítés, a virtuális gép díjszabási oszlop nem támogatott az Azure Stackben.

<!-- 2930718 - IS ASDK --> 
- Kijavítva, amelyben a felügyeleti portálon bezárja a panelt, és kattintson a után bármely felhasználói előfizetés részleteinek elérésekor **legutóbbi**, a felhasználói előfizetés neve ne jelenjenek meg. Most már megjelenik a felhasználói előfizetés neve.

<!-- 3060156 - IS ASDK --> 
- Javítva lett a rendszergazda és a felhasználói portálon egy probléma: a portál beállításainak kattint, és kiválasztja **összes beállítás és saját irányítópult törlése** volt a várt módon működik, és a egy hibaértesítésre zobrazilo. 

<!-- 2930799 - IS ASDK --> 
- Kijavítva az a rendszergazda és a felhasználói portálon: alatt **minden szolgáltatás**, az eszköz **DDoS potection-tervek** nem megfelelően szerepel, amíg nem érhető el az Azure Stackben.
 
<!--2760466 – IS  ASDK --> 
- Kijavítva a hiba akkor történt, amikor egy új Azure Stack-környezet, amelyben telepítve van, amely azt jelzi, hogy a riasztás **aktiválás szükséges** nem jelennek meg. Most már megfelelően jelenik meg.

<!--1236441 – IS  ASDK --> 
- Kijavítva, amely ebben az esetben a felhasználói csoport számára az RBAC-szabályzatok alkalmazása, ADFS használata esetén.

<!--3463840 - IS, ASDK --> 
- Kijavítva a hiba az infrastruktúra biztonsági mentése a nyilvános VIP-hálózat nem érhető el fájlkiszolgálót miatt meghiúsul. Javítás a biztonsági mentési infrastruktúra-szolgáltatás vissza helyezi a nyilvános infrastruktúra-hálózathoz. Ha a legújabb telepített [Azure Stack-gyorsjavítás a 1809](#azure-stack-hotfixes) , címek, a probléma, a 1811 frissítés nem hajt végre semmilyen további módosításokat végezhet. 

<!-- 2967387 – IS, ASDK --> 
- Javítva lett egy probléma, amelyben a fiókot, amellyel jelentkezzen be az Azure Stack rendszergazdai vagy a felhasználói portál néven **azonosítatlan felhasználó**. Ez az üzenet volt jelenik meg, ha a fiók nincs vagy egy *első* vagy *utolsó* megadott név.   

<!--  2873083 - IS ASDK --> 
- Javítva lett egy probléma, mely a portál segítségével hozzon létre egy virtuális gépet a méretezési csoport (VMSS), a *példány mérete* legördülő nem töltődött be megfelelően, ha az Internet Explorerben. Ez a böngésző most már megfelelően működik.  

<!-- 3190553 - IS ASDK -->
- Kijavítva, amely jelzi, hogy az infrastruktúra szerepkör-példány nem érhető el, vagy skálázási egység csomópont offline állapotban volt zajos riasztások jönnek létre.

### <a name="known-issues"></a>Ismert problémák

#### <a name="portal"></a>Portál  

<!-- 2930820 - IS ASDK --> 
- A rendszergazda és a felhasználói portálon Ha a "Docker," a cikk helytelenül adja vissza. Ez nem áll rendelkezésre, az Azure Stackben. Ha megpróbálja létrehozni, megjelenik egy panel, hibát jelezve. 

<!-- 2931230 – IS  ASDK --> 
- Nem lehet törölni a felhasználói előfizetés, mint egy kiegészítő csomag hozzáadott tervek, akkor is, ha a csomag eltávolítása a felhasználói előfizetés. A terv marad mindaddig, amíg az előfizetéseket, hogy a kiegészítő csomagot is törlődik. 

<!-- TBD - IS ASDK --> 
- A két felügyeleti előfizetés-típus verziójú 1804-es verzióban bevezetett nem használható. Az előfizetés-típusok a következők **előfizetés mérési**, és **Használatalapú előfizetés**. Ezek a típusok előfizetés új Azure Stack-környezetek verziójától kezdve az 1804 láthatók, de még nem használatra kész. Ön továbbra is használja a **alapértelmezett szolgáltató** előfizetés-típus.

<!-- TBD - IS ASDK --> 
- Felhasználói előfizetések eredmények az árva erőforrások törlése. Áthidaló megoldásként először törölje a felhasználó vagy a teljes erőforráscsoportot, és törölje a felhasználói előfizetések.

<!-- TBD - IS ASDK --> 
- Engedélyek nem tekintheti meg az Azure Stack portálok használata az előfizetéshez. Áthidaló megoldásként a PowerShell segítségével ellenőrizze az engedélyeket.

#### <a name="compute"></a>Compute 

<!-- 3235634 – IS, ASDK -->
- Tartalmazó méretű virtuális gépek telepítéséhez egy **v2** utótag; például **Standard_A2_v2**, adja meg, az utótag **Standard_A2_v2** (kis v). Ne használjon **Standard_A2_V2** (nagybetűs V). Ez a globális Azure-ban működik, és az Azure Stacken inkonzisztencia.

<!-- 2869209 – IS, ASDK --> 
- Használatakor a [ **Add-AzsPlatformImage** parancsmag](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), kell használnia a **- OsUri** paramétert, a tárfiók URI, ahol fel a rendszer a lemezen. Ha a lemez a helyi elérési utat használ, a parancsmag futtatása sikertelen, a következő hibával: *Hosszú ideig futó művelet sikertelen volt, "Sikertelen" állapotú*. 

<!--  2795678 – IS, ASDK --> 
- Ha a portálon hozhat létre virtuális gépeket (VM) a prémium szintű virtuális gép méretét (DS, Ds_v2, FS, FSv2) használ, a virtuális gép létrejött, a standard szintű storage-fiókban. A standard szintű tárfiók-létrehozás nem befolyásolja, funkcionálisan IOPs, vagy számlázási. Biztonságosan figyelmen kívül hagyhatja a figyelmeztetést, amely szerint: **Úgy döntött, a standard szintű lemezes használatához olyan méretnél, amely támogatja a prémium szintű lemezek használatát. Ez ronthatja az operációs rendszer teljesítményét, és nem ajánlott. Fontolja meg inkább a prémium szintű tárolást (SSD) használatát.**

<!-- 2967447 - IS, ASDK --> 
- A virtual machine scale set (VMSS) létrehozási folyamatának 7.2 CentOS-alapú üzembe helyezési lehetőséget biztosít. A rendszerképet az Azure Stacken nem érhető el, mert az üzemelő példány egy másik operációs rendszer válasszon vagy adjon meg egy másik CentOS lemezképet a marketplace-ről telepítené az üzemeltető által letöltött Azure Resource Manager sablonnal.  

<!-- TBD - IS ASDK --> 
- Ha egy virtuális gép üzembe helyezése a bővítmény kiépítési túl hosszú időt vesz igénybe, felhasználók hagyja, hogy a kiépítési időtúllépési helyett a folyamat fel vagy töröljön a virtuális gép leállítása.  

<!-- 1662991 IS ASDK --> 
- Linux rendszerű virtuális gép diagnosztikai nem támogatott az Azure Stackben. A Virtuálisgép-diagnosztika engedélyezve van egy Linux rendszerű virtuális Gépet helyez üzembe, a központi telepítés sikertelen lesz. Az üzembe helyezés akkor is sikertelen, ha engedélyezi a Linux rendszerű virtuális gép alapvető metrikák diagnosztikai beállításokon keresztül.  

<!-- 2724961- IS ASDK --> 
- Amikor regisztrál a **Microsoft.Insight** előfizetési beállítások, az erőforrás-szolgáltató egy Windows virtuális gép létrehozása és a vendég operációs rendszer diagnosztikai engedélyezve van, a Processzorhasználat (%) diagramon a virtuális gépek – Áttekintés lapon nem jelenik meg a mérőszámadatok.

   Keresse meg a mérőszámadatokat, például a processzor-diagram a virtuális gép, nyissa meg a metrikák ablakot, és a támogatott Windows virtuális gép vendég mérőszámok megjelenítése.

<!-- 3507629 - IS, ASDK --> 
- Felügyelt lemezeket hoz létre két új [kvótatípusok számítási](../azure-stack-quota-types.md#compute-quota-types) korlátozhatja a felügyelt lemezek, amelyek kioszthatóak maximális kapacitását. Alapértelmezés szerint a 2048 GiB az egyes felügyelt lemezek kvóta van lefoglalva. Azonban előfordulhat, hogy problémák merülnek fel a következő:

   - Kvóták 1808 frissítés előtt létrehozott a Managed Disks kvóta jelennek meg 0 értéket a felügyeleti portálon, bár 2048 GiB le van foglalva. Növelheti vagy csökkentheti a tényleges igényeinek és az újonnan beállított értéket kvótaérték felülbírálja a 2048 GiB alapértelmezett.
   - Ha a kvóta értékének 0-ra frissít, egyenértékű, 2048 GiB alapértelmezett értékét. A probléma megoldásához állítsa a kvótaérték 1.

<!-- TBD - IS ASDK --> 
- Frissítse a 1811 alkalmazása után, a következő problémák léphetnek a felügyelt lemezekkel rendelkező virtuális gépek üzembe helyezésekor:

   1. Ha az előfizetés korábban jött létre a 1808 frissítése, a felügyelt lemezekkel rendelkező virtuális gép üzembe helyezése egy belső hiba miatt sikertelen lehet. A hiba elhárításához kövesse ezeket a lépéseket minden egyes előfizetés esetén:
      1. A bérlői portálon lépjen a **előfizetések** , és keresse meg az előfizetés. Kattintson a **erőforrás-szolgáltatók**, majd kattintson az **Microsoft.Compute**, és kattintson a **újraregisztrálni**.
      2. Lépjen az azonos előfizetéshez tartozó **hozzáférés-vezérlés (IAM)**, és ellenőrizze, hogy **Azure Stack – felügyelt lemez** szerepel a listán.
   2. Egy több-bérlős környezet van beállítva, ha a virtuális gépek üzembe helyezéséhez egy adott előfizetéshez tartozó és a Vendég címtár egy belső hiba miatt meghiúsulhat. A hiba elhárításához kövesse az alábbi lépéseket a [Ez a cikk](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) újrakonfigurálása a Vendég címtárak mindegyike.

#### <a name="networking"></a>Hálózat

<!-- 1766332 - IS ASDK --> 
- A **hálózatkezelés**, ha rákattint **VPN-átjáró létrehozása** egy VPN-kapcsolat beállítása **csoportházirend-alapú** jelenik meg a VPN-típust. Válassza ezt a beállítást. Csak a **útválasztó alapú** beállítás támogatott az Azure Stackben.

<!-- 1902460 - IS ASDK --> 
- Az Azure Stack is támogatja egyetlen *helyi hálózati átjáró* IP-címenként. Ez az összes bérlői előfizetések között. Az első helyi hálózati átjáró kapcsolatának, ezt követő létrehozását, hozzon létre egy helyi hálózati átjáró erőforrás azonos IP-címmel rendelkező kísérlet után le lesznek tiltva.

<!-- 16309153 - IS ASDK --> 
- A DNS-kiszolgáló beállítását a létrehozott virtuális hálózat *automatikus*, változó, egy egyéni DNS-kiszolgáló meghibásodik. A frissített beállításokat nem kerülnek az adott virtuális hálózatban lévő virtuális gépen.

<!-- 2529607 - IS ASDK --> 
- Azure Stack során *titkos Elforgatás*, egy ideig, amely nyilvános IP-címek nem érhetők el 2 – 5 percet.

<!-- 2664148 - IS ASDK --> 
-   Olyan esetekben, ahol a bérlői fér hozzá a virtuális gépek egy S2S VPN-alagút használatával akkor léphetnek fel egy olyan forgatókönyvet, ahol sikertelen csatlakozási kísérletek, ha a helyszíni alhálózati hozzá lett adva a helyi hálózati átjáró után átjárón már létre lett hozva. 

#### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Felhasználók, az első Azure-függvény létrehozása az előfizetés előtt regisztrálnia kell a tárolásierőforrás-szolgáltató.

<!-- #### Usage -->  

<!-- #### Identity -->

## <a name="build-11809090"></a>1.1809.0.90 összeállítása

### <a name="new-features"></a>Új funkciók
A build tartalmaz a következő fejlesztések és javítások az Azure Stackhez.  

<!--  2712869   | IS  ASDK -->  
- **Azure Stack syslog-ügyfél (nyilvánosan elérhetők)** az ügyfél lehetővé teszi, hogy a naplózás, a riasztások és a syslog server vagy a biztonsági biztonságiadat- és eseménykezelés (SIEM) felügyeleti szoftverek az Azure Stack infrastruktúrájának biztonsági naplók továbbítását külső az Azure Stackhez. A syslog-ügyfél már támogatja a portot, amelyen a syslog-kiszolgáló figyel.

Ebben a kiadásban a syslog-ügyfél általánosan elérhető, és az éles környezetben is használható.

További információkért lásd: [syslog-továbbítás az Azure Stack](../azure-stack-integrate-security.md).

### <a name="fixed-issues"></a>Hibák kijavítva:

<!-- TBD - IS ASDK -->
- A portálon a szabad és felhasznált kapacitás reporting memória diagram már pontos. Tudja most megbízhatóan jelezni tudja hozhat létre, hogy hány virtuális gépet.

<!-- TBD - IS ASDK --> 
- Kijavítva, amelyben létrehozta a virtuális gépek az Azure Stack felhasználói portál és a portálon jelenik meg, hogy a DS sorozatú virtuális gép csatlakoztathat adatlemezek száma helytelen. DS sorozatú virtuális gépek, az Azure-konfiguráció lehetővé teszi tetszőleges számú adatlemezeket.

- <!-- 2702741 -  IS, ASDK --> Kijavítva a hiba melyik nyilvános IP-címek, amelyek a dinamikus kiosztási használatával lettek telepítve a metódus nem garantált, hogy egy állítsa le és vonja vissza kiadása után megőrzi. Most már megmaradnak.

- <!-- 3078022 - IS, ASDK --> Ha egy virtuális gép felszabadítva 1808 előtt nem lehet újból lefoglalni a 1808 frissítés után.  Ezt a problémát megoldottuk a 1809. A javítás 1809 is indítható el, amelyek ebben az állapotban vannak, és nem indítható el. A javítás is megakadályozza, hogy a probléma került.

<!-- TBD -  IS ASDK --> 
- Egy Virtuálisgép-lemezkép nem hozható létre, amikor egy sikertelen elem, amely nem törölhető, előfordulhat, hogy lehet hozzáadni a VM-lemezképek számítás panel.

  Áthidaló megoldásként hozzon létre egy új Virtuálisgép-lemezkép hozható létre a Hyper-V keresztül próba VHD-vel (New-VHD-elérési út C:\dummy.vhd – rögzített - SizeBytes 1 GB-os). Ez a folyamat kell kijavítani a problémát, amely megakadályozza, hogy a sikertelen elem törlése. Ezt követően a helyőrző rendszerkép létrehozása után 15 percig sikeresen törölheti.

  A letöltés a Virtuálisgép-rendszerképet, amely korábban nem sikerült, majd próbálja megismételni.

- **Különböző javításokat** teljesítményét, stabilitását, biztonsági és az Azure Stack által használt operációs rendszer

### <a name="changes"></a>Módosítások

### <a name="known-issues"></a>Ismert problémák

#### <a name="portal"></a>Portál  

<!-- 2515955   | IS ,ASDK--> 
- *Az összes szolgáltatás* váltja fel *további szolgáltatások* az az Azure Stack rendszergazdai és felhasználói portálon. Most már használhatja *minden szolgáltatás* helyett az Azure Stack portálon lépjen a ugyanúgy, mint az az Azure Portalon teheti meg.

<!--  TBD – IS, ASDK --> 
- *Alapszintű A* virtuálisgép-méretek esetében kivezettük [virtuálisgép-méretezési csoportok létrehozása](../azure-stack-compute-add-scalesets.md) (VMSS) a portálon keresztül. Ez a méret a VMSS létrehozásához, használja a PowerShell vagy a sablont.

#### <a name="health-and-monitoring"></a>Állapot és figyelés

<!-- 1264761 - IS ASDK -->  
- A riasztásokat a *egészségügyi vezérlő* összetevő, amely rendelkezik a következő adatokat:  

   #1 riasztás:
   - NÉV:  Infrastruktúra-szerepkör nem megfelelő állapotú
   - SÚLYOSSÁG: Figyelmeztetés
   - ÖSSZETEVŐ: Vezérlő állapota
   - LEÍRÁS: A health vezérlő szívverési képolvasó nem érhető el. Ez érinthet rendszerállapot-jelentések és metrikákat.  

  Riasztási #2:
   - NÉV:  Infrastruktúra-szerepkör nem megfelelő állapotú
   - SÚLYOSSÁG: Figyelmeztetés
   - ÖSSZETEVŐ: Vezérlő állapota
   - LEÍRÁS: A health vezérlő tartalék képolvasó nem érhető el. Ez érinthet rendszerállapot-jelentések és metrikákat.

  Mindkét riasztás biztonságosan figyelmen kívül hagyható, és idővel automatikusan bezáródik.  

<!-- 2368581 - IS. ASDK --> 
- Az Azure Stack operátorait, ha kevés a szabad memória figyelmeztetést, és a bérlői virtuális gépek sikertelen üzembe helyezés egy *Fabric Virtuálisgép-létrehozási hiba*, lehetséges, hogy az Azure Stack-blokk esik a rendelkezésre álló memória. Használja a [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) legjobban megérteni az elérhető kapacitás a számítási feladatokhoz.


#### <a name="compute"></a>Compute 

<!-- 3164607 – IS, ASDK -->
- Az azonos virtuális géphez (VM) ugyanazt a nevet és LUN-t a leválasztott lemez újracsatlakoztatása meghiúsul, és hiba például **adatok lemez "datadisk" nem csatolható "vm1" virtuális gép**. A hiba akkor fordul elő, mert a lemez leválasztása folyamatban vagy a legutóbbi leválasztási művelet nem sikerült. Várjon, amíg a lemez teljesen le van választva, és ezután próbálkozzon újra, vagy törölje vagy válassza le a lemezt explicit módon újra. A megoldás, hogy csatlakoztassa újra egy másik névvel, vagy egy másik logikai egységen. 

<!-- 3235634 – IS, ASDK -->
- Tartalmazó méretű virtuális gépek telepítéséhez egy **v2** utótag; például **Standard_A2_v2**, adja meg, az utótag **Standard_A2_v2** (kis v). Ne használjon **Standard_A2_V2** (nagybetűs V). Ez a globális Azure-ban működik, és az Azure Stacken inkonzisztencia.

<!-- 3099544 – IS, ASDK --> 
- Amikor hoz létre egy új virtuális gép (VM) az Azure Stack portal használatával, és a Virtuálisgép-méret választja, a USD/hónap oszlop jelenik meg egy **nem érhető el** üzenet. Nem jelenik meg ebben az oszlopban; megjelenítés, a virtuális gép díjszabási oszlop nem támogatott az Azure Stackben.

<!-- 2869209 – IS, ASDK --> 
- Használatakor a [ **Add-AzsPlatformImage** parancsmag](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), kell használnia a **- OsUri** paramétert, a tárfiók URI, ahol fel a rendszer a lemezen. Ha a lemez a helyi elérési utat használ, a parancsmag futtatása sikertelen, a következő hibával: *Hosszú ideig futó művelet sikertelen volt, "Sikertelen" állapotú*. 

<!--  2966665 – IS, ASDK --> 
- SSD csatolása a prémium szintű méretre adatlemezek felügyelt lemez virtuális gépek (DS, DSv2, Fs, Fs_V2) meghiúsul egy hiba miatt:  *Nem sikerült frissíteni a lemezt a virtuális gép "vmname" hiba: A kért művelet nem hajtható végre, mert a Virtuálisgép-méret nem támogatott a "Premium_LRS" fióktípust "Fs_v2 Standard_DS/Ds_V2 és FS)*

   A probléma megkerüléséhez használja *Standard_LRS* adatlemez, hanem *Premium_LRS lemezek*. Felhasználása *Standard_LRS* adatlemezek nem változik, iops-t vagy a számlázási költségeket.  

<!--  2795678 – IS, ASDK --> 
- Ha a portálon hozhat létre virtuális gépeket (VM) a prémium szintű virtuális gép méretét (DS, Ds_v2, FS, FSv2) használ, a virtuális gép létrejött, a standard szintű storage-fiókban. A standard szintű tárfiók-létrehozás nem befolyásolja, funkcionálisan IOPs, vagy számlázási. 

   Biztonságosan figyelmen kívül hagyhatja a figyelmeztetést, amely szerint: *Úgy döntött, a standard szintű lemezes használatához olyan méretnél, amely támogatja a prémium szintű lemezek használatát. Ez ronthatja az operációs rendszer teljesítményét, és nem ajánlott. Fontolja meg inkább a prémium szintű tárolást (SSD) használatát.*

<!-- 2967447 - IS, ASDK --> 
- A virtuálisgép-méretezési csoportot (VMSS) hozzon létre felhasználói élményt 7.2 CentOS-alapú üzembe helyezési lehetőséget biztosít. A rendszerképet az Azure Stacken nem érhető el, mert egy másik operációs rendszer az üzembe helyezéshez válassza ki, vagy adjon meg egy másik CentOS lemezképet a marketplace-ről telepítené az üzemeltető által letöltött Azure Resource Manager sablonnal.

<!-- TBD -  IS ASDK --> 
- A virtual machine scale sets méretezési beállításait a portálon nem érhetők el. Áthidaló megoldásként használhatja [Azure PowerShell-lel](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). PowerShell-verzió különbségek, miatt kell használnia a `-Name` paraméter helyett `-VMScaleSetName`.



<!-- TBD -  IS ASDK --> 
- Ha egy virtuális gép üzembe helyezése a bővítmény kiépítési túl hosszú időt vesz igénybe, felhasználók hagyja, hogy a kiépítési időtúllépési helyett a folyamat fel vagy töröljön a virtuális gép leállítása.  

<!-- 1662991 - IS ASDK --> 
- Linux rendszerű virtuális gép diagnosztikai nem támogatott az Azure Stackben. A Virtuálisgép-diagnosztika engedélyezve van egy Linux rendszerű virtuális Gépet helyez üzembe, a központi telepítés sikertelen lesz. Az üzembe helyezés akkor is sikertelen, ha engedélyezi a Linux rendszerű virtuális gép alapvető metrikák diagnosztikai beállításokon keresztül.

<!-- 2724961- IS ASDK --> 
- Amikor regisztrál a **Microsoft.Insight** előfizetési beállítások, az erőforrás-szolgáltató és a egy Windows virtuális gép létrehozása a vendég operációs rendszer diagnosztikai engedélyezve van, a Processzorhasználat (%) diagramon a virtuális gépek – Áttekintés lapon nem fogja tudni metrikai adatok megjelenítése.
 
  A virtuális gép számára a processzor diagram megkereséséhez nyissa meg a **metrikák** panel és a támogatott Windows VM show Vendég mérőszámok.

 

#### <a name="networking"></a>Hálózat
<!-- 1766332 - IS, ASDK --> 
- A **hálózatkezelés**, ha rákattint **VPN-átjáró létrehozása** egy VPN-kapcsolat beállítása **csoportházirend-alapú** jelenik meg a VPN-típust. Válassza ezt a beállítást. Csak a **útválasztó alapú** beállítás támogatott az Azure Stackben.

<!-- 1902460 -  IS ASDK --> 
- Az Azure Stack is támogatja egyetlen *helyi hálózati átjáró* IP-címenként. Ez az összes bérlői előfizetések között. Az első helyi hálózati átjáró kapcsolatának, ezt követő létrehozását, hozzon létre egy helyi hálózati átjáró erőforrás azonos IP-címmel rendelkező kísérlet után le lesznek tiltva.

<!-- 16309153 -  IS ASDK --> 
- A DNS-kiszolgáló beállítását a létrehozott virtuális hálózat *automatikus*, változó, egy egyéni DNS-kiszolgáló meghibásodik. A frissített beállításokat nem kerülnek az adott virtuális hálózatban lévő virtuális gépen.

<!-- 2702741 -  IS ASDK --> 
- Nyilvános IP-címek, a dinamikus kiosztási módszer használatával üzembe helyezett nem biztos, hogy egy állítsa le és vonja vissza kiadása után megőrzi.

<!-- 2529607 - IS ASDK --> 
- Azure Stack során *titkos Elforgatás*, egy ideig, amely nyilvános IP-címek nem érhetők el 2 – 5 percet.

<!-- 2664148 - IS ASDK --> 
- Olyan esetekben, ahol a bérlői fér hozzá a virtuális gépek egy S2S VPN-alagút használatával akkor léphetnek fel egy olyan forgatókönyvet, ahol sikertelen csatlakozási kísérletek, ha a helyszíni alhálózati hozzá lett adva a helyi hálózati átjáró után átjárón már létre lett hozva. 


<!--  #### SQL and MySQL  -->


#### <a name="app-service"></a>App Service
<!-- 2352906 - IS ASDK --> 
- Felhasználók, az első Azure-függvény létrehozása az előfizetés előtt regisztrálnia kell a tárolásierőforrás-szolgáltató.

<!-- TBD - IS ASDK --> 
- Annak érdekében, hogy a horizontális felskálázás infrastruktúra (feldolgozók, felügyeleti, előtér-szerepkörök), PowerShell számítási a kibocsátási megjegyzésekben leírtak szerint kell használnia.  


#### <a name="usage"></a>Használat  
<!-- TBD -  IS ASDK --> 
- Használati nyilvános IP-használati mérőszámok adatait jeleníti meg, azonos *EventDateTime* érték helyett minden egyes rekordja esetében a *TimeDate* , amely megjeleníti a rekord létrehozásának stamp. Ezeket az adatokat jelenleg nem használható nyilvános IP-címek használata pontos nyilvántartása végrehajtásához.

<!-- #### Identity -->

## <a name="build-11808097"></a>1.1808.0.97 összeállítása

### <a name="new-features"></a>Új funkciók
A build tartalmaz a következő fejlesztések és javítások az Azure Stackhez.  

<!-- 1658937 | ASDK, IS --> 
- **Indítsa el a biztonsági mentések előre meghatározott ütemezés szerint** -telepíthetőek, mint az Azure Stack is automatikusan aktiválja infrastruktúra biztonsági mentések rendszeres időközönként az emberi beavatkozás kiküszöbölése érdekében. Az Azure Stack biztonsági mentésekhez, régebbi, mint a meghatározott adatmegőrzési időszaka a külső megosztást is automatikusan törölni fogja. További információkért lásd: [biztonsági mentés engedélyezése az Azure Stack a PowerShell-lel](../azure-stack-backup-enable-backup-powershell.md).

<!-- 2496385 | ASDK, IS -->  
- **A hozzáadott adatátviteli időt a teljes biztonsági mentés időpontja.** További információkért lásd: [biztonsági mentés engedélyezése az Azure Stack a PowerShell-lel](../azure-stack-backup-enable-backup-powershell.md).

<!-- 1702130 | ASDK, IS --> 
- **Biztonsági mentési külső kapacitás most a megfelelő kapacitást a külső megosztás jeleníti meg.** (Ez korábban rögzített kód 10 GB-ra.) További információkért lásd: [biztonsági mentés engedélyezése az Azure Stack a PowerShell-lel](../azure-stack-backup-enable-backup-powershell.md).
 
<!-- 2753130 |  IS, ASDK   -->  
- **Az Azure Resource Manager-sablonok mostantól támogatják a feltétel elem** – most már telepítheti egy erőforrást egy Azure Resource Manager-sablonban vonatkozó feltétellel. A sablon üzembe helyezéséhez egy erőforrást, egy feltételt, például kiértékelése, ha a jelenlegi paraméter értéke alapján is tervezhet. Feltételként egy sablon használatával kapcsolatos információkért lásd: [erőforrás feltételes üzembe helyezése](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) és [változók szakaszban az Azure Resource Manager-sablonok](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) az Azure dokumentációjában olvashatók. 

   Is használhatja a sablonok [erőforrások üzembe helyezése az egynél több előfizetésen vagy erőforráscsoporton](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

<!--2753073 | IS, ASDK -->  
- **A rendszer frissítette az API a Microsoft.Network erőforrás Verziótámogatás** támogatását olyan API-verzió 2017-10-01, 2015-06-15 az Azure Stack hálózati erőforrásokhoz. 2017-10-01-es és a 2015-06-15 közötti erőforrás-verziók támogatása nem érhető el ebben a kiadásban. Tekintse meg [Azure Stack hálózati szempontjai](../user/azure-stack-network-differences.md) funkcióinak különbségeit.

<!-- 2272116 | IS, ASDK   -->  
- **Az Azure Stack megjelenésével támogatottá fordított DNS-lekérdezések a kívülről elérhető az Azure Stack-infrastruktúra-végpontokra** (amely a portál, adminportal, felügyeleti és adminmanagement). Ez lehetővé teszi az Azure Stack külső végpont nevének IP-címről fogja megoldani.

<!-- 2780899 |  IS, ASDK   --> 
- **Az Azure Stack már támogatja a további hálózati adapterek hozzáadása egy meglévő virtuális Gépet.**  Ez a funkció a portal, PowerShell és CLI használatával érhető el. További információkért lásd: [hozzáadása vagy eltávolítása a hálózati adapterek](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) az Azure dokumentációjában olvashatók. 

<!-- 2222444 | IS, ASDK   -->  
- **A pontosság és a rugalmasság fejlettebbé használati mérőszámok hálózatot**. Hálózati használati mérőszámok immár pontos és felfüggesztve fiók-előfizetésre, leállás és a versenyhelyzetek azokat.

<!-- 2297790 | IS, ASDK -->  
- **Az Azure Stack syslog-ügyfél (előzetes verziójú funkció) fejlesztései**. Ez az ügyfél lehetővé teszi, hogy a naplózási és az Azure Stack-infrastruktúra syslog server vagy a biztonsági biztonságiadat- és eseménykezelés (SIEM) felügyeleti szoftverek az Azure Stackhez külső naplók továbbítását. A syslog-ügyfél most már támogatja a TCP protokoll az egyszerű szöveges vagy a TLS 1.2-titkosítás, az utóbbi folyamatban van az alapértelmezett konfiguráció. Konfigurálhatja a TLS-kapcsolat csak kiszolgálói vagy kölcsönös hitelesítéssel.

  Hogyan kommunikál a a syslog-ügyfél (például a protokoll, titkosítási és hitelesítési) konfigurálni a syslog-kiszolgálónak, a Set-SyslogServer parancsmag használható. Ez a parancsmag a kiemelt végponthoz (EGP) érhető el. 

- <!-- ASDK --> **A Virtual Machine Scale Sets katalóguselemek immár beépített**.  Katalóguselemek virtuálisgép-méretezési most már elérhetők a felhasználói és rendszergazdai portálokon őket letöltése nélkül. 

- <!-- IS, ASDK --> **Skálázás virtuálisgép-méretezési**.  A portálon való [méretezése egy virtuálisgép-méretezési csoportban](../azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (VMSS).   

- <!-- 2489570 | IS ASDK--> **Egyéni IPSec/IKE-házirend konfigurációk támogatása** a [VPN-átjárók az Azure Stackben](/azure/azure-stack/azure-stack-vpn-gateway-about-vpn-gateways).

- <!-- | IS ASDK--> **Kubernetes Piactéri elem**. Kubernetes-fürtök használatával most már telepítheti a [Kubernetes Piactéri elem](/azure/azure-stack/azure-stack-solution-template-kubernetes-cluster-add). Felhasználók válassza ki a Kubernetes-elemet, és töltse ki a Kubernetes-fürt üzembe helyezése az Azure Stackhez néhány paramétert. A sablonok az a célja, hogy egyszerűsítsen felhasználóknak, hogy a telepítő fejlesztési-tesztelési Kubernetes-üzembehelyezés néhány lépésben.

<!-- ####### | IS, ASDK -->  
- **Az Azure Resource Manager tartalmazza a régió nevét.** Ebben a kiadásban objektumokat beolvasni az Azure Resource Manager most már tartalmazza a régió neve attribútum. Ha egy meglévő PowerShell-szkriptet közvetlenül továbbítja az objektum más parancsmagok, a parancsfájl hibaüzenet és sikertelen lesz. Azure Resource Manager megfelelő működés, és megköveteli a hívó ügyfél régió attribútum kivonása céljából. További információ az Azure Resource Manager lásd [Azure Resource Manager dokumentációja](https://docs.microsoft.com/azure/azure-resource-manager/).

<!-- TBD | IS, ASDK -->  
- **Áthelyezése előfizetések között a delegált szolgáltatók.** Most már továbbléphet az előfizetések közötti új vagy meglévő Directory ugyanazt bérlőhöz tartozó delegált szolgáltatói előfizetések. Az alapértelmezett szolgáltatója előfizetéshez tartozó előfizetéseket is áthelyezhetők a Directory-bérlőben azonos delegált szolgáltatói előfizetések. További információ: [ajánlat delegálása az Azure Stack](../azure-stack-delegated-provider.md).
 
<!-- 2536808 IS ASDK --> 
- **Virtuális gép létrehozáskor továbbfejlesztett** rendszerképek az Azure marketplace-ről letöltött használatával létrehozott virtuális gépek számára.

### <a name="fixed-issues"></a>Hibák kijavítva:
- <!-- IS ASDK--> Azt javítva a rendelkezésre állási csoportot a portálon, amely eredményezett a egy tartalék tartomány és a frissítési tartomány 1 csoport létrehozásához.

<!-- TBD | ASDK, IS --> 
- Különböző fejlesztések történtek a frissítési folyamat megbízhatóbbá tétele. Emellett javítja a mögöttes infrastruktúra, amely javítja a csomópont kiürítési, ezáltal minimálisra csökkentik a számítási feladatok esetében lehetséges állásidő a frissítés során került sor.

<!--2292271 | ASDK, IS --> 
- Ahol egy módosított kvótakorlát miatt nem vonatkozik a meglévő előfizetések probléma tudjuk rögzíteni.  Most egy kvótát egy hálózati erőforráshoz tartozó ajánlat emeli, és a csomag társított felhasználói előfizetés, az új korlátozás vonatkozik a már meglévő előfizetéseket, valamint a az új előfizetési lehetőségekről.

<!-- 2448955 | IS ASDK --> 
- Most már sikeresen lekérdezheti a Tevékenységnaplók az időzóna UTC + N üzembe helyezett rendszerekhez.    

<!-- 2319627 |  ASDK, IS --> 
- Biztonsági mentési konfiguráció paraméterei (elérési út/felhasználónév/jelszó/titkosítási kulcs) előzetes ellenőrzése nem megfelelő beállítások már nem a biztonsági mentési konfigurációhoz állítja be. (Korábban nem megfelelő beállítások állított be a biztonsági mentés és adatvezérelt majd sikertelen biztonsági mentés.)

<!-- 2215948 |  ASDK, IS --> 
- A biztonsági mentési listában most frissíti a külső megosztást a manuálisan törölje a biztonsági mentés során.

<!-- 2360715 |  ASDK, IS -->  
- Adatközpont integrációja beállításakor, már nem elérhető az AD FS metaadatait tartalmazó fájl egy megosztást. További információkért lásd: [összevonási metaadatait tartalmazó fájl azáltal, hogy az AD FS-integráció beállításával](../azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

<!-- 2388980 | ASDK, IS --> 
- Hogy kijavítva, hogy megakadályozta abban a rendelt felhasználók egy meglévő nyilvános IP-címet, amely korábban korábban hozzárendelve egy hálózati adapter vagy egy új hálózati adapter vagy a terheléselosztó a terheléselosztó.  

<!-- 2551834 - IS, ASDK --> 
- Ha bejelöli *áttekintése* a rendszergazdai vagy a felhasználói portált, a storage-fiókok a *Essentials* panelen most már megfelelően megjelenik-e a várt információkat. 

<!-- 2551834 - IS, ASDK --> 
- Ha bejelöli *címkék* a rendszergazdai vagy a felhasználói portált a storage-fiókok, az információt mostantól megfelelően megjelenik-e.

<!-- TBD - IS ASDK --> 
- Az Azure Stack jelen verziójában javítja a problémát, amely megakadályozta az OEM bővítménycsomagok illesztőprogram-frissítések alkalmazását.

<!-- 2055809- IS ASDK --> 
- A hibát, amely megakadályozza, hogy a számítási panelről virtuális gépek törlése, ha a virtuális gép létrehozása nem sikerült rögzíteni azt.  

<!--  2643962 IS ASDK -->  
- A riasztás *kevés a memória-kapacitás* hibásan már nem jelenik meg.

<!--  TBD ASDK --> 
- A virtuális gép, amelyen a jogosultság végpontja (EGP) 4GB-ra nőtt. A virtuális gép a ASDK AzS-ERCS01 neve.

- <!--  TBD – IS, ASDK --> *Alapszintű A* virtuálisgép-méretek esetében kivezettük [virtuálisgép-méretezési csoportok létrehozása](../azure-stack-compute-add-scalesets.md) (VMSS) a portálon keresztül. Ez a méret a VMSS létrehozásához, használja a PowerShell vagy a sablont. 

### <a name="known-issues"></a>Ismert problémák

#### <a name="portal"></a>Portál  
- <!-- 2967387 – IS, ASDK --> A fiók használatával jelentkezzen be az Azure Stack rendszergazdai vagy a felhasználói portálon jelenik meg **azonosítatlan felhasználó**. Ez akkor fordul elő, ha a fiók nem rendelkezik vagy egy *első* vagy *utolsó* megadott név. A probléma megkerüléséhez az első vagy utolsó nevét adja meg a felhasználói fiók szerkesztésével. Meg kell majd jelentkezzen ki, majd majd jelentkezzen be újra a portálra. 

-  <!--  2873083 - IS ASDK --> Amikor a portal használatával hozzon létre egy virtuálisgép-méretezési csoport beállítása (VMSS), a *példány mérete* legördülő továbbra sem töltődik be megfelelően az Internet Explorer használata esetén. Ez a probléma megkerüléséhez használja egy másik böngészőben egy VMSS létrehozása a portál használata során.  

#### <a name="portal"></a>Portál  
<!-- 2931230 – IS  ASDK --> 
- Nem lehet törölni a felhasználói előfizetés, mint egy kiegészítő csomag hozzáadott tervek, akkor is, ha a csomag eltávolítása a felhasználói előfizetés. A terv marad mindaddig, amíg az előfizetéseket, hogy a kiegészítő csomagot is törlődik. 

<!--2760466 – IS  ASDK --> 
- Amikor telepít egy új Azure Stack-környezet, amely ebben a verzióban fut, a riasztás azt jelzi, *aktiválás szükséges* nem jelenítik meg. [Az aktiválás](../azure-stack-registration.md) marketplace szindikálási használatához szükség. 

<!-- TBD - IS ASDK --> 
- A két felügyeleti előfizetés-típus verziójú 1804-es verzióban bevezetett nem használható. Az előfizetés-típusok a következők **előfizetés mérési**, és **Használatalapú előfizetés**. Ezek a típusok előfizetés **előfizetés mérési**, és **Használatalapú előfizetés**. Ezek a típusok előfizetés új Azure Stack-környezetek verziójától kezdve az 1804 láthatók, de még nem használatra kész. Ön továbbra is használja a **alapértelmezett szolgáltatói előfizetés** típusa.

<!-- 2403291 - IS ASDK --> 
- Lehet, hogy nincs használatát a vízszintes görgetősáv a rendszergazdai és felhasználói portál alján. Ha nem éri el a vízszintes görgetősáv, az útkövetés, keresse meg a portálon egy előző panelen válassza a panel neve, a tetején található navigációs listában megtekinteni kívánt használati a portál bal oldali.
  ![Breadcrumb](media/asdk-release-notes/breadcrumb.png)

<!-- TBD -  IS ASDK --> 
- Felhasználói előfizetések eredmények az árva erőforrások törlése. Áthidaló megoldásként először törölje a felhasználó vagy a teljes erőforráscsoportot, és törölje a felhasználói előfizetések.

<!-- TBD -  IS ASDK --> 
- Engedélyek nem tekintheti meg az Azure Stack portálok használata az előfizetéshez. Áthidaló megoldásként a PowerShell segítségével ellenőrizze az engedélyeket.

<!--  TBD | ASDK -->  
- Az Azure Stack üzembe helyezéshez az alapértelmezett időzóna UTC-re most első állítja. Kiválaszthat egy időzóna telepítésekor az Azure Stack, azonban azt automatikusan visszaáll az UTC Időzóna alapértelmezés szerint a telepítés során.

#### <a name="health-and-monitoring"></a>Állapot és figyelés
<!-- 1264761 - IS ASDK -->  
- A riasztásokat a *egészségügyi vezérlő* összetevő, amely rendelkezik a következő adatokat:  

   #1 riasztás:
   - NÉV:  Infrastruktúra-szerepkör nem megfelelő állapotú
   - SÚLYOSSÁG: Figyelmeztetés
   - ÖSSZETEVŐ: Vezérlő állapota
   - LEÍRÁS: A health vezérlő szívverési képolvasó nem érhető el. Ez érinthet rendszerállapot-jelentések és metrikákat.  

  Riasztási #2:
   - NÉV:  Infrastruktúra-szerepkör nem megfelelő állapotú
   - SÚLYOSSÁG: Figyelmeztetés
   - ÖSSZETEVŐ: Vezérlő állapota
   - LEÍRÁS: A health vezérlő tartalék képolvasó nem érhető el. Ez érinthet rendszerállapot-jelentések és metrikákat.

  Mindkét riasztás biztonságosan figyelmen kívül hagyható, és idővel automatikusan bezáródik.  

<!-- 2368581 - IS. ASDK --> 
- Az Azure Stack operátorait, ha kevés a szabad memória figyelmeztetést, és a bérlői virtuális gépek sikertelen üzembe helyezés egy *Fabric Virtuálisgép-létrehozási hiba*, lehetséges, hogy az Azure Stack-blokk esik a rendelkezésre álló memória. Használja a [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) legjobban megérteni az elérhető kapacitás a számítási feladatokhoz.

<!-- TBD - IS. ASDK --> 
- Futtatásakor a **Test-AzureStack** a kiemelt végponthoz (EGP), a parancsmag a **Azure Stack infrastruktúra szerepkör példány teljesítményének** teszt WARN üzenetet hoz létre a virtuális gép ERCS. Biztonságosan figyelmen kívül a figyelmeztetés az üzenetet, és továbbra is használhatja a ASDK.

#### <a name="compute"></a>Compute
<!-- 2494144 - IS, ASDK --> 
- Amikor kiválasztja a virtuális gép üzembe helyezéséhez virtuálisgép-méretet, néhány F-sorozat Virtuálisgép-méretek nem láthatók a virtuális gép létrehozásakor a méret választó részeként. Az alábbi virtuális gépeinek-választójában jelenítse nem jelennek meg: *F8s_v2*, *F16s_v2*, *F32s_v2*, és *F64s_v2*.  
  Áthidaló megoldásként használja a következő módszerek egyikét virtuális gép üzembe helyezése. Az egyes módszerek kell adja meg a használni kívánt virtuális gép méretét.

- <!-- 3099544 – IS, ASDK --> Amikor hoz létre egy új virtuális gép (VM) az Azure Stack portal használatával, és a Virtuálisgép-méret választja, a USD/hónap oszlop jelenik meg egy **nem érhető el** üzenet. Nem jelenik meg ebben az oszlopban; megjelenítés, a virtuális gép díjszabási oszlop nem támogatott az Azure Stackben.

- <!-- 2869209 – IS, ASDK --> Használatakor a [ **Add-AzsPlatformImage** parancsmag](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), kell használnia a **- OsUri** paramétert, a tárfiók URI, ahol fel a rendszer a lemezen. Ha a lemez a helyi elérési utat használ, a parancsmag futtatása sikertelen, a következő hibával: *Hosszú ideig futó művelet sikertelen volt, "Sikertelen" állapotú*. 

- <!--  2966665 – IS, ASDK --> SSD csatolása a prémium szintű méretre adatlemezek felügyelt lemez virtuális gépek (DS, DSv2, Fs, Fs_V2) meghiúsul egy hiba miatt:  *Nem sikerült frissíteni a lemezt a virtuális gép "vmname" hiba: A kért művelet nem hajtható végre, mert a Virtuálisgép-méret nem támogatott a "Premium_LRS" fióktípust "Fs_v2 Standard_DS/Ds_V2 és FS)*

   A probléma megkerüléséhez használja *Standard_LRS* adatlemez, hanem *Premium_LRS lemezek*. Felhasználása *Standard_LRS* adatlemezek nem változik, iops-t vagy a számlázási költségeket.  

- <!--  2795678 – IS, ASDK --> Ha a portálon hozhat létre virtuális gépeket (VM) a prémium szintű virtuális gép méretét (DS, Ds_v2, FS, FSv2) használ, a virtuális gép létrejött, a standard szintű storage-fiókban. A standard szintű tárfiók-létrehozás nem befolyásolja, funkcionálisan IOPs, vagy számlázási. 

   Biztonságosan figyelmen kívül hagyhatja a figyelmeztetést, amely szerint: *Úgy döntött, a standard szintű lemezes használatához olyan méretnél, amely támogatja a prémium szintű lemezek használatát. Ez ronthatja az operációs rendszer teljesítményét, és nem ajánlott. Fontolja meg inkább a prémium szintű tárolást (SSD) használatát.*

- <!-- 2967447 - IS, ASDK --> A virtuálisgép-méretezési csoportot (VMSS) hozzon létre felhasználói élményt 7.2 CentOS-alapú üzembe helyezési lehetőséget biztosít. A rendszerképet az Azure Stacken nem érhető el, mert az üzemelő példány egy másik operációs rendszer válasszon vagy adjon meg egy másik CentOS képet az operátor telepítené a marketplace-ről letöltött ARM-sablonokkal.

<!-- TBD -  IS ASDK --> 
- A virtual machine scale sets méretezési beállításait a portálon nem érhetők el. Áthidaló megoldásként használhatja [Azure PowerShell-lel](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). PowerShell-verzió különbségek, miatt kell használnia a `-Name` paraméter helyett `-VMScaleSetName`.

<!-- TBD -  IS ASDK --> 
- Az Azure Stack felhasználói portál virtuális gépek létrehozásakor a portál megjeleníti, amelyek a D sorozatú virtuális gépek csatolhat a szolgáltatáskéréshez adatlemezek száma helytelen. Az összes támogatott D sorozatú virtuális gépek, az Azure konfigurációs lehetővé teszi tetszőleges számú adatlemezeket.

<!-- TBD -  IS ASDK --> 
- Egy Virtuálisgép-lemezkép nem hozható létre, amikor egy sikertelen elem, amely nem törölhető, előfordulhat, hogy lehet hozzáadni a VM-lemezképek számítás panel.

  Áthidaló megoldásként hozzon létre egy új Virtuálisgép-lemezkép hozható létre a Hyper-V keresztül próba VHD-vel (New-VHD-elérési út C:\dummy.vhd – rögzített - SizeBytes 1 GB-os). Ez a folyamat kell kijavítani a problémát, amely megakadályozza, hogy a sikertelen elem törlése. Ezt követően a helyőrző rendszerkép létrehozása után 15 percig sikeresen törölheti.

  A letöltés a Virtuálisgép-rendszerképet, amely korábban nem sikerült, majd próbálja megismételni.

<!-- TBD -  IS ASDK --> 
- Ha egy virtuális gép üzembe helyezése a bővítmény kiépítési túl hosszú időt vesz igénybe, felhasználók hagyja, hogy a kiépítési időtúllépési helyett a folyamat fel vagy töröljön a virtuális gép leállítása.  

<!-- 1662991 - IS ASDK --> 
- Linux rendszerű virtuális gép diagnosztikai nem támogatott az Azure Stackben. A Virtuálisgép-diagnosztika engedélyezve van egy Linux rendszerű virtuális Gépet helyez üzembe, a központi telepítés sikertelen lesz. Az üzembe helyezés akkor is sikertelen, ha engedélyezi a Linux rendszerű virtuális gép alapvető metrikák diagnosztikai beállításokon keresztül.

<!-- 2724961- IS ASDK --> 
- Amikor regisztrál a **Microsoft.Insight** előfizetési beállítások, az erőforrás-szolgáltató egy Windows virtuális gép létrehozása és a vendég operációs rendszer diagnosztikai engedélyezve van, a virtuális gépek – Áttekintés lap nem jelenik meg a mérőszámadatok. 

 

#### <a name="networking"></a>Hálózat
<!-- 1766332 - IS, ASDK --> 
- A **hálózatkezelés**, ha rákattint **VPN-átjáró létrehozása** egy VPN-kapcsolat beállítása **csoportházirend-alapú** jelenik meg a VPN-típust. Válassza ezt a beállítást. Csak a **útválasztó alapú** beállítás támogatott az Azure Stackben.

<!-- 1902460 -  IS ASDK --> 
- Az Azure Stack is támogatja egyetlen *helyi hálózati átjáró* IP-címenként. Ez az összes bérlői előfizetések között. Az első helyi hálózati átjáró kapcsolatának, ezt követő létrehozását, hozzon létre egy helyi hálózati átjáró erőforrás azonos IP-címmel rendelkező kísérlet után le lesznek tiltva.

<!-- 16309153 -  IS ASDK --> 
- A DNS-kiszolgáló beállítását a létrehozott virtuális hálózat *automatikus*, változó, egy egyéni DNS-kiszolgáló meghibásodik. A frissített beállításokat nem kerülnek az adott virtuális hálózatban lévő virtuális gépen.

<!-- 2702741 -  IS ASDK --> 
- Nyilvános IP-címek, a dinamikus kiosztási módszer használatával üzembe helyezett nem biztos, hogy egy állítsa le és vonja vissza kiadása után megőrzi.

<!-- 2529607 - IS ASDK --> 
- Azure Stack során *titkos Elforgatás*, egy ideig, amely nyilvános IP-címek nem érhetők el 2 – 5 percet.

<!-- 2664148 - IS ASDK --> 
- Olyan esetekben, ahol a bérlői fér hozzá a virtuális gépek egy S2S VPN-alagút használatával akkor léphetnek fel egy olyan forgatókönyvet, ahol sikertelen csatlakozási kísérletek, ha a helyszíni alhálózati hozzá lett adva a helyi hálózati átjáró után átjárón már létre lett hozva. 


#### <a name="sql-and-mysql"></a>SQL- és MySQL
<!-- TBD - ASDK --> 
- Az adatbázist üzemeltető kiszolgálók a erőforrás-szolgáltató és a felhasználó munkaterhelések által használható kell kijelölnie. Bármely egyéb fogyasztói, beleértve az App Services által használt példány nem használhat.

<!-- IS, ASDK --> 
- Különleges karaktereket, szóközöket és időszakok, többek között nem támogatottak a **termékcsalád** nevezze a Termékváltozat létrehozott SQL- és MySQL erőforrás-szolgáltatókat.

#### <a name="app-service"></a>App Service
<!-- 2352906 - IS ASDK --> 
- Felhasználók, az első Azure-függvény létrehozása az előfizetés előtt regisztrálnia kell a tárolásierőforrás-szolgáltató.

<!-- TBD - IS ASDK --> 
- Annak érdekében, hogy a horizontális felskálázás infrastruktúra (feldolgozók, felügyeleti, előtér-szerepkörök), PowerShell számítási a kibocsátási megjegyzésekben leírtak szerint kell használnia.  

<!-- TBD - IS ASDK --> 
- App Service-ben csak lesz üzembe helyezve a *alapértelmezett szolgáltatói előfizetés* jelenleg.

#### <a name="usage"></a>Használat  
<!-- TBD -  IS ASDK --> 
- Használati nyilvános IP-használati mérőszámok adatait jeleníti meg, azonos *EventDateTime* érték helyett minden egyes rekordja esetében a *TimeDate* , amely megjeleníti a rekord létrehozásának stamp. Ezeket az adatokat jelenleg nem használható nyilvános IP-címek használata pontos nyilvántartása végrehajtásához.

<!-- #### Identity -->

