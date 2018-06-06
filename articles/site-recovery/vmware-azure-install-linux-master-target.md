---
title: A Linux-fő célkiszolgáló feladatátvevő telepítése az Azure-ból a helyszíni |} Microsoft Docs
description: A Linux virtuális gép újbóli védelméhez, előtt kell egy Linux fő célkiszolgáló. Megtudhatja, hogyan telepítsen.
services: site-recovery
documentationcenter: ''
author: nsoneji
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 05/08/2018
ms.author: nisoneji
ms.openlocfilehash: 4d28a9a840b25ea415021da149ab145836cbed28
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34802510"
---
# <a name="install-a-linux-master-target-server"></a>A Linux fő célkiszolgáló telepítése
Miután a rendszer átadja a virtuális gépek Azure-ba, akkor is feladat-visszavételt a virtuális gépeket a helyszíni hely. A feladat-visszavételt, állítsa a virtuális gépet az Azure-ból a helyszíni helyre kell. Ez a folyamat szüksége van egy a helyszíni fő célkiszolgáló forgalom fogadására. 

Ha a védett virtuális gépet egy Windows rendszerű virtuális gép, majd meg kell olyan Windows fő célkiszolgálót. A Linux virtuális gép van szüksége a Linuxos fő célkiszolgáló. Olvassa el az alábbi lépések végrehajtásával megtudhatja, hogyan hozzon létre és telepítse a Linuxos fő célkiszolgáló.

> [!IMPORTANT]
> A 9.10.0 a kiadástól kezdve fő célkiszolgálót, a legújabb fő célkiszolgáló csak telepíthető egy Ubuntu 16.04 kiszolgálót. Új telepítések CentOS6.6 kiszolgálók nincsenek engedélyezve. Azonban továbbra is a régi fő célkiszolgálók frissítése a 9.10.0 használatával verziója.

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti a Linuxos fő célkiszolgáló telepítése.

Megjegyzéseit vagy kérdéseit a cikk vagy a végén utáni a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Előfeltételek

* Válassza ki a gazdagépet, amelyre a fő célkiszolgáló telepítése, használatával állapítsa meg, ha a feladat-visszavétel lesz egy meglévő helyszíni virtuális gép vagy egy új virtuális gépet. 
    * Egy meglévő virtuális gép a gazdagép a fő célkiszolgáló hozzáféréssel kell rendelkeznie a virtuális gép adattároló.
    * Ha a helyszíni virtuális gép nem létezik (esetén a másodlagos helyre történő helyreállítást), a feladat-visszavétel a virtuális gép jön létre, a fő célkiszolgáló ugyanazon a gazdagépen. Kiválaszthatja, hogy bármely telepítéséhez a fő célkiszolgáló ESXi-állomáson.
* A fő célkiszolgálón, amely a folyamatkiszolgáló és a kiszolgáló képes kommunikálni a hálózaton kell lennie.
* A fő célkiszolgáló verziója korábbi, mint a folyamatkiszolgáló és a konfigurációs kiszolgáló verziói vagy azzal egyenlőnek kell lennie. Például ha a konfigurációs kiszolgáló verziószáma 9.4, a fő célkiszolgáló verziója lehet 9.4 vagy 9.3, de nem 9,5.
* A fő célkiszolgáló csak akkor lehet VMware virtuális gépek nem egy fizikai kiszolgálón.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>A fő célkiszolgáló létrehozásához méretezési útmutató

Hozza létre a fő célkiszolgálón a következő méretezési irányelvek szerint:
- **RAM**: legalább 6 GB
- **Az operációs rendszer lemezméret**: 100 GB vagy több (az operációs rendszer telepítése)
- **További lemezmérete adatmegőrzési meghajtó**: 1 TB-os
- **A Processzormagok**: 4 mag, vagy több

A következő támogatott Ubuntu kernelek támogatottak.


|Kernel-sorozat  |Legfeljebb  |
|---------|---------|
|4.4      |4.4.0-81-generic         |
|4.8      |4.8.0-56-Generic         |
|4.10     |4.10.0-24-Generic        |


## <a name="deploy-the-master-target-server"></a>A fő célkiszolgáló telepítése

