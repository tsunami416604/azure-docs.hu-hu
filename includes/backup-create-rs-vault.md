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
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34664927"
---
## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása
Recovery Services-tároló olyan entitás, amely a biztonsági mentések és adott idő alatt létrehozott helyreállítási pontok tárolására. A Recovery Services-tárolónak a biztonsági mentési házirendek a védett virtuális gépek társított is tartalmaz.

Egy Recovery Services-tároló létrehozásához:

1. Jelentkezzen be az előfizetéshez a [Azure-portálon](https://portal.azure.com/).
2. A bal oldali menüben válassza ki a **minden szolgáltatás**.

    ![A főmenü minden szolgáltatás választania](./media/backup-create-rs-vault/click-all-services.png) <br/>

3. Az összes szolgáltatások párbeszédpanelen írja be a *Recovery Services*. Írja be megkezdése előtt, a megadott erőforrások listájának szűrése. Miután látja, válassza ki a **Recovery Services-tárolók**.

    ![Az összes szolgáltatások párbeszédpanelen írja be a helyreállítási szolgáltatások](./media/backup-create-rs-vault/all-services.png) <br/>

    Az előfizetés Recovery Services-tárolók listája jelenik meg.
4. Az a **Recovery Services-tárolók** menü **Hozzáadás**.

    ![Recovery Services-tároló létrehozása – 2. lépés](./media/backup-create-rs-vault/add-button-create-vault.png)

    A **Recovery Services-tárolók** menü megnyitása. A rendszer kérni fogja, hogy információkat biztosítanak a **neve**, **előfizetés**, **erőforráscsoport**, és **hely**.

    !["Recovery Services-tárolók" ablak](./media/backup-create-rs-vault/create-new-vault-dialog.png)
5. A **Név** mezőben adjon meg egy egyszerű nevet a tároló azonosításához. A név az Azure-előfizetés egyedinek kell lennie. Írjon be egy nevet, amely legalább két, de legfeljebb 50 karakter hosszú lehet. A nevének betűvel kell kezdődnie, és csak betűket, számokat és kötőjeleket tartalmazhat.
6. A **előfizetés**, válassza ki a használni kívánt előfizetést. Ha csak egyetlen előfizetéssel tagja, ezen a néven jelenik meg. Ha nem biztos abban, hogy melyik előfizetéssel, nyissa meg az alapértelmezett (vagy javasolt) előfizetés. Nincs több lehetősége csak akkor, ha a munkahelyi vagy iskolai fiókkal társítva a több Azure-előfizetéssel.
7. A **erőforráscsoport** használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. A rendelkezésre álló lista az erőforráscsoportok az előfizetéshez, válasszon **meglévő**, és kattintson a legördülő menüből. Hozzon létre egy új erőforráscsoportot, válassza ki **hozzon létre új** a nevét. Teljes információ az erőforráscsoportokkal: [Azure Resource Manager áttekintése](../articles/azure-resource-manager/resource-group-overview.md).
8. A **hely** válassza ki a tároló földrajzi területét. Ha hoz létre egy tárolót a virtuális gépek, a tároló védelmére szolgáló *kell* a virtuális gépek ugyanabban a régióban kell.

   > [!IMPORTANT]
   > Ha biztos benne, hogy a hely, amelyben a virtuális gép található, a tároló létrehozása párbeszédpanel bezárásához, és nyissa meg a portál virtuális gépek listáját. Ha több régióban rendelkezik virtuális gépekkel, minden régióban hozzon létre egy Recovery Services-tárolót. Hozza létre a tárolót az első helyen, majd lépjen a következő helyre. Nincs szükség az adhatja meg a storage-fiókok a biztonsági mentési adatok tárolására. A Recovery Services-tároló és az Azure Backup szolgáltatás, amely automatikusan tud kezelni.
   >
   >

9. Amikor készen áll a Recovery Services-tároló létrehozásához, kattintson **létrehozása**.

    ![A Backup-tárolók listája](./media/backup-create-rs-vault/click-create-button.png)

    A Recovery Services-tároló létrehozása eltarthat egy ideig. Figyelje az állapot értesítéseket az értesítések szakaszban (a portál jobb felső terület). A tároló létrehozása után a Recovery Services-tárolók listája jelenik meg. Ha még nem látja a tároló, kattintson a **frissítése**.

     ![A Backup-tárolók listája](./media/backup-create-rs-vault/refresh-button.png)
