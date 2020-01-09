---
title: Biztonsági mentés automatikus engedélyezése a virtuális gép Azure Policyval végzett létrehozásakor
description: A cikk azt ismerteti, hogyan használható a Azure Policy az adott hatókörben létrehozott összes virtuális gép biztonsági mentésének automatikus engedélyezéséhez
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 7a3b526d654936d4e7ec89127a9074146c1b0179
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450121"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Biztonsági mentés automatikus engedélyezése a virtuális gép Azure Policyval végzett létrehozásakor

A szervezet biztonsági mentési vagy megfelelőségi rendszergazdájának egyik kulcsfontosságú feladata, hogy biztosítsa, hogy az üzleti szempontból kritikus fontosságú gépek biztonsági mentése a megfelelő megőrzéssel történjen.

Napjainkban a Azure Backup egy beépített szabályzatot (Azure Policy használatával) biztosít, amely az **előfizetésen vagy az erőforráscsoport egy adott helyén lévő összes Azure-beli virtuális**géphez hozzárendelhető. Ha a szabályzatot egy adott hatókörhöz rendeli hozzá, az abban a hatókörben létrehozott összes új virtuális gépet a rendszer automatikusan konfigurálja egy meglévő tárolóba **ugyanazon a helyen és előfizetésben**. A felhasználó megadhatja a tárolót és az adatmegőrzési házirendet, amelyhez a biztonsági másolatba mentett virtuális gépek társítva lesznek.

## <a name="supported-scenarios"></a>Támogatott helyzetek 

* A beépített szabályzat jelenleg csak Azure-beli virtuális gépek esetén támogatott. A felhasználóknak ügyelniük kell arra, hogy a hozzárendelés során megadott adatmegőrzési szabályzat a virtuális gép adatmegőrzési szabályzata legyen. Tekintse át [ezt](https://aka.ms/PolicySupportedSKUs) a dokumentumot a szabályzat által támogatott összes virtuális gép SKU megtekintéséhez.

* A szabályzat egyszerre egyetlen helyhez és előfizetéshez is hozzárendelhető. Ha engedélyezni szeretné a virtuális gépek biztonsági mentését a helyek és az előfizetések között, a házirend-hozzárendelés több példányát is létre kell hoznia, egyet a hely és az előfizetés minden kombinációja esetében.

* A megadott tár és a biztonsági mentésre konfigurált virtuális gépek különböző erőforráscsoportok alatt lehetnek.

* A felügyeleti csoport hatóköre jelenleg nem támogatott.

* A beépített szabályzat jelenleg nem érhető el az országos felhőkben.

## <a name="using-the-built-in-policy"></a>A beépített szabályzat használata

A szabályzatnak a szükséges hatókörhöz való hozzárendeléséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalra, és navigáljon a **szabályzat** irányítópultra.
2. A bal oldali menüben válassza a **definíciók** lehetőséget, hogy lekérje az Azure-erőforrások összes beépített szabályzatának listáját.
3. A **Kategória = Backup**szolgáltatás listájának szűrése Látni fogja, hogy a lista egyetlen "a biztonsági mentés konfigurálása a virtuális gépeken egy helyről egy meglévő központi tárba ugyanazon a helyen" nevű házirendre szűrve jelenik meg.
![szabályzat irányítópultja](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Kattintson a szabályzat nevére. A rendszer átirányítja a szabályzat részletes definícióját.
![szabályzat-definíció panel](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Kattintson a panel tetején található **hozzárendelés** gombra. Ez átirányítja a szabályzat- **hozzárendelési** panelre.
6. Az **alapbeállítások**területen kattintson a **hatókör** mező melletti három pontra. Ekkor megnyílik a megfelelő környezeti panel, ahol kiválaszthatja az előfizetést, amelyre alkalmazni kívánja a szabályzatot. Kiválaszthat egy erőforráscsoportot is, hogy a házirend csak egy adott erőforráscsoport virtuális gépei esetében legyen alkalmazva.
![szabályzat-hozzárendelés alapjai](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. A **Parameters (paraméterek** ) lapon válasszon ki egy helyet a legördülő menüből, és válassza ki a tároló-és biztonsági mentési szabályzatot, amelyhez társítani kell a hatókörben lévő virtuális gépeket.
![szabályzat-hozzárendelési paraméterek](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Győződjön meg arról, hogy a **hatás** a deployIfNotExists értékre van állítva.
9. Navigáljon a **felülvizsgálat + létrehozás** elemre, és kattintson a **Létrehozás**gombra.

> [!NOTE]
>
> A Azure Policy a meglévő virtuális gépeken is felhasználható [szervizeléssel](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources).

> [!NOTE]
>
> Javasoljuk, hogy a házirendet egyszerre ne rendelje hozzá több mint 200 virtuális géphez. Ha a házirend több mint 200 virtuális géphez van hozzárendelve, akkor a biztonsági mentés néhány órával később is elindítható, mint amelyet az ütemterv meghatároz.

## <a name="next-steps"></a>Következő lépések

[További információ a Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview)