---
title: Azure VMware-megoldás a CloudSimple által – biztonságos privát felhő
description: Ismerteti, hogyan védheti meg az Azure VMware-megoldást a CloudSimple Private Cloud használatával
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 39f451e94f2a825e69425f71aceda5f34de7eeb5
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642642"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>Saját felhőalapú környezet biztonságossá tétele

Szerepköralapú hozzáférés-vezérlés (RBAC) definiálása a CloudSimple szolgáltatáshoz, a CloudSimple-portálhoz és az Azure-beli privát felhőhöz.  A vCenter eléréséhez szükséges felhasználók, csoportok és szerepkörök a VMware SSO használatával vannak megadva.  

## <a name="rbac-for-cloudsimple-service"></a>RBAC a CloudSimple szolgáltatáshoz

A CloudSimple szolgáltatás létrehozásához **tulajdonosi** vagy **közreműködői** szerepkörre van szükség az Azure-előfizetésben.  Alapértelmezés szerint minden tulajdonos és közreműködő létrehoz egy CloudSimple szolgáltatást, és hozzáfér a CloudSimple-portálhoz a privát felhők létrehozásához és kezeléséhez.  Régiónként csak egy CloudSimple szolgáltatás hozható létre.  Az adott rendszergazdák hozzáférésének korlátozásához kövesse az alábbi eljárást.

1. CloudSimple-szolgáltatás létrehozása új **erőforráscsoport** Azure Portal
2. RBAC megadása az erőforráscsoporthoz.
3. Csomópontok megvásárlása és ugyanazt az erőforráscsoportot használja, mint a CloudSimple szolgáltatás

Csak azok a felhasználók láthatják a CloudSimple szolgáltatást, akik **tulajdonosi** vagy **közreműködői** jogosultsággal rendelkeznek az erőforráscsoport számára, és elindítják a CloudSimple-portált.

További információ a RBAC: [Mi az Azure-erőforrások szerepköralapú hozzáférés-vezérlése (RBAC)](../role-based-access-control/overview.md).

## <a name="rbac-for-private-cloud-vcenter"></a>RBAC a saját felhőalapú vCenter

A rendszer létrehoz `CloudOwner@cloudsimple.local` egy alapértelmezett felhasználót a vCenter SSO-tartományban a saját felhő létrehozásakor.  A CloudOwner-felhasználó jogosultságokkal rendelkezik a vCenter kezeléséhez.   A rendszer további identitási forrásokat ad hozzá a vCenter SSO-hoz a különböző felhasználókhoz való hozzáférés biztosítása érdekében.  Az előre definiált szerepkörök és csoportok a vCenter vannak beállítva, amelyek további felhasználók hozzáadására használhatók.

### <a name="add-new-users-to-vcenter"></a>Új felhasználók hozzáadása a vCenter

1. [](escalate-private-cloud-privileges.md) A *CloudOwner@cloudsimple.local* felhasználói jogosultságok kiterjesztésének megvonása a privát felhőben.
2. Bejelentkezés a vCenter használatával *CloudOwner@cloudsimple.local*
3. [VCenter egyszeri bejelentkezési felhasználók hozzáadása](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Felhasználók hozzáadása az [egyszeri bejelentkezési csoportok vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Az előre definiált szerepkörökkel és csoportokkal kapcsolatos további információkért lásd: [CloudSimple Private Cloud Permission Model of VMware vCenter](learn-private-cloud-permissions.md) cikk.

### <a name="add-new-identity-sources"></a>Új Identity sources hozzáadása

További identitás-szolgáltatókat is hozzáadhat a privát felhő vCenter SSO-tartományához.  Az identitás-szolgáltatók hitelesítési és vCenter SSO-csoportokat biztosítanak a felhasználók számára.

* A [Active Directory identitás-szolgáltatóként használhatja](set-vcenter-identity.md) a saját Felhőbeli vCenter.
* Az [Azure ad használata identitás](azure-ad.md) -szolgáltatóként a saját felhőalapú vCenter

1. [](escalate-private-cloud-privileges.md) A *CloudOwner@cloudsimple.local* felhasználói jogosultságok kiterjesztésének megvonása a privát felhőben.
2. Bejelentkezés a vCenter használatával *CloudOwner@cloudsimple.local*
3. Adja hozzá a felhasználókat az identitás-szolgáltatótól az [egyszeri bejelentkezési csoportok vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-private-cloud-environment"></a>Biztonságos hálózat a saját felhőalapú környezetében

A privát felhőalapú környezet hálózati biztonságát a hálózati hozzáférés biztonságossá tétele és az erőforrások közötti hálózati forgalom szabályozása vezérli.

### <a name="access-to-private-cloud-resources"></a>Hozzáférés a saját felhőalapú erőforrásokhoz

A saját felhőalapú vCenter és erőforrásaihoz való hozzáférés biztonságos hálózati kapcsolaton keresztül történik:

* **[ExpressRoute-kapcsolatok](on-premises-connection.md)** . A ExpressRoute biztonságos, nagy sávszélességű, kis késleltetésű kapcsolattal rendelkezik a helyszíni környezettől.  A kapcsolat lehetővé teszi, hogy a helyszíni szolgáltatások, hálózatok és felhasználók hozzáférjenek a saját Felhőbeli vCenter.
* **[Helyek közötti VPN-átjáró](vpn-gateway.md)** . A helyek közötti VPN a biztonságos alagúton keresztül hozzáférést biztosít a saját felhőalapú erőforrásaihoz a helyszínen.  Megadhatja, hogy mely helyszíni hálózatok küldhetnek és fogadhatnak hálózati forgalmat a saját felhőbe.
* **[Pont – hely típusú VPN-átjáró](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)** . Pont – hely VPN-kapcsolat használata a saját felhőalapú vCenter való gyors távoli hozzáféréshez.

### <a name="control-network-traffic-in-private-cloud"></a>A hálózati forgalom vezérlése a privát felhőben

A tűzfal táblái és szabályai vezérlik a hálózati forgalmat a privát felhőben.  A tűzfal tábla lehetővé teszi a hálózati forgalom vezérlését a forrásoldali hálózat vagy az IP-cím, valamint a célként megadott hálózat vagy IP-cím között a táblázatban meghatározott szabályok kombinációja alapján.

1. Hozzon létre egy [Tűzfalszabály-táblázatot](firewall.md#add-a-new-firewall-table).
2. [Szabályok hozzáadása](firewall.md#create-a-firewall-rule) a tűzfal táblához.
3. [Tűzfalszabály csatlakoztatása egy VLAN-hoz/alhálózathoz] (tűzfal. MD # Attach-vlanssubnet.
