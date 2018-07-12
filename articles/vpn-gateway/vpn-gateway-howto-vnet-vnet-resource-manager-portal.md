---
title: 'Azure virtuális hálózat csatlakoztatása másik virtuális hálózathoz: Portal | Microsoft Docs'
description: VPN Gateway-kapcsolatot hozhat létre virtuális hálózatok között a Resource Managerrel és az Azure Portallal.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: cherylmc
ms.openlocfilehash: 7e72e9ff9be0b092bc9425828bab46ffd60f0822
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38630267"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-the-azure-portal"></a>Virtuális hálózatok közötti VPN-átjárókapcsolat konfigurálása az Azure Portalon

Ez a cikk bemutatja, hogyan lehet virtuális hálózatokat csatlakoztatni virtuális hálózatok közötti kapcsolat használatával. A virtuális hálózatok lehetnek azonos vagy eltérő régiókban, illetve azonos vagy eltérő előfizetésekben. Amikor különböző előfizetésekről csatlakoztat virtuális hálózatokat, az előfizetéseket nem kell társítani ugyanazzal az Active Directory-bérlővel. 

A cikkben ismertetett lépések a Resource Manager-alapú üzemi modellre vonatkoznak, és az Azure Portalt használják. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Különböző üzemi modellek összekapcsolása – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Különböző üzemi modellek összekapcsolása – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![v2v ábra](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

## <a name="about"></a>Tudnivalók a virtuális hálózatok csatlakoztatásáról

A virtuális hálózatok többféleképpen is összekapcsolhatók. Az alábbi szakaszok a virtuális hálózatok összekapcsolásának különböző módjait ismertetik.

### <a name="vnet-to-vnet"></a>Virtuális hálózatok közötti kapcsolat

Virtuális hálózatok közötti kapcsolat konfigurálásával könnyedén kapcsolat össze virtuális hálózatokat. Két virtuális hálózat virtuális hálózatok közötti kapcsolattal történő összekapcsolása (VNet2VNet) nagyon hasonlít egy helyek közötti IPsec-kapcsolat helyszíni helyhez való csatlakoztatásához. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához, és mindkettő ugyanúgy működik a kommunikáció során. A kapcsolattípusok közötti különbség a helyi hálózati átjáró konfigurálásának módjában rejlik. Virtuális hálózatok közötti kapcsolat létrehozásakor a helyi hálózati átjáró címtere nem látható. Ennek létrehozása és feltöltése automatikusan történik. Amikor azonban frissíti az egyik virtuális hálózat címterét, a másik automatikusan tudni fogja a frissített címtér útvonalát. A virtuális hálózatok közötti kapcsolat létrehozása általában gyorsabb és egyszerűbb, mintha egy helyek közötti kapcsolatot hozna létre a virtuális hálózatok között.

### <a name="site-to-site-ipsec"></a>Helyek közötti kapcsolat (IPsec)

Amikor bonyolult hálózati konfigurációkkal dolgozik, érdemesebb lehet a [helyek közötti kapcsolatokra](vpn-gateway-howto-site-to-site-resource-manager-portal.md) vonatkozó lépéseket használni. A helyek közötti IPsec-kapcsolatokra vonatkozó lépésekkel manuálisan hozhatja létre és konfigurálhatja a helyi hálózati átjárókat. Az egyes virtuális hálózatok helyi hálózati átjárója helyi helyként kezeli a többi virtuális hálózatot. Így további címtereket határozhat meg a helyi hálózati átjáróhoz a forgalom irányítása érdekében. Ha egy virtuális hálózat címtere megváltozik, frissítenie kell a megfelelő helyi hálózati átjárót a változás tükrözése érdekében. Az átjáró nem frissül automatikusan.

### <a name="vnet-peering"></a>Társviszony létesítése virtuális hálózatok között

Érdemes megfontolni a virtuális hálózatok virtuális hálózatok közötti társviszony útján történő összekötését. A virtuális hálózatok közötti társviszony nem használ VPN-átjárót, és más korlátozásokkal rendelkezik. Emellett a [virtuális hálózatok közötti társviszony díjszabásának](https://azure.microsoft.com/pricing/details/virtual-network) kiszámítása máshogy történik, mint a [virtuális hálózatok közötti VPN-átjáró](https://azure.microsoft.com/pricing/details/vpn-gateway) esetén. További információ: [Társviszony létesítése virtuális hálózatok között](../virtual-network/virtual-network-peering-overview.md).

## <a name="why"></a>Mikor érdemes virtuális hálózatok közötti kapcsolatot létrehozni?

A virtuális hálózatokat a következő okokból érdemes virtuális hálózatok közötti kapcsolattal összekapcsolni:

* **Georedundancia és földrajzi jelenlét több régióban**

  * Beállíthatja a saját georeplikációját vagy szinkronizálását biztonságos kapcsolaton át, internetes végpontok használata nélkül.
  * Az Azure Traffic Manager és a Load Balancer segítségével magas rendelkezésre állású munkaterhelést állíthat be georedundanciával több Azure-régióban. Például beállíthat folyamatosan működő SQL-t több Azure-régióban található rendelkezésre állási csoportokkal.
* **Regionális többrétegű alkalmazások elkülönítéssel vagy felügyeleti határral**

  * Egy régión belül beállíthat többrétegű alkalmazásokat több, elkülönítéssel vagy felügyeleti követelményekkel összekapcsolt virtuális hálózatokkal.

A virtuális hálózatok közötti kommunikáció kombinálható többhelyes konfigurációkkal. Így létrehozhat olyan hálózati topológiákat, amelyek a létesítmények közötti kapcsolatokat a virtuális hálózatok közötti kapcsolatokkal egyesítik, ahogyan azt a következő diagram mutatja:

![Tudnivalók a kapcsolatokról](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Tudnivalók a kapcsolatokról")

Ez a cikk bemutatja, hogyan lehet virtuális hálózatokat csatlakoztatni virtuális hálózatok közötti kapcsolat használatával. Ha gyakorlatként használja ezeket a lépéseket, használhatja a példa beállításértékeket. A példában a virtuális hálózatok ugyanabban az előfizetésben, de különböző erőforráscsoportokban vannak. Ha a virtuális hálózatok különböző előfizetésekben találhatóak, nem hozhatja létre a kapcsolatot a portálon. Ehelyett használhatja a [PowerShellt](vpn-gateway-vnet-vnet-rm-ps.md) vagy a [parancssori felületet](vpn-gateway-howto-vnet-vnet-cli.md). A virtuális hálózatok közötti kapcsolatokról további információt a cikk végén, a [Virtuális hálózatok közötti kapcsolat – gyakori kérdések](#faq) című részben talál.

### <a name="values"></a>Példabeállítások

**Értékek a TestVNet1-hez:**

* Virtuális hálózat neve: TestVNet1
* Címtér: 10.11.0.0/16
* Előfizetés: Válassza ki a használni kívánt előfizetést
* Erőforráscsoport: TestRG1
* Hely: East US
* Alhálózat neve: FrontEnd
* Alhálózati címtartomány: 10.11.0.0/24
* Átjáróalhálózat neve: GatewaySubnet (a portálon ez a rész automatikusan ki lesz töltve)
* Átjáróalhálózat címtartománya: 10.11.255.0/27
* DNS-kiszolgáló: Használja a saját DNS-kiszolgálója IP-címét.
* Virtuális hálózati átjáró neve: TestVNet1GW
* Átjáró típusa: VPN
* VPN típusa: Útvonalalapú
* Termékváltozat: Válassza ki az átjáró használni kívánt termékváltozatát.
* Nyilvános IP-cím neve: TestVNet1GWIP
* Kapcsolat neve: TestVNet1toTestVNet4
* Megosztott kulcs: Saját maga létrehozhatja a megosztott kulcsot. Ebben a példában az abc123-at használjuk. Fontos, hogy az érték egyezzen, amikor létrehozza a virtuális hálózatok közötti kapcsolatot.

**Értékek a TestVNet4-hez:**

* Virtuális hálózat neve: TestVNet4
* Címtér: 10.41.0.0/16
* Előfizetés: Válassza ki a használni kívánt előfizetést
* Erőforráscsoport: TestRG4
* Hely: West US
* Alhálózat neve: FrontEnd
* Alhálózati címtartomány: 10.41.0.0/24
* Átjáró-alhálózat neve: GatewaySubnet (a portálon ez a rész automatikusan ki lesz töltve)
* Átjáró-alhálózat címtartománya: 10.41.255.0/27
* DNS-kiszolgáló: Használja a saját DNS-kiszolgálója IP-címét.
* Virtuális hálózati átjáró neve: TestVNet4GW
* Átjáró típusa: VPN
* VPN típusa: Útvonalalapú
* Termékváltozat: Válassza ki az átjáró használni kívánt termékváltozatát.
* Nyilvános IP-cím neve: TestVNet4GWIP
* Kapcsolat neve: TestVNet4toTestVNet1
* Megosztott kulcs: Saját maga létrehozhatja a megosztott kulcsot. Ebben a példában az abc123-at használjuk. Fontos, hogy az érték egyezzen, amikor létrehozza a virtuális hálózatok közötti kapcsolatot.

## <a name="CreatVNet"></a>1. A TestVNet1 létrehozása és konfigurálása
Ha már rendelkezik egy virtuális hálózattal, győződjön meg arról, hogy a beállításai kompatibilisek a VPN-átjáró kialakításával. Különösen ügyeljen az esetleges olyan alhálózatokra, amelyek átfedésbe kerülhetnek más hálózatokkal. Egymást átfedő alhálózatok esetén a kapcsolat nem fog megfelelően működni. Ha a virtuális hálózat a megfelelő beállításokkal lett konfigurálva, folytassa a [DNS-kiszolgáló megadása](#dns) szakaszban leírt lépésekkel.

### <a name="to-create-a-virtual-network"></a>Virtuális hálózat létrehozása
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="subnets"></a>2. További címterek hozzáadása és alhálózatok létrehozása
Miután létrehozta a virtuális hálózatot, további címtereket adhat hozzá és alhálózatokat hozhat létre.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3. Átjáróalhálózat létrehozása
Mielőtt virtuális hálózati átjárót hozna létre a virtuális hálózathoz, létre kell hoznia az átjáróalhálózatot. Az átjáróalhálózat tartalmazza a virtuális hálózati átjáró által használt IP-címeket. Ha lehetséges, a legjobb megoldás egy átjáróalhálózat létrehozása /28 vagy /27 CIDR-blokk használatával annak érdekében, hogy a jövőbeli további konfigurációs követelmények számára elegendő IP-címet biztosíthasson.

Ha gyakorlatként hozza létre ezt a konfigurációt, használja ezeket a [Példabeállításokat](#values) az átjáróalhálózat létrehozásakor.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Átjáróalhálózat létrehozása
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="dns"></a>4. DNS-kiszolgáló megadása (nem kötelező)
A virtuális hálózatok közötti kapcsolatokhoz nincs szükség DNS-re. Ha azonban azt szeretné, hogy a virtuális hálózatokon üzembe helyezett erőforrásokon működjön a névfeloldás, adjon meg egy DNS-kiszolgálót. Ezzel a beállítással megadhatja azt a DNS-kiszolgálót, amelyet névfeloldásra kíván használni ennél a virtuális hálózatnál. A beállítás nem hoz létre új DNS-kiszolgálót.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. Virtuális hálózati átjáró létrehozása
Ebben a lépésben a virtuális hálózat virtuális hálózati átjáróját fogja létrehozni. Az átjáró létrehozása akár 45 percet is igénybe vehet, az átjáró kiválasztott termékváltozatától függően. Ha gyakorlatként hozza létre ezt a konfigurációt, használja ezeket a [Példabeállításokat](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="CreateTestVNet4"></a>6. A TestVNet4 létrehozása és konfigurálása
Miután konfigurálta a TestVNet1-et, hozza létre a TestVNet4-et az előző lépések megismétlésével és az értékek a TestVNet4 értékeire való kicserélésével. A TestVNet4 konfigurálásához nem kell megvárnia, hogy befejeződjön a TestVNet1 virtuális hálózati átjárójának létrehozása. Ha a saját értékeit használja, győződjön meg róla, hogy a címterek nincsenek átfedésben azokkal a virtuális hálózatokkal, amelyekhez csatlakozni kíván.

## <a name="TestVNet1Connection"></a>7. A TestVNet1 átjárókapcsolat konfigurálása
Miután mind a TestVNet1, mind a TestVNet4 virtuális hálózati átjárójának létrehozása befejeződött, létrehozhatja a virtuális hálózati átjárókapcsolatokat. Ebben a szakaszban létrehozza a kapcsolatot a VNet1 felől a VNet4 felé. Ezek a lépések csak egyazon előfizetésben lévő virtuális hálózatokkal működnek. Ha a virtuális hálózatok különböző előfizetésekben találhatóak, a kapcsolatot a PowerShell használatával kell létrehozni. Lásd a [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)-re vonatkozó cikket. Ha a virtuális hálózatok ugyanazon előfizetés különböző erőforráscsoportjaiban találhatóak, a portállal csatlakoztathatja őket.

1. A **Minden erőforrás** részben navigáljon a virtuális hálózata virtuális hálózati átjárójához. Például: **TestVNet1GW**. A virtuális hálózati átjáró oldalának megnyitásához kattintson a **TestVNet1GW** elemre.

  ![Kapcsolatok oldal](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/1to4connect2.png "Kapcsolatok oldal")
2. Kattintson a **+Hozzáadás** elemre a **Kapcsolat hozzáadása** oldal megnyitásához.

  ![Kapcsolat hozzáadása](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add.png "Kapcsolat hozzáadása")
3. A **Kapcsolat hozzáadása** oldalon, a név mezőben adja meg a kapcsolat nevét. Például: **TestVNet1toTestVNet4**.
4. A **Kapcsolat típusa** mezőben válassza a **Virtuális hálózatok közötti** lehetőséget a legördülő listából.
5. Az **Első virtuális hálózati átjáró**mező értéke automatikusan ki lesz töltve, mert egy megadott virtuális hálózati átjáróból hozza létre a kapcsolatot.
6. A **Második virtuális hálózati átjáró**mezőben annak a virtuális hálózatnak a virtuális hálózati átjáróját kell megadni, amelyikhez létre szeretné hozni a kapcsolatot. Kattintson a **Másik virtuális hálózati átjáró kiválasztása** elemre a **Virtuális hálózati átjáró kiválasztása** oldal megnyitásához.
7. Tekintse át az oldalon felsorolt virtuális hálózati átjárók listáját. Csak azok a virtuális hálózati átjárók jelennek meg, amelyek az előfizetésében szerepelnek. Ha olyan virtuális hálózati átjáróhoz szeretne kapcsolódni, amely nem szerepel az előfizetésében, olvassa el a [PowerShell-lel kapcsolatos cikket](vpn-gateway-vnet-vnet-rm-ps.md).
8. Kattintson arra a virtuális hálózati átjáróra, amelyhez csatlakozni kíván.
9. A **Megosztott kulcs** mezőben adja meg a kapcsolat megosztott kulcsát. A kulcsot generálhatja, vagy saját maga is létrehozhatja. Egy helyek közötti kapcsolat esetében a helyszíni eszközhöz és a virtuális hálózati átjáró kapcsolatához használt kulcs ugyanaz lenne. A fogalom itt is hasonló, azonban itt nem egy VPN-eszközhöz, hanem egy másik virtuális hálózati átjáróhoz való csatlakozásról van szó.
10. A módosítások mentéséhez kattintson az **OK** gombra a lap alján.

## <a name="TestVNet4Connection"></a>8. A TestVNet4 átjárókapcsolat konfigurálása
Ezután hozzon létre kapcsolatot a TestVNet4 felől a TestVNet1 felé. Keresse meg a TestVNet4 kapcsolattal társított virtuális hálózati átjárót a portálon. Kövesse az előző szakasz lépéseit, és cserélje le az értékeket a TestVNet4 felől a TestVNet1 felé mutató kapcsolat létrehozásához. Ügyeljen arra, hogy ugyanazt a megosztott kulcsot használja.

## <a name="VerifyConnection"></a>9. Kapcsolatok ellenőrzése

Keresse meg a virtuális hálózati átjárót a portálon. A virtuális hálózati átjáró oldalán kattintson a **Kapcsolatok** elemre a virtuális hálózati átjáró kapcsolatoldalának megtekintéséhez. Amikor a kapcsolat létrejött, az állapotértékek a **Sikeres** és a **Csatlakoztatva** értékre váltanak. Egy kapcsolatra duplán kattintva megnyithatja az **Alapvető szolgáltatások** oldalt, ahol további információkhoz juthat.

![Sikeres](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Sikeres")

Amikor elindul az adatok forgalma, láthatja a bejövő és a kimenő adatforgalom értékeit.

![Alapvető szolgáltatások](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Alapvető szolgáltatások")

## <a name="to-add-additional-connections"></a>További kapcsolatok hozzáadása

Ha további kapcsolatokat szeretne hozzáadni, keresse meg a virtuális hálózati átjárót, amelyből a kapcsolatot létre szeretné hozni, majd kattintson a **Kapcsolatok** elemre. Létrehozhat egy másik virtuális hálózatok közötti kapcsolatot, vagy létrehozhat IPsec helyek közötti kapcsolatot egy helyszíni helyhez. Módosítsa a **Kapcsolat típusát**, hogy az megfeleljen a létrehozni kívánt kapcsolattípusnak. További kapcsolatok létrehozása előtt ellenőrizze, hogy a virtuális hálózat címtere ne legyen átfedésben azokkal a címterekkel, amelyekhez csatlakozni szeretne. A helyek közötti kapcsolat létrehozásának lépéseiért lásd: [Helyek közötti kapcsolat létrehozása](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="faq"></a>Virtuális hálózatok közötti kapcsolat – gyakori kérdések
A virtuális hálózatok közötti kapcsolatokról további információt a gyakori kérdésekben talál.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>További lépések

A [Hálózati biztonság](../virtual-network/security-overview.md) című témakörben talál információt egy adott virtuális hálózatban található erőforrásokra irányuló hálózati forgalom korlátozásáról.

A [Virtuális hálózat forgalmának útválasztása](../virtual-network/virtual-networks-udr-overview.md) című témakörben talál információt arról, hogyan irányítja az Azure az Azure, a helyszíni és az internetes erőforrások közötti forgalom útválasztását.
