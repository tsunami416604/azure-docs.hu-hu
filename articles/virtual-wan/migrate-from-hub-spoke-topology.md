---
title: 'Architektúra: Áttelepítés az Azure Virtual WAN-ra'
description: Ismerje meg, hogyan miként telepíthet át az Azure Virtual WAN-ra.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 8aa4fe143c78d2053ce8c48e4866a5522057aa0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063022"
---
# <a name="migrate-to-azure-virtual-wan"></a>Áttelepítés az Azure virtuális WAN-ra

Az Azure Virtual WAN lehetővé teszi a vállalatok számára, hogy egyszerűsítsék globális kapcsolataikat, hogy kihasználhassák a Microsoft globális hálózatának előnyeit. Ez a cikk technikai részleteket tartalmaz azoknak a vállalatoknak, amelyek egy meglévő ügyfél által felügyelt hub-and-spoke topológiáról a Microsoft által felügyelt virtuális WAN-központokat használó kialakításra szeretnének áttérni.

Az Azure Virtual WAN által a felhőközpontú, modern vállalati globális hálózatot alkalmazó vállalatok számára lehetővé tesz előnyökről a [Globális tranzithálózati architektúra és](virtual-wan-global-transit-network-architecture.md)a Virtual WAN című témakörben talál tájékoztatást.

![hub és](./media/migrate-from-hub-spoke-topology/hub-spoke.png)
küllős**ábra: Azure Virtual WAN**

Az Azure Virtual Datacenter (VDC) küllős kapcsolódási modelljét ügyfeleink ezrei fogadták el az Azure Networking alapértelmezett tranzitív útválasztási viselkedésének kihasználására egyszerű és méretezhető felhőhálózatok létrehozása érdekében. Az Azure Virtual WAN ezekre a koncepciókra épül, és olyan új képességeket vezet be, amelyek lehetővé teszik a globális kapcsolódási topológiákat, nem csak a helyszíni helyek és az Azure között, hanem lehetővé teszik az ügyfelek számára, hogy a Microsoft-hálózat méretezését kihasználva bővítsék meglévő globális hálózatok.

Ez a cikk bemutatja, hogyan telepítheti át a meglévő hibrid környezet et a Virtual WAN.This article shows how to migrate an existing hybrid environment to Virtual WAN.

## <a name="scenario"></a>Forgatókönyv

A Contoso egy globális pénzügyi szervezet, amelynek irodái európában és Ázsiában is vannak. Azt tervezik, hogy meglévő alkalmazásaikat egy helyszíni adatközpontból az Azure-ba helyezik át, és a VDC architektúrán alapuló alaptervet hoztak ki, beleértve a hibrid kapcsolatregionális ügyféláltal felügyelt központi virtuális hálózatait is. A felhőalapú technológiákra való áttérés részeként a hálózati csapat feladata annak biztosítása, hogy a kapcsolatuk a vállalkozás előrelépésre legyen optimalizálva.

Az alábbi ábra a meglévő globális hálózat magas szintű nézetét mutatja be, beleértve a több Azure-régióhoz való kapcsolódást is.

![Contoso meglévő hálózati](./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png)
topológia**ábra: Contoso meglévő hálózati topológia**

A meglévő hálózati topológiából a következő pontok érthetők:

- A hub-and-küllős topológiát több régióban is használják, beleértve az ExpressRoute Premium-áramköröket a közös hálózati WAN-hoz való kapcsolódáshoz.

- Néhány ilyen helyek is rendelkeznek VPN-alagutak közvetlenül az Azure-ba, hogy elérje a Microsoft felhőben üzemeltetett alkalmazásokat.

## <a name="requirements"></a>Követelmények

A hálózati csapat feladata egy globális hálózati modell, amely támogatja a Contoso áttelepítésa a felhőbe, és optimalizálnia kell a költségek, a méretezés és a teljesítmény területén. Összefoglalva, a következő követelményeknek kell megfelelni:

