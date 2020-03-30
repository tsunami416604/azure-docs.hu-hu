---
title: 'Kapcsolat létrehozása a virtuális hálózatok között: klasszikus: Azure Portal'
description: Az Azure virtuális hálózatait a PowerShell és az Azure Portal használatával összekapcsolhatja.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/12/2020
ms.author: cherylmc
ms.openlocfilehash: 63c6329ad62289cd127902c1438073b28fc8683e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201849"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Virtuális hálózat és virtuális hálózat között lévő kapcsolat konfigurálása (klasszikus)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Ez a cikk segít a virtuális hálózatok közötti VPN-átjárókapcsolat létrehozásában. A virtuális hálózatok lehetnek azonos vagy eltérő régiókban, illetve azonos vagy eltérő előfizetésekben. A cikkben ismertetett lépések a klasszikus üzembe helyezési modellre és az Azure Portalra vonatkoznak. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:

> [!div class="op_single_selector"]
> * [Azure-portál](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Powershell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Különböző üzemi modellek összekapcsolása – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Különböző üzemi modellek összekapcsolása – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Virtuális hálózat és virtuális hálózat kapcsolati diagramja](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)



## <a name="about-vnet-to-vnet-connections"></a>Tudnivalók a virtuális hálózatok közötti kapcsolatokról

Virtuális hálózat csatlakoztatása egy másik virtuális hálózathoz (VNet-vNet) a klasszikus központi telepítési modell egy VPN-átjáró hasonló a virtuális hálózat csatlakoztatása egy helyszíni hely helyét. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához.

A virtuális hálózatok csatlakoztatható lehet a különböző előfizetések és a különböző régiókban. A virtuális hálózat és a virtuális hálózat közötti kommunikáció kombinálható többhelyes konfigurációkkal. Így létrehozhat olyan hálózati topológiákat, amelyek a létesítmények közötti kapcsolatokat a virtuális hálózatok közötti kapcsolatokkal kombinálják.

