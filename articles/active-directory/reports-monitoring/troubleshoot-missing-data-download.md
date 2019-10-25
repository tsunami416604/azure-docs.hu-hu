---
title: 'Hibaelhárítás: hiányzó adatműveletek a letöltött tevékenységek naplóiban | Microsoft Docs'
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
ms.openlocfilehash: db96bd99a25ca7803dbdfe412e2cddbbcb57c0e4
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817525"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Nem találom az összes olyan adatnaplót, amelyet Letöltöttem a letöltött Azure Active Directory

## <a name="symptoms"></a>Probléma

Letöltöttem a tevékenységnaplókat (audit vagy bejelentkezési), és nem látom a kiválasztott időre vonatkozó összes rekordot. Miért? 

 ![Jelentések](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Ok

Amikor letölti a tevékenység naplóit a Azure Portalban, a skálázást 250 000 rekordra korlátozzuk, a legutóbbiek szerint rendezve. 

## <a name="resolution"></a>Felbontás

Az [Azure AD Reporting API-kkal](concept-reporting-api.md) akár egymillió rekordot is lekérdezhet.

## <a name="next-steps"></a>Következő lépések

* [Azure Active Directory jelentések – gyakori kérdések](reports-faq.md)

