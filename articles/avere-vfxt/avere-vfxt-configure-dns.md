---
title: Avere vFXT DNS – Azure
description: A Ciklikus időszeleteléses terheléselosztás Avere vFXT az Azure DNS-kiszolgáló konfigurálása
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 9fd9eaf1e62d063026e0e656346baaaade87064f
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54187144"
---
# <a name="avere-cluster-dns-configuration"></a>Avere-fürt DNS-konfigurációja

Ez a szakasz egy DNS-rendszerében a terheléselosztási a Avere vFXT fürt konfigurálása alapjait ismerteti. 

Ez a dokumentum *nem tartalmaz* beállítása és felügyelete az Azure-beli DNS-kiszolgáló vonatkozó utasításokat. 

Terheléselosztás ciklikus időszeletelési DNS használata helyett egy vFXT fürtöt az Azure-ban, fontolja meg manuális módszerrel az ügyfelek közötti egyenletes IP-címek kiosztására, ha csatlakoztatva vannak. Több módszert is ismertetett [a Avere fürt csatlakoztatási](avere-vfxt-mount-clients.md). 

Vegye figyelembe ezeket a módosításokat, amikor a annak eldöntése, hogy a DNS-kiszolgáló használata: 

* A rendszer csak az NFS-ügyfelek érhető el, ha DNS-sel, nem szükséges – az összes hálózati címek megadása numerikus IP-címek használatával lehetséges. 

* Ha a rendszer támogatja az SMB (CIFS) hozzáférés, DNS megadása kötelező, mivel az Active Directory-kiszolgáló DNS-tartományt adjon meg.

* DNS használata szükséges, ha azt szeretné, a Kerberos-hitelesítés használatára.

## <a name="load-balancing"></a>Terheléselosztás

A teljes terhelés elosztása érdekében konfigurálja a DNS-tartomány, Ciklikus időszeleteléses terheléselosztás használatához az ügyfél által használt IP-címeket.

## <a name="configuration-details"></a>Konfiguráció részletei

Amikor az ügyfelek hozzáférnek a fürt, RRDNS automatikusan elosztja a kérelmeket az összes rendelkezésre álló kapcsolatok között.

Az optimális teljesítmény érdekében konfigurálja a DNS-kiszolgáló, ügyfél által használt fürt címek kezelésére, az alábbi ábrán látható módon.

Egy fürt vserver a bal oldalon látható, és IP-címek a center és a jobb oldalon jelennek meg. Minden egyes ügyfél-hozzáférési pont konfigurálása a rekordok és -mutatók szemléltetett módon.

![Avere fürt ciklikus időszeletelési DNS diagramja](media/avere-vfxt-rrdns-diagram.png) 
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

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

A következő nsupdate parancsokat a DNS megfelelően konfigurálása példát kell megadni:

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

## <a name="cluster-dns-settings"></a>Fürt DNS-beállítások

Adja meg a DNS-kiszolgáló, amely a vFXT fürt használja a **fürt** > **felügyeleti hálózati** beállítások lapon. A lapon megjelenő beállítások a következők:

* DNS-kiszolgáló címe
* DNS-tartománynév
* DNS-keresési tartományok

Olvasási [DNS-beállítások](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) az Avere fürt konfigurálása útmutatóban ezt oldal használatával kapcsolatos további részletekért.


