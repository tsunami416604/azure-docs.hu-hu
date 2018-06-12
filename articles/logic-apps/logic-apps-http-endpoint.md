---
title: Hívja, indítsa el, vagy a HTTP-végpontok – Azure Logic Apps munkafolyamatok beágyazásához |} Microsoft Docs
description: Állítson be HTTP-végpontokról hívja, eseményindító, vagy az Azure Logic Apps munkafolyamatok egymásba
services: logic-apps
keywords: munkafolyamatok, HTTP-végpontok
author: jeffhollan
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.author: klam; LADocs
ms.openlocfilehash: 9c02a0f540f52007412a850b9d69e337743fc35f
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300198"
---
# <a name="call-trigger-or-nest-workflows-with-http-endpoints-in-logic-apps"></a>Hívja, eseményindító, vagy a logic Apps alkalmazások a HTTP-végpontokról munkafolyamatok egymásba

Is natív módon teszi ki szinkron HTTP-végpontokról a logic apps eseményindítóként használható, hogy indul el, vagy hívja a logic apps segítségével egy URL-címet. A logic Apps alkalmazásait is ágyazhatók be munkafolyamatok hívható végpontok egy mintát.

HTTP-végpontokat hoz létre, adja hozzá ezek az eseményindítók, hogy a logic apps bejövő kérelmek fogadására:

* [Kérés](../connectors/connectors-native-reqres.md)

* [API-kapcsolat Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnection-trigger)

* [HTTP Webhook](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > Bár az ezekben a példákban a **kérelem** eseményindító, a felsorolt HTTP-eseményindítók bármelyikét használhatja, és az összes azonos alapelvek a többi eseményindító típusától.

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>A Logic Apps alkalmazást HTTP végpont beállítása

Hozzon létre egy HTTP-végpontot, vegyen fel egy eseményindítót, amely képes fogadni a beérkező kéréseket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com "Azure Portal") Nyissa meg a logikai alkalmazáshoz, és nyissa meg a Logic App Tervező.

2. Adjon hozzá egy eseményindító, amely lehetővé teszi, hogy a bejövő kérések fogadásához Logic Apps alkalmazást. Adja hozzá például a **kérelem** eseményindító a logikai alkalmazáshoz.

