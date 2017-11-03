---
title: "Kezelheti a labor házirendeket a Azure DevTest Labs szolgáltatásban |} Microsoft Docs"
description: "Megtudhatja, hogyan határozza meg a labor házirendek, például a Virtuálisgép-méretek, minden felhasználó és a leállítási automation maximális virtuális gépeket."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 7756aa64-49ca-45a0-9f90-0fd101c7be85
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: tarcher
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 328a4d893637d7150807855e118b485a2c3bbfc5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Egy Azure DevTest Labs szolgáltatásban található, amikor az összes házirend kezeléséhez

Az Azure DevTest Labs lehetővé teszi a költségek szabályozásához, és a fejlesztőlaborokban lévő pazarlás minimalizálásához (beállítások) házirendek kezelése által az egyes labor. Ez a cikk részletes részletesen ismerteti, hogyan minden házirend beállítása.  

## <a name="set-allowed-virtual-machine-sizes"></a>Virtuális gépek méretét engedélyezett beállítása
Az engedélyezett Virtuálisgép-méretek beállításához a szabályzattal meggyőződhetnek labor pazarlás minimalizálásához engedélyezésével adhatja meg, melyik Virtuálisgép-méretek engedélyezettek a laborkörnyezetben. Ha ez a házirend aktív, csak Virtuálisgép-méretek a listáról a virtuális gépek létrehozásához használható.

