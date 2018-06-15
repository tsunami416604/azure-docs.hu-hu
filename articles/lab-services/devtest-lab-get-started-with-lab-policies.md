---
title: Az Azure DevTest Labs alapvető tesztlabor-házirendek kezeléséhez |} Microsoft Docs
description: Útmutató az alapvető házirendeket (beállítások) labor némelyike a DevTest Labs szolgáltatásban
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 40b8fb360be7b08540e25886aaebe7f911607b6d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787548"
---
# <a name="manage-basic-policies-for-a-lab-in-azure-devtest-labs"></a>Az Azure DevTest Labs szolgáltatásban a labor alapvető házirendjeinek kezelése

Azure DevTest Labs segítségével a labs a pazarlás minimalizálásához által az egyes labor (beállítások) házirendek kezelése és költségek szabályozásához. Ebben a cikkben megkezdése házirendek beállítása két legfontosabb házirendek hogyan - virtuális gépek (VM) létrehozott vagy egy felhasználó által igényelt számának korlátozása, és az automatikus rendszerleállítást konfigurálásával. Minden tesztkörnyezeti házirend beállításával megtekintése: [labor házirendeket definiálhat az Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>Egy tesztlabor házirendek az Azure DevTest Labs elérése
A következő lépések végigvezetik a Azure DevTest Labs szolgáltatásban labor házirendek beállítása:

Megtekintése (és módosítása) a házirendek egy tesztkörnyezetet, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.

1. Válassza ki a kívánt labor labs listájának megtekintéséhez.   

1. Válassza ki **konfigurációs és házirendek**.

    ![Házirend-beállítások panelen](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. A **konfigurációs és házirendek** ablaktáblán, megadható beállítások menüt tartalmaz. Ez a cikk foglalkozik beállításait csak **virtuális gépek száma felhasználónként**, **automatikus leállítási**, és **automatikusan elinduló**. A fennmaradó beállításaival kapcsolatos további tudnivalókért lásd: [egy Azure DevTest Labs szolgáltatásban található, amikor az összes házirend kezeléséhez](./devtest-lab-set-lab-policy.md). 
   
## <a name="set-virtual-machines-per-user"></a>Beállítása virtuális gépek száma felhasználónként
A házirend **virtuális gépek száma felhasználónként** megadhatja az egyes felhasználók által létrehozott virtuális gépek maximális számát. Ha egy felhasználó megpróbál létrehozásához vagy a virtuális gépek jogcímek a megadott felhasználói korlátot teljesülésekor, egy hibaüzenet arra utalnak, hogy a virtuális gép nem létrehozott/igényt. 

1. A tesztlabor a **konfigurációs és házirendek** menüjében válassza **virtuális gépek száma felhasználónként**.
   
    ![Virtuális gépek száma felhasználónként](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Válassza ki **Igen** korlátozza a virtuális gépek száma felhasználónként. Ha nem szeretné, hogy a virtuális gépek száma felhasználónként korlátozni, válassza ki a **nem**. Ha **Igen**, adjon meg egy numerikus érték, amely a létrehozott vagy a felhasználó által igényelt virtuális gépek maximális számát. 

1. Válassza ki **Igen** SSD (SSD lemezt) használó virtuális gépek számát. Ha nem szeretné, hogy a virtuális gépek, amelyek is SSD használni, válassza a számának korlátozásához **nem**. Ha **Igen**, adjon meg egy értéket, amely SSD segítségével hozhatók létre virtuális gépek maximális száma. 

1. Kattintson a **Mentés** gombra.

## <a name="set-auto-shutdown"></a>Készlet automatikus rendszerleállítást
Az automatikus rendszerleállítást a szabályzattal meggyőződhetnek labor pazarlás minimalizálásához azáltal, hogy adja meg a labor virtuális gépek leállítása idejét.

1. A tesztlabor a **konfigurációs és házirendek** ablaktáblán válassza előbb **automatikus leállítási**.
   
    ![Automatikus leállítási](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Válassza ki **a** ahhoz, hogy ezt a házirendet, és **ki** le kell tiltani.

1. Ha engedélyezi ezt a házirendet, adja meg az időt (és időzóna) leállítása az összes virtuális gép az aktuális tesztkörnyezetben.

1. Adja meg **Igen** vagy **nem** beállítás értesítés küldése előtt a megadott automatikus rendszerleállítást idő 15 perc. Ha úgy dönt, **Igen**, írja be a webhook URL-végpontjának vagy e-mail cím megadása, ahol azt szeretné, hogy a közzétett vagy küldött értesítést. A felhasználó értesítést kap, és a késleltetés a leállítási lehetőséget kap.

   További információ a webhookok: [webhook vagy API Azure-függvény létrehozása](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Kattintson a **Mentés** gombra.

Alapértelmezés szerint engedélyezve van, ez a házirend vonatkozik az összes virtuális gépen az aktuális tesztkörnyezetben. Távolítsa el ezt a beállítást egy adott virtuális géptől, nyissa meg a virtuális gép felügyeleti ablaktáblán, és módosítsa a **automatikus leállítási** beállítást.

## <a name="set-auto-start"></a>Készlet automatikus indítás
Az automatikus indítási házirend lehetővé teszi, hogy adja meg, amikor el kell indítani a virtuális gépeket az aktuális tesztkörnyezetben.  

1. A tesztlabor a **konfigurációs és házirendek** ablaktáblán válassza előbb **automatikusan elinduló**.
   
    ![Automatikus indítás](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Válassza ki **a** ahhoz, hogy ezt a házirendet, és **ki** le kell tiltani.

3. Ha engedélyezi ezt a házirendet, adja meg az ütemezett kezdési időpontban, időzóna és a napokat a hét, amelyre a idő vonatkozik. 

4. Kattintson a **Mentés** gombra.

Az engedélyezést követően a házirend nem automatikusan érvényben van a virtuális gépek az aktuális tesztkörnyezetben. Egy meglévő virtuális gépre alkalmazza ezt a beállítást, a virtuális gép felügyeleti ablaktábla megnyitása, és módosítsa a **automatikusan elinduló** beállítást.

## <a name="next-steps"></a>További lépések

- [Labor házirendeket definiálhat az Azure DevTest Labs](devtest-lab-set-lab-policy.md) -megtudhatja, hogyan módosíthat más lab-házirendeket.
