---
title: Azure VMware-megoldások (AVS) – biztonságos AVS Private Cloud
description: Útmutató az Azure VMware Solutions (AVS) privát felhő biztonságossá tételéhez
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b053f1493c2380153711176a4748ebf90b479a6c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020080"
---
# <a name="how-to-secure-your-avs-private-cloud-environment"></a>Az AVS Private Cloud Environment biztonságossá tétele

Szerepköralapú hozzáférés-vezérlés (RBAC) definiálása az Azure-ból az AVS-szolgáltatás, az AVS Portal és az AVS Private Cloud számára. Az AVS Private Cloud vCenter eléréséhez szükséges felhasználók, csoportok és szerepkörök a VMware SSO használatával vannak megadva. 

## <a name="rbac-for-avs-service"></a>RBAC az AVS szolgáltatáshoz

Az AVS-szolgáltatás létrehozásához **tulajdonosi** vagy **közreműködői** szerepkörre van szükség az Azure-előfizetésben. Alapértelmezés szerint minden tulajdonos és közreműködő létrehoz egy AVS-szolgáltatást, és hozzáfér az AVS-portálhoz az AVS privát felhők létrehozásához és kezeléséhez. Régiónként csak egy AVS-szolgáltatás hozható létre. Az adott rendszergazdák hozzáférésének korlátozásához kövesse az alábbi eljárást.

1. AVS-szolgáltatás létrehozása egy új **erőforráscsoport** Azure Portal
2. RBAC megadása az erőforráscsoporthoz.
3. Csomópontok vásárlása és az AVS-szolgáltatással megegyező erőforráscsoport használata

Csak azok a felhasználók láthatják az AVS szolgáltatást, akik **tulajdonosi** vagy **közreműködői** jogosultsággal rendelkeznek az erőforráscsoport számára, és elindítják az AVS-portált.

További információ a RBAC: [Mi az Azure-erőforrások szerepköralapú hozzáférés-vezérlése (RBAC)](../role-based-access-control/overview.md).

## <a name="rbac-for-avs-private-cloud-vcenter"></a>RBAC az AVS Private Cloud vCenter

A rendszer egy alapértelmezett felhasználói `CloudOwner@AVS.local` hoz létre a vCenter SSO-tartományban, amikor létrejön egy AVS Private Cloud. A CloudOwner-felhasználó jogosultságokkal rendelkezik a vCenter kezeléséhez. A rendszer további identitási forrásokat ad hozzá a vCenter SSO-hoz a különböző felhasználókhoz való hozzáférés biztosítása érdekében. Az előre definiált szerepkörök és csoportok a vCenter vannak beállítva, amelyek további felhasználók hozzáadására használhatók.

### <a name="add-new-users-to-vcenter"></a>Új felhasználók hozzáadása a vCenter

1. Az AVS Private Cloud-on **CloudOwner@AVS.local** felhasználó [jogosultságának](escalate-private-cloud-privileges.md) megemelése.
2. Bejelentkezés a vCenter **CloudOwner@AVS.local** használatával
3. [VCenter egyszeri bejelentkezési felhasználók hozzáadása](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Felhasználók hozzáadása az [egyszeri bejelentkezési csoportok vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Az előre definiált szerepkörökkel és csoportokkal kapcsolatos további információkért lásd: [az AVS Private Cloud Permission Model of VMware vCenter](learn-private-cloud-permissions.md) .

### <a name="add-new-identity-sources"></a>Új Identity sources hozzáadása

További identitás-szolgáltatókat adhat hozzá az AVS Private Cloud vCenter SSO-tartományához. Az identitás-szolgáltatók hitelesítési és vCenter SSO-csoportokat biztosítanak a felhasználók számára.

* A [Active Directory identitás-szolgáltatóként használhatja](set-vcenter-identity.md) az AVS Private Cloud vCenter.
* Az [Azure ad használata identitás-szolgáltatóként](azure-ad.md) az AVS Private Cloud vCenter

1. Az AVS Private Cloud-on **CloudOwner@AVS.local** felhasználó [jogosultságának](escalate-private-cloud-privileges.md) megemelése.
2. Bejelentkezés a vCenter **CloudOwner@AVS.local** használatával
3. Adja hozzá a felhasználókat az identitás-szolgáltatótól az [egyszeri bejelentkezési csoportok vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-avs-private-cloud-environment"></a>Biztonságos hálózat az AVS Private Cloud Environment-környezetében

Az AVS Private Cloud Environment hálózati biztonságát a hálózati hozzáférés biztonságossá tétele és az erőforrások közötti hálózati forgalom szabályozása vezérli.

### <a name="access-to-avs-private-cloud-resources"></a>Hozzáférés az AVS Private Cloud-erőforrásokhoz

Az AVS Private Cloud vCenter és erőforrásaihoz való hozzáférés biztonságos hálózati kapcsolaton keresztül történik:

* **[ExpressRoute-kapcsolatok](on-premises-connection.md)** . A ExpressRoute biztonságos, nagy sávszélességű, kis késleltetésű kapcsolattal rendelkezik a helyszíni környezettől. A kapcsolat lehetővé teszi a helyszíni szolgáltatások, hálózatok és felhasználók számára az AVS Private Cloud vCenter elérését.
* **[Helyek közötti VPN-átjáró](vpn-gateway.md)** . A helyek közötti VPN a biztonságos alagúton keresztül hozzáférést biztosít a helyszíni AVS saját felhőalapú erőforrásaihoz. Megadhatja, hogy mely helyszíni hálózatok küldhetnek és fogadhatnak hálózati adatforgalmat az AVS Private Cloud szolgáltatásba.
* **[Pont – hely típusú VPN-átjáró](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)** . Pont – hely VPN-kapcsolat használata az AVS Private Cloud vCenter való gyors távoli eléréshez.

### <a name="control-network-traffic-in-avs-private-cloud"></a>Hálózati forgalom szabályozása az AVS Private Cloud-ban

A tűzfal táblái és szabályai vezérlik a hálózati forgalmat az AVS Private Cloud-ban. A tűzfal tábla lehetővé teszi a hálózati forgalom vezérlését a forrásoldali hálózat vagy az IP-cím, valamint a célként megadott hálózat vagy IP-cím között a táblázatban meghatározott szabályok kombinációja alapján.

1. Hozzon létre egy [Tűzfalszabály-táblázatot](firewall.md#add-a-new-firewall-table).
2. [Szabályok hozzáadása](firewall.md#create-a-firewall-rule) a tűzfal táblához.
3. [Tűzfalszabály csatlakoztatása egy VLAN-hoz/alhálózathoz] (tűzfal. MD # Attach-vlanssubnet.
