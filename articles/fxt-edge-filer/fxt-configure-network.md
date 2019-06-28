---
title: A Microsoft Azure FXT Edge Filer fürt hálózati beállításainak módosítása
description: Hálózati beállítások testreszabása az Azure FXT Edge Filer fürt létrehozását követően
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 6b7c3099415aed9529727a1de30cd832189db58d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450379"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Oktatóanyag: A fürt hálózati beállítások konfigurálása 

Mielőtt az újonnan létrehozott Azure FXT Edge Filer fürtöt használ, ellenőrizze, és testre szabhatja a több hálózati beállításokat a munkafolyamathoz. 

Ez az oktatóanyag azt ismerteti, hogy a hálózati beállításokat, előfordulhat, hogy módosítania kell egy új fürtöt. 

Az oktatóanyagban érintett témák köre: 

> [!div class="checklist"]
> * Hálózati beállítások körét előfordulhat, hogy a fürt létrehozása után frissíteni kell
> * Melyik FXT Edge Filer használati esetek szükséges egy AD-kiszolgáló vagy a DNS-kiszolgáló 
> * Ciklikus időszeletelési DNS (RRDNS) automatikusan betölteni az ügyfél érkező kérések elosztása a FXT fürt konfigurálása

A lépések elvégzéséhez szükséges idő attól függ, a rendszer hány konfigurációs módosítások szükségesek:

* Csak szüksége, olvassa el az oktatóanyagot, és ellenőrizze a néhány beállítást, ha 10 – 15 percet kell vennie. 
* Ciklikus időszeletelési DNS konfigurálása van szüksége, ha a feladat egy vagy több óráig is igénybe vehet.

## <a name="adjust-network-settings"></a>Hálózati beállítások módosítása

Több hálózatokhoz kapcsolódó feladatokat beállítása egy új Azure FXT Edge Filer fürt részét képezik. Ellenőrizze a listában, és döntse el, melyeket a rendszer alkalmazni.

A fürt hálózati beállításaival kapcsolatos további tudnivalókért olvassa el a [hálózati szolgáltatások konfigurálása](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) az útmutató a fürt konfiguráció.

