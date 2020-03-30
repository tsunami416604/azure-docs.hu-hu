---
title: Magas rendelkezésre állás az NFS számára az SLES-en lévő Azure-beli virtuális gépeken | Microsoft dokumentumok
description: Magas rendelkezésre állás az NFS számára az Azure-beli virtuális gépeken a SUSE Linux Enterprise Server rendszeren
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/26/2020
ms.author: radeltch
ms.openlocfilehash: 4dce0a675f5841591da00a322b72718964d382ac
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348874"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Magas rendelkezésre állás az NFS számára az Azure-beli virtuális gépeken a SUSE Linux Enterprise Server rendszeren

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sles-hae-guides]:https://www.suse.com/documentation/sle-ha-12/
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

Ez a cikk ismerteti, hogyan telepítheti a virtuális gépeket, konfigurálhatja a virtuális gépeket, telepítheti a fürtkeretrendszert, és hogyan telepíthet egy magas rendelkezésre állású NFS-kiszolgálót, amely egy magas rendelkezésre állású SAP-rendszer megosztott adatainak tárolására használható.
Ez az útmutató bemutatja, hogyan lehet beállítani egy magas rendelkezésre állású NFS-kiszolgálót, amelyet két SAP-rendszer, az NW1 és az NW2 használ. A példában szereplő erőforrások (például virtuális gépek, virtuális hálózatok) neve feltételezi, hogy az [SAP fájlkiszolgáló sablont][template-file-server] az **prod**erőforráselőtaggal használta.

Olvassa el először a következő SAP-megjegyzéseket és dokumentumokat

* Az SAP Note [1928533,]amely:
  * Az SAP-szoftverek telepítéséhez támogatott Azure virtuális gépméretek listája
  * Fontos kapacitásadatok az Azure virtuális gépek méretéhez
  * Támogatott SAP szoftverek, operációs rendszer (OS) és adatbázis-kombinációk
  * Szükséges SAP kernel verzió Windows és Linux rendszeren a Microsoft Azure-ban

* Az SAP Note [2015553] felsorolja az SAP által támogatott SAP-szoftvertelepítések előfeltételeit az Azure-ban.
* Az SAP Note [2205917] ajánlott operációs rendszer-beállításokat a SUSE Linux Enterprise Server for SAP alkalmazásokhoz
* Az SAP Note [1944799] SAP HANA irányelvei tanoncasap Linux Enterprise Server sap alkalmazásokhoz
* Az SAP Note [2178632] részletes információkat tartalmaz az Azure-ban az SAP-hoz jelentett összes figyelési metrikáról.
* Az SAP Note [2191498] rendelkezik a szükséges SAP Host Agent linuxos verzióval az Azure-ban.
* Az SAP Note [2243692] információkat tartalmaz az Sap-licencelésről az Azure-ban.
* Az SAP Note [1984787] általános információkat tartalmaz a SUSE Linux Enterprise Server 12-ről.
* Az SAP Note [1999351] további hibaelhárítási információkat tartalmaz az SAP-hoz kiadott Azure továbbfejlesztett figyelési bővítményhez.
* [Az SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) minden szükséges SAP-jegyzetet igényel Linuxhoz.
* [Az Azure virtuális gépek tervezése és megvalósítása az SAP-hoz Linuxon][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP Linux on Linux (ez a cikk)][deployment-guide]
* [Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz Linuxon][dbms-guide]
* [A SUSE Linux Enterprise High Availability Extension 12 SP3 gyakorlati tanácsok – útmutatók][sles-hae-guides]
  * Magas rendelkezésre állású NFS-tároló DRBD-vel és pacemakerrel
