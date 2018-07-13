---
title: Az Azure DevTest Labs szolgáltatásban laborszabályzatok kezelése |} A Microsoft Docs
description: Ismerje meg, például a Virtuálisgép-méretek, maximális virtuális gépeket, valamint a felhasználói és leállítás automation laborszabályzatok definiálása.
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
ms.openlocfilehash: 212afbd605e3a16da7be2c04492ec41875ff5b75
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38666839"
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet szabályzatainak kezelését

Az Azure DevTest Labs lehetővé teszi a költségek és a maximális hatékonyság a Labs-környezetben, mivel kezeli az házirendek (beállítások) minden egyes laborban. Ez a cikk lépésről lépésre részletesen ismerteti, hogyan minden házirend beállítása.  

## <a name="set-allowed-virtual-machine-sizes"></a>Engedélyezett a virtuális gép méretének beállítása
A szabályzat beállításához az engedélyezett Virtuálisgép-méretek segít labor hatékonyság engedélyezésével adhatja meg, melyik Virtuálisgép-méretek használata engedélyezett a tesztkörnyezetben. Ez a szabályzat aktiválódik, ha csak Virtuálisgép-méretek ebből a listából virtuális gépek létrehozásához használható.

1. Az a [az Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040)válassza ki a labor, majd válassza ki **Konfigurace a zásady**.

    ![A labor konfigurace a zásady eléréséhez](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. A laborgyakorlat **Konfigurace a zásady** ablaktáblán válassza előbb **engedélyezett a virtuálisgép-méretekről**.
   
    ![Engedélyezett virtuálisgép-méretekről](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Válassza ki **a** ahhoz, hogy ezt a házirendet, és **ki** letiltja azt.

1. Ha engedélyezi ezt a házirendet, válassza ki a lab-ben létrehozható egy vagy több Virtuálisgép-méretek.

1. Kattintson a **Mentés** gombra.

## <a name="set-virtual-machines-per-user"></a>Virtuális gépeihez felhasználónként
A szabályzat **virtuális gépek felhasználónként** lehetővé teszi az egyes felhasználók által létrehozott virtuális gépek maximális számát. Ha egy felhasználó megpróbál létrehozni vagy jogcím egy virtuális Gépet, ha a felhasználói korlátot elérte, egy hibaüzenet azt jelzi, hogy a virtuális gép nem létrehozott/állította. 

1. A laborgyakorlat **Konfigurace a zásady** ablaktáblán válassza előbb **virtuális gépek felhasználónként**.
   
    ![Virtuális gépek felhasználónként](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Válassza ki **Igen** korlátozni a virtuális gépek száma felhasználónként. Ha nem szeretné korlátozni a virtuális gépek száma felhasználónként, válassza ki a **nem**. Ha **Igen**, adjon meg egy numerikus érték, amely a létrehozott, vagy a felhasználó által igényelt virtuális gépek maximális számát. 

1. Válassza ki **Igen** SSD (tartós állapotú lemezt) használó virtuális gépek számának korlátozására. Ha nem szeretné korlátozni a virtuális gépek SSD meghajtókon, válassza ki is **nem**. Ha **Igen**, adjon meg egy értéket, amely SSD segítségével hozható létre virtuális gépek maximális számát jelző. 

1. Kattintson a **Mentés** gombra.

## <a name="set-virtual-machines-per-lab"></a>Labor beállítása virtuális gépet
A szabályzat **tesztkörnyezeti virtuális gépet** lehetővé teszi, hogy az aktuális tesztlabor hozható létre virtuális gépek maximális számát. Ha a felhasználó megkísérli a virtuális gép létrehozása, ha a labor korlátot elérte, egy hibaüzenet azt jelzi, hogy a virtuális gép nem hozható létre. 

1. A laborgyakorlat **Konfigurace a zásady** ablaktáblán válassza előbb **tesztkörnyezeti virtuális gépet**.
   
    ![Tesztkörnyezeti virtuális gépet](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Válassza ki **Igen** korlátozni a labor virtuális gépek száma. Ha nem szeretné korlátozni a labor virtuális gépek száma, válassza ki a **nem**. Ha **Igen**, adjon meg egy numerikus érték, amely a létrehozott, vagy a felhasználó által igényelt virtuális gépek maximális számát. 

1. Válassza ki **Igen** SSD (tartós állapotú lemezt) használó virtuális gépek számának korlátozására. Ha nem szeretné korlátozni a virtuális gépek SSD meghajtókon, válassza ki is **nem**. Ha **Igen**, adjon meg egy értéket, amely SSD segítségével hozható létre virtuális gépek maximális számát jelző. 

1. Kattintson a **Mentés** gombra.

## <a name="set-auto-shutdown"></a>Set automatické vypnutí
Az automatikus leállítási házirend segítségével tesztlabor hatékonyság azáltal, hogy adja meg a labor virtuális gépek leállítása idejét.

1. A laborgyakorlat **Konfigurace a zásady** ablaktáblán válassza előbb **automatikus leállítási**.
   
    ![Automatické vypnutí](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Válassza ki **a** ahhoz, hogy ezt a házirendet, és **ki** letiltja azt.

1. Ha engedélyezi ezt a házirendet, adja meg a jelenlegi laborkörnyezetben található összes virtuális gép leállítása idő (és időzóna).

1. Adja meg **Igen** vagy **nem** értesítés küldése a megadott automatikus leállítási ideje előtt 15 perccel a beállítás. Ha úgy dönt, **Igen**, adjon meg egy webhook URL-végpontot, vagy adja meg, ahol azt szeretné, hogy az értesítés tesznek közzé vagy küldött e-mail-címet. A felhasználó értesítést kap, és arra, hogy a leállás késleltetés van megadva.

   További információ a webhookok: [hozzon létre egy webhook vagy API Azure-függvény](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Kattintson a **Mentés** gombra.

Alapértelmezés szerint engedélyezve van, ezt a házirendet alkalmazza az összes virtuális gépen a jelenlegi tesztkörnyezetben. Távolítsa el ezt a beállítást egy adott virtuális gépről, a virtuális gép kezelése panel megnyitásához, és módosítsa a **automatikus leállítási** beállítás.

## <a name="set-auto-start"></a>Készlet automatikus indítás
Az automatikus indítási házirend adja meg, amikor el kell indítani a virtuális gépek, a jelenlegi tesztkörnyezetben teszi lehetővé.  

1. A laborgyakorlat **Konfigurace a zásady** ablaktáblán válassza előbb **automatikusan elinduló**.
   
    ![Automatikus indítás](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Válassza ki **a** ahhoz, hogy ezt a házirendet, és **ki** letiltja azt.

3. Ha engedélyezi ezt a házirendet, adja meg az ütemezett kezdési idő időzóna és az idő vonatkozik, amelyhez a hét napjait. 

4. Kattintson a **Mentés** gombra.

Ha engedélyezve van, ezt a házirendet nem automatikusan alkalmazható olyan virtuális gépek, a jelenlegi tesztkörnyezetben. Egy adott virtuális gépre alkalmazza ezt a beállítást, nyissa meg a virtuális gép kezelése panelen, és módosítsa a **automatikusan elinduló** beállítás.

## <a name="set-expiration-date"></a>Lejárati dátum megadása
Beállíthat egy lejárati dátum, amikor Ön [a virtuális gép létrehozása](devtest-lab-add-vm.md). A **speciális beállítások**, kattintson a naptár ikonra, amelyen a rendszer automatikusan törli a virtuális gép dátumot adhat meg. Alapértelmezés szerint a virtuális gép soha nem jár le.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
Miután meghatározott, és a tesztkörnyezethez a különböző virtuális gép házirend-beállításokat alkalmazni, néhány dolog, próbálja meg a következő:

* [Megismerheti a közös IP-címek](devtest-lab-shared-ip.md) -ismerteti, hogyan megosztott IP címek segítségével a DevTest Labs szolgáltatásban a labor virtuális gépeken való kapcsolódáshoz szükséges nyilvános IP-címek számának csökkentése érdekében.
* [Költségkezelés konfigurálása](devtest-lab-configure-cost-management.md) -azt ábrázolja, hogyan használhatja a **havi becsült költség Trend** diagram  
  megtekintéséhez az aktuális havi becsült költség dátum és a hónap teljes költségről.
* [Egyéni lemezkép készítése](devtest-lab-create-template.md) – a virtuális gép létrehozásakor megad egy talál, amely egy egyéni rendszerkép vagy a Piactéri lemezképet. Ez a cikk bemutatja, hogyan hozzon létre egy egyéni rendszerkép VHD-fájlból.
* [Piactér képeinek konfigurálása](devtest-lab-configure-marketplace-images.md) – a VM-EK alapján az Azure Marketplace-rendszerképek létrehozása az Azure DevTest Labs támogatja. Ez a cikk bemutatja, hogyan határozhatja meg, ha van ilyen, az Azure Marketplace-rendszerképek lehet a labor virtuális gépek létrehozásakor használt.
* [Virtuális gép létrehozása tesztkörnyezetben](devtest-lab-add-vm.md) -azt ábrázolja, hogyan hozhat létre egy virtuális Gépet egy alaplemezkép (vagy egyéni vagy a Marketplace-en), és a virtuális gépen összetevők használata.

