---
title: Az Azure Active Directory jelentéskészítés késése |} A Microsoft Docs
description: Ismerje meg, mennyi ideig tart a jelentési események megjelennek az Azure portal tudnivalók
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66fc890e72c32cbe605d49f458a875cb692e6182
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56174333"
---
# <a name="azure-active-directory-reporting-latencies"></a>Az Azure Active Directory jelentéskészítés késése

Késései mennyi ideig tart az Azure Active Directory (Azure AD) jelentéskészítési adatok megjelennek a [az Azure portal](https://portal.azure.com). Ez a cikk a várható késés a különböző típusú jelentéseket sorolja fel. 

## <a name="activity-reports"></a>Tevékenységjelentések

Tevékenységjelentések két típusa van:

- [Bejelentkezések](concept-sign-ins.md) – arról nyújt tájékoztatást, a használati felügyelt alkalmazások és a felhasználó bejelentkezési tevékenységek
- [Auditnaplók](concept-audit-logs.md) – rendszertevékenység információk a felhasználók és csoportok, a felügyelt alkalmazások és a címtártevékenységekre vonatkozóan nyújt

A következő táblázat felsorolja a késési adatok tevékenységre vonatkozó jelentések. 

> [!NOTE]
> **Késés (95. százalékérték)** hivatkozik az idő, mely szerint a 95 %-át a naplókat kerülnek, és **késés (99. percentilis)** hivatkozik az idő, amely szerint a naplók 99 %-át lesz jelentve. 
>

| Jelentés | Késés (95. százalékérték) |Késés (99. percentilis)|Naplók kerülnek időtartomány|
| :-- | --- | --- | --- |
| Naplók | 2 perc  | 5 perc  | 2 – 60 perc |
| Bejelentkezések | 2 perc  | 5 perc | 2 – 120 perc |

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Hogy mikor is tevékenységek adatainak megtekintéséhez prémium licencre első után?

Ha már rendelkezik az ingyenes licenc tevékenységek adatokat, majd tekintheti meg azonnal a frissítés. Ha nem rendelkezik semmilyen adatot, majd vesz igénybe egy vagy két napot megjelenjen a jelentésekben, prémium licencre történő frissítés után az adatok.

## <a name="security-reports"></a>Biztonsági jelentések

Biztonsági jelentések két típusa van:

- [Kockázatos bejelentkezések](concept-risky-sign-ins.md) – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. 
- [Kockázatosként megjelölt felhasználók](concept-user-at-risk.md) – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. 

A következő táblázat felsorolja a biztonsági jelentések késési adatok.

| Jelentés | Minimális | Átlag | Maximum |
| :-- | --- | --- | --- |
| Veszélyeztetett felhasználók          | 5 perc   | 15 perc  | 2 óra  |
| Kockázatos bejelentkezések         | 5 perc   | 15 perc  | 2 óra  |

## <a name="risk-events"></a>Kockázati események

Azure ad-ben az adaptív gépi tanulási algoritmusok és heurisztika segítségével észleli a felhasználói fiókokhoz kapcsolódó gyanús tevékenységeket. Minden észlelt gyanús művelet nevű rekordot tárolja egy **kockázati esemény**.

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

* [Az Azure AD-jelentések áttekintése](overview-reports.md)
* [Az Azure AD-jelentések programozás alapú hozzáférést](concept-reporting-api.md)
* [Az Azure Active Directory kockázati eseményei](concept-risk-events.md)