- Biztosítsa mind a székhely-negyedévi (HQ) és a fiókirodák számára a felhőalapú üzemeltetett alkalmazások optimális elérési útját.
- A VPN-megszüntetéshez a meglévő helyszíni adatközpontokra (DC) való támaszkodás megszüntetése a következő kapcsolódási útvonalak megtartása mellett:
  - **Ág -to- VNet**: VPN csatlakoztatott irodák nak képesnek kell lennie a felhőbe áttelepített alkalmazások a helyi Azure-régióban.
  - **Ág -to- Hub -to- Hub -to- VNet**: VPN csatlakoztatott irodák nak képesnek kell lennie a távoli Azure-régióban a felhőbe áttelepített alkalmazások eléréséhez.
  - **Ág -to- branch**: A regionális VPN-csatlakoztatott irodáknak képesnek kell lenniük kommunikálni egymással és expressroute-alapú HQ/DC-helyekkel.
  - **Ág -to- Hub -to-Hub -to-branch**: A globálisan leválasztott VPN-csatlakoztatott irodáknak képesnek kell lenniük kommunikálni egymással és bármely ExpressRoute-csatlakoztatott HQ/DC-hellyel.
  - **Ág -–Internet**: A csatlakoztatott webhelyeknek képesnek kell lenniük az internettel való kommunikációra. Ezt a forgalmat szűrni és naplózni kell.
  - **VNet -to- VNet**: Küllős virtuális hálózatok ugyanabban a régióban képesnek kell lennie kommunikálni egymással.
  - **VNet -to- Hub -to- Hub -to- VNet**: A különböző régiókban lévő küllős virtuális hálózatoknak képesnek kell lenniük egymással kommunikálni.
- Lehetővé teszi a Contoso barangolásos felhasználók (laptop és telefon) számára, hogy a vállalati erőforrásokhoz férjenek hozzá, miközben nem a vállalati hálózaton vannak.

## <a name="azure-virtual-wan-architecture"></a><a name="architecture"></a>Azure Virtual WAN architektúra

Az alábbi ábra a frissített céltopológia magas szintű nézetét mutatja be az Azure Virtual WAN használatával az előző szakaszban részletezett követelmények teljesítéséhez.

![Contoso virtuális](./media/migrate-from-hub-spoke-topology/vwan-architecture.png)
WAN architektúra**Ábra: Azure Virtual WAN architektúra**

Összegzés:

- Az európai központ továbbra is expressroute-kapcsolatban marad, a helyszíni Európai tartományvezérlőteljes mértékben áttelepül az Azure-ba, és most levan szerelve.
- Asia DC and HQ remain connected to Private WAN. Az Azure Virtual WAN mostantól a helyi szolgáltatóhálózat bővítésére és globális kapcsolatra is használható.
- A Nyugat-Európában és Délkelet-Ázsiában telepített Azure Virtual WAN-központok az ExpressRoute- és VPN-alapú eszközök kapcsolódási központjának biztosítása érdekében.
- A hubok a központi felhasználók számára is biztosítvpn-végződtetést több ügyféltípuson keresztül a globális hálóhálózathoz való OpenVPN-kapcsolat használatával, így nem csak az Azure-ba áttelepített alkalmazások, hanem a helyszíni erőforrások is hozzáférhetnek.
- Az Azure Virtual WAN által biztosított virtuális hálózaton belüli erőforrások internetkapcsolata.

Az Azure Virtual WAN által is biztosított távoli webhelyek internetkapcsolata. A helyi internetes kitörés tanusíthatja a partnerintegráción keresztül a SaaS-szolgáltatások, például az Office 365 optimális elérését.

## <a name="migrate-to-virtual-wan"></a>Migrálás virtuális WAN-ba

Ez a szakasz az Azure Virtual WAN-ra való áttelepítés különböző lépéseit mutatja be.

