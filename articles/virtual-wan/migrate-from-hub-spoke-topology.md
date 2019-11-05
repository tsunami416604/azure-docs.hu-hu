---
title: Migrálás az Azure Virtual WAN-ba
description: Ismerje meg, hogyan migrálhat az Azure Virtual WAN-ba.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 584e1c7da70dd7b22969d8d19967fbbe2c52075f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502120"
---
# <a name="migrate-to-azure-virtual-wan"></a>Migrálás az Azure Virtual WAN-ba
Az Azure Virtual WAN lehetővé teszi a vállalatok számára, hogy leegyszerűsítsék globális kapcsolataikat, és kihasználhassa a Microsoft globális hálózatának előnyeit. Ez a tanulmány a vállalatoknak a Microsoft által felügyelt virtuális WAN-hubok kialakítására irányuló, meglévő ügyfél által kezelt sugaras topológiából való átirányítását végző vállalatok technikai részleteit ismerteti.

A [globális átviteli hálózati architektúra és a virtuális WAN-](virtual-wan-global-transit-network-architecture.md) cikk kiemeli, hogy az Azure Virtual WAN milyen előnyöket biztosít a nagyvállalatok számára a felhőalapú, modern vállalati globális hálózatok bevezetéséhez.

![hub és küllő](./media/migrate-from-hub-spoke-topology/figure1.png)
**1. ábra: Azure Virtual WAN**

Az Azure Virtual Datacenter (VDC) sugaras kapcsolati modelljét ügyfeleink 1000S fogadta el az Azure-hálózatkezelés alapértelmezett, tranzitív útválasztási viselkedésének kihasználása egyszerű és méretezhető felhőalapú hálózatok létrehozásához. Az Azure Virtual WAN ezen fogalmakon alapul, és olyan új képességeket vezet be, amelyek lehetővé teszik a globális kapcsolódási topológiák használatát, nem csupán a helyszíni és az Azure-t, hanem lehetővé teszi, hogy az ügyfelek kihasználják a Microsoft-hálózat méretezését meglévő globális hálózatok.

Ez a cikk bemutatja, hogyan telepíthet át egy meglévő hibrid környezetet a virtuális WAN-ra.

## <a name="scenario"></a>Alkalmazási helyzet

A contoso egy globális pénzügyi szervezet, amely az európai és az ázsiai irodákkal is rendelkezik. Azt tervezzük, hogy meglévő alkalmazásaikat a helyszíni tartományvezérlőről az Azure-ba helyezik át, és a VDC architektúrán alapuló Foundation-kialakítást építettek, beleértve a helyi ügyfél által felügyelt hub virtuális hálózatokat a hibrid kapcsolatokhoz. A felhőalapú technológiákba való áttérés részeként a hálózati csapatot felhasználták a kapcsolat biztosítására, hogy az üzleti tevékenység továbbra is optimalizálva legyen.

A 2. ábrán a meglévő globális hálózat magas szintű nézete látható, beleértve a több Azure-régióhoz való kapcsolódást is.

![contoso meglévő hálózati topológia](./media/migrate-from-hub-spoke-topology/figure2.png)
**2. ábra: contoso meglévő hálózati topológiája**

A meglévő hálózati topológia a következő pontokat tudja értelmezni:
 
- Több régióban használt sugaras modell. ExpressRoute prémium szintű áramköröket használ a gyakori privát WAN-hoz való csatlakozáshoz.
- Ezen helyek némelyike közvetlenül az Azure-ba helyezi át a VPN-alagutakat a Microsoft Cloud-ban üzemeltetett alkalmazások eléréséhez.

