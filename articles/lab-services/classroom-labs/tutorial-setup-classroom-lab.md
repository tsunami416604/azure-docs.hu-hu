---
title: Osztályterem-tesztkörnyezet beállítása az Azure Lab Services szolgáltatással | Microsoft Docs
description: Ebben az oktatóanyagban az Azure Lab Services segítségével egy tantermi labort állíthat be az osztályában lévő diákok által használt virtuális gépekkel.
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
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: 166ec4db2a2891d25a1e80526f8c1bd9770f9eef
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77592220"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Oktatóanyag: Osztályterem-tesztkörnyezet beállítása 
Ebben az oktatóanyagban megtanulhatja, hogyan állíthat be egy diákok által használható virtuális gépekkel rendelkező osztályterem-tesztkörnyezetet.  

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Osztályterem-tesztkörnyezet létrehozása
> * Felhasználók hozzáadása a tesztkörnyezethez
> * A labor ütemezésének beállítása
> * Meghívó e-mail küldése diákoknak

## <a name="prerequisites"></a>Előfeltételek
Ebben az oktatóanyagban egy tesztkörnyezetet hoz létre az osztály virtuális gépeivel. Tantermi tesztkörnyezet beállításához egy tesztkörnyezet-fiókban, akkor tagja kell lennie az egyik ilyen szerepkör a laborfiókban: Tulajdonos, Lab Creator vagy közreműködő. A tesztkörnyezet-fiók létrehozásához használt fiók automatikusan hozzáadódik a tulajdonosi szerepkörhöz. Így használhatja a felhasználói fiók, amely segítségével hozzon létre egy tesztkörnyezetben egy tantermi labor létrehozása. 

Az Azure Lab Services használata kor az alábbiaktipikus munkafolyamata:

1. A tesztkörnyezet-fiók létrehozója további felhasználókat ad hozzá a **Lab Creator** szerepkörhöz. Például a laborfiók létrehozója/rendszergazdája professzorokat ad hozzá a **Lab Creator** szerepkörhöz, hogy laborokat hozhassanak létre az osztályaikszámára. 
2. Ezután a professzorok laborokat hoznak létre virtuális gépekkel az osztályaikhoz, és regisztrációs linkeket küldenek az osztály tanulóinak. 
3. A diákok a professzoroktól kapott regisztrációs linket használják a laborba való regisztrációhoz. Miután regisztrált, a laborokban lévő virtuális gépek segítségével elláthatják az osztálymunkát és az otthoni munkát. 

## <a name="create-a-classroom-lab"></a>Osztályterem-tesztkörnyezet létrehozása
Ebben a lépésben hozzon létre egy tesztkörnyezetet az Azure-ban az osztályához. 

