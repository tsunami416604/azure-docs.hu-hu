---
title: Hálózati biztonsági csoportriasztásainak feloldása az Azure AD DS-ben | Microsoft dokumentumok
description: A hálózati biztonsági csoport azure Active Directory tartományi szolgáltatásaihoz szükséges hálózati csoportkonfigurációs riasztások hibaelhárítása és feloldása
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/19/2019
ms.author: iainfou
ms.openlocfilehash: 959f1e3f25602938d769c574ea975c4bba9300e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71258002"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>Ismert problémák: Hálózati konfigurációs riasztások az Azure Active Directory tartományi szolgáltatásokban

Ahhoz, hogy az alkalmazások és szolgáltatások megfelelően kommunikálhassanak az Azure Active Directory tartományi szolgáltatásokkal (Azure AD DS), bizonyos hálózati portok nak meg kell nyitva lenniük, hogy a forgalom forgalomban lehessen kommunikálni. Az Azure-ban a hálózati biztonsági csoportok használatával szabályozhatja a forgalom áramlását. Az Azure AD DS felügyelt tartomány állapotának állapota riasztást jelenít meg, ha a szükséges hálózati biztonsági csoportszabályok nincsenek érvényben.

Ez a cikk segít megérteni és megoldani a hálózati biztonsági csoport konfigurációs problémáira vonatkozó gyakori riasztásokat.

## <a name="alert-aadds104-network-error"></a>AADDS104 riasztás: Hálózati hiba

### <a name="alert-message"></a>Figyelmeztető üzenet

*A Microsoft nem tudja elérni a felügyelt tartomány tartományvezérlőit. Ez akkor fordulhat elő, ha a virtuális hálózaton konfigurált hálózati biztonsági csoport (NSG) blokkolja a felügyelt tartományhoz való hozzáférést. Egy másik lehetséges ok, ha van egy felhasználó által definiált útvonal, amely blokkolja a bejövő forgalmat az internetről.*

Az Azure AD DS hálózati hibáinak leggyakoribb oka ima a hálózati biztonsági csoport szabályok. A virtuális hálózat hálózati biztonsági csoportjának lehetővé kell tennie a hozzáférést bizonyos portokhoz és protokollokhoz. Ha ezek a portok le vannak tiltva, az Azure platform nem tudja figyelni vagy frissíteni a felügyelt tartományt. Az Azure AD-címtár és az Azure AD DS felügyelt tartomány közötti szinkronizálás is hatással van. A szolgáltatás megszakításának elkerülése érdekében tartsa nyitva az alapértelmezett portokat.

## <a name="default-security-rules"></a>Alapértelmezett biztonsági szabályok

A következő alapértelmezett bejövő és kimenő biztonsági szabályok vonatkoznak a hálózati biztonsági csoport egy Azure AD DS felügyelt tartományban. Ezek a szabályok biztonságossá teszik az Azure AD DS-t, és lehetővé teszik, hogy az Azure platform figyelje, kezelje és frissítse a felügyelt tartományt. A [biztonságos LDAP konfigurálása][configure-ldaps]esetén egy további szabály is lehet, amely lehetővé teszi a bejövő forgalmat.

### <a name="inbound-security-rules"></a>Bejövő biztonsági szabály

| Prioritás | Név | Port | Protocol (Protokoll) | Forrás | Cél | Műveletek |
|----------|------|------|----------|--------|-------------|--------|
| 101      | AllowSyncWithazuread | 443 | TCP | AzureActiveDirectoryDomainServices | Bármelyik | Engedélyezés |
| 201      | Engedélyezett | 3389 | TCP | CorpNetSaw között | Bármelyik | Engedélyezés |
| 301      | PsRemoting engedélyezése | 5986| TCP | AzureActiveDirectoryDomainServices | Bármelyik | Engedélyezés |
| 65000    | AllVnetInBound | Bármelyik | Bármelyik | VirtualNetwork | VirtualNetwork | Engedélyezés |
| 65001    | AllowAzureLoadBalancerInBound | Bármelyik | Bármelyik | AzureLoadBalancer | Bármelyik | Engedélyezés |
| 65500    | DenyAllInBound | Bármelyik | Bármelyik | Bármelyik | Bármelyik | Megtagadás |

### <a name="outbound-security-rules"></a>Kimenő biztonsági szabályok

| Prioritás | Név | Port | Protocol (Protokoll) | Forrás | Cél | Műveletek |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | Bármelyik | Bármelyik | VirtualNetwork | VirtualNetwork | Engedélyezés |
| 65001    | AllowAzureLoadBalancerOutBound | Bármelyik | Bármelyik |  Bármelyik | Internet | Engedélyezés |
| 65500    | DenyAllOutBound | Bármelyik | Bármelyik | Bármelyik | Bármelyik | Megtagadás |

>[!NOTE]
> Az Azure AD DS-nek korlátlan kimenő hozzáférést kell biztosítania a virtuális hálózatról. Nem javasoljuk, hogy hozzon létre olyan további szabályokat, amelyek korlátozzák a virtuális hálózat kimenő hozzáférését.

## <a name="verify-and-edit-existing-security-rules"></a>Meglévő biztonsági szabályok ellenőrzése és szerkesztése

A meglévő biztonsági szabályok ellenőrzéséhez és az alapértelmezett portok megnyitásának ellenőrzéséhez hajtsa végre az alábbi lépéseket:

1. Az Azure Portalon keressen és válasszon **hálózati biztonsági csoportokat.**
1. Válassza ki a felügyelt tartományhoz társított hálózati biztonsági csoportot, például *az AADDS-contoso.com-NSG.*
1. Az **Áttekintés** lapon a meglévő bejövő és kimenő biztonsági szabályok jelennek meg.

    Tekintse át a bejövő és kimenő szabályokat, és hasonlítsa össze az előző szakaszban a kötelező szabályok listájával. Szükség esetén jelölje ki, majd törölje azokat az egyéni szabályokat, amelyek blokkolják a szükséges forgalmat. Ha a szükséges szabályok bármelyike hiányzik, adjon hozzá egy szabályt a következő szakaszba.

    Miután hozzáadja vagy törli a szükséges forgalmat engedélyező szabályokat, az Azure AD DS felügyelt tartomány állapota két órán belül automatikusan frissíti magát, és eltávolítja a riasztást.

### <a name="add-a-security-rule"></a>Biztonsági szabály hozzáadása

Hiányzó biztonsági szabály hozzáadásához hajtsa végre az alábbi lépéseket:

1. Az Azure Portalon keressen és válasszon **hálózati biztonsági csoportokat.**
1. Válassza ki a felügyelt tartományhoz társított hálózati biztonsági csoportot, például *az AADDS-contoso.com-NSG.*
1. A bal oldali panel **Beállítások területén** kattintson a Bejövő *biztonsági szabályok* vagy a Kimenő *biztonsági szabályok* elemre attól függően, hogy melyik szabályt kell hozzáadnia.
1. Válassza **a Hozzáadás**lehetőséget, majd hozza létre a szükséges szabályt a port, a protokoll, az irány stb. Ha készen áll, válassza **az OK gombot.**

Néhány percet vesz igénybe a biztonsági szabály hozzáadása és megjelenítése a listában.

## <a name="next-steps"></a>További lépések

Ha továbbra is problémák merülnek fel, [nyisson meg egy Azure-támogatási kérelmet][azure-support] további hibaelhárítási segítségért.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
