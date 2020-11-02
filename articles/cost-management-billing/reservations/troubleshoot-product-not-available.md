---
title: Nem elérhető Azure foglalási típussal kapcsolatos problémák elhárítása
description: Ez a cikk segítséget nyújt az Azure Portalon nem elérhetőként megjelenő fenntartott példányokkal kapcsolatos problémák megértéséhez és elhárításához.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/27/2020
ms.openlocfilehash: 8575d9d86d8e720122a295cf92fa571ef33d5b4c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92798190"
---
# <a name="troubleshoot-reservation-type-not-available"></a>Nem elérhető foglalási típussal kapcsolatos problémák elhárítása

Ez a cikk segítséget nyújt az Azure Portalon nem elérhetőként megjelenő fenntartott példányokkal kapcsolatos problémák megértéséhez és elhárításához.

## <a name="symptoms"></a>Hibajelenségek

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/), és lépjen a **Foglalások** területre.
2. Válassza a **+ Hozzáadás** elemet, majd válasszon ki egy terméket.
3. Válassza a **Minden termék** lapot.
4. Válasszon ki egy terméket a terméklistából. Az alábbi üzenetek valamelyikét láthatja:
    - `Product unavailable for the selected subscription or region. Contact support.`  
        :::image type="content" source="./media/troubleshoot-product-not-available/product-unavailable-message.png" alt-text="Példa arra az üzenetre, amely azt jelzi, hogy a termék nem érhető el a kiválasztott előfizetés vagy régió esetében" lightbox="./media/troubleshoot-product-not-available/product-unavailable-message.png" :::
    - `The selected subscription does not have enough core quota remaining to purchase this product. Request quota increase`  
        :::image type="content" source="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" alt-text="Példa arra az üzenetre, amely azt jelzi, hogy a termék nem érhető el a kiválasztott előfizetés vagy régió esetében" lightbox="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" :::

## <a name="cause"></a>Ok

Egyes foglalások nem vásárolhatók meg, mert:

- Egyes fenntartottpéldány-termékek nem vásárolhatók meg minden régióban
- Az előfizetésre kvótakorlátozás érvényes

### <a name="cause-1"></a>1\. ok

Nem mindegyik fenntartottpéldány-termék vásárolható meg. Az Azure az ügyfelek számára biztosított pénzügyi kedvezmények nyújtásával járó költségek alapján dönt arról, hogy egyes termékek megvásárolhatók-e vagy sem. Más esetekben az Azure az adott adatközpontok kapacitásfeltételei miatt nem kínál bizonyos termékeket. Emellett előfordulhat, hogy az egyes Azure-termékek fejlesztéséért felelős csapatok nem engedélyezik bizonyos termékek megvásárlását, mert ki szeretnének vezetni egy régebbi terméket.

Más esetekben az Azure kapacitáskorlátozásokat vezet be különféle termékekre vonatkozóan az egyes régiókban az adott erőforrások iránt jelentkező kereslet alapján. Ilyen korlátozást vezethet be például akkor, ha a kereslet miatt egy adott virtuálisgép-méret elfogyott az adatközpontban. Ebben az esetben az Azure nem tudja garantálni a kapacitást azon ügyfelek számára, akik az adott régióban vásároltak foglalást a kérdéses méretre vonatkozóan. Végül vannak olyan termékek, amelyek különféle okok miatt egyedinek számítanak. Ezek a termékek csak kis számú, kiválasztott ügyfél számára érhetők el.

Ha nem vásárolhatók meg, akkor miért jelennek meg mégis egyes foglalások az Azure Portalon? Azért, mert a felhasználók támogatási kéréseket hoznak létre, jelezve, hogy nem találják a kívánt termékeket. Az Azure Reservations fejlesztői csapata úgy látta, hogy kevesebb támogatási kéréssel jár az, ha az összes termék megjelenik az esetleges `Product unavailable` üzenettel, mintha nem lennének láthatók.

### <a name="cause-2"></a>2\. ok

Az előfizetésre kvótakorlátozás érvényes. Az előfizetések esetében korlátozva van a használható processzormagok száma. A korlátozás bizonyos fenntartottpéldány-termékekre is érvényes, különösen a virtuális gépekre. Az Azure biztosítani szeretné, hogy a fenntartott példányt vásárló személyek használni is tudják a terméket. Az Azure egyszerű, futólagos ellenőrzést végez az Azure Portalon annak meghatározására, hogy rendelkezik-e az ügyfél az előfizetésében elegendő szabad maggal a virtuális gép üzembe helyezéséhez, és valóban előnnyel jár-e számára a foglalás megvásárlása.

Az ellenőrzés, amely lehetővé teszi egy adott terméknek a kosárhoz való hozzáadását és a foglalás megvásárlását, egyszerű. Az Azure felméri az előfizetés számára elérhető processzormagok teljes számát, és ellenőrzi, hogy ez a szám nagyobb-e a kiválasztott tételhez tartozó magok számánál.

Az Azure nem ellenőrzi a **megosztott** hatókörű fenntartott példányokhoz kapcsolódó kvótát. A megosztott hatókör esetén a fenntartott példánnyal járó előny a regisztráció mindegyik előfizetésére érvényes. Az Azure nem tudja megállapítani, hogy az ügyfél rendelkezik-e elegendő szabad maggal az erőforrás üzembe helyezéséhez az összes előfizetést figyelembe véve. Az Azure a kvótától függetlenül mindig lehetővé teszi a virtuálisgép-méret kiválasztását, ha a kiválasztott hatókör megosztott.

Az Azure emellett nem végez kvótaellenőrzést a **javasolt** vásárlásokra vonatkozóan sem. A javaslatok az aktív használaton alapulnak. Az Azure feltételezi, hogy az ügyfél rendelkezik elegendő maggal az adott virtuálisgép-méret futtatásához, mert az ügyfél esetében már megvalósult a javaslat létrehozásához szükséges használat.

## <a name="solution"></a>Megoldás

A kapott hibaüzenettől függően az alábbi megoldások valamelyikével háríthatja el a problémát.

### <a name="solution-1"></a>1\. megoldás

Ha arra vonatkozó üzenet jelenik meg, hogy a _termék nem érhető el_ , kattintson a hibaüzenetben található **Kapcsolatfelvétel a támogató szolgálattal** hivatkozásra, és kérje egy kivételnek az előfizetéshez való hozzáadását. A kivételek azonban nem mindig engedélyezhetők.

### <a name="solution-2"></a>2\. megoldás

Ha arra vonatkozó üzenet jelenik meg, hogy _nincs elegendő magkvóta_ , módosíthatja a hatókört a **megosztott** hatókörre. A foglalás megvásárlását követően módosíthatja a foglalás hatókörét a **megosztott** hatókörről az **egyetlen előfizetésre kiterjedő** hatókörre.

Másik megoldásként kattintson a hibaüzenetben található **Kvótanövelés kérése** hivatkozásra, és igényeljen további processzormagkvótát az előfizetés számára.

## <a name="next-steps"></a>Következő lépések

- A foglalásihatókör-beállításokkal kapcsolatos további információkért lásd: [A foglalások hatókörének beállítása](prepare-buy-reservation.md#scope-reservations).