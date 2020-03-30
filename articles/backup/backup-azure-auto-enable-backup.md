---
title: Biztonsági mentés automatikus engedélyezése a virtuális gép Azure Policyval végzett létrehozásakor
description: Cikk, amely leírja, hogyan használhatja az Azure Policy-t az adott hatókörben létrehozott összes virtuális gép biztonsági mentésének automatikus engedélyezéséhez
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1d423371788f87155328e8f5c9334e47349a68e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584268"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Biztonsági mentés automatikus engedélyezése a virtuális gép Azure Policyval végzett létrehozásakor

A szervezet biztonsági mentési vagy megfelelőségi rendszergazdájának egyik legfontosabb feladata annak biztosítása, hogy az összes üzleti legkritikusabb rendszerű gépről biztonsági másolat készüljön a megfelelő adatmegőrzéssel.

Ma az Azure Backup egy beépített szabályzatot biztosít (az Azure Policy használatával), amely **egy előfizetésen vagy erőforráscsoporton belül egy adott helyen az összes Azure-virtuális géphez**hozzárendelhető. Ha ez a házirend egy adott hatókörhöz van rendelve, az adott hatókörben létrehozott összes új virtuális gép automatikusan konfigurálva lesz **egy ugyanazon a helyen és előfizetésben lévő meglévő tárolóba**való biztonsági mentéshez. A felhasználó megadhatja a tárolóés az adatmegőrzési szabályzatot, amelyhez a biztonsági másolatot a virtuális gépeket kell társítani.

## <a name="supported-scenarios"></a>Támogatott helyzetek

* A beépített szabályzat jelenleg csak az Azure virtuális gépek támogatott. A felhasználóknak gondoskodniuk kell arról, hogy a hozzárendelés során megadott adatmegőrzési szabály egy virtuális gép-adatmegőrzési szabály legyen. Tekintse meg [ezt](https://docs.microsoft.com/azure/backup/backup-azure-policy-supported-skus) a dokumentumot az összes virtuális gép sku-k támogatja ezt a szabályzatot.

* A szabályzat egyszerre egyetlen helyhez és előfizetéshez rendelhető. Ahhoz, hogy a virtuális gépek biztonsági mentést a helyeken és az előfizetések között, több példányt kell létrehozni a szabályzat-hozzárendelés, egy-egy minden egyes kombinációja a hely és az előfizetés.

* A megadott tároló és a biztonsági mentéshez konfigurált virtuális gépek különböző erőforráscsoportok ban lehetnek.

* A felügyeleti csoport hatóköre jelenleg nem támogatott.

* A beépített szabályzat jelenleg nem érhető el a nemzeti felhőkben.

## <a name="using-the-built-in-policy"></a>A beépített házirend használata

Ha a házirendet a szükséges hatókörhöz szeretné rendelni, kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalon, és keresse meg a **szabályzat** irányítópultját.
2. Válassza **a definíciók a** bal oldali menüben az Azure Resources összes beépített szabályzatának listájának leéséhez.
3. A **Category=Backup lista szűrése.** A listát egy "Biztonsági mentés konfigurálása a virtuális gépeken egy helyen egy meglévő központi tárolóugyanazon a helyen" nevű egyetlen házirendre szűrve fogja látni.
![Házirend irányítópultja](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Kattintson a szabályzat nevére. A házirend részletes definíciója lesz átirányítva.
![Házirend-definíció panel](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Kattintson a panel tetején található **Hozzárendelés** gombra. Ez átirányítja a **Házirend hozzárendelése** panelre.
6. Az **Alapok csoportban**kattintson a **Hatókör** mező melletti három elemre. Ez megnyit egy megfelelő környezetet, ahol kiválaszthatja a szabályzat hoz a házirendet. Szükség esetén kijelölhet egy erőforráscsoportot is, így a házirend csak egy adott erőforráscsoport virtuális gépeire vonatkozik.
![A házirend-hozzárendelés alapjai](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. A **Paraméterek** lapon válasszon ki egy helyet a legördülő menüből, és válassza ki azt a tárolót és biztonsági mentési szabályzatot, amelyhez a hatókörben lévő virtuális gépeket társkell társozni.
![Házirend-hozzárendelési paraméterek](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Győződjön meg arról, hogy az **effect** deployIfNotExists beállításra van állítva.
9. Nyissa meg a **Véleményezés+létrehozás menüT,** és kattintson **a Létrehozás gombra.**

> [!NOTE]
>
> Az Azure Policy meglévő virtuális gépeken is használható [a szervizelés](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources)használatával.

> [!NOTE]
>
> Ajánlott, hogy ez a szabályzat nincs hozzárendelve több mint 200 virtuális gépek egy időben. Ha a szabályzat több mint 200 virtuális géphez van hozzárendelve, akkor a biztonsági mentés néhány órával később aktiválódik, mint az ütemezés által megadott.

## <a name="next-steps"></a>Következő lépések

[További információ az Azure-szabályzatról](https://docs.microsoft.com/azure/governance/policy/overview)
