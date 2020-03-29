---
title: Virtuális gép automatikus indítási beállításainak konfigurálása az Azure DevTest Labsben | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhatja a virtuális gépek automatikus indítási beállításait egy tesztkörnyezetben. Ez a beállítás lehetővé teszi, hogy a tesztkörnyezetben lévő virtuális gépek automatikusan elinduljanak ütemezés szerint.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: 95f810ba16f358c5aabc35e26294cdb3f8c3cca0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807913"
---
# <a name="auto-startup-lab-virtual-machines"></a>Automatikus indítási labor virtuális gépek  
Az Azure DevTest Labs lehetővé teszi, hogy konfigurálja a virtuális gépek a laborban, hogy automatikusan elindul, és leállításütemezés alapján. Az automatikus leállítási beállítások konfigurálásáról az [Azure DevTest Labs tesztkörnyezetében egy tesztkörnyezet automatikus leállítási szabályzatainak kezelése című témakörben olvashat.](devtest-lab-auto-shutdown.md) 

Az automatikus leállítással ellentétben, ahol az összes virtuális gép szerepel a házirend bekapcsolásakor, az automatikus indítási szabályzat megköveteli, hogy a tesztkörnyezet-felhasználó explicit módon válassza ki a virtuális gépet, és engedélyezze ezt az ütemezést. Így nem fog könnyen befutni abba a helyzetbe, ahol a nem kívánt virtuális gépek véletlenül automatikusan elindulnak, és váratlan kiadásokat okoznak.

Ez a cikk bemutatja, hogyan konfigurálhatja az automatikus indítási házirend egy tesztkörnyezetben.

## <a name="configure-autostart-settings-for-a-lab"></a>Tesztkörnyezet automatikus indítási beállításainak konfigurálása 
1. Nyissa meg a labor kezdőlapját. 
2. Válassza a bal oldali menü **Konfiguráció és házirendek parancsát.** 

    ![Konfiguráció és házirendek menü](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. A **Konfiguráció és házirendek** lapon tegye a következő lépéseket:
    
    1. Válassza **a Be** lehetőséget a Virtuális gépek automatikus **indításra való ütemezésének engedélyezése** lehetőséget a tesztkörnyezet automatikus indítási funkciójának engedélyezéséhez. 
    2. Válassza ki a kezdési időpontot (például: 8:00:00 AM) az **Ütemezés kezdő** mezőjéhez. 
    3. Válassza ki a használni kívánt **időzónát.** 
    4. Válassza ki **a hét azon napjait,** amelyeken a virtuális gépeket automatikusan el kell indítani. 
    5. Ezután a beállítások mentéséhez válassza a **Mentés** gombot az eszköztáron. 

        ![Automatikus indítás beállításai](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > Ez a szabályzat nem alkalmazza automatikusan az automatikus indítást a tesztkörnyezetben lévő virtuális gépekre. Egyéni virtuális gépek **engedélyezése,** lépjen a virtuális gép lapjára, és engedélyezze az **automatikus indítást** az adott virtuális gép.

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>Virtuális gép automatikus indításának engedélyezése a laborban
Az alábbi eljárás a virtuális gép nek a tesztkörnyezet autostart-házirendbe való letiltásának lépéseit ismerteti. 

1. A labor kezdőlapján válassza ki a **virtuális gépet** a **Virtuális gépek** listában. 

    ![Konfiguráció és házirendek menü](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. A **Virtuális gép** lapon válassza az **Automatikus indítás** lehetőséget a bal oldali menüben vagy az **Ütemezések** listában. 

    ![Az automatikus start menü kiválasztása](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. Az **Automatikus kezdés** lapon válassza a **Be** lehetőséget a Virtuális gép **automatikus indításra való ütemezésének engedélyezése beállításhoz.**

    ![Automatikus indítás engedélyezése a virtuális gépszámára](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. Ezután a beállítás mentéséhez válassza a **Mentés** gombot az eszköztáron. 


## <a name="next-steps"></a>További lépések
Ha többet szeretne tudni egy tesztkörnyezet konfigurációs automatikus leállítási házirendjéről, [olvassa el az Automatikus leállítási szabályzatok kezelése az Azure DevTest Labs ben című témakört.](devtest-lab-auto-shutdown.md)
