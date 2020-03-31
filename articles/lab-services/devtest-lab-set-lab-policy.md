---
title: Laborszabályzatok kezelése az Azure DevTest Labsben | Microsoft dokumentumok
description: Megtudhatja, hogyan definiálhat tesztkörnyezet-házirendeket, például a virtuális gép méreteit, a felhasználónkénti maximális virtuális gépeket és a leállításautomatizálást.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 7756aa64-49ca-45a0-9f90-0fd101c7be85
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: aa0ffbd69e73ddbef72e0eabf79f2736079c3d23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270718"
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Az Azure DevTest Labs összes szabályzatának kezelése

Az Azure DevTest Labs segítségével szabályozhatja a költségeket, és minimalizálhatja a laborokban lévő hulladékot az egyes laborok szabályzatai (beállításai) kezelésével. Ez a cikk részletesen ismerteti az egyes házirendek beállításának részletes beállítását.  

## <a name="set-allowed-virtual-machine-sizes"></a>A virtuális gép engedélyezett méretének beállítása
Az engedélyezett virtuálisgép-méretek beállításának házirendje segít minimalizálni a laborhulladékot azáltal, hogy lehetővé teszi, hogy megadja, mely virtuálisgép-méretek engedélyezettek a laborban. Ha ez a házirend aktiválva van, csak a listából csak virtuális gép méretei használhatók virtuális gépek létrehozásához.

