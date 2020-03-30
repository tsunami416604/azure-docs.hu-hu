---
title: Használati és elemzési jelentés | Microsoft dokumentumok
description: Bevezetés a használati és elemzési jelentésbe az Azure Active Directory portálon
services: active-directory
documentationcenter: ''
author: MarkusVi
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
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b3db86137207ae726c7befc393f62590fd1456d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008268"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Használati és elemzési jelentés az Azure Active Directory portálon

A használati és elemzési jelentéssel alkalmazásközpontú képet kaphat a bejelentkezési adatokról. A következő kérdésekre találhat választ:

*   Melyek a szervezet legnépszerűbb használt alkalmazásai?
*   Milyen alkalmazások rendelkeznek a legtöbb sikertelen bejelentkezési? 
*   Melyek az egyes alkalmazások legfontosabb bejelentkezési hibái?

## <a name="prerequisites"></a>Előfeltételek 

A használati és elemzési jelentésből származó adatok eléréséhez a következőkre van szükség:

* Egy Azure AD-bérlő
* Azure AD prémium (P1/P2) licenc a bejelentkezési adatok megtekintéséhez
* A globális rendszergazda, a biztonsági rendszergazda, a biztonsági olvasó vagy a jelentésolvasó szerepköreiben lévő felhasználó. Emellett bármely felhasználó (nem rendszergazda) hozzáférhet a saját bejelentkezések. 

## <a name="access-the-usage-and-insights-report"></a>A használati és elemzési jelentés elérése

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
2. Válassza ki a megfelelő könyvtárat, majd válassza az **Azure Active Directory** t, majd a **Nagyvállalati alkalmazások**lehetőséget.
3. A **Tevékenység** csoportban válassza a **Használati & elemzési adatok** lehetőséget a jelentés megnyitásához. 

![Használati és elemzési jelentés](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>A jelentés használata

A használati és elemzési jelentés egy vagy több bejelentkezési kísérlettel jeleníti meg az alkalmazások listáját, és lehetővé teszi a sikeres bejelentkezések, a sikertelen bejelentkezések és a sikerességi arány alapján való rendezést.

A lista alján található további betöltési menüre kattintva további alkalmazásokat tekinthet meg az oldalon. A dátumtartomány kiválasztásával megtekintheti a tartományon belül használt összes alkalmazást.

Beállíthatja azt is, hogy a fókusz egy adott alkalmazásra összpontosítson. Válassza ki **a bejelentkezési tevékenység megtekintése** az alkalmazás bejelentkezési tevékenységének idővel történő megtekintéséhez, valamint a legfontosabb hibák megtekintéséhez.  

Amikor kiválaszt egy napot az alkalmazás használati grafikonjában, részletes listát kap az alkalmazás bejelentkezési tevékenységeiről.  

![Használati és elemzési jelentés](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>További lépések

* [Bejelentkezési jelentés](concept-sign-ins.md)