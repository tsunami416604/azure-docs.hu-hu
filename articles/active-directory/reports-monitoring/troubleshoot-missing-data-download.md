---
title: Hibaelhárítás Hiányzó adatértékek a letöltött Azure Active Directory tevékenység naplójában | Microsoft Docs
description: A letöltött Azure Active Directory-tevékenységnaplókból hiányzó adatok problémájára nyújt megoldást.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f120c1b86efe94f4ff6316e6116b9049582b07e9
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68987986"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Nem találom az összes olyan adatnaplót, amelyet Letöltöttem a letöltött Azure Active Directory

## <a name="symptoms"></a>Probléma

Letöltöttem a tevékenységnaplókat (audit vagy bejelentkezési), és nem látom a kiválasztott időre vonatkozó összes rekordot. Miért? 

 ![Jelentéskészítés](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Ok

Amikor letölti a tevékenység naplóit a Azure Portalban, a skálázást 250 000 rekordra korlátozzuk, a legutóbbiek szerint rendezve. 

## <a name="resolution"></a>Megoldás:

Az [Azure AD Reporting API-kkal](concept-reporting-api.md) akár egymillió rekordot is lekérdezhet.

## <a name="next-steps"></a>További lépések

* [Azure Active Directory jelentések – gyakori kérdések](reports-faq.md)

