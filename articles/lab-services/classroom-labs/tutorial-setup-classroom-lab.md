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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 5bf8aea05855d81e88face1dd507f0006cc19cab
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73483889"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Oktatóanyag: Osztályterem-tesztkörnyezet beállítása 
Ebben az oktatóanyagban megtanulhatja, hogyan állíthat be egy diákok által használható virtuális gépekkel rendelkező osztályterem-tesztkörnyezetet.  

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Osztályterem-tesztkörnyezet létrehozása
> * Felhasználók hozzáadása a laborhoz
> * A laborhoz tartozó ütemterv beállítása
> * Meghívó e-mail küldése a tanulóknak

## <a name="prerequisites"></a>Előfeltételek
Ha Lab-fiókban szeretné beállítani a tantermi labort, akkor az egyik szerepkör tagjának kell lennie a labor-fiókban: tulajdonos, labor létrehozó vagy közreműködő. A rendszer automatikusan hozzáadja a tulajdonosi szerepkörhöz a labor-fiók létrehozásához használt fiókot.

A labor tulajdonosa más felhasználókat is hozzáadhat a **tesztkörnyezet létrehozói** szerepköréhez. Például a labor tulajdonosa hozzáadja a professzorokat a labor létrehozói szerepkörhöz. Ezután a professzorok létrehozzák a laborokat a virtuális gépekkel a saját osztályaik számára. A tanulók a professzorok által a laborba való regisztráláshoz használt regisztrációs hivatkozást használják. Ha regisztrálva vannak, a laborokban használhatnak virtuális gépeket az osztály munkahelyi és otthoni működéséhez. A felhasználók labor Creator szerepkörbe való felvételének részletes lépéseiért lásd: [felhasználó hozzáadása a labor létrehozói szerepkörhöz](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Osztályterem-tesztkörnyezet létrehozása

1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com). Vegye figyelembe, hogy az Internet Explorer 11 még nem támogatott. 
2. Válassza a **Bejelentkezés** lehetőséget, és adja meg a hitelesítő adatait. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 
3. Válassza az **új Labor**elemet. 
    
    ![Osztályterem-tesztkörnyezet létrehozása](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Az **Új tesztkörnyezet** ablakban tegye a következőket: 
    1. Adja meg a labor **nevét** , majd kattintson a **Tovább gombra**.  

        ![Osztályterem-tesztkörnyezet létrehozása](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        Ha Linux-rendszerképet választ, megjelenik egy lehetőség, amely lehetővé teszi a távoli asztali kapcsolat engedélyezését. Részletekért lásd: [Távoli asztali kapcsolat engedélyezése Linux](how-to-enable-remote-desktop-linux.md)rendszerhez.
    2. A **virtuális gép hitelesítő adatai** lapon a tesztkörnyezet összes virtuális gépe alapértelmezett hitelesítő adatait adhatja meg. Adja meg a felhasználó **nevét** és **jelszavát** , majd kattintson a **tovább**gombra.  

        ![Új tesztkörnyezet ablak](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Jegyezze fel a felhasználónevet és a jelszót, mert többször nem fognak megjelenni.
    3. A **labor-házirendek** lapon adja meg, hogy az egyes felhasználók számára hány óra legyen kiválasztva (az**egyes felhasználókra vonatkozó kvóta**) a tesztkörnyezet ütemezett idején kívül, majd válassza a **Befejezés**gombot. 

        ![Kvóta az egyes felhasználók számára](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Az alábbi képernyő jelenik meg, amely a sablon virtuális gépek létrehozásának állapotát jeleníti meg. A sablon létrehozása a tesztkörnyezetben akár 20 percig is eltarthat. 

    ![A sablon virtuális gép létrehozási állapota](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. A **sablon** oldalon hajtsa végre a következő lépéseket: ezek a lépések nem **kötelezőek** az oktatóanyaghoz.

    2. A **Csatlakozás** gomb kiválasztásával csatlakozzon a virtuálisgép-sablonhoz. Linux-sablonos virtuális gép esetén válassza ki, hogy SSH vagy RDP használatával szeretne-e csatlakozni (ha az RDP engedélyezve van).
    1. A virtuális gép jelszavának alaphelyzetbe állításához válassza a **jelszó alaphelyzetbe állítása** lehetőséget. 
    1. Telepítsen és konfiguráljon szoftvert a virtuálisgép-sablonon. 
    1. **Állítsa le** a virtuális gépet.  
    1. Adja meg a sablon **leírását**.
10. A **sablon** lapon válassza a **Közzététel** lehetőséget az eszköztáron. 

    ![Sablon közzététele gomb](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Közzététel után a lépés nem vonható vissza. 
8. A **sablon közzététele** lapon adja meg a laborban létrehozni kívánt virtuális gépek számát, majd válassza a **Közzététel**lehetőséget. 

    ![Sablon közzététele – virtuális gépek száma](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. A sablon **közzétételének állapota** az oldalon látható. Ez a folyamat akár egy órát is igénybe vehet. 

    ![Sablon közzétételének folyamata](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Váltson a **Virtual Machines Pool** lapra a bal oldali menüben, vagy válassza a virtuális gépek csempét. Győződjön meg arról, hogy a nem **hozzárendelt** állapotú virtuális gépek láthatók. Ezek a virtuális gépek még nincsenek diákokhoz rendelve. **Leállított** állapotban kell lenniük. Ezen a lapon indíthatja el a virtuális gépeket, csatlakozhat hozzájuk, leállíthatja, valamint törölheti őket. A virtuális gépeket elindíthatja ezen a lapon, vagy engedheti, hogy a diákjai indítsák el őket. 

    ![Leállított állapotban levő virtuális gépek](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Ezen a lapon a következő feladatokat hajthatja végre (ezeket a lépéseket nem kell végrehajtania az oktatóanyaghoz. Ezek a lépések csak az Ön adataira vonatkoznak.): 
    
    1. A labor kapacitásának módosításához (a laborban lévő virtuális gépek száma) válassza ki a **labor kapacitás** elemet az eszköztáron.
    2. Ha az összes virtuális gépet egyszerre szeretné elindítani, válassza az **összes elindítása** lehetőséget az eszköztáron. 
    3. Egy adott virtuális gép indításához válassza a lefelé mutató nyilat az **állapotban**, majd kattintson a **Start**gombra. Egy virtuális gépet úgy is elindíthat, ha kijelöl egy virtuális gépet az első oszlopban, majd kiválasztja az **Indítás** lehetőséget az eszköztáron.

## <a name="add-users-to-the-lab"></a>Felhasználók hozzáadása a laborhoz

1. Válassza a bal oldali menü **felhasználók** elemét. Alapértelmezés szerint a **hozzáférés korlátozása** beállítás engedélyezve van. Ha ez a beállítás be van kapcsolva, a felhasználók nem regisztrálhatnak a laborba még akkor sem, ha a felhasználó a felhasználók listáján szerepel. Csak a listán szereplő felhasználók regisztrálhatnak a laborba az Ön által küldött regisztrációs hivatkozás használatával. Ebben az eljárásban felhasználókat vesz fel a listára. Azt is megteheti, hogy kikapcsolja a **hozzáférés korlátozása**lehetőséget, amely lehetővé teszi a felhasználók számára, hogy regisztráljanak a laborban, amennyiben rendelkeznek a regisztrációs hivatkozással. 
2. Válassza a **felhasználók hozzáadása** lehetőséget az eszköztáron, majd válassza a **Hozzáadás e-mail-címek alapján**lehetőséget. 

    ![Felhasználók hozzáadása gomb](../media/how-to-configure-student-usage/add-users-button.png)
1. A **felhasználók hozzáadása** lapon adja meg a felhasználók e-mail-címeit külön sorokban, vagy egyetlen sorban pontosvesszővel elválasztva. 

    ![Felhasználói e-mail-címek hozzáadása](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Kattintson a **Mentés** gombra. A listában megjelenik a felhasználók e-mail-címe és állapota (regisztrált vagy nem). 

    ![Felhasználók listája](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-a-schedule-for-the-lab"></a>A laborhoz tartozó ütemterv beállítása
Hozzon létre egy ütemezett eseményt a laborhoz, hogy a laborban lévő virtuális gépek meghatározott időpontokban automatikusan elindulnak/leállnak. A korábban megadott felhasználói kvóta az egyes felhasználók számára az ütemezett időponton kívül hozzárendelt további idő. 

1. Váltson az **ütemezések** lapra, és válassza az eszköztár **ütemezett esemény hozzáadása** elemét. 

    ![Ütemterv hozzáadása gomb az ütemtervek lapon](../media/how-to-create-schedules/add-schedule-button.png)
2. Győződjön meg arról, hogy az **esemény típusaként**a **standard** van kiválasztva. A **csak indítás** gombra kattintva adhatja meg a virtuális gépek kezdési idejét. Ha csak a virtuális gépek leállítási idejét szeretné megadni, válassza a **Leállítás** lehetőséget. 
3. Az **ismétlés** szakaszban válassza ki az aktuális ütemtervet. 

    ![Ütemterv hozzáadása gomb az ütemtervek lapon](../media/how-to-create-schedules/select-current-schedule.png)
4. Az ütemterv kiválasztásával megnyílik a **REPEAT (ismétlés** ) párbeszédpanel. Ezen a párbeszédpanelen hajtsa végre a következő lépéseket:
    1. Győződjön meg arról, hogy **minden héten** be van állítva az **ismétlés** mező. 
    3. A **kezdő dátum**meghatározása.
    4. Itt adhatja meg azt a **kezdési időpontot** , amikor a virtuális gépeket el szeretné indítani.
    5. Itt adhatja meg a **leállítási időt** , amikor a virtuális gépeket le kell állítani. 
    6. Adja meg az **időzónát** a megadott kezdési és befejezési időpontnál. 
    2. Válassza ki azokat a napokat, amelyeknek érvénybe szeretné venni az ütemtervet. A következő példában a hétfő-csütörtök van kiválasztva. 
    8. Kattintson a **Mentés** gombra. 

5. Az **ütemezett esemény hozzáadása** lapon a **Megjegyzések (nem kötelező)** mezőben adja meg az ütemezés leírását vagy megjegyzéseit. 
6. Az **ütemezett esemény hozzáadása** lapon válassza a **Mentés**lehetőséget. 

    ![Heti ütemterv](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="send-invitation-emails-to-students"></a>Meghívó e-mailek küldése a tanulóknak

1. Váltson a **felhasználók** nézetre, ha már nincs a lapon, és válassza az **összes meghívása** lehetőséget az eszköztáron. 

    ![Tanulók kiválasztása](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. A **Meghívás küldése e-mailben** lapon adjon meg egy opcionális üzenetet, majd válassza a **Küldés**lehetőséget. Az e-mail automatikusan tartalmazza a regisztrációs hivatkozást. Ezt a regisztrációs hivatkozást a következő parancs kiválasztásával érheti el: **... (három pont)** az eszköztáron és a **regisztrációs hivatkozáson**. 

    ![Regisztrációs hivatkozás küldése e-mailben](../media/tutorial-setup-classroom-lab/send-email.png)
4. A **meghívás** állapota megjelenik a **felhasználók** listájában. Az állapotnak a **Küldés** gombra kell váltania, majd **&lt;dátummal kell elküldenie&gt;** . 

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy osztályterem-tesztkörnyezetet, és konfigurálta azt. Ha meg szeretné tudni, hogyan férhetnek hozzá a diákok a tesztkörnyezet virtuális gépeihez a regisztrációs hivatkozással, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Kapcsolódás az osztályterem-tesztkörnyezet virtuális gépeihez](tutorial-connect-virtual-machine-classroom-lab.md)

