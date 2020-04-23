---
title: 'Architektúra: áttelepítés az Azure Virtual WAN-ba'
description: Ismerje meg, hogyan migrálhat az Azure Virtual WAN-ba.
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
# <a name="migrate-to-azure-virtual-wan"></a>Migrálás az Azure Virtual WAN-ba

Az Azure Virtual WAN lehetővé teszi, hogy a vállalatok leegyszerűsítsék a globális kapcsolataikat a Microsoft globális hálózatának kihasználása érdekében. Ez a cikk technikai részleteket tartalmaz azoknak a vállalatoknak, akik egy meglévő ügyfél által felügyelt sugaras topológiából kívánnak áttérni egy olyan kialakításra, amely a Microsoft által felügyelt virtuális WAN-hubokat használja.

További információ arról, hogy az Azure Virtual WAN milyen előnyökkel jár a felhő-központú modern vállalati globális hálózatokat befogadó vállalatok számára: [globális átviteli hálózati architektúra és virtuális WAN](virtual-wan-global-transit-network-architecture.md).

![sugaras és küllős](./media/migrate-from-hub-spoke-topology/hub-spoke.png)
**ábra: Azure Virtual WAN**

Az Azure Virtual Datacenter (VDC) sugaras kapcsolati modelljét több ezer ügyfelünk fogadta el az Azure-hálózatkezelés alapértelmezett tranzitív útválasztási viselkedésének kihasználása érdekében egyszerű és méretezhető felhőalapú hálózatok létrehozásához. Az Azure Virtual WAN ezen fogalmakon alapul, és olyan új képességeket vezet be, amelyek lehetővé teszik a globális kapcsolódási topológiák használatát, nem csupán a helyszíni és az Azure-t, hanem lehetővé teszi, hogy az ügyfelek kihasználják a Microsoft-hálózat méretezését a meglévő globális hálózatok kibővítéséhez.

Ez a cikk bemutatja, hogyan telepíthet át egy meglévő hibrid környezetet a virtuális WAN-ra.

## <a name="scenario"></a>Forgatókönyv

A contoso egy globális pénzügyi szervezet, amely az európai és az ázsiai irodákkal is rendelkezik. Azt tervezzük, hogy meglévő alkalmazásaikat a helyszíni adatközpontból az Azure-ba helyezik át, és a VDC architektúrán alapuló Foundation-kialakítást alakítottunk ki, beleértve a regionális ügyfél által felügyelt hub virtuális hálózatokat a hibrid kapcsolatokhoz. A felhőalapú technológiákba való áttérés részeként a hálózati csapat feladata annak biztosítása, hogy a kapcsolat a vállalat felé irányuló továbbításra legyen optimalizálva.

Az alábbi ábra a meglévő globális hálózat magas szintű nézetét mutatja be, beleértve a több Azure-régióhoz való kapcsolódást.

![Contoso meglévő hálózati topológiai](./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png)
**ábra: contoso meglévő hálózati topológia**

A meglévő hálózati topológia a következő pontokat tudja értelmezni:

- A sugaras topológia több régióban is használatos, beleértve a ExpressRoute prémium szintű áramköröket is, amelyekkel egy gyakori privát WAN-kapcsolaton keresztül csatlakozhatnak.

- Ezen helyek némelyike közvetlenül az Azure-ba helyezi át a VPN-alagutakat a Microsoft Cloud-ban üzemeltetett alkalmazások eléréséhez.

## <a name="requirements"></a>Követelmények

A hálózati csapat feladata, hogy olyan globális hálózati modellt nyújtson, amely támogatja a contoso felhőbe való áttelepítését, és optimalizálni kell a költségeket, a méretezést és a teljesítményt. Az összefoglalás területen az alábbi követelmények teljesülnek:

