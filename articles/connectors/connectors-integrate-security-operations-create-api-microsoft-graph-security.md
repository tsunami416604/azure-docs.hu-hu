---
title: Biztonsági műveletek integrálása a Microsoft Graph-biztonság – Azure Logic Apps
description: Növelheti a veszélyforrások elleni védelem, észlelés és válasz képességek az alkalmazás kezelése a Microsoft Graph biztonsági & Azure Logic Apps biztonsági műveletek
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 01/30/19
tags: connectors
ms.openlocfilehash: 647df9e73804c8f261b58d5ede7c4b030d448fed
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513518"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Veszélyforrások elleni védelem növelheti a biztonsági műveletek integrálása a Microsoft Graph biztonság és az Azure Logic Apps

A [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a [Microsoft Graph biztonsági](https://docs.microsoft.com/graph/security-concept-overview) -összekötő, javíthatja, hogyan észleli az alkalmazását, védi és válaszol fenyegetések-való integrálásához Microsoft automatizált munkafolyamatok létrehozásával biztonsági termékek, szolgáltatások és partnerek. Létrehozhat például [forgatókönyvek az Azure Security Center](../security-center/security-center-playbooks.md) , amelyek figyelése és kezelése a Microsoft Graph-Security-entitásokkal, például a riasztások. Íme néhány a Microsoft Graph-Security-összekötő által támogatott forgatókönyveket:

* Alapján lekérdezések vagy által riasztási értesítéseket kaphat Például kaphat egy listát, amely tartalmazza a magas súlyossági szintű riasztások.
* Riasztások frissítése. Például frissítheti a riasztás hozzárendeléseket, megjegyzéseket fűzhet a riasztások, vagy riasztások megjelölése.
* Figyelő észleli, ha a riasztások rendszer által létrehozott vagy módosított létrehozása [riasztási előfizetések (webhookok)](https://docs.microsoft.com/graph/api/resources/webhooks).
* A riasztási előfizetések kezelése. Például aktív előfizetések beolvasása, kiterjesztheti a lejárati időt egy előfizetést, vagy az előfizetések törlése.

A logikai alkalmazás munkafolyamat használhatja a műveleteket, választ kaphat a Microsoft Graph-Security-összekötőről származó, és elérhetővé teheti, hogy a kimeneti egyéb műveleteket hajthat végre a munkafolyamatban. A munkafolyamat használja a Microsoft Graph biztonsági összekötő-műveletek kimenete más műveleteket is lehet. Például ha a Microsoft Graph-Security-összekötő segítségével magas súlyossági szintű riasztások, elküldheti ezeket a riasztásokat egy e-mail üzenet az Outlook-összekötő használatával. 

Microsoft Graph-biztonsággal kapcsolatos további információkért tekintse meg a [áttekintése a Microsoft Graph API-val biztonsági](https://aka.ms/graphsecuritydocs). Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Ha a Microsoft Flow vagy PowerApps keres, tekintse meg a [Mi az a folyamat?](https://flow.microsoft.com/) vagy [Mi a PowerApps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* A Microsoft Graph-Security-összekötő használatához rendelkeznie kell *explicit módon megadott* része az Azure Active Directory (AD) bérlő rendszergazdai jóváhagyást, a [Microsoft Graph biztonsági hitelesítési követelmények ](https://aka.ms/graphsecurityauth). A jóváhagyás szükséges, a Microsoft Graph Security connector Alkalmazásazonosítót és nevet, amely is található a [az Azure portal](https://portal.azure.com):

   | Tulajdonság | Érték |
   |----------|-------|
   | **Alkalmazás neve** | `MicrosoftGraphSecurityConnector` |
   | **Alkalmazás azonosítója** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
   |||

   Az összekötő hozzájárulását, az Azure AD-Bérlői rendszergazda vagy ezeket a lépéseket kövesse:

   * [Adja meg az Azure AD-alkalmazások bérlői rendszergazdai jóváhagyást](../active-directory/develop/v2-permissions-and-consent.md).

   * Során a logikai alkalmazás első futtatása, az alkalmazás használói kérhetnek jóváhagyási keresztül az Azure AD-Bérlői rendszergazda a [alkalmazás jóváhagyási felületen](../active-directory/develop/application-consent-experience.md).
   
* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné elérni a Microsoft Graph biztonsági entitások, például a riasztások. Jelenleg ez az összekötő nem tartozik eseményindító. Tehát egy Microsoft Graph biztonsági műveletet használja, indítsa el a logikai alkalmazás egy eseményindítóval, például a **ismétlődési** eseményindító.

## <a name="connect-to-microsoft-graph-security"></a>Csatlakozás a Microsoft Graph-biztonság 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. Üres logikai alkalmazások esetében adja hozzá az eseményindító és bármely más olyan műveleteket, amelyeket a Microsoft Graph biztonsági művelet hozzáadása előtt.

   – vagy –

   Meglévő logic Apps alkalmazások, az utolsó lépés egy Microsoft Graph biztonsági műveletet, amelyre a válasszon **új lépés**.

   – vagy –

   Lépések közötti művelet hozzáadása, helyezze az egérmutatót a nyíl lépések között. 
   Válassza a plusz jelre (+), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.

1. A keresőmezőbe írja be a "a microsoft graph biztonság" szűrőként. Jelölje ki az elvégzendő műveletek listájában.

1. Jelentkezzen be a Microsoft Graph biztonsági hitelesítő adataival.

1. Adja meg a szükséges adatokat a kiválasztott művelet, és továbbra is használhatja a logic app-munkafolyamatot.

## <a name="add-actions"></a>Műveletek hozzáadása

Az alábbiakban elérhető különböző műveletek használatával a Microsoft Graph-Security-összekötővel kapcsolatos részletesebb információt.

### <a name="manage-alerts"></a>Riasztások kezelése

Szűrési, rendezni, vagy a legutóbbi eredményt, adja meg *csak* a [ODATA-lekérdezésparamétereket támogatja a Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Ne adjon meg* a teljes alap URL-cím vagy a HTTP-művelet, például `https://graph.microsoft.com/v1.0/security/alerts`, vagy a `GET` vagy `PATCH` műveletet. Íme egy olyan példát, amely megjeleníti a paramétereket egy **értesítéseket kaphat** művelet, ha azt szeretné, hogy a magas súlyossági szintű riasztások listáját:

`Filter alerts value as Severity eq 'high'`

Ezzel az összekötővel használhatja a lekérdezésekkel kapcsolatos további információkért lásd: a [Microsoft Graph biztonsági riasztások referenciadokumentációt](https://docs.microsoft.com/graph/api/alert-list). Ezzel az összekötővel fokozott élményt hozhat létre, tudjon meg többet a [séma tulajdonságok riasztások](https://docs.microsoft.com/graph/api/resources/alert) , hogy az összekötő támogatja.

| Műveletek | Leírás |
|--------|-------------|
| **Riasztások beolvasása** | Get riasztások alapján szűrt egy vagy több [riasztási tulajdonságok](https://docs.microsoft.com/graph/api/resources/alert), például: <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **Beolvasása azonosító alapján riasztása** | Beolvasása egy adott riasztás alapján a riasztás azonosítója. | 
| **Riasztás frissítése** | A riasztási azonosító alapján adott riasztás frissítése <p>Ahhoz, hogy a szükséges és szerkeszthető tulajdonságait adja át a kérést, tekintse meg a [riasztások tartozik szerkeszthető tulajdonság](https://docs.microsoft.com/graph/api/alert-update). Például, hogy hozzárendel egy riasztást a biztonsági elemző, így azok segítségével megvizsgálhatja, frissítheti a riasztás **rendelt** tulajdonság. |
|||

### <a name="manage-alert-subscriptions"></a>Értesítés-előfizetések kezelése

Támogatja a Microsoft Graph [ *előfizetések*](https://docs.microsoft.com/graph/api/resources/subscription), vagy [ *webhookok*](https://docs.microsoft.com/graph/api/resources/webhooks). Szeretne kapni, frissítés, vagy az előfizetések törlése, adja meg a [ODATA-lekérdezésparamétereket támogatja a Microsoft Graph](https://docs.microsoft.com/graph/query-parameters) a Microsoft Graph-entitást létrehozni, és tartalmazzák `security/alerts` az ODATA-lekérdezés követ. 
*Nem tartalmazzák a* az alap URL-CÍMÉT, például `https://graph.microsoft.com/v1.0`. Ehelyett használja a formátumot ebben a példában:

`security/alerts?$filter=status eq 'New'`

| Műveletek | Leírás |
|--------|-------------|
| **Előfizetések létrehozása** | [Hozzon létre egy előfizetést](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) , értesítést küld a módosításokat. Szűrheti a kívánt riasztási típus ehhez az előfizetéshez. Például létrehozhat egy előfizetést, amely értesíti, magas súlyossági szintű riasztások is. |
| **Aktív előfizetések beolvasása** | [Lejárt az előfizetések beolvasása](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Előfizetés frissítése** | [Előfizetés frissítése](https://docs.microsoft.com/graph/api/subscription-update) azáltal, hogy az előfizetés azonosítóját. Például az előfizetés kiterjeszteni, frissítheti az előfizetéshez tartozó `expirationDateTime` tulajdonság. | 
| **Előfizetés törlése** | [Töröl egy előfizetést](https://docs.microsoft.com/graph/api/subscription-delete) azáltal, hogy az előfizetés azonosítóját. | 
||| 

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek korlátok, eseményindítók és műveletek, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át az összekötő [referencialapja](https://aka.ms/graphsecurityconnectorreference).

## <a name="get-support"></a>Támogatás kérése

A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)
