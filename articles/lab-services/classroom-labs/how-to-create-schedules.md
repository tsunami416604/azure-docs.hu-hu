---
title: Osztályterem-tesztkörnyezetek tartozó ütemezés létrehozásához az Azure Lab Services |} A Microsoft Docs
description: Ismerje meg, hogy az osztályterem-tesztkörnyezetek ütemezések létrehozása az Azure Lab Services, hogy a labs szolgáltatásban létrehozott virtuális gépek indítása és állítsa le a megadott időpontban.
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
ms.date: 01/30/2019
ms.author: spelluru
ms.openlocfilehash: 34bc8263053cd4a701c16ee1832cf1b27340a345
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55501360"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Létrehozása és kezelése az Azure Lab Services osztályterem-tesztkörnyezetek ütemezését 
Ütemezésekkel osztályterem-tesztkörnyezet konfigurálása, hogy a labor virtuális gépeinek automatikusan elindítani, és állítsa le a megadott időpontban. Egyszeri ütemezés szerint vagy ismétlődő ütemezés szerint határozhatja meg. Az alábbi eljárások létrehozásához és kezeléséhez az osztályterem-tesztkörnyezet ütemezések lépéseket biztosít: 

> [!IMPORTANT]
> Virtuális gépek ütemezett futási ideje nem számítanak bele a [egy felhasználó számára engedélyezett kvóta](how-to-configure-student-usage.md#set-quotas-per-user). A kvóta pedig egy diák foglalkozik a virtuális gépek idő ütemezése kívül alkalommal. 

## <a name="add-a-schedule-once"></a>(Ha) ütemezés hozzáadása

1. Váltson a **ütemezések** lapon, és válassza ki **Hozzáadás ütemezés** az eszköztáron. 

    ![Az ütemezések lapon ütemezés gomb hozzáadása](../media/how-to-create-schedules/add-schedule-button.png)
2. A a **Hozzáadás ütemezés** lapon **egyszer** beállítást az oldal tetején. Ha nem, válassza ki a **egyszer**. 
3. A **ütemezése (kötelező) dátum**, adja meg a dátumot, vagy kattintson a naptár ikonra, válassza ki a dátumot. 
4. A **kezdési idő**, válassza ki az időpontot, amikor a virtuális gépeket kell elindítani. A kezdési időpontját kötelező megadni, ha nincs beállítva a leállítási ideje. Válassza ki **Remove esemény indítása** Ha meg szeretné határozni a csak a leállítási ideje. Ha a **kezdési idő** van válassza le van tiltva, **Hozzáadás indítása esemény** mellett a legördülő listából válassza ki az engedélyezéshez. 
5. A **leállítási ideje**, válassza ki az időpontot, amikor a virtuális gépek leállítását. A leállási idő megadása kötelező, ha a kezdési időpont nincs beállítva. Válassza ki **Remove leállási esemény** Ha adja meg a kezdő időpont csak szeretné. Ha a **leállítási ideje** van válassza le van tiltva, **Hozzáadás leállási esemény** mellett a legördülő listából válassza ki az engedélyezéshez.
6. A **(kötelező) időzóna**, jelölje be a kezdő időzónáját, és állítsa le a megadott időpontok. 
7. A **megjegyzések**, adja meg az ütemezés bármely leírása, illetve a megjegyzések. 
8. Kattintson a **Mentés** gombra. 

    ![Egyszeri ütemezés](../media/how-to-create-schedules/add-schedule-page.png)

## <a name="add-a-recurring-schedule-weekly"></a>Adjon hozzá egy ismétlődő ütemezés szerint (hetente)

1. Váltson a **ütemezések** lapon, és válassza ki **Hozzáadás ütemezés** az eszköztáron. 

    ![Az ütemezések lapon ütemezés gomb hozzáadása](../media/how-to-create-schedules/add-schedule-button.png)
2. Az a **Hozzáadás ütemezés** lapon, váltson **heti** tetején. 
3. A **ütemezett nap (kötelező)**, válassza ki azokat a napokat, amelyeken az ütemezés érvénybe szeretné. A következő példában hétfőtől péntekig van kiválasztva. 
4. Az a **a** mezőbe írja be a **ütemezett kezdési dátum** válasszon dátumot kiválasztásával, vagy a **naptár** gombra. Ezt a mezőt kötelező kitölteni. 
5. A **az ütemezéshez záró dátumot**adja meg vagy válassza ki a befejező dátum, amelyen a virtuális gépek vannak, le kell állítani. 
6. A **kezdési idő**, válassza ki az időpontot, amelyeknél szeretné elindítani a virtuális gépeket. A kezdési időpontját kötelező megadni, ha nincs beállítva a leállítási ideje. Válassza ki **Remove esemény indítása** Ha meg szeretné határozni a csak a leállítási ideje. Ha a **kezdési idő** van válassza le van tiltva, **Hozzáadás indítása esemény** mellett a legördülő listából válassza ki az engedélyezéshez. 
7. A **leállítási ideje**, válassza ki az idő, amelyeknél szeretné a virtuális gépek leállítását. A leállási idő megadása kötelező, ha a kezdési időpont nincs beállítva. Válassza ki **Remove leállási esemény** Ha adja meg a kezdő időpont csak szeretné. Ha a **leállítási ideje** van válassza le van tiltva, **Hozzáadás leállási esemény** mellett a legördülő listából válassza ki az engedélyezéshez.
8. A **(kötelező) időzóna**, jelölje be a kezdő időzónáját, és állítsa le a megadott időpontok.  
9. A **megjegyzések**, adja meg az ütemezés bármely leírása, illetve a megjegyzések. 
10. Kattintson a **Mentés** gombra. 

    ![Heti ütemezés](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>A naptár ütemezések megtekintése
Az ütemezett dátum- és időértékek, kiemelve a Naptár nézetben, a következő képen látható módon tekintheti meg:

![A Naptár nézetben ütemezések](../media/how-to-create-schedules/schedules-in-calendar.png)

Válassza ki a **Ma** gombra a jobb felső sarokban, váltson át az aktuális dátumra a naptárban. Válassza ki **balra mutató nyíl** váltson át az előző hét és **jobbra mutató nyílra** váltson át a következő hét a naptárban. 

## <a name="edit-a-schedule"></a>Ütemezés szerkesztése
Amikor duplán rákattint egy kijelölt ütemezés szerint a naptárban, illetve válassza a **ceruza** gombra az eszköztáron látható a **ütemezés szerkesztése** lapot. Ugyanaz, mint a beállításainak frissítése folyamatban van ezen a lapon beállítások frissítése folyamatban a **Hozzáadás ütemezés** lapon ismertetett módon a [adjon hozzá egy ismétlődő ütemezés szerint](#add-a-recurring-schedule-weekly) szakaszban. 

![Ütemezés lapon szerkesztése](../media/how-to-create-schedules/edit-schedule-page.png)

## <a name="delete-a-schedule"></a>Ütemezés törlése

1. Ütemezés törléséhez válassza a Kuka is (Törlés) gombra az eszköztáron az alábbi képen látható módon:

    ![Eszköztár gombja törlése](../media/how-to-create-schedules/delete-schedule-button.png)

    A Törlés gombra használhat bármely a ütemezett dátumok és időpontok a naptárban, és válassza ki **törlése**. 
2. Az a **ütemezések törlése** lapon jelölje be **Igen**.

    ![Törlés ütemezések megerősítése](../media/how-to-create-schedules/delete-schedules-confirmation.png)




## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Rendszergazdaként hozzon létre, és tesztkörnyezetfiókok kezelése](how-to-manage-lab-accounts.md)
- [Labortulajdonosként hozzon létre és laborok kezelése](how-to-manage-classroom-labs.md)
- [Labortulajdonosként konfigurálása, és a egy lab használatának szabályozása](how-to-configure-student-usage.md)
- [Labor felhasználóként osztályterem-tesztkörnyezetek elérése](how-to-use-classroom-lab.md)
