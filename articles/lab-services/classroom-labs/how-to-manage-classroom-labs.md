---
title: Az Azure Lab Services osztályterem-tesztkörnyezetek kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre és konfigurálja az osztályterem-tesztkörnyezet, megtekintheti az összes az osztályterem-tesztkörnyezetek, a regisztráció hivatkozásra a lab-felhasználó vagy törlése a labor fájlmegosztás.
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
ms.openlocfilehash: 48056d6e2988dd674351aca83526032175c355b6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214392"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Az Azure Lab Services osztályterem-tesztkörnyezetek kezelése 
Ez a cikk bemutatja, hogyan hozzon létre és osztályterem-tesztkörnyezet konfigurálása, megtekintheti az összes osztályterem-tesztkörnyezetek vagy osztályterem-tesztkörnyezet törlése.

## <a name="prerequisites"></a>Előfeltételek
A **Tesztkörnyezet-létrehozó** szerepkörrel kell rendelkeznie a tesztkörnyezetfiókban ahhoz, hogy létrehozhasson egy osztályterem-tesztkörnyezetet. A lab-fiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. A labor tulajdonosa adhat hozzá más felhasználókat a tesztkörnyezet létrehozója szerepkör című cikkben ismertetett lépések segítségével: [felhasználó hozzáadása a tesztkörnyezet létrehozója szerepkör](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Osztályterem-tesztkörnyezet létrehozása

1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com).
2. Válassza a **Bejelentkezés** lehetőséget, és adja meg a hitelesítő adatait. Az Azure Lab Services támogatja a szervezeti fiókokat és a Microsoft-fiókok.
3. Az **Új tesztkörnyezet** ablakban tegye a következőket: 
    1. Adja meg az osztályterem-tesztkörnyezet **nevét**. 
    2. Válassza ki az osztályteremben használni kívánt virtuális gép **méretét**.
    3. Válassza ki a virtuális gép létrehozásához használandó **rendszerképet**.
    4. Adja meg a **alapértelmezett hitelesítő adatok** használandó jelentkezzen be a virtuális gépek a tesztkörnyezetben.
    7. Kattintson a **Mentés** gombra.

        ![Osztályterem-tesztkörnyezet létrehozása](../media/how-to-manage-classroom-labs/new-lab-window.png)
