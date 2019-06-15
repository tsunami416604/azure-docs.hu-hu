---
title: Használat és a insights jelentés az Azure Active Directory portálon |} A Microsoft Docs
description: Használat és insights jelentés az Azure Active Directory portálon – bevezetés
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
ms.openlocfilehash: 3fe1e72d277bffd4bc9b38ac377e33b341967e17
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65806356"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Használat és insights jelentés az Azure Active Directory portálon

A használati és insights jelentéssel kérheti le az alkalmazás-központú nézetben a bejelentkezési adatok. Annak az alábbi kérdésekre kaphat választ:

*   Mik azok a felső használt alkalmazásokat a szervezet?
*   Milyen alkalmazások vannak a legtöbb sikertelen bejelentkezést? 
*   Mik a legfontosabb bejelentkezési hibák minden alkalmazáshoz?

## <a name="prerequisites"></a>Előfeltételek 

A használati és insights jelentésből elérni az adatokat az alábbiak szükségesek:

* Az Azure AD-bérlő
* Az Azure ad-ben (P1 vagy P2) elem megtekintéséhez prémium licencre a bejelentkezési adatok
* A globális rendszergazdai, biztonsági rendszergazdai, biztonsági olvasó vagy a jelentés olvasói szerepkörök a felhasználó. Emellett bármely (nem rendszergazda jogosultságú) felhasználó hozzáférhet a saját bejelentkezések. 

## <a name="access-the-usage-and-insights-report"></a>A használati és insights jelentések eléréséhez

1. Lépjen az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a megfelelő címtárba, majd válassza ki **Azure Active Directory** válassza **vállalati alkalmazások**.
3. Az a **tevékenység** szakaszban jelölje be **használat & insights** nyissa meg a jelentést. 

![Használati és elemzési jelentés](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>A jelentés

A használati és insights jelentés kísérlet az egy vagy több bejelentkezési kérelmek listáját jeleníti meg, és lehetővé teszi, hogy a sikeres bejelentkezésekkor sikertelen bejelentkezést és a sikerességi arányról száma szerint rendezheti.

A Betöltés gombra kattint a lista alján több lehetővé teszi további alkalmazások megjelenítése a lapon. Kiválaszthatja a dátumtartomány tartományon belül használt összes alkalmazás megtekintése.

Is beállíthat egy adott alkalmazást a fókuszt. Válassza ki **megtekintheti a bejelentkezési tevékenységeket** , tekintse meg a bejelentkezési tevékenység az alkalmazás, valamint a legfontosabb hibák idővel.  

Amikor kiválaszt egy nap az alkalmazás használati diagramot, a bejelentkezési tevékenységek részletes listát kap az alkalmazás.  

![Használati és elemzési jelentés](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>További lépések

* [Bejelentkezések jelentés](concept-sign-ins.md)