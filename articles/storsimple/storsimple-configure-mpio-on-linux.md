---
title: Konfigurálása MPIO storSimple Linux host
description: Konfigurálja az MPIO-t a CentOS 6.6-ot futtató Linux-állomáshoz csatlakoztatott StorSimple rendszeren
author: alkohli
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alkohli
ms.openlocfilehash: 5dadd231335e93839e947077168f32dbfe96eb45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278362"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>Az MPIO konfigurálása CentOS-t futtató StorSimple-állomáson
Ez a cikk a Többutas IO (MPIO) Centos 6.6 gazdakiszolgálón történő konfigurálásához szükséges lépéseket ismerteti. A gazdakiszolgáló csatlakozik a Microsoft Azure StorSimple eszközhöz az iSCSI-kezdeményezőin keresztüli magas rendelkezésre állás érdekében. Részletesen ismerteti a többutas eszközök automatikus felderítését, és csak a StorSimple kötetek konkrét beállítását.

Ez az eljárás a StorSimple 8000 sorozatú eszközök összes modelljére vonatkozik.

> [!NOTE]
> Ez az eljárás nem használható StorSimple felhőalapú berendezéshez. További információ: hogyan konfigurálhatja a gazdakiszolgálókat a felhőalapú készülékhez.


## <a name="about-multipathing"></a>A többutas útról
A többutas szolgáltatás lehetővé teszi több I/O-útvonal konfigurálását egy gazdakiszolgáló és egy tárolóeszköz között. Ezek az I/O-útvonalak olyan fizikai SAN-kapcsolatok, amelyek különálló kábeleket, kapcsolókat, hálózati adaptereket és vezérlőket tartalmazhatnak. A többutas összesítés az I/O-útvonalakat összesíti, így az összes összesített elérési úthoz társított új eszköz konfigurálásához.

A többutas út kétirányú:

* **Magas rendelkezésre állás**: Alternatív útvonalat biztosít, ha az I/O elérési út bármely eleme (például kábel, kapcsoló, hálózati adapter vagy vezérlő) meghibásodik.
* **Terheléselosztás:** A tárolóeszköz konfigurációjától függően javíthatja a teljesítményt azáltal, hogy észleli a terheléseket az I/O-útvonalakon, és dinamikusan kiegyensúlyozza ezeket a terheléseket.

### <a name="about-multipathing-components"></a>Többutas összetevők –
A Linux ban való többutas alkalmazás kernel összetevőkből és az alábbi táblázatos felhasználói tér-összetevőkből áll.

* **Kernel**: A fő összetevő az *eszköz-leképező,* amely átirányítja az I/O és támogatja a feladatátvétel az elérési utak és az elérési út csoportok.

* **Felhasználói tér:** Ezek *többutas eszközök,* amelyek többutas eszközöket kezelnek az eszközleképező többutas modul utasításával, hogy mi a teendő. Az eszközök a következőkből állnak:
   
   * **Többutas**: többutas eszközöket sorol és konfigurál.
   * **Többutas:** démon, amely többutas tanévenként hajtja végre, és figyeli az elérési utakat.
   * **Devmap-name:** értelmes eszköznevet biztosít az udev számára a devmaps számára.
   * **Kpartx**: a lineáris fejlesztői térképeket eszközpartíciókhoz rendeli hozzá, hogy a többutas leképezések particionálhatók legyenek.
   * **Multipath.conf**: konfigurációs fájl többutas démonhoz, amely a beépített konfigurációs tábla felülírására szolgál.

### <a name="about-the-multipathconf-configuration-file"></a>A multipath.conf konfigurációs fájl
A konfigurációs fájl `/etc/multipath.conf` számos többutas szolgáltatást felhasználó által konfigurálhatóvá tesz. A `multipath` parancs és a kerneldémon a fájlban található információkat `multipathd` használja. A fájl csak a többutas eszközök konfigurálása során tekinthető meg. A parancs futtatása előtt győződjön `multipath` meg arról, hogy minden módosítás megtörtént. Ha ezt követően módosítja a fájlt, le kell állítania, és újra el kell indítania a többutas utat ahhoz, hogy a módosítások érvénybe lépjenek.

A multipath.conf öt szakaszból álló:

