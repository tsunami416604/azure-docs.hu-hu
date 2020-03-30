---
title: Hibák elhárítása az Azure Active Directory jelentéskészítési API-jában | Microsoft dokumentumok
description: Megoldást nyújt az Azure Active Directory-jelentési API-k hívása közbeni hibákra.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d1fb4f49e4f9ad41f971d869873200e6180b5cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399293"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Hibák elhárítása az Azure Active Directory jelentéskészítési API-jában

Ez a cikk felsorolja azokat a gyakori hibaüzeneteket, amelyek a Microsoft Graph API használatával történő tevékenységjelentések elérésekor, valamint a megoldásuk lépéseit sorolják fel.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 HTTP belső kiszolgálóhiba a Microsoft Graph V2 végpontelérése közben

Jelenleg nem támogatjuk a Microsoft Graph v2-végpontját – győződjön meg arról, hogy a Microsoft Graph v1-végpont használatával hozzáférhet a tevékenységnaplókhoz.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Hiba: Egyik bérlő sem B2C, vagy a bérlő nem rendelkezik prémium licenccel

A bejelentkezési jelentések eléréséhez Azure Active Directory prémium 1 (P1) licenc szükséges. Ha ez a hibaüzenet jelenik meg a bejelentkezések elérése közben, győződjön meg arról, hogy a bérlő rendelkezik egy Azure AD P1 licenccel.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Hiba: A felhasználó nem az engedélyezett szerepkörökben van 

Ha ez a hibaüzenet jelenik meg, miközben az API-t használó naplózási naplók vagy bejelentkezések elérésére próbál hozzáférni, győződjön meg arról, hogy a fiók az Azure Active Directory-bérlő **biztonsági olvasó** vagy **jelentésolvasó** szerepkörének része. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Hiba: Az alkalmazásból hiányzik az AAD "Címtáradatok olvasása" engedélye 

Kövesse az Előfeltételek az [Azure Active Directory jelentéskészítési API eléréséhez](howto-configure-prerequisites-for-reporting-api.md) kövesse az alkalmazás megfelelő engedélykészlettel való futtatásának biztosításához. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Hiba: Az alkalmazásból hiányzik a Microsoft Graph API "Az összes naplóadat olvasása" engedély

Kövesse az Előfeltételek az [Azure Active Directory jelentéskészítési API eléréséhez](howto-configure-prerequisites-for-reporting-api.md) kövesse az alkalmazás megfelelő engedélykészlettel való futtatásának biztosításához. 

## <a name="next-steps"></a>Következő lépések

[A naplózási API-hivatkozás](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
[használata A bejelentkezési tevékenység jelentés API-hivatkozásának használata](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