### <a name="step-1-vdc-hub-and-spoke-single-region"></a>1. lépés: VDC hub-and-spoke egyrégió

Tekintse át az architektúrát. Az alábbi ábrán a Contoso egyetlen régiós topológiája látható az Azure Virtual WAN bevezetése előtt:

![Egyrégiós](./media/migrate-from-hub-spoke-topology/figure1.png)
topológia**1.**

A Virtuális adatközpont (VDC) megközelítésének megfelelően az ügyfél által felügyelt központi virtuális hálózat több függvényblokkot tartalmaz:

- Megosztott szolgáltatások (több küllő által igényelt közös funkció). Példa: A Contoso Windows Server tartományvezérlőket használ az Infrastruktúra szolgáltatásként (IaaS) virtuális gépeken.
- Az IP/Routing tűzfal szolgáltatásokat egy külső hálózati virtuális készülék biztosítja, amely lehetővé teszi a küllős 3.
- Internetes bejövő forgalom/kimenő szolgáltatások, beleértve az Azure Application Gateway bejövő HTTPS-kérelmek és a virtuális gépeken futó külső proxy szolgáltatások az internetes erőforrások szűrt kimenő eléréséhez.
- ExpressRoute és VPN virtuális hálózati átjáró a helyszíni hálózatokhoz való kapcsolódáshoz.

### <a name="step-2-deploy-virtual-wan-hubs"></a>2. lépés: Virtuális WAN-elosztók telepítése

Virtuális WAN-elosztó üzembe helyezése minden régióban. Állítsa be a Virtual WAN hubot VPN-átjáróval és ExpressRoute-átjáróval az alábbi cikkekben leírtak szerint:

- [Oktatóanyag: Helyek közötti kapcsolat létrehozása az Azure Virtual WAN használatával](virtual-wan-site-to-site-portal.md)
- [Oktatóanyag: ExpressRoute-társítás létrehozása az Azure Virtual WAN használatával](virtual-wan-expressroute-portal.md)

> [!NOTE]
> Az Azure Virtual WAN-nak a standard termékváltozatot kell használnia a cikkben látható forgalmi útvonalak némelyikének engedélyezéséhez.

![Virtuális](./media/migrate-from-hub-spoke-topology/figure2.png)
**WAN-központok telepítése 2.**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>3. lépés: Távoli helyek (ExpressRoute és VPN) csatlakoztatása a Virtual WAN-hoz

Csatlakoztassa a virtual WAN-központot a meglévő ExpressRoute-áramkörökhöz, és állítsa be a helyek közötti VPN-eket az interneten keresztül bármely távoli ághoz.

> [!NOTE]
> A Virtual WAN hubhoz való csatlakozáshoz az Express Routes circuits-t prémium szintű termékváltozat-típusra kell frissíteni.

![Távoli helyek csatlakoztatása a Virtual WAN](./media/migrate-from-hub-spoke-topology/figure3.png)
**3.**

Ezen a ponton a helyszíni hálózati berendezések megkezdik a virtuális WAN által felügyelt központi virtuális hálózathoz rendelt IP-címterületnek tükröző útvonalak fogadását. A távoli VPN-kapcsolattal rendelkező ágak ebben a szakaszban két útvonalat fognak látni a küllővirtuális hálózatokban lévő meglévő alkalmazásokhoz. Ezeket az eszközöket úgy kell konfigurálni, hogy továbbra is használják a nívós alagutat a VDC hubhoz, hogy biztosítsák a szimmetrikus útválasztást az átmeneti fázisban.

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>4. lépés: Hibrid kapcsolat tesztelése a Virtual WAN-on keresztül

A felügyelt Virtual WAN hub éles kapcsolathoz való használata előtt azt javasoljuk, hogy állítson be egy tesztküllő virtuális hálózatot és virtuális WAN virtuális hálózatot. Ellenőrizze, hogy a tesztkörnyezethez való csatlakozás az ExpressRoute és a Hely és a hely közötti VPN segítségével működik-e, mielőtt folytatná a következő lépéseket.

