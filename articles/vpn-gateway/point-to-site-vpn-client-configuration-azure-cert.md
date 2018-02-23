---
title: "Hozzon létre és Azure tanúsítványhitelesítés P2S VPN ügyfél konfigurációs fájljainak telepítése: PowerShell: Azure |} Microsoft Docs"
description: "Hozzon létre, és a Windows és Mac OS X VPN-ügyfél konfigurációs fájljainak telepítése P2S-alapú hitelesítés."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: 0ca7b7ca9435d1ba05a2cc0951f5bc88b51bf81b
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-point-to-site-configurations"></a>Hozzon létre és telepíthető a VPN ügyfél-konfigurációs fájlok natív Azure tanúsítvány hitelesítési pont-pont konfigurációk

VPN-ügyfél konfigurációs fájlokat a zip-fájl tartalmazza. Konfigurációs fájlokat adja meg a natív Windows vagy Mac IKEv2 VPN-ügyfelek natív Azure Tanúsítványalapú hitelesítés használatára, pont-pont kapcsolatokon keresztül csatlakozni a virtuális hálózat szükséges beállításokat.

### <a name="workflow"></a>P2S munkafolyamat

  1. Állítsa be az Azure VPN gateway a P2S-kapcsolatokhoz.
  2. Legfelső szintű tanúsítvány és az ügyféltanúsítványok előállításához. A legfelső szintű tanúsítvány nyilvános kulcsa feltöltése az Azure-ba, és telepítse az ügyféltanúsítványokat az ügyféleszközökön. A tanúsítványok használatának csatlakozó felhasználók hitelesítéséhez.
  3. Szerezze be a VPN-ügyfél beállításainak konfigurálása a hitelesítési lehetőséget az Ön által választott, és állítsa be a VPN-ügyfél a Windows és Mac-eszközök segítségével. Ez lehetővé teszi az Azure Vnetekhez egy pont – hely kapcsolaton keresztül kapcsolódik.

>[!NOTE]
>A VPN-konfigurációt a Vnethez tartozó ügyfél-konfigurációs fájlok vonatkoznak. Ha vannak a pont – hely típusú VPN-konfigurációs módosításokat, a VPN ügyfél konfigurációs fájlok, például a VPN-protokoll típusát vagy a hitelesítési típus létrehozása után ne feledje, új VPN ügyfél konfigurációs fájljai a felhasználói eszközök létrehozásához.
>
>

## <a name="generate"></a>VPN-ügyfél konfigurációs fájlok létrehozása

Mielőtt elkezdené, győződjön meg arról, hogy az összes csatlakozó felhasználók rendelkeznek-e egy érvényes tanúsítvány a felhasználó eszközén. Ügyféltanúsítvány telepítésével kapcsolatos további információkért lásd: [telepíthet ügyféltanúsítványt](point-to-site-how-to-vpn-client-install-azure-cert.md).

Létrehozhat ügyfél-konfigurációs fájlok powershellel, vagy az Azure portál használatával. Mindkét módszer ugyanazt a zip-fájlt ad vissza. Bontsa ki a fájlt a következő mappák megtekintése:

  * **WindowsAmd64** és **WindowsX86**, amely tartalmaz a Windows 32 bites és 64 bites csomagok, illetve. A **WindowsAmd64** telepítőcsomag van az összes támogatott 64 bites Windows-ügyfelein, nem pedig csak Amd.
  * **Általános**, amelyek a saját VPN-ügyfél konfigurációja létrehozásához használt általános információkat tartalmaz. Hagyja figyelmen kívül ezt a mappát. Az általános mappában akkor biztosított, ha az IKEv2 és SSTP + IKEv2 konfigurálva lett az átjárót. Ha csak az SSTP van beállítva, majd az általános mappában nincs jelen.

### <a name="zipportal"></a>Az Azure portál használatával fájlok létrehozása

1. Az Azure portálon keresse meg a virtuális hálózati átjáró a virtuális hálózatnak, amelyhez csatlakozni kíván.
2. Kattintson a virtuális hálózati átjáró lap **pont-hely konfigurációs**.
3. A pont-pont lap tetején kattintson **letöltése VPN-ügyfél**. Ez néhány percet vesz igénybe az ügyfél konfigurációs csomag létrehozásához.
4. A böngésző azt jelzi, hogy egy ügyfél-konfigurációs zip fájlt érhető el. A neve megegyezik az átjáró neve. Bontsa ki a fájlt a mappák megtekintése.

### <a name="zipps"></a>PowerShell-lel fájlok létrehozása

1. Előállítása VPN-ügyfél konfigurációja fájlok történő, értéke "-AuthenticationMethod" rendszer "EapTls". Készítése a VPN-ügyfél konfigurációs fájlokat a következő parancsot:

  ```powershell
  $profile=New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

  $profile.VPNProfileSASUrl
  ```
2. Másolja az URL-címet a böngészőt, hogy a zip-fájl letöltésére, majd bontsa ki a fájlt a mappák megtekintése.

## <a name="installwin"></a>Telepítse a Windows VPN-ügyfélcsomag-konfiguráció

