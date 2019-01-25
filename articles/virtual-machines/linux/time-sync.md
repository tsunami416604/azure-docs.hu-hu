---
title: Szinkronizálási idő Linux rendszerű virtuális gépekhez az Azure-ban |} A Microsoft Docs
description: Linux rendszerű virtuális gépek idő szinkronizálása.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 58fd3afa37d965cfbe21dcf23823ddb8425442b9
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54887563"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Idő szinkronizálása az Azure-ban Linux rendszerű virtuális gépekhez

Időszinkronizálás fontos a biztonság és eseménykorreláció. Egyes esetekben használatos az elosztott tranzakciók végrehajtására. Szinkronizáció idő pontossága a több számítógépes rendszereken érhető el. Szinkronizálási hatással lehet más dolgokra, beleértve az újraindítások és az idő forrás- és a számítógép beolvasása az idő közötti hálózati forgalmat. 

Azure Windows Server 2016 rendszerű infrastruktúrája épül. A Windows Server 2016 továbbfejlesztett algoritmusok pontos idő és a helyi órája UTC szinkronizálását a feltétel rendelkezik.  A Windows Server 2016 – pontos idő funkció jelentősen javult hogyan a VMICTimeSync szolgáltatás, amely szabályozza a virtuális gépek a gazdagép a pontos idő. Fejlesztések közé tartoznak a pontosabb kezdeti ideje a virtuális gép start vagy a virtuális gép visszaállítási megszakítási késési javítási. 

