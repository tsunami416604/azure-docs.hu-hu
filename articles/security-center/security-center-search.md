---
title: Az Azure Security Center keresési |} A Microsoft Docs
description: Ismerje meg, hogyan az Azure Security Center használja és a biztonsági adatok elemzése a Log Analytics search.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: ec8c11dcea825dfaab19643e7aefc68a82542fa9
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301531"
---
# <a name="azure-security-center-search"></a>Az Azure Security Center keresési
Használja az Azure Security Center [Log Analytics keresési](../log-analytics/log-analytics-log-searches.md) lekérésére és a biztonsági adatok elemzéséhez. A log Analytics lekérdezési nyelv segítségével gyorsan és vonhat össze adatokat biztosít. A Security Centerből használhatja a Log Analytics keresési lekérdezéseket hozhat létre, és elemezheti az összegyűjtött adatokat.

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

Lásd: [Log Analytics lekérdezési nyelvre](../log-analytics/log-analytics-search-reference.md) lekérdezési adatok a kijelölt munkaterület alatt módjáról további információt.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerkedhetett a Security Center keresési eléréséhez. A Security Center Log Analytics search használja. A Log Analytics search kapcsolatos további információkért lásd:

- [Mi az a Log Analytics?](../log-analytics/log-analytics-overview.md) – Áttekintés a Log Analytics
- [Naplókereséseit ismertető a Log Analytics](../log-analytics/log-analytics-log-search-new.md) – ismerteti a naplókeresések a Log Analytics használata, és megadja a fogalmakat, amelyek előtt létrehozni egy naplókeresést értendő
- [Keresse meg a naplókeresések a Log Analytics adatokat](../log-analytics/log-analytics-log-searches.md) – útmutató a naplóbeli keresés használatával
- [A log Analytics keresési referenciáját bemutató](../log-analytics/log-analytics-search-reference.md) – ismerteti a Log Analytics lekérdezési nyelv

A Security Centerrel kapcsolatos további tudnivalókért lásd:

- [A Security Center – áttekintés](security-center-intro.md) – elküldéséhez a Security Center legfontosabb képességei

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
