---
title: Az Azure Security Center keresési |} A Microsoft Docs
description: Ismerje meg, hogyan az Azure Security Center használja a biztonsági adatok elemzése és beolvashatók az Azure Monitor naplók keresése.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: rkarlin
ms.openlocfilehash: 6cbf3d70bd835ce1b838b19c93507f7d9487a418
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58074505"
---
# <a name="azure-security-center-search"></a>Az Azure Security Center keresési
Használja az Azure Security Center [Azure Monitor naplózza a keresési](../log-analytics/log-analytics-log-searches.md) lekérésére és a biztonsági adatok elemzéséhez. Az Azure Monitor naplóira lekérdezési nyelv segítségével gyorsan és vonhat össze adatokat tartalmaz. A Security Centerből kihasználhatja az Azure Monitor naplók keresési lekérdezéseket hozhat létre, és elemezheti az összegyűjtött adatokat.

Keresés az ingyenes szint és a Standard szintű Security Centerben érhető el.  Elérhető a naplókeresések a adat szolgáltatás függ a alkalmazni a munkaterülethez réteg szerinti szintjére.  Tekintse meg a Security Center [díjszabását ismertető lapon](../security-center/security-center-pricing.md) további információt.


> [!NOTE]
> A Security Center nem menti az ingyenes szint alatt egy munkaterülethez tartozó biztonsági adatokat. Elküldheti a naplókat számos munkaterület az ingyenes és az adatok keresése, de a keresési eredmények nem tartalmazzák a Security Center adatokat. A Security Center csak menti az adatokat a Standard szintű munkaterület.
>
>

## <a name="access-search"></a>Access-keresés
1. A Security Center főmenüjében válassza **keresési**.

   ![Válassza ki a naplók keresése][1]

2. A Security Center az összes munkaterület alatt az Azure-előfizetések sorolja fel. Jelöljön ki egy munkaterületet. (Ha csak egy munkaterületet, a munkaterület-választó nem jelenik meg.)

   ![Válasszon munkaterületet][2]

3. **Naplóbeli keresés** nyílik meg. A kijelölt munkaterület alatt további adatokat kérhet, adja meg a példa lekérdezés:

   SecurityEvent |} ahol eseményazonosító 4625-ös számú == |} count() by TargetAccount összefoglalója

   Eredmény látható összes fiókot, amely nem sikerült a bejelentkezés (4625-ös számú esemény).

   ![Keresési eredmények][3]

Lásd: [Kusto-lekérdezés nyelvi](../log-analytics/log-analytics-search-reference.md) lekérdezési adatok a kijelölt munkaterület alatt módjáról további információt.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerkedhetett a Security Center keresési eléréséhez. A Security Center az Azure Monitor naplók search használja. Az Azure Monitor naplók keresése kapcsolatos további információkért lásd:

- [Mi az Azure Monitor naplóira?](../log-analytics/log-analytics-overview.md) – Áttekintés az Azure Monitor naplóira
- [Naplókereséseit ismertető az Azure Monitor naplóira](../log-analytics/log-analytics-log-search-new.md) – ismerteti a naplókeresések használata az Azure Monitor naplóira, és megadja a fogalmakat, amelyek előtt létrehozni egy naplókeresést értendő
- [Keresse meg a naplókeresések segítségével az Azure Monitor naplóira adatok](../log-analytics/log-analytics-log-searches.md) – útmutató a naplóbeli keresés használatával
- [Kusto keresési referencia](../log-analytics/log-analytics-search-reference.md) – ismerteti a lekérdezési nyelv az Azure Monitor naplóira

A Security Centerrel kapcsolatos további tudnivalókért lásd:

- [A Security Center – áttekintés](security-center-intro.md) – elküldéséhez a Security Center legfontosabb képességei

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
