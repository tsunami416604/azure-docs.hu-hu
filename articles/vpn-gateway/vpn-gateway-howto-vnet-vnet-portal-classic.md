---
title: 'Hozzon létre egy kapcsolatot a virtuális hálózatok: Classic: Azure Portal között'
description: A PowerShell és a Azure Portal együttes használatával összekapcsolhatja az Azure-beli virtuális hálózatokat.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/12/2020
ms.author: cherylmc
ms.openlocfilehash: 63c6329ad62289cd127902c1438073b28fc8683e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77201849"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>VNet-VNet közötti kapcsolatok konfigurálása (klasszikus)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Ebből a cikkből megtudhatja, hogyan hozhat létre VPN Gateway-kapcsolatot a virtuális hálózatok között. A virtuális hálózatok lehetnek azonos vagy eltérő régiókban, illetve azonos vagy eltérő előfizetésekben. A cikkben ismertetett lépések a klasszikus üzemi modellre és a Azure Portal vonatkoznak. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Különböző üzemi modellek összekapcsolása – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Különböző üzemi modellek összekapcsolása – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![VNet a VNet kapcsolati diagramhoz](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)



## <a name="about-vnet-to-vnet-connections"></a>Tudnivalók a virtuális hálózatok közötti kapcsolatokról

A virtuális hálózat egy másik virtuális hálózathoz (VNet – VNet) való csatlakoztatása a klasszikus üzemi modellben a VPN Gateway használatával hasonló a virtuális hálózat helyszíni helyhez való csatlakoztatásához. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához.

A csatlakoztatott virtuális hálózatok különböző előfizetésekben és különböző régiókban lehet. A VNet kombinálva VNet a többhelyes konfigurációkkal való kommunikációt. Így létrehozhat olyan hálózati topológiákat, amelyek a létesítmények közötti kapcsolatokat a virtuális hálózatok közötti kapcsolatokkal kombinálják.

