---
title: Az MPIO konfigurálása a StorSimple Linux-gazdagépen |} A Microsoft Docs
description: Az MPIO konfigurálása a CentOS 6.6 futó Linux-állomáshoz csatlakoztatott StorSimple
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
ms.openlocfilehash: bc1e8a5abc85af95448570497177030f17649d87
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58877584"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>CentOS rendszerű gazdagépen a StorSimple az MPIO konfigurálása
Ez a cikk azt ismerteti, a Centos 6.6 gazdakiszolgálón a többutas I/O (MPIO) konfigurálásához szükséges lépéseket. A gazdakiszolgálón a Microsoft Azure StorSimple-eszköz magas rendelkezésre állás érdekében iSCSI-kezdeményezők keresztül csatlakozik. Ismerteti részletesen a Többutas eszközök és az adott telepítő csak a StorSimple-kötetek automatikus felderítését.

Ez az eljárás akkor alkalmazható, a StorSimple 8000 sorozatú eszközök a modellek.

> [!NOTE]
> Ez az eljárás nem használható a StorSimple Cloud Appliance. További információkért lásd: a gazdakiszolgálók a felhőalapú berendezés konfigurálása.


## <a name="about-multipathing"></a>Többutas kapcsolatban
A többutas funkció lehetővé teszi, hogy több i/o-elérési út gazdakiszolgáló és a egy tárolóeszköz között konfigurálhatja. Ezeket az i/o-elérési utakat, amelyek tartalmazhatnak külön kábelek, a kapcsolók, a hálózati adapterek és a tartományvezérlők fizikai SAN-kapcsolatokat. Többutas i/o elérési utak konfigurálása egy új eszköz társított összes összesített görbét összesíti.

A többutas célja két lépésből:

* **Magas rendelkezésre állás**: Ha bármely elem, az i/o-útvonal (például a kábel, kapcsoló, hálózati adapter vagy tartományvezérlő) nem biztosít egy alternatív elérési utat.
* **Terheléselosztás**: A konfiguráció a tárolóeszköz, attól függően, a jobb teljesítmény érdekében az i/o-elérési terhelésének észlelése és dinamikusan a ezeket a terhelés kiegyenlítése.

### <a name="about-multipathing-components"></a>Azokról az összetevőkről a többutas működés
Többutas a Linux kernel-összetevők és a felhasználói térben összetevők, ahogy a lenti táblázatban láthatóak áll.

* **Kernel**: A fő összetevője a *eszköz-leképező* , amely reroutes i/o és feladatátvételi támogatja az elérési utak és az elérési út csoportok.

* **Felhasználói térben**: Ezek a *Többutas-eszközök* multipathed eszközök kezelését, amely ezt a többutas eszköz-eseményleképező modul Mi a teendő. Az eszközök állnak:
   
   * **A többutas**: sorolja fel, és konfigurálja a multipathed eszközök.
   * **Multipathd**: démont, amely végrehajtja a többutas, és az elérési utak figyeli.
   * **Devmap-name**: egy jelentéssel bíró eszköznév tartó kernelesemények devmaps biztosít.
   * **Kpartx**: lineáris devmaps képez le, hogy a többutas maps hardverparticionálható eszköz partíciók.
   * **MultiPath.conf**: felülírja a beépített configuration tábla használt többutas démon konfigurációs fájlt.

### <a name="about-the-multipathconf-configuration-file"></a>Tudnivalók a multipath.conf konfigurációs fájl
A konfigurációs fájl `/etc/multipath.conf` lehetővé teszi a többutas közül számos felhasználó által konfigurálható. A `multipath` parancs, és a kernel démon `multipathd` használja a fájlban található adatokat. A fájl konzultál csak a Többutas eszközök konfigurálása során. Győződjön meg arról, hogy az összes módosítások futtatása előtt a `multipath` parancsot. Ha ezt követően módosítja a fájlt, leállítására és elindítására a módosítások érvénybe léptetéséhez újra a multipathd kell.

A multipath.conf öt részből áll:

