---
title: 'A P2S VPN-ügyfél konfigurációs fájljainak & telepítése: tanúsítványhitelesítés'
titleSuffix: Azure VPN Gateway
description: Hozzon létre és telepítsen Windows, Linux, Linux (strongSwan) és Mac OS X VPN-ügyfélkonfigurációs fájlokat a P2S tanúsítványhitelesítéshez.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/04/2020
ms.author: cherylmc
ms.openlocfilehash: d15efee635e131d658cd650b7f80eb9e670a0dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279415"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>VPN-ügyfélkonfigurációs fájlok létrehozása és telepítése natív Azure-tanúsítványhitelesítési P2S-konfigurációkhoz

A VPN-ügyfél konfigurációs fájljai zip fájlban találhatók. A konfigurációs fájlok biztosítják a natív Windows, Mac IKEv2 VPN vagy Linux-ügyfelek számára szükséges beállításokat, amelyek a natív Azure-tanúsítványhitelesítést használó point-to-site kapcsolatokon keresztül csatlakozhatnak egy virtuális hálózathoz.

Az ügyfélkonfigurációs fájlok a virtuális hálózat VPN-konfigurációjára jellemzőek. Ha a VPN-ügyfél konfigurációs fájljainak létrehozása után bármilyen változás történik a pont-hely VPN-konfigurációban, például a VPN-protokoll típusán vagy a hitelesítési típuson, mindenképpen hozzon létre új VPN-ügyfélkonfigurációs fájlokat a felhasználói eszközök számára. 

* További információk a pont–hely kapcsolatokról: [Információk a pont–hely VPN-ről](point-to-site-about.md).
* Az OpenVPN-utasításokról az [OpenVPN konfigurálása P2S-hez](vpn-gateway-howto-openvpn.md) és [OpenVPN-ügyfelek konfigurálása című témakörben talál.](vpn-gateway-howto-openvpn-clients.md)

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>VPN-ügyfél konfigurációs fájljainak létrehozása

Mielőtt elkezdené, győződjön meg arról, hogy minden csatlakozó felhasználó rendelkezik érvényes tanúsítvánnyal a felhasználó eszközén. Az ügyféltanúsítványok telepítéséről az [Ügyféltanúsítvány telepítése című témakörben](point-to-site-how-to-vpn-client-install-azure-cert.md)talál további információt.

Ügyfélkonfigurációs fájlokat hozhat létre a PowerShell használatával, vagy az Azure Portalhasználatával. Mindkét módszer ugyanazt a zip fájlt adja vissza. Csomagolja ki a fájlt a következő mappák megtekintéséhez:

  * **A WindowsAmd64** és a **WindowsX86**, amelyek a Windows 32, illetve a 64 bites telepítőcsomagokat tartalmazzák. A **WindowsAmd64** telepítőcsomag minden támogatott 64 bites Windows-ügyfélhez tartozik, nem csak az Amd-hez.
  * **Általános**, amely a saját VPN-ügyfélkonfiguráció létrehozásához használt általános információkat tartalmazza. Az Általános mappa akkor érhető el, ha az IKEv2 vagy az SSTP+IKEv2 konfigurálva volt az átjárón. Ha csak az SSTP van konfigurálva, akkor az Általános mappa nincs jelen.

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>Fájlok létrehozása az Azure Portalon

1. Az Azure Portalon keresse meg annak a virtuális hálózatnak a virtuális hálózati átjáróját, amelyhez csatlakozni szeretne.
2. A virtuális hálózati átjáró lapon kattintson a **Pont-hely konfigurációgombra.**

   ![ügyfélportál letöltése](./media/point-to-site-vpn-client-configuration-azure-cert/client-configuration-portal.png)