1. Az [Azure Portalon](https://go.microsoft.com/fwlink/p/?LinkID=525040)válasszon ki egy tesztkörnyezetet, majd válassza a **Konfiguráció és szabályzatok**lehetőséget.

    ![A tesztkörnyezet konfigurációjának és házirendjeinek elérése](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. A tesztkörnyezet **Konfigurációs és házirendek** ablaktábláján válassza a **Virtuális gépek engedélyezett méretei**lehetőséget.
   
    ![Engedélyezett virtuális gépek méretei](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. A házirend engedélyezéséhez válassza **a Be** lehetőséget, a házirend letiltásához pedig a **Be** lehetőséget.

1. Ha engedélyezi ezt a házirendet, válasszon ki egy vagy több virtuálisgép-méretet, amely létrehozható a laborban.

1. Kattintson a **Mentés** gombra.

## <a name="set-virtual-machines-per-user"></a>Virtuális gépek beállítása felhasználónként
A **felhasználónkénti virtuális gépek** házirendje lehetővé teszi az egyes felhasználók által létrehozható virtuális gépek számának megadását. Ha egy felhasználó megpróbál létrehozni vagy igényelni egy virtuális gép, ha a felhasználói korlát teljesült, egy hibaüzenet azt jelzi, hogy a virtuális gép nem hozható létre/igényelt. 

1. A tesztkörnyezet **Konfigurációs és házirendek** ablaktábláján válassza a **felhasználónkénti virtuális gépek**lehetőséget.
   
    ![Felhasználónkénti virtuális gépek](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Válassza az **Igen** lehetőséget a felhasználónkénti virtuális gépek számának korlátozásához. Ha nem szeretné korlátozni a felhasználónkénti virtuális gépek számát, válassza a **Nem**lehetőséget. Ha az **Igen**lehetőséget választja, adjon meg egy numerikus értéket, amely a felhasználó által létrehozható vagy igényelt virtuális gépek számát jelzi. 

1. Válassza az **Igen** lehetőséget az SSD -t (ssd-lemez) használó virtuális gépek számának korlátozásához. Ha nem szeretné korlátozni az SSD-t használó virtuális gépek számát, válassza a **Nem**lehetőséget. Ha az **Igen**lehetőséget választja, adjon meg egy értéket, amely az SSD használatával létrehozható virtuális gépek számát jelzi. 

1. Kattintson a **Mentés** gombra.

## <a name="set-virtual-machines-per-lab"></a>Virtuális gépek beállítása laboronként
A **szabályzat a virtuális gépek laboronként** lehetővé teszi, hogy adja meg az aktuális laborhoz létrehozható virtuális gépek számát. Ha egy felhasználó megpróbál létrehozni egy virtuális gép, ha a labor korlát teljesült, egy hibaüzenet azt jelzi, hogy a virtuális gép nem hozható létre. 

1. A labor **konfigurációs és szabályzatok** ablaktábláján válassza a **Virtuális gépek laboronként**lehetőséget.
   
    ![Virtuális gépek laboronként](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Válassza az **Igen** lehetőséget a tesztkörnyezetenkénti virtuális gépek számának korlátozásához. Ha nem szeretné korlátozni a tesztkörnyezetenkénti virtuális gépek számát, válassza a **Nem**lehetőséget. Ha az **Igen**lehetőséget választja, adjon meg egy numerikus értéket, amely a felhasználó által létrehozható vagy igényelt virtuális gépek számát jelzi. 

1. Válassza az **Igen** lehetőséget az SSD -t (ssd-lemez) használó virtuális gépek számának korlátozásához. Ha nem szeretné korlátozni az SSD-t használó virtuális gépek számát, válassza a **Nem**lehetőséget. Ha az **Igen**lehetőséget választja, adjon meg egy értéket, amely az SSD használatával létrehozható virtuális gépek számát jelzi. 

1. Kattintson a **Mentés** gombra.

## <a name="set-auto-shutdown"></a>Automatikus leállítás beállítása
Az automatikus leállítási házirend segít minimalizálni a laborhulladékot azáltal, hogy megadja a tesztkörnyezet virtuális gépei leállításának idejét.

1. A tesztkörnyezet **Konfiguráció s és házirendek** ablaktábláján válassza az **Automatikus leállítás**lehetőséget.
   
    ![Automatikus leállítás](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. A házirend engedélyezéséhez válassza **a Be** lehetőséget, a házirend letiltásához pedig a **Be** lehetőséget.

1. Ha engedélyezi ezt a házirendet, adja meg az időt (és az időzónát) az aktuális tesztkörnyezetösszes virtuális gépének leállításához.

1. Adja meg az **Igen** vagy a **Nem** értéket ahhoz a beállításhoz, hogy a megadott automatikus leállítási idő előtt 15 perccel küldjön értesítést. Ha az **Igen**lehetőséget választja, adjon meg egy webhook URL-végpontot vagy egy e-mail címet, amely megadja, hogy hol szeretné közzétenni vagy elküldeni az értesítést. A felhasználó értesítést kap, és lehetőséget kap a leállítás elhalasztására.

   A webhookokról további információt a [Webhook vagy AZ API Azure-függvény létrehozása című témakörben talál.](../azure-functions/functions-create-a-web-hook-or-api-function.md) 

1. Kattintson a **Mentés** gombra.

Alapértelmezés szerint, ha engedélyezve van, ez a szabályzat az aktuális tesztkörnyezetben lévő összes virtuális gépre vonatkozik. Ha el szeretné távolítani ezt a beállítást egy adott virtuális gépről, nyissa meg a virtuális gép felügyeleti ablaktábláját, és módosítsa az **automatikus leállítási** beállítást.

## <a name="set-auto-shutdown-policy"></a>Automatikus leállítási házirend beállítása
A labor tulajdonosaként konfigurálhatja a leállítási ütemezést a tesztkörnyezetben lévő összes virtuális géphez. Ezzel költségeket takaríthat meg a nem használt (tétlen) gépek futtatásával szemben. Központilag kényszerítheti ki az összes tesztkörnyezetes virtuális gép leállítási házirendjét, de a tesztkörnyezet felhasználóinak is mentheti az egyes gépek ütemezésének beállításával kapcsolatos erőfeszítéseket. Ez a funkció lehetővé teszi, hogy állítsa be a szabályzatot a labor ütemezése kezdve nem nyújt vezérlésteljes vezérlés, a tesztkörnyezet felhasználói. A tesztkörnyezet tulajdonosaként az alábbi lépésekkel konfigurálhatja ezt a házirendet:

1. A tesztkörnyezet kezdőlapján válassza a **Konfiguráció és házirendek**lehetőséget.
2. A bal oldali menü **Ütemezések** szakaszában válassza az **Automatikus leállítás házirend** lehetőséget.
3. Válasszon a lehetőségek közül. A következő szakaszok további részleteket tartalmaznak ezekről a lehetőségekről: A készletházirend csak a laborban létrehozott új virtuális gépekre vonatkozik, a már meglévő virtuális gépekre nem. 

    ![Automatikus leállítási házirend-beállítások](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="user-sets-a-schedule-and-can-opt-out"></a>A felhasználó ütemezést állít be, és leiratkozhat
Ha a tesztkörnyezet et erre a házirendre állítja be, a tesztkörnyezet felhasználói felülbírálhatják vagy leiratkozhatnak a tesztkörnyezetütemezésről. Ez a beállítás biztosítja a tesztkörnyezet felhasználói nak teljes körű vezérlést a virtuális gépek automatikus leállítási ütemezése felett. A laborfelhasználók nem látnak változást a virtuális gép automatikus leállítási ütemezési lapján.

![Automatikus leállítási házirend -1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>A felhasználó ütemezést állít be, és nem tud leiratkozni
Ha a tesztkörnyezetet erre a házirendre állítja be, a tesztkörnyezet felhasználói felülbírálhatják a tesztkörnyezet ütemezését. Azonban nem tilthatják le az automatikus leállítási házirendet. Ez a beállítás gondoskodik arról, hogy a tesztkörnyezet minden gépe automatikus leállítási ütemezés alatt szerepeljön. A laborfelhasználók frissíthetik a virtuális gépek automatikus leállítási ütemezését, és beállíthatják a leállítási értesítéseket.

![Automatikus leállítási házirend -2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>A felhasználónak nincs befolyása a laboradminisztrátor által beállított ütemezésre
Ha a tesztkörnyezet et erre a házirendre állítja be, a tesztkörnyezet felhasználói nem bírálhatják felül a tesztkörnyezet ütemezését, illetve nem iratkozhatnak le róluk. Ez a beállítás a labor adminisztrátora számára a teljes ellenőrzést az ütemezés minden gép a laborban. A laborfelhasználók csak automatikus leállítási értesítéseket állíthatnak be a virtuális gépekhez.

![Automatikus leállítási házirend -3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="set-autostart"></a>Automatikus indítás beállítása
Az automatikus indítási szabályzat lehetővé teszi, hogy adja meg, ha a virtuális gépek az aktuális tesztkörnyezetben kell elindítani.  

1. A tesztkörnyezet **Konfiguráció s és házirendek** ablaktábláján válassza az **Automatikus indítás**lehetőséget.
   
    ![Automatikus indítás](./media/devtest-lab-set-lab-policy/auto-start.png)

2. A házirend engedélyezéséhez válassza **a Be** lehetőséget, a házirend letiltásához pedig a **Be** lehetőséget.

3. Ha engedélyezi ezt a házirendet, adja meg az ütemezett kezdési időpontot, az időzónát és a hét azon napjait, amelyekre az időpont vonatkozik. 

4. Kattintson a **Mentés** gombra.

Ha engedélyezve van, ez a szabályzat nem lesz automatikusan alkalmazva az aktuális tesztkörnyezetben lévő virtuális gépekre. Ha ezt a beállítást egy adott virtuális gépre szeretné alkalmazni, nyissa meg a virtuális gép felügyeleti ablaktábláját, és módosítsa az **automatikus indítási** beállítást.

## <a name="set-expiration-date"></a>Lejárati dátum beállítása
A virtuális gép létrehozásakor beállíthat egy lejárati [dátumot.](devtest-lab-add-vm.md) A **Speciális beállítások csoportban**válassza ki a naptár ikonját, és adja meg azt a dátumot, amikor a virtuális gép automatikusan törlődik. Alapértelmezés szerint a virtuális gép soha nem jár le.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
Miután definiálta és alkalmazta a tesztkörnyezet különböző virtuálisgép-házirend-beállításait, az alábbiakat kell megpróbálnia:

* [A megosztott IP-címek ismertetése](devtest-lab-shared-ip.md) – Bemutatja, hogyan használják a megosztott IP-címeket a DevTest Labs a laborvirtuális gépekhez való csatlakozáshoz szükséges nyilvános IP-címek számának minimalizálása érdekében.
* [Költséggazdálkodás konfigurálása](devtest-lab-configure-cost-management.md) – A **Havi becsült költségtrend** diagram használatának szemléltetése  
  az aktuális hónap becsült költségének és a várható hónap végi költségnek a megtekintéséhez.
* [Egyéni lemezkép létrehozása](devtest-lab-create-template.md) – Virtuális gép létrehozásakor meg kell adnia egy bázist, amely lehet egyéni lemezkép vagy Piactér-lemezkép. Ez a cikk bemutatja, hogyan hozhat létre egyéni lemezképet egy vhd fájlból.
* [Marketplace-lemezképek konfigurálása](devtest-lab-configure-marketplace-images.md) – Az Azure DevTest Labs támogatja a virtuális gépek létrehozását az Azure Marketplace-lemezképek alapján. Ez a cikk bemutatja, hogyan adja meg, hogy mely, ha van ilyen, az Azure Marketplace-lemezképek használható virtuális gépek létrehozásakor egy tesztkörnyezetben.
* [Virtuális gép létrehozása egy tesztkörnyezetben](devtest-lab-add-vm.md) – bemutatja, hogyan hozhat létre virtuális gép egy alaprendszerképből (egyéni vagy Piactér), és hogyan működik a virtuális gép összetevők.

