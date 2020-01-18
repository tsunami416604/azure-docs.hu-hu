---
title: 'MFA engedélyezése VPN-felhasználók számára: Azure AD-hitelesítés'
description: A többtényezős hitelesítés engedélyezése a VPN-felhasználók számára
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: alzam
ms.openlocfilehash: fcb60e80189da89b3f634c14582be606307536e6
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166695"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Azure Multi-Factor Authentication (MFA) engedélyezése a VPN-felhasználók számára

Ha azt szeretné, hogy a felhasználók a hozzáférés megadását megelőzően egy második hitelesítési tényezőt kérjenek, konfigurálhatja az Azure Multi-Factor Authentication (MFA) az Azure AD-bérlőhöz. A cikkben ismertetett lépések segítségével engedélyezheti a kétlépéses ellenőrzés követelményeit.

## <a name="prereq"></a>Előfeltétele

Ennek a konfigurációnak az előfeltétele egy konfigurált Azure AD-bérlő a [bérlő konfigurálása](openvpn-azure-ad-tenant.md)című szakaszban ismertetett lépések alapján.

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="enablesign"></a>Bejelentkezési beállítások konfigurálása

Az **Azure VPN-Properties** lapon adja meg a bejelentkezési beállításokat.

1. **Engedélyezi a felhasználók számára a bejelentkezést?** **Igen**értékre. Ez lehetővé teszi, hogy az AD-bérlő összes felhasználója sikeresen kapcsolódjon a VPN-hez.
2. **Kötelezővé teszi a felhasználó-hozzárendelést?** ha a bejelentkezést csak olyan felhasználókra szeretné korlátozni, akik engedéllyel rendelkeznek az Azure VPN-hez, akkor az **Igen** értékre kell állítani.
3. Mentse a módosításokat.

   ![Engedélyek](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>Következő lépések

A virtuális hálózathoz való csatlakozáshoz létre kell hoznia és konfigurálnia kell egy VPN-ügyféloldali profilt. Lásd: [Az Azure ad-hitelesítés konfigurálása pont – hely kapcsolathoz az Azure-hoz](virtual-wan-point-to-site-azure-ad.md).