- Az üzemeltetett Felhőbeli alkalmazásokhoz optimalizált elérési úttal rendelkező Head Quarter (HQ) és fiókirodák is elérhetők.
- Szüntesse meg a VPN-leállításhoz szükséges meglévő helyszíni adatközpontok (DC) függőségét, miközben megtartja a következő csatlakozási útvonalakat:
  - **Ág – VNet**: a VPN-kapcsolattal rendelkező irodáknak képesnek kell lenniük a felhőbe áttelepített alkalmazások elérésére a helyi Azure-régióban.
  - **Ág – központ**– központ – VNet: a VPN-kapcsolattal rendelkező irodáknak képesnek kell lenniük a felhőbe áttelepített alkalmazások elérésére a távoli Azure-régióban.
  - **Ág – ág**: a regionális VPN-kapcsolattal rendelkező irodáknak képesnek kell lenniük az egymással való kommunikációra és a EXPRESSROUTE csatlakoztatott HQ/DC-helyekre.
  - **Ág – központ –** központ – ág: a globálisan elkülönített VPN-irodáknak képesnek kell lenniük kommunikálni egymással és a EXPRESSROUTE csatlakoztatott HQ-vagy tartományvezérlő-webhelyekkel.
  - **Ág – Internet**: a csatlakoztatott helyeknek képesnek kell lenniük az internettel való kommunikációra. Ezt a forgalmat szűrni és naplózni kell.
  - **VNet – VNet**: az azonos régióban található küllős virtuális hálózatoknak képesnek kell lenniük egymással való kommunikációra.
  - **VNet-csomópontok közötti VNet**: a különböző régiókban található küllős virtuális hálózatoknak képesnek kell lenniük egymással való kommunikációra.
- Lehetővé teszi a contoso barangoló felhasználói (laptop és telefon) számára a vállalati erőforrások elérését, miközben a vállalati hálózaton nem.

## <a name="azure-virtual-wan-architecture"></a><a name="architecture"></a>Azure-beli virtuális WAN-architektúra

Az alábbi ábra az Azure Virtual WAN használatával az előző szakaszban részletezett követelmények teljesítése érdekében a frissített célként megadott topológia magas szintű nézetét jeleníti meg.

![A contoso virtuális WAN](./media/migrate-from-hub-spoke-topology/vwan-architecture.png)
architektúrájának**ábrája: Azure virtuális WAN-architektúra**

Összegzés:

- Az Európai HQ ExpressRoute továbbra is csatlakoztatva van, a helyszíni TARTOMÁNYVEZÉRLŐk teljes mértékben áttelepültek az Azure-ba, és most már leszerelték.
- Az Asia DC és a HQ továbbra is csatlakoztatva van a privát WAN-hoz. Az Azure Virtual WAN mostantól a helyi Carrier hálózat bővítésére és a globális kapcsolatok biztosítására szolgál.
- A Nyugat-Európában és Dél-Kelet-Ázsia Azure-régiókban üzembe helyezett Azure-beli virtuális WAN-hubok a ExpressRoute és a VPN-hez csatlakoztatott eszközökhöz biztosítanak csatlakozási központot.
- A hubok VPN-lezárást is biztosítanak a barangoló felhasználók számára több ügyfélen keresztül, a globális Mesh hálózattal való OpenVPN-kapcsolat használatával, amely lehetővé teszi, hogy ne csak az Azure-ba migrált alkalmazások, hanem a helyszínen maradó erőforrások is elérhetők legyenek.
- Internetkapcsolat az Azure Virtual WAN által biztosított virtuális hálózaton belüli erőforrásokhoz.

Az Azure Virtual WAN által is biztosított távoli helyek internetkapcsolata. A helyi Internet breakout a partner-integráción keresztül támogatott a SaaS-szolgáltatásokhoz, például az Office 365-hoz való optimalizált hozzáféréshez.

## <a name="migrate-to-virtual-wan"></a>Migrálás virtuális WAN-ba

Ez a szakasz az Azure Virtual WAN-ba való Migrálás különböző lépéseit mutatja be.

### <a name="step-1-vdc-hub-and-spoke-single-region"></a>1. lépés: a VDC hub és küllős egyetlen régió

