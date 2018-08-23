---
title: Az Azure Active Directory jelentéskészítés késése |} A Microsoft Docs
description: Ismerje meg, mennyi ideig tart a jelentési események megjelennek az Azure portal tudnivalók
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 12/15/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: f0de2f8700bef83b5a8a9303e90c97aab29722a3
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "42060592"
---
# <a name="azure-active-directory-reporting-latencies"></a>Az Azure Active Directory jelentéskészítés késése

A [reporting](../active-directory-preview-explainer.md) az Azure Active Directoryban, meg kell határoznia, hogy a környezet működésébe összes információ lekérése. Mennyi ideig tart a jelentés adatainak megjelenjen az Azure Portalon a késés is nevezik. 

Ez a témakör a késési adatok az Azure Portalon az összes jelentési kategóriába sorolja fel. 


## <a name="activity-reports"></a>Tevékenységjelentések

Nincsenek Tevékenységjelentés két terület:

- **Bejelentkezési tevékenységek** – A felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információk
- **Naplók** – Rendszertevékenység információk a felhasználó- és csoportfelügyeletre, valamint a felügyelt alkalmazásokra és a címtártevékenységekre vonatkozóan

A következő táblázat felsorolja a késési adatok tevékenységre vonatkozó jelentések.

| Jelentés | Késés (P95) |Késés (P99)|
| :-- | --- | --- | 
| Naplók | 2 perc  | 5 perc  |
| Bejelentkezések | 2 perc  | 5 perc |







## <a name="security-reports"></a>Biztonsági jelentések

Nincsenek biztonsági jelentések készítése két terület:

- **Kockázatos bejelentkezések** – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. 
- **Kockázatosként megjelölt felhasználók** – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. 

A következő táblázat felsorolja a biztonsági jelentések késési adatok.

| Jelentés | Minimális | Átlag | Maximum |
| :-- | --- | --- | --- |
| Érintett felhasználók          | 5 perc   | 15 perc  | 2 óra  |
| Kockázatos bejelentkezések         | 5 perc   | 15 perc  | 2 óra  |

## <a name="risk-events"></a>Kockázati események

Az Azure Active Directory adaptív gépi tanulási algoritmusok és heurisztika segítségével észleli a felhasználói fiókokhoz kapcsolódó gyanús tevékenységeket. Minden észlelt gyanús művelet egy rekord nevű kockázati esemény van tárolva.

A következő táblázat felsorolja a kockázati események késési adatok.

| Jelentés | Minimális | Átlag | Maximum |
| :-- | --- | --- | --- |
| Bejelentkezések névtelen IP-címről |5 perc |15 perc |2 óra |
| Bejelentkezések ismeretlen helyekről |5 perc |15 perc |2 óra |
| Felhasználók, akiknek kiszivárogtak a hitelesítő adatai |2 óra |4 óra |8 óra |
| Bejelentkezés szokatlan helyekről |5 perc |1 óra |8 óra  |
| Bejelentkezések fertőzött eszközökről |2 óra |4 óra |8 óra  |
| Bejelentkezések gyanús tevékenységeket mutató IP-címekkel |2 óra |4 óra |8 óra  |



## <a name="next-steps"></a>További lépések

Ha szeretne többet megtudni a tevékenységre vonatkozó jelentések az Azure Portalon, tekintse meg:

- [Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon](concept-sign-ins.md)
- [Naplózási tevékenységre vonatkozó jelentések az Azure Active Directory portálon](concept-audit-logs.md)

Ha szeretne többet megtudni az Azure Portalon a biztonsági jelentések, lásd:

- [Felhasználókról szóló biztonsági jelentés az Azure Active Directory portálon](concept-user-at-risk.md)
- [Kockázatos bejelentkezések jelentés az Azure Active Directory portálon](concept-risky-sign-ins.md)

Ha szeretne többet megtudni a kockázati események, [Azure Active Directory kockázati események](concept-risk-events.md).
