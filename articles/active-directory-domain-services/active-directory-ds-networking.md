---
title: 'Azure AD tartományi szolgáltatások: Hálózat irányelvek |} Microsoft Docs'
description: Azure Active Directory tartományi szolgáltatások hálózati szempontjai
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: maheshu
ms.openlocfilehash: bb69c217c1038a66333e65629023357e5854d242
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265043"
---
# <a name="networking-considerations-for-azure-ad-domain-services"></a>Azure AD tartományi szolgáltatások hálózati szempontjai
## <a name="how-to-select-an-azure-virtual-network"></a>Egy Azure virtuális hálózat kiválasztása
A következő irányelvek segítségével válassza ki a virtuális hálózatot az Azure AD tartományi szolgáltatások használatára.

### <a name="type-of-azure-virtual-network"></a>Az Azure virtuális hálózat típusát
* **Erőforrás-kezelő virtuális hálózatok**: Azure AD tartományi szolgáltatásokat Azure Resource Manager használatával létrehozott virtuális hálózatok engedélyezhető.
* A klasszikus Azure virtuális hálózatot az Azure AD tartományi szolgáltatások nem engedélyezhető.
* Más virtuális hálózatokhoz csatlakozhat a virtuális hálózat, amelyen engedélyezve van az Azure AD tartományi szolgáltatások. További információkért lásd: a [hálózati kapcsolatra](active-directory-ds-networking.md#network-connectivity) szakasz.

### <a name="azure-region-for-the-virtual-network"></a>A virtuális hálózat az Azure-régió
* A felügyelt tartományra telepítve van a azonos Azure-régió, ahol a virtuális hálózatot az Azure AD tartományi szolgáltatások úgy dönt, hogy engedélyezze a szolgáltatást.
* Válasszon egy virtuális hálózatot az Azure AD tartományi szolgáltatások által támogatott Azure-régióba.
* [Az Azure régiói](https://azure.microsoft.com/regions/#services/) lapon találja azoknak az Azure-régióknak a felsorolását, amelyekben elérhetők az Azure AD tartományi szolgáltatások.

### <a name="requirements-for-the-virtual-network"></a>A virtuális hálózati követelményei
* **Az Azure munkaterhelések közelében**: válassza ki a virtuális hálózatot, amely jelenleg futtatja/fogja tárolni az Azure AD tartományi szolgáltatások elérését igénylő virtuális gépektől. Ha egy másik virtuális hálózatot, mint a felügyelt tartományra a munkaterhelések vannak telepítve, előfordulhat, hogy meg csatlakozni a virtuális hálózatok.
* **Egyéni/bring-a-saját DNS-kiszolgálók**: Győződjön meg arról, hogy nincsenek-e nincsenek egyéni DNS-kiszolgálók konfigurálva a virtuális hálózat. Egy egyéni DNS-kiszolgáló például egy Windows Server virtuális gépen központilag telepített virtuális hálózaton futó Windows Server DNS példánya. Azure AD tartományi szolgáltatások nem integrálható a virtuális hálózaton belül telepített egyéni DNS-kiszolgálók.
* **Az azonos nevű tartomány meglévő tartományok**: Győződjön meg arról, hogy nem rendelkezik egy meglévő tartományhoz, a tartomány néven érhető el a virtuális hálózat. Tegyük fel például, hogy a kiválasztott virtuális hálózatban már van egy contoso.com nevű tartomány. Később akkor próbálja meg engedélyezni a ugyanazon tartomány nevét (az "contoso.com") a virtuális hálózat az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz. Azure AD tartományi szolgáltatások engedélyezése közben hibát tapasztal. Ez a hiba a tartománynév, a virtuális hálózatban tapasztalható névütközés okozza. Ebben az esetben az Azure AD tartományi szolgáltatások által kezelt tartomány beállításához másik nevet kell használnia. A másik lehetséges megoldás az, hogy leépíti a meglévő tartományt, majd ezután folytatja a Azure AD tartományi szolgáltatások engedélyezését.

> [!WARNING]
> Tartományi szolgáltatások nem helyezhető át egy másik virtuális hálózathoz, a szolgáltatás engedélyezése után.
>
>


## <a name="guidelines-for-choosing-a-subnet"></a>Egy alhálózat kiválasztására vonatkozó irányelvek

![Ajánlott alhálózat kialakítása](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

* Az Azure AD tartományi szolgáltatások telepítése a **dedikált alhálózati külön** Azure virtuális hálózaton belül.
* Az NSG-k nem vonatkoznak a dedikált IP-alhálózatot a felügyelt tartományok. Ha a dedikált alhálózati NSG-ket kell alkalmaznia, győződjön meg arról, **nem blokkolja a szolgáltatás szükséges portokat, és a tartomány kezelése**.
* Nem túlságosan korlátozhatja a felügyelt tartományok dedikált alhálózaton belül elérhető IP-címek számát. Ez a korlátozás miatt a szolgáltatás elérhetővé teszi két tartományvezérlő a felügyelt tartományok.
* **Ne engedélyezze az átjáró alhálózatának az Azure AD tartományi szolgáltatások** a virtuális hálózat.
* Ne tiltsák le az alhálózat, amelyben engedélyezve van a felügyelt tartományok kimenő hozzáférést.

> [!WARNING]
> Amikor társít egy NSG-t egy alhálózattal, amelyben az Azure AD tartományi szolgáltatások engedélyezve van, a szolgáltatást, és a tartomány kezelése a Microsoft képes zavart okozhat. Emellett az Azure AD-bérlő és a felügyelt tartományok közötti szinkronizálás megszakad. **Az SLA nem vonatkozik a központi telepítések, ahol az NSG telepítve van, amely blokkolja az Azure AD tartományi szolgáltatások frissítése és a tartomány kezelése.**
>
>

## <a name="ports-required-for-azure-ad-domain-services"></a>Az Azure AD tartományi szolgáltatásokhoz szükséges portok
A következő portokat Azure AD tartományi szolgáltatások szolgáltatáshoz szükséges, és a felügyelt tartományok karbantartása. Győződjön meg arról, hogy ezek a portok nincsenek letiltva az alhálózat, ahol a felügyelt tartományok engedélyezett.

| Portszám | Kötelező? | Cél |
| --- | --- | --- |
| 443 | Kötelező |Az Azure AD-bérlő-szinkronizálás |
| 5986 | Kötelező | A tartomány kezelése |
| 3389 | Kötelező | A tartomány kezelése |
| 636 | Optional | Biztonságos LDAP (LDAPS) hozzáféréssel a felügyelt tartományhoz |

**Port 443-as (szinkronizálási Azure AD-val)**
* Az Azure AD-címtár szinkronizálja a felügyelt tartományok szolgál.
* Akkor kötelező, hogy férjen hozzá az NSG-t a porton. Ehhez a porthoz való hozzáférés, nélkül a felügyelt tartomány nincs szinkronban vannak az Azure AD-címtár. Lehet, hogy felhasználók nem jelentkezhetnek be, mivel a jelszavuk módosításai nincsenek szinkronizálva. a felügyelt tartományra.
* Ezt a portot a Azure IP-címtartományba tartozó IP-címek befelé korlátozhatja. Vegye figyelembe, hogy az Azure IP-címtartomány különböző széles, mint a PowerShell-tartomány, az alábbi szabály látható.

**Port 5986-os (PowerShell-távelérés)**
* PowerShell távoli eljáráshívás segítségével a felügyelt tartományok felügyeleti feladatok elvégzésére szolgál.
* Kötelező a az NSG-t a porton keresztüli hozzáférést is. Ehhez a porthoz való hozzáférés, nélkül a felügyelt tartományok frissített, konfigurált, a biztonsági mentésben vagy figyelt nem lehet.
* Új tartományok és tartományok egy ARM virtuális hálózattal, korlátozhatja befelé ezt a portot, a következő forrás IP-címek: 52.180.179.108, 52.180.177.87, 13.75.105.168, 52.175.18.134, 52.138.68.41, 52.138.65.157, 104.41.159.212, 104.45.138.161, 52.169.125.119, 52.169.218.0, 52.187.19.1, 52.187.120.237, 13.78.172.246, 52.161.110.169, 52.174.189.149, 40.68.160.142, 40.83.144.56, 13.64.151.161, 52.180.183.67, 52.180.181.39, 52.175.28.111, 52.175.16.141, 52.138.70.93, 52.138.64.115, 40.80.146.22, 40.121.211.60, 52.138.143.173, 52.169.87.10, 13.76.171.84, 52.187.169.156, 13.78.174.255, 13.78.191.178, 40.68.163.143, 23.100.14.28, 13.64.188.43, 23.99.93.197
* A klasszikus virtuális hálózatot rendelkező tartományokhoz, korlátozhatja befelé ezt a portot, a következő forrás IP-címek: 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161
* A tartományvezérlők, a felügyelt tartományok általában figyelni a porton. A szolgáltatás megnyitja ezt a portot, a felügyelt tartományvezérlőkön, csak akkor, ha egy felügyeleti vagy karbantartási művelet kell végezhető el a felügyelt tartományra. Amint a művelet befejeződik, a szolgáltatás leáll ezt a portot, a felügyelt tartományvezérlőkön.

**3389-es (távoli asztali verziók)**
* A távoli asztali kapcsolatokat a felügyelt tartományok tartományvezérlők szolgál.
* Bejövő hozzáférés korlátozása a következő forrás IP-címek: 207.68.190.32/27, 13.106.78.32/27, 13.106.174.32/27, 13.106.4.96/27
* Ez a port is nagy mértékben ki van kapcsolva a felügyelt tartomány marad. A módszer nem használata folyamatos, mivel a kezelési és figyelési feladatok végrehajtása használatával történik PowerShell távoli eljáráshívás. Csak az esemény ritkán fordul elő, amelyet a Microsoft számára a felügyelt tartományok speciális hibaelhárítás távolról történő csatlakozást ezt a portot használja. A port zárva van, amint a hibaelhárítási művelet be nem fejeződött.

**Port a 636 (biztonságos LDAP)**
* Biztonságos LDAP hozzáférés engedélyezése a felügyelt tartományra az interneten keresztül szolgál.
* Ezen keresztül a NSG port megnyitása nem kötelező megadni. Nyissa meg a portot, csak ha hozzáfér biztonságos LDAP engedélyezve az interneten keresztül.
* Ezt a portot a forrás IP-címek biztonságos LDAP keresztül csatlakozó várhatóan befelé korlátozhatja.

**Kimenő hozzáférést** AAD tartományi szolgáltatásokra különböző más Azure-szolgáltatásokhoz való ahhoz, hogy a kezelése, biztonsági mentését, és figyelheti a felügyelt tartományok kimenő engedéllyel kell rendelkeznie. Ne blokkolják a dedikált alhálózatból, ahol a felügyelt tartományok engedélyezett kimenő hozzáférést.


## <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)
A [hálózati biztonsági csoport (NSG)](../virtual-network/security-overview.md) hozzáférés-vezérlési lista (ACL) szabályokat, amelyek engedélyezik vagy megtagadják a Virtuálisgép-példány egy virtuális hálózat hálózati forgalmának listáját tartalmazza. Az NSG-ket alhálózatokhoz vagy az alhálózaton belüli virtuálisgép-példányokhoz lehet hozzárendelni. Ha az NSG-t hozzárendelik egy alhálózathoz, az ACL-szabályok érvényesek lesznek az alhálózatban lévő összes virtuálisgép-példányra. Emellett az egyes virtuális gép is lehet korlátozni további korlátozásokat NGS társítása közvetlenül a virtuális gép.

### <a name="sample-nsg-for-virtual-networks-with-azure-ad-domain-services"></a>NSG a minta virtuális hálózatok az Azure AD tartományi szolgáltatások
Az alábbi táblázat mutatja be egy minta NSG-t is konfigurálhatja a virtuális hálózat az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz. Ez a szabály lehetővé teszi a bejövő forgalom a felügyelt tartományra marad biztosításához szükséges portokon keresztül lett, frissítése és a Microsoft által is figyelhetők. Minden bejövő forgalom az internetről az alapértelmezett "DenyAll" szabály vonatkozik.

Emellett az NSG-t is bemutatja, hogyan biztonságos LDAP hozzáférést zárolását az interneten keresztül. A szabály mellőzése, ha nem biztonságos LDAP access számára engedélyezett a felügyelt tartományok az interneten keresztül. Az NSG tartalmaz egy TCP-porton keresztül 636 csak egy megadott készletből IP-címek bejövő LDAPS hozzáférést engedélyező szabályokat. Az NSG-szabály LDAPS hozzáférést a megadott IP-címeket az interneten keresztül rendelkezik, magasabb prioritású, mint a DenyAll NSG-szabály.

![Minta NSG LDAPS hozzáférés biztonságossá az interneten keresztül](.\media\active-directory-domain-services-alerts\default-nsg.png)

**További információ** - [hálózati biztonsági csoport létrehozása](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).


## <a name="network-connectivity"></a>Hálózati kapcsolat
Az Azure-ban egy virtuális hálózaton belül csak akkor engedélyezhető az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz.

### <a name="scenarios-for-connecting-azure-networks"></a>Csatlakozás Azure-hálózatok forgatókönyvei
Csatlakozás az Azure virtuális hálózatok a felügyelt tartományra, sem a következő telepítési helyzetekben:

#### <a name="use-the-managed-domain-in-more-than-one-azure-virtual-network"></a>A felügyelt tartományra egynél több Azure virtuális hálózat használata
Egyéb Azure virtuális hálózatokhoz csatlakozhat, amelyben engedélyezte az Azure AD tartományi szolgáltatásokat az Azure virtuális hálózat. A VPN/Vnetben társviszony-létesítési kapcsolat lehetővé teszi a felügyelt tartományra használ a többi virtuális hálózatok üzembe helyezett munkaterhelések tekintetében.

![Klasszikus virtuális hálózati kapcsolat](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>A felügyelt tartományra Resource Manager-alapú virtuális hálózat használata
Az Azure klasszikus virtuális hálózatot, amelyben engedélyezte az Azure AD tartományi szolgáltatásokat a Resource Manager-alapú virtuális hálózat csatlakozhat. Ez a kapcsolat lehetővé teszi a felügyelt tartományra használata a Resource Manager-alapú virtuális hálózatban üzembe helyezett munkaterhelések tekintetében.

![Erőforrás-kezelő klasszikus virtuális hálózati kapcsolat](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)

### <a name="network-connection-options"></a>A hálózati kapcsolati beállítások
* **VNet – VNet kapcsolatokhoz használó virtuális hálózati társviszony-létesítés**: virtuális hálózati társviszony-létesítés egy olyan mechanizmus, amely összeköti a két virtuális hálózat ugyanabban a régióban a Azure gerincét a hálózaton keresztül. A társviszony kialakítását követően a két virtuális hálózat a csatlakozás minden szempontjából egyetlen hálózatnak látszik. A kezelésük továbbra is külön erőforrásként történik, de az ezekbe a virtuális hálózatokba tartozó virtuális gépek közvetlenül, magánhálózati IP-címekkel kommunikálhatnak egymással.

    ![Virtuális hálózati kapcsolat használata a társviszony-létesítés](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [További információ – a virtuális hálózati társviszony-létesítés](../virtual-network/virtual-network-peering-overview.md)

* **Pont-pont VPN-kapcsolattal VNet – VNet kapcsolatokhoz**: Csatlakozás a virtuális hálózat egy másik virtuális hálózathoz (VNet – VNet) hasonlít egy virtuális hálózathoz csatlakozik egy helyszíni hely. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához.

    ![Virtuális hálózati kapcsolat segítségével a VPN-átjáró](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [További információ – segítségével a VPN-átjáró virtuális hálózatok csatlakoztatása](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

<br>

## <a name="related-content"></a>Kapcsolódó tartalom
* [Az Azure virtuális hálózati társviszony-létesítés](../virtual-network/virtual-network-peering-overview.md)
* [A klasszikus üzembe helyezési modell VNet – VNet kapcsolat konfigurálása](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)
* [Az Azure hálózati biztonsági csoportok](../virtual-network/security-overview.md)
* [Hálózati biztonsági csoport létrehozása](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)