Tekintse át az architektúrát. Az alábbi ábra a contoso egyetlen régiós topológiáját mutatja be az Azure Virtual WAN bevezetése előtt:

![Egyrégiós topológia](./media/migrate-from-hub-spoke-topology/figure1.png)
**1. ábra: VDC hub és küllős egyetlen régió**

A virtuális adatközpont (VDC) megközelítésének megfelelően az ügyfél által felügyelt hub virtuális hálózat több függvény blokkot tartalmaz:

- Megosztott szolgáltatások (a több küllő által igényelt általános függvények). Példa: a contoso a Windows Server tartományvezérlőket használja az infrastruktúra-szolgáltatás (IaaS) virtuális gépeken.
- Az IP-/útválasztási tűzfalakat egy külső gyártótól származó hálózati virtuális készülék biztosítja, amely küllős, 3. rétegbeli IP-útválasztást tesz lehetővé.
- A bejövő HTTPS-kérelmekre és a virtuális gépeken futó külső proxy szolgáltatásokra, például az internetes erőforrásokhoz való szűrt kimenő hozzáféréshez használt Azure Application Gateway.
- ExpressRoute és VPN virtuális hálózati átjáró a helyszíni hálózatokhoz való kapcsolódáshoz.

### <a name="step-2-deploy-virtual-wan-hubs"></a>2. lépés: virtuális WAN-hubok üzembe helyezése

Helyezzen üzembe egy virtuális WAN-hubot minden régióban. Állítsa be a virtuális WAN-hubot VPN Gateway és ExpressRoute átjáróval a következő cikkekben leírtak szerint:

- [Oktatóanyag: Helyek közötti kapcsolat létrehozása az Azure Virtual WAN használatával](virtual-wan-site-to-site-portal.md)
- [Oktatóanyag: ExpressRoute-társítás létrehozása az Azure Virtual WAN használatával](virtual-wan-expressroute-portal.md)

> [!NOTE]
> Az Azure Virtual WAN-nak a standard SKU-t kell használnia ahhoz, hogy a jelen cikkben szereplő forgalmi útvonalak némelyikét engedélyezzék.

![Virtuális WAN-hubok](./media/migrate-from-hub-spoke-topology/figure2.png)
üzembe helyezése**2. ábra: VDC hub – küllő virtuális WAN-Migrálás**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>3. lépés: távoli helyek (ExpressRoute és VPN) összekötése virtuális WAN-kapcsolattal

Kapcsolja össze a virtuális WAN-hubot a meglévő ExpressRoute-áramkörökkel, és állítsa be a helyek közötti VPN-eket az interneten keresztül bármely távoli ág számára.

> [!NOTE]
> A virtuális WAN-hubhoz való kapcsolódáshoz a Express Routes-áramköröket prémium SKU-típusra kell frissíteni.

![Távoli helyek összekötése a](./media/migrate-from-hub-spoke-topology/figure3.png)
virtuális WAN **-ábrával 3. ábra: a VDC hub és a küllő virtuális WAN-Migrálás**

Ezen a ponton a helyszíni hálózati berendezések elkezdik fogadni a virtuális WAN által felügyelt hub-VNet hozzárendelt IP-címtartomány alapján érkező útvonalakat. A távoli VPN-kapcsolattal rendelkező ágak ebben a fázisban két elérési utat látnak a küllős virtuális hálózatok meglévő alkalmazásaihoz. Ezeket az eszközöket úgy kell konfigurálni, hogy továbbra is az alagút a VDC-hubhoz való használatára legyenek konfigurálva, így biztosítva a szimmetrikus útválasztást az áttérési fázisban.

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>4. lépés: a hibrid kapcsolatok tesztelése virtuális WAN-kapcsolaton keresztül

A felügyelt virtuális WAN hub éles kapcsolathoz való használata előtt javasoljuk, hogy állítson be egy teszt küllős virtuális hálózatot és egy virtuális WAN VNet-kapcsolatot. Ellenőrizze, hogy a tesztkörnyezet kapcsolatai a ExpressRoute és a helyek közötti VPN-kapcsolaton keresztül működnek-e, mielőtt folytatná a következő lépéseket.

