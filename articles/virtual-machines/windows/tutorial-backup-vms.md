---
title: A Windows Azure virtuális gépek biztonsági mentése |} Microsoft Docs
description: A Windows virtuális gépek védelme készítésével Azure Backup segítségével.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 12859bf967cf8de1b57ab9dfd5c0bd080806f2eb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="back-up-windows-virtual-machines-in-azure"></a>Készítsen biztonsági másolatot a Windows virtuális gépek Azure-ban

Adatai védelme érdekében érdemes rendszeres időközönként biztonság mentést végeznie. Az Azure Backup georedundáns helyreállítási tárolókban tárolt helyreállítási pontokat hoz létre. Helyreállítási pontról történő visszaállításkor visszaállíthatja a teljes virtuális gépet, vagy csak bizonyos fájlokat. Ez a cikk ismerteti a Windows Server és az IIS rendszert futtató virtuális gép egyetlen fájl visszaállítása. Ha még nem rendelkezik a virtuális gépek használatához, hozhat létre egy a [Windows gyors üzembe helyezés](quick-create-portal.md). Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Biztonsági másolat készítése egy virtuális gépről
> * Napi biztonsági mentés ütemezése
> * Fájl visszaállítása biztonsági másolatból




## <a name="backup-overview"></a>A biztonsági mentés áttekintése

Az Azure Backup szolgáltatás a biztonsági mentési feladatot indít, amikor elindítja a tartalék mellék időpontban pillanatképének elkészítéséhez. Az Azure Backup szolgáltatás használ a _VMSnapshot_ bővítmény. A rendszer a virtuális gép első biztonsági mentésekor telepíti a bővítményt, ha a virtuális gép fut. Ha a virtuális gép nem fut, a Backup szolgáltatás az alapul szolgáló tárolóról készít pillanatképet (mivel nem történik alkalmazásírás, amikor a virtuális gép le van állítva).

Windows virtuális gépek pillanatkép létrehozása van folyamatban, amikor a biztonsági mentési szolgáltatás koordinálja a a kötet árnyékmásolata szolgáltatás (VSS) a virtuális gép lemezeinek konzisztens pillanatképének eléréséhez. Amikor az Azure Backup szolgáltatás elkészítette a pillanatképet, az adatok átkerülnek a tárolóba. A maximális hatékonyság érdekében a szolgáltatás csak azokat az adatblokkokat azonosítja és továbbítja, amelyek az előző biztonsági mentés óta változtak.

Ha az adatátvitel befejeződött, a rendszer eltávolítja a pillanatképet, és létrehoz egy helyreállítási pontot.


## <a name="create-a-backup"></a>Biztonsági mentés létrehozása
Hozzon létre egy egyszerű, ütemezett napi biztonsági mentést egy Recovery Services-tárolóba. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A bal oldali menüben válassza a **Virtuális gépek** elemet. 
3. Válasszon egy virtuális gépet a listából, amelyről biztonsági mentést kíván készíteni.
4. A virtuális gép paneljének **Beállítások** szakaszában kattintson a **Backup** elemre. Megnyílik a **Biztonsági mentés engedélyezése** panel.
5. A **Recovery Services-tárolóban** kattintson az **Új létrehozása** elemre, és adja meg az új tároló nevét. Az új tároló ugyanabban az Erőforráscsoportban és ugyanazon a helyen jön létre, ahol a virtuális gép is van.
6. Kattintson a **Biztonsági mentési szabályzat** lehetőségre. Ehhez a példához hagyja változatlanul az alapértelmezett beállításokat, és kattintson az **OK** gombra.
7. A **Biztonsági mentés engedélyezése** panelen kattintson a **Biztonsági mentés engedélyezése** elemre. Ez létrehoz egy napi biztonsági mentést az alapértelmezett ütemezés alapján.
10. Az első helyreállítási pont létrehozásához a **Biztonsági mentés** panelen kattintson a **Biztonsági mentés most** elemre.
11. A **Biztonsági mentés most** panelen kattintson a naptár ikonra, használja a naptárvezérlőt annak kiválasztására, hogy meddig kívánja megőrizni a helyreállítási pontot, majd kattintson a **Biztonsági mentés** elemre.
12. A virtuális géphez tartozó **Biztonsági mentés** panelen látni fogja a kész helyreállítási pontok számát.

    ![Helyreállítási pontok](./media/tutorial-backup-vms/backup-complete.png)
    
Az első biztonsági mentés körülbelül 20 percet vesz igénybe. Amikor a biztonsági mentés befejeződik, lépjen tovább az oktatóanyag következő részére.

## <a name="recover-a-file"></a>A fájl helyreállításához

