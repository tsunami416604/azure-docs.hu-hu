---
title: Az Azure-ba irányuló pont–hely típusú kapcsolat létrehozása az Azure Virtual WAN használatával | Microsoft Docs
description: Ez az oktatóanyag az Azure-ba irányuló pont–hely VPN-kapcsolat létrehozását ismerteti az Azure Virtual WAN használatával.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 03/18/2020
ms.author: alzam
ms.openlocfilehash: fd415e1da00f52a9a3b55c946a07a30cf841cf4a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80060298"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Oktatóanyag: Felhasználói VPN-kapcsolat létrehozása az Azure Virtual WAN használatával

Ez az oktatóanyag bemutatja, hogyan kapcsolódhat a Virtual WAN használatával az Azure-ban lévő erőforrásaihoz IPsec/IKE (IKEv2) vagy OpenVPN VPN-kapcsolaton keresztül. Ehhez a kapcsolattípushoz konfigurálni kell egy ügyfelet az ügyfélszámítógépen. A Virtual WAN-ról a [Virtual WAN áttekintése című témakörben olvashat bővebben.](virtual-wan-about.md)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * WAN létrehozása
> * Elosztó létrehozása
> * Pont–hely konfiguráció létrehozása
> * VPN-ügyfélprofil letöltése
> * Pont–hely konfiguráció alkalmazása hubra
> * Virtuális hálózat csatlakoztatása elosztóhoz
> * A VPN-ügyfél konfigurációjának letöltése és alkalmazása
> * A virtuális WAN megtekintése

