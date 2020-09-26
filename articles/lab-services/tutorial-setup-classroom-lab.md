---
title: Osztályterem-tesztkörnyezet beállítása az Azure Lab Services szolgáltatással | Microsoft Docs
description: Ebben az oktatóanyagban a Azure Lab Services használatával állít be egy tantermi labort olyan virtuális gépekkel, amelyeket az osztályban tanulók használnak.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: 1155646d31ddb8a0a3abce025acde5c4cb645f54
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336734"
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
Ebben az oktatóanyagban egy labort állít be az osztályhoz tartozó virtuális gépekkel. Ha Lab-fiókban szeretné beállítani a tantermi labort, akkor az egyik szerepkör tagjának kell lennie a labor-fiókban: tulajdonos, labor létrehozó vagy közreműködő. A rendszer automatikusan hozzáadja a tulajdonosi szerepkörhöz a labor-fiók létrehozásához használt fiókot. Így használhatja azt a felhasználói fiókot, amellyel létrehoz egy Lab-fiókot egy osztályterem labor létrehozásához. 

A Azure Lab Services használata esetén a tipikus munkafolyamat:

1. A labor-fiók létrehozója más felhasználókat is felvesz a **labor létrehozói** szerepkörbe. Például a labor-fiók létrehozója/rendszergazdája felveszi az oktatókat a **labor létrehozói** szerepkörbe, hogy a laborokat a saját osztályaik számára is létrehozzák. 
2. Ezután a pedagógusok létrehozhatnak virtuális gépeket a saját osztályaik számára, és regisztrációs hivatkozásokat küldhetnek a tanulóknak az osztályban. 
3. A tanulók az oktatóktól kapott regisztrációs hivatkozást használják a laborba való regisztrációhoz. Ha regisztrálva vannak, a laborokban használhatnak virtuális gépeket az osztály munkahelyi és otthoni működéséhez. 

## <a name="create-a-classroom-lab"></a>Osztályterem-tesztkörnyezet létrehozása
Ebben a lépésben létrehoz egy labort az osztályhoz az Azure-ban. 