Ha véletlenül törölt vagy módosított egy fájlt, a fájlhelyreállítási szolgáltatással visszaállíthatja a fájlt a biztonsági mentési tárolóból. A fájlok helyreállítása a helyreállítási pont helyi meghajtóként csatlakoztatni a virtuális gép futó parancsfájlt használ. Ezek a meghajtók 12 órán át csatlakoztatva maradnak, hogy át tudja másolni a fájlokat a helyreállítási pontról, és vissza tudja állítani őket a virtuális gépre.  

Ebben a példában megmutatjuk, hogyan helyreállítása a bináris fájl, amely az IIS alapértelmezett weblap szolgál. 

1. Nyisson meg egy böngészőt, és kapcsolódjon az IP-cím, a virtuális gép alapértelmezett IIS lap megjelenítéséhez.

    ![Alapértelmezett IIS-weblap](./media/tutorial-backup-vms/iis-working.png)

2. Csatlakoztassa a virtuális Gépet.
3. Nyissa meg a virtuális gép **Fájlkezelőben** , \inetpub\wwwroot lépjen, és törölje a fájlt **iisstart.png**.
4. A helyi számítógépen a böngésző meg arról, hogy a lemezképet, az alapértelmezett IIS lapon már nem frissíthetők.

    ![Alapértelmezett IIS-weblap](./media/tutorial-backup-vms/iis-broken.png)

5. A helyi számítógépen nyisson meg egy új lapot, és nyissa meg a a [Azure-portálon](https://portal.azure.com).
6. A bal oldali menüben válasszon ki **virtuális gépek** , és válassza ki a virtuális Gépet a listából.
8. A virtuális gép paneljének **Beállítások** szakaszában kattintson a **Backup** elemre. Megnyílik a **Biztonsági mentés** panel. 
9. A panel tetején található menüben válassza a **Fájlhelyreállítás** elemet. Megnyílik a **Fájlhelyreállítás** panel.
10. Az **1. lépés: Válassza ki a helyreállítási pontot** lépésben válasszon ki egy helyreállítási pontot a legördülő menüből.
11. A **2. lépés: Töltse le a szkriptet a fájlok megkereséséhez és helyreállításához** lépésben kattintson a **Végrehajtható fájl letöltése** gombra. Mentse a fájlt a **letölti** mappát.
12. Nyissa meg a helyi számítógép **Fájlkezelőben** , és keresse meg a **letölti** mappát, másolja a letöltött .exe fájlt. A fájlnevet fogja szerepelnie, hogy a virtuális gép nevét. 
13. A virtuális gépen (keresztül a távoli ASZTAL kapcsolaton keresztül) illessze be az .exe fájlt az asztalra, a virtuális gép. 
14. Keresse meg a virtuális gép asztalán, és az .exe kattintson rá duplán. Ez elindítani a parancssort, és csatlakoztassa a fájlmegosztást, amelyet el tud érni, a helyreállítási pont. Miután befejezte a megosztás létrehozása, írja be a **q** kattintva zárja be a parancssorba.
15. Nyissa meg a virtuális gép **Fájlkezelőben** , és keresse meg a meghajtóbetűjelet, a fájlmegosztás lett megadva.
16. Navigáljon a \inetpub\wwwroot, és másolja **iisstart.png** a következő fájl megosztani, és illessze be \inetpub\wwwroot. Például F:\inetpub\wwwroot\iisstart.png másolja és illessze be a fájl helyreállítását c:\inetpub\wwwroot.
17. A helyi számítógépen nyissa meg a böngészőlapon ahol csatlakozik az IIS alapértelmezett lap megjeleníti a virtuális IP-címét. Nyomja le a CTRL + F5 billentyűparancsot a böngészőlap frissítéséhez. Most látnia kell, hogy a kép helyre lett állítva.
18. A helyi számítógépen térjen vissza az Azure Portal böngészőlapjára, és a **3. lépés: Válassza le a lemezeket a helyreállítás után** lépésben kattintson a **Lemezek leválasztása** gombra. Ha ezt a lépést, a kapcsolat a csatlakozási pont nem automatikusan Bezárás 12 óra elteltével. Amikor letelik a 12 óra, le kell töltenie egy új szkriptet az új csatlakoztatási pont létrehozásához.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Biztonsági másolat készítése egy virtuális gépről
> * Napi biztonsági mentés ütemezése
> * Fájl visszaállítása biztonsági másolatból

Folytassa a következő oktatóanyaggal, amely a virtuális gépek monitorozását ismerteti.

> [!div class="nextstepaction"]
> [Virtuális gépek szabályozása](tutorial-govern-resources.md)









