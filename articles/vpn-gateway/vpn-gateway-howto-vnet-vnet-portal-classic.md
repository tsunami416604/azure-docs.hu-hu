---
title: 'Virtuális hálózatok közötti kapcsolat létrehozása: klasszikus: Az Azure portal |} A Microsoft Docs'
description: PowerShell és az Azure portal használata az Azure virtuális hálózatok összekapcsolása.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: e323a8d71bbffd1d29ad793dff7b5b4a072b6979
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046122"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Konfigurálja a VNet – VNet kapcsolat (klasszikus)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Ez a cikk segítségével hozhat létre virtuális hálózatok közötti VPN gateway-kapcsolat. A virtuális hálózatok lehetnek azonos vagy eltérő régiókban, illetve azonos vagy eltérő előfizetésekben. A jelen cikkben ismertetett lépések a klasszikus üzemi modell és az Azure Portalra vonatkoznak. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Különböző üzemi modellek összekapcsolása – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Különböző üzemi modellek összekapcsolása – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Virtuális hálózatok közötti virtuális hálózatok közötti kapcsolat diagramja](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-vnet-to-vnet-connections"></a>Tudnivalók a virtuális hálózatok közötti kapcsolatokról

Két virtuális hálózat egy másik virtuális hálózathoz (VNet – VNet) VPN-átjáró használatával a klasszikus üzemi modellben hasonlít a két virtuális hálózat egy helyszíni helyhez. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához.

Az összekapcsolt virtuális hálózatok különböző előfizetésekben találhatóak, és különböző régiókban is lehetnek. Virtuális hálózat kombinálhatja többhelyes konfigurációkkal rendelkező virtuális hálózatok közötti kommunikációra. Így létrehozhat olyan hálózati topológiákat, amelyek a létesítmények közötti kapcsolatokat a virtuális hálózatok közötti kapcsolatokkal kombinálják.

