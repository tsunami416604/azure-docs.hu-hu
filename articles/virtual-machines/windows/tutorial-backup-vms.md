---
title: "A Windows Azure virtuális gépek biztonsági mentése |} Microsoft Docs"
description: "A Windows virtuális gépek védelme készítésével Azure Backup segítségével."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 8e58a2290e5034ef393f65cbcddb86e18cf4a6ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-windows-virtual-machines-in-azure"></a>Készítsen biztonsági másolatot a Windows virtuális gépek Azure-ban

Adatai védelme érdekében érdemes rendszeres időközönként biztonság mentést végeznie. Az Azure Backup georedundáns helyreállítás tárolók tárolt helyreállítási pontokat hoz létre. Helyreállításakor a helyreállítási pont, az egész virtuális gép vagy csak adott fájlokat is helyreállíthatja. Ez a cikk ismerteti a Windows Server és az IIS rendszert futtató virtuális gép egyetlen fájl visszaállítása. Ha még nem rendelkezik a virtuális gépek használatához, hozhat létre egy a [Windows gyors üzembe helyezés](quick-create-portal.md). Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Készítsen biztonsági másolatot a virtuális gépek
> * Napi biztonsági mentés ütemezése
> * A fájl visszaállítása biztonsági másolatból




## <a name="backup-overview"></a>A biztonsági mentés áttekintése

Az Azure Backup szolgáltatás a biztonsági mentési feladatot indít, amikor elindítja a tartalék mellék időpontban pillanatképének elkészítéséhez. Az Azure Backup szolgáltatás használ a _VMSnapshot_ bővítmény. Ha a virtuális gép fut, a virtuális gép első biztonsági mentés során telepítve a bővítmény. Ha a virtuális gép nem fut, a biztonsági mentési szolgáltatás pillanatképet készít a az alapul szolgáló tárolási (mert nem alkalmazás írások végrehajthatók, miközben a virtuális gép le van állítva).

Windows virtuális gépek pillanatkép létrehozása van folyamatban, amikor a biztonsági mentési szolgáltatás koordinálja a a kötet árnyékmásolata szolgáltatás (VSS) a virtuális gép lemezeinek konzisztens pillanatképének eléréséhez. Miután az Azure Backup szolgáltatás a pillanatfelvételt, az adatátvitel a tárolóba. Hatékonyságának maximalizálása érdekében a szolgáltatás azonosítja, és csak az adatok a korábbi biztonsági mentés óta módosult blokkok átvitele.

Ha az adatok átvitele befejeződött, a rendszer eltávolítja a pillanatkép, és egy helyreállítási pontot hoz létre.


## <a name="create-a-backup"></a>Biztonsági mentés létrehozása
Hozzon létre egy egyszerű, ütemezett napi biztonsági mentést egy Recovery Services-tárolóba. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A bal oldali menüben válassza a **Virtuális gépek** elemet. 
3. Válasszon egy virtuális gépet a listából, amelyről biztonsági mentést kíván készíteni.
4. A virtuális gép panelen a a **beállítások** kattintson **biztonsági mentés**. A **biztonságimásolat-készítés engedélyezése** panel nyílik meg.
5. A **Recovery Services-tároló**, kattintson a **hozzon létre új** , és adja meg az új tároló nevét. Egy új tárolót a azonos erőforráscsoport és a virtuális gép jön létre.
6. Kattintson a **biztonsági mentési házirend**. Ehhez a példához hagyja az alapértelmezett beállításokat, és kattintson a **OK**.
7. Az a **biztonságimásolat-készítés engedélyezése** panelen kattintson a **biztonsági mentés engedélyezése**. Ezzel létrehoz egy alapértelmezett ütemezés szerint a napi biztonsági mentéshez.
10. Az első helyreállítási pont létrehozásához a **biztonsági mentés** panelen kattintson **biztonsági mentés most**.
11. Az a **biztonsági mentés most** panelen kattintson a naptár ikonra, válassza ki az elmúlt nap során ez a helyreállítási pont őrzi meg, majd kattintson a havinaptár-vezérlő segítségével **biztonsági mentés**.
12. Az a **biztonsági mentés** a virtuális gép paneljén láthatja, amelyek a teljes helyreállítási pontok száma.

    ![Helyreállítási pontok](./media/tutorial-backup-vms/backup-complete.png)
    
Az első biztonsági mentés körülbelül 20 percet vesz igénybe. A biztonsági mentés befejezése után ez az oktatóanyag következő része folytassa a műveletet.

## <a name="recover-a-file"></a>A fájl helyreállításához

