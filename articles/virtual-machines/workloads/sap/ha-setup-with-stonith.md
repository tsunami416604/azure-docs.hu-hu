---
title: Magas rendelkezésre állás az SAP Hana az Azure-ban (nagyméretű példányok) a STONITH beállítása |} A Microsoft Docs
description: Magas rendelkezésre állás az SAP Hana az Azure-ban (nagyméretű példányok) a STONITH használatával SUSE létrehozni
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 66973ce78004d0f29d08264869f166202aaaf109
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011851"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Magas rendelkezésre állás beállítása a STONITH használatával SUSE
Ez a dokumentum beállítása a STONITH eszközzel SUSE operációs rendszer a magas rendelkezésre állás a részletes lépésenkénti utasításokat biztosít.

**Jogi nyilatkozat:** *Ez az útmutató a Microsoft nagyméretű HANA-példányok a környezetben, amely sikeresen működik a telepítés tesztelésével származik. Microsoft Service Management csapata HANA nagyméretű példányokhoz nem támogatja az operációs rendszer, mert szükség lehet bármilyen további hibaelhárítási és az operációs rendszer rétegen pontosítása SUSE kapcsolódni. Microsoft service management csapata beállítása a STONITH eszköz és a teljes körűen támogatja, és tehetők STONITH eszközzel kapcsolatos problémák elhárítása.*
## <a name="overview"></a>Áttekintés
A SUSE-fürtszolgáltatás használata magas rendelkezésre állású beállításához, meg kell felelnie a következő előfeltételek teljesülésére.
### <a name="pre-requisites"></a>Előfeltételek
- Nagyméretű HANA-példányok törlődnek.
- Regisztrálva van az operációs rendszer
- Nagyméretű HANA-példányokhoz kiszolgálói kapcsolódnak SMT kiszolgáló beolvasni a javítások és csomagok
- Operációs rendszer a legfrissebb javításoknak telepítve van
- NTP (time-kiszolgáló) be van állítva.
- Elolvastam és megértettem a legújabb verzióra a magas rendelkezésre ÁLLÁSÚ telepítési SUSE-dokumentáció

### <a name="setup-details"></a>Beállítás részletei
Ez az útmutató a következő beállítást használja:
- Operációs rendszer: Az SAP SLES 12 SP1
- Nagyméretű HANA-példányok: 2xS192 (négy sockets, 2 TB)
- HANA-verzió: HANA 2.0 SP1
- Kiszolgálók nevei: sapprdhdb95 (csomópont1) és sapprdhdb96 (csomópont2)
- STONITH eszköz: az iSCSI-alapú STONITH eszköz
- Állítsa be, a nagyméretű HANA-példány csomópontjának egyikét, NTP

A HSR nagyméretű HANA-példányok üzembe helyezésekor a Microsoft szolgáltatás-felügyeleti csoport beállítása a STONITH igényelhet. Ha már egy meglévő ügyfél rendelkező, nagyméretű HANA-példányok üzembe helyezett, és meg kell állítható be a meglévő paneleket a STONITH eszköz, a következő adatokat a szolgáltatás űrlapot (SRF) a Microsoft Service Management csapatának szeretne. A Technical Account Manager vagy a nagyméretű HANA-példány bevezetéséhez a Microsoft Contact SRF űrlap kérhetnek. Az új ügyfelek kérhetnek STONITH eszköz kiépítése időpontjában. A bemeneti kiépítési kérést formájában érhetők el.

- Kiszolgáló neve és a kiszolgáló IP-címet (például myhanaserver1, 10.35.0.1)
- Hely (például USA keleti régiója)
- Ügyfél neve (például Microsoft)
- SID - HANA rendszer azonosítóját (például H11)

Miután konfigurálta a STONITH eszköz, a Microsoft szolgáltatás-felügyeleti csoport nyújtanak a SBD eszköz és az iSCSI-tárolóhoz, amelyet beállítása a STONITH telepítő használhat IP-címét. 

Beállítása a STONITH használatával teljes körű magas rendelkezésre ÁLLÁS, a következő lépéseket kell követnie:

