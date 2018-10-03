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
ms.date: 10/01/2018
ms.author: spelluru
ms.openlocfilehash: eebdc98db5ecdf518d3b0b58e6757a2b7ecd5dd7
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48043804"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Az Azure Lab Services osztályterem-tesztkörnyezetek kezelése 
Ez a cikk bemutatja, hogyan hozzon létre és osztályterem-tesztkörnyezet konfigurálása, megtekintheti az összes osztályterem-tesztkörnyezetek vagy osztályterem-tesztkörnyezet törlése.

## <a name="prerequisites"></a>Előfeltételek
A **Tesztkörnyezet-létrehozó** szerepkörrel kell rendelkeznie a tesztkörnyezetfiókban ahhoz, hogy létrehozhasson egy osztályterem-tesztkörnyezetet. A tesztkörnyezetfiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. A tesztkörnyezet tulajdonosa a [felhasználó a Tesztkörnyezet-létrehozó szerepkörhöz történő hozzáadását](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) ismertető cikkben leírt lépésekkel adhat hozzá további felhasználókat a Tesztkörnyezet-létrehozó szerepkörhöz.

## <a name="create-a-classroom-lab"></a>Osztályterem-tesztkörnyezet létrehozása

1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com). 
2. Válassza a **Bejelentkezés** lehetőséget, és adja meg a hitelesítő adatait. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 
3. Az **Új tesztkörnyezet** ablakban tegye a következőket: 
    1. Adjon meg egy **neve** a tesztkörnyezethez. 
    2. Adja meg a maximális **felhasználók száma,** engedélyezett a laborkörnyezetbe. 
    6. Kattintson a **Mentés** gombra.

        ![Osztályterem-tesztkörnyezet létrehozása](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Az a **válassza ki a virtuálisgép-specifikációk** lapon, tegye a következőket:
    1. Válassza ki a **mérete** a lab-ben létrehozott virtuális gépek (VM). 
    2. Válassza ki a **régió** a amely szeretné létrehozni a virtuális gépek. 
    3. Válassza ki a **Virtuálisgép-lemezkép** hozhat létre virtuális gépeket a laborban használt. 
    4. Kattintson a **Tovább** gombra.

        ![Adja meg a Virtuálisgép-specifikációk](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Az a **hitelesítő adatok beállítása a** csoportjában adja meg az alapértelmezett hitelesítő adatok az összes virtuális gép a tesztkörnyezetben. 
    1. Adja meg a **felhasználó nevét** az összes virtuális gép a tesztkörnyezetben.
    2. Adja meg a **jelszó** a felhasználó számára. 

        > [!IMPORTANT]
        > Jegyezze fel a felhasználónevet és jelszót. Ezek nem jelennek meg újra.
    3. Kattintson a **Létrehozás** gombra. 

        ![Hitelesítő adatok beállítása](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Az a **konfigurálása sablon** lapon tesztkörnyezet létrehozásának folyamatát állapotának megtekintéséhez. A sablon a labor létrehozása akár 20 percet vesz igénybe. 

    ![Sablon konfigurálása](../media/tutorial-setup-classroom-lab/configure-template.png)
7. A sablon a konfiguráció befejezését követően a következő oldal jelenik meg: 

    ![Sablon lapjának konfigurálása, Miután befejeződött](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Az alábbi lépéseket ebben az oktatóanyagban választható lépésben történik: 
    1. Indítsa el a Virtuálisgép-sablon kiválasztásával **Start**.
    2. Csatlakozás a virtuális gép sablon kiválasztásával **Connect**. 
    3. Telepítse és konfigurálja a szoftver a Virtuálisgép-sablon. 
    4. **Állítsa le** a virtuális Gépet.  
    5. Adjon meg egy **leírás** a sablon

        ![Ezután a konfigurálás lapot](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. Válassza ki **tovább** a sablon oldalon. 
10. A **a sablon közzététele** lapon, tegye a következőket. 
    1. A sablon azonnali közzétételének tartozó jelölőnégyzet bejelölésével *nem módosítsa a sablon közzététele után tisztában vagyok. Ez a folyamat kizárólag valósítható meg egyszer, és a egy óráig is eltarthat*, és válassza ki **közzététel**.  

        > [!WARNING]
        > Miután tesz közzé, amelyet nem közzétételének visszavonása. 
    2. Később kattintson **későbbi használatra mentse**. A varázsló befejezése után a Virtuálisgép-sablon teheti közzé. Hogyan konfigurálása és közzététele a varázsló befejezése után, konfigurálása és közzététele a varázsló befejezése után információt talál a részletekért lásd: [a sablon közzététele](#publish-the-template) című rész a [osztályterem-tesztkörnyezetek kezelése ](how-to-manage-classroom-labs.md) cikk.

        ![Sablon közzététele](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Megjelenik a **közzétételi állapotát** a sablont. Ez a folyamat akár is igénybe vehet egy óra. 

    ![Sablon - folyamat közzététele](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. A következő oldal jelenik meg, ha a sablon közzététele sikerült. Válassza a **Done** (Kész) lehetőséget.

    ![Sablon – sikeres közzététel](../media/tutorial-setup-classroom-lab/publish-success.png)
1. A rendszer megjeleníti a tesztkörnyezet **irányítópultját**. 
    
    ![Az osztályterem-tesztkörnyezet irányítópultja](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Váltson a **Virtuális gépek** lapra, és ellenőrizze, hogy vannak-e ott **Nem hozzárendelt** állapotú virtuális gépek. Ezek a virtuális gépek még nincsenek diákokhoz rendelve. **Leállított** állapotban kell lenniük. Ezen a lapon indíthatja el a virtuális gépeket, csatlakozhat hozzájuk, leállíthatja, valamint törölheti őket. A virtuális gépeket elindíthatja ezen a lapon, vagy engedheti, hogy a diákjai indítsák el őket. 

    ![Leállított állapotban levő virtuális gépek](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

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


## <a name="publish-the-template"></a>A sablon közzététele 
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
 
## <a name="manage-student-vms"></a>Diákok virtuális gépeinek felügyelete
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