![Hibrid kapcsolat tesztelése](./media/migrate-from-hub-spoke-topology/figure4.png)
a Virtual WAN**4.**

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>5. lépés: Átállási kapcsolat a virtuális WAN hubra

![Átmenet kapcsolat a](./media/migrate-from-hub-spoke-topology/figure5.png)
Virtual WAN hub**5.**

**a .** Törölje a meglévő társviszony-létesítési kapcsolatokat a küllővirtuális hálózatokból a régi VDC hubra. A küllővirtuális hálózatokban lévő alkalmazásokhoz való hozzáférés nem érhető el, amíg az a-c lépések be nem fejeződnek.

**B) Az (1)** bekezdésben Csatlakoztassa a küllővirtuális hálózatokat a virtuális hálózatkapcsolatokon keresztül a virtuális WAN-elosztóhoz.

**c.** Távolítson el minden olyan felhasználó által definiált útvonalat (UDR), amelyet korábban a küllős virtuális hálózatokban használtak a küllős kommunikációhoz. Ezt az elérési utat a Virtual WAN hubon elérhető dinamikus útválasztás engedélyezi.

**d) az (1)** bekezdésben A VDC-hub meglévő ExpressRoute- és VPN-átjárói mostantól le vannak szerelve a következő lépés (e) engedélyezéséhez.

**e.** Csatlakoztassa a régi VDC hubot (hub virtuális hálózat) a virtuális WAN hubhoz egy új virtuális hálózaton keresztül.

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>6. lépés: Régi hub válik megosztott szolgáltatások beszélt

Most újraterveztük Azure-hálózatunkat, hogy a Virtual WAN hub az új topológiánk központi pontja legyen.

![Régi hub lesz](./media/migrate-from-hub-spoke-topology/figure6.png)
megosztott szolgáltatások küllős**ábra 6: VDC hub-and-spoke a Virtual WAN migráció**

Mivel a Virtual WAN hub felügyelt entitás, és nem teszi lehetővé egyéni erőforrások, például virtuális gépek telepítését, a megosztott szolgáltatások blokkja most küllős virtuális hálózatként létezik, és olyan funkciókat üzemeltet, mint az Internetes be- és internet-hozzáférés az Azure Application Gateway-en keresztül vagy hálózati virtualizált készülék. A megosztott szolgáltatási környezet és a háttérrendszer virtuális gépei közötti forgalom most áthalad a virtuális WAN által felügyelt hubon.

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>7. lépés: Optimalizálja a helyszíni kapcsolatot, hogy teljes mértékben kihasználja a Virtual WAN-t

Ebben a szakaszban a Contoso többnyire befejezte az üzleti alkalmazások áttelepítését a Microsoft Cloud-ba, és csak néhány régebbi alkalmazás maradt a helyszíni tartományvezérlőn belül.

![Optimalizálja a helyszíni kapcsolatot, hogy](./media/migrate-from-hub-spoke-topology/figure7.png)
teljes mértékben kihasználja a Virtual WAN**7.**

Az Azure Virtual WAN teljes funkcionalitásának kihasználása érdekében a Contoso úgy dönt, hogy leszereli az örökölt helyszíni VPN-kapcsolatokat. A HQ vagy DC hálózatokelérését továbbra is hozzáférő ágak az Azure Virtual WAN beépített tranzitútválasztásával képesek áthaladni a Microsoft globális hálózatán.

> [!NOTE]
> Az ExpressRoute Global Reach egy alternatív választás azoknak az ügyfeleknek, akik a Microsoft gerincét szeretnék kihasználni meglévő privát WAN-jeik kiegészítésére.

## <a name="end-state-architecture-and-traffic-paths"></a>Végállapot-architektúra és forgalmi útvonalak

