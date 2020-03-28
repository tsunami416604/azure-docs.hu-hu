---
title: 'Oktatóanyag: Hálózat konfigurálása Egy Azure FXT Edge Filer-fürtben'
description: A hálózati beállítások testreszabása az Azure FXT Edge Filer fürt létrehozása után
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: a40ff50dcb0934cbf1ea5222675bd75948ac1d03
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239797"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Oktatóanyag: A fürt hálózati beállításainak konfigurálása

Mielőtt egy újonnan létrehozott Azure FXT Edge Filer-fürtöt használna, ellenőrizze és szabja testre a munkafolyamat több hálózati beállítását. 

Ez az oktatóanyag ismerteti az új fürthöz szükséges hálózati beállításokat. 

Az oktatóanyagban érintett témák köre: 

> [!div class="checklist"]
> * Mely hálózati beállításokat kell frissíteni a fürt létrehozása után
> * Mely Azure FXT Edge Filer használati esetekhez szükséges AD-kiszolgáló vagy DNS-kiszolgáló 
> * Ciklikus multiplexeléses DNS (RRDNS) beállítása az FXT-fürtügyfél-kérelmek automatikus terheléséhez

A lépések végrehajtásához szükséges idő attól függ, hogy hány konfigurációs módosításra van szükség a rendszerben:

* Ha csak át kell olvasnia a bemutatót, és ellenőriznie kell néhány beállítást, akkor 10-15 percet vesz igénybe. 
* Ha ciklikus multiplexeléses DNS-t kell konfigurálnia, a feladat akár egy órát is igénybe vehet.

## <a name="adjust-network-settings"></a>Hálózati beállítások módosítása

Számos hálózattal kapcsolatos feladat egy új Azure FXT Edge Filer-fürt beállításának részét képezi. Ellenőrizze ezt a listát, és döntse el, hogy melyik vonatkozik a rendszerre.

Ha többet szeretne tudni a fürt hálózati beállításairól, olvassa el a [Hálózati szolgáltatások konfigurálása](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) című témakört a Fürtkonfigurációs útmutatóban.

