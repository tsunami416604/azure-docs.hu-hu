---
title: Linux rendszerű virtuális gépek időszinkronizálása az Azure-ban
description: Linux rendszerű virtuális gépek időszinkronizálása.
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
ms.openlocfilehash: e5d68a31db3797f9919d044eed284d0d09052390
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034661"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Linux rendszerű virtuális gépek időszinkronizálása az Azure-ban

Az időszinkronizálás fontos a biztonság és az események korrelációs használata esetén. Időnként az elosztott tranzakciók megvalósítására használják. A több számítógépes rendszer közötti idő-pontosság a szinkronizáláson keresztül érhető el. A szinkronizálás több dologra is hatással lehet, többek között a újraindításra és a hálózati forgalomra az idő forrása és a számítógép lekérése között. 

Az Azure-t a Windows Server 2016-et futtató infrastruktúra támogatja. A Windows Server 2016 továbbfejlesztett algoritmusokkal rendelkezik, amelyek az időpontot és a feltételt használják a helyi óra és az UTC közötti szinkronizáláshoz.  A Windows Server 2016 pontos idő funkciója jelentősen javította, hogy a VMICTimeSync szolgáltatás hogyan szabályozza a virtuális gépeket a gazdagépen a pontos idő tekintetében. A javításokban pontosabb kezdeti idő van a virtuális gépek indításakor, illetve a virtuális gépek visszaállítása és a késések késleltetésének javítása. 

