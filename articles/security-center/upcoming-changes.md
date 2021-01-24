---
title: Fontos változások a Azure Security Center
description: Előfordulhat, hogy a Azure Security Center közelgő módosításaival tisztában kell lennie a szolgáltatással, és amelyre szükség lehet a tervezéshez
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 1b034c0f1c62eecf8139ed908a5a242060f3e886
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746560"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>A Azure Security Center fontos jövőbeli módosításai

> [!IMPORTANT]
> Az ezen az oldalon található információk a kiadás előtti termékekhez vagy szolgáltatásokhoz kapcsolódnak, amelyek a kereskedelmi forgalomba hozatal előtt jelentős mértékben módosíthatók, ha még nem. A Microsoft nem vállal semmilyen kifejezett vagy vélelmezett szavatosságot az itt megadott információk tekintetében.

Ezen az oldalon megismerheti a Security Center tervezett módosításokat. Ismerteti a termék tervezett módosításait, amelyek hatással lehetnek a biztonságos pontszámra vagy munkafolyamatokra.

Ha a legújabb kibocsátási megjegyzéseket keresi, a [Azure Security Center újdonságai](release-notes.md)között találhatja meg őket.


## <a name="planned-changes"></a>Tervezett változások

- [A Kubernetes munkaterhelés-védelmi javaslatai hamarosan elérhetők lesznek általánosan elérhetővé (GA)](#kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga)
- [A System Updates (rendszerfrissítések alkalmazása) biztonsági ellenőrzésének két javaslata elavult](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Az SQL-adatbesorolásra vonatkozó javaslat továbbfejlesztése](#enhancements-to-sql-data-classification-recommendation)
- [35 az Azure biztonsági teljesítményteszt lefedettségének növeléséhez hozzáadott előzetes javaslatok](#35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark)


### <a name="kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga"></a>A Kubernetes munkaterhelés-védelmi javaslatai hamarosan elérhetők lesznek általánosan elérhetővé (GA)

**Változás becsült dátuma:** Január 2021

A [Kubernetes-munkaterhelések védelme](kubernetes-workload-protections.md) című cikkben ismertetett Kubernetes munkaterhelés-védelmi javaslatok jelenleg előzetes verzióban érhetők el. Habár egy javaslat előzetes verzióban érhető el, nem jelenik meg az erőforrás állapota, és nem szerepel a biztonságos pontszám számításában.

A javaslatok hamarosan elérhetők lesznek általánosan elérhetővé (GA), így a pontszám kiszámítása is *megtörténik.* Ha már nem szervizelte őket, ez a biztonsági pontszám enyhe kihatását okozhatja.

Ha lehetséges, javítsa őket (ha szeretné megismerni, hogyan javíthatja az [ajánlásokat a Azure Security Center](security-center-remediate-recommendations.md)).

A Kubernetes munkaterhelés-védelmi javaslatai a következők:

- A Kubernetes Azure Policy bővítményét telepíteni és engedélyezni kell a fürtökön
- A tároló CPU-és memória-korlátozásait kényszeríteni kell
- Az emelt szintű tárolókat el kell kerülni
- Nem módosítható (csak olvasható) rendszerindító fájlrendszert kell kikényszeríteni a tárolók számára
- A jogosultság-eszkalációs tárolót el kell kerülni
- A tárolók futtatását root felhasználóként el kell kerülni
- A bizalmas gazdagépek névtereit megosztó tárolókat el kell kerülni
- A minimálisan privilegizált Linux-funkciókat kell kikényszeríteni a tárolók számára
- A pod HostPath mennyiségi csatlakoztatások használatát egy ismert listára kell korlátozni
- A tárolóknak csak az engedélyezett portok figyelésére kell figyelniük
- A szolgáltatásoknak csak a megengedett portok figyelésére kell figyelniük
- A gazdagép hálózatkezelésének és portjainak használatát korlátozni kell
- A tárolók AppArmor-profiljának felülbírálását vagy letiltását korlátozni kell
- A tároló lemezképeit csak a megbízható kibocsátásiegység-forgalmi jegyzékből kell telepíteni             

További információ ezekről a javaslatokról a [Kubernetes-munkaterhelések elleni védelemben](kubernetes-workload-protections.md).

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>A System Updates (rendszerfrissítések alkalmazása) biztonsági ellenőrzésének két javaslata elavult 

**Változás becsült dátuma:** Február 2021

A következő két javaslatot ütemezjük a 2021 februárjában:

- A számítógépeket újra kell **indítani a rendszerfrissítések alkalmazásához**. Ez enyhe hatással lehet a biztonságos pontszámra.
- A **figyelési ügynököt telepíteni kell a gépekre**. Ez a javaslat csak a helyszíni gépekre vonatkozik, és a logikája egy másik javaslatba kerül át, **log Analytics az ügynök állapotával kapcsolatos problémákat fel kell oldani a gépeken**. Ez enyhe hatással lehet a biztonságos pontszámra.

Javasoljuk, hogy ellenőrizze a folyamatos exportálási és munkafolyamat-automatizálási konfigurációkat, és ellenőrizze, hogy ezek a javaslatok szerepelnek-e bennük. Emellett az azokat használó irányítópultokat vagy egyéb figyelési eszközöket ennek megfelelően kell frissíteni.

További információ ezekről a javaslatokról a [biztonsági javaslatok hivatkozását ismertető oldalon](recommendations-reference.md)található.


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Az SQL-adatbesorolásra vonatkozó javaslat továbbfejlesztése

**Változás becsült dátuma:** Q2 2021

Az **SQL-adatbázisokban lévő bizalmas adatokra** vonatkozó ajánlás aktuális verzióját az **adatok besorolása** biztonsági ellenőrzés alá kell sorolni, és egy új, a Microsoft adatbesorolási stratégiájának jobban illeszkedő verziójára kell lecserélni. Ennek eredményeképpen:

- A javaslat a továbbiakban nem lesz hatással a biztonságos pontszámra
- A biztonsági vezérlő ("adatbesorolás alkalmazása") már nem lesz hatással a biztonságos pontszámra
- Az ajánlás azonosítója is módosul (jelenleg b0df6f56-862d-4730-8597-38c0fd4ebd59)


### <a name="35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark"></a>35 az Azure biztonsági teljesítményteszt lefedettségének növeléséhez hozzáadott előzetes javaslatok

**Változás becsült dátuma:** Január 2021

Az Azure biztonsági teljesítményteszt a Microsoft által létrehozott, Azure-specifikus irányelvek a biztonsági és megfelelőségi szabályzatok közös megfelelőségi keretrendszereken alapuló bevált eljárásaihoz. [További tudnivalók az Azure-biztonsági teljesítménytesztről](../security/benchmarks/introduction.md).

A jelen teljesítményteszt lefedettségének növeléséhez a következő 35 előzetes verzióra vonatkozó javaslatok lesznek hozzáadva a Security Centerhoz.

Az előzetes verzióra vonatkozó javaslatok nem jelenítik meg az erőforrás állapotát, és nem tartoznak bele a biztonságos pontszám számításaiba. Ha lehetséges, javítsa őket, hogy ha az előzetes verzió időtartama lejár, a pontszáma is hozzájárul. További információ a javaslatokról a [Azure Security Centerban található javaslatok szervizelése](security-center-remediate-recommendations.md)című témakörben található.

| Biztonsági ellenőrzés                     | Új javaslatok                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Titkosítás engedélyezése nyugalmi állapotban            | – Azure Cosmos DB fiókoknak az ügyfelek által felügyelt kulcsokat kell használniuk a REST-adatok titkosításához<br>-Azure Machine Learning munkaterületeket ügyfél által felügyelt kulccsal kell titkosítani (CMK)<br>-A saját kulcsú adatvédelem engedélyezése a MySQL-kiszolgálókon<br>-A saját kulcsú adatvédelem engedélyezése a PostgreSQL-kiszolgálókon<br>-Cognitive Services fiókoknak engedélyeznie kell az adattitkosítást az ügyfél által felügyelt kulccsal (CMK)<br>– A tároló-beállításjegyzékek titkosítása ügyfél által felügyelt kulccsal (CMK) történik<br>– Az SQL felügyelt példányainak az ügyfelek által felügyelt kulcsokat kell használniuk a REST-adatok titkosításához<br>– Az SQL-kiszolgálóknak az ügyfél által felügyelt kulcsokat kell használniuk a REST-adatok titkosításához<br>– A Storage-fiókoknak ügyfél által felügyelt kulcsot (CMK) kell használniuk a titkosításhoz                                                                                                                                                              |
| Ajánlott biztonsági eljárások megvalósítása    | – A biztonsági problémákhoz az előfizetésekhez kapcsolattartó e-mail-címmel kell rendelkeznie<br> – Az Log Analytics ügynök automatikus kiépítés engedélyezése az előfizetésen<br> – Engedélyezni kell a nagy súlyosságú riasztások e-mailes értesítését<br> – Engedélyezni kell a nagy súlyosságú riasztások előfizetésének tulajdonosának szóló e-mailes értesítést.<br> – A Key vaultoknak engedélyezve kell lennie a kiürítési védelemmel<br> – A Key vaultok esetében engedélyezve van a helyreállítható törlés |
| Hozzáférés és engedélyek kezelése        | – A Function alkalmazásoknak engedélyezve kell lennie az "Ügyféltanúsítványok (bejövő Ügyféltanúsítványok)" |
| Alkalmazások elleni védelem a DDoS-támadások ellen | – A webalkalmazási tűzfalat (WAF) engedélyezni kell Application Gateway<br> – Engedélyezni kell a webalkalmazási tűzfalat (WAF) az Azure bejárati szolgáltatásának szolgáltatásához |
| Jogosulatlan hálózati hozzáférés korlátozása | – Engedélyezni kell a tűzfalat Key Vault<br> -A magánhálózati végpontot be kell állítani Key Vault<br> – Az alkalmazás konfigurációjának privát hivatkozást kell használnia<br> – Az Azure cache for Redis-nek egy virtuális hálózaton belül kell lennie<br> – Azure Event Grid tartományoknak privát hivatkozást kell használniuk<br> – Azure Event Grid témaköröknek privát hivatkozást kell használniuk<br> – Azure Machine Learning munkaterületeknek privát hivatkozást kell használniuk<br> – Az Azure Signaler szolgáltatásnak privát hivatkozást kell használnia<br> – Az Azure Spring Cloud-nak hálózati befecskendezést kell használnia<br> – A tároló-beállításjegyzékek nem engedélyezhetik a nem korlátozott hálózati hozzáférést<br> – A tároló-beállításjegyzékeknek privát hivatkozást kell használniuk<br> – A MariaDB-kiszolgálók esetében le kell tiltani a nyilvános hálózati hozzáférést<br> – A nyilvános hálózati hozzáférést le kell tiltani a MySQL-kiszolgálók esetében<br> – A nyilvános hálózati hozzáférést le kell tiltani a PostgreSQL-kiszolgálókon<br> – A Storage-fióknak magánhálózati kapcsolatot kell használnia<br> – A Storage-fiókoknak a virtuális hálózati szabályok használatával kell korlátoznia a hálózati hozzáférést<br> – A virtuálisgép-rendszerkép-készítő sablonoknak privát hivatkozást kell használniuk|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Kapcsolódó hivatkozások:

- [További információ az Azure biztonsági teljesítménytesztről](../security/benchmarks/introduction.md)
- [További információ a Azure Database for MariaDB](../mariadb/overview.md)
- [További információ a Azure Database for MySQL](../mysql/overview.md)
- [További információ a Azure Database for PostgreSQL](../postgresql/overview.md)





## <a name="next-steps"></a>További lépések

A termék legutóbbi módosításaival kapcsolatban tekintse [meg az Újdonságok a Azure Security Center?](release-notes.md)című témakört.