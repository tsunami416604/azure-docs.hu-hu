---
title: "Hibaelhárítás: Hiányzó adatok a letöltött Azure Active Directory-tevékenységnaplókban – előzetes verzió | Microsoft Docs"
description: "A letöltött Azure Active Directory-tevékenységnaplók előzetes verziójából hiányzó adatokra nyújt megoldást."
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
ms.date: 03/09/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: e0d65edcb7c14114565402038b0958c3a2ffb477
ms.lasthandoff: 03/09/2017


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


