---
title: Az MPIO konfigurálása a StorSimple Linux gazdagépen |} Microsoft Docs
description: A CentOS 6.6 futó Linux-állomáshoz csatlakoztatott StorSimple az MPIO konfigurálása
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: tysonn
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 2fbae15c1c6a9ec886f57f9df903612ae10d8e12
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
ms.locfileid: "27779091"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>Az MPIO konfigurálása a StorSimple gazdagépen fut a CentOS
Ez a cikk ismerteti a Centos 6.6 gazdakiszolgáló többutas I/O (MPIO) konfigurálásához szükséges lépéseket. A kiszolgáló csatlakozik-e a Microsoft Azure StorSimple eszközt a magas rendelkezésre álláshoz az iSCSI-kezdeményezők keresztül. Ismerteti részletesen a automatikus felderítés többutas eszközről és beállításai csak a StorSimple-köteteket.

Ez az eljárás akkor alkalmazható a StorSimple 8000 sorozat eszközeire összes modellt.

> [!NOTE]
> Ez az eljárás nem használható a StorSimple felhő alapplatformjaként. További információkért tekintse meg a felhő alapplatformjaként kiszolgálók konfigurálása.


## <a name="about-multipathing"></a>Többutas kapcsolatos
A többutas funkció lehetővé teszi a kiszolgáló és a tárolóeszköz közötti több i/o-útvonal konfigurálásához. Az i/o-elérési utak, amelyek külön kábelek, a kapcsolók, a hálózati adapterek és a tartományvezérlők fizikai SAN-kapcsolatokat. Többutas i/o elérési utak társított összes összesített görbét új eszköz beállítása összesíti.

A többutas célja kettős:

* **Magas rendelkezésre állású**: Ha az i/o-útvonal (például egy kábel, kapcsoló, hálózati adapter vagy tartományvezérlő) bármely elem nem egy alternatív útvonalat biztosít.
* **Terheléselosztás**: attól függően, hogy a tárolóeszköz konfigurációját, akkor a jobb teljesítmény érdekében terhelések az i/o-elérési utakat a észlelésére, és ezek terhelések dinamikus újraelosztás.

### <a name="about-multipathing-components"></a>Többutas összetevőivel kapcsolatos
A Linux többutas áll kernel és felhasználói térben összetevői, az alábbi táblázatban látható.

* **Kernel**: A fő összetevője a *eszköz-leképező* , amely reroutes i/o és elérési utak és elérési útja csoportok támogatja a feladatátvételt.

* **Felhasználói térben**: ezek a *Többutas-eszközök* , amelyek felügyelhetők a multipathed eszközök utasítja az eszköz-többutas leképezőmodulja Mi a teendő. Az eszközök foglalják magukban:
   
   * **Többutas**: sorolja fel, és beállítja az multipathed eszközöket.
   * **Multipathd**: démon, amely végrehajtja a többutas, és az elérési utak figyeli.
   * **Devmap-name**: egy jelentéssel bíró eszköznév udevre tartó biztosít devmaps.
   * **Kpartx**: lineáris devmaps van leképezve eszköz partíciók többutas maps particionálható végrehajtásához.
   * **MultiPath.conf**: felülírja a beépített configuration tábla használt többutas démon a konfigurációs fájlban.

### <a name="about-the-multipathconf-configuration-file"></a>A multipath.conf konfigurációs fájl
A konfigurációs fájl `/etc/multipath.conf` lehetővé teszi a többutas szolgáltatások számos felhasználó által konfigurálható. A `multipath` parancs és a kernel démon `multipathd` használja a fájlban található információkat. A fájl a van csak a Többutas eszközök konfigurálása során megkeresett. Győződjön meg arról, hogy minden módosításai futtatása előtt a `multipath` parancsot. Ha ezt követően módosíthatja a fájlt, szüksége lesz leállítására és elindítására a módosítások életbe léptetéséhez újra az multipathd.

A multipath.conf öt részből áll:

- **Rendszer alapbeállításainak** *(alapértelmezett)*: rendszer alapbeállításainak felülbírálható.
- **Eszközök feketelistára teszi** *(blacklist)*: megadhatja, hogy az eszközök, eszköz-leképező nem kell vezérelnie listája.
- **Kivételek tiltólistára** *(blacklist_exceptions)*: azonosíthatja, konkrét eszközökhöz többutas eszközként kell kezelni, még akkor is, ha a blacklist szerepel.
- **Tárolási vezérlő adott beállítások** *(eszközök)*: olyan eszközökre, amelyeken a szállító és a termékkulcsot alkalmazandó beállításokat is megadhat.
- **Adott eszközbeállítások** *(multipaths)*: Ez a szakasz segítségével finomhangolásához különálló logikaiegység-számok konfigurációs beállításait.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Linux-állomáshoz csatlakoztatott StorSimple többutas konfigurálása
A Linux-állomáshoz csatlakoztatott StorSimple eszköz konfigurálhatja a magas rendelkezésre állás és a terheléselosztás. Például ha a Linux rendszerű állomás felületei két San hálózathoz csatlakoznak, és az eszköz a San hálózathoz csatlakoznak, úgy, hogy ugyanazon az alhálózaton legyenek konfigurációkezelővel két csatolót, majd lesz 4 útvonal érhető el. Azonban ha az eszköz és a gazdagép felületen található adatok csatolóhoz a különböző IP-alhálózat (és nem irányítható), majd csak 2 elérési utak lesz elérhető. Konfigurálhatja a többutas automatikusan elérhető elérési utakban felderítése, válassza ki az adott elérési útján a terheléselosztási algoritmust, csak a StorSimple-köteteket, megadott konfigurációs beállítások alkalmazása engedélyezése és többutas ellenőrzése.

Az alábbi eljárás ismerteti, hogyan többutas konfigurálásához, amikor két hálózati adapterrel rendelkező StorSimple eszköz csatlakozik-e olyan gazdagépet, két hálózati adapterrel rendelkezik.

## <a name="prerequisites"></a>Előfeltételek
Ez a szakasz részletesen a CentOS kiszolgáló és a StorSimple eszköz konfigurációs előfeltételeit.

### <a name="on-centos-host"></a>A CentOS gazdagépen
1. Győződjön meg arról, hogy a CentOS gazdagépen engedélyezett 2 hálózati illesztőt. Típus:
   
    `ifconfig`
   
    A következő példa bemutatja a kimenet, amikor két hálózati illesztőt (`eth0` és `eth1`) szerepelnek a gazdagépen.
   
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
2. Telepítés *iSCSI-kezdeményező-utils* a CentOS kiszolgálón. A következő lépésekkel telepítse *iSCSI-kezdeményező-utils*.
   
   1. Jelentkezzen be `root` a CentOS gazdagép be.
   2. Telepítse a *iSCSI-kezdeményező-utils*. Típus:
      
       `yum install iscsi-initiator-utils`
   3. Után a *iSCSI-kezdeményező-utils* sikeresen van telepítve, indítsa el az iSCSI szolgáltatást. Típus:
      
       `service iscsid start`
      
       Alkalommal `iscsid` esetleg nem indul el, és a `--force` beállítás szükséges
   4. Győződjön meg arról, hogy az iSCSI-kezdeményező rendszerindítás ideje alatt engedélyezve van, használja a `chkconfig` parancs használatával engedélyezze a szolgáltatást.
      
       `chkconfig iscsi on`
   5. Győződjön meg arról, hogy a rendszer megfelelően beállítani, futtassa a parancsot:
      
       `chkconfig --list | grep iscsi`
      
       Az alábbiakban egy példa látható a kimenetre.
      
           iscsi   0:off   1:off   2:on3:on4:on5:on6:off
           iscsid  0:off   1:off   2:on3:on4:on5:on6:off
      
       A fenti példában láthatja, hogy az iSCSI-környezetben működnek rendszerindítás futtatási szinten, 2, 3, 4 és 5.
3. Telepítés *eszköz-leképező-többutas*. Típus:
   
    `yum install device-mapper-multipath`
   
    A telepítés indul el. Típus **Y** során a rendszer megerősítést kér a folytatáshoz.

### <a name="on-storsimple-device"></a>A StorSimple eszköz
A StorSimple eszköz kell rendelkezniük:

