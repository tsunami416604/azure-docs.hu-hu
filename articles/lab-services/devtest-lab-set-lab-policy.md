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
ms.openlocfilehash: aa0ffbd69e73ddbef72e0eabf79f2736079c3d23
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622023"
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet szabályzatainak kezelését

Az Azure DevTest Labs lehetővé teszi a költségek és minimálisra csökkenti a pazarlást a Labs-környezetben, mivel kezeli az egyes lab (beállítások) házirendeket. Ez a cikk lépésről lépésre részletesen ismerteti, hogyan minden házirend beállítása.  

## <a name="set-allowed-virtual-machine-sizes"></a>Engedélyezett a virtuális gép méretének beállítása
A szabályzat beállításához az engedélyezett Virtuálisgép-méretek segít labor hatékonyság engedélyezésével adhatja meg, melyik Virtuálisgép-méretek használata engedélyezett a tesztkörnyezetben. Ez a szabályzat aktiválódik, ha csak Virtuálisgép-méretek ebből a listából virtuális gépek létrehozásához használható.

1. Az a [az Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)válassza ki a labor, majd válassza ki **Konfigurace a zásady**.

    ![A labor konfigurace a zásady eléréséhez](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. A laborgyakorlat **Konfigurace a zásady** ablaktáblán válassza előbb **engedélyezett a virtuálisgép-méretekről**.
   
    ![Engedélyezett virtuálisgép-méretekről](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Válassza ki **a** ahhoz, hogy ezt a házirendet, és **ki** letiltja azt.

1. Ha engedélyezi ezt a házirendet, válassza ki a lab-ben létrehozható egy vagy több Virtuálisgép-méretek.

1. Kattintson a **Mentés** gombra.

## <a name="set-virtual-machines-per-user"></a>Virtuális gépeihez felhasználónként
A szabályzat **virtuális gépek felhasználónként** megadhatja az egyes felhasználók által létrehozott virtuális gépek számát. Ha egy felhasználó megpróbál létrehozni vagy jogcím egy virtuális Gépet, ha a felhasználói korlátot elérte, egy hibaüzenet azt jelzi, hogy a virtuális gép nem létrehozott/állította. 

1. A laborgyakorlat **Konfigurace a zásady** ablaktáblán válassza előbb **virtuális gépek felhasználónként**.
   
    ![Virtuális gépek felhasználónként](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Válassza ki **Igen** korlátozni a virtuális gépek száma felhasználónként. Ha nem szeretné korlátozni a virtuális gépek száma felhasználónként, válassza ki a **nem**. Ha **Igen**, adjon meg egy numerikus érték, amely virtuális gépek létrehozása vagy a felhasználó által igényelt. 

1. Válassza ki **Igen** SSD (tartós állapotú lemezt) használó virtuális gépek számának korlátozására. Ha nem szeretné korlátozni a virtuális gépek SSD meghajtókon, válassza ki is **nem**. Ha **Igen**, adja meg, amely SSD segítségével hozható létre virtuális gépek számát jelző érték beolvasása. 

1. Kattintson a **Mentés** gombra.

## <a name="set-virtual-machines-per-lab"></a>Labor beállítása virtuális gépet
A szabályzat **tesztkörnyezeti virtuális gépet** megadhatja, hogy az aktuális tesztlabor hozható létre virtuális gépek számát. Ha egy felhasználó megpróbál egy virtuális gép létrehozása, ha a labor korlátot elérte, egy hibaüzenet azt jelzi, hogy a virtuális gép nem hozható létre. 

1. A laborgyakorlat **Konfigurace a zásady** ablaktáblán válassza előbb **tesztkörnyezeti virtuális gépet**.
   
    ![Tesztkörnyezeti virtuális gépet](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Válassza ki **Igen** korlátozni a labor virtuális gépek száma. Ha nem szeretné korlátozni a labor virtuális gépek száma, válassza ki a **nem**. Ha **Igen**, adjon meg egy numerikus érték, amely virtuális gépek létrehozása vagy a felhasználó által igényelt. 

1. Válassza ki **Igen** SSD (tartós állapotú lemezt) használó virtuális gépek számának korlátozására. Ha nem szeretné korlátozni a virtuális gépek SSD meghajtókon, válassza ki is **nem**. Ha **Igen**, adja meg, amely SSD segítségével hozható létre virtuális gépek számát jelző érték beolvasása. 

1. Kattintson a **Mentés** gombra.

## <a name="set-auto-shutdown"></a>Automatikus leállítás beállítása
Az automatikus leállítási házirend segítségével tesztlabor hatékonyság azáltal, hogy adja meg a labor virtuális gépek leállítása idejét.

1. A laborgyakorlat **Konfigurace a zásady** ablaktáblán válassza előbb **automatikus leállítás**.
   
    ![Automatikus leállítás](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Válassza ki **a** ahhoz, hogy ezt a házirendet, és **ki** letiltja azt.

1. Ha engedélyezi ezt a házirendet, adja meg a jelenlegi laborkörnyezetben található összes virtuális gép leállítása idő (és időzóna).

1. Adja meg **Igen** vagy **nem** értesítés küldése a megadott automatikus leállítási időpont előtt 15 perc a következő kapcsoló számára. Ha úgy dönt, **Igen**, adjon meg egy webhook URL-végpontot, vagy adja meg, ahol azt szeretné, hogy az értesítés tesznek közzé vagy küldött e-mail-címet. A felhasználó értesítést kap, és elhalasztja a leállítást lehetősége van megadva.

   További információ a webhookok: [hozzon létre egy webhook vagy API Azure-függvény](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Kattintson a **Mentés** gombra.

Alapértelmezés szerint engedélyezve van, ezt a házirendet alkalmazza az összes virtuális gépen a jelenlegi tesztkörnyezetben. Távolítsa el ezt a beállítást egy adott virtuális gépről, a virtuális gép kezelése panel megnyitásához, és módosítsa a **automatikus leállítás** beállítás.

## <a name="set-auto-shutdown-policy"></a>Az automatikus leállítás szabályzat beállítása
Labortulajdonosként leállítási ütemezés szerint konfigurálhatja a virtuális gépek a lab-ben. Ezzel a módszerrel mentheti az éppen nem használt gépek fut (inaktív) költségek. A leállítási házirend az összes a labor virtuális gépeken, hanem központilag is menteni a labor felhasználók részéről az erőfeszítés egyes gépeikhez egy ütemezés beállításával kényszerítheti. Ez a funkció lehetővé teszi, hogy állítson be a házirendet a Labs-környezeti ütemezés kezdő ajánlat teljes hozzáférés nem szabályozza a labor számára. Labortulajdonosként konfigurálja a házirendet a következő lépések végrehajtásával:

1. Válassza ki a labor kezdőlapja, **Konfigurace a zásady**.
2. Válassza ki **automatikus leállítási házirend** a a **ütemezések** szakaszában a bal oldali menüben.
3. Válassza ki a beállítások. A következő részek biztosítanak a beállításokkal kapcsolatos további részletekért: A set-szabályzat vonatkozik, csak a lab-ben létrehozott új virtuális gépek és a már meglévő virtuális gépet. 

    ![Automatikus leállítási házirend-beállítások](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Felhasználó beállítja az ütemezés és kikapcsolhatja az újat
Ha a labor-e a házirend, a labor felhasználók felülbírálás vagy tilthatják le a Labs-környezeti ütemezés. Ez a beállítás a virtuális gépek automatikus leállítási ütemezés teljes hozzáférést biztosít a labor felhasználók. Labor felhasználók nem változik a virtuális gépek automatikus leállítási ütemezés lapon talál.

![Az automatikus leállítás szabályzat 1. lehetőség –](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Felhasználói csoportok ütemezés szerint, és nem vesznek
Ha a labor-e a házirend, a labor felhasználók felülbírálhatják a Labs-környezeti ütemezés. Azonban ezek nem tilthatók le automatikus leállítási házirend. Ez a beállítás gondoskodik arról, hogy a lab-ben minden gép van-e egy automatikus leállítási ütemezés szerint. Labor a felhasználók a virtuális gépek automatikus leállítási ütemezés frissítése, és leállítást értesítések beállítása.

![Az automatikus leállítás szabályzat 2. lehetőség –](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Felhasználó rendelkezik nem szabályozza a labor-rendszergazda által beállított ütemezés szerint
Ha a labor-e a házirend, a labor felhasználók nem bírálja felül, és tilthatják le a Labs-környezeti ütemezés. Ez a beállítás lehetővé teszi a labor rendszergazdai a teljes az ütemezés a lab-ben minden gép. Labor felhasználók csak értesítéseket állíthat be automatikus leállítás a virtuális gépek számára.

![Automatikus leállítási házirend-beállításként – 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="set-autostart"></a>Készlet automatikus indítása
Az automatikus indítási házirend adja meg, amikor el kell indítani a virtuális gépek, a jelenlegi tesztkörnyezetben teszi lehetővé.  

1. A laborgyakorlat **Konfigurace a zásady** ablaktáblán válassza előbb **Autostart**.
   
    ![Automatikus indítás](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Válassza ki **a** ahhoz, hogy ezt a házirendet, és **ki** letiltja azt.

3. Ha engedélyezi ezt a házirendet, adja meg az ütemezett kezdési idő időzóna és az idő vonatkozik, amelyhez a hét napjait. 

4. Kattintson a **Mentés** gombra.

Ha engedélyezve van, ezt a házirendet nem automatikusan alkalmazható olyan virtuális gépek, a jelenlegi tesztkörnyezetben. Egy adott virtuális gépre alkalmazza ezt a beállítást, nyissa meg a virtuális gép kezelése panelen, és módosítsa a **Autostart** beállítás.

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

