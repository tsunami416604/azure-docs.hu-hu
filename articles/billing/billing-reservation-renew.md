---
title: Azure-foglalások automatikus megújítása
description: Ismerje meg, hogyan újíthatja meg automatikusan az Azure-foglalásokat a foglalási kedvezmények beszerzésének folytatásához.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: banders
ms.openlocfilehash: c19c6af68bcde753ec9bed990e08aa81eabdd37d
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679457"
---
# <a name="automatically-renew-reservations"></a>Foglalások automatikus megújítása

A foglalások megújításával automatikusan megvásárolhatja a cserét, ha egy meglévő foglalás lejár. Az automatikus megújítás egyszerű módszert kínál a foglalási kedvezmények folytatására. Emellett a foglalás lejáratának alapos figyelését is elvégezheti. Az automatikus megújítás miatt a megtakarítási előnyök elvesztését nem kell manuálisan megújítani. Alapértelmezés szerint a megújítási beállítás ki van kapcsolva. Engedélyezheti vagy letilthatja a megújítási beállítást bármikor, a meglévő foglalás lejáratával.

A foglalás megújítása egy új foglalást hoz létre, ha a meglévő foglalás lejár. Nem terjeszti ki a meglévő foglalás időtartamát.

Az automatikus megújítás bármikor letiltható. A megújítási díj a meglévő foglalás lejárta előtt 30 nappal elérhető. Ha a megújítást több mint 30 nappal a foglalás lejárata előtt engedélyezi, a megújítást elküldő e-mailt a lejárat előtt 30 nappal megújítjuk. A foglalási ár változhat a megújítási ár és a megújítási idő zárolása között. Ha igen, a megújítási költség a két költség alacsonyabb. Módosíthatja a foglalások mennyiségét. Ha így tesz, a megújítást a rendszer a mennyiségi változás időpontjában beállított piaci árak használatára frissíti.

Nincs szükség a megújításra, és a meglévő foglalás lejárta előtt bármikor letilthatja a megújítást.

## <a name="set-up-renewal"></a>Megújítás beállítása

Ugrás Azure Portal > **foglalások**.

1. Válassza ki a foglalást.
2. Kattintson a **megújítás**elemre.
3. **A lejárat után válassza az új foglalás automatikus vásárlása**lehetőséget.  
  ![A foglalás megújítását bemutató példa](./media/billing-reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>Ha nem újítja meg

A szolgáltatások általában továbbra is futnak. Az utólagos elszámolású díjszabás díját a foglalás lejárta után számítjuk fel.

## <a name="required-renewal-permissions"></a>Szükséges megújítási engedélyek

A foglalás megújításához a következő feltételek szükségesek:

- A meglévő foglalás tulajdonosának kell lennie.
- Az előfizetés tulajdonosának kell lennie, ha a foglalás egy adott előfizetéshez vagy erőforráscsoporthoz tartozik.
- Ha megosztott hatókörrel rendelkezik, az előfizetés tulajdonosának kell lennie.

## <a name="default-renewal-settings"></a>Alapértelmezett megújítási beállítások

Alapértelmezés szerint a megújítás a lejáró foglalásból örökli az összes tulajdonságot. A foglalás megújítása esetén az SKU, a régió, a Scope, a számlázási előfizetés, a kifejezés és a mennyiség szerepel.

Azonban a megújítási foglalások vásárlási mennyiségét frissítheti a megtakarítások optimalizálása érdekében.

## <a name="when-the-new-reservation-is-purchased"></a>Az új foglalás megvásárlásakor

Ha a meglévő foglalás lejár, új foglalást kell megvásárolnia. Megpróbáljuk elkerülni a két foglalás közötti késleltetést. A folytonosság biztosítja, hogy a költségek kiszámíthatóak, és továbbra is kedvezményt kap.

## <a name="changing-parent-reservation-after-setting-renewal"></a>Szülői foglalás módosítása a megújítás beállítása után

Ha a következő módosítások valamelyikét hajtja végre a lejáró foglalásban, a foglalás megújítása megszakad:

- Megosztott
- Egyesítés
- A foglalás áthelyezése egyik fiókból a másikba
- A foglalás átadása egy webdirect-előfizetésből egy nagyvállalati szerződés (EA) előfizetésre vagy bármely más vásárlási módszerre
- A regisztráció megújítása

Az új foglalás a Megújításkor örökli a hatókör és a példány méretének rugalmassági beállítását a lejárati foglalás során.

## <a name="new-reservation-permissions"></a>Új foglalási engedélyek

Az Azure a lejárati foglalásból az új foglalásba másolja az engedélyeket. Emellett a foglalási vásárláshoz tartozó előfizetés-fiók rendszergazdája hozzáférhet az új foglaláshoz.

## <a name="potential-renewal-problems"></a>Lehetséges megújítási problémák

Az Azure nem dolgozza fel a megújítást, ha:

- A fizetés nem gyűjthető
- Rendszerhiba történik A megújítás során
- A lejáró SKU nem aktív a megújítás során
- Az EA-t egy másik nagyvállalati szerződéssel újítják meg

E-mailben értesítést kap, ha az előző feltételek bármelyike bekövetkezik, és a megújítás inaktiválva van.

## <a name="renewal-notification"></a>Megújítási értesítés

Az e-maileket a rendszer a vásárlási módszertől függően különböző személyeknek küldi el:

- Nagyvállalati szerződéssel rendelkező ügyfelek – a rendszer elküldi az e-maileket az EA portálon beállított értesítési partnereknek.
- Egyéni előfizetéssel rendelkező ügyfelek – utólagos elszámolású díjszabás – e-maileket a rendszer a fiók rendszergazdájaként beállított felhasználóknak küldi el.
- Felhőalapú megoldás-szolgáltatói ügyfelek – a rendszer e-maileket küld a partner értesítési kapcsolattartójának.

## <a name="next-steps"></a>További lépések
- További információ a Azure Reservationsről: [Mi a Azure Reservations?](billing-save-compute-costs-reservations.md)
