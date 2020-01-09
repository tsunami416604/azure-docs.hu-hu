---
title: 'VPN Gateway: Azure AD-bérlő különböző felhasználói csoportokhoz: Azure AD-hitelesítés'
description: A P2S VPN használatával kapcsolódhat a VNet az Azure AD-hitelesítés használatával
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: alzam
ms.openlocfilehash: 73a47b20010be0b4eadec790dd8fb254f912ff2a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477173"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Azure Active Directory bérlő létrehozása a P2S OpenVPN protokoll kapcsolataihoz

A VNet való csatlakozáskor tanúsítványalapú hitelesítést vagy RADIUS-hitelesítést használhat. Ha azonban a nyílt VPN protokollt használja, akkor Azure Active Directory hitelesítést is használhat. Ha azt szeretné, hogy a különböző felhasználók a különböző VPN-átjárókkal csatlakozhassanak, több alkalmazást is regisztrálhat az AD-ben, és különböző VPN-átjárókkal kapcsolhat össze. Ebből a cikkből megtudhatja, hogyan állíthat be egy Azure AD-bérlőt az P2S OpenVPN-hitelesítéshez, és hogyan hozhat létre és regisztrálhat több alkalmazást az Azure AD-ben a különböző felhasználók és csoportok hozzáférésének engedélyezéséhez.

> [!NOTE]
> Az Azure AD-hitelesítés csak az OpenVPN® protokoll-kapcsolatok esetén támogatott.
>

## <a name="tenant"></a>1. az Azure AD-bérlő létrehozása

Hozzon létre egy Azure AD-bérlőt az [új bérlő létrehozása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) című cikk lépéseivel:

* Szervezet neve
* Kezdeti tartománynév

Példa:

   ![Új Azure AD-bérlő](./media/openvpn-azure-ad-tenant-multi-app/newtenant.png)

## <a name="users"></a>2. Azure AD-bérlői felhasználók létrehozása

Ezután hozzon létre két felhasználói fiókot. Hozzon létre egy globális rendszergazdai fiókot és egy fő felhasználói fiókot. A fő felhasználói fiókot a rendszer fő beágyazási fiókként (szolgáltatásfiók) használja. Azure AD-bérlői felhasználói fiók létrehozásakor a címtárbeli szerepkört a létrehozni kívánt felhasználó típusára kell beállítania.

Az [ebben a cikkben](../active-directory/fundamentals/add-users-azure-active-directory.md) ismertetett lépések segítségével hozzon létre legalább két felhasználót az Azure ad-bérlőhöz. Ügyeljen arra, hogy a **címtárbeli szerepkört** a fióktípus létrehozásához módosítsa:

* Globális rendszergazda
* Felhasználó

## <a name="enable-authentication"></a>3. az Azure VPN-ügyfél regisztrálása az Azure AD-bérlőben

1. Keresse meg a hitelesítéshez használni kívánt címtár AZONOSÍTÓját. A Active Directory lap Tulajdonságok szakaszában szerepel.

    ![Könyvtár azonosítója](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. Másolja a címtár-azonosítót.

3. A **globális rendszergazdai** szerepkörhöz rendelt felhasználóként jelentkezzen be a Azure Portalba.

4. Ezután adja meg a rendszergazdai engedélyt. Másolja és illessze be a böngésző címsorába a telepítési helyére vonatkozó URL-címet:

    Nyilvános

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Germany

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Ha a rendszer kéri, válassza a **globális rendszergazdai** fiókot.

    ![Könyvtár azonosítója](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Ha a rendszer kéri, válassza az **elfogadás** lehetőséget.

    ![Elfogadás](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Az Azure AD-ben a **vállalati alkalmazásokban**megjelenik a felsorolt **Azure VPN** .

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azurevpn.png)

## <a name="enable-authentication"></a>4. További alkalmazások regisztrálása különböző felhasználókhoz vagy csoportokhoz

1. A Azure Active Directory alatt kattintson a **Alkalmazásregisztrációk** , majd az **+ új regisztráció** elemre.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. Az **alkalmazás regisztrálása** panelen adja meg a **nevet** , és válassza ki a kívánt **támogatott fióktípus** , majd kattintson a **regisztráció** elemre.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Miután regisztrálta az új alkalmazást, kattintson az alkalmazás panelen **elérhető API** -k közzététele lehetőségre.

4. Kattintson a **+ hatókör hozzáadása** elemre.
5. Hagyja meg az alapértelmezett **alkalmazás-azonosító URI** -t, majd kattintson a **Mentés és folytatás** gombra.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)
6. Adja meg a kötelező mezőket, és győződjön meg arról, hogy az **állapot** **engedélyezve**van. Kattintson a **hatókör hozzáadása** lehetőségre.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)
7. Kattintson az **API közzététele** , majd az **ügyfélalkalmazás hozzáadása**lehetőségre.  Az **ügyfél-azonosító**mezőben adja meg a következő értékeket a felhőtől függően:
    -   Adja meg a **41b23e61-6c1e-4545-b367-cd054e0ed4b4** az Azure **Public** -hoz
    -   Adja meg az Azure **Government** **51bb15d4-3a4f-4EBF-9dca-40096fe32426**
    -   Adja meg az Azure **Germany** **538ee9e6-310a-468d-AFEF-ea97365856a9**
    -   **49f817b6-84ae-4cc0-928c-73f27289b3aa** megadása az Azure **China 21Vianet**


