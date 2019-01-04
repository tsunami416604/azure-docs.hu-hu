---
title: 'Hozzon létre és telepítse a VPN-ügyfél konfigurációs fájljainak a P2S RADIUS-kapcsolatok: PowerShell: Azure |} A Microsoft Docs'
description: Windows, Mac OS X és Linux-VPN-ügyfél konfigurációs fájlok létrehozásához a RADIUS-hitelesítést használó kapcsolatok.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/07/2018
ms.author: cherylmc
ms.openlocfilehash: 52c7734c2af80d29433c20191d8b5b7c0ee0fe48
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "51252006"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Hozzon létre és telepítse a VPN-ügyfél konfigurációs fájljainak a P2S RADIUS-hitelesítés

Szeretne kapcsolódni egy virtuális hálózat pont – hely (P2S) keresztül, az ügyféleszközön, amelyről csatlakozik fogja konfigurálni kell. P2S VPN-kapcsolatok Windows, Mac OS X és Linux rendszerű ügyféleszközök létrehozhat. 

Ha a RADIUS-hitelesítést használ, több hitelesítési lehetőség van: felhasználóneves és jelszavas hitelesítéssel, a Tanúsítványalapú hitelesítés és a többi hitelesítési típusok. A VPN-ügyfél konfigurációja nem azonos az egyes hitelesítési. A VPN-ügyfél konfigurálásához a szükséges beállításokat tartalmazó konfigurációs fájlok ügyfél használja. Ez a cikk segítséget nyújt a létrehozása és telepítése a RADIUS hitelesítési típus, amely a használni kívánt VPN-konfiguráció.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

A P2S RADIUS-hitelesítés konfiguráció munkafolyamata a következőképpen történik:

