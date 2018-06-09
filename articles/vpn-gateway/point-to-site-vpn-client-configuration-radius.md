---
title: 'Hozzon létre és P2S RADIUS kapcsolatok VPN ügyfél konfigurációs fájljainak telepítése: PowerShell: Azure |} Microsoft Docs'
description: Windows, Mac OS X és Linux VPN-ügyfél konfigurációs fájlok RADIUS-hitelesítést használó kapcsolatok létrehozása.
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
ms.openlocfilehash: 19b1090a37ae1f97537fcabe128e7958fc26a96a
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235889"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Hozzon létre és telepítse a VPN-ügyfél konfigurációs fájlok P2S RADIUS-hitelesítés

Pont-pont (P2S) keresztül kapcsolódik a virtuális hálózat, szüksége konfigurálásához az ügyféleszközön a csatlakozó lesz. A Windows, Mac OS X és Linux rendszerű ügyféleszközök P2S VPN-kapcsolatokat hozhat létre. 

Ha a RADIUS-hitelesítést használ, több hitelesítési lehetőség áll rendelkezésre: felhasználónév/jelszó-hitelesítés, tanúsítvány hitelesítése és más hitelesítési típusok. A VPN-ügyfél konfigurációja nem azonos az egyes hitelesítési. A VPN-ügyfél konfigurálásához használja a szükséges beállításokat tartalmazó ügyfél-konfigurációs fájlok. Ez a cikk segítséget nyújt a létrehozása és telepítése a RADIUS hitelesítési típushoz, amely a használni kívánt VPN-konfiguráció.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

A konfigurációs munkafolyamat P2S RADIUS-hitelesítés a következőképpen történik:

