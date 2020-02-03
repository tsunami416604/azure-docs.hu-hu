---
title: 'MFA engedélyezése VPN-felhasználók számára: Azure AD-hitelesítés'
description: A többtényezős hitelesítés engedélyezése a VPN-felhasználók számára
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 771dea2d9ae2979bc71880368ed3a9a538e8a803
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964714"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Azure Multi-Factor Authentication (MFA) engedélyezése a VPN-felhasználók számára

Ha azt szeretné, hogy a felhasználók a hozzáférés megadását megelőzően egy második hitelesítési tényezőt kérjenek, konfigurálhatja az Azure Multi-Factor Authentication (MFA) felhasználónként, vagy a [feltételes hozzáférésen](../active-directory/conditional-access/overview.md) keresztül használhatja a multi-Factor Authentication (MFA) szolgáltatást a részletesebb szabályozás érdekében. A felhasználónként Multi-Factor Authentication konfigurálása további költségek nélkül engedélyezhető, azonban amikor felhasználónként engedélyezi az MFA-t, a rendszer a felhasználót az Azure AD-bérlőhöz kötött összes alkalmazásra vonatkozóan kéri a második tényező hitelesítésére. A feltételes hozzáférés lehetővé teszi, hogy finomabb gabona-szabályozást biztosítson a második tényező előléptetéséhez, és lehetővé teszi az MFA hozzárendelését csak VPN-re, és nem az Azure AD-bérlőhöz kötött más alkalmazások számára.

## <a name="enableauth"></a>Hitelesítés engedélyezése

1. Navigáljon **Azure Active Directory – > vállalati alkalmazások – > minden alkalmazás**elemre.
2. A **vállalati alkalmazások – minden alkalmazás** lapon válassza az **Azure VPN**elemet.

   ![Könyvtár azonosítója](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>Bejelentkezési beállítások konfigurálása

Az **Azure VPN-Properties** lapon adja meg a bejelentkezési beállításokat.

1. **Engedélyezi a felhasználók számára a bejelentkezést?** **Igen**értékre. Ez lehetővé teszi, hogy az AD-bérlő összes felhasználója sikeresen kapcsolódjon a VPN-hez.
2. **Kötelezővé teszi a felhasználó-hozzárendelést?** ha a bejelentkezést csak olyan felhasználókra szeretné korlátozni, akik engedéllyel rendelkeznek az Azure VPN-hez, akkor az **Igen** értékre kell állítani.
3. Mentse a módosításokat.

   ![Engedélyek](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="option-1---enable-multi-factor-authentication-mfa-via-conditional-access"></a>1\. lehetőség – Multi-Factor Authentication (MFA) engedélyezése feltételes hozzáférésen keresztül

A feltételes hozzáférés lehetővé teszi a részletes hozzáférés-vezérlést az alkalmazások alapján.  Vegye figyelembe, hogy a feltételes hozzáférés kihasználása érdekében a feltételes hozzáférési szabályok hatálya alá tartozó felhasználókra vonatkozóan prémium szintű Azure AD 1 vagy nagyobb licencelési licencet kell alkalmaznia.

1. A **vállalati alkalmazások – minden alkalmazás** lapon válassza az **Azure VPN**elemet, válassza a **feltételes hozzáférés**lehetőséget, majd kattintson az **új házirend**elemre.
2. A felhasználók és csoportok területen a *beágyazás* lapon jelölje be a **felhasználók és csoportok kiválasztása**jelölőnégyzetet, és jelölje ki a **felhasználókat és a csoportokat**, majd válasszon ki egy olyan csoportot vagy felhasználókat, akik számára az MFA alá kell tartoznia.  Kattintson a **Done** (Kész) gombra.
![Hozzárendelések](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. A **támogatás**területen jelölje be a **hozzáférés engedélyezése**jelölőnégyzetet, jelölje be a **többtényezős hitelesítés megkövetelése**jelölőnégyzetet, jelölje be az **összes kijelölt vezérlő megkövetelése**elemet, majd kattintson a **kiválasztás**
![hozzáférés engedélyezése – MFA](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. Jelölje **be** a **házirend engedélyezése** és a **Létrehozás** gombra kattintva.
![házirend engedélyezése](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)

## <a name="option-2---enable-multi-factor-authentication-mfa-per-user"></a>2\. lehetőség – Multi-Factor Authentication (MFA) engedélyezése felhasználónként

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="next-steps"></a>Következő lépések

A virtuális hálózathoz való csatlakozáshoz létre kell hoznia és konfigurálnia kell egy VPN-ügyféloldali profilt. Lásd: [VPN-ügyfél konfigurálása P2S VPN-kapcsolatokhoz](openvpn-azure-ad-client.md).