- **Rendszerszintű alapértelmezések** *(alapértelmezett értékek):* Felülírhatja a rendszerszintű alapértelmezéseket.
- **Feketelistára helyezett eszközök** *(feketelista)*: Megadhatja azoknak az eszközöknek a listáját, amelyeket nem szabad eszközleképezővel vezérelni.
- **Feketelista kivételek** *(blacklist_exceptions)*: Azonosíthatja azokat az eszközöket, amelyektöbbutas eszközként kezelendők, még akkor is, ha szerepel nek a feketelistában.
- **A tárolóvezérlőre vonatkozó beállítások** *(eszközök)*: Megadhatja azokat a konfigurációs beállításokat, amelyek a Szállító és a Termék adatokkal tartalmazó eszközökre lesznek alkalmazva.
- **Eszközspecifikus beállítások** *(többutas)*: Ezzel a paranchevel finomíthatja az egyes lun-ok konfigurációs beállításait.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Többutas üzemeltetés konfigurálása Linux-állomáshoz csatlakoztatott StorSimple-en
A Linux-állomáshoz csatlakoztatott StorSimple-eszköz konfigurálható a magas rendelkezésre állás és a terheléselosztás érdekében. Ha például a Linux-állomás két kapcsolattal rendelkezik a SAN-hoz, és az eszköz két, a SAN-hoz csatlakoztatott felülettel rendelkezik, így ezek az összeköttetések ugyanazon az alhálózaton vannak, akkor 4 elérési út lesz elérhető. Ha azonban az eszköz és az állomáskapcsolat minden adatilleszte egy másik IP-alhálózaton található (és nem irányítható), akkor csak 2 elérési út lesz elérhető. Beállíthatja a többutas utat úgy, hogy automatikusan felderítse az összes elérhető elérési utat, válasszon egy terheléselosztási algoritmust ezekhez az elérési utakhoz, alkalmazzon konkrét konfigurációs beállításokat a csak StorSimple kötetekre, majd engedélyezze és ellenőrizze a többutas verziót.

Az alábbi eljárás azt ismerteti, hogyan konfigurálható a többutas, ha egy két hálózati csatolmányral rendelkező StorSimple-eszköz két hálózati csatolmányral rendelkező állomáshoz csatlakozik.

## <a name="prerequisites"></a>Előfeltételek
Ez a szakasz részletezi a CentOS-kiszolgáló és a StorSimple-eszköz konfigurációs előfeltételeit.

### <a name="on-centos-host"></a>A CentOS gazdagépen
1. Győződjön meg arról, hogy a CentOS-állomás 2 hálózati csatolója van engedélyezve. Típus:
   
    `ifconfig`
   
    A következő példa a kimenetet mutatja`eth0` `eth1`be, ha két hálózati adapter ( és ) van jelen az állomáson.
   
        [root@centosSS ~]# ifconfig
        eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
          inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
          inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
          inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
         RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
          TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)
   
        eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
          inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
          inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
          inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
          TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)
   
        loLink encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:12 errors:0 dropped:0 overruns:0 frame:0
          TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)
1. Telepítse *az iSCSI-initiator-utils-t* a CentOS kiszolgálóra. Az *iSCSI-initiator-utils*telepítéséhez hajtsa végre az alábbi lépéseket.
   
   1. Jelentkezzen be `root` a CentOS-állomásra.
   1. Telepítse az *iSCSI-initiator-utils -t.* Típus:
      
       `yum install iscsi-initiator-utils`
   1. Az *iSCSI-Initiator-utils* sikeres telepítése után indítsa el az iSCSI szolgáltatást. Típus:
      
       `service iscsid start`
      
       Időnként előfordulhat, `iscsid` hogy nem indul `--force` el, és szükség lehet erre a lehetőségre
   1. Annak érdekében, hogy az iSCSI-kezdeményező engedélyezve `chkconfig` legyen a rendszerindítás idikált ideje alatt, használja a parancsot a szolgáltatás engedélyezéséhez.
      
       `chkconfig iscsi on`
   1. Annak ellenőrzéséhez, hogy megfelelően volt-e beállítva, futtassa a következő parancsot:
      
       `chkconfig --list | grep iscsi`
      
       Az alábbiakban egy példa látható a kimenetre.
      
           iscsi   0:off   1:off   2:on3:on4:on5:on6:off
           iscsid  0:off   1:off   2:on3:on4:on5:on6:off
      
       A fenti példában láthatja, hogy az iSCSI-környezet a 2., 3., 4.
