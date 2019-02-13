---
title: Az Azure Active Directory reporting API hibáinak elhárítása |} A Microsoft Docs
description: Nyújt a hibák megoldását az Azure Active Directory Reporting API-k hívása során.
services: active-directory
documentationcenter: ''
author: priyamohanram
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
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41d085b8043470ea16b4c2493760e4dc5875fe15
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56176441"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Az Azure Active Directory reporting API kapcsolatos hibák elhárítása

Ez a cikk a Gyakori hibaüzenetek tevékenységre vonatkozó jelentések az MS Graph API-t és a lépések használatával a névfeloldáshoz elérése során felmerülhet sorolja fel.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500-as HTTP-belső hiba a Microsoft Graph V2 végpont használata közben

Jelenleg nem támogatja a Microsoft Graph-v2 végpont – ellenőrizze, hogy a vizsgálati naplók használata a Microsoft Graph v1 végpont elérésére.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Hiba: Nem sikerült beolvasni a felhasználói szerepköröket a AD Graph

Ezt a hibaüzenetet kaphat bejelentkezések elérésére tett kísérlet során Graph Explorer használatával. Ellenőrizze, hogy jelentkezett be a fiókjába mind a bejelentkezési gomb a Graph Explorer felhasználói felületén, a következő képen látható módon. 

![Graph Explorer](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Hiba: Az AD Graph premium licenc ellenőrzése nem sikerült 

Ha ez a hibaüzenet bejelentkezések elérése közben Graph Explorer használatával, válassza a **módosítási hozzáférést** alul a bal oldali navigációs, és válassza a fiók **Tasks.ReadWrite** és **Directory.Read.All**. 

![Felhasználói felület engedélyek módosítása](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Hiba: Sem bérlő az B2C vagy a bérlő nem rendelkezik premium-licenc

Egy Azure Active Directory premium 1 (P1) jelentkezzen be a jelentések eléréséhez szükséges licencet. Ha ez a hibaüzenet jelenik meg közben: bejelentkezések, ügyeljen arra, hogy a bérlő Azure AD P1 szintű licenccel rendelkezik licenccel.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Hiba: Felhasználó nem szerepel az engedélyezett szerepkörök 

Ha ezt a hibaüzenetet látja miközben megpróbált hozzáférni a naplókhoz és a bejelentkezések a API-val, győződjön meg arról, hogy a fiók tagja a **biztonsági olvasó** vagy **jelentés olvasó** szerepkört az Azure Active Directoryban bérlő. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Hiba: Az alkalmazás AAD "Címtáradatok olvasása" engedéllyel hiányzik 

Kövesse a lépéseket a [az Azure Active Directory reporting API elérésének előfeltételeit](howto-configure-prerequisites-for-reporting-api.md) annak érdekében, hogy az alkalmazás fut, a megfelelő engedélyekkel együtt. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Hiba: Hiányzik az "Összes naplózási Teljesítménynapló-adatok olvasási" engedélyt MSGraph API alkalmazás

Kövesse a lépéseket a [az Azure Active Directory reporting API elérésének előfeltételeit](howto-configure-prerequisites-for-reporting-api.md) annak érdekében, hogy az alkalmazás fut, a megfelelő engedélyekkel együtt. 

## <a name="next-steps"></a>További lépések

[Az API-referencia naplórend](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
[használja a bejelentkezési tevékenységek jelentéskészítő API-referencia](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
