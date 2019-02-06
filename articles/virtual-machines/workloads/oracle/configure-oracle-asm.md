---
title: Oracle ASM beállítása Azure-beli Linuxos virtuális géphez |} A Microsoft Docs
description: Oracle ASM fel, és az Azure-környezetben futó használatának gyors.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: 8cf977f9c5bbf10c6a4d862a29fda98d3ce71844
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755689"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Oracle ASM beállítása Azure Linux virtuális gépen  

Az Azure-beli virtuális gépek egy teljes mértékben konfigurálható és rugalmas számítási környezetet nyújtanak. Ez az oktatóanyag bemutatja az alapvető Azure virtuális gép üzembe helyezési telepítési és konfigurációs az Oracle automatikus Storage szolgáltatásfelügyelet (ASM).  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzon létre, és csatlakozhat egy Oracle Database-beli virtuális Gépen
> * Telepítse és konfigurálja az Oracle automatikus Tároláskezelés
> * Telepítse és konfigurálja az Oracle Grid infrastruktúrájának
> * Oracle ASM telepítés inicializálása
> * Hozzon létre egy Oracle DB, az ASM által felügyelt


[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>A környezet előkészítése

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport létrehozásához használja az [az group create](/cli/azure/group) parancsot. Azure-erőforráscsoport olyan logikai tároló, amelyben az Azure erőforrások üzembe helyezése és felügyelt. Ebben a példában egy erőforráscsoportot nevű *myResourceGroup* a a *eastus* régióban.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Virtuális gép létrehozása

Az Oracle Database lemezképen alapuló virtuális gép létrehozása és konfigurálása, hogy Oracle ASM használja a [az virtuális gép létrehozása](/cli/azure/vm) parancsot. 

A következő példában létrehozunk egy, amely egy Standard_DS2_v2 mérete 50 GB négy csatlakoztatott adatlemezekkel rendelkező myVM nevű virtuális Gépet. Ha azok még nem léteznek a kulcsok alapértelmezett helyén, SSH-kulcsokat is létrehoz.  Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

Miután létrehozta a virtuális Gépet, az Azure CLI az alábbi példához hasonló információkat jeleníti meg. Jegyezze fel az értékét a `publicIpAddress`. Ez a cím a virtuális gép eléréséhez használt.

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

Hozzon létre SSH-munkamenetből a virtuális géppel, és további beállítások konfigurálására, használja a következő parancsot. Cserélje le az IP-címet a `publicIpAddress` értéket a virtuális géphez.

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Oracle ASM telepítése

Oracle ASM telepítéséhez kövesse az alábbi lépéseket. 

Oracle ASM telepítésével kapcsolatos további információkért lásd: [Oracle ASMLib letölti az Oracle Linux 6](http://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. Jelentkezzen be gyökér szintű ASM telepítésének folytatásához kell megadnia:

   ```bash
   sudo su -
   ```
   
2. Oracle ASM-összetevők telepítése további parancsok futtatása:

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

    Ez a parancs kimenete a következő összetevőket kell listázza:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. Az ASM bizonyos felhasználók és szerepkörök igényel a megfelelő működéséhez. Az alábbi parancsokat a előfeltételként felhasználói fiókok és csoportok létrehozása: 

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

    Ez a parancs kimenete felsorolásban szerepelnie kell a következő felhasználókat és csoportokat:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Hozzon létre egy mappát a felhasználó *rács* és a tulajdonos módosítása:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Set up Oracle ASM

Ebben az oktatóanyagban az alapértelmezett felhasználó van *rács* és az alapértelmezett csoport *asmadmin*. Ügyeljen arra, hogy a *oracle* felhasználó, a asmadmin csoport tagja. Az Oracle ASM telepítése beállításához, kövesse az alábbi lépéseket:

1. Az Oracle ASM könyvtár illesztőprogram beállítása magában foglalja, az alapértelmezett felhasználói (rács) és az alapértelmezett csoport (asmadmin), valamint a konfigurálás indítsa el a rendszerindító meghajtó (válassza ki az y), és a rendszerindító lemez vizsgálata (válassza ki az y). Az alábbi parancs által a kérdések megválaszolásával kell megadnia:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   Ez a parancs kimenete hasonlóan kell kinéznie a következő kéri a leállítási vet fel.

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

   Ez a parancs kimenete a következő listában a rendelkezésre álló lemezek hasonlóan kell kinéznie.

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

3. Lemez formázása */dev/sdc* a következő parancs futtatásával, és válaszolja meg:
   - *n* új partíció
   - *p* elsődleges partíció
   - *1* kiválasztásához az első partíció
   - nyomja le az ENTER `enter` esetében az alapértelmezett első 3D henger
   - nyomja le az ENTER `enter` esetében az alapértelmezett utolsó 3D henger
   - nyomja le az *w* írni a módosításokat a partíciós táblán  

   ```bash
   fdisk /dev/sdc
   ```
   
   Használja a fent megadott válaszok, a kimenet az fdisk parancshoz hasonlóan kell kinéznie a következő:

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

   A parancs kimenete a következőhöz hasonlóan kell kinéznie:

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

6. Az Oracle ASM-szolgáltatás állapotának ellenőrzése, és indítsa el az Oracle ASM szolgáltatással:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   A parancs kimenete a következőhöz hasonlóan kell kinéznie:
   
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

   A parancs kimenete a következőhöz hasonlóan kell kinéznie:

   ```bash
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Oracle ASM-lemezek listája:

   ```bash
   service oracleasm listdisks
   ```   

   A parancs kimenete listázza ki a következő Oracle ASM lemezekről kell:

   ```bash
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Módosítsa a legfelső szintű, az oracle és a rács felhasználók jelszavát. **Jegyezze fel ezeket az új jelszavak** használja őket később a telepítés során.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. A mappa engedély módosítása:

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

Töltse le, és készítse elő a Grid infrastruktúrájának Oracle-szoftver, hajtsa végre az alábbi lépéseket:

1. Oracle Grid infrastruktúrájának töltse le a [Oracle ASM letöltési oldal](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

   A letöltés címe alatt **Oracle Database 12c Release 1 Grid infrastruktúrájának (12.1.0.2.0) Linux x86-64**, töltse le a két .zip-fájlt.

2. Miután letöltötte a .zip fájlokat az ügyfélszámítógépre, a biztonságos másolás protokoll (SCP) használatával másolja a fájlokat a virtuális géphez:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. Az Azure-ban a .zip fájlokat helyezze át az Oracle virtuális gép be újra az SSH a / opt mappát. Majd módosíthatja a fájlok tulajdonosa:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Csomagolja ki a fájlokat. (A Linux telepítése csomagolja ki eszköz Ha még nem telepítette az.)
   
   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Engedélyek módosítása:
   
   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Frissítés konfigurált lapozóterület. Oracle rács összetevőket kell telepíteni a rács lapozóterület értékét legalább 6,8 GB. Az alapértelmezett lapozófájl-kapacitás az Azure-ban elérhető Oracle Linux-lemezképekhez mérete csak 2048 MB-ot. Növelni szeretné `ResourceDisk.SwapSizeMB` a a `/etc/waagent.conf` fájlt, és indítsa újra a WALinuxAgent szolgáltatást ahhoz, hogy a frissített beállítások érvénybe léptetéséhez. Mivel a fájl csak olvasható, azt kell írási hozzáférés engedélyezése a fájl engedélyeinek módosítása.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```
   
   Keresse meg `ResourceDisk.SwapSizeMB` , és módosítsa az értéket **8192**. Nyomja le kell `insert` insert üzemmódba, írja be az értékét **8192** , és nyomja le az `esc` parancs nézetre való visszatéréshez. A módosítások, és lépjen ki a fájlt, írja be a `:wq` nyomja le az ENTER `enter`.
   
   > [!NOTE]
   > Erősen javasoljuk, hogy Ön mindig használjon `WALinuxAgent` konfigurálhatja a lapozófájl-kapacitás, úgy, hogy a helyi ideiglenes lemez (ideiglenes lemez) a legjobb teljesítmény érdekében mindig létrejön. További információkért lásd: [egy lapozófájl hozzáadása a Linux Azure virtual machines szolgáltatásban](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>A helyi ügyfél és a virtuális gép futtatásához x11 előkészítése
Oracle ASM konfigurálása szükséges a telepítés és konfiguráció befejezéséhez grafikus felület. Használjuk a x11 protokoll a telepítés megkönnyítése érdekében. Ha egy ügyfél system (Mac vagy Linux), amely már rendelkezik X11 használ képességek engedélyezve és konfigurálva – ugorjon a konfigurációs és a beállítási kizárólagos Windows gépekre. 

1. [Töltse le a PuTTY](http://www.putty.org/) és [Xming letöltése](https://xming.en.softonic.com/) a Windows-számítógépre. Szüksége lesz, ezeknek az alkalmazásoknak az alapértelmezett értékeket, a folytatás előtt mindkettő a telepítés befejezéséhez.

2. Miután telepítette a putty-kapcsolaton keresztül, nyisson meg egy parancssort, a PuTTY mappába (például C:\Program Files\PuTTY) módosítása és futtatása `puttygen.exe` annak érdekében, hogy hozzon létre egy kulcsot.

3. A PuTTY Key Generator:
   
   1. Hozzon létre egy kulcsot kiválasztásával a `Generate` gombra.
   2. Másolja a kulcsot (Ctrl + C) tartalmát.
   3. Kattintson a(z) `Save private key` gombra.
   4. A kulcs jelszóval védelmével kapcsolatos figyelmeztetést figyelmen kívül, és válassza ki `OK`.

   ![Képernyőkép a PuTTY Key Generator](./media/oracle-asm/puttykeygen.png)

4. A virtuális gépen futtassa a következő parancsokat:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Hozzon létre egy `authorized_keys` nevű fájlt. Ebben a fájlban illessze be a kulcs tartalmát, és mentse a fájlt.

   > [!NOTE]
   > A kulcsot kell tartalmaznia a karakterlánc `ssh-rsa`. Ezenkívül a kulcs a tartalmát egy egysoros szöveg kell lennie.
   >  

6. Az ügyfél rendszeren indítsa el a putty-kapcsolaton keresztül. Az a **kategória** panelen lépjen a **kapcsolat** > **SSH** > **Auth**. Az a **titkos kulcs fájlját a hitelesítéshez** mezőben tallózással keresse meg a korábban létrehozott kulcsot.

   ![Az SSH-hitelesítés beállításai képernyőkép](./media/oracle-asm/setprivatekey.png)

7. Az a **kategória** panelen lépjen a **kapcsolat** > **SSH** > **X11**. Válassza ki a **engedélyezése X11 továbbítási** jelölőnégyzetet.

   ![Képernyőkép a SSH X11-továbbítás beállítások](./media/oracle-asm/enablex11.png)

8. Az a **kategória** panelen lépjen a **munkamenet**. Adja meg az Oracle ASM VM `<publicIPaddress>` a host name párbeszédpanelen adja meg az új `Saved Session` nevet, és kattintson a `Save`.  A mentés után kattintson a `open` az Oracle ASM virtuális géphez történő csatlakozáshoz.  Az első csatlakozás alkalmával figyelmeztet a távoli rendszer nem gyorsítótárazza a tárolójegyzékbe. Kattintson a `yes` adja hozzá, és továbbra is.

   ![A PuTTY-munkamenethez lehetőségek képernyőképe](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Oracle Grid infrastruktúrájának telepítése

Oracle Grid infrastruktúrájának telepítéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be, **rács**. (Kell jelszó megadása nélkül jelentkezhet be.) 

   > [!NOTE]
   > Ha Windows futtat, győződjön meg arról, Xming kezdte meg a telepítés megkezdése előtt.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Oracle Grid infrastruktúrájának 12c Release 1 telepítő nyílik meg. (A telepítő elindításához néhány percet igénybe vehet.)

2. Az a **telepítési lehetőség kiválasztása** lapra, jelölje be **telepítése és konfigurálása Oracle Grid infrastruktúrájának egy önálló kiszolgáló**.

   ![A telepítő telepítési lehetőség kiválasztása oldalát bemutató képernyőkép](./media/oracle-asm/install01.png)

3. Az a **termék nyelvválasztás** lapon **angol** vagy a kívánt nyelvre van kiválasztva.  Kattintson a `next` gombra.

4. Az a **ASM lemez csoport létrehozása** oldalon:
   - Adja meg a csoport nevét.
   - A **redundancia**válassza **külső**.
   - A **lemezfoglalási egység mérete**válassza **4**.
   - A **lemezek hozzáadása**válassza **ORCLASMSP**.
   - Kattintson a `next` gombra.

5. Az a **ASM jelszót adjon meg** lapon válassza ki a **használja ugyanazt a jelszót a fiókokhoz** lehetőséget, majd adjon meg egy jelszót.

   ![A telepítő az ASM-jelszó megadása oldalát bemutató képernyőkép](./media/oracle-asm/install04.png)

6. Az a **felügyeleti beállítások megadása** lapon lehetősége van EM felhőalapú vezérlő konfigurálásához. Ez a beállítás azt kihagyjuk – kattintson `next` folytatásához. 

7. Az a **operációs rendszer Rendszerjogosultságú csoportokat** lapon, az alapértelmezett beállításokat használja. Kattintson a `next` folytatásához.

8. Az a **adja meg a telepítési hely** lapon, az alapértelmezett beállításokat használja. Kattintson a `next` folytatásához.

9. Az a **készlet létrehozása** lapon, a készlet könyvtárát `/u01/app/grid/oraInventory`. Kattintson a `next` folytatásához.

   ![A telepítő-készlet létrehozása oldalát bemutató képernyőkép](./media/oracle-asm/install08.png)

10. Az a **legfelső szintű szkript végrehajtási konfiguráció** lapon válassza ki a **automatikusan a konfigurációs szkripteket futtasson** jelölőnégyzetet. Ezután válassza ki a **"root" felhasználói hitelesítő adatok használata** lehetőséget, majd adja meg a felhasználói jelszót.

    ![A telepítő legfelső szintű szkript végrehajtási konfiguráció lapjának képernyőképe](./media/oracle-asm/install09.png)

11. Az a **végre előfeltétel-ellenőrzési** oldalon, a jelenlegi telepítés sikertelen lesz, és hibákat. Ez az a várt viselkedéssel. Válassza a(z) `Fix & Check Again` lehetőséget.

12. Az a **Opravu parancsfájl** párbeszédpanelen kattintson a `OK`.

13. Az a **összefoglalás** lapon tekintse át a beállításokat, és kattintson a `Install`.

    ![A telepítő összesítő oldalát bemutató képernyőkép](./media/oracle-asm/install12.png)

14. Egy figyelmeztetés jelenik meg a tájékoztatása, konfigurációs parancsfájlokat kell futtatni, egy rendszergazdai engedéllyel rendelkező felhasználó. Kattintson a `Yes` folytatásához.

15. Az a **Befejezés** kattintson `Close` telepítésének befejezéséhez.

## <a name="set-up-your-oracle-asm-installation"></a>Oracle ASM-telepítésének beállítása

Az Oracle ASM telepítése beállításához, kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy továbbra is felhasználóként van bejelentkezve **rács**, a X11 a munkamenet. Szüksége lehet a találati `enter` helyreállítani azt a terminálon. Indítsa el az Oracle automatikus Storage Management Configuration Segéd majd:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Oracle ASM konfigurálása Segéd nyílik meg.

2. Az a **ASM konfigurálása: Csoportok lemez** párbeszédpanelen kattintson a `Create` gombra, majd `Show Advanced Options`.

3. Az a **lemez-csoport létrehozása** párbeszédpanel:

   - Adja meg a lemez nevét **adatok**.
   - A **tag lemezek kiválasztása**válassza **ORCL_DATA** és **ORCL_DATA1**.
   - A **lemezfoglalási egység mérete**válassza **4**.
   - Kattintson a `ok` a lemez csoport létrehozásához.
   - Kattintson a `ok` gombra kattintva zárja be a megerősítési ablakban.

   ![A lemez-csoport létrehozása párbeszédpanel képernyőképe](./media/oracle-asm/asm02.png)

4. Az a **ASM konfigurálása: Csoportok lemez** párbeszédpanelen kattintson a `Create` gombra, majd `Show Advanced Options`.

5. Az a **lemez-csoport létrehozása** párbeszédpanel:

   - Adja meg a lemez nevét **FRA**.
   - A **redundancia**válassza **(nincs) külső**.
   - A **tag lemezek kiválasztása**válassza **ORCL_FRA**.
   - A **lemezfoglalási egység mérete**válassza **4**.
   - Kattintson a `ok` a lemez csoport létrehozásához.
   - Kattintson a `ok` gombra kattintva zárja be a megerősítési ablakban.

   ![A lemez-csoport létrehozása párbeszédpanel képernyőképe](./media/oracle-asm/asm04.png)

6. Válassza ki **kilépési** ASM konfigurációs Segéd bezárásához.

   ![Képernyőkép az ASM konfigurálása: Lemez csoportok párbeszédpanelt a Kilépés gombra](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Az adatbázis létrehozása

Az Oracle-adatbázis szoftver már telepítve van az Azure Marketplace-beli rendszerképét. Hozzon létre egy adatbázist, hajtsa végre az alábbi lépéseket:

1. Az Oracle superuser felhasználói váltson, és majd a naplózáshoz a figyelő inicializálása:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```
   Az adatbázis-konfigurációs Segéd megnyílik.

2. Az a **adatbázis-művelet** kattintson `Create Database`.

3. Az a **létrehozási mód** oldalon:

   - Adja meg az adatbázis nevét.
   - A **tárolótípus**, győződjön meg, hogy **automatikus Storage szolgáltatásfelügyelet (ASM)** van kiválasztva.
   - A **adatbázisfájlok helye**, használja az alapértelmezett ASM hely javasolt.
   - A **gyors helyreállítási terület**, használja az alapértelmezett ASM hely javasolt.
   - Írjon be egy **rendszergazdai jelszó** és **jelszó megerősítése**.
   - Győződjön meg, hogy `create as container database` van kiválasztva.
   - Írjon be egy `pluggable database name` értéket.

4. Az a **összefoglalás** lapon tekintse át a beállításokat, és kattintson a `Finish` az adatbázis létrehozásához.

   ![Képernyőkép az összefoglalás lapon](./media/oracle-asm/createdb03.png)

5. Az adatbázis létrejött. Az a **Befejezés** lapon lehetősége van ezt az adatbázist használja, és a jelszavak módosítása további fiókok zárolásának feloldásához. Ha szeretné megtenni, válassza ki a **jelszókezelés** – ellenkező esetben kattintson a `close`.

## <a name="delete-the-vm"></a>A virtuális gép törlése

Sikeresen konfigurálta a Oracle DB-lemezképet az Azure Marketplace-ről Oracle automatikus Tároláskezelés.  Ha már nincs szüksége a virtuális Gépet, a következő paranccsal eltávolítható az erőforráscsoport, a virtuális gép és minden kapcsolódó erőforrás:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

[Oktatóanyag: Oracle DataGuard konfigurálása](configure-oracle-dataguard.md)

[Oktatóanyag: Configure Oracle GoldenGate](Configure-oracle-golden-gate.md)

Felülvizsgálat [Oracle-adatbázis tervezése](oracle-design.md)
