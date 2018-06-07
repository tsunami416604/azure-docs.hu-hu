---
title: Bevezetés az Azure AD reporting API használatába |} Microsoft Docs
description: Ismerkedés az Azure Active Directory reporting API-val
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: dhanyahk;rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: df0b672a07575a0d26fff89c90008043d02818dd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589100"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Bevezetés az Azure Active Directory reporting API használatába

Az Azure Active Directory biztosít számos különböző [jelentések](active-directory-reporting-azure-portal.md). Ezen jelentések adatai nagyon hasznosak lehetnek az alkalmazások számára, például a SIEM rendszerekhez, a naplózáshoz és az üzleti intelligencia eszközökhöz. 

Az Azure AD reporting API használatával kaphatnak a programozott hozzáférést a REST-alapú API-készlet adatokat. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat.

Ez a cikk nyújt a terv a jelentési adatokat, a kapcsolódó API-jával eléréséhez.

Ha problémákat tapasztal, tekintse meg [hogyan kérhet támogatást az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).


## <a name="prerequisites"></a>Előfeltételek

A reporting API-t, még akkor is, ha azt tervezi, az API-t egy parancsfájl segítségével fér hozzá a eléréséhez kell:

1. (Biztonsági olvasó, biztonsági rendszergazda globális felügyeleti) szerepkörök hozzárendelése
2. Egy alkalmazás regisztrálása
3. Engedélyek megadása
4. Konfigurációs beállítások összegyűjtése


 
Részletes útmutatásért lásd: a [Előfeltételek az Azure Active Directory reporting API eléréséhez](active-directory-reporting-api-prerequisites-azure-portal.md).


## <a name="recommendation"></a>Ajánlás 

Ha azt tervezi, felhasználói beavatkozás nélkül jelentési adatok beolvasása a, érdemes az Azure AD jelentéskészítési API és tanúsítványok segítségével.

Részletes útmutatásért lásd: [adatok és tanúsítványok segítségével történik az Azure AD jelentéskészítési API](active-directory-reporting-api-with-certificates.md).


## <a name="explore"></a>Tallózás

A reporting API-k első benyomást beolvasása:
   
   - [A mintákat a ellenőrzési API használatával](active-directory-reporting-api-audit-samples.md) 
 
   - [A mintákat a bejelentkezési tevékenység jelentés API használatával](active-directory-reporting-api-sign-in-activity-samples.md)


## <a name="customize"></a>Testreszabás  

A saját megoldás létrehozása: 
   
   - [A naplózási API-hivatkozás használata](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 

   - [A bejelentkezési tevékenység jelentéshivatkozás API használatával](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



