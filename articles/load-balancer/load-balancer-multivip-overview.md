---
title: Az Azure több Frontends terheléselosztó |} Microsoft Docs
description: Több Frontends az Azure Load Balancer áttekintése
services: load-balancer
documentationcenter: na
author: chkuhtz
manager: narayan
editor: ''
ms.assetid: 748e50cd-3087-4c2e-a9e1-ac0ecce4f869
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: chkuhtz
ms.openlocfilehash: cf8fa396e0518e1c847225dfc1d8f91c3421bd11
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Az Azure Load Balancer több Frontends

Az Azure terheléselosztó lehetővé teszi, hogy egyenleg szolgáltatások több portot vagy több IP-címet. Nyilvános és a belső terheléselosztó definíciók segítségével egyenleg adatfolyamok betölteni a virtuális gépek csoportja között.

Ez a cikk ismerteti a lehetősége, fontos fogalmakat és korlátozások alapjait. Ha csak egy IP-címet a szolgáltatások közzétételét, egyszerűsített utasításokat található [nyilvános](load-balancer-get-started-internet-portal.md) vagy [belső](load-balancer-get-started-ilb-arm-portal.md) terheléselosztó konfigurációjában betölteni. Több frontends hozzáadása akkor növekményes egyetlen előtér-konfigurációhoz. A koncepció ismertetése érdekében használja a cikkben, bővítheti egy egyszerűsített konfigurációs bármikor.

Ha az Azure Load Balancer, egy előtér- és egy háttér címkészletet konfigurációs csatlakoztatott szabályok. A szabály által hivatkozott állapotmintáihoz azt határozza meg, hogyan új forgalom háttérkészlethez csomópont küldött. Az előtér (más néven VIP) 3-rekordot egy IP-cím (nyilvános vagy belső), egy átviteli protokoll (UDP vagy TCP) és a terheléselosztási szabály egy portot határozza meg. A háttérkészlet virtuális gép IP-konfigurációk (a hálózati erőforrás része), amelyik a terheléselosztó háttérkészletéből gyűjteménye.

Az alábbi táblázatban néhány példa előtér konfigurációkat tartalmazza:

| Előtér | IP-cím | protokoll | port |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

A táblázat négy különböző frontends. Frontends #1, #2 és a #3 több szabályokkal egyetlen időtúllépést. Az ugyanazon az IP-cím, de a port vagy a protokoll nem azonos az összes előtérbeli. Frontends #1 és #4 is több frontends, például ha előtér ugyanazon protokoll és port újra felhasználja a rendszer több frontends között.

Az Azure terheléselosztó a terheléselosztási szabályok definiálása rugalmasságot biztosít. A szabály azt deklarálja, hogy hogyan egy címet és portot a előtér van leképezve a rendeltetési címet és portot a háttérkiszolgálón. A szabály típusától függ-e szabályok között háttérportokat fel újra. Minden szabály megvan konkrét követelmények, amelyek hatással lehetnek a gazdagép-konfiguráció és a mintavételi megtervezése. A szabályok két típusa van:

1. Az alapértelmezett szabály nem háttér port újbóli
2. A fix IP-Címek szabály, ahol háttérportokat újra felhasználja a rendszer

Az Azure terheléselosztó az azonos terheléselosztó-konfigurációt a szabály mindkétféle kombinálhatók teszi lehetővé. A load balancer használhatja őket egyszerre egy adott virtuális Gépet, vagy tetszőleges kombinációját, mindaddig, amíg Ön elfogadják a szabály a korlátozásait. Úgy dönt, a szabály típusát az alkalmazás követelményeinek és, hogy a konfigurálás támogató bonyolultságától függ. Akkor ki kell értékelnie, mely szabálytípusok ajánlott a forgatókönyvéhez.

Az alapértelmezett viselkedés kezdve további forgatókönyvekben azt vizsgálatát.

## <a name="rule-type-1-no-backend-port-reuse"></a>Szabály típusa #1: nincs háttér port újbóli