>[!NOTE]
>A Windows időszolgáltatásának gyors áttekintéséhez tekintse meg ezt a [magas szintű áttekintő videót](https://aka.ms/WS2016TimeVideo).
>
> További információ: [a Windows Server 2016 pontos ideje](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Áttekintés

A számítógép órájának pontossága a számítógép órájának az egyezményes világidő (UTC) időszabványhoz való zárásának mérésére szolgál. Az UTC-t egy, a pontos Atomic-órákat tartalmazó többnemzetiségű minta határozza meg, amely csak 300 év egy másodpercében lehet kikapcsolható. Az UTC közvetlen olvasása azonban speciális hardvert igényel. Ehelyett az időkiszolgálókat a rendszer az UTC szerint szinkronizálja, és más számítógépekről éri el a méretezhetőség és a robusztusság biztosítása érdekében. Minden számítógépen van olyan időszinkronizálási szolgáltatás, amely tudja, hogy milyen időkiszolgálókat használ, és rendszeres időközönként ellenőrzi, hogy a számítógép óráját javítani kell-e, és szükség esetén módosítani kell-e az időt. 

Az Azure-gazdagépek szinkronizálása a Microsoft által a Microsoft által birtokolt stratum 1-eszközökről, a GPS-antennák használatával történik. Az Azure-beli virtuális gépek vagy a gazdagéptől függenek, hogy a pontos időt (*gazda időt*) a virtuális gépre irányítják, vagy a virtuális gép közvetlenül lekérheti az időt egy időkiszolgálóról, vagy mindkettő kombinációját. 

Önálló hardveren a Linux operációs rendszer csak a gazdagép hardveres óráját olvassa be a rendszerindításkor. Ezt követően a rendszer az órát a Linux kernel megszakítási időzítője segítségével tartja karban. Ebben a konfigurációban az óra az idő múlásával fog sodródni. Az Azure-beli újabb Linux-disztribúciókban a virtuális gépek a Linux Integration Services (LIS) szolgáltatásban is használhatják a VMICTimeSync szolgáltatót, hogy gyakrabban kérdezzenek le az óra frissítéseiről a gazdagépről.

A virtuális gép és a gazdagép közötti interakció is hatással lehet az órára. A [karbantartás](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)során a virtuális gépek legfeljebb 30 másodpercig szünetelnek. Például a karbantartás megkezdése előtt a virtuális gép órája a 10:00:00-as és 28 másodperces időtartamot jeleníti meg. A virtuális gép újraindítása után a virtuális gép órája továbbra is a 10:00:00-as értéket fogja megjeleníteni, ami 28 másodperc lenne. Ennek kiválasztásához a VMICTimeSync szolgáltatás figyeli, hogy mi történik a gazdagépen, és felszólítja a virtuális gépeken végrehajtott módosítások elvégzésére a kompenzálás érdekében.

Az időszinkronizálás nem működik, mert a virtuális gépen lévő idő felhalmozza a hibákat. Ha csak egy virtuális gép van, akkor előfordulhat, hogy a hatás nem jelentős, kivéve, ha a munkaterhelés nagyon pontos időmérést igényel. A legtöbb esetben azonban több, egymással összekapcsolt virtuális géppel is rendelkezünk, amelyek időt használnak a tranzakciók nyomon követésére, és az idő a teljes telepítés során konzisztensnek kell lennie. Ha a virtuális gépek közötti idő eltér, a következő hatások jelenhetnek meg:

- A hitelesítés sikertelen lesz. A biztonsági protokollok, például a Kerberos vagy a tanúsítványtól függő technológiák támaszkodnak a rendszerek közötti konzisztens időszakra.
- Nagyon nehéz megállapítani, hogy mi történt a rendszeren, ha a naplók (vagy más adat) nem fogadnak el időt. Ugyanez az esemény a különböző időpontokban történt, így megnehezíti a korrelációt.
- Ha az óra ki van kapcsolva, a számlázás helytelenül számítható ki.



## <a name="configuration-options"></a>Beállítási lehetőségek

Az Azure-ban üzemeltetett linuxos virtuális gépek időszinkronizálását általában három módon konfigurálhatja:

- Az Azure Marketplace-lemezképek alapértelmezett konfigurációja az NTP-időt és a VMICTimeSync-gazdagépet is használja. 
- Gazdagép – csak a VMICTimeSync használatával.
- Használjon egy másik, külső időkiszolgálót a VMICTimeSync-gazdagép-idő használatával vagy anélkül.


### <a name="use-the-default"></a>Alapértelmezett érték használata

Alapértelmezés szerint a Linux rendszerhez készült legtöbb Azure Marketplace-lemezkép két forrásból való szinkronizálásra van konfigurálva: 

- Az NTP elsődlegesként, amely egy NTP-kiszolgálótól kap időt. Az Ubuntu 16,04 LTS Marketplace-lemezképek például a **NTP.Ubuntu.com**-t használják.
- A VMICTimeSync szolgáltatás másodlagosként, amely a gazdagépek és a virtuális gépek közötti kommunikációra szolgál, és a virtuális gép karbantartás utáni szüneteltetése után végez javítást. Az Azure-gazdagépek a Microsoft által birtokolt stratum 1 eszközöket használják a pontos idő megtartására.

Az újabb Linux-disztribúciókban a VMICTimeSync szolgáltatás a (z) pontosságot használja, de előfordulhat, hogy a korábbi disztribúciók nem támogatják a PTP-t, és az NTP-re kerülnek vissza a gazdagépről való idő beszerzéséhez.

Az NTP sikeres szinkronizálásának megerősítéséhez futtassa az `ntpq -p` parancsot.

### <a name="host-only"></a>Csak gazdagép 

Mivel az NTP-kiszolgálók, például a time.windows.com és a ntp.ubuntu.com nyilvánosak, az adatok szinkronizálásához az interneten keresztül kell elküldeni a forgalmat. A csomagok különböző késései negatívan befolyásolhatják az idő szinkronizálásának minőségét. Ha az NTP-t csak gazdagépre való áttéréssel távolítja el, időnként javíthatja az idő szinkronizálási eredményeit.

Ha az alapértelmezett konfiguráció használatával időt vesz igénybe, a csak a gazdagépre való áttéréskor érdemes váltani. Próbálja ki a gazdagépek szinkronizálását, és ellenőrizze, hogy ez javítaná-e a virtuális gépen futó idő szinkronizálását. 

### <a name="external-time-server"></a>Külső idő kiszolgálója

Ha bizonyos időszinkronizálási követelményekkel rendelkezik, akkor a külső időkiszolgálók is használhatók. A külső időkiszolgálók meghatározott időt biztosíthatnak, ami hasznos lehet a tesztelési forgatókönyvekhez, így biztosítva a nem Microsoft-adatközpontokban üzemeltetett gépekkel való időbeli egységességet, illetve a LEAP másodpercek speciális módon történő kezelését.

Egy külső időkiszolgálót kombinálhat a VMICTimeSync szolgáltatással az alapértelmezett konfigurációhoz hasonló eredmények biztosításához. A külső időkiszolgálók és a VMICTimeSync együttes használata a legjobb megoldás az olyan problémák kezelésére, amelyek a virtuális gépek karbantartás közbeni felfüggesztésével járhatnak. 

## <a name="tools-and-resources"></a>Eszközök és erőforrások

Az időszinkronizálási konfiguráció ellenőrzéséhez néhány alapszintű parancs szükséges. A Linux-disztribúció dokumentációja részletesebben ismerteti a terjesztés időszinkronizálásának legjobb módját.

### <a name="integration-services"></a>Integrációs szolgáltatások

Ellenőrizze, hogy be van-e töltve az integrációs szolgáltatás (hv_utils).

```bash
lsmod | grep hv_utils
```
Ehhez a következőhöz hasonlónak kell lennie:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Ellenőrizze, hogy fut-e a Hyper-V Integration Services démon.

```bash
ps -ef | grep hv
```

Ehhez a következőhöz hasonlónak kell lennie:

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp"></a>A PTP keresése

A Linux újabb verzióiban a VMICTimeSync-szolgáltató részeként a (z) Precision Time Protocol (PTP) órajel-forrás érhető el. Red Hat Enterprise Linux vagy CentOS 7. x régebbi verzióiban a [Linux integrációs szolgáltatások](https://github.com/LIS/lis-next) tölthetők le, és a frissített illesztőprogram telepítéséhez használhatók. A PTP használatakor a Linux-eszköz/dev/PTP*x*formátumú lesz. 

Itt láthatja, hogy mely PTP-órajelek érhetők el.

```bash
ls /sys/class/ptp
```

Ebben a példában a visszaadott érték a *ptp0*, ezért ezt használjuk az óra nevének megadásához. Az eszköz ellenőrzéséhez ellenőrizze az óra nevét.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

Ennek a **HyperV**kell visszaadnia.

### <a name="chrony"></a>chrony

A Red Hat Enterprise Linux és a CentOS 7. x esetében a [chrony](https://chrony.tuxfamily.org/) a PTP-forrás órájának használatára van konfigurálva. A Network Time Protocol Daemon (ntpd) nem támogatja a PTP-forrásokat, ezért a **chronyd** használata javasolt. A PTP engedélyezéséhez frissítse a **chrony. conf fájlt**.

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

A Red Hat és az NTP szolgáltatással kapcsolatos további információkért lásd: az [NTP konfigurálása](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/s1-configure_ntp). 

További információ a chrony-ről: a [Chrony használata](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-using_chrony).

Ha a chrony-és a TimeSync-források egyszerre is engedélyezve vannak, megjelölheti az egyiket **előnyben részesítettként** , amely a másik forrást biztonsági másolatként állítja be. Mivel az NTP-szolgáltatások nem frissítik a nagy terhelések óráját a hosszú időtartam után, a VMICTimeSync sokkal gyorsabban fogja helyreállítani a szüneteltetett virtuális gépek eseményeinek óráját, mint az NTP-alapú eszközökön.


### <a name="systemd"></a>rendszerszintű 

Ubuntu és SUSE idő szinkronizálásakor a [rendszer](https://www.freedesktop.org/wiki/Software/systemd/)a rendszerbeállítással van konfigurálva. Az Ubuntuval kapcsolatos további információkért lásd: [időszinkronizálás](https://help.ubuntu.com/lts/serverguide/NTP.html). További információ a SUSE-ről: [SUSE Linux Enterprise Server 12 SP3 kibocsátási megjegyzések](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement)4.5.8 szakasza.



## <a name="next-steps"></a>Következő lépések

További információ: [a Windows Server 2016 pontos ideje](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time).