1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com). Ne feledje, hogy az Internet Explorer 11 még nem támogatott. 
2. Válassza a **Bejelentkezés** lehetőséget, és adja meg a hitelesítő adatait. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 
3. Válassza az **Új tesztkörnyezet**lehetőséget. 
    
    ![Osztályterem-tesztkörnyezet létrehozása](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Az **Új tesztkörnyezet** ablakban tegye a következőket: 
    1. Adja meg a tesztkörnyezet **nevét,** és válassza a **Tovább gombot.**  

        ![Osztályterem-tesztkörnyezet létrehozása](../media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. A **virtuális gép hitelesítő adatai** lapon adja meg az alapértelmezett hitelesítő adatokat a laborban lévő összes virtuális géphez. Adja meg a felhasználó **nevét** és **jelszavát,** majd válassza a **Tovább**gombot.  

        ![Új laborablak](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Jegyezze fel a felhasználónevet és a jelszót, mert többször nem fognak megjelenni.
    3. A **Labor házirendek** lapon válassza a **Befejezés lehetőséget.** 

        ![Kvóta minden felhasználószámára](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Meg kell jelennie a következő képernyőn, amely a sablon virtuális gép létrehozásának állapotát mutatja. Ez a művelet legfeljebb 20 percet vesz igénybe. 

    ![A sablon virtuális gép létrehozásának állapota](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. A **Sablon** lapon tegye a következő lépéseket: Ezek a lépések **nem kötelezőek** az oktatóanyag számára.

    1. A **Csatlakozás** gomb kiválasztásával csatlakozzon a virtuálisgép-sablonhoz. Ha ez egy Linux sablon virtuális gép, akkor válassza ki, hogy szeretne csatlakozni ssh vagy RDP (ha RDP engedélyezve van).
    3. Telepítse és konfigurálja az osztályhoz szükséges szoftvert a sablon virtuális gépén. 
    4. **Állítsa le** a sablon virtuális gép.  

## <a name="publish-the-template-vm"></a>A virtuálisgép-sablon közzététele
Ebben a lépésben közzé kell tennie a sablon virtuális gép. A sablon virtuális gép közzétételekor az Azure Lab Services virtuális gépeket hoz létre a laborban a sablon használatával. A virtuális gépek konfigurációja megegyezik a sablonéval.

1. A **Sablon** lapon válassza a **Közzététel** gombot az eszköztáron. 

    ![Sablon közzététele gomb](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Közzététel után a lépés nem vonható vissza. 
2. A **Közzétételi sablon** lapon adja meg a laborban létrehozni kívánt virtuális gépek számát, majd válassza a **Közzététel**lehetőséget. 

    ![Közzétételi sablon – virtuális gépek száma](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. A sablon **közzétételi állapota** a lapon. Ez a folyamat akár egy órát is igénybe vehet. 

    ![Sablon közzétételének folyamata](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Várjon, amíg a közzététel befejeződik, majd váltson a **Virtuális gépek készlet** lapra a bal oldali menü Virtuális **gépek** parancsával vagy a **Virtuális gépek** csempéjének kiválasztásával. Ellenőrizze, hogy olyan virtuális gépek jelennek-e meg, amelyek **hozzárendelés nélküli** állapotban vannak. Ezek a virtuális gépek még nincsenek diákokhoz rendelve. **Leállított** állapotban kell lenniük. Ezen a lapon indíthatja el a virtuális gépeket, csatlakozhat hozzájuk, leállíthatja, valamint törölheti őket. A virtuális gépeket elindíthatja ezen a lapon, vagy engedheti, hogy a diákjai indítsák el őket. 

    ![Leállított állapotban levő virtuális gépek](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)   

    > [!NOTE]
    > Amikor egy oktató bekapcsolja a tanuló virtuális gép, kvóta a hallgató nem érinti. A felhasználó kvótája megadja, hogy az ütemezett óraidőn kívül hány laboróra érhető el a felhasználó számára. A kvótákról a [Kvóták beállítása felhasználókszámára című](how-to-configure-student-usage.md?#set-quotas-for-users)témakörben talál további információt.

## <a name="set-a-schedule-for-the-lab"></a>A labor ütemezésének beállítása
Hozzon létre egy ütemezett eseményt a laborszámára, hogy a tesztkörnyezetben lévő virtuális gépek automatikusan elindulnak/leállnak bizonyos időpontokban. A korábban megadott felhasználói kvóta (alapértelmezett: 10 óra) az ütemezett időn kívül az egyes felhasználókhoz rendelt további idő. 

1. Váltson az **Ütemezések** lapra, és válassza az **ütemezett esemény hozzáadása lehetőséget** az eszköztáron. 

    ![Ütemezés hozzáadása gomb az Ütemezés ek lapon](../media/how-to-create-schedules/add-schedule-button.png)
2. Az **Ütemezett esemény hozzáadása** lapon tegye a következő lépéseket:
    1. Annak ellenőrzése, hogy a **Standard** az **Esemény típust**választotta.  
    2. Válassza ki az osztály **kezdési dátumát.** 
    4. Válassza ki azt a **kezdési időpontot,** amikor el szeretné indítani a virtuális gépeket.
    5. Válassza ki a **leállítási időt,** amikor a virtuális gépeket le kell állítani. 
    6. Válassza ki a megadott kezdési és leállítási idők **időzónáját.** 
3. Ugyanazon az **Ütemezett esemény hozzáadása** lapon jelölje ki az aktuális ütemezést az **Ismétlés** szakaszban.  

    ![Ütemezés hozzáadása gomb az Ütemezés ek lapon](../media/how-to-create-schedules/select-current-schedule.png)
5. Az **Ismétlés** párbeszédpanelen tegye a következő lépéseket:
    1. Ellenőrizze, hogy **minden hétbe** be van-e állítva az **Ismétlés** mező. 
    2. Válassza ki azokat a napokat, amelyeken az ütemezés érvénybe lép. A következő példában a hétfő-péntek van kiválasztva. 
    3. Adja meg az ütemezés **záró dátumát.**
    8. Kattintson a **Mentés** gombra. 

        ![Ismétlési ütemezés beállítása](../media/how-to-create-schedules/set-repeat-schedule.png)

3. Most az **Ütemezett esemény hozzáadása** lapon a **Jegyzetek (nem kötelező)** lapon adja meg az ütemezés leírását vagy jegyzeteit. 
4. Az **Ütemezett esemény hozzáadása** lapon válassza a **Mentés lehetőséget.** 

    ![Heti menetrend](../media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Keresse meg a naptár kezdő dátumát, és ellenőrizze, hogy az ütemezés be van-e állítva.
    
    ![Ütemezés a naptárban](../media/how-to-create-schedules/schedule-calendar.png)

    Az osztály ütemezésének létrehozásáról és kezeléséről az [Ütemezés létrehozása és kezelése az osztálytermi laborokhoz](how-to-create-schedules.md)című témakörben talál további információt.


## <a name="add-users-to-the-lab"></a>Felhasználók hozzáadása a tesztkörnyezethez

1. Válassza a bal oldali menü **Felhasználók parancsát.** Alapértelmezés szerint a **Hozzáférés korlátozása** beállítás engedélyezve van. Ha ez a beállítás be van kapcsolva, a felhasználó nem regisztrálhat a tesztkörnyezetben, még akkor sem, ha a felhasználó rendelkezik a regisztrációs hivatkozással, kivéve, ha a felhasználó szerepel a felhasználók listájában. Csak a listában szereplő felhasználók regisztrálhatnak a tesztkörnyezetben az elküldött regisztrációs hivatkozás használatával. Ebben az eljárásban felhasználókat vesz fel a listára. Másik lehetőségként kikapcsolhatja **a Hozzáférés korlátozása**lehetőséget, amely lehetővé teszi a felhasználók számára, hogy regisztráljanak a laborban, amíg rendelkeznek a regisztrációs hivatkozással. 
2. Válassza a **Felhasználók hozzáadása** lehetőséget az eszköztáron, majd a **Hozzáadás e-mail cím alapján**lehetőséget. 

    ![Felhasználók hozzáadása gomb](../media/how-to-configure-student-usage/add-users-button.png)
1. A **Felhasználók hozzáadása** lapon írja be a felhasználók e-mail címét külön sorokban vagy pontosvesszővel elválasztott sorban. 

    ![Felhasználói e-mail címek hozzáadása](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Kattintson a **Mentés** gombra. A listában láthatja a felhasználók e-mail címét és állapotukat (regisztrált vagy nem regisztrált). 

    ![Felhasználók listája](../media/how-to-configure-student-usage/users-list-new.png)

    A felhasználók nevét a tesztkörnyezetbe való regisztrálásután fogja látni a listában. 
    

## <a name="send-invitation-emails-to-users"></a>Meghívó e-mailek küldése a felhasználóknak

1. Váltson a **Felhasználók** nézetre, ha még nem vagyunk a lapon, és az eszköztáron válassza az **Összes meghívása** lehetőséget. 

    ![Diákok kiválasztása](../media/tutorial-setup-classroom-lab/invite-all-button.png)
1. A **Meghívó küldése e-mailben** lapon írjon be egy nem kötelező üzenetet, majd kattintson a **Küldés gombra.** Az e-mail automatikusan tartalmazza a regisztrációs linket. Lehet kapni ezt a regisztrációs linket kiválasztásával **... (Ellipszis)** az eszköztáron, és a **Regisztrációs linken**. 

    ![Regisztrációs hivatkozás küldése e-mailben](../media/tutorial-setup-classroom-lab/send-email.png)
4. A **meghívó** állapota a **Felhasználók** listában jelenik meg. Az állapotnak **Küldés,** majd **Elküldés &lt;dátuma lesz.&gt;** 

    A diákok osztályhoz való hozzáadásáról és a labor használatának kezeléséről a [Tanulói használat konfigurálása](how-to-configure-student-usage.md)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy tesztkörnyezetet az Azure-ban az osztályához. Ha meg szeretné tudni, hogyan férhetnek hozzá a diákok a tesztkörnyezet virtuális gépeihez a regisztrációs hivatkozással, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Kapcsolódás az osztályterem-tesztkörnyezet virtuális gépeihez](tutorial-connect-virtual-machine-classroom-lab.md)

