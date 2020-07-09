---
title: Az Azure-ba irányuló pont–hely típusú kapcsolat létrehozása az Azure Virtual WAN használatával | Microsoft Docs
description: Ez az oktatóanyag az Azure-ba irányuló pont–hely VPN-kapcsolat létrehozását ismerteti az Azure Virtual WAN használatával.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 06/29/2020
ms.author: alzam
ms.openlocfilehash: 9c93ad0357011008c45b2898260a655509b02dc2
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/30/2020
ms.locfileid: "85560738"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Oktatóanyag: felhasználói VPN-kapcsolat létrehozása az Azure Virtual WAN használatával

Ez az oktatóanyag bemutatja, hogyan kapcsolódhat a Virtual WAN használatával az Azure-ban lévő erőforrásaihoz IPsec/IKE (IKEv2) vagy OpenVPN VPN-kapcsolaton keresztül. Ehhez a kapcsolattípushoz konfigurálni kell egy ügyfelet az ügyfélszámítógépen. A virtuális WAN-ról további információt a [virtuális WAN áttekintése](virtual-wan-about.md) című témakörben talál.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * WAN létrehozása
> * Pont–hely konfiguráció létrehozása
> * Elosztó létrehozása
> * DNS-kiszolgálók meghatározása
> * VPN-ügyfél profiljának letöltése
> * A virtuális WAN megtekintése