* (Nem kötelező) az ügyfél által használt hálózat ciklikus időszeletelési DNS konfigurálása

  Fürt forgalom terheléselosztása betöltheti leírtak szerint állítja be a DNS rendszer [DNS konfigurálása a FXT Edge Filer fürt](#configure-dns-for-load-balancing).

* NTP-beállítások ellenőrzése

* Az Active Directory és a felhasználónevet vagy csoportnevet letöltések konfigurálása (ha szükséges)

  Ha a hálózati állomások használja az Active Directory vagy más típusú külső címtárszolgáltatás, módosítania kell a fürt directory services-konfiguráció hogyan letölti a fürt a felhasználónév-és beállításához. Olvasási **fürt** > **címtárszolgáltatások** az útmutató a fürt konfiguráció részleteiről.

  Egy AD-kiszolgálóra szükség, ha azt szeretné, SMB támogatása. Konfigurálja az AD SMB beállításához megkezdése előtt.

* Adja meg a VLAN-ok (nem kötelező)
  
  Minden további VLAN-ok szükséges a fürt vservers és globális névtér definiálása előtt konfigurálja. Olvasási [VLAN-OK használata](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) az útmutató a fürt konfiguráció további.

* Proxy-kiszolgálók konfigurálása (ha szükséges)

  Ha a fürt külső címek eléréséhez proxykiszolgálót használ, állítsa be az alábbi lépésekkel:

  1. Adja meg a proxykiszolgáló a a **proxykonfiguráció** beállítások lap
  1. A proxykiszolgáló beállításait a alkalmazni a **fürt** > **általános beállítások** lap vagy az **Core Filer részletei** lapot.
  
  További információkért olvassa el [használatával a webes proxykat](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) az útmutató a fürt konfiguráció.

* Töltse fel [titkosítási tanúsítványok](#encryption-certificates) a fürt használata (nem kötelező)

### <a name="encryption-certificates"></a>Titkosítási tanúsítványok

A FXT Edge Filer fürt X.509-tanúsítványokat használ a ezek a függvények:

* A fürt felügyeleti forgalom titkosításához

* Külső KMIP kiszolgálók az ügyfél nevében hitelesítéséhez

* A felhőbeli szolgáltatók kiszolgálói tanúsítványok ellenőrzése

Ha a tanúsítványok feltöltése a fürtre van szüksége, használja a **fürt** > **tanúsítványok** beállítások lapon. Részleteket a [fürt > Tanúsítványok](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) útmutató a fürt konfiguráció lapján.

Fürt felügyeleti kommunikáció titkosításához, használja a **fürt** > **általános beállítások** beállítások oldalon válassza ki, melyik felügyeleti SSL-hez használandó tanúsítványt.

> [!Note] 
> Felhőalapú szolgáltatás hozzáférési kulcsok segítségével tárolják a **Felhőhöz tartozó hitelesítő adatok** konfigurációs lapon. A [adjon hozzá egy alapvető filer](fxt-add-storage.md#add-a-core-filer) szakasz fölött látható egy példa; a fürt konfiguráció útmutatójának elolvasásához [Felhőhöz tartozó hitelesítő adatok](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) című szakasz részletezi. 

## <a name="configure-dns-for-load-balancing"></a>DNS konfigurálása terheléselosztásra

Ez a szakasz egy ciklikus időszeletelési DNS (RRDNS) rendszer a ügyfél terheléselosztásban, többek között a FXT Edge Filer fürtben az összes ügyfél által használt IP-cím konfigurálása alapjait ismerteti. 

### <a name="decide-whether-or-not-to-use-dns"></a>Döntse el, hogy e DNS-ÉT használhatja

Terheléselosztás mindig ajánlott, de nem kell mindig használja a DNS. Például az ügyfél a munkafolyamatok bizonyos típusú, előfordulhat, hogy több értelme egy parancsfájl segítségével hozzárendelni a fürt IP-címeket az ügyfelek közötti egyenletes, ha azok csatlakoztatni a fürthöz. Egyes metódusok ismertetett [a fürt csatlakoztatási](fxt-mount-clients.md). 

Vegye figyelembe ezeket a módosításokat, amikor a annak eldöntése, hogy a DNS-kiszolgáló használata: 

* A rendszer csak az NFS-ügyfelek érhető el, ha a DNS, nem szükséges. Az összes hálózati címek megadása numerikus IP-címek használatával lehetőség. 

* Ha a rendszer támogatja az SMB (CIFS) hozzáférés, DNS megadása kötelező, mivel az Active Directory-kiszolgáló DNS-tartományt adjon meg.

* DNS használata szükséges, ha azt szeretné, a Kerberos-hitelesítés használatára.

### <a name="round-robin-dns-configuration-details"></a>Ciklikus időszeletelési DNS-konfiguráció részletei

Amikor az ügyfelek hozzáférnek a fürt, RRDNS automatikusan elosztja a kérelmeket az összes rendelkezésre álló kapcsolatok között.

Az optimális teljesítmény érdekében konfigurálja a DNS-kiszolgáló, ügyfél által használt fürt címek kezelésére, az alábbi ábrán látható módon.

Egy fürt vserver a bal oldalon látható, és IP-címek a center és a jobb oldalon jelennek meg. Minden egyes ügyfél-hozzáférési pont konfigurálása a rekordok és -mutatók szemléltetett módon.

![Fürt ciklikus időszeletelési DNS diagram – részletes helyettesítő szöveg hivatkozás a következő kép](media/fxt-rrdns-diagram.png) 
[részletes leírása](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html)

Minden ügyfél által használt IP-cím a fürt rendelkeznie kell egy egyedi nevet a belső használatra. (Az alábbi ábrán az ügyfél IP-címek vs1 nevesített – ügyfél - IP-* ajánlattartalomnak, de éles környezetben valószínűleg használjon valami hasonló ügyfél * tömörebb.)

Az ügyfelek a fürt kiszolgálói argumentumként a vserver név használatával csatlakoztassa. 

Módosítsa a DNS-kiszolgáló ``named.conf`` fájlt, ha szeretné a vserver ciklikus ahhoz, hogy a lekérdezések. Ez a beállítás biztosítja, hogy az összes rendelkezésre álló értékek keresztül szakad. Adjon hozzá egy utasítást a következőhöz hasonló:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

A következő ``nsupdate`` parancsok DNS konfigurálása megfelelően példát kell megadni:

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

### <a name="enable-dns-in-the-cluster"></a>A fürt DNS engedélyezése 

Adja meg a fürt által használt a DNS-kiszolgáló a **fürt** > **felügyeleti hálózati** beállítások lapon. A lapon megjelenő beállítások a következők:

* DNS-kiszolgáló címe
* DNS domain name
* DNS search domains

További tudnivalókért olvassa el [DNS-beállítások](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) az útmutató a fürt konfiguráció.

## <a name="next-steps"></a>További lépések

Ez a alapvető konfigurációs utolsó lépése a Azure FXT Edge Filer fürt. 

* Ismerje meg a rendszer LED-EK és az egyéb jeleket [hardver állapotának figyelése](fxt-monitor.md).
* Ismerje meg, hogyan ügyfelek kell csatlakoztatni, a FXT Edge Filer bővebben a fürt [a fürt csatlakoztatási](fxt-mount-clients.md). 
* Működő, és a egy FXT Edge Filer fürt kezelésével kapcsolatos további információkért lásd: a [fürt beállítási útmutató](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html). 