## <a name="requirements"></a>Követelmények
A hálózati csapat feladata, hogy olyan globális hálózati modellt nyújtson, amely támogatja a contoso felhőbe való áttelepítését, és optimalizálni kell a költségeket, a méretezést és a teljesítményt. Az összefoglalás területen az alábbi követelmények teljesülnek:
- Az üzemeltetett Felhőbeli alkalmazásokhoz optimalizált elérési úttal rendelkező Head Quarter (HQ) és fiókirodák is elérhetők. 
- Szüntesse meg a VPN-megszakítás meglévő helyszíni tartományvezérlőkön (DC) való függőségét, miközben megtartja a következő csatlakozási útvonalakat:
    - **Ág – VNet**: a VPN-kapcsolattal rendelkező irodáknak képesnek kell lenniük a felhőbe áttelepített alkalmazások elérésére a helyi Azure-régióban.
    - **Ág – központ**– központ – VNet: a VPN-kapcsolattal rendelkező irodáknak képesnek kell lenniük a felhőbe áttelepített alkalmazások elérésére a távoli Azure-régióban. 
    - **Ág – ág**: a regionális VPN-kapcsolattal rendelkező irodáknak képesnek kell lenniük az egymással való kommunikációra és a EXPRESSROUTE csatlakoztatott HQ/DC-helyekre. 
    - **Ág – központ –** központ – ág: a globálisan elkülönített VPN-irodáknak képesnek kell lenniük kommunikálni egymással és a EXPRESSROUTE csatlakoztatott HQ-vagy tartományvezérlő-webhelyekkel.
    - **Ág – Internet**: a csatlakoztatott helyeknek képesnek kell lenniük az internettel való kommunikációra, és ezt a forgalmat szűrni és naplózni kell.
    - **VNet – VNet**: az azonos régióban található küllős virtuális hálózatoknak képesnek kell lenniük egymással való kommunikációra.
    - **VNet-csomópontok közötti VNet**: a különböző régiókban található küllős virtuális hálózatoknak képesnek kell lenniük egymással való kommunikációra.
- Lehetővé teszi a contoso barangoló felhasználók (laptop és telefon) számára a vállalati erőforrások elérését, miközben nem a vállalati hálózaton.

## <a name="azure-virtual-wan-architecture"></a>Azure-beli virtuális WAN-architektúra

A 3. ábrán az Azure Virtual WAN használatával, az előző szakaszban részletezett követelmények teljesítése érdekében a frissített célként megadott topológia magas szintű nézete látható.

![contoso virtuális WAN-architektúra](./media/migrate-from-hub-spoke-topology/figure3.png)
**3. ábra: Azure virtuális WAN-architektúra**

Összegezve: 
- Az Európai HQ ExpressRoute továbbra is csatlakoztatva van, a helyszíni TARTOMÁNYVEZÉRLŐk teljes mértékben áttelepültek az Azure-ba, és most már leszerelték.
- Az Asia DC és a HQ továbbra is csatlakoztatva van a privát WAN-hoz. Az Azure Virtual WAN mostantól a helyi szolgáltatói hálózat bővítésére és a globális kapcsolatok biztosítására szolgál. 
- A Nyugat-Európában és Dél-Kelet-Ázsia Azure-régiókban üzembe helyezett Azure-beli virtuális WAN-hubok a ExpressRoute és a VPN-hez csatlakoztatott eszközökhöz biztosítanak csatlakozási központot. 
- A hubok VPN-kapcsolatot biztosítanak a barangoló felhasználók számára több ügyfél-típusból a globális Mesh hálózathoz való OpenVPN-kapcsolat használatával, amely lehetővé teszi, hogy ne csak az Azure-ba migrált alkalmazások, hanem a helyszínen maradó erőforrások is elérhetők legyenek. 
- Internetkapcsolat az Azure Virtual WAN által biztosított virtuális hálózaton belüli erőforrásokhoz. Internetkapcsolat az Azure Virtual WAN által biztosított távoli helyekhez. A helyi Internet breakout a partner-integráción keresztül támogatott a SaaS-szolgáltatásokhoz, például az Office 365-hoz való optimalizált hozzáféréshez.

## <a name="migrate-to-azure-virtual-wan"></a>Migrálás az Azure Virtual WAN-ba

Ez a szakasz az Azure Virtual WAN-ba való Migrálás különböző lépéseit ismerteti.
 
### <a name="vdc-hub-and-spoke-single-region"></a>VDC hub és küllős egyetlen régió