![Végállapot-architektúra](./media/migrate-from-hub-spoke-topology/figure8.png)
és forgalmi útvonalak**Ábra: Kétrégiós Virtual WAN**

Ez a szakasz azt ismerteti, hogy ez a topológia hogyan felel meg az eredeti követelményeknek, ha megvizsgál néhány példa forgalmi folyamatot.

### <a name="path-1"></a>1. elérési út

Az 1-es elérési út egy Ázsiai S2S VPN-kapcsolattal rendelkező ágból egy Délkelet-ázsiai régióban lévő Azure-virtuális hálózatba irányuló forgalmat jeleníti meg.

A forgalom a következőképpen halad:

- Az ázsiai ág rugalmas S2S BGP-kompatibilis alagutakon keresztül csatlakozik a délkelet-ázsiai virtuális WAN hubba.

- Az Asia Virtual WAN hub helyileg irányítja a forgalmat a csatlakoztatott virtuális hálózathoz.

![1. áramlás](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>2. elérési út

A 2-es elérési út az ExpressRoute-hoz csatlakoztatott európai központból a délkelet-ázsiai régióban lévő Azure-virtuális hálózathoz való forgalmat jeleníti meg.

A forgalom a következőképpen halad:

- Az európai főhadiszállás prémium ExpressRoute áramkörön keresztül csatlakozik a Nyugat-Európa Virtuális WAN hubhoz.

- A Virtual WAN hub-hub globális kapcsolat lehetővé teszi a távoli régióban csatlakoztatott virtuális hálózatra irányuló forgalom átvitelét.

![Áramlás 2](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>3. elérési út

A 3-as elérési út a privát WAN-hoz egy európai S2S-hez csatlakoztatott ághoz csatlakoztatott ázsiai helyszíni tartományvezérlőről érkező forgalmat mutatja.

A forgalom a következőképpen halad:

- Asia DC csatlakozik a helyi Private WAN fuvarozóhoz.

- ExpressRoute-kapcsolat helyileg leáll a private WAN csatlakozik a délkelet-ázsiai Virtual WAN hub.

- A Virtual WAN hub-hub globális kapcsolat lehetővé teszi a forgalom átvitelét.

![3. áramlás](./media/migrate-from-hub-spoke-topology/flow3.png)

### <a name="path-4"></a>4. elérési út

A 4-es elérési út a délkelet-ázsiai régióban lévő Azure-virtuális hálózattól a nyugat-európai régióban lévő Azure-virtuális hálózathoz való forgalmat jeleníti meg.

A forgalom a következőképpen halad:

- A Virtual WAN hub-hub globális kapcsolat lehetővé teszi az összes csatlakoztatott Azure-virtuális hálózat natív átvitelét további felhasználói konfiguráció nélkül.

![4. áramlás](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>5. út

Az 5-ös elérési út a központi VPN-felhasználóktól a nyugat-európai régióban lévő Azure-virtuális hálózatig irányuló forgalmat jeleníti meg.

A forgalom a következőképpen halad:

- A laptop- és mobileszköz-felhasználók az OpenVPN klienst használják az átlátható kapcsolathoz a Nyugat-Európában a P2S VPN átjáróhoz.

- A Nyugat-európai Virtuális WAN-központ helyileg irányítja a forgalmat a csatlakoztatott virtuális hálózathoz.

![5. áramlás](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>Biztonság és házirend-ellenőrzés az Azure tűzfalon keresztül

A Contoso most már érvényesítette az összes ág és a virtuális hálózatok közötti kapcsolatot a cikkben korábban tárgyalt követelményeknek megfelelően. A biztonsági ellenőrzésre és a hálózatelkülönítésre vonatkozó követelmények teljesítéséhez továbbra is el kell különíteniük és naplózniuk kell a forgalmat a központi hálózaton keresztül. Korábban ezt a funkciót egy hálózati virtuális berendezés (NVA) hajtotta végre. A Contoso a meglévő proxyszolgáltatásait is le szeretné szerelni, és natív Azure-szolgáltatásokat kíván használni a kimenő internet-szűréshez.

![Biztonság és házirend-ellenőrzés](./media/migrate-from-hub-spoke-topology/security-policy.png)
az Azure**tűzfalon keresztül: Azure tűzfal virtuális WAN-ban (biztonságos virtuális központ)**

A következő magas szintű lépések szükségesek az Azure Firewall virtuális WAN-központokba való bevezetéséhez, hogy engedélyezze az egységes házirend-vezérlési pontot. A folyamatról és a biztonságos virtuális központok koncepciójáról az [Azure Firewall Manager](../firewall-manager/index.yml)című témakörben talál további információt.

1. Hozzon létre Azure Firewall-szabályzatot.
2. Tűzfal-házirend összekapcsolása az Azure Virtual WAN hubbal. Ez a lépés lehetővé teszi, hogy a meglévő Virtual WAN hub biztonságos virtuális központként működjön, és telepíti a szükséges Azure tűzfal-erőforrásokat.

> [!NOTE]
> Ha az Azure tűzfal egy szabványos virtuális WAN hubon (SKU : Standard): V2V, B2V, V2I és B2I FW szabályzatok csak a virtuális hálózatokból és ágakból származó forgalom az adott hub, ahol az Azure FW telepítve van (Biztonságos hub) csak kényszerítik. Az azonos Virtuális WAN-csomópontokhoz kapcsolódó távoli virtuális hálózatokról és ágakról származó forgalom nem lesz "tűzfallal", még akkor sem, ha a távoli ágak és a virtuális hálózatok virtual WAN hubon keresztül kapcsolódnak a hub-kapcsolatokhoz. A hubok közötti tűzfaltámogatás az Azure Virtual WAN és firewall Manager ütemterven található.

A következő elérési utak az Azure által védett virtuális elosztók használatával engedélyezett kapcsolódási útvonalakat mutatják:

### <a name="path-6"></a>6. elérési út

A 6-os elérési út biztonságos forgalmat jelenít meg a virtuális hálózatok között ugyanazon a területen belül.

A forgalom a következőképpen halad:

- Az ugyanahhoz a biztonságos virtuális elosztóhoz csatlakoztatott virtuális hálózatok most antól az Azure tűzfalon keresztül irányítják a forgalmat.

- Az Azure Firewall szabályzatot alkalmazhat ezekre a folyamatokra.

![6. áramlás](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>7. elérési út

7-es elérési út az Azure virtuális hálózatról az internetre vagy a külső biztonsági szolgáltatásra irányuló forgalmat jeleníti meg.

A forgalom a következőképpen halad:

- A biztonságos virtuális központhoz csatlakoztatott virtuális hálózatok forgalmat küldhetnek a nyilvános, az interneten található célállomásokra, a Biztonságos központ ot használva az internet-hozzáférés központi pontjaként.

- Ez a forgalom helyileg szűrhető az Azure Firewall FQDN-szabályai használatával, vagy elküldhető egy külső biztonsági szolgáltatásnak ellenőrzésre.

![Áramlás 7](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>8. út

A 8-as elérési út a fiókból az internetre vagy a külső biztonsági szolgáltatásra irányuló forgalmat mutatja.

A forgalom a következőképpen halad:

- A biztonságos virtuális központhoz kapcsolódó ágak forgalmat küldhetnek az interneten lévő nyilvános helyekre a Biztonságos központ használata az internet-hozzáférés központi pontjaként.

- Ez a forgalom helyileg szűrhető az Azure Firewall FQDN-szabályai használatával, vagy elküldhető egy külső biztonsági szolgáltatásnak ellenőrzésre.

![Áramlás 8](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>További lépések

További információ az [Azure Virtual WAN-ról](virtual-wan-about.md)
