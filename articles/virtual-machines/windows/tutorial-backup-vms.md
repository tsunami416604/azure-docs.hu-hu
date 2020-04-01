---
title: Oktatóanyag – A Windows virtuális gépek biztonsági és biztonsági kezelése az Azure Portalon
description: Ez az oktatóanyag bemutatja, hogyan védheti meg Windows rendszerű virtuális gépeit az Azure Backup segítségével az Azure Portalon.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 89ed0bad2729a9e0983d4ef7f8a53faa4f5426ac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79415642"
---
# <a name="tutorial-back-up-and-restore-files-for-windows-virtual-machines-in-azure"></a>Oktatóanyag: A Windows rendszerű virtuális gépek fájljainak biztonsági mentése és visszaállítása az Azure-ban

Adatai védelme érdekében érdemes rendszeres időközönként biztonság mentést végeznie. Az Azure Backup georedundáns helyreállítási tárolókban tárolt helyreállítási pontokat hoz létre. Helyreállítási pontról történő visszaállításkor visszaállíthatja a teljes virtuális gépet, vagy bizonyos fájlokat. Ez a cikk azt ismerteti, hogyan állíthat vissza egy fájlt egy Windows Servert és IIS-t futtató virtuális gépre. Ha még nem rendelkezik virtuális géppel, a [windowsos rövid útmutatóval](quick-create-portal.md) létrehozhat egyet. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Biztonsági másolat készítése egy virtuális gépről
> * Napi biztonsági mentés ütemezése
> * Fájl visszaállítása biztonsági másolatból

## <a name="backup-overview"></a>A biztonsági mentés áttekintése

