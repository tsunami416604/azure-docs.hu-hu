---
title: Az Azure-ba irányuló pont–hely típusú kapcsolat létrehozása az Azure Virtual WAN használatával | Microsoft Docs
description: Ez az oktatóanyag az Azure-ba irányuló pont–hely VPN-kapcsolat létrehozását ismerteti az Azure Virtual WAN használatával.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 09/21/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect remote users to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: bf0e766f082b2e137c90b5ea66bb7570bea2e1e6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963372"
---
# <a name="tutorial-create-a-point-to-site-connection-using-azure-virtual-wan-preview"></a>Oktatóanyag: Pont–hely kapcsolat létrehozása az Azure Virtual WAN (előzetes verzió) használatával

Ez az oktatóanyag bemutatja, hogyan kapcsolódhat a Virtual WAN használatával az Azure-ban lévő erőforrásaihoz IPsec/IKE (IKEv2) vagy OpenVPN VPN-kapcsolaton keresztül. Ehhez a kapcsolattípushoz konfigurálni kell egy ügyfelet az ügyfélszámítógépen. A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintését](virtual-wan-about.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * WAN létrehozása
> * Pont–hely konfiguráció létrehozása
> * Elosztó létrehozása
> * Pont–hely konfiguráció alkalmazása hubra
> * Virtuális hálózat csatlakoztatása elosztóhoz
> * A VPN-ügyfél konfigurációjának letöltése és alkalmazása
> * A virtuális WAN megtekintése
> * Erőforrás állapotának megtekintése
> * Kapcsolatok monitorozása

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]


## <a name="vnet"></a>1. Virtuális hálózat létrehozása

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2. Virtuális WAN létrehozása