![Virtuális WAN ábrája](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

* Rendelkezik egy virtuális hálózattal, amelyhez csatlakozni szeretne. Győződjön meg arról, hogy a helyszíni hálózatok egyik alhálózata sem fedi át azokat a virtuális hálózatokat, amelyekhez csatlakozni szeretne. Ha virtuális hálózatot szeretne létrehozni a Azure Portalban, tekintse meg a rövid [útmutatót.](../virtual-network/quick-create-portal.md)

* A virtuális hálózat nem rendelkezik virtuális hálózati átjárókkal. Ha a virtuális hálózat átjáróval rendelkezik (VPN vagy ExpressRoute), akkor el kell távolítania az összes átjárót. Ehhez a konfigurációhoz az szükséges, hogy a virtuális hálózatok a virtuális WAN hub-átjáróhoz legyenek csatlakoztatva.

* Igényeljen egy IP-címtartományt az elosztó régiójában. A hub egy virtuális WAN által létrehozott és használt virtuális hálózat. Az hubhoz megadott címtartomány nem fedi át a meglévő virtuális hálózatait, amelyhez csatlakozik. Emellett nem lehet átfedésben azokkal a címtartományokkal sem, amelyekhez a helyszínen csatlakozik. Ha nem ismeri a helyszíni hálózati konfigurációjában található IP-címtartományok körét, akkor egyeztessen valakivel, aki ezeket az adatokat megadhatja Önnek.

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Virtuális WAN létrehozása

Egy böngészőből lépjen az [Azure Portalra](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

1. Navigáljon a virtuális WAN lapra. A portálon válassza az **+ erőforrás létrehozása**lehetőséget. Írja be a **virtuális WAN** kifejezést a keresőmezőbe, majd válassza az **ENTER billentyűt**.
1. Válassza ki a **virtuális WAN** elemet az eredmények közül. A virtuális WAN lapon válassza a **Létrehozás** lehetőséget a WAN létrehozása lap megnyitásához.
1. A **WAN létrehozása** lap **alapok** lapján töltse ki a következő mezőket:

   ![Virtual WAN](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Előfizetés** – Válassza ki a használni kívánt előfizetést.
   * **Erőforráscsoport** – új létrehozása vagy meglévő használata.
   * **Erőforráscsoport helye** – válasszon ki egy erőforrás-helyet a legördülő listából. A WAN egy globális erőforrás, és nem egy adott régióhoz tartozik. Mindazonáltal mégis ki kell választania egy régiót, hogy könnyebben kezelhesse és megtalálhassa a létrehozott WAN-erőforrást.
   * **Név** – írja be a WAN-híváshoz használni kívánt nevet.
   * **Írja be a következőt:** Standard. Ha alapszintű WAN-t hoz létre, akkor csak egy alapszintű hubot hozhat létre. Az alapszintű hubok csak a VPN-helyek közötti kapcsolatra képesek.
1. Miután befejezte a mezők kitöltését, válassza a **felülvizsgálat + létrehozás**lehetőséget.
1. Az ellenőrzés után válassza a **Létrehozás** lehetőséget a virtuális WAN létrehozásához.

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Pont–hely konfiguráció létrehozása

A pont–hely konfiguráció határozza meg a távoli ügyfelek csatlakoztatására vonatkozó paramétereket.

1. Lépjen a **Minden erőforrás** menüpontra.
1. Válassza ki a létrehozott virtuális WAN-t.
1. A lap tetején található **+ felhasználói VPN-konfiguráció létrehozása** lapon megnyithatja az **új felhasználói VPN-konfiguráció létrehozása** lapot.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/p2s1.jpg" alt-text="Felhasználói VPN-konfigurációk":::

1. Az **új felhasználói VPN-konfiguráció létrehozása** lapon töltse ki a következő mezőket:

   * **Konfiguráció neve** – Ez az a név, amellyel hivatkozni szeretne a konfigurációra.
   * **Alagút típusa** – Az alagúthoz használni kívánt protokoll.
   * **Főtanúsítvány neve** – A tanúsítvány leíró neve.
   * **Nyilvános tanúsítvány-adatok** -Base-64 kódolt X. 509 tanúsítvány-adatok.
  
1. Válassza a **Létrehozás** lehetőséget a konfiguráció létrehozásához.

## <a name="create-hub-with-point-to-site-gateway"></a><a name="hub"></a>Hub létrehozása pont – hely átjáróval

1. A virtuális WAN területen válassza a hubok lehetőséget, és válassza az **+ új hub**elemet.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub1.jpg" alt-text="új központ":::

1. A virtuális központ létrehozása lapon töltse ki a következő mezőket.

   * **Régió** – válassza ki azt a régiót, amelyben a virtuális hubot telepíteni szeretné.
   * **Név** – adja meg a virtuális központ meghívásához használni kívánt nevet.
   * **Hub magánhálózati címtartomány** – a központ CIDR jelölése.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub2.jpg" alt-text="virtuális központ létrehozása":::

1. A pont – hely lapon hajtsa végre a következő mezőket:

   * **Átjáró skálázási egységei** – ez a felhasználói VPN-átjáró összesített kapacitását jelenti.
   * **Mutasson a hely konfigurációja** lehetőségre, amelyet az előző lépésben hozott létre.
   * **Ügyféloldali címkészlet** – a távoli felhasználók számára.
   * **Egyéni DNS-kiszolgáló IP-címe**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub-with-p2s.png" alt-text="központ pont – hely kapcsolattal":::

1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. Az **átadott érvényesítés** lapon válassza a **Létrehozás**lehetőséget.

## <a name="specify-dns-server"></a><a name="dns"></a>DNS-kiszolgáló meghatározása

A virtuális WAN felhasználói VPN-átjárók legfeljebb 5 DNS-kiszolgáló megadását teszik lehetővé. Ezt konfigurálhatja a hub létrehozási folyamata során, vagy később is módosíthatja. Ehhez keresse meg a virtuális hubot. A **felhasználói VPN (pont – hely)** területen kattintson a configure (Konfigurálás) elemre, és adja meg a DNS-kiszolgáló IP-címét (ek) az **Egyéni DNS-kiszolgálók** szövegmezőben (es).

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="Egyéni DNS" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="download-vpn-profile"></a><a name="download"></a>VPN-profil letöltése

A VPN-profillal konfigurálhatja az ügyfeleket.

1. A virtuális WAN lapján válassza a **felhasználói VPN-konfigurációk**lehetőséget.
2. A lap tetején válassza a **felhasználói VPN-konfiguráció letöltése**lehetőséget. A WAN szintű konfigurációk letöltése beépített Traffic Manager-alapú felhasználói VPN-profilt biztosít. A globális profilokkal vagy a hub-alapú profilokkal kapcsolatos további információkért tekintse meg ezt a [központi profilt](https://docs.microsoft.com/azure/virtual-wan/global-hub-profile).   A feladatátvételi forgatókönyvek egyszerűsített globális profillal.

   Ha valamilyen okból kifolyólag egy központ nem érhető el, a szolgáltatás által biztosított beépített forgalom-kezelés biztosítja a kapcsolódást egy másik hubhoz az Azure-erőforrásokhoz a pont – hely felhasználók számára. A hub-specifikus VPN-konfigurációkat mindig letöltheti az adott hubhoz való navigálással. A **felhasználói VPN (pont – hely)** területen töltse le a virtuális központ **felhasználói VPN-** profilját.

1. Miután befejeződött a fájl létrehozása, kiválaszthatja a letöltésre mutató hivatkozást.
1. A profil fájl segítségével konfigurálja a VPN-ügyfeleket.

### <a name="configure-user-vpn-clients"></a>Felhasználói VPN-ügyfelek konfigurálása

A letöltött profillal konfigurálhatja a távelérésű ügyfeleket. Az eljárás minden operációs rendszer esetén eltérő, kövesse az alábbi utasításokat:

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Töltse le az OpenVPN-ügyfelet a hivatalos webhelyről, majd telepítse.
1. Töltse le az átjáró VPN-profilját. Ezt a Azure Portal a felhasználó VPN-konfigurációk lapján vagy a PowerShell új AzureRmVpnClientConfiguration teheti meg.
1. Csomagolja ki a profilt. Nyissa meg az OpenVPN mappában található vpnconfig.ovpn konfigurációs fájlt a Jegyzettömbben.
1. Töltse ki a pont–hely ügyféltanúsítványra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású nyilvános kulcsával. PEM formátumú tanúsítvány esetén egyszerűen nyissa meg a .cer-fájlt, és másolja a Base64-kódolású kulcsot a tanúsítványfejlécek között. A lépéseket lásd: [Tanúsítvány exportálása a kódolt nyilvános kulcs lekéréséhez.](certificates-point-to-site.md)
1. Töltse ki a titkos kulcsra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású titkos kulcsával. A lépéseket lásd: [titkos kulcs kibontása.](howto-openvpn-clients.md#windows).
1. Ne módosítson semmilyen egyéb mezőt. Az ügyfélbemenet kitöltött konfigurációjával csatlakozhat a VPN-hez.
1. Másolja a vpnconfig.ovpn fájlt a C:\Program Files\OpenVPN\config mappába.
1. Kattintson a jobb gombbal a tálcán található OpenVPN ikonra, majd válassza a **kapcsolat**lehetőséget.

##### <a name="ikev2"></a>IKEv2

1. Válassza ki a Windows rendszerű számítógép architektúrájának megfelelő VPN-ügyfélkonfigurációs fájlokat. 64 bites processzorarchitektúra esetén a „VpnClientSetupAmd64” telepítőcsomagot válassza. 32 bites processzorarchitektúra esetén a „VpnClientSetupX86” telepítőcsomagot válassza.
1. Kattintson duplán a csomagra a telepítéséhez. Ha egy SmartScreen előugró ablak jelenik meg, válassza a **További információ**, majd a **Futtatás amúgy parancsot**.
1. Az ügyfélszámítógépen navigáljon a **hálózati beállítások** elemre, és válassza a **VPN**lehetőséget. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott.
1. Mielőtt megkísérli a csatlakozást, ellenőrizze, hogy telepített-e ügyféltanúsítványt az ügyfélszámítógépen. A natív Azure-tanúsítványhitelesítési típus használata esetén a hitelesítéshez ügyféltanúsítványra van szükség. A tanúsítványok létrehozásával kapcsolatos további információkért lásd: [tanúsítványok létrehozása](certificates-point-to-site.md). Az ügyféltanúsítványok telepítésével kapcsolatos információkért lásd: [ügyféltanúsítvány telepítése](../vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>A virtuális WAN megtekintése

1. Lépjen a virtuális WAN-ra.
1. Az **Áttekintés** oldalon a térképen lévő minden pont egy hubot jelöl.
1. A **hubok és kapcsolatok** szakaszban megtekintheti a hub állapotát, a helyet, a régiót, a VPN-kapcsolat állapotát és a bejövő és kimenő bájtokat.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Az erőforrások eltávolítása

Ha már nincs szükség ezekre az erőforrásokra, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást. A „myResourceGroup” helyére írja be az erőforráscsoport nevét, és futtassa a következő PowerShell-parancsot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.
