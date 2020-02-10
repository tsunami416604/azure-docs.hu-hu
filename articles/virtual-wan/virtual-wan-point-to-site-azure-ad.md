---
title: Azure AD-hitelesítés konfigurálása pont – hely kapcsolathoz az Azure-hoz | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhatja Azure Active Directory hitelesítését a felhasználói VPN-hez.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: alzam
ms.openlocfilehash: cad502dc759a4d481f3071a8a27ddeca79fbc18c
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2020
ms.locfileid: "77108318"
---
# <a name="tutorial-create-a-user-vpn-connection-by-using-azure-virtual-wan"></a>Oktatóanyag: felhasználói VPN-kapcsolat létrehozása az Azure Virtual WAN használatával

Ez az oktatóanyag bemutatja, hogyan konfigurálhatja az Azure AD-hitelesítést a virtuális WAN-beli felhasználói VPN-hez az Azure-beli erőforrásokhoz való csatlakozáshoz egy OpenVPN VPN-kapcsolaton keresztül. Azure Active Directory hitelesítés csak az OpenVPN protokollt használó átjárók és a Windows rendszerű ügyfelek esetében érhető el.

Ehhez a kapcsolattípushoz konfigurálni kell egy ügyfelet az ügyfélszámítógépen. A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintését](virtual-wan-about.md).

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

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

## <a name="before-you-begin"></a>Előkészületek

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

   ![Virtuális WAN](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **Előfizetés** – Válassza ki a használni kívánt előfizetést.
   * **Erőforráscsoport** – új létrehozása vagy meglévő használata.
   * **Erőforráscsoport helye** – válasszon ki egy erőforrás-helyet a legördülő listából. A WAN egy globális erőforrás, és nem egy adott régióhoz tartozik. Mindazonáltal mégis ki kell választania egy régiót, hogy könnyebben kezelhesse és megtalálhassa a létrehozott WAN-erőforrást.
   * **Név** – írja be a WAN-híváshoz használni kívánt nevet.
   * **Írja be a következőt:** Standard. Ha alapszintű WAN-t hoz létre, akkor csak egy alapszintű hubot hozhat létre. Az alapszintű hubok csak a VPN-helyek közötti kapcsolatra képesek.
4. Miután befejezte a mezők kitöltését, válassza a **felülvizsgálat + létrehozás**lehetőséget.
5. Az ellenőrzés után válassza a **Létrehozás** lehetőséget a virtuális WAN létrehozásához.

## <a name="site"></a>Üres virtuális központ létrehozása

1. A virtuális WAN területen válassza a hubok lehetőséget, majd kattintson az **+ új hub**elemre.

   ![új hely](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. A virtuális központ létrehozása lapon töltse ki a következő mezőket.

   **Régió** – válassza ki azt a régiót, amelyben a virtuális hubot telepíteni szeretné.

   **Név** – adja meg a virtuális központ meghívásához használni kívánt nevet.

   **Hub magánhálózati címtartomány** – a központ CIDR jelölése.

   ![új hely](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. Kattintson az **Áttekintés + létrehozás** elemre.
4. Az **átadott érvényesítés** lapon kattintson a **Létrehozás**gombra.

## <a name="site"></a>Új P2S-konfiguráció létrehozása

A pont–hely konfiguráció határozza meg a távoli ügyfelek csatlakoztatására vonatkozó paramétereket.

1. A virtuális WAN területen válassza a **felhasználói VPN-konfigurációk**lehetőséget.

   ![Új konfiguráció](media/virtual-wan-point-to-site-azure-ad/aadportal1.jpg)

2. kattintson a **+ felhasználói VPN-konfiguráció létrehozása**lehetőségre.

   ![Új konfiguráció](media/virtual-wan-point-to-site-azure-ad/aadportal2.jpg)

3. Adja meg az adatokat, és kattintson a **Létrehozás** gombra.

   ![Új konfiguráció](media/virtual-wan-point-to-site-azure-ad/aadportal3.jpg)

## <a name="hub"></a>Hub-hozzárendelés szerkesztése

1. Navigáljon a virtuális WAN alatt lévő **hubok** panelre.
2. Válassza ki azt a hubot, amelyhez hozzá szeretné rendelni a VPN-kiszolgáló konfigurációját, majd kattintson a három pontra (...).

   ![új hely](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Kattintson a **virtuális központ szerkesztése**elemre.
4. Jelölje be a **pont – hely átjáró belefoglalása** jelölőnégyzetet, és válassza ki a kívánt **átjáró-méretezési egységet** .

   ![új hely](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Adja meg azt a **címkészletet** , amelyből a VPN-ügyfelek IP-címeket rendelnek.
6. Kattintson a **Megerősítés** gombra.
7. A művelet végrehajtása akár 30 percet is igénybe vehet.

## <a name="device"></a>VPN-profil letöltése

A VPN-profillal konfigurálhatja az ügyfeleket.

1. A virtuális WAN lapján kattintson a **felhasználói VPN-konfigurációk**elemre.
2. A lap tetején kattintson a **felhasználói VPN-konfiguráció letöltése**elemre.
3. Miután befejeződött a fájl létrehozása, a hivatkozásra kattintva letöltheti.
4. A profil fájl segítségével konfigurálja a VPN-ügyfeleket.

## <a name="configure-user-vpn-clients"></a>Felhasználói VPN-ügyfelek konfigurálása

A csatlakozáshoz le kell töltenie az Azure VPN-ügyfelet (előzetes verzió), és importálnia kell az előző lépésekben letöltött VPN-ügyféloldali profilt minden olyan számítógépen, amely csatlakozni szeretne a VNet.

> [!NOTE]
> Az Azure AD-hitelesítés csak az OpenVPN® protokoll-kapcsolatok esetén támogatott.
>

#### <a name="to-download-the-azure-vpn-client"></a>Az Azure VPN-ügyfél letöltése

Ezzel a [hivatkozással](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) töltheti le az Azure VPN-ügyfelet (előzetes verzió).

#### <a name="import"></a>Ügyféloldali profil importálása

1. A lapon válassza az **Importálás**lehetőséget.

    ![importálása](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Keresse meg a profil XML-fájlját, és jelölje ki. A fájl kijelölése után válassza a **Megnyitás**lehetőséget.

    ![importálása](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Adja meg a profil nevét, majd válassza a **Mentés**lehetőséget.

    ![importálása](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Válassza a **Csatlakozás** lehetőséget a VPN-hez való csatlakozáshoz.

    ![importálása](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. A csatlakozás után az ikon zöldre vált, és a rendszer a **csatlakozást**.

    ![importálása](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>Ügyféloldali profil törlése

1. Válassza a törölni kívánt ügyféloldali profil melletti három pontot (...). Ezután válassza az **Eltávolítás**lehetőséget.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Válassza az **Eltávolítás** elemet a törléshez.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>Kapcsolatok problémáinak diagnosztizálása

1. A kapcsolódási problémák diagnosztizálásához használhatja a **Diagnosztizálás** eszközt. Válassza ki a használni kívánt VPN-kapcsolat melletti három pontot (...) a menü megjelenítéséhez. Ezután válassza a **Diagnosztizálás**lehetőséget.

    ![diagnosztizálása](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. A **kapcsolatok tulajdonságai** lapon válassza a **diagnosztika futtatása**lehetőséget.

    ![diagnosztizálása](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Jelentkezzen be a hitelesítő adataival.

    ![diagnosztizálása](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. A diagnosztika eredményeinek megtekintése.

    ![diagnosztizálása](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

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
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Következő lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.
