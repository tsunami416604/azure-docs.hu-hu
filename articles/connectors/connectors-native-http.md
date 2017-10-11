---
title: "A tetszőleges végpontot HTTP - Azure Logic Apps protokollt használó kommunikációra |} Microsoft Docs"
description: "Hozzon létre a logic apps, amely képes kommunikálni a tetszőleges végpontot HTTP Protokollon keresztül"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: e11c6b4d-65a5-4d2d-8e13-38150db09c0b
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: d422a07a27ffa62a673bd2d471ae4fc837251dee
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-http-action"></a>Ismerkedjen meg a HTTP-művelet

A HTTP-művelettel munkafolyamatok kiterjesztheti a szervezet és a tetszőleges végpontot HTTP protokollt használó kommunikációra.

A következőket teheti:

* Hozzon létre programot (trigger) aktiválása, ha leáll a webhelyet, ahol kezelheti az alkalmazás munkafolyamatok.
* A tetszőleges végpontot HTTP protokollt használó kommunikációra a munkafolyamatok kiterjeszti egyéb szolgáltatásokat.

Első lépések egy logikai alkalmazás a HTTP-művelet használatával, lásd: [logikai alkalmazás létrehozása](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="use-the-http-trigger"></a>A HTTP-eseményindítóval használata
Egy eseményindító egy eseményt, amely segítségével indítsa el a munkafolyamatot, amely a logikai alkalmazás van definiálva. [További tudnivalók az eseményindítók](connectors-overview.md).

Íme egy parancssorozat-példa bemutatja, hogyan állíthatja be a HTTP-eseményindítóval a Logic App tervezőben.

1. Adja hozzá a HTTP-eseményindítóval a Logic Apps alkalmazást.
2. Töltse ki a HTTP-végpont kívánt kérdezze le a paramétereket.
3. Az ismétlődési időköz az, hogy milyen gyakran le kell kérdeznie.

   A logikai alkalmazás most már minden egyes ellenőrzése során visszaküldött tartalommal következik be.

   ![HTTP eseményindító](./media/connectors-native-http/using-trigger.png)

### <a name="how-the-http-trigger-works"></a>A HTTP-eseményindítóval működése

A HTTP-eseményindítóval küld egy ismétlődési időköz HTTP-végpont hívásakor. Alapértelmezés szerint a HTTP-válaszkód, amely kisebb, mint 300 hatására a logikai alkalmazás futtatásához. Adja meg, hogy a logikai alkalmazást kell érvényesítést, szerkessze a logikai alkalmazást a kód nézetre, és adja hozzá a HTTP-hívása után kiértékelésére szolgáló feltétel. Íme egy példa egy HTTP-eseményindítóval, amely akkor következik be, ha az eredményül adott állapotkód: nagyobb vagy egyenlő `400`.

```javascript
"Http":
{
    "conditions": [
        {
            "expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
        }
    ],
    "inputs": {
        "method": "GET",
        "uri": "https://blogs.msdn.microsoft.com/logicapps/",
        "headers": {
            "accept-language": "en"
        }
    },
    "recurrence": {
        "frequency": "Second",
        "interval": 15
    },
    "type": "Http"
}
```

Részletes információ a HTTP-trigger paraméterek érhetők el a [MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger).

## <a name="use-the-http-action"></a>Használja a HTTP-művelet

Egy művelet során, amely a logikai alkalmazás definiált munkafolyamat végzi. 
[További információ a műveletek](connectors-overview.md).

1. Válasszon **új lépés** > **művelet hozzáadása**.
3. A művelet a keresőmezőbe írja be **http** a HTTP-műveletek listáját.
   
    ![Válassza ki a HTTP-művelet](./media/connectors-native-http/using-action-1.png)

4. Adja hozzá a HTTP-hívás a szükséges paramétereket.
   
    ![Fejezze be a HTTP-művelet](./media/connectors-native-http/using-action-2.png)

5. A designer eszköztáron kattintson **mentése**. A Logic Apps alkalmazást mentése és egy időben (aktivált) közzététele.

## <a name="http-trigger"></a>HTTP eseményindító
Az alábbiak az eseményindító, amely támogatja ezt az összekötőt. A HTTP-összekötő egy eseményindító tartozik.

| Eseményindító | Leírás |
| --- | --- |
| HTTP |Egy HTTP-hívást, és a válasz tartalmat adja vissza. |

## <a name="http-action"></a>HTTP-művelet
Az alábbiak a művelet, amely támogatja ezt az összekötőt. A HTTP-összekötő lehetséges egy-egy művelettel rendelkezik.

| Műveletek | Leírás |
| --- | --- |
| HTTP |Egy HTTP-hívást, és a válasz tartalmat adja vissza. |

## <a name="http-details"></a>HTTP-részletek
A következő táblázatok ismertetik, hogy a művelet és a megfelelő kimeneti részletek művelettel társított szükséges és választható bemeneti mező.

#### <a name="http-request"></a>HTTP-kérelem
A művelet, amely a kimenő HTTP-kérelem a beviteli mezők a következők:
A * azt jelenti, hogy mezőt kötelező kitölteni.

| Megjelenített név | Tulajdonság neve | Leírás |
| --- | --- | --- |
| Módszer * |Módszer |A HTTP-műveletet használata |
| URI * |URI |A HTTP-kérelem URI-JÁNAK |
| Fejlécek |Fejlécek |A HTTP-fejlécek felvenni egy JSON-objektum |
| Törzs |Törzs |A HTTP-kérelem törzse |
| Authentication |Hitelesítés |A részletek a [hitelesítési](#authentication) szakasz |

<br>

#### <a name="output-details"></a>Kimeneti részletei
A HTTP-válasz kimeneti részleteit az alábbiakban.

| Tulajdonság neve | Adattípus | Leírás |
| --- | --- | --- |
| Fejlécek |Objektum |Válaszfejlécek |
| Törzs |Objektum |Válasz objektum |
| Állapotkód |int |HTTP-állapotkód: |

## <a name="authentication"></a>Authentication
A Logic Apps szolgáltatás lehetővé teszi a különböző típusú HTTP-végpontokról hitelesítést használja. Használhatja a hitelesítés a **HTTP**,  **[HTTP + Swagger](connectors-native-http-swagger.md)**, és  **[HTTP Webhook](connectors-native-webhook.md)**  összekötők. A következő hitelesítési típusokat, amelyek konfigurálhatók:

* [Az egyszerű hitelesítés](#basic-authentication)
* [Ügyféltanúsítvány-alapú hitelesítés](#client-certificate-authentication)
* [Az Azure Active Directory (Azure AD) OAuth-hitelesítés](#azure-active-directory-oauth-authentication)

#### <a name="basic-authentication"></a>Az egyszerű hitelesítés

A következő hitelesítési objektumot az alapszintű hitelesítés van szükség.
A * azt jelenti, hogy mezőt kötelező kitölteni.

| Tulajdonság neve | Adattípus | Leírás |
| --- | --- | --- |
| Típus * |type |Hitelesítés típusa (lehet `Basic` az egyszerű hitelesítés) |
| Felhasználónév * |felhasználónév |Felhasználónév hitelesítéséhez |
| Jelszó * |jelszó |Jelszó a hitelesítéshez |

> [!TIP]
> Ha szeretné használni, melyek nem kérhető le a definíció, használja a jelszó egy `securestring` paraméter és a `@parameters()`  
>  [munkafolyamat-definíció funkció](http://aka.ms/logicappdocs).

Példa:

```javascript
{
    "type": "Basic",
    "username": "user",
    "password": "test"
}
```

#### <a name="client-certificate-authentication"></a>Ügyféltanúsítvány-hitelesítés

A következő hitelesítési objektum ügyféltanúsítvány-alapú hitelesítés szükséges. A * azt jelenti, hogy mezőt kötelező kitölteni.

| Tulajdonság neve | Adattípus | Leírás |
| --- | --- | --- |
| Típus * |type |A hitelesítés típusát (kell `ClientCertificate` az ügyfél SSL-tanúsítványok) |
| PFX * |PFX |A Base64-kódolású tartalmak a személyes információcseréhez kapcsolódó (PFX) fájl |
| Jelszó * |jelszó |A jelszó megadásával érheti el a PFX-fájlból |

> [!TIP]
> Egy paramétert, amely olvashatók a logic app mentését követően a definíció nem lesz használható a `securestring` paraméter és a `@parameters()`  
>  [munkafolyamat-definíció funkció](http://aka.ms/logicappdocs).

Példa:

```javascript
{
    "type": "ClientCertificate",
    "pfx": "aGVsbG8g...d29ybGQ=",
    "password": "@parameters('myPassword')"
}
```

#### <a name="azure-ad-oauth-authentication"></a>Az Azure AD OAuth-hitelesítés
A következő hitelesítési objektum Azure AD OAuth-hitelesítés szükséges. A * azt jelenti, hogy mezőt kötelező kitölteni.

| Tulajdonság neve | Adattípus | Leírás |
| --- | --- | --- |
| Típus * |type |A hitelesítés típusát (kell `ActiveDirectoryOAuth` az Azure AD OAuth) |
| Bérlői * |Bérlői |A bérlő azonosítóját az Azure AD-bérlő |
| A célközönség * |Célközönség |Az engedélyezési használatára a kért erőforrás. Például:`https://management.core.windows.net/` |
| Ügyfél-azonosító * |clientId |Az ügyfél az Azure AD-alkalmazás azonosítója |
| Titkos kulcs * |titkos kulcs |A titkos kulcsot a jogkivonatot kérő ügyfél |

> [!TIP]
> Használhatja a `securestring` paraméter és a `@parameters()` [munkafolyamat-definíció funkció](http://aka.ms/logicappdocs) egy paraméterrel, amely a kezdeti mentést követően a definíció nem olvasható.
> 
> 

Példa:

```javascript
{
    "type": "ActiveDirectoryOAuth",
    "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "audience": "https://management.core.windows.net/",
    "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
    "secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

## <a name="next-steps"></a>Következő lépések
Most, próbálja ki a platformot és [logikai alkalmazás létrehozása](../logic-apps/logic-apps-create-a-logic-app.md). Megismerheti az egyéb rendelkezésre álló összekötők Logic Apps, ha megnézi a [API-k lista](apis-list.md).

