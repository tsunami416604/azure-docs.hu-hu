---
title: Tantermi Labs-ütemterv létrehozása Azure Lab Servicesban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre ütemterveket a tanterem Labs szolgáltatáshoz Azure Lab Services, hogy a laborokban lévő virtuális gépek egy adott időpontban induljon el és legyenek leállítva.
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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 68d608471b347895a170cc6cf83dfb4180c0a488
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84895926"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Tantermi Labs-ütemtervek létrehozása és kezelése Azure Lab Services 
Az ütemtervek lehetővé teszik a tantermi laborok konfigurálását, hogy a laborban lévő virtuális gépek automatikusan elindulnak és leálljanak egy adott időpontban. Meghatározhat egy egyszeri vagy ismétlődő ütemtervet. Az alábbi eljárások egy osztályterem laborhoz tartozó ütemtervek létrehozását és kezelését ismertetik: 

> [!IMPORTANT]
> A virtuális gépek ütemezett futási ideje nem számít bele a [felhasználó számára kiosztott kvótába](how-to-configure-student-usage.md#set-quotas-for-users). A kvóta az ütemezett órákon kívüli időre szól, amelyet a tanuló a virtuális gépeken tölt. 

## <a name="set-a-schedule-for-the-lab"></a>A laborhoz tartozó ütemterv beállítása
Hozzon létre egy ütemezett eseményt a laborhoz, hogy a laborban lévő virtuális gépek meghatározott időpontokban automatikusan elindulnak/leállnak. A korábban megadott felhasználói kvóta az egyes felhasználók számára az ütemezett időponton kívül hozzárendelt további idő. 

> [!NOTE]
> Az első lépések elkezdése előtt itt látható, hogyan befolyásolják az ütemezett Tesztkörnyezet virtuális gépei: 
>- A sablon virtuális gépe nem szerepel az ütemtervekben. 
>- Csak a hozzárendelt virtuális gépek vannak elindítva. Ez azt jelenti, hogy ha egy gépet nem igényel a végfelhasználó (tanuló), akkor a gép nem indul el az ütemezett órákon. 
>- Az összes virtuális gép (akár felhasználó által igényelt, akár nem) leáll a labor-ütemterv alapján. 

1. Váltson az **ütemezések** lapra, és válassza az eszköztár **ütemezett esemény hozzáadása** elemét. 

    ![Ütemterv hozzáadása gomb az ütemtervek lapon](./media/how-to-create-schedules/add-schedule-button.png)
2. Ellenőrizze, hogy a **standard** érték van-e kiválasztva az **esemény típusára**. A **csak indítás** gombra kattintva adhatja meg a virtuális gépek kezdési idejét. Ha csak a virtuális gépek leállítási idejét szeretné megadni, válassza a **Leállítás** lehetőséget. 
7. Az **ismétlés** szakaszban válassza ki az aktuális ütemtervet. 

    ![Ütemterv hozzáadása gomb az ütemtervek lapon](./media/how-to-create-schedules/select-current-schedule.png)
5. A **REPEAT (ismétlés** ) párbeszédpanelen hajtsa végre a következő lépéseket:
    1. Győződjön meg arról, hogy **minden héten** be van állítva az **ismétlés** mező. 
    3. A **kezdő dátum**meghatározása.
    4. Itt adhatja meg azt a **kezdési időpontot** , amikor a virtuális gépeket el szeretné indítani.
    5. Itt adhatja meg a **leállítási időt** , amikor a virtuális gépeket le kell állítani. 
    6. Adja meg az **időzónát** a megadott kezdési és befejezési időpontnál. 
    2. Válassza ki azokat a napokat, amelyeknek érvénybe szeretné venni az ütemtervet. A következő példában a hétfő-csütörtök van kiválasztva. 
    8. Kattintson a **Mentés** gombra. 

        ![Ismétlődő ütemterv beállítása](./media/how-to-create-schedules/set-repeat-schedule.png)

3. Az **ütemezett esemény hozzáadása** lapon a **Megjegyzések (nem kötelező)** mezőben adja meg az ütemezés leírását vagy megjegyzéseit. 
4. Az **ütemezett esemény hozzáadása** lapon válassza a **Mentés**lehetőséget. 

    ![Heti ütemterv](./media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Ütemtervek megtekintése a naptárban
A naptár nézetben Kiemelt dátumok és időpontok a következő képen látható módon jeleníthetők meg:

![Ütemtervek a naptár nézetben](./media/how-to-create-schedules/schedules-calendar.png)

Kattintson a jobb felső sarokban található **ma** gombra, hogy az aktuális dátumra váltson a naptárban. Kattintson a **balra mutató nyílra** az előző hétre és a **jobbra mutató nyílra** való váltáshoz a naptár következő hetében. 

## <a name="edit-a-schedule"></a>Ütemterv szerkesztése
Amikor kijelöl egy kiemelt ütemtervet a naptárban, a gombokkal **szerkesztheti** vagy **törölheti** az ütemtervet. 

![Ütemterv szerkesztése lap](./media/how-to-create-schedules/schedule-edit-button.png)

Az **ütemezett esemény szerkesztése** lapon frissítheti az ütemezést, és a **Mentés**gombra kattinthat. 

## <a name="delete-a-schedule"></a>Ütemezés törlése

1. Az ütemterv törléséhez jelöljön ki egy kiemelt ütemtervet a naptárban, és válassza a Kuka ikon (Törlés) gombot:

    ![Törlés gomb az eszköztáron](./media/how-to-create-schedules/schedule-delete-button.png)
2. Az **ütemezett esemény törlése** párbeszédpanelen válassza az **Igen** lehetőséget a törlés megerősítéséhez. 



## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Rendszergazdaként, labor-fiókok létrehozása és kezelése](how-to-manage-lab-accounts.md)
- [Labor tulajdonosaként Labs létrehozása és kezelése](how-to-manage-classroom-labs.md)
- [Tesztkörnyezet tulajdonosaként konfigurálhatja és szabályozhatja a labor használatát](how-to-configure-student-usage.md)
- [Labor-felhasználóként az osztályterem Labs eléréséhez](how-to-use-classroom-lab.md)