Ha véletlenül törli vagy módosítja a fájlt, a fájlok helyreállítása használatával állítsa vissza a fájlt a biztonsági mentési tárolóból. A fájlok helyreállítása a helyreállítási pont helyi meghajtóként csatlakoztatni a virtuális gép futó parancsfájlt használ. Ezek a meghajtók marad csatlakoztatott 12 óra, hogy a fájlok másolását a helyreállítási pont, és állítsa vissza őket a virtuális Gépet.  

Ebben a példában megmutatjuk, hogyan helyreállítása a bináris fájl, amely az IIS alapértelmezett weblap szolgál. 

1. Nyisson meg egy böngészőt, és kapcsolódjon az IP-cím, a virtuális gép alapértelmezett IIS lap megjelenítéséhez.

    ![Alapértelmezett IIS-weblap](./media/tutorial-backup-vms/iis-working.png)

2. Csatlakoztassa a virtuális Gépet.
3. Nyissa meg a virtuális gép **Fájlkezelőben** , \inetpub\wwwroot lépjen, és törölje a fájlt **iisstart.png**.
4. A helyi számítógépen a böngésző meg arról, hogy a lemezképet, az alapértelmezett IIS lapon már nem frissíthetők.

    ![Alapértelmezett IIS-weblap](./media/tutorial-backup-vms/iis-broken.png)

5. A helyi számítógépen nyisson meg egy új lapot, és nyissa meg a a [Azure-portálon](https://portal.azure.com).
6. A bal oldali menüben válasszon ki **virtuális gépek** , és jelölje ki a virtuális gép űrlapot a listában.
8. A virtuális gép panelen a a **beállítások** kattintson **biztonsági mentés**. A **biztonsági mentés** panel nyílik meg. 
9. A panel tetején menüben válasszon ki **fájlhelyreállítás**. A **fájlhelyreállítás** panel nyílik meg.
10. A **1. lépés: válassza ki a helyreállítási pont**, a legördülő listán válasszon egy helyreállítási pontot.
11. A **2. lépés: Töltse le a parancsprogramot, keresse meg, majd a fájlok helyreállítása**, kattintson a **végrehajtható fájl letöltése** gombra. Mentse a fájlt a **letölti** mappát.
12. Nyissa meg a helyi számítógép **Fájlkezelőben** , és keresse meg a **letölti** mappát, másolja a letöltött .exe fájlt. A fájlnevet fogja szerepelnie, hogy a virtuális gép nevét. 
13. A virtuális gépen (keresztül a távoli ASZTAL kapcsolaton keresztül) illessze be az .exe fájlt az asztalra, a virtuális gép. 
14. Keresse meg a virtuális gép asztalán, és az .exe kattintson rá duplán. Ez elindítani a parancssort, és csatlakoztassa a fájlmegosztást, amelyet el tud érni, a helyreállítási pont. Miután befejezte a megosztás létrehozása, írja be a **q** kattintva zárja be a parancssorba.
15. Nyissa meg a virtuális gép **Fájlkezelőben** , és keresse meg a meghajtóbetűjelet, a fájlmegosztás lett megadva.
16. Navigáljon a \inetpub\wwwroot, és másolja **iisstart.png** a következő fájl megosztani, és illessze be \inetpub\wwwroot. Például F:\inetpub\wwwroot\iisstart.png másolja és illessze be a fájl helyreállítását c:\inetpub\wwwroot.
17. A helyi számítógépen nyissa meg a böngészőlapon ahol csatlakozik az IIS alapértelmezett lap megjeleníti a virtuális IP-címét. Nyomja le a CTRL + F5 csak a böngésző lap frissítése. Most látnia kell, hogy a kép helyre lett állítva.
18. A helyi számítógépen, térjen vissza az Azure portál és a böngészőlapon **3. lépés: a lemez leválasztása a helyreállítás után** kattintson a **lemez leválasztása** gombra. Ha ezt a lépést, a kapcsolat a csatlakozási pont nem automatikusan Bezárás 12 óra elteltével. E 12 óra elteltével le kell töltenie egy új parancsfájl egy új csatlakoztatási pont létrehozásához.


## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Készítsen biztonsági másolatot a virtuális gépek
> * Napi biztonsági mentés ütemezése
> * A fájl visszaállítása biztonsági másolatból

Előzetes további virtuális gépek figyelésével kapcsolatos következő oktatóanyagot.

> [!div class="nextstepaction"]
> [Virtuális gépek figyelése](tutorial-monitoring.md)









