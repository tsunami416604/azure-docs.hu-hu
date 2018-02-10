---
title: "Hozzon létre és P2S RADIUS kapcsolatok VPN ügyfél konfigurációs fájljainak telepítése: PowerShell: Azure |} Microsoft Docs"
description: "Ez a cikk segít a pont – hely kapcsolatok RADIUS-hitelesítést használó VPN ügyfél-konfigurációs fájl létrehozása."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/05/2018
ms.author: cherylmc
ms.openlocfilehash: fb83bda50535dc002120ee4621cd4c8df71c141c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Hozzon létre és telepítse a VPN-ügyfél konfigurációs fájlok P2S RADIUS-hitelesítés

Pont-pont keresztül kapcsolódik a virtuális hálózat, az ügyféleszközt, amelyen meg csatlakoztatni fog konfigurálni kell. A RADIUS-kiszolgáló több hitelesítési beállítások itt: felhasználónév/jelszó-hitelesítés, tanúsítvány alapú hitelesítést, valamint más hitelesítési típusok. A VPN-ügyfél konfigurációja nem azonos az egyes hitelesítési. A VPN-ügyfél konfigurálásához használja a szükséges beállításokat tartalmazó ügyfél-konfigurációs fájlok. Ez a cikk segítséget nyújt a létrehozása és telepítése a RADIUS hitelesítési típushoz, amely a használni kívánt VPN-konfiguráció.

### <a name="workflow"></a>Munkafolyamat

