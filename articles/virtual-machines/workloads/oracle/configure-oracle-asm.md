---
title: "Egy Azure Linux virtuális gép Oracle ASM beállítása |} Microsoft Docs"
description: "Gyorsan karban lehessen Oracle ASM be és az Azure környezetben futna."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: RicksterCDN
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/19/2017
ms.author: rclaus
ms.openlocfilehash: 117212a2e7e3da7c3e249798eec804a652e0ef58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Oracle címterület-kezelés beállítása az Azure Linux virtuális gépen  

Az Azure virtuális gépek adjon meg egy teljes mértékben konfigurálhatók és rugalmas számítási környezet. Ez az oktatóanyag kombinálva, a telepítés és konfigurálás az Oracle automatikus tárolási kezelési (ASM) alapszintű Azure virtuálisgép-telepítést ismertet.  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzon létre, és csatlakozzon az Oracle adatbázis-virtuális gép
> * Telepítse és konfigurálja az Oracle automatikus tárolók kezelése
> * Telepítse és konfigurálja az Oracle rács infrastruktúra
> * Oracle ASM telepítés inicializálása
> * A címterület-kezelés által felügyelt az Oracle-adatbázis létrehozása


[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez az oktatóanyag van szükség, hogy futnak-e az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>A környezet előkészítése

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Erőforráscsoport létrehozásához használja a [az csoport létrehozása](/cli/azure/group#create) parancsot. Egy Azure erőforráscsoport egy olyan logikai tároló, amelyre erőforrások telepítése és kezelése. Ebben a példában az erőforráscsoport neve *myResourceGroup* a a *eastus* régióban.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Virtuális gép létrehozása

Az Oracle-adatbázishoz lemezképen alapuló virtuális gép létrehozása, és konfigurálja úgy, hogy az Oracle ASM használja, használja a [az virtuális gép létrehozása](/cli/azure/vm#create) parancsot. 

Az alábbi példa létrehoz egy 50 GB négy csatolt adatlemezekkel rendelkező Standard_DS2_v2 méretű myVM nevű virtuális gép. Ha még nem léteznek az alapértelmezett kulcshelyen, SSH-kulcsok is létrehoz.  Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

A virtuális gép létrehozása után a Azure CLI-t az alábbi példához hasonló információkat jeleníti meg. Vegye figyelembe a következő `publicIpAddress`. Ez a cím a virtuális gép elérésére használhat.

   ```azurecli
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "eastus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

### <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

A virtuális gép SSH-munkamenetet létrehozni, és további beállításokat, a következő paranccsal. Cserélje le az IP-cím a `publicIpAddress` értéket a virtuális gép számára.

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Oracle ASM telepítése

Oracle ASM telepítéséhez kövesse az alábbi lépéseket. 

Oracle ASM telepítésével kapcsolatos további információkért lásd: [Oracle ASMLib letölti az Oracle Linux 6](http://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. A címterület-kezelési telepítés folytatásához a legfelső szintű bejelentkezés szüksége:

   ```bash
   sudo su -
   ```
   
2. A további parancsok a címterület-kezelés Oracle-összetevők telepítéséhez:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget http://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Győződjön meg arról, hogy telepítve van-e az Oracle ASM:

   ```bash
   rpm -qa |grep oracleasm
   ```

    Ez a parancs kimenetében a következő összetevőket:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. A címterület-kezelés meghatározott felhasználókra és szerepkörökre helyes működéséhez. Az alábbi parancsokat a működéséhez szükséges felhasználói fiókok és csoportok létrehozása: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Ellenőrizze a felhasználók és csoportok létrehozott megfelelően:

   ```bash
   id grid
   ```

    Az a parancs kimenetében a következő felhasználókat és csoportokat:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Hozzon létre egy mappát felhasználói *rács* és a tulajdonos módosítása:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Oracle címterület-kezelés beállítása

Ebben az oktatóanyagban az alapértelmezett felhasználói van *rács* és alapértelmezett csoport *asmadmin*. Győződjön meg arról, hogy a *oracle* felhasználói a asmadmin csoport része. Az Oracle ASM telepítése beállításához tegye a következőket:

1. Az Oracle címterület-kezelési könyvtár illesztőprogram beállítása magában foglalja az alapértelmezett felhasználói (grid) és az alapértelmezett csoport (asmadmin), valamint a elindítja a rendszerindító meghajtó konfigurálása (válassza ki a y), és a rendszerindító lemezzel vizsgálat (y kiválasztása). A megjelenő utasításokat az alábbi parancsot a válaszolnia kell:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   A parancs kimenetének hasonlóan kell kinéznie a következő, a leállás kéri vet.

    ```bash
   Configuring the Oracle ASM library driver.

   This will configure the on-boot properties of the Oracle ASM library
   driver. The following questions will determine whether the driver is
   loaded on boot and what permissions it will have. The current values
   will be shown in brackets ('[]'). Hitting <ENTER> without typing an
   answer will keep that current value. Ctrl-C will abort.

   Default user to own the driver interface []: grid
   Default group to own the driver interface []: asmadmin
   Start Oracle ASM library driver on boot (y/n) [n]: y
   Scan for Oracle ASM disks on boot (y/n) [y]: y
   Writing Oracle ASM library driver configuration: done
   ```

2. A lemezkonfiguráció megtekintése:
   ```bash
   cat /proc/partitions
   ```

   Ez a parancs kimenete a következő a rendelkezésre álló lemezek tőzsdei hasonlóan kell kinéznie.

   ```bash
   8       16   14680064 sdb
   8       17   14678976 sdb1
   8        0   52428800 sda
   8        1     512000 sda1
   8        2   51915776 sda2
   8       48   52428800 sdd
   8       64   52428800 sde
   8       80   52428800 sdf
   8       32   52428800 sdc
   11       0       1152 sr0
   ```

3. Lemez formázása */dev/sdc* a következő parancs futtatásával, majd a kérdések megválaszolásával:
   - *n*Új partíció
   - *p* elsődleges partíció
   - *1* jelölje be az első partíció
   - nyomja le az ENTER `enter` a vonatkozó alapértelmezett első henger
   - nyomja le az ENTER `enter` a vonatkozó alapértelmezett utolsó henger
   - nyomja le az ENTER *w* írni a módosításokat a partíciós táblán  

   ```bash
   fdisk /dev/sdc
   ```
   
   A fenti választ használ, a fdisk parancs kimenetét kell a következőhöz hasonló:

   ```bash
   Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
   Building a new DOS disklabel with disk identifier 0xf865c6ca.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   The device presents a logical sector size that is smaller than
   the physical sector size. Aligning to a physical sector (or optimal
   I/O) size boundary is recommended, or performance may be impacted.

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
           switch off the mode (command 'c') and change display units to
           sectors (command 'u').

   Command (m for help): n
   Command action
     e   extended
     p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-6527, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
   Using default value 6527

   Command (m for help): w
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. Ismételje meg az előző fdisk parancsot `/dev/sdd`, `/dev/sde`, és `/dev/sdf`.

5. Ellenőrizze a lemezkonfigurációt:

   ```bash
   cat /proc/partitions
   ```

   A parancs a következő hasonlóan kell kinéznie:

   ```bash
   major minor  #blocks  name

     8       16   14680064 sdb
     8       17   14678976 sdb1
     8       32   52428800 sdc
     8       33   52428096 sdc1
     8       48   52428800 sdd
     8       49   52428096 sdd1
     8       64   52428800 sde
     8       65   52428096 sde1
     8       80   52428800 sdf
     8       81   52428096 sdf1
     8        0   52428800 sda
     8        1     512000 sda1
     8        2   51915776 sda2
     11       0    1048575 sr0
   ```

6. Az Oracle ASM szolgáltatás állapotának ellenőrzése és az Oracle címterület-kezelés szolgáltatás elindítása:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   A parancs a következő hasonlóan kell kinéznie:
   
   ```bash
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Oracle ASM lemezek létrehozásához:

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```    

   A parancs a következő hasonlóan kell kinéznie:

   ```bash
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Oracle ASM lemezek listáját:

   ```bash
   service oracleasm listdisks
   ```   

   A parancs kimenetében a következő Oracle ASM ki:

   ```bash
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Módosítsa a legfelső szintű, az oracle és a rács felhasználók jelszavait. **Jegyezze fel ezeket az új jelszót a** módon használja őket, később a telepítés során.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. A mappa engedélyt módosítása:

   ```bash
   chmod -R 775 /opt 
   chown grid:oinstall /opt 
   chown oracle:oinstall /dev/sdc1 
   chown oracle:oinstall /dev/sdd1 
   chown oracle:oinstall /dev/sde1 
   chown oracle:oinstall /dev/sdf1 
   chmod 600 /dev/sdc1 
   chmod 600 /dev/sdd1 
   chmod 600 /dev/sde1 
   chmod 600 /dev/sdf1
   ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Töltse le és Oracle rács infrastruktúra előkészítése

Töltse le, és az Oracle rács infrastruktúra szoftver előkészítése, végezze el az alábbi lépéseket:

1. Töltse le az Oracle rács infrastruktúrát a [Oracle ASM letöltési oldala](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

   A letöltés című alatt **Oracle adatbázis 12c kiadás 1 rács infrastruktúra (12.1.0.2.0) a Linux x86-64**, a két .zip fájl letöltése.

2. Miután letöltötte a .zip fájlokat az ügyfélszámítógépre, biztonságos másolási protokoll (SCP) segítségével másolja a fájlokat a virtuális Gépet:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. SSH újra üzembe a Oracle Azure-ban ahhoz, hogy helyezze át a .zip fájlokat a / opt mappát. Módosítsa a fájlok tulajdonosa:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Bontsa ki a fájlokat. (A Linux telepítés csomagolja ki eszköz Ha még nincs telepítve.)
   
   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Engedély módosítása:
   
   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Frissítés konfigurált lapozóterület. Oracle rács összetevőket legalább 6,8 GB lapozóterület rács telepíteni kell. Az alapértelmezett lapozófájl-kapacitás az Azure-ban Oracle Linux képek mérete csak 2048MB. Növelnie kell `ResourceDisk.SwapSizeMB` a a `/etc/waagent.conf` fájlt, és indítsa újra a WALinuxAgent szolgáltatást ahhoz, hogy a frissített beállítások életbe lépjenek. Mivel a csak olvasható fájlba, írási hozzáférés engedélyezése a fájl engedélyeit módosítani szeretné.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```
   
   Keresse meg `ResourceDisk.SwapSizeMB` és módosítsa az értéket a következőre **8192**. Nyomja le az ENTER kell `insert` adja meg a beszúrási módban, írja be a értékének **8192** , és nyomja le az `esc` üzemmód való visszatéréshez. A módosítások, és lépjen ki a fájlt, írja be a következőt `:wq` nyomja le az ENTER `enter`.
   
   > [!NOTE]
   > Erősen ajánlott, hogy mindig használjon `WALinuxAgent` lapozóterület konfigurálásához arra, hogy mindig jön létre a helyi ideiglenes (lemez ideiglenes) a legjobb teljesítmény érdekében. További információkért lásd: [lapozófájl hozzáadása a Linux Azure virtuális gépek](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Készítse elő a helyi ügyfél és a virtuális gép x11 futtatásához
A grafikus felület segítségével végezze el a telepítési és konfigurációs Oracle ASM konfigurálásához szükséges. A x11 használjuk protokoll lehetővé teszi a telepítést. Ha egy ügyfél rendszer (Mac vagy Linux), amely már rendelkezik X11 használ képességek engedélyezve és konfigurálva – ugorjon a konfigurációs és kizárólagos telepítő Windows-alapú gépek. 

1. [Töltse le a PuTTY](http://www.putty.org/) és [Xming letöltése](https://xming.en.softonic.com/) a Windows rendszerű számítógépen. Szüksége lesz, mindkettő ezeknek az alkalmazásoknak az alapértelmezett értékeket, a folytatás előtt a telepítés befejezéséhez.

2. PuTTY telepítése után nyisson meg egy parancssort, módosítsa a PuTTY mappába (például, C:\Program Files\PuTTY), és futtassa `puttygen.exe` ahhoz, hogy hozzon létre egy kulcsot.

3. A PuTTY Megosztottelérésikulcs-készítő:
   
   1. Hozzon létre egy kulcsot kiválasztásával a `Generate` gombra.
   2. A kulcs (Ctrl + C) a tartalom másolása.
   3. Válassza ki a `Save private key` gombra.
   4. A kulcsot egy hozzáférési kóddal biztosításával kapcsolatos figyelmeztetést figyelmen kívül, majd válassza ki `OK`.

   ![Képernyőkép a PuTTY Megosztottelérésikulcs-készítő](./media/oracle-asm/puttykeygen.png)

4. A virtuális gépen, futtassa az alábbi parancsokat:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Hozzon létre egy fájlt `authorized_keys`. A kulcs tartalmának beillesztése a fájlban, és mentse a fájlt.

   > [!NOTE]
   > A kulcs hossza csak a karakterlánc `ssh-rsa`. A kulcs tartalmát is, egy egyszerű szövegsor kell lennie.
   >  

6. Indítsa el a PuTTY az ügyfélrendszeren. Az a **kategória** ablaktáblában lépjen **kapcsolat** > **SSH** > **Auth**. Az a **hitelesítéshez titkos kulcsfájl** mezőben tallózással keresse meg a korábban létrehozott kulcsot.

   ![Az SSH hitelesítési lehetőségek képernyőképe](./media/oracle-asm/setprivatekey.png)

7. Az a **kategória** ablaktáblában lépjen **kapcsolat** > **SSH** > **X11**. Válassza ki a **engedélyezése X11 továbbítási** jelölőnégyzetet.

   ![Képernyőkép a SSH X11 a továbbítási beállítások](./media/oracle-asm/enablex11.png)

8. Az a **kategória** ablaktáblában lépjen **munkamenet**. Adja meg az Oracle ASM virtuális gép `<publicIPaddress>` a host name párbeszédpanelen adja meg egy új `Saved Session` nevet, majd kattintson a `Save`.  Ha menteni, kattintson a `open` az Oracle ASM virtuális géphez történő csatlakozáshoz.  Az első csatlakozáskor figyelmeztet a távoli rendszer nem gyorsítótárazza a beállításjegyzékben. Kattintson a `yes` hozzáadásához és a folytatáshoz.

   ![A PuTTY-munkamenet lehetőségek képernyőképe](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Oracle-rács infrastruktúra telepítése

Oracle rács infrastruktúra telepítéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a **rács**. (Kell használva jelentkezhet be a jelszó megadása nélkül.) 

   > [!NOTE]
   > Ha a Windows rendszer használata esetén győződjön meg arról Xming elindította a telepítés megkezdése előtt.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Megnyílik a Oracle rács infrastruktúra 12c kiadás 1 telepítőjét. (A telepítő elindításához néhány percig is eltarthat.)

2. Az a **telepítési lehetőség kiválasztása** lapon, hogy melyik **telepítése és konfigurálása Oracle rács infrastruktúra egy önálló kiszolgáló**.

   ![A telepítő telepítési lehetőség kiválasztása oldalát bemutató képernyőkép](./media/oracle-asm/install01.png)

3. Az a **termék nyelvek kiválasztása** oldalon **angol** vagy a kívánt nyelv kiválasztása.  Kattintson a `next` gombra.

4. Az a **ASM lemez csoport létrehozása** lap:
   - Adjon meg egy nevet a lemez csoporthoz.
   - A **redundancia**, jelölje be **külső**.
   - A **foglalásiegység-méret**, jelölje be **4**.
   - A **lemezek hozzáadása a**, jelölje be **ORCLASMSP**.
   - Kattintson a `next` gombra.

5. Az a **ASM jelszót adjon meg** lapon jelölje be a **használja ugyanazt a jelszót a fiókokhoz** lehetőséget, és adjon meg egy jelszót.

   ![A telepítő adja meg a címterület-kezelés jelszó oldalát bemutató képernyőkép](./media/oracle-asm/install04.png)

6. Az a **felügyeleti beállítások megadása** lap, lehetősége van a EM felhő-vezérlés konfigurálása. Ez a beállítás kihagyjuk - kattintson `next` folytatja. 

7. Az a **jogosultsággal rendelkező operációs rendszer csoportjának** lapon, az alapértelmezett beállításokat használja. Kattintson a `next` a folytatáshoz.

8. Az a **adja meg a telepítési hely** lapon, az alapértelmezett beállításokat használja. Kattintson a `next` a folytatáshoz.

9. Az a **létrehozása** lapon, majd a készlet könyvtárat `/u01/app/grid/oraInventory`. Kattintson a `next` a folytatáshoz.

   ![A telepítő létrehozása oldalát bemutató képernyőkép](./media/oracle-asm/install08.png)

10. Az a **legfelső szintű parancsfájl végrehajtásának konfigurációs** lapon jelölje be a **automatikusan a parancsfájlokat futtasson** jelölőnégyzetet. Ezután válassza ki a **"Gyökér" felhasználó hitelesítő adatainak használata** lehetőséget, és adja meg a gyökér szintű felhasználó jelszavát.

    ![A telepítő legfelső szintű parancsfájl végrehajtásának konfigurációs oldalát bemutató képernyőkép](./media/oracle-asm/install09.png)

11. Az a **előfeltételek ellenőrzésének végrehajtása** lap, a jelenlegi telepítés sikertelen lesz, és hibákat. Ez az várt viselkedése. Válassza a(z) `Fix & Check Again` lehetőséget.

12. Az a **javítás parancsfájl** párbeszédpanel, kattintson a `OK`.

13. Az a **összegzés** lapon tekintse át a beállításokat, és kattintson a `Install`.

    ![A telepítő összesítő oldalát bemutató képernyőkép](./media/oracle-asm/install12.png)

14. Egy figyelmeztetés jelenik meg a tájékoztatása, konfigurációs parancsfájlokat kell futtatnia a jogosultsággal rendelkező felhasználóként. Kattintson a `Yes` a folytatáshoz.

15. Az a **Befejezés** kattintson `Close` fejezze be a telepítést.

## <a name="set-up-your-oracle-asm-installation"></a>Állítsa be az Oracle ASM telepítése

Az Oracle ASM telepítése beállításához tegye a következőket:

1. Győződjön meg arról, hogy továbbra is felhasználóként van bejelentkezve **rács**, a X11 a munkamenet. Szükség lehet a találati `enter` Reaktiválja a Terminálszolgáltatások számára. Indítsa el az Oracle automatikus tárolási felügyeleti konfiguráció Segéd majd:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Oracle címterület-kezelési konfigurációs Segéd nyílik meg.

2. Az a **konfigurálása ASM: lemez csoportok** párbeszédpanel, kattintson a `Create` gombra, majd `Show Advanced Options`.

3. Az a **lemezcsoport létrehozása** párbeszédpanel:

   - Adja meg a lemez csoportnév **adatok**.
   - A **tag lemez kiválasztása**, jelölje be **ORCL_DATA** és **ORCL_DATA1**.
   - A **foglalásiegység-méret**, jelölje be **4**.
   - Kattintson a `ok` a lemez csoport létrehozásához.
   - Kattintson a `ok` az ablak bezárásához.

   ![A lemez-csoport létrehozása párbeszédpanel képernyőképe](./media/oracle-asm/asm02.png)

4. Az a **konfigurálása ASM: lemez csoportok** párbeszédpanel, kattintson a `Create` gombra, majd `Show Advanced Options`.

5. Az a **lemezcsoport létrehozása** párbeszédpanel:

   - Adja meg a lemez csoportnév **FRA**.
   - A **redundancia**, jelölje be **(nincs) külső**.
   - A **tag lemez kiválasztása**, jelölje be **ORCL_FRA**.
   - A **foglalásiegység-méret**, jelölje be **4**.
   - Kattintson a `ok` a lemez csoport létrehozásához.
   - Kattintson a `ok` az ablak bezárásához.

   ![A lemez-csoport létrehozása párbeszédpanel képernyőképe](./media/oracle-asm/asm04.png)

6. Válassza ki **kilépési** címterület-kezelési konfigurációs Segéd bezárásához.

   ![Képernyőfelvétel a konfigurálása ASM: lemez csoportok párbeszédpanel a Kilépés gombra](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Az adatbázis létrehozása

Az Oracle adatbázis-szoftver már telepítve van az Azure Piactéri lemezképhez. Egy adatbázis létrehozásához kövesse az alábbi lépéseket:

1. Az Oracle felügyelő felhasználók váltson, és majd a naplózás a figyelő inicializálása:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```
   Adatbázis-konfigurációs Segéd nyílik meg.

2. Az a **adatbázis-művelet** kattintson `Create Database`.

3. Az a **létrehozási mód** lap:

   - Adja meg az adatbázis nevét.
   - A **tárolótípus**, győződjön meg arról **automatikus tárhely-kezelési (ASM)** van kiválasztva.
   - A **adatbázis helye**, használja az alapértelmezett ASM hely javasolt.
   - A **gyors helyreállítás terület**, használja az alapértelmezett ASM hely javasolt.
   - Adja meg egy **rendszergazdai jelszó** és **jelszó megerősítése**.
   - Győződjön meg arról `create as container database` van kiválasztva.
   - Írja be a `pluggable database name` érték.

4. Az a **összegzés** lapon tekintse át a beállításokat, és kattintson a `Finish` létrehozni az adatbázist.

   ![Képernyőkép az összefoglalás panelről](./media/oracle-asm/createdb03.png)

5. Az adatbázis létrehozását. Az a **Befejezés** lap, lehetősége van az adatbázis használatára, és a jelszavak módosítása további fiókok zárolásának feloldásához. Ha szeretne ehhez, jelölje be **jelszókezelés** – ellenkező esetben kattintson a `close`.

## <a name="delete-the-vm"></a>A virtuális gép törlése

Sikeresen konfigurálta az Azure piactérről az Oracle-DB kép Oracle automatikus tárolók kezelése.  Ha már nincs szüksége a virtuális gép, a következő paranccsal távolítsa el az erőforráscsoportot, virtuális gép és az összes kapcsolódó erőforrások:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

[Oktatóanyag: Oracle DataGuard konfigurálása](configure-oracle-dataguard.md)

[Oktatóanyag: Oracle GoldenGate konfigurálása](Configure-oracle-golden-gate.md)

Tekintse át [az Oracle DB tervezővel](oracle-design.md)
