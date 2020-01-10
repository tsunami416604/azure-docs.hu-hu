---
title: Peer ASN társítása az Azure-előfizetéshez a portál használatával
titleSuffix: Azure
description: Peer ASN társítása az Azure-előfizetéshez a portál használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d3737be5a3186774f230aef9d932464a27a764f4
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775640"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>Peer ASN társítása az Azure-előfizetéshez a portál használatával

A kérések elküldése előtt először társítsa az ASN-t az Azure-előfizetéshez az alábbi lépések segítségével.

Ha szeretné, a [PowerShell](howto-subscription-association-powershell.md)használatával is elvégezheti ezt az útmutatót.

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>PeerAsn létrehozása az ASN-nek az Azure-előfizetéshez való hozzárendeléséhez

### <a name="sign-in-to-the-portal"></a>Bejelentkezés a portálra
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-peerasn"></a>PeerAsn létrehozása
Létrehozhat egy új PeerAsn-erőforrást, amely az Azure-előfizetéssel rendelkező autonóm rendszerbeli szám (ASN) társítására használható. Egy előfizetéshez több ASN is hozzárendelhet, ehhez létre kell hoznia egy **PeerAsn** a társítandó ASN-hez.

1. Kattintson az **erőforrás létrehozása** > **az összes**megjelenítése elemre.

    > [!div class="mx-imgBorder"]
    > ![keresési PeerAsn](./media/peerasn-seeall.png)

1. Keresse meg a *PeerAsn* a keresőmezőbe, és nyomja le az *ENTER billentyűt* a billentyűzeten. Az eredmények között kattintson a **PeerAsn** erőforrás elemre.

    > [!div class="mx-imgBorder"]
    > PeerAsn ![elindítása](./media/peerasn-launch.png)

1. A **PeerAsn** elindítása után kattintson a **Létrehozás**gombra.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn létrehozása](./media/peerasn-create.png)

1. A társ-visszavonási ASN-oldal **társítása** az **alapok** lapon töltse ki a mezőket az alább látható módon.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn alapjai lap](./media/peerasn-basics-tab.png)

    * A **név** megegyezik az erőforrás nevével, és tetszőlegesen választhatja ki.  
    * Válassza ki azt az **előfizetést** , amelyhez az ASN-t hozzá kell rendelni.
    * A **társ neve** megegyezik a vállalat nevével, és a lehető leghamarabb meg kell felelnie a PeeringDB-profilnak. Vegye figyelembe, hogy az érték csak a-z, A-Z és szóköz karaktereket támogatja
    * Adja meg az ASN-t a **társ ASN** mezőben.
    * Kattintson a **Create New (új létrehozása** ) elemre, és adja meg a hálózati operatív központ (Noc) **E-mail címét** és **telefonszámát**
1. Ezután kattintson a **felülvizsgálat + létrehozás** lehetőségre, és figyelje meg, hogy a portál a beírt adatok alapszintű érvényesítését futtatja. Ez a felső menüszalagon jelenik meg, a *végső ellenőrzés futtatásával..* .

    > [!div class="mx-imgBorder"]
    > ![PeerAsn áttekintése lap](./media/peerasn-review-tab-validation.png)

1. Miután a menüszalagon lévő üzenet bekapcsolta az *érvényesítést*, ellenőrizze az adatokat, és küldje el a kérést a **Létrehozás**gombra kattintva. Ha az ellenőrzés nem megy át, kattintson az **előző** gombra, és ismételje meg a fenti lépéseket a kérelem módosításához, és győződjön meg arról, hogy a megadott értékek nincsenek hibák.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn áttekintése lap](./media/peerasn-review-tab.png)

1. A kérelem elküldése után várjon, amíg a telepítés befejeződött. Ha a telepítés sikertelen, forduljon a [Microsoft-partneri](mailto:peering@microsoft.com)kapcsolathoz. A sikeres üzembe helyezés az alábbi módon fog megjelenni.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn sikeres](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>PeerAsn állapotának megtekintése
Ha a PeerAsn-erőforrás üzembe helyezése sikeresen megtörtént, meg kell várnia, hogy a Microsoft jóváhagyja a társítási kérést. Jóváhagyás esetén akár 12 órát is igénybe vehet. A jóváhagyást követően a rendszer értesítést küld a fenti szakaszban megadott e-mail-címre.

> [!IMPORTANT]
> Várjon, amíg a ValidationState bekapcsolja a "jóváhagyva" beállítást, mielőtt elküldené a kérést. Ez a jóváhagyás akár 12 órát is igénybe vehet.

## <a name="modify-peerasn"></a>PeerAsn módosítása
A PeerAsn módosítása jelenleg nem támogatott. Ha módosítania kell, forduljon a [Microsoft-partneri](mailto:peering@microsoft.com)kapcsolathoz.

## <a name="delete-peerasn"></a>PeerAsn törlése
A PeerAsn törlése jelenleg nem támogatott. Ha törölnie kell a PeerAsn, forduljon a [Microsoft-partneri](mailto:peering@microsoft.com)kapcsolathoz.

## <a name="next-steps"></a>Következő lépések

* [Közvetlen társ létrehozása vagy módosítása a portál használatával](howto-direct-portal.md)
* [Örökölt közvetlen társítás átalakítása Azure-erőforrásra a portál használatával](howto-legacy-direct-portal.md)
* [Exchange-társ létrehozása vagy módosítása a portál használatával](howto-exchange-portal.md)
* [Örökölt Exchange-társ átalakítása az Azure-erőforrásra a portál használatával](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>További források

További információért látogasson el az internetes kereséssel kapcsolatos [Gyakori kérdések](faqs.md) oldalra.