Egy böngészőből lépjen az [Azure Portalra](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. Elosztó létrehozása

> [!NOTE]
> Ne válassza a pont–hely átjáró hozzáadására szolgáló beállítást ebben a lépésben.
>

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="site"></a>4. Pont–hely konfiguráció létrehozása

A pont–hely konfiguráció határozza meg a távoli ügyfelek csatlakoztatására vonatkozó paramétereket.

1. Lépjen a **Minden erőforrás** menüpontra.
2. Kattintson a létrehozott virtuális WAN-ra.
3. A **Virtuális WAN-architektúra** részen kattintson a **Pont–hely konfigurációk** elemre.
4. Kattintson az oldal tetején található **+Pont-hely konfiguráció hozzáadása** elemre az **Új pont-hely konfiguráció létrehozása** lap megnyitásához.
5. Az **Új pont-hely konfiguráció létrehozása** lapon töltse ki az alábbi mezőket:

  *  **Konfiguráció neve** – Ez az a név, amellyel hivatkozni szeretne a konfigurációra.
  *  **Alagút típusa** – Az alagúthoz használni kívánt protokoll.
  *  **Címkészlet** – Ez az IP-címkészlet, amelyből a rendszer IP-címeket rendel az ügyfelekhez.
  *  **Főtanúsítvány neve** – A tanúsítvány leíró neve.
  *  **Főtanúsítvány adatai** – Az X.509-tanúsítvány Base-64-kódolású adatai.

5. Kattintson a **Létrehozás** elemre a konfiguráció létrehozásához.

## <a name="hub"></a>5. Hubhozzárendelés szerkesztése

1. A virtuális WAN lapján kattintson a **Pont–hely konfigurációk** elemre.
2. A **Hub** területen láthatja azoknak a konfigurációknak a listáját, amelyek még nem lettek hubhoz csatlakoztatva.
3. Jelölje ki a társítani kívánt konfigurációt, majd kattintson a **Hubhozzárendelés szerkesztése** parancsra.
4. Jelölje ki azt az egy vagy több hubot a legördülő listában, amelyet társítani kíván a konfigurációhoz.
5. Kattintson a **Hozzárendelés** gombra. 
6. A művelet végrehajtása 30 percig is eltarthat.

## <a name="vnet"></a>6. A virtuális hálózat csatlakoztatása egy elosztóhoz

Ebben a lépésben a társviszony-kapcsolatot hozzuk létre az elosztó és egy virtuális hálózat között. Ismételje meg a fenti lépéseket minden csatlakoztatni kívánt virtuális hálózat esetében.

1. A virtuális WAN lapján kattintson a **Virtuális hálózati kapcsolat** elemre.
2. A virtuális hálózati kapcsolat lapján kattintson a **+Kapcsolat hozzáadása** elemre.
3. A **Kapcsolat hozzáadása** lapon töltse ki a következő mezőket:

    * **Kapcsolat neve** – Nevezze el a kapcsolatot.
    * **Elosztók** – Válassza ki azt az elosztót, amelyet a kapcsolattal társítani kíván.
    * **Előfizetés** – Ellenőrizze az előfizetést.
    * **Virtuális hálózat** – Válassza ki azt a virtuális hálózatot, amelyet az elosztóhoz csatlakoztatni kíván. A virtuális hálózat nem rendelkezhet már meglévő virtuális hálózati átjáróval.

## <a name="device"></a>7. VPN-profil letöltése

A VPN-profillal konfigurálhatja az ügyfeleket.

1. A virtuális WAN lapján kattintson a **Pont–hely konfigurációk** elemre.
2. Kattintson a lap tetején található **Pont–hely profil letöltése** parancsra. 
3. Miután befejeződött a fájl létrehozása, a hivatkozásra kattintva letöltheti.
4. A profilfájllal konfigurálhatja a pont–hely ügyfeleket.

## <a name="device"></a>8. Pont–hely ügyfelek konfigurálása
A letöltött profillal konfigurálhatja a távelérésű ügyfeleket. Az eljárás minden operációs rendszer esetén eltérő, kövesse az alábbi utasításokat:

### <a name="microsoft-windows"></a>Microsoft Windows
#### <a name="openvpn"></a>OpenVPN

1.  Töltse le az OpenVPN-ügyfelet a hivatalos webhelyről, majd telepítse.
2.  Töltse le az átjáró VPN-profilját. Ezt az Azure Portal Point-to-site configurations (Pont–hely konfigurációk) lapján, vagy a PowerShell New-AzureRmVpnClientConfiguration parancsával teheti meg.
3.  Csomagolja ki a profilt. Nyissa meg az OpenVPN mappában található vpnconfig.ovpn konfigurációs fájlt a Jegyzettömbben.
4.  Töltse ki a pont–hely ügyféltanúsítványra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású nyilvános kulcsával. PEM formátumú tanúsítvány esetén egyszerűen nyissa meg a .cer-fájlt, és másolja a Base64-kódolású kulcsot a tanúsítványfejlécek között. Itt tekintheti meg, hogyan szerezheti meg a kódolt nyilvános kulcsot a tanúsítvány exportálásával.
5.  Töltse ki a titkos kulcsra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású titkos kulcsával. Itt tekintheti meg a titkos kulcs kinyerésének a módját.
6.  Ne módosítson semmilyen egyéb mezőt. Az ügyfélbemenet kitöltött konfigurációjával csatlakozhat a VPN-hez.
7.  Másolja a vpnconfig.ovpn fájlt a C:\Program Files\OpenVPN\config mappába.
8.  Kattintson a jobb gombbal az OpenVPN ikonjára a rendszertálcán, majd kattintson a Csatlakozás parancsra.

#### <a name="ikev2"></a>IKEv2

1. Válassza ki a Windows rendszerű számítógép architektúrájának megfelelő VPN-ügyfélkonfigurációs fájlokat. 64 bites processzorarchitektúra esetén a „VpnClientSetupAmd64” telepítőcsomagot válassza. 32 bites processzorarchitektúra esetén a „VpnClientSetupX86” telepítőcsomagot válassza.
2. Kattintson duplán a csomagra a telepítéséhez. Ha megjelenik a SmartScreen egy előugró ablaka, kattintson a További információ, majd a Futtatás mindenképpen elemre.
3. Nyissa meg az ügyfélszámítógépen a Hálózati beállítások eszközt, és kattintson a VPN elemre. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott.
4. Mielőtt megkísérli a csatlakozást, ellenőrizze, hogy telepített-e ügyféltanúsítványt az ügyfélszámítógépen. A natív Azure-tanúsítványhitelesítési típus használata esetén a hitelesítéshez ügyféltanúsítványra van szükség. A tanúsítványok létrehozásával kapcsolatban a tanúsítványok létrehozásával foglalkozó témakörben tekinthet meg további információt. Az ügyféltanúsítvány telepítésével kapcsolatban az ügyféltanúsítvány telepítésével foglalkozó témakörben tekinthet meg további információt.

### <a name="mac-os-x"></a>Mac (OS X)
#### <a name="openvpn"></a>OpenVPN

1.  Töltsön le egy OpenVPN-ügyfelet, például a Tunnelblicket (innen tölthető le: https://tunnelblick.net/downloads.html), majd telepítse. 
2.  Töltse le az átjáró VPN-profilját. Ezt az Azure Portal Pont–hely konfigurációk lapján, vagy a PowerShell New-AzureRmVpnClientConfiguration parancsával teheti meg.
3.  Csomagolja ki a profilt. Nyissa meg az OpenVPN mappában található vpnconfig.ovpn konfigurációs fájlt a Jegyzettömbben.
4.  Töltse ki a pont–hely ügyféltanúsítványra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású nyilvános kulcsával. PEM formátumú tanúsítvány esetén egyszerűen nyissa meg a .cer-fájlt, és másolja a Base64-kódolású kulcsot a tanúsítványfejlécek között. Itt tekintheti meg, hogyan szerezheti meg a kódolt nyilvános kulcsot a tanúsítvány exportálásával.
5.  Töltse ki a titkos kulcsra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású titkos kulcsával. Itt tekintheti meg a titkos kulcs kinyerésének a módját.
6.  Ne módosítson semmilyen egyéb mezőt. Az ügyfélbemenet kitöltött konfigurációjával csatlakozhat a VPN-hez.
7.  Kattintson duplán a profilfájlra a profil létrehozásához a Tunnelblickben.
8.  Indítsa el a Tunnelblicket az Alkalmazások mappából.
9.  Kattintson a Tunnelblick ikonjára a rendszertálcán, és válassza a Csatlakozás parancsot.

#### <a name="ikev2"></a>IKEv2

Az Azure nem biztosít mobilkonfigurációs fájlt a natív Azure-tanúsítványhitelesítéshez. A natív IKEv2 VPN-ügyfelet manuálisan kell konfigurálnia az Azure-hoz csatlakozó Mac gépek mindegyikén. A Generic mappa tartalmazza az ügyfél konfigurálásához szükséges összes információt. Ha nem látja a Generic mappát a letöltésben, valószínűleg nem az IKEv2 lett kiválasztva alagúttípusként. Az IKEv2 kiválasztását követően hozza létre újból a ZIP-fájlt a Generic mappa lekéréséhez. A Generic mappa az alábbi fájlokat tartalmazza:

- VpnSettings.xml, amely fontos beállításokat tartalmaz, például a kiszolgálócímet és az alagúttípust.
- VpnServerRoot.cer, amely az Azure VPN Gatewaynek a pont–hely kapcsolat beállítása során történő érvényesítéséhez szükséges főtanúsítványt tartalmazza.

## <a name="viewwan"></a>9. A virtuális WAN megtekintése

1. Lépjen a virtuális WAN-ra.
2. Az Áttekintés lapon a térképen látható pontok mindegyike egy elosztót jelöl. Az elosztók állapotösszegzéséért vigye a mutatót az egyes pontok fölé.
3. Az elosztók és kapcsolatok szakaszában láthatja az elosztók állapotát, helyét, régióját, VPN-kapcsolati állapotát, valamint a bájtban kifejezett be- és kimenő forgalmát.

## <a name="viewhealth"></a>10. Az erőforrás állapotának megtekintése

1. Lépjen a WAN-ra.
2. A WAN lapjának **TÁMOGATÁS + hibaelhárítás** szakaszában kattintson az **Állapot** lehetőségre, és tekintse meg az erőforrást.

## <a name="connectmon"></a>11. Kapcsolatok monitorozása

Létrehozhat egy kapcsolatot az Azure-beli virtuális gépek és a távoli helyek közötti kommunikáció monitorozására. A kapcsolatmonitor beállításával kapcsolatos információkért lásd a [hálózati kommunikáció monitorozását](~/articles/network-watcher/connection-monitor.md) ismertető szakaszt. A forrásmező a virtuális gép IP-címe az Azure-ban, a cél IP-cím pedig a hely IP-címe.

## <a name="cleanup"></a>12. Az erőforrások eltávolítása

Ha már nincs szükség ezekre az erőforrásokra, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást. A „myResourceGroup” helyére írja be az erőforráscsoport nevét, és futtassa a következő PowerShell-parancsot:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * WAN létrehozása
> * Hely létrehozása
> * Elosztó létrehozása
> * Elosztó csatlakoztatása helyhez
> * Virtuális hálózat csatlakoztatása elosztóhoz
> * A VPN-eszköz konfigurációjának letöltése és alkalmazása
> * A virtuális WAN megtekintése
> * Erőforrás állapotának megtekintése
> * Kapcsolatok monitorozása

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.