Az alábbi ábra a contoso egyetlen régiós topológiáját mutatja be az Azure Virtual WAN bevezetését megelőzően.

![Virtuális WAN-hubok üzembe helyezése](./media/migrate-from-hub-spoke-topology/figure4.png)

 **4. ábra: VDC hub – küllős egyetlen régió – 1. lépés**

A virtuális adatközpont (VDC) módszerével összhangban az ügyfél által felügyelt hub virtuális hálózat több függvény blokkot tartalmaz:
- Megosztott szolgáltatások (a több küllő által megkövetelt általános függvények) egy példa arra, hogy a contoso milyen módon használja a Windows Server-tartományvezérlőket az infrastruktúra-szolgáltatás (IaaS) virtuális gépek IaaS.
- Az IP-/útválasztási tűzfalakat egy külső gyártótól származó hálózati virtuális készülék biztosítja, amely küllős, 3. rétegbeli IP-útválasztást tesz lehetővé. 
- A bejövő HTTPS-kérelmekre és a virtuális gépeken futó külső proxy szolgáltatásokra, például az internetes erőforrásokhoz való szűrt kimenő hozzáféréshez használt Azure Application Gateway.
- ExpressRoute és VPN Virtual Network átjáró a helyszíni hálózatokhoz való kapcsolódáshoz.

### <a name="deploy-virtual-wan-hubs"></a>Virtuális WAN-hubok üzembe helyezése

Az első lépés magában foglalja egy virtuális WAN-központ üzembe helyezését az egyes régiókban. A következő cikkekben leírtak szerint telepítse a virtuális WAN-hubot VPN Gateway és Express Route Gateway használatával: 
- [Oktatóanyag: helyek közötti kapcsolat létrehozása az Azure Virtual WAN használatával](virtual-wan-site-to-site-portal.md)
- [Oktatóanyag: ExpressRoute-társítás létrehozása az Azure Virtual WAN használatával](virtual-wan-expressroute-portal.md) 

> [!NOTE]
> Az Azure Virtual WAN-nak a standard SKU-t kell használnia a jelen cikkben ismertetett forgalmi útvonalak némelyikének engedélyezéséhez.


Virtuális WAN-hubok üzembe helyezése ![](./media/migrate-from-hub-spoke-topology/figure5.png)
**5. ábra: VDC hub – küllő virtuális WAN-Migrálás – 2. lépés**

### <a name="connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>Távoli helyek (ExpressRoute és VPN) összekötése virtuális WAN-kapcsolattal

Most csatlakoztatjuk a virtuális WAN-hubot a vállalatok ExpressRoute áramkörökhöz, és a helyek közötti VPN-eket az interneten keresztül bármely távoli ágra.

> [!NOTE]
> A virtuális WAN-hubhoz való kapcsolódáshoz a Express Routes-áramköröket prémium SKU-típusra kell frissíteni.


![távoli helyeket csatlakoztathat a virtuális WAN **-hoz](./media/migrate-from-hub-spoke-topology/figure6.png)
6. ábra: VDC hub – küllő virtuális WAN-Migrálás – 3. lépés**

Ezen a ponton a helyszíni hálózati berendezések elkezdik fogadni azokat az útvonalakat, amelyek a virtuális WAN által felügyelt hub-VNet hozzárendelt IP-címtartományt tükrözik. A távoli VPN-kapcsolattal rendelkező ágak ebben a fázisban két elérési utat látnak a küllős virtuális hálózatok meglévő alkalmazásaihoz. Ezeket az eszközöket úgy kell konfigurálni, hogy továbbra is az alagút a VDC-hubhoz való használatára legyenek konfigurálva, így biztosítva a szimmetrikus útválasztást az áttérési fázisban.

### <a name="test-hybrid-connectivity-via-virtual-wan"></a>Hibrid kapcsolatok tesztelése virtuális WAN-kapcsolaton keresztül

