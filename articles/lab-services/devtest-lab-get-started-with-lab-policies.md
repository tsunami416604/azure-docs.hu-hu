---
title: Az Azure DevTest Labs szolgáltatásban alapszintű laborszabályzatok kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan megadhatja DevTest Labs szolgáltatásban létrehozott tesztkörnyezet alapszintű szabályzatainak (beállítások)
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
ms.openlocfilehash: 8cc529fbf9b24335be1bec07f81c732ced7a2b72
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51227008"
---
# <a name="manage-basic-policies-for-a-lab-in-azure-devtest-labs"></a>Az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet alapszintű szabályzatok kezelése

Az Azure DevTest Labs lehetővé teszi, hogy a költségek és a maximális hatékonyság a Labs-környezetben, mivel kezeli az egyes lab (beállítások) házirendeket. Ebben a cikkben, első lépésként a házirendek két legfontosabb házirendek létrehozásával – száma a virtuális gépek (VM) létrehozott, vagy egyetlen felhasználó által igényelt, és automatikus leállítás beállítása. Minden labor szabályának beállításáról, tekintse meg [laborszabályzatok definiálása az Azure DevTest Labs szolgáltatásban](devtest-lab-set-lab-policy.md).  

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>A laborszabályzatok az Azure DevTest Labs szolgáltatásban elérése
A következő lépések végigvezetik az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet szabályzatainak beállítása:

Megtekintése (és módosítása) a tesztkörnyezet szabályzatainak, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.

1. Tesztkörnyezetek listájában jelölje ki a kívánt tesztkörnyezetben.   

1. Válassza ki **Konfigurace a zásady**.

    ![A házirend-beállítások panel](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. A **Konfigurace a zásady** ablaktábla tartalmaz egy Ön által megadott beállítások menüjében. Ez a cikk ismerteti, csak a beállítások **virtuális gépek felhasználónként**, **automatikus leállítási**, és **automatikusan elinduló**. A többi beállítás kapcsolatos további információkért lásd: [Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet szabályzatainak kezelését](./devtest-lab-set-lab-policy.md). 
   
## <a name="set-virtual-machines-per-user"></a>Virtuális gépeihez felhasználónként
A szabályzat **virtuális gépek felhasználónként** lehetővé teszi, hogy adja meg az egyes felhasználók által létrehozott virtuális gépek maximális számát. Ha egy felhasználó megpróbál létrehozni vagy jogcím egy virtuális Gépet, ha a felhasználói korlátot elérte, egy hibaüzenet azt jelzi, hogy a virtuális gép nem létrehozott/állította. 

1. A laborgyakorlat **Konfigurace a zásady** menüjében válassza **virtuális gépek felhasználónként**.
   
    ![Virtuális gépek felhasználónként](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Válassza ki **Igen** korlátozni a virtuális gépek száma felhasználónként. Ha nem szeretné korlátozni a virtuális gépek száma felhasználónként, válassza ki a **nem**. Ha **Igen**, adjon meg egy numerikus érték, amely a létrehozott, vagy a felhasználó által igényelt virtuális gépek maximális számát. 

1. Válassza ki **Igen** SSD (tartós állapotú lemezt) használó virtuális gépek számának korlátozására. Ha nem szeretné korlátozni a virtuális gépek SSD meghajtókon, válassza ki is **nem**. Ha **Igen**, adjon meg egy értéket, amely SSD segítségével hozható létre virtuális gépek maximális számát jelző. 

1. Kattintson a **Mentés** gombra.

## <a name="set-auto-shutdown"></a>Set automatické vypnutí
Az automatikus leállítási a szabályzattal meggyőződhetnek labor hatékonyság azáltal, hogy adja meg a labor virtuális gépek leállítása idejét.

1. A laborgyakorlat **Konfigurace a zásady** ablaktáblán válassza előbb **automatikus leállítási**.
   
    ![Automatické vypnutí](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Válassza ki **a** ahhoz, hogy ezt a házirendet, és **ki** letiltja azt.

1. Ha engedélyezi ezt a házirendet, adja meg a jelenlegi laborkörnyezetben található összes virtuális gép leállítása idő (és időzóna).

1. Adja meg **Igen** vagy **nem** értesítés küldése a megadott automatikus leállítási ideje előtt 15 perccel a beállítás. Ha úgy dönt, **Igen**, adjon meg egy webhook URL-végpontot, vagy e-mail-címét adja meg, ahol azt szeretné, hogy a közzétett vagy küldött értesítést. A felhasználó értesítést kap, és arra, hogy a leállás késleltetés van megadva.

   További információ a webhookok: [hozzon létre egy webhook vagy API Azure-függvény](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Kattintson a **Mentés** gombra.

Alapértelmezés szerint engedélyezve van, ezt a házirendet alkalmazza az összes virtuális gépen a jelenlegi tesztkörnyezetben. Távolítsa el ezt a beállítást egy adott virtuális gépről, a virtuális gép kezelése panel megnyitásához, és módosítsa a **automatikus leállítási** beállítás.

## <a name="set-auto-start"></a>Készlet automatikus indítás
Az automatikus indítási házirend lehetővé teszi, el kell indítani a virtuális gépek, a jelenlegi tesztkörnyezetben történő telepítésekor.  

1. A laborgyakorlat **Konfigurace a zásady** ablaktáblán válassza előbb **automatikusan elinduló**.
   
    ![Automatikus indítás](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Válassza ki **a** ahhoz, hogy ezt a házirendet, és **ki** letiltja azt.

3. Ha engedélyezi ezt a házirendet, adja meg az ütemezett kezdési idő időzóna és az idő vonatkozik, amelyhez a hét napjait. 

4. Kattintson a **Mentés** gombra.

Ha engedélyezve van, ezt a házirendet nem automatikusan alkalmazható olyan virtuális gépek, a jelenlegi tesztkörnyezetben. A alkalmazni ezt a beállítást meglévő virtuális géphez, nyissa meg a virtuális gép kezelése panelen, és módosítsa a **automatikusan elinduló** beállítás.

## <a name="next-steps"></a>További lépések

- [Laborszabályzatok definiálása az Azure DevTest Labs szolgáltatásban](devtest-lab-set-lab-policy.md) – ismerje meg, hogyan más laborszabályzatok módosításához.