1. A tesztlabor a **konfigurációs és házirendek** panelen válassza **engedélyezett virtuális gépek méretét**.
   
    ![Engedélyezett virtuális gépek méretét](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Válassza ki **a** ahhoz, hogy ezt a házirendet, és **ki** le kell tiltani.

1. Ha engedélyezi ezt a házirendet, válassza ki a tesztkörnyezetben hozható létre egy vagy több Virtuálisgép-méretek.

1. Kattintson a **Mentés** gombra.

## <a name="set-virtual-machines-per-user"></a>Beállítása virtuális gépek száma felhasználónként
A házirend **virtuális gépek száma felhasználónként** megadhatja az egyes felhasználók által létrehozott virtuális gépek maximális számát. Ha egy felhasználó megpróbál létrehozásához vagy a virtuális gépek jogcímek a megadott felhasználói korlátot teljesülésekor, egy hibaüzenet arra utalnak, hogy a virtuális gép nem létrehozott/igényt. 

1. A tesztlabor a **konfigurációs és házirendek** menüjében válassza **virtuális gépek száma felhasználónként**.
   
    ![Virtuális gépek száma felhasználónként](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Válassza ki **Igen** korlátozza a virtuális gépek száma felhasználónként. Ha nem szeretné, hogy a virtuális gépek száma felhasználónként korlátozni, válassza ki a **nem**. Ha **Igen**, adjon meg egy numerikus érték, amely a létrehozott vagy a felhasználó által igényelt virtuális gépek maximális számát. 

1. Válassza ki **Igen** SSD (SSD lemezt) használó virtuális gépek számát. Ha nem szeretné, hogy a virtuális gépek, amelyek is SSD használni, válassza a számának korlátozásához **nem**. Ha **Igen**, adjon meg egy értéket, amely SSD segítségével hozhatók létre virtuális gépek maximális száma. 

1. Kattintson a **Mentés** gombra.

## <a name="set-virtual-machines-per-lab"></a>Beállítása virtuális gépek száma tesztkörnyezetenként
A házirend **virtuális gépek száma tesztkörnyezetenként** megadhatja, hogy az aktuális tesztkörnyezetben hozható létre virtuális gépek maximális számát. Ha a felhasználó megkísérli a virtuális gép létrehozása a tesztkörnyezeti korlátot teljesülésekor, egy hibaüzenet azt jelzi, hogy a virtuális gép nem hozható létre. 

1. A tesztlabor a **konfigurációs és házirendek** menü **virtuális gépek száma tesztkörnyezetenként**.
   
    ![Virtuális gépek száma tesztkörnyezetenként](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Válassza ki **Igen** korlátozza a virtuális gépek száma tesztkörnyezetenként számát. Ha nem szeretné, hogy a virtuális gépek száma tesztkörnyezetenként számát korlátozni, válassza ki a **nem**. Ha **Igen**, adjon meg egy numerikus érték, amely a létrehozott vagy a felhasználó által igényelt virtuális gépek maximális számát. 

1. Válassza ki **Igen** SSD (SSD lemezt) használó virtuális gépek számát. Ha nem szeretné, hogy a virtuális gépek, amelyek is SSD használni, válassza a számának korlátozásához **nem**. Ha **Igen**, adjon meg egy értéket, amely SSD segítségével hozhatók létre virtuális gépek maximális száma. 

1. Kattintson a **Mentés** gombra.

## <a name="set-auto-shutdown"></a>Készlet automatikus rendszerleállítást
Az automatikus rendszerleállítást a szabályzattal meggyőződhetnek labor pazarlás minimalizálásához azáltal, hogy adja meg a labor virtuális gépek leállítása idejét.

1. A tesztlabor a **konfigurációs és házirendek** panelen válassza **automatikus leállítási**.
   
    ![Automatikus leállítási](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Válassza ki **a** ahhoz, hogy ezt a házirendet, és **ki** le kell tiltani.

1. Ha engedélyezi ezt a házirendet, adja meg az időt (és időzóna) leállítása az összes virtuális gép az aktuális tesztkörnyezetben.

1. Adja meg **Igen** vagy **nem** beállítás értesítés küldése előtt a megadott automatikus rendszerleállítást idő 15 perc. Ha megad **Igen**, írja be a webhook URL-végpontjának az értesítések fogadásához. További információ a webhookok: [webhook vagy API Azure-függvény létrehozása](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Kattintson a **Mentés** gombra.

    Alapértelmezés szerint engedélyezve van, ez a házirend vonatkozik az összes virtuális gépen az aktuális tesztkörnyezetben. Távolítsa el ezt a beállítást egy adott virtuális géptől, nyissa meg a virtuális gép panelt, és módosítsa a **automatikus leállítási** beállítás 

## <a name="set-auto-start"></a>Készlet automatikus indítás
Az automatikus indítási házirend lehetővé teszi, hogy adja meg, amikor el kell indítani a virtuális gépeket az aktuális tesztkörnyezetben.  

1. A tesztlabor a **konfigurációs és házirendek** panelen válassza **automatikusan elinduló**.
   
    ![Automatikus indítás](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Válassza ki **a** ahhoz, hogy ezt a házirendet, és **ki** le kell tiltani.

3. Ha engedélyezi ezt a házirendet, adja meg az ütemezett kezdési időpontban, időzóna és a napokat a hét, amelyre a idő vonatkozik. 

4. Kattintson a **Mentés** gombra.

    Az engedélyezést követően a házirend nem automatikusan érvényben van a virtuális gépek az aktuális tesztkörnyezetben. Alkalmazza ezt a beállítást egy adott virtuális géphez, nyissa meg a virtuális gép panelt, és módosítsa a **automatikusan elinduló** beállítás 

## <a name="set-expiration-date"></a>Lejárat dátuma
Megadhat egy lejárati dátum, [a virtuális gép létrehozása](devtest-lab-add-vm.md). A **speciális beállítások**, válassza ki a naptár ikonra, és adjon meg egy dátum, amelyen a virtuális gép automatikusan törölve lesznek.  Alapértelmezés szerint a virtuális Gépet soha nem jár le.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Következő lépések
Miután definiálva, és a tesztkörnyezet a különböző virtuális gép házirend-beállításokat alkalmazza, az alábbiakban ezután próbálja meg a következőkről:

* [Megosztott IP-címek megértéséhez](devtest-lab-shared-ip.md) -ismerteti, hogyan megosztott IP címek szolgálnak a DevTest Labs szolgáltatásban a labor virtuális gépeken való kapcsolódáshoz szükséges nyilvános IP-címek számának minimalizálása érdekében.
* [Költség felügyeletének konfigurálásához](devtest-lab-configure-cost-management.md) -bemutatja, hogyan használható a **havi becsült Költségtrend** diagram  
  az aktuális hónap megtekintheti a következőre becsült költség-date és a várható befejezési hónap költsége.
* [Hozzon létre egyéni lemezkép](devtest-lab-create-template.md) – a virtuális gépek létrehozásakor megad egy talál, amely lehet, vagy egy egyéni lemezképet, vagy egy Piactéri lemezképhez. Ez a cikk bemutatja, hogyan egyéni lemezkép létrehozása a VHD-fájl.
* [Konfigurálja a piactéren elérhető rendszerkép](devtest-lab-configure-marketplace-images.md) – Azure DevTest Labs támogatja az Azure piactéren elérhető rendszerkép alapján virtuális gépek létrehozását. Ez a cikk bemutatja, hogyan határozhatja meg, ha bármely, az Azure piactéren elérhető rendszerkép lehet egy, amikor a virtuális gépek létrehozásakor használt.
* [Hozzon létre egy virtuális Gépet egy tesztkörnyezetben](devtest-lab-add-vm-with-artifacts.md) -bemutatja, hogyan hozható létre a virtuális gépek alapjául szolgáló lemezképhez (vagy egyéni vagy Marketplace), és hogyan működnek együtt a virtuális gépen.

