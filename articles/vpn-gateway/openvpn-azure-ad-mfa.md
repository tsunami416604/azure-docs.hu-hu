---
title: 'MFA engedélyezése VPN-felhasználók számára: Azure AD-hitelesítés | Microsoft Docs'
description: A többtényezős hitelesítés engedélyezése a VPN-felhasználók számára
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: alzam
ms.openlocfilehash: 3f407ecfcbfdff72b79bfe6b5bc3e6b7606b1cf0
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174628"
---
# <a name="enable-multi-factor-authentication-mfa-for-vpn-users"></a>Multi-Factor Authentication (MFA) engedélyezése a VPN-felhasználók számára

Engedélyezheti az MFA-t az Azure AD-bérlőben lévő felhasználók számára, így a felhasználók a hozzáférés megadását megelőzően egy második tényezős hitelesítést kapnak.

> [! Előfeltételek] az Azure AD-bérlőt a "bérlő konfigurálása" dokumentumban leírtak szerint konfigurálta.
>

### <a name="tenant"></a>1. Jelentkezzen be a Azure Portalba, és navigáljon a **Azure Active Directoryhoz** , **minden felhasználóhoz** , és kattintson a **multi-Factor Authentication**


   ![Új Azure AD-bérlő](./media/openvpn-azure-ad-mfa/mfa1.jpg)

### <a name="users"></a>2. Válassza ki azokat a felhasználókat, akik számára engedélyezni szeretné az MFA-t, majd kattintson az **Engedélyezés** gombra.

   ![Új Azure AD-bérlő](./media/openvpn-azure-ad-mfa/mfa2.jpg)

### <a name="enable-authentication"></a>3. Navigáljon **Azure Active Directory** , **vállalati alkalmazásokhoz**, **minden alkalmazáshoz** , és kattintson az **Azure VPN** elemre.


   ![Könyvtár azonosítója](./media/openvpn-azure-ad-mfa/user1.jpg)

### <a name="users"></a>4. Ellenőrizze, hogy **engedélyezve van-** e a felhasználók számára a bejelentkezés? az Igen értékre van állítva. Ha azt szeretné, hogy csak az Azure VPN-hez engedéllyel rendelkező felhasználók tudják bejelentkezni, akkor a **felhasználó-hozzárendelés szükséges?** igen, különben az ad-bérlő összes felhasználója sikeresen tud csatlakozni a VPN-hez.

   ![Engedélyek](./media/openvpn-azure-ad-mfa/user2.jpg)


## <a name="next-steps"></a>Következő lépések

A virtuális hálózathoz való csatlakozáshoz létre kell hoznia és konfigurálnia kell egy VPN-ügyféloldali profilt. Lásd: [VPN-ügyfél konfigurálása P2S VPN-kapcsolatokhoz](openvpn-azure-ad-client.md).
