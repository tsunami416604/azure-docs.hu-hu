---
title: A Secure LDAP hibáinak megoldása a Azure AD Domain Servicesban | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani egy Azure Active Directory Domain Services felügyelt tartomány biztonságos LDAP (LDAPs) megoldását
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
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132168"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>A Azure Active Directory Domain Services felügyelt tartományhoz való biztonságos LDAP-kapcsolati problémák elhárítása

A Lightweight Directory Access Protocol (LDAP) protokollt használó alkalmazások és szolgáltatások, amelyekkel a Azure Active Directory Domain Services (Azure AD DS) kommunikálnak, [a biztonságos LDAP használatára konfigurálhatók](tutorial-configure-ldaps.md). A biztonságos LDAP működéséhez meg kell nyitni egy megfelelő tanúsítványt és szükséges hálózati portot.

Ez a cikk segítséget nyújt a biztonságos LDAP-hozzáférésekkel kapcsolatos hibák elhárításához az Azure AD DSban.

## <a name="common-connection-issues"></a>Gyakori kapcsolatok problémái

Ha nem sikerül az Azure AD DS felügyelt tartományhoz való kapcsolódás biztonságos LDAP használatával, tekintse át a következő hibaelhárítási lépéseket. Az egyes hibaelhárítási lépések után próbálkozzon újra az Azure AD DS felügyelt tartományhoz való kapcsolódással:

* A biztonságos LDAP-tanúsítvány kiállítói láncának megbízhatónak kell lennie az ügyfélen. A megbízhatóság létrehozásához hozzáadhatja a legfelső szintű hitelesítésszolgáltatót (CA) a megbízható főtanúsítvány-tárolóhoz az ügyfélen.
    * Győződjön meg róla, hogy [exportálja és alkalmazza a tanúsítványt az ügyfélszámítógépekre][client-cert].
* Ellenőrizze, hogy a felügyelt tartományhoz tartozó biztonságos LDAP-tanúsítvány rendelkezik-e a DNS-névvel a *tulajdonos* vagy a *tulajdonos alternatív nevek* attribútumában.
    * Tekintse át a [biztonságos LDAP-tanúsítványokra vonatkozó követelményeket][certs-prereqs] , és szükség esetén hozzon létre egy helyettesítő tanúsítványt.
* Győződjön meg arról, hogy az LDAP-ügyfél, például az *Ldp. exe* csatlakozik a biztonságos LDAP-végponthoz egy DNS-név használatával, nem az IP-címmel.
    * Az Azure AD DS felügyelt tartományra alkalmazott tanúsítvány nem tartalmazza a szolgáltatás IP-címeit, csak a DNS-neveket.
* Keresse meg azt a DNS-nevet, amelyhez az LDAP-ügyfél csatlakozik. Az Azure AD DS felügyelt tartomány biztonságos LDAP-szolgáltatásának nyilvános IP-címére kell feloldania.
    * Ha a DNS-név a belső IP-címhez lett feloldva, frissítse a DNS-rekordot a külső IP-cím feloldásához.
* A külső kapcsolatok esetében a hálózati biztonsági csoportnak tartalmaznia kell egy olyan szabályt, amely engedélyezi az internetről a 636-as TCP-portra irányuló forgalmat.
    * Ha az Azure AD DS felügyelt tartományhoz közvetlenül a virtuális hálózathoz csatlakoztatott erőforrások biztonságos LDAP szolgáltatásával tud csatlakozni, de külső kapcsolatok nélkül, akkor győződjön meg arról, hogy [létrehoz egy hálózati biztonsági csoportra vonatkozó szabályt a biztonságos LDAP-forgalom engedélyezéséhez][ldaps-nsg].

## <a name="next-steps"></a>Következő lépések

Ha továbbra is problémákba ütközik, [Nyisson meg egy Azure-támogatási kérést][azure-support] további hibaelhárítási segítségért.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
