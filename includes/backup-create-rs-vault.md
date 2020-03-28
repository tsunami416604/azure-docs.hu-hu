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
ms.openlocfilehash: 6167774171affda7e5469d5852a79657a6da700d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262095"
---
## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Recovery Services-tároló egy tárolási entitás, amely tárolja a helyreállítási pontok at az idő múlásával létrehozott. A védett elemekhez társított biztonsági mentési házirendeket is tartalmazza.

A Helyreállítási szolgáltatások tárolójának létrehozásához kövesse az alábbi lépéseket.

1. Jelentkezzen be az előfizetésbe az [Azure Portalon.](https://portal.azure.com/)

1. A bal oldali menüben válassza a **Minden szolgáltatás lehetőséget.**

    ![Az összes szolgáltatás kijelölése](./media/backup-create-rs-vault/click-all-services.png)

1. A **Minden szolgáltatás** párbeszédpanelen adja meg a *Helyreállítási szolgáltatások párbeszédpanelt.* Az erőforrások listája a bevitt adatok nak megfelelően szűr. Az erőforrások listájában válassza a **Helyreállítási szolgáltatások tárolói lehetőséget.**

    ![Adja meg és válassza a Helyreállítási szolgáltatások tárolóit](./media/backup-create-rs-vault/all-services.png)

    Megjelenik a Helyreállítási szolgáltatások tárolóinak listája az előfizetésben.

1. A **Recovery Services-tárolók** irányítópultján válassza a **Hozzáadás lehetőséget.**

    ![Helyreállítási szolgáltatások tárolójának hozzáadása](./media/backup-create-rs-vault/add-button-create-vault.png)

    Megnyílik **a Helyreállítási szolgáltatások tárolója** párbeszédpanel. Adja meg a **Név**, **Előfizetés**, **Erőforrás csoport**és **Hely értékét.**

    ![A Helyreállítási szolgáltatások tárolójának konfigurálása](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Név**: Adjon meg egy rövid nevet a tároló azonosításához. A névnek egyedinek kell lennie az Azure-előfizetésben. Adjon meg legalább 2, de legfeljebb 50 karakterből álló nevet. A névnek betűvel kell kezdődnie, és csak betűkből, számokból és kötőjelekből állhat.
   - **Előfizetés**: Válassza ki a használni kívánt előfizetést. Ha csak egy előfizetés tagja, ezt a nevet fogja látni. Ha nem biztos abban, hogy melyik előfizetést használja, használja az alapértelmezett (javasolt) előfizetést. Több lehetőség csak akkor áll meg, ha a munkahelyi vagy iskolai fiókja egynél több Azure-előfizetéshez van társítva.
   - **Erőforráscsoport**: Használjon meglévő erőforráscsoportot, vagy hozzon létre egy újat. Az előfizetésben elérhető erőforráscsoportok listájának megtekintéséhez válassza a **Meglévő használata**lehetőséget, majd válasszon ki egy erőforrást a legördülő listából. Új erőforráscsoport létrehozásához válassza **az Új létrehozása lehetőséget,** és írja be a nevet. Az erőforráscsoportokról az [Azure Resource Manager áttekintése című témakörben olvashat bővebben.](../articles/azure-resource-manager/management/overview.md)
   - **Hely**: Válassza ki a tároló földrajzi régióját. A virtuális gépek védelmére szolgáló tároló létrehozásához a *tárolónak* ugyanabban a régióban kell lennie, mint a virtuális gépeknek.

      > [!IMPORTANT]
      > Ha nem biztos a virtuális gép helyében, zárja be a párbeszédpanelt. Nyissa meg a portálon található virtuális gépek listáját. Ha több régióban rendelkezik virtuális gépekkel, hozzon létre egy Recovery Services-tárolót minden régióban. Hozza létre a tárolót az első helyen, mielőtt létrehozna egy másik helyhez a tárolót. Nincs szükség a biztonsági mentési adatok tárolásához tárfiókok megadására. A Recovery Services-tároló és az Azure Backup leíró, amely automatikusan.
      >
      >

1. Ha készen áll a Helyreállítási szolgáltatások tárolójának létrehozására, válassza a **Létrehozás lehetőséget.**

    ![A Helyreállítási szolgáltatások tárolójának létrehozása](./media/backup-create-rs-vault/click-create-button.png)

    Eltarthat egy ideig a Recovery Services-tároló létrehozása. Az állapotértesítések figyelése a portál jobb felső sarkában lévő **Értesítések** területen. A tároló létrehozása után látható a Helyreállítási szolgáltatások tárolóinak listájában. Ha nem látja a trezort, válassza a **Frissítés**lehetőséget.

     ![A biztonsági másolat tárolóinak listájának frissítése](./media/backup-create-rs-vault/refresh-button.png)
