---
title: "Bevezetés az Azure AD reporting API használatába |} Microsoft Docs"
description: "Ismerkedés az Azure Active Directory reporting API-val"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: c94a52b8a34100f22b627e291cb0becd3501fd55
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="getting-started-with-the-azure-active-directory-reporting-api"></a>Bevezetés az Azure Active Directory reporting API használatába

Az Azure Active Directory tartalmazza a különféle jelentések. Ezen jelentések adatai nagyon hasznosak lehetnek az alkalmazások számára, például a SIEM rendszerekhez, a naplózáshoz és az üzleti intelligencia eszközökhöz. Az Azure AD-jelentéskészítés API-k REST-alapú API-kon keresztül biztosítják az adatok szoftveres hozzáférését. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat.

Ez a cikk azt ismerteti a kell Ismerkedés az Azure AD reporting API-k.
A következő szakaszban hogy a naplózás használatával kapcsolatos további részletekért és jelentkezzen be API-k. 

Gyakran ismételt kérdések, olvassa el a [gyakran ismételt kérdések](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-faq). A problémák, kérjük, [fájlt egy támogatási jegy](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-troubleshooting-support-howto)

## <a name="learning-map"></a>Tanulási térkép
1. **Készítse elő** -előtt tesztelheti az API-mintákat, végre kell hajtania a [Előfeltételek az Azure AD reporting API eléréséhez](active-directory-reporting-api-prerequisites-azure-portal.md).
2. **Fedezze fel** -jelentéskészítési API-k első benyomást beolvasása:
   
   * [A mintákat a ellenőrzési API használatával](active-directory-reporting-api-audit-samples.md) 
   * [A mintákat a bejelentkezési tevékenység jelentés API használatával](active-directory-reporting-api-sign-in-activity-samples.md)
3. **Testre szabhatja** -saját megoldás létrehozása: 
   
   * [A naplózási API-hivatkozás használata](active-directory-reporting-api-audit-reference.md) 
   * [A bejelentkezési tevékenység jelentéshivatkozás API használatával](active-directory-reporting-api-sign-in-activity-reference.md)

## <a name="next-steps"></a>Következő lépések
Ha fejezetét megtekintéséhez az összes elérhető Azure AD Graph API-végpontok, akkor erre a hivatkozásra: [https://graph.windows.net/tenant-name/activities/$ metaadatok? api-version = beta](https://graph.windows.net/tenant-name/activities/$metadata?api-version=beta).