1. [Állítsa be az Azure VPN gateway P2S-kapcsolatokhoz](point-to-site-how-to-radius-ps.md).
2. [Állítsa be a RADIUS-kiszolgáló hitelesítési](point-to-site-how-to-radius-ps.md#radius). 
3. **Szerezze be a VPN-ügyfél beállításainak konfigurálása a hitelesítés lehetőséget, majd állítsa be a VPN-ügyfél** (Ez a cikk).
4. [A P2S-konfigurálás befejezéséhez és a csatlakozás](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Ha módosítania kellene a pont – hely VPN-konfiguráció után hozza létre a VPN ügyfél konfigurációs profil, például a VPN-protokoll típusa vagy a hitelesítési típus, Ön hozza létre, és telepítse egy új VPN-ügyfél konfigurációja a felhasználók eszközeire.
>
>

Ez a cikk a szakaszok használatához először döntse el, használni kívánt hitelesítési típustól: felhasználónév/jelszó, tanúsítvány vagy más típusú hitelesítés. Minden szakasz a Windows, Mac OS X és Linux (korlátozott lépések jelenleg rendelkezésre álló) lépésből áll.

## <a name="adeap"></a>Felhasználónév-és jelszóalapú hitelesítés

Felhasználónév-és jelszóalapú hitelesítés vagy az Active Directory használatával, vagy használja az Active Directory konfigurálhatja. Mindkét forgatókönyvvel győződjön meg róla, hogy minden csatlakozó felhasználók rendelkeznek-e a felhasználónév/jelszó hitelesítő adatait, amely RADIUS révén.

Felhasználónév-és jelszóalapú hitelesítés konfigurálásakor csak a felhasználónév/jelszó EAP-MSCHAPv2 hitelesítési protokoll konfigurációt hozhat létre. A parancsokban szereplő `-AuthenticationMethod` van `EapMSChapv2`.

### <a name="usernamefiles"></a> 1. VPN-ügyfél konfigurációs fájljainak létrehozása

Hozzon létre a VPN-ügyfél konfigurációs fájljainak felhasználóneves és jelszavas hitelesítéssel való használatra. A VPN-ügyfélkonfigurációs fájlok a következő parancs használatával is létrehozhat:

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
A következő parancs futtatásával adja vissza egy hivatkozást. Másolja és illessze be a hivatkozásra kattintva töltheti le egy webes böngésző **VpnClientConfiguration.zip**. Bontsa ki a fájlt, a következő mappák megtekintése: 
 
* **WindowsAmd64** és **WindowsX86**: Ezeket a mappákat a Windows 64 bites és 32 bites installer csomagokat, illetve tartalmaznak. 
* **Általános**: Ez a mappa hozhat létre a saját VPN-ügyfél konfigurációja általános adatokat tartalmaz. Ez a mappa felhasználóneves és jelszavas hitelesítési konfigurációk esetén nem szükséges.
* **Mac**: Ha úgy állította be az IKEv2, a virtuális hálózati átjáró létrehozásakor, akkor egy nevű mappát láthatnak **Mac** , amely tartalmaz egy **mobileconfig** fájlt. Ez a fájl segítségével konfigurálhatja a Mac-ügyfél.

Ha már létrehozott ügyfél konfigurációs fájljainak, kérheti le azokat használatával a `Get-AzureRmVpnClientConfiguration` parancsmagot. Azonban ha módosításokat hajt végre a P2S VPN konfigurációját, például a VPN-protokoll típusa vagy a hitelesítési típus, a konfiguráció nem frissül automatikusan. Futtatnia kell a `New-AzureRmVpnClientConfiguration` parancsmaggal hozzon létre egy új konfigurációjának letöltéséhez.

Korábban létrehozott ügyfél-konfigurációs fájlok lekéréséhez használja a következő parancsot:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="setupusername"></a> 2. A VPN-ügyfelek konfigurálása

A következő VPN-ügyfelek konfigurálhatja:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux rendszerű strongSwan használatával](#adlinuxcli)
 
#### <a name="adwincli"></a>Windows VPN-ügyfél telepítése

Használhatja az azonos VPN-ügyfélkonfigurációs csomagot minden Windows ügyfélszámítógépen, mindaddig, amíg a verzió megegyezik az architektúra az ügyfél. A támogatott ügyfél operációs rendszerek listáját lásd: a [– gyakori kérdések](vpn-gateway-vpn-faq.md#P2S).

A következő lépések használatával konfigurálja a natív Windows VPN-ügyfél hitelesítési tanúsítvány:

1. Válassza ki a Windows rendszerű számítógép architektúrájának megfelelő VPN-ügyfélkonfigurációs fájlokat. Egy 64 bites processzor-architektúra, válassza a **VpnClientSetupAmd64** installer-csomag. Egy 32-bit-es processzor-architektúra, válassza a **VpnClientSetupX86** installer-csomag. 
2. Telepítse a csomagot, kattintson rá duplán. Ha egy SmartScreen előugró ablak jelenik meg, válassza ki a **szűrőfüggvénnyel** > **Futtatás mindenképpen**.
3. Az ügyfélszámítógépen, keresse meg a **hálózati beállítások** válassza **VPN**. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott. 

#### <a name="admaccli"></a>Mac (OS X) VPN-ügyfél telepítése

1. Válassza ki a **VpnClientSetup mobileconfig** fájlt, és küldje el az összes felhasználó. E-mailt vagy egy másik módszerrel is használhatja.

2. Keresse meg a **mobileconfig** Mac-fájlt

   ![A mobilconfig fájl helye](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)

3. Nem kötelező lépés – Ha azt szeretné, adja meg egy egyéni DNS, adja hozzá a következő sorokat a **mobileconfig** fájlt:
```
    <key>DNS</key>
    <dict>
      <key>ServerAddresses</key>
        <array>
            <string>10.0.0.132</string>
        <array>
      <key>SupplementalMatchDomains</key>
        <array>
            <string>TestDomain.com</string>
        </array>
    </dict> 
```
4. Kattintson duplán a profilt telepítheti, és válassza ki az **Folytatás**. A profil neve megegyezik a virtuális hálózat nevét.

   ![Telepítés üzenet](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
5. Válassza ki **Folytatás** megbízzon a küldő a profil, és folytassa a telepítést.

   ![Megerősítő üzenet](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. Profil telepítése során lehetősége van, adja meg a felhasználónevet és jelszót a VPN-hitelesítéshez. Akkor sem, adja meg ezt az információt kötelező. Ha így tesz, az adatok mentése és automatikusan használja a kapcsolat kezdeményezésekor. Válassza ki **telepítése** a folytatáshoz.

   ![Felhasználónév és jelszó mezők VPN-hez](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. Felhasználónév és jelszó megadása szükséges, telepítse a profilt a számítógépen a jogosultságokat. Kattintson az **OK** gombra.

   ![Felhasználónév és jelszó mezők profil telepítése](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. A profil telepítése után is látható, az a **profilok** párbeszédpanel bezárásához. Ezen a párbeszédpanelen, később is megnyithatja **rendszerbeállítások**.

   !["Profilok" párbeszédpanel](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. Használja a VPN-kapcsolatot, nyissa meg a **hálózati** párbeszédpanelt **rendszerbeállítások**.

   ![A rendszerbeállítások ikonok](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. A VPN-kapcsolat autópéldány **IkeV2 VPN**. A nevet módosíthatja frissítésével a **mobileconfig** fájlt.

   ![A VPN-kapcsolat részletei](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. Válassza ki **hitelesítési beállítások**. Válassza ki **felhasználónév** a listában, és adja meg a hitelesítő adatait. Ha a megadott hitelesítő adatokat, majd **felhasználónév** automatikusan a listában, és a felhasználónevet és jelszót a kiválasztott vannak töltve. Válassza ki **OK** a beállítások mentéséhez.

    ![Hitelesítési beállítások](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. Térjen vissza a **hálózati** párbeszédpanelen jelölje ki **alkalmaz** menti a módosításokat. Válassza ki a kapcsolat létrehozására, **Connect**.

#### <a name="adlinuxcli"></a>Keresztül strongSwan Linux VPN-ügyfél telepítése

Az alábbi utasítások alapján létrehozott strongSwan 5.5.1 Ubuntu 17.0.4 rendszeren keresztül. Tényleges képernyők Linux- és strongSwan verziójától függően eltérő lehet.

1. Nyissa meg a **terminálon** telepítéséhez **strongSwan** és a hálózatkezelő, a példában a parancs futtatásával. Ha hibaüzenet jelenik meg, amely kapcsolódik `libcharon-extra-plugins`, cserélje le a következőre `strongswan-plugin-eap-mschapv2`.

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Válassza ki a **hálózatkezelő** (up-nyíl/lefelé mutató nyilat) ikonra, és válassza ki **kapcsolatok szerkesztése**.

   ![A hálózatkezelő kiválasztása "Kapcsolatok szerkesztése"](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Válassza ki a **Hozzáadás** gombra kattintva hozzon létre egy új kapcsolatot.

   !["Hozzáadás" kapcsolat gomb](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Válassza ki **IPsec vagy IKEv2 (strongswan)** a legördülő menüből, és válassza ki a **létrehozás**. Ebben a lépésben a kapcsolat át lehet nevezni.

   ![A kapcsolat típusának kiválasztása](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Nyissa meg a **VpnSettings.xml** fájlt a **általános** a letöltött ügyfél konfigurációs fájljainak mappájába. Keresse meg a címke neve `VpnServer` , és másolja a nevét, kezdve `azuregateway` tagig terjedően `.cloudapp.net`.

   ![A VpnSettings.xml fájl tartalma](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Illessze be a nevét a **cím** mezőt az új VPN-kapcsolat a **átjáró** szakaszban. Ezután válassza a mappa ikont a végén a **tanúsítvány** mezőben keresse meg a **általános** mappában, és válassza a **VpnServerRoot** fájlt.
7. Az a **ügyfél** szakaszában a kapcsolathoz, válassza ki **EAP** a **hitelesítési**, és írja be a felhasználónevét és jelszavát. Előfordulhat, hogy válassza ki a lakat ikonra a jobb oldali menteni ezeket az információkat. Ezt követően válassza a **Mentés** lehetőséget.

   ![Kapcsolati beállítások szerkesztése](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Válassza ki a **hálózatkezelő** (up-nyíl/lefelé mutató nyilat) ikonra, és vigye a kurzort **VPN-kapcsolatok**. Megjelenik a létrehozott VPN-kapcsolatot. A kapcsolat létrehozására, kattintson rá.

   ![A hálózatkezelő "VPN Radius" kapcsolat](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certeap"></a>Tanúsítványalapú hitelesítés
 
Létrehozhat VPN-ügyfél konfigurációs fájljait az EAP-TLS protokollt használó RADIUS tanúsítványalapú hitelesítése. Egy vállalati által kiállított tanúsítvány általában VPN-hez a felhasználó hitelesítésére szolgál. Győződjön meg arról, hogy az összes csatlakozó felhasználók rendelkeznek-e az eszközökön telepített tanúsítvány, és, hogy a RADIUS-kiszolgáló képes-e a tanúsítvány érvényesítéséhez.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

A parancsokban szereplő `-AuthenticationMethod` van `EapTls`. Tanúsítvány a hitelesítés során az ügyfél ellenőrzi a RADIUS-kiszolgáló a tanúsítvány érvényesítésével. `-RadiusRootCert` a rendszer a .cer fájlt, amely tartalmazza a legfelső szintű tanúsítvány, amely a RADIUS-kiszolgáló ellenőrzésére szolgál.

Minden egyes VPN-ügyfél eszköz egy telepített ügyfél-tanúsítványt igényel. Néha egy Windows eszköz rendelkezik több ügyféltanúsítványt. A hitelesítés során ez egy felugró párbeszédpanel, amely felsorolja az összes tanúsítvány eredményezhet. A felhasználó ezután kell választania a használni kívánt tanúsítványt. A megfelelő tanúsítvány, amely az ügyfél-tanúsítványt kell hozzákapcsolva a legfelső szintű tanúsítvány megadásával is kizárhat. 

`-ClientRootCert` a .cer fájlt, amely tartalmazza a legfelső szintű tanúsítvány van. Ez egy nem kötelező paraméter. Ha az eszköz csatlakoztatása a kívánt csak egy ügyféltanúsítványt, nem kell megadni ezt a paramétert.

### <a name="certfiles"></a>1. VPN-ügyfél konfigurációs fájljainak létrehozása

Hozzon létre a VPN-ügyfél konfigurációs fájljainak a Tanúsítványalapú hitelesítés segítségével. A VPN-ügyfélkonfigurációs fájlok a következő parancs használatával is létrehozhat:
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

A következő parancs futtatásával adja vissza egy hivatkozást. Másolja és illessze be a hivatkozást egy webes böngésző VpnClientConfiguration.zip letöltéséhez. Bontsa ki a fájlt, a következő mappák megtekintése:

* **WindowsAmd64** és **WindowsX86**: Ezeket a mappákat a Windows 64 bites és 32 bites installer csomagokat, illetve tartalmaznak. 
* **GenericDevice**: Ez a mappa létrehozása a saját VPN-ügyfél konfigurációja használható általános információkat tartalmaz.

Ha már létrehozott ügyfél konfigurációs fájljainak, kérheti le azokat használatával a `Get-AzureRmVpnClientConfiguration` parancsmagot. Azonban ha módosításokat hajt végre a P2S VPN konfigurációját, például a VPN-protokoll típusa vagy a hitelesítési típus, a konfiguráció nem frissül automatikusan. Futtatnia kell a `New-AzureRmVpnClientConfiguration` parancsmaggal hozzon létre egy új konfigurációjának letöltéséhez.

Korábban létrehozott ügyfél-konfigurációs fájlok lekéréséhez használja a következő parancsot:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="setupusername"></a> 2. A VPN-ügyfelek konfigurálása

A következő VPN-ügyfelek konfigurálhatja:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (támogatott, nem a cikk lépésről lépésre még)

#### <a name="certwincli"></a>Windows VPN-ügyfél telepítése

1. Válassza ki a konfigurációs csomagot, és telepítse az ügyféleszközön. Egy 64 bites processzor-architektúra, válassza a **VpnClientSetupAmd64** installer-csomag. Egy 32-bit-es processzor-architektúra, válassza a **VpnClientSetupX86** installer-csomag. Ha egy SmartScreen előugró ablak jelenik meg, válassza ki a **szűrőfüggvénnyel** > **Futtatás mindenképpen**. A csomagot mentheti is, így más ügyfélszámítógépekre is telepítheti.
2. Minden ügyfél ügyféltanúsítványt igényel a hitelesítéshez. Az ügyféltanúsítvány telepítése. Ügyfél-tanúsítványokkal kapcsolatos további információkért lásd: [ügyféltanúsítványok, a pont – hely](vpn-gateway-certificates-point-to-site.md). A létrehozott tanúsítvány telepítéséhez lásd: [tanúsítvány telepítése a Windows-ügyfeleken](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Az ügyfélszámítógépen, keresse meg a **hálózati beállítások** válassza **VPN**. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott.

#### <a name="certmaccli"></a>Mac (OS X) VPN-ügyfél telepítése

Minden Mac-eszköz, amely csatlakozik az Azure virtuális hálózat külön profilt kell létrehoznia. Ennek oka az, ezeknek az eszközöknek a felhasználói tanúsítvány adható meg a profil-hitelesítés szükséges. A **általános** a mappában megtalálja a profil létrehozásához szükséges információkat:

* **VpnSettings.xml** tartalmazza a fontos beállítások, például a kiszolgáló címét és az alagút típusát.
* **VpnServerRoot.cer** tartalmazza a legfelső szintű tanúsítvány P2S-kapcsolat beállítása során a VPN-átjáró érvényesítéséhez szükséges.
* **RadiusServerRoot.cer** tartalmazza a legfelső szintű tanúsítvány szükséges a RADIUS-kiszolgáló ellenőrzése a hitelesítés során.

Az alábbi lépéseket követve konfigurálhatja a natív VPN-ügyfél Tanúsítványalapú hitelesítés egy Mac számítógépen:

1. Importálás a **VpnServerRoot** és **RadiusServerRoot** legfelső szintű tanúsítványok Mac Másolja a Mac minden fájlt, kattintson rá duplán, és válassza **Hozzáadás**.

   ![A VpnServerRoot tanúsítvány hozzáadása](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![A RadiusServerRoot tanúsítvány hozzáadása](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Minden ügyfél ügyféltanúsítványt igényel a hitelesítéshez. Az ügyféltanúsítvány telepítése az ügyféleszközön.
3. Nyissa meg a **hálózati** párbeszédpanel **hálózati beállítások**. Válassza ki **+** a P2S-kapcsolat az Azure virtuális hálózat új VPN-ügyfél kapcsolati profil létrehozásához.

   A **felület** érték **VPN**, és a **VPN-típust** érték **IKEv2**. Adjon meg egy nevet a profilnak a a **szolgáltatásnév** mezőbe, majd válassza ki **létrehozás** a VPN-ügyfél-csatlakozási profil létrehozásához.

   ![Felület és a szolgáltatás neve információk](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. Az a **általános** mappába, az a **VpnSettings.xml** fájlt, másolja a **VpnServer** címke értéke. Illessze be ezt az értéket a **kiszolgálócím** és **távoli azonosítója** be a profil. Hagyja a **helyi azonosítója** mező üres.

   ![Kiszolgálóadatok](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Válassza ki **hitelesítési beállítások**, és válassza ki **tanúsítvány**. 

   ![Hitelesítési beállítások](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Kattintson a **kiválasztása** , válassza ki a hitelesítéshez használni kívánt tanúsítványt.

   ![A hitelesítési tanúsítvány kiválasztása](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Válassza ki az identitás** közül választhat tanúsítványainak listáját jeleníti meg. Válassza ki a megfelelő tanúsítványt, majd a **Folytatás**.

   !["Az identitás kiválasztása" lista](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. Az a **helyi azonosítója** adja meg azt a tanúsítványt (a 6. lépés). Ebben a példában van **ikev2Client.com**. Ezután válassza ki a **alkalmaz** gombra kattintva mentse a módosításokat.

   !["Helyi ID" mezőbe](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. Az a **hálózati** párbeszédpanelen jelölje ki **alkalmaz** összes módosítások mentéséhez. Ezután válassza ki **Connect** elindításához a P2S-kapcsolat az Azure virtual Networkhöz.

## <a name="otherauth"></a>Más hitelesítési típusok vagy protokollok használata

Egy másik hitelesítési típus (például OTP) használatára, vagy egy másik hitelesítési protokoll (például a PEAP-MSCHAPv2 helyett az EAP-MSCHAPv2) használatára, létre kell hoznia a saját VPN-ügyfél konfigurációs profil. A profil létrehozásához szüksége információkat, például a virtuális hálózati átjáró IP-cím, az alagúttípus és a vegyes alagútkezelési útvonalakat. Ezt az információt kaphat az alábbi lépések segítségével:

1. Használja a `Get-AzureRmVpnClientConfiguration` parancsmag segítségével hozzon létre a VPN-ügyfél konfigurációja a EapMSChapv2. Útmutatásért lásd: [ebben a szakaszban](#ccradius) a cikk.

2. Bontsa ki a VpnClientConfiguration.zip fájlt, és keresse meg a **GenenericDevice** mappát. Hagyja figyelmen kívül a Windows 64 bites és 32 bites architektúrák telepítőinek tartalmazó mappához.
 
3. A **GenenericDevice** mappa tartalmaz egy XML-fájl nevű **VpnSettings**. Ez a fájl tartalmazza a szükséges adatokat:

   * **VpnServer**: Az Azure VPN gateway teljes Tartományneve. Ez az a cím, amely az ügyfél csatlakozik.
   * **VpnType**: Alagút típusa, amely kapcsolódik.
   * **Útvonalak**: A profilban adja meg, hogy csak az Azure virtuális hálózat kötött adatforgalom a P2S-alagúton keresztül rendelkező útvonalak.
   
   A **GenenericDevice** mappa is tartalmaz egy .cer fájlba nevű **VpnServerRoot**. Ez a fájl tartalmazza a legfelső szintű tanúsítvány P2S-kapcsolat beállítása során az Azure VPN gateway érvényesítéséhez szükséges. Telepítse a tanúsítványt minden olyan eszköz, amely az Azure virtuális hálózat csatlakozni fog.

## <a name="next-steps"></a>További lépések

Térjen vissza a cikkhez a [a P2S-konfigurálás befejezéséhez](point-to-site-how-to-radius-ps.md).

P2S hibaelhárítási információkért lásd: [pont – hely kapcsolatok hibaelhárítása Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).