1. [Állítsa be az Azure VPN gateway p2s-kapcsolatokhoz tartozó](point-to-site-how-to-radius-ps.md).
2. [Állítsa be a RADIUS-kiszolgáló hitelesítési](point-to-site-how-to-radius-ps.md#radius). 
3. **Szerezze be a VPN-ügyfél beállításainak konfigurálása a hitelesítési lehetőséget az Ön által választott, és állítsa be a VPN-ügyfél segítségével**. (Ez a cikk)
4. [A P2S konfigurálásának befejezése és csatlakozzon](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Ha a pont – hely típusú VPN-konfiguráció módosításainak a VPN-ügyfél konfigurációs profil, például a VPN-protokoll típusát vagy a hitelesítési típus létrehozása után Ön hozza létre és telepíthet egy új VPN-ügyfél konfigurációja a felhasználói eszközökön.
>
>

## <a name="adeap"></a>Felhasználónév/jelszó-hitelesítés

Felhasználónév/jelszó-hitelesítés konfigurálása két módja van. Vagy konfigurálhatja a hitelesítési, vagy használhatja az Active Directory, AD használata nélkül végezzen hitelesítést. Mindkét forgatókönyv esetén győződjön meg arról, minden csatlakozó felhasználók felhasználónév/jelszó hitelesítő adatokat, amelyek a RADIUS használatával hitelesítheti.

* A felhasználónév/jelszó-hitelesítést, amikor csak lehet létrehozni a felhasználónév/jelszó EAP-MSCHAPv2 hitelesítési protokoll konfigurációját.
* "-AuthenticationMethod" rendszer "EapMSChapv2".

### <a name="usernamefiles"></a> 1. VPN-ügyfél konfigurációs fájlok létrehozása

Felhasználónév/jelszó-hitelesítés használható VPN ügyfél konfigurációs fájlokat generál. A VPN-ügyfél konfigurációs fájlokat a következő parancsot hozhat létre:

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Hivatkozás a parancs futtatásával adja vissza. Másolja és illessze be a hivatkozást egy webes böngésző "VpnClientConfiguration.zip" letöltéséhez. Bontsa ki a fájlt a következő mappák megtekintése: 
 
* **WindowsAmd64** és **WindowsX86** -ezeket a mappákat a Windows 64 bites és 32 bites csomagok, illetve tartalmaz. 
* **Általános** – Ez a mappa létrehozása a saját VPN-ügyfél konfigurációja használt általános információkat tartalmaz. Ez a mappa nem szükséges felhasználónév/jelszó hitelesítési konfigurációt.
* **Mac** -Ha IKEv2 lett konfigurálva, a virtuális hálózati átjáró létrehozása után úgy, hogy a "Mac" tartalmazó nevű mappát a **mobileconfig** fájlt. Ezt a fájlt a Mac ügyfelek konfigurálására szolgál.

Ha már létrehozott ügyfél konfigurációs fájlok, a "Get-AzureRmVpnClientConfiguration" parancsmaggal visszakereshetők. Azonban ha módosítja a P2S VPN-konfigurációt, például a VPN-protokoll típusát vagy a hitelesítés típusa, a konfiguráció nem frissül automatikusan. A "New-AzureRmVpnClientConfiguration" parancsmaggal hozzon létre egy új konfigurációs letöltés kell futtatni.

Korábban létrehozott ügyfél-konfigurációs fájlok lekéréséhez használja a következő parancsot:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="setupusername"></a> 2. A VPN-ügyfelek konfigurálása

A következő VPN-ügyfelek is konfigurálhatja:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux strongSwan használatával](#adlinuxcli)
 
#### <a name="adwincli"></a>A Windows VPN-ügyfél beállítása

Használhatja az azonos VPN-ügyfél konfigurációs csomag a Windows-ügyfél számítógépekre, mindaddig, amíg a verziót az ügyfél architektúrájának megfelelő. Által támogatott ügyfél operációs rendszerek listájáért lásd: a pont-pont szakasza a [gyakran ismételt kérdések](vpn-gateway-vpn-faq.md#P2S).

A következő lépésekkel konfigurálhatja a natív Windows VPN-ügyfél esetében a tanúsítványalapú hitelesítéshez:

1. Válassza ki a VPN-ügyfél konfigurációs fájlokat, a Windows-számítógép architektúrájának megfelelő. Egy 64 bites processzor-architektúra válassza a "VpnClientSetupAmd64" installer-csomag. Válassza a "VpnClientSetupX86" installer-csomag egy 32 bites processzorarchitektúra. 
2. A csomag telepítéséhez kattintson duplán. Ha megjelenik a SmartScreen egy előugró ablaka, kattintson a **További információ**, majd a **Futtatás mindenképpen** elemre.
3. Nyissa meg az ügyfélszámítógépen a **Hálózati beállítások** eszközt, és kattintson a **VPN** elemre. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott. 

#### <a name="admaccli"></a>Mac (OS X) VPN-ügyfél beállítása

1. Válassza ki a **VpnClientSetup mobileconfig** fájlt, és küldje el a felhasználók. E-mailben vagy egy másik módszer használható.

2. Keresse meg a **mobileconfig** a Mac-fájl

  ![Keresse meg a mobilconfig fájl](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)
3. Kattintson duplán a telepítéshez, kattintson a profil **Folytatás**. A profil neve megegyezik a virtuális hálózat nevét.

  ![Telepítése](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
4. Kattintson a **Folytatás** a küldő a profil megbízhatósági és a telepítés folytatásához.

  ![folytatás](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
5. Profil telepítése során lehetősége van arra, hogy megadja a felhasználónevet és a VPN-hitelesítéshez használt jelszót. A rendszer nem adja meg ezt az információt kötelező. Ha meg van adva, az adatok mentése, és automatikusan használjuk a kapcsolat kezdeményezésekor. Kattintson a **telepítése** a folytatáshoz.

  ![beállítások](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
6. Adja meg a szükséges jogosultságokkal, telepítse a profilt a számítógépen szükséges felhasználónevet és jelszót. Kattintson az **OK** gombra.

  ![felhasználónév és jelszó](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
7. A telepítést követően a profil látható-e a **profilok** párbeszédpanel megnyitásához. Ezen a párbeszédpanelen is nyitható később **rendszerbeállítások**.

  ![Rendszerbeállítások](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
8. A VPN-kapcsolat megnyitásához nyissa meg a **hálózati** párbeszédpanel a **rendszerbeállítások**.

  ![network](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
9. A VPN-kapcsolat jeleníti meg, mint a **IkeV2 VPN**. A név értéke módosítható azáltal, hogy frissíti a **mobileconfig** fájlt.

  ![kapcsolat](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
10. Kattintson a **hitelesítési beállítások**. Válasszon **felhasználónév** a legördülő lista a hitelesítő adataival. Ha a megadott hitelesítő adatok korábbi, majd **felhasználónév** automatikusan a legördülő lista a kiválasztott és a felhasználónév és jelszó ki van töltve. Kattintson az **OK** gombra a beállítások mentéséhez. Ezzel megnyitná vissza a hálózati párbeszédpanel.

  ![authenticate](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
11. Kattintson a **alkalmaz** menti a módosításokat. Kezdeményezzen kapcsolatot, kattintson a **Connect**.

#### <a name="adlinuxcli"></a>Linux VPN-ügyfél beállítást strongSwan használatával

Az alábbi utasítások alapján létrehozott Ubuntu 17.0.4 strongSwan 5.5.1 használatával. Tényleges képernyők, amelyekhez a Linux és strongSwan verziójától függően eltérő lehet.

1. Nyissa meg a **Terminálszolgáltatások** telepítendő **strongSwan** és a hálózatkezelő, a következő parancs futtatásával. Ha a "libcharon-extra-beépülő modulok" vonatkozó hibaüzenetet kap, akkor cserélje le a "strongswan-beépülő modul – eap-mschapv2".

  ```Terminal
  sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
  ```
2. Kattintson a **hálózatkezelő** (felfelé-nyíl/lefelé mutató nyíl) ikonra, majd válassza **szerkesztése kapcsolatok**.

  ![kapcsolat szerkesztése](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Kattintson a **Hozzáadás** gombra kattintva hozzon létre egy új kapcsolatot.

  ![Kapcsolat hozzáadása](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Válassza ki **IPsec vagy IKEv2 (strongswan)** a legördülő menüből, majd kattintson a **létrehozása**. Ebben a lépésben a kapcsolat át lehet nevezni.

  ![add ikev2](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Nyissa meg a **VpnSettings.xml** fájlt a **általános** mappa a letöltött ügyfél-konfigurációs fájlok. A címke neve található **VpnServer** , és másolja a nevét, "azuregateway" kezdve, és a záró ". cloudapp.net".

  ![VPN-beállítások](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Illessze be a nevét a **cím** mezőjét, az új VPN-kapcsolat alatt a **átjáró** szakasz. Ezután kattintson a ikonja végén a **tanúsítvány** mezőben tallózással keresse meg az általános mappában, majd válassza ki a **VpnServerRoot** fájl nem található.
7. Alatt a **ügyfél** szakaszban válassza ki a kapcsolat **EAP** a **hitelesítési**, és adja meg a felhasználónév/jelszó. Előfordulhat, hogy jelölje be a zárolási ikonjával jelenik meg ezt az információt mentéséhez szükséges jogosultságot. Kattintson a **mentése**.

  ![kapcsolat beállításainak szerkesztése](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Kattintson a **hálózatkezelő** (felfelé-nyíl/lefelé mutató nyíl) ikonra, és állítsa az egérmutatót **VPN-kapcsolatok**. A VPN-kapcsolat létrehozott jelenik meg. Kezdeményezzen kapcsolatot, válasszon való csatlakozásra.

  ![Csatlakozás radius](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certeap"></a>Tanúsítványhitelesítés
 
VPN-ügyfél konfigurációs fájlokat a RADIUS-alapú hitelesítéséhez EAP-TLS protokollt használó hozhat létre. Általában egy vállalati által kiállított tanúsítvány VPN-felhasználó hitelesítéséhez használatos. Győződjön meg arról, hogy az összes csatlakozó felhasználók rendelkeznek-e a felhasználói eszközökön telepített tanúsítvánnyal, és hogy a tanúsítványt hitelesíteni tud a RADIUS-kiszolgáló.
 
* "-AuthenticationMethod" rendszer "EapTls".
* Tanúsítvány a hitelesítés során az ügyfél ellenőrzi a RADIUS-kiszolgáló a tanúsítvány érvényesítésével. -RadiusRootCert a .cer fájlt, amely tartalmazza a legfelső szintű tanúsítvány, amely ellenőrzi a RADIUS-kiszolgáló.
* Minden egyes VPN-ügyfél eszköz egy telepített ügyfél-tanúsítványt igényel.
* A Windows-eszközön kapott több ügyféltanúsítványt. A hitelesítés során ez egy felugró párbeszédpanel a tanúsítványokat felsoroló eredményezhet. A felhasználó, majd ki kell választania a használni kívánt tanúsítványt. A megfelelő tanúsítványt a főtanúsítvány, amelyhez az ügyfél-tanúsítvány tanúsítványlánc-érdemes megadásával is kiszűri. "-ClientRootCert", amely tartalmazza a legfelső szintű tanúsítvány .cer-fájl. Egy nem kötelező paraméter is. Ha az eszköz, amelyről szeretné csatlakoztatni csak egy ügyféltanúsítványt, majd ezt a paramétert nem kell megadni.

### <a name="certfiles"></a>1. VPN-ügyfél konfigurációs fájlok létrehozása

Tanúsítványalapú hitelesítés használható VPN ügyfél konfigurációs fájlokat generál. A VPN-ügyfél konfigurációs fájlokat a következő parancsot hozhat létre:
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Hivatkozás a parancs futtatásával adja vissza. Másolja és illessze be a hivatkozást egy webes böngésző "VpnClientConfiguration.zip" letöltéséhez. Bontsa ki a fájlt a következő mappák megtekintése:

* **WindowsAmd64** és **WindowsX86** -ezeket a mappákat a Windows 64 bites és 32 bites csomagok, illetve tartalmaz. 
* **GenericDevice** – Ez a mappa létrehozása a saját VPN-ügyfél konfigurációja használt általános információkat tartalmaz.

Ha már létrehozott ügyfél konfigurációs fájlok, a "Get-AzureRmVpnClientConfiguration" parancsmaggal visszakereshetők. Azonban ha módosítja a P2S VPN-konfigurációt, például a VPN-protokoll típusát vagy a hitelesítés típusa, a konfiguráció nem frissül automatikusan. A "New-AzureRmVpnClientConfiguration" parancsmaggal hozzon létre egy új konfigurációs letöltés kell futtatni.

Korábban létrehozott ügyfél-konfigurációs fájlok lekéréséhez használja a következő parancsot:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="setupusername"></a> 2. A VPN-ügyfelek konfigurálása

A következő VPN-ügyfelek is konfigurálhatja:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (támogatott, nem a cikk lépéseit még)

#### <a name="certwincli"></a>A Windows VPN-ügyfél beállítása

1. Válassza ki a konfigurációs csomagot, és telepítse az ügyféleszközön. Egy 64 bites processzor-architektúra válassza a "VpnClientSetupAmd64" installer-csomag. Válassza a "VpnClientSetupX86" installer-csomag egy 32 bites processzorarchitektúra. Ha megjelenik a SmartScreen egy előugró ablaka, kattintson a **További információ**, majd a **Futtatás mindenképpen** elemre. A csomagot mentheti is, így más ügyfélszámítógépekre is telepítheti.
2. Minden ügyfél igényel az ügyféltanúsítvány hitelesítéséhez. Telepíti az ügyféltanúsítványt. Ügyfél-tanúsítványokkal kapcsolatos további információkért lásd: [pont-pont ügyféltanúsítványainak](vpn-gateway-certificates-point-to-site.md). A létrehozott tanúsítvány telepítéséhez lásd: [tanúsítvány telepítése Windows-ügyfelek](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Nyissa meg az ügyfélszámítógépen a **Hálózati beállítások** eszközt, és kattintson a **VPN** elemre. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott.

#### <a name="certmaccli"></a>Mac (OS X) VPN-ügyfél beállítása

Minden Mac eszköz, amely kapcsolódik az Azure virtuális hálózat külön profilt kell létrehozni. Ennek oka az, ezeknek az eszközöknek a felhasználói tanúsítványt az adható meg a profil-hitelesítés szükséges. A **általános** mappa tartalmaz egy profil létrehozásához szükséges összes adatot.

  * **VpnSettings.xml** például a kiszolgáló címét és alagút típusú fontos beállításait tartalmazza.
  * **VpnServerRoot.cer** tartalmazza a legfelső szintű tanúsítvány P2S csatlakozási telepítés során a VPN-átjáró érvényesítéséhez szükséges.
  * **RadiusServerRoot.cer** tartalmazza a hitelesítés során a RADIUS-kiszolgáló érvényesítéséhez szükséges főtanúsítvány.

A következő lépésekkel konfigurálhatja a natív VPN-ügyfél a Mac esetében a tanúsítványalapú hitelesítéshez:

1. Importálás a **VpnServerRoot** és a **RadiusServerRoot** legfelső szintű tanúsítványok a Mac. Erre a fájl felülírását visszakerülnek a Mac, dupla kattintással.  
Kattintson a **Hozzáadás** importálásához.

  *VpnServerRoot hozzáadása*

  ![tanúsítvány hozzáadása](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

  *RadiusServerRoot hozzáadása*

  ![tanúsítvány hozzáadása](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Minden ügyfél igényel az ügyféltanúsítvány hitelesítéséhez. Telepíti az ügyféltanúsítványt az ügyféleszközön.
3. Nyissa meg a **hálózati** párbeszédpanel **hálózati beállítások** kattintson **"+"** egy új VPN-ügyfél kapcsolati profil a P2S kapcsolat az Azure virtuális hálózat létrehozásához.

  A **felület** értéke 'VPN' és **VPN-típus** értéke "IKEv2". Adja meg a profil nevét a **szolgáltatásnév** mezőben, majd kattintson az **létrehozása** a VPN-ügyfél-csatlakozási profil létrehozásához.

  ![network](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. Az a **általános** mappa, a a **VpnSettings.xml** fájlt, másolja a **VpnServer** címke értéke. Illessze be ezt az értéket a **kiszolgálócímet** és **távoli azonosítója** mezők a profil. Hagyja a **helyi azonosítója** mező üres.

  ![kiszolgáló adatai](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Kattintson a **hitelesítési beállítások** válassza **tanúsítvány**. 

  ![hitelesítési beállítások](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Kattintson a **válasszon...** a hitelesítéshez használni kívánt tanúsítvány kiválasztásához.

  ![tanúsítvány](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Válassza ki az identitás** választhat tanúsítványainak listáját jeleníti meg. Válassza ki a megfelelő tanúsítványt, majd kattintson az **Folytatás**.

  ![identity](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. Az a **helyi azonosítója** mezőben adja meg azt a tanúsítványt (a 6. lépés). Ebben a példában a "ikev2Client.com" esetében. Kattintson a **alkalmaz** gombra a módosítások mentéséhez.

  ![alkalmaz](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. Az a **hálózati** párbeszédpanel, kattintson a **alkalmaz** összes módosítások mentéséhez. Kattintson a **Connect** elindítani az Azure VNet P2S csatlakozni.

## <a name="otherauth"></a>Más hitelesítési típusok vagy protokollok használata

Egy másik hitelesítési típus (például OTP), és nem felhasználónév/jelszó vagy tanúsítványokat használ, vagy egy másik hitelesítési protokoll (például a PEAP-MSCHAPv2, EAP-MSCHAPv2 helyett), létre kell hoznia a saját VPN-ügyfél konfigurációs profil. A profil létrehozásához szüksége a virtuális hálózati átjáró IP-címet, a bújtatási típusának, és a vegyes alagútkezelési útvonalak. Ezt az információt kaphat az alábbi lépéseket követve:

1. A "Get-AzureRmVpnClientConfiguration" parancsmagot használja a VPN-ügyfél beállításainak konfigurálása EapMSChapv2 létrehozásához. Útmutatásért lásd: [ebben a szakaszban](#ccradius) a cikk.

2. Bontsa ki a VpnClientConfiguration.zip fájlt, és keresse meg a GenenericDevice mappát. Hagyja figyelmen kívül a Windows Installer telepítők 64 bites és 32 bites architektúrára való tartalmazó mappákat.
 
3. A GenenericDevice mappa VpnSettings nevű XML-fájl tartalmazza. Ez a fájl tartalmazza a szükséges adatokat.

  * VpnServer – az Azure VPN Gateway teljes Tartományneve. Ez az a cím, amely az ügyfél csatlakozik.
  * VpnType - a kapcsolódáshoz használt bújtatási típusának.
  * Útvonalak - útvonal, amelyet be kell állítania a profil úgy, hogy csak az Azure VNet forgalmát az P2S-alagúton keresztül zajlik.
  * A GenenericDevice mappa is tartalmaz egy .cer fájlba "VpnServerRoot" nevezik. Ez a fájl tartalmazza a legfelső szintű tanúsítvány P2S csatlakozási telepítés során az Azure VPN Gateway érvényesítéséhez szükséges. Telepítse a tanúsítványt, amelyek csatlakozni fognak az Azure VNet összes eszközön.

## <a name="next-steps"></a>További lépések

Térjen vissza a cikkhez [végezze el a P2S konfigurálását](point-to-site-how-to-radius-ps.md).