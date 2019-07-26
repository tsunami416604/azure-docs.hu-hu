---
title: Használati beállítások konfigurálása a Azure Lab Services osztályterem Labs-ban | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a laborhoz tartozó felhasználók számát, regisztrálhatja őket a laborban, meghatározhatja, hogy hány órát használhatnak a virtuális gép, és így tovább.
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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 86f22864c416ad2a90bea09c02675d6eb3322308
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385627"
---
# <a name="configure-usage-settings-and-policies"></a>Használati beállítások és szabályzatok konfigurálása
Ez a cikk azt ismerteti, hogyan adhat hozzá felhasználókat a laborhoz, hogyan regisztrálhatja őket a laborban, hogy hány órát használhat a virtuális gép, és így tovább. 


## <a name="add-users-to-the-lab"></a>Felhasználók hozzáadása a laborhoz
Ha a **hozzáférés korlátozása** engedélyezve van, vegyen fel felhasználókat (e-mail-címeket) a listára.

1. Válassza a bal oldali menü **felhasználók** elemét.
2. Kattintson a **felhasználók hozzáadása** elemre az eszköztáron. 

    ![Felhasználók hozzáadása gomb](../media/how-to-configure-student-usage/add-users-button.png)
1. A **felhasználók hozzáadása** lapon adja meg a felhasználók e-mail-címeit külön sorokban, vagy egyetlen sorban pontosvesszővel elválasztva. 

    ![Felhasználói e-mail-címek hozzáadása](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Kattintson a **Mentés** gombra. A listában megjelenik a felhasználók e-mail-címe és állapota (regisztrált vagy nem). 

    ![Felhasználók listája](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="share-registration-link-with-students"></a>Regisztrációs hivatkozás megosztása diákokkal
A következő módszerek egyikével elküldheti a regisztrációs hivatkozást a tanulóknak. Az első módszer azt mutatja be, hogyan küldhet e-maileket a tanulóknak a regisztrációs hivatkozással és egy opcionális üzenettel. A második módszer azt mutatja be, hogyan kérheti le a másokkal megosztható regisztrációs hivatkozást a kívánt módon. 

Ha a **hozzáférés korlátozása** engedélyezve van a laborban, csak a felhasználók listájában lévő felhasználók használhatják a regisztrációs hivatkozást a laborba való regisztrációhoz. Ez a beállítás alapértelmezés szerint engedélyezve van. 

### <a name="send-email-to-users"></a>E-mail küldése a felhasználóknak
Azure Lab Services lehetővé teszi, hogy a tanárok az összes vagy a kiválasztott tanulók számára ne kelljen egy másik e-mail-ügyfélprogramot használni. A tanárok a listán szereplő egyes tanulók számára is megtekinthetik az e-mail-ikont, vagy kijelölhetnek egy vagy több tanulót, és használhatják a **Meghívás küldése** lehetőséget az eszköztáron. Ez a funkció egy regisztrációs hivatkozást tartalmazó e-mailt küld, valamint egy, a tanár által hozzáadott üzenetet (ha van). A meghívás elküldése után a Meghívási állapot meghívást **küld** , hogy a tanárok nyomon kövessék, mely tanulók már megkapták a regisztrációs hivatkozást és az elküldött dátumot.

1. Váltson a **felhasználók** nézetre, ha már nincs a lapon. 
2. Válassza a listában a megadott vagy az összes felhasználó elemet. Adott felhasználók kiválasztásához jelölje be a jelölőnégyzeteket a lista első oszlopában. Az összes felhasználó kijelöléséhez jelölje be a jelölőnégyzetet az első oszlop címe előtt, vagy jelölje beaz összes jelölőnégyzetet a listában szereplő összes felhasználónál. A Meghívási **állapot** állapota ebben a listában látható.  A következő ábrán a tanulók Meghívási állapota a **meghívás nem**lett elküldve értékre van állítva. 

    ![Tanulók kiválasztása](../media/tutorial-setup-classroom-lab/select-students.png)
1. Válassza ki az **e-mail-ikont (borítékot)** az egyik sorban (vagy) válassza a **Meghívás küldése** lehetőséget az eszköztáron. Az e-mail-ikon megjelenítéséhez az egérmutatót egy tanuló neve fölé is helyezheti a listában. 

    ![Regisztrációs hivatkozás küldése e-mailben](../media/tutorial-setup-classroom-lab/send-email.png)
4. A **regisztrációs hivatkozás küldése e-mailben** lapon kövesse az alábbi lépéseket: 
    1. Írjon be egy **opcionális üzenetet** , amelyet el szeretne küldeni a tanulóknak. Az e-mail automatikusan tartalmazza a regisztrációs hivatkozást. 
    2. A **regisztrációs hivatkozás küldése e-mailben** lapon válassza a **Küldés**lehetőséget. Megtekintheti a meghívó küldésének  és a meghívónak **küldött**meghívások állapotát. 
        
        ![Meghívások elküldése](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="get-registration-link"></a>Regisztrációs hivatkozás beolvasása
1. Váltson a **felhasználók** nézetre a bal oldali menüben a **felhasználók** lehetőség kiválasztásával. 
2. Válassza a **regisztrálási hivatkozás** beolvasása csempét.

    ![A diákok regisztrációs hivatkozása](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. A **Felhasználói regisztráció** párbeszédablakban kattintson a **Másolás** gombra. A rendszer a vágólapra másolja a hivatkozást. Illessze be egy e-mail-szerkesztőbe, majd küldje el e-mailben a diákoknak. 

    ![A diákok regisztrációs hivatkozása](../media/tutorial-setup-classroom-lab/registration-link.png)
2. A **Felhasználói regisztráció** párbeszédablakban kattintson a **Bezárás** gombra. 
4. Ossza meg a **regisztrációs hivatkozást** egy tanulóval, hogy a tanuló regisztráljon az osztályra. 

## <a name="view-users-registered-with-the-lab"></a>A tesztkörnyezetben regisztrált felhasználók megtekintése

A bal oldali menüben válassza a **felhasználók** lehetőséget a laborban regisztrált felhasználók listájának megtekintéséhez. 

![A laborban regisztrált felhasználók listája](../media/how-to-configure-student-usage/users-list-new.png)

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



> [!IMPORTANT]
> Mielőtt elküldi a regisztrációs hivatkozást a tanulóknak, a tanároknak be kell állítania az osztályhoz tartozó ütemtervet, ha 0 kvótát vesznek fel, vagy meg kell adniuk a laborhoz tartozó kvótákat.
>
> A [virtuális gépek ütemezett futási ideje](how-to-create-schedules.md) nem számít bele a felhasználó számára kiosztott kvótába. A kvóta az ütemezett órákon kívüli időre szól, amelyet a tanuló a virtuális gépeken tölt. 

### <a name="add-users-by-uploading-a-csv-file"></a>Felhasználók hozzáadása CSV-fájl feltöltésével
Felhasználókat úgy is hozzáadhat, ha feltölt egy CSV-fájlt a felhasználók e-mail-címeivel.

1. Hozzon létre egy CSV-fájlt a felhasználók e-mail-címeivel egy oszlopban.

    ![Kvóta felhasználónként](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. A labor **felhasználók** lapján válassza a CSV-fájl **feltöltése** lehetőséget az eszköztáron.

    ![CSV-fájl feltöltése gomb](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Válassza ki a CSV-fájlt a felhasználói e-mail-címekkel. Ha a CSV-fájl kiválasztása után a **Megnyitás** lehetőséget választja, a következő **felhasználók hozzáadása** ablak jelenik meg. Az e-mail cím lista a CSV-fájlból származó e-mail-címekkel van kitöltve. 

    ![Felhasználói ablak hozzáadása a CSV-fájlból származó e-mail-címekkel](../media/how-to-configure-student-usage/add-users-window.png)
4. A **felhasználók hozzáadása** ablakban válassza a **Mentés** lehetőséget. 
5. Erősítse meg, hogy a felhasználók listáját látja a felhasználók listájában. 

    ![Hozzáadott felhasználók listája](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="manage-user-vms"></a>Felhasználói virtuális gépek kezelése
Ha a tanulók az Ön számára megadott regisztrációs hivatkozással regisztrálják Azure Lab Services a-ban, a **virtuális gépek** lapon láthatja a tanulók számára hozzárendelt virtuális gépeket. 

![Tanulók számára rendelt virtuális gépek](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

Egy tanuló virtuális gépen a következő feladatokat végezheti el: 

- Állítsa le a virtuális gépet, ha a virtuális gép fut. 
- Ha a virtuális gép le van állítva, indítsa el a virtuális gépet. 
- Csatlakozzon a virtuális géphez. 
- Törölje a virtuális gépet. 
- Megtekintheti, hogy hány óra elteltével használta a felhasználók a virtuális gépet. 

## <a name="update-number-of-virtual-machines-in-lab"></a>Virtuális gépek számának frissítése a laborban
A laborban található virtuális gépek számának frissítéséhez hajtsa végre a következő lépéseket a **Virtual Machines** oldalon:

1. A bal oldali menüben válassza a **virtuális gépek** lehetőséget. 
2. Válassza ki a **labor &lt;kapacitása&gt; : számú gép (ek)** et az eszköztáron. 
3. Adja meg a virtuális gépek **számát** .
4. Kattintson a **Mentés** gombra.

    ![Virtuális gépek a laborban](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Rendszergazdaként, labor-fiókok létrehozása és kezelése](how-to-manage-lab-accounts.md)
- [Labor tulajdonosaként Labs létrehozása és kezelése](how-to-manage-classroom-labs.md)
- [A labor tulajdonosaként hozzon létre és tegyen közzé sablonokat](how-to-create-manage-template.md)
- [Labor-felhasználóként az osztályterem Labs eléréséhez](how-to-use-classroom-lab.md)
