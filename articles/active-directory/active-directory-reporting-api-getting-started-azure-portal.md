---
title: Az Azure AD reporting API használatának első lépései |} A Microsoft Docs
description: Útmutató az Azure Active Directory reporting API használatába
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ce8630f5718a849f460b372d9e15b03902ac2117
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159679"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Az Azure Active Directory reporting API használatának első lépései

Az Azure Active Directory segítségével számos [jelentések](active-directory-reporting-azure-portal.md). Ezen jelentések adatai nagyon hasznosak lehetnek az alkalmazások számára, például a SIEM rendszerekhez, a naplózáshoz és az üzleti intelligencia eszközökhöz. 

Az Azure AD reporting API-t használ, a REST-alapú API-kon keresztül az adatok programozott hozzáférést kaphatnak. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat.

Ez a cikk biztosít egy ütemtervet a kapcsolódó API-val a jelentési adatok eléréséhez.

Ha problémákat tapasztal, tekintse meg [hogyan kérhet támogatást az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).


## <a name="prerequisites"></a>Előfeltételek

A jelentéskészítő API eléréséhez, akkor is, ha azt tervezi, a parancsfájl használatával az API elérése, kell tennie:

1. (Biztonsági olvasó, biztonsági rendszergazdai, globális rendszergazdaként) szerepkörök hozzárendelése
2. Egy alkalmazás regisztrálása
3. Engedélyek megadása
4. Konfigurációs beállítások összegyűjtése


 
Részletes útmutatásért lásd: a [az Azure Active Directory reporting API elérésének előfeltételeit](active-directory-reporting-api-prerequisites-azure-portal.md).

## <a name="apis-with-graph-explorer"></a>A Graph Explorer API-k

Használhatja a [MSGraph explorer](https://developer.microsoft.com/en-us/graph/graph-explorer) ellenőrizze a bejelentkezést és naplózási adatok API-t. Ügyeljen arra, hogy jelentkezzen be a fiókjába mind a bejelentkezési gomb a Graph Explorer felhasználói felületén, és állítsa be **AuditLog.Read.All** és **Directory.Read.All** látható módon a bérlőhöz tartozó engedélyeket.   

![Graph Explorer](./media/active-directory-reporting-api-getting-started-azure-portal/graph-explorer.png)

![Felhasználói felület engedélyek módosítása](./media/active-directory-reporting-api-getting-started-azure-portal/modify-permissions.png)

## <a name="recommendation"></a>Ajánlás 

Ha azt tervezi, felhasználói beavatkozás nélkül jelentési adatok lekérésével, érdemes az Azure AD Reporting API és tanúsítványok használatával.

Részletes útmutatásért lásd: [adatok lekérése az Azure AD Reporting API és tanúsítványok használatával](active-directory-reporting-api-with-certificates.md).


## <a name="explore"></a>Tallózás

Kérje le a jelentéskészítési API-k egy első benyomást:
   
   - [Az ellenőrzési API-példák használata](active-directory-reporting-api-audit-samples.md) 
 
   - [A bejelentkezési tevékenységek jelentés API-példák használata](active-directory-reporting-api-sign-in-activity-samples.md)


## <a name="customize"></a>Testreszabás  

Hozza létre a saját megoldását: 
   
   - [Használatával a naplózási API-referencia](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 

   - [A bejelentkezési tevékenységek jelentéshivatkozás API használatával](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