1.  A SBD eszköz azonosítása
2.  A SBD eszköz inicializálása
3.  A fürt konfigurálása
4.  Setting Up the Softdog Watchdog
5.  Csatlakozzon a csomópontot a fürthöz
6.  A fürt ellenőrzése
7.  A szükséges erőforrásokat a fürt konfigurálása
8.  A feladatátvételi folyamat tesztelése

## <a name="1---identify-the-sbd-device"></a>1.   A SBD eszköz azonosítása
Ez a szakasz ismerteti, hogyan lehet megállapítani, hogy az SBD eszköz, a telepítés után a Microsoft service management csapata a STONITH konfigurálta a. **Ez a szakasz csak vonatkozik a meglévő ügyfél**. Ha egy új ügyfél, Microsoft service management csapata által nyújtott SBD eszköz nevét, és kihagyhatja az ebben a szakaszban.

1.1 módosítása */etc/iscsi/initiatorname.isci* , 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

A Microsoft service management adja meg ezt a karakterláncot. A fájl módosítása a **mindkét** a csomópontokon, azonban a csomópont száma eltér a csomópontokon.

![initiatorname.png](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 módosítása */etc/iscsi/iscsid.conf*: Állítsa be *node.session.timeo.replacement_timeout=5* és *node.startup = automatikus*. A fájl módosítása a **mindkét** a csomópontokat.

1.3-as, hajtsa végre a felderítési parancsot, négy munkamenetek jeleníti meg. Futtassa a csomópontokon is.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4-es, hajtsa végre a parancsot, jelentkezzen be az iSCSI-eszközt, négy munkamenetek jeleníti meg. Futtassa **mindkét** a csomópontokat.

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5-ös, hajtsa végre az újraellenőrzés parancsfájlt: *újraellenőrzés-scsi-bus.sh*.  Ez a szkript bemutatja, az Ön számára létrehozott új lemezeket.  Futtassa a csomópontokon is. A LUN számot kell megjelennie, amely csak nullánál nagyobb (például: 1, 2 stb.)

```
rescan-scsi-bus.sh
```
![rescanscsibus.png](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6-os, futtassa a parancsot az eszköznév *fdisk – l*. Futtassa a csomópontokon is. Válassza ki az eszközt az méretének **178 MiB**.

```
  fdisk –l
```

![fdisk-l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2.   A SBD eszköz inicializálása

2.1 inicializálni az SBD eszköz **mindkét** a csomópontok

```
sbd -d <SBD Device Name> create
```
![sbdcreate.png](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2-es ellenőrizze, hogy az eszköz milyen lett írva. Éppen **mindkét** a csomópontok

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3.   A fürt konfigurálása
Ez a szakasz ismerteti, hogyan állította be a SUSE magas rendelkezésre ÁLLÁS fürtöt.
### <a name="31-package-installation"></a>3.1 csomag telepítése
3.1.1 Ellenőrizze, hogy ha_sles és SAPHanaSR-doc minták telepítve vannak. Ha nincs telepítve, telepítse őket. Vagy telepítheti **mindkét** a csomópontokat.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.png](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR-doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2-es a fürt beállítása
3.2.1 használhatja *magas rendelkezésre állás – fürt-init* parancsot, vagy a fürt beállításának yast2 varázsló segítségével. Ebben az esetben a yast2 varázsló szolgál. Ez a lépés végrehajtása **csak az elsődleges csomóponton**.

Hajtsa végre a yast2 > magas rendelkezésre állású > fürt ![yast-vezérlés – center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![yast-hawk-install.png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Kattintson a **Mégse** óta a halk2 csomag már telepítve van.

![yast-hawk-continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Kattintson a **folytatása**

Várt érték (ebben az esetben 2) üzembe helyezett csomópontok számától = ![yast-fürt-Security.png](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) kattintson **tovább**
![yast-fürt-konfigurálása – csync2.png](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) hozzáadása csomópont nevét, majd kattintson "javasolt-fájlok hozzáadása"

Kattintson a "Bekapcsolása csync2"

Kattintson a "Létrehozás előtti Shared kulcsok", a helyi menü alatt jelenik meg

![yast-key-file.png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Kattintson az **OK** gombra

A hitelesítés Csync2 az IP-címek és előtti shared kulcsok használatával történik. A kulcsfájl csync2 -k /etc/csync2/key_hagroup hozza létre. A fájl key_hagroup kell másolni, hogy a fürt összes tagja manuális létrehozása után. **Győződjön meg, hogy a fájl másolása 1. csomópont csomópont2**.

![yast-cluster-conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Kattintson a **tovább**
![yast-fürt-service.png](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

Az alapértelmezett beállítás rendszerindítása ki volt kapcsolva, módosítsa a "be", így támasztja rendszerindító elindult. A kiválasztott telepítő igényei alapján teheti meg.
Kattintson a **tovább** , és a fürt konfigurálása befejeződött.

## <a name="4---setting-up-the-softdog-watchdog"></a>4.   Setting Up the Softdog Watchdog
Ez a szakasz ismerteti a figyelő (softdog) konfigurációját.

4.1 adja hozzá a következő sort */etc/init.d/boot.local* a **mindkét** a csomópontokat.
```
modprobe softdog
```
![modprobe-softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 a fájl frissítése */etc/sysconfig/sbd* a **mindkét** a csomópontok a következőképpen:
```
SBD_DEVICE="<SBD Device Name>"
```
![sbd-device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 a kernel modul betöltése a **mindkét** a csomópontok a következő parancs futtatásával
```
modprobe softdog
```
![modprobe-softdog-command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4. Ellenőrizze, és győződjön meg arról, hogy softdog fut a következőképpen **mindkét** a csomópontok:
```
lsmod | grep dog
```
![lsmod-grep-dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 a SBD eszköz indítsa el a **mindkét** a csomópontok
```
/usr/share/sbd/sbd.sh start
```
![sbd-sh-start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 a SBD démon tesztelni az **mindkét** a csomópontokat. Két bejegyzés látja a konfigurálás után **mindkét** a csomópontok
```
sbd -d <SBD Device Name> list
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 tesztüzenet küldése **egy** a csomópontok
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 a a **második** csomópont (csomópont2) ellenőrizheti, hogy az üzenet állapota
```
sbd  -d <SBD Device Name> list
```
![sbd-list-message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 elfogadják a sbd config, hogy a fájl frissítése */etc/sysconfig/sbd* következőképpen. A fájl frissítése a **mindkét** a csomópontok
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10 támasztja szolgáltatás indítása a **elsődleges csomóponthoz** (csomópont1)
```
systemctl start pacemaker
```
![start-pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Ha a támasztja szolgáltatás *sikertelen*, tekintse meg *5. forgatókönyv: Támasztja szolgáltatás meghiúsul*

## <a name="5---joining-the-cluster"></a>5.   A fürthöz való csatlakozás
Ez a szakasz ismerteti, hogyan csatlakoztathatók a csomópontot a fürthöz a.

### <a name="51-add-the-node"></a>5.1 a csomópont hozzáadása
Futtassa a következő parancsot **csomópont2** ahhoz, hogy csatlakozzon a fürt csomópont2.
```
ha-cluster-join
```
Ha megjelenik egy *hiba* tekintse meg a fürthöz való csatlakozás során *forgatókönyv 6: Nem sikerült csatlakozni a fürthöz 2 csomópont*.

## <a name="6---validating-the-cluster"></a>6.   A fürt érvényesítése

### <a name="61-start-the-cluster-service"></a>6.1 a Fürtszolgáltatás indítása
Ellenőrizze és szükség esetén indítsa el a fürt első alkalommal a **mindkét** csomópontok.
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl-status-pacemaker.png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 a állapotának figyelése
Futtassa a parancsot *crm_mon* biztosításához **mindkét** a csomópontok online állapotban. Futtatás **bármely csomópontjának** a fürt
```
crm_mon
```
![CRM-mon.png](media/HowToHLI/HASetupWithStonith/crm-mon.png) is bejelentkezhet, a fürt állapotának hawk *https://<node IP>: 7630*. Az alapértelmezett felhasználó hacluster, és a jelszó linux. Ha szükséges, módosíthatja, a jelszó használatával *passwd* parancsot.

## <a name="7-configure-cluster-properties-and-resources"></a>7. Konfigurálja a fürt tulajdonságainak és erőforrásainak 
Ez a szakasz ismerteti, hogyan konfigurálhatja a fürt erőforrásait.
Ebben a példában a következő erőforrás-a többi konfigurálható (ha szükséges) a SUSE magas rendelkezésre ÁLLÁS útmutató hivatkozva. Hajtsa végre a konfiguráció a **egyik csomópont valamelyik** csak. Hajtsa végre az elsődleges csomópont.

- Fürt rendszerindítási
- STONITH eszköz
- The Virtual IP Address


### <a name="71-cluster-bootstrap-and-more"></a>7.1-es fürt rendszerindítási és egyéb
Adja hozzá a fürt rendszerindítási. A fájl létrehozásához, és adja hozzá a szöveget a következőképpen:
```
sapprdhdb95:~ # vi crm-bs.txt
# enter the following to crm-bs.txt
property $id="cib-bootstrap-options" \
no-quorum-policy="ignore" \
stonith-enabled="true" \
stonith-action="reboot" \
stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
resource-stickiness="1000" \
migration-threshold="5000"
op_defaults $id="op-options" \
timeout="600"
```
A konfiguráció hozzáadása a fürthöz.
```
crm configure load update crm-bs.txt
```
![crm-configure-crmbs.png](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7.2 STONITH eszköz
Adja hozzá a STONITH erőforrás. A fájl létrehozásához és a következőképpen adja hozzá a szöveget.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15"
```
A konfiguráció hozzáadása a fürthöz.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7.3 a virtuális IP-cím
Erőforrás virtuális IP-cím hozzáadása. A fájl létrehozásához és az alábbiak szerint adja hozzá a szöveget.
```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
A konfiguráció hozzáadása a fürthöz.
```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7.4 az erőforrások ellenőrzése

A parancs futtatásakor *crm_mon*, láthatja, hogy a két erőforrás.
![crm_mon_command.png](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Ezenkívül láthatja a állapotát *https://<node IP address>: 7630/cib/élő/állapota*

![hawlk-status-page.png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. A feladatátvételi folyamat tesztelése
A feladatátvételi folyamat teszteléséhez, állítsa le a csomópont1 támasztja szolgáltatást, és az erőforrások feladatátvételének csomópont2.
```
Service pacemaker stop
```
Most támasztja szolgáltatás leállítása **csomópont2** és erőforrások átadja **csomópont1**

**A feladatátvétel előtt**
![előtt failover.png](media/HowToHLI/HASetupWithStonith/Before-failover.png)
**a feladatátvételt követően**
![után failover.png](media/HowToHLI/HASetupWithStonith/after-failover.png)
 ![ CRM-hétfő-után-failover.png](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)


## <a name="9-troubleshooting"></a>9. Hibaelhárítás
Ez a szakasz ismerteti a néhány hiba forgatókönyveket, amelyek a telepítés során előforduló is. Előfordulhat, hogy nem feltétlenül között ezeket a problémákat.

### <a name="scenario-1-cluster-node-not-online"></a>1. forgatókönyv: Fürtcsomópont nincs online állapotban
A csomópontokon nem jeleníti meg a Feladatátvevőfürt-kezelő online, megpróbálhatja a következő online állapotba.

Az iSCSI-szolgáltatás indítása
```
service iscsid start
```

És most meg kell tudni jelentkezzen be, hogy az iSCSI-csomópont
```
iscsiadm -m node -l
```
A következő várt kimenetet tűnik
```
sapprdhdb45:~ # iscsiadm -m node -l
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
```
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>2. forgatókönyv: yast2 nem grafikus nézetének megjelenítése
Ebben a dokumentumban a magas rendelkezésre állású fürt beállítása a yast2 grafikus képernyő szolgál. Ha yast2 nyissa meg a grafikus ablakban látható módon, és nem Qt hiba throw, hajtsa végre az alábbi lépéseket. A grafikus ablak nyílik meg, ha a lépést kihagyhatja.

**Hiba történt**

![yast2-qt-gui-error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Várt kimenet**

![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

A yast2 a grafikus nézet nem nyílik meg, kövesse a lépéseket következő.

Telepítse a szükséges csomagokat. "Root" felhasználóként kell bejelentkeznie, és rendelkezik SMT beállítani a csomagok letöltése és telepítése.

A csomagokat telepíteni, használja a yast > Software > szoftverkezelés > Függőségek > "Ajánlott csomagok... telepítés" lehetőséget. Az alábbi képernyőképen a várt képernyők mutatja be.
>[!NOTE]
>Kell mindkét csomóponton, hajtsa végre a lépéseket, hogy a yast2 grafikus nézetet a mindkét csomópont érheti el.

![yast-sofwaremanagement.png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

Függőségek, válassza a "Ajánlott csomagok telepítése" ![yast-dependencies.png](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Tekintse át a módosításokat, és kattintson az OK gombra

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

Csomag telepítési bevételből ![yast végrehajtása installation.png](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Kattintson a Next (Tovább) gombra.

![yast-installation-report.png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Kattintson a Befejezés gombra

Emellett telepítenie kell a libqt4 és libyui-qt csomagokat.
```
zypper -n install libqt4
```
![zypper-install-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-telepítés – ligyui.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![zypper-telepítés – ligyui_part2.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) Yast2 itt nyissa meg a grafikus nézetet, mostantól látható módon képesnek kell lennie.
![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>3. forgatókönyv: yast2 does nem magas rendelkezésre állási lehetőségek
A magas rendelkezésre állású beállítás lesznek láthatók a yast2 vezérlőközpont a további csomagokat telepítenie kell.

Yast2 használatával > Software > szoftverkezelés > Válassza ki a következő minták

- Alap SAP HANA-kiszolgáló
- C/C++ fordítóprogram- és eszközök
- Magas rendelkezésre állás
- Alap SAP-alkalmazáskiszolgáló

A következő képernyőt jeleníti meg a lépéseket a minták telepítéséhez.

Yast2 használatával > Software > szoftverkezelés

![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Válassza ki a minták

![yast-pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast-pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Kattintson a **elfogadása**

![yast-changed-packages.png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Kattintson a **folytatása**

![yast2-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Kattintson a **tovább** amikor a telepítés befejeződött

![yast2-installation-report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>4. forgatókönyv: HANA telepítése meghiúsul a gcc-szerelvények hiba
A HANA telepítése meghiúsul a következő hiba miatt.

![Hana-installation-error.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

A probléma elhárításához kódtárak telepítése kell (libgcc_sl és libstdc ++ 6), a következőket.

![zypper-install-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>5. forgatókönyv: Támasztja szolgáltatás meghiúsul

A következő hiba történt a támasztja szolgáltatás indítása során.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

A javításhoz törölni a következő sort a fájl */usr/lib/systemd/system/fstrim.timer*

```
Persistent=true
```

![Persistent.png](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>6. példa: Nem lehet csatlakozni a fürthöz a 2. csomópont

Ha a csomópont2 csatlakoztatása a meglévő fürt használatával *magas rendelkezésre állás fürt-illesztési* parancsot, a következő hiba történt.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![ha-cluster-join-error.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Javítsa ki, futtassa a következő mindkét csomóponton

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![ssh-keygen-node1.PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![ssh-keygen-node2.PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

A fenti javítás után csomópont2 kell hozzáadja a fürthöz

![ha-cluster-join-fix.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Általános dokumentáció
A következő cikkekben található további információ a SUSE magas rendelkezésre ÁLLÁS telepítési: 

- [Az SAP HANA SR teljesítményre optimalizált forgatókönyv](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Storage-alapú szintaxiskiemeléshez](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blog – SAP HANA - rész 1 támasztja fürt használatával](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog – SAP HANA - rész 2 támasztja fürt használatával](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