![Hibrid kapcsolat tesztelése a virtuális WAN](./media/migrate-from-hub-spoke-topology/figure4.png)
 **-ábrán keresztül 4: VDC hub – küllős virtuális WAN-Migrálás**

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>5. lépés: a virtuális WAN-hubhoz való áttérési kapcsolat

![Áttérési kapcsolat a virtuális WAN](./media/migrate-from-hub-spoke-topology/figure5.png)
 **-hubhoz 5. ábra: a VDC hub és a küllő virtuális WAN-Migrálás**

**a**. Törölje a küllős virtuális hálózatokról a régi VDC-hubhoz való meglévő társ-összekapcsolási kapcsolatokat. A küllős virtuális hálózatokban lévő alkalmazásokhoz való hozzáférés addig nem érhető el, amíg a-c lépések be nem fejeződik.

**b**. Csatlakoztassa a küllős virtuális hálózatokat a virtuális WAN-hubhoz VNet-kapcsolatokon keresztül.

**c**. Távolítson el minden korábban használt felhasználó által megadott útvonalat (UDR) küllős kommunikációhoz a küllős virtuális hálózatokon belül. Ez az elérési út már engedélyezve van a virtuális WAN hub-ban elérhető dinamikus útválasztással.

**d**. A VDC hub meglévő ExpressRoute és VPN-átjárói már le vannak szerelve, hogy engedélyezzék a következő lépést (e).

**e**. A régi VDC hub (hub Virtual Network) csatlakoztatása a virtuális WAN-hubhoz új VNet-kapcsolaton keresztül.

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>6. lépés: a régi hub lesz a megosztott szolgáltatások küllő

Most átalakítottuk az Azure-hálózatot, hogy a virtuális WAN hub a központi pont legyen az új topológiában.

![A régi hub a megosztott szolgáltatások](./media/migrate-from-hub-spoke-topology/figure6.png)
részévé válik **, 6. ábra: a VDC hub és a küllő virtuális WAN-Migrálás**

Mivel a virtuális WAN-központ felügyelt entitás, és nem teszi lehetővé az egyéni erőforrások, például a virtuális gépek üzembe helyezését, a megosztott szolgáltatások blokk már létezik küllős virtuális hálózatként, és a gazdagépek olyan funkciói, mint például az Azure Application Gateway vagy a hálózati virtualizált készülék. A megosztott szolgáltatások környezete és a háttérbe tartozó virtuális gépek közötti forgalom mostantól átirányítja a virtuális WAN által felügyelt hubot.

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>7. lépés: a helyszíni kapcsolat optimalizálása a virtuális WAN teljes körű kihasználásához

Ebben a szakaszban a contoso többnyire befejezte az üzleti alkalmazások áttelepítését a Microsoft Cloudba, és csak néhány örökölt alkalmazás van hátra a helyszíni TARTOMÁNYVEZÉRLŐn belül.

![A helyszíni kapcsolat optimalizálása a virtuális WAN](./media/migrate-from-hub-spoke-topology/figure7.png)
teljes körű kihasználása érdekében**7. ábra: VDC hub – küllős virtuális WAN-Migrálás**

Az Azure Virtual WAN teljes funkcionalitásának kihasználásához a contoso úgy dönt, hogy leszereli a régi helyszíni VPN-kapcsolatait. A HQ vagy a DC hálózatok elérését továbbra is elérő ágak a Microsoft globális hálózatát az Azure Virtual WAN beépített átviteli útvonalán keresztül tudják továbbítani.

> [!NOTE]
> A ExpressRoute Global Reach egy másik lehetőség azon ügyfelek számára, akik a Microsoft gerincét szeretnék kihasználni a meglévő privát WAN-hálózatok kiegészítéseként.