![Virtuális hálózatok közötti kapcsolatok](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why"></a>Miért érdemes összekapcsolni a virtuális hálózatokat?

A virtuális hálózatokat a következő okokból érdemes összekapcsolni:

* **Georedundancia és földrajzi jelenlét több régióban**

  * Beállíthatja a saját georeplikációját vagy szinkronizálását biztonságos kapcsolaton át, internetes végpontok használata nélkül.
  * Az Azure Load Balancer és a Microsoft vagy harmadik fél fürtözési technológia beállíthat magas rendelkezésre állású munkaterhelést georedundanciával több Azure-régiók között. Például beállíthat folyamatosan működő SQL-t több Azure-régióban található rendelkezésre állási csoportokkal.
* **Regionális Többrétegű alkalmazások erős elkülönítési határ**

  * Egy régión belül beállíthat Többrétegű alkalmazások az erős elkülönítést és réteg közötti kommunikációt a csatlakoztatott virtuális hálózatokat.
* **Kereszt-előfizetéssel, az Azure-ban a szervezet közötti kommunikáció**

  * Ha több Azure-előfizetéssel rendelkezik, csatlakoztathatja számítási feladatok különböző előfizetésekről csatlakoztat együtt biztonságosan virtuális hálózatok között.
  * A vállalatok és szolgáltatók engedélyezheti a biztonságos VPN-technológia, Azure-beli szervezetek közötti kommunikációt.

A virtuális hálózatok közötti kapcsolatokról további információt a cikk végén, [A virtuális hálózatok közötti kapcsolatokra vonatkozó szempontok](#faq) című részben talál.

### <a name="before-you-begin"></a>Előkészületek

Mielőtt megkezdené az ebben a gyakorlatban, töltse le és telepítse az Azure Service Management (SM) PowerShell-parancsmagjainak legújabb verzióját. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál. A lépések többségét használjuk a portálon, de a PowerShell használatával kell létrehozni a virtuális hálózatok közötti kapcsolatok. A kapcsolatok az Azure portal használatával nem hozható létre.

## <a name="plan"></a>1. lépés – Az IP-címtartományok megtervezése

Fontos eldönteni, milyen tartományokat, amelyek segítségével a virtuális hálózatok konfigurálása. Ehhez a konfigurációhoz győződjön meg arról, hogy a virtuális hálózatok közötti tartományok egyike átfedésben vannak egymással, vagy azokkal a helyi hálózatokat, amelyekhez csatlakoznak.

Az alábbi táblázat a virtuális hálózatok definiálása egy példát mutat be. Használja a tartományok csak irányelvként. Jegyezze fel a virtuális hálózatok azokat a tartományokat. Ezt az információt későbbi lépésekben szüksége.

**Példa**

| Virtual Network | Címtartomány | Régió | Csatlakozik a helyi hálózati telephely |
|:--- |:--- |:--- |:--- |
| A TestVNet1 |A TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |USA keleti régiója |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| A TestVNet4 |A TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA nyugati régiója |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="vnetvalues"></a>2. lépés – a virtuális hálózatok létrehozása

Hozzon létre két virtuális hálózatot a [az Azure portal](https://portal.azure.com). A klasszikus virtuális hálózatot hozhat létre lépéseiért lásd: [klasszikus virtuális hálózat létrehozása](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). 

Egy klasszikus virtuális hálózat létrehozása a portál használata esetén meg kell megkeresnie a virtuális hálózat lap a következő lépésekkel, ellenkező esetben létrehozhat egy klasszikus virtuális hálózat nem jelenik meg:

1. Kattintson a "+", "Új" lap megnyitásához.
2. A "Keresés a piactéren" mezőbe írja be a "Virtuális hálózat". Ha ehelyett hálózat -> virtuális hálózat, nem fog kapni egy klasszikus virtuális hálózat létrehozásának lehetősége.
3. A visszaadott listában keresse meg a "Virtuális hálózat", és kattintson rá a virtuális hálózat lap megnyitásához. 
4. A virtuális hálózat lapon válassza ki a "Klasszikus" klasszikus virtuális hálózat létrehozásához. 

Ha gyakorlatként használja ez a cikk, használhatja a következő példaértékeket:

**Értékek a testvnet1-hez**

Név: A TestVNet1<br>
Címtér: 10.11.0.0/16, 10.12.0.0/16 (nem kötelező)<br>
Alhálózat neve: alapértelmezett<br>
Alhálózati címtartomány: 10.11.0.1/24<br>
Erőforráscsoport: ClassicRG<br>
Hely: USA keleti régiója<br>
Átjáró-alhálózat: 10.11.1.0/27

**Értékek a testvnet4-hez**

Név: A TestVNet4<br>
Címtér: 10.41.0.0/16, 10.42.0.0/16 (nem kötelező)<br>
Alhálózat neve: alapértelmezett<br>
Alhálózati címtartomány: 10.41.0.1/24<br>
Erőforráscsoport: ClassicRG<br>
Hely: USA nyugati régiója<br>
Átjáró-alhálózat: 10.41.1.0/27

**Ha a virtuális hálózatok létrehozása, vegye figyelembe a következő beállításokat:**

* **Virtuális hálózat Címterei** – a virtuális hálózat Címterei lap, adja meg a címtartományt, amelyet a virtuális hálózatához használni szeretne. Ezek olyan dinamikus IP-címeket rendel a virtuális gépek és a többi szerepkörpéldány úgy, hogy a virtuális hálózaton.<br>A címterek választja a címterek bármely más virtuális hálózatokhoz vagy helyszíni hely, amelyhez a virtuális hálózathoz csatlakoznak, nem lehet átfedésben.

* **Hely** – egy virtuális hálózat létrehozásakor társítsa azt egy Azure-beli hely (régió). Például ha azt szeretné, hogy a virtuális gépek, amelyek a virtuális hálózat helyezkedjenek el az USA nyugati Régiójában vannak telepítve, válassza ki azt helyként. A létrehozása után a virtuális hálózathoz társított helye nem módosítható.

**Miután létrehozta a virtuális hálózatok, adja hozzá a következő beállításokat:**

* **Címtér** – további címtereket nem kötelező ehhez a konfigurációhoz, de a virtuális hálózat létrehozása után is hozzáadhat további címtereket.

* **Alhálózatok** – további alhálózatok nem szükségesek ehhez a konfigurációhoz, de előfordulhat, hogy szeretné, hogy a virtuális gépeit egy a többi szerepkörpéldánytól különálló alhálózaton.

* **DNS-kiszolgálók** – adja meg a DNS-kiszolgáló nevét és IP-címet. A beállítás nem hoz létre DNS-kiszolgálót. Lehetővé teszi, hogy megadja azokat a DNS-kiszolgálókat, amelyeket névfeloldásra kíván használni ennél a virtuális hálózatnál.

Ebben a szakaszban a kapcsolat típusát, a helyi hely konfigurálása, és az átjáró létrehozásához.

## <a name="localsite"></a>3. lépés – a helyi hely konfigurálása

Az Azure az egyes helyi hálózati helyeken megadott beállítások segítségével határozza meg, hogyan irányíthatja a forgalmat a virtuális hálózat között. Minden egyes virtuális hálózat a megfelelő helyi hálózati forgalmat útvonal kívánt kell mutatnia. Meghatározhatja a hivatkozzon az egyes helyi hálózati telephely kívánt nevét. Célszerű használni egy leíró névre.

A TestVNet1 például egy helyi hálózati telephely létrehozott "VNet4Local" nevű csatlakozik. VNet4Local beállításait a testvnet4-hez a címelőtagokat tartalmaz.

Az egyes virtuális hálózatok helyi webhelyhez, a másik virtuális hálózat. A konfiguráció a következő példában szereplő értékeket használják:

| Virtual Network | Címtartomány | Régió | Csatlakozik a helyi hálózati telephely |
|:--- |:--- |:--- |:--- |
| A TestVNet1 |A TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |USA keleti régiója |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| A TestVNet4 |A TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA nyugati régiója |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Keresse meg a testvnet1-et az Azure Portalon. Az a **VPN-kapcsolatok** szakaszban kattintson az oldal **átjáró**.

    ![Nincs átjáró](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. Az a **új VPN-kapcsolat** lapon jelölje be **Site-to-Site**.
3. Kattintson a **helyi hely** nyissa meg a helyi hely lapot, és adja meg a beállításokat.
4. Az a **helyi hely** lapon, a helyi hely neve. Ebben a példában a helyi hely "VNet4Local" nevet használjuk.
5. A **VPN-átjáró IP-cím**, is használhatja, amelyet szeretne, IP-címeket, mindaddig, amíg a egy érvényes formátumban. A tényleges külső IP-cím általában használna egy VPN-eszköz számára. De klasszikus VNet – VNet-konfiguráció esetén az átjáró a virtuális hálózathoz rendelt nyilvános IP-cím használható. Tekintettel arra, hogy a virtuális hálózati átjáró még nem létrehozott, adjon meg érvényes nyilvános IP-címet helyettesíti.<br>Ne hagyja üresen a mezőt – már nem kötelező ehhez a konfigurációhoz. Egy későbbi lépésben térjen vissza ezeket a beállításokat, és konfigurálja őket a megfelelő virtuális hálózati átjáró IP-címekkel rendelkező, Azure állít elő, ha.
6. A **Ügyfélcímtér**, a másik virtuális hálózat címteréhez használja. Tekintse meg a tervezési példa. Kattintson a **OK** mentse a beállításokat, és térjen vissza a **új VPN-kapcsolat** lapot.

    ![helyi webhely](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="gw"></a>4. lépés – a virtuális hálózati átjáró létrehozása

Minden virtuális hálózatnak rendelkeznie kell egy virtuális hálózati átjárót. A virtuális hálózati átjáró irányítja, és titkosítja a forgalmat.

1. Az **Új VPN-kapcsolat** lapon jelölje be az **Átjáró azonnali létrehozása** jelölőnégyzetet.
2. Kattintson a **alhálózat, méret és útválasztási típus**. Az a **átjárókonfiguráció** kattintson **alhálózati**.
3. Az átjáró-alhálózat neve automatikusan kitölti a szükséges "GatewaySubnet" névvel. A **címtartomány** tartalmazza a VPN-átjáró szolgáltatásai kiosztott IP-címeket. Bizonyos konfigurációk lehetővé teszik egy/29 méretű átjáró-alhálózatot, de a legjobb, ha egy/28 vagy/27-eset használja, szükség lehet további IP-címeket az Átjárószolgáltatások számára jövőbeni konfigurációk megvalósításához. A például szolgáló beállításokban 10.11.1.0/27 használjuk. Módosítsa a címtartományt, majd kattintson a **OK**.
4. Konfigurálja a **az átjáró mérete**. Ez a beállítás hivatkozik a [átjáró-Termékváltozat](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Konfigurálja a **útválasztási típus**. Ez a konfiguráció kell lennie az Útválasztás típusa **dinamikus**. Az útválasztási típus nem módosítható később, kivéve, ha az átjáró üzemen, és hozzon létre egy újat.
6. Kattintson az **OK** gombra.
7. Az a **új VPN-kapcsolat** kattintson **OK** a virtuális hálózati átjáró létrehozásához. Az átjáró létrehozása akár 45 percet is igénybe vehet, az átjáró kiválasztott termékváltozatától függően.

## <a name="vnet4settings"></a>5. lépés – a TestVNet4-beállítások konfigurálása

Ismételje meg ezeket a lépéseket [hozzon létre egy helyi](#localsite) és [a virtuális hálózati átjáró létrehozása](#gw) konfigurálása a testvnet4-et, és cserélje le az értékeket, ha szükséges. Ha gyakorlatként végeznek, használja a [példaértékeket](#vnetvalues).

## <a name="updatelocal"></a>6. lépés – a helyek frissítése

Miután a virtuális hálózati átjárók mindkét virtuális hálózaton lett létrehozva, módosítania kell a helyek **VPN-átjáró IP-cím** értékeket.

|Virtuális hálózat neve|Az összekapcsolt hely|Átjáró IP-címe|
|:--- |:--- |:--- |
|A TestVNet1|VNet4Local|VPN-átjáró IP-cím a testvnet4-hez|
|A TestVNet4|VNet1Local|VPN-átjáró IP-cím a testvnet1-hez|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>1. rész – virtuális hálózati átjáró nyilvános IP-címének lekérése

1. Keresse meg a virtuális hálózat az Azure Portalon.
2. Kattintással nyissa meg a virtuális hálózat **áttekintése** lapot. A lapon lévő **VPN-kapcsolatok**, megtekintheti az IP-címet a virtuális hálózati átjáró.

   ![Nyilvános IP-cím](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Másolja ki az IP-címet. A következő szakaszban be fogja használni.
4. Ismételje meg ezeket a lépéseket a testvnet4-hez

### <a name="part-2---modify-the-local-sites"></a>2. rész – a helyi hely módosítása

1. Keresse meg a virtuális hálózat az Azure Portalon.
2. A virtuális hálózaton **áttekintése** lap, kattintson a helyi webhelyhez.

   ![Helyi hely létrehozva](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. Az a **Site-to-Site VPN-kapcsolatok** lap, kattintson a módosítandó hely nevére.

   ![Helyi webhely megnyitása](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Kattintson a **helyi hely** , amelyet módosítani szeretne.

   ![hely módosítása](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Frissítés a **VPN-átjáró IP-cím** kattintson **OK** a beállítások mentéséhez.

   ![Átjáró IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Zárja be a más oldalakon.
7. Ismételje meg ezeket a lépéseket a testvnet4-hez.

## <a name="getvalues"></a>7. lépés – kérhető le a hálózati konfigurációs fájlban szereplő értékek

Klasszikus virtuális hálózatok az Azure Portalon hoz létre, ha a megjelenített név nem a teljes nevet használhat olyan PowerShell. Például egy virtuális hálózat neve lehet megjelenő **TestVNet1** a portálon, előfordulhat, hogy rendelkezik egy sokkal hosszabb név a hálózati konfigurációs fájlt. Neve a következőhöz hasonló: **Csoport ClassicRG TestVNet1**. A kapcsolatok létrehozásakor fontos látja értéket használja a hálózati konfigurációs fájlban.

A következő lépések, Azure-fiókjához csatlakozhat, és letölti és megtekintheti a hálózati konfigurációs fájlt, amelyek szükségesek a kapcsolat értékeinek lekéréséhez.

1. Töltse le és telepítse az Azure Service Management (SM) PowerShell-parancsmagjainak legújabb verzióját. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál.

2. Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

   ```powershell
   Connect-AzAccount
   ```

   Keresse meg a fiókot az előfizetésekben.

   ```powershell
   Get-AzSubscription
   ```

   Ha egynél több előfizetéssel rendelkezik, akkor válassza ki azt, amelyiket használni szeretné.

   ```powershell
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

   Ezután használja a következő parancsmagot az Azure-előfizetés hozzáadása a PowerShell a klasszikus üzemi modellhez.

   ```powershell
   Add-AzureAccount
   ```
3. Exportálja, és megtekintheti a hálózati konfigurációs fájlt. Hozzon létre egy könyvtárat a számítógépén, majd exportálja a hálózati konfigurációs fájlt a könyvtárba. Ebben a példában a hálózati konfigurációs fájlt exportálják **C:\AzureNet**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
4. Nyissa meg a fájlt egy szövegszerkesztőben, és a virtuális hálózatok és helyek nevének megtekintése. Ezek a kapcsolatok létrehozásakor használt név lesz.<br>Virtuális hálózat nevei jelennek meg **VirtualNetworkSite name =**<br>Helynevek létrehoztak **LocalNetworkSiteRef neve =**

## <a name="createconnections"></a>8. lépés – a VPN gateway-kapcsolatok létrehozása

Ha az előző lépés befejeződött, állítsa be az IPsec/IKE előmegosztott kulcsok, és hozza létre a kapcsolatot. Ezen lépések készlete a Powershellt használja. A klasszikus üzemi modell esetében a virtuális hálózatok közötti kapcsolatok nem konfigurálható az Azure Portalon.

A példákban láthatja, hogy a megosztott kulcs pontosan ugyanaz. A megosztott kulcs mindig meg kell egyeznie. Mindenképpen ezekben a példákban szereplő értékeket cserélje le a virtuális hálózatok és a helyi hálózati telephelyek pontos nevét.

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
3. Várjon, amíg a kapcsolatok inicializálása. Miután az átjáró inicializálódott, az állapota "Sikeres".

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="faq"></a>A klasszikus virtuális hálózatok virtuális hálózatok – szempontok
* A virtuális hálózatok lehetnek azonos vagy eltérő előfizetésekben.
* A virtuális hálózatok lehetnek azonos vagy eltérő Azure-régiókban (helyeken).
* A felhőszolgáltatás és a terheléselosztási végpont nem ívelhet át több virtuális hálózaton, akkor sem, ha ezek össze vannak kapcsolva.
* Virtual networkök összekapcsolása nem igényel VPN-eszköze.
* Virtuális hálózatok Azure virtuális hálózatok csatlakoztatását támogatja. Nem támogatja a csatlakozó virtuális gépeket, és nem felhőszolgáltatások, virtuális hálózat nem telepített.
* Virtuális hálózatok dinamikus útválasztási átjáró szükséges. Az Azure statikus útválasztású átjárók nem támogatottak.
* A virtuális hálózati kapcsolat használható többhelyes virtuális VPN-ekkel együtt. Nincs más virtuális hálózatokhoz vagy helyszíni webhely csatlakoztatása virtuális hálózathoz VPN-átjáró esetében 10 VPN-alagutak maximális.
* A virtuális hálózatok címterei és a helyszíni helyi hálózati helyek nem lehetnek egymással átfedésben. Egymással átfedésben lévő címterek a virtuális hálózatok vagy fájlokat tölthet fel a netcfg konfigurációs sikertelen létrehozása miatt.
* A virtuális hálózatok párjai közötti redundáns alagutak nem támogatottak.
* Az összes VPN-alagút, P2S VPN-eket, beleértve a vnet ossza meg a VPN-átjáró és az azonos VPN-átjáró üzemidőre SLA-t az Azure-ban rendelkezésre álló sávszélességet.
* A virtuális hálózatok közötti adatforgalom az Azure gerinchálózatán halad át.

## <a name="next-steps"></a>További lépések
Ellenőrizze a kapcsolatokat. Lásd: [VPN Gateway-kapcsolat ellenőrzése](vpn-gateway-verify-connection-resource-manager.md).
