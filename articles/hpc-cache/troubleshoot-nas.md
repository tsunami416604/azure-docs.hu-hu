---
title: Az Azure HPC-gyorsítótár NFS-tárolócéljainak – problémamegoldás
description: Tippek a konfigurációs hibák és egyéb problémák elkerüléséhez és javításához, amelyek hibát okozhatnak az NFS-tárolócéljának létrehozásakor
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: rohogue
ms.openlocfilehash: 0a24530810a448a713c01efbc8933b9f22d15b3b
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536369"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>NAS-konfigurációval és NFS-tárterületcéllal kapcsolatos problémák elhárítása

Ez a cikk megoldásokat kínál néhány gyakori konfigurációs hibák és egyéb problémák, amelyek megakadályozhatják, hogy az Azure HPC cache hozzáadása NFS-tárolórendszer tárolócélként.

Ez a cikk a portok ellenőrzésének és a NAS-rendszerhez való gyökérhozzáférés engedélyezésének részleteit tartalmazza. Emellett részletes információkat tartalmaz a kevésbé gyakori problémákról, amelyek az NFS-tárolócél létrehozásának sikertelenssé tévődhetnek.

> [!TIP]
> Az útmutató használata előtt olvassa el [az NFS-tárolócélok előfeltételeit.](hpc-cache-prereqs.md#nfs-storage-requirements)

Ha a probléma megoldása itt nem szerepel, [nyisson meg egy támogatási jegyet,](hpc-cache-support-ticket.md) hogy a Microsoft Szolgáltatás és Támogatás együttműködhessen Önnel a probléma kivizsgálásában és megoldásában.

## <a name="check-port-settings"></a>Portbeállítások ellenőrzése

Az Azure HPC-gyorsítótárnak olvasási/írási hozzáférésre van szüksége a háttér-NAS tárolórendszeren lévő több UDP/TCP-porthoz. Győződjön meg arról, hogy ezek a portok elérhetők a NAS-rendszeren, és hogy a tárolórendszer és a gyorsítótár alhálózata közötti tűzfalakon keresztül engedélyezett-e a portok közötti forgalom. Előfordulhat, hogy az adatközpont tűzfalakkal és hálózati rendszergazdáival kell együttműködnie a konfiguráció ellenőrzéséhez.

A portok különbözőtároló rendszerek különböző szállítóktól, ezért ellenőrizze a rendszer követelményeit, amikor a tárolási cél beállítása.

A gyorsítótárnak általában hozzá kell férnie ezekhez a portokhoz:

| Protocol (Protokoll) | Port  | Szolgáltatás  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr között |
| TCP/UDP  | 4046  | csatlakoztatva   |
| TCP/UDP  | 4047  | status   |

A rendszerhez szükséges portok megismeréséhez ``rpcinfo`` használja a következő parancsot. Ez az alábbi parancs felsorolja a portokat, és formázza a táblázatban szereplő releváns eredményeket. (A *<storage_IP>storage_IP* kifejezés helyett használja a rendszer IP-címét.)

Ezt a parancsot bármely Olyan Linux-ügyfélről kiadhatja, amelyen telepítve van az NFS-infrastruktúra. Ha a fürt alhálózatán belül használ egy ügyfelet, az segíthet az alhálózat és a tárolórendszer közötti kapcsolat ellenőrzésében is.

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

Győződjön meg arról, hogy ``rpcinfo`` a lekérdezés által visszaadott összes port engedélyezi az Azure HPC-gyorsítótár alhálózatának korlátlan forgalmát.

Ellenőrizze ezeket a beállításokat mind a NAS-on, mind a tárolórendszer és a gyorsítótár alhálózata közötti tűzfalakon.

## <a name="check-root-access"></a>Gyökérhozzáférés ellenőrzése

Az Azure HPC cache-nek hozzá kell férnie a tárolórendszer exportálásához a tárolási cél létrehozásához. Pontosabban az exportálást 0-s felhasználóazonosítóként csatlakoztatja.

A különböző tárolórendszerek különböző módszereket használnak a hozzáférés engedélyezéséhez:

* A Linux-kiszolgálók általában hozzáadnak ``no_root_squash`` az exportált elérési úthoz a alkalmazásban. ``/etc/exports``
* A NetApp és az EMC rendszerek általában meghatározott IP-címekhez vagy hálózatokhoz kötött exportálási szabályokkal szabályozzák a hozzáférést.

Exportálási szabályok használata esetén ne feledje, hogy a gyorsítótár több különböző IP-címet is használhat a gyorsítótár alhálózatából. Hozzáférés engedélyezése a lehetséges alhálózati IP-címek teljes tartományából.

> [!NOTE]
> Alapértelmezés szerint az Azure HPC-gyorsítótár összenyomja a root hozzáférést. Olvassa [el A további gyorsítótár-beállítások konfigurálása](configuration.md#configure-root-squash) a részletekhez című szöveget.

A NAS-tároló szállítójával együttműködve engedélyezze a gyorsítótár megfelelő szintű hozzáférését.

### <a name="allow-root-access-on-directory-paths"></a>Gyökérhozzáférés engedélyezése a címtárelérési utakon
<!-- linked in prereqs article -->

A hierarchikus könyvtárakat exportáló NAS-rendszerek esetében az Azure HPC-gyorsítótárnak minden exportálási szinthez gyökérszintű hozzáférésre van szüksége.

Egy rendszer például három ilyen exportálást mutathat:

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

Az ``/ifs/accounting/payroll`` export a ``/ifs/accounting``gyermeke ``/ifs/accounting`` , és maga ``/ifs``is a gyermeke.

Ha az ``payroll`` exportálást HPC-gyorsítótár-tárolócélként adja ``/ifs/`` hozzá, a gyorsítótár ténylegesen csatlakoztatja és hozzáfér a bérlistakönyvtárhoz. Így az Azure HPC ``/ifs`` cache root hozzáférést ``/ifs/accounting/payroll`` kell biztosítani az exportálás eléréséhez.

Ez a követelmény azzal függ össze, hogy a gyorsítótár hogyan indexeli a fájlokat, és hogyan kerüli el a fájlütközéseket a tárolórendszer által biztosított fájlleírók használatával.

A hierarchikus exportálással rendelkező NAS-rendszerek különböző fájlleírókat adhatnak ugyanahhoz a fájlhoz, ha a fájlt különböző exportálásokból olvassa be. Egy ügyfél például ``/ifs/accounting`` csatlakoztathatja és ``payroll/2011.txt``elérheti a fájlt. Egy másik ``/ifs/accounting/payroll`` ügyfél csatlakoztatja ``2011.txt``és hozzáfér a fájlhoz. Attól függően, hogy a tárolórendszer hogyan rendeli hozzá a fájlleírókat, ``<mount2>/payroll/2011.txt`` ez a ``<mount3>/2011.txt``két ügyfél ugyanazt a fájlt különböző fájlleírókkal (egy a) és egy).

A háttértároló rendszer megtartja a fájlleírók belső aliasait, de az Azure HPC cache nem tudja megmondani, hogy az indexben melyik fájlleíró hivatkozik ugyanarra az elemre. Így lehetséges, hogy a gyorsítótár különböző írási adatokat gyorsítótárazott ugyanahhoz a fájlhoz, és alkalmazza a módosításokat helytelenül, mert nem tudja, hogy ugyanaz a fájl.

A több exportálásban lévő fájlok esetleges fájlütközésének elkerülése érdekében az Azure HPC-gyorsítótár automatikusan csatlakoztatja a legsekélyebb elérhető exportálást az elérési úthoz (a``/ifs`` példában), és az adott exportálásból kapott fájlleírót használja. Ha több exportálás ugyanazt az alapelérési utat használja, az Azure HPC-gyorsítótárnak root hozzáférésre van szüksége az adott elérési úthoz.

## <a name="enable-export-listing"></a>Exportálási lista engedélyezése
<!-- link in prereqs article -->

A NAS-nak fel kell sorolnia az exportálását, amikor az Azure HPC-gyorsítótár lekérdezi.

A legtöbb NFS tárolórendszeren ezt a következő lekérdezés elküldésével teheti meg egy Linux-ügyféltől:``showmount -e <storage IP address>``

Ha lehetséges, használjon linuxos klienst ugyanabból a virtuális hálózatból, mint a gyorsítótár.

Ha ez a parancs nem sorolja fel az exportálást, a gyorsítótár nem tud csatlakozni a tárolórendszerhez. Az exportálási lista engedélyezéséhez működjön együtt a NAS-szállítóval.

## <a name="adjust-vpn-packet-size-restrictions"></a>A VPN-csomagméretre vonatkozó korlátozások módosítása
<!-- link in prereqs article and configuration article -->

Ha a gyorsítótár és a NAS-eszköz között VAN VPN, előfordulhat, hogy a VPN blokkolja a teljes méretű 1500 bájtos Ethernet csomagokat. Ez a probléma akkor fordulhat elő, ha a NAS és az Azure HPC-gyorsítótár-példány közötti nagy cserék nem fejeződnek be, de a kisebb frissítések a várt módon működnek.

Nincs egyszerű módja annak, hogy megmondja, hogy a rendszer rendelkezik-e ezzel a problémával, hacsak nem ismeri a VPN-konfiguráció részleteit. Íme néhány módszer, amely segíthet a probléma ellenőrzésében.

* A VPN mindkét oldalán használja a csomagkeresőket annak észleléséhez, hogy mely csomagok átvitele sikeresen.
* Ha a VPN lehetővé teszi a ping parancsokat, tesztelheti a teljes méretű csomag küldését.

  Ezekkel a beállításokkal futtasson ping parancsot a VPN-en keresztül a NAS-ba. (A *<storage_IP>storage_IP érték* helyett használja a tárolórendszer IP-címét.)

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  Ezek a lehetőségek a parancsban:

  * ``-M do``- Nem töredezik
  * ``-c 1``- Küldjön csak egy csomag
  * ``-s 1472``- Állítsa a hasznos teher méretét 1472 bájtra. Ez az 1500 bájtos csomagok maximális mérete az Ethernet-terhelés elszámolása után.

  A sikeres válasz így néz ki:

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  Ha a ping 1472 bájttal sikertelen, valószínűleg csomagméret-probléma merül fel.

A probléma megoldásához szükség lehet az MSS befogásának konfigurálására a VPN-en, hogy a távoli rendszer megfelelően észlelje a maximális keretméretet. További információért olvassa el a [VPN-átjáró IPsec/IKE paramétereinek dokumentációját.](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec)

Bizonyos esetekben az Azure HPC-gyorsítótár MTU-beállításának 1400-ra való módosítása segíthet. Ha azonban korlátozza az MTU a gyorsítótárban is korlátoznia kell az MTU-beállításokat az ügyfelek és a háttér-tároló rendszerek, amelyek a gyorsítótárakat. Olvassa [el A további Azure HPC-gyorsítótár beállításainak konfigurálása](configuration.md#adjust-mtu-value) a részletekért.

## <a name="check-for-acl-security-style"></a>Az ACL biztonsági stílusának ellenőrzése

Egyes NAS-rendszerek hibrid biztonsági stílust használnak, amely egyesíti a hozzáférés-vezérlési listákat (ACL-k) a hagyományos POSIX vagy UNIX-biztonsággal.

Ha a rendszer a biztonsági stílusát UNIX vagy POSIX néven jelenti az "ACL" betűszó nélkül, ez a probléma nincs hatással Önre.

Az ACL-eket használó rendszerek esetében az Azure HPC-gyorsítótárnak további felhasználóspecifikus értékeket kell nyomon követnie a fájlhozzáférés szabályozásához. Ez a hozzáférési gyorsítótár engedélyezésével történik. Nincs felhasználó felé néző vezérlő a hozzáférési gyorsítótár bekapcsolására, de megnyithat egy támogatási jegyet, amely azt kéri, hogy engedélyezve legyen az érintett tárolási célokhoz a gyorsítótár-rendszeren.

## <a name="next-steps"></a>További lépések

Ha olyan problémája van, amellyel ebben a cikkben nem foglalkozott, [nyisson meg egy támogatási jegyet,](hpc-cache-support-ticket.md) hogy szakértői segítséget kapjon.
