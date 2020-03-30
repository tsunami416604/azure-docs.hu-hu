---
title: Azure VMware-megoldások a CloudSimple től – Biztonságos magánfelhő
description: Bemutatja, hogyan lehet biztonságossá tenni az Azure VMware-megoldásokat a CloudSimple Private Cloud szolgáltatásban
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4541874a9e8fc4111e5c65d02f07535c4d14f9f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565978"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>A privát felhőkörnyezet védelme

Szerepköralapú hozzáférés-vezérlés (RBAC) definiálása a CloudSimple szolgáltatáshoz, a CloudSimple portálhoz és az Azure-ból származó magánfelhőhöz.  A vCenter of Private Cloud eléréséhez a felhasználók, csoportok és szerepkörök a VMware SSO használatával vannak megadva.  

## <a name="rbac-for-cloudsimple-service"></a>RBAC a CloudSimple szolgáltatáshoz

A CloudSimple szolgáltatás létrehozásához **tulajdonosi** vagy **közreműködői** szerepkör szükséges az Azure-előfizetésben.  Alapértelmezés szerint minden tulajdonos és közreműködő létrehozhat egy CloudSimple szolgáltatást, és hozzáférhet a CloudSimple portálhoz a privát felhők létrehozásához és kezeléséhez.  Régiónként csak egy CloudSimple szolgáltatás hozható létre.  Ha bizonyos rendszergazdákra szeretné korlátozni a hozzáférést, kövesse az alábbi eljárást.

1. CloudSimple szolgáltatás létrehozása új **erőforráscsoportban** az Azure Portalon
2. Adja meg az Erőforráscsoport RBAC-át.
3. Csomópontok vásárlása és a CloudSimple szolgáltatással megegyező erőforráscsoport használata

Csak azok a felhasználók, akik **tulajdonosi** vagy **közreműködői** jogosultsággal rendelkeznek az erőforráscsoportban, láthatják a CloudSimple szolgáltatást, és elindítják a CloudSimple portált.

Az RBAC-ról további információt [a Mi a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-erőforrásokhoz](../role-based-access-control/overview.md)című témakörben talál.

## <a name="rbac-for-private-cloud-vcenter"></a>RBAC a privát felhőbeli vCenterhez

Egy alapértelmezett `CloudOwner@cloudsimple.local` felhasználó jön létre a vCenter SSO-tartományban, amikor egy privát felhő jön létre.  A CloudOwner-felhasználó rendelkezik a vCenter kezelésével kapcsolatos jogosultságokkal. További identitásforrások kerülnek a vCenter SSO-hoz a különböző felhasználók számára való hozzáférés megadásához.  Előre definiált szerepkörök és csoportok vannak beállítva a vCenter, amely további felhasználók hozzáadására használható.

### <a name="add-new-users-to-vcenter"></a>Új felhasználók hozzáadása a vCenterhez

1. A **CloudOwner\@cloudsimple.local** felhasználó [jogosultságai nak eszkalálása](escalate-private-cloud-privileges.md) a magánfelhőben.
2. Jelentkezzen be a vCenterbe a **CloudOwner\@cloudsimple.local** használatával
3. [VCenter single sign-on felhasználók hozzáadása](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Felhasználók hozzáadása a [vCenter egyszeri bejelentkezési csoportjaihoz.](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)

Az előre definiált szerepkörökről és csoportokról a [VMware vCenter-cikk Felhőegyszerű magánfelhő-engedélyezési modelljében](learn-private-cloud-permissions.md) olvashat bővebben.

### <a name="add-new-identity-sources"></a>Új identitásforrások hozzáadása

További identitásszolgáltatókat adhat hozzá a privát felhő vCenter-sSO-tartományához.  Az identitásszolgáltatók hitelesítést biztosítanak, a vCenter SSO-csoportok pedig engedélyezést a felhasználók számára.

* [Használja az Active Directoryt identitásszolgáltatóként](set-vcenter-identity.md) a Private Cloud vCenter szolgáltatásban.
* [Az Azure AD használata identitásszolgáltatóként](azure-ad.md) a Private Cloud vCenter szolgáltatásban

1. A **CloudOwner\@cloudsimple.local** felhasználó [jogosultságai nak eszkalálása](escalate-private-cloud-privileges.md) a magánfelhőben.
2. Jelentkezzen be a vCenterbe a **CloudOwner\@cloudsimple.local** használatával
3. Az identitásszolgáltatóból felhasználókat vehet fel a [vCenter egyszeri bejelentkezési csoportjaiba.](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)

## <a name="secure-network-on-your-private-cloud-environment"></a>Biztonságos hálózat a privát felhőkörnyezetben

A privát felhőkörnyezet hálózati biztonságát a hálózati hozzáférés biztosítása és az erőforrások közötti hálózati forgalom szabályozása szabályozza.

### <a name="access-to-private-cloud-resources"></a>Hozzáférés a magánfelhő erőforrásaihoz

A Private Cloud vCenter és az erőforrások elérése biztonságos hálózati kapcsolaton keresztül történik:

* **[ExpressRoute-kapcsolat](on-premises-connection.md)**. Az ExpressRoute biztonságos, nagy sávszélességű, alacsony késésű kapcsolatot biztosít a helyszíni környezetből.  A kapcsolat használata lehetővé teszi, hogy a helyszíni szolgáltatások, hálózatok és a felhasználók hozzáférjenek a Private Cloud vCenter.
* **[Helyek közötti VPN-átjáró](vpn-gateway.md)**. A helyek közötti VPN hozzáférést biztosít a magánfelhő erőforrásaihoz a helyszíni biztonságos alagúton keresztül.  Megadhatja, hogy mely helyszíni hálózatok küldhetnek és fogadhatnak hálózati forgalmat a magánfelhőbe.
* **[Pont-hely VPN átjáró](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)**. A point-to-site VPN-kapcsolat segítségével gyors távoli hozzáférést biztosít a privát felhőbeli vCenterhez.

### <a name="control-network-traffic-in-private-cloud"></a>A hálózati forgalom szabályozása a magánfelhőben

A tűzfaltáblák és -szabályok szabályozzák a hálózati forgalmat a magánfelhőben.  A tűzfaltábla lehetővé teszi a forráshálózat vagy IP-cím, valamint a célhálózat vagy IP-cím közötti hálózati forgalom szabályozását a táblázatban meghatározott szabályok kombinációja alapján.

1. [Tűzfaltábla](firewall.md#add-a-new-firewall-table)létrehozása .
2. [Adjon hozzá szabályokat](firewall.md#create-a-firewall-rule) a tűzfaltáblához.
3. [Tűzfaltábla csatolása VLAN/alhálózathoz](firewall.md#attach-vlans-subnet).
