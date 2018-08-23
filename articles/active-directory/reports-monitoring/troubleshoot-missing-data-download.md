---
title: 'Hibaelhárítás: Hiányzó adatok a letöltött Azure Active Directory-tevékenységnaplókban | Microsoft Docs'
description: A letöltött Azure Active Directory-tevékenységnaplókból hiányzó adatok problémájára nyújt megoldást.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 9138da42eeb87e45b86be10aff67792ee6de09b4
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42058715"
---
# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Nem találhatók adatok az Azure Active Directory-tevékenységnaplókban letöltöttem


## <a name="symptoms"></a>Probléma

Letöltöttem a tevékenységnaplókat (audit vagy bejelentkezési), és nem látom a kiválasztott időre vonatkozó összes rekordot. Hogy miért? 

 ![Jelentéskészítés](./media/troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>Ok

Az Azure Portalon tevékenységnaplókat tölt le, amikor azt korlátozza a méretezési csoport 5000 rekordot legutóbbi van legelöl szerint rendezve. 

## <a name="resolution"></a>Megoldás:

Az [Azure AD Reporting API-kkal](concept-reporting-api.md) akár egymillió rekordot is lekérdezhet. Az ajánlott módszer, hogy a parancsfájl futtatása ütemezés szerint, amely meghívja a jelentéskészítő API-kat növekményes módon kérdezzék bejegyzések beolvasása egy időszakon belül (például naponta vagy hetente).

## <a name="next-steps"></a>További lépések
További információ: [Jelentéskészítés az Azure Active Directoryban – gyakori kérdések](reports-faq.md).

