---
title: Biztonsági műveletek integrálása és kezelése & Microsoft Graph biztonsággal
description: Javítsa az alkalmazás veszélyforrások elleni védelmét, észlelését és válaszát Microsoft Graph biztonsági & Azure Logic Apps
services: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 01/30/2019
tags: connectors
ms.openlocfilehash: 7e9cc2d8d38af7e5e6cf26ccc3659ee58ef17e59
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789052"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>A biztonsági műveletek Microsoft Graph biztonsági & való integrálásával növelheti a veszélyforrások elleni védelmet Azure Logic Apps

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és a [Microsoft Graph biztonsági](https://docs.microsoft.com/graph/security-concept-overview) összekötővel javíthatja az alkalmazás által észlelt, védett és a fenyegetésekre való reagálást a Microsoft biztonsági termékeinek, szolgáltatásainak és partnereinek integrálására szolgáló automatizált munkafolyamatok létrehozásával. Létrehozhat például olyan [Azure Security Center](../security-center/security-center-playbooks.md) forgatókönyveket, amelyek Microsoft Graph biztonsági entitások, például riasztások figyelésére és kezelésére szolgálnak. Az Microsoft Graph biztonsági összekötő által támogatott néhány forgatókönyv:

* Riasztások lekérése lekérdezés vagy riasztási azonosító alapján. Lekérhet például egy olyan listát, amely nagy súlyosságú riasztásokat tartalmaz.
* Riasztások frissítése. Például frissítheti a riasztási hozzárendeléseket, megjegyzéseket fűzhet a riasztásokhoz, vagy címkézheti a riasztásokat.
* A riasztási [előfizetések (webhookok)](https://docs.microsoft.com/graph/api/resources/webhooks)létrehozásával figyelheti a riasztások létrehozását vagy módosítását.
* A riasztási előfizetések kezelése. Lekérheti például az aktív előfizetéseket, kiterjesztheti az előfizetés lejárati idejét, vagy törölhet előfizetéseket.

A logikai alkalmazás munkafolyamata olyan műveleteket is használhat, amelyek válaszokat kapnak a Microsoft Graph biztonsági összekötőtől, és ezt a kimenetet a munkafolyamat más műveletei számára is elérhetővé teszik. A munkafolyamatban más műveletek is megadhatók a Microsoft Graph biztonsági összekötő műveleteiből származó kimenet használatával. Ha például magas súlyosságú riasztásokat kap a Microsoft Graph biztonsági összekötőn keresztül, az Outlook Connector használatával e-mailben elküldheti ezeket a riasztásokat egy e-mail-üzenetben. 

A Microsoft Graph biztonsággal kapcsolatos további tudnivalókért tekintse meg a [Microsoft Graph biztonsági API áttekintése](https://aka.ms/graphsecuritydocs)című témakört. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át a következőt: [Mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md). Ha Microsoft Flow vagy PowerApps keres, tekintse meg a [Mi az a flow?](https://flow.microsoft.com/) című témakört. vagy [Mi a PowerApps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* A Microsoft Graph biztonsági összekötő használatához *explicit módon* meg kell adnia Azure Active Directory (ad) bérlői rendszergazdai beleegyezését, amely a [Microsoft Graph biztonsági hitelesítési követelményeinek](https://aka.ms/graphsecurityauth)részét képezi. Ehhez a megkötéshez a Microsoft Graph biztonsági összekötő alkalmazás-azonosítója és neve szükséges, amelyet a [Azure Portal](https://portal.azure.com)is talál:

   | Tulajdonság | Value (Díj) |
   |----------|-------|
   | **Alkalmazás neve** | `MicrosoftGraphSecurityConnector` |
   | **Alkalmazás azonosítója** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
   |||

   Az összekötő engedélyezéséhez az Azure AD-bérlő rendszergazdája a következő lépéseket hajthatja végre:

   * [Adja meg a bérlői rendszergazdai jóváhagyást az Azure ad-alkalmazásokhoz](../active-directory/develop/v2-permissions-and-consent.md).

   * A logikai alkalmazás első futtatásakor az alkalmazás beleegyezett az Azure AD-bérlő rendszergazdájától az [alkalmazás-beleegyező felhasználói](../active-directory/develop/application-consent-experience.md)felületen keresztül.
   
* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez el szeretné érni Microsoft Graph biztonsági entitásait, például a riasztásokat. Ez az összekötő jelenleg nem rendelkezik eseményindítókkal. Ha tehát Microsoft Graph biztonsági műveletet szeretne használni, indítsa el a logikai alkalmazást egy eseményindítóval, például az **Ismétlődés** eseményindítóját.

## <a name="connect-to-microsoft-graph-security"></a>Kapcsolódás Microsoft Graph biztonsághoz 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Az üres logikai alkalmazások esetében adja hozzá a triggert és minden egyéb műveletet, amelyet a Microsoft Graph biztonsági művelet hozzáadása előtt szeretne használni.

   – vagy –

   Meglévő Logic apps esetén az utolsó lépésben, amelyhez Microsoft Graph biztonsági műveletet szeretne hozzáadni, válassza az **új lépés**lehetőséget.

   – vagy –

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. 
   Válassza ki a megjelenő pluszjelet (+), majd válassza a **művelet hozzáadása**lehetőséget.

1. A keresőmezőbe írja be a "Microsoft Graph Security" kifejezést a szűrőként. A műveletek listából válassza ki a kívánt műveletet.

1. Jelentkezzen be Microsoft Graph biztonsági hitelesítő adataival.

1. Adja meg a kiválasztott művelethez szükséges adatokat, és folytassa a logikai alkalmazás munkafolyamatának összeállítását.

## <a name="add-actions"></a>Műveletek hozzáadása

Az alábbiakban részletesebb információkat talál a Microsoft Graph biztonsági összekötővel elérhető különböző műveletek használatáról.

### <a name="manage-alerts"></a>Riasztások kezelése

A legutóbbi eredmények szűréséhez, rendezéséhez vagy beszerzéséhez *csak* a [Microsoft Graph által támogatott ODATA-lekérdezési paramétereket](https://docs.microsoft.com/graph/query-parameters)adja meg. *Ne adja meg* a teljes alap URL-címet vagy a http-műveletet, például `https://graph.microsoft.com/v1.0/security/alerts`, vagy a `GET` vagy `PATCH` műveletet. Íme egy konkrét példa, amely a **riasztások beolvasása** művelet paramétereit jeleníti meg, ha nagy súlyosságú riasztásokkal rendelkező listát szeretne:

`Filter alerts value as Severity eq 'high'`

Az összekötővel használható lekérdezésekkel kapcsolatos további információkért tekintse meg a [Microsoft Graph biztonsági riasztások dokumentációját](https://docs.microsoft.com/graph/api/alert-list). Az összekötővel folytatott továbbfejlesztett funkciók létrehozásával kapcsolatban további információt olvashat az összekötő által támogatott [séma-tulajdonságok riasztásokról](https://docs.microsoft.com/graph/api/resources/alert) .

| Műveletek | Leírás |
|--------|-------------|
| **Riasztások beolvasása** | Egy vagy több [riasztási tulajdonság](https://docs.microsoft.com/graph/api/resources/alert)alapján szűrt riasztások lekérése, például: <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **Riasztás beolvasása azonosító alapján** | Egy adott riasztást kap a riasztás azonosítója alapján. | 
| **Riasztás frissítése** | Egy adott riasztás frissítése a riasztás azonosítója alapján. <p>A kérelemben szereplő kötelező és szerkeszthető tulajdonságok megadásához tekintse meg a [riasztások szerkeszthető tulajdonságait](https://docs.microsoft.com/graph/api/alert-update). Ha például riasztást szeretne hozzárendelni egy biztonsági elemzőhöz, hogy el lehessen végezni a vizsgálatot, frissítheti a riasztáshoz **rendelt** tulajdonságot. |
|||

### <a name="manage-alert-subscriptions"></a>Riasztás-előfizetések kezelése

Microsoft Graph támogatja az [*előfizetéseket*](https://docs.microsoft.com/graph/api/resources/subscription)vagy [*webhookokat*](https://docs.microsoft.com/graph/api/resources/webhooks). Az előfizetések lekéréséhez, frissítéséhez vagy törléséhez adja meg a [Microsoft Graph által támogatott ODATA-lekérdezési paramétereket](https://docs.microsoft.com/graph/query-parameters) a Microsoft Graph entitás-létrehozáshoz, és adja meg a `security/alerts`, amelyet a ODATA-lekérdezés követ. 
*Ne adja* meg az alap URL-címet, például `https://graph.microsoft.com/v1.0`. Ehelyett használja az ebben a példában szereplő formátumot:

`security/alerts?$filter=status eq 'New'`

| Műveletek | Leírás |
|--------|-------------|
| **Előfizetések létrehozása** | [Hozzon létre egy előfizetést](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) , amely értesítést küld a változásokról. Ezt az előfizetést szűrheti a kívánt riasztási típusokra. Létrehozhat például egy olyan előfizetést, amely értesíti a nagy súlyosságú riasztásokról. |
| **Aktív előfizetések beolvasása** | [Lejárt előfizetések beolvasása](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Előfizetés frissítése** | [Előfizetés frissítése](https://docs.microsoft.com/graph/api/subscription-update) az előfizetés-azonosító megadásával. Az előfizetés kiterjesztéséhez például frissítheti az előfizetés `expirationDateTime` tulajdonságát. | 
| **Előfizetés törlése** | [Előfizetés törlése](https://docs.microsoft.com/graph/api/subscription-delete) az előfizetés-azonosító megadásával. | 
||| 

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos technikai részletekért lásd az összekötő OpenAPI (korábban: hencegés) leírását, tekintse át az összekötő [hivatkozási oldalát](https://aka.ms/graphsecurityconnectorreference).

## <a name="get-support"></a>Támogatás kérése

A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Következő lépések

További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
