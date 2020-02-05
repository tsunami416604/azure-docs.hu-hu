---
title: 'VPN Gateway: Azure AD-bérlő különböző felhasználói csoportokhoz: Azure AD-hitelesítés'
description: A P2S VPN használatával kapcsolódhat a VNet az Azure AD-hitelesítés használatával
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/10/2020
ms.author: alzam
ms.openlocfilehash: 90244b9dcf30c2ef01d4e57c9d8e35fa1d71f434
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985645"
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

    Microsoft Cloud Németország

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

    ![Fogadja el](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

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

## <a name="site"></a>6. új P2S-konfiguráció létrehozása

A pont–hely konfiguráció határozza meg a távoli ügyfelek csatlakoztatására vonatkozó paramétereket.

1. Állítsa be a következő változókat, és szükség szerint cserélje le az értékeket a környezetéhez.

   ```powershell
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. A konfiguráció létrehozásához futtassa a következő parancsokat:

   ```powershell
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

> [!NOTE]
> Ne használja az Azure VPN-ügyfél alkalmazás-AZONOSÍTÓját a fenti parancsokban, mivel az összes felhasználó számára hozzáférést biztosít a VPN-átjáróhoz. Használja a regisztrált alkalmazás (ok) AZONOSÍTÓját.

## <a name="hub"></a>7. hub-hozzárendelés szerkesztése

1. Navigáljon a virtuális WAN alatt lévő **hubok** panelre.
2. Válassza ki azt a hubot, amelyhez hozzá szeretné rendelni a VPN-kiszolgáló konfigurációját, majd kattintson a három pontra (...).

   ![új hely](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Kattintson a **virtuális központ szerkesztése**elemre.
4. Jelölje be a **pont – hely átjáró belefoglalása** jelölőnégyzetet, és válassza ki a kívánt **átjáró-méretezési egységet** .

   ![új hely](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Adja meg azt a **címkészletet** , amelyből a VPN-ügyfelek IP-címeket rendelnek.
6. Kattintson a **Megerősítés** gombra.
7. A művelet végrehajtása akár 30 percet is igénybe vehet.

## <a name="device"></a>8. a VPN-profil letöltése

A VPN-profillal konfigurálhatja az ügyfeleket.

1. A virtuális WAN lapján kattintson a **felhasználói VPN-konfigurációk**elemre.
2. A lap tetején kattintson a **felhasználói VPN-konfiguráció letöltése**elemre.
3. Miután befejeződött a fájl létrehozása, a hivatkozásra kattintva letöltheti.
4. A profil fájl segítségével konfigurálja a VPN-ügyfeleket.

4. Bontsa ki a letöltött zip-fájlt.

5. Tallózással keresse meg a kibontott "AzureVPN" mappát.

6. Jegyezze fel a "azurevpnconfig. xml" fájl helyét. A azurevpnconfig. XML a VPN-kapcsolat beállítását tartalmazza, és közvetlenül importálható az Azure VPN-ügyfélalkalmazás alkalmazásba. Ezt a fájlt az összes olyan felhasználó számára is terjesztheti, akiknek e-mailben vagy más módon kell csatlakozniuk. A felhasználónak érvényes Azure AD-beli hitelesítő adatokra lesz szüksége a sikeres kapcsolódáshoz.
## <a name="configure-user-vpn-clients"></a>Felhasználói VPN-ügyfelek konfigurálása

A csatlakozáshoz le kell töltenie az Azure VPN-ügyfelet (előzetes verzió), és importálnia kell az előző lépésekben letöltött VPN-ügyféloldali profilt minden olyan számítógépen, amely csatlakozni szeretne a VNet.

> [!NOTE]
> Az Azure AD-hitelesítés csak az OpenVPN® protokoll-kapcsolatok esetén támogatott.
>

#### <a name="to-download-the-azure-vpn-client"></a>Az Azure VPN-ügyfél letöltése

Ezzel a [hivatkozással](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) töltheti le az Azure VPN-ügyfelet (előzetes verzió).

#### <a name="import"></a>Ügyféloldali profil importálása

1. A lapon válassza az **Importálás**lehetőséget.

    ![importálása](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Keresse meg a profil XML-fájlját, és jelölje ki. A fájl kijelölése után válassza a **Megnyitás**lehetőséget.

    ![importálása](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Adja meg a profil nevét, majd válassza a **Mentés**lehetőséget.

    ![importálása](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Válassza a **Csatlakozás** lehetőséget a VPN-hez való csatlakozáshoz.

    ![importálása](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. A csatlakozás után az ikon zöldre vált, és a rendszer a **csatlakozást**.

    ![importálása](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>Ügyféloldali profil törlése

1. Válassza a törölni kívánt ügyféloldali profil melletti három pontot (...). Ezután válassza az **Eltávolítás**lehetőséget.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Válassza az **Eltávolítás** elemet a törléshez.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>Kapcsolatok problémáinak diagnosztizálása

1. A kapcsolódási problémák diagnosztizálásához használhatja a **Diagnosztizálás** eszközt. Válassza ki a használni kívánt VPN-kapcsolat melletti három pontot (...) a menü megjelenítéséhez. Ezután válassza a **Diagnosztizálás**lehetőséget.

    ![diagnosztizálása](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. A **kapcsolatok tulajdonságai** lapon válassza a **diagnosztika futtatása**lehetőséget.

    ![diagnosztizálása](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Jelentkezzen be a hitelesítő adataival.

    ![diagnosztizálása](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. A diagnosztika eredményeinek megtekintése.

    ![diagnosztizálása](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>Virtuális WAN megtekintése

1. Lépjen a virtuális WAN-ra.
2. Az Áttekintés lapon a térképen látható pontok mindegyike egy elosztót jelöl. Az elosztók állapotösszegzéséért vigye a mutatót az egyes pontok fölé.
3. Az elosztók és kapcsolatok szakaszában láthatja az elosztók állapotát, helyét, régióját, VPN-kapcsolati állapotát, valamint a bájtban kifejezett be- és kimenő forgalmát.

## <a name="viewhealth"></a>Az erőforrás állapotának megtekintése

1. Lépjen a WAN-ra.
2. A WAN lapjának **TÁMOGATÁS + hibaelhárítás** szakaszában kattintson az **Állapot** lehetőségre, és tekintse meg az erőforrást.


## <a name="cleanup"></a>Erőforrások törlése

Ha már nincs szükség ezekre az erőforrásokra, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást. A „myResourceGroup” helyére írja be az erőforráscsoport nevét, és futtassa a következő PowerShell-parancsot:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Következő lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.