A felügyelt virtuális WAN hub éles kapcsolathoz való használata előtt javasoljuk, hogy hozzon létre egy teszt küllős virtuális hálózatot és egy virtuális WAN VNet-kapcsolatot. Ellenőrizze, hogy a tesztkörnyezet kapcsolatai a ExpressRoute és a helyek közötti VPN-kapcsolaton keresztül működnek-e, mielőtt folytatná a következő lépéseket.

![hibrid kapcsolat tesztelése virtuális WAN-on keresztül](./media/migrate-from-hub-spoke-topology/figure7.png)
**7. ábra: VDC hub – küllő virtuális WAN-Migrálás – 4. lépés**

### <a name="transition-connectivity-to-virtual-wan-hub"></a>Áttérési kapcsolat a virtuális WAN-hubhoz


![áttérési kapcsolat a virtuális WAN-hubhoz](./media/migrate-from-hub-spoke-topology/figure8.png)
**8. ábra: VDC hub – küllő a virtuális WAN-Migrálás – 5. lépés**

**a**. Törölje a küllős virtuális hálózatokról a régi VDC-hubhoz való meglévő társ-összekapcsolási kapcsolatokat. A küllős virtuális hálózatokban lévő alkalmazásokhoz való hozzáférés addig nem érhető el, amíg a-c lépések be nem fejeződik.

**b**. Csatlakoztassa a küllős virtuális hálózatokat a virtuális WAN-hubhoz VNet-kapcsolatokon keresztül.

**c**. Távolítson el minden korábban használt felhasználó által megadott útvonalat (UDR) küllős kommunikációhoz a küllős virtuális hálózatokon belül. Ez az elérési út már engedélyezve van a virtuális WAN hub-ban elérhető dinamikus útválasztással.

**d**. A VDC hub meglévő ExpressRoute-és VPN-átjárói már le vannak szerelve az 5. lépés engedélyezéséhez.

**e**. A régi VDC hub (hub Virtual Network) csatlakoztatása a virtuális WAN-hubhoz új VNet-kapcsolaton keresztül.

### <a name="old-hub-becomes-shared-services-spoke"></a>A régi hub a megosztott szolgáltatások részévé válik

Most átalakítottuk az Azure-hálózatot, hogy a virtuális WAN hub a központi pont legyen az új topológiában.

![a régi hub a megosztott szolgáltatások részévé válik](./media/migrate-from-hub-spoke-topology/figure9.png)
**9. ábra: a VDC hub és a küllő virtuális WAN-Migrálás – 6. lépés**

Mivel a virtuális WAN-központ felügyelt entitás, és nem teszi lehetővé az egyéni erőforrások, például a virtuális gépek üzembe helyezését, a megosztott szolgáltatások blokk már létezik küllős virtuális hálózatként, amely az Azure Application Gateway vagy hálózatán keresztül elérhető üzemeltetési funkciókat is tartalmaz. virtualizált berendezés. A megosztott szolgáltatások környezete és a háttérbe tartozó virtuális gépek közötti forgalom mostantól átirányítja a virtuális WAN által felügyelt hubot.

### <a name="optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>A helyszíni kapcsolat optimalizálása a virtuális WAN teljes körű kihasználásához

Ebben a szakaszban a contoso többnyire befejezte az üzleti alkalmazások áttelepítését a Microsoft Cloudba, és csak néhány örökölt alkalmazás van hátra a helyszíni TARTOMÁNYVEZÉRLŐn belül.

![optimalizálja a helyszíni kapcsolatot a virtuális WAN teljes körű kihasználásához](./media/migrate-from-hub-spoke-topology/figure10.png)
**10. ábra: VDC hub – küllő virtuális WAN-Migrálás – 7. lépés**

 Az Azure Virtual WAN teljes funkcionalitásának kihasználásához a contoso úgy dönt, hogy leszereli a régi helyszíni VPN-kapcsolatát. A HQ vagy a DC hálózatok elérését továbbra is elérő ágak a Microsoft globális hálózatát az Azure Virtual WAN beépített átviteli útvonalán keresztül tudják továbbítani. A ExpressRoute Global Reach egy másik lehetőség azon ügyfelek számára, akik a Microsoft gerincét szeretnék kihasználni a meglévő privát WAN-hálózatok kiegészítéseként.

