---
title: Magas rendelkezésre állású beállítás a STONITH sap HANA az Azure-ban (nagy példányok)| Microsoft dokumentumok
description: Magas rendelkezésre állás létrehozása az SAP HANA számára az Azure-ban (nagy példányok) a SUSE-ban a STONITH használatával
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4060dbe936af8ff1f9dd8c958f64834cb06525de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77615090"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Magas rendelkezésre állás beállítása SUSE rendszeren a STONITH használatával
Ez a dokumentum részletes, lépésenkénti útmutatót tartalmaz a SUSE operációs rendszer magas rendelkezésre állásának beállításához a STONITH eszköz használatával.

**Jogi nyilatkozat:** *Ez az útmutató származik a telepítés tesztelésével a Microsoft HANA nagy példányok környezetben, amely sikeresen működik. Mivel a Microsoft service Management csapata a HANA nagypéldányok esetében nem támogatja az operációs rendszert, előfordulhat, hogy az operációs rendszer rétegével kapcsolatos további hibaelhárításvagy pontosítás érdekében kapcsolatba kell lépnie a SUSE-rel. A Microsoft szolgáltatáskezelő csapata beállította a STONITH eszközt, és teljes mértékben támogatja és részt vehet a STONITH-eszközökkel kapcsolatos problémák elhárításában.*
## <a name="overview"></a>Áttekintés
A SUSE-fürtözés magas rendelkezésre állásának beállításához a következő előfeltételeknek kell megfelelniük.
### <a name="pre-requisites"></a>Előfeltételek
- Hana nagy példányok vannak kiépítve
- Az operációs rendszer regisztrálva van
- A HANA nagy példányok kiszolgálói az SMT-kiszolgálóhoz csatlakoznak a javítások/csomagok lefogadásához
- Az operációs rendszer legújabb javításait telepítette
- Az NTP (időkiszolgáló) be van állítva
- A SUSE dokumentációjának legújabb verziójának elolvasása és megértése a HA beállításkor

### <a name="setup-details"></a>A beállítás részletei
Ez az útmutató a következő beállításokat használja:
- Operációs rendszer: SLES 12 SP1 SAP-hoz
- HANA nagy példányok: 2xS192 (négy foglalat, 2 TB)
- HANA verzió: HANA 2.0 SP1
- Kiszolgálónevek: sapprdhdb95 (node1) és sapprdhdb96 (node2)
- STONITH eszköz: iSCSI alapú STONITH eszköz
- NTP beállítása a HANA nagy példánycsomópontjainak egyikén

Amikor a HANA nagy példányokat hsr-rel állítja be, kérheti a Microsoft Service Management csapatát a STONITH beállítására. Ha ön már egy meglévő ügyfél, aki hana nagy példányok kiépített, és szükség Van STONITH eszköz beállítása a meglévő blades, meg kell adnia a következő információkat a Microsoft Service Management csapat a szolgáltatáskérelem űrlap (SRF). Az SRF-űrlapot a Technikai fiókkezelőn vagy a HANA nagypéldány-bevezetéshez felelős Microsoft-kapcsolattartón keresztül kérheti. Az új ügyfelek a kiépítés időpontjában stonith eszközt kérhetnek. A bemenetek a kiépítési kérelem képernyőn érhetők el.

- Kiszolgáló neve és kiszolgáló IP-címe (például myhanaserver1, 10.35.0.1)
- Hely (például US East)
- Vevő neve (például Microsoft)
- SID - HANA rendszerazonosító (például H11)

A STONITH-eszköz konfigurálása után a Microsoft Service Management csapata megadja az iSCSI-tároló SBD-eszköznevét és IP-címét, amellyel konfigurálhatja a STONITH telepítését. 

A STONITH használatával a HA végpontig történő beállításához a következő lépéseket kell követni:

