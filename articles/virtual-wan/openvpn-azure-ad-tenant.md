---
title: 'Azure AD-bérlő felhasználói VPN-kapcsolatokhoz: Azure AD-hitelesítés'
description: Az Azure Virtual WAN-felhasználó VPN-jével (pontról-helyre) csatlakozhat a virtuális hálózathoz az Azure AD-hitelesítés használatával
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: 74347ce969b6a5ffd57f5ca8396517e78590f3f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059456"
---
# <a name="create-an-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Azure Active Directory-bérlő létrehozása felhasználói VPN OpenVPN protokollkapcsolatokhoz

A virtuális hálózathoz való csatlakozáskor tanúsítványalapú hitelesítést vagy RADIUS-hitelesítést használhat. Azonban az Open VPN protokoll használatakor is használhatja az Azure Active Directory-hitelesítést. Ez a cikk segít egy Azure AD-bérlő beállításában a virtuális WAN-felhasználó VPN -hez (pont-hely) VPN-hitelesítés megnyitásához.

> [!NOTE]
> Az Azure AD-hitelesítés csak&reg; OpenVPN protokollkapcsolatok esetén támogatott.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Az Azure AD-bérlő létrehozása

Hozzon létre egy Azure AD-bérlőt az [Új bérlő létrehozása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) cikkben leírt lépésekkel:

* Szervezeti név
* Kezdeti tartománynév

Példa:

   ![Új Azure AD-bérlő](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Azure AD-bérlői felhasználók létrehozása

Ezután hozzon létre két felhasználói fiókot. Hozzon létre egy globális rendszergazdai fiókot és egy fő felhasználói fiókot. A fő felhasználói fiók fő beágyazási fiókként (szolgáltatásfiókként) használatos. Amikor létrehoz egy Azure AD-bérlői felhasználói fiókot, módosítja a címtárszerepkört a létrehozni kívánt felhasználó típusához.

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

5. Ha a rendszer kéri, válassza ki a **globális rendszergazdai** fiókot.

    ![Címtár azonosítója](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Amikor a program kéri, válassza **az Elfogadás** lehetőséget.

    ![Elfogadás](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Az Azure **AD,Enterprise-alkalmazások,** az Azure VPN-listázáslátható. **Azure VPN**

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Konfigurálja az Azure AD-hitelesítést a felhasználói VPN-hez, és rendelje hozzá egy virtuális elosztóhoz az [Azure AD-hitelesítés konfigurálása az Azure-hoz-hely kapcsolathoz az Azure-hoz című azure-beli kapcsolatlépéseit](virtual-wan-point-to-site-azure-ad.md) követve.

## <a name="next-steps"></a>További lépések

A virtuális hálózathoz való csatlakozáshoz létre kell hoznia és konfigurálnia kell egy VPN-ügyfélprofilt, és hozzá kell rendelnie azt egy virtuális elosztóhoz. Lásd: [Az Azure AD-hitelesítés konfigurálása az Azure-hoz való pont-hely kapcsolathoz.](virtual-wan-point-to-site-azure-ad.md)