![Virtuális hálózat és virtuális hálózat-kapcsolatok](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why-connect-virtual-networks"></a><a name="why"></a>Miért érdemes összekapcsolni a virtuális hálózatokat?

A virtuális hálózatokat a következő okokból érdemes összekapcsolni:

* **Georedundancia és földrajzi jelenlét több régióban**

  * Beállíthatja a saját georeplikációját vagy szinkronizálását biztonságos kapcsolaton át, internetes végpontok használata nélkül.
  * Az Azure Load Balancer és a Microsoft vagy a külső fürtözési technológia segítségével magas rendelkezésre állású számítási feladatokat állíthat be georedundanciával több Azure-régióban. Például beállíthat folyamatosan működő SQL-t több Azure-régióban található rendelkezésre állási csoportokkal.
* **Regionális többrétegű alkalmazások erős elkülönítési határral**

  * Ugyanabban a régióban többrétegű alkalmazásokat állíthat be több virtuális hálózattal, erős elkülönítéssel és biztonságos rétegközi kommunikációval.
* **Keresztelőfizetés, szervezetek közötti kommunikáció az Azure-ban**

  * Ha több Azure-előfizetéssel rendelkezik, a különböző előfizetésekből származó számítási feladatokat biztonságosan csatlakoztathatja a virtuális hálózatok között.
  * Nagyvállalatok vagy szolgáltatók számára engedélyezheti a szervezetközi kommunikációt biztonságos VPN-technológiával az Azure-on belül.

A virtuális hálózatok közötti kapcsolatokról további információt a cikk végén, [A virtuális hálózatok közötti kapcsolatokra vonatkozó szempontok](#faq) című részben talál.

### <a name="working-with-azure-powershell"></a><a name="powershell"></a>Az Azure PowerShell együttműködése

A portált használjuk a legtöbb lépéshez, de a Virtuális hálózatok közötti kapcsolatok létrehozásához a PowerShell használatával kell létrehozni. A kapcsolatok nem hozhatlétre az Azure Portalhasználatával. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="step-1---plan-your-ip-address-ranges"></a><a name="plan"></a>1. lépés – Az IP-címtartományok megtervezése

Fontos, hogy döntse el, hogy milyen tartományokat fog használni a virtuális hálózatok konfigurálásához. Ehhez a konfigurációhoz győződjön meg arról, hogy egyik virtuális hálózati tartományok átfedésben vannak egymással, vagy a helyi hálózatok, amelyek hez csatlakoznak.

Az alábbi táblázat egy példát mutat be a virtuális hálózatok definiálására. A tartományokat csak iránymutatásként használja. Írja le a virtuális hálózatok tartományait. Erre az információra a későbbi lépésekhez van szükség.

**Példa**

| Virtual Network | Címtartomány | Régió | Csatlakozás a helyi hálózati helyhez |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |USA keleti régiója |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA nyugati régiója |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="step-2---create-the-virtual-networks"></a><a name="vnetvalues"></a>2. lépés - A virtuális hálózatok létrehozása

Hozzon létre két virtuális hálózatot az [Azure Portalon.](https://portal.azure.com) A klasszikus virtuális hálózatok létrehozásának lépéseit a [Klasszikus virtuális hálózat létrehozása](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)című témakörben található. 

Ha a portálsegítségével hoz létre egy klasszikus virtuális hálózatot, a következő lépésekkel kell navigálnia a virtuális hálózat lapra, ellenkező esetben a klasszikus virtuális hálózat létrehozásának lehetősége nem jelenik meg:

1. Kattintson a '+' gombra az "Új" oldal megnyitásához.
2. A "Keresés a piacon" mezőbe írja be a "Virtuális hálózat" kifejezést. Ha ehelyett válassza a Hálózat -> virtuális hálózat lehetőséget, nem kapja meg a klasszikus virtuális hálózat létrehozásának lehetőségét.
3. Keresse meg a "Virtuális hálózat" a visszaadott listát, és kattintson rá, hogy nyissa meg a Virtuális hálózat lapot. 
4. A virtuális hálózati lapon válassza a "Klasszikus" klasszikus virtuális hálózat létrehozásához. 

Ha ezt a cikket gyakorlatként használja, a következő példaértékeket használhatja:

**A TestVNet1 értékei**

Név: TestVNet1<br>
Címterület: 10.11.0.0/16, 10.12.0.0/16 (nem kötelező)<br>
Alhálózat neve: alapértelmezett<br>
Alhálózati címtartomány: 10.11.0.1/24<br>
Erőforrás csoport: ClassicRG<br>
Hely: East US<br>
GatewaySubnet: 10.11.1.0/27

**A TestVNet4 értékei**

Név: TestVNet4<br>
Címterület: 10.41.0.0/16, 10.42.0.0/16 (nem kötelező)<br>
Alhálózat neve: alapértelmezett<br>
Alhálózati címtartomány: 10.41.0.1/24<br>
Erőforrás csoport: ClassicRG<br>
Hely: West US<br>
GatewaySubnet: 10.41.1.0/27

**A virtuális hálózatok létrehozásakor tartsa szem előtt a következő beállításokat:**

* **Virtuális hálózati címterek** – A Virtuális hálózati címterek lapon adja meg a virtuális hálózathoz használni kívánt címtartományt. Ezek azok a dinamikus IP-címek, amelyek a virtuális gépekhez és a virtuális hálózatra telepített egyéb szerepkörpéldányokhoz lesznek rendelve.<br>A kijelölt címterek nem fedhetik át a címtereket a többi virtuális hálózat vagy helyszíni hely bármely más, amelyhez a virtuális hálózat csatlakozni fog.

* **Hely** – Amikor létrehoz egy virtuális hálózatot, társítja azt egy Azure-helyhez (régióhoz). Ha például azt szeretné, hogy a virtuális hálózatra telepített virtuális gépek fizikailag az USA nyugati részén legyenek, válassza ki azt a helyet. A virtuális hálózathoz társított hely létrehozása után nem módosítható.

**A virtuális hálózatok létrehozása után a következő beállításokat veheti fel:**

* **Címterület** – Ehhez a konfigurációhoz nincs szükség további címterületre, de a virtuális hálózat létrehozása után további címterületet is hozzáadhat.

* **Alhálózatok** – További alhálózatok nem szükségesek ehhez a konfigurációhoz, de előfordulhat, hogy a virtuális gépeket egy olyan alhálózatban szeretné látni, amely elkülönül a többi szerepkörpéldányoktól.

* **DNS-kiszolgálók** – Adja meg a DNS-kiszolgáló nevét és IP-címét. A beállítás nem hoz létre DNS-kiszolgálót. Lehetővé teszi, hogy megadja azokat a DNS-kiszolgálókat, amelyeket névfeloldásra kíván használni ennél a virtuális hálózatnál.

Ebben a szakaszban konfigurálhatja a kapcsolat típusát, a helyi helyet, és létrehozhatja az átjárót.

## <a name="step-3---configure-the-local-site"></a><a name="localsite"></a>3. lépés - A helyi hely konfigurálása

Az Azure az egyes helyi hálózati helyeken megadott beállításokat használja annak meghatározásához, hogyan irányíthatja a virtuális hálózatok közötti forgalmat. Minden virtuális hálózatnak arra a helyi hálózatra kell mutatnia, amelyhez a forgalmat irányítani szeretné. Meghatározza azt a nevet, amelyet az egyes helyi hálózati helyekre szeretne hivatkozni. A legjobb, ha valami leíró tanuskodik.

A TestVNet1 például egy "VNet4Local" nevű helyi hálózati helyhez csatlakozik. A VNet4Local beállításai tartalmazzák a TestVNet4 címelőtagjait.

Az egyes virtuális hálózatok helyi helye a másik virtuális hálózat. A következő példaértékek et használjuk a konfigurációnkhoz:

| Virtual Network | Címtartomány | Régió | Csatlakozás a helyi hálózati helyhez |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |USA keleti régiója |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA nyugati régiója |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Keresse meg a TestVNet1-et az Azure Portalon. A lap **VPN-kapcsolatok** szakaszában kattintson az **Átjáró gombra.**

    ![Nincs átjáró](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. Az **Új VPN-kapcsolat** lapon válassza **a Helyek**közötti lehetőséget .
3. Kattintson a **Helyi hely gombra** a Helyi webhely lap megnyitásához és a beállítások konfigurálásához.
4. A **Helyi webhely** lapon nevezze el a helyi webhelyet. A példában a helyi webhelyet "VNet4Local" nevet ad.
5. A **VPN-átjáró IP-címéhez**tetszőleges IP-címet használhat, feltéve, hogy az érvényes formátumban van. Általában a vpn-eszköz tényleges külső IP-címét kell használnia. De egy klasszikus virtuális hálózat-vnet konfiguráció, használja a nyilvános IP-címet, amely a virtuális hálózat átjárójához van rendelve. Mivel még nem hozta létre a virtuális hálózati átjárót, helyőrzőként meg kell adnia bármely érvényes nyilvános IP-címet.<br>Ne hagyja üresen – ez a konfiguráció nem kötelező. Egy későbbi lépésben lépjen vissza ezekre a beállításokra, és konfigurálja őket a megfelelő virtuális hálózati átjáró IP-címekkel, amint az Azure létrehozza azt.
6. Az **ügyfélcímtérhez**használja a másik virtuális hálózat címterét. Tekintse meg a tervezési példát. Kattintson **az OK** gombra a beállítások mentéséhez és az **Új VPN-kapcsolat** laphoz való visszatéréshez.

    ![helyi kemping](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="step-4---create-the-virtual-network-gateway"></a><a name="gw"></a>4. lépés - A virtuális hálózati átjáró létrehozása

Minden virtuális hálózatnak rendelkeznie kell egy virtuális hálózati átjáróval. A virtuális hálózati átjáró útvonalak és titkosítja a forgalmat.

1. Az **Új VPN-kapcsolat** lapon jelölje be az **Átjáró azonnali létrehozása** jelölőnégyzetet.
2. Kattintson **az Alhálózat, méret és útválasztási típus elemre.** Az **Átjáró konfigurációja** lapon kattintson az **Alhálózat gombra.**
3. Az átjáró alhálózatának neve automatikusan kitöltődik a szükséges "GatewaySubnet" névvel. A **Cím tartomány** tartalmazza a VPN-átjárószolgáltatások számára lefoglalt IP-címeket. Egyes konfigurációk lehetővé teszik a /29-es átjáró-alhálózatot, de a legjobb, ha a /28 vagy /27 kapcsolót használja a jövőbeli konfigurációk befogadására, amelyek további IP-címeket igényelnek az átjárószolgáltatásokhoz. A példabeállításokban a 10.11.1.0/27-et használjuk. Állítsa be a címterületet, majd kattintson az **OK gombra.**
4. Konfigurálja az **átjáró méretét**. Ez a beállítás az [átjáró termékváltozatára](vpn-gateway-about-vpn-gateway-settings.md#gwsku)vonatkozik.
5. Konfigurálja a **Műveletterv típust**. A konfiguráció útválasztási típusának **Dinamikus**nak kell lennie. Az útválasztási típust csak akkor módosíthatja később, ha lebontja az átjárót, és újat hoz létre.
6. Kattintson az **OK** gombra.
7. Az **Új VPN-kapcsolat** lapon kattintson az **OK** gombra a virtuális hálózati átjáró létrehozásának megkezdéséhez. Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet a választott átjáró-termékváltozattól függően.

## <a name="step-5---configure-testvnet4-settings"></a><a name="vnet4settings"></a>5. lépés - A TestVNet4 beállításainak konfigurálása

Ismételje meg a lépéseket [a Helyi hely létrehozása](#localsite) és a virtuális hálózati átjáró létrehozása [a](#gw) TestVNet4 konfigurálásához, szükség esetén az értékek helyettesítése. Ha ezt gyakorlatként végzi, használja a [Példa értékeket.](#vnetvalues)

## <a name="step-6---update-the-local-sites"></a><a name="updatelocal"></a>6. lépés - Frissítse a helyi oldalakat

Miután a virtuális hálózati átjárók mindkét virtuális hálózathoz létrehozták, módosítania kell a helyi helyek **VPN-átjáró IP-címértékeit.**

|Virtuális hálózat neve|Csatlakoztatott webhely|Átjáró IP-címe|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|VPN-átjáró IP-címe a TestVNet4-hez|
|TestVNet4|VNet1Local|VPN-átjáró IP-címe a TestVNet1-hez|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>1. rész - A virtuális hálózati átjáró nyilvános IP-címének beszereznie

1. Keresse meg a virtuális hálózatot az Azure Portalon.
2. Kattintson ide a Virtuális hálózat **áttekintése** lap megnyitásához. A lapon a **VPN-kapcsolatokban**megtekintheti a virtuális hálózati átjáró IP-címét.

   ![Nyilvános IP-cím](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Másolja az IP-címet. A következő szakaszban fogja használni.
4. A következő lépések megismétlése a TestVNet4 esetében

### <a name="part-2---modify-the-local-sites"></a>2. rész - A helyi helyszínek módosítása

1. Keresse meg a virtuális hálózatot az Azure Portalon.
2. A Virtuális hálózat **áttekintése** lapon kattintson a helyi webhelyre.

   ![Helyi webhely létrehozva](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. A **helyek közötti VPN-kapcsolatok** lapon kattintson a módosítani kívánt helyi hely nevére.

   ![Helyi webhely megnyitása](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Kattintson a módosítani kívánt **helyi webhelyre.**

   ![webhely módosítása](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Frissítse a **VPN-átjáró IP-címét,** és a beállítások mentéséhez kattintson az **OK** gombra.

   ![átjáró IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Zárja be a többi oldalt.
7. Ismételje meg ezeket a lépéseket a TestVNet4 esetében.

## <a name="step-7---retrieve-values-from-the-network-configuration-file"></a><a name="getvalues"></a>7. lépés – Értékek beolvasása a hálózati konfigurációs fájlból

Amikor klasszikus virtuális hálózatokat hoz létre az Azure Portalon, a megtekintett név nem a PowerShell hez használt teljes név. Például egy virtuális hálózat, amely úgy tűnik, hogy **testvNet1** nevű a portálon, lehet, hogy sokkal hosszabb nevet a hálózati konfigurációs fájlban. A név valahogy így nézhet ki: **Group ClassicRG TestVNet1**. A kapcsolatok létrehozásakor fontos, hogy a hálózati konfigurációs fájlban látható értékeket használja.

A következő lépésekben csatlakozik az Azure-fiókjához, és letölti és megtekintheti a hálózati konfigurációs fájlt a kapcsolatokhoz szükséges értékek beszerzéséhez.

1. Töltse le és telepítse az Azure Service Management (SM) PowerShell-parancsmagok legújabb verzióját. További információ: [Az Azure PowerShell együttműködése.](#powershell)

2. Nyissa meg emelt szintű jogosultságokkal rendelkező PowerShell-konzolját. Az alábbi példák segítségével segítséget nyújt a csatlakozáshoz. Ezeket a parancsokat helyileg kell futtatnia a PowerShell szolgáltatásfelügyeleti modul használatával. A szolgáltatáskezelésre való váltáshoz használja ezt a parancsot:

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
6. A hálózati konfigurációs fájl exportálása és megtekintése. Hozzon létre egy könyvtárat a számítógépén, majd exportálja a hálózati konfigurációs fájlt a könyvtárba. Ebben a példában a hálózati konfigurációs fájl a **C:\AzureNet**fájlba lesz exportálva.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
7. Nyissa meg a fájlt egy szövegszerkesztővel, és tekintse meg a virtuális hálózatok és webhelyek nevét. Ezek a nevek lesznek a kapcsolatok létrehozásakor használt nevek.<br>A virtuális hálózatok nevei **VirtualNetworkSite névként** vannak felsorolva =<br>A webhelynevek **LocalNetworkSiteRef névként** vannak felsorolva =

## <a name="step-8---create-the-vpn-gateway-connections"></a><a name="createconnections"></a>8. lépés - A VPN-átjárókapcsolatok létrehozása

Az összes előző lépés elvégzése után beállíthatja az IPsec/IKE előmegosztott kulcsokat, és létrehozhatja a kapcsolatot. Ez a lépéskészlet a PowerShellt használja. A klasszikus üzembe helyezési modell virtuális hálózathoz –virtuális hálózathoz való kapcsolatai nem konfigurálhatók az Azure Portalon.

A példákban figyelje meg, hogy a megosztott kulcs pontosan ugyanaz. A megosztott kulcsnak mindig egyeznie kell. Ügyeljen arra, hogy cserélje le az értékeket ezekben a példákban a pontos nevét a virtuális hálózatok és a helyi hálózati helyek.

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
3. Várja meg, amíg a kapcsolatok inicializálódnak. Az átjáró inicializálása után az állapot "Sikeres".

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="vnet-to-vnet-considerations-for-classic-vnets"></a><a name="faq"></a>Virtuális hálózatokra vonatkozó figyelembe vegánként idáig –tudnió konthét
* A virtuális hálózatok lehetnek azonos vagy különböző előfizetések.
* A virtuális hálózatok lehetnek azonos vagy eltérő Azure-régiókban (helyeken).
* Egy felhőszolgáltatás vagy egy terheléselosztási végpont nem terjedhet ki a virtuális hálózatokközött, még akkor sem, ha össze vannak kapcsolva.
* Több virtuális hálózat összekapcsolása nem igényel VPN-eszközöket.
* A VNet-vnet támogatja az Azure virtuális hálózatok csatlakoztatását. Nem támogatja a virtuális gépek vagy a virtuális hálózatra nem telepített felhőszolgáltatások csatlakoztatását.
* Virtuális hálózat-vnet dinamikus útválasztási átjárók. Az Azure statikus útválasztási átjárók nem támogatottak.
* A virtuális hálózati kapcsolat használható többhelyes virtuális VPN-ekkel együtt. Egy virtuális hálózati VPN-átjáróhoz legfeljebb 10 VPN-alagút tartozik, amely más virtuális hálózatokhoz vagy helyszíni helyekhez csatlakozik.
* A virtuális hálózatok címterei és a helyszíni helyi hálózati helyek nem lehetnek egymással átfedésben. Az egymást átfedő címterek a virtuális hálózatok létrehozását vagy a netcfg konfigurációs fájlok feltöltését eredményezik.
* A virtuális hálózatok párjai közötti redundáns alagutak nem támogatottak.
* A virtuális hálózat összes VPN-alagútja, beleértve a P2S VPN-eket is, megosztja a VPN-átjáró rendelkezésre álló sávszélességét, és ugyanazt a VPN-átjáró üzemidő SLA-t az Azure-ban.
* VNet-to-VNet forgalom az Azure gerincét.

## <a name="next-steps"></a>További lépések
Ellenőrizze a kapcsolatokat. Lásd: [VPN-átjárókapcsolat ellenőrzése.](vpn-gateway-verify-connection-resource-manager.md)