## <a name="end-state-architecture-and-traffic-paths"></a>Állapot-architektúra és forgalmi útvonalak

![Az állapot architektúrájának és forgalmának](./media/migrate-from-hub-spoke-topology/figure8.png)
elérési útjai az**alábbi ábra: kettős régió virtuális WAN**

Ez a szakasz összefoglalja, hogy a topológia hogyan teljesíti az eredeti követelményeket úgy, hogy megkeres néhány példát a forgalmi folyamatokra.

### <a name="path-1"></a>1. elérési út

Az 1. elérési út azt mutatja be, hogy egy S2S VPN-kapcsolattal rendelkező ág forgalma egy Dél-Kelet-Ázsia régióban található Azure-VNet.

A forgalom a következőképpen lesz átirányítva:

- Az ázsiai ág rugalmas S2S BGP-t használó alagutakon keresztül csatlakozik a Dél-Kelet-Ázsia virtuális WAN-hubhoz.

- Az Asia Virtual WAN hub a forgalmat helyileg irányítja a csatlakoztatott VNet.

![1. folyamat](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>2. elérési út

A 2. elérési út a ExpressRoute csatlakoztatott európai HQ és a Dél-Kelet-Ázsia régióban található Azure-VNet közötti forgalmat mutatja.

A forgalom a következőképpen lesz átirányítva:

- Az Európai HQ a prémium szintű ExpressRoute áramkörön keresztül csatlakozik a Nyugat-európai Virtual WAN hub-hoz.

- A virtuális WAN hub – hub globális kapcsolat lehetővé teszi a forgalom átvitelét a távoli régióba csatlakoztatott VNet.

![2. folyamat](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>3. elérési út

A 3. elérési út azt mutatja, hogy az ázsiai helyszíni TARTOMÁNYVEZÉRLŐről érkező forgalom a belső WAN-hoz csatlakozik egy európai S2S csatlakoztatott ágra.

A forgalom a következőképpen lesz átirányítva:

- Az Asia DC a helyi privát WAN-szolgáltatóhoz csatlakozik.

- A ExpressRoute áramkör helyi leállítása a magánhálózati WAN-kapcsolattal a Dél-Kelet-Ázsia virtuális WAN-hubhoz csatlakozik.

- A virtuális WAN hub – hub globális kapcsolat lehetővé teszi a forgalom átvitelét.

![3. folyamat](./media/migrate-from-hub-spoke-topology/flow3.png)

### <a name="path-4"></a>4. elérési út

A 4. elérési út a Dél-Kelet-Ázsia régióban található Azure-VNet származó forgalmat mutatja be a Nyugat-európai régióban lévő Azure-VNet.

A forgalom a következőképpen lesz átirányítva:

- A virtuális WAN hub – hub globális kapcsolat lehetővé teszi az összes csatlakoztatott Azure-virtuális hálózatok natív átvitelét további felhasználói konfiguráció nélkül.

![4. folyamat](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>5. elérési út

Az 5. elérési út a barangoló VPN (P2S) felhasználóinak a Nyugat-európai régióban lévő Azure-VNet való forgalmát mutatja.

A forgalom a következőképpen lesz átirányítva:

- A laptop-és mobileszköz-felhasználók az OpenVPN-ügyféllel transzparens módon csatlakozhatnak a P2S VPN-átjáróhoz Nyugat-Európában.

- A Nyugat-európai Virtual WAN hub a forgalmat helyileg irányítja a csatlakoztatott VNet.

![5. folyamat](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>Biztonság és házirend-vezérlés Azure Firewallon keresztül

A contoso már ellenőrizte az összes ág és virtuális hálózatok közötti kapcsolatot a jelen cikk korábbi részében ismertetett követelményekkel összhangban. A biztonság szabályozására és a hálózat elkülönítésére vonatkozó követelmények teljesítése érdekében továbbra is el kell különíteni a forgalmat a hub-hálózaton keresztül. Ezt a funkciót korábban egy hálózati virtuális berendezés (NVA) hajtotta végre. A contoso azt is szeretné, hogy a meglévő proxy szolgáltatásokat leszerelje, és natív Azure-szolgáltatásokat használ a kimenő internetes szűréshez.

![Biztonság és házirend-vezérlés Azure Firewall](./media/migrate-from-hub-spoke-topology/security-policy.png)
**ábrán keresztül: Azure Firewall a virtuális WAN-ban (biztonságos virtuális központ)**

A következő magas szintű lépések szükségesek ahhoz, hogy bevezesse Azure Firewallt a virtuális WAN-hubokba, hogy lehetővé váljon egy egységes házirend-vezérlés. További információ erről a folyamatról és a biztonságos virtuális hubok fogalmáról: [Azure Firewall Manager](../firewall-manager/index.yml).

1. Azure Firewall házirend létrehozása.
2. Tűzfal-házirend csatolása az Azure Virtual WAN hub szolgáltatáshoz. Ez a lépés lehetővé teszi, hogy a meglévő virtuális WAN-központ biztonságos virtuális hubhoz működjön, és üzembe helyezi a szükséges Azure Firewall erőforrásokat.

> [!NOTE]
> Ha a Azure Firewall standard szintű virtuális WAN-központban (SKU: standard) van telepítve: a V2V, a B2V, a V2I és a B2I FW-házirendek csak az Azure FW üzembe helyezéséhez (biztonságos hubhoz) csatlakozó adott hubhoz csatlakoztatott virtuális hálózatok és ágakból származó forgalomra érvényesek. A távoli virtuális hálózatok és az azonos virtuális WAN-beli virtuális WAN-központokhoz csatolt ágakból származó forgalom nem lesz "tűzfallal elválasztva", még akkor is, ha a távoli ágak és a VNet a virtuális WAN hub-kapcsolaton keresztül kapcsolódnak egymáshoz. A több-hubhoz kiterjedő tűzfalak támogatása az Azure Virtual WAN és a Firewall Manager ütemtervén érhető el.

Az alábbi elérési utak az Azure biztonságos virtuális hubok használatával elérhető csatlakozási útvonalakat mutatják:

### <a name="path-6"></a>6. elérési út

A 6. elérési út a virtuális hálózatok közötti biztonságos forgalmat mutatja ugyanazon a régión belül.

A forgalom a következőképpen lesz átirányítva:

- Az ugyanahhoz a biztonságos virtuális hubhoz csatlakoztatott virtuális hálózatok mostantól a Azure Firewall keresztül irányítják a forgalmat.

- A Azure Firewall szabályzatot alkalmazhat ezekre a folyamatokra.

![6. folyamat](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>7. elérési út

Az elérési út 7 az Azure-VNet az internetre vagy a harmadik féltől származó biztonsági szolgáltatásra irányuló forgalmat mutatja.

A forgalom a következőképpen lesz átirányítva:

- A biztonságos virtuális hubhoz csatlakozó virtuális hálózatok a nyilvános, az internet felé irányuló adatforgalmat a biztonságos központi internet-hozzáférési pont használatával küldhetik el.

- Ez a forgalom a Azure Firewall FQDN-szabályokkal helyileg szűrhető, vagy egy külső biztonsági szolgáltatásba való elküldése a vizsgálathoz.

![7. folyamat](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>8. elérési út

A 8. elérési út a fiókirodából az internetre vagy a harmadik féltől származó biztonsági szolgáltatásra irányuló forgalmat mutatja.

A forgalom a következőképpen lesz átirányítva:

- A biztonságos virtuális hubhoz csatlakozó ágak a biztonságos központi internet-hozzáférési pont használatával küldhetnek forgalmat a nyilvános célhelyekre az interneten.

- Ez a forgalom a Azure Firewall FQDN-szabályokkal helyileg szűrhető, vagy egy külső biztonsági szolgáltatásba való elküldése a vizsgálathoz.

![8. folyamat](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>További lépések

További információ az [Azure Virtual WAN](virtual-wan-about.md) -ról