* [A SUSE Linux Enterprise Server for SAP Applications 12 SP3 gyakorlati tanácsok útmutatói][sles-for-sap-bp]
* [SUSE high availability Extension 12 SP3 kiadási megjegyzések][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Áttekintés

A magas rendelkezésre állás eléréséhez az SAP NetWeaver nfs-kiszolgálót igényel. Az NFS-kiszolgáló egy külön fürtben van konfigurálva, és több SAP-rendszer is használható.

![SAP NetWeaver magas rendelkezésre állás – áttekintés](./media/high-availability-guide-nfs/ha-suse-nfs.png)

Az NFS-kiszolgáló dedikált virtuális állomásnevet és virtuális IP-címeket használ minden OLYAN SAP-rendszerhez, amely ezt az NFS-kiszolgálót használja. Az Azure-ban egy terheléselosztó szükséges a virtuális IP-cím használatához. Az alábbi lista a terheléselosztó konfigurációját mutatja be.        

* Előtér-konfiguráció
  * 10.0.0.4 IP-cím NW1 esetén
  * 10.0.0.5 IP-cím NW2 esetén
* Háttérrendszer-konfiguráció
  * Az NFS-fürt részét alkotó összes virtuális gép elsődleges hálózati interfészeihez csatlakoztatva
* Szonda port
  * 61000-es port NW1-hez
  * 61001-es port NW2-hez
* Terheléselosztási szabályok (alapvető terheléselosztó használata esetén)
  * 2049 TCP NW1-hez
  * 2049 UDP az NW1-hez
  * 2049 TCP AZ NW2-hez
  * 2049 UDP az NW2-hez

## <a name="set-up-a-highly-available-nfs-server"></a>Magas rendelkezésre állású NFS-kiszolgáló beállítása

Használhatja a GitHub Azure-sablonját az összes szükséges Azure-erőforrás üzembe helyezéséhez, beleértve a virtuális gépeket, a rendelkezésre állási készletet és a terheléselosztót, vagy manuálisan is telepítheti az erőforrásokat.

### <a name="deploy-linux-via-azure-template"></a>Linux telepítése az Azure-sablonon keresztül

Az Azure Marketplace tartalmaz egy lemezképet a SUSE Linux Enterprise Server for SAP Applications 12 számára, amely új virtuális gépek üzembe helyezéséhez használható.
A GitHub egyik rövid útmutató sablonjával üzembe helyezheti az összes szükséges erőforrást. A sablon telepíti a virtuális gépeket, a terheléselosztót, a rendelkezésre állási készletet stb. A sablon üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Az [SAP-fájlkiszolgálósablon megnyitása][template-file-server] az Azure Portalon   
1. Adja meg a következő paramétereket
   1. Erőforrás-előtag  
      Adja meg a használni kívánt előtagot. Az érték az üzembe helyezett erőforrások előtagjaként használatos.
   2. SAP-rendszer száma  
      Adja meg a fájlkiszolgálót használó SAP-rendszerek számát. Ez telepíti a szükséges mennyiségű előtér-konfigurációk, terheléselosztási szabályok, mintavételi portok, lemezek stb.
   3. Os típus  
      Válassza ki az egyik Linux disztribúciók. Ebben a példában válassza az SLES 12
   4. Rendszergazdai felhasználónév és rendszergazdai jelszó  
      Új felhasználó jön létre, amely a számítógépre való bejelentkezéshez használható.
   5. Alhálózati azonosító  
      Ha azt szeretné, hogy a virtuális gép egy meglévő virtuális hálózat, ahol a virtuális gép definiált alhálózat a virtuális gép kell hozzárendelni, nevezze el az adott alhálózat azonosítóját. Az azonosító általában úgy néz ki, mint a /subscription/**&lt;&gt;subscription ID**/resourceGroups/**&lt;erőforráscsoport neve&gt;**/providers/Microsoft.Network/virtualNetworks/**&lt;virtuális hálózat neve&gt;**/alhálózatok/**&lt;alhálózat neve&gt; **

### <a name="deploy-linux-manually-via-azure-portal"></a>Linux manuális üzembe helyezése az Azure Portalon keresztül

Először létre kell hoznia a virtuális gépeket ehhez az NFS-fürthöz. Ezután hozzon létre egy terheléselosztót, és használja a háttérkészletekben lévő virtuális gépeket.

1. Erőforráscsoport létrehozása
1. Virtuális hálózat létrehozása
1. Rendelkezésre állási készlet létrehozása  
   Maximális frissítési tartomány beállítása
1. Virtuális gép létrehozása 1 Használja legalább SLES4SAP 12 SP3, ebben a példában az SLES4SAP 12 SP3 BYOS image SLES SAP alkalmazások 12 SP3 (BYOS) használják  
   Korábbian létrehozott elérhetőségi készlet kiválasztása  
1. Virtuális gép létrehozása 2 Használja legalább SLES4SAP 12 SP3, ebben a példában az SLES4SAP 12 SP3 BYOS rendszerkép  
   SLES SAP alkalmazásokhoz 12 SP3 (BYOS) használatos  
   Korábbian létrehozott elérhetőségi készlet kiválasztása  
1. Adjon hozzá egy adatlemezt minden SAP-rendszerhez mindkét virtuális géphez.
1. Hozzon létre egy terheléselosztót (belső). Normál [terheléselosztót](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)ajánlunk.  
   1. A szabványos terheléselosztó létrehozásához kövesse az alábbi utasításokat:
      1. Előtér-IP-címek létrehozása
         1. 10.0.0.4 IP-cím NW1 esetén
            1. Nyissa meg a terheléselosztót, válassza az előtétes IP-készletet, és kattintson a Hozzáadás gombra
            1. Adja meg az új előtér-IP-készlet nevét (például **nw1-frontend**)
            1. Állítsa a hozzárendelést Statikusra, és adja meg az IP-címet (például **10.0.0.4**)
            1. Kattintson az OK gombra
         1. 10.0.0.5 IP-cím NW2 esetén
            * Ismételje meg a fenti lépéseket az NW2 esetében
      1. Háttérkészletek létrehozása
         1. Az NFS-fürt részét alkotó összes virtuális gép elsődleges hálózati interfészeihez csatlakoztatva
            1. Nyissa meg a terheléselosztót, jelölje ki a háttérkészleteket, és kattintson a Hozzáadás gombra
            1. Adja meg az új háttérkészlet nevét (például **nw-backend**)
            1. Virtuális hálózat kiválasztása
            1. Kattintson a Virtuális gép hozzáadása gombra.
            1. Válassza ki az NFS-fürt virtuális gépeit és IP-címeit.
            1. Kattintson az Add (Hozzáadás) parancsra.
      1. Az állapotminta-szondák létrehozása
         1. 61000-es port NW1-hez
            1. Nyissa meg a terheléselosztót, válassza ki az állapotmintakat, és kattintson a Hozzáadás gombra
            1. Adja meg az új állapotérzékelő nevét (például **nw1-hp**)
            1. Válassza a TCP protokollt, a 610**00-as**portot, az 5- es intervallumot és a nem megfelelő küszöbértéket 2
            1. Kattintson az OK gombra
         1. 61001-es port NW2-hez
            * Ismételje meg a fenti lépéseket, hogy hozzon létre egy egészségügyi szonda NW2
      1. Terheléselosztási szabályok
         1. Nyissa meg a terheléselosztót, válassza ki a terheléselosztási szabályokat, és kattintson a Hozzáadás gombra
         1. Adja meg az új terheléselosztó szabály nevét (például **nw1-lb**)
         1. Válassza ki a korábban létrehozott előtér-IP-címet, háttérkészletet és állapotmintát (például **nw1-előtér.** **nw-backend** és **nw1-hp**)
         1. Válassza a **HA portok lehetőséget**.
         1. Az tétlen időszám növelése 30 percre
         1. **A lebegő IP engedélyezése**
         1. Kattintson az OK gombra
         * Ismételje meg a fenti lépéseket az NW2 terheléselosztási szabályának létrehozásához
   1. Ha a forgatókönyv alapvető terheléselosztót igényel, kövesse az alábbi utasításokat:
      1. Előtér-IP-címek létrehozása
         1. 10.0.0.4 IP-cím NW1 esetén
            1. Nyissa meg a terheléselosztót, válassza az előtétes IP-készletet, és kattintson a Hozzáadás gombra
            1. Adja meg az új előtér-IP-készlet nevét (például **nw1-frontend**)
            1. Állítsa a hozzárendelést Statikusra, és adja meg az IP-címet (például **10.0.0.4**)
            1. Kattintson az OK gombra
         1. 10.0.0.5 IP-cím NW2 esetén
            * Ismételje meg a fenti lépéseket az NW2 esetében
      1. Háttérkészletek létrehozása
         1. Az NFS-fürt részét alkotó összes virtuális gép elsődleges hálózati interfészeihez csatlakoztatva
            1. Nyissa meg a terheléselosztót, jelölje ki a háttérkészleteket, és kattintson a Hozzáadás gombra
            1. Adja meg az új háttérkészlet nevét (például **nw-backend**)
            1. Kattintson a Virtuális gép hozzáadása gombra.
            1. A korábban létrehozott elérhetőségi készlet kiválasztása
            1. Az NFS-fürt virtuális gépeinek kiválasztása
            1. Kattintson az OK gombra
      1. Az állapotminta-szondák létrehozása
         1. 61000-es port NW1-hez
            1. Nyissa meg a terheléselosztót, válassza ki az állapotmintakat, és kattintson a Hozzáadás gombra
            1. Adja meg az új állapotérzékelő nevét (például **nw1-hp**)
            1. Válassza a TCP protokollt, a 610**00-as**portot, az 5- es intervallumot és a nem megfelelő küszöbértéket 2
            1. Kattintson az OK gombra
         1. 61001-es port NW2-hez
            * Ismételje meg a fenti lépéseket, hogy hozzon létre egy egészségügyi szonda NW2
      1. Terheléselosztási szabályok
         1. 2049 TCP NW1-hez
            1. Nyissa meg a terheléselosztót, válassza ki a terheléselosztási szabályokat, és kattintson a Hozzáadás gombra
            1. Adja meg az új terheléselosztó szabály nevét (például **nw1-lb-2049**)
            1. Válassza ki a korábban létrehozott előtér-IP-címet, háttérkészletet és állapotmintát (például **nw1-frontend**)
            1. TCP protokoll **megtartása**, adja meg a **2049-es portot**
            1. Az tétlen időszám növelése 30 percre
            1. **A lebegő IP engedélyezése**
            1. Kattintson az OK gombra
         1. 2049 UDP az NW1-hez
            * Ismételje meg a fenti lépéseket a 2049-es porthoz és az UDP-hez az NW1-hez
         1. 2049 TCP AZ NW2-hez
            * Ismételje meg a fenti lépéseket a 2049-es porthoz és a TCP-hez az NW2 esetében
         1. 2049 UDP az NW2-hez
            * Ismételje meg a fenti lépéseket a 2049-es porthoz és az UDP-hez az NW2-hez

> [!Note]
> Ha a nyilvános IP-címekkel nem rendelkező virtuális gépek a belső (nyilvános IP-cím nélküli) standard Azure-terheléselosztó háttérkészletébe kerülnek, nem lesz kimenő internetkapcsolat, kivéve, ha további konfigurációt hajt végre a nyilvános végpontok útválasztásának engedélyezéséhez. A kimenő kapcsolat eléréséről további információt a [nyilvános végpont-kapcsolat az Azure Standard Load Balancer használatával az SAP magas rendelkezésre állású forgatókönyvekben használó virtuális gépekhez.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)  

> [!IMPORTANT]
> Ne engedélyezze a TCP-időbélyegeket az Azure Load Balancer mögött elhelyezett Azure-beli virtuális gépeken. A TCP-időbélyegek engedélyezése az állapotminta sikertelensítését eredményezi. Állítsa a **net.ipv4.tcp_timestamps** paramétert **0-ra**. További részletek: [Terheléselosztó állapotminta.](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

### <a name="create-pacemaker-cluster"></a>Szívritmus-szabályozó fürt létrehozása

Kövesse a Pacemaker beállítása az [Azure-beli SUSE Linux Enterprise Server környezetben](high-availability-guide-suse-pacemaker.md) című lépéseit egy alapvető pacemaker-fürt létrehozásához ehhez az NFS-kiszolgálóhoz.

### <a name="configure-nfs-server"></a>NFS-kiszolgáló konfigurálása

A következő elemek előtaggal vannak ellátva **az [A]** - az összes csomópontra vonatkozóan, **[1]** - csak az 1- es vagy **a [2]** csomópontra vonatkozik, és csak a 2-es csomópontra vonatkoznak.

1. **[A]** Állomásnév feloldása

   Használhatja a DNS-kiszolgálót, vagy módosíthatja az /etc/hosts kapcsolót az összes csomóponton. Ez a példa az /etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és az állomásnevet a következő parancsokban

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   Szúrja be a következő sorokat az /etc/hosts könyvtárba. Az IP-cím és az állomásnév módosítása a környezetnek megfelelően
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]** NFS-kiszolgáló engedélyezése

   A gyökér NFS-exportálási bejegyzésének létrehozása

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]** Drbd-összetevők telepítése

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** Partíció létrehozása a drbd eszközök

   Az összes rendelkezésre álló adatlemez listázása

   <pre><code>sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Példa kimenetre
   
   ```
   lun0  lun1
   ```

   Partíciók létrehozása minden adatlemezhez

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]** LVM-konfigurációk létrehozása

   Az összes elérhető partíció felsorolása

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Példa kimenetre
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   LVM-kötetek létrehozása minden partícióhoz

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]** Drbd konfigurálása

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   Győződjön meg arról, hogy a drbd.conf fájl a következő két sort tartalmazza

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   A globális drbd konfiguráció módosítása

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Adja hozzá a következő bejegyzéseket a kezelőhöz és a hálószakaszhoz.

   <pre><code>global {
        usage-count no;
   }
   common {
        handlers {
             fence-peer "/usr/lib/drbd/crm-fence-peer.sh";
             after-resync-target "/usr/lib/drbd/crm-unfence-peer.sh";
             split-brain "/usr/lib/drbd/notify-split-brain.sh root";
             pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
        }
        startup {
             wfc-timeout 0;
        }
        options {
        }
        disk {
             md-flushes yes;
             disk-flushes yes;
             c-plan-ahead 1;
             c-min-rate 100M;
             c-fill-target 20M;
             c-max-rate 4G;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
             protocol     C;
             tcp-cork yes;
             max-buffers 20000;
             max-epoch-size 20000;
             sndbuf-size 0;
             rcvbuf-size 0;
        }
   }
   </code></pre>

1. **[A]** Az NFS drbd eszközök létrehozása

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Helyezze be az új drbd eszköz konfigurációját, és lépjen ki a

   <pre><code>resource <b>NW1</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   <pre><code>sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   Helyezze be az új drbd eszköz konfigurációját, és lépjen ki a

   <pre><code>resource <b>NW2</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   Hozza létre a drbd eszközt, és indítsa el

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]** A kezdeti szinkronizálás kihagyása

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Az elsődleges csomópont beállítása

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Várja meg, amíg az új drbd eszközök szinkronizálódnak

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]** Fájlrendszer létrehozása a drbd eszközökön

   <pre><code>sudo mkfs.xfs /dev/drbd0
   sudo mkdir /srv/nfs/NW1
   sudo chattr +i /srv/nfs/NW1
   sudo mount -t xfs /dev/drbd0 /srv/nfs/NW1
   sudo mkdir /srv/nfs/NW1/sidsys
   sudo mkdir /srv/nfs/NW1/sapmntsid
   sudo mkdir /srv/nfs/NW1/trans
   sudo mkdir /srv/nfs/NW1/ASCS
   sudo mkdir /srv/nfs/NW1/ASCSERS
   sudo mkdir /srv/nfs/NW1/SCS
   sudo mkdir /srv/nfs/NW1/SCSERS
   sudo umount /srv/nfs/NW1

   sudo mkfs.xfs /dev/drbd1
   sudo mkdir /srv/nfs/NW2
   sudo chattr +i /srv/nfs/NW2
   sudo mount -t xfs /dev/drbd1 /srv/nfs/NW2
   sudo mkdir /srv/nfs/NW2/sidsys
   sudo mkdir /srv/nfs/NW2/sapmntsid
   sudo mkdir /srv/nfs/NW2/trans
   sudo mkdir /srv/nfs/NW2/ASCS
   sudo mkdir /srv/nfs/NW2/ASCSERS
   sudo mkdir /srv/nfs/NW2/SCS
   sudo mkdir /srv/nfs/NW2/SCSERS
   sudo umount /srv/nfs/NW2
   </code></pre>

1. **[A]** Beállítás drbd osztott agy kimutatására

   Amikor a drbd segítségével szinkronizálja az adatokat az egyik gazdagépről a másikra, előfordulhat egy úgynevezett osztott agy. A megosztott agy egy olyan forgatókönyv, amelyben mindkét fürtcsomópont elősegítette a drbd-eszközt elsődlegesként, és kikerült a szinkronból. Lehet, hogy egy ritka helyzet, de még mindig szeretné kezelni, és megoldani a split agy a lehető leggyorsabban. Ezért fontos, hogy értesítést kapjunk, ha egy osztott agy történt.

   Olvassa el [a hivatalos drbd dokumentációt](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-split-brain-notification) arról, hogyan kell beállítani egy osztott agyi értesítést.

   Az is lehetséges, hogy automatikusan felépüljön egy osztott agyi forgatókönyv. További információkért olvassa el [az Automatikus osztott agyhelyreállítási házirendeket](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Fürtkeret rendszerkörnyezet konfigurálása

1. **[1]** Adja hozzá az NFS drbd eszközöket az SAP-rendszer NW1-hez a fürtkonfigurációhoz

   > [!IMPORTANT]
   > A legutóbbi tesztelés olyan helyzeteket tárt fel, amikor a netcat nem válaszol a kérelmekre a hátralék miatt, és csak egy kapcsolat kezelésének korlátozása miatt. A netcat erőforrás leállítja az Azure Load balancer kérelmek meghallgatását, és a lebegő IP elérhetetlenné válik.  
   > Meglévő Pacemaker klaszterek esetében korábban azt javasoljuk, hogy a netcat-et socat-ra cserélje. Jelenleg azt javasoljuk, hogy az azure-lb erőforrás-ügynök, amely része a csomag erőforrás-ügynökök, a következő csomag verziókövetelmények:
   > - Az SLES 12 SP4/SP5 esetében a verziónak legalább erőforrás-ügynököknek kell lennie-4.3.018.a7fb5035-3.30.1.  
   > - Az SLES 15/15 SP1 esetében a verziónak legalább erőforrás-ügynököknek kell lennie-4.3.0184.6ee15eb2-4.13.1.  
   >
   > Vegye figyelembe, hogy a módosítás rövid állásidőt igényel.  
   > Meglévő pacemaker-fürtök esetén, ha a konfiguráció már megváltozott socat használatára az [Azure Load-Balancer Detection Hardening című](https://www.suse.com/support/kb/doc/?id=7024128)dokumentumban leírtak szerint, nincs szükség arra, hogy azonnal váltson az azure-lb erőforrás-ügynökre.

   <pre><code>sudo crm configure rsc_defaults resource-stickiness="200"

   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW1</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW1</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW1</b>_nfs drbd_<b>NW1</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW1</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NW1</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive nfsserver systemd:nfs-server \
     op monitor interval="30s"
   sudo crm configure clone cl-nfsserver nfsserver

   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs azure-lb port=<b>61000</b>
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]** Adja hozzá az SAP-rendszer NW2 NFS drbd eszközeit a fürtkonfigurációhoz

   <pre><code># Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW2</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW2</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW2</b>_nfs drbd_<b>NW2</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW2</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/srv/nfs/<b>NW2</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW2</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs azure-lb port=<b>61001</b>
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

   A `crossmnt` lehetőség `exportfs` a fürt erőforrások jelen vannak a dokumentációban a régebbi SLES-verziókkal való visszamenőleges kompatibilitás érdekében.  

1. **[1]** Karbantartási mód letiltása
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>További lépések

* [Az SAP ASCS és az adatbázis telepítése](high-availability-guide-suse.md)
* [Az Azure virtuális gépek tervezése és megvalósítása az SAP-hoz][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP-hoz][deployment-guide]
* [Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz][dbms-guide]
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és tervezze meg az SAP HANA vészutáni helyreállítását az Azure virtuális gépeken, olvassa el [az SAP HANA magas rendelkezésre állását az Azure virtuális gépeken (VM-ek) című témakört.][sap-hana-ha]
