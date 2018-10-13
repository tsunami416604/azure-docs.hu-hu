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
ms.component: report-monitor
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 1e6ad35702b15090202278cfdead62b245040302
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309620"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Az Azure Active Directory reporting API használatának első lépései

Az Azure Active Directory segítségével számos [jelentések](overview-reports.md). Ezen jelentések adatai nagyon hasznosak lehetnek az alkalmazások számára, például a SIEM rendszerekhez, a naplózáshoz és az üzleti intelligencia eszközökhöz. 

Az Azure AD reporting API-t használ, a REST-alapú API-kon keresztül az adatok programozott hozzáférést kaphatnak. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat.

Ez a cikk biztosít egy ütemtervet a kapcsolódó API-val a jelentési adatok eléréséhez.

Ha problémákat tapasztal, tekintse meg [hogyan kérhet támogatást az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).

Ez a cikk az Azure AD Graph API-ra vonatkozik.  Hasonló a Microsoft Graph API-val kapcsolatos információk: [directoryAudit erőforrástípus](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/directoryaudit).

## <a name="prerequisites"></a>Előfeltételek

A jelentéskészítő API eléréséhez, akkor is, ha azt tervezi, a parancsfájl használatával az API elérése, kell tennie:

1. (Biztonsági olvasó, biztonsági rendszergazdai, globális rendszergazdaként) szerepkörök hozzárendelése
2. Egy alkalmazás regisztrálása
3. Engedélyek megadása
4. Konfigurációs beállítások összegyűjtése

Részletes útmutatásért lásd: a [az Azure Active Directory reporting API elérésének előfeltételeit](howto-configure-prerequisites-for-reporting-api.md).

## <a name="apis-with-graph-explorer"></a>A Graph Explorer API-k

Használhatja a [MSGraph explorer](https://developer.microsoft.com/graph/graph-explorer) ellenőrizze a bejelentkezést és naplózási adatok API-t. Ügyeljen arra, hogy jelentkezzen be a fiókjába mind a bejelentkezési gomb a Graph Explorer felhasználói felületén, és állítsa be **AuditLog.Read.All** és **Directory.Read.All** látható módon a bérlőhöz tartozó engedélyeket.   

![Graph Explorer](./media/concept-reporting-api/graph-explorer.png)

![Felhasználói felület engedélyek módosítása](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Tanúsítványok használata az Azure AD reporting API eléréséhez 

Az Azure AD Reporting API használata tanúsítványok, ha azt tervezi, hogy a jelentési adatok felhasználói beavatkozás nélkül.

Részletes útmutatásért lásd: [adatok lekérése az Azure AD Reporting API és tanúsítványok használatával](tutorial-access-api-with-certificates.md).


## <a name="next-steps"></a>További lépések

 * [Naplózási referencia API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
 * [Bejelentkezési tevékenységek jelentésének API-referencia](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
 * [Az Azure AD reporting API hibáinak elhárítása](troubleshoot-graph-api.md)


