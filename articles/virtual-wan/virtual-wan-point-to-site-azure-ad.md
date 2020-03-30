---
title: 'Azure AD-hitelesítés konfigurálása felhasználói VPN-kapcsolathoz: Virtual WAN'
description: Ismerje meg, hogyan konfigurálhatja az Azure Active Directory-hitelesítést a felhasználói VPN-hez.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: alzam
ms.openlocfilehash: 703b832d58f2374eac131cfd380ba27f2c890618
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059487"
---
# <a name="configure-azure-active-directory-authentication-for-user-vpn"></a>Az Azure Active Directory-hitelesítés konfigurálása felhasználói VPN-hez

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure AD-hitelesítést a virtuális WAN felhasználói VPN-hez, hogy openVPN VPN-kapcsolaton keresztül csatlakozzon az erőforrásokhoz az Azure-ban. Az Azure Active Directory-hitelesítés csak openvpn protokollt használó átjárók és Windows-ügyfelek számára érhető el.

Ehhez a kapcsolattípushoz konfigurálni kell egy ügyfelet az ügyfélszámítógépen. A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintését](virtual-wan-about.md).

Ebben a cikkben az alábbiakkal ismerkedhet meg:

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

* Van egy virtuális hálózat, amelyhez csatlakozni szeretne. Ellenőrizze, hogy a helyszíni hálózatok egyik alhálózata sem fedi-e át azokat a virtuális hálózatokat, amelyekhez csatlakozni szeretne. Virtuális hálózat létrehozásához az Azure Portalon tekintse meg a [gyorsútmutatót.](../virtual-network/quick-create-portal.md)

* A virtuális hálózat nem rendelkezik virtuális hálózati átjárókkal. Ha a virtuális hálózat rendelkezik átjáróval (VPN vagy ExpressRoute), el kell távolítania az összes átjárót. Ez a konfiguráció megköveteli, hogy a virtuális hálózatok csatlakoznak helyett, a Virtual WAN hub átjáró.

* Igényeljen egy IP-címtartományt az elosztó régiójában. A hub egy virtuális hálózat, amelyet a Virtual WAN hoz létre és használ. A hubhoz megadott címtartomány nem fedheti át a meglévő virtuális hálózatokat, amelyekhez csatlakozik. Emellett nem lehet átfedésben azokkal a címtartományokkal sem, amelyekhez a helyszínen csatlakozik. Ha nem ismeri a helyszíni hálózati konfigurációban található IP-címtartományokat, egyeztessen valakivel, aki meg tudja adni ezeket az adatokat.

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Virtuális WAN létrehozása

