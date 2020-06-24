---
title: 'A P2S VPN-ügyfél konfigurációs fájljainak létrehozása & telepítése: tanúsítványalapú hitelesítés'
titleSuffix: Azure VPN Gateway
description: Windows, Linux, Linux (alapú strongswan) és Mac OS X VPN-ügyfél konfigurációs fájljainak létrehozása és telepítése a P2S-tanúsítvány hitelesítéséhez.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/04/2020
ms.author: cherylmc
ms.openlocfilehash: d917bc1d52cc2a43e87affcc9c5e3c2ab533da07
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2020
ms.locfileid: "84984868"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>A VPN-ügyfél konfigurációs fájljainak létrehozása és telepítése az Azure natív tanúsítványalapú hitelesítést használó pont–hely kapcsolatokhoz

A VPN-ügyfél konfigurációs fájljai zip-fájlban vannak tárolva. A konfigurációs fájlok biztosítják a natív Windows-, Mac-IKEv2 VPN-vagy Linux-ügyfelek számára, hogy olyan pont – hely kapcsolatokon keresztül csatlakozzanak a virtuális hálózathoz, amelyek natív Azure tanúsítványalapú hitelesítést használnak.

Az ügyfél-konfigurációs fájlok a virtuális hálózat VPN-konfigurációjához vannak jellemzőek. Ha a VPN-ügyfél konfigurációs fájljainak létrehozása után megváltozik a pont – hely VPN-konfiguráció, például a VPN protokoll típusa vagy a hitelesítés típusa, ne felejtsen el új VPN-ügyfél konfigurációs fájlokat készíteni a felhasználói eszközökhöz. 