1. [Állítsa be az Azure VPN gateway p2s-kapcsolatokhoz tartozó](point-to-site-how-to-radius-ps.md).
2. [Állítsa be a RADIUS-kiszolgáló hitelesítési](point-to-site-how-to-radius-ps.md#radius). 
3. **Szerezze be a VPN-ügyfél beállításainak konfigurálása a hitelesítési lehetőséget az Ön által választott, és állítsa be a VPN-ügyfél segítségével** (Ez a cikk).
4. [A P2S konfigurálásának befejezése és csatlakozzon](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Ha a pont-pont VPN-konfiguráció módosításainak a VPN-ügyfél konfigurációs profil, például a VPN-protokoll típusát vagy a hitelesítési típus létrehozása után Ön hozza létre, és telepítse egy új VPN-ügyfél konfigurációja a felhasználói eszközökön.
>
>

Ez a cikk a részeket használatához először döntse el, használni kívánt hitelesítési típustól: felhasználónév/jelszó, tanúsítvány vagy egyéb típusú hitelesítés. Az egyes szakaszokon lépéseket a Windows, Mac OS X és Linux (korlátozott lépéseket rendelkezésre) rendelkezik.

## <a name="adeap"></a>Felhasználónév/jelszó-hitelesítés

Az Active Directory használatával, vagy nem használja az Active Directory felhasználónév/jelszó-hitelesítés konfigurálása Mindkét forgatókönyv esetén győződjön meg arról, minden csatlakozó felhasználók felhasználónév/jelszó hitelesítő adatokat, amelyek a RADIUS használatával hitelesítheti.

A felhasználónév/jelszó-hitelesítést, amikor csak lehet létrehozni a felhasználónév/jelszó EAP-MSCHAPv2 hitelesítési protokoll konfigurációját. A parancsokban `-AuthenticationMethod` van `EapMSChapv2`.

### <a name="usernamefiles"></a> 1. VPN-ügyfél konfigurációs fájlok létrehozása

Felhasználónév/jelszó-hitelesítés használható VPN ügyfél konfigurációs fájlokat generál. A VPN-ügyfél konfigurációs fájlok létrehozásához a következő parancsot:

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Hivatkozás a parancs futtatásával adja vissza. Másolja és illessze be a hivatkozásra kattintva egy webböngészőben letöltése **VpnClientConfiguration.zip**. Bontsa ki a fájlt a következő mappák megtekintése: 
 
* **WindowsAmd64** és **WindowsX86**: ezeket a mappákat a Windows 64 bites és 32 bites csomagok, illetve tartalmaz. 
* **Általános**: Ez a mappa hozhat létre egyéni VPN-ügyfél konfigurációja általános információkat tartalmaz. Ez a mappa az felhasználónév/jelszó-hitelesítés konfiguráció nincs szükség.
* **Mac**: Ha úgy állította be az IKEv2, a virtuális hálózati átjáró létrehozása után, akkor egy nevű mappát láthatnak **Mac** , amely tartalmazza a **mobileconfig** fájlt. Ez a fájl segítségével Mac-ügyfél konfigurálása.

Ha már létrehozott ügyfél konfigurációs fájlokat, helyreállíthatók használatával a `Get-AzureRmVpnClientConfiguration` parancsmag. De végzett módosításokat a P2S VPN-konfigurációt, például a VPN-protokoll típusát vagy a hitelesítés típusa, ha a konfiguráció nem frissülnek automatikusan. Futtatnia kell a `New-AzureRmVpnClientConfiguration` parancsmaggal hozzon létre egy új konfigurációs letölthető.

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

Használhatja az azonos VPN-ügyfél konfigurációs csomag a Windows-ügyfél számítógépekre, mindaddig, amíg a verziót az ügyfél architektúrájának megfelelő. A támogatott ügyfél operációs rendszerek listájáért lásd: a [gyakran ismételt kérdések](vpn-gateway-vpn-faq.md#P2S).

A következő lépésekkel konfigurálhatja a natív Windows VPN-ügyfél esetében a tanúsítványalapú hitelesítéshez:

1. Válassza ki a VPN-ügyfél konfigurációs fájlokat, a Windows-számítógép architektúrájának megfelelő. Egy 64 bites processzor-architektúra, válassza ki a **VpnClientSetupAmd64** installer-csomag. A 32 bites processzorarchitektúra, válassza a **VpnClientSetupX86** installer-csomag. 
2. A csomag telepítéséhez kattintson rá duplán. Ha a SmartScreen előugró ablak megjelenik, válassza ki a **információ** > **mégis futtatni**.
3. Az ügyfélszámítógépen navigáljon **hálózati beállítások** válassza **VPN**. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott. 

#### <a name="admaccli"></a>Mac (OS X) VPN-ügyfél beállítása

1. Válassza ki a **VpnClientSetup mobileconfig** fájlt, és küldje el a felhasználók. E-mailben vagy egy másik módszer használható.

2. Keresse meg a **mobileconfig** a Mac-fájl

   ![A mobilconfig fájl helyét](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)
3. Kattintson duplán a telepítéshez, és válassza ki a profil **Folytatás**. A profil neve megegyezik a virtuális hálózat nevét.

   ![Telepítés üzenet](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
4. Válassza ki **Folytatás** a küldő a profil megbízhatósági és a telepítés folytatásához.

   ![Megerősítő üzenet](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
5. Profil telepítése során lehetősége van a adja meg a felhasználónevet és jelszót a VPN-hitelesítéshez. A rendszer nem adja meg ezt az információt kötelező. Ha így tesz, az adatok mentése és automatikusan használja a kapcsolat kezdeményezésekor. Válassza ki **telepítése** a folytatáshoz.

   ![Felhasználónév és jelszó jelölőnégyzetéből VPN](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
6. Adja meg egy felhasználónevet és jelszót a telepítse a profilt a számítógépen szükséges jogosultságokkal. Kattintson az **OK** gombra.

   ![Felhasználónév és jelszó jelölőnégyzetéből profil telepítése](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
7. A profil telepítése után is látható a **profilok** párbeszédpanel megnyitásához. Ezen a párbeszédpanelen, később is megnyithatja **rendszerbeállítások**.

   !["Profilok" párbeszédpanel](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
8. A VPN-kapcsolat megnyitásához nyissa meg a **hálózati** párbeszédpanelt **rendszerbeállítások**.

   ![A rendszer beállítások ikonok](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
9. A VPN-kapcsolat jelenik meg **IkeV2 VPN**. Módosíthatja a nevet frissítése a **mobileconfig** fájlt.

   ![A VPN-kapcsolat részletei](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
10. Válassza ki **hitelesítési beállítások**. Válassza ki **felhasználónév** a listában, és írja be a hitelesítő adatait. Ha a megadott hitelesítő adatok korábbi, majd **felhasználónév** automatikusan választott a listában, és a felhasználónevet és jelszót a rendszer előre feltöltve. Válassza ki **OK** menti a beállításokat.

    ![Hitelesítési beállítások](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
11. Vissza a **hálózati** párbeszédpanelen jelölje ki **alkalmaz** menti a módosításokat. Válassza ki a kapcsolat létrehozására, **Connect**.

#### <a name="adlinuxcli"></a>Linux VPN-ügyfél beállítást strongSwan keresztül

Az alábbi utasításokat a Ubuntu 17.0.4 5.5.1 strongSwan keresztül lettek létrehozva. Tényleges képernyők, amelyekhez a Linux és strongSwan verziójától függően eltérő lehet.

1. Nyissa meg a **Terminálszolgáltatások** telepítendő **strongSwan** és a hálózatkezelő, a példában a parancs futtatásával. Ha hibaüzenet jelenik meg, amely kapcsolódik `libcharon-extra-plugins`, cserélje le `strongswan-plugin-eap-mschapv2`.

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Válassza ki a **hálózatkezelő** (felfelé-nyíl/lefelé mutató nyíl) ikonra, majd válassza **szerkesztése kapcsolatok**.

   ![A hálózatkezelő kijelölés "Szerkesztése kapcsolatok"](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Válassza ki a **Hozzáadás** gombra kattintva hozzon létre egy új kapcsolatot.

   ![A kapcsolat "Hozzáadás" gombra](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Válassza ki **IPsec vagy IKEv2 (strongswan)** a legördülő menüből, és válassza a **létrehozása**. Ebben a lépésben a kapcsolat át lehet nevezni.

   ![Jelölje ki a kapcsolat](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Nyissa meg a **VpnSettings.xml** fájlt a **általános** mappa a letöltött ügyfél-konfigurációs fájlok. A címke neve található `VpnServer` , és másolja a neve, kezdve `azuregateway` és végződő `.cloudapp.net`.

   ![A VpnSettings.xml fájl tartalma](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Illessze be a nevét a **cím** mező az új VPN-kapcsolat a **átjáró** szakasz. Ezután válassza ki a végén ikonja a **tanúsítvány** mezőben tallózással keresse meg, hogy a **általános** mappa, és válassza ki a **VpnServerRoot** fájlt.
7. Az a **ügyfél** a kapcsolat, jelölje be a szakasz **EAP** a **hitelesítési**, és írja be a felhasználónevét és jelszavát. Lehetséges, hogy jelölje be a zárolási ikonjával jelenik meg ezt az információt mentéséhez szükséges jogosultságot. Ezt követően válassza a **Mentés** lehetőséget.

   ![Kapcsolat beállításainak szerkesztése](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Válassza ki a **hálózatkezelő** (felfelé-nyíl/lefelé mutató nyíl) ikonra, és állítsa az egérmutatót **VPN-kapcsolatok**. A VPN-kapcsolat létrehozott láthatja. A kapcsolat létrehozására, válassza ki azt.

   ![A hálózatkezelő "VPN Radius" kapcsolat](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certeap"></a>Tanúsítványhitelesítés
 
VPN-ügyfél konfigurációs fájlokat a RADIUS-alapú hitelesítéséhez EAP-TLS protokollt használó hozhat létre. Általában egy vállalati által kiállított tanúsítvány VPN-felhasználó hitelesítéséhez használatos. Győződjön meg arról, hogy az összes csatlakozó felhasználók rendelkeznek-e az eszközökön telepített tanúsítvány, és, hogy a RADIUS-kiszolgáló képes-e a tanúsítvány érvényesítéséhez.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

A parancsokban `-AuthenticationMethod` van `EapTls`. Tanúsítvány a hitelesítés során az ügyfél ellenőrzi a RADIUS-kiszolgáló a tanúsítvány érvényesítésével. `-RadiusRootCert` a .cer fájlt, amely tartalmazza a legfelső szintű tanúsítvány, amely ellenőrzi a RADIUS-kiszolgáló van.

Minden egyes VPN-ügyfél eszköz egy telepített ügyfél-tanúsítványt igényel. A Windows-eszközön kapott több ügyféltanúsítványt. A hitelesítés során ez egy felugró párbeszédpanel a tanúsítványokat felsoroló eredményezhet. A felhasználó, majd ki kell választania a használni kívánt tanúsítványt. A megfelelő tanúsítványt a főtanúsítvány, amelyek az ügyfél-tanúsítványt kell hozzákapcsolva megadásával is kiszűri. 

`-ClientRootCert` a .cer fájlt, amely tartalmazza a legfelső szintű tanúsítvány van. Egy nem kötelező paraméter is. Ha az eszköz csatlakozhat a kívánt csak egy ügyféltanúsítványt, a nem rendelkezik a paraméter megadásával.

### <a name="certfiles"></a>1. VPN-ügyfél konfigurációs fájlok létrehozása

Tanúsítványalapú hitelesítés használható VPN ügyfél konfigurációs fájlokat generál. A VPN-ügyfél konfigurációs fájlok létrehozásához a következő parancsot:
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Hivatkozás a parancs futtatásával adja vissza. Másolja és illessze be a hivatkozást egy webes böngésző VpnClientConfiguration.zip letöltéséhez. Bontsa ki a fájlt a következő mappák megtekintése:

* **WindowsAmd64** és **WindowsX86**: ezeket a mappákat a Windows 64 bites és 32 bites csomagok, illetve tartalmaz. 
* **GenericDevice**: Ez a mappa hozhatók létre egyéni VPN-ügyfél konfigurációja általános információkat tartalmaz.

Ha már létrehozott ügyfél konfigurációs fájlokat, helyreállíthatók használatával a `Get-AzureRmVpnClientConfiguration` parancsmag. De végzett módosításokat a P2S VPN-konfigurációt, például a VPN-protokoll típusát vagy a hitelesítés típusa, ha a konfiguráció nem frissülnek automatikusan. Futtatnia kell a `New-AzureRmVpnClientConfiguration` parancsmaggal hozzon létre egy új konfigurációs letölthető.

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

1. Válassza ki a konfigurációs csomagot, és telepítse az ügyféleszközön. Egy 64 bites processzor-architektúra, válassza ki a **VpnClientSetupAmd64** installer-csomag. A 32 bites processzorarchitektúra, válassza a **VpnClientSetupX86** installer-csomag. Ha a SmartScreen előugró ablak megjelenik, válassza ki a **információ** > **mégis futtatni**. A csomagot mentheti is, így más ügyfélszámítógépekre is telepítheti.
2. Minden ügyfél ügyféltanúsítványt igényel a hitelesítéshez. Telepíti az ügyféltanúsítványt. Ügyfél-tanúsítványokkal kapcsolatos további információkért lásd: [pont-pont ügyféltanúsítványainak](vpn-gateway-certificates-point-to-site.md). A létrehozott tanúsítvány telepítéséhez lásd: [tanúsítvány telepítése Windows-ügyfelek](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Az ügyfélszámítógépen navigáljon **hálózati beállítások** válassza **VPN**. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott.

#### <a name="certmaccli"></a>Mac (OS X) VPN-ügyfél beállítása

Minden Mac eszköz, amely kapcsolódik az Azure virtuális hálózat külön profilt kell létrehoznia. Ennek oka az, ezeknek az eszközöknek a felhasználói tanúsítványt az adható meg a profil-hitelesítés szükséges. A **általános** mappában van, amely egy profil létrehozásához szükséges összes információt:

* **VpnSettings.xml** például a kiszolgáló címét és alagút típusú fontos beállításait tartalmazza.
* **VpnServerRoot.cer** tartalmazza a legfelső szintű tanúsítvány P2S csatlakozási telepítés során a VPN-átjáró érvényesítéséhez szükséges.
* **RadiusServerRoot.cer** tartalmazza a legfelső szintű tanúsítvány, amely a RADIUS-kiszolgáló hitelesítése során érvényesítéséhez szükséges.

Az alábbi lépések segítségével a natív VPN-ügyfél konfigurálása a tanúsítványok hitelesítéséhez Mac számítógépen:

1. Importálás a **VpnServerRoot** és **RadiusServerRoot** legfelső szintű tanúsítványok a Mac. Minden fájlt másolja a Mac, kattintson rá duplán, és válassza **Hozzáadás**.

   ![VpnServerRoot tanúsítványának felvétele](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![RadiusServerRoot tanúsítványának felvétele](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Minden ügyfél ügyféltanúsítványt igényel a hitelesítéshez. Telepíti az ügyféltanúsítványt az ügyféleszközön.
3. Nyissa meg a **hálózati** párbeszédpanel **hálózati beállítások**. Válassza ki **+** egy új VPN-ügyfél kapcsolati profil a P2S kapcsolat az Azure virtuális hálózat létrehozásához.

   A **felület** értéke **VPN**, és a **VPN-típus** értéke **IKEv2**. Adja meg a profil nevét a **szolgáltatásnév** mezőbe, majd válassza ki **létrehozása** a VPN-ügyfél-csatlakozási profil létrehozásához.

   ![Felület és a szolgáltatás neve információk](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. Az a **általános** mappa, a a **VpnSettings.xml** fájlt, másolja a **VpnServer** címke értéke. Illessze be ezt az értéket a **kiszolgálócímet** és **távoli azonosítója** a profil jelölőnégyzetéből. Hagyja a **helyi azonosítója** mező üres.

   ![Kiszolgáló adatai](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Válassza ki **hitelesítési beállítások**, és válassza ki **tanúsítvány**. 

   ![Hitelesítési beállítások](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Kattintson a **válasszon** a hitelesítéshez használni kívánt tanúsítvány kiválasztásához.

   ![Egy hitelesítési tanúsítvány kiválasztása](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Válassza ki az identitás** választhat tanúsítványainak listáját jeleníti meg. Válassza ki a megfelelő tanúsítványt, majd a **Folytatás**.

   ![A lista "Identitás választása"](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. Az a **helyi azonosítója** adja meg azt a tanúsítványt (a 6. lépés). Ebben a példában van **ikev2Client.com**. Ezután válassza ki a **alkalmaz** gombra a módosítások mentéséhez.

   !["Helyi ID" mezőbe](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. Az a **hálózati** párbeszédpanelen jelölje ki **alkalmaz** összes módosítások mentéséhez. Ezt követően válassza **Connect** elindítani az Azure virtuális hálózat P2S csatlakozni.

## <a name="otherauth"></a>Más hitelesítési típusok vagy protokollok használata

Egy másik hitelesítési típus (például OTP) használatára, vagy egy másik hitelesítési protokoll (például a PEAP-MSCHAPv2 helyett EAP-MSCHAPv2) használatára, létre kell hoznia a saját VPN-ügyfél konfigurációs profil. A profil létrehozásához szüksége a virtuális hálózati átjáró IP-címet, a bújtatási típusának, és a vegyes alagútkezelési útvonalak. Ezt az információt kaphat az alábbi lépéseket követve:

1. Használja a `Get-AzureRmVpnClientConfiguration` parancsmag a VPN-ügyfél beállításainak konfigurálása EapMSChapv2 létrehozásához. Útmutatásért lásd: [ebben a szakaszban](#ccradius) a cikk.

2. Bontsa ki a VpnClientConfiguration.zip fájlt, és keresse meg a **GenenericDevice** mappa. Hagyja figyelmen kívül a Windows Installer telepítők 64 bites és 32 bites architektúrára való tartalmazó mappákat.
 
3. A **GenenericDevice** mappa tartalmaz egy XML-fájl neve **VpnSettings**. Ez a fájl tartalmazza a szükséges adatokat:

   * **VpnServer**: az Azure VPN gateway teljes Tartományneve. Ez az a cím, amely az ügyfél csatlakozik.
   * **VpnType**: Alagúteszköz-típus, amely kapcsolódni.
   * **Útvonalak**: be kell állítania a profilban, így csak az Azure virtuális hálózat kötött adatforgalom P2S-alagúton keresztüli útvonalakat.
   
   A **GenenericDevice** mappa is tartalmaz egy .cer fájlba nevű **VpnServerRoot**. Ez a fájl tartalmazza a legfelső szintű tanúsítvány P2S csatlakozási telepítés során az Azure VPN gateway érvényesítéséhez szükséges. Telepítse a tanúsítványt, amelyek csatlakozni fognak az Azure virtuális hálózat összes eszközön.

## <a name="next-steps"></a>További lépések

Térjen vissza a cikkhez [végezze el a P2S konfigurálását](point-to-site-how-to-radius-ps.md).

P2S hibaelhárítási információkat lásd: [Azure hibaelhárítási pont – hely kapcsolatok](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).