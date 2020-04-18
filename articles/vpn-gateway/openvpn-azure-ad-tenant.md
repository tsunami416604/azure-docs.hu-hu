---
title: 'VPN-átjáró: Azure AD-bérlő P2S VPN-kapcsolatokhoz: Azure AD-hitelesítés'
description: A P2S VPN használatával azure AD-hitelesítéssel csatlakozhat a virtuális hálózathoz
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: alzam
ms.openlocfilehash: 00db2ed05285a1637414aa1e3adbe3b047ff0568
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641349"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Azure Active Directory-bérlő létrehozása P2S OpenVPN protokollkapcsolatokhoz

A virtuális hálózathoz való csatlakozáskor tanúsítványalapú hitelesítést vagy RADIUS-hitelesítést használhat. Azonban az Open VPN protokoll használatakor is használhatja az Azure Active Directory-hitelesítést. Ez a cikk segít egy Azure AD-bérlő beállításával a P2S Open VPN-hitelesítéshez.

> [!NOTE]
> Az Azure AD-hitelesítés csak OpenVPN® protokollkapcsolatok esetén támogatott.
>


## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. Az Azure AD-bérlő ellenőrzése

Ellenőrizze, hogy rendelkezik-e Egy Azure AD-bérlővel. Ha nem rendelkezik Azure AD-bérlővel, létrehozhat egyet az [Új bérlő létrehozása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) cikkben leírt lépésekkel:

* Szervezeti név
* Kezdeti tartománynév

Példa:

   ![Új Azure AD-bérlő](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Azure AD-bérlői felhasználók létrehozása

Az Azure AD-bérlőnek a következő fiókokra van szüksége: globális rendszergazdai fiókra és fő felhasználói fiókra. A fő felhasználói fiók fő beágyazási fiókként (szolgáltatásfiókként) használatos. Amikor létrehoz egy Azure AD-bérlői felhasználói fiókot, módosítja a címtárszerepkört a létrehozni kívánt felhasználó típusához.

A [cikkben](../active-directory/fundamentals/add-users-azure-active-directory.md) ismertetett lépésekkel legalább két felhasználót hozhat létre az Azure AD-bérlőszámára. Ügyeljen arra, hogy módosítsa a **címtárszerepkört** a fióktípusok létrehozásához:

* Globális rendszergazda
* Felhasználó

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Az Azure AD-hitelesítés engedélyezése a VPN-átjárón

1. Keresse meg a hitelesítéshez használni kívánt könyvtár könyvtárazonosítóját. Az Active Directory lap tulajdonságok szakaszában jelenik meg.

    ![Címtár azonosítója](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Másolja ki a Címtár-azonosítót.

3. Jelentkezzen be az Azure Portalon a **globális rendszergazdai** szerepkörhöz rendelt felhasználóként.

4. Ezután adja meg az admin beleegyezését. Másolja és illessze be a telepítés helyére vonatkozó URL-címet a böngésző címsorába:

    Nyilvános

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
   https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Germany

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Ha a rendszer kéri, válassza ki a **globális rendszergazdai** fiókot.

    ![Címtár azonosítója](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Amikor a program kéri, válassza **az Elfogadás** lehetőséget.

    ![Elfogadás](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Az Azure **AD,Enterprise-alkalmazások,** az Azure VPN-listázáslátható. **Azure VPN**

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. Ha még nem rendelkezik működő pont-hely környezettel, kövesse az utasításokat, hogy hozzon létre egyet. Lásd: [Pont-hely VPN létrehozása](vpn-gateway-howto-point-to-site-resource-manager-portal.md) és konfigurálása egy pont-hely VPN átjáró natív Azure-tanúsítvány hitelesítéssel. 

    > [!IMPORTANT]
    > Az alaptermékváltozat nem támogatott az OpenVPN esetében.

9. Engedélyezze az Azure AD-hitelesítést a VPN-átjárón a következő parancsok futtatásával, és győződjön meg arról, hogy a parancsot a saját környezetének megfelelően módosítja:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>/" -AadAudienceId "41b23e61-6c1e-4545-b367-cd054e0ed4b4" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24 -VpnClientProtocol OpenVPN
    ```

   > [!NOTE]
   > Győződjön meg arról, hogy az `AadIssuerUri` érték végén egy záró perjelet is mellékel. Ellenkező esetben a parancs sikertelen lesz.

10. A profilt a következő parancsok futtatásával hozd létre és töltsd le. Módosítsa a -ResourceGroupName és -Name értékeket, hogy megfeleljenek a sajátjának.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

11. A parancsok futtatása után az alábbihoz hasonló eredményt láthat. Másolja az eredmény URL-jét a böngészőbe a profilzip fájl letöltéséhez.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/profile.png)

12. Bontsa ki a letöltött zip fájlt.

13. Tallózással keresse meg a kibontott "AzureVPN" mappát.

14. Jegyezze fel az "azurevpnconfig.xml" fájl helyét. Az azurevpnconfig.xml tartalmazza a VPN-kapcsolat beállítását, és közvetlenül az Azure VPN-ügyfélalkalmazásba importálható. Ezt a fájlt minden olyan felhasználónak is terjesztheti, amelyiknek e-mailben vagy más módon kell csatlakoznia. A sikeres csatlakozáshoz a felhasználónak érvényes Azure AD-hitelesítő adatokra van szüksége.

## <a name="next-steps"></a>További lépések

A virtuális hálózathoz való csatlakozáshoz létre kell hoznia és konfigurálnia kell egy VPN-ügyfélprofilt. Lásd: [VPN-ügyfél konfigurálása p2s VPN-kapcsolatokhoz](openvpn-azure-ad-client.md).