Amikor az Azure Backup szolgáltatás biztonsági mentési feladatot kezdeményez, elindítja a biztonsági mentési bővítményt, hogy készítsen egy időponthoz kötött pillanatképet. Az Azure Backup szolgáltatás a [VMSnapshot bővítményt](https://docs.microsoft.com/azure/virtual-machines/extensions/vmsnapshot-windows)használja. A rendszer a virtuális gép első biztonsági mentésekor telepíti a bővítményt, ha a virtuális gép fut. Ha a virtuális gép nem fut, a Backup szolgáltatás az alapul szolgáló tárolóról készít pillanatképet (mivel nem történik alkalmazásírás, amikor a virtuális gép le van állítva).

Amikor Windows rendszerű virtuális gépekről készít pillanatképet, a Backup szolgáltatás együttműködik a Kötet árnyékmásolata szolgáltatással (VSS), hogy egységes pillanatképet készítsen a virtuális gép lemezeiről. Amikor az Azure Backup szolgáltatás elkészítette a pillanatképet, az adatok átkerülnek a tárolóba. A maximális hatékonyság érdekében a szolgáltatás csak azokat az adatblokkokat azonosítja és továbbítja, amelyek az előző biztonsági mentés óta változtak.

Ha az adatátvitel befejeződött, a rendszer eltávolítja a pillanatképet, és létrehoz egy helyreállítási pontot.

## <a name="create-a-backup"></a>Biztonsági mentés létrehozása
Hozzon létre egy egyszerű, ütemezett napi biztonsági mentést egy Recovery Services-tárolóba. 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
1. A bal oldali menüben válassza a **Virtuális gépek** elemet. 
1. Válasszon egy virtuális gépet a listából, amelyről biztonsági mentést kíván készíteni.
1. A virtuális gép panelen a **Műveletek** csoportban kattintson a **Biztonsági mentés gombra.** Megnyílik **a Biztonsági másolat engedélyezése** panel.
1. A **Recovery Services-tárolóban** kattintson az **Új létrehozása** elemre, és adja meg az új tároló nevét. Egy új tároló jön létre ugyanabban az erőforráscsoportban és helyen, mint a virtuális gép.
1. A **Biztonsági másolat megadása csoportban**tartsa meg az alapértelmezett **(Új) DailyPolicy házirendet,** majd kattintson a Biztonsági másolat **engedélyezése**gombra.
1. Az első helyreállítási pont létrehozásához a **Biztonsági mentés** panelen kattintson a **Biztonsági mentés most** elemre.
1. A **Biztonsági másolat most** panelen kattintson a naptár ikonra, a naptár vezérlővel adja meg, hogy mennyi ideig maradjon meg a visszaállítási pont, majd kattintson az **OK**gombra.
1. A **virtuális gép biztonsági mentési** paneljén láthatja a teljes visszaállítási pontok számát.


    ![Helyreállítási pontok](./media/tutorial-backup-vms/backup-complete.png)
    
Az első biztonsági mentés körülbelül 20 percet vesz igénybe. Amikor a biztonsági mentés befejeződik, lépjen tovább az oktatóanyag következő részére.

## <a name="recover-a-file"></a>Fájl helyreállítása

Ha véletlenül törölt vagy módosított egy fájlt, a fájlhelyreállítási szolgáltatással visszaállíthatja a fájlt a biztonsági mentési tárolóból. A Fájlhelyreállítás egy, a virtuális gépen futó szkriptet használ a helyreállítási pont helyi meghajtóként való csatlakoztatásához. Ezek a meghajtók 12 órán át csatlakoztatva maradnak, hogy át tudja másolni a fájlokat a helyreállítási pontról, és vissza tudja állítani őket a virtuális gépre.  

Ebben a példában bemutatjuk, hogyan állíthatja helyre az IIS alapértelmezett weboldalán használt képfájlt. 

1. Nyisson meg egy böngészőt, és kapcsolódjon a virtuális gép IP-címéhez, hogy megjelenjen az alapértelmezett IIS-oldal.

    ![Alapértelmezett IIS-weboldal](./media/tutorial-backup-vms/iis-working.png)

1. Csatlakozzon a virtuális géphez.
1. A virtuális gépen nyissa meg a **Fájlkezelőt**, navigáljon az \inetpub\wwwroot helyre, és törölje az **iisstart.png** fájlt.
1. A helyi számítógépen frissítse a böngészőt. Láthatja, hogy a kép eltűnt az alapértelmezett IIS-oldalról.

    ![Alapértelmezett IIS-weboldal](./media/tutorial-backup-vms/iis-broken.png)

1. A helyi számítógépen nyisson meg egy új lapot, majd nyissa meg az [Azure Portalt](https://portal.azure.com).
1. A bal oldali menüben válassza a **Virtuális gépek** elemet, majd válassza ki a virtuális gépet a listából.
1. A virtuális gép panelen a **Műveletek** csoportban kattintson a **Biztonsági mentés gombra.** Megnyílik a **Biztonsági mentés** panel. 
1. A panel tetején található menüben válassza a **Fájlhelyreállítás** elemet. Megnyílik a **Fájlhelyreállítás** panel.
1. Az **1. lépés: Válassza ki a helyreállítási pontot** lépésben válasszon ki egy helyreállítási pontot a legördülő menüből.
1. A **2. lépés: Töltse le a szkriptet a fájlok megkereséséhez és helyreállításához** lépésben kattintson a **Végrehajtható fájl letöltése** gombra. Másolja a fájl jelszavát, és mentse biztonságos helyre.
1. A helyi számítógépen nyissa meg a **Fájlkezelőt**, keresse meg a **Letöltések** mappát, és másolja a letöltött .exe fájlt. A fájlnév a virtuális gép nevét tartalmazó előtaggal lesz ellátva. 
1. A virtuális gépen (az RDP-kapcsolat használatával) illessze be az .exe fájlt a virtuális gép asztalára. 
1. Lépjen a virtuális gép asztalára, és kattintson duplán az .exe fájlra. Elindul a parancssor. A program a helyreállítási pontot fájlmegosztásként csatlakoztatja, amelyet el érhet. A megosztás létrehozása után írja be a **q** betűt a parancssor bezárásához.
1. A virtuális gépen nyissa meg a **Fájlkezelőt**, és navigáljon a fájlmegosztáshoz használt meghajtóbetűjelre.
1. Navigáljon az \inetpub\wwwroot helyre, másolja ki az **iisstart.png** fájlt a fájlmegosztásból, és illessze be az \inetpub\wwwroot helyre. Például másolja az F:\inetpub\wwwroot\iisstart.png fájlt, és illessze be a c:\inetpub\wwwroot helyre a fájl helyreállításához.
1. A helyi számítógépen nyissa meg azt a böngészőlapot, amelyen az alapértelmezett IIS-weboldalt megjelenítő virtuális gép IP-címéhez csatlakozott. Nyomja le a CTRL + F5 billentyűparancsot a böngészőlap frissítéséhez. Most láthatja, hogy a kép vissza lett állítva.
1. A helyi számítógépen térjen vissza az Azure Portal böngészőlapjára, és a **3. lépés: Válassza le a lemezeket a helyreállítás után** lépésben kattintson a **Lemezek leválasztása** gombra. Ha elfelejti elvégezni ezt a lépést, a csatlakoztatási ponttal létesített kapcsolat 12 óra elteltével automatikusan lezárul. A 12 óra elteltével le kell töltenie egy új parancsfájlt egy új csatlakoztatási pont létrehozásához.





## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Biztonsági másolat készítése egy virtuális gépről
> * Napi biztonsági mentés ütemezése
> * Fájl visszaállítása biztonsági másolatból

Folytassa a következő oktatóanyaggal, amely a virtuális gépek monitorozását ismerteti.

> [!div class="nextstepaction"]
> [Virtuális gépek szabályozása](tutorial-govern-resources.md)









