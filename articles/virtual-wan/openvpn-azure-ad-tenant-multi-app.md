---
title: 'VPN Gateway: Azure AD-bérlő különböző felhasználói csoportokhoz: Azure AD-hitelesítés'
description: A P2S VPN használatával kapcsolódhat a VNet az Azure AD-hitelesítés használatával
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/10/2020
ms.author: alzam
ms.openlocfilehash: b390b5f8b00f61994db820a3af7bce26a3e0a30d
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934972"
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
> [!NOTE]
> Do not use the Azure VPN client's application ID in the commands above as it will grant all users access to the VPN gateway. Use the ID of the application(s) you registered.

## <a name="hub"></a>7. Edit hub assignment

1. Navigate to the **Hubs** blade under the virtual WAN.
2. Select the hub that you want to associate the vpn server configuration to and click the ellipsis (...).

   ![new site](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Click **Edit virtual hub**.
4. Check the **Include point-to-site gateway** check box and pick the **Gateway scale unit** that you want.

   ![new site](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Enter the **Address pool** from which the VPN clients will be assigned IP addresses.
6. Click **Confirm**.
7. The operation will can take up to 30 minutes to complete.

## <a name="device"></a>8. Download VPN profile

Use the VPN profile to configure your clients.

1. On the page for your virtual WAN, click **User VPN configurations**.
2. At the top of the  page, click **Download user VPN config**.
3. Once the file has finished creating, you can click the link to download it.
4. Use the profile file to configure the VPN clients.

4. Extract the downloaded zip file.

5. Browse to the unzipped “AzureVPN” folder.

6. Make a note of the location of the “azurevpnconfig.xml” file. The azurevpnconfig.xml contains the setting for the VPN connection and can be imported directly into the Azure VPN Client application. You can also distribute this file to all the users that need to connect via e-mail or other means. The user will need valid Azure AD credentials to connect successfully.
## Configure user VPN clients

To connect, you need to download the Azure VPN Client (Preview) and import the VPN client profile that was downloaded in the previous steps on every computer that wants to connect to the VNet.

> [!NOTE]
> Azure AD authentication is supported only for OpenVPN® protocol connections.
>

#### To download the Azure VPN client

Use this [link](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) to download the Azure VPN Client (Preview).

#### <a name="import"></a>To import a client profile

1. On the page, select **Import**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Browse to the profile xml file and select it. With the file selected, select **Open**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Specify the name of the profile and select **Save**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Select **Connect** to connect to the VPN.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Once connected, the icon will turn green and say **Connected**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>To delete a client profile

1. Select the ellipsis (...) next to the client profile that you want to delete. Then, select **Remove**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Select **Remove** to delete.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>Diagnose connection issues

1. To diagnose connection issues, you can use the **Diagnose** tool. Select the ellipsis (...) next to the VPN connection that you want to diagnose to reveal the menu. Then select **Diagnose**.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. On the **Connection Properties** page, select **Run Diagnosis**.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Sign in with your credentials.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. View the diagnosis results.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>View your virtual WAN

1. Navigate to the virtual WAN.
2. On the Overview page, each point on the map represents a hub. Hover over any point to view the hub health summary.
3. In the Hubs and connections section, you can view hub status, site, region, VPN connection status, and bytes in and out.

## <a name="viewhealth"></a>View your resource health

1. Navigate to your WAN.
2. On your WAN page, in the **SUPPORT + Troubleshooting** section, click **Health** and view your resource.


## <a name="cleanup"></a>Clean up resources

When you no longer need these resources, you can use [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) to remove the resource group and all of the resources it contains. Replace "myResourceGroup" with the name of your resource group and run the following PowerShell command:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Következő lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.