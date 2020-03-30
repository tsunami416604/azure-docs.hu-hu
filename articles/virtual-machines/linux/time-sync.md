---
title: Linuxos virtuális gépek időszinkronizálása az Azure-ban
description: Időszinkronizálás Linux os virtuális gépekhez.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: c3571d9ba94e1803259457d473ed3f1669ea67ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330581"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Linuxos virtuális gépek időszinkronizálása az Azure-ban

Az időszinkronizálás fontos a biztonság és az eseménykorreláció érdekében. Néha elosztott tranzakciók megvalósításához használják. Több számítógépes rendszer közötti időpontosság szinkronizálással érhető el. A szinkronizálást több dolog is befolyásolhatja, például az újraindítások és a hálózati forgalom az időforrás és a számítógép időbetöltése között. 

Az Azure-t a Windows Server 2016 rendszert futtató infrastruktúra támogatja. A Windows Server 2016 továbbfejlesztett algoritmusokat használt az idő és a helyi óra állapotának javítására az UTC-vel való szinkronizáláshoz.  A Windows Server 2016 pontos idő szolgáltatása jelentősen javította azt, ahogyan a virtuális gépeket a gazdagépen irányító VMICTimeSync szolgáltatás a pontos idő érdekében szabályozza. A fejlesztések közé tartozik a virtuális gép indításakor vagy a virtuális gép visszaállítása és a késés megszakításának javítása pontosabb kezdeti idő. 

