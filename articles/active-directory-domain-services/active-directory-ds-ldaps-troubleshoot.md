---
title: Biztonságos LDAP (LDAPS) az Azure AD Domain Services hibaelhárítása |} A Microsoft Docs
description: Biztonságos LDAP (LDAPS) elhárítása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: maheshu
ms.openlocfilehash: 83571654ca67fc886201b37706f9a6cc59385cbe
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449896"
---
# <a name="troubleshoot-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Biztonságos LDAP (LDAPS) elhárítása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz

## <a name="connection-issues"></a>Kapcsolódási problémák
Ha nem lehet csatlakozni a felügyelt tartomány secure LDAP használatával:

* A secure LDAP-tanúsítványt kiállító láncában az ügyfélen megbízhatónak kell lennie. Választhatja a legfelső szintű hitelesítésszolgáltató hozzáadása a megbízható főtanúsítvány-tárolóba az ügyfélen, a bizalmi kapcsolat létrehozásához.
* Győződjön meg arról, hogy az LDAP-ügyfél (például az ldp.exe) csatlakozik a biztonságos LDAP-végpont DNS-név, nem az IP-cím használatával.
* Ellenőrizze a DNS-név, az LDAP-ügyfél csatlakozik. A nyilvános IP-cím azt kell hozzárendelni, a felügyelt tartomány secure LDAP számára.
* Ellenőrizze, hogy a felügyelt tartomány secure LDAP-tanúsítványt tartalmaz, a DNS-név, a tulajdonos vagy a tulajdonos alternatív neve attribútum.
* Az NSG-beállításait a virtuális hálózatnak engedélyeznie kell a forgalmat a 636-os portot az internetről. Ez a lépés csak akkor, ha engedélyezte a secure LDAP-hozzáférését az interneten keresztül vonatkozik.


## <a name="need-help"></a>Segítség
Ha még nem lehet csatlakozni a felügyelt tartomány secure LDAP használatával [lépjen kapcsolatba a termékcsoport](active-directory-ds-contact-us.md) segítséget. Az alábbi információkat, amelyek segítenek a probléma diagnosztizálásában jobban:
* Képernyőkép az ldp.exe a kapcsolatot és működik.
* Az Azure AD-bérlő azonosítója, és a felügyelt tartomány DNS-tartomány nevét.
* Kötés kívánt felhasználói név.


## <a name="related-content"></a>Kapcsolódó tartalom
* [Az Azure AD tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [Azure AD tartományi szolgáltatások által kezelt tartomány felügyelete](active-directory-ds-admin-guide-administer-domain.md)
* [LDAP-lekérdezés alapjai](https://technet.microsoft.com/library/aa996205.aspx)
* [Az Azure AD tartományi szolgáltatásokkal felügyelt tartományban a Csoportházirend-kezelése](active-directory-ds-admin-guide-administer-group-policy.md)
* [Hálózati biztonsági csoportok](../virtual-network/security-overview.md)
* [Hálózati biztonsági csoport létrehozása](../virtual-network/tutorial-filter-network-traffic.md)
