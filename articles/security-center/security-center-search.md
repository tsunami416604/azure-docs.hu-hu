---
title: "Az Azure Security Center keresési |} Microsoft Docs"
description: "Ismerje meg, hogyan az Azure Security Center segítségével Naplóelemzési keresési beolvasása és a biztonsági adatok elemzése."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 513c98237a322dabd6b2bf13443e8998ca843b1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-search"></a>Az Azure Security Center keresése
Az Azure Security Center által használt [Naplóelemzési keresési](../log-analytics/log-analytics-log-searches.md) lekérdezéséhez és elemezheti a biztonsági adatokat. A Naplóelemzési lekérdezésnyelvet gyorsan lekérdezéséhez és összevonni az adatokat tartalmazza. A Security Centerből kihasználhatja Naplóelemzési keresési lekérdezések összeállításához, és a gyűjtött adatok elemzésére.

Keresés az ingyenes szint és a Standard szint a Security Center érhető el.  A napló keresések elérhető adatok a réteg szintjét a munkaterület függ.  Tekintse meg a Security Center [árképzést ismertető oldalra](../security-center/security-center-pricing.md) további információt.


> [!NOTE]
> A Security Center biztonsági adatokat ingyenes szint alatt egy munkaterület nem menti. Naplók számos küldhetnek egy munkaterület az ingyenes szint és az adatok keresése, de a találatok között nem szerepelnek a Security Center adatait. A Security Center csak menti az adatok egy munkaterület Standard csomagra.
>
>

## <a name="access-search"></a>Hozzáférés-keresés
1. Az a Security Center fő menüben válassza a **keresési**.

  ![Válassza ki a napló keresése][1]

2. A Security Center az Azure-előfizetések az összes munkaterületek sorolja fel. Jelöljön ki egy munkaterületet. (Ha csak egy munkaterületen, a munkaterület választó nem jelenik meg.)

  ![A munkaterület kiválasztása][2]

3. **Naplófájl-keresési** nyílik meg. A kijelölt munkaterületen további adatok lekérdezéséhez írja be a példalekérdezés:

  SecurityEvent |} Ha EventID == 4625 |} által TargetAccount count() összefoglalója

  Eredmény látható összes fiókot, amely nem sikerült a bejelentkezés (4625 esemény).

  ![Keresési eredmények][3]

Lásd: [Log Analytics lekérdezési nyelv](../log-analytics/log-analytics-search-reference.md) a kijelölt munkaterületen adatok lekérdezése módjáról további információt.

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, hogyan férhet hozzá a Keresés a Security Center. A Security Center Naplóelemzési keresési használja. A Naplóelemzési keresési kapcsolatos további információkért lásd:

- [Mi az a Log Analytics?](../log-analytics/log-analytics-overview.md) – A Naplóelemzési áttekintése
- [Understanding napló keres a Naplóelemzési](../log-analytics/log-analytics-log-search-new.md) - ismerteti napló keresés használata a Naplóelemzési és a naplófájl-keresési létrehozása előtt kell értelmezni fogalmat
- [Napló keresések használatát Naplóelemzési adatok kereséséhez](../log-analytics/log-analytics-log-searches.md) – útmutató a naplófájl-keresési
- [A Naplóelemzési hivatkozás keresése](../log-analytics/log-analytics-search-reference.md) – ismerteti a napló Analytics lekérdezési nyelv

Security Centerrel kapcsolatos további tudnivalókért lásd:

- [A Security Center áttekintése](security-center-intro.md) – Describes Security Center főbb képességek

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
