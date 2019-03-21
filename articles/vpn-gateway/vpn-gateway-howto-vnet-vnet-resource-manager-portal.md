---
title: A virtuális hálózatok közötti VPN gateway-kapcsolat konfigurálása az Azure portal használatával |} A Microsoft Docs
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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2018
ms.author: cherylmc
ms.openlocfilehash: 94b32595cf2c884ccfd1362f6c8d03f542aabfc5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090100"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>A virtuális hálózatok közötti VPN gateway-kapcsolat konfigurálása az Azure portal használatával

Ez a cikk a VNet – VNet kapcsolat típusát a hogyan lehet csatlakoztatni virtuális hálózatok (Vnetek). Virtuális hálózatok lehetnek különböző régiókban, és a különböző előfizetésekből. Amikor különböző előfizetésekről csatlakoztat virtuális hálózatokat kapcsolódik, az előfizetéseket nem kell társítható ugyanahhoz az Active Directory-bérlőhöz. 

![v2v ábra](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

A jelen cikkben ismertetett lépések az Azure Resource Manager-alapú üzemi modellre vonatkoznak, és az Azure Portalon. Az alábbi cikkekben leírt beállítások használatával hozhat létre ezt a konfigurációt más üzembehelyezési eszközzel vagy modellt:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Különböző üzemi modellek összekapcsolása – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Különböző üzemi modellek összekapcsolása – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>


## <a name="about-connecting-vnets"></a>Tudnivalók a virtuális hálózatok csatlakoztatása

A következő szakaszok ismertetik a virtuális hálózatok összekapcsolása a különböző módjait.

### <a name="vnet-to-vnet"></a>Virtuális hálózatok közötti kapcsolat

Egy egyszerű módja, virtuális hálózatok összekapcsolása virtuális hálózatok közötti kapcsolat konfigurálása. Egy másik virtuális hálózat egy virtuális hálózat csatlakoztatása egy VNet – VNet kapcsolat típusú történő összekapcsolása (VNet2VNet), ha nagyon hasonlít egy helyek közötti IPsec-kapcsolat a helyszíni helyre. Két kapcsolattípus egy VPN-átjárót használ biztonságos alagút kialakításához IPsec/IKE-, és ugyanúgy működik a kommunikáció során. Azonban eltérnek a módon a helyi hálózati átjáró van konfigurálva. 

A VNet – VNet kapcsolat létrehozásakor a helyi hálózati átjáró címtér az automatikusan létrehozott és kitölti a rendszer. Ha egy virtuális hálózat a címtér frissíti, a másik virtuális hálózat automatikusan továbbítja a frissített címtér. Fontos általában gyorsabb és egyszerűbb, mint a helyek közötti kapcsolat a virtuális hálózatok közötti kapcsolat létrehozásához.

### <a name="site-to-site-ipsec"></a>Helyek közötti kapcsolat (IPsec)

Ha egy összetett hálózati konfiguráció dolgozik, előfordulhat, hogy inkább csatlakozás a virtuális hálózatok használatával olyan [helyek közötti kapcsolat](vpn-gateway-howto-site-to-site-resource-manager-portal.md) helyette. Ha a helyek közötti IPsec lépésekkel hozzon létre, és manuálisan konfigurálhatja a helyi hálózati átjárókat. Az egyes virtuális hálózatok helyi hálózati átjárója helyi helyként kezeli a többi virtuális hálózatot. Ezeket a lépéseket adja meg a helyi hálózati átjáróhoz irányítsa a forgalmat, további címtereket adhat. Ha egy virtuális hálózat címtere megváltozik, manuálisan frissítenie kell a megfelelő helyi hálózati átjárót.

### <a name="vnet-peering"></a>Társviszony létesítése virtuális hálózatok között

A virtuális hálózatokat is csatlakoztathat a virtuális hálózatok közötti társviszony-létesítés. Virtuális hálózatok közötti társviszony nem használ VPN-átjárót, és más korlátozásokkal rendelkezik. Emellett a [virtuális hálózatok közötti társviszony díjszabásának](https://azure.microsoft.com/pricing/details/virtual-network) kiszámítása máshogy történik, mint a [virtuális hálózatok közötti VPN-átjáró](https://azure.microsoft.com/pricing/details/vpn-gateway) esetén. További információ: [Társviszony létesítése virtuális hálózatok között](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a>Miért érdemes létrehozni egy VNet – VNet kapcsolat?

Érdemes virtuális hálózatok összekapcsolása virtuális hálózatok közötti kapcsolat használatával a következő okok miatt:

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>Georedundancia és földrajzi jelenlét több régióban

  * Beállíthat saját georeplikációját vagy szinkronizálási biztonságos kapcsolattal rendelkező kapcsolaton át, internetes végpontok nélkül.
  * Az Azure Traffic Manager és az Azure Load Balancer beállíthat magas rendelkezésre állású munkaterhelést georedundanciával több Azure-régiók között. Például akkor állíthat be az SQL Server Always On rendelkezésre állási csoportok több Azure-régiók között.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>Regionális Többrétegű alkalmazások elkülönítéssel vagy felügyeleti határokat

  * Egy régión belül beállíthat többrétegű alkalmazásokat több, elkülönítéssel vagy felügyeleti követelményekkel miatt össze vannak kapcsolva virtuális hálózatokkal.

A virtuális hálózatok közötti kommunikáció kombinálható többhelyes konfigurációkkal. Ezen konfigurációk lehetővé teszi, hogy olyan hálózati topológiákat, amelyek létesítmények közötti kapcsolatokat a virtuális hálózatok közötti kapcsolatokkal, az alábbi ábrán látható módon:

![Tudnivalók a kapcsolatokról](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Tudnivalók a kapcsolatokról")

Ez a cikk bemutatja, hogyan virtuális hálózatok összekapcsolása a VNet – VNet kapcsolat típus használatával. Ha gyakorlatként kövesse az alábbi lépéseket, használhatja a következő példa beállításértékeket. A példában a virtuális hálózatok ugyanabban az előfizetésben, de különböző erőforráscsoportokban vannak. Ha a virtuális hálózatok különböző előfizetésekben találhatóak, nem hozhatja létre a kapcsolatot a portálon. Használat [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) vagy [CLI](vpn-gateway-howto-vnet-vnet-cli.md) helyette. A virtuális hálózatok közötti kapcsolatokról további információkért lásd: [VNet – VNet – gyakori kérdések](#vnet-to-vnet-faq).

### <a name="example-settings"></a>Példabeállítások

**Értékek a TestVNet1-hez:**

- **A virtuális hálózati beállítások**
    - **Név**: Adja meg *TestVNet1*.
    - **Címtér**: Adja meg *10.11.0.0/16*.
    - **Előfizetés**: Válassza ki a használni kívánt előfizetést.
    - **Erőforráscsoport**: Adja meg *TestRG1*.
    - **Hely**: Válassza az **USA keleti régiója** lehetőséget.
    - **Alhálózat**
        - **Név**: Adja meg *előtérbeli*.
        - **Címtartomány**: Adja meg *10.11.0.0/24*.
    - **Átjáró-alhálózat**:
        - **Név**: *Átjáró-alhálózat* autofilled van.
        - **Címtartomány**: Adja meg *10.11.255.0/27*.
    - **DNS-kiszolgáló**: Válassza ki **egyéni** , és adja meg a DNS-kiszolgáló IP-címét.

- **Virtuális hálózati átjáró beállításai** 
    - **Név**: Adja meg *TestVNet1GW*.
    - **Átjáró típusa**: Válassza ki **VPN**.
    - **VPN-típust**: Válassza ki **útvonalalapú**.
    - **TERMÉKVÁLTOZAT**: Válassza ki az átjáró használni kívánt Termékváltozatát.
    - **Nyilvános IP-cím neve**: Adja meg *TestVNet1GWIP*
    - **kapcsolat** 
       - **Név**: Adja meg *TestVNet1toTestVNet4*.
       - **Megosztott kulcs**: Adja meg *abc123*. Hozhat létre a megosztott kulcs saját magának. A virtuális hálózatok közötti kapcsolat létrehozásakor meg az értékeket meg kell egyeznie.

**Értékek a TestVNet4-hez:**

- **A virtuális hálózati beállítások**
   - **Név**: Adja meg *TestVNet4*.
   - **Címtér**: Adja meg *10.41.0.0/16*.
   - **Előfizetés**: Válassza ki a használni kívánt előfizetést.
   - **Erőforráscsoport**: Adja meg *TestRG4*.
   - **Hely**: Válassza ki **USA nyugati RÉGIÓJA**.
   - **Alhálózat** 
      - **Név**: Adja meg *előtérbeli*.
      - **Címtartomány**: Adja meg *10.41.0.0/24*.
   - **Átjáró-alhálózat** 
      - **Név**: *Átjáró-alhálózat* autofilled van.
      - **Címtartomány**: Adja meg *10.41.255.0/27*.
   - **DNS-kiszolgáló**: Válassza ki **egyéni** , és adja meg a DNS-kiszolgáló IP-címét.

- **Virtuális hálózati átjáró beállításai** 
    - **Név**: Adja meg *TestVNet4GW*.
    - **Átjáró típusa**: Válassza ki **VPN**.
    - **VPN-típust**: Válassza ki **útvonalalapú**.
    - **TERMÉKVÁLTOZAT**: Válassza ki az átjáró használni kívánt Termékváltozatát.
    - **Nyilvános IP-cím neve**: Adja meg *TestVNet4GWIP*.
    - **kapcsolat** 
       - **Név**: Adja meg *TestVNet4toTestVNet1*.
       - **Megosztott kulcs**: Adja meg *abc123*. Hozhat létre a megosztott kulcs saját magának. A virtuális hálózatok közötti kapcsolat létrehozásakor meg az értékeket meg kell egyeznie.

## <a name="create-and-configure-testvnet1"></a>A TestVNet1 létrehozása és konfigurálása
Ha már rendelkezik egy virtuális hálózattal, győződjön meg arról, hogy a beállításai kompatibilisek a VPN-átjáró kialakításával. Különösen ügyeljen az esetleges olyan alhálózatokra, amelyek átfedésbe kerülhetnek más hálózatokkal. A kapcsolat nem fog megfelelően működni, ha egymást átfedő alhálózatok. A virtuális hálózat konfigurálása után a megfelelő beállításokkal, elkezdheti a adjon meg egy DNS-kiszolgáló szakasz lépéseit.

### <a name="to-create-a-virtual-network"></a>Virtuális hálózat létrehozása
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="add-additional-address-space-and-create-subnets"></a>További címterek hozzáadása és alhálózatok létrehozása
Miután létrehozta a virtuális hálózatot, további címtereket adhat hozzá és alhálózatokat hozhat létre.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="create-a-gateway-subnet"></a>Átjáróalhálózat létrehozása
Mielőtt virtuális hálózati átjárót hozna létre a virtuális hálózathoz, létre kell hoznia az átjáróalhálózatot. Az átjáróalhálózat tartalmazza a virtuális hálózati átjáró által használt IP-címeket. Ha lehetséges érdemes egy átjáróalhálózat létrehozása/28-as vagy/27 CIDR-blokk használatával elegendő IP-címet, hogy megfeleljen a jövőbeli további konfigurációs követelmények számára.

Ha ezt a konfigurációt, gyakorlatként próbál létrehozni, tekintse meg ezeket [példabeállításokat](#example-settings) az átjáróalhálózat létrehozásakor.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Átjáróalhálózat létrehozása
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="specify-a-dns-server-optional"></a>DNS-kiszolgáló megadása (nem kötelező)
DNS nem szükséges a VNet – VNet kapcsolatokhoz. Ha szeretné a virtuális hálózaton üzembe helyezett erőforrások a névfeloldás, azonban DNS-kiszolgáló megadása. Ezzel a beállítással megadhatja azt a DNS-kiszolgálót, amelyet névfeloldásra kíván használni ennél a virtuális hálózatnál. DNS-kiszolgáló nem hoz létre.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="create-a-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása
Ebben a lépésben a virtuális hálózat virtuális hálózati átjáróját fogja létrehozni. Az átjáró létrehozása akár 45 percet is igénybe vehet, az átjáró kiválasztott termékváltozatától függően. Ha ezt a konfigurációt, gyakorlatként próbál létrehozni, tekintse meg a [példabeállításokat](#example-settings).

### <a name="to-create-a-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="create-and-configure-testvnet4"></a>A TestVNet4 létrehozása és konfigurálása
Miután konfigurálta a testvnet1-et, hozzon létre a TestVNet4 ismétlődő az előző lépést, és az értékeket a TestVNet4 értékeket cserélje le. Nem kell várnia, amíg a testvnet1 virtuális hálózati átjáró létrehozása a TestVNet4 konfigurálása előtt befejeződött. Ha a saját értékeit használja, ellenőrizze a címterek nincsenek átfedésben azokkal a virtuális hálózat, amelyhez szeretné csatlakoztatni.

## <a name="configure-the-testvnet1-gateway-connection"></a>A TestVNet1 átjárókapcsolat konfigurálása
Miután mind a TestVNet1, mind a TestVNet4 virtuális hálózati átjárójának létrehozása befejeződött, létrehozhatja a virtuális hálózati átjárókapcsolatokat. Ebben a szakaszban létrehozza a kapcsolatot a VNet1 felől a VNet4 felé. Ezek a lépések csak egyazon előfizetésben lévő virtuális hálózatokkal működnek. Ha a virtuális hálózatok különböző előfizetésekben találhatóak, kell használnia a [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) hozhatja létre a kapcsolatot. Azonban ha a virtuális hálózatok eltérő erőforráscsoportokban ugyanabban az előfizetésben, csatlakoztathatja őket a portál használatával.

1. Az Azure Portalon válassza ki a **összes erőforrás**, adja meg *virtuális hálózati átjáró* keresési mezőbe, majd navigáljon arra a virtuális hálózati átjáró a virtuális hálózat számára. Például: **TestVNet1GW**. Ezt választva nyithatja meg a **virtuális hálózati átjáró** lapot.

   ![Kapcsolatok oldal](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/1to4connect2.png "Kapcsolatok oldal")
2. Alatt **beállítások**, jelölje be **kapcsolatok**, majd válassza ki **Hozzáadás** megnyitásához a **kapcsolat hozzáadása** lap.

   ![Kapcsolat hozzáadása](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add.png "Kapcsolat hozzáadása")
3. Az a **kapcsolat hozzáadása** lap, adja meg a kapcsolathoz tartozó értékeket:

   - **Név**: Adja meg a kapcsolat nevét. Például: *TestVNet1toTestVNet4*.

   - **Kapcsolat típusa**: Válassza ki **VNet – VNet** a legördülő listából.

   - **Első virtuális hálózati átjáró**: Ez a mező értéke automatikusan kitölti a rendszer, mert a megadott virtuális hálózati átjáró ezt a kapcsolatot hoz létre.

   - **Második virtuális hálózati átjáró**: Ez a mező a virtuális hálózattal, hozzon létre egy kapcsolatot szeretne a virtuális hálózati átjáróját. Válassza ki **egy másik virtuális hálózati átjáró kiválasztása** megnyitásához a **virtuális hálózati átjáró kiválasztása** lapot.

     - Tekintse át az oldalon felsorolt virtuális hálózati átjárók listáját. Csak azok a virtuális hálózati átjárók jelennek meg, amelyek az előfizetésében szerepelnek. Ha szeretne kapcsolódni egy virtuális hálózati átjáró, amely nem az előfizetésében, használja a [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

     - Válassza ki a virtuális hálózati átjárót, amelyhez szeretné csatlakoztatni.

     - **Megosztott kulcs (PSK)**: Ebben a mezőben adjon meg egy megosztott kulcsot a kapcsolat. A kulcsot generálhatja, vagy saját maga is létrehozhatja. Helyek közötti kapcsolaton keresztül a kulcs megegyezik a helyszíni eszközhöz és a virtuális hálózati átjáró kapcsolatának. A fogalom itt is hasonló, kivéve, hanem egy VPN-eszközre, a másik virtuális hálózati átjáró csatlakozik.
    
4. Válassza ki **OK** a módosítások mentéséhez.

## <a name="configure-the-testvnet4-gateway-connection"></a>A TestVNet4 átjárókapcsolat konfigurálása
Ezután hozzon létre kapcsolatot a TestVNet4 felől a TestVNet1 felé. Keresse meg a TestVNet4 kapcsolattal társított virtuális hálózati átjárót a portálon. Kövesse az előző szakasz lépéseit, és cserélje le az értékeket a TestVNet4 felől a TestVNet1 felé mutató kapcsolat létrehozásához. Ügyeljen arra, hogy ugyanazt a megosztott kulcsot használja.

## <a name="verify-your-connections"></a>Kapcsolatok ellenőrzése

Az Azure Portalon keresse meg a virtuális hálózati átjárót. Az a **virtuális hálózati átjáró** lapon jelölje be **kapcsolatok** megtekintéséhez a **kapcsolatok** a virtuális hálózati átjáró lapját. Miután a kapcsolat létrejött, megjelenik a **állapot** értékeket módosítsa **sikeres** és **csatlakoztatva**. Jelöljön ki egy kapcsolatot, nyissa meg a **Essentials** oldalt, ahol további információt.

![Sikeres](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Sikeres")

Amikor elindul az adatok tárfiókba, látni fogja a értékeit **adatok** és **adatforgalom**.

![Alapvető szolgáltatások](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Alapvető szolgáltatások")

## <a name="add-additional-connections"></a>További kapcsolatok hozzáadása

Ha szeretne további kapcsolatok hozzáadása, lépjen a virtuális hálózati átjáró, amelyről szeretné létrehozni a kapcsolatot, majd válassza ki **kapcsolatok**. Létrehozhat egy másik virtuális hálózatok közötti kapcsolatot, vagy létrehozhat IPsec helyek közötti kapcsolatot egy helyszíni helyhez. Módosítsa a **Kapcsolat típusát**, hogy az megfeleljen a létrehozni kívánt kapcsolattípusnak. Mielőtt további kapcsolatokat hoz létre, győződjön meg arról, hogy a cím a virtuális hálózat nem átfedésben a címterekkel, amelyhez csatlakozni kíván. A helyek közötti kapcsolat létrehozásának lépéseiért lásd: [Helyek közötti kapcsolat létrehozása](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="vnet-to-vnet-faq"></a>Virtuális hálózatok közötti kapcsolat – gyakori kérdések
A virtuális hálózatok közötti kapcsolatokról további információt a gyakori kérdésekben talál.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>További lépések

További információ a virtuális hálózatban lévő erőforrásokra irányuló hálózati forgalom korlátozásáról: [hálózati biztonság](../virtual-network/security-overview.md).

Információ arról, hogyan irányítja az Azure az Azure, a helyszíni és az internetes erőforrások közötti forgalom útválasztását: [Virtuális hálózat forgalmának útválasztása](../virtual-network/virtual-networks-udr-overview.md).