3.  A **kérelem törzse JSON-séma**, nem kötelezően megadhat egy JSON-séma az eseményindító fogadni várt tartalom (adatok).

    A Tervező, amellyel a Logic Apps alkalmazást használ, elemzi és adatok adhatók át az eseményindító a munkafolyamaton keresztül jogkivonatokat előállító használja ebben a sémában. 
    További részletek [JSON-sémák alapján generált jogkivonatok](#generated-tokens).

    Ehhez a példához írja be a tervezőben megjelennek a séma:

    ```json
    {
      "type": "object",
      "properties": {
        "address": {
          "type": "string"
        }
      },
      "required": [
        "address"
      ]
    }
    ```

    ![A kérelem művelet hozzáadása][1]

    > [!TIP]
    > 
    > A sémát a JSON hasznos generálása egy eszköz, például [jsonschema.net](http://jsonschema.net/), vagy a **kérelem** kiválasztásával eseményindító **séma létrehozásához használja a minta hasznos**. 
    > Adjon meg a minta-adattartalmat, és válassza a **végzett**.

    Ha például a minta hasznos:

    ```json
    {
       "address": "21 2nd Street, New York, New York"
    }
    ```

    hoz létre ebben a sémában:

    ```json
    {
       "type": "object",
       "properties": {
          "address": {
             "type": "string" 
          }
       }
    }
    ```

4.  Mentse a logikai alkalmazást. A **az URL-címet a HTTP POST**, most látnia kell egy generált visszahívási URL-t, például ebben a példában:

    ![Generált visszahívási végpont URL-címe](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

    Az URL-cím a hitelesítéshez használt lekérdezési paraméterek közös hozzáférésű Jogosultságkód (SAS) kulcsot tartalmaz. 
    A HTTP-végpont URL-címet is lekérheti a logic app áttekintése az Azure portálon. A **eseményindító előzmények**, válassza ki az eseményindító:

    ![HTTP-végpont URL-cím beszerzése az Azure portálról][2]

    Vagy adott hívás végrehajtása kaphat az URL-cím:

    ```
    POST https://management.azure.com/{logic-app-resourceID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>A HTTP-metódus az eseményindító módosítása

Alapértelmezés szerint a **kérelem** eseményindító vár HTTP POST-kérelmet, de különböző HTTP-metódus használatával kérheti le. 

> [!NOTE]
> Megadhatja, hogy csak egy metódus típusa.

1. Az a **kérelem** indítható el, válassza a **speciális beállítások megjelenítése**.

2. Nyissa meg a **metódus** listája. Ehhez a példához válassza ki a **beolvasása** , hogy később tesztelheti a HTTP-végpont URL-CÍMÉT.

    > [!NOTE]
    > Jelölje be más HTTP-metódus, vagy adjon meg egyéni módszer a saját logikai alkalmazást.

    ![HTTP-metódus módosítása](./media/logic-apps-http-endpoint/change-method.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>Fogadja el a paraméterek használatával a HTTP-végpont URL-címe

Ha azt szeretné, hogy a HTTP végponti URL-Címének fogadnak el paramétereket, testre szabhatja a trigger elem relatív elérési útja.

1. Az a **kérelem** indítható el, válassza a **speciális beállítások megjelenítése**. 

2. A **metódus**, adja meg a HTTP-metódus, amely azt szeretné, hogy a kérelemhez. Ehhez a példához válassza ki a **beolvasása** metódust, ha még nem tette meg, így ellenőrizheti, hogy a HTTP-végpont URL-címet.

      > [!NOTE]
      > Relatív elérési útvonal az eseményindító megadása esetén az eseményindító közvetlenül is adjon meg egy HTTP-metódus.

3. A **relatív elérési út**, adja meg a paraméter, amely az URL-CÍMÉRE kell fogadnia, például a relatív elérési útnak `customers/{customerID}`.

    ![Adja meg a HTTP-metódus és a relatív elérési út paraméter](./media/logic-apps-http-endpoint/relativeurl.png)

4. A paraméter használata esetén vegye fel a **válasz** műveletet úgy, hogy a Logic Apps alkalmazást. (Válassza ki az eseményindító **új lépés** > **művelet hozzáadása** > **válasz**) 

5. A válaszban szereplő **törzs**, tartalmazza a jogkivonatot a paraméter, a trigger elem relatív elérési utat adott meg.

    Ahhoz például, hogy vissza `Hello {customerID}`, frissítése az Ön válaszára **törzs** rendelkező `Hello {customerID token}`. 
    A dinamikus tartalom listába kell jelenik meg, és megjelenítése a `customerID` token ki.

    ![Választörzs paraméter hozzáadása](./media/logic-apps-http-endpoint/relativeurlresponse.png)

    A **törzs** ebben a példában hasonlóan kell kinéznie:

    ![Választörzs paraméterrel](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. Mentse a logikai alkalmazást. 

    A HTTP-végpont URL-cím mostantól tartalmazza a relatív elérési út, például: 

    https&#58;//prod-00.southcentralus.logic.azure.com/workflows/f90cb66c52ea4e9cabe0abf4e197deff/triggers/manual/paths/invoke/customers/{customerID}...

7. A HTTP-végpont teszteléséhez másolja és illessze be a frissített URL-cím egy másik böngészőablakban, de cserélje le `{customerID}` rendelkező `123456`, és nyomja le az ENTER billentyűt.

    A böngésző ezt a szöveget kell megjelenítése: 

    `Hello 123456`

<a name="generated-tokens"></a>

### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>A logikai alkalmazásnak JSON-sémák alapján generált jogkivonatok

Ha megadta a JSON-séma a a **kérelem** eseményindító, a Logic App Designer azokhoz a tulajdonságokhoz jogkivonatokat hoz létre a séma. A jogkivonatok az adatokat a logic app munkafolyamaton keresztül használhatja.

Ehhez a példához, ha a `title` és `name` tulajdonságokat a JSON-séma, a jogkivonatok is elérhető munkafolyamat a későbbi lépésekben használatára. 

A teljes JSON-séma a következő:

```json
{
   "type": "object",
   "properties": {
      "address": {
         "type": "string"
      },
      "title": {
         "type": "string"
      },
      "name": {
         "type": "string"
      }
   },
   "required": [
      "address",
      "title",
      "name"
   ]
}
```

## <a name="create-nested-workflows-for-logic-apps"></a>Beágyazott munkafolyamatok a logic Apps alkalmazások létrehozása

A Logic Apps alkalmazást a munkafolyamatok ágyazhatja más logikai alkalmazások fogadhassanak kérelmek hozzáadásával. Ezek a logic apps, történő felvételéhez adja hozzá a **Azure Logic Apps - válassza ki a Logic Apps munkafolyamat** az eseményindító művelet. Ezután kiválaszthatja a megfelelő logic Apps alkalmazásokból.

![Egy másik logikai alkalmazás hozzáadása](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>Hívás vagy eseményindítót, a logic apps HTTP-végpontokról keresztül

A HTTP-végpont létrehozása után a logikai alkalmazás keresztül is elindíthatja egy `POST` metódust a teljes URL-címet. A Logic apps van a közvetlen hozzáférés végpontok beépített támogatása.

> [!NOTE] 
> Manuálisan a logikai alkalmazás bármikor futtathatja őket, a Logic App Designer vagy a Logic App kódnézetben eszköztáron válassza a **futtatása**.

## <a name="reference-content-from-an-incoming-request"></a>Olyan bejövő kérelemre a referencia-tartalom

Ha a tartalom típusa `application/json`, a bejövő kérelem tulajdonságok is hivatkozni. Ellenkező esetben a tartalom egyetlen egységként bináris, amely képes továbbadni más API-k kezelnek. Ez a munkafolyamat belüli tartalom hivatkozik, akkor konvertálni kell ezt a tartalmat. Ha át például `application/xml` tartalom, használhat `@xpath()` egy XPath kiolvasásához vagy `@json()` az XML konvertálása JSON. További tudnivalók [tartalomtípusok használata](../logic-apps/logic-apps-content-type.md).

Ahhoz, hogy a kimenet egy bejövő kérelem, használhatja a `@triggerOutputs()` függvény. A kimeneti ebben a példában látható:

```json
{
    "headers": {
        "content-type" : "application/json"
    },
    "body": {
        "myProperty" : "property value"
    }
}
```

Hozzáférés a `body` tulajdonság kifejezetten, használhatja a `@triggerBody()` helyi. 

## <a name="respond-to-requests"></a>Válasz a kérelmekre

Előfordulhat, hogy a logikai alkalmazás tartalom visszaküldésével a hívónak bizonyos kérésekre való válaszolás érdemes. Az állapotkód, a fejléc és a szervezet az Ön válaszára történő összeállításához, használhatja a **válasz** művelet. Ez a művelet bárhol megjelenhet a Logic Apps alkalmazást, nem csak a munkafolyamat végén.

> [!NOTE] 
> Ha nem adja meg a Logic Apps alkalmazást egy **válasz**, válaszol a HTTP-végpont *azonnal* rendelkező egy **202 elfogadott** állapotát. Is, az eredeti kérés lekérni a választ, a válasz lépéseit Befejezés belül a [kérelem időkorlátja](./logic-apps-limits-and-config.md) csak akkor hívható meg a munkafolyamat beágyazott logikai alkalmazás számára. Nincs válasz a határidőn belül történik, ha a bejövő kérelem időkorlátja lejár, és a HTTP-válasz fogadása **408 az ügyfél időtúllépése**. A beágyazott a logic apps a szülő logikai alkalmazás továbbra is fennáll, a válaszra, amíg befejeződik, függetlenül attól, hogy mennyi idő szükséges.

### <a name="construct-the-response"></a>A válasz szerkezet

Az adott válasz törzsének egynél több fejléc és a tartalom bármilyen típusú tartalmazhatnak. A példa egy válasz, a fejléc meghatározza, hogy rendelkezik-e a válasz tartalomtípusa `application/json`. és törzsében `title` és `name`korábban frissítve a JSON-séma alapján a **kérelem** eseményindító.

![HTTP-válasz művelet][3]

Válaszok ezekkel a tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás |
| --- | --- |
| állapotkód |Meghatározza a bejövő kérelem válaszol a HTTP-állapotkód. Ez a kód bármilyen érvényes állapotkód 2xx, 4xx vagy 5xx kezdetű lehet. 3xx állapotkódok azonban nem engedélyezett. |
| fejlécek |Meghatározza bármely fejlécek ahhoz, hogy a válasz szerepeljen. |
| törzs |Adja meg a szervezet objektum, amely egy karakterlánc lehet, egy JSON-objektumból, vagy az előző lépésben hivatkozott még bináris tartalom. |

Ez a JSON-séma néz most a **válasz** művelet:

``` json
"Response": {
   "inputs": {
      "body": {
         "title": "@{triggerBody()?['title']}",
         "name": "@{triggerBody()?['name']}"
      },
      "headers": {
           "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {},
   "type": "Response"
}
```

> [!TIP]
> A Logic App Designer a Logic Apps alkalmazást teljes JSON-definícióból megtekintéséhez válassza **nézet Code**.

## <a name="q--a"></a>Kérdések és válaszok

#### <a name="q-what-about-url-security"></a>K: Mi a helyzet URL-cím biztonsági?

V: azure biztonságosan logika visszahívási URL-címek egy közös hozzáférésű Jogosultságkód (SAS) használatával hoz létre. Az aláírás haladnak keresztül lekérdezési paramétert, és ellenőrizni kell, mielőtt a Logic Apps alkalmazást is érvényesítést. Az aláírás logikai alkalmazás, az eseményindító neve és a végrehajtandó műveletet a titkos kulcs egyedi kombinációját használva az Azure létrehoz. Kivéve, ha valaki hozzáfér a titkos logic app kulcs, azok nem hoznak létre érvényes aláírással.

   > [!IMPORTANT]
   > A gyártás és biztonságos erősen ajánlott szemben a Logic Apps alkalmazást hívja közvetlenül a böngészőből, mert:
   > 
   > * A megosztott elérési kulcsot az URL-cím jelenik meg.
   > * Biztonságos tartalom házirendek megosztott tartományok miatt nem tudja kezelni a logikai alkalmazást az ügyfelek között.

#### <a name="q-can-i-configure-http-endpoints-further"></a>K: beállítani a HTTP-végpontokról további?

V: Igen, támogatja a HTTP-végpontokról a keresztül speciális konfigurációs [ **API Management**](../api-management/api-management-key-concepts.md). Ez a szolgáltatás is lehetőséget nyújt a ahhoz, hogy következetesen minden az API-kat, beleértve a logic Apps alkalmazásokat kezeléséhez, állítson be egyéni tartománynevek, használja a további hitelesítési módszerek és egyéb, például:

* [A kérelem módszerének módosítása](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#SetRequestMethod)
* [Módosítsa az URL-szegmenseknek a kérelem](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#RewriteURL)
* Az API Management tartományok beállítása a [Azure-portálon](https://portal.azure.com/ "Azure-portálon")
* Ellenőrizze az egyszerű hitelesítés házirend beállítása

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>K: Mi változott, a séma áttelepítésekor a 2014. decemberi 1 Preview?

V: Itt található egy Összegzés, ezekről a változásokról:

| 2014. decemberi 1 preview | 2016. június 1. |
| --- | --- |
| Kattintson a **HTTP-figyelő** API-alkalmazás |Kattintson a **kézi indítási** (API-alkalmazás nem kötelező) |
| HTTP-figyelő beállítás "*automatikusan választ küld*" |Tartalmaznak egy **válasz** művelet vagy nem a munkafolyamat-definíciót |
| Basic vagy az OAuth-hitelesítés konfigurálása |API Management szolgáltatáson keresztül |
| HTTP-módszer konfigurálása |A **speciális beállítások megjelenítése**, válassza a HTTP-metódus |
| Adja meg relatív elérési útját |A **speciális beállítások megjelenítése**, relatív elérési út |
| A bejövő törzsében keresztül hivatkozik `@triggerOutputs().body.Content` |A hivatkozás `@triggerOutputs().body` |
| **HTTP-válasz küldése** műveletet alkalmazza a HTTP-figyelő |Kattintson a **válaszol a HTTP-kérelem** (API-alkalmazás nem kötelező) |

## <a name="get-help"></a>Segítségkérés

Látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps), ahol kérdéseket tehet fel és válaszolhat meg, valamint megtudhatja, mivel foglalkoznak az Azure Logic Apps más felhasználói.

Ha szeretné segíteni az Azure Logic Apps és összekötők fejlesztését, szavazzon az ötletekre az [Azure Logic Apps felhasználói visszajelzések oldalán](http://aka.ms/logicapps-wish), vagy küldje be saját javaslatait.

## <a name="next-steps"></a>További lépések

* [Logikaialkalmazás-definíciók készítése](./logic-apps-author-definitions.md)
* [Hibák és kivételek kezelése](./logic-apps-exception-handling.md)

[1]: ./media/logic-apps-http-endpoint/manualtrigger.png
[2]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[3]: ./media/logic-apps-http-endpoint/response.png