![VNet a VNet-kapcsolatokhoz](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why-connect-virtual-networks"></a><a name="why"></a>Miért érdemes összekapcsolni a virtuális hálózatokat?

A virtuális hálózatokat a következő okokból érdemes összekapcsolni:

* **Georedundancia és földrajzi jelenlét több régióban**

  * Beállíthatja a saját georeplikációját vagy szinkronizálását biztonságos kapcsolaton át, internetes végpontok használata nélkül.
  * A Azure Load Balancer és a Microsoft vagy harmadik féltől származó fürtözési technológiával több Azure-régióban is beállíthatja a kiválóan elérhető számítási feladatokat. Például beállíthat folyamatosan működő SQL-t több Azure-régióban található rendelkezésre állási csoportokkal.
* **Regionális többrétegű alkalmazások erős elkülönítési határral**

  * Ugyanazon a régión belül több virtuális hálózatok csatlakoztatott többrétegű alkalmazásokat is beállíthat, amelyek erős elkülönítéssel és a rétegek közötti kommunikációval rendelkeznek.
* **Több előfizetés, szervezeten belüli kommunikáció az Azure-ban**

  * Ha több Azure-előfizetéssel is rendelkezik, a különböző előfizetésekhez tartozó számítási feladatokat a virtuális hálózatok között biztonságosan összekapcsolhatja.
  * Vállalatok vagy szolgáltatók számára engedélyezheti a szervezeten belüli kommunikációt a biztonságos VPN-technológiával az Azure-ban.

A virtuális hálózatok közötti kapcsolatokról további információt a cikk végén, [A virtuális hálózatok közötti kapcsolatokra vonatkozó szempontok](#faq) című részben talál.

### <a name="working-with-azure-powershell"></a><a name="powershell"></a>A Azure PowerShell használata

A legtöbb lépésben a portált használjuk, de a PowerShell használatával kell létrehoznia a virtuális hálózatok közötti kapcsolatokat. A kapcsolatok nem hozhatók létre a Azure Portal használatával. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="step-1---plan-your-ip-address-ranges"></a><a name="plan"></a>1. lépés – Az IP-címtartományok megtervezése

Fontos, hogy döntse el, milyen tartományokat fog használni a virtuális hálózatok konfigurálásához. Ehhez a konfigurációhoz meg kell győződnie arról, hogy egyik VNet tartomány sem fedi egymást egymással, sem pedig azokkal a helyi hálózatokkal, amelyekhez csatlakoznak.

Az alábbi táblázat a virtuális hálózatok definiálására mutat példát. A tartományokat csak iránymutatásként használja. Jegyezze fel a virtuális hálózatok tartományait. Ezekre az információkra szüksége lesz a későbbi lépésekhez.

**Például**

| Virtual Network | Címtartomány | Régió | Kapcsolódás helyi hálózati helyhez |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |USA keleti régiója |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA nyugati régiója |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="step-2---create-the-virtual-networks"></a><a name="vnetvalues"></a>2. lépés – a virtuális hálózatok létrehozása

Hozzon létre két virtuális hálózatot a [Azure Portal](https://portal.azure.com). A klasszikus virtuális hálózatok létrehozásához szükséges lépéseket lásd: [klasszikus virtuális hálózat létrehozása](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). 

Ha a portálon klasszikus virtuális hálózatot hoz létre, a következő lépésekkel kell megnyitnia a virtuális hálózat lapot, ellenkező esetben a klasszikus virtuális hálózat létrehozásának lehetősége nem jelenik meg:

1. A "+" elemre kattintva nyissa meg az "új" lapot.
2. A "keresés a piactéren" mezőbe írja be a következőt: "Virtual Network". Ha ehelyett a hálózatkezelés-> Virtual Network lehetőséget választja, nem fogja tudni klasszikus VNet létrehozni.
3. Keresse meg a "Virtual Network" elemet a visszaadott listából, és kattintson rá a Virtual Network lap megnyitásához. 
4. Klasszikus VNet létrehozásához a virtuális hálózat lapon válassza a klasszikus lehetőséget. 

Ha gyakorlatként használja ezt a cikket, a következő példa értékeket használhatja:

**TestVNet1 értékei**

Név: TestVNet1<br>
Címterület: 10.11.0.0/16, 10.12.0.0/16 (nem kötelező)<br>
Alhálózat neve: default<br>
Alhálózati címtartomány: 10.11.0.1/24<br>
Erőforráscsoport: ClassicRG<br>
Hely: East US<br>
GatewaySubnet: 10.11.1.0/27

**TestVNet4 értékei**

Név: TestVNet4<br>
Címterület: 10.41.0.0/16, 10.42.0.0/16 (nem kötelező)<br>
Alhálózat neve: default<br>
Alhálózati címtartomány: 10.41.0.1/24<br>
Erőforráscsoport: ClassicRG<br>
Hely: West US<br>
GatewaySubnet: 10.41.1.0/27

**A virtuális hálózatok létrehozásakor vegye figyelembe az alábbi beállításokat:**

* **Virtual Network címterület** – a Virtual Network címterület lapon adja meg a virtuális hálózathoz használni kívánt címtartományt. Ezek a dinamikus IP-címek lesznek hozzárendelve a virtuális gépekhez és más, a virtuális hálózaton üzembe helyezett szerepkörökhöz.<br>A kiválasztott címterület nem fedik át a többi virtuális hálózatok vagy a helyszíni helyek számára, amelyhez ez a VNet csatlakozni fog.

* **Hely** – a virtuális hálózat létrehozásakor társítsa azt egy Azure-beli helyhez (régióhoz). Ha például azt szeretné, hogy a virtuális hálózaton üzembe helyezett virtuális gépek fizikailag az USA nyugati régiójában legyenek, válassza ki ezt a helyet. A virtuális hálózathoz társított hely nem módosítható a létrehozása után.

**A virtuális hálózatok létrehozása után a következő beállításokat adhatja hozzá:**

* **Címterület** – ehhez a konfigurációhoz nem szükséges további címtartomány, de a VNet létrehozása után további címtartományt is hozzáadhat.

* **Alhálózatok** – további alhálózatok nem szükségesek ehhez a konfigurációhoz, de előfordulhat, hogy a virtuális gépeket egy olyan alhálózaton szeretné használni, amely elkülönül a többi szerepkör-példánytól.

* **DNS-kiszolgálók** – adja meg a DNS-kiszolgáló nevét és IP-címét. A beállítás nem hoz létre DNS-kiszolgálót. Lehetővé teszi, hogy megadja azokat a DNS-kiszolgálókat, amelyeket névfeloldásra kíván használni ennél a virtuális hálózatnál.

Ebben a szakaszban a kapcsolat típusát, a helyi helyet és az átjárót hozza létre.

## <a name="step-3---configure-the-local-site"></a><a name="localsite"></a>3. lépés – a helyi hely konfigurálása

Az Azure az egyes helyi hálózati helyekben megadott beállítások alapján határozza meg, hogyan irányítsa át a forgalmat a virtuális hálózatok között. Minden VNet arra a helyi hálózatra kell mutatnia, amelyhez át szeretné irányítani a forgalmat. Meg kell határoznia, hogy melyik nevet szeretné használni az egyes helyi hálózati helyekre való hivatkozáshoz. A legjobb megoldás, ha valami leírót használ.

Például a TestVNet1 egy "VNet4Local" nevű helyi hálózati webhelyhez csatlakozik. A VNet4Local beállításai tartalmazzák a TestVNet4 tartozó címek előtagjait.

Az egyes VNet helyi helye a másik VNet. A következő példában szereplő értékek használatosak a konfigurációhoz:

| Virtual Network | Címtartomány | Régió | Kapcsolódás helyi hálózati helyhez |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |USA keleti régiója |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA nyugati régiója |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Keresse meg a TestVNet1 a Azure Portalban. A lap **VPN-kapcsolatok** szakaszában kattintson az **átjáró**elemre.

    ![Nincs átjáró](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. Az **új VPN-kapcsolat** lapon válassza a **helyek közötti**lehetőséget.
3. A helyi hely lap megnyitásához és a beállítások konfigurálásához kattintson a **helyi hely** elemre.
4. A **helyi hely** lapon nevezze el a helyi helyet. A példánkban a "VNet4Local" helyi helyet nevezjük.
5. A **VPN-átjáró IP-címe**esetén bármilyen kívánt IP-címet használhat, amennyiben az formátuma érvényes. A VPN-eszköz esetében általában a tényleges külső IP-címet kell használnia. A klasszikus VNet-VNet konfiguráció esetében azonban a VNet átjáróhoz rendelt nyilvános IP-címet kell használnia. Mivel még nem hozta létre a virtuális hálózati átjárót, minden érvényes nyilvános IP-címet helyőrzőként kell megadnia.<br>Ne hagyja ezt üresen – ez a konfiguráció nem választható. Egy későbbi lépésben visszatérhet ezekhez a beállításokhoz, és konfigurálja azokat a megfelelő virtuális hálózati átjáró IP-címeivel, amint az Azure létrehozza azt.
6. Az **ügyfél címterület területéhez**használja a másik VNet címterület területét. Tekintse meg a tervezési példát. Kattintson az **OK** gombra a beállítások mentéséhez, és térjen vissza az **új VPN-kapcsolat** lapra.

    ![helyi webhely](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="step-4---create-the-virtual-network-gateway"></a><a name="gw"></a>4. lépés – a virtuális hálózati átjáró létrehozása

Minden virtuális hálózatnak rendelkeznie kell egy virtuális hálózati átjáróval. A virtuális hálózati átjáró irányítja és titkosítja a forgalmat.

1. Az **Új VPN-kapcsolat** lapon jelölje be az **Átjáró azonnali létrehozása** jelölőnégyzetet.
2. Kattintson **az alhálózat, méret és útválasztási típus**elemre. Az **átjáró konfigurációja** lapon kattintson az **alhálózat**elemre.
3. Az átjáró alhálózatának neve automatikusan kitöltődik a kötelező "GatewaySubnet" névvel. A **címtartomány** a VPN Gateway szolgáltatásokhoz lefoglalt IP-címeket tartalmazza. Egyes konfigurációk lehetővé teszik a/29 átjáró-alhálózatok használatát, de a/28 vagy/27 az a legjobb megoldás, ha olyan jövőbeli konfigurációkat szeretne használni, amelyek további IP-címeket igényelhetnek az átjáró szolgáltatásokhoz. A példánkban szereplő beállításokban a 10.11.1.0/27-et használjuk. Állítsa be a Címterület méretét, majd kattintson **az OK**gombra.
4. Adja meg az **átjáró méretét**. Ez a beállítás az [ÁTJÁRÓ SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)-ra hivatkozik.
5. Konfigurálja az **útválasztási típust**. A konfiguráció útválasztási típusának **dinamikusnak**kell lennie. Az útválasztási típust később nem módosíthatja, hacsak nem szakítja meg az átjárót, és nem hoz létre újat.
6. Kattintson az **OK** gombra.
7. Az **új VPN-kapcsolat** lapon kattintson az **OK** gombra a virtuális hálózati átjáró létrehozásának megkezdéséhez. Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet a választott átjáró-termékváltozattól függően.

## <a name="step-5---configure-testvnet4-settings"></a><a name="vnet4settings"></a>5. lépés – a TestVNet4 beállításainak konfigurálása

Ismételje meg a lépéseket [egy helyi hely létrehozásához](#localsite) , és [hozza létre a virtuális hálózati átjárót](#gw) a TestVNet4 konfigurálásához, és szükség esetén helyettesítse be az értékeket. Ha gyakorlatként végzi ezt, használja a [példában szereplő értékeket](#vnetvalues).

## <a name="step-6---update-the-local-sites"></a><a name="updatelocal"></a>6. lépés – a helyi helyek frissítése

Miután mindkét virtuális hálózatok létrehozta a virtuális hálózati átjárókat, módosítania kell a helyi helyek **VPN-átjárójának IP-címeinek** értékeit.

|VNet neve|Csatlakoztatott hely|Átjáró IP-címe|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|VPN Gateway IP-címe TestVNet4|
|TestVNet4|VNet1Local|VPN Gateway IP-címe TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>1. rész – a virtuális hálózati átjáró nyilvános IP-címének beolvasása

1. Keresse meg a virtuális hálózatot a Azure Portalban.
2. Ide kattintva megnyithatja a VNet **Áttekintés** lapját. A lapon, a **VPN-kapcsolatokban**megtekintheti a virtuális hálózati átjáró IP-címét.

   ![Nyilvános IP-cím](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Másolja ki az IP-címet. Ezt a következő szakaszban fogja használni.
4. Ismételje meg ezeket a lépéseket a TestVNet4

### <a name="part-2---modify-the-local-sites"></a>2. rész – a helyi helyek módosítása

1. Keresse meg a virtuális hálózatot a Azure Portalban.
2. A VNet **áttekintése** lapon kattintson a helyi webhelyre.

   ![Helyi hely létrehozva](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. A **helyek közötti VPN-kapcsolatok** lapon kattintson a módosítani kívánt helyi hely nevére.

   ![Helyi webhely megnyitása](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Kattintson a módosítani kívánt **helyi helyre** .

   ![hely módosítása](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Frissítse a **VPN-átjáró IP-címét** , és kattintson az **OK** gombra a beállítások mentéséhez.

   ![átjáró IP-címe](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. A többi oldal bezárásához.
7. Ismételje meg ezeket a lépéseket a TestVNet4.

## <a name="step-7---retrieve-values-from-the-network-configuration-file"></a><a name="getvalues"></a>7. lépés – értékek beolvasása a hálózati konfigurációs fájlból

Ha a Azure Portal klasszikus virtuális hálózatok hoz létre, a megtekintett név nem a PowerShellhez használt teljes név. Előfordulhat például, hogy a portálon a **TestVNet1** nevű VNet sokkal több nevet tartalmaz a hálózati konfigurációs fájlban. A név valahogy így néz ki: **Group ClassicRG TestVNet1**. A kapcsolatok létrehozásakor fontos, hogy a hálózati konfigurációs fájlban látható értékeket használja.

A következő lépésekben csatlakozni fog az Azure-fiókjához, és letölti és megtekinti a hálózati konfigurációs fájlt a kapcsolatokhoz szükséges értékek beszerzéséhez.

1. Töltse le és telepítse az Azure Service Management (SM) PowerShell-parancsmagok legújabb verzióját. További információ: [a Azure PowerShell használata](#powershell).

2. Nyissa meg a PowerShell-konzolt emelt szintű jogosultságokkal. Az alábbi példák segítséget nyújtanak a kapcsolódáshoz. Ezeket a parancsokat helyileg kell futtatni a PowerShell Service Management modul használatával. A Service Management szolgáltatásra való váltáshoz használja a következő parancsot:

   ```powershell
   azure config mode asm
   ```
3. Csatlakozás a fiókhoz. A következő példa segít a kapcsolódásban:

   ```powershell
   Add-AzureAccount
   ```
4. Keresse meg a fiókot az előfizetésekben.

   ```powershell
   Get-AzureSubscription
   ```
5. Ha egynél több előfizetéssel rendelkezik, akkor válassza ki azt, amelyiket használni szeretné.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```
6. Exportálja és tekintse meg a hálózati konfigurációs fájlt. Hozzon létre egy könyvtárat a számítógépén, majd exportálja a hálózati konfigurációs fájlt a könyvtárba. Ebben a példában a hálózati konfigurációs fájlt a rendszer a **C:\AzureNet**exportálja.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
7. Nyissa meg a fájlt egy szövegszerkesztővel, és tekintse meg a virtuális hálózatok és a webhelyek nevét. Ezek a nevek lesznek a kapcsolatok létrehozásakor használt nevek.<br>A VNet nevei a **VirtualNetworkSite neve =**<br>A helyek nevei a **LocalNetworkSiteRef neve =**

## <a name="step-8---create-the-vpn-gateway-connections"></a><a name="createconnections"></a>8. lépés – a VPN Gateway-kapcsolatok létrehozása

Ha az összes korábbi lépés befejeződött, beállíthatja az IPsec/IKE előmegosztott kulcsokat, és létrehozhatja a kapcsolódást. Ez a lépés a PowerShellt használja. A klasszikus üzemi modellhez tartozó VNet-VNet kapcsolatok nem konfigurálhatók a Azure Portal.

A példákban figyelje meg, hogy a megosztott kulcs pontosan ugyanaz. A megosztott kulcsnak mindig egyeznie kell. Ügyeljen rá, hogy a példákban szereplő értékeket a virtuális hálózatok és a helyi hálózati helyek pontos neveire cserélje le.

1. Hozza létre a TestVNet1–TestVNet4 kapcsolatot.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName '17BE5E2C_VNet4Local' -SharedKey A1b2C3D4
   ```
2. Hozza létre a TestVNet4–TestVNet1 kapcsolatot.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'F7F7BFC7_VNet1Local' -SharedKey A1b2C3D4
   ```
3. Várjon, amíg a kapcsolatok inicializálása megtörtént. Az átjáró inicializálása után az állapot "sikeres".

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="vnet-to-vnet-considerations-for-classic-vnets"></a><a name="faq"></a>VNet – VNet megfontolások klasszikus virtuális hálózatok
* A virtuális hálózatok lehetnek azonos vagy eltérő előfizetésekben.
* A virtuális hálózatok lehetnek azonos vagy eltérő Azure-régiókban (helyeken).
* A felhőalapú szolgáltatás vagy egy terheléselosztási végpont nem terjedhet ki a virtuális hálózatok között még akkor sem, ha azok össze vannak kapcsolva.
* Több virtuális hálózat összekapcsolása nem igényel VPN-eszközöket.
* A VNet – VNet támogatja az Azure-beli virtuális hálózatok csatlakoztatását. Nem támogatja a virtuális hálózatokra telepített virtuális gépek és felhőalapú szolgáltatások összekapcsolását.
* A VNet – VNet dinamikus útválasztási átjárók szükségesek. Az Azure statikus útválasztási átjárók nem támogatottak.
* A virtuális hálózati kapcsolat használható többhelyes virtuális VPN-ekkel együtt. A virtuális hálózati VPN-átjárók számára legfeljebb 10 VPN-alagút lehet, amely más virtuális hálózatokhoz vagy helyszíni helyekhez csatlakozik.
* A virtuális hálózatok címterei és a helyszíni helyi hálózati helyek nem lehetnek egymással átfedésben. Az átfedésben lévő címterület a virtuális hálózatok létrehozását vagy a netcfg konfigurációs fájljainak a meghibásodáshoz való feltöltését eredményezi.
* A virtuális hálózatok párjai közötti redundáns alagutak nem támogatottak.
* A VNet összes VPN-alagútja, beleértve a P2S VPN-eket is, megoszthatja a VPN-átjáró számára rendelkezésre álló sávszélességet, valamint az Azure-ban megegyező VPN Gateway-üzemidőt.
* A VNet-VNet forgalom az Azure-gerincen halad át.

## <a name="next-steps"></a>További lépések
Ellenőrizze a kapcsolatokat. Lásd: [VPN Gateway-kapcsolatok ellenőrzése](vpn-gateway-verify-connection-resource-manager.md).
