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
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 9109c698e4e8b43eeb7534c338adc99476012a3f
ms.contentlocale: hu-hu
ms.lasthandoff: 05/08/2017

---

# Nem találhatók adatok az Azure Active Directory letöltött tevékenységnaplóiban
<a id="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-have-downloaded" class="xliff"></a>


## Probléma
<a id="symptoms" class="xliff"></a>

Letöltöttem a tevékenységnaplókat (audit vagy bejelentkezési), és nem látom a kiválasztott időre vonatkozó összes rekordot. Hogy miért? 

 ![Jelentéskészítés](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## Ok
<a id="cause" class="xliff"></a>

Amikor tevékenységnaplókat tölt le az Azure Portalon, 120 ezer rekordra korlátozzuk a letöltött tartományt, és a legfrissebb elemek kerülnek előre. 

## Megoldás:
<a id="resolution" class="xliff"></a>

Az [Azure AD Reporting API-kkal](active-directory-reporting-api-getting-started.md) akár egymillió rekordot is lekérdezhet. Azt ajánljuk, hogy futtasson egy ütemezett szkriptet, amely meghívja a jelentéskészítő API-kat, hogy növekményes módon kérdezzék le az egy adott időszakra (például egy napra vagy hétre) vonatkozó rekordokat.

## Következő lépések
<a id="next-steps" class="xliff"></a>
További információ: [Jelentéskészítés az Azure Active Directoryban – gyakori kérdések](active-directory-reporting-faq.md).


