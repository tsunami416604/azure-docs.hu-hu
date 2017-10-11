---
title: "Hibaelhárítás: Hiányzó adatok a letöltött Azure Active Directory-tevékenységnaplókban | Microsoft Docs"
description: "A letöltött Azure Active Directory-tevékenységnaplókból hiányzó adatok problémájára nyújt megoldást."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 3d56f89035da4d1a0074256b165663f81fc2b01e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-have-downloaded"></a>Nem találhatók adatok az Azure Active Directory letöltött tevékenységnaplóiban


## <a name="symptoms"></a>Probléma

Letöltöttem a tevékenységnaplókat (audit vagy bejelentkezési), és nem látom a kiválasztott időre vonatkozó összes rekordot. Hogy miért? 

 ![Jelentéskészítés](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>Ok

Amikor tevékenységnaplókat tölt le az Azure Portalon, 120 ezer rekordra korlátozzuk a letöltött tartományt, és a legfrissebb elemek kerülnek előre. 

## <a name="resolution"></a>Megoldás:

Az [Azure AD Reporting API-kkal](active-directory-reporting-api-getting-started.md) akár egymillió rekordot is lekérdezhet. Azt ajánljuk, hogy futtasson egy ütemezett szkriptet, amely meghívja a jelentéskészítő API-kat, hogy növekményes módon kérdezzék le az egy adott időszakra (például egy napra vagy hétre) vonatkozó rekordokat.

## <a name="next-steps"></a>Következő lépések
További információ: [Jelentéskészítés az Azure Active Directoryban – gyakori kérdések](active-directory-reporting-faq.md).

