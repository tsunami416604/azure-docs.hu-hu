---
title: 'Azure AD-bérlő felhasználói VPN-kapcsolatokhoz: Azure AD-hitelesítés'
description: Az Azure Virtual WAN felhasználói VPN (pont – hely) használatával csatlakozhat a VNet az Azure AD-hitelesítés használatával
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: 74347ce969b6a5ffd57f5ca8396517e78590f3f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80059456"
---
# <a name="create-an-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Azure Active Directory bérlő létrehozása a felhasználói VPN OpenVPN protokoll kapcsolataihoz

A VNet való csatlakozáskor tanúsítványalapú hitelesítést vagy RADIUS-hitelesítést használhat. Ha azonban a nyílt VPN protokollt használja, akkor Azure Active Directory hitelesítést is használhat. Ebből a cikkből megtudhatja, hogyan állíthat be egy Azure AD-bérlőt a virtuális WAN-felhasználók VPN-hez (pont – hely) nyitott VPN-hitelesítéshez.

> [!NOTE]
> Az Azure AD-hitelesítés csak az OpenVPN&reg; protokoll kapcsolatai esetén támogatott.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. az Azure AD-bérlő létrehozása

Hozzon létre egy Azure AD-bérlőt az [új bérlő létrehozása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) című cikk lépéseivel:

* Szervezet neve
* Kezdeti tartománynév

Példa:

   ![Új Azure AD-bérlő](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Azure AD-bérlői felhasználók létrehozása

Ezután hozzon létre két felhasználói fiókot. Hozzon létre egy globális rendszergazdai fiókot és egy fő felhasználói fiókot. A fő felhasználói fiókot a rendszer fő beágyazási fiókként (szolgáltatásfiók) használja. Azure AD-bérlői felhasználói fiók létrehozásakor a címtárbeli szerepkört a létrehozni kívánt felhasználó típusára kell beállítania.

Az [ebben a cikkben](../active-directory/fundamentals/add-users-azure-active-directory.md) ismertetett lépések segítségével hozzon létre legalább két felhasználót az Azure ad-bérlőhöz. Ügyeljen arra, hogy a **címtárbeli szerepkört** a fióktípus létrehozásához módosítsa:

* Globális rendszergazda
* Felhasználó

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. az Azure AD-hitelesítés engedélyezése a VPN-átjárón

1. Keresse meg a hitelesítéshez használni kívánt címtár AZONOSÍTÓját. A Active Directory lap Tulajdonságok szakaszában szerepel.

    ![Könyvtár azonosítója](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Másolja ki a Címtár-azonosítót.

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

    ![Könyvtár azonosítója](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Ha a rendszer kéri, válassza az **elfogadás** lehetőséget.

    ![Elfogadás](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Az Azure AD-ben a **vállalati alkalmazásokban**a felsorolt **Azure VPN** látható.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Konfigurálja az Azure AD-hitelesítést a felhasználói VPN-hez, és rendelje hozzá egy virtuális hubhoz az Azure [AD-hitelesítés konfigurálása az Azure-hoz pont – hely kapcsolathoz](virtual-wan-point-to-site-azure-ad.md) című témakör lépéseit követve.

## <a name="next-steps"></a>További lépések

A virtuális hálózathoz való csatlakozáshoz létre kell hoznia és konfigurálnia kell egy VPN-ügyféloldali profilt, és hozzá kell rendelnie azt egy virtuális hubhoz. Lásd: [Az Azure ad-hitelesítés konfigurálása pont – hely kapcsolathoz az Azure-hoz](virtual-wan-point-to-site-azure-ad.md).
