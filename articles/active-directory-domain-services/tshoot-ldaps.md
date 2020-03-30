---
title: Biztonságos LDAP – problémamegoldás az Azure AD tartományi szolgáltatásokban | Microsoft dokumentumok
description: A biztonságos LDAP (LDAPS) hibaelhárítása egy Azure Active Directory tartományi szolgáltatások által kezelt tartományban
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 22d1b6e2344256b52cfdbc48720a680a770a4216
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132168"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Biztonságos LDAP-kapcsolati problémák elhárítása az Azure Active Directory tartományi szolgáltatások által kezelt tartományban

Az Azure Active Directory tartományi szolgáltatásokkal (Azure AD DS) való kommunikációhoz könnyű címtár-hozzáférési protokollt (LDAP) használó alkalmazások és szolgáltatások [biztonságos LDAP használatára konfigurálhatók.](tutorial-configure-ldaps.md) A biztonságos LDAP megfelelő működéséhez megfelelő tanúsítványnak és szükséges hálózati portnak nyitva kell lennie.

Ez a cikk segít az Azure AD DS biztonságos LDAP-hozzáférésével kapcsolatos problémák elhárításában.

## <a name="common-connection-issues"></a>Gyakori csatlakozási problémák

Ha nem sikerül biztonságos LDAP-t használó Azure AD DS felügyelt tartományhoz csatlakoznia, tekintse át az alábbi hibaelhárítási lépéseket. Minden hibaelhárítási lépés után próbáljon meg újra csatlakozni az Azure AD DS felügyelt tartományához:

* A biztonságos LDAP-tanúsítvány kiállítói láncát meg kell bízni az ügyfélben. A megbízhatóság létrehozásához hozzáadhatja a legfelső szintű hitelesítésszolgáltatót (CA) az ügyfél megbízható főtanúsítvány-tárolójához.
    * Győződjön meg arról, hogy [exportálja és alkalmazza a tanúsítványt az ügyfélszámítógépekre.][client-cert]
* Ellenőrizze, hogy a felügyelt tartomány biztonságos LDAP-tanúsítványa rendelkezik-e a *Tulajdonos* vagy a *Tulajdonos alternatív nevek* attribútumban található DNS-névvel.
    * Tekintse át a [biztonságos LDAP-tanúsítvány követelményeit,][certs-prereqs] és szükség esetén hozzon létre egy helyettesítő tanúsítványt.
* Ellenőrizze, hogy az LDAP-ügyfél, például *az ldp.exe* dns-név, nem pedig IP-cím használatával csatlakozik-e a biztonságos LDAP-végponthoz.
    * Az Azure AD DS felügyelt tartományra alkalmazott tanúsítvány nem tartalmazza a szolgáltatás IP-címét, csak a DNS-neveket.
* Ellenőrizze azt a DNS-nevet, amelyhez az LDAP-ügyfél csatlakozik. Fel kell oldania a nyilvános IP-címet a biztonságos LDAP az Azure AD DS felügyelt tartományban.
    * Ha a DNS-név a belső IP-címre oldódik fel, frissítse a DNS-rekordot a külső IP-címre való feloldáshoz.
* Külső kapcsolat esetén a hálózati biztonsági csoportnak tartalmaznia kell egy szabályt, amely lehetővé teszi a 636-os TCP-portra irányuló forgalmat az internetről.
    * Ha biztonságos LDAP-kapcsolaton keresztül tud csatlakozni az Azure AD DS felügyelt tartományához a virtuális hálózathoz közvetlenül kapcsolódó erőforrásokból, de a külső kapcsolatokból nem, győződjön meg arról, [hogy létrehoz egy hálózati biztonsági csoportszabályt a biztonságos LDAP-forgalom engedélyezéséhez.][ldaps-nsg]

## <a name="next-steps"></a>További lépések

Ha továbbra is problémák merülnek fel, [nyisson meg egy Azure-támogatási kérelmet][azure-support] további hibaelhárítási segítségért.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