## <a name="end-state-architecture-and-traffic-paths"></a>Befejező állapot architektúrája és a forgalmi útvonalak


![a befejező állapot architektúrája és a forgalom elérési útjai a **11. ábrán](./media/migrate-from-hub-spoke-topology/figure11.png)
: kettős régió virtuális WAN**

Ez a szakasz összefoglalja, hogy a topológia hogyan teljesíti az eredeti követelményeket úgy, hogy megkeres néhány példát a forgalmi folyamatokra.

### <a name="path-1"></a>1\. elérési út

Az 1. elérési út leírja az Asia S2S VPN-ág forgalmát az Azure VNet Dél-Kelet-Ázsia régióban.

A forgalom a következőképpen lesz átirányítva:
- Az ázsiai ág rugalmas S2S BGP-t használó alagutakon keresztül csatlakozik a Dél-Kelet-Ázsia virtuális WAN-hubhoz.
- Az Asia Virtual WAN hub a forgalmat helyileg irányítja a csatlakoztatott VNet.

![1\. folyamat](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>2\. elérési út
Az elérési út a ExpressRoute csatlakoztatott európai HQ és az Azure VNet Dél-Kelet-Ázsia régióban való forgalmát ismerteti.

A forgalom a következőképpen lesz átirányítva:
- Az Európai HQ a standard ExpressRoute áramkörön keresztül csatlakozik a Nyugat-európai Virtual WAN hub-hoz.
- A virtuális WAN-központ és a központ közötti globális kapcsolat lehetővé teszi a forgalom zökkenőmentes átvitelét a távoli régióba csatlakoztatott VNet.

![2\. folyamat](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>3\. elérési út
A Path 3 leírja, hogy az ázsiai helyszíni TARTOMÁNYVEZÉRLŐről érkező forgalom a privát WAN-hoz csatlakozik az Európai S2S csatlakoztatott ágra.

A forgalom a következőképpen lesz átirányítva:
- Az Asia DC a helyi privát WAN-szolgáltatóhoz csatlakozik.
- A ExpressRoute áramkör helyi leállítása a privát WAN-ban a Dél-Kelet-Ázsia virtuális WAN-hubhoz csatlakozik.
- A virtuális WAN-központ és a központ közötti globális kapcsolat lehetővé teszi, hogy zökkenőmentesen átirányítsa a forgalmat az Európai távoli hubhoz.

![3\. folyamat](./media/migrate-from-hub-spoke-topology/flow3.png)


### <a name="path-4"></a>4\. elérési út
Az elérési út 4 a Dél-Kelet-Ázsia régióban található Azure-VNet származó forgalmat mutatja be a Nyugat-európai régióban lévő Azure-VNet.

A forgalom a következőképpen lesz átirányítva:
- A virtuális WAN hub – hub globális kapcsolat lehetővé teszi az összes csatlakoztatott Azure-virtuális hálózatok natív átvitelét további felhasználói konfiguráció nélkül.

![4\. folyamat](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>5\. elérési út
Az 5. elérési út a barangoló VPN-(P2S-) felhasználótól a Nyugat-európai régióban lévő Azure VNet-ra irányuló forgalmat ismerteti.

A forgalom a következőképpen lesz átirányítva:
- A laptop és a telefon felhasználói az OpenVPN-ügyfelet használják a P2S VPN Gateway-hez a Nyugat-Európában.
- A Nyugat-európai Virtual WAN hub a forgalmat helyileg irányítja a csatlakoztatott VNet.

![5\. folyamat](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>Biztonság és házirend-vezérlés Azure Firewallon keresztül

A contoso már ellenőrizte az összes ág és virtuális hálózatok közötti kapcsolatot a jelen dokumentumban korábban tárgyalt követelményekkel összhangban. A biztonság szabályozására és a hálózat elkülönítésére vonatkozó követelmények teljesítése érdekében továbbra is el kell különíteni a forgalmat a hub hálózaton keresztül, és korábban ezt a funkciót egy NVA hajtotta végre. A contoso azt is szeretné, hogy a meglévő proxy szolgáltatásokat leszerelje, és a natív Azure-szolgáltatásokat használja a kimenő internetes szűréshez. 

![biztonság és házirend-vezérlés Azure Firewall](./media/migrate-from-hub-spoke-topology/figure12.png)
**12. ábra: Azure Firewall a virtuális WAN-on (biztonságos virtuális központ)**

A következő magas szintű lépések szükségesek ahhoz, hogy bevezesse Azure Firewallt a virtuális WAN-hubokba, hogy lehetővé váljon egy egységes házirend-vezérlés. Ezt a folyamatot és a biztonságos virtuális hubok koncepcióját [itt](https://go.microsoft.com/fwlink/?linkid=2107683)részletesen ismertetjük.
- Azure Firewall házirend létrehozása.
- Tűzfal-házirend csatolása az Azure Virtual WAN hub szolgáltatáshoz.
A fenti lépés lehetővé teszi, hogy a meglévő virtuális WAN-központ biztonságos virtuális hubhoz működjön, és üzembe helyezi a szükséges Azure Firewall erőforrásokat.

> [!NOTE]
> Ha a Azure Firewall standard szintű virtuális WAN-központban (SKU: standard) van telepítve: a V2V, a B2V, a V2I és a B2I FW-házirendek csak az Azure FW üzembe helyezéséhez (biztonságos hubhoz) csatlakozó adott hubhoz csatlakoztatott virtuális hálózatok és ágakból származó forgalomra érvényesek. A távoli virtuális hálózatok és az ugyanabban a virtuális WAN-beli virtuális WAN-hubhoz csatolt ágakból érkező forgalom nem lesz "tűzfallal elválasztva", annak ellenére, hogy ezek a távoli ágak és vnet a virtuális WAN hub-kapcsolaton keresztül csatlakoznak egymáshoz. A több-központú tűzfalak támogatása az Azure Virtual WAN és a Firewall Manager ütemtervén érhető el.

Az alábbi elérési utak ismertetik az Azure biztonságos virtuális hubok használatával engedélyezett csatlakozási útvonalakat.

### <a name="path-6"></a>6\. elérési út
A 6. elérési út a VNet és a VNet közötti biztonságos tranzit forgalmát ismerteti ugyanazon a régión belül.

A forgalom a következőképpen lesz átirányítva:
- Az ugyanahhoz a biztonságos virtuális hubhoz csatlakoztatott virtuális hálózatok mostantól a Azure Firewall keresztül irányítják a forgalmat.
- A Azure Firewall szabályzatot alkalmazhat ezekre a folyamatokra.

![6\. folyamat](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>7\. elérési út
A 7. elérési út leírja a vnet és az Internet közötti forgalmat, vagy a harmadik féltől származó biztonsági szolgáltatásokat.

A forgalom a következőképpen lesz átirányítva:
- A biztonságos virtuális hubhoz csatlakozó virtuális hálózatok a nyilvános, az internet felé irányuló adatforgalmat a biztonságos központi internet-hozzáférési pont használatával küldhetik el.
- Ez a forgalom a Azure Firewall FQDN-szabályokkal helyileg szűrhető, vagy egy külső biztonsági szolgáltatásba történő elküldése a vizsgálathoz.

![7\. folyamat](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>8\. elérési út
A 8. elérési út a fiókirodából az internetre vagy a harmadik féltől származó biztonsági szolgáltatásra irányuló forgalmat ismerteti.

A forgalom a következőképpen lesz átirányítva:
- A biztonságos virtuális hubhoz csatlakozó ágak az interneten keresztül küldhetnek forgalmat a nyilvános célhelyekre, a biztonságos hubot pedig az Internet-hozzáférés központi pontja használhatja.
- Ez a forgalom a Azure Firewall FQDN-szabályokkal helyileg szűrhető, vagy egy külső biztonsági szolgáltatásba történő elküldése a vizsgálathoz.

![8\. folyamat](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>További lépések
További információ az [Azure Virtual WAN](virtual-wan-about.md) -ról