* Legalább két csatolót engedélyezni kell az iSCSI. Ellenőrizze, hogy két felületet a StorSimple eszköz iSCSI-kompatibilis, hajtsa végre az alábbi lépéseket a StorSimple eszköz, a klasszikus Azure portálon:
  
  1. Jelentkezzen be a klasszikus portálra, a StorSimple eszközhöz.
  2. Jelölje ki azt a StorSimple Manager szolgáltatás **eszközök** , és válassza ki a StorSimple-eszközre. Kattintson a **konfigurálása** és a hálózati kapcsolat beállításainak ellenőrzése. A képernyőfelvételen két iSCSI-kompatibilis hálózati adapterrel rendelkező alább láthatók. Itt DATA 2 és a DATA 3, mind 10 GbE adapterek iSCSI engedélyezve vannak.
     
      ![Az MPIO StorsSimple DATA 2 config](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![Az MPIO StorSimple adatok 3 Config](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      Az a **konfigurálása** lap
     
     1. Győződjön meg arról, hogy mindkét hálózati adapterek iSCSI engedélyezve. A **iSCSI engedélyezve** mezőt állítsa **Igen**.
     2. Győződjön meg arról, hogy a hálózati adapternek ugyanazt a sebességet, is 1 gbe-s vagy 10 GbE kell lennie.
     3. Vegye figyelembe az IPv4-címeket az iSCSI-kompatibilis felületek, és mentse a gazdagépen későbbi használatra.
* A StorSimple eszköz iSCSI csatolókat a CentOS kiszolgálóról elérhetőnek kell lennie.
      Ezt ellenőrizheti, hogy meg kell adnia az StorSimple iSCSI-kompatibilis hálózati adapterek IP-címét a gazdagép-kiszolgálón. A használt parancsok és a megfelelő kimenet adat2 (10.126.162.25) és DATA3 (10.126.162.26) az alábbiakban tekintheti meg:
  
        [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
        10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
        10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target

### <a name="hardware-configuration"></a>Hardverkonfiguráció
Azt javasoljuk, hogy csatlakozni, a két külön útvonalak a redundancia érdekében iSCSI hálózati adapterrel. Az alábbi ábra az ajánlott hardverkonfigurációt a magas rendelkezésre állás érdekében és terheléselosztás többutas a CentOS server és a StorSimple eszköz.

![Az MPIO hardverkonfiguráció a StorSimple Linux gazdagépre](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

A fenti ábrán látható:

* A StorSimple eszköz van egy aktív-passzív konfigurációban két vezérlőkkel.
* Két SAN kapcsolót a eszközvezérlők csatlakoznak.
* Két iSCSI-kezdeményezők engedélyezve vannak a StorSimple eszközt.
* Két hálózati adapterrel engedélyezve vannak a CentOS állomáson.

A fenti konfigurációban az eszköz és a gazdagép közötti 4 külön útvonalak eredményez, ha a gazdagép és az adatok felületek irányítható.

> [!IMPORTANT]
> * Azt javasoljuk, hogy azt ne keverje 1 gbe-s és a 10 GbE hálózati adaptert használjon a többutas. Ha két hálózati adapterrel, a két felülethez a azonos típusúnak kellene lennie.
> * A StorSimple eszköz DATA0, adat1, DATA4 és DATA5 1 GbE felületek mivel adat2 és DATA3 10 GbE hálózati adapterrel. |}
> 
> 

## <a name="configuration-steps"></a>Konfigurációs lépések
A konfigurációs lépéseket a többutas tartalmaz, amely automatikus felderítést, adja meg a terheléselosztási algoritmust, hogy használja, elérhető útvonalai konfigurálása többutas engedélyezése, és végül a konfigurációjának ellenőrzése. A következő szakaszokban részletesen tárgyalt egyes lépéseket.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>1. lépés: Az automatikus felderítést használjon többutas konfigurálása
A többutas által támogatott eszközök automatikusan felderíthető és konfigurálva.

1. Inicializálni `/etc/multipath.conf` fájlt. Típus:
   
     `mpathconf --enable`
   
    A fenti parancs létrehoz egy `sample/etc/multipath.conf` fájlt.
2. A többutas szolgáltatás elindítása. Típus:
   
    `service multipathd start`
   
    A következő eredmény jelenik meg:
   
    `Starting multipathd daemon:`
3. Multipaths automatikus észleléséhez. Típus:
   
    `mpathconf --find_multipaths y`
   
    Ez az alapértelmezett részében módosítani fogja a `multipath.conf` alább látható módon:
   
        defaults {
        find_multipaths yes
        user_friendly_names yes
        path_grouping_policy multibus
        }

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>2. lépés: A StorSimple-köteteket többutas konfigurálása
Alapértelmezés szerint minden eszköz fekete a listában szereplő multipath.conf és művelet megkerülését eredményezte. Szüksége lesz blacklist kivételeket úgy, hogy engedélyezze a többutas StorSimple eszközökhöz kötetek létrehozásához.

1. Szerkessze a `/etc/mulitpath.conf` fájlt. Típus:
   
    `vi /etc/multipath.conf`
2. Keresse meg a blacklist_exceptions szakaszt a multipath.conf fájlban. A StorSimple eszköz ebben a szakaszban blacklist kivételként szerepelnie kell. Ezt a fájlt (használja az Ön által használt eszköz csak az adott modell) alábbi ábrán látható módon módosítsa a megfelelő sorok is állítsa vissza:
   
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

### <a name="step-3-configure-round-robin-multipathing"></a>3. lépés: Ciklikus multiplexelés többutas konfigurálása
A terheléselosztási algoritmust a rendelkezésre álló multipaths, az aktív vezérlővel elosztott terhelésű, a ciklikus multiplexelési módon használja.

1. Szerkessze a `/etc/multipath.conf` fájlt. Típus:
   
    `vi /etc/multipath.conf`
2. Az a `defaults` területen állítsa be a `path_grouping_policy` való `multibus`. A `path_grouping_policy` alapértelmezés szerint a szabályzatot alkalmazni szeretné meghatározatlan multipaths csoportosítási határozza meg. Az alapértelmezett szakasz jelenik meg, mint a lent látható módon.
   
        defaults {
                user_friendly_names yes
                path_grouping_policy multibus
        }

> [!NOTE]
> Leggyakoribb értékeinek `path_grouping_policy` tartalmazza:
> 
> * feladatátvételi prioritási csoportonként 1 elérési útja =
> * multibus = 1 prioritás csoportban lévő összes érvényes elérési utat
> 
> 

### <a name="step-4-enable-multipathing"></a>4. lépés: Engedélyezze többutas
1. Indítsa újra a `multipathd` démon. Típus:
   
    `service multipathd restart`
2. A kimenet lesz a lent látható módon:
   
        [root@centosSS ~]# service multipathd start
        Starting multipathd daemon:  [OK]

### <a name="step-5-verify-multipathing"></a>5. lépés: Ellenőrizze, hogy többutas
1. Először győződjön meg arról, hogy az iSCSI-kapcsolatot létesíteni az alábbiak szerint a StorSimple eszköz:
   
   a. A StorSimple eszköz felderítése. Típus:
      
    ```
    iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>
    ```
    
    Ha IP-címet DATA0 10.126.162.25 és 3260-as port meg van nyitva, a kimenő iSCSI forgalomhoz a StorSimple eszközön eredménye a lent látható módon:
    
    ```
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Másolja a StorSimple eszköz IQN-Nevének `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`, az előző kimenetből.

   b. Csatlakoztassa az eszközt használja a cél IQN. A StorSimple eszköz itt az iSCSI-cél. Típus:

    ```
    iscsiadm -m node --login -T <IQN of iSCSI target>
    ```

    A következő példa bemutatja a kimeneti IQN cél a `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`. A kimeneti azt jelzi, hogy sikeresen csatlakozott a két iSCSI-kompatibilis hálózati adapterek az eszközön.

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

    Ha csak egy állomás felület, és itt két elérési utak, akkor szüksége ahhoz, hogy az iSCSI-gazdagép két a felülethez. Kövesse a [részletes utasításokat Linux dokumentációjának](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

2. A kötet a StorSimple eszközön a CentOS kiszolgáló van kitéve. További információkért lásd: [6. lépés: kötet létrehozása](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) a StorSimple eszköz Azure-portálon.

3. Ellenőrizze a rendelkezésre álló elérési utat. Típus:

      ```
      multipath –l
      ```

      A következő példában két hálózati adapterrel a kimenetét mutatja be a StorSimple eszköz csatlakozik egy állomás hálózati illesztő – két elérhető elérési útját.

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

## <a name="troubleshoot-multipathing"></a>Többutas hibaelhárítása
Ez a szakasz néhány hasznos tipp nyújt, ha probléma merül fel többutas konfiguráció során.

Q. Nem jelenik meg a változásai `multipath.conf` fájl lépnek érvénybe.

A. Ha végzett módosításokat a `multipath.conf` fájl, akkor a többutas szolgáltatás újraindításához. Írja be a következő parancsot:

    service multipathd restart

Q. A StorSimple eszköz két hálózati adapterrel és két hálózati adapterrel a gazdagépen engedélyezve van. A rendelkezésre álló útvonalak felsorolásához a csak két elérési útnak jelenik meg. Várt négy elérhető elérési utakat megjelenítéséhez.

A. Győződjön meg arról, hogy az elérési utat ugyanazon az alhálózaton és irányítható. Ha a hálózati adapterek különböző VLAN-on, és nem irányítható, látni fogja csak két elérési útnak. Ez győződhet, győződjön meg arról, hogy mindkét a gazdagép illesztőket egy adott hálózati csatoló a StorSimple eszköz érhető el. Szüksége lesz a [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) , ez az ellenőrzés csak akkor lehet elvégezni a támogatási munkameneten keresztül.

Q. Amikor a rendelkezésre álló útvonalak felsorolásához kimenetet nem látható.

A. Általában nem jelennek meg minden multipathed elérési utak javasol, a többutas démon probléma, amely a legvalószínűbb ok az, hogy van-e bármilyen probléma a `multipath.conf` fájlt.

Érdemes ellenőrzése, hogy ténylegesen megjelenik egyes lemezek csatlakoztassa a célkiszolgálóhoz, mivel nincs válasz a többutas listaelemek a is jelentheti, nem rendelkezik olyan lemezek is lenne.

* A következő paranccsal ellenőrizze újra a SCSI-busz:
  
    `$ rescan-scsi-bus.sh `(a sg3_utils csomag részeként)
* Írja be a következő parancsokat:
  
    `$ dmesg | grep sd*`
     
     Vagy
  
    `$ fdisk –l`
  
    Ezek visszaadható nemrégiben felvett lemezek adatait.
* Határozzák meg, hogy a StorSimple-lemez, használja a következő parancsokat:
  
    `cat /sys/block/<DISK>/device/model`
  
    Ezzel visszatér a karakterláncot, amely határozza meg, hogy a StorSimple lemez esetén.

A kevésbé valószínű, de lehetséges oka is lehet elavult iscsid azonosítója (PID). A következő parancsot használja az iSCSI-munkamenet kijelentkeztetése:

    iscsiadm -m node --logout -p <Target_IP>

Ismételje meg ezt a parancsot az iSCSI-tároló, amely a StorSimple eszköz összes csatlakoztatott hálózati csatolókat. Miután az összes iSCSI-munkameneteket kijelentkezett a az iSCSI-cél IQN helyreállítani az iSCSI-munkamenetet. Írja be a következő parancsot:

    iscsiadm -m node --login -T <TARGET_IQN>


Q. Nem biztos, ha az eszköz nem szerepel az engedélyezési listán.

A. Ellenőrizze, hogy az eszköz szerepel az engedélyezési listán, használja a következő hibaelhárítási interaktív parancsot:

    multipathd –k
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


További információkért látogasson el [hibaelhárítási többutas interaktív parancsot használja](http://www.centos.org/docs/5/html/5.1/DM_Multipath/multipath_config_confirm.html).

## <a name="list-of-useful-commands"></a>Hasznos parancsok listája
| Típus | Parancs | Leírás |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |ISCSI szolgáltatás elindítása |
| &nbsp; |`service iscsid stop` |ISCSI szolgáltatás leállítása |
| &nbsp; |`service iscsid restart` |ISCSI szolgáltatás újraindítása |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |A megadott cím a rendelkezésre álló tárolók felderítése |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Jelentkezzen be az iSCSI-cél |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Jelentkezzen ki a az iSCSI-cél |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Nyomtatási iSCSI-kezdeményező neve |
| &nbsp; |`iscsiadm –m session –s <sessionid> -P 3` |Az iSCSI-munkamenetet, és a kötet a gazdagépen felderített állapotának ellenőrzéséhez. |
| &nbsp; |`iscsi –m session` |A gazdagép és a StorSimple eszköz közötti összes iSCSI-munkameneteket jeleníti meg |
|  | | |
| **Többutas** |`service multipathd start` |Indítsa el a többutas démont |
| &nbsp; |`service multipathd stop` |A többutas démon leállítása |
| &nbsp; |`service multipathd restart` |Indítsa újra a többutas démon |
| &nbsp; |`chkconfig multipathd on` </br> VAGY </br> `mpathconf –with_chkconfig y` |Engedélyezze a többutas démon rendszerindítás elindításához |
| &nbsp; |`multipathd –k` |Indítsa el a hibaelhárítási interaktív konzolt |
| &nbsp; |`multipath –l` |Lista többutas kapcsolatok és eszközök |
| &nbsp; |`mpathconf --enable` |A minta mulitpath.conf fájl létrehozása`/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>További lépések
Konfigurálja az MPIO Linux-gazdagépre, mert is szükség lehet a következő CentoS 6.6 dokumentumok hivatkozik:

* [A CentOS MPIO beállítása](http://www.centos.org/docs/5/html/5.1/DM_Multipath/setup_procedure.html)
* [Linux-képzési útmutató](http://linux-training.be/files/books/LinuxAdm.pdf)