Egy böngészőből lépjen az [Azure Portalra](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

1. Nyissa meg a Virtual WAN lapot. A portálon kattintson az **+Erőforrás létrehozása** gombra. Írja be a **Virtual WAN** parancsot a keresőmezőbe, és válassza az Enter lehetőséget.
2. Válassza ki a **Virtual WAN** az eredmények közül. A Virtual WAN lapon kattintson a **Létrehozás** gombra a WAN létrehozása lap megnyitásához.
3. A **WAN létrehozása** lapon az **Alapok** lapon töltse ki a következő mezőket:

   ![Virtuális WAN](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **Előfizetés** – Válassza ki a használni kívánt előfizetést.
   * **Erőforráscsoport** – Új létrehozása vagy meglévő használata.
   * **Erőforráscsoport helye** – Erőforrás helyének kiválasztása a legördülő menüből. A WAN egy globális erőforrás, és nem egy adott régióhoz tartozik. Mindazonáltal mégis ki kell választania egy régiót, hogy könnyebben kezelhesse és megtalálhassa a létrehozott WAN-erőforrást.
   * **Név** – Írja be a WAN-nak hívni kívánt nevet.
   * **Típus:** Standard. Ha alapwanst hoz létre, csak alapszintű elosztót hozhat létre. Az alapvető elosztók csak vpn-helyek közötti kapcsolatra képesek.
4. Miután befejezte a mezők kitöltését, válassza a **Véleményezés +Létrehozás**gombot.
5. Az érvényesítési fázisok után válassza a **Létrehozás** lehetőséget a virtuális WAN létrehozásához.

## <a name="create-an-empty-virtual-hub"></a><a name="site"></a>Üres virtuális elosztó létrehozása

1. A virtuális WAN csoportban válassza a Hubs (Hubs) lehetőséget, és kattintson **az +Új központ**gombra.

   ![új hely](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. A virtuális központ létrehozása lapon töltse ki a következő mezőket.

   **Régió** – Válassza ki azt a régiót, amelyben a virtuális központot telepíteni szeretné.

   **Név** – Adja meg a virtuális központ meghívni kívánt nevét.

   **Hub privát címtér** – A hub címtartománya CIDR jelölésben.

   ![új hely](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. Kattintson az **Áttekintés + létrehozás** elemre.
4. Az **érvényesítési átadott** lapon kattintson a **Create gombra.**

## <a name="create-a-new-p2s-configuration"></a><a name="site"></a>Új P2S-konfiguráció létrehozása

A pont–hely konfiguráció határozza meg a távoli ügyfelek csatlakoztatására vonatkozó paramétereket.

1. A virtuális WAN csoportban válassza a **Felhasználói VPN-konfigurációk lehetőséget.**

   ![új config](media/virtual-wan-point-to-site-azure-ad/aadportal1.jpg)

2. kattintson **a +Felhasználói VPN-konfiguráció létrehozása gombra.**

   ![új config](media/virtual-wan-point-to-site-azure-ad/aadportal2.jpg)

3. Adja meg az adatokat, és kattintson a **Létrehozás gombra.**

   ![új config](media/virtual-wan-point-to-site-azure-ad/aadportal3.jpg)

## <a name="edit-hub-assignment"></a><a name="hub"></a>Hubhozzárendelés szerkesztése

1. Keresse meg a **Hubs panelt** a virtuális WAN alatt.
2. Válassza ki azt a hubot, amelyhez a VPN-kiszolgáló konfigurációját társítani szeretné, majd kattintson a három pontra (...).

   ![új hely](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Kattintson **a Virtuális központ szerkesztése gombra.**
4. Jelölje be a **Pont-hely átjáró felvétele** jelölőnégyzetet, és válassza ki a kívánt **átjáróméretezési egységet.**

   ![új hely](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Adja meg azt a **címkészletet,** amelyből a VPN-ügyfelek IP-címeket kapnak.
6. Kattintson a **Megerősítés** gombra.
7. A művelet akár 30 percet is igénybe vehet.

## <a name="download-vpn-profile"></a><a name="device"></a>VPN-profil letöltése

A VPN-profillal konfigurálhatja az ügyfeleket.

1. A virtuális WAN lapján kattintson a **Felhasználói VPN-konfigurációk**elemre.
2. A lap tetején kattintson a **Felhasználói VPN-konfiguráció letöltése gombra.**
3. Miután befejeződött a fájl létrehozása, a hivatkozásra kattintva letöltheti.
4. A profilfájl segítségével konfigurálhatja a VPN-ügyfeleket.

## <a name="configure-user-vpn-clients"></a>Felhasználói VPN-ügyfelek konfigurálása

A csatlakozáshoz le kell töltenie az Azure VPN-ügyfelet, és importálnia kell az előző lépésekben letöltött VPN-ügyfélprofilt minden olyan számítógépen, amely csatlakozni szeretne a virtuális hálózathoz.

> [!NOTE]
> Az Azure AD-hitelesítés csak&reg; OpenVPN protokollkapcsolatok esetén támogatott.
>

#### <a name="to-download-the-azure-vpn-client"></a>Az Azure VPN-ügyfél letöltése

Ezen a [hivatkozáson](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) keresztül letöltheti az Azure VPN-ügyfelet.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Ügyfélprofil importálása

1. A lapon válassza az **Importálás**lehetőséget.

    ![Importálása](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Tallózással keresse meg a profil XML-fájlját, és jelölje ki. Ha a fájl ki van jelölve, válassza a **Megnyitás**gombot.

    ![Importálása](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Adja meg a profil nevét, és válassza a **Mentés gombot.**

    ![Importálása](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. A VPN-hez való csatlakozáshoz válassza a **Csatlakozás** lehetőséget.

    ![Importálása](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Miután csatlakozott, az ikon zöldre vált, és azt mondja **Connected**.

    ![Importálása](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Ügyfélprofil törlése

1. Jelölje ki a törölni kívánt ügyfélprofil melletti három pontot (...). Ezután válassza **az Eltávolítás lehetőséget.**

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. A törléshez válassza az **Eltávolítás** gombot.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Csatlakozási problémák diagnosztizálása

1. A csatlakozási problémák diagnosztizálásához használja a **Diagnosztizálás** eszközt. Válassza ki a három pontot (...) a diagnosztizálni kívánt VPN-kapcsolat mellett, hogy felfedje a menüt. Ezután válassza **a Diagnosztizálás lehetőséget.**

    ![Diagnosztizálni](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. A **Kapcsolat tulajdonságai** lapon válassza a **Diagnosztika futtatása**lehetőséget.

    ![Diagnosztizálni](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Jelentkezzen be a hitelesítő adataival.

    ![Diagnosztizálni](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Tekintse meg a diagnosztikai eredményeket.

    ![Diagnosztizálni](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>A virtuális WAN megtekintése

1. Lépjen a virtuális WAN-ra.
2. Az Áttekintés lapon a térképen látható pontok mindegyike egy elosztót jelöl.
3. Az elosztók és kapcsolatok szakaszában láthatja az elosztók állapotát, helyét, régióját, VPN-kapcsolati állapotát, valamint a bájtban kifejezett be- és kimenő forgalmát.


## <a name="clean-up-resources"></a><a name="cleanup"></a>Az erőforrások eltávolítása

Ha már nincs szükség ezekre az erőforrásokra, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást. A „myResourceGroup” helyére írja be az erőforráscsoport nevét, és futtassa a következő PowerShell-parancsot:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.
