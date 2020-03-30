---
title: Az Oracle ASM beállítása Azure Linux os virtuális gépen | Microsoft dokumentumok
description: Gyorsan működésbe hozhatja az Oracle ASM-et azure-környezetében.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: a27d4c1712e9d65afcfc8792eac88be468829f6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536377"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Az Oracle ASM beállítása Azure Linux os virtuális gépen  

Az Azure-beli virtuális gépek egy teljes mértékben konfigurálható és rugalmas számítási környezetet nyújtanak. Ez az oktatóanyag az Azure virtuális gépek alapvető üzembe helyezését, valamint az Oracle Automated Storage Management (ASM) telepítését és konfigurálását ismerteti.  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Oracle Database virtuális gép létrehozása és csatlakozás
> * Az Oracle automated Storage Management telepítése és konfigurálása
> * Az Oracle Grid infrastruktúra telepítése és konfigurálása
> * Oracle ASM-telepítés inicializálása
> * Az ASM által kezelt Oracle DB létrehozása


Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>A környezet előkészítése

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport létrehozásához használja az [az group create](/cli/azure/group) parancsot. Az Azure-erőforráscsoport egy logikai tároló, amelyben az Azure-erőforrások üzembe helyezése és kezelése. Ebben a példában egy *myResourceGroup* nevű erőforráscsoport az *eastus* régióban.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Virtuális gép létrehozása

Ha az Oracle Database lemezkép alapján szeretne létrehozni egy virtuális gépet, és konfigurálni szeretné az Oracle ASM használatára, használja az [az vm create](/cli/azure/vm) parancsot. 

A következő példa létrehoz egy myVM nevű virtuális gép, amely egy Standard_DS2_v2 méretű, négy csatlakoztatott adatlemezek 50 GB-os mindegyik. Ha még nem léteznek az alapértelmezett kulcshelyen, sSH-kulcsokat is létrehoz.  Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

A virtuális gép létrehozása után az Azure CLI a következő példához hasonló információkat jelenít meg. Figyelje meg `publicIpAddress`a értékét. Ezzel a címmel érheti el a virtuális gép.

   ```output
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

Ha ssh-munkamenetet szeretne létrehozni a virtuális géppel, és további beállításokat szeretne konfigurálni, használja a következő parancsot. Cserélje le az `publicIpAddress` IP-címet a virtuális gép értékére.

```bash
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Az Oracle ASM telepítése

Az Oracle ASM telepítéséhez hajtsa végre az alábbi lépéseket. 

