---
title: A Linuxos fő célkiszolgáló feladatátvételi telepítse az Azure-ból a helyszíni |} A Microsoft Docs
description: Egy Linux rendszerű virtuális gép ismételt védelme, mielőtt szüksége van egy Linux rendszerű fő célkiszolgálót. Ismerje meg, hogyan telepítsen egyet.
author: nsoneji
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: nisoneji
ms.openlocfilehash: 470d4547a63105e319f072461a4d8f24ebe3dbe8
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094122"
---
# <a name="install-a-linux-master-target-server"></a>A Linuxos fő célkiszolgáló telepítése
Miután átadja a feladatokat a virtuális gépek az Azure-ba, visszaadhatja a virtuális gépek, a helyszíni helyre. Feladat-visszavételt, ismételt védelem a virtuális gép az Azure-ból a helyszíni helyre kell. Ez a folyamat szüksége lesz egy a helyszíni fő célkiszolgáló forgalom fogadására. 

Ha a védett virtuális gépet egy Windows virtuális gépet, majd meg kell egy Windows fő célkiszolgáló. Linuxos virtuális gép szüksége lesz egy Linux rendszerű fő célkiszolgálót. Olvassa el az alábbi lépések végrehajtásával megtudhatja, hogyan hozhat létre, és a Linuxos fő célkiszolgáló telepítéséhez.

> [!IMPORTANT]
> A 9.10.0 kiadásával kezdődően fő célkiszolgálót a legújabb fő célkiszolgáló csak telepíthető egy Ubuntu 16.04-kiszolgálón. Új telepítések CentOS6.6 kiszolgálókon nem engedélyezett. Azonban továbbra is a régi fő célkiszolgálók frissítése a 9.10.0 használatával verzió.

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti a Linuxos fő célkiszolgáló telepítése.

Megjegyzéseit vagy kérdéseit közzététele Ez a cikk vagy a végén a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Előfeltételek

* A gazdagépen, amelyen a fő célkiszolgáló telepítéséhez kiválasztásához határozza meg, ha a feladat-visszavételi topológiában a helyszíni virtuális gép vagy egy új virtuális gépet. 
    * Egy meglévő virtuális gép esetében a gazdagép a fő célkiszolgáló hozzáféréssel kell rendelkeznie a virtuális gép adattárakban.
    * Ha a helyszíni virtuális gép nem létezik (esetén helyreállítást másik helyre), a feladat-visszavételi virtuális gép jön létre a fő célkiszolgáló ugyanazon a gazdagépen. Minden ESXi-gazdagép, a fő célkiszolgáló telepítéséhez választhat.
* A fő célkiszolgálón, amely a folyamatkiszolgáló és a konfigurációs kiszolgáló képes kommunikálni a hálózaton kell lennie.
* A fő célkiszolgáló verziója korábbi, mint a folyamatkiszolgáló és a konfigurációs kiszolgáló verziója vagy azzal egyenlőnek kell lennie. Például ha a konfigurációs kiszolgáló verziója 9.4, a fő célkiszolgáló verziója lehet 9.4 vagy 9.3, de nem 9,5.
* A fő célkiszolgáló csak lehet, VMware virtuális gépeket és a egy fizikai kiszolgáló.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Méretezési útmutatójának a fő célkiszolgáló létrehozása

Hozza létre a fő célkiszolgáló megfelelően a méretezése a következőkre:
- **RAM**: 6 GB vagy több
- **Operációsrendszer-lemez mérete**: 100 GB vagy több (az operációs rendszer telepítése)
- **Adatmegőrzési meghajtó méretét további**: 1 TB-ot
- **CPU-magok**: 4 mag, vagy több

A következő támogatott Ubuntu kernelekkel támogatottak.


|Kernel-sorozat  |Legfeljebb  |
|---------|---------|
|4.4      |4.4.0-81-generic         |
|4.8      |4.8.0-56-Generic         |
|4.10     |4.10.0-24-Generic        |


## <a name="deploy-the-master-target-server"></a>A fő célkiszolgáló telepítése

### <a name="install-ubuntu-16042-minimal"></a>Ubuntu 16.04.2 telepítése minimális

