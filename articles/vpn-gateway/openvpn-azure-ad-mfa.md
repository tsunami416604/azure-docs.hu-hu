---
title: 'MFA engedélyezése VPN-felhasználók számára: Azure AD-hitelesítés'
description: A többtényezős hitelesítés engedélyezése a VPN-felhasználók számára
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/14/2020
ms.author: alzam
ms.openlocfilehash: e6d3ff32d2daaf35ff00bd0ee1d8f42669683827
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "86998595"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Azure Multi-Factor Authentication (MFA) engedélyezése a VPN-felhasználók számára

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enable-authentication"></a><a name="enableauth"></a>Hitelesítés engedélyezése

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="configure-sign-in-settings"></a><a name="enablesign"></a>Bejelentkezési beállítások konfigurálása

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="option-1---per-user-access"></a><a name="peruser"></a>1. lehetőség – felhasználónkénti hozzáférés

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="option-2---conditional-access"></a><a name="conditional"></a>2. lehetőség – feltételes hozzáférés

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>További lépések

A virtuális hálózathoz való csatlakozáshoz létre kell hoznia és konfigurálnia kell egy VPN-ügyféloldali profilt. Lásd: [VPN-ügyfél konfigurálása P2S VPN-kapcsolatokhoz](openvpn-azure-ad-client.md).