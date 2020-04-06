---
title: Az azure Lab Services tantermi laborjainak ütemezése | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre ütemezéseket az azure Lab Services tantermi laborok számára, hogy a laborokban lévő virtuális gépek egy adott időpontban elinduljanak és állítsanak le.
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
ms.openlocfilehash: 4887b4359451ca5ce85042b4de42d5376bf4a730
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667768"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Tantermi laborok ütemezésének létrehozása és kezelése az Azure Lab Servicesben 
Az ütemezések lehetővé teszik egy tantermi labor konfigurálását úgy, hogy a tesztkörnyezetben lévő virtuális gépek automatikusan elinduljanak és leállítsanak egy adott időpontban. Megadhatja az egyszeri vagy az ismétlődő ütemezést. Az alábbi eljárások lépéseket nyújtanak az osztálytermi labor ütemezésének létrehozásához és kezeléséhez: 

> [!IMPORTANT]
> A virtuális gépek ütemezett futási ideje nem számít bele a [felhasználónak kiosztott kvótába.](how-to-configure-student-usage.md#set-quotas-for-users) A kvóta a tanuló által a virtuális gépekre fordított ütemezési órákon kívül töltött időre vonatkozik. 

## <a name="set-a-schedule-for-the-lab"></a>A labor ütemezésének beállítása
Hozzon létre egy ütemezett eseményt a laborszámára, hogy a tesztkörnyezetben lévő virtuális gépek automatikusan elindulnak/leállnak bizonyos időpontokban. A korábban megadott felhasználói kvóta az ütemezett időn kívüli, minden egyes felhasználóhoz rendelt további idő. 

> [!NOTE]
> Mielőtt elkezdenénk, az ütemezések hogyan befolyásolják a laborvirtuális gépeket: 
>- A sablon virtuális gépe nem szerepel az ütemezésekben. 
>- Csak a hozzárendelt virtuális gépek indulnak el. Ez azt jelenti, hogy ha egy számítógép nem igényelegy végfelhasználó (diák), a gép nem indul el az ütemezett órákban. 
>- Az összes virtuális gép (függetlenül attól, hogy egy felhasználó által igényelt vagy sem) leáll a tesztkörnyezet alapján. 

1. Váltson az **Ütemezések** lapra, és válassza az **ütemezett esemény hozzáadása lehetőséget** az eszköztáron. 

    ![Ütemezés hozzáadása gomb az Ütemezés ek lapon](../media/how-to-create-schedules/add-schedule-button.png)
2. Annak ellenőrzése, hogy a **Standard** az **Esemény típust**választotta. Csak a **Kezdő** lehetőséget választva csak a virtuális gépek kezdési idejét adhatja meg. Válassza **a Csak leállítás** lehetőséget, ha csak a virtuális gépek leállítási idejét adja meg. 
7. Az **Ismétlés csoportban** jelölje ki az aktuális ütemezést. 

    ![Ütemezés hozzáadása gomb az Ütemezés ek lapon](../media/how-to-create-schedules/select-current-schedule.png)
5. Az **Ismétlés** párbeszédpanelen tegye a következő lépéseket:
    1. Ellenőrizze, hogy **minden hétbe** be van-e állítva az **Ismétlés** mező. 
    3. Adja meg a **kezdő dátumot**.
    4. Adja meg a virtuális gépek indításának **kezdési idejét.**
    5. Adja meg a **leállítási időt,** amelyen a virtuális gépeket le kell állítani. 
    6. Adja **time zone** meg a megadott kezdési és leállítási idők időzónáját. 
    2. Válassza ki azokat a napokat, amelyeken az ütemezés érvénybe lép. A következő példában hétfő-csütörtök van kiválasztva. 
    8. Kattintson a **Mentés** gombra. 

        ![Ismétlési ütemezés beállítása](../media/how-to-create-schedules/set-repeat-schedule.png)

3. Most az **Ütemezett esemény hozzáadása** lapon a **Jegyzetek (nem kötelező)** lapon adja meg az ütemezés leírását vagy jegyzeteit. 
4. Az **Ütemezett esemény hozzáadása** lapon válassza a **Mentés lehetőséget.** 

    ![Heti menetrend](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Ütemezések megtekintése a naptárban
A naptárnézetben az ütemezett dátumokat és időpontokat az alábbi képen látható módon tekintheti meg:

![Ütemezések naptárnézetben](../media/how-to-create-schedules/schedules-calendar.png)

A jobb felső sarokban a **Ma** gombra kattintva váltson aktuális dátumra a naptárban. Válassza **a balra nyíl lehetőséget** az előző hétre való váltáshoz, a **jobbra nyílra** a naptár következő hetére való váltáshoz. 

## <a name="edit-a-schedule"></a>Ütemezés szerkesztése
Amikor kijelölt ütemezést jelöl ki a naptárban, az ütemezés **szerkesztéséhez** vagy **törléséhez** gombok jelennek meg. 

![Ütemezési lap szerkesztése](../media/how-to-create-schedules/schedule-edit-button.png)

Az **Ütemezett esemény szerkesztése** lapon frissítheti az ütemezést, és a **Mentés gombra**léphet. 

## <a name="delete-a-schedule"></a>Ütemezés törlése

1. Ütemezés törléséhez jelöljön ki egy kiemelt ütemezést a naptárban, és kattintson a Kuka ikon (törlés) gombra:

    ![Törlés gomb az eszköztáron](../media/how-to-create-schedules/schedule-delete-button.png)
2. A **Törlés hez** kattintson az **Igen** gombra az Ütemezett esemény törlése párbeszédpanelen. 



## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Rendszergazdaként hozzon létre és kezeljen laborfiókokat](how-to-manage-lab-accounts.md)
- [Labortulajdonosként hozzon létre és kezeljen laborokat](how-to-manage-classroom-labs.md)
- [Labortulajdonosként konfigurálja és szabályozza a tesztkörnyezet használatát](how-to-configure-student-usage.md)
- [Laborfelhasználóként tantermi laborok elérése](how-to-use-classroom-lab.md)
