---
title: 'Az Azure AD tartományi szolgáltatások: Hálózati útmutató |} A Microsoft Docs'
description: Hálózati megfontolások az Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/01/2017
ms.author: ergreenl
ms.openlocfilehash: c37dfecf7204f3f8d050c0f36d4c32ea02477f75
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54851862"
---
# <a name="networking-considerations-for-azure-ad-domain-services"></a>Hálózati megfontolások az Azure AD tartományi szolgáltatások
## <a name="how-to-select-an-azure-virtual-network"></a>Az Azure virtuális hálózat kiválasztása
Az alábbi irányelvek segítenek válassza ki egy virtuális hálózatot az Azure AD tartományi szolgáltatásokkal együtt használja.

### <a name="type-of-azure-virtual-network"></a>Az Azure virtuális hálózat típusa
* **Resource Manager virtuális hálózatok**: Az Azure AD tartományi szolgáltatásokat az Azure Resource Managerrel létrehozott virtuális hálózatok engedélyezhető.
* Az Azure klasszikus virtuális hálózat az Azure AD Domain Services nem engedélyezhető.
* Más virtuális hálózatokhoz csatlakozhat a virtuális hálózatot, amelyben az Azure AD tartományi szolgáltatások engedélyezve van. További információkért lásd: a [hálózati kapcsolat](active-directory-ds-networking.md#network-connectivity) szakaszban.

### <a name="azure-region-for-the-virtual-network"></a>A virtuális hálózat Azure-régió
* Az Azure AD Domain Services felügyelt tartomány a virtuális hálózat és az Azure ugyanabban a régióban van üzembe helyezve úgy dönt, hogy a szolgáltatás engedélyezése a.
* Válasszon egy virtuális hálózatot az Azure AD tartományi szolgáltatások által támogatott Azure-régióba.
* [Az Azure régiói](https://azure.microsoft.com/regions/#services/) lapon találja azoknak az Azure-régióknak a felsorolását, amelyekben elérhetők az Azure AD tartományi szolgáltatások.

### <a name="requirements-for-the-virtual-network"></a>A virtuális hálózat követelményei
* **Az Azure-beli számítási közelségi**: Válassza ki azt a virtuális hálózatot, amelyben jelenleg üzemelnek/üzemelni fognak azok a virtuális gépek, amelyeknek hozzá kell férniük az Azure AD tartományi szolgáltatásokhoz. Ha a számítási feladatokat egy másik virtuális hálózatban, mint a felügyelt tartomány van telepítve, előfordulhat, hogy meg a virtuális hálózatok összekapcsolása.
* **DNS-kiszolgálók egyéni és használata saját**: Győződjön meg arról, hogy nincsenek-e nincs egyéni DNS-kiszolgálók konfigurálva a virtuális hálózat. Egy példa egy egyéni DNS-kiszolgáló, egy Windows Server DNS egy Windows Server virtuális Gépet üzembe helyezett virtuális hálózatban futó példányát. Az Azure AD Domain Services és a virtuális hálózaton belül üzembe helyezett egyéni DNS-kiszolgálók nem integrálható.
* **Létező tartományok az azonos tartománynévvel**: Győződjön meg arról, hogy nem kell ugyanazon a néven érhető el, hogy a virtuális hálózatban a meglévő tartományhoz. Tegyük fel például, hogy a kiválasztott virtuális hálózatban már van egy contoso.com nevű tartomány. Később akkor próbálja meg engedélyezni a ugyanazon tartomány nevét (Ez a "contoso.com") a virtuális hálózat az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz. Az Azure AD tartományi szolgáltatások engedélyezése közben hibát tapasztal. Ez a hiba a tartomány nevét, hogy a virtuális hálózatban tapasztalható névütközés okozza. Ebben az esetben az Azure AD tartományi szolgáltatások által kezelt tartomány beállításához másik nevet kell használnia. A másik lehetséges megoldás az, hogy leépíti a meglévő tartományt, majd ezután folytatja a Azure AD tartományi szolgáltatások engedélyezését.

> [!WARNING]
> Tartományi szolgáltatások nem helyezhető át egy másik virtuális hálózatot, a szolgáltatás engedélyezése után.
>
>


## <a name="guidelines-for-choosing-a-subnet"></a>Egy alhálózat kiválasztására vonatkozó irányelvek

![Ajánlott alhálózat kialakítása](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

* Az Azure AD tartományi szolgáltatások üzembe helyezése egy **külön dedikált alhálózatán** Azure virtuális hálózaton belül.
* Az NSG-k nem vonatkoznak a felügyelt tartomány a kijelölt alhálózatot. Ha a kijelölt alhálózatot kell alkalmaznia az NSG-k, ügyeljen arra, hogy **ne tiltsa le a szolgáltatáshoz szükséges portokat és a tartomány kezelése**.
* Nem túlságosan korlátozzák a felügyelt tartományhoz tartozó dedikált alhálózaton elérhető IP-címek számát. Ez a korlátozás megakadályozható, hogy a szolgáltatás két tartományvezérlő elérhető a felügyelt tartományhoz.
* **Ne engedélyezze az Azure AD tartományi szolgáltatásokat az átjáró-alhálózat** a virtuális hálózat.

> [!WARNING]
> Amikor társít egy NSG-t egy alhálózathoz, amelyben az Azure AD tartományi szolgáltatások engedélyezve van, szolgáltatást, és a tartomány kezelése a Microsoft képes zavart okozhat. Emellett az Azure AD-bérlő és a felügyelt tartomány közötti szinkronizálásra megszakad. **Az SLA nem vonatkozik a központi telepítések, ahol az NSG-KET telepítve van, amely blokkolja az Azure AD tartományi szolgáltatások frissítése és a tartomány kezelése.**
>
>

## <a name="ports-required-for-azure-ad-domain-services"></a>Az Azure AD tartományi szolgáltatásokhoz szükséges portok
A következő portokat szükségesek az Azure AD Domain Services szolgáltatáshoz, és a felügyelt tartomány karbantartása. Győződjön meg arról, hogy ezeket a portokat nem blokkolja az alhálózat, amelyiken engedélyezte a felügyelt tartományra.

| Portszám | Kötelező? | Cél |
| --- | --- | --- |
| 443 | Kötelező |Az Azure AD-bérlőhöz való szinkronizálás |
| 5986 | Kötelező | A tartomány kezelése |
| 3389 | Kötelező | A tartomány kezelése |
| 636 | Optional | Biztonságos LDAP (LDAPS) elérését a felügyelt tartományhoz |

**443-as (szinkronizálás az Azure ad-vel)**
* Az Azure AD-címtár szinkronizálása a felügyelt tartomány szolgál.
* Fontos, hogy az NSG-ben a porthoz való hozzáférés megadása kötelező. A porthoz való hozzáférés nélkül a felügyelt tartomány nem áll az Azure AD-címtár szinkronban. Felhasználók nem tudnak bejelentkezni, a jelszavukat a módosításokat a rendszer nem szinkronizálja a felügyelt tartomány.
* Ezt a portot, az Azure IP-címtartományt tartozó IP-címekre bejövő hozzáférést korlátozhatja. Vegye figyelembe, hogy az Azure IP-címtartományt, mint a a szabályt alább látható PowerShell tartomány másfajta tartományt.

**Port 5986-os (PowerShell-táveléréssel)**
* PowerShell távoli eljáráshívás segítségével a felügyelt tartományra a felügyeleti feladatok elvégzésére szolgál.
* A kötelező hozzáférés engedélyezése ezen a porton keresztül az NSG-ben. A porthoz való hozzáférés nélkül frissített, konfigurált, a biztonsági mentésben vagy figyelt nem lehet a felügyelt tartományra.
* Bármilyen új tartományok és a egy Azure Resource Managerbeli virtuális hálózat tartományok ezt a portot, a következő forrás IP-címekre bejövő hozzáférést korlátozhatja: 52.180.179.108, 52.180.177.87, 13.75.105.168, 52.175.18.134, 52.138.68.41, 52.138.65.157, 104.41.159.212, 104.45.138.161, 52.169.125.119, 52.169.218.0, 52.187.19.1, 52.187.120.237, 13.78.172.246, 52.161.110.169, 52.174.189.149, 40.68.160.142, 40.83.144.56, 13.64.151.161, 52.180.183.67, 52.180.181.39, 52.175.28.111, 52.175.16.141, 52.138.70.93, 52.138.64.115, 40.80.146.22, 40.121.211.60, 52.138.143.173, 52.169.87.10, 13.76.171.84, 52.187.169.156, 13.78.174.255, 13.78.191.178, 40.68.163.143, 23.100.14.28, 13.64.188.43, 23.99.93.197
* A tartományok és a klasszikus virtuális hálózat ezt a portot, a következő forrás IP-címekre bejövő hozzáférést korlátozhatja: 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209
* A tartományvezérlők, a felügyelt tartományhoz tartozó általában ne figyeljen a porton. A szolgáltatás megnyitja ezt a portot, a felügyelt tartományvezérlőkön, csak akkor, ha egy felügyeleti vagy karbantartási művelet hajtható végre, a felügyelt tartományhoz tartozó kell. Amint a művelet befejeződik, a szolgáltatás leállítja ezt a portot, a felügyelt tartományvezérlőkön.

**A 3389-es (távoli asztali verzió)**
* Távoli asztali kapcsolatokat a felügyelt tartományhoz tartozó tartományvezérlőket használatos.
* Bejövő hozzáférést korlátozhatja a forrás IP-címek: 207.68.190.32/27, 13.106.78.32/27, 13.106.174.32/27, 13.106.4.96/27
* Ez a port is nagymértékben ki van kapcsolva a felügyelt tartomány marad. Ezt a mechanizmust nem használja folyamatosan, mivel a felügyeleti és monitorozási feladatokat végzik, PowerShell-táveléréssel történő. Csak az esemény ritkán fordul elő, amelyet a Microsoft távolról csatlakozhat a felügyelt tartományra a speciális hibaelhárítás ezt a portot használja. A port le van zárva, amint a hibaelhárítási művelet befejeződik.

**Port a 636-os (a Secure LDAP)**
* A felügyelt tartomány secure LDAP-hozzáférését engedélyezi az interneten keresztül szolgál.
* Nyissa meg az ezen a porton keresztül az NSG-t nem kötelező. Nyissa meg a portot, csak akkor, ha a secure LDAP-hozzáférését az interneten, ha engedélyezve van.
* Ezen a porton keresztül a secure LDAP várhatóan forrás IP-címekre bejövő hozzáférést korlátozhatja.


## <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)
A [hálózati biztonsági csoport (NSG)](../virtual-network/virtual-networks-nsg.md) tartalmaz, amelyek engedélyezik vagy megtagadják a hálózati forgalmat a virtuális hálózatban futó Virtuálisgép-példányokat hozzáférés-vezérlési lista (ACL) szabályok listáját. Az NSG-ket alhálózatokhoz vagy az alhálózaton belüli virtuálisgép-példányokhoz lehet hozzárendelni. Ha az NSG-t hozzárendelik egy alhálózathoz, az ACL-szabályok érvényesek lesznek az alhálózatban lévő összes virtuálisgép-példányra. Emellett a forgalmat egy adott virtuális gépre is lehet korlátozni további korlátozásokat egy NSG-t közvetlenül a virtuális Gépre.

### <a name="sample-nsg-for-virtual-networks-with-azure-ad-domain-services"></a>Minta NSG-t a virtuális hálózatok az Azure AD tartományi szolgáltatásokkal
Az alábbi táblázatban látható egy minta NSG-t konfigurálhatja egy Azure AD tartományi szolgáltatásokkal felügyelt tartományban rendelkező virtuális hálózaton. Ez a szabály lehetővé teszi, hogy a felügyelt tartomány marad biztosításához szükséges portokon keresztül bejövő forgalom javítani, frissítése és figyelhető a Microsoft által. Minden bejövő forgalom az internetről az alapértelmezett "DenyAll" szabály vonatkozik.

Ezenkívül az NSG-t is azt ábrázolja, hogyan zárolhat a secure LDAP-hozzáférését az interneten keresztül. Hagyja ki ezt a szabályt, ha nem engedélyezte a secure LDAP-hozzáférését a felügyelt tartomány az interneten keresztül. Az NSG-szabályokat, amelyek engedélyezik a bejövő LDAPS hozzáférést TCP-porton keresztül 636 csak egy megadott készletből az IP-címek tartalmazza. LDAPS hozzáférés engedélyezése a megadott IP-címekről az interneten keresztül Hálózatibiztonságicsoport-szabály DenyAll Hálózatibiztonságicsoport-szabály, a magasabb prioritású rendelkezik.

![NSG-t az interneten keresztül LDAPS hozzáférés biztonsága érdekében minta](./media/active-directory-domain-services-alerts/default-nsg.png)

**További információ** - [hozzon létre egy hálózati biztonsági csoport](../virtual-network/manage-network-security-group.md).


## <a name="network-connectivity"></a>Hálózati kapcsolat
Az Azure-ban egyetlen virtuális hálózaton belül csak egy Azure AD tartományi szolgáltatásokkal felügyelt tartományban lehet engedélyezni.

### <a name="scenarios-for-connecting-azure-networks"></a>Azure-hálózatok forgatókönyvei
A felügyelt tartomány a következő központi telepítési forgatókönyv bármelyikében használata Azure virtuális hálózatok összekapcsolása:

#### <a name="use-the-managed-domain-in-more-than-one-azure-virtual-network"></a>Egynél több Azure virtuális hálózatban a felügyelt tartomány használatához
Más Azure virtuális hálózatokhoz csatlakozhat, amelyben engedélyezte az Azure AD tartományi szolgáltatásokat az Azure virtuális hálózat. A VPN/virtuális hálózatok közötti társviszony-létesítési kapcsolat lehetővé teszi a felügyelt tartomány használatához a más virtuális hálózatokon üzembe helyezett számítási feladatokkal.

![Klasszikus virtuális hálózati kapcsolat](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>A Resource Manager-alapú virtuális hálózatban a felügyelt tartomány használatához
Resource Manager-alapú virtuális hálózat kapcsolódhat az Azure klasszikus virtuális hálózatot, amelyiken engedélyezte az Azure AD tartományi szolgáltatásokat. Ez a kapcsolat lehetővé teszi a felügyelt tartomány használata a Resource Manager-alapú virtuális hálózaton üzembe helyezett számítási feladatok.

![Klasszikus virtuális hálózati kapcsolat a Resource Manager](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)

### <a name="network-connection-options"></a>Hálózati kapcsolati lehetőségek
* **VNet – VNet kapcsolatokhoz használ a virtuális hálózati társviszony-létesítés**: Virtuális hálózatok közötti társviszony olyan mechanizmus, amely összeköti a két virtuális hálózat ugyanabban a régióban az Azure gerinchálózatán keresztül. A társviszony kialakítását követően a két virtuális hálózat a csatlakozás minden szempontjából egyetlen hálózatnak látszik. A kezelésük továbbra is külön erőforrásként történik, de az ezekbe a virtuális hálózatokba tartozó virtuális gépek közvetlenül, magánhálózati IP-címekkel kommunikálhatnak egymással.

    ![Virtuális hálózati kapcsolat használatával a társviszony-létesítés](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [További információk – virtuális hálózati társviszony-létesítés](../virtual-network/virtual-network-peering-overview.md)

* **Site-to-site VPN-kapcsolatok használatával VNet – VNet kapcsolatokhoz**: Két virtuális hálózat egy másik virtuális hálózatot (VNet – VNet) hasonlít a két virtuális hálózat egy helyszíni helyhez. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához.

    ![VPN-átjárót használó virtuális hálózati kapcsolat](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [További információk – VPN-átjárót használó virtuális hálózatok összekapcsolása](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

<br>

## <a name="related-content"></a>Kapcsolódó tartalom
* [Az Azure virtuális hálózatok közötti társviszony](../virtual-network/virtual-network-peering-overview.md)
* [A klasszikus üzemi modellhez tartozó virtuális hálózatok közötti kapcsolat konfigurálása](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)
* [Azure-beli hálózati biztonsági csoportok](../virtual-network/security-overview.md)
* [Hálózati biztonsági csoport létrehozása](../virtual-network/manage-network-security-group.md)
