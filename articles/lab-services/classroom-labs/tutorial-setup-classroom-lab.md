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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 0c50a321cbeb0d07a5039038ff796df00463ac8a
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385671"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Oktatóanyag: Osztályterem-tesztkörnyezet beállítása 
Ebben az oktatóanyagban megtanulhatja, hogyan állíthat be egy diákok által használható virtuális gépekkel rendelkező osztályterem-tesztkörnyezetet.  

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Osztályterem-tesztkörnyezet létrehozása
> * Felhasználók hozzáadása a laborhoz
> * Regisztrációs hivatkozás küldése a diákoknak

## <a name="prerequisites"></a>Előfeltételek
Ha Lab-fiókban szeretné beállítani a tantermi labort, akkor a labor-fiókban a következő szerepkörök egyikének kell tartoznia: Tulajdonos, tesztkörnyezet létrehozója vagy közreműködő. A rendszer automatikusan hozzáadja a tulajdonosi szerepkörhöz a labor-fiók létrehozásához használt fiókot.

A labor tulajdonosa más felhasználókat is hozzáadhat a **tesztkörnyezet létrehozói** szerepköréhez. Például a labor tulajdonosa hozzáadja a professzorokat a labor létrehozói szerepkörhöz. Ezután a professzorok létrehozzák a laborokat a virtuális gépekkel a saját osztályaik számára. A tanulók a professzorok által a laborba való regisztráláshoz használt regisztrációs hivatkozást használják. Ha regisztrálva vannak, a laborokban használhatnak virtuális gépeket az osztály munkahelyi és otthoni működéséhez. A felhasználók labor Creator szerepkörbe való felvételének részletes lépéseiért lásd: [felhasználó hozzáadása a labor létrehozói szerepkörhöz](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Osztályterem-tesztkörnyezet létrehozása

1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com). Vegye figyelembe, hogy az Internet Explorer 11 még nem támogatott. 
2. Válassza a **Bejelentkezés** lehetőséget, és adja meg a hitelesítő adatait. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 
3. Az **Új tesztkörnyezet** ablakban tegye a következőket: 
    1. Adjon egy **nevet** a tesztkörnyezetnek. 
    2. A laborban található **virtuális gépek maximális számát** határozza meg. A virtuális gépek számát a tesztkörnyezet létrehozása után vagy egy meglévő laborban növelheti vagy törölheti. További információ: [virtuális gépek számának frissítése tesztkörnyezetben](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)
    6. Kattintson a **Mentés** gombra.

        ![Osztályterem-tesztkörnyezet létrehozása](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. A **virtuális gép specifikációinak kiválasztására** szolgáló lapon hajtsa végre a következőket:
    1. Válasszon **méretet** a tesztkörnyezetben létrehozott virtuális gépeknek. Jelenleg a **kis**, **közepes**, **közepes (virtualizációs)** , **nagyméretű**és **GPU** méretek használata engedélyezett.
    3. Válassza ki a tesztkörnyezetben a virtuális gépek létrehozásához használni kívánt **virtuálisgép-rendszerképet**. Ha Linux-rendszerképet választ, megjelenik egy lehetőség, amely lehetővé teszi a távoli asztali kapcsolat engedélyezését. Részletekért lásd: [Távoli asztali kapcsolat engedélyezése Linux](how-to-enable-remote-desktop-linux.md)rendszerhez.
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
8. A **sablon konfigurálása** lapon hajtsa végre a következő lépéseket: Ezek a lépések  nem kötelezőek az oktatóanyaghoz.
    2. A **Csatlakozás** gomb kiválasztásával csatlakozzon a virtuálisgép-sablonhoz. Linux-sablonos virtuális gép esetén válassza ki, hogy SSH vagy RDP használatával szeretne-e csatlakozni (ha az RDP engedélyezve van).
    1. A virtuális gép jelszavának alaphelyzetbe állításához válassza a **jelszó** alaphelyzetbe állítása lehetőséget. 
    1. Telepítsen és konfiguráljon szoftvert a virtuálisgép-sablonon. 
    1. **Állítsa le** a virtuális gépet.  
    1. Adja meg a sablon **leírását**.
9. Válassza ki a **Tovább** gombot a sablon oldalán. 
10. A **Sablon közzététele** oldalon tegye az alábbiakat. 
    1. A sablon azonnali közzétételéhez válassza a **Közzététel**lehetőséget.  

        > [!WARNING]
        > Közzététel után a lépés nem vonható vissza. 
    2. Későbbi közzétételhez kattintson a **Mentés későbbre** gombra. A sablon virtuális gépet a varázsló befejeződése után teheti közzé. A varázsló befejeződése után történő konfigurálásával és közzétételével kapcsolatos részletekért lásd a [sablon közzététele](how-to-create-manage-template.md#publish-the-template-vm) című szakaszt a [tanterem Labs kezelése](how-to-manage-classroom-labs.md) című cikkben.

        ![Sablon közzététele](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Nyomon követheti, hogy **hol tart a sablon közzététele**. Ez a folyamat akár egy órát is igénybe vehet. 

    ![Sablon közzétételének folyamata](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Miután a sablon közzététele sikeresen befejeződött, az alábbi oldalt fogja látni. Válassza a **Done** (Kész) lehetőséget.

    ![Sablon sikeres közzététele](../media/tutorial-setup-classroom-lab/publish-success.png)
1. A rendszer megjeleníti a tesztkörnyezet **irányítópultját**. 
    
    ![Az osztályterem-tesztkörnyezet irányítópultja](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Váltson a **Virtual Machines** (virtuális gépek) lapra a bal oldali menüben, vagy válassza a virtuális gépek csempét. Győződjön meg arról, hogy a nem hozzárendelt  állapotú virtuális gépek láthatók. Ezek a virtuális gépek még nincsenek diákokhoz rendelve. **Leállított** állapotban kell lenniük. Ezen a lapon indíthatja el a virtuális gépeket, csatlakozhat hozzájuk, leállíthatja, valamint törölheti őket. A virtuális gépeket elindíthatja ezen a lapon, vagy engedheti, hogy a diákjai indítsák el őket. 

    ![Leállított állapotban levő virtuális gépek](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="add-users-to-the-lab"></a>Felhasználók hozzáadása a laborhoz

1. Válassza a bal oldali menü **felhasználók** elemét. Alapértelmezés szerint a **hozzáférés korlátozása** beállítás engedélyezve van. Ha ez a beállítás be van kapcsolva, a felhasználók nem regisztrálhatnak a laborba még akkor sem, ha a felhasználó a felhasználók listáján szerepel. Csak a listán szereplő felhasználók regisztrálhatnak a laborba az Ön által küldött regisztrációs hivatkozás használatával. Ebben az eljárásban felhasználókat vesz fel a listára. Azt is megteheti, hogy kikapcsolja a **hozzáférés korlátozása**lehetőséget, amely lehetővé teszi a felhasználók számára, hogy regisztráljanak a laborban, amennyiben rendelkeznek a regisztrációs hivatkozással. 
2. Kattintson a **felhasználók hozzáadása** elemre az eszköztáron. 

    ![Felhasználók hozzáadása gomb](../media/how-to-configure-student-usage/add-users-button.png)
1. A **felhasználók hozzáadása** lapon adja meg a felhasználók e-mail-címeit külön sorokban, vagy egyetlen sorban pontosvesszővel elválasztva. 

    ![Felhasználói e-mail-címek hozzáadása](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Kattintson a **Mentés** gombra. A listában megjelenik a felhasználók e-mail-címe és állapota (regisztrált vagy nem). 

    ![Felhasználók listája](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-for-users"></a>Kvóták beállítása a felhasználók számára
A kvótákat felhasználónként is beállíthatja a következő lépésekkel: 

1. Ha a lap még nem aktív, válassza a bal oldali menüben a **felhasználók** lehetőséget. 
2. Kvóta **kiválasztása felhasználónként: 10 óra** az eszköztáron. 
3. A **kvóta felhasználónként** lapon adja meg, hogy hány órát szeretne adni az egyes felhasználóknak (tanulók): 
    1. **A labor-órák teljes száma**felhasználónként. A felhasználók a beütemezett időponton **kívül**használhatják a virtuális gépeket a megadott számú (ebben a mezőben meghatározott) óraszámban. Ha ezt a lehetőséget választja, adja meg az **órák számát** a szövegmezőben. 

        ![Órák száma felhasználónként](../media/how-to-configure-student-usage/number-of-hours-per-user.png). 
    1. **0 óra (csak ütemezett)** . A felhasználók csak az ütemezett időszakban használhatják a virtuális gépeket, vagy ha a labor tulajdonosa bekapcsolja a virtuális gépeket.

        ![Nulla óra – csak ütemezett idő](../media/how-to-configure-student-usage/zero-hours.png)
    4. Kattintson a **Mentés** gombra. 
5. Ekkor megjelennek a megváltozott értékek az eszköztáron: **Kvóta felhasználónként: &lt;&gt;órák száma**. 

    ![Kvóta felhasználónként](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="set-a-schedule-for-the-lab"></a>A laborhoz tartozó ütemterv beállítása
Ha a kvóta beállítását **0 órára konfigurálta (csak az ütemterv esetében)** , be kell állítania egy ütemtervet a laborhoz. Ebben az oktatóanyagban beállíthatja, hogy az ütemterv ismétlődő heti ütemterv legyen.

1. Váltson az **ütemtervek** lapra, és válassza az **ütemterv hozzáadása** lehetőséget az eszköztáron. 

    ![Ütemterv hozzáadása gomb az ütemtervek lapon](../media/how-to-create-schedules/add-schedule-button.png)
2. Az **ütemterv hozzáadása** lapon a felülre váltson **hetente** . 
3. Az ütemezett **napok (kötelező)** beállításnál válassza ki azokat a napokat, amelyeken az ütemterv érvénybe lép. A következő példában a hétfő-péntek beállítás van kiválasztva. 
4. A **from (forrás** ) mezőben adja meg az **ütemezett kezdési dátumot** , vagy válasszon ki egy dátumot a **Naptár** gomb kiválasztásával. A mező kitöltése kötelező. 
5. Az **ütemezett befejezési dátumnál**adja meg vagy válassza ki azt a befejezési dátumot, amikor a virtuális gépeket le szeretné állítani. 
6. A **kezdési**időponthoz válassza ki azt az időpontot, amikor a virtuális gépeket el szeretné indítani. A kezdési időpontot kötelező megadni, ha a leállítási idő nincs beállítva. Válassza a **kezdési esemény eltávolítása** lehetőséget, ha csak a leállítási időt szeretné megadni. Ha a **Kezdési idő** le van tiltva, a legördülő lista melletti **indítási esemény hozzáadása** lehetőséggel engedélyezheti azt. 
7. A **leállítási idő**beállításnál válassza ki azt az időpontot, amikor a virtuális gépeket le szeretné állítani. A leállítási idő megadása kötelező, ha a kezdési időpont nincs beállítva. Válassza a leállítási **esemény eltávolítása** lehetőséget, ha csak a kezdési időpontot szeretné megadni. Ha a **leállítási idő** le van tiltva, válassza a leállítási **esemény hozzáadása** lehetőséget a legördülő lista mellett az engedélyezéshez.
8. Az **időzóna (kötelező)** beállításnál válassza ki a megadott kezdési és befejezési időzónát.  
9. A **Megjegyzések**mezőben adja meg az ütemterv leírását vagy megjegyzéseit. 
10. Kattintson a **Mentés** gombra. 

    ![Heti ütemterv](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="send-an-email-with-the-registration-link"></a>E-mail küldése a regisztrációs hivatkozással

1. Váltson a **felhasználók** nézetre, ha már nincs a lapon. 
2. Válassza a listában a megadott vagy az összes felhasználó elemet. Adott felhasználók kiválasztásához jelölje be a jelölőnégyzeteket a lista első oszlopában. Az összes felhasználó kijelöléséhez jelölje be a jelölőnégyzetet az első oszlop címe előtt, vagy jelölje beaz összes jelölőnégyzetet a listában szereplő összes felhasználónál. A Meghívási **állapot** állapota ebben a listában látható.  A következő ábrán a tanulók Meghívási állapota a **meghívás nem**lett elküldve értékre van állítva. 

    ![Tanulók kiválasztása](../media/tutorial-setup-classroom-lab/select-students.png)
1. Válassza ki az **e-mail-ikont (borítékot)** az egyik sorban (vagy) válassza a **Meghívás küldése** lehetőséget az eszköztáron. Az e-mail-ikon megjelenítéséhez az egérmutatót egy tanuló neve fölé is helyezheti a listában. 

    ![Regisztrációs hivatkozás küldése e-mailben](../media/tutorial-setup-classroom-lab/send-email.png)
4. A **regisztrációs hivatkozás küldése e-mailben** lapon kövesse az alábbi lépéseket: 
    1. Írjon be egy **opcionális üzenetet** , amelyet el szeretne küldeni a tanulóknak. Az e-mail automatikusan tartalmazza a regisztrációs hivatkozást. 
    2. A **regisztrációs hivatkozás küldése e-mailben** lapon válassza a **Küldés**lehetőséget. Megtekintheti a meghívó küldésének  és a meghívónak **küldött**meghívások állapotát. 
        
        ![Meghívások elküldése](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy osztályterem-tesztkörnyezetet, és konfigurálta azt. Ha meg szeretné tudni, hogyan férhetnek hozzá a diákok a tesztkörnyezet virtuális gépeihez a regisztrációs hivatkozással, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Kapcsolódás az osztályterem-tesztkörnyezet virtuális gépeihez](tutorial-connect-virtual-machine-classroom-lab.md)