Használhatja az azonos VPN-ügyfél konfigurációs csomag a Windows-ügyfél számítógépekre, mindaddig, amíg a verziót az ügyfél architektúrájának megfelelő. Által támogatott ügyfél operációs rendszerek listájáért lásd: a pont-pont szakasza a [VPN Gateway – gyakori kérdések](vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Rendszergazdai jogosultságokkal kell rendelkeznie az ügyfélszámítógépen Windows ahonnan szeretné csatlakoztatni.
>
>

A következő lépésekkel konfigurálhatja a natív Windows VPN-ügyfél esetében a tanúsítványalapú hitelesítéshez:

1. Válassza ki a VPN-ügyfél konfigurációs fájlokat, a Windows-számítógép architektúrájának megfelelő. Egy 64 bites processzor-architektúra válassza a "VpnClientSetupAmd64" installer-csomag. Válassza a "VpnClientSetupX86" installer-csomag egy 32 bites processzorarchitektúra. 
2. Kattintson duplán a csomagra, a telepítéshez. Ha megjelenik a SmartScreen egy előugró ablaka, kattintson a **További információ**, majd a **Futtatás mindenképpen** elemre.
3. Nyissa meg az ügyfélszámítógépen a **Hálózati beállítások** eszközt, és kattintson a **VPN** elemre. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott. 
4. Mielőtt megkísérli a csatlakozás, ellenőrizze, hogy az ügyfélszámítógépen telepített ügyféltanúsítványt. Ügyféltanúsítvány a natív Azure ügyféltanúsítvány típusú használata esetén a hitelesítéshez szükséges. További információ a tanúsítványok létrehozása: [tanúsítványok előállításához](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Ügyfél-tanúsítvány telepítésével kapcsolatos információkért lásd: [telepíthet ügyféltanúsítványt](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="installmac"></a>VPN-ügyfél konfigurációja a Mac-eket (OS X)

Azure nem biztosít natív Azure tanúsítványhitelesítés mobileconfig fájlt. Meg kell manuálisan konfigurálnia a natív IKEv2 VPN-ügyfél minden Azure csatlakozó Mac gépen. A **általános** mappában van, hogy meg kell azt konfigurálnia minden információt. Ha a letöltés nem jelenik meg az általános mappában, valószínű, hogy az IKEv2 nem volt kijelölve alagút típusként. A kijelölt IKEv2 készítése a zip-fájlt újra beolvasása az általános mappában. Az általános mappában a következő fájlokat tartalmazza:

* **VpnSettings.xml**, amely tartalmazza a fontos beállítások, például a kiszolgáló címét és alagút típusa. 
* **VpnServerRoot.cer**, amely tartalmazza a legfelső szintű tanúsítvány P2S csatlakozási telepítés során az Azure VPN Gateway érvényesítéséhez szükséges.

Az alábbi lépések segítségével konfigurálja a a natív VPN-ügyfél a Mac alapú hitelesítés. Végre kell hajtania ezeket a lépéseket, minden Azure csatlakozó Mac gépen:

1. Importálás a **VpnServerRoot** legfelső szintű tanúsítvány a Mac. Erre a fájl felülírását visszakerülnek a Mac, dupla kattintással.  
Kattintson a **Hozzáadás** importálásához.

  ![tanúsítvány hozzáadása](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Ehhez kattintson duplán a tanúsítványra lehet, hogy nem jelennek meg a **Hozzáadás** párbeszédpanelen, de a tanúsítvány a megfelelő tárolóban van telepítve. A tanúsítvány a bejelentkezési kulcsláncban a tanúsítványok kategóriához tartozó ellenőrizheti.
  
2. Győződjön meg arról, hogy telepítette-e a legfelső szintű tanúsítvány P2S-beállítások konfigurálása, során az Azure-bA feltöltött által kibocsátott ügyféltanúsítványt. Ez eltér az előző lépésben telepített VPNServerRoot. Az ügyféltanúsítvány-hitelesítésre használják, és szükséges. További információ a tanúsítványok létrehozása: [tanúsítványok előállításához](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Ügyfél-tanúsítvány telepítésével kapcsolatos információkért lásd: [telepíthet ügyféltanúsítványt](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Nyissa meg a **hálózati** párbeszédpanel **hálózati beállítások** kattintson **"+"** egy új VPN-ügyfél kapcsolati profil a P2S kapcsolat az Azure virtuális hálózat létrehozásához.

  A **felület** értéke 'VPN' és **VPN-típus** értéke "IKEv2". Adja meg a profil nevét a **szolgáltatásnév** mezőben, majd kattintson az **létrehozása** a VPN-ügyfél-csatlakozási profil létrehozásához.

  ![network](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. Az a **általános** mappa, a a **VpnSettings.xml** fájlt, másolja a **VpnServer** címke értéke. Illessze be ezt az értéket a **kiszolgálócímet** és **távoli azonosítója** mezők a profil.

  ![kiszolgáló adatai](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Kattintson a **hitelesítési beállítások** válassza **tanúsítvány**. 

  ![hitelesítési beállítások](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
6. Kattintson a **válasszon...** az ügyféltanúsítványt a hitelesítéshez használni kívánt elemre. Ez az a 2. lépésben telepített tanúsítvány.

  ![tanúsítvány](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Válassza ki az identitás** választhat tanúsítványainak listáját jeleníti meg. Válassza ki a megfelelő tanúsítványt, majd kattintson az **Folytatás**.

  ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. Az a **helyi azonosítója** mezőben adja meg azt a tanúsítványt (a 6. lépés). Ebben a példában a "ikev2Client.com" esetében. Kattintson a **alkalmaz** gombra a módosítások mentéséhez.

  ![alkalmaz](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. Az a **hálózati** párbeszédpanel, kattintson a **alkalmaz** összes módosítások mentéséhez. Kattintson a **Connect** elindítani az Azure VNet P2S csatlakozni.

## <a name="next-steps"></a>További lépések

Térjen vissza a cikkhez [végezze el a P2S konfigurálását](vpn-gateway-howto-point-to-site-rm-ps.md).

A pont–hely hibaelhárítási információiért tekintse át az [Azure pont–hely kapcsolatok hibaelhárításával](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) foglalkozó cikket.