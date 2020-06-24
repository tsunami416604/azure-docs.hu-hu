---
title: fájlbefoglalás
description: fájlbefoglalás
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/21/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2eb2dbc43c59f4f6301c7f5073a73462639d35b2
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797097"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. az Azure AD-bérlő létrehozása

Hozzon létre egy Azure AD-bérlőt az [új bérlő létrehozása](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md) című cikk lépéseivel:

* Szervezet neve
* Kezdeti tartománynév

  Példa:

   ![Új Azure AD-bérlő](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. bérlői felhasználók létrehozása

Ebben a lépésben két Azure AD-bérlői felhasználót hoz létre: egy globális rendszergazdai fiókot és egy fő felhasználói fiókot. A fő felhasználói fiókot a rendszer fő beágyazási fiókként (szolgáltatásfiók) használja. Azure AD-bérlői felhasználói fiók létrehozásakor a címtárbeli szerepkört a létrehozni kívánt felhasználó típusára kell beállítania. Az [ebben a cikkben](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) ismertetett lépések segítségével hozzon létre legalább két felhasználót az Azure ad-bérlőhöz. Ügyeljen arra, hogy a **címtárbeli szerepkört** a fióktípus létrehozásához módosítsa:

* Globális rendszergazda
* Felhasználó

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. regisztrálja a VPN-ügyfelet

Regisztrálja a VPN-ügyfelet az Azure AD-bérlőben.

1. Keresse meg a hitelesítéshez használni kívánt címtár AZONOSÍTÓját. A Active Directory lap Tulajdonságok szakaszában szerepel.

    ![Könyvtár azonosítója](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

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
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Ha a rendszer kéri, válassza a **globális rendszergazdai** fiókot.

    ![Könyvtár azonosítója](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Ha a rendszer kéri, válassza az **elfogadás** lehetőséget.

    ![Elfogadás](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Az Azure AD-ben a **vállalati alkalmazásokban**megjelenik a felsorolt **Azure VPN** .

     ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. További alkalmazások regisztrálása

Ebben a lépésben további alkalmazásokat fog regisztrálni a különböző felhasználókhoz és csoportokhoz.

1. A Azure Active Directory alatt kattintson a **Alkalmazásregisztrációk** , majd az **+ új regisztráció**elemre.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. Az **alkalmazás regisztrálása** lapon adja meg a **nevet**. Válassza ki a kívánt **támogatott típusú fiókokat**, majd kattintson a **regisztrálás**gombra.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Miután regisztrálta az új alkalmazást, kattintson az **API közzététele** lehetőségre az alkalmazás panelen.

4. Kattintson **a + hatókör hozzáadása**lehetőségre.

5. Hagyja meg az alapértelmezett **alkalmazás-azonosító URI**-t. Kattintson **a Mentés és folytatás**gombra.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Adja meg a kötelező mezőket, és győződjön meg arról, hogy az **állapot** **engedélyezve**van. Kattintson a **hatókör hozzáadása**lehetőségre.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Kattintson **az API közzététele** , majd az **ügyfélalkalmazás hozzáadása**lehetőségre.  Az **ügyfél-azonosító**mezőben adja meg a következő értékeket a felhőtől függően:

    - Adja meg a **41b23e61-6c1e-4545-b367-cd054e0ed4b4** az Azure **Public** -hoz
    - Adja meg az Azure **Government** **51bb15d4-3a4f-4EBF-9dca-40096fe32426**
    - Adja meg az Azure **Germany** **538ee9e6-310a-468d-AFEF-ea97365856a9**
    - **49f817b6-84ae-4cc0-928c-73f27289b3aa** megadása az Azure **China 21Vianet**

8. Kattintson az **alkalmazás hozzáadása**lehetőségre.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Másolja az **alkalmazás (ügyfél) azonosítóját** az **Áttekintés** lapról. Ezekre az információkra szüksége lesz a VPN-átjáró (k) konfigurálásához.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Ismételje meg a [További alkalmazások regisztrálása](#register-apps) szakasz lépéseit, és hozzon létre annyi alkalmazást, amely szükséges a biztonsági követelményhez. Minden alkalmazás egy VPN-átjáróhoz lesz társítva, és különböző felhasználói csoportokkal rendelkezhet. Egy átjáróhoz csak egy alkalmazás társítható.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. felhasználók kiosztása alkalmazásokhoz

Rendelje hozzá a felhasználókat az alkalmazásaihoz.

1. Az **Azure ad-> vállalati alkalmazások**területen válassza ki az újonnan regisztrált alkalmazást, és kattintson a **Tulajdonságok**elemre. Győződjön meg arról, hogy a **felhasználó-hozzárendelés szükséges?** beállítás értéke **Igen**. Kattintson a **Save** (Mentés) gombra.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Az alkalmazás lapon kattintson a **felhasználók és csoportok**elemre, majd a **+ felhasználó hozzáadása**lehetőségre.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. A **hozzárendelés hozzáadása**alatt kattintson a **felhasználók és csoportok**elemre. Válassza ki azokat a felhasználókat, akik számára elérhetővé szeretné tenni a VPN-alkalmazást. Kattintson a **Kiválasztás** gombra.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)