- **Rendszer alapbeállításainak** *(alapértékek)*: Rendszer alapbeállításainak felül lehet bírálni.
- **Eszközök tiltólistán** *(blacklist)*: Megadhatja, hogy nem kell vezérelnie eszköz-leképező eszközök listáját.
- **Kivételek tiltólistára** *(blacklist_exceptions)*: Azonosíthatja a többutas eszközökként kell kezelni, még akkor is, ha a blacklist szerepel az adott eszközökre.
- **Tárolási vezérlő meghatározott beállítások** *(eszközökön)*: Szállítói és termékinformációk rendelkező eszközökre alkalmazandó beállításokat is megadhat.
- **Egyedi eszközbeállítások** *(multipaths)*: Ebben a szakaszban használhatja az egyes logikai egységek beállításai finomhangolásához.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>A Linux-állomáshoz csatlakoztatott StorSimple többutas konfigurálása
Egy Linux-állomáshoz csatlakoztatott StorSimple eszköz magas rendelkezésre állás és a terheléselosztás konfigurálható. Például ha a Linux-állomáshoz San hálózathoz csatlakozó két adapterrel rendelkezik, és az eszköz csatlakozik a TÁROLÓHÁLÓZAT úgy, hogy ezeket az adaptereket ugyanazon az alhálózaton két adapterrel rendelkezik, majd számítunk 4 elérési utak érhető el. Azonban ha egyes adatillesztő az eszköz és a gazdagépcsoport adapter egy másik IP-alhálózat (és nem irányítható), majd csak 2 elérési utak lesz elérhető. Konfigurálhatja a többutas automatikusan felderíteni az elérhető elérési utakat, egy adott elérési útján a terheléselosztási algoritmus kiválasztása, egyes konfigurációs beállítások csak a StorSimple-köteteket, a alkalmazni engedélyezése és többutas működés ellenőrzéséhez.

Az alábbi eljárás ismerteti, hogyan lehet többutas konfigurálása a StorSimple eszköz két hálózati adapterrel rendelkező két hálózati adapterrel rendelkező gazdagépre való csatlakozáskor.

## <a name="prerequisites"></a>Előfeltételek
Ez a szakasz részletesen, a CentOS-kiszolgáló és a StorSimple eszköz konfigurációs előfeltételeit.

### <a name="on-centos-host"></a>On CentOS host
1. Győződjön meg arról, hogy a CentOS-gazdagép rendelkezik-e engedélyezve 2 hálózati illesztőt. Típus:
   
    `ifconfig`
   
    Az alábbi példa bemutatja a kimenet, amikor két hálózati adapter (`eth0` és `eth1`) a gazdagépen található.
   
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
1. Telepítés *iSCSI-kezdeményező-utils* a CentOS-kiszolgálón. A következő lépésekkel telepítse *iSCSI-kezdeményező-utils*.
   
   1. Jelentkezzen be `root` , a CentOS-gazdagépen.
   1. Telepítse a *iSCSI-kezdeményező-utils*. Típus:
      
       `yum install iscsi-initiator-utils`
   1. Miután a *iSCSI-kezdeményező-utils* sikeresen van az iSCSI-szolgáltatás telepítve van, indítsa el. Típus:
      
       `service iscsid start`
      
       Alkalmakkor `iscsid` előfordulhat, hogy ténylegesen ne indítsa el a `--force` lehetőséget is szükség lehet
   1. Győződjön meg arról, hogy az iSCSI-kezdeményező rendszerindítás ideje alatt engedélyezve van, használja a `chkconfig` parancsot a szolgáltatás engedélyezéséhez.
      
       `chkconfig iscsi on`
   1. Szerepel, hogy megfelelően beállítás ellenőrzéséhez futtassa a parancsot:
      
       `chkconfig --list | grep iscsi`
      
       Az alábbiakban egy példa látható a kimenetre.
      
           iscsi   0:off   1:off   2:on3:on4:on5:on6:off
           iscsid  0:off   1:off   2:on3:on4:on5:on6:off
      
       A fenti példában láthatja, hogy az iSCSI-környezet rendszerindító időben fog futni futtatási szintjei 2, 3, 4 és 5.
1. Telepítés *a többutas eseményleképező eszköz*. Típus:
   
    `yum install device-mapper-multipath`
   
    A telepítés indul el. Típus **Y** esetén a rendszer megerősítést kér is.

### <a name="on-storsimple-device"></a>StorSimple-eszközön
A StorSimple-eszköz kell rendelkeznie:

