---
title: fájl belefoglalása
description: fájl belefoglalása
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 74fe531c07aa871b06b5d0773f7e8fb0ade80be4
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49946215"
---
## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása
Recovery Services-tároló olyan entitás, amely a biztonsági másolatok és az idők során létrehozott helyreállítási pontokat tárolja. A Recovery Services-tárolót is tartalmaz, amely a védett virtuális gépek társítva vannak a biztonsági mentési szabályzatok.

Egy Recovery Services-tároló létrehozásához:

1. Jelentkezzen be az előfizetését a [az Azure portal](https://portal.azure.com/).

2. A bal oldali menüben válassza ki a **minden szolgáltatás**.

    ![Válassza ki az összes szolgáltatás](./media/backup-create-rs-vault/click-all-services.png)

3. Az a **minden szolgáltatás** párbeszédpanelen adja meg **Recovery Services**. Az erőforrások listájához a bemenetet megfelelően szűri. Az erőforrások listájában válassza ki a **Recovery Services-tárolók**.

    ![Adja meg, és válassza ki a Recovery Services-tárolók](./media/backup-create-rs-vault/all-services.png)

    Az előfizetés Recovery Services-tárolók listája megjelenik.
    
4. Az a **Recovery Services-tárolók** irányítópulton válassza **Hozzáadás**.

    ![Adja hozzá a Recovery Services-tároló](./media/backup-create-rs-vault/add-button-create-vault.png)

    A **Recovery Services-tároló** párbeszédpanel nyílik meg. Adja meg az értékeket a **neve**, **előfizetés**, **erőforráscsoport**, és **hely**.

    ![A Recovery Services-tároló konfigurálása](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Név**: Adjon meg egy, a tárolót azonosító rövid nevet. Neve az Azure-előfizetés egyedinek kell lennie. Adjon meg legalább két, de legfeljebb 50 karakter hosszú lehet. A névnek kell betűvel kezdődhet, és tartalmazhatnak csak betűket, számokat és kötőjeleket tartalmazhat.
   - **Előfizetés**: válassza ki a használni kívánt előfizetést. Ha Ön a tagja, csak egy előfizetéssel, látni fogja, hogy a neve. Ha nem biztos abban, hogy melyik előfizetést szeretné használni, használja az alapértelmezett (javasolt) előfizetést. Nincs több lehetőség csak akkor, ha a munkahelyi vagy iskolai fiókot egynél több Azure-előfizetéssel társítva.
   - **Erőforráscsoport**: használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Az előfizetésben elérhető erőforráscsoportok listájának megtekintéséhez válasszon **meglévő**, és majd a legördülő listából válasszon ki egy erőforrást. Hozzon létre egy új erőforráscsoportot, válassza a **új létrehozása** , és adja meg a nevét. A erőforráscsoportokkal kapcsolatos részletes információkért lásd: [Azure Resource Manager áttekintése](../articles/azure-resource-manager/resource-group-overview.md).
   - **Hely**: a tárolóhoz tartozó földrajzi régió kiválasztásához. Hozzon létre egy tárolót a virtuális gépek, a tároló védelmére, **kell** lehet, mint a virtuális gépeknek ugyanabban a régióban.

      > [!IMPORTANT]
      > Ha nem biztos a virtuális gép helyét, zárja be a párbeszédpanelt. Nyissa meg a portálon lévő virtuális gépek listáját. Ha több régióban rendelkezik virtuális gépekkel, minden régióban hozzon létre egy Recovery Services-tárolót. Hozza létre a tárolót az első helyen, egy másik helyre a tároló létrehozása előtt. Hiba esetén nem kell megadnia a storage-fiókok a biztonsági mentési adatok tárolására. A Recovery Services-tároló és az Azure Backup szolgáltatás, amely automatikusan kezeli.
      >
      >

5. Ha készen áll a Recovery Services-tárolót létrehozni, válassza ki a **létrehozás**.

    ![A Recovery Services-tároló létrehozása](./media/backup-create-rs-vault/click-create-button.png)

    Eltarthat egy ideig a Recovery Services-tároló létrehozása. Figyelje a a **értesítések** területet, a portál jobb felső sarkában. Miután a tároló létrejött, a Recovery Services-tárolók listája látható. Ha nem látja a tárolót, válassza ki a **frissítése**.

     ![A backup-tárolók listájának frissítése](./media/backup-create-rs-vault/refresh-button.png)
