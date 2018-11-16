---
title: Osztályterem-tesztkörnyezet beállítása az Azure Lab Services szolgáltatással | Microsoft Docs
description: Ebben az oktatóanyagban megtanulhatja, hogyan állíthat be egy tesztkörnyezetet osztálytermi használatra.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/14/2018
ms.author: spelluru
ms.openlocfilehash: babff55d6684feb1f0414970616260be96b994f4
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706007"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Oktatóanyag: Osztályterem-tesztkörnyezet beállítása 
Ebben az oktatóanyagban megtanulhatja, hogyan állíthat be egy diákok által használható virtuális gépekkel rendelkező osztályterem-tesztkörnyezetet.  

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Osztályterem-tesztkörnyezet létrehozása
> * Az osztályterem-tesztkörnyezet konfigurálása
> * Regisztrációs hivatkozás küldése a diákoknak

## <a name="prerequisites"></a>Előfeltételek
A **Tesztkörnyezet-létrehozó** szerepkörrel kell rendelkeznie a tesztkörnyezetfiókban ahhoz, hogy létrehozhasson egy osztályterem-tesztkörnyezetet. A tesztkörnyezetfiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. A tesztkörnyezet tulajdonosa a [felhasználó a Tesztkörnyezet-létrehozó szerepkörhöz történő hozzáadását](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) ismertető cikkben leírt lépésekkel adhat hozzá további felhasználókat a Tesztkörnyezet-létrehozó szerepkörhöz.


## <a name="create-a-classroom-lab"></a>Osztályterem-tesztkörnyezet létrehozása

