---
title: Frissítés az Azure Analysis Services-hez készült logic apps modellekkel | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan kódolhatja az Azure Analysis Services aszinkron frissítését az Azure Logic Apps használatával.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: 78bc629598c0635b7760285d0507b7a85a4ab551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127024"
---
# <a name="refresh-with-logic-apps"></a>Frissítés a Logic Apps használatával

A Logic Apps és a REST-hívások használatával automatizált adatfrissítési műveleteket hajthat végre az Azure Analysis táblázatos modelljein, beleértve a csak olvasható replikák szinkronizálását a lekérdezés horizontális felskálázásához.

Ha többet szeretne tudni a REST API-k Azure Analysis Services használatával kapcsolatos további tudnivalókról: [Aszinkron frissítés a REST API-val című témakörben.](analysis-services-async-refresh.md)

## <a name="authentication"></a>Hitelesítés

Minden hívást hitelesíteni kell egy érvényes Azure Active Directory (OAuth 2) jogkivonattal.  A jelen cikkben szereplő példák egy egyszerű szolgáltatás (SPN) használatával hitelesíti az Azure Analysis Services. További információ: [Egyszerű szolgáltatás létrehozása az Azure Portal használatával](../active-directory/develop/howto-create-service-principal-portal.md)című témakörben olvashat.

## <a name="design-the-logic-app"></a>A logikai alkalmazás tervezése

> [!IMPORTANT]
> A következő példák feltételezik, hogy az Azure Analysis Services tűzfal le van tiltva. Ha a tűzfal engedélyezve van, a kérelem kezdeményezőjének nyilvános IP-címét az Azure Analysis Services tűzfalán kell engedélyezni. Ha régiónként szeretne többet megtudni az Azure Logic Apps IP-tartományairól, olvassa [el az Azure Logic Apps korlátozási és konfigurációs információi című témakört.](../logic-apps/logic-apps-limits-and-config.md#configuration)

### <a name="prerequisites"></a>Előfeltételek

#### <a name="create-a-service-principal-spn"></a>Egyszerű szolgáltatás létrehozása

Az egyszerű szolgáltatás létrehozásáról az Egyszerű szolgáltatás létrehozása az Azure Portal használatával című [témakörben](../active-directory/develop/howto-create-service-principal-portal.md)olvashat.

#### <a name="configure-permissions-in-azure-analysis-services"></a>Engedélyek konfigurálása az Azure Analysis Services szolgáltatásban
 
A létrehozott egyszerű szolgáltatásnak kiszolgálórendszergazdai engedélyekkel kell rendelkeznie a kiszolgálón. További információ: [Egyszerű szolgáltatás hozzáadása a kiszolgálói rendszergazdai szerepkörhöz](analysis-services-addservprinc-admins.md)című témakörben olvashat.

### <a name="configure-the-logic-app"></a>A logikai alkalmazás konfigurálása

Ebben a példában a logikai alkalmazás http-kérelem fogadásakor aktiválódik. Ez lehetővé teszi egy vezénylési eszköz, például az Azure Data Factory használatát az Azure Analysis Services modell frissítésének aktiválásához.

Miután létrehozott egy logikai alkalmazást:

1. A Logic App designerben válassza ki az első műveletet **HTTP-kérelem érkezésekor.**

   ![HTTP fogadott tevékenység hozzáadása](./media/analysis-services-async-refresh-logic-app/1.png)

Ez a lépés a Logic App mentése után feltölti a HTTP POST URL-címet.

2. Új lépés hozzáadása és **HTTP**keresése.  

   ![HTTP-tevékenység hozzáadása](./media/analysis-services-async-refresh-logic-app/9.png)

   ![HTTP-tevékenység hozzáadása](./media/analysis-services-async-refresh-logic-app/10.png)

3. A művelet hozzáadásához válassza a **HTTP** lehetőséget.

   ![HTTP-tevékenység hozzáadása](./media/analysis-services-async-refresh-logic-app/2.png)

Konfigurálja a HTTP-tevékenységet az alábbiak szerint:

|Tulajdonság  |Érték  |
|---------|---------|
|**Módszer**     |POST         |
|**Uri**     | https://*a szerver régió*/szerverek/*aas szerver név*/modellek / az adatbázis*neve*/ frissít <br /> <br /> Például: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes|
|**Fejlécek**     |   Tartalomtípus, alkalmazás/json <br /> <br />  ![Fejlécek](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Törzs**     |   Ha többet szeretne tudni a kérelemtörzs létrehozásáról, olvassa el [az Aszinkron frissítés a REST API-val - POST /refreshs című témakört.](analysis-services-async-refresh.md#post-refreshes) |
|**Hitelesítés**     |Active Directory OAuth         |
|**Bérlő**     |Töltse ki az Azure Active Directory tenantId azonosítóját         |
|**Célközönség**     |https://*.asazure.windows.net         |
|**Ügyfélazonosító**     |Adja meg az egyszerű szolgáltatásnév ügyfélazonosítóját         |
|**Hitelesítő adatok típusa**     |Titkos         |
|**Titkos**     |Adja meg az egyszerű szolgáltatásnév titkos adattitkát         |

Példa:

![Befejezett HTTP-tevékenység](./media/analysis-services-async-refresh-logic-app/7.png)

Most tesztelje a Logikai alkalmazást.  A Logikai alkalmazás tervezőjében kattintson a **Futtatás**gombra.

![A logikai alkalmazás tesztelése](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Használja fel a logikai alkalmazást az Azure Data Factory segítségével

A logikai alkalmazás mentése után tekintse át a **HTTP-kérelem fogadásának lekérése** közben tevékenységet, majd másolja a most létrehozott **HTTP-POST URL-címet.**  Ez az URL-cím, amely et az Azure Data Factory használhatja az aszinkron hívás a logikai alkalmazás aktiválásához.

Íme egy példa az Azure Data Factory webes tevékenység, amely ezt a műveletet.

![Adatgyári webes tevékenység](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Önálló logikai alkalmazás használata

Ha nem tervezi egy vezénylési eszköz, például a Data Factory a modell frissítésének aktiválásához, beállíthatja a logikai alkalmazást, hogy a frissítés ütemezés alapján.

A fenti példában törölje az első tevékenységet, és cserélje le **egy Ütemezési** tevékenységre.

![Tevékenység ütemezése](./media/analysis-services-async-refresh-logic-app/12.png)

![Tevékenység ütemezése](./media/analysis-services-async-refresh-logic-app/13.png)

Ez a példa az **Ismétlődés**.

A tevékenység hozzáadása után konfigurálja az Időköz és gyakoriság beállítást, majd adjon hozzá egy új paramétert, és válassza **a Most an hours (Ezekben az órákban)** lehetőséget.

![Tevékenység ütemezése](./media/analysis-services-async-refresh-logic-app/16.png)

Válassza ki a kívánt órákat.

![Tevékenység ütemezése](./media/analysis-services-async-refresh-logic-app/15.png)

Mentse a logikai alkalmazást.

## <a name="next-steps"></a>További lépések

[Minták](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
