---
title: Az Azure AD reporting API használatának első lépései |} A Microsoft Docs
description: Útmutató az Azure Active Directory reporting API használatába
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ff3e530dae3a6db4b7c84292a25e83c11000baf
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437764"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Az Azure Active Directory reporting API használatának első lépései

Az Azure Active Directory segítségével számos [jelentések](overview-reports.md), hasznos információt alkalmazásokhoz, mint a SIEM rendszerekhez, naplózási és üzletiintelligencia-eszközök esetén. 

A Microsoft Graph API használatával az Azure AD-jelentések, a REST-alapú API-kon keresztül az adatok programozott hozzáférést kaphatnak. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat.

Ez a cikk a jelentéskészítési API-– többek közt az eléréséhez áttekintést nyújt.

Ha problémákat tapasztal, tekintse meg [hogyan kérhet támogatást az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="prerequisites"></a>Előfeltételek

A jelentéskészítő API- vagy felhasználói beavatkozás nélkül, el kell tennie:

1. (Biztonsági olvasó, biztonsági rendszergazdai, globális rendszergazdaként) szerepkörök hozzárendelése
2. Egy alkalmazás regisztrálása
3. Engedélyek megadása
4. Konfigurációs beállítások összegyűjtése

Részletes útmutatásért lásd: a [az Azure Active Directory reporting API elérésének előfeltételeit](howto-configure-prerequisites-for-reporting-api.md). 

## <a name="api-endpoints"></a>API-végpontok 

A Microsoft Graph API-végpont az auditnaplókhoz `https://graph.microsoft.com/beta/auditLogs/directoryAudits` és a Microsoft Graph API-végpont a bejelentkezések `https://graph.microsoft.com/beta/auditLogs/signIns`. További információkért lásd: a [naplózási API-referencia](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) és [be API-referencia](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signIn).

Emellett használhatja a [Identity Protection kockázati események API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) programozott hozzáférést a Microsoft Graph használatával biztonsági észlelések eléréséhez. További információkért lásd: [Ismerkedés az Azure Active Directory Identity Protection és Microsoft Graph](../identity-protection/graph-get-started.md). 

> [!NOTE]
>  A **https:\/\/graph.windows.net\/\<bérlőnevet\>\/jelentések\/**  végpont elavult. Az új API-végpontokat, programozott módon érheti el a tevékenység és a biztonsági jelentések a fent leírt használja.
  
## <a name="apis-with-graph-explorer"></a>A Graph Explorer API-k

Használhatja a [MSGraph explorer](https://developer.microsoft.com/graph/graph-explorer) ellenőrizze a bejelentkezést és naplózási adatok API-t. Ügyeljen arra, hogy jelentkezzen be a fiókjába mind a bejelentkezési gomb a Graph Explorer felhasználói felületén, és állítsa be **AuditLog.Read.All** és **Directory.Read.All** látható módon a bérlőhöz tartozó engedélyeket.   

![Graph Explorer](./media/concept-reporting-api/graph-explorer.png)

![Felhasználói felület engedélyek módosítása](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Tanúsítványok használata az Azure AD reporting API eléréséhez 

Az Azure AD Reporting API használata tanúsítványok, ha azt tervezi, hogy a jelentési adatok felhasználói beavatkozás nélkül.

Részletes útmutatásért lásd: [adatok lekérése az Azure AD Reporting API és tanúsítványok használatával](tutorial-access-api-with-certificates.md).

## <a name="next-steps"></a>További lépések

 * [A reporting API elérésének előfeltételeit](howto-configure-prerequisites-for-reporting-api.md) 
 * [Adatok lekérése az Azure AD Reporting API és tanúsítványok használatával](tutorial-access-api-with-certificates.md)
 * [Az Azure AD reporting API hibáinak elhárítása](troubleshoot-graph-api.md)


