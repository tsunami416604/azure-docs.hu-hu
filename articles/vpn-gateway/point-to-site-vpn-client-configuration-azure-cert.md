---
title: 'Hozzon létre, és telepítse a P2S VPN-ügyfélkonfigurációs fájlok Azure Tanúsítványalapú hitelesítés: Azure |} A Microsoft Docs'
description: Hozzon létre és telepítse a Windows, Linux, (strongSwan) Linux és Mac OS X VPN ügyfél konfigurációs fájljainak a P2S-tanúsítvány hitelesítéséhez.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/05/2018
ms.author: cherylmc
ms.openlocfilehash: 11d23102ca807ab1ddf41f1d0e72aed8a8513ac8
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636645"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Hozzon létre és telepítse a VPN-ügyfélkonfigurációs fájlok az Azure natív Tanúsítványalapú hitelesítés P2S konfiguráció

VPN-ügyfél konfigurációs fájljainak egy zip-fájlt tartalmazza. Konfigurációs fájlokat adja meg a natív Windows, az IKEv2 VPN Mac vagy Linux rendszerű ügyfelek Azure natív tanúsítványalapú hitelesítést használó pont – hely kapcsolaton keresztül egy virtuális hálózathoz való kapcsolódáshoz szükséges beállításokat. További információk a pont–hely kapcsolatokról: [Információk a pont–hely VPN-ről](point-to-site-about.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

>[!NOTE]
>Ügyfél-konfigurációs fájlok kapcsolódnak a virtuális hálózatok közötti VPN konfigurációját. Ha módosítania kellene a pont – hely VPN-konfiguráció után hozza létre a VPN-ügyfélkonfigurációs fájlok, például a VPN-protokoll típusa vagy a hitelesítési típus, ügyeljen arra, hozzon létre új VPN-ügyfél konfigurációs fájljainak a felhasználói eszközökön.
>
>

## <a name="generate"></a>VPN-ügyfél konfigurációs fájlok létrehozása

Mielőtt elkezdené, győződjön meg arról, hogy az összes csatlakozó felhasználók rendelkeznek-e egy érvényes tanúsítványt a felhasználói eszközökön telepített. Ügyféltanúsítvány telepítésével kapcsolatos további információkért lásd: [telepíthet ügyféltanúsítványt](point-to-site-how-to-vpn-client-install-azure-cert.md).

PowerShell-lel, ügyfél-konfigurációs fájlok is létrehozhat vagy az Azure portal használatával. Mindkét módszer ugyanazt a zip-fájlt adja vissza. Bontsa ki a fájlt, a következő mappák megtekintése:

  * **WindowsAmd64** és **WindowsX86**, amely tartalmaz a Windows 32 bites és 64 bites csomagok, illetve. A **WindowsAmd64** telepítőcsomag van az összes támogatott 64 bites Windows-ügyfeleken, nem csak Amd.
  * **Általános**, amely tartalmazza a saját VPN-ügyfél konfigurációja létrehozásához használt általános információkat. Az általános mappában akkor áll elő, ha IKEv2- vagy SSTP + IKEv2 lett konfigurálva, az átjárón. Ha csak az SSTP van beállítva, majd az általános mappában nem szerepel.

### <a name="zipportal"></a>Hozzon létre fájlokat az Azure portal használatával

1. Az Azure Portalon lépjen a virtuális hálózati átjáró a virtuális hálózat, amelyhez csatlakozni kíván.
2. A virtuális hálózati átjáró oldalán kattintson **pont – hely konfiguráció**.
3. A pont – hely konfiguráció lap tetején kattintson **VPN-ügyfél letöltése**. Néhány percig tart az ügyfél konfigurációs csomagjának létrehozása.
4. A böngésző azt jelzi, hogy egy ügyfél-konfigurációs zip fájl érhető el. A neve megegyezik az átjáró neve. Bontsa ki a fájlt, a mappa megtekintéséhez.

### <a name="zipps"></a>Hozzon létre fájlokat a PowerShell használatával

1. Amikor létrehozni VPN-ügyfél konfigurációs fájlokat, az érték "– AuthenticationMethod a(z)"EapTls"van. Hozzon létre a VPN-ügyfélkonfigurációs fájlok a következő paranccsal:

  ```powershell
  $profile=New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

  $profile.VPNProfileSASUrl
  ```
2. Másolja az URL-címet a böngésző töltheti le a zip-fájlt, majd bontsa ki a fájlt a mappa megtekintéséhez.

## <a name="installwin"></a>Windows

Használhatja az azonos VPN-ügyfélkonfigurációs csomagot minden Windows ügyfélszámítógépen, mindaddig, amíg a verzió megegyezik az architektúra az ügyfél. A pont – hely című szakaszában talál által támogatott ügyfél operációs rendszerek listáját a [VPN Gateway – gyakori kérdések](vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Rendszergazdai jogosultságokkal kell rendelkeznie az ügyfélszámítógépen Windows csatlakoztatni szeretné.
>
>

A következő lépések használatával konfigurálja a natív Windows VPN-ügyfél hitelesítési tanúsítvány:

1. Válassza ki a Windows rendszerű számítógép architektúrájának megfelelő VPN-ügyfélkonfigurációs fájlokat. 64 bites processzorarchitektúra esetén a „VpnClientSetupAmd64” telepítőcsomagot válassza. 32 bites processzorarchitektúra esetén a „VpnClientSetupX86” telepítőcsomagot válassza. 
2. Kattintson duplán a csomagra a telepítéséhez. Ha megjelenik a SmartScreen egy előugró ablaka, kattintson a **További információ**, majd a **Futtatás mindenképpen** elemre.
3. Nyissa meg az ügyfélszámítógépen a **Hálózati beállítások** eszközt, és kattintson a **VPN** elemre. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott. 
4. Mielőtt megkísérli a csatlakozást, ellenőrizze, hogy telepített-e ügyféltanúsítványt az ügyfélszámítógépen. A natív Azure-tanúsítványhitelesítési típus használata esetén a hitelesítéshez ügyféltanúsítványra van szükség. További információ a tanúsítványok létrehozása: [készítése a tanúsítványok](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Ügyféltanúsítvány telepítésével kapcsolatos információkért lásd: [telepíthet ügyféltanúsítványt](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="installmac"></a>Mac (OS X)

 A natív IKEv2 VPN-ügyfelet manuálisan kell konfigurálnia az Azure-hoz csatlakozó Mac gépek mindegyikén. Az Azure nem biztosít mobilkonfigurációs fájlt a natív Azure-tanúsítványhitelesítéshez. A **általános** az összes szükséges konfigurációs információkat tartalmazza. Ha nem látja a Generic mappát a letöltésben, valószínűleg nem az IKEv2 lett kiválasztva alagúttípusként. Az IKEv2 kiválasztását követően hozza létre újból a ZIP-fájlt a Generic mappa lekéréséhez.<br>A Generic mappa az alábbi fájlokat tartalmazza:

* **VpnSettings.xml**, amely tartalmazza a fontos beállítások, például a kiszolgáló címét és az alagút típusát. 
* **VpnServerRoot.cer**, amely tartalmazza a legfelső szintű tanúsítvány P2S-kapcsolat beállítása során az Azure VPN Gateway érvényesítéséhez szükséges.

Használja az alábbi lépéseket Mac natív VPN-ügyfél konfigurálása a tanúsítvány hitelesítéséhez. Minden Azure-ra csatlakozó Mac gépen a lépések elvégzéséhez rendelkezésére:

1. Importálás a **VpnServerRoot** Mac legfelső szintű tanúsítványok Ezt megteheti a fájl keresztül másolja a Mac és a dupla kattintással.  
Kattintson a **Hozzáadás** importálásához.

  ![tanúsítvány hozzáadása](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Kattintson duplán a tanúsítványra lehet, hogy nem jelennek meg a **Hozzáadás** párbeszédpanelen, de a tanúsítvány telepítve van a megfelelő tárolóban. A tanúsítvány meg a bejelentkezési kulcsláncban a tanúsítványok kategória alatt ellenőrizheti.
    >
  
2. Győződjön meg arról, hogy telepítette-e a legfelső szintű tanúsítvány P2S-beállítások konfigurálása, ha az Azure-bA feltöltött által kiadott ügyféltanúsítvány. Ez eltér az előző lépésben telepített VPNServerRoot. Az ügyféltanúsítvány hitelesítést, és megadása kötelező. További információ a tanúsítványok létrehozása: [készítése a tanúsítványok](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Ügyféltanúsítvány telepítésével kapcsolatos információkért lásd: [telepíthet ügyféltanúsítványt](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Nyissa meg a **hálózati** párbeszédpanel **hálózati beállítások** kattintson **"+"** a P2S-kapcsolat az Azure VNet új VPN-ügyfél kapcsolati profil létrehozásához.

  A **felület** értéke "VPN" és a **VPN-típust** értéke "IKEv2". Adjon meg egy nevet a profilnak a a **szolgáltatásnév** mezőbe, majd kattintson a **létrehozás** a VPN-ügyfél-csatlakozási profil létrehozásához.

  ![network](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. Az a **általános** mappába, az a **VpnSettings.xml** fájlt, másolja a **VpnServer** címke értéke. Illessze be ezt az értéket a **kiszolgálócím** és **távoli azonosítója** mezőket a profil.

  ![Kiszolgálóadatok](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Kattintson a **hitelesítési beállítások** válassza **tanúsítvány**. 

  ![hitelesítési beállítások](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
6. Kattintson a **kiválasztása...** a hitelesítéshez használandó ügyféltanúsítványt választhat. Ez az a 2. lépésben telepített tanúsítvány.

  ![tanúsítvány](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Válassza ki az identitás** közül választhat tanúsítványainak listáját jeleníti meg. Válassza ki a megfelelő tanúsítványt, majd kattintson a **Folytatás**.

  ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. Az a **helyi azonosítója** mezőben adja meg azt a tanúsítványt (a 6. lépés). Ebben a példában a "ikev2Client.com". Kattintson a **alkalmaz** gombra kattintva mentse a módosításokat.

  ![alkalmaz](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. Az a **hálózati** párbeszédpanelen kattintson a **alkalmaz** összes módosítások mentéséhez. Kattintson a **Connect** elindításához a P2S-kapcsolat az Azure vnetre.

## <a name="linuxgui"></a>Linux (strongSwan grafikus felhasználói felület)

### <a name="extract-the-key-and-certificate"></a>Bontsa ki a kulcsot, és a tanúsítvány

StrongSwan szeretne csomagolja ki a kulcsot, és a tanúsítvány az ügyfél-tanúsítvány (.pfx-fájl), és mentse azokat az egyes .pem fájlt.
Kövesse az alábbi lépéseket:

1. Töltse le és telepítse az OpenSSL [OpenSSL](https://www.openssl.org/source/).
2. Nyisson meg egy parancssori ablakot, és váltson arra a könyvtárra, amelyen OpenSSL, például "c:\OpenSLL-Win64\bin\'.
3. Bontsa ki a titkos kulcsot, és mentse az ügyféltanúsítványt a "privatekey.pem" nevű új fájlt a következő parancsot futtassa:

  ```
  C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nocerts -out privatekey.pem -nodes
  ```
4.  Most futtassa a következő parancsot a nyilvános tanúsítvány kibontása, és mentse azt egy új fájlt:

  ```
  C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nokeys -out publiccert.pem -nodes
  ```

### <a name="install"></a>Telepítése és konfigurálása

Az alábbi utasítások alapján létrehozott strongSwan 5.5.1 Ubuntu 17.0.4 rendszeren keresztül. Ubuntu 16.0.10 strongSwan grafikus felhasználói felület nem támogatja. Ha azt szeretné, Ubuntu 16.0.10 használandó, kell használni a [parancssori](#linuxinstallcli). Az alábbi példák nem feltétlenül egyeznek a képernyőn megjelenő látja, Linux- és strongSwan verziójától függően.

1. Nyissa meg a **terminálon** telepítéséhez **strongSwan** és a hálózatkezelő, a példában a parancs futtatásával. Ha hibaüzenet jelenik meg, amely kapcsolódik *libcharon-extra – beépülő modulok*, cserélje le a "strongswan-beépülő modul – eap-mschapv2".

  ```
  sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
  ```
2. Válassza ki a **hálózatkezelő** ikonra (up-nyíl/lefelé mutató nyilat), majd válassza ki **kapcsolatok szerkesztése**.

  ![kapcsolatok szerkesztése](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Kattintson a **Hozzáadás** gombra kattintva hozzon létre egy új kapcsolatot.

  ![kapcsolat hozzáadása](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Válassza ki **IPsec vagy IKEv2 (strongswan)** a legördülő menüből, és kattintson a **létrehozás**. Ebben a lépésben a kapcsolat át lehet nevezni.

  ![Válassza ki a kapcsolat típusa](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Nyissa meg a **VpnSettings.xml** fájlt a **általános** a letöltött ügyfél-konfigurációs fájlokban lévő mappára. Keresse meg a címke neve **VpnServer** , és másolja a neve "azuregateway" kezdve és a ". cloudapp.net".

  ![példány neve](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Illessze be a nevét a **cím** mezőt az új VPN-kapcsolat a **átjáró** szakaszban. Ezután válassza a mappa ikont a végén a **tanúsítvány** mezőben keresse meg a **általános** mappában, és válassza a **VpnServerRoot** fájlt.
7. Az a **ügyfél** szakasz a kapcsolat a **hitelesítési**, jelölje be **tanúsítvány és titkos kulcs**. A **tanúsítvány** és **titkos kulcs**, válassza ki a tanúsítványt és a titkos kulcsot, amely a korábban létrehozott. A **beállítások**válassza **egy belső IP-cím kérése**. Kattintson a **Hozzáadás**.

  ![egy belső IP-cím kérése](./media/point-to-site-vpn-client-configuration-azure-cert/inneripreq.png)
8. Kattintson a **hálózatkezelő** (up-nyíl/lefelé mutató nyilat) ikonra, és vigye a kurzort **VPN-kapcsolatok**. Megjelenik a létrehozott VPN-kapcsolatot. Kattintson a kapcsolat létrehozására.

## <a name="linuxinstallcli"></a>Linux (strongSwan parancssori felület)

### <a name="install-strongswan"></a>StrongSwan telepítése

A következő CLI-parancsokat használhatja, vagy strongSwan szereplő lépések segítségével a [grafikus felhasználói Felülettel](#install) strongSwan telepítéséhez.

1. `apt-get install strongswan-ikev2 strongswan-plugin-eap-tls`
2. `apt-get install libstrongswan-standard-plugins`

### <a name="install-and-configure"></a>Telepítés és konfigurálás

1. A VPN-ügyfele csomag letöltése az Azure Portalról.
2. Bontsa ki a fájlt.
3. Az a **általános** mappát, másolja vagy helyezze át a VpnServerRoot.cer /etc/ipsec.d/cacerts.
4. Másolja, vagy helyezze át cp client.p12 /etc/ipsec.d/private/. Ez a fájl az Azure VPN-átjáró ügyfél-tanúsítványa.
5. Nyissa meg a VpnSettings.xml fájlt, és másolja a <VpnServer> értéket. Ez az érték a következő lépésben fogja használni.
6. Az alábbi példában szereplő értékeket módosítsa, majd adja hozzá a példában a /etc/ipsec.conf konfigurációhoz.
  
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
6. Adja hozzá a következőt */etc/ipsec.secrets*.

  ```
  : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
  ```

7. Futtassa az alábbi parancsot:

  ```
  # ipsec restart
  # ipsec up azure
  ```

## <a name="next-steps"></a>További lépések

Térjen vissza a cikkhez a [a P2S-konfigurálás befejezéséhez](vpn-gateway-howto-point-to-site-rm-ps.md).

A P2S-kapcsolatokkal kapcsolatos hibaelhárítás a következő cikkekben talál:

  * [Az Azure pont – hely kapcsolatok hibaelhárítása](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [A Mac OS X VPN-ügyfelek a VPN-kapcsolatok hibaelhárítása](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
