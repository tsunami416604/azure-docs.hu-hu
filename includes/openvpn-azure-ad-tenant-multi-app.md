---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/18/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e950d194ab48cec1a70c7bd17617332cb858a55d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77485684"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Az Azure AD-bérlő létrehozása

Hozzon létre egy Azure AD-bérlőt az [Új bérlő létrehozása](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md) cikkben leírt lépésekkel:

* Szervezeti név
* Kezdeti tartománynév

  Példa:

   ![Új Azure AD-bérlő](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. Bérlői felhasználók létrehozása

Ebben a lépésben hozzon létre két Azure AD-bérlői felhasználók: egy globális rendszergazdai fiók és egy fő felhasználói fiók. A fő felhasználói fiók fő beágyazási fiókként (szolgáltatásfiókként) használatos. Amikor létrehoz egy Azure AD-bérlői felhasználói fiókot, módosítja a címtárszerepkört a létrehozni kívánt felhasználó típusához. A [cikkben](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) ismertetett lépésekkel legalább két felhasználót hozhat létre az Azure AD-bérlőszámára. Ügyeljen arra, hogy módosítsa a **címtárszerepkört** a fióktípusok létrehozásához:

* Globális rendszergazda
* Felhasználó

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. Regisztrálja a VPN-ügyfelet

Regisztrálja a VPN-ügyfelet az Azure AD-bérlőben.

1. Keresse meg a hitelesítéshez használni kívánt könyvtár könyvtárazonosítóját. Az Active Directory lap tulajdonságok szakaszában jelenik meg.

    ![Címtár azonosítója](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

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

    ![Címtár azonosítója](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Amikor a program kéri, válassza **az Elfogadás** lehetőséget.

    ![Elfogadás](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Az Azure AD, **nagyvállalati alkalmazások,** megjelenik **az Azure VPN-listázása.**

     ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. További kérelmek regisztrálása

Ebben a lépésben további alkalmazásokat regisztrál hat különböző felhasználókés csoportok számára.

1. Az Azure Active Directory alatt kattintson **az Alkalmazásregisztrációk** elemre, majd **a + Új regisztráció gombra.**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. A **Jelentkezés regisztrálása** lapon adja meg a **Név lehetőséget.** Jelölje ki a kívánt **támogatott fióktípusokat,** majd kattintson a **Regisztráció gombra.**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Az új alkalmazás regisztrálása után kattintson az **API-k felfedése** elemre az alkalmazáspanel alatt.

4. Kattintson **a + Hatókör hozzáadása gombra.**

5. Hagyja meg az alapértelmezett **alkalmazásazonosító URI-t**. Kattintson a **Mentés gombra, és folytassa a gombot.**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Töltse ki a szükséges mezőket, és győződjön meg arról, hogy az **állapot** **engedélyezve**van. Kattintson **a Hatókör hozzáadása gombra.**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Kattintson **az API felfedése,** majd **az Ügyfélalkalmazás hozzáadása gombra.**  **Ügyfélazonosító**esetén a felhőtől függően adja meg a következő értékeket:

    - Írja be **a 41b23e61-6c1e-4545-b367-cd054e0ed4b4-t** az Azure **Nyilvános**
    - Írja be **az 51bb15d4-3a4f-4ebf-9dca-40096fe32426** értéket az Azure **Government**
    - Írja be **az 538ee9e6-310a-468d-afef-ea97365856a9** értéket az Azure **Germany-hez**
    - Írja be **a 49f817b6-84ae-4cc0-928c-73f27289b3a értéket** az Azure **China 21Vianet-hez**

8. Kattintson **az Alkalmazás hozzáadása gombra.**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Másolja az **alkalmazás (ügyfél) azonosítóját** az **Áttekintés** lapról. Erre az információra szüksége lesz a VPN-átjáró(k) konfigurálásához.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Ismételje meg a lépéseket ebben a [regiszterben további alkalmazások](#register-apps) szakaszban hozzon létre annyi alkalmazás, amely szükséges a biztonsági követelmény. Minden alkalmazás egy VPN-átjáróhoz lesz társítva, és különböző felhasználói készletet rendelkezhet. Csak egy alkalmazás társítható egy átjáróhoz.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. Felhasználók hozzárendelése alkalmazásokhoz

Rendelje hozzá a felhasználókat az alkalmazásokhoz.

1. Az **Azure AD -> Enterprise alkalmazások csoportban**válassza ki az újonnan regisztrált alkalmazást, és kattintson a **Tulajdonságok**gombra. Győződjön meg arról, hogy a **szükséges felhasználói hozzárendelés** értéke **igen.** Kattintson a **Mentés** gombra.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Az alkalmazáslapon kattintson a **Felhasználók és csoportok**elemre, majd a **+Felhasználó hozzáadása**gombra.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. A **Hozzárendelés hozzáadása csoportban**kattintson a **Felhasználók és csoportok**elemre. Válassza ki azokat a felhasználókat, akiket el szeretne érni a VPN-alkalmazással. Kattintson a **Kiválasztás** gombra.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)