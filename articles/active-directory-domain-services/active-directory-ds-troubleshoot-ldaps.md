---
title: "Az Azure Active Directory tartományi szolgáltatások: A biztonságos LDAP-konfiguráció hibaelhárítása |} Microsoft Docs"
description: "Biztonságos LDAP hibaelhárítása az Azure AD tartományi szolgáltatásokhoz"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: ergreenl
ms.openlocfilehash: ad98f3fb1ddb753976be627764d34864e5bf3d50
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Azure AD tartományi szolgáltatások - hibaelhárítás biztonságos LDAP-konfiguráció

Ez a cikk megoldások biztosít közös ad ki, mikor [biztonságos LDAP beállítása](active-directory-ds-admin-guide-configure-secure-ldap.md) az Azure AD tartományi szolgáltatásokhoz.

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101: Biztonságos LDAP hálózati biztonsági csoport konfigurációs

**Figyelmeztető üzenet:**

*A felügyelt tartomány számára engedélyezve van a biztonságos LDAP az interneten keresztül. Azonban 636-os port elérését nem zárolva van a hálózati biztonsági csoportok használatával. Ezt tehetik közzé a felhasználói fiókok a felügyelt tartományra, a jelszó találgatásos támadásokkal szemben.*

### <a name="secure-ldap-port"></a>Biztonságos LDAP-port

Ha engedélyezve van a biztonságos LDAP, ajánlott bejövő LDAPS hozzáférést csak bizonyos IP-címekről további szabályok létrehozása. Ezek a szabályok a tartomány megvédeni a találgatásos támadásokkal szemben, amelyek egy biztonsági kockázatot jelenthetnek. Port a 636 lehetővé teszi a hozzáférést a felügyelt tartományra. Biztonságos LDAP-hozzáférés engedélyezésének a NSG frissítése a következő:

1. Keresse meg a [hálózati biztonsági csoportok lapon](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) az Azure-portálon
2. Válassza ki a tartományt a táblázatban a társított NSG.
3. Kattintson a **bejövő biztonsági szabályok**
4. A port: 636 szabály létrehozása
   1. Kattintson a **Hozzáadás** a felső navigációs sávján látható.
   2. Válasszon **IP-címek** adatforrásra vonatkozóan.
   3. Adja meg a forrás porttartományok ehhez a szabályhoz.
   4. A megadott "636" cél porttartományok.
   5. Protokoll **TCP**.
   6. Adjon a szabálynak megfelelő nevet, leírást és prioritás. Ez a szabály prioritása nagyobb, mint a "Minden Deny" szabály prioritásának, kell lennie, ha rendelkezik ilyennel.
   7. Kattintson az **OK** gombra.
5. Győződjön meg arról, hogy a szabály jött létre.
6. Ellenőrizze a tartomány állapotát annak érdekében, hogy végrehajtotta a megfelelően két órában.


## <a name="contact-us"></a>Kapcsolat
Lépjen kapcsolatba az Azure Active Directory tartományi szolgáltatások termékért felelős csoport a [visszajelzés fájlmegosztás vagy a támogatáshoz](active-directory-ds-contact-us.md).
