---
title: 'Hibaelhárítás: hiányzó adatműveletek a letöltött tevékenységek naplóiban | Microsoft Docs'
description: A letöltött Azure Active Directory-tevékenységnaplókból hiányzó adatok problémájára nyújt megoldást.
services: active-directory
documentationcenter: ''
author: MarkusVi
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
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee07fdb6f8a4e69600297bdb16b6ad74793f10c9
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007719"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Nem találom az összes olyan adatnaplót, amelyet Letöltöttem a letöltött Azure Active Directory

## <a name="symptoms"></a>Probléma

Letöltöttem a tevékenységnaplókat (audit vagy bejelentkezési), és nem látom a kiválasztott időre vonatkozó összes rekordot. Hogy miért? 

 ![Jelentéskészítés](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Ok

Amikor letölti a tevékenység naplóit a Azure Portalban, a skálázást 250 000 rekordra korlátozzuk, a legutóbbiek szerint rendezve. 

## <a name="resolution"></a>Megoldás:

Az [Azure AD Reporting API-kkal](concept-reporting-api.md) akár egymillió rekordot is lekérdezhet.

## <a name="next-steps"></a>Következő lépések

* [Azure Active Directory jelentések – gyakori kérdések](reports-faq.md)