1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com). 
2. Válassza a **Bejelentkezés** lehetőséget, és adja meg a hitelesítő adatait. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 
3. Az **Új tesztkörnyezet** ablakban tegye a következőket: 
    1. Adjon egy **nevet** a tesztkörnyezetnek. 
    2. Adja meg azoknak a **felhasználóknak a maximális számát**, akik használhatják a tesztkörnyezetet. 
    6. Kattintson a **Mentés** gombra.

        ![Osztályterem-tesztkörnyezet létrehozása](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. A **virtuális gép specifikációinak kiválasztására** szolgáló lapon hajtsa végre a következőket:
    1. Válasszon **méretet** a tesztkörnyezetben létrehozott virtuális gépeknek. 
    2. Válassza ki a **régiót**, amelyben létre szeretné hozni a virtuális gépeket. 
    3. Válassza ki a tesztkörnyezetben a virtuális gépek létrehozásához használni kívánt **virtuálisgép-rendszerképet**. 
    4. Kattintson a **Tovább** gombra.

        ![Virtuális gép specifikációinak megadása](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. A **Hitelesítő adatok beállítása** oldalon adja meg az alapértelmezett hitelesítő adatokat a tesztkörnyezet összes virtuális gépéhez. 
    1. Adja meg a **felhasználónevet** a tesztkörnyezet összes virtuális gépéhez.
    2. Adja meg a felhasználó **jelszavát**. 

        > [!IMPORTANT]
        > Jegyezze fel a felhasználónevet és a jelszót, mert többször nem fognak megjelenni.
    3. Kattintson a **Létrehozás** gombra. 

        ![Hitelesítő adatok beállítása](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. A **Sablon konfigurálása** oldalon látható a tesztkörnyezet létrehozási folyamatának állapota. A sablon létrehozása a tesztkörnyezetben akár 20 percig is eltarthat. 

    ![Sablon konfigurálása](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Miután a sablon konfigurálása befejeződött, az alábbi oldal jelenik meg: 

    ![Sablon konfigurálása oldal a folyamat befejeződése után](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Az oktatóanyag alábbi lépéseit nem kötelező végrehajtani: 
    1. Az **Indítás** gomb kiválasztásával indítsa el a virtuálisgép-sablont.
    2. A **Csatlakozás** gomb kiválasztásával csatlakozzon a virtuálisgép-sablonhoz. 
    3. Telepítsen és konfiguráljon szoftvert a virtuálisgép-sablonon. 
    4. **Állítsa le** a virtuális gépet.  
    5. Adja meg a sablon **leírását**.

        ![A Tovább lépés a Sablon konfigurálása oldalon](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. Válassza ki a **Tovább** gombot a sablon oldalán. 
10. A **Sablon közzététele** oldalon tegye az alábbiakat. 
    1. A sablon azonnali közzétételéhez jelölje be a következő figyelmeztetés jelölőnégyzetét: *Megértettem, hogy közzététel után nem lehet módosítani a sablont. A folyamatot csak egyszer lehet elvégezni, és akár egy órát is igénybe vehet*, majd válassza ki a **Közzététel** gombot.  

        > [!WARNING]
        > Közzététel után a lépés nem vonható vissza. 
    2. Későbbi közzétételhez kattintson a **Mentés későbbre** gombra. A varázsló futtatása után közzéteheti a virtuálisgép-sablont. Ha tudni szeretné, hogyan konfigurálhatja és teheti közzé a sablont a varázsló futtatása után, tekintse át a [sablon közzétételével](how-to-create-manage-template.md#publish-the-template-vm) foglalkozó szakaszt az [osztálytermi tesztkörnyezetek kezelését](how-to-manage-classroom-labs.md) ismertető cikkben.

        ![Sablon közzététele](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Nyomon követheti, hogy **hol tart a sablon közzététele**. Ez a folyamat akár egy órát is igénybe vehet. 

    ![Sablon közzétételének folyamata](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Miután a sablon közzététele sikeresen befejeződött, az alábbi oldalt fogja látni. Válassza a **Done** (Kész) lehetőséget.

    ![Sablon sikeres közzététele](../media/tutorial-setup-classroom-lab/publish-success.png)
1. A rendszer megjeleníti a tesztkörnyezet **irányítópultját**. 
    
    ![Az osztályterem-tesztkörnyezet irányítópultja](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Váltson a **Virtuális gépek** lapra, és ellenőrizze, hogy vannak-e ott **Nem hozzárendelt** állapotú virtuális gépek. Ezek a virtuális gépek még nincsenek diákokhoz rendelve. **Leállított** állapotban kell lenniük. Ezen a lapon indíthatja el a virtuális gépeket, csatlakozhat hozzájuk, leállíthatja, valamint törölheti őket. A virtuális gépeket elindíthatja ezen a lapon, vagy engedheti, hogy a diákjai indítsák el őket. 

    ![Leállított állapotban levő virtuális gépek](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="add-users-to-the-lab"></a>Felhasználók hozzáadása a labor létrehozása

1. Válassza ki **felhasználók** a bal oldali menüben. Alapértelmezés szerint a **hozzáférés korlátozása** beállítás engedélyezve van. Ha ez a beállítás ki kapcsolva, a felhasználó nem regisztrálja a labor létrehozása akkor is, ha a felhasználó nem rendelkezik a regisztráció hivatkozásra, ha a felhasználó szerepel a felhasználók listáját. Csak a listán szereplő felhasználók regisztrálhatja az a labor létrehozása a regisztrációs hivatkozással küld. Ezzel az eljárással, hozzá felhasználókat a listából. Másik megoldásként kikapcsolhatja a **hozzáférés korlátozása**, lehetővé teszi a felhasználók is rendelkeznek a regisztráció hivatkozásra a labor regisztrálni. 
2. Válassza ki **felhasználók hozzáadása** az eszköztáron. 
3. Az a **felhasználók hozzáadása** lap, adja meg a felhasználók e-mail címét, külön sorban, vagy egyetlen sorban, egymástól pontosvesszővel elválasztva. 

    ![Adja hozzá a felhasználó e-mail-címei](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Kattintson a **Mentés** gombra. Láthatja, hogy a felhasználók és azok állapotát (vagy nem regisztrált) a lista e-mail címét. 

    ![Felhasználók listája](../media/how-to-configure-student-usage/users-list-new.png)


## <a name="send-registration-link-to-students"></a>Regisztrációs hivatkozás küldése a diákoknak

1. Váltson a **felhasználók** megtekintheti, ha nem az oldalon már. 
2. Válassza ki **Get-regisztrációs hivatkozást** csempére.

    ![A diákok regisztrációs hivatkozása](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. A **Felhasználói regisztráció** párbeszédablakban kattintson a **Másolás** gombra. A rendszer a vágólapra másolja a hivatkozást. 

    ![A diákok regisztrációs hivatkozása](../media/tutorial-setup-classroom-lab/registration-link.png)
2. A **Felhasználói regisztráció** párbeszédablakban kattintson a **Bezárás** gombra. 
4. Ossza meg a regisztrációs hivatkozást egy diákkal, hogy a diák regisztrálhasson az osztályba. Ha rendelkezik a **korlátozása beállítást** beállítás engedélyezve van, és felhasználók listája szerepel a listában, a következő műveleteket hajthatja végre:
    1. Válassza ki a **e-mail-cím** a felhasználó a listán. 
    2. Megjelenik egy ablak az alapértelmezett e-mail programból, az a **TO** cím kitölti a rendszer. 
    3. Illessze be a **regisztrációs URL-cím** korábban vágólapra másolt. 
    4. Küldés a **e-mail**.


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy osztályterem-tesztkörnyezetet, és konfigurálta azt. Ha meg szeretné tudni, hogyan férhetnek hozzá a diákok a tesztkörnyezet virtuális gépeihez a regisztrációs hivatkozással, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Kapcsolódás az osztályterem-tesztkörnyezet virtuális gépeihez](tutorial-connect-virtual-machine-classroom-lab.md)

