---
title: Biztonsági mentés automatikus engedélyezése a virtuális gép Azure Policyval végzett létrehozásakor
description: A cikk azt ismerteti, hogyan használható a Azure Policy az adott hatókörben létrehozott összes virtuális gép biztonsági mentésének automatikus engedélyezéséhez
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: de4923000bc842203535e03727fd532c67a8f517
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826072"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Biztonsági mentés automatikus engedélyezése a virtuális gép Azure Policyval végzett létrehozásakor

A szervezet biztonsági mentési vagy megfelelőségi rendszergazdájának egyik kulcsfontosságú feladata, hogy biztosítsa, hogy az üzleti szempontból kritikus fontosságú gépek biztonsági mentése a megfelelő megőrzéssel történjen.

Napjainkban a Azure Backup egy beépített szabályzatot (Azure Policy használatával) biztosít, amely az **előfizetésen vagy az erőforráscsoport egy adott helyén lévő összes Azure-beli virtuális**géphez hozzárendelhető. Ha a szabályzatot egy adott hatókörhöz rendeli hozzá, az abban a hatókörben létrehozott összes új virtuális gépet a rendszer automatikusan konfigurálja egy meglévő tárolóba **ugyanazon a helyen és előfizetésben**. A felhasználó megadhatja azt a tárolót és adatmegőrzési házirendet, amelyhez a biztonsági másolattal rendelkező virtuális gépeket társítani kell.

## <a name="supported-scenarios"></a>Támogatott helyzetek

* A beépített szabályzat jelenleg csak Azure-beli virtuális gépek esetén támogatott. A felhasználóknak ügyelniük kell arra, hogy a hozzárendelés során megadott adatmegőrzési szabályzat a virtuális gép adatmegőrzési szabályzata legyen. Tekintse át [ezt](./backup-azure-policy-supported-skus.md) a dokumentumot a szabályzat által támogatott összes virtuális gép SKU megtekintéséhez.

* A szabályzat egyszerre egyetlen helyhez és előfizetéshez is hozzárendelhető. Ha engedélyezni szeretné a virtuális gépek biztonsági mentését a helyek és az előfizetések között, a házirend-hozzárendelés több példányát is létre kell hoznia, egyet a hely és az előfizetés minden kombinációja esetében.

* A megadott tár és a biztonsági mentésre konfigurált virtuális gépek különböző erőforráscsoportok alatt lehetnek.

* A felügyeleti csoport hatóköre jelenleg nem támogatott.

* A beépített szabályzat jelenleg nem érhető el az országos felhőkben.

## <a name="using-the-built-in-policy"></a>A beépített szabályzat használata

A szabályzatnak a szükséges hatókörhöz való hozzárendeléséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a Azure Portalba, és navigáljon a **szabályzat** irányítópultra.
1. A bal oldali menüben válassza a **definíciók** lehetőséget, hogy lekérje az Azure-erőforrások összes beépített szabályzatának listáját.
1. A **Kategória = Backup**szolgáltatás listájának szűrése A lista a "biztonsági mentés konfigurálása egy helyen lévő virtuális gépeken egy meglévő központi tárolóra ugyanazon a helyen" nevű házirendre szűrve jelenik meg.
![Házirend-irányítópult](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
1. Válassza ki a szabályzat nevét. A rendszer átirányítja a szabályzat részletes definícióját.
![Házirend-definíciós ablaktábla](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
1. Kattintson a **hozzárendelés** gombra a panel tetején. A rendszer átirányítja a **házirend-hozzárendelési** panelre.
1. Az **alapok**területen válassza a **hatókör** mező melletti három pontot. Ekkor megnyílik a megfelelő környezeti ablaktábla, ahol kiválaszthatja az előfizetést, amelyre alkalmazni kívánja a szabályzatot. Kiválaszthat egy erőforráscsoportot is, hogy a házirend csak egy adott erőforráscsoport virtuális gépei esetében legyen alkalmazva.
![Szabályzat-hozzárendelés alapjai](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
1. A **Parameters (paraméterek** ) lapon válasszon ki egy helyet a legördülő menüből, és válassza ki a tároló-és biztonsági mentési szabályzatot, amelyhez társítani kell a hatókörben lévő virtuális gépeket.
![Szabályzat-hozzárendelési paraméterek](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
1. Győződjön meg arról, hogy a **hatás** a deployIfNotExists értékre van állítva.
1. Keresse meg a **felülvizsgálat + létrehozás** elemet, majd válassza a **Létrehozás**lehetőséget.

> [!NOTE]
>
> A Azure Policy a meglévő virtuális gépeken is felhasználható [szervizeléssel](../governance/policy/how-to/remediate-resources.md).

> [!NOTE]
>
> Javasoljuk, hogy a szabályzatot egyszerre több mint 200 virtuális géphez lehessen hozzárendelni. Ha a házirend több mint 200 virtuális géphez van hozzárendelve, akkor a biztonsági mentés néhány órával később is elindítható, mint amelyet az ütemterv meghatároz.

## <a name="next-steps"></a>További lépések

[További információ a Azure Policy](../governance/policy/overview.md)
