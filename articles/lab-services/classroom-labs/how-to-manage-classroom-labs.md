---
title: Számítógéptermi labs Azure labor szolgáltatások kezelése |} Microsoft Docs
description: Megtudhatja, hogyan hozza létre és egy osztályteremben, amikor konfigurálja, és tekintse meg az összes a osztályteremben labs, a regisztráció lab-felhasználó a hivatkozásra, vagy egy tesztkörnyezetet törlése fájlmegosztás.
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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: f8cf5a46e1d1e3242fd46900b56f7d8cf15d3fc5
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082523"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Számítógéptermi labs Azure labor Services kezelése 
Ez a cikk ismerteti, hogyan létrehozásához és egy osztályteremben, amikor konfigurálja, minden osztályteremben labs megtekintéséhez, vagy egy osztályteremben, amikor törölni.

## <a name="prerequisites"></a>Előfeltételek
Egy osztály, amikor a labor fiók beállításához tagjának kell lennie a **labor létrehozó** szerepkör a tesztlabor-fiókban. A tesztkörnyezet tulajdonosa adhat hozzá a felhasználó a labor létrehozó szerepkör a következő cikkben szereplő lépések segítségével: [felhasználó hozzáadása a labor létrehozó szerepkör](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Osztályterem-tesztkörnyezet létrehozása

1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com).
2. Az **Új tesztkörnyezet** ablakban tegye a következőket: 
    1. Adja meg az osztályterem-tesztkörnyezet **nevét**. 
    2. Válassza ki az osztályteremben használni kívánt virtuális gép **méretét**.
    3. Válassza ki a virtuális gép létrehozásához használandó **rendszerképet**.
    4. Adja meg a **alapértelmezett hitelesítő adatok** jelentkezzen be a virtuális gépek, a laborban használt.
    7. Kattintson a **Mentés** gombra.

        ![Osztályterem-tesztkörnyezet létrehozása](../media/how-to-manage-classroom-labs/new-lab-window.png)