1.  Az SBD-eszköz azonosítása
2.  Az SBD eszköz inicializálása
3.  A fürt konfigurálása
4.  Beállítása a Softdog Watchdog
5.  A csomópont csatlakoztatása a fürthöz
6.  A fürt ellenőrzése
7.  Az erőforrások konfigurálása a fürthöz
8.  A feladatátvételi folyamat tesztelése

## <a name="1---identify-the-sbd-device"></a>1. Azonosítsa az SBD eszközt
Ez a szakasz azt ismerteti, hogyan határozható meg a telepítő SBD-eszköze, miután a Microsoft szolgáltatáskezelő csapata konfigurálta az STONITH-ot. **Ez a szakasz csak a meglévő vevőre vonatkozik.** Ha Ön új ügyfél, a Microsoft szolgáltatáskezelő csapata megadja az SBD-eszköz nevét, és kihagyhatja ezt a szakaszt.

1.1 Módosítsa *az /etc/iscsi/initiatorname.isci* 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

A Microsoft szolgáltatáskezelése biztosítja ezt a karakterláncot. Módosítsa a fájlt **mindkét** csomóponton, de a csomópontszáma minden csomóponton eltérő.

![kezdeményezőnév.png](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 Az */etc/iscsi/iscsid.conf*módosítása : *Node.session.timeo.replacement_timeout=5* és *node.startup = automatikus*beállítása. Módosítsa a fájlt **mindkét** csomóponton.

1.3 A felderítési parancs végrehajtása, négy munkamenetet mutat. Futtasd le mindkét csomóponton.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 A parancs végrehajtása az iSCSI-eszközre való bejelentkezéshez négy munkamenetet jelenít meg. Futtasd **le mindkét** csomóponton.

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 Az újrascan parancsfájl végrehajtása: *rescan-scsi-bus.sh*.  Ez a parancsfájl az Ön számára létrehozott új lemezeket jeleníti meg.  Futtasd le mindkét csomóponton. A nulla-nál nagyobb LUN-számnak (például: 1, 2 stb.)

```
rescan-scsi-bus.sh
```
![rescanscsibus.png](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6 Az eszköz nevének bevéhez futtassa az *fdisk –l*parancsot. Futtasd le mindkét csomóponton. Válassza ki a készüléket a mérete **178 MiB**.

```
  fdisk –l
```

![fdisk-l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2. Az SBD eszköz inicializálása

2.1 Az SBD eszköz inicializálása **mindkét** csomóponton

```
sbd -d <SBD Device Name> create
```
![sbdcreate.png](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 Ellenőrizze, hogy mi van írva a készülékre. Csináld **mindkét** csomóponton

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3. A fürt konfigurálása
Ez a szakasz a SUSE HA-fürt beállításának lépéseit ismerteti.
### <a name="31-package-installation"></a>3.1 A csomag telepítése
3.1.1 Ellenőrizze, hogy ha_sles és SAPHanaSR-doc minták vannak-e telepítve. Ha nincs telepítve, telepítse őket. Telepítse **mindkét** csomópontra.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.png](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpattern](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 A fürt beállítása
3.2.1 Használhatja a *ha-cluster-init* parancsot, vagy használhatja a yast2 varázslót a fürt beállításához. Ebben az esetben a yast2 varázslót használja a használat. Ezt a lépést **csak az Elsődleges csomóponton**hajthatja végre.

Kövesse yast2> Magas ![rendelkezésre állású >](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![Cluster yast-control-center.png yast-hawk-install.png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Kattintson a **Mégse gombra,** mivel a halk2 csomag már telepítve van.

![yast-hawk-continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Kattintson a **Folytatás gombra.**

Várt érték=A telepített csomópontok száma (ebben az ![esetben 2) yast-Cluster-Security.png](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) Kattintson a **Következő**
![yast-cluster-configure-csync2.png](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) Csomópontnevek hozzáadása elemre, majd a "Javasolt fájlok hozzáadása" gombra

Kattintson a "Csync2 BE bekapcsolása" gombra

Kattintson a "Generate Pre-Shared-Keys", ez alatt jelenik meg felugró

![yast-key-file.png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Kattintson **az OK gombra**

A hitelesítés a Csync2 IP-címei és előmegosztott kulcsai használatával történik. A kulcsfájl csync2 -k /etc/csync2/key_hagroup.The key file is generated with csync2 -k /etc/csync2/key_hagroup. A key_hagroup fájlt a létrehozásután manuálisan kell átmásolni a fürt összes tagjára. **Győződjön meg arról, hogy a fájlt az 1-es csomópontról a csomópont2-be másolja.**

![yast-cluster-conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Kattintson a **Tovább**
![yast-cluster-service.png gombra.](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

Az alapértelmezett beállítás, Booting ki volt kapcsolva, változtassa meg a "on", így pacemaker indul a boot. A beállítási követelmények alapján választhat.
Kattintson a **Tovább** gombra, és a fürtkonfiguráció befejeződött.

## <a name="4---setting-up-the-softdog-watchdog"></a>4. Beállítása a Softdog Watchdog
Ez a szakasz a figyelő (softdog) konfigurációját ismerteti.

4.1 Adja hozzá a következő sort az */etc/init.d/boot.local* **könyvtárhoz mindkét** csomóponton.
```
modprobe softdog
```
![modprobe-softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 Frissítse az */etc/sysconfig/sbd* fájlt **mindkét** csomóponton a következőképpen:
```
SBD_DEVICE="<SBD Device Name>"
```
![sbd-eszköz.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 Töltse be a kernel modult **mindkét** csomópontra a következő parancs futtatásával
```
modprobe softdog
```
![modprobe-softdog-command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4 Ellenőrizze és győződjön meg arról, hogy softdog fut a következő **mindkét** csomóponton:
```
lsmod | grep dog
```
![lsmod-grep-dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 Indítsa el az SBD eszközt **mindkét** csomóponton
```
/usr/share/sbd/sbd.sh start
```
![sbd-sh-start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 Tesztelje az SBD démont **mindkét** csomóponton. Két bejegyzés jelenik meg, miután **mindkét** csomóponton konfigurálta
```
sbd -d <SBD Device Name> list
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 Tesztüzenet küldése **az egyik** csomópontra
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 A **második** csomóponton (2. csomópont) ellenőrizheti az üzenet állapotát
```
sbd  -d <SBD Device Name> list
```
![sbd-list-message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 Az sbd config elfogadásához frissítse az */etc/sysconfig/sbd* fájlt a következőképpen. A fájl frissítése **mindkét** csomóponton
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10 Indítsa el a pacemaker szolgáltatást az **Elsődleges csomóponton** (1 csomópont)
```
systemctl start pacemaker
```
![start-pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Ha a pacemaker szolgáltatás *meghibásodik*, lásd *az 5.*

## <a name="5---joining-the-cluster"></a>5. A fürt összekötése
Ez a szakasz a csomópont fürthöz való csatlakoztatásának módját ismerteti.

### <a name="51-add-the-node"></a>5.1 A csomópont hozzáadása
Futtassa a következő parancsot a **csomópont2-en,** hogy node2 csatlakozzon a fürthöz.
```
ha-cluster-join
```
Ha a fürthöz való csatlakozáskor *hibaüzenet* jelenik meg, olvassa el *a 6.*

## <a name="6---validating-the-cluster"></a>6. A fürt érvényesítése

### <a name="61-start-the-cluster-service"></a>6.1 A fürtszolgáltatás indítása
A fürt ellenőrzése és szükség esetén indítása **mindkét** csomóponton.
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl-status-pacemaker.png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 Állapot ának figyelemmel kísérése
Futtassa a *parancsot crm_mon,* hogy **mindkét** csomópont online állapotban legyen. Futtathatja a fürt **bármelyik csomópontján**
```
crm_mon
```
![crm-mon.png](media/HowToHLI/HASetupWithStonith/crm-mon.png) A fürt állapotát *https://\<csomópont IP>:7630*https:// is bejelentkezhet. Az alapértelmezett felhasználó hacluster és a jelszó linux. Szükség esetén módosíthatja a jelszót a *passwd* paranccsal.

## <a name="7-configure-cluster-properties-and-resources"></a>7. Fürttulajdonságainak és erőforrásainak konfigurálása 
Ez a szakasz a fürterőforrások konfigurálásának lépéseit ismerteti.
Ebben a példában állítsa be a következő erőforrást, a többi konfigurálható (ha szükséges) hivatkozva a SUSE HA útmutató. Csak az **egyik csomóponton hajtsa végre a konfigurációt.** Teendő az elsődleges csomóponton.

- Fürtrendszerindítás
- STONITH eszköz
- A virtuális IP-cím


### <a name="71-cluster-bootstrap-and-more"></a>7.1 Cluster bootstrap és így tovább
Fürtrendszerindítás hozzáadása. Hozza létre a fájlt, és adja hozzá a szöveget a következőképpen:
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
Adja hozzá a konfigurációt a fürthöz.
```
crm configure load update crm-bs.txt
```
![crm-configure-crmbs.png](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7.2 STONITH készülék
Erőforrás hozzáadása STONITH. Hozza létre a fájlt, és adja hozzá a szöveget a következőképpen.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15"
```
Adja hozzá a konfigurációt a fürthöz.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7.3 A virtuális IP-cím
Erőforrás virtuális IP hozzáadása. Hozza létre a fájlt, és adja hozzá a szöveget az alábbiak szerint.
```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
Adja hozzá a konfigurációt a fürthöz.
```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7.4 A források érvényesítése

A parancs *crm_mon*futtatásakor a két erőforrás látható.
![crm_mon_command.png](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Az állapot ot *https://\<csomópont IP-címén is láthatja>:7630/cib/live/state*

![hawlk-status-page.png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. A feladatátvételi folyamat tesztelése
A feladatátvételi folyamat teszteléséhez állítsa le a pacemaker szolgáltatás csomóponton1, és az erőforrások feladatátvétel a csomópont2.
```
Service pacemaker stop
```
Most állítsa le a pacemaker szolgáltatást a **csomópont2-en,** és az erőforrások átjutottak a **csomópontra1**

**Feladatátvétel előtt**  
![Feladatátvétel előtt.png](media/HowToHLI/HASetupWithStonith/Before-failover.png)  

**Feladatátvétel után**  
![feladat-átvétel után.png](media/HowToHLI/HASetupWithStonith/after-failover.png)  
![crm-mon-after-failover.png](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  


## <a name="9-troubleshooting"></a>9. Hibaelhárítás
Ez a szakasz a néhány hibaforgatókönyvet ismerteti, amelyek a telepítés során előfordulhatnak. Lehet, hogy nem feltétlenül szembesülnek ezekkel a kérdésekkel.

### <a name="scenario-1-cluster-node-not-online"></a>1. forgatókönyv: A fürtcsomópont nincs online állapotban
Ha a csomópontok bármelyike nem jelenik meg online a fürtkezelőben, megpróbálhatja a következőt követni, hogy online állapotba hozza.

Az iSCSI-szolgáltatás indítása
```
service iscsid start
```

És most már képesnek kell lennie arra, hogy jelentkezzen be, hogy az iSCSI-csomópont
```
iscsiadm -m node -l
```
A várt kimenet a következő
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
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>2. forgatókönyv: a yast2 nem jeleníti meg a grafikus nézetet
A yast2 grafikus képernyő segítségével állítsa be a magas rendelkezésre állású fürt ebben a dokumentumban. Ha a yast2 nem nyílik meg a grafikus ablakkal, ahogy az látható, és dobja a Qt hibát, hajtsa végre a lépéseket a következőképpen. Ha a grafikus ablakkal nyílik meg, kihagyhatja a lépéseket.

**Hiba**

![yast2-qt-gui-error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Várt kimenet**

![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Ha a yast2 nem nyílik meg a grafikus nézettel, kövesse az alábbi lépéseket.

Telepítse a szükséges csomagokat. Be kell jelentkeznie, mint a felhasználó "root", és smt létre, hogy töltse le / telepítse a csomagokat.

A csomagok telepítéséhez használja a yast>Software>Software Management>Dependencies> "Ajánlott csomagok telepítése..." opciót. A következő képernyőkép a várt képernyőket mutatja be.
>[!NOTE]
>Mindkét csomóponton végre kell hajtania a lépéseket, hogy mindkét csomópontról elérhesse a yast2 grafikus nézetet.

![yast-sofwaremanagement.png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

A Függőségek csoportban válassza az !["Ajánlott csomagok telepítése" yast-dependencies.png lehetőséget.](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Tekintse át a módosításokat, és nyomja meg az OK gombot

![Yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

A csomag ![telepítése folytatódik yast-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Kattintson a Next (Tovább) gombra.

![yast-telepítés-report.png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Kattintson a Befejezés gombra.

Azt is telepítenie kell a libqt4 és libyui-qt csomagokat.
```
zypper -n install libqt4
```
![zypper-install-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-install-ligyui.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![zypper-install-ligyui_part2.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) Yast2 képesnek kell lennie arra, hogy nyissa meg a grafikus nézet most, mint itt látható.
![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>3. forgatókönyv: a yast2 nem rendelkezik magas rendelkezésre állási lehetőséggel
Ahhoz, hogy a Magas rendelkezésre állás beállítás látható legyen a yast2 vezérlőközpontban, telepítenie kell a további csomagokat.

A Yast2>szoftver->szoftverkezelési>a következő minták kiválasztása

- SAP HANA-kiszolgáló alapja
- C/C++ fordító és eszközök
- Magas rendelkezésre állás
- SAP-alkalmazáskiszolgáló alapja

A következő képernyőn látható a minták telepítésének lépései.

A yast2 > szoftver> szoftverkezelés használata

![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

A minták kiválasztása

![yast-pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast-pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Kattintson **az Elfogadás gombra.**

![yast-megváltozott-csomagok.png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Kattintson a **Folytatás gombra.**

![yast2-teljesítő-installation.png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

A telepítés befejeztével kattintson a **Tovább** gombra

![yast2-telepítés-report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>4. forgatókönyv: A HANA-telepítés sikertelen a gcc-szerelvények hibájával
A HANA telepítése a következő hibával sikertelen.

![Hana-install-error.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

A probléma megoldásához telepítenie kell a könyvtárakat (libgcc_sl és a libstdc++6) az alábbiak szerint.

![zypper-install-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>5. forgatókönyv: A pacemaker szolgáltatás meghibásodik

A következő hiba történt a pacemaker szolgáltatás indítása közben.

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

A javításhoz törölje a következő sort a */usr/lib/systemd/system/fstrim.timer fájlból.*

```
Persistent=true
```

![Persistent.png](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>6. forgatókönyv: A 2.

Amikor a csomópont2-t a *ha-cluster-join* paranccsal csatlakozik a meglévő fürthöz, a következő hiba történt.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![ha-cluster-join-error.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

A javításhoz futtassa a következőket mindkét csomóponton

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![ssh-keygen-csomópont1. Png](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![ssh-keygen-csomópont2. Png](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Az előző javítás után a node2-t hozzá kell adni a fürthöz

![ha-cluster-join-fix.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Általános dokumentáció
A SUSE HA beállításáról az alábbi cikkekben talál további információt: 

- [SAP HANA SR teljesítmény optimalizált forgatókönyv](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Tároláson alapuló kerítés](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blog - Pacemaker Cluster használata az SAP HANA-1.](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog - Pacemaker Cluster használata az SAP HANA-2.](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
