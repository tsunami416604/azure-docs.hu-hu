---
title: 'Azure VPN-átjáró: Hozzon létre & telepíteni a VPN-ügyfél konfigurációs fájljait - P2S RADIUS-kapcsolatok'
description: Hozzon létre Windows, Mac OS X és Linux VPN-ügyfélkonfigurációs fájlokat radius-hitelesítést használó kapcsolatokhoz.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: 69517d69a26364cf1cc950d7aaa849522decacf1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732735"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>VPN-ügyfélkonfigurációs fájlok létrehozása és telepítése a P2S RADIUS-hitelesítéshez

Ha pontról helyre (P2S) keresztül szeretne virtuális hálózathoz csatlakozni, konfigurálnia kell azt az ügyféleszközt, amelyről csatlakozni fog. P2S VPN-kapcsolatokat Windows, Mac OS X és Linux ügyféleszközökről hozhat létre. 

RADIUS-hitelesítés használata esetén többféle hitelesítési lehetőség közül választhat: felhasználónév/jelszó hitelesítés, tanúsítványhitelesítés és egyéb hitelesítési típusok. A VPN-ügyfél konfigurációja minden hitelesítéstípus esetében eltérő. A VPN-ügyfél konfigurálásához a szükséges beállításokat tartalmazó ügyfélkonfigurációs fájlokat kell használnia. Ez a cikk segítséget nyújt a vpn-ügyfél konfigurációjának létrehozásában és telepítésében a használni kívánt RADIUS-hitelesítési típushoz.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

A P2S RADIUS-hitelesítés konfigurációs munkafolyamata a következő:

