---
title: 'Hibaelhárítás: Hiányzó adatok a letöltött Azure Active Directory-Tevékenységnaplókban |} A Microsoft Docs'
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
ms.openlocfilehash: 2200a9c75b371ed72ffefe6900367e698101e0fe
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58434840"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Nem találhatók adatok az Azure Active Directory-tevékenységnaplókban letöltöttem

## <a name="symptoms"></a>Probléma

Letöltöttem a tevékenységnaplókat (audit vagy bejelentkezési), és nem látom a kiválasztott időre vonatkozó összes rekordot. Hogy miért? 

 ![Jelentéskészítés](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Ok

Az Azure Portalon tevékenységnaplókat tölt le, amikor azt korlátozza a méretezési csoport 250 000 rekordot legutóbbi van legelöl szerint rendezve. 

## <a name="resolution"></a>Megoldás:

Az [Azure AD Reporting API-kkal](concept-reporting-api.md) akár egymillió rekordot is lekérdezhet.

## <a name="next-steps"></a>További lépések

* [Az Azure Active Directory-jelentések – gyakori kérdések](reports-faq.md)

