---
title: "Azure-beli Linux rendszerű virtuális gépek biztonsági mentése | Microsoft Docs"
description: "Linux rendszerű virtuális gépek védelme az Azure Backup használatával készített biztonsági mentésekkel."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 1c07fa40964fdcbae6ec1cbbbf77094753956cf1
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2018
---
# <a name="back-up-linux--virtual-machines-in-azure"></a>Linux rendszerű virtuális gépek biztonsági mentése az Azure-ban

Adatai védelme érdekében érdemes rendszeres időközönként biztonság mentést végeznie. Az Azure Backup georedundáns helyreállítási tárolókban tárolt helyreállítási pontokat hoz létre. Helyreállítási pontról történő visszaállításkor visszaállíthatja a teljes virtuális gépet, vagy csak bizonyos fájlokat. Ez a cikk azt ismerteti, hogyan állíthat vissza egy fájlt egy nginxet futtató Linux rendszerű virtuális gépre. Ha még nem rendelkezik virtuális géppel, a [linuxos rövid útmutatóval](quick-create-cli.md) létrehozhat egyet. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Biztonsági másolat készítése egy virtuális gépről
> * Napi biztonsági mentés ütemezése
> * Fájl visszaállítása biztonsági másolatból



## <a name="backup-overview"></a>A biztonsági mentés áttekintése

Amikor az Azure Backup szolgáltatás biztonsági mentést kezdeményez, elindítja a biztonsági mentési bővítményt, hogy készítsen egy időponthoz kötött pillanatképet. Az Azure Backup szolgáltatás a _VMSnapshotLinux_ Linux-bővítményt használja. A rendszer a virtuális gép első biztonsági mentésekor telepíti a bővítményt, ha a virtuális gép fut. Ha a virtuális gép nem fut, a Backup szolgáltatás az alapul szolgáló tárolóról készít pillanatképet (mivel nem történik alkalmazásírás, amikor a virtuális gép le van állítva).

Alapértelmezés szerint az Azure Backup a fájlrendszerrel konzisztens biztonsági másolatot készít a Linux rendszerű virtuális gépről, de úgy is lehet konfigurálni, hogy [alkalmazáskonzisztens biztonsági mentést készítsem szkript előtti és utáni keretrendszerrel](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). Amikor az Azure Backup szolgáltatás elkészítette a pillanatképet, az adatok átkerülnek a tárolóba. A maximális hatékonyság érdekében a szolgáltatás csak azokat az adatblokkokat azonosítja és továbbítja, amelyek az előző biztonsági mentés óta változtak.

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

## <a name="restore-a-file"></a>Fájl visszaállítása

Ha véletlenül törölt vagy módosított egy fájlt, a fájlhelyreállítási szolgáltatással visszaállíthatja a fájlt a biztonsági mentési tárolóból. A Fájlhelyreállítás egy, a virtuális gépen futó szkriptet használ a helyreállítási pont helyi meghajtóként való csatlakoztatásához. Ezek a meghajtók 12 órán át csatlakoztatva maradnak, hogy át tudja másolni a fájlokat a helyreállítási pontról, és vissza tudja állítani őket a virtuális gépre.  

Ebben a példában bemutatjuk, hogyan állíthatja vissza az alapértelmezett /var/www/html/index.nginx-debian.html nginx-weblapot. A virtuális gép nyilvános IP-címe ebben a példában a következő: *13.69.75.209*. A virtuális gép IP-címét a következő paranccsal találhatja meg:

 ```bash 
 az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
 ```

 
1. A helyi számítógépen nyisson meg egy böngészőt, és írja be a virtuális gép nyilvános IP-címét az alapértelmezett nginx-weblap megtekintéséhez.

    ![Alapértelmezett nginx-weblap](./media/tutorial-backup-vms/nginx-working.png)

1. Jelentkezzen be a virtuális gépre SSH-val.

    ```bash
    ssh 13.69.75.209
    ```
2. Törölje a /var/www/html/index.nginx-debian.html fájlt.

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```
    
4. A helyi számítógépen frissítse a böngészőt a CTRL + F5 billentyűkombináció lenyomásával. Láthatja, hogy az alapértelmezett nginx-lap eltűnt.

    ![Alapértelmezett nginx-weblap](./media/tutorial-backup-vms/nginx-broken.png)
    
1. A helyi számítógépen jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
6. A bal oldali menüben válassza a **Virtuális gépek** elemet. 
7. Válassza ki a virtuális gépet a listából.
8. A virtuális gép paneljének **Beállítások** szakaszában kattintson a **Backup** elemre. Megnyílik a **Biztonsági mentés** panel. 
9. A panel tetején található menüben válassza a **Fájlhelyreállítás** elemet. Megnyílik a **Fájlhelyreállítás** panel.
10. Az **1. lépés: Válassza ki a helyreállítási pontot** lépésben válasszon ki egy helyreállítási pontot a legördülő menüből.
11. A **2. lépés: Töltse le a szkriptet a fájlok megkereséséhez és helyreállításához** lépésben kattintson a **Végrehajtható fájl letöltése** gombra. Mentse a letöltött fájlt a helyi számítógépen.
7. Kattintson a **Szkript letöltése** lehetőségre a szkript helyi letöltéséhez.
8. Nyisson meg egy Bash-parancssort, és írja be a következő parancsot, a *Linux_myVM_05-05-2017.sh* helyén a letöltött szkript helyes elérési útjával és fájlnevével, az *azureuser* helyén a virtuális géphez tartozó felhasználónévvel, valamint a *13.69.75.209* helyén a virtuális gép nyilvános IP-címével.
    
    ```bash
    scp Linux_myVM_05-05-2017.sh azureuser@13.69.75.209:
    ```
    
9. A helyi számítógépen nyisson egy SSH-kapcsolatot a virtuális géphez.

    ```bash
    ssh 13.69.75.209
    ```
    
10. A virtuális gépen adjon végrehajtási engedélyeket a szkripthez.

    ```bash
    chmod +x Linux_myVM_05-05-2017.sh
    ```
    
11. A virtuális gépen futtassa a szkriptet a helyreállítási pont fájlrendszerként való csatlakoztatásához.

    ```bash
    ./Linux_myVM_05-05-2017.sh
    ```
    
12. A szkript kimenetében megtalálható a csatlakoztatási pont elérési útja. A kimeneti ehhez hasonlóan néz ki:

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
                          
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
                         
    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath 

    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170505191055/Volume1

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

12. A virtuális gépen másolja vissza az alapértelmezett nginx-weblapot a csatlakoztatási pontról oda, ahonnan törölte a fájlt.

    ```bash
    sudo cp ~/myVM-20170505191055/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```
    
17. A helyi számítógépen nyissa meg azt a böngészőlapot, amelyen az alapértelmezett nginx-lapot megjelenítő virtuális gép IP-címéhez csatlakozott. Nyomja le a CTRL + F5 billentyűparancsot a böngészőlap frissítéséhez. Ezután ismét működnie kell az alapértelmezett lapnak.

    ![Alapértelmezett nginx-weblap](./media/tutorial-backup-vms/nginx-working.png)

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