1. [Állítsa be az Azure VPN-átjárót a P2S-kapcsolathoz.](point-to-site-how-to-radius-ps.md)
2. [Állítsa be a RADIUS-kiszolgálót hitelesítésre.](point-to-site-how-to-radius-ps.md#radius) 
3. **Szerezze be a VPN-ügyfél konfigurációját a választott hitelesítési beállításhoz, és használja a VPN-ügyfél beállításához** (ez a cikk).
4. [Töltse ki a P2S konfigurációt és csatlakoztassa](point-to-site-how-to-radius-ps.md)a .

>[!IMPORTANT]
>Ha a VPN-ügyfél konfigurációs profiljának létrehozása után bármilyen változás történik a pont-hely VPN-konfigurációban, például a VPN-protokoll típusában vagy a hitelesítéstípusán, új VPN-ügyfélkonfigurációt kell létrehoznia és telepítenie a felhasználók eszközeire.
>
>

A cikk szakaszainak használatához először döntse el, hogy milyen típusú hitelesítést szeretne használni: felhasználónév/jelszó, tanúsítvány vagy más típusú hitelesítés. Minden szakasz a Windows, a Mac OS X és a Linux (jelenleg korlátozott lépések) lépéseit ismerteti.


## <a name="usernamepassword-authentication"></a><a name="adeap"></a>Felhasználónév/jelszó hitelesítése

A felhasználónév-jelszó hitelesítést beállíthatja az Active Directory használatára, vagy az Active Directory használatára. Mindkét esetben győződjön meg arról, hogy minden csatlakozó felhasználó rendelkezik a RADIUS-on keresztül hitelesíthető felhasználónév/jelszó hitelesítő adatokkal.

Felhasználónév/jelszó hitelesítésének konfigurálásakor csak az EAP-MSCHAPv2 felhasználónév/jelszó hitelesítési protokoll konfigurációját hozhatja létre. A parancsokban `-AuthenticationMethod` `EapMSChapv2`a .

### <a name="1-generate-vpn-client-configuration-files"></a><a name="usernamefiles"></a>1. VPN-ügyfél konfigurációs fájlok létrehozása

A VPN-ügyfél konfigurációs fájlokat az Azure Portalon vagy az Azure PowerShell használatával hozhat létre.

#### <a name="azure-portal"></a>Azure Portal

1. Keresse meg a virtuális hálózati átjárót.
2. Kattintson **a pont-hely konfigurációja gombra.**
3. Kattintson **a VPN-ügyfél letöltése gombra.**
4. Válassza ki az ügyfelet, és töltse ki a kért információkat.
5. A **.zip** fájl létrehozásához kattintson a Letöltés gombra.
6. A .zip fájl letöltése általában a Letöltések mappába lesz.

#### <a name="azure-powershell"></a>Azure PowerShell

VPN-ügyfélkonfigurációs fájlok létrehozása felhasználónév-jelszó hitelesítéshez. A VPN-ügyfél konfigurációs fájljait a következő paranccsal hozhatja létre:

```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
A parancs futtatása hivatkozást ad vissza. Másolja és illessze be a linket egy webböngészőbe a **VpnClientConfiguration.zip**fájl letöltéséhez. Csomagolja ki a fájlt a következő mappák megtekintéséhez: 
 
* **WindowsAmd64** és **WindowsX86**: Ezek a mappák a Windows 64 bites, illetve a 32 bites telepítőcsomagokat tartalmazzák. 
* **Általános**: Ez a mappa általános információkat tartalmaz, amelyeket a saját VPN-ügyfélkonfiguráció létrehozásához használ. A felhasználónév-jelszó hitelesítési konfigurációkhoz nincs szükség erre a mappára.
* **Mac**: Ha a virtuális hálózati átjáró létrehozásakor konfigurálta az IKEv2-t, megjelenik egy **Mac** nevű mappa, amely egy **mobileconfig** fájlt tartalmaz. Ezzel a fájllal konfigurálhatja a Mac-ügyfeleket.

Ha már létrehozott ügyfélkonfigurációs fájlokat, a `Get-AzVpnClientConfiguration` parancsmag használatával lekérheti azokat. Ha azonban módosítja a P2S VPN-konfigurációt, például a VPN-protokoll típusát vagy a hitelesítés típusát, a konfiguráció nem frissül automatikusan. Új konfigurációs `New-AzVpnClientConfiguration` letöltés létrehozásához futtatnia kell a parancsmamot.

A korábban létrehozott ügyfélkonfigurációs fájlok beolvasásához használja a következő parancsot:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a>2. VPN-ügyfelek konfigurálása

A következő VPN-ügyfeleket konfigurálhatja:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux segítségével strongSwan](#adlinuxcli)
 
#### <a name="windows-vpn-client-setup"></a><a name="adwincli"></a>A Windows VPN-ügyfél beállítása

Minden Windows-ügyfélszámítógépen használhatja ugyanazt a VPN-ügyfélkonfigurációs csomagot, amennyiben a verzió megfelel az ügyfél architektúrájának. A támogatott ügyféloperációs rendszerek listáját a [GYAKRAN FELTETT KÉRDÉSEK című témakörben talál.](vpn-gateway-vpn-faq.md#P2S)

A natív Windows VPN-ügyfél tanúsítványhitelesítésre történő konfigurálásához kövesse az alábbi lépéseket:

1. Válassza ki a Windows rendszerű számítógép architektúrájának megfelelő VPN-ügyfélkonfigurációs fájlokat. 64 bites processzorarchitektúrához válassza a **VpnClientSetupAmd64** telepítőcsomagot. 32 bites processzorarchitektúrához válassza a **VpnClientSetupX86** telepítőcsomagot. 
2. A csomag telepítéséhez kattintson rá duplán. Ha megjelenik egy SmartScreen előugró ablak, válassza a **További információ** > **futtatása**lehetőséget.
3. Az ügyfélszámítógépen tallózással keresse meg a **Hálózati beállítások gombot,** és válassza a **VPN**lehetőséget. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott. 

#### <a name="mac-os-x-vpn-client-setup"></a><a name="admaccli"></a>Mac (OS X) VPN-ügyfél beállítása

1. Jelölje ki a **VpnClientSetup mobileconfig** fájlt, és küldje el az egyes felhasználóknak. Használhatja e-mail ben vagy más módszerrel.

2. Keresse meg a **mobileconfig** fájlt a Macen.

   ![A mobilkonfigurációs fájl helye](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)

3. Nem kötelező lépés – Ha egyéni DNS-t szeretne megadni, adja hozzá a következő sorokat a **mobileconfig** fájlhoz:

   ```xml
    <key>DNS</key>
    <dict>
      <key>ServerAddresses</key>
        <array>
            <string>10.0.0.132</string>
        </array>
      <key>SupplementalMatchDomains</key>
        <array>
            <string>TestDomain.com</string>
        </array>
    </dict> 
   ```
4. A telepítéshez kattintson duplán a profilra, és válassza a **Folytatás gombot.** A profil név megegyezik a virtuális hálózat nevével.

   ![Telepítési üzenet](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
5. Válassza a **Folytatás** lehetőséget, ha megbízik a profil küldőjében, és folytatja a telepítést.

   ![Megerősítő üzenet](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. A profil telepítése során megadhatja a VPN-hitelesítés hez szükséges felhasználónevet és jelszót. Ezeket az adatokat nem kötelező megadni. Ha így tesz, a rendszer menti az adatokat, és automatikusan felhasználja a kapcsolatot.A folytatáshoz válassza a **Telepítés** gombot.

   ![Felhasználónév és jelszó mezők vpn-hez](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. Adja meg a profil számítógépre való telepítéséhez szükséges jogosultságok felhasználónevét és jelszavát. Válassza **az OK gombot.**

   ![Felhasználónév és jelszó mezők a profil telepítéséhez](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. A profil telepítése után látható a **Profilok** párbeszédpanelen. Ezt a párbeszédpanelt később a **Rendszerbeállítások**párbeszédpanelen is megnyithatja.

   !["Profilok" párbeszédpanel](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. A VPN-kapcsolat eléréséhez nyissa meg a **Hálózat** párbeszédpanelt a **Rendszerbeállítások párbeszédpanelen.**

   ![Ikonok a Rendszerbeállítások ban](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. A VPN-kapcsolat **IkeV2-VPN**néven jelenik meg. A nevet a **mobileconfig** fájl frissítésével módosíthatja.

    ![A VPN-kapcsolat részletei](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. Válassza a **Hitelesítési beállítások lehetőséget.** Válassza a **Felhasználónév elemet** a listában, és adja meg a hitelesítő adatait. Ha korábban adta meg a hitelesítő adatokat, a rendszer automatikusan kiválasztja a **Felhasználónevet** a listában, és a felhasználónevet és a jelszót előre kitölti. A beállítások mentéséhez válassza az **OK gombot.**

    ![Hitelesítési beállítások](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. A **Hálózat** párbeszédpanelen válassza az **Alkalmaz** lehetőséget a módosítások mentéséhez. A kapcsolat elindításához válassza a **Csatlakozás**lehetőséget.

#### <a name="linux-vpn-client-setup-through-strongswan"></a><a name="adlinuxcli"></a>Linux VPN kliens beállítása a strongSwan-on keresztül

A következő utasításokat hoztak létre erősSwan 5.5.1 ubuntu 17.0.4. A tényleges képernyők eltérőek lehetnek, a Linux és a strongSwan verziójától függően.

1. Nyissa meg a **terminált** a **strongSwan** és hálózati kezelőjének telepítéséhez a példában található parancs futtatásával. Ha a rendszerrel kapcsolatos hibaüzenetet `libcharon-extra-plugins`kap, `strongswan-plugin-eap-mschapv2`cserélje le a helyére.

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Válassza a **Hálózatkezelő** ikont (felfelé nyíl/lefelé), majd a **Kapcsolatok szerkesztése**lehetőséget.

   !["Kapcsolatok szerkesztése" beállítás a Hálózatkezelőben](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Új kapcsolat létrehozásához kattintson a **Hozzáadás** gombra.

   !["Hozzáadás" gomb a kapcsolathoz](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Válassza az **IPsec/IKEv2 (strongswan)** lehetőséget a legördülő menüből, majd válassza a **Létrehozás parancsot.** Ebben a lépésben átnevezheti a kapcsolatot.

   ![A kapcsolat típusának kiválasztása](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Nyissa meg a **VpnSettings.xml** fájlt a letöltött **ügyfélkonfigurációs** fájlok Általános mappájából. Keresse meg `VpnServer` a hívott címkét, `azuregateway` és `.cloudapp.net`másolja a nevet, kezdve a és végződő .

   ![A VpnSettings.xml fájl tartalma](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Illessze be ezt a nevet az új VPN-kapcsolat **Cím** mezőjébe az **Átjáró** szakaszban. Ezután jelölje ki a **Tanúsítvány** mező végén lévő mappaikont, keresse meg az **Általános** mappát, és válassza a **VpnServerRoot** fájlt.
7. A kapcsolat **Ügyfél** szakaszában válassza az **EAP** **hitelesítéshez**lehetőséget, és adja meg a felhasználónevét és a jelszavát. Előfordulhat, hogy az adatok mentéséhez a jobb oldalon ki kell választania a lakat ikont. Ezután válassza a **Mentés gombot.**

   ![Kapcsolatbeállítások szerkesztése](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Jelölje ki a **Hálózatkezelő** ikont (felfelé nyíl/lefelé), és mutasson a **VPN-kapcsolatok**elemre. Megjelenik a létrehozott VPN-kapcsolat. A kapcsolat elindításához jelölje ki azt.

   !["VPN Radius" kapcsolat a Hálózatkezelőben](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certificate-authentication"></a><a name="certeap"></a>Tanúsítványhitelesítés
 
Az EAP-TLS protokollt használó RADIUS-tanúsítványhitelesítéshez VPN-ügyfélkonfigurációs fájlokat hozhat létre. A vállalati tanúsítvány általában a VPN-felhasználó hitelesítésére szolgál. Győződjön meg arról, hogy minden csatlakozó felhasználó rendelkezik az eszközeire telepített tanúsítvánnyal, és hogy a RADIUS-kiszolgáló érvényesítheti a tanúsítványt.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

A parancsokban `-AuthenticationMethod` `EapTls`a . A tanúsítványhitelesítés során az ügyfél a tanúsítvány ellenőrzésével ellenőrzi a RADIUS-kiszolgálót. `-RadiusRootCert`A RADIUS-kiszolgáló érvényesítéséhez használt főtanúsítványt tartalmazó .cer fájl.

Minden VPN-ügyféleszközhöz telepített ügyféltanúsítvány szükséges. Előfordulhat, hogy egy Windows-eszköz több ügyféltanúsítvánnyal rendelkezik. A hitelesítés során ez egy előugró párbeszédpanelt eredményezhet, amely felsorolja az összes tanúsítványt. A felhasználónak ezután ki kell választania a használni kívánt tanúsítványt. A megfelelő tanúsítvány kiszűrhető az ügyféltanúsítvány által láncolandó főtanúsítvány megadásával. 

`-ClientRootCert`A főtanúsítványt tartalmazó .cer fájl. Ez egy opcionális paraméter. Ha az eszköz, amelyről csatlakozni szeretne, csak egy ügyféltanúsítvánnyal rendelkezik, nem kell megadnia ezt a paramétert.

### <a name="1-generate-vpn-client-configuration-files"></a><a name="certfiles"></a>1. VPN-ügyfél konfigurációs fájlok létrehozása

VPN-ügyfélkonfigurációs fájlok létrehozása tanúsítványhitelesítéssel való használatra. A VPN-ügyfél konfigurációs fájljait a következő paranccsal hozhatja létre:
 
```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

A parancs futtatása hivatkozást ad vissza. Másolja és illessze be a hivatkozást egy webböngészőre a VpnClientConfiguration.zip fájl letöltéséhez. Csomagolja ki a fájlt a következő mappák megtekintéséhez:

* **WindowsAmd64** és **WindowsX86**: Ezek a mappák a Windows 64 bites, illetve a 32 bites telepítőcsomagokat tartalmazzák. 
* **GenericDevice**: Ez a mappa olyan általános információkat tartalmaz, amelyek a saját VPN-ügyfélkonfiguráció létrehozásához használatosak.

Ha már létrehozott ügyfélkonfigurációs fájlokat, a `Get-AzVpnClientConfiguration` parancsmag használatával lekérheti azokat. Ha azonban módosítja a P2S VPN-konfigurációt, például a VPN-protokoll típusát vagy a hitelesítés típusát, a konfiguráció nem frissül automatikusan. Új konfigurációs `New-AzVpnClientConfiguration` letöltés létrehozásához futtatnia kell a parancsmamot.

A korábban létrehozott ügyfélkonfigurációs fájlok beolvasásához használja a következő parancsot:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a>2. VPN-ügyfelek konfigurálása

A következő VPN-ügyfeleket konfigurálhatja:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (támogatott, még nincs cikklépés)

#### <a name="windows-vpn-client-setup"></a><a name="certwincli"></a>A Windows VPN-ügyfél beállítása

1. Jelöljön ki egy konfigurációs csomagot, és telepítse azt az ügyféleszközre. 64 bites processzorarchitektúrához válassza a **VpnClientSetupAmd64** telepítőcsomagot. 32 bites processzorarchitektúrához válassza a **VpnClientSetupX86** telepítőcsomagot. Ha megjelenik egy SmartScreen előugró ablak, válassza a **További információ** > **futtatása**lehetőséget. A csomagot mentheti is, így más ügyfélszámítógépekre is telepítheti.
2. Minden ügyfélnek ügyféltanúsítványra van szüksége a hitelesítéshez. Telepítse az ügyféltanúsítványt. Az ügyféltanúsítványokról a [pont-hely ügyféltanúsítványok](vpn-gateway-certificates-point-to-site.md)című témakörben talál további információt. A létrehozott tanúsítvány telepítéséről a [Tanúsítvány telepítése Windows-ügyfelekre](point-to-site-how-to-vpn-client-install-azure-cert.md)című témakörben szerepel.
3. Az ügyfélszámítógépen tallózással keresse meg a **Hálózati beállítások gombot,** és válassza a **VPN**lehetőséget. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott.

#### <a name="mac-os-x-vpn-client-setup"></a><a name="certmaccli"></a>Mac (OS X) VPN-ügyfél beállítása

Minden Olyan Mac-eszközhöz, amely az Azure virtuális hálózathoz csatlakozik, külön profilt kell létrehoznia. Ennek az az oka, hogy ezek az eszközök megkövetelik a hitelesítéshez szükséges felhasználói tanúsítványt a profilban. Az **Általános** mappa tartalmazza a profil létrehozásához szükséges összes információt:

* **A VpnSettings.xml** fájl olyan fontos beállításokat tartalmaz, mint a kiszolgáló címe és a bújtatás típusa.
* **A VpnServerRoot.cer** tartalmazza a VPN-átjáró p2S-kapcsolat beállítása során való érvényesítéséhez szükséges főtanúsítványt.
* **A RadiusServerRoot.cer** tartalmazza a RADIUS-kiszolgáló hitelesítés során idobe való érvényesítéséhez szükséges főtanúsítványt.

Az alábbi lépésekkel konfigurálhatja a natív VPN-ügyfelet maces hitelesítésre:

1. Importálja a **VpnServerRoot** és **a RadiusServerRoot** főtanúsítványokat a Macre. Másolja az egyes fájlokat a Macre, kattintson rá duplán, majd válassza **a Hozzáadás gombot.**

   ![VpnServerRoot tanúsítvány hozzáadása](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![RadiusServerRoot tanúsítvány hozzáadása](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Minden ügyfélnek ügyféltanúsítványra van szüksége a hitelesítéshez. Telepítse az ügyféltanúsítványt az ügyféleszközre.
3. Nyissa **meg** a Hálózat párbeszédpanelt a **Hálózati beállítások csoportban.** Bejelölésével **+** új VPN-ügyfélkapcsolati profilt hozhat létre az Azure virtuális hálózathoz való P2S-kapcsolathoz.

   A **kapcsolat** értéke **VPN**, a **VPN-típus** értéke **IKEv2**. Adja meg a profil nevét a **Szolgáltatásnév** mezőben, majd válassza a **Létrehozás** gombot a VPN-ügyfélkapcsolati profil létrehozásához.

   ![Kapcsolat- és szolgáltatásnév-információk](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. Az **Általános** mappa **VpnSettings.xml** fájlból másolja a **VpnServer** címke értékét. Illessze be ezt az értéket a profil **Kiszolgálócím** és **Távoli azonosító** mezőibe. Hagyja üresen a **Helyi azonosító** mezőt.

   ![Kiszolgáló adatai](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Válassza a **Hitelesítési beállítások**lehetőséget, majd a **Tanúsítvány**lehetőséget. 

   ![Hitelesítési beállítások](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. A **Kijelölés gombra** kattintva válassza ki a hitelesítéshez használni kívánt tanúsítványt.

   ![Tanúsítvány kiválasztása hitelesítéshez](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Válassza az Identitás lehetőséget,** amely megjeleníti a választható tanúsítványok listáját. Jelölje ki a megfelelő tanúsítványt, majd kattintson a **Folytatás gombra.**

   !["Identitás kiválasztása" lista](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. A **Helyi azonosító** mezőben adja meg a tanúsítvány nevét (a 6. lépéstől). Ebben a példában **ez ikev2Client.com**. Ezután a módosítások mentéséhez kattintson az **Alkalmaz** gombra.

   !["Helyi azonosító" mező](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. A **Hálózat** párbeszédpanelen válassza az **Alkalmaz** lehetőséget az összes módosítás mentéséhez. Ezután válassza a **Csatlakozás** lehetőséget a P2S-kapcsolat elindításához az Azure virtuális hálózathoz.

## <a name="working-with-other-authentication-types-or-protocols"></a><a name="otherauth"></a>Más hitelesítési típusok vagy protokollok használata

Ha más hitelesítési típust (például OTP) szeretne használni, vagy egy másik hitelesítési protokollt (például PEAP-MSCHAPv2-t szeretne használni az EAP-MSCHAPv2 helyett), létre kell hoznia saját VPN-ügyfélkonfigurációs profilját. A profil létrehozásához olyan információkra van szüksége, mint például a virtuális hálózati átjáró IP-címe, a bújtatás típusa és az osztott alagút útvonalai. Ezeket az információkat az alábbi lépésekkel szerezheti be:

1. A `Get-AzVpnClientConfiguration` parancsmag segítségével hozza létre az EapMSChapv2 VPN-ügyfélkonfigurációját.

2. Csomagolja ki a VpnClientConfiguration.zip fájlt, és keresse meg a **GenericDevice** mappát. Hagyja figyelmen kívül a Windows telepítőit tartalmazó mappákat a 64 bites és a 32 bites architektúrákhoz.
 
3. A **GenericDevice** mappa egy **VpnSettings**nevű XML-fájlt tartalmaz. Ez a fájl tartalmazza az összes szükséges információt:

   * **VpnServer:** Az Azure VPN-átjáró fqdn. Ez az a cím, amelyhez az ügyfél csatlakozik.
   * **VpnType**: A csatlakozáshoz használt bújtatási típus.
   * **Útvonalak:** A profilban konfigurálandó útvonalak, amelyek csak az Azure virtuális hálózathoz kötött forgalmat küldik el a P2S-alagúton keresztül.
   
   A **GenericDevice** mappa egy **VpnServerRoot**nevű .cer fájlt is tartalmaz. Ez a fájl tartalmazza az Azure VPN-átjáró p2S-kapcsolat beállítása során az Azure VPN-átjáró érvényesítéséhez szükséges főtanúsítványt. Telepítse a tanúsítványt az Azure virtuális hálózathoz csatlakozó összes eszközre.

## <a name="next-steps"></a>További lépések

Térjen vissza a cikkhez [a P2S-konfiguráció befejezéséhez.](point-to-site-how-to-radius-ps.md)

A P2S hibaelhárítási információkért olvassa el az [Azure pont-hely közötti kapcsolatok hibaelhárítása című témakört.](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
