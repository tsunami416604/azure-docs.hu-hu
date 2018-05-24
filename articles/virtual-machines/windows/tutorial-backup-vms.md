---
title: Oktatóanyag – Windows rendszerű virtuális gépek biztonsági mentése az Azure Portalon | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan védheti meg Windows rendszerű virtuális gépeit az Azure Backup segítségével az Azure Portalon.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6ae014597a89c75e4426715227bbb19f1e98a438
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-back-up-and-restore-files-for-windows-virtual-machines-in-azure"></a>Oktatóanyag: A Windows rendszerű virtuális gépek fájljainak biztonsági mentése és visszaállítása az Azure-ban

Adatai védelme érdekében érdemes rendszeres időközönként biztonság mentést végeznie. Az Azure Backup georedundáns helyreállítási tárolókban tárolt helyreállítási pontokat hoz létre. Helyreállítási pontról történő visszaállításkor visszaállíthatja a teljes virtuális gépet, vagy bizonyos fájlokat. Ez a cikk azt ismerteti, hogyan állíthat vissza egy fájlt egy Windows Servert és IIS-t futtató virtuális gépre. Ha még nem rendelkezik virtuális géppel, a [windowsos rövid útmutatóval](quick-create-portal.md) létrehozhat egyet. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Biztonsági másolat készítése egy virtuális gépről
> * Napi biztonsági mentés ütemezése
> * Fájl visszaállítása biztonsági másolatból

## <a name="backup-overview"></a>A biztonsági mentés áttekintése

Amikor az Azure Backup szolgáltatás biztonsági mentési feladatot kezdeményez, elindítja a biztonsági mentési bővítményt, hogy készítsen egy időponthoz kötött pillanatképet. Az Azure Backup szolgáltatás a _VMSnapshot_ bővítményt használja. A rendszer a virtuális gép első biztonsági mentésekor telepíti a bővítményt, ha a virtuális gép fut. Ha a virtuális gép nem fut, a Backup szolgáltatás az alapul szolgáló tárolóról készít pillanatképet (mivel nem történik alkalmazásírás, amikor a virtuális gép le van állítva).

Amikor Windows rendszerű virtuális gépekről készít pillanatképet, a Backup szolgáltatás együttműködik a Kötet árnyékmásolata szolgáltatással (VSS), hogy egységes pillanatképet készítsen a virtuális gép lemezeiről. Amikor az Azure Backup szolgáltatás elkészítette a pillanatképet, az adatok átkerülnek a tárolóba. A maximális hatékonyság érdekében a szolgáltatás csak azokat az adatblokkokat azonosítja és továbbítja, amelyek az előző biztonsági mentés óta változtak.

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
12. A virtuális géphez tartozó **Biztonsági mentés** panelen láthatja a kész helyreállítási pontok számát.

    ![Helyreállítási pontok](./media/tutorial-backup-vms/backup-complete.png)
    
Az első biztonsági mentés körülbelül 20 percet vesz igénybe. Amikor a biztonsági mentés befejeződik, lépjen tovább az oktatóanyag következő részére.

## <a name="recover-a-file"></a>Fájl helyreállítása

Ha véletlenül törölt vagy módosított egy fájlt, a fájlhelyreállítási szolgáltatással visszaállíthatja a fájlt a biztonsági mentési tárolóból. A Fájlhelyreállítás egy, a virtuális gépen futó szkriptet használ a helyreállítási pont helyi meghajtóként való csatlakoztatásához. Ezek a meghajtók 12 órán át csatlakoztatva maradnak, hogy át tudja másolni a fájlokat a helyreállítási pontról, és vissza tudja állítani őket a virtuális gépre.  

Ebben a példában bemutatjuk, hogyan állíthatja helyre az IIS alapértelmezett weboldalán használt képfájlt. 

1. Nyisson meg egy böngészőt, és kapcsolódjon a virtuális gép IP-címéhez, hogy megjelenjen az alapértelmezett IIS-oldal.

    ![Alapértelmezett IIS-weboldal](./media/tutorial-backup-vms/iis-working.png)

