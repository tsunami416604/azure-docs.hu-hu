---
title: 'VPN Gateway: Azure AD-bérlő a P2S VPN-kapcsolatokhoz: Azure AD-hitelesítés'
description: A P2S VPN használatával kapcsolódhat a VNet az Azure AD-hitelesítés használatával
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 01fe9331d4063ae45a5d30aa3546d6338d30822d
ms.sourcegitcommit: 206629373b7c2246e909297d69f4fe3728446af5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2020
ms.locfileid: "89499960"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Azure Active Directory-bérlő létrehozása P2S OpenVPN-protokollkapcsolatokhoz

A VNet való csatlakozáskor tanúsítványalapú hitelesítést vagy RADIUS-hitelesítést használhat. Ha azonban a nyílt VPN protokollt használja, akkor Azure Active Directory hitelesítést is használhat. Ebből a cikkből megtudhatja, hogyan állíthat be egy Azure AD-bérlőt a P2S Open VPN-hitelesítéshez.

> [!NOTE]
> Az Azure AD-hitelesítés csak az OpenVPN® protokoll-kapcsolatokhoz támogatott, és az Azure VPN-ügyfelet igényli, amely csak a Windows 10 rendszerhez érhető el.
>


## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. az Azure AD-bérlő ellenőrzése

Ellenőrizze, hogy rendelkezik-e Azure AD-Bérlővel. Ha nem rendelkezik Azure AD-Bérlővel, létrehozhat egyet az [új bérlő létrehozása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) című cikk lépéseivel:

* Szervezet neve
* Kezdeti tartománynév

Példa:

   ![Új Azure AD-bérlő](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Azure AD-bérlői felhasználók létrehozása

Az Azure AD-bérlőnek a következő fiókokra van szüksége: egy globális rendszergazdai fiók és egy fő felhasználói fiók. A fő felhasználói fiókot a rendszer fő beágyazási fiókként (szolgáltatásfiók) használja. Azure AD-bérlői felhasználói fiók létrehozásakor a címtárbeli szerepkört a létrehozni kívánt felhasználó típusára kell beállítania.

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

5. Ha a rendszer kéri, válassza a **globális rendszergazdai** fiókot.

    ![Könyvtár azonosítója](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Ha a rendszer kéri, válassza az **elfogadás** lehetőséget.

    ![Elfogadás](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Az Azure AD-ben a **vállalati alkalmazásokban**a felsorolt **Azure VPN** látható.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. Ha még nem rendelkezik működő pont – hely környezettel, kövesse az utasításokat, és hozzon létre egyet. Lásd: [pont – hely típusú VPN létrehozása](vpn-gateway-howto-point-to-site-resource-manager-portal.md) és konfigurálása pont – hely típusú VPN-átjáró létrehozásához és konfigurálásához. 

    > [!IMPORTANT]
    > Az OpenVPN esetében az alapszintű SKU nem támogatott.

9. Engedélyezze az Azure AD-hitelesítést a VPN-átjárón, ha a **pont – hely konfigurációhoz** navigál, és az **OpenVPN (SSL)** lehetőséget **adja meg bújtatási típusként**. A **hitelesítési típusként** válassza a **Azure Active Directory** lehetőséget, majd adja meg a **Azure Active Directory** szakaszban található információkat.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azure-ad-auth-portal.png)


   > [!NOTE]
   > Győződjön meg róla, hogy az érték végén szerepelnie kell egy záró perjelnek `AadIssuerUri` . Ellenkező esetben a kapcsolatok sikertelenek lehetnek.

10. A **VPN-ügyfél letöltése** hivatkozásra kattintva hozza létre és töltse le a profilt.

11. Bontsa ki a letöltött zip-fájlt.

12. Tallózással keresse meg a kibontott "AzureVPN" mappát.

13. Jegyezze fel a "azurevpnconfig.xml" fájl helyét. A azurevpnconfig.xml a VPN-kapcsolat beállítását tartalmazza, és közvetlenül importálható az Azure VPN-ügyfélalkalmazás alkalmazásba. Ezt a fájlt az összes olyan felhasználó számára is terjesztheti, akiknek e-mailben vagy más módon kell csatlakozniuk. A felhasználónak érvényes Azure AD-beli hitelesítő adatokra lesz szüksége a sikeres kapcsolódáshoz.

## <a name="next-steps"></a>Következő lépések

A virtuális hálózathoz való csatlakozáshoz létre kell hoznia és konfigurálnia kell egy VPN-ügyféloldali profilt. Lásd: [VPN-ügyfél konfigurálása P2S VPN-kapcsolatokhoz](openvpn-azure-ad-client.md).
