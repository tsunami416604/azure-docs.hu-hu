---
title: Kötést létrehozni biztonságos LDAP (LDAPS) használatával egy Azure AD tartományi szolgáltatásokkal felügyelt tartományban |} A Microsoft Docs
description: Kötést létrehozni biztonságos LDAP (LDAPS) használatával az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 6871374a-0300-4275-9a45-a39a52c65ae4
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: maheshu
ms.openlocfilehash: b5ddc44f0011cc90bbfe2a1bdd32e45d44f4b16a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39460190"
---
# <a name="bind-to-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Kötést létrehozni biztonságos LDAP (LDAPS) használatával az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz

## <a name="before-you-begin"></a>Előkészületek
Teljes [4. feladat – konfigurálja a felügyelt tartomány elérését az internetről érkező DNS](active-directory-ds-ldaps-configure-dns.md).


## <a name="task-5-bind-to-the-managed-domain-over-ldap-using-ldpexe"></a>5. feladat: Kötés az LDAP használata az LDP.exe keresztül a felügyelt tartományhoz
Használhatja az LDP.exe eszközt, amely tartalmazza a Távoli kiszolgálófelügyelet eszközei csomag kötést létrehozni, és az LDAP kereshet.

Először nyissa meg az LDP, és csatlakozzon ahhoz a felügyelt tartományhoz. Kattintson a **kapcsolat** kattintson **Connect...**  menüjében. Adja meg a felügyelt tartomány DNS-tartománynév. Adja meg a kapcsolatokhoz használandó portot. Az LDAP-kapcsolatokat használja a 389-es portot. LDAPS-kapcsolatok esetén a 636-os portot használja. Kattintson a **OK** a felügyelt tartományhoz csatlakoztatására szolgáló gomb.

Ezután kötést létrehozni a felügyelt tartományhoz. Kattintson a **kapcsolat** kattintson **kötési...**  menüjében. Adja meg a "AAD DC rendszergazdák" csoportba tartozó felhasználói fiók.

Válassza ki **nézet**, majd válassza ki **fa** menüjében. Az Alap DN mezőt üresen hagyja, és kattintson az OK gombra. Keresse meg a tárolót, amelyet szeretne keresését, kattintson a jobb gombbal a tárolóra, és válassza ki a keresés.

> [!TIP]
> - Felhasználók és csoportok Azure AD-ből szinkronizált vannak tárolva a **AADDC felhasználók** tároló. A keresési útvonalat a tároló néz ```CN=AADDC\ Users,DC=CONTOSO100,DC=COM```.
> - A felügyelt tartományhoz csatlakoztatott számítógépek esetében a számítógépfiókoknak vannak tárolva a **AADDC számítógépek** tároló. A keresési útvonalat a tároló néz ```CN=AADDC\ Computers,DC=CONTOSO100,DC=COM```.
>
>

További információ - [LDAP-lekérdezés alapjai](https://technet.microsoft.com/library/aa996205.aspx)


## <a name="task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>6. feladat: Zároljuk az interneten keresztül a felügyelt tartomány secure LDAP-hozzáférését
> [!NOTE]
> Ha LDAPS-t a hozzáférést a felügyelt tartomány nincs engedélyezve az interneten keresztül, hagyja ki ezt a konfigurációs feladatot.
>
>

Mielőtt ezt a feladatot, teljes körű lépéseinek végrehajtása a [3. feladat](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

Amikor engedélyezi a LDAPS-t az interneten keresztül a felügyelt tartományra, biztonsági fenyegetést hoz létre. A felügyelt tartomány secure LDAP protokollhoz használt port, az internetről érhető el (vagyis a 636-os-port). Kiválaszthatja, hogy a felügyelt tartomány ismert IP-címek való hozzáférés korlátozásához. Hozzon létre egy hálózati biztonsági csoport (NSG), és társíthatja azt az alhálózatot, ha engedélyezte az Azure AD tartományi szolgáltatásokat.

A minta az alábbi táblázatban az NSG zárol secure LDAP-hozzáférését az interneten keresztül. A szabályokat az NSG bejövő secure LDAP-hozzáférését TCP-porton keresztül 636 csak egy megadott készletből az IP-címek lehetővé teszik. Minden bejövő forgalom az internetről az alapértelmezett "DenyAll" szabály vonatkozik. LDAPS hozzáférés engedélyezése a megadott IP-címekről az interneten keresztül Hálózatibiztonságicsoport-szabály DenyAll Hálózatibiztonságicsoport-szabály, a magasabb prioritású rendelkezik.

![NSG-t az interneten keresztül LDAPS hozzáférés biztonsága érdekében minta](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**További információ** - [hálózati biztonsági csoportok](../virtual-network/security-overview.md).


## <a name="related-content"></a>Kapcsolódó tartalom
* [Az Azure AD tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [Azure AD tartományi szolgáltatások által kezelt tartomány felügyelete](active-directory-ds-admin-guide-administer-domain.md)
* [LDAP-lekérdezés alapjai](https://technet.microsoft.com/library/aa996205.aspx)
* [Az Azure AD tartományi szolgáltatásokkal felügyelt tartományban a Csoportházirend-kezelése](active-directory-ds-admin-guide-administer-group-policy.md)
* [Hálózati biztonsági csoportok](../virtual-network/security-overview.md)
* [Hálózati biztonsági csoport létrehozása](../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-step"></a>Következő lépés
[A felügyelt tartomány secure LDAP hibaelhárítása](active-directory-ds-ldaps-troubleshoot.md)
