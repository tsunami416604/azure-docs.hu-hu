---
title: Biztonságos LDAP (LDAPS) az Azure AD Domain Services hibaelhárítása |} A Microsoft Docs
description: Biztonságos LDAP (LDAPS) elhárítása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: a194919a488f6cb59c76315c9d8a3db1e9c1feb1
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472200"
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
Ha még nem lehet csatlakozni a felügyelt tartomány secure LDAP használatával [lépjen kapcsolatba a termékcsoport](contact-us.md) segítséget. Az alábbi információkat, amelyek segítenek a probléma diagnosztizálásában jobban:
* Képernyőkép az ldp.exe a kapcsolatot és működik.
* Az Azure AD-bérlő azonosítója, és a felügyelt tartomány DNS-tartomány nevét.
* Kötés kívánt felhasználói név.


## <a name="related-content"></a>Kapcsolódó tartalom
* [Az Azure AD tartományi szolgáltatások – első lépések útmutató](create-instance.md)
* [Az Azure AD Domain Services tartomány kezelése](manage-domain.md)
* [LDAP-lekérdezés alapjai](https://technet.microsoft.com/library/aa996205.aspx)
* [A Csoportházirend kezelése az Azure AD tartományi szolgáltatásokhoz](manage-group-policy.md)
* [Hálózati biztonsági csoportok](../virtual-network/security-overview.md)
* [Hálózati biztonsági csoport létrehozása](../virtual-network/tutorial-filter-network-traffic.md)
