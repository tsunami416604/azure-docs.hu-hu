---
title: A (z) Azure AD Domain Services Secure LDAP (LDAPs) hibáinak megoldása | Microsoft Docs
description: Azure AD Domain Services felügyelt tartomány Secure LDAP (LDAPs) hibáinak megoldása
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
ms.openlocfilehash: 285af0e5e5d5ab03027fc29064a5f3623ed10e2f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617052"
---
# <a name="troubleshoot-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services felügyelt tartomány Secure LDAP (LDAPs) hibáinak megoldása

## <a name="connection-issues"></a>Kapcsolódási problémák
Ha a biztonságos LDAP használatával nem sikerül csatlakozni a felügyelt tartományhoz:

* A biztonságos LDAP-tanúsítvány kiállítói láncának megbízhatónak kell lennie az ügyfélen. Dönthet úgy, hogy hozzáadja a legfelső szintű hitelesítésszolgáltatót a megbízható főtanúsítvány-tárolóhoz az ügyfélen a megbízhatósági kapcsolat létrehozásához.
* Győződjön meg arról, hogy az LDAP-ügyfél (például az Ldp. exe) a biztonságos LDAP-végponthoz csatlakozik a DNS-név használatával, nem az IP-címmel.
* Keresse meg azt a DNS-nevet, amelyhez az LDAP-ügyfél csatlakozik. A felügyelt tartományon a biztonságos LDAP-t a nyilvános IP-címen kell feloldani.
* Ellenőrizze, hogy a felügyelt tartományhoz tartozó biztonságos LDAP-tanúsítvány rendelkezik-e a DNS-névvel a tulajdonos vagy a tulajdonos alternatív nevek attribútumában.
* A virtuális hálózathoz tartozó NSG-beállításoknak engedélyeznie kell a forgalmat az internetről a 636-es portra. Ez a lépés csak akkor érvényes, ha az interneten keresztül engedélyezte a biztonságos LDAP-hozzáférést.


## <a name="need-help"></a>Segítség
Ha továbbra sem sikerül csatlakozni a felügyelt tartományhoz biztonságos LDAP használatával, [forduljon a termék csapatához](contact-us.md) segítségért. A probléma diagnosztizálásához vegye fel a következő információkat:
* Az Ldp. exe képernyőképe, amely a kapcsolatokat és a feladatátvételt hajtja végre.
* Az Azure AD-bérlő azonosítója és a felügyelt tartomány DNS-tartományneve.
* Annak a felhasználónévnek a pontos felhasználóneve, amelyet szeretne kötni.


## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD Domain Services – Első lépések útmutató](tutorial-create-instance.md)
* [Azure AD Domain Services tartomány kezelése](tutorial-create-management-vm.md)
* [Az LDAP-lekérdezés alapjai](https://technet.microsoft.com/library/aa996205.aspx)
* [Azure AD Domain Services Csoportházirend kezelése](manage-group-policy.md)
* [Hálózati biztonsági csoportok](../virtual-network/security-overview.md)
* [Hálózati biztonsági csoport létrehozása](../virtual-network/tutorial-filter-network-traffic.md)
