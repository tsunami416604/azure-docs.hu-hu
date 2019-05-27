---
title: A Logic Apps szolgáltatással az Azure Analysis Services-modellekhez frissítése |} A Microsoft Docs
description: Ismerje meg, hogyan aszinkron Adatfrissítés code az Azure Logic Apps használatával.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 6ffce339fe7b1a434c8f007b417ee81a42529dfc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66142495"
---
# <a name="refresh-with-logic-apps"></a>Frissítés a Logic Apps használatával

A Logic Apps és a REST-hívások segítségével, az Azure Analysis táblázatos modellekhez, beleértve a lekérdezések kiterjesztése kibővítése csak olvasható replikák szinkronizálása az automatizált adatfrissítési műveletek is végezhet.

Az Azure Analysis Services REST API-k használatával kapcsolatos további tudnivalókért lásd: [aszinkron frissítése a REST API-val](analysis-services-async-refresh.md).

## <a name="authentication"></a>Hitelesítés

Összes hívás érvényes Azure Active Directory (OAuth 2) tokennel kell hitelesíteni.  Ebben a cikkben szereplő példák egy egyszerű szolgáltatásnév (SPN) használatával hitelesíti az Azure Analysis Services. További tudnivalókért lásd: [egyszerű szolgáltatás létrehozása az Azure portal használatával](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>A Logic App Tervező

> [!IMPORTANT]
> Az alábbi példák azt feltételezik, hogy a az Azure Analysis Services tűzfala le van tiltva.  Ha a tűzfal engedélyezve van, a kérés kezdeményezője nyilvános IP-címét az Azure Analysis Services tűzfala az engedélyezési listán kell lennie. Logikai alkalmazás IP-címtartományok régiónként kapcsolatos további információkért lásd: [korlátozásai és konfigurációs adatokat az Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses).

### <a name="prerequisites"></a>Előfeltételek

#### <a name="create-a-service-principal-spn"></a>Szolgáltatásnév (SPN) létrehozása

Egyszerű szolgáltatás létrehozása kapcsolatos további információkért lásd: [egyszerű szolgáltatás létrehozása az Azure portal használatával](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Az Azure Analysis Services-engedélyek konfigurálása
 
Az egyszerű szolgáltatás létrehozása a kiszolgáló kiszolgáló-rendszergazdai engedélyekkel kell rendelkeznie. További tudnivalókért lásd: [szolgáltatásnév hozzáadása kiszolgálói rendszergazdai szerepkörhöz](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>A logikai alkalmazás konfigurálása

Ebben a példában a logikai alkalmazás célja indítható el, ha HTTP-kérés érkezik. Ez lehetővé teszi egy vezénylési eszköz, például az Azure Data Factory, az Azure Analysis Services-modell frissítés indításához használata.

Miután létrehozott egy logikai alkalmazást:

1. A Logic App Designerben kattintson az első művelet, **amikor egy HTTP-kérés érkezik**.

   ![Fogadott HTTP-tevékenység hozzáadása](./media/analysis-services-async-refresh-logic-app/1.png)

Ebben a lépésben fogja a HTTP POST URL-CÍMÉT adja meg a logikai alkalmazás mentése után.

2. Új lépés hozzáadása, és keressen rá a **HTTP**.  

   ![Adja hozzá a HTTP-tevékenység](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Adja hozzá a HTTP-tevékenység](./media/analysis-services-async-refresh-logic-app/10.png)

3. Válassza ki **HTTP** hozzáadni ezt a műveletet.

   ![Adja hozzá a HTTP-tevékenység](./media/analysis-services-async-refresh-logic-app/2.png)

A HTTP-tevékenység a következőképpen konfigurálja:

|Tulajdonság  |Érték  |
|---------|---------|
|**Metódus**     |POST         |
|**URI-T**     | https://*a kiszolgáló régió*/servers/*aas kiszolgálónév*/models/*az adatbázis neve*/ <br /> <br /> Például: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/|
|**Fejlécek**     |   Content-Type, application/json <br /> <br />  ![Fejlécek](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Törzs**     |   A kérelem törzsében képező kapcsolatos további információkért lásd: [aszinkron frissítése a REST API - bejegyzés /refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Hitelesítés**     |Active Directory OAuth         |
|**bérlő**     |Töltse ki az Azure Active Directory-bérlő azonosítója         |
|**Célközönség**     |https://*.asazure.windows.net         |
|**Ügyfél-azonosító**     |Adja meg a szolgáltatásnév neve ClientID         |
|**Hitelesítő adatok típusa**     |Secret         |
|**Titkos kód**     |Adja meg a szolgáltatásnév neve titkos kód         |

Példa:

![Befejezett HTTP-tevékenység](./media/analysis-services-async-refresh-logic-app/7.png)

Most tesztelje a logikai alkalmazást.  A Logic App Designerben kattintson **futtatása**.

![A logikai alkalmazás tesztelése](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Az Azure Data Factoryvel a logikai alkalmazás felhasználása

A logikai alkalmazás a mentés után tekintse át a **amikor egy HTTP-kérés érkezik** tevékenységre, majd másolja a **HTTP POST URL-címe** most jön létre, amely.  Ez az Azure Data Factory által az aszinkron hívás eseményindítót a logikai alkalmazás használható URL-CÍMÉT.

Íme egy példa az Azure Data Factory webes tevékenység, amely ezt a műveletet.

![Data Factory webes tevékenység](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Önálló logikai alkalmazás használata

Ha nem tervezi a modellfrissítéshez trigger használatával például a Data Factory egy Vezénylési eszköz, a logikai alkalmazás aktiválásához a frissítési ütemezés alapján állíthatja be.

A fenti példa használata esetén az első tevékenység törlése, és cserélje le a **ütemezés** tevékenység.

![Ütemezés tevékenység](./media/analysis-services-async-refresh-logic-app/12.png)

![Ütemezés tevékenység](./media/analysis-services-async-refresh-logic-app/13.png)

Ebben a példában használandó **ismétlődési**.

Ha a tevékenység hozzá lett adva, időközét és gyakoriságát, konfigurálása, majd adjon hozzá egy új paramétert, és válassza **ezekben az órákban**.

![Ütemezés tevékenység](./media/analysis-services-async-refresh-logic-app/16.png)

Válassza ki a kívánt órák.

![Ütemezés tevékenység](./media/analysis-services-async-refresh-logic-app/15.png)

Mentse a logikai alkalmazást.

## <a name="next-steps"></a>További lépések

[Példák](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
