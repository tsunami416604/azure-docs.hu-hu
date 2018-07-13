---
title: fájl belefoglalása
description: fájl belefoglalása
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 5/14/2018
ms.author: markgal
ms.custom: include file
ms.openlocfilehash: 5590da80a1c217e7902e8e010688e40f5624898c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38730498"
---
## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása
Recovery Services-tároló olyan entitás, amely a biztonsági mentések és idővel létrehozott helyreállítási pontokat tárolja. A Recovery Services-tárolót is tartalmaz, amely a védett virtuális gépek társítva vannak a biztonsági mentési szabályzatok.

Egy Recovery Services-tároló létrehozásához:

1. Jelentkezzen be az előfizetését a [az Azure portal](https://portal.azure.com/).
2. A bal oldali menüben válassza ki a **minden szolgáltatás**.

    ![Az összes szolgáltatás lehetőséget a főmenü](./media/backup-create-rs-vault/click-all-services.png) <br/>

3. Az összes szolgáltatás párbeszédpanelen írja be a *Recovery Services*. Ahogy elkezd gépelni, a bemeneti szűri az erőforrások listájában. Ha azt látja, válassza ki a **Recovery Services-tárolók**.

    ![Az összes szolgáltatások párbeszédpanelen írja be a Recovery Services](./media/backup-create-rs-vault/all-services.png) <br/>

    Az előfizetés Recovery Services-tárolók listája megjelenik.
4. Az a **Recovery Services-tárolók** menüjében válassza **Hozzáadás**.

    ![Recovery Services-tároló létrehozása – 2. lépés](./media/backup-create-rs-vault/add-button-create-vault.png)

    A **Recovery Services-tárolók** menü megnyitása. Akkor arra kéri, hogy a **neve**, **előfizetés**, **erőforráscsoport**, és **hely**.

    !["A recovery Services-tárolók" panel](./media/backup-create-rs-vault/create-new-vault-dialog.png)
5. A **Név** mezőben adjon meg egy egyszerű nevet a tároló azonosításához. Neve az Azure-előfizetés egyedinek kell lennie. Írja be egy nevet, amely tartalmazza a legalább két, de legfeljebb 50 karakter hosszú lehet. A név egy betűvel kell kezdődnie, és csak betűket, számokat és kötőjeleket tartalmazhat.
6. A **előfizetés**, válassza ki a használni kívánt előfizetést. Ha csak egy előfizetéssel tagja, ezen a néven fog megjelenni. Ha nem biztos abban, hogy melyik előfizetést szeretné használni, nyissa meg az alapértelmezett (vagy javasolt) előfizetést. Nincs több lehetőség csak akkor, ha a munkahelyi vagy iskolai fiók több Azure-előfizetéssel társítva.
7. A **erőforráscsoport** használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Az erőforráscsoportok az előfizetésében elérhető listájának megtekintéséhez válasszon **meglévő használata**, és kattintson a legördülő menüből. Hozzon létre egy új erőforráscsoportot, válassza a **új létrehozása** írja be a nevet. Az erőforráscsoportokkal teljes körű információkért lásd: [Azure Resource Manager áttekintése](../articles/azure-resource-manager/resource-group-overview.md).
8. A **hely** a tárolóhoz tartozó földrajzi régió kiválasztásához. Virtuális gépek, a tároló védelmére egy tárolót hoz *kell* lehet, mint a virtuális gépeknek ugyanabban a régióban.

   > [!IMPORTANT]
   > Ha biztos benne, hogy a hely, amelyben a virtuális gép található, a tároló létrehozása párbeszédpanel bezárásához, és nyissa meg a portálon lévő virtuális gépek listáját. Ha több régióban rendelkezik virtuális gépekkel, minden régióban hozzon létre egy Recovery Services-tárolót. Hozza létre a tárolót az első helyen, majd lépjen a következő helyre. Hiba esetén nem kell megadnia a storage-fiókok a biztonsági mentési adatok tárolására. A Recovery Services-tároló és az Azure Backup szolgáltatás, amely automatikusan kezeli.
   >
   >

9. Amikor készen áll a Recovery Services-tárolót létrehozni, kattintson az **létrehozás**.

    ![A Backup-tárolók listája](./media/backup-create-rs-vault/click-create-button.png)

    A Recovery Services-tároló létrehozása eltarthat egy ideig. Figyelje az értesítéseket szakaszában (a portál jobb felső területén). Miután a tároló létrejött, megjelenik a Recovery Services-tárolók listája. Ha továbbra sem látja a tárolót, kattintson a **frissítése**.

     ![A Backup-tárolók listája](./media/backup-create-rs-vault/refresh-button.png)