1. Megjelenik a **irányítópult** a labor. 
    
    ![Számítógéptermi labor irányítópult](../media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Használati szabályzat konfigurálása

1. Válassza a **Használati szabályzat** lehetőséget. 
2. A **Használati szabályzat** terület beállításaiban adja meg, **hány felhasználó** használhatja a tesztkörnyezetet.
3. Kattintson a **Mentés** gombra. 

    ![Használati szabályzat](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Sablon beállítása
A tesztkörnyezet sablonja egy alapszintű virtuálisgép-rendszerkép, amelyből az összes felhasználó virtuális gépe létrejön. Úgy állítsa be a virtuálisgép-sablont, hogy az pontosan aszerint legyen konfigurálva, amit a tesztkörnyezet felhasználóinak biztosítani kíván. A sablonhoz megadhat egy nevet és egy leírást, amely a tesztkörnyezet felhasználói számára jelenik meg. A sablont a Virtuálisgép-sablon példányainak a labor felhasználók számára elérhetővé tegye közzé.  

### <a name="set-template-title-and-description"></a>Set-sablon nevét és leírását
1. A **Sablon** szakaszban válassza a sablonhoz tartozó **Szerkesztés** (ceruza ikon) elemet. 
2. A **Felhasználói nézet** ablakban adja meg a sablon **címét**.
3. Adja meg a sablon **leírását**.
4. Kattintson a **Mentés** gombra.

    ![Az osztályterem-tesztkörnyezet leírása](../media/how-to-manage-classroom-labs/lab-description.png)

### <a name="set-up-the-template-vm"></a>A Virtuálisgép-sablon beállítása
 A sablon virtuális Gépet csatlakozhat, és a szükséges szoftver telepítését és az elérhetővé válik a diákoknak előtt. 

1. Várjon, amíg a sablon virtuális gép készen áll-e. Miután elkészült, a **Start** gomb engedélyezni kell. Indítsa el a virtuális Gépet, jelölje be **Start**.

    ![Indítsa el a Virtuálisgép-sablon](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. Csatlakoztassa a virtuális Gépet, jelölje be **Connect**, és kövesse az utasításokat. 

    ![A sablon virtuális gép kapcsolódni](../media/tutorial-setup-classroom-lab/connect-template-vm.png)
1. A diákok (például a Visual Studio Azure Tártallózó, stb.) a labor elvégzéséhez szükséges szoftvereket telepíteniük. 
2. Kapcsolat bontása (zárja be a távoli asztali munkamenetgazda) Virtuálisgép-sablonból. 
3. **Állítsa le** a virtuális gép sablon kiválasztásával **leállítása**. 

    ![A sablon virtuális gép leállítása](../media/tutorial-setup-classroom-lab/stop-template-vm.png)


### <a name="publish-the-template"></a>A sablon közzététele 
Ha közzéteszi a sablont, Azure labor szolgáltatások hoz létre virtuális gépek a laborban a sablon használatával. A folyamat során létrehozott virtuális gépek száma megegyezik a tesztkörnyezet felhasználóinak maximális számával, amelyet a tesztkörnyezet használati szabályzatában állíthat be. A virtuális gépek konfigurációja megegyezik a sablonéval. 

1. Válassza ki **közzététel** a a **sablon** szakasz. 

    ![A sablon virtuális Gépet közzététele](../media/tutorial-setup-classroom-lab/public-access.png)
1. Az a **közzététel** ablakban válassza ki a **közzétett** lehetőséget. 
2. Jelölje ki, a **közzététel** gombra. Ez a folyamat eltarthat néhány alkalommal attól függően, hogy hány virtuális gépek létrehozása, amely megegyezik a laborkörnyezetbe felhasználók számát.
    
    > [!IMPORTANT]
    > Miután közzétette a sablont, nem lehet közzé nem tett. 
4. Váltás a **virtuális gépek** lapon, és ellenőrizze, hogy látható-e a virtuális gépek **hozzá nem rendelt** állapotát. Virtuális gépeken nincsenek hozzárendelve a diákok még. 

    ![Virtual machines (Virtuális gépek)](../media/tutorial-setup-classroom-lab/virtual-machines.png)
5. Várjon, amíg a virtuális gépek jönnek létre. A kell **leállítva** állapotát. Indítsa el a virtuális gép student, csatlakoztassa a virtuális Gépet, a virtuális gép leállítása és törlése a virtuális gép ezen a lapon. Ezen a lapon elindíthatja azokat, vagy lehetővé teszik a diákok indítsa el a virtuális gépeket. 

    ![A leállított állapotban lévő virtuális gépek](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="send-registration-link-to-students"></a>Regisztrációs hivatkozás küldése a diákoknak

1. Váltás a **irányítópult** nézet. 
2. Válassza a **Felhasználói regisztráció** csempét.

    ![A diákok regisztrációs hivatkozása](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. A **Felhasználói regisztráció** párbeszédablakban kattintson a **Másolás** gombra. A rendszer a vágólapra másolja a hivatkozást. Illessze be egy e-mail-szerkesztőbe, majd küldje el e-mailben a diákoknak. 

    ![A diákok regisztrációs hivatkozása](../media/tutorial-setup-classroom-lab/registration-link.png)
2. Az a **felhasználói regisztráció** párbeszédpanelen jelölje ki **Bezárás**. 

## <a name="view-all-classroom-labs"></a>Minden osztály labs megtekintése
1. Navigáljon a [Azure labor szolgáltatások portálja](https://labs.azure.com).
2. Ellenőrizze, hogy látható-e a kijelölt labor fiók összes labs-e. 

    ![Minden labs](../media/how-to-manage-classroom-labs/all-labs.png)
3. Felső a legördülő lista használatával jelöljön ki egy másik labor fiókot. Megjelenik a kijelölt labor fiók labs szolgáltatásának ismertetése. 

## <a name="delete-a-classroom-lab"></a>Egy osztályteremben tesztkörnyezet törlése
1. A a labor csempére válassza a három pontra (...) sarokban. 

    ![A tesztkörnyezet kiválasztása](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Válassza a **Törlés** elemet. 

    ![Gomb törlése](../media/how-to-manage-classroom-labs/delete-button.png)
3. Az a **Delete labor** párbeszédpanelen jelölje ki **törlése**. 

    ![Törlése párbeszédpanel](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 
## <a name="manage-student-vms"></a>Student virtuális gépek kezeléséhez
A diákok regisztrálása az Azure labor Services használata a regisztrációs összekapcsolása a diákok hozzárendelni a virtuális gépek látja el, feltéve ha az **virtuális gépek** fülre. 

![A diákok rendelt virtuális gépek](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

A következő feladatokat a virtuális gép student teheti meg: 

- Ha a virtuális gép fut, állítsa le a virtuális gépek. 
- Indítsa el a virtuális Gépet, a virtuális gép leállítása. 
- Csatlakozzon a virtuális géphez. 
- Törölje a virtuális Gépet. 

## <a name="next-steps"></a>További lépések
Kezdje meg egy tesztkörnyezet létrehozását az Azure Lab Services használatával:

- [Osztályterem-tesztkörnyezet beállítása](how-to-manage-classroom-labs.md)
- [Tesztkörnyezet beállítása](../tutorial-create-custom-lab.md)
