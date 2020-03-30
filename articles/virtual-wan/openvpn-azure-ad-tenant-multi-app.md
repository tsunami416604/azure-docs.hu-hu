---
title: 'Virtuális WAN: Azure AD-bérlő különböző felhasználói csoportokhoz: Azure AD-hitelesítés'
description: A P2S VPN használatával azure AD-hitelesítéssel csatlakozhat a virtuális hálózathoz
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: af5ff5817ee9ae7e6d7432fe281ecb440bf25b9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060719"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Azure Active Directory-bérlő létrehozása P2S OpenVPN protokollkapcsolatokhoz

A virtuális hálózathoz való csatlakozáskor tanúsítványalapú hitelesítést vagy RADIUS-hitelesítést használhat. Azonban az Open VPN protokoll használatakor is használhatja az Azure Active Directory-hitelesítést. Ha azt szeretné, hogy a felhasználók különböző felhasználói készletei képesek legyenek különböző átjárókhoz csatlakozni, több alkalmazást regisztrálhat az AD-ben, és összekapcsolhatja őket különböző átjárókhoz.

Ez a cikk segít beállítani egy Azure AD-bérlő p2s OpenVPN-hitelesítéshez, és hozzon létre és regisztráljon több alkalmazást az Azure AD-ben, hogy különböző hozzáférést biztosítson a különböző felhasználók és csoportok számára.

> [!NOTE]
> Az Azure AD-hitelesítés csak&reg; OpenVPN protokollkapcsolatok esetén támogatott.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-create-a-new-p2s-configuration"></a><a name="site"></a>6. Hozzon létre egy új P2S konfiguráció

A pont–hely konfiguráció határozza meg a távoli ügyfelek csatlakoztatására vonatkozó paramétereket.

1. Állítsa be a következő változókat, és szükség szerint cserélje le az értékeket a környezetben.

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
   > Ne használja az Azure VPN-ügyfél alkalmazásazonosítóját a fenti parancsokban: Minden felhasználó számára hozzáférést biztosít az átjáróhoz. Használja a regisztrált alkalmazás(ok) azonosítóját.

## <a name="7-edit-hub-assignment"></a><a name="hub"></a>7. Hub-hozzárendelés szerkesztése

1. Keresse meg a **Hubs panelt** a virtuális WAN alatt.

2. Válassza ki azt a hubot, amelyhez a VPN-kiszolgáló konfigurációját társítani szeretné, majd kattintson a három pontra (...).

    ![új hely](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. Kattintson **a Virtuális központ szerkesztése gombra.**

4. Jelölje be a **Pont-hely átjáró felvétele** jelölőnégyzetet, és válassza ki a kívánt **átjáróméretezési egységet.**

    ![új hely](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. Adja meg azt a **címkészletet,** amelyből a VPN-ügyfelek IP-címeket kapnak.

6. Kattintson a **Megerősítés** gombra.

7. A művelet befejezése akár 30 percet is igénybe vehet.

## <a name="8-download-vpn-profile"></a><a name="device"></a>8. VPN-profil letöltése

A VPN-profillal konfigurálhatja az ügyfeleket.

1. A virtuális WAN lapján kattintson a **Felhasználói VPN-konfigurációk**elemre.

2. A lap tetején kattintson a **Felhasználói VPN-konfiguráció letöltése gombra.**

3. Miután befejeződött a fájl létrehozása, a hivatkozásra kattintva letöltheti.

4. A profilfájl segítségével konfigurálhatja a VPN-ügyfeleket.

5. Bontsa ki a letöltött zip fájlt.

6. Tallózással keresse meg a kibontott "AzureVPN" mappát.

7. Jegyezze fel az "azurevpnconfig.xml" fájl helyét. Az azurevpnconfig.xml tartalmazza a VPN-kapcsolat beállítását, és közvetlenül az Azure VPN-ügyfélalkalmazásba importálható. Ezt a fájlt minden olyan felhasználónak is terjesztheti, amelyiknek e-mailben vagy más módon kell csatlakoznia. A sikeres csatlakozáshoz a felhasználónak érvényes Azure AD-hitelesítő adatokra van szüksége.

## <a name="9-configure-user-vpn-clients"></a>9. Felhasználói VPN-ügyfelek konfigurálása

A csatlakozáshoz le kell töltenie az Azure VPN-ügyfelet, és importálnia kell az előző lépésekben letöltött VPN-ügyfélprofilt minden olyan számítógépen, amely csatlakozni szeretne a virtuális hálózathoz.

> [!NOTE]
> Az Azure AD-hitelesítés csak&reg; OpenVPN protokollkapcsolatok esetén támogatott.
>

#### <a name="to-download-the-azure-vpn-client"></a>Az Azure VPN-ügyfél letöltése

Ezen a [hivatkozáson](https://go.microsoft.com/fwlink/?linkid=2117554) keresztül letöltheti az Azure VPN-ügyfelet.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Ügyfélprofil importálása

1. A lapon válassza az **Importálás**lehetőséget.

    ![Importálása](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. Tallózással keresse meg a profil XML-fájlját, és jelölje ki. Ha a fájl ki van jelölve, válassza a **Megnyitás**gombot.

    ![Importálása](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. Adja meg a profil nevét, és válassza a **Mentés gombot.**

    ![Importálása](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. A VPN-hez való csatlakozáshoz válassza a **Csatlakozás** lehetőséget.

    ![Importálása](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. Miután csatlakozott, az ikon zöldre vált, és azt mondja **Connected**.

    ![Importálása](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Ügyfélprofil törlése

1. Jelölje ki a törölni kívánt ügyfélprofil melletti három pontot (...). Ezután válassza **az Eltávolítás lehetőséget.**

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. A törléshez válassza az **Eltávolítás** gombot.

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="to-diagnose-connection-issues"></a><a name="diagnose"></a>Csatlakozási problémák diagnosztizálása

1. A csatlakozási problémák diagnosztizálásához használja a **Diagnosztizálás** eszközt. Válassza ki a három pontot (...) a diagnosztizálni kívánt VPN-kapcsolat mellett, hogy felfedje a menüt. Ezután válassza **a Diagnosztizálás lehetőséget.**

    ![Diagnosztizálni](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. A **Kapcsolat tulajdonságai** lapon válassza a **Diagnosztika futtatása**lehetőséget.

    ![Diagnosztizálni](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Jelentkezzen be a hitelesítő adataival.

    ![Diagnosztizálni](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Tekintse meg a diagnosztikai eredményeket.

    ![Diagnosztizálni](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="10-view-your-virtual-wan"></a><a name="viewwan"></a>10. Tekintse meg virtuális WAN

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