1. A rendszer megjeleníti a tesztkörnyezet **irányítópultját**. 
    
    ![Az osztályterem-tesztkörnyezet irányítópultja](../media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Használati szabályzat konfigurálása

1. Válassza a **Használati szabályzat** lehetőséget. 
2. A **Használati szabályzat** terület beállításaiban adja meg, **hány felhasználó** használhatja a tesztkörnyezetet.
3. Kattintson a **Mentés** gombra. 

    ![Használati szabályzat](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Sablon beállítása
A tesztkörnyezet sablonja egy alapszintű virtuálisgép-rendszerkép, amelyből az összes felhasználó virtuális gépe létrejön. Úgy állítsa be a virtuálisgép-sablont, hogy az pontosan aszerint legyen konfigurálva, amit a tesztkörnyezet felhasználóinak biztosítani kíván. A sablonhoz megadhat egy nevet és egy leírást, amely a tesztkörnyezet felhasználói számára jelenik meg. A sablon közzétételével a tesztkörnyezet felhasználói számára elérhető példányokat hozhat létre a virtuálisgép-sablonról.  

### <a name="set-template-title-and-description"></a>Set-sablon nevét és leírását
1. A **Sablon** szakaszban válassza a sablonhoz tartozó **Szerkesztés** (ceruza ikon) elemet. 
2. A **Felhasználói nézet** ablakban adja meg a sablon **címét**.
3. Adja meg a sablon **leírását**.
4. Kattintson a **Mentés** gombra.

    ![Az osztályterem-tesztkörnyezet leírása](../media/how-to-manage-classroom-labs/lab-description.png)

### <a name="set-up-the-template-vm"></a>Virtuálisgép-sablon beállítása
 A virtuálisgép-sablonhoz csatlakozva telepítse a szükséges szoftvereket, mielőtt elérhetővé tenné a sablont a diákok számára. 

1. Várja meg, amíg elkészül a virtuálisgép-sablon. Ha elkészült, elérhetővé válik a **Start** gomb. A virtuális gép elindításához kattintson a **Start** gombra.

    ![A virtuálisgép-sablon elindítása](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. A virtuális géphez való csatlakozáshoz kattintson a **Csatlakozás** gombra, majd kövesse az utasításokat. 

    ![Csatlakozás a virtuálisgép-sablonhoz](../media/tutorial-setup-classroom-lab/connect-template-vm.png)
1. Telepítse a diákok számára a tesztkörnyezet használatához szükséges szoftvereket (például Visual Studio, Azure Storage Explorer stb). 
2. Szakítsa meg a kapcsolatot a virtuálisgép-sablonnal (zárja be a távoli asztali munkamenetet). 
3. **Állítsa le** a virtuálisgép-sablont a **Leállítás** kiválasztásával. 

    ![A virtuálisgép-sablon leállítása](../media/tutorial-setup-classroom-lab/stop-template-vm.png)


### <a name="publish-the-template"></a>A sablon közzététele 
Amikor közzétesz egy sablont, az Azure Lab Services létrehozza a virtuális gépeket a tesztkörnyezetben a sablon használatával. A folyamat során létrehozott virtuális gépek száma megegyezik a tesztkörnyezet felhasználóinak maximális számával, amelyet a tesztkörnyezet használati szabályzatában állíthat be. A virtuális gépek konfigurációja megegyezik a sablonéval. 

1. A **Sablon** szakaszban válassza a **Közzététel** lehetőséget. 

    ![A virtuálisgép-sablon közzététele](../media/tutorial-setup-classroom-lab/public-access.png)
1. A **Közzététel** ablakban válassza a **Közzétett** lehetőséget. 
2. Most kattintson a **Közzététel** gombra. Ez a folyamat eltarthat egy ideig a létrehozandó virtuális gépek számától függően, amely a tesztkörnyezet felhasználóinak maximális számával egyezik meg.
    
    > [!IMPORTANT]
    > Miután a sablon közzé lett téve, a közzétételt nem lehet visszavonni. 
4. Váltson a **Virtuális gépek** lapra, és ellenőrizze, hogy vannak-e ott **Nem hozzárendelt** állapotú virtuális gépek. Ezek a virtuális gépek még nincsenek diákokhoz rendelve. 

    ![Virtual machines (Virtuális gépek)](../media/tutorial-setup-classroom-lab/virtual-machines.png)
5. Várja meg, amíg befejeződik a virtuális gépek létrehozása. **Leállított** állapotban kell lenniük. Ezen a lapon indíthatja el a virtuális gépeket, csatlakozhat hozzájuk, leállíthatja, valamint törölheti őket. A virtuális gépeket elindíthatja ezen a lapon, vagy engedheti, hogy a diákjai indítsák el őket. 

    ![Leállított állapotban levő virtuális gépek](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="send-registration-link-to-students"></a>Regisztrációs hivatkozás küldése a diákoknak

1. Váltson az **Irányítópult** nézetre. 
2. Válassza a **Felhasználói regisztráció** csempét.

    ![A diákok regisztrációs hivatkozása](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. A **Felhasználói regisztráció** párbeszédablakban kattintson a **Másolás** gombra. A rendszer a vágólapra másolja a hivatkozást. Illessze be egy e-mail-szerkesztőbe, majd küldje el e-mailben a diákoknak. 

    ![A diákok regisztrációs hivatkozása](../media/tutorial-setup-classroom-lab/registration-link.png)
2. A **Felhasználói regisztráció** párbeszédablakban kattintson a **Bezárás** gombra. 

## <a name="view-all-classroom-labs"></a>Osztályterem-tesztkörnyezetek megtekintése
1. Navigáljon a [Azure Lab Services portálon](https://labs.azure.com).
2. Győződjön meg arról, hogy megjelenik-e a kiválasztott labor-fiókban lévő összes labs. 

    ![Az összes labs](../media/how-to-manage-classroom-labs/all-labs.png)
3. Az oldal tetején a legördülő lista segítségével válassza ki a különböző tesztkörnyezetfiók. A kiválasztott labor fiókban labs láthatja. 

## <a name="delete-a-classroom-lab"></a>Osztályterem-tesztkörnyezet törlése
1. A tesztkörnyezethez a csempére válassza a három pontra (...) sarokban. 

    ![A tesztkörnyezet kiválasztása](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Válassza a **Törlés** elemet. 

    ![Törlés gomb](../media/how-to-manage-classroom-labs/delete-button.png)
3. Az a **Delete lab** párbeszédpanelen jelölje ki **törlése**. 

    ![Törlése párbeszédpanel](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 
## <a name="manage-student-vms"></a>Tanulói virtuális gépek kezelése
Miután tanulók regisztrálása az Azure Lab Services segítségével a regisztráció társítani a megadott, a tanulók a hozzárendelt virtuális gépeket láthatja a **virtuális gépek** fülre. 

![Diákok rendelt virtuális gépek](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

A következő feladatokat a virtuális gép tanuló teheti meg: 

- Virtuális gép leállítása, ha a virtuális gép fut-e. 
- Virtuális gép elindítása, ha a virtuális gép le van állítva. 
- Csatlakozzon a virtuális géphez. 
- Törölje a virtuális Gépet. 

## <a name="next-steps"></a>További lépések
Kezdje meg egy tesztkörnyezet létrehozását az Azure Lab Services használatával:

- [Osztályterem-tesztkörnyezet beállítása](how-to-manage-classroom-labs.md)
- [Tesztkörnyezet beállítása](../tutorial-create-custom-lab.md)
