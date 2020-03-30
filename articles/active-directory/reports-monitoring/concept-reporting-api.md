---
title: Ismerkedés az Azure AD jelentéskészítési API-jával | Microsoft dokumentumok
description: Az Azure Active Directory jelentéskészítési API-jának első lépései
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
ms.openlocfilehash: 56f240a5191dd483f89889f3ffe13b1819ca1e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399316"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Ismerkedés az Azure Active Directory jelentéskészítési API-jával

Az Azure Active Directory számos [jelentést](overview-reports.md)tartalmaz, amelyek hasznos információkat tartalmaznak az alkalmazások, például a SIEM-rendszerek, a naplózás és az üzletiintelligencia-eszközök számára. 

A Microsoft Graph API-t használja az Azure AD-jelentésekhez, és programozott hozzáférést nyerhet az adatokhoz REST-alapú API-k készletén keresztül. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat.

Ez a cikk áttekintést nyújt a jelentéskészítési API-ról, beleértve a hozzáférés módját is.

Ha problémákba ütközik, olvassa el, [hogyan kaphat támogatást az Azure Active Directoryhoz.](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

## <a name="prerequisites"></a>Előfeltételek

A jelentéskészítő API eléréséhez felhasználói beavatkozással vagy anélkül a következőket kell tenni:

1. Szerepkörök hozzárendelése (biztonsági olvasó, biztonsági rendszergazda, globális rendszergazda)
2. Egy alkalmazás regisztrálása
3. Engedélyek megadása
4. Konfigurációs beállítások összegyűjtése

Részletes útmutatást az [Azure Active Directory jelentéskészítési API elérésének előfeltételei ben talál.](howto-configure-prerequisites-for-reporting-api.md) 

## <a name="api-endpoints"></a>API-végpontok 

A naplózási naplók Microsoft Graph `https://graph.microsoft.com/beta/auditLogs/directoryAudits` API-végpontja, a bejelentkezések Microsoft `https://graph.microsoft.com/beta/auditLogs/signIns`Graph API-végpontja pedig a . További információt a [naplózási API-hivatkozás](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) és a [bejelentkezési API-referencia című témakörben talál.](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signIn)

Emellett az [Identity Protection kockázatészlelési API-val](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) programozott hozzáférést kaphat a biztonsági észlelésekhez a Microsoft Graph használatával. További információ: [Az Azure Active Directory identity protection és a Microsoft Graph – első lépések című témakörben talál.](../identity-protection/graph-get-started.md) 
  
## <a name="apis-with-microsoft-graph-explorer"></a>API-k a Microsoft Graph Explorer rel

A Microsoft [Graph explorer](https://developer.microsoft.com/graph/graph-explorer) segítségével ellenőrizheti a bejelentkezési és api-adatok naplózását. Győződjön meg arról, hogy a Graph Explorer felhasználói felületén lévő mindkét bejelentkezési gombbal bejelentkezik a fiókjába, és állítsa be az **AuditLog.Read.All** és **a Directory.Read.All** engedélyeket a bérlőhöz az ábrán látható módon.   

![Graph Explorer](./media/concept-reporting-api/graph-explorer.png)

![Engedélyek felhasználói felületének módosítása](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Az Azure AD jelentéskészítési API eléréséhez használjon tanúsítványokat 

Használja az Azure AD reporting API-t a tanúsítványokkal, ha felhasználói beavatkozás nélkül szeretné lekérni a jelentési adatokat.

Részletes útmutatást az [Azure AD Reporting API tanúsítványokkal történő beolvassa.](tutorial-access-api-with-certificates.md)

## <a name="next-steps"></a>További lépések

 * [A jelentéskészítő API elérésének előfeltételei](howto-configure-prerequisites-for-reporting-api.md) 
 * [Adatok lekérése az Azure AD Reporting API és tanúsítványok használatával](tutorial-access-api-with-certificates.md)
 * [Hibák elhárítása az Azure AD jelentéskészítési API-jában](troubleshoot-graph-api.md)


