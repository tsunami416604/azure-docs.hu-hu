---
title: "Magas rendelkezésre állású STONITH SAP Hana Azure (nagy példányok) beállítása |} Microsoft Docs"
description: "Magas rendelkezésre állású SAP Hana Azure (nagy példányok) használatával a STONITH SUSE létrehozni"
services: virtual-machines-linux
documentationcenter: 
author: saghorpa
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d710fe24673c6ddc581d36e4f0cacdb750ff74f9
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2017
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Állítsa be a STONITH használatával SUSE a magas rendelkezésre állás
A dokumentum biztosít a részletes, lépésekre osztott utasításaiért beállítása a magas rendelkezésre állás az STONITH eszközt SUSE operációs rendszeren.

**Jogi nyilatkozat:** *Ez az útmutató a készlet teszteléséhez mentése sikeresen működik a Microsoft HANA nagy példányok környezetben származik. Microsoft szolgáltatás-felügyeleti csoport nagy HANA-példányok nem támogatja az operációs rendszer, mert szükség lehet további hibaelhárítási és az operációs rendszer réteg tisztázása SUSE kapcsolódni. Microsoft felügyeleti csoport STONITH eszköz beállítása és teljes mértékben támogatja, és tehetők STONITH eszközök kapcsolatos problémák elhárítása.*
## <a name="overview"></a>Áttekintés
Állítsa be a SUSE fürtszolgáltatás használata magas rendelkezésre állás, a következő előfeltételeknek kell megfelelnie.
### <a name="pre-requisites"></a>Előfeltételek
- HANA nagy példányok törlődnek.
- Regisztrálva van-e az operációs rendszer
- HANA nagy példányok kiszolgálók SMT server javítások/csomagok segítségével csatlakoznak
- Operációs rendszer van telepítve, a legújabb javítások
- NTP (kiszolgálót) be van állítva.
- Elolvastam és megértettem a legújabb SUSE dokumentációja beállítása magas rendelkezésre ÁLLÁSÚ

### <a name="set-up-details"></a>Részletek beállítása
- Ebben az útmutatóban használt állítsa be a következőt:
- Operációs rendszer: SLES 12 SP1 SAP
- HANA nagy példányok: 2xS192 (négy szoftvercsatornák, 2 TB)
- HANA-verzió: HANA 2.0 SP1
- Kiszolgálók nevei: sapprdhdb95 (1. csomópont) és sapprdhdb96 (2)
- STONITH eszköz: iSCSI alapú STONITH eszköz
- Állítsa be a HANA nagy példány csomópontok egyikén NTP

Nagy példányok HANA HSR beállításakor szolgáltatás vezetőség Microsoft STONITH beállítása is igényelhet. Ha már egy meglévő ügyfél, aki HANA nagy példányok üzembe helyezve, és szükség a meglévő paneleken beállítása STONITH eszköz rendelkezik, adja meg a következő szolgáltatás vezetőség Microsoft szolgáltatás-kérelmek formájában (SRF) adatokat szeretné. A műszaki Ügyfélfelelőshöz vagy a Microsoft Contact HANA nagy példány bevezetése SRF űrlap kérhet. Az új ügyfelek STONITH eszköz kérheti a kiépítés során. A bemeneti adatok érhetők el az üzembe helyezési űrlap.

- Kiszolgáló neve és a kiszolgáló IP-cím (például myhanaserver1, 10.35.0.1)
- Helyét (például amerikai keleti)
- Ügyfél neve (például Microsoft)
- SID - HANA rendszerazonosító (például H11)

Miután beállította a STONITH eszköz, Microsoft Service vezetőség adhat a SBD eszköz neve, és az iSCSI-tárolóhoz, STONITH konfigurálására használó IP-címének beállítása. 

A végpontok közötti STONITH használatával magas rendelkezésre ÁLLÁSÚ beállításához a következő lépéseket kell követni kell:

1.  A SBD eszköz azonosítására
2.  A SBD eszköz inicializálása
3.  A fürt konfigurálása
4.  A Softdog figyelő beállítása
5.  A csomópontok csatlakoztatása a fürt
6.  A fürt ellenőrzése
7.  Az erőforrások a fürt konfigurálása
8.  A feladatátvételi folyamat tesztelése

## <a name="1---identify-the-sbd-device"></a>1.   A SBD eszköz azonosítására
Ez a szakasz ismerteti, hogyan meghatározásához SBD a készlet mentése után a Microsoft service management csapatának van beállítva a STONITH. **Ez a szakasz csak a meglévő ügyfél vonatkozik**. Ha egy új ügyfél, a Microsoft felügyeleti csoport által nyújtott SBD eszköz neve, és hogy ez a szakasz kihagyhatja.

1.1 módosítása */etc/iscsi/initiatorname.isci* számára 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

A Microsoft service management adja meg ezt a karakterláncot. A következő fájl módosításához a **mindkét** a csomópontok, azonban a csomópont nem mindegyik csomópontján.

![initiatorname.png](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 módosítása */etc/iscsi/iscsid.conf*: beállítása *node.session.timeo.replacement_timeout=5* és *node.startup = automatikus*. A következő fájl módosításához a **mindkét** a csomópontok.

1.3 a felderítési parancs hajtható végre, négy munkamenetek mutatja. Futtassa a csomópontokon is.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4-es, hajtsa végre a parancsot próbál bejelentkezni az iSCSI-eszközhöz, négy munkamenetek mutatja. Futtassa **mindkét** a csomópontok.

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5-ös, hajtsa végre az újraellenőrzés parancsfájlt: *újraellenőrzés-scsi-bus.sh*.  A parancsfájl bemutatja, az új lemezek hozott létre.  Futtassa a csomópontokon is. Láthatja, hogy a logikai egység száma nagyobb, mint nulla (például: 1, 2 stb.)

```
rescan-scsi-bus.sh
```
![rescanscsibus.png](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6 a futtassa a parancsot az eszköznév *fdisk – l*. Futtassa a csomópontokon is. Válassza ki az eszközt az méretének **178 MiB**.

```
  fdisk –l
```

![Fdisk-l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2.   A SBD eszköz inicializálása

2.1 inicializálni az SBD eszköz **mindkét** a csomópontok

```
sbd -d <SBD Device Name> create
```
![sbdcreate.png](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 Ellenőrizze, hogy mi van írva az eszközre. A **mindkét** a csomópontok

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3.   A fürt konfigurálása
Ez a szakasz a SUSE magas rendelkezésre ÁLLÁSÚ fürt beállítása a lépéseit ismerteti.
### <a name="31-package-installation"></a>3.1 csomag telepítése
3.1.1 Ellenőrizze, hogy ha_sles és SAPHanaSR-doc minták vannak telepítve. Ha nincs telepítve, telepítse őket. Telepítse a **mindkét** a csomópontok.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.png](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR-doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 a fürt beállítása
3.2.1 használhatja *magas rendelkezésre állású fürt-inicializálás* parancsot, vagy a fürt beállítása a yast2 varázsló segítségével. Ebben az esetben yast2 varázslót használta azt. Ez a lépés végrehajtása **csak az elsődleges csomóponton lévő**.

Hajtsa végre a yast2 > magas rendelkezésre állású > fürt ![yast-vezérlő-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![yast-hawk-install.png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Kattintson a **Mégse** azt már a halk2 csomag telepítve van.

![yast-hawk-continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Kattintson a **folytatása**

Várt érték (ebben az esetben 2) telepített csomópontok száma = ![yast-fürt-Security.png](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) kattintson **következő**
![yast-fürt-konfigurálása-csync2.png](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) hozzáadása csomópont nevét, és kattintson az "javasolt fájlok hozzáadása"

Kattintson a "Bekapcsolása csync2"

Kattintson a "Shared kulcsok előtti létrehozásához.", azt jeleníti meg a helyi menü alatti

![yast-kulcs-file.png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Kattintson az **OK** gombra

A hitelesítés az IP-címek és előtti shared kulcsok használata az Csync2 történik. A kulcs fájlját a csync2 -k /etc/csync2/key_hagroup jön létre. A fájl key_hagroup kell másolni, hogy a fürt összes tagja manuális létrehozása után. **Ügyeljen arra, hogy a fájl másolása 1 csomópont csomópont2**.

![yast-fürt-conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Kattintson a **következő**
![yast-fürt-service.png](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

Az alapértelmezett beállítás rendszerindítása ki volt kapcsolva, módosítsa az "on", így támasztja rendszerindító elindult. A választás a beállított követelmények alapján végezheti el.
Kattintson a **következő** és a fürt konfigurálása nem fejeződött be.

## <a name="4---setting-up-the-softdog-watchdog"></a>4.   A Softdog figyelő beállítása
Ez a szakasz ismerteti a figyelő (softdog) konfigurációját.

4.1 adja a következő sort */etc/init.d/boot.local* a **mindkét** a csomópontok.
```
modprobe softdog
```
![modprobe-softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 a fájl frissítése */etc/sysconfig/sbd* a **mindkét** a csomópontok a következőként:
```
SBD_DEVICE="<SBD Device Name>"
```
![sbd-device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 a kernel modul betöltése a **mindkét** a csomópontokat a következő parancs futtatásával
```
modprobe softdog
```
![modprobe-softdog-command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4. Ellenőrizze, és győződjön meg arról, hogy softdog fut. a következőképpen **mindkét** a csomópontok:
```
lsmod | grep dog
```
![lsmod-grep-dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 a SBD eszköz indítsa el a **mindkét** a csomópontok
```
/usr/share/sbd/sbd.sh start
```
![sbd-sh-start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 a SBD démon tesztelése a **mindkét** a csomópontok. Két bejegyzés megjelenik után konfigurálja úgy a **mindkét** a csomópontok
```
sbd -d <SBD Device Name> list
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 a tesztüzenet küldése **egy** a csomópontok
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 a a **második** csomópont ellenőrizheti, hogy az üzenet állapota (2)
```
sbd  -d <SBD Device Name> list
```
![sbd-lista-message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 elfogadják a sbd config, módosítsa a fájlt */etc/sysconfig/sbd* következőként. A fájl frissítése a **mindkét** a csomópontok
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10 támasztja szolgáltatás indítása a **elsődleges csomópont** (1. csomópont)
```
systemctl start pacemaker
```
![Start-pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Ha a támasztja szolgáltatás *sikertelen*, tekintse meg *forgatókönyv 5: támasztja szolgáltatás sikertelen*

## <a name="5---joining-the-cluster"></a>5.   A fürt csatlakoztatása
Ez a szakasz ismerteti a csomópontok csatlakoztatása a fürt módjáról.

### <a name="51-add-the-node"></a>5.1 a csomópont hozzáadása
A következő parancsot **csomópont2** ahhoz, hogy a 2. csomópont csatlakoztatását a fürthöz.
```
ha-cluster-join
```
Ha megjelenik egy *hiba* közben csatlakozik, a fürtöt, tekintse meg a *forgatókönyv 6: nem lehet csatlakozni a fürthöz csomópont 2*.

## <a name="6---validating-the-cluster"></a>6.   A fürt érvényesítése

### <a name="61-start-the-cluster-service"></a>6.1, indítsa el a fürtszolgáltatást
Ellenőrizze, és opcionálisan indítsa el a fürt első alkalommal a **mindkét** csomópontok.
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl-állapot-pacemaker.png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 a állapotának figyelése
Futtassa a parancsot *crm_mon* biztosításához **mindkét** a csomópontja online állapotban. Futtathatja **a csomópontokon** a fürt
```
crm_mon
```
![CRM-mon.png](media/HowToHLI/HASetupWithStonith/crm-mon.png) akkor is bejelentkezhet a fürt állapotának hawk *https://<node IP>: 7630*. Az alapértelmezett felhasználói hacluster, és a jelszó linux. Ha szükséges, a jelszó használatával módosíthatja *passwd* parancsot.

## <a name="7-configure-cluster-properties-and-resources"></a>7. Fürt tulajdonságainak és erőforrásainak konfigurálása 
Ez a szakasz a fürterőforrásokat konfigurálásának lépéseit ismerteti.
Ebben a példában azt adta meg a következő erőforrást, a többi konfigurálhatja (ha szükséges) hivatkozik a SUSE magas rendelkezésre ÁLLÁSÚ útmutató. Hajtsa végre a config **egyik csomópontján** csak. Hajtsa végre az elsődleges csomóponton.

- Fürt rendszerindítási
- STONITH eszköz
- A virtuális IP-cím


### <a name="71-cluster-bootstrap-and-more"></a>7.1 a rendszerindítási és több fürt
Vegye fel a fürt rendszerindítási. Hozza létre a fájlt, és adja hozzá a szöveg következő:
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
![CRM konfigurálása crmbs.png](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7.2 STONITH eszköz
Vegyen fel STONITH erőforrást. Hozza létre a fájlt, és adja hozzá a szöveg következő.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15" \
op monitor interval="15" timeout="15"
```
A konfiguráció hozzáadása a fürthöz.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7.3 a virtuális IP-cím
Erőforrás virtuális IP-cím hozzáadása. Hozza létre a fájlt, és adja hozzá az alábbi szöveget.
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

### <a name="74-validate-the-resources"></a>7.4 érvényesíteni az erőforrások

A parancs futtatásakor *crm_mon*, láthatja, hogy a két erőforrásnak.
![crm_mon_command.png](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Ezenkívül megtekintheti a állapotát *https://<node IP address>: 7630/cib/élő/állapota*

![hawlk-állapot-page.png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. A feladatátvételi folyamat tesztelése
A feladatátvételi folyamat teszteléséhez állítsa le a támasztja szolgáltatást, az 1. csomópont, és az erőforrások feladatátvételének csomópont2.
```
Service pacemaker stop
```
Most, támasztja szolgáltatás leállítása **csomópont2** és erőforrások átadja a **csomópont1**

**Feladatátvétel előtt**
![előtt failover.png](media/HowToHLI/HASetupWithStonith/Before-failover.png)
**feladatátvételt követően**
![után failover.png](media/HowToHLI/HASetupWithStonith/after-failover.png)
 ![ CRM-f-után-failover.png](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)


## <a name="9-troubleshooting"></a>9. Hibaelhárítás
Ez a szakasz ismerteti a néhány hiba forgatókönyveket, amelyek a létrehozott terjedelme is. Előfordulhat, hogy nem feltétlenül szembesülhetnek ezeket a problémákat.

### <a name="scenario-1-cluster-node-not-online"></a>1. forgatókönyv: Fürtcsomópont nincs online állapotban
Ha a csomópontokon nem jeleníti meg a kezelő online, megpróbálhatja kapcsolása a következő.

Az iSCSI szolgáltatás elindítása
```
service iscsid start
```

Most kell tudni bejelentkezni az iSCSI csomópontot és
```
iscsiadm -m node -l
```
A várt kimeneti láthatóhoz hasonló
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
A yast2 grafikus képernyő segítségével állítsa be a magas rendelkezésre állású fürt ebben a dokumentumban. Ha yast2 grafikus ablak megnyitásához, és nem Qt hiba throw, hajtsa végre a következő lépéseket. A grafikus ablak nyílik meg, ha a lépéseket kihagyhatja.

**Hiba történt**

![yast2-qt-gui-error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Várt kimenet**

![yast-vezérlő-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

A yast2 a grafikus nézet nem nyitható meg, ha kövesse a lépéseket következő.

Telepítse a szükséges csomagokat. "Gyökér" felhasználóként kell bejelentkeznie, és rendelkezik SMT beállítva a csomagok letöltése vagy telepítése.

A csomagok telepítéséhez használja a yast > szoftver > szoftverkezelés > Függőségek > "A telepítés ajánlott csomagok..." lehetőséget. Az alábbi képernyőfelvételen a várt képernyők mutatja be.
>[!NOTE]
>Kell mindkét csomóponton, hajtsa végre a lépéseket, hogy mindkét csomópontjából yast2 grafikus nézetében végezheti el.

![yast-sofwaremanagement.png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

A függőségek, válassza a "Ajánlott csomagok telepítése" ![yast-dependencies.png](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Tekintse át a módosításokat, és kattintson az OK gombra

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

Csomag telepítési hibákra vonatkozó ![yast végrehajtása installation.png](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Kattintson a Tovább gombra

![yast-telepítés – report.png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Kattintson a Befejezés gombra

Szükség libqt4 és libyui-qt csomagok telepítését.
```
zypper -n install libqt4
```
![zypper-install-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-install-ligyui.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![zypper-install-ligyui_part2.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) Yast2 kell tudni megnyitni grafikus nézetként most látható itt.
![yast2-vezérlő-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>3. forgatókönyv: yast2 does nem magas rendelkezésre állási beállítása
A magas rendelkezésre állású lehetőség a yast2 control center látható legyen meg kell a további csomagok telepítése.

Yast2 használatával > szoftver > szoftverkezelés > Válassza ki a következő minták

- Alap SAP HANA-kiszolgáló
- C/C++ fordítóprogram- és eszközök
- Magas rendelkezésre állás
- Alap SAP-alkalmazáskiszolgáló

A következő képernyőn látható a minták telepítésének lépéseit.

Yast2 használatával > szoftver > szoftverkezelés

![yast2-vezérlő-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Válassza ki a minták

![yast-pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast-pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Kattintson a **elfogadása**

![yast megváltozott packages.png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Kattintson a **folytatása**

![yast2 végrehajtása installation.png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Kattintson a **következő** Ha a telepítés befejeződött

![yast2-telepítés – report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>4. forgatókönyv: Az HANA telepítési ÖET szerelvények hibaüzenettel meghiúsul.
A HANA telepítése a következő hiba miatt sikertelen.

![Hana-telepítés – error.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

A probléma megoldásához telepítendő függvénytárak (libgcc_sl és libstdc ++ 6), a következő.

![zypper-install-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>5. forgatókönyv: Támasztja szolgáltatás sikertelen

A következő probléma lépett fel a támasztja szolgáltatás indítása során.

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
-- Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
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

Oldhatja meg a problémát, törölje a következő sort a következő fájl */usr/lib/systemd/system/fstrim.timer*

```
Persistent=true
```

![Persistent.png](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>6. példa: Csomópont nem a fürthöz való csatlakoztatáshoz 2

Amikor a 2. csomópont csatlakoztatása a meglévő fürt használatával *magas rendelkezésre állású fürt-illesztési* parancsot a következő hiba történt.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![magas rendelkezésre állású-fürt-illesztési-error.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Javítsa ki, futtassa a következő mindkét csomóponton

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![ssh-keygen-1. csomópont. PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![ssh-keygen – 2. PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

A fenti javítás után csomópont2 kell hozzáadják a fürt

![magas rendelkezésre állású-fürt-illesztési-fix.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Általános dokumentáció
További információ a SUSE magas rendelkezésre ÁLLÁSÚ állítsa be a következő cikkekben talál: 

- [SAP HANA SR teljesítményre optimalizált forgatókönyv](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Storage-alapú kerítés](https://www.suse.com/documentation/sle-ha-2/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blog - támasztja fürtöt használ a SAP HANA - rész 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog - támasztja fürtöt használ a SAP HANA - rész 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)