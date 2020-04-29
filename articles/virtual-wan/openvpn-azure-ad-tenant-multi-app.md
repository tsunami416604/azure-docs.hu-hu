---
title: 'Virtuális WAN: Azure AD-bérlő különböző felhasználói csoportokhoz: Azure AD-hitelesítés'
description: A P2S VPN használatával kapcsolódhat a VNet az Azure AD-hitelesítés használatával
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: af5ff5817ee9ae7e6d7432fe281ecb440bf25b9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80060719"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Azure Active Directory bérlő létrehozása a P2S OpenVPN protokoll kapcsolataihoz

A VNet való csatlakozáskor tanúsítványalapú hitelesítést vagy RADIUS-hitelesítést használhat. Ha azonban a nyílt VPN protokollt használja, akkor Azure Active Directory hitelesítést is használhat. Ha azt szeretné, hogy a különböző felhasználók a különböző átjárók számára is képesek legyenek csatlakozni, több alkalmazást is regisztrálhat az AD-ben, és összekapcsolhatja őket különböző átjárókkal.

Ebből a cikkből megtudhatja, hogyan állíthat be Azure AD-bérlőt az P2S OpenVPN-hitelesítéshez, és hogyan hozhat létre és regisztrálhat több alkalmazást az Azure AD-ben, hogy különböző felhasználók és csoportok számára engedélyezze a hozzáférést.

> [!NOTE]
> Az Azure AD-hitelesítés csak az OpenVPN&reg; protokoll kapcsolatai esetén támogatott.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-create-a-new-p2s-configuration"></a><a name="site"></a>6. új P2S-konfiguráció létrehozása

A pont–hely konfiguráció határozza meg a távoli ügyfelek csatlakoztatására vonatkozó paramétereket.

1. Állítsa be a következő változókat, és szükség szerint cserélje le az értékeket a környezetéhez.

   ```azurepowershell-interactive
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. A konfiguráció létrehozásához futtassa a következő parancsokat:

   ```azurepowershell-interactive
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

   > [!NOTE]
   > Ne használja az Azure VPN-ügyfél alkalmazás-AZONOSÍTÓját a fenti parancsokban: az összes felhasználó számára hozzáférést biztosít az átjáróhoz. Használja a regisztrált alkalmazás (ok) AZONOSÍTÓját.

## <a name="7-edit-hub-assignment"></a><a name="hub"></a>7. hub-hozzárendelés szerkesztése

1. Navigáljon a virtuális WAN alatt lévő **hubok** panelre.

2. Válassza ki azt a hubot, amelyhez hozzá szeretné rendelni a VPN-kiszolgáló konfigurációját, majd kattintson a három pontra (...).

    ![új hely](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. Kattintson a **virtuális központ szerkesztése**elemre.

4. Jelölje be a **pont – hely átjáró belefoglalása** jelölőnégyzetet, és válassza ki a kívánt **átjáró-méretezési egységet** .

    ![új hely](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. Adja meg azt a **címkészletet** , amelyből a VPN-ügyfelek IP-címeket rendelnek.

6. Kattintson a **Megerősítés** gombra.

7. A művelet végrehajtása akár 30 percet is igénybe vehet.

## <a name="8-download-vpn-profile"></a><a name="device"></a>8. a VPN-profil letöltése

A VPN-profillal konfigurálhatja az ügyfeleket.

1. A virtuális WAN lapján kattintson a **felhasználói VPN-konfigurációk**elemre.

2. A lap tetején kattintson a **felhasználói VPN-konfiguráció letöltése**elemre.

3. Miután befejeződött a fájl létrehozása, a hivatkozásra kattintva letöltheti.

4. A profil fájl segítségével konfigurálja a VPN-ügyfeleket.

5. Bontsa ki a letöltött zip-fájlt.

6. Tallózással keresse meg a kibontott "AzureVPN" mappát.

7. Jegyezze fel a "azurevpnconfig. xml" fájl helyét. A azurevpnconfig. XML a VPN-kapcsolat beállítását tartalmazza, és közvetlenül importálható az Azure VPN-ügyfélalkalmazás alkalmazásba. Ezt a fájlt az összes olyan felhasználó számára is terjesztheti, akiknek e-mailben vagy más módon kell csatlakozniuk. A felhasználónak érvényes Azure AD-beli hitelesítő adatokra lesz szüksége a sikeres kapcsolódáshoz.

## <a name="9-configure-user-vpn-clients"></a>9. felhasználói VPN-ügyfelek konfigurálása

A csatlakozáshoz le kell töltenie az Azure VPN-ügyfelet, és importálnia kell az előző lépésekben letöltött VPN-ügyféloldali profilt minden olyan számítógépen, amely csatlakozni szeretne a VNet.

> [!NOTE]
> Az Azure AD-hitelesítés csak az OpenVPN&reg; protokoll kapcsolatai esetén támogatott.
>

#### <a name="to-download-the-azure-vpn-client"></a>Az Azure VPN-ügyfél letöltése

Ezzel a [hivatkozással](https://go.microsoft.com/fwlink/?linkid=2117554) töltheti le az Azure VPN-ügyfelet.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Ügyféloldali profil importálása

1. A lapon válassza az **Importálás**lehetőséget.

    ![importálása](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. Keresse meg a profil XML-fájlját, és jelölje ki. A fájl kijelölése után válassza a **Megnyitás**lehetőséget.

    ![importálása](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. Adja meg a profil nevét, majd válassza a **Mentés**lehetőséget.

    ![importálása](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. Válassza a **Csatlakozás** lehetőséget a VPN-hez való csatlakozáshoz.

    ![importálása](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. A csatlakozás után az ikon zöldre vált, és a rendszer a **csatlakozást**.

    ![importálása](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Ügyféloldali profil törlése

1. Válassza a törölni kívánt ügyféloldali profil melletti három pontot (...). Ezután válassza az **Eltávolítás**lehetőséget.

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. Válassza az **Eltávolítás** elemet a törléshez.

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="to-diagnose-connection-issues"></a><a name="diagnose"></a>A kapcsolódási problémák diagnosztizálása

1. A kapcsolódási problémák diagnosztizálásához használhatja a **Diagnosztizálás** eszközt. Válassza ki a használni kívánt VPN-kapcsolat melletti három pontot (...) a menü megjelenítéséhez. Ezután válassza a **Diagnosztizálás**lehetőséget.

    ![diagnosztizálása](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. A **kapcsolatok tulajdonságai** lapon válassza a **diagnosztika futtatása**lehetőséget.

    ![diagnosztizálása](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Jelentkezzen be a hitelesítő adataival.

    ![diagnosztizálása](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. A diagnosztika eredményeinek megtekintése.

    ![diagnosztizálása](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="10-view-your-virtual-wan"></a><a name="viewwan"></a>10. a virtuális WAN megtekintése

1. Lépjen a virtuális WAN-ra.

2. Az Áttekintés lapon a térképen látható pontok mindegyike egy elosztót jelöl.

3. Az elosztók és kapcsolatok szakaszában láthatja az elosztók állapotát, helyét, régióját, VPN-kapcsolati állapotát, valamint a bájtban kifejezett be- és kimenő forgalmát.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Az erőforrások eltávolítása

Ha már nincs szükség ezekre az erőforrásokra, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást. A „myResourceGroup” helyére írja be az erőforráscsoport nevét, és futtassa a következő PowerShell-parancsot:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.
