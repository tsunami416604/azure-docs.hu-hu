---
title: Társ ASN társítása Azure-előfizetéshez a portál használatával
titleSuffix: Azure
description: Társ ASN társítása Azure-előfizetéshez a portál használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: cee548aff49cd5e4a57eed994b8ade2d157c6313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912151"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>Társ ASN társítása Azure-előfizetéshez a portál használatával

Mielőtt társviszony-létesítési kérelmet küld, először társítsa az ASN-t az Azure-előfizetéssel az alábbi lépések végrehajtásával.

Ha szeretné, ezt az útmutatót a PowerShell használatával is [elvégezheti.](howto-subscription-association-powershell.md)

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>PeerAsn létrehozása az ASN azure-előfizetéssel való társításához

### <a name="sign-in-to-the-portal"></a>Bejelentkezés a portálra
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>Regisztráció társviszony-létesítési erőforrás-szolgáltatóhoz
Regisztráljon társviszony-létesítési erőforrás-szolgáltatóaz előfizetésben az alábbi lépéseket követve. Ha ezt nem hajtja végre, akkor a társviszony-létesítés beállításához szükséges Azure-erőforrások nem érhetők el.

1. Kattintson **az Előfizetések** elemre a portál bal felső sarkában. Ha nem látja, kattintson a **További szolgáltatások gombra,** és keresse meg.

    > [!div class="mx-imgBorder"]
    > ![Előfizetések megnyitása](./media/rp-subscriptions-open.png)

1. Kattintson a társviszony-létesítéshez használni kívánt előfizetésre.

    > [!div class="mx-imgBorder"]
    > ![Előfizetés indítása](./media/rp-subscriptions-launch.png)

1. Az előfizetés megnyitása után a bal oldalon kattintson az **Erőforrás-szolgáltatók**elemre. Ezután a jobb oldali ablaktáblában keressen *társviszony-létesítést* a keresőablakban, vagy a görgetősáv segítségével keresse meg a **Microsoft.Peering elemet,** és tekintse meg az **Állapot**lehetőséget. Ha az állapot ***regisztrálva***van, hagyja ki az alábbi lépéseket, és folytassa a **PeerAsn létrehozása**című szakaszt. Ha az állapot ***nincs regisztrálva,*** válassza a **Microsoft.Peering** lehetőséget, és kattintson a **Regisztráció gombra.**

    > [!div class="mx-imgBorder"]
    > ![Regisztráció kezdete](./media/rp-register-start.png)

1. Vegye figyelembe, hogy az állapot a Regisztráció állapotára ***változik.***

    > [!div class="mx-imgBorder"]
    > ![Folyamatban lévő regisztráció](./media/rp-register-progress.png)

1. Várjon egy percet, vagy úgy, hogy a teljes regisztráció. Ezután kattintson a **Frissítés gombra,** és ellenőrizze, hogy az állapot ***regisztrálva van-e.***

    > [!div class="mx-imgBorder"]
    > ![Regisztráció befejeződött](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>Társtárs-asn létrehozása
Létrehozhat egy új PeerAsn-erőforrást egy autonóm rendszerszám (ASN) Azure-előfizetéssel való társításához. Több ASN-t társíthat egy előfizetéshez, ha létrehoz egy **Társ- és** AsN-t, amelyet társítania kell.

1. Kattintson **az Erőforrás** > létrehozása**Az összes megtekintése gombra.**

    > [!div class="mx-imgBorder"]
    > ![Társtárs-keresés](./media/peerasn-seeall.png)

1. Keressen rá a *PeerAsn* kifejezésre a keresőmezőben, és nyomja le az *Enter* billentyűt a billentyűzeten. Az eredmények, kattintson **a PeerAsn** erőforrás.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn indítása](./media/peerasn-launch.png)

1. A **PeerAsn** elindítása után kattintson a **Létrehozás gombra.**

    > [!div class="mx-imgBorder"]
    > ![Társtárs-asn létrehozása](./media/peerasn-create.png)

1. A **TársASN-társítás** lapján az **Alapok** lapon töltse ki a mezőket az alábbi módon.

    > [!div class="mx-imgBorder"]
    > ![A PeerAsn alapjai lap](./media/peerasn-basics-tab.png)

    * **A név** az erőforrás nevének felel meg, és bármi lehet, amit csak választhat.  
    * Válassza ki azt az **előfizetést,** amelyhez az ASN-t társítania kell.
    * **A társnév** megfelel a vállalat nevének, és a lehető legközelebb kell lennie a PeeringDB-profilhoz. Ne feledje, hogy az érték csak az a-z, a-z és szóköz karaktereket támogatja
    * Írja be az ASN-t a **Társ ASN** mezőjébe.
    * Kattintson az **Új létrehozása** gombra, és adja meg **az e-MAIL CÍMET** és a **TELEFONSZÁMOT** a Hálózati Műveleti Központhoz (NOC)
1. Ezután kattintson **a Review + create** and observe that portal runs basic validation of the information you entered. Ez egy szalagon jelenik meg a tetején, mint *futás végső érvényesítés...*.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn-ellenőrzés lap](./media/peerasn-review-tab-validation.png)

1. Miután a menüszalagüzenet *átvált,* ellenőrizze az adatokat, és küldje el a kérelmet a **Létrehozás**gombra kattintva. Ha az érvényesítés nem felel meg, akkor kattintson az **Előző gombra,** és ismételje meg a fenti lépéseket a kérés módosításához, és győződjön meg arról, hogy a megadott értékek nem tartalmaznak hibákat.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn-ellenőrzés lap](./media/peerasn-review-tab.png)

1. A kérelem elküldése után várja meg, amíg befejeződik a központi telepítés. Ha a telepítés sikertelen, forduljon [a Microsoft társviszony-létesítési](mailto:peering@microsoft.com). A sikeres telepítés az alábbiak szerint jelenik meg.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn sikeres](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>Társtárs-asszn állapotának megtekintése
A PeerAsn-erőforrás sikeres üzembe helyezése után meg kell várnia, amíg a Microsoft jóváhagyja a társítási kérelmet. A jóváhagyás akár 12 órát is igénybe vehet. A jóváhagyást követően értesítést kap a fenti szakaszban megadott e-mail címre.

> [!IMPORTANT]
> Várakozás, amíg a ValidationState bekapcsolja a "Jóváhagyva" a társviszony-létesítési kérelem elküldése előtt. A jóváhagyás akár 12 órát is igénybe vehet.

## <a name="modify-peerasn"></a>Társtárs-asszn módosítása
A PeerAsn módosítása jelenleg nem támogatott. Ha módosítani kell, forduljon a [Microsoft társviszony-létesítési](mailto:peering@microsoft.com).

## <a name="delete-peerasn"></a>Társtárs-társítás törlése
A Társtársiasn törlése jelenleg nem támogatott. Ha törölnie kell a Társtársiasnt, forduljon a [Microsoft társviszony-létesítési](mailto:peering@microsoft.com).

## <a name="next-steps"></a>További lépések

* [Közvetlen társviszony létesítése vagy módosítása a portál használatával](howto-direct-portal.md)
* [Örökölt közvetlen társviszony konvertálása Azure-erőforrássá a portál használatával](howto-legacy-direct-portal.md)
* [Exchange-társviszony-létesítés létrehozása vagy módosítása a portál használatával](howto-exchange-portal.md)
* [Örökölt Exchange-társviszony konvertálása Azure-erőforrássá a portál használatával](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>További források

További információkért látogasson el [az internetes társviszony-létesítési GYIK-be](faqs.md)