---
title: Linuxos virtuálisgép-feladat-visszavétel fő célkiszolgálójának telepítése az Azure Site Recovery szolgáltatással
description: Ismerje meg, hogyan állíthat be egy Linux-fő célkiszolgálót a helyszíni helyre a VMware virtuális gépek Azure-ba az Azure-ba az Azure-ba az Azure-ba az Azure-ba az Azure Site Recovery használatával történő vész-helyreállítási helyreállítása során.
author: mayurigupta13
services: site-recovery
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: mayg
ms.openlocfilehash: 5b4d625d28584bb601905e9439c112c845219e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954381"
---
# <a name="install-a-linux-master-target-server-for-failback"></a>Linux fő célkiszolgáló telepítése feladat-visszavételhez
Miután feladatátvételt az Azure-ba, a virtuális gépek a helyszíni hely. A feladat-visszavételhez újra meg kell védenie a virtuális gépet az Azure-ból a helyszíni helyre. Ehhez a folyamathoz a forgalom fogadásához helyszíni fő célkiszolgálóra van szükség. 

Ha a védett virtuális gép egy Windows virtuális gép, akkor windowsos fő célra van szüksége. Egy Linux virtuális gép, szüksége van egy Linux fő cél. Olvassa el az alábbi lépéseket, hogy megtanulják, hogyan hozhat létre és telepíthet egy Linux-fő cél.

> [!IMPORTANT]
> A 9.10.0 fő célszerver kiadásáttól kezdve a legújabb fő célkiszolgáló csak ubuntu 16.04 kiszolgálóra telepíthető. A CentOS6.6 kiszolgálóin nem engedélyezettek az új telepítések. A régi fő célkiszolgálókat azonban a 9.10.0-s verzió használatával továbbra is frissítheti.
> Az LVM fő célkiszolgálója nem támogatott.

## <a name="overview"></a>Áttekintés
Ez a cikk a Linux-fő cél telepítésének útmutatója.

A cikk végén vagy az [Azure Recovery Services Fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)megjegyzéseket vagy kérdéseket tehet közzé.

## <a name="prerequisites"></a>Előfeltételek

* Válassza ki azt a gazdagépet, amelyen a fő tárolót telepíteni szeretné, határozza meg, hogy a feladat-visszavétel egy meglévő helyszíni virtuális gépre vagy egy új virtuális gépre lesz-e. 
    * Egy meglévő virtuális gép, a fő cél gazdagép hozzáférhessen a virtuális gép adattárak.
    * Ha a helyszíni virtuális gép nem létezik (alternatív hely helyreállítása esetén), a feladat-visszavételi virtuális gép ugyanazon az állomáson jön létre, mint a fő cél. Bármelyik ESXi állomást kiválaszthatja a fő cél telepítéséhez.
* A fő célnak olyan hálózaton kell lennie, amely képes kommunikálni a folyamatkiszolgálóval és a konfigurációs kiszolgálóval.
* A fő cél verziójának egyenlőnek vagy korábbinak kell lennie, mint a folyamatkiszolgáló és a konfigurációs kiszolgáló verzióinak. Ha például a konfigurációs kiszolgáló verziója 9.4, a fő cél verziója lehet 9.4 vagy 9.3, de nem 9.5.
* A fő cél csak vmware virtuális gép lehet, fizikai kiszolgáló nem.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>A fő célkiszolgáló létrehozásának méretezési irányelvei

Hozza létre a fő célt a következő méretezési irányelveknek megfelelően:
- **RAM**: 6 GB vagy több
- **Operációs rendszer lemezmérete**: 100 GB vagy több (operációs rendszer telepítéséhez)
- **További lemezméret a megőrzési meghajtóhoz:** 1 TB
- **CPU magok:** 4 mag vagy több

A következő Ubuntu kernelek támogatottak.


|Kernel sorozat  |Támogatás legfeljebb  |
|---------|---------|
|4.4      |4.4.0-81-generikus         |
|4.8      |4.8.0-56-generikus         |
|4.10     |4.10.0-24-generikus        |


## <a name="deploy-the-master-target-server"></a>A fő célkiszolgáló telepítése

### <a name="install-ubuntu-16042-minimal"></a>Az Ubuntu 16.04.2 telepítése Minimális

