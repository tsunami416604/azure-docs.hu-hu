---
title: Avere vFXT DNS - Azure
description: DNS-kiszolgáló konfigurálása ciklikus multiplexeléses terheléselosztáshoz az Azure-hoz tartozó Avere vFXT-vel
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 81b53904f85e2ac936195b1e39d7586fd1d47524
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153785"
---
# <a name="avere-cluster-dns-configuration"></a>Avere-fürt DNS-konfigurációja

Ez a szakasz az Avere vFXT-fürt terheléselosztáshoz való konfigurálásának alapjait ismerteti.

Ez a dokumentum *nem tartalmaz* utasításokat a DNS-kiszolgáló azure-környezetben történő beállításához és kezeléséhez.

Ahelyett, hogy ciklikus multiplexelésdns-t használna egy virtuális gép-fürt azure-beli terheléselosztásához, fontolja meg manuális módszerek használatával az IP-címek egyenletes hozzárendelését az ügyfelek között csatlakoztatáskor. Az [Avere-fürt felszerelhető.](avere-vfxt-mount-clients.md)

Tartsa szem előtt az alábbi dolgokat annak eldöntésekor, hogy használ-e DNS-kiszolgálót:

* Ha a rendszert csak az NFS-ügyfelek férnek hozzá, a DNS használata nem kötelező - az összes hálózati cím megadható numerikus IP-címek használatával.

* Ha a rendszer támogatja az SMB (CIFS) hozzáférést, dns szükséges, mert meg kell adnia egy DNS-tartományt az Active Directory-kiszolgálóhoz.

* A Kerberos-hitelesítés használatához DNS szükséges.

## <a name="load-balancing"></a>Terheléselosztás

A teljes terhelés elosztásához konfigurálja a DNS-tartományt úgy, hogy ciklikus multiplexeléses terheléselosztást használjon az ügyféloldali IP-címekhez.

## <a name="configuration-details"></a>A konfiguráció részletei

Amikor az ügyfelek hozzáférnek a fürthöz, az RRDNS automatikusan kiegyensúlyozza a kéréseiket az összes elérhető felület között.

Az optimális teljesítmény érdekében állítsa be a DNS-kiszolgálót úgy, hogy az ügyfélfelé néző fürtcímeket az alábbi ábrán látható módon kezelje.

A fürt vserver jelenik meg a bal oldalon, és IP-címek jelennek meg a középső és a jobb oldalon. Konfigurálja az egyes ügyfél-hozzáférési pontot a látható A rekordokkal és mutatókkal.

![Avere fürt ciklikus multiplexeléses DNS-diagramja](media/avere-vfxt-rrdns-diagram.png)
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

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

## <a name="cluster-dns-settings"></a>Fürt DNS-beállításai

Adja meg a vFXT-fürt által használt DNS-kiszolgálót a **Fürtfelügyeleti** > **hálózat** beállításai lapon. Az oldal beállításai a következők:

* DNS-kiszolgáló címe
* DNS-tartománynév
* DNS-kereső tartományok

A lap használatával kapcsolatos további részletekért olvassa el az Avere fürtkonfigurációs [útmutatódns-beállításokcímű](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) lapját.