3. A Pont-hely konfigurációs lap tetején kattintson a **VPN-ügyfél letöltése gombra.** Az ügyfélkonfigurációs csomag létrehozása néhány percet vesz igénybe.
4. A böngésző azt jelzi, hogy elérhető egy ügyfélkonfigurációs zip fájl. A neve megegyezik az átjáró nevével. Csomagolja ki a fájlt a mappák megtekintéséhez.

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>Fájlok létrehozása a PowerShell használatával


1. VPN-ügyfélkonfigurációs fájlok létrehozásakor a "-AuthenticationMethod" értéke "EapTls". A VPN-ügyfél konfigurációs fájljainak létrehozása a következő paranccsal:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. Másolja az URL-címet a böngészőbe a zip fájl letöltéséhez, majd csomagolja ki a fájlt a mappák megtekintéséhez.

## <a name="windows"></a><a name="installwin"></a>Windows

Minden Windows-ügyfélszámítógépen használhatja ugyanazt a VPN-ügyfélkonfigurációs csomagot, amennyiben a verzió megfelel az ügyfél architektúrájának. A támogatott ügyféloperációs rendszerek listáját a [VPN-átjáró](vpn-gateway-vpn-faq.md#P2S)– gyakori kérdések pont-hely szakaszában talál.

>[!NOTE]
>Rendszergazdai jogosultságokkal kell rendelkeznie azon a Windows-ügyfélszámítógépen, amelyről csatlakozni kíván.
>
>

A natív Windows VPN-ügyfél tanúsítványhitelesítésre történő konfigurálásához kövesse az alábbi lépéseket:

1. Válassza ki a Windows rendszerű számítógép architektúrájának megfelelő VPN-ügyfélkonfigurációs fájlokat. 64 bites processzorarchitektúra esetén a „VpnClientSetupAmd64” telepítőcsomagot válassza. 32 bites processzorarchitektúra esetén a „VpnClientSetupX86” telepítőcsomagot válassza. 
2. Kattintson duplán a csomagra a telepítéséhez. Ha megjelenik a SmartScreen egy előugró ablaka, kattintson a **További információ**, majd a **Futtatás mindenképpen** elemre.
3. Nyissa meg az ügyfélszámítógépen a **Hálózati beállítások** eszközt, és kattintson a **VPN** elemre. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott. 
4. Mielőtt megkísérli a csatlakozást, ellenőrizze, hogy telepített-e ügyféltanúsítványt az ügyfélszámítógépen. A natív Azure-tanúsítványhitelesítési típus használata esetén a hitelesítéshez ügyféltanúsítványra van szükség. A tanúsítványok létrehozásáról a Tanúsítványok létrehozása című [témakörben](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)talál további információt. Az ügyféltanúsítványok telepítéséről az [Ügyféltanúsítvány telepítése című](point-to-site-how-to-vpn-client-install-azure-cert.md)témakörben talál további információt.

## <a name="mac-os-x"></a><a name="installmac"></a>Mac (OS X)

 A natív IKEv2 VPN-ügyfelet manuálisan kell konfigurálnia az Azure-hoz csatlakozó Mac gépek mindegyikén. Az Azure nem biztosít mobilkonfigurációs fájlt a natív Azure-tanúsítványhitelesítéshez. Az **általános** tartalmazza a konfigurációhoz szükséges összes információt. Ha nem látja a Generic mappát a letöltésben, valószínűleg nem az IKEv2 lett kiválasztva alagúttípusként. Vegye figyelembe, hogy a VPN-átjáró alaptermékváltozata nem támogatja az IKEv2-t. Az IKEv2 kiválasztását követően hozza létre újból a ZIP-fájlt a Generic mappa lekéréséhez.<br>A Generic mappa az alábbi fájlokat tartalmazza:

* **VpnSettings.xml**, amely olyan fontos beállításokat tartalmaz, mint a kiszolgáló címe és a bújtatás típusa. 
* **VpnServerRoot.cer**, amely tartalmazza az Azure VPN-átjáró p2S-kapcsolat beállítása során az azure VPN-átjáró érvényesítéséhez szükséges főtanúsítványt.

Az alábbi lépésekkel konfigurálhatja a natív VPN-ügyfelet maces tanúsítványhitelesítésre. Ezeket a lépéseket minden Azure-hoz csatlakozó Macen el kell végeznie:

1. Importálja a **VpnServerRoot** főtanúsítványt a Macre. Ezt úgy teheti meg, hogy a fájlt átmásolja a Macre, és duplán kattint rá. Kattintson a **Hozzáadás** gombra az importáláshoz.

   ![tanúsítvány hozzáadása](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Előfordulhat, hogy a tanúsítványra duplán kattintva nem jelenik meg a **Hozzáadás** párbeszédpanel, de a tanúsítvány a megfelelő tárolóban van telepítve. A tanúsítványt a bejelentkezési kulcskarikában a tanúsítványkategóriában ellenőrizheti.
    >
  
2. Ellenőrizze, hogy telepített-e olyan ügyféltanúsítványt, amelyet az Azure-ba feltöltött főtanúsítvány adott ki a P2S-beállítások konfigurálásakor. Ez eltér az előző lépésben telepített VPNServerRoot-tól. Az ügyféltanúsítvány hitelesítésre szolgál, és kötelező. A tanúsítványok létrehozásáról a Tanúsítványok létrehozása című [témakörben](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)talál további információt. Az ügyféltanúsítványok telepítéséről az [Ügyféltanúsítvány telepítése című](point-to-site-how-to-vpn-client-install-azure-cert.md)témakörben talál további információt.
3. Nyissa **meg** a Hálózati párbeszédpanelt a **Hálózati beállítások csoportban,** és kattintson a **'+'** gombra egy új VPN-ügyfélkapcsolati profil létrehozásához az Azure virtuális hálózathoz való P2S-kapcsolathoz.

   A **kapcsolat** értéke "VPN", **a VPN-típus** értéke pedig "IKEv2". Adja meg a profil nevét a **Szolgáltatásnév** mezőben, majd kattintson a **Létrehozás gombra** a VPN-ügyfél kapcsolati profiljának létrehozásához.

   ![network](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. Az **Általános** mappa **VpnSettings.xml** fájlból másolja a **VpnServer** címke értékét. Illessze be ezt az értéket a profil **Kiszolgálócím** és **Távoli azonosító** mezőjébe.

   ![kiszolgáló adatai](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Kattintson **a Hitelesítési beállítások gombra,** és válassza a **Tanúsítvány**lehetőséget.**Catalina**esetén kattintson a **Nincs** elemre, majd **a tanúsítványra**

   ![hitelesítési beállítások](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)

   * Catalina esetén válassza a **Nincs** lehetőséget, majd **a Tanúsítvány lehetőséget.** **Válassza ki** a megfelelő tanúsítványt:
   
   ![Catalina](./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png)

6. Kattintson **a Kijelölés gombra...** a hitelesítéshez használni kívánt ügyféltanúsítvány kiválasztásához. Ez a tanúsítvány, amelyet a 2.

   ![tanúsítvány](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Válassza az Identitás lehetőséget,** amely megjeleníti a választható tanúsítványok listáját. Jelölje ki a megfelelő tanúsítványt, majd kattintson a **Folytatás gombra.**

   ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. A **Helyi azonosító** mezőben adja meg a tanúsítvány nevét (a 6. lépéstől). Ebben a példában ez a "ikev2Client.com". Ezután kattintson az **Alkalmaz** gombra a módosítások mentéséhez.

   ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. A **Hálózat** párbeszédpanelen kattintson az **Alkalmaz** gombra az összes módosítás mentéséhez. Ezután kattintson a **Csatlakozás** gombra a P2S-kapcsolat elindításához az Azure virtuális hálózathoz.

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (erősSwan GUI)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>Telepítse strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>Tanúsítványok előállítása

Ha még nem hozott létre tanúsítványokat, kövesse az alábbi lépéseket:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>Telepítés és konfigurálás

A következő utasításokat az Ubuntu 18.0.4-en hozták létre. Ubuntu 16.0.10 nem támogatja a strongSwan GUI. Ha az Ubuntu 16.0.10-et szeretné használni, akkor a parancssort kell [használnia.](#linuxinstallcli) Az alábbi példák nem egyeznek képernyők, hogy látod, attól függően, hogy a linuxos és strongSwan.

1. Nyissa meg a **terminált** a **strongSwan** és hálózati kezelőjének telepítéséhez a példában található parancs futtatásával.

   ```
   sudo apt install network-manager-strongswan
   ```
2. Válassza a **Beállítások**lehetőséget, majd a **Hálózat**lehetőséget.

   ![kapcsolatok szerkesztése](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Új **+** kapcsolat létrehozásához kattintson a gombra.

   ![kapcsolat hozzáadása](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Válassza az **IPsec/IKEv2 (strongSwan)** parancsot a menüből, és kattintson duplán. Ebben a lépésben nevezheti el a kapcsolatot.

   ![kapcsolattípus kiválasztása](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Nyissa meg a **VpnSettings.xml** fájlt a letöltött **ügyfélkonfigurációs** fájlokban található Általános mappából. Keresse meg a **VpnServer** nevű címkét, és másolja a nevet, kezdve az "azuregateway" és a ".cloudapp.net" végződéssel.

   ![másolat neve](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Illessze be ezt a nevet az új VPN-kapcsolat **Cím** mezőjébe az **Átjáró** szakaszban. Ezután jelölje ki a **Tanúsítvány** mező végén lévő mappaikont, keresse meg az **Általános** mappát, és válassza a **VpnServerRoot** fájlt.
7. A kapcsolat **Ügyfél** szakaszában a **Hitelesítés**hez válassza a **Tanúsítvány/személyes kulcs**lehetőséget. A **Tanúsítvány** és **a Titkos kulcs esetében**válassza ki a korábban létrehozott tanúsítványt és személyes kulcsot. A **Beállítások párbeszédpanelen**válassza **a Belső IP-cím kérése**lehetőséget. Ezután kattintson a **Hozzáadás gombra.**

   ![belső IP-cím kérése](./media/point-to-site-vpn-client-configuration-azure-cert/turnon.png)
8. Kapcsolja be a **kapcsolatot**.

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (erősSwan CLI)

### <a name="install-strongswan"></a>Telepítse strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Tanúsítványok előállítása

Ha még nem hozott létre tanúsítványokat, kövesse az alábbi lépéseket:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Telepítés és konfigurálás

1. Töltse le a VPNClient csomagot az Azure Portalról.
2. Bontsa ki a fájlt.
3. Másolja **Generic** vagy helyezze át a VpnServerRoot.cer fájlt az /etc/ipsec.d/cacerts mappába.
4. Másolja vagy helyezze át a cp client.p12 címet /etc/ipsec.d/private/. Ez a fájl az Azure VPN-átjáró ügyféltanúsítványa.
5. Nyissa meg a VpnSettings.xml fájlt, és másolja az `<VpnServer>` értéket. Ezt az értéket fogja használni a következő lépésben.
6. Állítsa be az alábbi példában található értékeket, majd adja hozzá a példát az /etc/ipsec.conf konfigurációhoz.
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
6. Adja hozzá a következőket az */etc/ipsec.secrets könyvtárhoz.*

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. Futtassa az alábbi parancsot:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>További lépések

Térjen vissza a cikkhez [a P2S-konfiguráció befejezéséhez.](vpn-gateway-howto-point-to-site-rm-ps.md)

A P2S-kapcsolatok hibáiról az alábbi cikkekben lehet tudni:

  * [Az Azure pont-hely közötti kapcsolatainak hibaelhárítása](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Mac OS X VPN-ügyfelek VPN-kapcsolatainak – problémamegoldás](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