2. Csatlakozzon a virtuális géphez.
3. A virtuális gépen nyissa meg a **Fájlkezelőt**, navigáljon az \inetpub\wwwroot helyre, és törölje az **iisstart.png** fájlt.
4. A helyi számítógépen frissítse a böngészőt. Láthatja, hogy a kép eltűnt az alapértelmezett IIS-oldalról.

    ![Alapértelmezett IIS-weboldal](./media/tutorial-backup-vms/iis-broken.png)

5. A helyi számítógépen nyisson meg egy új lapot, majd nyissa meg az [Azure Portalt](https://portal.azure.com).
6. A bal oldali menüben válassza a **Virtuális gépek** elemet, majd válassza ki a virtuális gépet a listából.
8. A virtuális gép paneljének **Beállítások** szakaszában kattintson a **Backup** elemre. Megnyílik a **Biztonsági mentés** panel. 
9. A panel tetején található menüben válassza a **Fájlhelyreállítás** elemet. Megnyílik a **Fájlhelyreállítás** panel.
10. Az **1. lépés: Válassza ki a helyreállítási pontot** lépésben válasszon ki egy helyreállítási pontot a legördülő menüből.
11. A **2. lépés: Töltse le a szkriptet a fájlok megkereséséhez és helyreállításához** lépésben kattintson a **Végrehajtható fájl letöltése** gombra. Mentse a fájlt a **Letöltések** mappába.
12. A helyi számítógépen nyissa meg a **Fájlkezelőt**, keresse meg a **Letöltések** mappát, és másolja a letöltött .exe fájlt. A fájlnév a virtuális gép nevét tartalmazó előtaggal lesz ellátva. 
13. A virtuális gépen (RDP-kapcsolaton keresztül) illessze be az .exe fájlt a virtuális gép asztalára. 
14. Lépjen a virtuális gép asztalára, és kattintson duplán az .exe fájlra. Ez elindít egy parancssort. Csatlakoztassa a helyreállítási pontot olyan fájlmegosztásként, amelyhez hozzá tud férni. A megosztás létrehozása után írja be a **q** betűt a parancssor bezárásához.
15. A virtuális gépen nyissa meg a **Fájlkezelőt**, és navigáljon a fájlmegosztáshoz használt meghajtóbetűjelre.
16. Navigáljon az \inetpub\wwwroot helyre, másolja ki az **iisstart.png** fájlt a fájlmegosztásból, és illessze be az \inetpub\wwwroot helyre. Például másolja az F:\inetpub\wwwroot\iisstart.png fájlt, és illessze be a c:\inetpub\wwwroot helyre a fájl helyreállításához.
17. A helyi számítógépen nyissa meg azt a böngészőlapot, amelyen az alapértelmezett IIS-weboldalt megjelenítő virtuális gép IP-címéhez csatlakozott. Nyomja le a CTRL + F5 billentyűparancsot a böngészőlap frissítéséhez. Most láthatja, hogy a kép vissza lett állítva.
18. A helyi számítógépen térjen vissza az Azure Portal böngészőlapjára, és a **3. lépés: Válassza le a lemezeket a helyreállítás után** lépésben kattintson a **Lemezek leválasztása** gombra. Ha elfelejti elvégezni ezt a lépést, a csatlakoztatási ponttal létesített kapcsolat 12 óra elteltével automatikusan lezárul. Amikor letelik a 12 óra, le kell töltenie egy új szkriptet az új csatlakoztatási pont létrehozásához.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Biztonsági másolat készítése egy virtuális gépről
> * Napi biztonsági mentés ütemezése
> * Fájl visszaállítása biztonsági másolatból

Folytassa a következő oktatóanyaggal, amely a virtuális gépek monitorozását ismerteti.

> [!div class="nextstepaction"]
> [Virtuális gépek szabályozása](tutorial-govern-resources.md)