* Ciklikus multiplexeléses DNS konfigurálása az ügyfélfelé néző hálózathoz (nem kötelező)

  Terheléselosztási fürtforgalom a DNS-rendszer konfigurálásával az [FXT Edge Filer fürt DNS-ének konfigurálása című](#configure-dns-for-load-balancing)témakörben leírtak szerint.

* NTP-beállítások ellenőrzése

* Az Active Directory és a felhasználónév/csoport nevének letöltésének konfigurálása (ha szükséges)

  Ha a hálózati állomások active directoryt vagy más típusú külső címtárszolgáltatást használnak, módosítania kell a fürt címtárszolgáltatások konfigurációját, hogy a fürt hogyan töltse le a felhasználónevet és a csoportadatokat. A részleteket a Fürtkonfigurációs útmutató > **fürtcímtár-szolgáltatásai** között olvassa el. **Cluster**

  Az SMB-támogatás esetén AD-kiszolgálóra van szükség. Az SMB beállításának megkezdése előtt konfigurálja az AD-t.

* VLAN-ok definiálása (nem kötelező)
  
  Konfigurálja a fürt v-kiszolgálóinak és globális névterének definiálásához szükséges további VLAN-okat. További információ a [VLAN-okkal való együttműködés](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) a fürtkonfigurációs útmutatóban.

* Proxykiszolgálók konfigurálása (ha szükséges)

  Ha a fürt proxykiszolgálót használ a külső címek eléréséhez, az alábbi lépésekkel állíthatja be:

  1. A proxykiszolgáló definiálása a **Proxy konfigurációja** lapon
  1. Alkalmazza a proxykiszolgáló konfigurációját a **Fürt** > **általános beállítása** vagy a Core **Filer Részletei** lapra.
  
  További információt a Fürtkonfigurációs útmutató [Webes proxyk használata](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) című részben talál.

* Titkosítási [tanúsítványok](#encryption-certificates) feltöltése a fürt számára (nem kötelező)

### <a name="encryption-certificates"></a>Titkosítási tanúsítványok

Az FXT Edge Filer fürt X.509 tanúsítványokat használ a következő függvényekhez:

* Fürtfelügyeleti forgalom titkosítása

* Hitelesítés egy ügyfél nevében harmadik fél KMIP-kiszolgálókfelé

* A felhőszolgáltatók kiszolgálói tanúsítványainak ellenőrzéséhez

Ha tanúsítványokat kell feltöltenie a fürtbe, használja a > **Fürttanúsítványok** beállításai lapot. **Cluster** A részletek a fürtkonfigurációs útmutató [fürt> tanúsítványok](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) lapján találhatók.

A fürtkezelési kommunikáció titkosításához a **Fürt** > **általános beállítási** beállításai lapon válassza ki, hogy melyik tanúsítványt használja a felügyeleti SSL-hez.

> [!Note] 
> A felhőszolgáltatás hozzáférési kulcsait a **felhőbeli hitelesítő adatok konfigurációs** lapja tárolja. A [fenti Core filer](fxt-add-storage.md#add-a-core-filer) hozzáadása szakasz egy példát mutat be; a részleteket a Fürtkonfigurációs útmutató [felhőbeli hitelesítő adatai](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) című részében találja. 

## <a name="configure-dns-for-load-balancing"></a>A DNS konfigurálása terheléselosztáshoz

Ez a szakasz a ciklikus multiplexelésű DNS (RRDNS) rendszer konfigurálásának alapjait ismerteti az ügyfelek terhelésének elosztásához az FXT Edge Filer-fürt összes ügyféloldali IP-címe között. 

### <a name="decide-whether-or-not-to-use-dns"></a>Annak eldöntése, hogy a DNS-t használja-e vagy sem

A terheléselosztás mindig ajánlott, de nem kell mindig a DNS-t használnia. Bizonyos típusú ügyfél-munkafolyamatok esetén például célszerűbb lehet parancsfájlhasználatával a fürt IP-címeinek egyenletes hozzárendelése az ügyfelek között a fürt csatlakoztatásakor. Néhány módszert [a Fürt csatlakoztatása](fxt-mount-clients.md)című részben ismertetett. 

Tartsa szem előtt az alábbi dolgokat annak eldöntésekor, hogy használ-e DNS-kiszolgálót: 

* Ha a rendszert csak NFS-ügyfelek érhetik el, a DNS nem kötelező. Az összes hálózati cím numerikus IP-címek használatával is megadható. 

* Ha a rendszer támogatja az SMB (CIFS) hozzáférést, dns szükséges, mert meg kell adnia egy DNS-tartományt az Active Directory-kiszolgálóhoz.

* A Kerberos-hitelesítés használatához DNS szükséges.

### <a name="round-robin-dns-configuration-details"></a>Ciklikus multiplexeléses DNS-konfiguráció részletei

Amikor az ügyfelek hozzáférnek a fürthöz, az RRDNS automatikusan kiegyensúlyozza a kéréseiket az összes elérhető felület között.

Az optimális teljesítmény érdekében állítsa be a DNS-kiszolgálót úgy, hogy az ügyfélfelé néző fürtcímeket az alábbi ábrán látható módon kezelje.

A fürt vserver jelenik meg a bal oldalon, és IP-címek jelennek meg a középső és a jobb oldalon. Konfigurálja az egyes ügyfél-hozzáférési pontot a látható A rekordokkal és mutatókkal.

![Fürt ciklikus multiplexeléses DNS-diagramja – a részletes helyettesítő szöveghivatkozás a kép](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
részletes szöveges[leírását](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html) követi

Minden ügyfélfelé néző IP-címnek egyedi névvel kell rendelkeznie a fürt belső használatához. (Ebben az ábrán az ügyfél IP-k neve vs1-client-IP-* az egyértelműség érdekében, de éles környezetben érdemes használni valami tömörebb, mint az ügyfél *.)

Az ügyfelek a virtuális kiszolgáló nevét használva csatlakoztatják a fürtöt kiszolgálói argumentumként. 

Módosítsa a DNS-kiszolgáló fájlját ``named.conf`` a vserver lekérdezések ciklikus sorrendjének beállításához. Ez a beállítás biztosítja, hogy az összes rendelkezésre álló érték végighalad. Adjon hozzá egy utasítást, mint a következő:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

A ``nsupdate`` következő parancsok a DNS megfelelő konfigurálására nyújtanak példát:

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

### <a name="enable-dns-in-the-cluster"></a>Dns engedélyezése a fürtben 

Adja meg a fürt által használt DNS-kiszolgálót a **Fürtfelügyeleti** > **hálózat** beállításai lapon. Az oldal beállításai a következők:

* DNS-kiszolgáló címe
* DNS-tartománynév
* DNS-kereső tartományok

További részletekért olvassa el a [DNS-beállítások című](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) részt a Fürtkonfigurációs útmutatóban.

## <a name="next-steps"></a>További lépések

Ez az Azure FXT Edge Filer-fürt utolsó alapszintű konfigurációs lépése. 

* Ismerje meg a rendszer LED-jeit és egyéb jelzőit a [Hardver állapotának figyelése című](fxt-monitor.md)részben.
* További információ arról, hogy az ügyfelek hogyan kell csatlakoztatniuk az FXT Edge Filer fürtöt [a fürt csatlakoztatása kor.](fxt-mount-clients.md) 
* Az FXT Edge Filer fürt kezeléséről további információt a [Fürtkonfigurációs útmutatóban talál.](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) 