Vegye figyelembe a következőket az Ubuntu 16.04.2 64 bites operációs rendszer telepítésének lépéseit.

1.   Nyissa meg a [letöltési hivatkozás](https://www.ubuntu.com/download/server/thank-you?version=16.04.2&architecture=amd64), válassza a legközelebbi tükrözött anddownload egy Ubuntu 16.04.2 minimális 64-bit ISO.
Egy Ubuntu 16.04.2 minimális 64-bit ISO tartsa a DVD-meghajtóba, és indítsa el a rendszer.

1.  Válassza ki **angol** a választott nyelven, és válassza ki, **Enter**.
    
    ![Válasszon nyelvet](./media/vmware-azure-install-linux-master-target/image1.png)
1. Válassza ki **Ubuntu Server telepítése**, majd válassza ki **Enter**.

    ![Válassza ki az Ubuntu Server telepítése](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Válassza ki **angol** a választott nyelven, és válassza ki, **Enter**.

    ![Válassza ki az angol, a választott nyelven](./media/vmware-azure-install-linux-master-target/image3.png)

1. Válassza ki a megfelelő lehetőséget a **időzóna** lehetőségek listájából, és válassza ki **Enter**.

    ![Válassza ki a megfelelő időzónában](./media/vmware-azure-install-linux-master-target/image4.png)

1. Válassza ki **nem** (az alapértelmezett beállítás), majd válassza ki **Enter**.

     ![A billentyűzet konfigurálása](./media/vmware-azure-install-linux-master-target/image5.png)
1. Válassza ki **angol (amerikai)** , a billentyűzet, majd válassza ki a származási **Enter**.

1. Válassza ki **angol (amerikai)** a billentyűzetkiosztás, és válassza ki, **Enter**.

1. Adja meg az állomásnevet a kiszolgáló számára a **állomásnév** mezőbe, majd válassza ki **Folytatás**.

1. Hozzon létre egy felhasználói fiókot, adja meg a felhasználónevet, és válassza **Folytatás**.

      ![Felhasználói fiók létrehozása](./media/vmware-azure-install-linux-master-target/image9.png)

1. Adja meg az új felhasználói fiók jelszavát, és válassza **Folytatás**.

1.  Az új felhasználó jelszavát, és válassza ki **Folytatás**.

    ![A jelszó megerősítése](./media/vmware-azure-install-linux-master-target/image11.png)

1.  A következő kijelölés a kezdőkönyvtár titkosításához, jelölje be a **nem** (az alapértelmezett beállítás), majd válassza ki **Enter**.

1. Ha az adott időzóna megjelenített megfelelő, válassza ki a **Igen** (az alapértelmezett beállítás), majd válassza ki **Enter**. Konfigurálja újra az időzónát, jelölje be **nem**.

1. Az particionálási mód közül válassza ki a **interaktív – használja a teljes lemez**, majd válassza ki **Enter**.

     ![Válassza a particionálási mód](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Válassza ki a megfelelő lemezt a **válassza lemez partíció** lehetőségeket, és válassza ki **Enter**.

    ![Válassza ki a lemezt](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Válassza ki **Igen** írni lemezre, és válassza ki a módosítások **Enter**.

    ![Válassza ki az alapértelmezett beállítás](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  Válassza ki a configure proxy kijelölés, válassza ki az alapértelmezett beállítás **Folytatás**, majd válassza ki **Enter**.
     
     ![Válassza ki a frissítések kezelése](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Válassza ki **nincs automatikus frissítések** a kijelölt frissítéseket a rendszer kezelése lehetőséget, majd válassza ki **Enter**.

     ![Válassza ki a frissítések kezelése](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Az Azure Site Recovery fő célkiszolgáló az Ubuntu jellemző verziója szükséges, mert annak érdekében, hogy a kernel verziófrissítések le vannak tiltva, a virtuális gép szüksége. Ha engedélyezve vannak, a rendszeres megjelenjenek a fő célkiszolgáló hibás működését miatt. Mindenképpen jelölje ki a **nincs automatikus frissítések** lehetőséget.

1.  Válassza ki az alapértelmezett beállításokat. Ha openSSH SSH csatlakozás vonatkozó, jelölje be a **OpenSSH-server** lehetőséget, majd válassza ki **Folytatás**.

    ![Válassza ki a szoftver](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. Válassza ki a grub-HIBÁT rendszertöltő telepítéséhez a selction **Igen**, majd válassza ki **Enter**.
     
    ![GRUB rendszerindító telepítő](./media/vmware-azure-install-linux-master-target/image20.png)


1. Válassza ki a megfelelő eszköz, a rendszerindító betöltő telepítés (lehetőleg **/dev/sda**), majd válassza ki **Enter**.
     
    ![Válassza ki a megfelelő eszköz](./media/vmware-azure-install-linux-master-target/image21.png)

1. Válassza ki **Folytatás**, majd válassza ki **Enter** telepítésének befejezéséhez.

    ![A telepítés befejezése](./media/vmware-azure-install-linux-master-target/image22.png)

1. Miután a telepítés befejeződött, jelentkezzen be a virtuális Gépet az új felhasználói hitelesítő adatokkal. (Tekintse meg **10. lépésében** további információt.)

1. Használja az alábbi képernyőképen a felhasználó jelszót állíthat be a legfelső szintű leírt lépéseket. Ezután jelentkezzen be GYÖKÉRSZINTŰ felhasználóként.

    ![A legfelső szintű felhasználói jelszó beállítása](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>A gép egy fő célkiszolgáló konfigurálása

A Linux rendszerű virtuális gépen, minden egyes SCSI merevlemez Azonosítójának lekéréséhez a **lemez. EnableUUID = TRUE** paramétert meg kell engedélyezni. Ahhoz, hogy ezt a paramétert, a következő lépéseket:

1. Állítsa le a virtuális gépet.

2. Kattintson a jobb gombbal a virtuális gép a bal oldali ablaktáblán a bejegyzést, és válassza **beállításainak szerkesztése**.

3. Válassza ki a **beállítások** fülre.

4. A bal oldali panelen válassza ki a **speciális** > **általános**, majd válassza ki a **konfigurációs paramétereket** gombra a képernyő jobb alsó részén.

    ![Nyissa meg a konfigurációs paraméter](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    A **konfigurációs paramétereket** lehetőség nem érhető el, ha a gép fut-e. Ahhoz, hogy ezen a lapon aktív, a virtuális gép leállítása.

5. Tekintse meg, hogy a sor **lemez. EnableUUID** már létezik.

    - Ha az érték létezik, és értékre van állítva **hamis**, módosítsa az értéket **igaz**. (Az értékek nem kis-és nagybetűket.)

    - Ha az érték létezik, és értékre van állítva **igaz**válassza **Mégse**.

    - Ha az érték nem létezik, válassza ki a **sor hozzáadása**.

    - A név oszlop hozzáadása **lemez. EnableUUID**, majd állítsa az értékét, és **igaz**.

    ![Annak ellenőrzése e a lemezen. EnableUUID már létezik.](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Tiltsa le a kernel-frissítések

Az Azure Site Recovery fő célkiszolgáló szükséges egy adott verzióját az Ubuntu, győződjön meg arról, hogy a kernel frissítéseket le vannak tiltva, a virtuális gép. Ha kernel frissítések engedélyezve vannak, a fő célkiszolgáló hibás működését okozhat.

#### <a name="download-and-install-additional-packages"></a>További csomagok letöltése és telepítése

> [!NOTE]
> Győződjön meg arról, hogy van-e internetkapcsolat további csomagok letöltése és telepítése. Ha nem rendelkezik internetkapcsolattal, kell manuálisan keresse meg ezeket a RPM-csomagokat, és telepítse őket.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>A telepítő a telepítő beolvasása

Ha a fő célkiszolgáló rendelkezik internetkapcsolattal, a következő lépések segítségével töltse le a telepítőt. Ellenkező esetben másolja a telepítőt a folyamatkiszolgálóról, majd telepítse.

#### <a name="download-the-master-target-installation-packages"></a>Töltse le a fő célkiszolgáló telepítési csomagok

[Töltse le a legújabb Linux rendszerű fő célkiszolgáló telepítése bits](https://aka.ms/latestlinuxmobsvc).

Letöltheti a Linux használatával, írja be:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Győződjön meg arról, hogy töltse le és csomagolja ki a telepítő a kezdőkönyvtárban. Ha, csomagolja ki, hogy **/usr/Local**, majd a telepítés sikertelen lesz.


#### <a name="access-the-installer-from-the-process-server"></a>A telepítő a folyamatkiszolgálóról hozzáférés

1. A folyamatkiszolgáló, lépjen a **C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

2. Másolja a szükséges telepítőfájl a folyamatkiszolgáló, és mentse a fájt **latestlinuxmobsvc.tar.gz** a kezdőkönyvtárban.


### <a name="apply-custom-configuration-changes"></a>Egyéni konfigurációs módosítások alkalmazása

Egyéni konfigurációs módosítások alkalmazásához, használja az alábbi lépéseket:


1. A következő parancsot a bináris untar.

    `tar -zxvf latestlinuxmobsvc.tar.gz`

    ![Képernyőkép a parancs futtatása](./media/vmware-azure-install-linux-master-target/image16.png)

2. Futtassa a következő parancsot az engedélyt.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Futtassa a következő parancsot a szkript futtatásához.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Csak egyszer a szkript futtatása a kiszolgálón. Ezután állítsa le a kiszolgálót. Indítsa újra a kiszolgálót egy lemezt, hozzáadása után a következő szakaszban leírtak szerint.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Adatmegőrzési lemez hozzáadása a Linuxos fő célkiszolgáló virtuális géphez

A következő lépések segítségével hozzon létre egy adatmegőrzési lemez:

1. 1 TB-os új lemez csatolása a Linuxos fő célkiszolgáló virtuális géphez, és indítsa el a gépet.

2. Használja a **z-a többutas összes** parancsot az adatmegőrzési lemez többutas azonosítója további: **z-a többutas összes**

    ![A többutas azonosítója](./media/vmware-azure-install-linux-master-target/image27.png)

3. Formázza a meghajtót, és hozza létre az operációs rendszer az új meghajtó: **mkfs.ext4 /dev/eseményleképező/< adatmegőrzési lemez többutas azonosítója >**.
    
    ![Fájlrendszer](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. Miután létrehozta a fájlrendszer, csatlakoztassa az adatmegőrzési lemez.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Hozzon létre a **fstab** bejegyzés az adatmegőrzési meghajtó csatlakoztatása a rendszer minden indításakor.
    
    `vi /etc/fstab`
    
    Válassza ki **beszúrása** a fájl szerkeszthető. Hozzon létre egy új sort, és helyezze be a következő szöveget. Szerkessze a többutas Lemezazonosítót az előző parancs által kiemelt többutas azonosítója alapján.

    **/dev/eseményleképező/ <Retention disks multipath id> /mnt/megőrzési ext4 rw 0 0**

    Válassza ki **Esc**, majd írja be **: wq** (írás és kilépés) gombra kattintva zárja be a szerkesztő ablakot.

### <a name="install-the-master-target"></a>A fő célkiszolgáló telepítése

> [!IMPORTANT]
> A fő célkiszolgáló verziója korábbi, mint a folyamatkiszolgáló és a konfigurációs kiszolgáló verziója vagy azzal egyenlőnek kell lennie. Ha ez a feltétel nem teljesül, a védelem-újrabeállítás sikeres, de a replikáció sikertelen lesz.


> [!NOTE]
> Mielőtt telepíti a fő célkiszolgáló, ellenőrizze, hogy a **/etc/hosts** fájlt a virtuális gépen bejegyzéseket, amelyek a helyi gazdanevet az összes hálózati adapterhez társított IP-címeket tartalmazza.

1. Másolja a hozzáférési kódot a **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** a konfigurációs kiszolgálón. Ezután mentse a fájt **passphrase.txt** ugyanabban a helyi könyvtárban a következő parancs futtatásával:

    `echo <passphrase> >passphrase.txt`

    Példa: 

       `echo itUx70I47uxDuUVY >passphrase.txt`
    

2. Jegyezze fel a konfigurációs kiszolgáló IP-címet. Futtassa a következő parancsot a fő célkiszolgáló telepítéséhez, és a konfigurációs kiszolgálót regisztrálja a kiszolgálót.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Példa: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Várjon, amíg a parancsfájl befejeződik. Ha a fő célkiszolgáló regisztrálása sikeresen megtörtént, a fő célkiszolgáló megtalálható a **Site Recovery-infrastruktúra** a portál.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>A fő célkiszolgáló telepítéséhez használja az interaktív telepítés

1. Futtassa a következő parancsot a fő célkiszolgáló telepítéséhez. Az ügynök szerepkör kiválasztása **fő célkiszolgáló**.

    ```
    ./install
    ```

2. Válassza ki az alapértelmezett hely a telepítéshez, és válassza ki **Enter** folytatásához.

    ![Alapértelmezett hely a fő célkiszolgáló telepítéséhez kiválasztása](./media/vmware-azure-install-linux-master-target/image17.png)

A telepítés befejezése után, a konfigurációs kiszolgálót regisztrálja a parancssor használatával.

1. Megjegyzés: a konfigurációs kiszolgáló IP-címét. A következő lépésben kell.

2. Futtassa a következő parancsot a fő célkiszolgáló telepítéséhez, és a konfigurációs kiszolgálót regisztrálja a kiszolgálót.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    Példa: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

     Várjon, amíg a parancsfájl befejeződik. Ha a fő célkiszolgáló sikeres regisztrációja esetén a fő célkiszolgáló megtalálható a **Site Recovery-infrastruktúra** a portál.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>Telepítse a VMware-eszközök / nyílt-vm-eszközök a fő célkiszolgálón

Telepítenie kell a VMware-eszközök vagy a nyílt-vm-eszközök a fő célkiszolgáló, hogy azt felderíthessék adattárakban. Ha az eszközök nincsenek telepítve, a védelem-újrabeállítás képernyő nem szerepel a listán adattárakban. A VMware-eszközöket a telepítés után indítsa újra kell.

### <a name="upgrade-the-master-target-server"></a>Frissítse a fő célkiszolgálót

Futtassa a telepítőt. Automatikusan észleli, hogy az ügynök telepítve van-e a fő célkiszolgáló. Szeretné frissíteni, válassza ki a **Y**.  A telepítés befejezése után a következő parancs segítségével telepítve van a fő célkiszolgáló verziójának ellenőrzése:

`cat /usr/local/.vx_version`


Látni fogja, hogy a **verzió** mező lehetővé teszi a fő célkiszolgáló verziószáma.

## <a name="common-issues"></a>Gyakori problémák

* Ellenőrizze, hogy a Storage vMotion összetevőt minden olyan felügyeleti összetevők, például a fő célkiszolgáló nem kapcsolja. Ha a fő célkiszolgáló egy sikeres ismételt védelme után helyez át, nem sikerült leválasztani a virtuális gép lemezeinek (vmdk-inak). Ebben az esetben a feladat-visszavétele sikertelen lesz.

* A fő célkiszolgáló nem kell minden olyan pillanatképek a virtuális gépen. Ha pillanatképeket, feladat-visszavétele sikertelen lesz.

* Egyes egyéni Hálózatiadapter-konfigurációk miatt a hálózati adapter le van tiltva, a rendszerindítás során, és a fő célkiszolgáló ügynököt nem lehet inicializálni. Győződjön meg arról, hogy a következő tulajdonságai megfelelően vannak-e beállítva. Ellenőrizze, hogy ezek a tulajdonságok a Ethernet kártya fájl /etc/sysconfig/network-scripts/ifcfg-eth *.
    * BOOTPROTO = dhcp
    * ONBOOT = Igen


## <a name="next-steps"></a>További lépések
A telepítés és a fő célkiszolgáló regisztráció befejezése után, megjelenik a fő célkiszolgáló jelenik meg a **fő célkiszolgáló** szakasz **Site Recovery-infrastruktúra**, a konfiguráció alatt Server áttekintése.

Most már folytathatja [ismételt védelem](vmware-azure-reprotect.md), majd a feladat-visszavétel.

