---
title: Az Azure-ba irányuló pont–hely típusú kapcsolat létrehozása az Azure Virtual WAN használatával | Microsoft Docs
description: Ez az oktatóanyag az Azure-ba irányuló pont–hely VPN-kapcsolat létrehozását ismerteti az Azure Virtual WAN használatával.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: alzam
ms.openlocfilehash: 02c8bf24d4ddb6408160da7a4c517d6c8c82de5f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450903"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Oktatóanyag: felhasználói VPN-kapcsolat létrehozása az Azure Virtual WAN használatával

Ez az oktatóanyag bemutatja, hogyan kapcsolódhat a Virtual WAN használatával az Azure-ban lévő erőforrásaihoz IPsec/IKE (IKEv2) vagy OpenVPN VPN-kapcsolaton keresztül. Ehhez a kapcsolattípushoz konfigurálni kell egy ügyfelet az ügyfélszámítógépen. A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintését](virtual-wan-about.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * WAN létrehozása
> * Elosztó létrehozása
> * Pont–hely konfiguráció létrehozása
> * VPN-ügyfél profiljának letöltése
> * Pont–hely konfiguráció alkalmazása hubra
> * Virtuális hálózat csatlakoztatása elosztóhoz
> * A VPN-ügyfél konfigurációjának letöltése és alkalmazása
> * A virtuális WAN megtekintése
> * Erőforrás állapotának megtekintése

![A Virtual WAN ábrája](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Előzetes teendők

A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

* Rendelkezik egy virtuális hálózattal, amelyhez csatlakozni szeretne. Győződjön meg arról, hogy a helyszíni hálózatok egyik alhálózata sem fedi át azokat a virtuális hálózatokat, amelyekhez csatlakozni szeretne. Ha virtuális hálózatot szeretne létrehozni a Azure Portalban, tekintse meg a rövid [útmutatót.](../virtual-network/quick-create-portal.md)

* A virtuális hálózat nem rendelkezik virtuális hálózati átjárókkal. Ha a virtuális hálózat átjáróval rendelkezik (VPN vagy ExpressRoute), akkor el kell távolítania az összes átjárót. Ehhez a konfigurációhoz az szükséges, hogy a virtuális hálózatok a virtuális WAN hub-átjáróhoz legyenek csatlakoztatva.

* Igényeljen egy IP-címtartományt az elosztó régiójában. A hub egy virtuális WAN által létrehozott és használt virtuális hálózat. Az hubhoz megadott címtartomány nem fedi át a meglévő virtuális hálózatait, amelyhez csatlakozik. Emellett nem lehet átfedésben azokkal a címtartományokkal sem, amelyekhez a helyszínen csatlakozik. Ha nem ismeri a helyszíni hálózati konfigurációjában található IP-címtartományok körét, akkor egyeztessen valakivel, aki ezeket az adatokat megadhatja Önnek.

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="wan"></a>Virtuális WAN létrehozása

Egy böngészőből lépjen az [Azure Portalra](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

1. Navigáljon a virtuális WAN lapra. A portálon kattintson az **+Erőforrás létrehozása** gombra. Írja be a **virtuális WAN** kifejezést a keresőmezőbe, majd válassza az ENTER billentyűt.
2. Válassza ki a **virtuális WAN** elemet az eredmények közül. A virtuális WAN lapon kattintson a **Létrehozás** elemre a WAN létrehozása lap megnyitásához.
3. A **WAN létrehozása** lap **alapok** lapján töltse ki a következő mezőket:

   ![Virtuális WAN](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Előfizetés** – Válassza ki a használni kívánt előfizetést.
   * **Erőforráscsoport** – új létrehozása vagy meglévő használata.
   * **Erőforráscsoport helye** – válasszon ki egy erőforrás-helyet a legördülő listából. A WAN egy globális erőforrás, és nem egy adott régióhoz tartozik. Mindazonáltal mégis ki kell választania egy régiót, hogy könnyebben kezelhesse és megtalálhassa a létrehozott WAN-erőforrást.
   * **Név** – írja be a WAN-híváshoz használni kívánt nevet.
   * **Írja be a következőt:** Standard. Ha alapszintű WAN-t hoz létre, akkor csak egy alapszintű hubot hozhat létre. Az alapszintű hubok csak a VPN-helyek közötti kapcsolatra képesek.
4. Miután befejezte a mezők kitöltését, válassza a **felülvizsgálat + létrehozás**lehetőséget.
5. Az ellenőrzés után válassza a **Létrehozás** lehetőséget a virtuális WAN létrehozásához.

## <a name="site"></a>Üres virtuális központ létrehozása

1. A virtuális WAN területen válassza a hubok lehetőséget, majd kattintson az **+ új hub** elemre.

   ![új hely](media/virtual-wan-point-to-site-portal/hub1.jpg)
2. A virtuális központ létrehozása lapon töltse ki a következő mezőket.

   **Régió** – válassza ki azt a régiót, amelyben a virtuális hubot telepíteni szeretné.

   **Név** – adja meg a virtuális központ meghívásához használni kívánt nevet.

   **Hub magánhálózati címtartomány** – a központ CIDR jelölése.

   ![új hely](media/virtual-wan-point-to-site-portal/hub2.jpg)  
3. Kattintson a **felülvizsgálat + létrehozás** gombra.
4. Az **átadott érvényesítés** lapon kattintson a **Létrehozás** gombra.

## <a name="site"></a>P2S-konfiguráció létrehozása

A pont–hely konfiguráció határozza meg a távoli ügyfelek csatlakoztatására vonatkozó paramétereket.

1. Lépjen a **Minden erőforrás** menüpontra.
2. Kattintson a létrehozott virtuális WAN-ra.
3. Kattintson a lap tetején a **+ felhasználói VPN-konfiguráció létrehozása** lehetőségre az **új felhasználói VPN-konfiguráció létrehozása** lap megnyitásához.

   ![új hely](media/virtual-wan-point-to-site-portal/p2s1.jpg)
4. Az **új felhasználói VPN-konfiguráció létrehozása** lapon töltse ki a következő mezőket:

   **Konfiguráció neve** – Ez az a név, amellyel hivatkozni szeretne a konfigurációra.

   **Alagút típusa** – Az alagúthoz használni kívánt protokoll.

   **Főtanúsítvány neve** – A tanúsítvány leíró neve.

   **Nyilvános tanúsítvány-adatok** -Base-64 kódolt X. 509 tanúsítvány-adatok.
  
   ![új hely](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. Kattintson a **Létrehozás** elemre a konfiguráció létrehozásához.

## <a name="hub"></a>Hub-hozzárendelés szerkesztése

1. Navigáljon a virtuális WAN alatt lévő **hubok** panelre
2. Válassza ki azt a hubot, amelyhez hozzá szeretné rendelni a VPN-kiszolgáló konfigurációját, és kattintson a **.** ..

   ![új hely](media/virtual-wan-point-to-site-portal/p2s4.jpg)
3. Kattintson a **virtuális központ szerkesztése**elemre.
4. Jelölje be a **pont – hely átjáró belefoglalása** jelölőnégyzetet, és válassza ki a kívánt **átjáró-méretezési egységet** .

   ![új hely](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. Adja meg azt a **címkészletet** , amelyből a VPN-ügyfelek IP-címeket rendelnek.
6. Kattintson a **megerősítés** gombra.
7. A művelet végrehajtása akár 30 percet is igénybe vehet.

## <a name="device"></a>VPN-profil letöltése

A VPN-profillal konfigurálhatja az ügyfeleket.

1. A virtuális WAN lapján kattintson a **felhasználói VPN-konfigurációk**elemre.
2. A lap tetején kattintson a **felhasználói VPN-konfiguráció letöltése**elemre.
3. Miután befejeződött a fájl létrehozása, a hivatkozásra kattintva letöltheti.
4. A profil fájl segítségével konfigurálja a VPN-ügyfeleket.

### <a name="configure-user-vpn-clients"></a>Felhasználói VPN-ügyfelek konfigurálása
A letöltött profillal konfigurálhatja a távelérésű ügyfeleket. Az eljárás minden operációs rendszer esetén eltérő, kövesse az alábbi utasításokat:

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Töltse le az OpenVPN-ügyfelet a hivatalos webhelyről, majd telepítse.
2. Töltse le az átjáró VPN-profilját. Ezt a Azure Portal a felhasználó VPN-konfigurációk lapján vagy a PowerShell új AzureRmVpnClientConfiguration teheti meg.
3. Csomagolja ki a profilt. Nyissa meg az OpenVPN mappában található vpnconfig.ovpn konfigurációs fájlt a Jegyzettömbben.
4. Töltse ki a pont–hely ügyféltanúsítványra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású nyilvános kulcsával. PEM formátumú tanúsítvány esetén egyszerűen nyissa meg a .cer-fájlt, és másolja a Base64-kódolású kulcsot a tanúsítványfejlécek között. Tekintse meg a [Tanúsítvány exportálása a kódolt nyilvános kulcs beolvasására](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site) című témakört.
5. Töltse ki a titkos kulcsra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású titkos kulcsával. Tekintse [meg a titkos kulcs kinyerését](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn-clients#windows) ismertető témakört.
6. Ne módosítson semmilyen egyéb mezőt. Az ügyfélbemenet kitöltött konfigurációjával csatlakozhat a VPN-hez.
7. Másolja a vpnconfig.ovpn fájlt a C:\Program Files\OpenVPN\config mappába.
8. Kattintson a jobb gombbal az OpenVPN ikonjára a rendszertálcán, majd kattintson a Csatlakozás parancsra.

##### <a name="ikev2"></a>IKEv2

1. Válassza ki a Windows rendszerű számítógép architektúrájának megfelelő VPN-ügyfélkonfigurációs fájlokat. 64 bites processzorarchitektúra esetén a „VpnClientSetupAmd64” telepítőcsomagot válassza. 32 bites processzorarchitektúra esetén a „VpnClientSetupX86” telepítőcsomagot válassza.
2. Kattintson duplán a csomagra a telepítéséhez. Ha megjelenik a SmartScreen egy előugró ablaka, kattintson a További információ, majd a Futtatás mindenképpen elemre.
3. Nyissa meg az ügyfélszámítógépen a Hálózati beállítások eszközt, és kattintson a VPN elemre. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott.
4. Mielőtt megkísérli a csatlakozást, ellenőrizze, hogy telepített-e ügyféltanúsítványt az ügyfélszámítógépen. A natív Azure-tanúsítványhitelesítési típus használata esetén a hitelesítéshez ügyféltanúsítványra van szükség. A tanúsítványok létrehozásával kapcsolatos további információkért lásd: [tanúsítványok létrehozása](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site). Az ügyféltanúsítvány telepítésével kapcsolatban az ügyféltanúsítvány telepítésével foglalkozó témakörben tekinthet meg további információt.

## <a name="viewwan"></a>Virtuális WAN megtekintése

1. Lépjen a virtuális WAN-ra.
2. Az Áttekintés lapon a térképen látható pontok mindegyike egy elosztót jelöl. Az elosztók állapotösszegzéséért vigye a mutatót az egyes pontok fölé.
3. Az elosztók és kapcsolatok szakaszában láthatja az elosztók állapotát, helyét, régióját, VPN-kapcsolati állapotát, valamint a bájtban kifejezett be- és kimenő forgalmát.

## <a name="viewhealth"></a>Az erőforrás állapotának megtekintése

1. Lépjen a WAN-ra.
2. A WAN lapjának **TÁMOGATÁS + hibaelhárítás** szakaszában kattintson az **Állapot** lehetőségre, és tekintse meg az erőforrást.


## <a name="cleanup"></a>Erőforrások törlése

Ha már nincs szükség ezekre az erőforrásokra, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást. A „myResourceGroup” helyére írja be az erőforráscsoport nevét, és futtassa a következő PowerShell-parancsot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Következő lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.