![A zöld és a lila előtér több előtér ábra](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

Ebben a forgatókönyvben a frontends az alábbiak szerint vannak konfigurálva:

| Előtér | IP-cím | protokoll | port |
| --- | --- | --- | --- |
| ![zöld előtér](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![lila előtér](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

A DIP-ről az a hely a bejövő adatfolyam. A háttérkészlet minden virtuális gép egy egyedi portot, a DIP-ről a kívánt szolgáltatás elérhetővé teszi. Ez a szolgáltatás társítva az előtér egy szabályát leíró definíció beolvasása keresztül.

Azt adja meg a két szabályt:

| Szabály | Előtérbeli leképezése | A háttérkészlet |
| --- | --- | --- |
| 1 |![zöld előtér](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![háttér](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![háttér](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![háttér](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![háttér](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

A teljes leképezés az Azure Load Balancer jelenleg az alábbiak szerint:

| Szabály | Előtérbeli IP-cím | protokoll | port | Cél | port |
| --- | --- | --- | --- | --- | --- |
| ![zöld szabály](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |DIP IP-cím |80 |
| ![lila szabály](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |DIP IP-cím |81 |

Minden egyes szabály a folyamat a cél IP-cím és a célport egyedi kombinációját kell eredményez. A folyamat célport változtatásával több szabály és a különböző porttal azonos DIP által biztosított adatfolyamok.

A virtuális gépek DIP állapotteljesítmény mindig irányítja. Győződjön meg arról, hogy a mintavétel tükrözi a virtuális gép állapotát.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Szabály típusa #2: háttér port újbóli fix IP-Címek használatával

Az Azure Load Balancer felhasználhatja az elülső rétegbeli portot között használt szabálytípus függetlenül több frontends biztosít. Emellett egyes alkalmazás-forgatókönyvek inkább, vagy több alkalmazáspéldányt a háttérkészlethez egyetlen virtuális gép által használandó ugyanazt a portot szükséges. Például a port újbóli magában foglalhatja a fürtözése a magas rendelkezésre állású, hálózati, a virtuális készülékek, és adjon meg több TLS végpont ismételt titkosítás nélkül.

Ha azt szeretné, a háttérportot újból között több szabály, engedélyeznie kell a szabályát leíró definíció beolvasása fix IP-Címek.

"Fix IP-címe" az Azure-terminológia egy részének, mint a közvetlen kiszolgálói vissza (DSR) néven ismert. DSR két részből áll: a folyamat-topológia és egy IP-cím a leképezési séma. A platform szintjén Azure Load Balancer mindig működik, függetlenül attól, hogy a fix IP-Címek engedélyezve van-e vagy sem DSR folyamata topológiában. Ez azt jelenti, hogy a kimenő részét a folyamat mindig megfelelően átírása rendszer közvetlenül vissza az eredeti.

Alapértelmezett szabály típusú Azure egy hagyományos terheléselosztási IP cím leképezési séma könnyű használatra elérhetővé teszi. Az IP cím leképezési séma kiegészítésekkel további rugalmasságával lehetővé fix IP-Címek engedélyezése módosítja.

Az alábbi ábrán ez a konfiguráció látható:

![A zöld és a lila elülső rétegbeli DSR több előtér ábra](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

Ebben a forgatókönyvben minden virtuális gép háttérkészlethez három hálózati adapterrel rendelkezik:

* DIP: egy virtuális hálózati Adaptert a virtuális gép (IP-konfiguráció Azure hálózati erőforrás) társított
* 1. előtér: egy visszacsatolási belül a vendég operációs rendszer, amely 1 előtérbeli IP-címmel van konfigurálva
* 2. előtér: egy visszacsatolási belül a vendég operációs rendszer, amely 2 előtérbeli IP-címmel van konfigurálva

> [!IMPORTANT]
> A konfiguráció visszacsatolási csatolók belül a vendég operációs rendszer történik. Ez a konfiguráció nem végre vagy Azure által kezelt. E beállítások nélkül a szabályok nem működnek. Állapotfigyelő mintavételi definíciókat a DIP-ről a DSR előtér képviselő visszacsatolási, hanem a virtuális gép használja. Ezért a szolgáltatás biztosítania kell mintavételi válaszok, amelyek a szolgáltatás a DSR előtér képviselő visszacsatolási kínált állapotát tükrözik DIP porton.

Tegyük fel, ahogy az előző példában ugyanazt az előtérbeli konfigurációt:

| Előtér | IP-cím | protokoll | port |
| --- | --- | --- | --- |
| ![zöld előtér](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![lila előtér](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Azt adja meg a két szabályt:

| Szabály | Előtér | Háttérkészlet leképezése |
| --- | --- | --- |
| 1 |![szabály](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![háttér](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) (A VM1 és vm2 virtuális gépnek) Frontend1:80 |
| 2 |![szabály](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![háttér](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) (A VM1 és vm2 virtuális gépnek) Frontend2:80 |

Az alábbi táblázat mutatja a teljes leképezés a terheléselosztó:

| Szabály | Előtérbeli IP-cím | protokoll | port | Cél | port |
| --- | --- | --- | --- | --- | --- |
| ![zöld szabály](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |ugyanaz, mint az előtéri (65.52.0.1) |ugyanaz, mint az előtéri (80-as) |
| ![lila szabály](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |ugyanaz, mint az előtéri (65.52.0.2) |ugyanaz, mint az előtéri (80-as) |

A bejövő folyamat célja a virtuális gép visszacsatolási előtérbeli IP-címét. Minden egyes szabály a folyamat a cél IP-cím és a célport egyedi kombinációját kell eredményez. A cél IP-címe a folyamat változtatásával port újbóli lehetőség, az azonos virtuális gépen. A szolgáltatás által az előtérbeli IP-cím és port a megfelelő visszacsatolási való kötés van kitéve a terheléselosztót.

Figyelje meg, hogy ez a példa nem változtatja meg a célport. Annak ellenére, hogy ez egy webfarmos fix IP-Címek, az Azure Load Balancer is támogatja újraírnia a háttér-célport és abba, hogy a cél az előtérportot eltérő szabályt.

A fix IP-Címek szabálytípus az alapja a terheléselosztói konfiguráció-minták több betöltése. Ilyen például, hogy jelenleg rendelkezésre áll a [több figyelők az SQL AlwaysOn](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) konfigurációs. Adott idő alatt azt fogja a dokumentum több forgatókönyvekben.

## <a name="limitations"></a>Korlátozások

* Több előtér-konfiguráció csak az infrastruktúra-szolgáltatási virtuális gépek támogatottak.
* A fix IP-Címek szabállyal az alkalmazást kell használnia az elsődleges IP-konfiguráció kimenő forgalom. Ha az alkalmazás az előtérbeli IP-cím konfigurálva van kötve a visszacsatolás a felület a vendég operációs rendszer, Azure tartozó SNAT nem elérhető legyen a kimenő folyam újraírási és a folyamat sikertelen lesz.
* Nyilvános IP-címek számlázási hatással. További információkért lásd: [IP-cím díjszabása](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Érvényes előfizetési korlátozásait. További információkért lásd: [szolgáltatási korlátait](../azure-subscription-service-limits.md#networking-limits) részleteiről.

## <a name="next-steps"></a>További lépések

- Felülvizsgálati [kimenő kapcsolatok](load-balancer-outbound-connections.md) tudni, hogy a kimenő kapcsolat viselkedés több frontends hatását.