Az Ubuntu 16.04.2 64 bites operációs rendszer telepítéséhez kövesse az alábbi lépéseket.

1.   Menj a [letöltési linkre](http://old-releases.ubuntu.com/releases/16.04.2/ubuntu-16.04.2-server-amd64.iso), válassza ki a legközelebbi tükröt, és töltsön le egy Ubuntu 16.04.2 minimális 64 bites ISO-t.
Tartsa az Ubuntu 16.04.2 minimális 64 bites ISO a DVD-meghajtót, és indítsa el a rendszert.

1.  Válassza az **angol nyelvet** a kívánt nyelvként, majd válassza az **Enter**lehetőséget.
    
    ![Válasszon nyelvet](./media/vmware-azure-install-linux-master-target/image1.png)
1. Válassza **az Ubuntu Server telepítése**lehetőséget, majd az **Enter**lehetőséget.

    ![Válassza az Ubuntu Server telepítése lehetőséget](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Válassza az **angol nyelvet** a kívánt nyelvként, majd válassza az **Enter**lehetőséget.

    ![Válassza ki az angolnyelvet a kívánt nyelvként.](./media/vmware-azure-install-linux-master-target/image3.png)

1. Válassza ki a megfelelő beállítást az **Időzóna** beállításlistájában, majd válassza az **Enter lehetőséget.**

    ![A megfelelő időzóna kiválasztása](./media/vmware-azure-install-linux-master-target/image4.png)

1. Válassza a **Nem** (alapértelmezett beállítás) lehetőséget, majd az **Enter**lehetőséget.

     ![A billentyűzet konfigurálása](./media/vmware-azure-install-linux-master-target/image5.png)
1. Válassza az **Angol (US)** lehetőséget a billentyűzet származási országaként/régiójaként, majd válassza az **Enter (Enter) lehetőséget.**

1. Billentyűzetkiosztásként válassza az **Angol (US)** lehetőséget, majd válassza az **Enter**lehetőséget.

1. Írja be a kiszolgáló állomásnevét a **Hostname** mezőbe, majd kattintson a **Folytatás gombra.**

1. Felhasználói fiók létrehozásához írja be a felhasználónevet, majd kattintson a **Folytatás gombra.**

      ![Felhasználói fiók létrehozása](./media/vmware-azure-install-linux-master-target/image9.png)

1. Írja be az új felhasználói fiók jelszavát, majd kattintson a **Folytatás gombra.**

1.  Erősítse meg az új felhasználó jelszavát, majd kattintson a **Folytatás gombra.**

    ![A jelszavak megerősítése](./media/vmware-azure-install-linux-master-target/image11.png)

1.  A kezdőkönyvtár titkosításához legközelebb válassza a **Nem** (alapértelmezett beállítás) lehetőséget, majd az **Enter lehetőséget.**

1. Ha a megjelenített időzóna helyes, válassza az **Igen** (alapértelmezett beállítás) lehetőséget, majd az **Enter lehetőséget.** Az időzóna újrakonfigurálásához válassza a **Nem**lehetőséget.

1. A particionálási módszer beállításai között válassza az **Irányított - teljes lemez használata**, majd az **Enter**lehetőséget.

     ![Válassza a particionálási módszer beállítását](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Válassza ki a megfelelő lemezt a **Partícióra** szolgáló lemez kiválasztása beállításból, majd válassza az **Enter**lehetőséget.

    ![A lemez kijelölése](./media/vmware-azure-install-linux-master-target/image15.png)

1.  A módosítások lemezre írásához válassza az **Igen** lehetőséget, majd válassza az **Enter**lehetőséget.

    ![Az alapértelmezett beállítás kiválasztása](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  A Proxy beállítási elemében jelölje be az alapértelmezett beállítást, válassza a **Continue (Folytatás)** lehetőséget, majd az **Enter**lehetőséget.
     
     ![A frissítések kezelésének kiválasztása](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Válassza az **Automatikus frissítések lehetőséget** a rendszer frissítéseinek kezeléséhez szükséges beállításokban, majd válassza az Enter **lehetőséget.**

     ![A frissítések kezelésének kiválasztása](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Mivel az Azure Site Recovery főkiszolgáló jap-kiszolgáló az Ubuntu egy nagyon speciális verzióját igényli, biztosítania kell, hogy a kernel frissítések le legyenek tiltva a virtuális gépen. Ha engedélyezve vannak, akkor a rendszeres frissítések a fő célkiszolgáló hibás működését okozzák. Győződjön meg arról, hogy a **Nincs automatikus frissítés** lehetőséget választotta.

1.  Adja meg az alapértelmezett beállításokat. Ha openSSH sSH csatlakozást szeretne, válassza az **OpenSSH kiszolgáló** lehetőséget, majd a **Continue (Folytatás)** lehetőséget.

    ![Szoftver kiválasztása](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. A GRUB rendszertöltő telepítéséhez válassza az **Igen**lehetőséget, majd válassza az **Enter**lehetőséget.
     
    ![GRUB rendszerindító telepítő](./media/vmware-azure-install-linux-master-target/image20.png)


1. Válassza ki a rendszertöltő telepítéséhez megfelelő eszközt (lehetőleg **/dev/sda),** majd válassza az **Enter**lehetőséget.
     
    ![Válassza ki a megfelelő eszközt](./media/vmware-azure-install-linux-master-target/image21.png)

1. Válassza **a Folytatás**gombot, majd a telepítés befejezéséhez válassza az **Enter** lehetőséget.

    ![A telepítés befejezése](./media/vmware-azure-install-linux-master-target/image22.png)

1. A telepítés befejezése után jelentkezzen be a virtuális gépaz új felhasználói hitelesítő adatokkal. (További információt a **10. lépésben** talál.)

1. Az alábbi képernyőképen ismertetett lépésekkel állítsa be a ROOT felhasználói jelszavát. Ezután jelentkezzen be ROOT felhasználóként.

    ![A ROOT felhasználói jelszó beállítása](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>A számítógép konfigurálása fő célkiszolgálóként

Ahhoz, hogy az azonosító minden SCSI merevlemez egy Linux virtuális gép, a **lemez. EnableUUID = TRUE** paramétert engedélyezni kell. A paraméter engedélyezéséhez tegye a következő lépéseket:

1. Állítsa le a virtuális gépet.

2. Kattintson a jobb gombbal a virtuális gép bejegyzésére a bal oldali ablaktáblában, majd válassza a **Beállítások szerkesztése parancsot.**

3. Válassza a **Beállítások** lapot.

4. A bal oldali ablaktáblában válassza a **Speciális** > **általános**lehetőséget, majd a képernyő jobb alsó részén a **Konfigurációs paraméterek** gombot.

    ![Konfigurációs paraméter megnyitása](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    A **Konfigurációs paraméterek** beállítás nem érhető el, ha a gép fut. Ha aktívvá szeretné tenni ezt a lapot, állítsa le a virtuális gépet.

5. Nézze meg, hogy van-e **lemezes sor. Az EnableUUID már** létezik.

   - Ha az érték létezik, és **értéke Hamis,** módosítsa az értéket **True**értékre. (Az értékek nem tartalmaznak kis- és nagybetűket.)

   - Ha az érték létezik, és **Értéke Igaz,** válassza **a Mégse lehetőséget.**

   - Ha az érték nem létezik, válassza **a Sor hozzáadása**lehetőséget.

   - A név oszlopban adja hozzá a **lemezt. EnableUUID**, majd állítsa az értéket **TRUE**értékre.

     ![Annak ellenőrzése, hogy a lemez. Az EnableUUID már létezik](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Kernel-frissítések letiltása

Az Azure Site Recovery főcélkiszolgálója az Ubuntu egy adott verzióját igényli, győződjön meg arról, hogy a kernel frissítések le vannak tiltva a virtuális gépen. Ha a kernelfrissítések engedélyezve vannak, a fő célkiszolgáló hibás működését okozhatja.

#### <a name="download-and-install-additional-packages"></a>További csomagok letöltése és telepítése

> [!NOTE]
> Győződjön meg arról, hogy rendelkezik internetkapcsolattal a további csomagok letöltéséhez és telepítéséhez. Ha nem rendelkezik internetkapcsolattal, manuálisan meg kell találnia ezeket a Deb csomagokat, és telepítenie kell őket.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>A telepítő beszerezni a telepítőt

Ha a fő cél rendelkezik internetkapcsolattal, a következő lépésekkel töltheti le a telepítőt. Ellenkező esetben átmásolhatja a telepítőt a folyamatkiszolgálóról, majd telepítheti azt.

#### <a name="download-the-master-target-installation-packages"></a>A fő céltelepítő csomagok letöltése

[Töltse le a legújabb Linux fő cél telepítési biteket](https://aka.ms/latestlinuxmobsvc).

A Linux használatával történő letöltéshez írja be a következőt:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Győződjön meg arról, hogy a kezdőkönyvtárban töltötte le és csomagolja ki a telepítőt. Ha a **/usr/Local**kapcsolót csomagolja ki, akkor a telepítés sikertelen lesz.


#### <a name="access-the-installer-from-the-process-server"></a>A telepítő elérése a folyamatkiszolgálóról

1. A folyamatkiszolgálón nyissa meg a **C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository mappát.**

2. Másolja a szükséges telepítőfájlt a folyamatkiszolgálóról, és mentse **a kezdőkönyvtárába legújabb linuxmobsvc.tar.gz** fájlként.


### <a name="apply-custom-configuration-changes"></a>Egyéni konfigurációs módosítások alkalmazása

Egyéni konfigurációs módosítások alkalmazásához root felhasználóként hajtsa végre a következő lépéseket:

1. Futtassa a következő parancsot a bináris kioldásához.

    `tar -xvf latestlinuxmobsvc.tar.gz`

    ![Képernyőkép a futtatni andó parancsról](./media/vmware-azure-install-linux-master-target/image16.png)

2. Az engedély engedélyezéséhez futtassa a következő parancsot.

    `chmod 755 ./ApplyCustomChanges.sh`


3. A szkript futtatásához hajtsa végre a következő parancsot.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Futtassa a parancsfájlt csak egyszer a kiszolgálón. Ezután állítsa le a kiszolgálót. A lemez hozzáadása után indítsa újra a kiszolgálót, ahogy azt a következő szakasz ismerteti.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Adatmegőrzési lemez hozzáadása a Linux-fő célvirtuális géphez

Adatmegőrzési lemez létrehozásához kövesse az alábbi lépéseket:

1. Csatlakoztasson egy új 1 TB-os lemezt a Linux-fő célvirtuális géphez, majd indítsa el a gépet.

2. A **multipath -ll** paranccsal megtanulja a megőrzési lemez többutas azonosítóját: **multipath -ll**

    ![Többutas azonosító](./media/vmware-azure-install-linux-master-target/image27.png)

3. Formázza a meghajtót, majd hozzon létre egy fájlrendszert az új meghajtón: **mkfs.ext4 /dev/mapper/\<Retention disk's multipath id>**.
    
    ![Fájlrendszer](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. A fájlrendszer létrehozása után csatlakoztassa az adatmegőrzési lemezt.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Hozza létre az **fstab** bejegyzést a visszatartó meghajtó csatlakoztatásához minden alkalommal, amikor a rendszer elindul.
    
    `vi /etc/fstab`
    
    A fájl szerkesztésének megkezdéséhez válassza a **Beszúrás** lehetőséget. Hozzon létre egy új sort, majd szúrja be a következő szöveget. A lemez többutas azonosítójának szerkesztése az előző parancs kiemelt többutas azonosítója alapján.

    **/dev/mapper/\<Megőrzési lemezek többutas> /mnt/retention ext4 rw 0 0**

    Válassza **az Esc**lehetőséget, majd a szerkesztőablak bezárásához írja be a **:wq** (írás és kilépés) parancsot.

### <a name="install-the-master-target"></a>A fő cél telepítése

> [!IMPORTANT]
> A fő célkiszolgáló verziójának egyenlőnek vagy korábbinak kell lennie, mint a folyamatkiszolgáló és a konfigurációs kiszolgáló verzióinak. Ha ez a feltétel nem teljesül, az újbóli védelem sikeres, de a replikáció sikertelen.


> [!NOTE]
> A fő célkiszolgáló telepítése előtt ellenőrizze, hogy a virtuális gépen lévő **/etc/hosts** fájl tartalmaz-e olyan bejegyzéseket, amelyek a helyi állomásnevet az összes hálózati adapterhez társított IP-címekhez társítják.

1. Másolja a jelszót a **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase mappából** a konfigurációs kiszolgálón. Ezután mentse **passphrase.txt** fájlként ugyanabban a helyi könyvtárban a következő parancs futtatásával:

    `echo <passphrase> >passphrase.txt`

    Példa: 

       `echo itUx70I47uxDuUVY >passphrase.txt`
    

2. Jegyezze fel a konfigurációs kiszolgáló IP-címét. A következő parancs futtatásával telepítse a fő célkiszolgálót, és regisztrálja a kiszolgálót a konfigurációs kiszolgálóval.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Példa: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Várjon, amíg a szkript befejeződik. Ha a fő cél sikeresen regisztrál, a fő cél a portál **Hely-helyreállítási infrastruktúra** lapján jelenik meg.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>A fő cél telepítése interaktív telepítéssel

1. A fő cél telepítéséhez futtassa a következő parancsot. Az ügynöki szerepkörhöz válassza a **fő célt.**

    ```
    ./install
    ```

2. Válassza ki a telepítés alapértelmezett helyét, majd a folytatáshoz válassza az **Enter** lehetőséget.

    ![A fő cél telepítésének alapértelmezett helyének kiválasztása](./media/vmware-azure-install-linux-master-target/image17.png)

A telepítés befejezése után regisztrálja a konfigurációs kiszolgálót a parancssorból.

1. Jegyezze fel a konfigurációs kiszolgáló IP-címét. Szükséged van rá a következő lépésben.

2. A következő parancs futtatásával telepítse a fő célkiszolgálót, és regisztrálja a kiszolgálót a konfigurációs kiszolgálóval.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    Példa: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

     Várjon, amíg a szkript befejeződik. Ha a fő cél sikeresen regisztrálva van, a fő cél megjelenik a portál **Hely-helyreállítási infrastruktúra** lapján.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>Telepítse vmware eszközök / open-vm-eszközök a fő célszerver

Telepítenie kell a VMware-eszközöket vagy a nyílt vm-eszközöket a fő célra, hogy felderíthese az adattárakat. Ha az eszközök nincsenek telepítve, az újravédő képernyő nem jelenik meg az adattárakban. A VMware-eszközök telepítése után újra kell indítania.

### <a name="upgrade-the-master-target-server"></a>A fő célkiszolgáló frissítése

Indítsa el a telepítőt. Automatikusan észleli, hogy az ügynök telepítve van a fő cél. A frissítéshez válassza az **Y**lehetőséget.  A telepítés befejezése után ellenőrizze a fő cél verzióját a következő paranccsal:

`cat /usr/local/.vx_version`


Látni fogja, hogy a **Verzió** mező megadja a fő cél verziószámát.

## <a name="common-issues"></a>Gyakori problémák

* Győződjön meg róla, hogy nem kapcsolja be a Storage vMotion-t semmilyen felügyeleti összetevőn, például egy fő célon. Ha a fő cél a sikeres újravédelem után mozog, a virtuálisgép-lemezek (VMDK-k) nem választhatók le. Ebben az esetben a feladat-visszavétel sikertelen.

* A fő cél nem rendelkezik pillanatképekkel a virtuális gépen. Ha vannak pillanatképek, a feladat-visszavétel sikertelen lesz.

* Néhány egyéni hálózati adapter konfigurációja miatt a hálózati adapter le van tiltva az indítás során, és a fő célügynök nem inicializálható. Ellenőrizze, hogy a következő tulajdonságok megfelelően vannak-e beállítva. Ellenőrizze ezeket a tulajdonságokat az Ethernet-kártyafájl /etc/sysconfig/network-scripts/ifcfg-eth* fájljában.
    * BOOTPROTO=dhcp
    * ONBOOT=igen


## <a name="next-steps"></a>További lépések
Miután a fő cél telepítése és regisztrálása befejeződött, a fő cél a **Hely-helyreállítási infrastruktúra** **fő célszakaszában** látható a konfigurációs kiszolgáló áttekintése alatt.

Most folytathatja az [újravédelmet,](vmware-azure-reprotect.md)majd a feladat-visszavételt.

