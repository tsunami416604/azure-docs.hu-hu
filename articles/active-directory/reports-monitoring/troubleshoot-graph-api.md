---
title: Hibák elhárítása Azure Active Directory jelentési API-ban | Microsoft Docs
description: A Azure Active Directory jelentési API-k meghívása során felmerülő hibák megoldását teszi lehetővé.
services: active-directory
documentationcenter: ''
author: cawrites
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
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6f1f34dcece9acb20d0db091152b24b26cb9fa2
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989535"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Hibák elhárítása Azure Active Directory jelentési API-ban

Ez a cikk felsorolja azokat a gyakori hibaüzeneteket, amelyekkel az MS Graph API használatával és a megoldásuk lépéseivel férhet hozzá a tevékenységek jelentéseihez.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 HTTP belső kiszolgálóhiba a Microsoft Graph v2-végpont elérésekor

Jelenleg nem támogatjuk a Microsoft Graph v2-végpontot – ügyeljen arra, hogy a Microsoft Graph v1-végpont használatával hozzáférhessen a tevékenység naplóihoz.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Hiba: Nem sikerült beolvasni a felhasználói szerepköröket az AD Graph-ból

Ez a hibaüzenet akkor jelenik meg, amikor a Graph Explorer használatával próbál hozzáférni a bejelentkezésekhez. Győződjön meg arról, hogy a Graph Explorer felhasználói felületének mindkét bejelentkezési gombján bejelentkezett a fiókjába, ahogy az alábbi képen is látható. 

![Graph Explorer](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Hiba: Nem sikerült a prémium szintű licenc-ellenőrzések végrehajtása az AD Graph-ból 

Ha ezt a hibaüzenetet akkor futtatja, amikor a Graph Explorer használatával próbál hozzáférni a bejelentkezésekhez, válassza a bal oldali navigációs menüben a fiók **módosítása** lehetőséget, majd válassza a **feladatok. READWRITE** és a **könyvtár. olvasás. összes**lehetőséget. 

![Engedélyek módosítása felhasználói felület](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Hiba: Sem a bérlő sem a B2C, sem a bérlő nem rendelkezik prémium szintű licenccel

A bejelentkezési jelentések eléréséhez Azure Active Directory Premium 1 (P1) licenc szükséges. Ha ez a hibaüzenet jelenik meg a bejelentkezések elérésekor, győződjön meg arról, hogy a bérlője Azure AD P1 licenccel rendelkezik.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Hiba: A felhasználó nem szerepel az engedélyezett szerepkörökben 

Ha ez a hibaüzenet akkor jelenik meg, amikor az API-val megpróbál hozzáférni a naplókhoz vagy a bejelentkezésekhez, győződjön meg arról, hogy a fiókja a Azure Active Directory-bérlő **biztonsági olvasójának** vagy a **jelentéskészítő olvasó** szerepkörének része. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Hiba: Az alkalmazásból hiányzik a HRE olvasása engedély 

Kövesse az előfeltételekben ismertetett lépéseket a [Azure Active Directory jelentési API eléréséhez](howto-configure-prerequisites-for-reporting-api.md) , hogy az alkalmazás a megfelelő engedélyekkel legyen fut. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Hiba: Az alkalmazás hiányzó MSGraph API "a naplózási napló összes adatszolgáltatásának olvasása" engedély

Kövesse az előfeltételekben ismertetett lépéseket a [Azure Active Directory jelentési API eléréséhez](howto-configure-prerequisites-for-reporting-api.md) , hogy az alkalmazás a megfelelő engedélyekkel legyen fut. 

## <a name="next-steps"></a>További lépések

[A naplózási API-hivatkozás](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
használata[a bejelentkezési tevékenység jelentésének API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin) -referenciája
