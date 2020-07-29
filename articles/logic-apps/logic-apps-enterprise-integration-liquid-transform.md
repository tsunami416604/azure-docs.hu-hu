---
title: JSON-adatátalakítás a folyékony átalakításokkal
description: Átalakítások vagy leképezések létrehozása speciális JSON-átalakításokhoz Logic Apps és folyékony sablon használatával
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 04/01/2020
ms.openlocfilehash: d2598dfe9d7972dcb764abf4a1239613a1e8417a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80879173"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Fejlett JSON-átalakítások elvégzése Liquid-sablonokkal az Azure Logic Appsben

Alapszintű JSON-átalakításokat végezhet a logikai alkalmazásokban a natív adatműveletek műveleteivel, például az **összeállítás** vagy az **elemzés JSON**használatával. A speciális JSON-transzformációk elvégzéséhez létrehozhat olyan sablonokat vagy térképeket [, amelyek](https://shopify.github.io/liquid/)a rugalmas webalkalmazások nyílt forráskódú sablonjának nyelve. A Liquid-sablon határozza meg, hogyan lehet átalakítani a JSON-kimenetet, emellett támogatja az olyan összetettebb JSON-átalakításokat, mint az iterációk, a vezérlési folyamok, a változók stb.

A logikai alkalmazásban a folyékony átalakítás elvégzése előtt meg kell határoznia a JSON-t a JSON-hozzárendeléshez egy likvid sablonnal, és az integrációs fiókban tárolnia kell a leképezést. Ez a cikk bemutatja, hogyan hozhatja létre és használhatja ezt a likvid sablont vagy térképet.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Alapszintű [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Alapvető ismeretek a [folyékony sablon nyelvéről](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Likvid sablon vagy Térkép létrehozása az integrációs fiókhoz

1. Ebben a példában hozza létre az ebben a lépésben ismertetett minta likvid sablont. A Liquid-sablonban használhat folyadék- [szűrőket](https://shopify.github.io/liquid/basics/introduction/#filters), amelyek [DotLiquid](https://github.com/dotliquid/dotliquid) és C# elnevezési konvenciókat használnak.

   > [!NOTE]
   > Győződjön meg arról, hogy a szűrő neve *mondatot* használ a sablonban. Ellenkező esetben a szűrők nem működnek. Emellett a Maps [fájl mérete korlátokat](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)is tartalmaz.

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}
   
   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
   }
   ```

1. A [Azure Portal](https://portal.azure.com)az Azure Search mezőbe írja be a kifejezést `integration accounts` , majd válassza az **integrációs fiókok**elemet.

   !["Integrációs fiókok" keresése](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. Keresse meg és válassza ki az integrációs fiókját.

   ![Integrációs fiók kiválasztása](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. Az **Áttekintés** ablaktábla **összetevők**területén válassza a **Maps**elemet.

    ![A "Maps" csempe kiválasztása](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. A **térképek** ablaktáblán válassza a **Hozzáadás** lehetőséget, és adja meg a térképen a következő adatokat:

   | Tulajdonság | Érték | Leírás | 
   |----------|-------|-------------|
   | **Name (Név)** | `JsonToJsonTemplate` | A Térkép neve, amely ebben a példában "JsonToJsonTemplate". | 
   | **Térkép típusa** | **folyékony** | A Térkép típusa A JSON-ből a JSON-átalakításhoz ki kell választania a **folyadék**elemet. | 
   | **Térkép** | `SimpleJsonToJsonTemplate.liquid` | Ebben a példában a "SimpleJsonToJsonTemplate. Liquid" nevű meglévő likvid sablon vagy leképezési fájl, amely az átalakításhoz használható. A fájl megkereséséhez használhatja a fájl-választót. A Térkép méretének korlátaival kapcsolatban lásd: [korlátok és konfiguráció](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   ||| 

   ![Likvid sablon hozzáadása](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>A folyékony művelet hozzáadása a JSON-átalakításhoz

1. A Azure Portal az alábbi lépéseket követve [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. A Logic app Designerben adja hozzá a [kérelem-triggert](../connectors/connectors-native-reqres.md#add-request) a logikai alkalmazáshoz.

1. Az trigger alatt válassza az **új lépés**lehetőséget. A keresőmezőbe írja be szűrőként a kifejezést, `liquid` majd válassza a következő műveletet: **JSON átalakítása JSON-Liquid**

   ![Likvid művelet keresése és kiválasztása](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. Nyissa meg a **Térkép** listát, és válassza ki a folyékony sablont, amely ebben a példában a "JsonToJsonTemplate".

   ![Térkép kiválasztása](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Ha a térképek lista üres, valószínűleg a logikai alkalmazás nincs összekapcsolva az integrációs fiókkal. 
   Ha a logikai alkalmazást a folyékony sablonnal vagy a leképezéssel rendelkező integrációs fiókhoz szeretné kapcsolni, kövesse az alábbi lépéseket:

   1. A logikai alkalmazás menüjében válassza a **munkafolyamat-beállítások**lehetőséget.

   1. Az **integrációs fiók kiválasztása** listából válassza ki az integrációs fiókot, majd kattintson a **Mentés**gombra.

      ![Logikai alkalmazás összekapcsolása integrációs fiókkal](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. Most adja hozzá a **Content** tulajdonságot ehhez a művelethez. Nyissa meg az **új paraméter hozzáadása** listát, és válassza a **tartalom**elemet.

   ![A "Content" tulajdonság hozzáadása művelethez](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. A **tartalom** tulajdonság értékének megadásához kattintson a **szövegmezőbe** , hogy megjelenjen a dinamikus tartalmak listája. Válassza ki a **törzs** jogkivonatát, amely a törzs tartalom kimenetét jelöli az triggerből.

   ![Válassza ki a "szövegtörzs" tokent a "Content" tulajdonság értékéhez](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   Ha elkészült, a művelet a következő példához hasonlít:

   ![Befejeződött a "JSON átalakítása JSON-re" művelet.](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

JSON-bevitel küldése a logikai alkalmazásba [Poster](https://www.getpostman.com/postman) vagy hasonló eszköz használatával. Az átalakított JSON-kimenet a logikai alkalmazásból a következő példához hasonlóan néz ki:
  
![Példa kimenetre](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>További példák a likvid műveletekre
A folyadék nem korlátozódik kizárólag a JSON-átalakításokra. Az alábbiakban a folyadékot használó egyéb átalakítási műveletek is elérhetők.

* JSON átalakítása szöveggé
  
  Itt látható a következő példában használt likvid sablon:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Íme a minta bemenetek és kimenetek:
  
   ![Példa kimeneti JSON-szövegre](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* XML átalakítása JSON-formátumba
  
  Itt látható a következő példában használt likvid sablon:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Íme a minta bemenetek és kimenetek:

   ![Példa kimeneti XML-ből JSON-ra](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* XML átalakítása szöveggé
  
  Itt látható a következő példában használt likvid sablon:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Íme a minta bemenetek és kimenetek:

   ![Példa kimeneti XML-szövegre](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>További lépések

* [További információ a Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Tudnivalók a Enterprise Integration Pack")  
* [További információ a mapsről](../logic-apps/logic-apps-enterprise-integration-maps.md "További tudnivalók a vállalati integrációs térképekről")  