Az Oracle ASM telepítéséről az [Oracle ASMLib Downloads for Oracle Linux 6](https://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html)című témakörben talál további információt.  

1. Az ASM telepítésének folytatásához root ként kell bejelentkeznie:

   ```bash
   sudo su -
   ```
   
2. Az Oracle ASM-összetevők telepítéséhez futtassa ezeket a további parancsokat:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget https://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Ellenőrizze, hogy az Oracle ASM telepítve van-e:

   ```bash
   rpm -qa |grep oracleasm
   ```

    A parancs kimenetének a következő összetevőket kell felsorolnia:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. Az ASM-nek meghatározott felhasználókra és szerepkörökre van szüksége a megfelelő működéshez. A következő parancsok hozzák létre az előfeltételként szükséges felhasználói fiókokat és csoportokat: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Ellenőrizze, hogy helyesen hozták-e létre a felhasználókat és a csoportokat:

   ```bash
   id grid
   ```

    A parancs kimenetének a következő felhasználókat és csoportokat kell felsorolnia:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Hozzon létre egy mappát a felhasználói *rácsszámára,* és módosítsa a tulajdonost:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Az Oracle ASM beállítása

Ebben az oktatóanyagban az alapértelmezett felhasználó a *rács,* az alapértelmezett csoport pedig *az asmadmin*. Győződjön meg arról, hogy az *oracle-felhasználó* az asmadmin csoport része. Az Oracle ASM telepítésének beállításához hajtsa végre az alábbi lépéseket:

1. Az Oracle ASM könyvtár-illesztőprogram beállítása magában foglalja az alapértelmezett felhasználó (rács) és alapértelmezett csoport (asmadmin) meghatározását, valamint a meghajtó konfigurálását a rendszerindításhoz (az y kiválasztása) és a lemezek kereséséhez a rendszerindításkor (y). A következő parancs ból kell válaszolnia a kérdésekre:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   A parancs kimenetének a következőhöz hasonlóan kell kinéznie, és a válaszra váró gyorsakkal kell megállnia.

    ```output
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

2. A lemez konfigurációjának megtekintése:

   ```bash
   cat /proc/partitions
   ```

   A parancs kimenetének a következő elérhető lemezek listájához hasonlóan kell kinéznie.

   ```output
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

3. Formázza a *lemezt /dev/sdc* a következő parancs futtatásával és a következő kkel válaszolva a kérdéseket:
   - *n* az új partícióhoz
   - *p* az elsődleges partícióhoz
   - *1* az első partíció kiválasztásához
   - nyomja `enter` meg az alapértelmezett első henger
   - nyomja `enter` meg az alapértelmezett utolsó henger
   - nyomja meg a *w* írni a módosításokat a partíciós tábla  

   ```bash
   fdisk /dev/sdc
   ```
   
   A fenti válaszok alapján a `fdisk` parancs kimenetének a következőkre kell hasonlítania:

   ```output
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

4. Ismételje meg `fdisk` az `/dev/sdd` `/dev/sde`előző `/dev/sdf`parancsot a és a területen.

5. Ellenőrizze a lemez konfigurációját:

   ```bash
   cat /proc/partitions
   ```

   A parancs kimenetének a következőnek kell lennie:

   ```output
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

6. Ellenőrizze az Oracle ASM szolgáltatás állapotát, és indítsa el az Oracle ASM szolgáltatást:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   A parancs kimenetének a következőnek kell lennie:

   ```output
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Oracle ASM-lemezek létrehozása:

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```

   A parancs kimenetének a következőnek kell lennie:

   ```output
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Oracle ASM lemezek listázása:

   ```bash
   service oracleasm listdisks
   ```

   A parancs kimenetének a következő Oracle ASM lemezeket kell felsorolnia:

   ```output
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Módosítsa a gyökér-, oracle- és rácsfelhasználók jelszavát. **Jegyezze fel ezeket az új jelszavakat,** amikor később használja őket a telepítés során.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. A mappaengedély módosítása:

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

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Az Oracle Grid Infrastructure letöltése és előkészítése

Az Oracle Grid Infrastructure szoftver letöltéséhez és előkészítéséhez hajtsa végre az alábbi lépéseket:

1. Töltse le az Oracle Grid Infrastructure-t az [Oracle ASM letöltési oldaláról.](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html) 

   A **linuxos x86-64-es Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0)** letöltés alatt töltse le a két .zip fájlt.

