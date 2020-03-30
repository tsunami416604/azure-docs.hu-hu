---
title: Microsoft Graph API | Microsoft dokumentumok
description: A Microsoft Graph API egy RESTful webes API, amely lehetővé teszi a Microsoft Cloud szolgáltatás erőforrásainak elérését.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 6c1b4390282f6d54178365714b1e2e665b4cf061
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136500"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

A Microsoft Graph API egy RESTful webes API, amely lehetővé teszi a Microsoft Cloud szolgáltatás erőforrásainak elérését. Miután regisztrálta az alkalmazást, és hitelesítési jogkivonatokat kért egy felhasználóhoz vagy szolgáltatáshoz, kéréseket tehet a Microsoft Graph API-hoz. További információt a Microsoft Graph áttekintése című [témakörben talál.](https://docs.microsoft.com/graph/overview)

A Microsoft Graph elérhetővé teszi a REST API-kat és az ügyféltárakat a következő Microsoft 365-szolgáltatások adatainak eléréséhez:
- Office 365-szolgáltatások: Delve, Excel, Microsoft Bookings, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner és SharePoint
- Vállalati mobilitási és biztonsági szolgáltatások: Komplex fenyegetéselemzés, komplex veszélyforrások elleni védelem, Azure Active Directory, Identity Manager és Intune
- Windows 10-szolgáltatások: tevékenységek, eszközök, értesítések
- Dynamics 365 Business Central

## <a name="versions"></a>Verziók

A Microsoft Graph jelenleg két verziót támogat: az 1.0-s verziót és a bétaverziót. A v1.0-s verzió általánosan elérhető API-kat tartalmaz. Használja a v1.0-s verziót az összes éles alkalmazáshoz. A béta verzió az előzetes verzióban elérhető API-kat tartalmazza. Mivel előfordulhat, hogy a béta VERZIÓink ban törésmódosításokat vezetünk be, azt javasoljuk, hogy a bétaverziót csak a fejlesztés alatt lévő alkalmazások tesztelésére használja; ne használjon béta API-kat az éles alkalmazásokban. További információt a [Microsoft Graph verziószámozási, támogatási és módosítási szabályzatának megszegése című témakörben talál.](https://docs.microsoft.com/graph/versioning-and-support)

A béta API-k használatának megkezdéséhez olvassa el a [Microsoft Graph bétavégpont-hivatkozási témakörét](https://docs.microsoft.com/graph/api/overview?view=graph-rest-beta)

A v1.0 API-k használatának megkezdéséhez olvassa el a [Microsoft Graph REST API 1.0-s verziós hivatkozását.](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)

## <a name="get-started"></a>Bevezetés

Ha egy erőforrásból, például felhasználóról vagy e-mailüzenetből szeretne olvasni vagy írni, a következőhöz hasonló kérést kell létrehoznia:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

A létrevett kérelem elemeiről a Microsoft Graph API használata című témakörben talál további [információt.](https://docs.microsoft.com/graph/use-the-api)

A gyorsindítási minták segítségével megtudhatja, hogyan érheti el a Microsoft Graph API-t. Az elérhető minták két szolgáltatást érhetnek el egy hitelesítéssel: a Microsoft-fiókot és az Outlookot. Minden rövid útmutató a Microsoft-fiók felhasználóinak profiljaiból fér hozzá az információkhoz, és megjeleníti az eseményeket a naptárból.
A rövid útmutatók négy lépést foglalnak magukban:
- Válassza ki a platformot
- Az alkalmazásazonosító (ügyfélazonosító) beszerezése
- A minta összeállítása
- Bejelentkezés és események megtekintése a naptárban

A rövid útmutató befejezésekor egy használatra kész alkalmazással rendelkezik. További információt a [Microsoft Graph rövidútmutató – gyakori kérdések című témakörben talál.](https://docs.microsoft.com/graph/quick-start-faq) A minták első lépései a [Microsoft Graph rövid útmutatója című témakörben vannak.](https://developer.microsoft.com/graph/quick-start)

## <a name="tools"></a>Eszközök

A Microsoft Graph Explorer egy webalapú eszköz, amellyel a Microsoft Graph API-k használatával hozhat létre és tesztelhet kéréseket. A Microsoft Graph Explorer `https://developer.microsoft.com/graph/graph-explorer`a következő helyen érhető el: .

A Postman egy olyan eszköz, amellyel a Microsoft Graph API-k használatával is létrehozhat és tesztelhet kéréseket. Letöltheti Postman a: `https://www.getpostman.com/`. A Microsoft Graph postán való használatához használja a Microsoft Graph gyűjteményt a Postman alkalmazásban. További információt a [Postman használata a Microsoft Graph API-val című témakörben talál.](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)