* Legalább két csatolót engedélyezni az iSCSI. Ellenőrizze, hogy két adapter a StorSimple eszköz iSCSI-kompatibilis, hajtsa végre az alábbi lépéseket a StorSimple-eszköz, a klasszikus Azure portálon:
  
  1. Jelentkezzen be a klasszikus portálon, a StorSimple eszközhöz.
  1. Válassza ki a StorSimple Manager szolgáltatást, kattintson a **eszközök** , és válassza ki a StorSimple-eszközre. Kattintson a **konfigurálása** , és ellenőrizze a hálózati adapter beállításait. Az alábbiakban látható egy képernyőkép két iSCSI-kompatibilis hálózati adapterrel rendelkező. Itt DATA 2 és DATA 3, két 10 GbE adapter engedélyezve vannak az iSCSI.
     
      ![Az MPIO StorsSimple DATA 2 config](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![MPIO-t a StorSimple DATA 3 Config](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      Az a **konfigurálása** lap
     
     1. Győződjön meg arról, hogy mindkét hálózati adapterek-e az iSCSI-kompatibilis. A **az iSCSI-kompatibilis** mezőt állítsa **Igen**.
     1. Győződjön meg arról, hogy a hálózati adapternek ugyanazt a sebességet, mindkét 1 gbe-s vagy 10 GbE kell lennie.
     1. Vegye figyelembe az iSCSI-kompatibilis adapterek IPv4-címeket, és mentse későbbi használatra a gazdagépen.
* A StorSimple eszköz iSCSI kapcsolódási CentOS kiszolgálóról kell lennie.
      Ennek az ellenőrzéséhez meg kell adnia az a StorSimple-iSCSI-kompatibilis hálózati adapterek IP-címek a gazdagép-kiszolgálón. Használt parancsok és a megfelelő adat2 kimenet (10.126.162.25) és DATA3 (10.126.162.26) az alábbiakban látható:
  
        [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
        10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
        10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target

### <a name="hardware-configuration"></a>Hardverkonfiguráció
Azt javasoljuk, hogy kapcsolódik-e az iSCSI két hálózati adaptert külön útvonalakon a redundancia biztosítása érdekében. Az alábbi ábrán látható, a magas rendelkezésre álláshoz ajánlott hardverkonfigurációja és többutas terheléselosztás, a CentOS-kiszolgáló és a StorSimple-eszköz.

![Az MPIO hardverkonfiguráció storsimple Linux gazdagépre](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Az előző ábrán látható:

* A StorSimple-eszköz szerepel a két vezérlőn egy aktív / passzív konfigurációt.
* Két SAN-kapcsolót a eszközvezérlők kapcsolódnak.
* Két iSCSI-kezdeményezők engedélyezve vannak a StorSimple eszközön.
* Két hálózati adaptert a CentOS gazdagépen engedélyezve van.

A fenti konfigurációs értékét fogják eredményezni 4 külön útvonalak az eszköz és a gazdagép között, ha a gazdagép és az adatok felületek irányítható.

> [!IMPORTANT]
> * Azt javasoljuk, hogy ne használja együtt a 1 gbe-s és 10 GbE hálózati adapterek a többutas működés. Két hálózati adaptert használatakor az interfészek, mind az azonos típusú kell lennie.
> * A StorSimple eszköz DATA0, adat1, DATA4 DATA5 jsou 1 GbE-adapterek mivel adat2 és DATA3 10 GbE hálózati adapterek. |}
> 
> 

## <a name="configuration-steps"></a>Konfigurációs lépések
A konfigurációs lépéseket a többutas működés magában foglalja, automatikus felderítéshez, a terheléselosztási algoritmus használatához adja meg a rendelkezésre álló útvonalak konfigurálása többutas engedélyezését, és végül a konfigurációjának ellenőrzése. Egyes lépéseket a következő szakaszokban részletesen tárgyalja.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>1. lépés: Többutas automatikus felderítés konfigurálása
A többutas által támogatott eszközök automatikusan felderíthető és konfigurálva.

1. Inicializálása `/etc/multipath.conf` fájlt. Típus:
   
     `mpathconf --enable`
   
    A fenti parancs létrehoz egy `sample/etc/multipath.conf` fájlt.
1. Indítsa el a többutas szolgáltatást. Típus:
   
    `service multipathd start`
   
    A következő kimenetet fog látni:
   
    `Starting multipathd daemon:`
1. Multipaths automatikus észlelésének engedélyezése. Típus:
   
    `mpathconf --find_multipaths y`
   
    Ez az alapértelmezett érték szakaszában módosítani fogja a `multipath.conf` alább látható módon:
   
        defaults {
        find_multipaths yes
        user_friendly_names yes
        path_grouping_policy multibus
        }

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>2. lépés: A StorSimple-kötetek többutas konfigurálása
Alapértelmezés szerint minden eszköz fekete a listában szereplő multipath.conf és művelet megkerülését eredményezte. Szüksége lesz, hogy a kötetek a StorSimple-eszközök esetén a többutas blacklist kivételek létrehozása.

1. Szerkessze a `/etc/mulitpath.conf` fájlt. Típus:
   
    `vi /etc/multipath.conf`
1. Keresse meg a blacklist_exceptions szakaszt a multipath.conf fájlban. A StorSimple-eszköz kell szerepelnie, ebben a szakaszban egy blacklist kivételt. Ezt a fájlt (használja az Ön által használt eszköznek csak az adott modell) alább látható módon módosítsa a megfelelő sorokat is állítsa vissza:
   
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

### <a name="step-3-configure-round-robin-multipathing"></a>3. lépés: Ciklikus időszeleteléses többutas konfigurálása
A terheléselosztási algoritmus használ az aktív vezérlőn az összes rendelkezésre álló multipaths elosztott terhelésű, Ciklikus időszeleteléses módon.

1. Szerkessze a `/etc/multipath.conf` fájlt. Típus:
   
    `vi /etc/multipath.conf`
1. Alatt a `defaults` szakaszában a `path_grouping_policy` való `multibus`. A `path_grouping_policy` alapértelmezett elérési útját adja meg a szabályzat vonatkozik meghatározatlan multipaths csoportosítása. Az alapértelmezett érték szakaszban fognak kinézni, ahogy az alábbi.
   
        defaults {
                user_friendly_names yes
                path_grouping_policy multibus
        }

> [!NOTE]
> Leggyakoribb értékeinek `path_grouping_policy` tartalmazza:
> 
> * feladatátvételi = 1 elérési prioritású csoportonként
> * multibus = 1 prioritású csoportban található összes érvényes elérési utat
> 
> 

### <a name="step-4-enable-multipathing"></a>4. lépés: Engedélyezze a többutas működés
1. Indítsa újra a `multipathd` démon. Típus:
   
    `service multipathd restart`
1. A kimenet lesz az alább látható módon:
   
        [root@centosSS ~]# service multipathd start
        Starting multipathd daemon:  [OK]

### <a name="step-5-verify-multipathing"></a>5. lépés: Többutas működés ellenőrzése
1. Először győződjön meg arról, hogy az iSCSI-kapcsolatot létesíteni a következő a StorSimple-eszköz:
   
   a. A StorSimple-eszköz felderítése. Típus:
      
    ```
    iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>
    ```
    
    Ha IP-cím DATA0 10.126.162.25 és 3260-as portot nyitjuk kimenő iSCSI forgalomhoz a StorSimple eszközön kimenete alább látható módon:
    
    ```
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Másolja az IQN-t a StorSimple eszköz `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`, a fenti kimenetből.

   b. Csatlakozzon a cél IQN-t az eszközön. A StorSimple-eszköz az iSCSI-cél itt. Típus:

    ```
    iscsiadm -m node --login -T <IQN of iSCSI target>
    ```

    Az alábbi példa bemutatja a kimeneti cél IQN- `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`. A kimenet azt jelzi, hogy sikeresen csatlakozott a két iSCSI-kompatibilis hálózati adapterek az eszközön.

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

    Ha csak egy gazdagép felület és a két terveket itt jelenik meg, majd, engedélyeznie kell interfészek, mind az iSCSI-gazdagép. Kövesse a [részletes utasítások a Linux-dokumentációban](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

1. Egy kötet a StorSimple-eszközről van kitéve a CentOS-kiszolgáló. További információkért lásd: [6. lépés: Hozzon létre egy kötetet](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) az Azure Portalon a StorSimple eszközön.

1. Ellenőrizze az elérhető elérési utakat. Típus:

      ```
      multipath –l
      ```

      Az alábbi példa két hálózati adaptert a kimenetét mutatja be két elérhető elérési utakat az egyetlen hálózati adapter csatlakozik a StorSimple eszközön.

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

## <a name="troubleshoot-multipathing"></a>Többutas működés hibaelhárítása
Ez a szakasz néhány hasznos Tanács nyújt, ha problémákat tapasztal többutas konfiguráció során.

K. A változások nem láthatók `multipath.conf` fájl lépnek érvénybe.

A. Ha végzett módosításokat a `multipath.conf` fájl, szüksége lesz a többutas szolgáltatás újraindításához. Írja be a következő parancsot:

    service multipathd restart

K. A StorSimple eszközön két hálózati adaptert, és két hálózati adaptert a gazdagépen engedélyezve van. Ha a rendelkezésre álló útvonalak felsorolásához csak két útvonalon látható. Várt négy elérhető elérési utakat megtekintéséhez.

A. Győződjön meg arról, hogy a két útvonalon ugyanazon az alhálózaton és irányítható. Ha a hálózati adapterek különböző VLAN-on, és nem irányítható, látni fogja csak két elérési útját. Ennek ellenőrzéséhez egyik módja, hogy győződjön meg arról, hogy a gazdagép interfészek, mind a hálózati adapterre a StorSimple eszközön elérheti. Kell [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) , ezt az ellenőrzést csak egy támogatási munkamenetet keresztül lehetséges.

K. Ha a rendelkezésre álló útvonalak felsorolásához kimenetet nem láthatók.

A. Általában nem jelennek meg minden olyan multipathed elérési utak javasol a többutas démon problémájára, és valószínűleg, hogy van-e bármilyen probléma Itt a `multipath.conf` fájlt.

Érdemes ellenőrzése, hogy ténylegesen látja egyes lemezek csatlakozzon a cél, mivel nincs válasz a többutas felsorolt is jelentheti, nem kell minden olyan lemezeket is lenne.

* A következő paranccsal ellenőrizze újra az SCSI-busz:
  
    `$ rescan-scsi-bus.sh` (sg3_utils csomag részeként)
* Írja be a következő parancsokat:
  
    `$ dmesg | grep sd*`
     
     Vagy
  
    `$ fdisk –l`
  
    Ezek nemrégiben hozzáadott lemezek adatait adja vissza.
* Vizsgálja, hogy a StorSimple-lemez, használja a következő parancsokat:
  
    `cat /sys/block/<DISK>/device/model`
  
    Ez visszaadja a karakterláncot, amely meghatározza, hogy a StorSimple-lemez.

A kevésbé valószínű, de lehetséges ok lehet is elavult iscsid pid. A következő parancsot használja az iSCSI-munkamenet kijelentkeztetése:

    iscsiadm -m node --logout -p <Target_IP>

Ismételje meg ezt a parancsot az iSCSI-tároló, amely a StorSimple-eszköz az összes csatlakoztatott hálózati adapter. A bejelentkezés után az összes iSCSI-munkameneteket, használja az iSCSI-cél IQN megszüntette az iSCSI-munkamenetet. Írja be a következő parancsot:

    iscsiadm -m node --login -T <TARGET_IQN>


K. Nem biztos, ha az eszköz szerepel az engedélyezési listán.

A. Győződjön meg arról, hogy az eszköz nem szerepel az engedélyezési listán, használja a következő hibaelhárítási interaktív parancsot:

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


További információért ugorjon [használata a hibaelhárítás interaktív parancsot a többutas működés](http://www.centos.org/docs/5/html/5.1/DM_Multipath/multipath_config_confirm.html).

## <a name="list-of-useful-commands"></a>Hasznos parancsok listája
| Typo | Parancs | Leírás |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |ISCSI szolgáltatás elindítása |
| &nbsp; |`service iscsid stop` |ISCSI szolgáltatás leállítása |
| &nbsp; |`service iscsid restart` |ISCSI szolgáltatás újraindítása |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |Fedezze fel a megadott cím a rendelkezésre álló tárolók |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Jelentkezzen be az iSCSI-cél |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Jelentkezzen ki az iSCSI-tároló |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Nyomtatási iSCSI-kezdeményező neve |
| &nbsp; |`iscsiadm –m session –s <sessionid> -P 3` |Az iSCSI-munkamenetet és a kötet a gazdagépen felderített állapotának ellenőrzése |
| &nbsp; |`iscsi –m session` |Megjeleníti a gazdagép és a StorSimple-eszköz között létesített kapcsolat összes iSCSI-munkameneteket |
|  | | |
| **Többutas** |`service multipathd start` |A többutas démon indítása |
| &nbsp; |`service multipathd stop` |Állítsa le a többutas démon |
| &nbsp; |`service multipathd restart` |A többutas démon újraindítása |
| &nbsp; |`chkconfig multipathd on` </br> VAGY </br> `mpathconf –with_chkconfig y` |Engedélyezze a többutas démon rendszerindítás elindításához |
| &nbsp; |`multipathd –k` |Indítsa el a hibaelhárítási interaktív konzol |
| &nbsp; |`multipath –l` |Lista többutas kapcsolatok és -eszközök |
| &nbsp; |`mpathconf --enable` |A minta mulitpath.conf fájl létrehozása `/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>További lépések
Konfigurálja az MPIO Linux-gazdagépen, mert szükség lehet hivatkozni a következő CentoS 6.6 dokumentumokat:

* [MPIO a CentOS beállítása](http://www.centos.org/docs/5/html/5.1/DM_Multipath/setup_procedure.html)
* [Linux-képzési útmutató](http://linux-training.be/linuxsys.pdf)