### <a name="install-ubuntu-16042-minimal"></a>Ubuntu 16.04.2 telepítése minimális

A következő igénybe az Ubuntu 16.04.2 64 bites operációs rendszer telepítésének lépéseit.

1.   Lépjen a [letöltése hivatkozásra](https://www.ubuntu.com/download/server/thank-you?version=16.04.2&architecture=amd64), válassza ki a legközelebbi tükör anddownload Ubuntu 16.04.2 minimális 64 bites ISO-Lemezképet.
Ubuntu 16.04.2 minimális 64 bites ISO-Lemezképet tartani a DVD-meghajtóba, és indítsa el a rendszer.

1.  Válassza ki **angol** a kívánt nyelvet, és válassza ki, **Enter**.
    
    ![Válasszon nyelvet](./media/vmware-azure-install-linux-master-target/image1.png)
1. Válassza ki **Ubuntu Server telepítése**, majd válassza ki **Enter**.

    ![Válassza ki a telepítés Ubuntu Server](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Válassza ki **angol** a kívánt nyelvet, és válassza ki, **Enter**.

    ![Jelölje ki a kívánt nyelvet angol nyelven](./media/vmware-azure-install-linux-master-target/image3.png)

1. Válassza ki a megfelelő beállítást a **időzóna** beállítások listáját, és válassza **Enter**.

    ![Válassza ki a megfelelő időzónát](./media/vmware-azure-install-linux-master-target/image4.png)

1. Válassza ki **nem** (az alapértelmezett beállítás), majd válassza ki **Enter**.

     ![A billentyűzet konfigurálása](./media/vmware-azure-install-linux-master-target/image5.png)
1. Válassza ki **angol (amerikai)** , billentyűzet, és válassza ki azt a származási **Enter**.

1. Válassza ki **angol (amerikai)** a billentyűzetkiosztás módosítása, és válassza ki, **Enter**.

1. Adja meg az állomásnevet a kiszolgáló a **állomásnév** mezőbe, majd válassza ki **Folytatás**.

1. Hozzon létre egy felhasználói fiókot, adja meg a felhasználónevet, és válassza **Folytatás**.

      ![Felhasználói fiók létrehozása](./media/vmware-azure-install-linux-master-target/image9.png)

1. Adja meg az új felhasználói fiók jelszavát, majd válassza ki **Folytatás**.

1.  Erősítse meg az új felhasználói jelszót, majd válassza ki **Folytatás**.

    ![A jelszó megerősítése](./media/vmware-azure-install-linux-master-target/image11.png)

1.  Válassza ki a következő kijelölés a kezdőkönyvtár titkosítására, **nem** (az alapértelmezett beállítás), majd válassza ki **Enter**.

1. Ha helyesek az időzónát, amely akkor jelenik meg, válassza ki a **Igen** (az alapértelmezett beállítás), majd válassza ki **Enter**. Válassza ki, hogy engedélyezzék az időzónát, **nem**.

1. A particionálási mód közül válassza ki a **interaktív - használja a teljes lemez**, majd válassza ki **Enter**.

     ![Válassza a particionálási mód lehetőséget](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Válassza ki a megfelelő lemezt a **partícióra válassza lemez** beállítások, és válassza **Enter**.

    ![Válassza ki a lemezt](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Válassza ki **Igen** írása a lemezre, majd válassza a módosítások **Enter**.

    ![Válassza ki az alapértelmezett beállítás](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  A konfigurálás proxy kiválasztása lapon jelölje be az alapértelmezett beállítás, válassza ki **Folytatás**, majd válassza ki **Enter**.
     
     ![Válassza ki a frissítések kezelése](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Válassza ki **automatikus frissítések** a telepítendő frissítések a rendszeren kezelése lehetőséget, majd válassza ki **Enter**.

     ![Válassza ki a frissítések kezelése](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Az Azure Site Recovery fő célkiszolgáló az Ubuntu olyan speciális verziója szükséges, mert annak érdekében, hogy a frissítések le vannak tiltva, a virtuális gép kernel szüksége. Ha engedélyezve vannak, a rendszeres megjelenjenek ezután a fő célkiszolgáló hibás működését. Mindenképpen jelölje ki a **automatikus frissítések** lehetőséget.

1.  Válassza ki az alapértelmezett beállításokat. Ha openSSH az SSH csatlakozni, jelölje be a **OpenSSH server** lehetőséget, majd válassza ki **Folytatás**.

    ![Válassza ki a szoftver](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. A LÁRVAJÁRAT rendszertöltő telepítésének selction, válassza ki **Igen**, majd válassza ki **Enter**.
     
    ![Rendszerindító telepítő LÁRVAJÁRAT](./media/vmware-azure-install-linux-master-target/image20.png)


1. Válassza ki a megfelelő eszköz, a rendszerindító betöltő telepítés (lehetőleg **/dev/sda**), majd válassza ki **Enter**.
     
    ![Válassza ki a megfelelő eszköz](./media/vmware-azure-install-linux-master-target/image21.png)

1. Válassza ki **Folytatás**, majd válassza ki **Enter** fejezze be a telepítést.

    ![Fejezze be a telepítést](./media/vmware-azure-install-linux-master-target/image22.png)

1. Miután a telepítés befejeződött, jelentkezzen be a virtuális Gépet az új felhasználói hitelesítő adatokkal. (Hivatkoznak **lépés 10** további információt.)

1. Az alábbi képernyőfelvételen a legfelső szintű felhasználói jelszó beállítása ismertetett lépésekkel. Legfelső szintű felhasználóként, majd jelentkezzen be.

    ![A legfelső szintű felhasználói jelszó beállítása](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>A fő célkiszolgálón a gép beállítása

Az azonosító lekérése minden SCSI merevlemez Linux virtuális gépre, a **lemez. EnableUUID = TRUE** paramétert meg kell engedélyezni. Ahhoz, hogy ez a paraméter, a következő lépéseket:

1. Állítsa le a virtuális gépet.

2. Kattintson a jobb gombbal a virtuális gép a bal oldali panelen a bejegyzést, és válassza ki **beállításainak szerkesztése**.

3. Válassza ki a **beállítások** fülre.

4. A bal oldali panelen válassza ki a **speciális** > **általános**, majd válassza ki a **konfigurációs paraméterek** gomb a képernyő jobb alsó részén.

    ![Nyissa meg a konfigurációs paraméter](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    A **konfigurációs paraméterek** beállítás nem érhető el, a gép futása közben. Ahhoz, hogy aktív ezen a lapon, a virtuális gép leállítása.

5. Tekintse meg, hogy a sor **lemez. EnableUUID** már létezik.

    - Ha az érték létezik-e, és értéke **hamis**, módosítsa a beállítást **igaz**. (A értékei nem kis-és nagybetűket.)

    - Ha az érték létezik-e, és értéke **igaz**, jelölje be **Mégse**.

    - Ha az érték nem létezik, válassza ki a **Hozzáadás sor**.

    - A Név oszlopban hozzáadása **lemez. EnableUUID**, majd állítsa be az értéket, és **igaz**.

    ![A rendszer ellenőrzi a e lemezen. EnableUUID már létezik.](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Tiltsa le a kernel frissítések

Az Azure Site Recovery fő célkiszolgáló az Ubuntu adott verzióját igényli, győződjön meg arról, hogy a rendszermag-frissítéseket a virtuális géphez van-e tiltva. Kernel frissítések engedélyezve vannak, a fő célkiszolgáló hibás működését okozhat.

#### <a name="download-and-install-additional-packages"></a>Töltse le és telepítse a további csomagokat

> [!NOTE]
> Győződjön meg arról, hogy rendelkezik-e internetkapcsolat, letöltése és telepítése további csomagokat. Ha nem rendelkezik internetkapcsolattal, szeretné manuálisan a RPM csomagok keresése és telepítése.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>A telepítő a telepítés beolvasása

Ha a fő célkiszolgáló rendelkezik internetkapcsolattal, a következő lépések segítségével töltse le a telepítő. Ellenkező esetben a folyamatkiszolgálót a telepítő másolja, majd telepítés.

#### <a name="download-the-master-target-installation-packages"></a>Töltse le a fő célkiszolgáló telepítőcsomagok

[Töltse le a legfrissebb Linux fő célkiszolgáló telepítése bits](https://aka.ms/latestlinuxmobsvc).

Töltse le Linux használ, írja be:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Győződjön meg arról, hogy töltse le és csomagolja ki a telepítő a kezdőkönyvtárban. Ha Ön csomagolja ki, hogy **/usr/helyi**, majd a telepítés sikertelen lesz.


#### <a name="access-the-installer-from-the-process-server"></a>A telepítő adatok a folyamatkiszolgálótól hozzáférés

1. A folyamatkiszolgáló Ugrás **C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

2. Másolja a szükséges telepítőfájl a folyamatkiszolgálót, és mentse a fájt **latestlinuxmobsvc.tar.gz** a kezdőkönyvtárban.


### <a name="apply-custom-configuration-changes"></a>Egyéni konfigurációs módosítások alkalmazása

Egyéni konfigurációs módosítások alkalmazásához, tegye a következőket:


1. A következő parancsot a bináris untar.

    `tar -zxvf latestlinuxmobsvc.tar.gz`

    ![Képernyőfelvétel a parancs futtatása](./media/vmware-azure-install-linux-master-target/image16.png)

2. A következő parancsot engedélyt.

    `chmod 755 ./ApplyCustomChanges.sh`


3. A következő parancsot a parancsfájl futtatásához.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Csak egyszer a parancsfájl futtatása a kiszolgálón. Ezután leállíthatja a kiszolgálót. Indítsa újra a kiszolgálót a lemez hozzáadása után a következő szakaszban leírtak szerint.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>A fő célkiszolgáló Linux virtuális gép egy adatmegőrzési lemez hozzáadása

Adatmegőrzési lemez létrehozásához tegye a következőket:

1. A Linux fő célkiszolgáló virtuális géphez csatolni egy új 1 TB méretű lemezt, és indítsa el a gépet.

2. Használja a **többutas -inden** parancs az adatmegőrzési lemez többutas azonosítója további: **többutas -inden**

    ![A többutas azonosítója](./media/vmware-azure-install-linux-master-target/image27.png)

3. Formázza a meghajtót, és hozza létre a fájlrendszer az új meghajtón: **mkfs.ext4 /dev/leképező/< adatmegőrzési lemez többutas azonosítója >**.
    
    ![Fájlrendszer](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. Miután létrehozta a fájlrendszer, csatlakoztassa az adatmegőrzési lemez.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Hozzon létre a **fstab** bejegyzés az adatmegőrzési meghajtó csatlakoztatása, a rendszer minden indításakor.
    
    `vi /etc/fstab`
    
    Válassza ki **beszúrása** a fájl szerkeszthető. Hozzon létre egy új sort, és helyezze be a következő szöveg. A kijelölt többutas az előző parancs azonosítója alapján többutas Lemezazonosítót szerkesztése.

    **/dev/eseményleképező/ <Retention disks multipath id> /mnt/megőrzési ext4 rw 0 0**

    Válassza ki **Esc**, majd írja be **: wq** (írása, és lépjen ki a) a szerkesztő ablak bezárásához.

### <a name="install-the-master-target"></a>A fő célkiszolgáló telepítése

> [!IMPORTANT]
> A fő célkiszolgáló verziója korábbi, mint a folyamatkiszolgáló és a konfigurációs kiszolgáló verziói vagy azzal egyenlőnek kell lennie. Ha ez a feltétel nem teljesül, a védelem-újrabeállítási sikeres, de a replikálás mindaddig sikertelen.


> [!NOTE]
> A fő célkiszolgáló telepítése előtt ellenőrizze, hogy a **/etc/hosts** fájl a virtuális gépen bejegyzéseit, amelyek a társított összes hálózati adapter IP-címek hozzárendelését a helyi állomásnevével tartalmazza.

1. Másolja a jelszót a **C:\ProgramData\Microsoft Azure hely Recovery\private\connection.passphrase** a konfigurációs kiszolgálón. Majd mentse a fájt **passphrase.txt** ugyanabban a helyi könyvtárban a következő parancs futtatásával:

    `echo <passphrase> >passphrase.txt`

    Példa: 

       `echo itUx70I47uxDuUVY >passphrase.txt`
    

2. Jegyezze fel a konfigurációs kiszolgáló IP-címet. A következő parancsot a fő célkiszolgáló telepítése, és regisztrálja a kiszolgálót a konfigurációs kiszolgáló.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Példa: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Várjon, amíg a parancsfájl befejeződik. Ha a fő célkiszolgáló sikeresen regisztrálja, a fő célkiszolgáló megtalálható a **Site Recovery-infrastruktúra** a portál lapján.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Interaktív telepítés használatával a fő célkiszolgáló telepítése

1. A következő parancsot a fő célkiszolgáló telepítése. Válassza ki az ügynök szerepkör **fő**.

    ```
    ./install
    ```

2. Válassza ki az alapértelmezett helyet a telepítéshez, majd válassza ki **Enter** folytatja.

    ![Fő célkiszolgáló telepítése az alapértelmezett hely kiválasztása](./media/vmware-azure-install-linux-master-target/image17.png)

Miután a telepítés befejeződött, a konfigurációs kiszolgáló regisztrálása a parancssor használatával.

1. Megjegyzés: a konfigurációs kiszolgáló IP-címét. A következő lépésben van szükség.

2. A következő parancsot a fő célkiszolgáló telepítése, és regisztrálja a kiszolgálót a konfigurációs kiszolgáló.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    Példa: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

     Várjon, amíg a parancsfájl befejeződik. Ha a fő célkiszolgáló sikeresen regisztrálva van, a fő célkiszolgáló megtalálható a **Site Recovery-infrastruktúra** a portál lapján.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>A VMware-eszközök telepítése / nyílt-virtuálisgép-eszközök a fő célkiszolgálón

Szeretne telepíteni a VMware-eszközök vagy nyílt-virtuálisgép-eszközök a fő célkiszolgáló, hogy az képes felderíteni az adattároló. Ha az eszközök nincsenek telepítve, a védelem-újrabeállítási képernyőn nem szerepel az adattároló. A VMware-eszközök telepítése, után újra kell indítania.

### <a name="upgrade-the-master-target-server"></a>A fő célkiszolgáló frissítése

Futtassa a telepítőt. Automatikusan észleli, hogy az ügynök telepítve van-e a fő célkiszolgáló. Frissítéséhez válassza **Y**.  A telepítés befejezése után, a fő célkiszolgálón a következő parancs használatával telepített verziójának ellenőrzése:

`cat /usr/local/.vx_version`


Látni fogja, hogy a **verzió** mező biztosít a fő célkiszolgáló verziószámát.

## <a name="common-issues"></a>Gyakori problémák

* Győződjön meg arról, hogy ne kapcsolja be az összes felügyeleti összetevőkön, például az olyan fő célkiszolgálót, a Storage vmotion szolgáltatások használata. Ha a fő célkiszolgáló sikeres védelem-újrabeállítási után helyezi át, nem választható le a virtuális gép lemezeinek (VMDKs). Ebben az esetben a feladat-visszavétel sikertelen lesz.

* A fő célkiszolgáló nem kell az esetlegesen pillanatképek a virtuális gépen. Ha a pillanatképek vannak, a feladat-visszavétel sikertelen lesz.

* Bizonyos egyéni hálózati konfigurációk, mert a hálózati adapter le van tiltva, indításakor, és a fő célkiszolgáló ügynöke nem tudja inicializálni. Győződjön meg arról, hogy helyesen vannak-e beállítva a következő tulajdonságokkal. Ellenőrizze, hogy ezeket a tulajdonságokat a Ethernet a kártya fájl /etc/sysconfig/network-scripts/ifcfg-eth *.
    * BOOTPROTO = dhcp
    * ONBOOT = Igen


## <a name="next-steps"></a>További lépések
Miután befejezte a telepítési és a fő célkiszolgáló regisztrálását, megjelenik a fő célkiszolgáló jelennek meg a **fő** szakasz **Site Recovery-infrastruktúra**, a konfigurációja Server áttekintése.

Most már folytathatja az [ismételt védelem](vmware-azure-reprotect.md), feladat-visszavétel követ.

