---
title: Osztályterem-tesztkörnyezetek az Azure Lab Services használati beállításainak konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a labor létrehozása a felhasználók számát, azok regisztrálva a labor, szabályozhatja a virtuális Gépet, és további használatához órák száma.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2018
ms.author: spelluru
ms.openlocfilehash: a91d5826f52b2beb05f2d9a08f3646bd776b294e
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142412"
---
# <a name="configure-usage-settings-and-policies"></a>És használati beállítások és szabályzatok konfigurálása
Ez a cikk bemutatja, hogyan konfigurálhatja a labor létrehozása a felhasználók számát, azok regisztrálva a labor, szabályozhatja a virtuális Gépet, és további használatához órák száma. 


## <a name="specify-the-number-of-users-allowed-into-the-lab"></a>Adja meg a laborkörnyezetbe engedélyezett felhasználók száma

1. Válassza a **Használati szabályzat** lehetőséget. 
2. A **Használati szabályzat** terület beállításaiban adja meg, **hány felhasználó** használhatja a tesztkörnyezetet.
3. Kattintson a **Mentés** gombra. 

    ![Használati szabályzat](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="send-registration-link-to-users"></a>Regisztráció hivatkozás küldése a felhasználóknak

1. Váltson az **Irányítópult** nézetre. 
2. Válassza a **Felhasználói regisztráció** csempét.

    ![A diákok regisztrációs hivatkozása](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. A **Felhasználói regisztráció** párbeszédablakban kattintson a **Másolás** gombra. A rendszer a vágólapra másolja a hivatkozást. Illessze be egy e-mail-szerkesztőbe, majd küldje el e-mailben a diákoknak. Diákok a hivatkozás segítségével nyissa meg a lapot, amely segít nekik a Regisztrálás a labor létrehozása. 

    ![A diákok regisztrációs hivatkozása](../media/tutorial-setup-classroom-lab/registration-link.png)
2. A **Felhasználói regisztráció** párbeszédablakban kattintson a **Bezárás** gombra. 

## <a name="view-users-registered-with-the-lab"></a>A tesztkörnyezetben regisztrált felhasználók megtekintése

Válassza ki **felhasználók** a labor létrehozása a felhasználók listája a bal oldali menü regisztrálva. 

![A labor regisztrált felhasználók listája](../media/how-to-configure-student-usage/users-list.png)

## <a name="set-quotas-per-user"></a>Felhasználónként kvóták beállítása

1. Válassza ki **felhasználói beállítások** a bal oldali menüben.
2. Válassza ki **felhasználónként Quta** csempére. 
3. Válassza ki **óra, a felhasználó használhatja a virtuális gépek számának korlátozásához**. 
4. A **hány órát kíván adni, az egyes felhasználók?**, adja meg az órák számát, és válassza ki **mentése**. 

    ![Felhasználónként órák száma](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
5. Akkor jelenik meg az órák számát az **felhasználónként kvóta** most csempére. 

    ![Kvóta felhasználó szerint](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="manage-user-vms"></a>Felhasználói virtuális gépek kezelése
Miután tanulók regisztrálása az Azure Lab Services segítségével a regisztráció társítani a megadott, a tanulók a hozzárendelt virtuális gépeket láthatja a **virtuális gépek** fülre. 

![Diákok rendelt virtuális gépek](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

A következő feladatokat a virtuális gép tanuló teheti meg: 

- Virtuális gép leállítása, ha a virtuális gép fut-e. 
- Virtuális gép elindítása, ha a virtuális gép le van állítva. 
- Csatlakozzon a virtuális géphez. 
- Törölje a virtuális Gépet. 
- Megtekintheti, hogy a felhasználók a virtuális gép felhasznált órák számát. 


## <a name="next-steps"></a>További lépések
Kezdje meg egy tesztkörnyezet létrehozását az Azure Lab Services használatával:

- [Osztályterem-tesztkörnyezet beállítása](how-to-manage-classroom-labs.md)
- [Tesztkörnyezet beállítása](../tutorial-create-custom-lab.md)
