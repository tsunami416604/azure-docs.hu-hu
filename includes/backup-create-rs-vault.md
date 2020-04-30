---
title: fájl belefoglalása
description: fájl belefoglalása
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: 2c74783ea8246232cb5c4270691daf3f83fe9a30
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82204522"
---
## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Recovery Services-tároló egy olyan tárolási entitás, amely az idő múlásával létrehozott helyreállítási pontokat tárolja. Emellett tartalmazza a védett elemekhez társított biztonsági mentési házirendeket is.

Recovery Services-tároló létrehozásához kövesse az alábbi lépéseket.

1. Jelentkezzen be az előfizetésbe a [Azure Portalban](https://portal.azure.com/).

1. A bal oldali menüben válassza a **minden szolgáltatás**lehetőséget.

    ![Minden szolgáltatás kiválasztása](./media/backup-create-rs-vault/click-all-services.png)

1. A **minden szolgáltatás** párbeszédpanelen írja be a *Recovery Services*értéket. Az erőforrás-szűrők listája a bemenet alapján. Az erőforrások listájában válassza a **Recovery Services**-tárolók lehetőséget.

    ![Adja meg és válassza ki Recovery Services tárolókat](./media/backup-create-rs-vault/all-services.png)

    Megjelenik az előfizetésben található Recovery Services-tárolók listája.

1. A **Recovery Services** -tárolók irányítópultján válassza a **Hozzáadás**lehetőséget.

    ![Recovery Services-tároló hozzáadása](./media/backup-create-rs-vault/add-button-create-vault.png)

    Megnyílik az **Recovery Services** -tároló párbeszédpanel. Adja meg a **név**, az **előfizetés**, az **erőforráscsoport**és a **hely**értékét.

    ![A Recovery Services-tároló konfigurálása](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Név**: adjon meg egy rövid nevet a tároló azonosításához. A névnek egyedinek kell lennie az Azure-előfizetésben. Olyan nevet adjon meg, amely legalább 2 karakterből áll, de legfeljebb 50 karakterből állhat. A névnek betűvel kell kezdődnie, és csak betűt, számot és kötőjelet tartalmazhat.
   - **Előfizetés**: válassza ki a használni kívánt előfizetést. Ha csak egy előfizetés tagja, akkor ezt a nevet fogja látni. Ha nem biztos abban, hogy melyik előfizetést szeretné használni, használja az alapértelmezett (javasolt) előfizetést. Több választási lehetőség is van, ha a munkahelyi vagy iskolai fiók egynél több Azure-előfizetéshez van társítva.
   - **Erőforráscsoport**: használjon meglévő erőforráscsoportot, vagy hozzon létre egy újat. Az előfizetésben elérhető erőforráscsoportok listájának megtekintéséhez válassza a **meglévő használata**lehetőséget, majd válasszon ki egy erőforrást a legördülő listából. Új erőforráscsoport létrehozásához válassza az **új létrehozása** lehetőséget, és adja meg a nevet. További információ az erőforráscsoportok használatáról: [Azure Resource Manager Overview (áttekintés](../articles/azure-resource-manager/management/overview.md)).
   - **Hely**: válassza ki a tároló földrajzi régióját. Ha tárolót szeretne létrehozni az adatforrások védelme érdekében, a tárolónak ugyanabban a régióban *kell lennie* , mint az adatforrásnak.

      > [!IMPORTANT]
      > Ha nem biztos benne, hogy az adatforrás hol található, akkor zárjuk be a párbeszédpanelt. Nyissa meg az erőforrások listáját a portálon. Ha több régióban rendelkezik adatforrásokkal, minden régióhoz hozzon létre egy Recovery Services-tárolót. Hozza létre a tárolót az első helyen, mielőtt létrehozza a tárolót egy másik helyhez. Nem kell megadnia a tárolási fiókokat a biztonsági másolatok tárolásához. A Recovery Services-tároló és a Azure Backup automatikusan kezeli.
      >
      >

1. Ha készen áll az Recovery Services-tároló létrehozására, válassza a **Létrehozás**lehetőséget.

    ![A Recovery Services-tároló létrehozása](./media/backup-create-rs-vault/click-create-button.png)

    A Recovery Services-tároló létrehozása eltarthat egy ideig. Figyelje az **értesítéseket** a portál jobb felső sarkában található értesítések területről. A tároló létrehozása után a Recovery Services-tárolók listájában látható. Ha nem látja a tárolót, válassza a **frissítés**lehetőséget.

     ![A Backup-tárolók listájának frissítése](./media/backup-create-rs-vault/refresh-button.png)
