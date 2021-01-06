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
ms.date: 01/05/2021
ms.author: memildin
ms.openlocfilehash: 0656000a1d6449306e8afe538f846c55c79a31a2
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915286"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>A Azure Security Center fontos jövőbeli módosításai

> [!IMPORTANT]
> Az ezen az oldalon található információk a kiadás előtti termékekhez vagy szolgáltatásokhoz kapcsolódnak, amelyek a kereskedelmi forgalomba hozatal előtt jelentős mértékben módosíthatók, ha még nem. A Microsoft nem vállal semmilyen kifejezett vagy vélelmezett szavatosságot az itt megadott információk tekintetében.

Ezen az oldalon megismerheti a Security Center tervezett módosításokat. Ismerteti a termék tervezett módosításait, amelyek hatással lehetnek a biztonságos pontszámra vagy munkafolyamatokra.

Ha a legújabb kibocsátási megjegyzéseket keresi, a [Azure Security Center újdonságai](release-notes.md)között találhatja meg őket.


## <a name="planned-changes"></a>Tervezett változások

- ["Nem alkalmazható" erőforrások, amelyeket "megfelelőként" kell jelenteni Azure Policy értékelésekben](#not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments)
- [35 az Azure biztonsági teljesítményteszt lefedettségének növeléséhez hozzáadott előzetes javaslatok](#35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark)

### <a name="not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments"></a>"Nem alkalmazható" erőforrások, amelyeket "megfelelőként" kell jelenteni Azure Policy értékelésekben

**Változás becsült dátuma:** Január 2021

Jelenleg a javaslathoz kiértékelt és a **nem** megfelelőnek talált erőforrások megjelennek a Azure Policy nem megfelelőként. Egyetlen felhasználói művelet sem változtathatja meg a "megfelelő" állapotot. Ebből a tervezett változásból a rendszer "megfelelőként" fogja jelenteni a jobb érthetőség érdekében.

Az egyetlen hatása a Azure Policy, ahol a megfelelő erőforrások száma növekedni fog. Azure Security Center a biztonságos pontszáma nem lesz hatással.

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





## <a name="next-steps"></a>Következő lépések

A termék legutóbbi módosításaival kapcsolatban tekintse [meg az Újdonságok a Azure Security Center?](release-notes.md)című témakört.