2. Miután letöltötte a .zip fájlokat az ügyfélszámítógépre, a Secure Copy Protocol (SCP) protokoll segítségével másolhatja a fájlokat a virtuális gépre:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. Az SSH-t visszahelyezheti az Oracle virtuális gépbe az Azure-ban, hogy a .zip fájlokat áthelyezhesse a /opt mappába. Ezután módosítsa a fájlok tulajdonosát:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Csomagolja ki a fájlokat. (Telepítse a Linux csomagolja eszköz, ha még nincs telepítve.)

   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Engedély módosítása:

   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Frissítse a konfigurált csereterületet. Az Oracle Grid összetevőinek legalább 6,8 GB swapterületre van szükségük a Grid telepítéséhez. Az Oracle Linux-lemezképek alapértelmezett lapkamérete az Azure-ban csak 2048 MB. Növelnie kell `ResourceDisk.SwapSizeMB` a `/etc/waagent.conf` fájlt, és újra kell indítania a WALinuxAgent szolgáltatást a frissített beállítások érvénybe léptetéséhez. Mivel írásvédett fájlról van szó, az írási hozzáférés engedélyezéséhez módosítania kell a fájlengedélyeket.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```

   Keresse `ResourceDisk.SwapSizeMB` meg és módosítsa az értéket **8192**értékre. A beszúrási `insert` módba való belépéshez nyomja meg a billentyűt, `esc` írja be a **8192** értéket, majd nyomja meg a billentyűt a parancsmódba való visszatéréshez. A módosítások írásához és a `:wq` fájlból való kilépéshez írja be és nyomja meg a billentyűt. `enter`
   
   > [!NOTE]
   > Javasoljuk, hogy mindig `WALinuxAgent` használja a csereterület konfigurálásához, hogy mindig a helyi ideiglenes lemezen (ideiglenes lemezen) jön létre a legjobb teljesítmény érdekében. További információ: [Cserefájl hozzáadása Linux Azure virtuális gépeken.](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines)

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>A helyi ügyfél és a virtuális gép előkészítése az x11 futtatására
Az Oracle ASM konfigurálásához grafikus felületre van szükség a telepítés és a konfiguráció befejezéséhez. Az x11 protokollt használjuk a telepítés megkönnyítésére. Ha olyan ügyfélrendszert (Mac vagy Linux) használ, amely már rendelkezik X11 képességekkel , és konfigurálva van - kihagyhatja ezt a konfigurációt, és kizárólag a Windows gépekre vonatkozóbeállításokat használhatja. 

1. [Töltse le a PuTTY-t](https://www.putty.org/) és töltse le az [Xming-et](https://xming.en.softonic.com/) windowsos számítógépére. A folytatás előtt mindkét alkalmazás telepítését az alapértelmezett értékekkel kell befejeznie.

2. A PuTTY telepítése után nyisson meg egy parancssort, váltson a PuTTY mappába (például C:\Program Files\PuTTY), és futtassa `puttygen.exe` a kulcs létrehozásához.

3. A PuTTY kulcs generátor:
   
   1. Hozzon létre egy `Generate` kulcsot a gomb kiválasztásával.
   2. Másolja a billentyű tartalmába (Ctrl+C).
   3. Kattintson a(z) `Save private key` gombra.
   4. Hagyja figyelmen kívül a kulcs jelszóval történő `OK`védelmére vonatkozó figyelmeztetést, majd válassza a lehetőséget.

   ![Képernyőkép a PuTTY kulcsgenerátorról](./media/oracle-asm/puttykeygen.png)

4. A virtuális gépen futtassa ezeket a parancsokat:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Hozzon létre egy `authorized_keys` nevű fájlt. Illessze be a kulcs tartalmát a fájlba, majd mentse a fájlt.

   > [!NOTE]
   > A kulcsnak tartalmaznia kell a karakterláncot `ssh-rsa`. A kulcs tartalmának is egyetlen szövegsornak kell lennie.
   >  

6. Az ügyfélrendszereden indítsa el a PuTTY-t. A **Kategória ablaktáblán** nyissa meg **a Connection** > **SSH** > **Auth**lehetőséget. A **hitelesítéshez szükséges személyes kulcsfájlmezőben** keresse meg a korábban létrehozott kulcsot.

   ![Képernyőkép az SSH hitelesítési beállításokról](./media/oracle-asm/setprivatekey.png)

7. A **Kategória** ablaktáblán nyissa meg **a Connection** > **SSH** > **X11**lehetőséget. Jelölje be az **X11 továbbítás engedélyezése** jelölőnégyzetet.

   ![Képernyőkép az SSH X11 továbbítási lehetőségeiről](./media/oracle-asm/enablex11.png)

8. A **Kategória** ablaktáblán nyissa meg a **Munkamenet**lehetőséget. Írja be az Oracle `<publicIPaddress>` ASM virtuális gépet az állomásnév párbeszédpanelen, töltsön ki egy új `Saved Session` nevet, majd kattintson a gombra. `Save`  A mentés után `open` kattintson az Oracle ASM virtuális géphez való csatlakozáshoz.  Az első csatlakozáskor figyelmezteti a rendszer, hogy a távoli rendszer nincs a rendszerleíró adatbázisban gyorsítótárazva. Kattintson `yes` a hozzá, és folytassa.

   ![Képernyőkép a PuTTY munkamenet beállításairól](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Az Oracle Grid Infrastructure telepítése

Az Oracle Grid Infrastructure telepítéséhez hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be **rácsként**. (A rendszer jelszó kérése nélkül tud bejelentkezni.) 

   > [!NOTE]
   > Ha Windows rendszert futtat, a telepítés megkezdése előtt győződjön meg arról, hogy elindította az Xming alkalmazást.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Megnyílik az Oracle Grid Infrastructure 12c Release 1 Installer. (A telepítő indítása eltarthat néhány percig.)

2. A **Telepítési beállítás kiválasztása** lapon válassza **az Oracle Grid Infrastructure telepítése és konfigurálása önálló kiszolgálóhoz**lehetőséget.

   ![Képernyőkép a telepítő Telepítési lehetőség kiválasztása lapjáról](./media/oracle-asm/install01.png)

3. A **Terméknyelvek kiválasztása** lapon győződjön meg arról, hogy az **angol** vagy a kívánt nyelv ki van jelölve.  Kattintson a `next` gombra.

4. Az **ASM-lemezcsoport létrehozása** lapon:
   - Adja meg a lemezcsoport nevét.
   - A **Redundancia**csoportban válassza **a Külső**lehetőséget.
   - A **Felosztási egység mérete csoportban**válassza a **4**lehetőséget.
   - A **Lemezek hozzáadása**csoportban válassza az **ORCLASMSP**lehetőséget.
   - Kattintson a `next` gombra.

5. Az **ASM-jelszó megadása** lapon jelölje be az **Azonos jelszavak használata a fiókokhoz** jelölőnégyzetet, és adjon meg egy jelszót.

   ![Képernyőkép a telepítő ASM-jelszó megadása lapjáról](./media/oracle-asm/install04.png)

6. A **Kezelési beállítások megadása** lapon konfigurálhatja az EM Felhővezérlést. Kihagyjuk ezt a `next` lehetőséget - kattintson a folytatáshoz. 

7. A **Kiemelt operációsrendszer-csoportok** lapon használja az alapértelmezett beállításokat. Kattintson ide `next` a folytatáshoz.

8. A **Telepítési hely megadása** lapon használja az alapértelmezett beállításokat. Kattintson ide `next` a folytatáshoz.

9. A **Készlet létrehozása** lapon módosítsa a `/u01/app/grid/oraInventory`Készletkönyvtárat . Kattintson ide `next` a folytatáshoz.

   ![Képernyőkép a telepítő Készlet létrehozása lapjáról](./media/oracle-asm/install08.png)

10. A **Gyökérparancsfájl-végrehajtás konfigurációs** lapján jelölje be a **Konfigurációs parancsfájlok automatikus futtatása jelölőnégyzetet.** Ezután válassza a **"root" felhasználói hitelesítő adatok használata** lehetőséget, és adja meg a gyökér felhasználói jelszót.

    ![Képernyőkép a telepítő root parancsfájl-végrehajtási konfigurációs lapjáról](./media/oracle-asm/install09.png)

11. Az **Előfeltétel-ellenőrzések végrehajtása** lapon az aktuális beállítás hibákkal sikertelen lesz. Ez egy várt viselkedés. Válassza a(z) `Fix & Check Again` lehetőséget.

12. A **Fixup Script** párbeszédpanelen `OK`kattintson a gombra.

13. Az **Összegzés** lapon tekintse át a kiválasztott `Install`beállításokat, majd kattintson a gombra.

    ![Képernyőkép a telepítő Összefoglaló lapjáról](./media/oracle-asm/install12.png)

14. Megjelenik egy figyelmeztető párbeszédpanel, amely arról tájékoztatja, hogy a konfigurációs parancsfájlokat kiemelt jogosultságú felhasználóként kell futtatni. Kattintson ide `Yes` a folytatáshoz.

15. A **Befejezés** lapon `Close` kattintson ide a telepítés befejezéséhez.

## <a name="set-up-your-oracle-asm-installation"></a>Az Oracle ASM telepítésének beállítása

Az Oracle ASM telepítésének beállításához hajtsa végre az alábbi lépéseket:

1. Győződjön meg arról, hogy továbbra is **rácsként**van bejelentkezve az X11 munkamenetből. Lehet, hogy `enter` meg kell ütni, hogy feléleszteni a terminált. Ezután indítsa el az Oracle Automated Storage Management Configuration Assistant-et:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Megnyílik az Oracle ASM konfigurációs segéd.

2. Az **ASM konfigurálása: Lemezcsoportok** `Create` párbeszédpanelen kattintson `Show Advanced Options`a gombra, majd a gombra.

3. A **Lemezcsoport létrehozása** párbeszédpanelen:

   - Írja be a **lemezcsoport NEVÉT DATA**.
   - A **Taglemezek kiválasztása**csoportban válassza a **ORCL_DATA ORCL_DATA1** és **ORCL_DATA1**lehetőséget.
   - A **Felosztási egység mérete csoportban**válassza a **4**lehetőséget.
   - Ide `ok` kattintva létrehozhatja a lemezcsoportot.
   - Kattintson ide `ok` a megerősítő ablak bezárásához.

   ![Képernyőkép a Lemezcsoport létrehozása párbeszédpanelről](./media/oracle-asm/asm02.png)

4. Az **ASM konfigurálása: Lemezcsoportok** `Create` párbeszédpanelen kattintson `Show Advanced Options`a gombra, majd a gombra.

5. A **Lemezcsoport létrehozása** párbeszédpanelen:

   - Adja meg az **FRA**lemezcsoport nevét.
   - A **Redundancia**csoportban válassza **a Külső (nincs)** lehetőséget.
   - A **Taglemezek kiválasztása**csoportban válassza **a ORCL_FRA**lehetőséget.
   - A **Felosztási egység mérete csoportban**válassza a **4**lehetőséget.
   - Ide `ok` kattintva létrehozhatja a lemezcsoportot.
   - Kattintson ide `ok` a megerősítő ablak bezárásához.

   ![Képernyőkép a Lemezcsoport létrehozása párbeszédpanelről](./media/oracle-asm/asm04.png)

6. Az ASM konfigurációs segéd bezárásához válassza a **Kilépés** lehetőséget.

   ![Képernyőkép az ASM konfigurálása: Lemezcsoportok párbeszédpanel kilépés gombbal](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Az adatbázis létrehozása

Az Oracle adatbázis-szoftver már telepítve van az Azure Marketplace-lemezkép. Adatbázis létrehozásához hajtsa végre az alábbi lépéseket:

1. Váltson a felhasználókra az Oracle-rendszergazdai felületre, majd inicializálja a figyelőt a naplózáshoz:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```

   Megnyílik az Adatbázis-konfigurációs segéd.