>[!NOTE]
>A Windows időszolgáltatás gyors áttekintéséhez tekintse meg ezt a [magas szintű áttekintő videót.](https://aka.ms/WS2016TimeVideo)
>
> További információt a [Windows Server 2016 pontos ideje](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)című témakörben talál. 

## <a name="overview"></a>Áttekintés

A számítógép órájának pontosságát annak a felmérnivalója, hogy a számítógép órája milyen közel van az egyezményes világidő (UTC) időszabványhoz. Az UTC-t a pontos atomórák ból álló multinacionális minta határozza meg, amely 300 év alatt csak egy másodperccel kapcsolható ki. De az UTC olvasása közvetlenül speciális hardvert igényel. Ehelyett az időkiszolgálók atutc-ra vannak szinkronizálva, és más számítógépekről érhetők el, hogy méretezhetőséget és robusztusságot biztosítsanak. Minden számítógépen van idő szinkronizálási szolgáltatás fut, amely tudja, hogy mikor szerverek használata, és rendszeresen ellenőrzi, ha a számítógép óráját kell javítani, és beállítja az időt, ha szükséges. 

Az Azure-állomások szinkronizálva vannak a Microsoft belső időkiszolgálóival, amelyek a Microsoft tulajdonában lévő Stratum 1 eszközökről, GPS-antennákból veszik igénybe az idejüket. Virtuális gépek az Azure-ban vagy függ a gazdagép adja át a pontos időt *(állomás idő*) a virtuális gép, vagy a virtuális gép közvetlenül időt kap egy időkiszolgáló, vagy a kettő kombinációja. 

Önálló hardveren a Linux operációs rendszer csak a gazdagép hardveróráját olvassa be a rendszerindításkor. Ezt követően az óra a Linux kernel megszakítási időzítőjével marad fenn. Ebben a konfigurációban az óra idővel elsodródik. Az Azure-beli újabb Linux-disztribúciókban a virtuális gépek a Linux-integrációs szolgáltatásokban (LIS) található VMICTimeSync-szolgáltatóval gyakrabban kérdezhetik le a gazdagép órafrissítéseit.

A virtuális gép és a gazdagép közötti interakciók hatással lehetnek az órára is. A [memória megőrzése során](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)a virtuális gépek legfeljebb 30 másodpercre szünetelnek. Például a karbantartás megkezdése előtt a virtuális gép órája 10:00:00 am-t jelenít meg, és 28 másodpercig tart. Miután a virtuális gép folytatódik, a virtuális gép órája továbbra is 10:00:00 am,ami 28 másodperc szünet. Ennek kijavításához a VMICTimeSync szolgáltatás figyeli, hogy mi történik az állomáson, és kéri a módosításokat a virtuális gépeken, hogy kompenzálja.

Idő szinkronizálás a munka nélkül, az óra a virtuális gép felhalmozódna hibákat. Ha csak egy virtuális gép van, a hatás nem lehet jelentős, kivéve, ha a számítási feladatok rendkívül pontos időmérést igényel. De a legtöbb esetben több, összekapcsolt virtuális gépünk van, amelyek a tranzakciók nyomon követésére időt használnak, és az időnek konzisztensnek kell lennie a teljes központi telepítés során. Ha a virtuális gépek közötti idő eltérő, a következő hatások jelennek meg:

- A hitelesítés sikertelen lesz. Az olyan biztonsági protokollok, mint a Kerberos vagy a tanúsítványfüggő technológia, az idő konzisztensek a rendszerek között.
- Nagyon nehéz kitalálni, hogy mi történt a rendszerben, ha a naplók (vagy más adatok) nem értenek egyet az időben. Ugyanaz az esemény úgy nézne ki, mintha különböző időpontokban történt volna, ami megnehezíti a korrelációt.
- Ha az óra ki van kapcsolva, a számlázás helytelenül számítható ki.



## <a name="configuration-options"></a>Beállítási lehetőségek

Az Azure-ban üzemeltetett Linux-virtuális gépek időszinkronizálását általában három féleképpen konfigurálhatja:

- Az Azure Marketplace-rendszerképek alapértelmezett konfigurációja ntp-időt és VMICTimeSync állomásidőt is használ. 
- Csak állomás a VMICTimeSync használatával.
- Használjon egy másik külső időkiszolgálót VMICTimeSync állomásidő használatával vagy anélkül.


### <a name="use-the-default"></a>Az alapértelmezett beállítás használata

Alapértelmezés szerint a legtöbb Linuxos Azure Marketplace-lemezkép két forrásból történő szinkronizálásra van konfigurálva: 

- NTP elsődlegesként, amely időt kap egy NTP-kiszolgálótól. Az Ubuntu 16.04 LTS Marketplace-képek például **ntp.ubuntu.com.**
- A VMICTimeSync szolgáltatás másodlagos, az állomásidő kommunikálására szolgál a virtuális gépekhez, és a virtuális gép karbantartása után javításokat tesz le. Az Azure-állomások a Microsoft tulajdonában lévő Stratum 1 eszközöket használják a pontos idő megtartásához.

Az újabb Linux-disztribúciókban a VMICTimeSync szolgáltatás a pontossági időprotokollt (PTP) használja, de előfordulhat, hogy a korábbi disztribúciók nem támogatják a PTP-t, és az NTP-re térnek vissza a gazdagéptől való időszerzéshez.

Az NTP megfelelő szinkronizálásának ellenőrzéséhez futtassa a `ntpq -p` parancsot.

### <a name="host-only"></a>Csak állomás 

Mivel az NTP-kiszolgálók, például a time.windows.com és a ntp.ubuntu.com nyilvánosak, a velük töltött idő szinkronizálásához az interneten keresztül kell forgalmat küldeni. A változó csomagkésések negatívan befolyásolhatják az időszinkronizálás minőségét. Ha eltávolítja az NTP-t a csak állomásszinkronizálásra való váltással, az néha javíthatja az időszinkronizálás eredményeit.

Ha az alapértelmezett konfigurációval időszinkronizálási problémákat tapasztal, célszerű a csak állomásidő-szinkronizálásra váltani. Próbálja ki a csak állomás szinkronizálási, hogy ha ez javítaná az idő szinkronizálása a virtuális gépen. 

### <a name="external-time-server"></a>Külső időkiszolgáló

Ha konkrét időszinkronizálási követelményekkel rendelkezik, akkor a külső időkiszolgálók is igénybe vehetik. A külső időkiszolgálók meghatározott időt biztosíthatnak, ami hasznos lehet a tesztforgatókönyvekhez, biztosítva az idő egységét a nem microsoftos adatközpontokban üzemeltetett gépekkel, vagy a szökőmásodpercek speciális módon történő kezelését.

A külső időkiszolgáló t az alapértelmezett konfigurációhoz hasonló eredmények biztosításához kombinálhatja a VMICTimeSync szolgáltatással. A külső időkiszolgáló és a VMICTimeSync kombinálása a legjobb megoldás a virtuális gépek karbantartásra való szüneteltetése esetén okokkal okozott problémák kezelésére. 

## <a name="tools-and-resources"></a>Eszközök és források

Van néhány alapvető parancs az időszinkronizálási konfiguráció ellenőrzéséhez. Dokumentáció Linux disztribúció további részleteket a legjobb módja annak, hogy konfigurálja az idő szinkronizálása az adott disztribúció.

### <a name="integration-services"></a>Integrációs szolgáltatások

Ellenőrizze, hogy az integrációs szolgáltatás (hv_utils) be van-e töltve.

```bash
lsmod | grep hv_utils
```
Meg kell látni valami hasonlót:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Nézze meg, hogy a Hyper-V integrációs szolgáltatások démon fut.See if the Hyper-V integration services daemon is running.

```bash
ps -ef | grep hv
```

Meg kell látni valami hasonlót:

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp"></a>PTP ellenőrzése

A Linux újabb verzióival a Precision Time Protocol (PRECISION Time Protocol) órajel forrása a VMICTimeSync szolgáltató részeként érhető el. A Red Hat Enterprise Linux vagy CentOS 7.x régebbi verzióiban a [Linux integrációs szolgáltatások](https://github.com/LIS/lis-next) letölthetők és felhasználhatók a frissített illesztőprogram telepítéséhez. PtP használata esetén a Linux-eszköz /dev/ptp*x*. 

Megtekintheti, hogy mely PTP óraforrások érhetők el.

```bash
ls /sys/class/ptp
```

Ebben a példában a visszaadott érték *ptp0*, ezért ezt használjuk az óra nevének ellenőrzésére. Az eszköz ellenőrzéséhez ellenőrizze az óra nevét.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

Ez vissza kell térnie **hyperv**.

### <a name="chrony"></a>krony

Az Ubuntu 19.10 és újabb verziók, a Red Hat Enterprise Linux és a CentOS 7.x verziókon [a chrony](https://chrony.tuxfamily.org/) PTP forrásórát használ. A régebbi Linux-kiadások helyett a Network Time Protocol démont (ntpd) használják, amely nem támogatja a PTP-forrásokat. Ahhoz, hogy a PTP-t ezekben a kiadásokban engedélyezze, a chrony-t manuálisan kell telepíteni és konfigurálni (a chrony.conf fájlban) a következő kód használatával:

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

Az Ubunturól és az NTP-ről további információt az [Időszinkronizálás című](https://help.ubuntu.com/lts/serverguide/NTP.html)témakörben talál.

A Red Hat ról és az NTP-ről az [NTP konfigurálása című](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/s1-configure_ntp)témakörben talál további információt. 

A krony használatáról további információt a [Krony használata című](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-using_chrony)témakörben talál.

Ha egyszerre is engedélyezve van a kronoázott és a TimeSync-források, megjelölheti az egyiket **előnyben részesíti,** ami a másik forrást biztonsági másolatként állítja be. Mivel az NTP-szolgáltatások csak hosszú idő elteltével frissítik az órát a nagy ferdeségek esetében, a VMICTimeSync sokkal gyorsabban állítja helyre az órát a szüneteltetett virtuálisgép-eseményekből, mint az NTP-alapú eszközök.

Alapértelmezés szerint a kronyd felgyorsítja vagy lelassítja a rendszerórát, hogy bármikor kijavítsa az időeltolódást. Ha a sodródás túl nagy lesz, chrony nem tudja megjavítani a driftet. Ennek leküzdése érdekében az `makestep` **/etc/chrony.conf** paraméter módosítható úgy, hogy időszinkronizálást kényszerítsen ki, ha az eltolódás meghaladja a megadott küszöbértéket.

 ```bash
makestep 1.0 -1
```

Itt a krony időfrissítést kényszerít, ha a drift nagyobb, mint 1 másodperc. A módosítások alkalmazásához indítsa újra a kronyd szolgáltatást:

```bash
systemctl restart chronyd
```

### <a name="systemd"></a>rendszerezve 

A SUSE és ubuntu kiadások előtt 19.10, idő szinkronizálás van beállítva [a systemd](https://www.freedesktop.org/wiki/Software/systemd/). Az Ubuntuval kapcsolatos további információkért lásd: [Időszinkronizálás.](https://help.ubuntu.com/lts/serverguide/NTP.html) A SUSE-ről a [SUSE Enterprise Server 12 SP3 kiadási megjegyzések](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement)4.5.8 szakaszában talál további információt.

## <a name="next-steps"></a>További lépések

További információt a [Windows Server 2016 pontos ideje](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)című témakörben talál.