* További információk a pont–hely kapcsolatokról: [Információk a pont–hely VPN-ről](point-to-site-about.md).
* Az OpenVPN-utasításokért lásd: [az OpenVPN konfigurálása a P2S](vpn-gateway-howto-openvpn.md) és az [OpenVPN-ügyfelek konfigurálása](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>VPN-ügyfél konfigurációs fájljainak előállítása

Mielőtt elkezdené, győződjön meg arról, hogy az összes csatlakozó felhasználó rendelkezik a felhasználó eszközére telepített érvényes tanúsítvánnyal. Az ügyféltanúsítványok telepítésével kapcsolatos további információkért lásd: [ügyféltanúsítvány telepítése](point-to-site-how-to-vpn-client-install-azure-cert.md).

Az ügyfél-konfigurációs fájlokat a PowerShell-lel vagy a Azure Portal használatával is létrehozhatja. Bármelyik metódus ugyanazt a zip-fájlt adja vissza. Bontsa ki a fájlt a következő mappák megtekintéséhez:

  * **WindowsAmd64** és **WindowsX86**, amelyek tartalmazzák a Windows 32 bites és a 64 bites telepítési csomagokat. A **WindowsAmd64** telepítőcsomag az összes támogatott 64-bites Windows-ügyfélhez használható, nem csak az AMD.
  * **Általános, amely**a saját VPN-ügyfél konfigurációjának létrehozásához használt általános információkat tartalmazza. Az általános mappa akkor van megadva, ha a IKEv2 vagy az SSTP + IKEv2 konfigurálva lett az átjárón. Ha csak az SSTP van konfigurálva, akkor az általános mappa nem jelenik meg.

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>Fájlok előállítása a Azure Portal használatával

1. A Azure Portal navigáljon annak a virtuális hálózatnak a virtuális hálózati átjáróhoz, amelyhez csatlakozni szeretne.
2. A virtuális hálózati átjáró lapon kattintson a **pont – hely konfiguráció**elemre.

   ![ügyféloldali portál letöltése](./media/point-to-site-vpn-client-configuration-azure-cert/client-configuration-portal.png)
3. A pont – hely konfiguráció lap tetején kattintson a **VPN-ügyfél letöltése**elemre. Az ügyfél-konfigurációs csomag létrehozása néhány percet vesz igénybe.
4. A böngésző azt jelzi, hogy az ügyfél-konfiguráció zip-fájlja elérhető. Neve megegyezik az átjáró nevével. A mappák megtekintéséhez bontsa ki a fájlt.

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>Fájlok készítése a PowerShell használatával


1. A VPN-ügyfél konfigurációs fájljainak létrehozásakor a "-AuthenticationMethod" értéke "EapTls". A következő paranccsal hozhatja elő a VPN-ügyfél konfigurációs fájljait:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. Másolja az URL-címet a böngészőjébe a zip-fájl letöltéséhez, majd bontsa ki a fájlt a mappák megtekintéséhez.

## <a name="windows"></a><a name="installwin"></a>Windows

Ugyanazt a VPN-ügyfél-konfigurációs csomagot használhatja minden Windows-ügyfélszámítógépen, ha a verzió megfelel az ügyfél architektúrájának. A támogatott ügyféloldali operációs rendszerek listáját a [VPN Gateway gyakori kérdések](vpn-gateway-vpn-faq.md#P2S)pont – hely szakasza tartalmazza.

>[!NOTE]
>Rendszergazdai jogosultságokkal kell rendelkeznie azon a Windows-ügyfélszámítógépen, amelyről csatlakozni szeretne.
>
>

A következő lépésekkel konfigurálhatja a natív Windows VPN-ügyfelet a tanúsítványalapú hitelesítéshez:

1. Válassza ki a Windows rendszerű számítógép architektúrájának megfelelő VPN-ügyfélkonfigurációs fájlokat. 64 bites processzorarchitektúra esetén a „VpnClientSetupAmd64” telepítőcsomagot válassza. 32 bites processzorarchitektúra esetén a „VpnClientSetupX86” telepítőcsomagot válassza. 
2. Kattintson duplán a csomagra a telepítéséhez. Ha megjelenik a SmartScreen egy előugró ablaka, kattintson a **További információ**, majd a **Futtatás mindenképpen** elemre.
3. Nyissa meg az ügyfélszámítógépen a **Hálózati beállítások** eszközt, és kattintson a **VPN** elemre. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott. 
4. Mielőtt megkísérli a csatlakozást, ellenőrizze, hogy telepített-e ügyféltanúsítványt az ügyfélszámítógépen. A natív Azure-tanúsítványhitelesítési típus használata esetén a hitelesítéshez ügyféltanúsítványra van szükség. A tanúsítványok létrehozásával kapcsolatos további információkért lásd: [tanúsítványok létrehozása](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Az ügyféltanúsítványok telepítésével kapcsolatos információkért lásd: [ügyféltanúsítvány telepítése](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="mac-os-x"></a><a name="installmac"></a>Mac (OS X)

 A natív IKEv2 VPN-ügyfelet manuálisan kell konfigurálnia az Azure-hoz csatlakozó Mac gépek mindegyikén. Az Azure nem biztosít mobilkonfigurációs fájlt a natív Azure-tanúsítványhitelesítéshez. Az **általános** tartalmazza az összes, a konfigurációhoz szükséges információt. Ha nem látja a Generic mappát a letöltésben, valószínűleg nem az IKEv2 lett kiválasztva alagúttípusként. Vegye figyelembe, hogy a VPN Gateway alapszintű SKU nem támogatja a IKEv2. Az IKEv2 kiválasztását követően hozza létre újból a ZIP-fájlt a Generic mappa lekéréséhez.<br>A Generic mappa az alábbi fájlokat tartalmazza:

* **VpnSettings.xml**, amely olyan fontos beállításokat tartalmaz, mint a kiszolgáló címe és az alagút típusa. 
* **VpnServerRoot. cer**, amely tartalmazza az Azure-VPN Gateway ellenőrzéséhez szükséges főtanúsítványt a P2S-kapcsolatok beállítása során.

A következő lépésekkel konfigurálhatja a natív VPN-ügyfelet a Mac számítógépen a tanúsítványalapú hitelesítéshez. Ezeket a lépéseket minden olyan Mac gépen végre kell hajtania, amely csatlakozni fog az Azure-hoz:

1. Importálja a **VpnServerRoot** legfelső szintű tanúsítványát a Mac számítógépére. Ezt úgy teheti meg, hogy átmásolja a fájlt a Mac gépre, és duplán rákattint rá. Kattintson a **Hozzáadás** elemre az importáláshoz.

   ![tanúsítvány hozzáadása](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Ha duplán kattint a tanúsítványra, előfordulhat, hogy nem jeleníti meg a **Hozzáadás** párbeszédpanelt, de a tanúsítvány telepítve van a megfelelő tárolóban. A tanúsítvány kategóriájában a bejelentkezési kulcstartóban tekintheti meg a tanúsítványt.
    >
  
2. Ellenőrizze, hogy telepítette-e az Azure-ba a P2S-beállítások konfigurálásakor feltöltött főtanúsítvány által kiadott ügyféltanúsítványt. Ez eltér az előző lépésben telepített VPNServerRoot. Az ügyféltanúsítvány hitelesítésre szolgál, és kötelező megadni. A tanúsítványok létrehozásával kapcsolatos további információkért lásd: [tanúsítványok létrehozása](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Az ügyféltanúsítványok telepítésével kapcsolatos információkért lásd: [ügyféltanúsítvány telepítése](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Nyissa meg a **hálózat** párbeszédpanelt a **hálózati beállítások** területen, és kattintson a **"+"** gombra egy új VPN-ügyfél kapcsolati profil létrehozásához az Azure Virtual Network P2S-kapcsolathoz.

   Az **illesztőfelület** értéke "VPN", a **VPN-típus** értéke pedig "IKEv2". Adja meg a profil nevét a **szolgáltatás neve** mezőben, majd kattintson a **Létrehozás** elemre a VPN-ügyfél kapcsolati profiljának létrehozásához.

   ![network](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. Az **általános** mappában, a **VpnSettings.xml** fájlból másolja a **VpnServer** címke értékét. Illessze be ezt az értéket a profil **kiszolgáló címe** és a **Távoli azonosító** mezőibe.

   ![kiszolgáló adatai](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Kattintson a **hitelesítési beállítások** elemre, és válassza a **tanúsítvány**elemet.A **Catalina**esetében kattintson a **nincs** , majd a **tanúsítvány** elemre.

   ![hitelesítési beállítások](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)

   * A Catalina esetében válassza a **nincs** , majd a **tanúsítvány**lehetőséget. **Válassza ki** a megfelelő tanúsítványt:
   
   ![Catalina](./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png)

6. Kattintson a **kijelölés...** lehetőségre. Válassza ki a hitelesítéshez használni kívánt ügyféltanúsítványt. Ez az a tanúsítvány, amelyet a 2. lépésben telepített.

   ![tanúsítvány](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Válasszon ki egy identitást** a tanúsítványok listájának megjelenítéséhez. Válassza ki a megfelelő tanúsítványt, majd kattintson a **Continue (folytatás**) gombra.

   ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. A **helyi azonosító** mezőben adja meg a tanúsítvány nevét (a 6. lépésből). Ebben a példában ez a "ikev2Client.com". Ezután kattintson az **Apply (alkalmaz** ) gombra a módosítások mentéséhez.

   ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. A **hálózat** párbeszédpanelen kattintson az **alkalmaz** gombra az összes módosítás mentéséhez. Ezután kattintson a **Kapcsolódás** gombra a P2S-kapcsolat Azure-beli virtuális hálózatra való indításához.

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (alapú strongswan GUI)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>A alapú strongswan telepítése

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>Tanúsítványok előállítása

Ha még nem hozott létre tanúsítványokat, kövesse az alábbi lépéseket:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>Telepítés és konfigurálás

Az alábbi utasítások az Ubuntu 18.0.4 lettek létrehozva. Az Ubuntu-16.0.10 nem támogatja a alapú strongswan grafikus felhasználói felületét. Ha Ubuntu-16.0.10 szeretne használni, a [parancssort](#linuxinstallcli)kell használnia. Előfordulhat, hogy az alábbi példák nem egyeznek meg a megjelenő képernyőkkel a Linux és a alapú strongswan verziójától függően.

1. A példában a parancs futtatásával nyissa meg a **terminált** a **alapú strongswan** és a hálózati kezelő telepítéséhez.

   ```
   sudo apt install network-manager-strongswan
   ```
2. Válassza a **Beállítások**, majd a **hálózat**lehetőséget.

   ![kapcsolatok szerkesztése](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. **+** Új kapcsolatok létrehozásához kattintson a gombra.

   ![kapcsolatok hozzáadása](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Válassza az **IPSec/IKEv2 (alapú strongswan)** lehetőséget a menüben, majd kattintson duplán a elemre. Ebben a lépésben megadhatja a kapcsolatok nevét.

   ![kapcsolattípus kiválasztása](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Nyissa meg a **VpnSettings.xml** fájlt a letöltött ügyfél-konfigurációs fájlokban található **általános** mappából. Keresse meg a **VpnServer** nevű címkét, és másolja a nevet a "azuregateway" kezdetű és a ". cloudapp.net" végződéssel.

   ![másolat neve](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Illessze be ezt a nevet az új VPN-kapcsolat **címe** mezőbe az **átjáró** szakaszban. Ezután válassza ki a mappa ikont a **tanúsítvány** mező végén, keresse meg az **általános** mappát, és válassza ki a **VpnServerRoot** fájlt.
7. A kapcsolatok **ügyfél** szakaszában, a **hitelesítéshez**válassza a **tanúsítvány/titkos kulcs**lehetőséget. A **tanúsítvány** és a **titkos kulcs**esetében válassza ki a korábban létrehozott tanúsítványt és titkos kulcsot. A **Beállítások**területen válassza a **belső IP-cím kérése**lehetőséget. Ezután kattintson a **Hozzáadás**gombra.

   ![belső IP-cím kérése](./media/point-to-site-vpn-client-configuration-azure-cert/turnon.png)
8. Kapcsolja **be a következőt:.**

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (alapú strongswan CLI)

### <a name="install-strongswan"></a>A alapú strongswan telepítése

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Tanúsítványok előállítása

Ha még nem hozott létre tanúsítványokat, kövesse az alábbi lépéseket:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Telepítés és konfigurálás

1. Töltse le a ügyfele csomagot a Azure Portalról.
2. Bontsa ki a fájlt.
3. Másolja vagy helyezze át az VpnServerRoot. cer mappát az **általános** mappából a/etc/IPSec.d/cacerts.
4. CP Client. P12 másolása vagy áthelyezése a/etc/IPSec.d/Private/.-be Ez a fájl az Azure VPN Gatewayhoz tartozó ügyféltanúsítvány.
5. Nyissa meg VpnSettings.xml fájlt, és másolja az `<VpnServer>` értéket. Ezt az értéket a következő lépésben fogja használni.
6. Módosítsa az alábbi példában szereplő értékeket, majd adja hozzá a példát a/etc/IPSec.conf-konfigurációhoz.
  
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
6. Adja hozzá a következőt a */etc/IPSec.Secrets*.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. Futtassa az alábbi parancsot:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>További lépések

A [P2S-konfiguráció befejezéséhez](vpn-gateway-howto-point-to-site-rm-ps.md)térjen vissza a cikkhez.

A P2S-kapcsolatok hibáinak megoldásához tekintse meg a következő cikkeket:

  * [Azure pont – hely kapcsolatok hibaelhárítása](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Mac OS X VPN-ügyfelektől érkező VPN-kapcsolatok hibáinak megoldása](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
