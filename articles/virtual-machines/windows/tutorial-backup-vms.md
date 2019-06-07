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
ms.date: 06/06/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 1efa76cf6bb29dfac473ad6ce31cefdfee0c52ec
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2019
ms.locfileid: "66808784"
---
# <a name="tutorial-back-up-and-restore-files-for-windows-virtual-machines-in-azure"></a>Oktatóanyag: Biztonsági mentése és visszaállítása az Azure-beli Windows virtuális gépek fájljait

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
1. A bal oldali menüben válassza a **Virtuális gépek** elemet. 
1. Válasszon egy virtuális gépet a listából, amelyről biztonsági mentést kíván készíteni.
1. A virtuális gép paneljén a a **műveletek** területén kattintson **Backup**. Megnyílik a **Biztonsági mentés engedélyezése** panel.
1. A **Recovery Services-tárolóban** kattintson az **Új létrehozása** elemre, és adja meg az új tároló nevét. Az új tároló ugyanabban az erőforráscsoportban és helyen, ahol a virtuális gép jön létre.
1. A **biztonsági mentési házirend kiválasztása**, tartsa meg az alapértelmezett **(új) DailyPolicy**, és kattintson a **biztonsági mentés engedélyezése**.
1. Az első helyreállítási pont létrehozásához a **Biztonsági mentés** panelen kattintson a **Biztonsági mentés most** elemre.
1. Az a **biztonsági mentés** panelen kattintson a naptár ikonra, és válassza ki a visszaállítási pont mennyi ideig őrzi meg a rendszer, majd kattintson a Naptár vezérlőelem használatával **OK**.
1. Az a **Backup** a virtuális gép paneljén láthatja, amelyek a teljes visszaállítási pontok száma.


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
1. A virtuális gép paneljén a a **műveletek** területén kattintson **Backup**. Megnyílik a **Biztonsági mentés** panel. 
1. A panel tetején található menüben válassza a **Fájlhelyreállítás** elemet. Megnyílik a **Fájlhelyreállítás** panel.
1. A **1. lépés: Válassza ki a helyreállítási pont**, válasszon egy helyreállítási pontot a legördülő listából.
1. A **2. lépés: Fájlok tallózására és helyreállítására szolgáló szkript letöltése**, kattintson a **végrehajtható fájl letöltése** gombra. Másolja a fájlhoz tartozó jelszót, és mentse azt biztonságos helyen.
1. A helyi számítógépen nyissa meg a **Fájlkezelőt**, keresse meg a **Letöltések** mappát, és másolja a letöltött .exe fájlt. A fájlnév a virtuális gép nevét tartalmazó előtaggal lesz ellátva. 
1. A virtuális gép (az RDP-kapcsolat használatával) illessze be a virtuális gép az asztalhoz az .exe fájlra. 
1. Lépjen a virtuális gép asztalára, és kattintson duplán az .exe fájlra. A parancssor használatával indul el. A program csatlakoztatja a helyreállítási pont keresztül elérhető fájlmegosztásként. A megosztás létrehozása után írja be a **q** betűt a parancssor bezárásához.
1. A virtuális gépen nyissa meg a **Fájlkezelőt**, és navigáljon a fájlmegosztáshoz használt meghajtóbetűjelre.
1. Navigáljon az \inetpub\wwwroot helyre, másolja ki az **iisstart.png** fájlt a fájlmegosztásból, és illessze be az \inetpub\wwwroot helyre. Például másolja az F:\inetpub\wwwroot\iisstart.png fájlt, és illessze be a c:\inetpub\wwwroot helyre a fájl helyreállításához.
1. A helyi számítógépen nyissa meg azt a böngészőlapot, amelyen az alapértelmezett IIS-weboldalt megjelenítő virtuális gép IP-címéhez csatlakozott. Nyomja le a CTRL + F5 billentyűparancsot a böngészőlap frissítéséhez. Most láthatja, hogy a kép vissza lett állítva.
1. A helyi számítógépen térjen vissza az Azure Portalon, majd a böngészőlapon **3. lépés: A lemezek leválasztása a helyreállítás után** kattintson a **lemezek leválasztása** gombra. Ha elfelejti elvégezni ezt a lépést, a csatlakoztatási ponttal létesített kapcsolat 12 óra elteltével automatikusan lezárul. E 12 óra elteltével le kell töltenie egy új szkriptet az új csatlakoztatási pont létrehozásához.





## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Biztonsági másolat készítése egy virtuális gépről
> * Napi biztonsági mentés ütemezése
> * Fájl visszaállítása biztonsági másolatból

Folytassa a következő oktatóanyaggal, amely a virtuális gépek monitorozását ismerteti.

> [!div class="nextstepaction"]
> [Virtuális gépek szabályozása](tutorial-govern-resources.md)









