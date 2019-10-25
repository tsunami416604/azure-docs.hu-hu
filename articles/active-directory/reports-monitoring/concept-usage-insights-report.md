---
title: Használati és adatellenőrzési jelentés | Microsoft Docs
description: A használati és betekintési jelentés bemutatása a Azure Active Directory portálon
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.openlocfilehash: 568eaa358f6788af525a69cd8ecda76a0591879f
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820940"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Használati és bepillantást a Azure Active Directory portálon

A használati és elemzési jelentéssel a bejelentkezési adatok alkalmazás-központú nézetét érheti el. Az alábbi kérdésekre kaphat választ:

*   Melyek a szervezeten belül leggyakrabban használt alkalmazások?
*   Milyen alkalmazások rendelkeznek a legtöbb sikertelen bejelentkezéssel? 
*   Melyek az egyes alkalmazások leggyakoribb bejelentkezési hibái?

## <a name="prerequisites"></a>Előfeltételek 

A használati és elemzési jelentésből származó adatok eléréséhez a következőkre lesz szüksége:

* Azure AD-bérlő
* Egy prémium szintű Azure AD-(P1/P2-) licenc a bejelentkezési adatszolgáltatások megtekintéséhez
* Egy felhasználó a globális rendszergazda, a biztonsági rendszergazda, a biztonsági olvasó vagy a jelentési olvasó szerepkörben. Emellett a felhasználók (nem rendszergazdák) is hozzáférhetnek a saját bejelentkezésekhez. 

## <a name="access-the-usage-and-insights-report"></a>Hozzáférés a használati és a bepillantást a jelentéshez

1. Lépjen az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a megfelelő könyvtárat, majd válassza a **Azure Active Directory** lehetőséget, és válassza a **vállalati alkalmazások**lehetőséget.
3. A **tevékenység** szakaszban válassza a **használati & a bepillantást** a jelentés megnyitásához. 

![Használati és elemzési jelentés](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>A jelentés használata

A használati és keresési jelentés az alkalmazások listáját jeleníti meg, amelyek egy vagy több bejelentkezési kísérlettel rendelkeznek, és lehetővé teszi a sikeres bejelentkezések, a sikertelen bejelentkezések és a sikerességi arány szerinti rendezést.

Ha a lista alján a további Betöltés gombra kattint, a lapon további alkalmazások is megtekinthetők. A dátumtartomány kiválasztásával megtekintheti a tartományon belül használt összes alkalmazást.

Beállíthatja a fókuszt egy adott alkalmazásra is. Válassza a **bejelentkezési tevékenység megtekintése** lehetőséget, hogy a bejelentkezési tevékenységet az alkalmazáshoz, valamint a leggyakoribb hibákhoz az idő múlásával láthassa.  

Amikor kiválaszt egy napot az alkalmazás használati gráfjában, részletes listát kap az alkalmazás bejelentkezési tevékenységeiről.  

![Használati és elemzési jelentés](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>Következő lépések

* [Bejelentkezések jelentés](concept-sign-ins.md)