---
title: Ismerkedés az Azure AD Reporting API-val | Microsoft Docs
description: Ismerkedés a Azure Active Directory jelentési API-val
services: active-directory
documentationcenter: ''
author: cawrites
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
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: c30669c8f5aeefc14db42a32d87f333419758327
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989865"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Ismerkedés a Azure Active Directory Reporting API-val

Azure Active Directory számos [jelentést](overview-reports.md)biztosít, amelyek hasznos információkat tartalmaznak az olyan alkalmazásokhoz, mint például a Siem-rendszerek, az audit és az üzleti intelligencia-eszközök. 

Az Azure AD-jelentések Microsoft Graph API-jának használatával programozott hozzáférést nyerhet az adatokhoz REST-alapú API-k segítségével. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat.

Ez a cikk áttekintést nyújt a jelentéskészítési API-ról, beleértve az elérésének módjait.

Ha problémákba ütközik, tekintse meg a [Azure Active Directory támogatásának](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)beszerzése című témakört.

## <a name="prerequisites"></a>Előfeltételek

Ha a jelentéskészítési API-t felhasználói beavatkozás nélkül vagy anélkül szeretné elérni, a következőket kell tennie:

1. Szerepkörök kiosztása (biztonsági olvasó, biztonsági rendszergazda, globális rendszergazda)
2. Alkalmazás regisztrálása
3. Engedélyek megadása
4. Konfigurációs beállítások összegyűjtése

Részletes útmutatást a [Azure Active Directory jelentési API elérésének](howto-configure-prerequisites-for-reporting-api.md)előfeltételei című témakörben talál. 

## <a name="api-endpoints"></a>API-végpontok 

A naplók `https://graph.microsoft.com/beta/auditLogs/directoryAudits` Microsoft Graph API-végpontja, valamint a bejelentkezések `https://graph.microsoft.com/beta/auditLogs/signIns`Microsoft Graph API-végpontja. További információkért tekintse meg a [naplózási API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) -referenciát és a [bejelentkezési API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signIn)-referenciát.

Emellett az [Identity Protection kockázati eseményei API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) -val programozási hozzáférést nyerhet a biztonsági észlelésekhez Microsoft Graph használatával. További információ: Ismerkedés [a Azure Active Directory Identity Protection és Microsoft Graphával](../identity-protection/graph-get-started.md). 

> [!NOTE]
>  A **https:\/\/Graph.Windows.net\/bérlői\>-név\/jelentésekvégpontjaelavult\/ .\<** A fentiekben ismertetett új API-végpontok használatával programozott módon férhet hozzá a tevékenység-és biztonsági jelentésekhez.
  
## <a name="apis-with-graph-explorer"></a>API-k a Graph Explorerrel

A [MSGraph Explorer](https://developer.microsoft.com/graph/graph-explorer) segítségével ellenőrizheti a bejelentkezési és a naplózási API-adatait. Ügyeljen arra, hogy jelentkezzen be a fiókjába a Graph Explorer felhasználói felületének mindkét bejelentkezési gombján, és állítsa be a **AuditLog. Read. All** és a **Directory. Read. All** engedélyeket a bérlőhöz az ábrán látható módon.   

![Graph Explorer](./media/concept-reporting-api/graph-explorer.png)

![Engedélyek módosítása felhasználói felület](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Tanúsítványok használata az Azure AD Reporting API eléréséhez 

Használja az Azure AD Reporting API-t a tanúsítványokkal, ha felhasználói beavatkozás nélkül szeretné lekérni a jelentéskészítési adatgyűjtést.

Részletes utasításokért lásd: [Az Azure ad Reporting API és a tanúsítványok használatával kapcsolatos információk](tutorial-access-api-with-certificates.md)lekérése.

## <a name="next-steps"></a>További lépések

 * [A jelentéskészítési API elérésének előfeltételei](howto-configure-prerequisites-for-reporting-api.md) 
 * [Az Azure AD Reporting API és a tanúsítványok használatával kapott adatlekérdezés](tutorial-access-api-with-certificates.md)
 * [Hibák elhárítása az Azure AD Reporting API-ban](troubleshoot-graph-api.md)