8. Kattintson az **alkalmazás hozzáadása** elemre.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)
9. Másolja az **alkalmazás (ügyfél) azonosítóját** az **Áttekintés** lapról. Szüksége lesz rá a VPN-átjáró (k) konfigurálásához

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Az ebben a szakaszban (4) szereplő lépések megismétlésével hozzon létre annyi alkalmazást, amely szükséges a biztonsági követelményhez. Minden alkalmazás egy VPN-átjáróhoz lesz társítva, és különböző felhasználói csoportokkal rendelkezhet. Egy átjáróhoz csak egy alkalmazás társítható.

## <a name="enable-authentication"></a>5. felhasználók kiosztása az alkalmazásokhoz

1. Az Azure AD-ben a **vállalati alkalmazások**területen válassza ki az újonnan regisztrált alkalmazást, és kattintson a **Tulajdonságok**elemre. Győződjön meg arról, hogy a **felhasználó-hozzárendelés szükséges?** beállítás értéke **Igen**. Kattintson a **Mentés** gombra.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Az alkalmazás lapon kattintson a **felhasználók és csoportok** , majd a **felhasználó hozzáadása** elemre.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)
3. A **hozzárendelés hozzáadása**alatt kattintson a **felhasználók és csoportok**elemre. Válassza ki azokat a felhasználókat, akik számára elérhetővé szeretné tenni a VPN-alkalmazást. Kattintson a **Kiválasztás** gombra.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)

## <a name="enable-authentication"></a>6. az Azure AD-hitelesítés engedélyezése a VPN-átjárón

1. Engedélyezze az Azure AD-hitelesítést a VPN-átjárón az alábbi parancsok futtatásával, ügyeljen arra, hogy a parancsot a saját környezetének megfelelően módosítsa:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
> [!NOTE]
> Ne használja az Azure VPN-ügyfél alkalmazás-AZONOSÍTÓját a fenti parancsokban, mivel az összes felhasználó számára hozzáférést biztosít a VPN-átjáróhoz. Használja a regisztrált alkalmazás (ok) AZONOSÍTÓját.

2. Hozza létre és töltse le a profilt a következő parancsok futtatásával. Módosítsa a-ResourcGroupName és a-name értékeket a saját igényeinek megfelelően.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. A parancsok futtatása után az alábbihoz hasonló eredményt láthat. Másolja ki az eredmény URL-címét a böngészőbe a profil zip-fájljának letöltéséhez.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. Bontsa ki a letöltött zip-fájlt.

5. Tallózással keresse meg a kibontott "AzureVPN" mappát.

6. Jegyezze fel a "azurevpnconfig. xml" fájl helyét. A azurevpnconfig. XML a VPN-kapcsolat beállítását tartalmazza, és közvetlenül importálható az Azure VPN-ügyfélalkalmazás alkalmazásba. Ezt a fájlt az összes olyan felhasználó számára is terjesztheti, akiknek e-mailben vagy más módon kell csatlakozniuk. A felhasználónak érvényes Azure AD-beli hitelesítő adatokra lesz szüksége a sikeres kapcsolódáshoz.

## <a name="next-steps"></a>Következő lépések

A virtuális hálózathoz való csatlakozáshoz létre kell hoznia és konfigurálnia kell egy VPN-ügyféloldali profilt. Lásd: [VPN-ügyfél konfigurálása P2S VPN-kapcsolatokhoz](openvpn-azure-ad-client.md).