1. Telepítse *az eszköz-mapper-multipath*. Típus:
   
    `yum install device-mapper-multipath`
   
    A telepítés elindul. Írja be az **Y** típust, amikor megerősítést kér.

### <a name="on-storsimple-device"></a>StorSimple eszközön
A StorSimple eszköznek rendelkeznie kell:

* Legalább két, az iSCSI-hez engedélyezett csatoló. Annak ellenőrzéséhez, hogy két felület iSCSI-kompatibilis-e a StorSimple-eszközön, hajtsa végre a következő lépéseket a Klasszikus Azure-portálon a StorSimple-eszközhöz:
  
  1. Jelentkezzen be a StorSimple-eszköz klasszikus portáljára.
  1. Válassza ki a StorSimple Manager szolgáltatást, kattintson **az Eszközök** elemre, és válassza ki az adott StorSimple-eszközt. Kattintson a **Konfigurálás gombra,** és ellenőrizze a hálózati adapter beállításait. Az alábbiakban egy két iSCSI-kompatibilis hálózati csatolóval ellátott képernyőkép látható. Itt a DATA 2 és a DATA 3, mindkét 10 GbE felület engedélyezve van az iSCSI számára.
     
      ![MPIO StorsSimple DATA 2 konfiguráció](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![MPIO StorSimple DATA 3 Konfiguráció](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      A **Konfigurálás** lapon
     
     1. Győződjön meg arról, hogy mindkét hálózati csatoló iSCSI-kompatibilis. Az **iSCSI-kompatibilis** mezőt **Igen**értékre kell állítani.
     1. Győződjön meg arról, hogy a hálózati adapterek azonos sebességgel, mindkettő nek 1 GbE vagy 10 GbE.
     1. Jegyezze fel az iSCSI-kompatibilis összeköttetések IPv4-címét, és mentse későbbi használatra az állomáson.
* A StorSimple eszköz iSCSI-felületei elérhetők a CentOS-kiszolgálóról.
      Ennek ellenőrzéséhez meg kell adnia a StorSimple iSCSI-kompatibilis hálózati adapterek IP-címét a gazdakiszolgálón. Az alkalmazott parancsok és a megfelelő kimenet a DATA2 (10.126.162.25) és a DATA3 (10.126.162.26) értékkel az alábbiak:
  
        [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
        10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
        10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target

### <a name="hardware-configuration"></a>Hardverkonfiguráció
Javasoljuk, hogy a két iSCSI-hálózati adaptert külön elérési utakra csatlakoztassa a redundancia érdekében. Az alábbi ábra a CentOS-kiszolgáló és a StorSimple-eszköz magas rendelkezésre állásához és terheléselosztási többutas kezeléséhez ajánlott hardverkonfigurációt mutatja be.

![MPIO hardverkonfiguráció a StorSimple–Linux állomáshoz](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Az előző ábrán látható módon:

* A StorSimple-eszköz aktív-passzív konfigurációban van, két vezérlővel.
* Két SAN kapcsoló csatlakozik az eszköz vezérlők.
* Két iSCSI-kezdeményező engedélyezve van a StorSimple eszközön.
* A CentOS-állomáson két hálózati kapcsolat van engedélyezve.

A fenti konfiguráció 4 különálló elérési utat eredményez az eszköz és az állomás között, ha az állomás- és adatfelületek irányíthatóak.

> [!IMPORTANT]
> * Azt javasoljuk, hogy ne keverjen 1 GbE és 10 GbE hálózati adaptert a többutas küldéshez. Két hálózati csatoló használata esetén mindkét kapcsolatnak azonos típusúnak kell lennie.
> * A StorSimple eszközön a DATA0, A DATA1, a DATA4 és a DATA5 1 GbE-felület, míg a DATA2 és a DATA3 10 GbE hálózati interfész.|
> 
> 

## <a name="configuration-steps"></a>Konfigurációs lépések
A többutas küldés konfigurációs lépései közé tartozik az automatikus felderítéshez rendelkezésre álló elérési utak konfigurálása, a használandó terheléselosztási algoritmus megadása, a többutas küldés engedélyezése és a konfiguráció végleges ellenőrzése. A lépések mindegyikét részletesen tárgyaljuk a következő szakaszokban.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>1. lépés: Többutas küldés konfigurálása az automatikus felderítéshez
A többutas támogatott eszközök automatikusan felderíthetők és konfigurálhatók.

1. Fájl `/etc/multipath.conf` inicializálása. Típus:
   
     `mpathconf --enable`
   
    A fenti parancs `sample/etc/multipath.conf` létrehoz egy fájlt.
1. Indítsa el a többutas szolgáltatást. Típus:
   
    `service multipathd start`
   
    A következő kimenet jelenik meg:
   
    `Starting multipathd daemon:`
1. A többutas utak automatikus felderítésének engedélyezése. Típus:
   
    `mpathconf --find_multipaths y`
   
    Ez módosítja az alapértelmezett `multipath.conf` szakasza az alábbiak szerint:
   
        defaults {
        find_multipaths yes
        user_friendly_names yes
        path_grouping_policy multibus
        }

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>2. lépés: Többutas küldés konfigurálása StorSimple kötetekhez
Alapértelmezés szerint az összes eszköz fekete listára kerül a multipath.conf fájlban, és megkerülendő. Feketelista-kivételeket kell létrehoznia a StorSimple-eszközök köteteinek többutas lehetővé teszi.

1. A fájl `/etc/mulitpath.conf` szerkesztése. Típus:
   
    `vi /etc/multipath.conf`
1. Keresse meg a blacklist_exceptions szakaszt a multipath.conf fájlban. A StorSimple-eszköz kell szerepelnie feketelista kivételként ebben a szakaszban. A fájl megfelelő sorainak megjegyzését az alábbi módon módosíthatja (csak a használt eszköz adott modelljét használja):
   
        blacklist_exceptions {
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8100*"
            }
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8600*"
            }
           }

### <a name="step-3-configure-round-robin-multipathing"></a>3. lépés: Ciklikus multiplexelés többutas beállítása
Ez a terheléselosztási algoritmus az aktív vezérlő höz rendelkezésre álló összes többutat kiegyensúlyozott, ciklikus multiplexelési módon használja.

1. A fájl `/etc/multipath.conf` szerkesztése. Típus:
   
    `vi /etc/multipath.conf`
1. A `defaults` szakasz alatt `path_grouping_policy` állítsa `multibus`a beállítását a . A `path_grouping_policy` megadott alapértelmezett elérési útcsoportosítási házirend, amelyet a meghatározatlan többutas utakra kell alkalmazni. Az alapértelmezett szakaszok az alábbiak szerint jelennek meg.
   
        defaults {
                user_friendly_names yes
                path_grouping_policy multibus
        }

> [!NOTE]
> A leggyakoribb értékek `path_grouping_policy` a következők:
> 
> * feladatátvétel = 1 elérési út prioritási csoportonként
> * multibus = az összes érvényes elérési út 1 prioritási csoportban
> 
> 

### <a name="step-4-enable-multipathing"></a>4. lépés: Többutas út engedélyezése
1. Indítsa `multipathd` újra a démont. Típus:
   
    `service multipathd restart`
1. A kimenet az alábbiak szerint történik:
   
        [root@centosSS ~]# service multipathd start
        Starting multipathd daemon:  [OK]

### <a name="step-5-verify-multipathing"></a>5. lépés: Többutas út ellenőrzése
1. Először győződjön meg arról, hogy az iSCSI-kapcsolat létrejött a StorSimple eszközzel az alábbiak szerint:
   
   a. Fedezze fel a StorSimple eszközt. Típus:
      
    ```
    iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>
    ```
    
    Ha a DATA0 IP-címe 10.126.162.25, a 3260-as port pedig a StorSimple eszközön nyílik meg a kimenő iSCSI-forgalomszámára, az alábbi módon érhető el:
    
    ```
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Másolja a StorSimple eszköz IQN-jét `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`az előző kimenetről.

   b. Csatlakozzon az eszközhöz a cél IQN használatával. A StorSimple eszköz itt az iSCSI-tároló. Típus:

    ```
    iscsiadm -m node --login -T <IQN of iSCSI target>
    ```

    A következő példa a kimenetet `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`mutatja be a cél IQN értékével. A kimenet azt jelzi, hogy sikeresen csatlakozott az eszköz két iSCSI-kompatibilis hálózati illesztőjéhez.

    ```
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    ```

    Ha itt csak egy állomásfelület et és két elérési utat lát, akkor engedélyeznie kell az iSCSI gazdagépen lévő mindkét összeköttetést. Tudod követ a [részletes oktatás -ban Linux dokumentáció](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

1. Egy kötet a StorSimple-eszközről van kitéve a CentOS-kiszolgálónak. További információ: [6. lépés: Kötet létrehozása](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) az Azure Portalon keresztül a StorSimple-eszközön.

1. Ellenőrizze a rendelkezésre álló elérési utakat. Típus:

      ```
      multipath -l
      ```

      A következő példa két hálózati adapter kimenetét mutatja be egy storSimple-eszközön, amely két elérhető elérési úttal rendelkező egyetlen gazdahálózati adapterhez csatlakozik.

        ```
        mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
        size=100G features='0' hwhandler='0' wp=rw
        `-+- policy='round-robin 0' prio=0 status=active
        |- 7:0:0:1 sdc 8:32 active undef running
        `- 6:0:0:1 sdd 8:48 active undef running
        ```

        The following example shows the output for two network interfaces on a StorSimple device connected to two host network interfaces with four available paths.

        ```
        mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
        size=100G features='0' hwhandler='0' wp=rw
        `-+- policy='round-robin 0' prio=0 status=active
        |- 17:0:0:0 sdb 8:16 active undef running
        |- 15:0:0:0 sdd 8:48 active undef running
        |- 14:0:0:0 sdc 8:32 active undef running
        `- 16:0:0:0 sde 8:64 active undef running
        ```

        After the paths are configured, refer to the specific instructions on your host operating system (Centos 6.6) to mount and format this volume.

## <a name="troubleshoot-multipathing"></a>Többutas megoldás – problémamegoldás
Ez a szakasz néhány hasznos tippet tartalmaz, ha a többutas konfiguráció során bármilyen problémába ütközik.

K. Nem látom, hogy `multipath.conf` a módosítások életbe lépnek.

A. Ha módosította a `multipath.conf` fájlt, újra kell indítania a többutas szolgáltatást. Írja be a következő parancsot:

    service multipathd restart

K. Két hálózati interfészt engedélyeztem a StorSimple eszközön és két hálózati interfészt a gazdagépen. Amikor felsorolom a rendelkezésre álló elérési utakat, csak két útvonal jelenik meg. Négy elérhető utat vártam.

A. Győződjön meg arról, hogy a két útvonal ugyanazon az alhálózaton és irányítható. Ha a hálózati adapterek különböző vLA-kon vannak, és nem irányíthatóak, csak két elérési utat fog látni. Ennek ellenőrzésének egyik módja annak ellenőrzése, hogy a StorSimple-eszköz hálózati felületéről mindkét gazdafelület elérhető-e. Kapcsolatba kell [lépnie a Microsoft támogatási szolgálatával,](storsimple-8000-contact-microsoft-support.md) mivel ez az ellenőrzés csak támogatási munkameneten keresztül végezhető el.

K. Amikor felsorolom az elérhető elérési utakat, nem jelenik meg kimenet.

A. Általában nem lát többutas elérési utak azt sugallja, hogy a probléma a többutas démon, és ez a `multipath.conf` legvalószínűbb, hogy minden probléma itt rejlik a fájlban.

Azt is érdemes ellenőrizni, hogy valóban látni néhány lemezt csatlakozás után a cél, mivel nincs válasz a többutas listát is azt is jelentheti, hogy nincs lemez.

* Az SCSI-busz átvizsgálatához használja a következő parancsot:
  
    `$ rescan-scsi-bus.sh`(sg3_utils csomag része)
* Írja be a következő parancsokat:
  
    `$ dmesg | grep sd*`
     
     Vagy
  
    `$ fdisk -l`
  
    Ezek a legutóbb hozzáadott lemezek részleteit adják vissza.
* Annak megállapításához, hogy StorSimple lemezről van-e szó, használja a következő parancsokat:
  
    `cat /sys/block/<DISK>/device/model`
  
    Ez egy karakterláncot ad vissza, amely meghatározza, hogy storSimple lemezről van-e szó.

Egy kevésbé valószínű, de lehetséges ok a skanikai iscsid pid is lehet. Az iSCSI-munkamenetekből a következő paranccsal jelentkezhet ki:

    iscsiadm -m node --logout -p <Target_IP>

Ismételje meg ezt a parancsot az iSCSI-tároló összes csatlakoztatott hálózati adapterén, amely a StorSimple-eszköz. Miután kijelentkezett az összes iSCSI-munkamenetből, az iSCSI-tároló IQN-jával állítsa létre újra az iSCSI-munkamenetet. Írja be a következő parancsot:

    iscsiadm -m node --login -T <TARGET_IQN>


K. Nem vagyok biztos benne, hogy az eszközem szerepel-e a listán.

A. Annak ellenőrzéséhez, hogy az eszköz szerepel-e az engedélyezési listán, használja a következő hibaelhárítási interaktív parancsot:

    multipathd -k
    multipathd> show devices
    available block devices:
    ram0 devnode blacklisted, unmonitored
    ram1 devnode blacklisted, unmonitored
    ram2 devnode blacklisted, unmonitored
    ram3 devnode blacklisted, unmonitored
    ram4 devnode blacklisted, unmonitored
    ram5 devnode blacklisted, unmonitored
    ram6 devnode blacklisted, unmonitored
    ram7 devnode blacklisted, unmonitored
    ram8 devnode blacklisted, unmonitored
    ram9 devnode blacklisted, unmonitored
    ram10 devnode blacklisted, unmonitored
    ram11 devnode blacklisted, unmonitored
    ram12 devnode blacklisted, unmonitored
    ram13 devnode blacklisted, unmonitored
    ram14 devnode blacklisted, unmonitored
    ram15 devnode blacklisted, unmonitored
    loop0 devnode blacklisted, unmonitored
    loop1 devnode blacklisted, unmonitored
    loop2 devnode blacklisted, unmonitored
    loop3 devnode blacklisted, unmonitored
    loop4 devnode blacklisted, unmonitored
    loop5 devnode blacklisted, unmonitored
    loop6 devnode blacklisted, unmonitored
    loop7 devnode blacklisted, unmonitored
    sr0 devnode blacklisted, unmonitored
    sda devnode whitelisted, monitored
    dm-0 devnode blacklisted, unmonitored
    dm-1 devnode blacklisted, unmonitored
    dm-2 devnode blacklisted, unmonitored
    sdb devnode whitelisted, monitored
    sdc devnode whitelisted, monitored
    dm-3 devnode blacklisted, unmonitored


További információt a [többutas megoldásokkal kapcsolatos hibaelhárítás című témakörben](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/mpio_admin-troubleshoot)talál.

## <a name="list-of-useful-commands"></a>Hasznos parancsok listája
| Típus | Parancs | Leírás |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |ISCSI-szolgáltatás indítása |
| &nbsp; |`service iscsid stop` |Az iSCSI-szolgáltatás leállítása |
| &nbsp; |`service iscsid restart` |Az iSCSI-szolgáltatás újraindítása |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |A megadott címen elérhető célok felderítése |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Bejelentkezés az iSCSI-tárolóba |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Kijelentkezés az iSCSI-tárolóból |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Az iSCSI-kezdeményező nevének nyomtatása |
| &nbsp; |`iscsiadm -m session -s <sessionid> -P 3` |Az állomáson felderített iSCSI-munkamenet és kötet állapotának ellenőrzése |
| &nbsp; |`iscsi -m session` |A gazdagép és a StorSimple eszköz között létrehozott összes iSCSI-munkamenet megjelenítése |
|  | | |
| **Többutas** |`service multipathd start` |Többutas démon indítása |
| &nbsp; |`service multipathd stop` |Többutas démon leállítása |
| &nbsp; |`service multipathd restart` |Többutas démon újraindítása |
| &nbsp; |`chkconfig multipathd on` </br> VAGY </br> `mpathconf -with_chkconfig y` |Többutas démon indításának engedélyezése indításkor |
| &nbsp; |`multipathd -k` |Az interaktív konzol indítása hibaelhárításhoz |
| &nbsp; |`multipath -l` |Többutas kapcsolatok és eszközök listázása |
| &nbsp; |`mpathconf --enable` |Minta mulitpath.conf fájl létrehozása a`/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>További lépések
Az MPIO Linux-gazdagépen történő konfigurálásakor előfordulhat, hogy a következő CentoS 6.6 dokumentumokra is hivatkoznia kell:

* [Az MPIO beállítása a CentOS rendszeren](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/index)
* [Linux képzési útmutató](http://linux-training.be/linuxsys.pdf)