![Virtuális WAN ábrája](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

* Van egy virtuális hálózat, amelyhez csatlakozni szeretne. Ellenőrizze, hogy a helyszíni hálózatok egyik alhálózata sem fedi-e át azokat a virtuális hálózatokat, amelyekhez csatlakozni szeretne. Virtuális hálózat létrehozásához az Azure Portalon tekintse meg a [rövid útmutatót.](../virtual-network/quick-create-portal.md)

* A virtuális hálózat nem rendelkezik virtuális hálózati átjárókkal. Ha a virtuális hálózat rendelkezik átjáróval (VPN vagy ExpressRoute), el kell távolítania az összes átjárót. Ez a konfiguráció megköveteli, hogy a virtuális hálózatok csatlakoznak helyett, a Virtual WAN hub átjáró.

* Igényeljen egy IP-címtartományt az elosztó régiójában. A hub egy virtuális hálózat, amelyet a Virtual WAN hoz létre és használ. A hubhoz megadott címtartomány nem fedheti át a meglévő virtuális hálózatokat, amelyekhez csatlakozik. Emellett nem lehet átfedésben azokkal a címtartományokkal sem, amelyekhez a helyszínen csatlakozik. Ha nem ismeri a helyszíni hálózati konfigurációban található IP-címtartományokat, egyeztessen valakivel, aki meg tudja adni ezeket az adatokat.

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Virtuális WAN létrehozása

Egy böngészőből lépjen az [Azure Portalra](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

1. Nyissa meg a Virtual WAN lapot. A portálon kattintson az **+Erőforrás létrehozása** gombra. Írja be a **Virtual WAN** parancsot a keresőmezőbe, és válassza az Enter lehetőséget.
2. Válassza ki a **Virtual WAN** az eredmények közül. A Virtual WAN lapon kattintson a **Létrehozás** gombra a WAN létrehozása lap megnyitásához.
3. A **WAN létrehozása** lapon az **Alapok** lapon töltse ki a következő mezőket:

   ![Virtuális WAN](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Előfizetés** – Válassza ki a használni kívánt előfizetést.
   * **Erőforráscsoport** – Új létrehozása vagy meglévő használata.
   * **Erőforráscsoport helye** – Erőforrás helyének kiválasztása a legördülő menüből. A WAN egy globális erőforrás, és nem egy adott régióhoz tartozik. Mindazonáltal mégis ki kell választania egy régiót, hogy könnyebben kezelhesse és megtalálhassa a létrehozott WAN-erőforrást.
   * **Név** – Írja be a WAN-nak hívni kívánt nevet.
   * **Típus:** Standard. Ha alapwanst hoz létre, csak alapszintű elosztót hozhat létre. Az alapvető elosztók csak vpn-helyek közötti kapcsolatra képesek.
4. Miután befejezte a mezők kitöltését, válassza a **Véleményezés +Létrehozás**gombot.
5. Az érvényesítés sikeressítése után válassza a **Létrehozás** lehetőséget a virtuális WAN létrehozásához.

## <a name="create-an-empty-virtual-hub"></a><a name="hub"></a>Üres virtuális elosztó létrehozása

1. A virtuális WAN alatt válassza a Hubs lehetőséget, és kattintson **az +Új központ**

   ![új hely](media/virtual-wan-point-to-site-portal/hub1.jpg)
2. A virtuális központ létrehozása lapon töltse ki a következő mezőket.

   **Régió** – Válassza ki azt a régiót, amelyben a virtuális központot telepíteni szeretné.

   **Név** – Adja meg a virtuális központ meghívni kívánt nevét.

   **Hub privát címtér** – A hub címtartománya CIDR jelölésben.

   ![új hely](media/virtual-wan-point-to-site-portal/hub2.jpg)  
3. Kattintson **a Véleményezés + létrehozás gombra**
4. Az **érvényesítési átadott** lapon kattintson a **Létrehozás gombra**

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Pont–hely konfiguráció létrehozása

A pont–hely konfiguráció határozza meg a távoli ügyfelek csatlakoztatására vonatkozó paramétereket.

1. Lépjen a **Minden erőforrás** menüpontra.
2. Kattintson a létrehozott virtuális WAN-ra.
3. Kattintson a lap tetején a **+Felhasználói VPN-konfiguráció létrehozása** gombra az **Új felhasználói VPN-konfiguráció létrehozása** lap megnyitásához.

   ![új hely](media/virtual-wan-point-to-site-portal/p2s1.jpg)
4. Az **Új felhasználói VPN-konfiguráció létrehozása** lapon töltse ki a következő mezőket:

   **Konfiguráció neve** – Ez az a név, amellyel hivatkozni szeretne a konfigurációra.

   **Alagút típusa** – Az alagúthoz használni kívánt protokoll.

   **Főtanúsítvány neve** – A tanúsítvány leíró neve.

   **Nyilvános tanúsítvány adatok** - Base-64 kódolt X.509 tanúsítvány adatait.
  
5. Kattintson a **Létrehozás** elemre a konfiguráció létrehozásához.

## <a name="edit-hub-assignment"></a><a name="edit"></a>Hubhozzárendelés szerkesztése

1. Keresse meg a **Hubs panelt** a virtuális WAN
2. Válassza ki a hubot, amelyhez a VPN-kiszolgáló konfigurációját társítani szeretné, és kattintson **a ...**

   ![új hely](media/virtual-wan-point-to-site-portal/p2s4.jpg)
3. Kattintson **a Virtuális központ szerkesztése gombra.**
4. Jelölje be a **Pont-hely átjáró felvétele** jelölőnégyzetet, és válassza ki a kívánt **átjáróméretezési egységet.**

   ![új hely](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. Adja meg azt a **címkészletet,** amelyből a VPN-ügyfelek IP-címeket kapnak.
6. Kattintson a **Megerősítés gombra**
7. A művelet akár 30 percet is igénybe vehet.

## <a name="download-vpn-profile"></a><a name="download"></a>VPN-profil letöltése

A VPN-profillal konfigurálhatja az ügyfeleket.

1. A virtuális WAN lapján kattintson a **Felhasználói VPN-konfigurációk**elemre.
2. A lap tetején kattintson a **Felhasználói VPN-konfiguráció letöltése gombra.**
3. Miután befejeződött a fájl létrehozása, a hivatkozásra kattintva letöltheti.
4. A profilfájl segítségével konfigurálhatja a VPN-ügyfeleket.

### <a name="configure-user-vpn-clients"></a>Felhasználói VPN-ügyfelek konfigurálása
A letöltött profillal konfigurálhatja a távelérésű ügyfeleket. Az eljárás minden operációs rendszer esetén eltérő, kövesse az alábbi utasításokat:

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Töltse le az OpenVPN-ügyfelet a hivatalos webhelyről, majd telepítse.
2. Töltse le az átjáró VPN-profilját. Ez az Azure Portal felhasználói VPN-konfigurációk lapján, illetve a PowerShell új AzureRmVpnClientConfiguration konfigurációja lapon végezhető el.
3. Csomagolja ki a profilt. Nyissa meg az OpenVPN mappában található vpnconfig.ovpn konfigurációs fájlt a Jegyzettömbben.
4. Töltse ki a pont–hely ügyféltanúsítványra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású nyilvános kulcsával. PEM formátumú tanúsítvány esetén egyszerűen nyissa meg a .cer-fájlt, és másolja a Base64-kódolású kulcsot a tanúsítványfejlécek között. A lépéseket a [tanúsítvány exportálása a kódolt nyilvános kulcs bekésezéséhez című témakörben található.](certificates-point-to-site.md)
5. Töltse ki a titkos kulcsra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású titkos kulcsával. A lépéseket a [Személyes kulcs kinyerése.](howto-openvpn-clients.md#windows).
6. Ne módosítson semmilyen egyéb mezőt. Az ügyfélbemenet kitöltött konfigurációjával csatlakozhat a VPN-hez.
7. Másolja a vpnconfig.ovpn fájlt a C:\Program Files\OpenVPN\config mappába.
8. Kattintson a jobb gombbal az OpenVPN ikonjára a rendszertálcán, majd kattintson a Csatlakozás parancsra.

##### <a name="ikev2"></a>IKEv2

1. Válassza ki a Windows rendszerű számítógép architektúrájának megfelelő VPN-ügyfélkonfigurációs fájlokat. 64 bites processzorarchitektúra esetén a „VpnClientSetupAmd64” telepítőcsomagot válassza. 32 bites processzorarchitektúra esetén a „VpnClientSetupX86” telepítőcsomagot válassza.
2. Kattintson duplán a csomagra a telepítéséhez. Ha megjelenik a SmartScreen egy előugró ablaka, kattintson a További információ, majd a Futtatás mindenképpen elemre.
3. Nyissa meg az ügyfélszámítógépen a Hálózati beállítások eszközt, és kattintson a VPN elemre. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott.
4. Mielőtt megkísérli a csatlakozást, ellenőrizze, hogy telepített-e ügyféltanúsítványt az ügyfélszámítógépen. A natív Azure-tanúsítványhitelesítési típus használata esetén a hitelesítéshez ügyféltanúsítványra van szükség. A tanúsítványok létrehozásáról a Tanúsítványok létrehozása című [témakörben](certificates-point-to-site.md)talál további információt. Az ügyféltanúsítványok telepítéséről az [Ügyféltanúsítvány telepítése című](../vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md)témakörben talál további információt.

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>A virtuális WAN megtekintése

1. Lépjen a virtuális WAN-ra.
2. Az Áttekintés lapon a térképen látható pontok mindegyike egy elosztót jelöl.
3. Az elosztók és kapcsolatok szakaszában láthatja az elosztók állapotát, helyét, régióját, VPN-kapcsolati állapotát, valamint a bájtban kifejezett be- és kimenő forgalmát.


## <a name="clean-up-resources"></a><a name="cleanup"></a>Az erőforrások eltávolítása

Ha már nincs szükség ezekre az erőforrásokra, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást. A „myResourceGroup” helyére írja be az erőforráscsoport nevét, és futtassa a következő PowerShell-parancsot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.
