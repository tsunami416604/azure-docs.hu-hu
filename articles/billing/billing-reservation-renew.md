---
title: Az Azure-foglalások automatikus megújítása
description: Ismerje meg, hogyan újíthatja meg automatikusan az Azure-foglalásokat, hogy továbbra is jogosult legyen a foglalási kedvezményekre.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: banders
ms.openlocfilehash: c19c6af68bcde753ec9bed990e08aa81eabdd37d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "68679457"
---
# <a name="automatically-renew-reservations"></a>A foglalások automatikus megújítása

A foglalások megújításával automatikusan megvásárolhatja a cserét, ha egy meglévő foglalása lejár. Az automatikus megújítás egyszerű módja a foglalási kedvezmények további igénybevételének. Ezenkívül nem kell részletesen monitoroznia, hogy mikor jár egy foglalása. Az automatikus megújítással elkerülheti a megtakarítási előnyök elvesztését, ami a manuális megújítással járna. A megújítási beállítás alapértelmezés szerint ki van kapcsolva. A megújítási beállítást bármikor engedélyezheti vagy letilthatja egészen a meglévő foglalás lejáratáig.

A foglalás megújítása a meglévő foglalás lejártakor egy új foglalást hoz létre. Nem a meglévő foglalás időtartamát hosszabbítja meg.

Az automatikus megújítást bármikor engedélyezheti. A megújítási díj a meglévő foglalás lejárta előtt 30 nappal válik elérhetővé. Ha a megújítást több mint 30 nappal a foglalás lejárata előtt engedélyezi, a lejárat előtt 30 nappal egy e-mailt küldünk Önnek a megújítási költségek részleteiről. A foglalási ár változhat a megújítási ár és a megújítási idő zárolása között eltelt idő alatt. Ha ez történik, a megújítási költség az alacsonyabb érték lesz a két költség közül. Módosíthatja a foglalási mennyiséget. Ha így tesz, a megújított előfizetés úgy lesz frissítve, hogy a mennyiségi változás időpontjában beállított piaci árakat használja.

A megújítás nem kötelező, és a meglévő előfizetés lejárta előtt bármikor dönthet úgy, hogy nem szeretné megújítani előfizetését.

## <a name="set-up-renewal"></a>Megújítás beállítása

Lépjen az Azure Portal > **Reservations** menüponthoz.

1. Válassza ki a foglalást.
2. Kattintson a **Megújítás** lehetőségre.
3. Válassza az **Új foglalás automatikus megvásárlása lejáratkor** lehetőséget.  
  ![A foglalás megújítását bemutató példa](./media/billing-reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>Ha nem újít meg

Szolgáltatásai tovább működnek a megszokott módon. A foglalás lejárta után a díjak felszámítása a használatalapú fizetéses díjszabás szerint történik.

## <a name="required-renewal-permissions"></a>Szükséges megújítási engedélyek

A következő feltételek szükségesek a foglalás megújításához:

- A meglévő foglalás tulajdonosának kell lennie.
- Az előfizetés tulajdonosának kell lenni, ha a foglalás hatóköre egyetlen előfizetésre vagy erőforráscsoportra terjed ki.
- Közös hatókör esetén az előfizetés tulajdonosának kell lennie.

## <a name="default-renewal-settings"></a>Alapértelmezett megújítási beállítások

Alapértelmezés szerint a megújított foglalás a lejáró foglalás összes tulajdonságát örökli. A megújított foglalás azonos termékváltozattal, régióval, hatókörrel, számlázási előfizetéssel, időtartammal és mennyiséggel fog rendelkezni.

A megtakarítások optimalizálása érdekében azonban frissítheti a megújított foglalásvásárlási mennyiségét.

## <a name="when-the-new-reservation-is-purchased"></a>Mikor lesz az új foglalás megvásárolva?

Az új foglalás megvásárlása akkor történik, amikor a meglévő foglalás lejár. Megpróbáljuk elkerülni a két foglalás közötti késést. A folytonosság biztosítja, hogy költségei kiszámíthatóak legyenek, és továbbra is megkapja a kedvezményeket.

## <a name="changing-parent-reservation-after-setting-renewal"></a>Szülő foglalás módosítása a megújítás beállítása után

Ha a lejáró foglaláson a következő módosítások bármelyikét végrehajtja, a foglalás megújítása vissza lesz vonva:

- Felosztás
- Egyesítés
- A foglalás másik fióknak való átadása
- A foglalás átadása egy WebDirect-előfizetésből egy Nagyvállalati Szerződéssel (EA) rendelkező előfizetésre vagy bármely más vásárlási módszerre
- A regisztráció megújítása

A megújításkor az új foglalás örökli a lejáró foglalás hatókörét és a példány méretrugalmassági beállítását.

## <a name="new-reservation-permissions"></a>Új foglalási engedélyek

Az Azure a lejáró foglalás engedélyeit átmásolja az új foglalásba. Ezenkívül, a foglalásvásárlási előfizetés fiókadminisztrátora hozzáfér az új foglaláshoz.

## <a name="potential-renewal-problems"></a>Lehetséges megújítási problémák

Elképzelhető, hogy az Azure nem dolgozza fel a műveletet, ha:

- A fizetés nem szedhető be
- A megújítás során rendszerhiba lép fel
- A lejáró termékváltozat a megújítás során nem aktív
- A Nagyvállalati Szerződést egy másik Nagyvállalati Szerződéssel újítják meg

E-mailben értesítést kap, ha az előző feltételek bármelyike bekövetkezik, és a megújítás inaktiválódik.

## <a name="renewal-notification"></a>Megújítási értesítés

Az e-maileket a rendszer a vásárlási módszertől függően különböző személyeknek küldi el:

- Nagyvállalati Szerződéssel rendelkező ügyfelek – Az e-maileket a rendszer az EA Portalon beállított értesítési kapcsolattartóknak küldi el.
- Egyéni előfizetéssel rendelkező, használatalapú fizetéses ügyfelek – Az e-maileket a rendszer a fiókadminisztrátorként beállított felhasználóknak küldi.
- Felhőszolgáltató-ügyfelek – Az e-maileket a rendszer a partner értesítési kapcsolattartójának küldi.

## <a name="next-steps"></a>További lépések
- Ha többet szeretne tudni az Azure Reservationsről, tekintse meg a [Mi az az Azure Reservations?](billing-save-compute-costs-reservations.md) szakaszt.
