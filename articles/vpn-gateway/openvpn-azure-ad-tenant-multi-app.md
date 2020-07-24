---
title: 'VPN Gateway: Azure AD-bérlő különböző felhasználói csoportokhoz: Azure AD-hitelesítés'
description: A P2S VPN használatával kapcsolódhat a VNet az Azure AD-hitelesítés használatával
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/25/2020
ms.author: alzam
ms.openlocfilehash: 57650b32d9155277d8ecc25d1b2147ebc87e7ecb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87009679"
---
# <a name="create-an-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>Active Directory (AD) bérlő létrehozása a P2S OpenVPN protokoll kapcsolataihoz

A VNet való csatlakozáskor tanúsítványalapú hitelesítést vagy RADIUS-hitelesítést használhat. Ha azonban a nyílt VPN protokollt használja, akkor Azure Active Directory hitelesítést is használhat. Ha azt szeretné, hogy a különböző felhasználók a különböző VPN-átjárókkal csatlakozhassanak, több alkalmazást is regisztrálhat az AD-ben, és különböző VPN-átjárókkal kapcsolhat össze. Ebből a cikkből megtudhatja, hogyan állíthat be egy Azure AD-bérlőt az P2S OpenVPN-hitelesítéshez, és hogyan hozhat létre és regisztrálhat több alkalmazást az Azure AD-ben a különböző felhasználók és csoportok hozzáférésének engedélyezéséhez.

> [!NOTE]
> Az Azure AD-hitelesítés csak az OpenVPN® protokoll-kapcsolatok esetén támogatott.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. hitelesítés engedélyezése az átjárón

Ebben a lépésben engedélyezni fogja az Azure AD-hitelesítést a VPN-átjárón.

1. Engedélyezze az Azure AD-hitelesítést a VPN-átjárón, ha a **pont – hely konfigurációhoz** navigál, és az **OpenVPN (SSL)** lehetőséget **adja meg bújtatási típusként**. A **hitelesítési típusként** válassza a **Azure Active Directory** lehetőséget, majd adja meg a **Azure Active Directory** szakaszban található információkat.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-ad-auth-portal.png)

    > [!NOTE]
    > Ne használja az Azure VPN-ügyfél alkalmazás-AZONOSÍTÓját: minden felhasználónak hozzáférést biztosít a VPN-átjáróhoz. Használja a regisztrált alkalmazás (ok) AZONOSÍTÓját.

2. A **VPN-ügyfél letöltése** hivatkozásra kattintva hozza létre és töltse le a profilt.

3. Bontsa ki a letöltött zip-fájlt.

4. Tallózással keresse meg a kibontott "AzureVPN" mappát.

5. Jegyezze fel a "azurevpnconfig.xml" fájl helyét. A azurevpnconfig.xml a VPN-kapcsolat beállítását tartalmazza, és közvetlenül importálható az Azure VPN-ügyfélalkalmazás alkalmazásba. Ezt a fájlt az összes olyan felhasználó számára is terjesztheti, akiknek e-mailben vagy más módon kell csatlakozniuk. A felhasználónak érvényes Azure AD-beli hitelesítő adatokra lesz szüksége a sikeres kapcsolódáshoz.

## <a name="next-steps"></a>További lépések

A virtuális hálózathoz való csatlakozáshoz létre kell hoznia és konfigurálnia kell egy VPN-ügyféloldali profilt. Lásd: [VPN-ügyfél konfigurálása P2S VPN-kapcsolatokhoz](openvpn-azure-ad-client.md).
