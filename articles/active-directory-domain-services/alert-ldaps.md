---
title: Biztonságos LDAP-riasztások feloldása az Azure AD tartományi szolgáltatásokban | Microsoft dokumentumok
description: Megtudhatja, hogy miként háríthatja el és oldhatja fel a gyakori riasztásokat az Azure Active Directory tartományi szolgáltatások biztonságos LDAP-jával.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: iainfou
ms.openlocfilehash: 06b0fa1979f18981ec5cf78dc9a9dbad8b196394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71258047"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>Ismert problémák: Biztonságos LDAP-riasztások az Azure Active Directory tartományi szolgáltatásokban

Az Azure Active Directory tartományi szolgáltatásokkal (Azure AD DS) való kommunikációhoz könnyű címtár-hozzáférési protokollt (LDAP) használó alkalmazások és szolgáltatások [biztonságos LDAP használatára konfigurálhatók.](tutorial-configure-ldaps.md) A biztonságos LDAP megfelelő működéséhez megfelelő tanúsítványnak és szükséges hálózati portnak nyitva kell lennie.

Ez a cikk segít megérteni és megoldani a gyakori riasztások biztonságos LDAP-hozzáférés az Azure AD DS-ben.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: Biztonságos LDAP hálózati konfiguráció

### <a name="alert-message"></a>Figyelmeztető üzenet

*Biztonságos LDAP az interneten keresztül engedélyezve van a felügyelt tartományban. A 636-os porthoz való hozzáférés azonban nem zárolva van egy hálózati biztonsági csoport használatával. Ez a felügyelt tartomány felhasználói fiókjait jelszóval való jelszó-találgatásos támadásoknak teheti ki.*

### <a name="resolution"></a>Megoldás:

Ha engedélyezi a biztonságos LDAP-t, ajánlott további szabályokat létrehozni, amelyek korlátozzák a bejövő LDAPS-hozzáférést adott IP-címekhez. Ezek a szabályok védik az Azure AD DS felügyelt tartományt a találgatásos támadások ellen. Ha a hálózati biztonsági csoportot úgy szeretné frissíteni, hogy korlátozza a TCP-port 636-os hozzáférését a biztonságos LDAP-hoz, hajtsa végre az alábbi lépéseket:

1. Az Azure Portalon keressen és válasszon **hálózati biztonsági csoportokat.**
1. Válassza ki a felügyelt tartományhoz társított hálózati biztonsági csoportot, például *az AADDS-contoso.com-NSG*lehetőséget, majd válassza **a Bejövő biztonsági szabályok lehetőséget.**
1. **+ Adjon hozzá** egy szabályt a 636-os TCP-porthoz. Ha szükséges, a szabály létrehozásához válassza a **Speciális** lehetőséget az ablakban.
1. A **Forrás menüben**válassza a legördülő menü *IP-címeit.* Adja meg azokat a forrás IP-címeket, amelyeket hozzáférést kíván biztosítani a biztonságos LDAP-forgalomhoz.
1. Válassza a *Bármely* lehetőséget **célként,** majd írja be a *636* értéket a **Célport tartományokhoz.**
1. Állítsa be a **protokollt** *TCP-ként* és az *engedélyező* **műveletként.**
1. Adja meg a szabály prioritását, majd adjon meg egy *nevet(restrictLDAPS).*
1. Ha készen áll, a **hozzáadás gombra** a szabály létrehozásához.

Az Azure AD DS felügyelt tartomány állapota automatikusan frissíti magát két órán belül, és eltávolítja a riasztást.

> [!TIP]
> A 636-os TCP-port nem az egyetlen olyan szabály, amely az Azure AD DS zökkenőmentes működéséhez szükséges. További információ: az [Azure AD DS Network biztonsági csoportjai és a szükséges portok.](network-considerations.md#network-security-groups-and-required-ports)

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Az LDAP-tanúsítvány biztonságos lejárata

### <a name="alert-message"></a>Figyelmeztető üzenet

*A felügyelt tartomány biztonságos LDAP-tanúsítványa [dátum]] lejár.*

### <a name="resolution"></a>Megoldás:

Hozzon létre egy helyettesítő biztonságos LDAP-tanúsítványt a [biztonságos LDAP tanúsítvány létrehozásának](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)lépései végrehajtásával. Alkalmazza a helyettesítő tanúsítványt az Azure AD DS-ben, és ossza el a tanúsítványt minden olyan ügyfélnek, amely biztonságos LDAP használatával csatlakozik.

## <a name="next-steps"></a>További lépések

Ha továbbra is problémák merülnek fel, [nyisson meg egy Azure-támogatási kérelmet][azure-support] további hibaelhárítási segítségért.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
