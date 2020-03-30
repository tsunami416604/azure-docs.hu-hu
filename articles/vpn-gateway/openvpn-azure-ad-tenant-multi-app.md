---
title: 'VPN-átjáró: Azure AD-bérlő különböző felhasználói csoportokhoz: Azure AD-hitelesítés'
description: A P2S VPN használatával azure AD-hitelesítéssel csatlakozhat a virtuális hálózathoz
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 118ea21cbdd2e0527659c7c1beb40d8e42fa1d10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77485728"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Azure Active Directory-bérlő létrehozása P2S OpenVPN protokollkapcsolatokhoz

A virtuális hálózathoz való csatlakozáskor tanúsítványalapú hitelesítést vagy RADIUS-hitelesítést használhat. Azonban az Open VPN protokoll használatakor is használhatja az Azure Active Directory-hitelesítést. Ha azt szeretné, hogy a felhasználók különböző készletei képesek legyenek csatlakozni a különböző VPN-átjárókhoz, több alkalmazást regisztrálhat az AD-ben, és összekapcsolhatja őket különböző VPN-átjárókhoz. Ez a cikk segít beállítani egy Azure AD-bérlő p2s OpenVPN-hitelesítéshez, és hozzon létre és regisztráljon több alkalmazást az Azure AD-ben, amely lehetővé teszi a különböző hozzáférés a különböző felhasználók és csoportok számára.

> [!NOTE]
> Az Azure AD-hitelesítés csak OpenVPN® protokollkapcsolatok esetén támogatott.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. Hitelesítés engedélyezése az átjárón

Ebben a lépésben engedélyezi az Azure AD-hitelesítést a VPN-átjárón.

1. Engedélyezze az Azure AD-hitelesítést a VPN-átjárón a következő parancsok futtatásával. Ügyeljen arra, hogy módosítsa a parancsokat, hogy azok tükrözzék a saját környezetét:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
    > [!NOTE]
    > Ne használja az Azure VPN-ügyfél alkalmazásazonosítóját a fenti parancsokban: Minden felhasználó számára hozzáférést biztosít a VPN-átjáróhoz. Használja a regisztrált alkalmazás(ok) azonosítóját.

2. A profilt a következő parancsok futtatásával hozd létre és töltsd le. Módosítsa a -ResourcGroupName és -Name értékeket, hogy megfeleljenek a sajátjának.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. A parancsok futtatása után az alábbihoz hasonló eredményt láthat. Másolja az eredmény URL-jét a böngészőbe a profilzip fájl letöltéséhez.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. Bontsa ki a letöltött zip fájlt.

5. Tallózással keresse meg a kibontott "AzureVPN" mappát.

6. Jegyezze fel az "azurevpnconfig.xml" fájl helyét. Az azurevpnconfig.xml tartalmazza a VPN-kapcsolat beállítását, és közvetlenül az Azure VPN-ügyfélalkalmazásba importálható. Ezt a fájlt minden olyan felhasználónak is terjesztheti, amelyiknek e-mailben vagy más módon kell csatlakoznia. A sikeres csatlakozáshoz a felhasználónak érvényes Azure AD-hitelesítő adatokra van szüksége.

## <a name="next-steps"></a>További lépések

A virtuális hálózathoz való csatlakozáshoz létre kell hoznia és konfigurálnia kell egy VPN-ügyfélprofilt. Lásd: [VPN-ügyfél konfigurálása p2s VPN-kapcsolatokhoz](openvpn-azure-ad-client.md).