>[!NOTE]
>A Windows időszolgáltatás gyors áttekintést, vessen egy pillantást, ez [videó összefoglaló jellegű áttekintést nyújt](https://aka.ms/WS2016TimeVideo).
>
> További információkért lásd: [Windows Server 2016 – pontos idő](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Áttekintés

A számítógép órája pontosságának van kivett a hogyan zárja be a számítógép óráját, hogy az idő egyezményes világidő (UTC) standard. (UTC) egy második 300 évben ki csak lehet pontos atomi órájával multinacionális mintát határozza meg. De UTC olvasása közvetlenül speciális hardvert igényel. Ehelyett időkiszolgálók szinkronizálva lesznek az UTC és más számítógépek méretezhetőségét és háttértárat érhető el. Minden számítógép szinkronizálási szolgáltatás fut, hogy tudja, milyen idő-kiszolgálók által használt, és rendszeresen ellenőrzi, hogy ha a számítógép órája javításra szoruló idő tartozik, és beállítja az időt, szükség esetén. 

Azure gazdagép belső hierarchiabeli 1 Microsoft tulajdonú eszközökről, a GPS-antennák azok időt vesz igénybe a Microsoft idő kiszolgálók vannak szinkronizálva. Az Azure Virtual machines vagy függőségi viszonyban lehet átadni a pontos időt a gazdagép (*idő gazdagép*) be a virtuális gép vagy a virtuális gép közvetlenül lekérheti a idő egy kiszolgálót vagy mindkettőt. 

Önálló hardverre, a Linux operációs rendszer csak olvassa be a gazdagép hardverének mért rendszertöltés. Ezt követően az óra, kezelik a Linux kernel a megszakítási időzítő segítségével. Ebben a konfigurációban az óra lesz konfigurációsodródás idővel. Újabb Linux-disztribúciókon az Azure-ban a virtuális gépek használatával a VMICTimeSync szolgáltatója, a Linux integration services (LIS), a lekérdezési óra frissítések gyakran a gazdagépről.

A gazdagép virtuálisgép-interakció is befolyásolhatja az óra. Során [karbantartás megőrzése memória](maintenance-and-updates.md#memory-preserving-maintenance), virtuális gépek vannak függesztve, akár 30 másodpercig. Például karbantartás megkezdése előtt a virtuális gép órája jeleníti meg a 10:00:00-kor és 28 másodperc tart. Után folytatja a virtuális Gépet, az óra, a virtuális gép továbbra is megjeleníti 10:00:00-kor, amelyek 28 másodperc ki. Ennek, a VMICTimeSync szolgáltatás figyeli a gazdagép és a módosítások utasításokat található a virtuális gépek esetében történő kompenzálás mi történik.

Nélkül idő szinkronizálási működő, a virtuális gép órája hibák lenne összeadódhatnak. Ha csak egy virtuális Gépet, a hatás nem jelentős, kivéve, ha a munkaterhelés rendkívül pontos időmérő igényel. De a legtöbb esetben azt kell több, összekapcsolt virtuális gépeket, amelyek idő segítségével nyomon követheti a tranzakciók és az időt kell lennie a teljes telepítés során. Ha más virtuális gépek között eltelt idő, sikerült jelenik meg a következő hatásai vannak:

- Hitelesítés sikertelen lesz. Biztonsági protokollok, például a Kerberos vagy a tanúsítvány-függő technológia támaszkodik a rendszeridő konzisztens a rendszerek között.
- Legyen nagyon nehéz döntse el, mi történhetett rendszerekben, hogy a naplók (vagy egyéb adat) nem fogadom el lehetőséget időben. Az ugyanahhoz az eseményhez jelenne meg, különböző időpontokban, nehéz és korrelációs történt.
- Ha óra ki kapcsolva, a számlázás helytelenül sikerült kiszámítani.



## <a name="configuration-options"></a>Beállítási lehetőségek

Általában három módon szinkronizálás konfigurálása az Azure-ban üzemeltetett Linuxos virtuális gépek:

- Az Azure Marketplace-rendszerképek alapértelmezett konfigurációja egyaránt NTP és VMICTimeSync gazdagép-időpontot használja. 
- Gazdagép csak VMICTimeSync használatával.
- Egy másik, külső idő kiszolgálót használjon, vagy anélkül VMICTimeSync gazdagép-ideje.


### <a name="use-the-default"></a>Használja az alapértelmezett

Alapértelmezés szerint a legtöbb Azure Marketplace-rendszerképek Linux vannak konfigurálva a szinkronizálási két forrásból származó: 

- NTP elsődleges, amely lekérdezi az idő az NTP-kiszolgálóról. Például az Ubuntu 16.04 LTS Marketplace lemezképek használata **ntp.ubuntu.com**.
- Másodlagos, mint a VMICTimeSync szolgáltatás kommunikálni a virtuális gépek gazdagép időt, és javíthatja a megadottakat, miután a virtuális gép fel van függesztve, a karbantartási szolgál. Azure gazdagépek hierarchiabeli 1 a Microsoft által birtokolt eszközök használatával – pontos idő tartani.

Újabb Linux-disztribúció a VMICTimeSync szolgáltatás használ a pontosság time protocol (PTP), de korábbi disztribúciók nem támogatja a PTP és fog fall vissza NTP az első alkalommal a gazdagépről.

Győződjön meg arról, NTP megfelelően szinkronizál, futtassa a `ntpq -p` parancsot.

### <a name="host-only"></a>Csak állomásnév 

Az NTP-kiszolgálókra, mint például a time.windows.com és ntp.ubuntu.com nyilvánosak, mert azokat az idő szinkronizálása szükséges forgalom küldése az interneten keresztül. Csomag késések változó negatív hatással lehet az idő szinkronizálása minőségét. NTP eltávolítása átvált a gazdagép csak szinkronizálás néha javítja az idő szinkronizálása az eredményeket.

Váltás csak állomásnév idő szinkronizálása teszi értelemben vett idő szinkronizálása tapasztal problémák az alapértelmezett konfigurációt használja. Próbálja ki a gazdagép csak szinkronizálás megtekintéséhez Ha, amelyek az idő szinkronizálása a virtuális Gépen. 

### <a name="external-time-server"></a>Külső időkiszolgálóval

Ha adott időpont-szinkronizálás követelményei, is lehetőség van a külső idő kiszolgálók használatával. Külső idő kiszolgálók biztosíthat az adott időpontot, amely a tesztelési helyzetekhez, ideje egységességének biztosítása üzemeltetett-Microsoft adatközpontok vagy kezelési azt másodperc, speciális módon gépekkel hasznos lehet.

Egy külső időkiszolgálóval egyesíthetők a VMICTimeSync szolgáltatás az alapértelmezett konfiguráció hasonló eredményeket. Egy külső időkiszolgálóval egyesül VMICTimeSync problémákat, amelyek a virtuális gépek karbantartás szüneteltetésekor ok lehet többé vesződnie a sérült leginkább megfelelő opció kiválasztásához. 

## <a name="tools-and-resources"></a>Eszközök és erőforrások

Van néhány alapszintű parancsainak a idő szinkronizálási konfiguráció ellenőrzése. Linux-disztribúció dokumentációjában részletesebb információkat tartalmaz a szinkronizálás ideje, hogy a terjesztés konfigurálása a legjobb módszer.

### <a name="integration-services"></a>Az integrációs szolgáltatások

Ellenőrizze, hogy ha az integrációs szolgáltatás (hv_utils) be van-e töltve.

```bash
lsmod | grep hv_utils
```
Valami ehhez hasonlóan kell megjelennie:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Tekintse meg, hogy a Hyper-V integrációs szolgáltatások démon fut-e.

```bash
ps -ef | grep hv
```

Valami ehhez hasonlóan kell megjelennie:

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp"></a>PTP keresése

Egy újabb verziója Linux, a pontosság Time Protocol (PTP) óra forrás álljon rendelkezésre a VMICTimeSync szolgáltató részeként. A Red Hat Enterprise Linux vagy CentOS régebbi verzióin 7.x a [Linux Integration Services](https://github.com/LIS/lis-next) letölthető, és a frissített illesztőprogram telepítéséhez használt. PTP használatakor a Linux rendszerű eszköz lesz a képernyő/dev/ptp*x*. 

Tekintse meg, mely PTP óra források érhetők el.

```bash
ls /sys/class/ptp
```

Ebben a példában a visszaadott érték *ptp0*, ezért használjuk, hogy ellenőrizze a óra nevét. Az eszköz ellenőrzéséhez tekintse az óra neve.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

A kapott **Hyper-v**.

### <a name="chrony"></a>chrony

A Red Hat Enterprise Linux és a CentOS 7.x, [chrony](https://chrony.tuxfamily.org/) PTP forrás hodiny használatára konfigurálva. A Network Time Protocol démon (ntpd) nem támogatja a PTP forrásokból, így használatával **chronyd** ajánlott. PTP engedélyezéséhez frissítse **chrony.conf**.

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

A Red Hat és NTP további információkért lásd: [konfigurálása NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/s1-configure_ntp). 

Chrony további információkért lásd: [chrony használatával](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-using_chrony).

Chrony és TimeSync forrásokból is egyidejű engedélyezése, ha több mint megjelölheti **inkább** amely állítja be a más forrásból, biztonsági mentéséhez. Mivel az NTP-szolgáltatások, amíg nem frissítik az óra, kivéve a hosszú idő nagy dönt a a VMICTimeSync állítja helyre az óra szüneteltetett virtuális gép események sokkal gyorsabban önálló NTP-alapú eszközök.


### <a name="systemd"></a>systemd 

Az Ubuntu és a SUSE sync konfigurálva van az használatával [systemd](https://www.freedesktop.org/wiki/Software/systemd/). További információ az ubuntu rendszeren: [időszinkronizálás](https://help.ubuntu.com/lts/serverguide/NTP.html). További információ a SUSE rendszeren című 4.5.8 [SUSE Linux Enterprise Server 12 SP3 kibocsátási megjegyzések](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement).



## <a name="next-steps"></a>További lépések

További információkért lásd: [Windows Server 2016 – pontos idő](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time).