2. Az **Adatbázis-művelet** lapon `Create Database`kattintson a gombra.

3. A **Létrehozási mód** lapon:

   - Adja meg az adatbázis nevét.
   - A **Tárolótípus beállításnál**győződjön meg arról, hogy az **automatikus tárolókezelés (ASM)** ki van jelölve.
   - Az **adatbázisfájlok helyhelyéhez**használja az alapértelmezett ASM javasolt helyet.
   - A **Gyors helyreállítási terület**mezőben használja az alapértelmezett ASM javasolt helyet.
   - írja be a **rendszergazdai jelszót,** és **erősítse meg a jelszót**.
   - biztos `create as container database` beállítás van kiválasztva.
   - beír `pluggable database name` egy értéket.

4. Az **Összegzés** lapon tekintse át a kijelölt `Finish` beállításokat, majd kattintson az adatbázis létrehozásához.

   ![Az Összefoglalás lap képernyőképe](./media/oracle-asm/createdb03.png)

5. Az adatbázis létrejött. A **Befejezés** lapon további fiókok zárolását is feloldhatja az adatbázis használatához és a jelszavak módosításához. Ha ezt szeretné, válassza a **Jelszókezelés** `close`lehetőséget - ellenkező esetben kattintson a lehetőségre.

## <a name="delete-the-vm"></a>A virtuális gép törlése

Sikeresen konfigurálta az Oracle Automated Storage Management szolgáltatást az Oracle DB-lemezképen az Azure Piactérről.  Ha már nincs szüksége erre a virtuális gépre, a következő paranccsal eltávolíthatja az erőforráscsoportot, a virtuális gépés az összes kapcsolódó erőforrást:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

[Oktatóanyag: Az Oracle DataGuard konfigurálása](configure-oracle-dataguard.md)

[Oktatóanyag: Az Oracle GoldenGate konfigurálása](Configure-oracle-golden-gate.md)

Az [Architect egy Oracle DB áttekintése](oracle-design.md)