1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com). Vegye figyelembe, hogy az Internet Explorer 11 még nem támogatott. 
2. Válassza a **Bejelentkezés** lehetőséget, és adja meg a hitelesítő adatait. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 
3. Válassza az **új Labor**elemet. 
    
    ![Képernyőfelvétel: "Azure Lab Services", az "új Labor" gomb kiválasztásával.](./media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Az **Új tesztkörnyezet** ablakban tegye a következőket: 
    1. Adja meg a labor **nevét** , majd kattintson a **Tovább gombra**.  

        ![Osztályterem-tesztkörnyezet létrehozása](./media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. A **virtuális gép hitelesítő adatai** lapon a tesztkörnyezet összes virtuális gépe alapértelmezett hitelesítő adatait adhatja meg. Adja meg a felhasználó **nevét** és **jelszavát** , majd kattintson a **tovább**gombra.  

        ![Új tesztkörnyezet ablak](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Jegyezze fel a felhasználónevet és a jelszót, mert többször nem fognak megjelenni.
    3. A **labor-házirendek** lapon válassza a **Befejezés**lehetőséget. 

        ![Kvóta az egyes felhasználók számára](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Az alábbi képernyő jelenik meg, amely a sablon virtuális gépek létrehozásának állapotát jeleníti meg. A művelet akár 20 percet is igénybe vehet. 

    ![A sablon virtuális gép létrehozási állapota](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. A **sablon** oldalon hajtsa végre a következő lépéseket: ezek a lépések nem **kötelezőek** az oktatóanyaghoz.

    1. A **Csatlakozás** gomb kiválasztásával csatlakozzon a virtuálisgép-sablonhoz. Linux-sablonos virtuális gép esetén válassza ki, hogy SSH vagy RDP használatával szeretne-e csatlakozni (ha az RDP engedélyezve van).
    3. Telepítse és konfigurálja az osztályhoz szükséges szoftvereket a sablon virtuális gépén. 
    4. **Állítsa le** a sablon virtuális gépet.  

    > [!NOTE]
    > A sablon virtuális gépei a futtatáskor **költségekkel** járnak, ezért győződjön meg arról, hogy a sablon virtuális gépe leáll, ha nincs szükség a futtatására. 

## <a name="publish-the-template-vm"></a>A virtuálisgép-sablon közzététele
Ebben a lépésben közzéteszi a sablon virtuális gépet. Amikor közzéteszi a sablon virtuális gépet, Azure Lab Services létrehozza a virtuális gépeket a laborban a sablon használatával. A virtuális gépek konfigurációja megegyezik a sablonéval.

1. A **sablon** lapon válassza a **Közzététel** lehetőséget az eszköztáron. 

    ![Sablon közzététele gomb](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Közzététel után a lépés nem vonható vissza. 
2. A **sablon közzététele** lapon adja meg a laborban létrehozni kívánt virtuális gépek számát, majd válassza a **Közzététel**lehetőséget. 

    ![Sablon közzététele – virtuális gépek száma](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. A sablon **közzétételének állapota** az oldalon látható. Ez a folyamat akár egy órát is igénybe vehet. 

    ![Sablon közzétételének folyamata](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Várjon, amíg a közzététel befejeződik, majd váltson a **Virtual Machines Pool** lapra, ehhez válassza a bal oldali menüben a **virtuális gépek** lehetőséget, vagy a **virtuális gépek** csempét. Győződjön meg arról, hogy a nem **hozzárendelt** állapotú virtuális gépek láthatók. Ezek a virtuális gépek még nincsenek diákokhoz rendelve. **Leállított** állapotban kell lenniük. Ezen a lapon indíthatja el a virtuális gépeket, csatlakozhat hozzájuk, leállíthatja, valamint törölheti őket. A virtuális gépeket elindíthatja ezen a lapon, vagy engedheti, hogy a diákjai indítsák el őket. 

    ![Leállított állapotban levő virtuális gépek](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)   

    > [!NOTE]
    > Ha egy oktató bekapcsol egy tanulói virtuális gépet, az nem érinti a tanulóra vonatkozó kvótát. A felhasználóhoz tartozó kvóta meghatározza, hogy a felhasználó számára hány labor óra legyen elérhető az ütemezett osztály időpontján kívül. A kvótákkal kapcsolatos további információkért lásd: [kvóták beállítása a felhasználók](how-to-configure-student-usage.md?#set-quotas-for-users)számára.

## <a name="set-a-schedule-for-the-lab"></a>A laborhoz tartozó ütemterv beállítása
Hozzon létre egy ütemezett eseményt a laborhoz, hogy a laborban lévő virtuális gépek meghatározott időpontokban automatikusan elindulnak/leállnak. A korábban megadott felhasználói kvóta (alapértelmezett: 10 óra) az egyes felhasználók számára az ütemezett időponton kívül hozzárendelt további idő. 

1. Váltson az **ütemezések** lapra, és válassza az eszköztár **ütemezett esemény hozzáadása** elemét. 

    ![Képernyőkép az "ütemezett esemény hozzáadása" gombra az "ütemezések" lapon.](./media/how-to-create-schedules/add-schedule-button.png)
2. Az **ütemezett esemény hozzáadása** oldalon hajtsa végre a következő lépéseket:
    1. Ellenőrizze, hogy a **standard** érték van-e kiválasztva az **esemény típusára**.  
    2. Válassza ki az osztály **kezdő dátumát** . 
    4. Válassza ki a **kezdési időpontot** , amikor a virtuális gépeket el szeretné indítani.
    5. Válassza ki a **leállítási időt** , amikor a virtuális gépeket le kell állítani. 
    6. Válassza ki az **időzónát** a megadott kezdési és befejezési időponthoz. 
3. Az **ütemezett esemény hozzáadása** lapon válassza ki az aktuális ütemezést az **ismétlés** szakaszban.  

    ![Ütemterv hozzáadása gomb az ütemtervek lapon](./media/how-to-create-schedules/select-current-schedule.png)
5. A **REPEAT (ismétlés** ) párbeszédpanelen hajtsa végre a következő lépéseket:
    1. Győződjön meg arról, hogy **minden héten** be van állítva az **ismétlés** mező. 
    2. Válassza ki azokat a napokat, amelyeknek érvénybe szeretné venni az ütemtervet. A következő példában a hétfő-péntek beállítás van kiválasztva. 
    3. Válassza ki az ütemterv **befejezési dátumát** .
    8. Kattintson a **Mentés** gombra. 

        ![Ismétlődő ütemterv beállítása](./media/how-to-create-schedules/set-repeat-schedule.png)

3. Az **ütemezett esemény hozzáadása** lapon a **Megjegyzések (nem kötelező)** mezőben adja meg az ütemezés leírását vagy megjegyzéseit. 
4. Az **ütemezett esemény hozzáadása** lapon válassza a **Mentés**lehetőséget. 

    ![Heti ütemterv](./media/how-to-create-schedules/add-schedule-page-weekly.png)
5. A naptárban navigáljon a kezdő dátumhoz, és ellenőrizze, hogy az ütemterv be van-e állítva.
    
    ![Ütemterv a naptárban](./media/how-to-create-schedules/schedule-calendar.png)

    További információ egy osztályhoz tartozó ütemtervek létrehozásáról és kezeléséről: [az Órarend létrehozása és kezelése az osztályterem Labs szolgáltatásban](how-to-create-schedules.md).


## <a name="add-users-to-the-lab"></a>Felhasználók hozzáadása a laborhoz

1. Válassza a bal oldali menü **felhasználók** elemét. Alapértelmezés szerint a **hozzáférés korlátozása** beállítás engedélyezve van. Ha ez a beállítás be van kapcsolva, a felhasználók nem regisztrálhatnak a laborba még akkor sem, ha a felhasználó a felhasználók listáján szerepel. Csak a listán szereplő felhasználók regisztrálhatnak a laborba az Ön által küldött regisztrációs hivatkozás használatával. Ebben az eljárásban felhasználókat vesz fel a listára. Azt is megteheti, hogy kikapcsolja a **hozzáférés korlátozása**lehetőséget, amely lehetővé teszi a felhasználók számára, hogy regisztráljanak a laborban, amennyiben rendelkeznek a regisztrációs hivatkozással. 
2. Válassza a **felhasználók hozzáadása** lehetőséget az eszköztáron, majd válassza a **Hozzáadás e-mail-cím alapján**lehetőséget. 

    ![Felhasználók hozzáadása gomb](./media/how-to-configure-student-usage/add-users-button.png)
1. A **felhasználók hozzáadása** lapon adja meg a felhasználók e-mail-címeit külön sorokban, vagy egyetlen sorban pontosvesszővel elválasztva. 

    ![Felhasználói e-mail-címek hozzáadása](./media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Kattintson a **Mentés** gombra. A listában megjelenik a felhasználók e-mail-címe és állapota (regisztrált vagy nem). 

    ![Felhasználók listája](./media/how-to-configure-student-usage/users-list-new.png)

    A listában szereplő felhasználók nevét a laborba való regisztráció után fogja látni. 
    

## <a name="send-invitation-emails-to-users"></a>Meghívó e-mailek küldése a felhasználóknak

1. Váltson a **felhasználók** nézetre, ha már nincs a lapon, és válassza az **összes meghívása** lehetőséget az eszköztáron. 

    ![Tanulók kiválasztása](./media/tutorial-setup-classroom-lab/invite-all-button.png)
1. A **Meghívás küldése e-mailben** lapon adjon meg egy opcionális üzenetet, majd válassza a **Küldés**lehetőséget. Az e-mail automatikusan tartalmazza a regisztrációs hivatkozást. Ezt a regisztrációs hivatkozást a következő parancs kiválasztásával érheti el: **... (három pont)** az eszköztáron és a **regisztrációs hivatkozáson**. 

    ![Regisztrációs hivatkozás küldése e-mailben](./media/tutorial-setup-classroom-lab/send-email.png)
4. A **meghívás** állapota megjelenik a **felhasználók** listájában. Az állapotnak a **Küldés** gombra kell váltania, majd el kell **küldenie a &lt; dátumot &gt; **. 

    A tanulók osztályhoz való hozzáadásával és a labor használatának felügyeletével kapcsolatos további információkért lásd: [a tanulói használat konfigurálása](how-to-configure-student-usage.md).

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban létrehozott egy labort az osztályhoz az Azure-ban. Ha meg szeretné tudni, hogyan férhetnek hozzá a diákok a tesztkörnyezet virtuális gépeihez a regisztrációs hivatkozással, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Kapcsolódás az osztályterem-tesztkörnyezet virtuális gépeihez](tutorial-connect-virtual-machine-classroom-lab.md)

