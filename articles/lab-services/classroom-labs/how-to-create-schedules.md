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
ms.date: 01/30/2019
ms.author: spelluru
ms.openlocfilehash: f607ba68563aa92797f45cf77db0575ae6802fee
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385607"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Tantermi Labs-ütemtervek létrehozása és kezelése Azure Lab Services 
Az ütemtervek lehetővé teszik a tantermi laborok konfigurálását, hogy a laborban lévő virtuális gépek automatikusan elindulnak és leálljanak egy adott időpontban. Meghatározhat egy egyszeri vagy ismétlődő ütemtervet. Az alábbi eljárások egy osztályterem laborhoz tartozó ütemtervek létrehozását és kezelését ismertetik: 

> [!IMPORTANT]
> A virtuális gépek ütemezett futási ideje nem számít bele a [felhasználó számára kiosztott kvótába](how-to-configure-student-usage.md#set-quotas-for-users). A kvóta az ütemezett órákon kívüli időre szól, amelyet a tanuló a virtuális gépeken tölt. 

## <a name="add-a-schedule-once"></a>Ütemterv hozzáadása (egyszer)

1. Váltson az **ütemtervek** lapra, és válassza az **ütemterv hozzáadása** lehetőséget az eszköztáron. 

    ![Ütemterv hozzáadása gomb az ütemtervek lapon](../media/how-to-create-schedules/add-schedule-button.png)
2. Az **ütemterv hozzáadása** lapon ellenőrizze, hogy a **felül lehetőség van** -e kiválasztva. Ha nem, akkor válassza a **egyszer**lehetőséget. 
3. Az **ütemezett dátum (kötelező)** mezőben adja meg a dátumot, vagy válassza a naptár ikont a dátum kiválasztásához. 
4. A **kezdő időpont**mezőben adja meg, hogy mikor szeretné elindítani a virtuális gépeket. A kezdési időpontot kötelező megadni, ha a leállítási idő nincs beállítva. Válassza a **kezdési esemény eltávolítása** lehetőséget, ha csak a leállítási időt szeretné megadni. Ha a **Kezdési idő** le van tiltva, a legördülő lista melletti **indítási esemény hozzáadása** lehetőséggel engedélyezheti azt. 
5. A **leállítási idő**beállításnál válassza ki azt az időpontot, amikor a virtuális gépeket le szeretné állítani. A leállítási idő megadása kötelező, ha a kezdési időpont nincs beállítva. Válassza a leállítási **esemény eltávolítása** lehetőséget, ha csak a kezdési időpontot szeretné megadni. Ha a **leállítási idő** le van tiltva, válassza a leállítási **esemény hozzáadása** lehetőséget a legördülő lista mellett az engedélyezéshez.
6. Az **időzóna (kötelező)** beállításnál válassza ki a megadott kezdési és befejezési időzónát. 
7. A **Megjegyzések**mezőben adja meg az ütemterv leírását vagy megjegyzéseit. 
8. Kattintson a **Mentés** gombra. 

    ![Egyszeri időpont](../media/how-to-create-schedules/add-schedule-page.png)

## <a name="add-a-recurring-schedule-weekly"></a>Ismétlődő ütemterv hozzáadása (hetente)

1. Váltson az **ütemtervek** lapra, és válassza az **ütemterv hozzáadása** lehetőséget az eszköztáron. 

    ![Ütemterv hozzáadása gomb az ütemtervek lapon](../media/how-to-create-schedules/add-schedule-button.png)
2. Az **ütemterv hozzáadása** lapon a felülre váltson **hetente** . 
3. Az ütemezett **napok (kötelező)** beállításnál válassza ki azokat a napokat, amelyeken az ütemterv érvénybe lép. A következő példában a hétfő-péntek beállítás van kiválasztva. 
4. A **from (forrás** ) mezőben adja meg az **ütemezett kezdési dátumot** , vagy válasszon ki egy dátumot a **Naptár** gomb kiválasztásával. Ezt a mezőt kötelező kitölteni. 
5. Az **ütemezett befejezési dátumnál**adja meg vagy válassza ki azt a befejezési dátumot, amikor a virtuális gépeket le szeretné állítani. 
6. A **kezdési**időponthoz válassza ki azt az időpontot, amikor a virtuális gépeket el szeretné indítani. A kezdési időpontot kötelező megadni, ha a leállítási idő nincs beállítva. Válassza a **kezdési esemény eltávolítása** lehetőséget, ha csak a leállítási időt szeretné megadni. Ha a **Kezdési idő** le van tiltva, a legördülő lista melletti **indítási esemény hozzáadása** lehetőséggel engedélyezheti azt. 
7. A **leállítási idő**beállításnál válassza ki azt az időpontot, amikor a virtuális gépeket le szeretné állítani. A leállítási idő megadása kötelező, ha a kezdési időpont nincs beállítva. Válassza a leállítási **esemény eltávolítása** lehetőséget, ha csak a kezdési időpontot szeretné megadni. Ha a **leállítási idő** le van tiltva, válassza a leállítási **esemény hozzáadása** lehetőséget a legördülő lista mellett az engedélyezéshez.
8. Az **időzóna (kötelező)** beállításnál válassza ki a megadott kezdési és befejezési időzónát.  
9. A **Megjegyzések**mezőben adja meg az ütemterv leírását vagy megjegyzéseit. 
10. Kattintson a **Mentés** gombra. 

    ![Heti ütemterv](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Ütemtervek megtekintése a naptárban
A naptár nézetben Kiemelt dátumok és időpontok a következő képen látható módon jeleníthetők meg:

![Ütemtervek a naptár nézetben](../media/how-to-create-schedules/schedules-in-calendar.png)

Kattintson a jobb felső sarokban található **ma** gombra, hogy az aktuális dátumra váltson a naptárban. Kattintson a **balra mutató nyílra** az előző hétre és a **jobbra mutató nyílra** való váltáshoz a naptár következő hetében. 

## <a name="edit-a-schedule"></a>Ütemterv szerkesztése
Ha duplán kattint egy kiemelt ütemtervre a naptárban, vagy kiválasztja a **ceruza** gombot az eszköztáron, megjelenik az **ütemterv szerkesztése** lap. Az ezen a lapon található beállítások frissítése megegyeznek az **ütemezett** ütemterv hozzáadása lapon leírt beállítások frissítésével. [](#add-a-recurring-schedule-weekly) 

![Ütemterv szerkesztése lap](../media/how-to-create-schedules/edit-schedule-page.png)

## <a name="delete-a-schedule"></a>Ütemezés törlése

1. Az ütemterv törléséhez válassza a Kuka (Törlés) gombot az eszköztáron az alábbi képen látható módon:

    ![Törlés gomb az eszköztáron](../media/how-to-create-schedules/delete-schedule-button.png)

    A naptárban az ütemezett dátumok és időpontok bármelyikéhez használhatja a Delete (Törlés) gombot, majd válassza a **Törlés**lehetőséget. 
2. Az **ütemtervek törlése** lapon válassza az **Igen**lehetőséget.

    ![Ütemezett jóváhagyások törlése](../media/how-to-create-schedules/delete-schedules-confirmation.png)




## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Rendszergazdaként, labor-fiókok létrehozása és kezelése](how-to-manage-lab-accounts.md)
- [Labor tulajdonosaként Labs létrehozása és kezelése](how-to-manage-classroom-labs.md)
- [Tesztkörnyezet tulajdonosaként konfigurálhatja és szabályozhatja a labor használatát](how-to-configure-student-usage.md)
- [Labor-felhasználóként az osztályterem Labs eléréséhez](how-to-use-classroom-lab.md)
