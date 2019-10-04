---
title: JSON-adatátalakítások átalakítása folyékony átalakításokkal – Azure Logic Apps | Microsoft Docs
description: Átalakítások vagy leképezések létrehozása speciális JSON-átalakításokhoz Logic Apps és folyékony sablon használatával
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/16/2018
ms.openlocfilehash: 203c57a2755a3287566a774e2878a87b847337b9
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900662"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Speciális JSON-átalakítások elvégzése folyékony sablonokkal Azure Logic Apps

Alapszintű JSON-átalakításokat végezhet a logikai alkalmazásokban a natív adatműveletek műveleteivel, például az **összeállítás** vagy az **elemzés JSON**használatával. A speciális JSON-transzformációk elvégzéséhez létrehozhat olyan sablonokat vagy térképeket [, amelyek](https://shopify.github.io/liquid/)a rugalmas webalkalmazások nyílt forráskódú sablonjának nyelve. A folyékony sablon meghatározza, hogyan alakíthatja át a JSON-kimenetet, és támogatja az összetettebb JSON-átalakításokat, például az ismétléseket, a vezérlési folyamatokat és a változókat. 

A logikai alkalmazásban a folyékony átalakítás elvégzése előtt meg kell határoznia a JSON-t a JSON-hozzárendeléshez egy likvid sablonnal, és az integrációs fiókban tárolnia kell a leképezést. Ez a cikk bemutatja, hogyan hozhatja létre és használhatja ezt a likvid sablont vagy térképet. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [kezdhet egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/). Vagy regisztráljon az utólagos elszámolású [előfizetésre](https://azure.microsoft.com/pricing/purchase-options/).

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Alapszintű [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Alapvető ismeretek a [folyékony sablon nyelvéről.](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Likvid sablon vagy Térkép létrehozása az integrációs fiókhoz

1. Ebben a példában hozza létre az ebben a lépésben ismertetett minta likvid sablont. A Liquid-sablonban használhat folyadék- [szűrőket](https://shopify.github.io/liquid/basics/introduction/#filters), amelyek [DotLiquid](https://dotliquidmarkup.org/) és C# elnevezési konvenciókat használnak. 

   > [!NOTE]
   > Győződjön meg arról, hogy a szűrő neve *mondatot* használ a sablonban. Ellenkező esetben a szűrők nem működnek.

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

2. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Az Azure fő menüjében válassza az **összes erőforrás**lehetőséget. A keresőmezőbe keresse meg és válassza ki az integrációs fiókját.

   ![Integrációs fiók kiválasztása](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

3.  Az **összetevők**területen válassza a **Maps**elemet.

    ![Térképek kiválasztása](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

4. Válassza a **Hozzáadás** lehetőséget, és adja meg a térképen a következő adatokat:

   | Tulajdonság | Érték | Leírás | 
   |----------|-------|-------------|
   | **Name** | JsonToJsonTemplate | A Térkép neve, amely ebben a példában "JsonToJsonTemplate". | 
   | **Térkép típusa** | **folyékony** | A Térkép típusa A JSON-ből a JSON-átalakításhoz ki kell választania a **folyadék**elemet. | 
   | **Térkép** | "SimpleJsonToJsonTemplate.liquid" | Ebben a példában a "SimpleJsonToJsonTemplate. Liquid" nevű meglévő likvid sablon vagy leképezési fájl, amely az átalakításhoz használható. A fájl megkereséséhez használhatja a fájl-választót. |
   ||| 

   ![Likvid sablon hozzáadása](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>A folyékony művelet hozzáadása a JSON-átalakításhoz

1. A Azure Portal az alábbi lépéseket követve [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. A Logic app Designerben adja hozzá a [kérelem-triggert](../connectors/connectors-native-reqres.md#add-request) a logikai alkalmazáshoz.

3. Az trigger alatt válassza az **új lépés**lehetőséget. 
   A keresőmezőbe írja be szűrőként a "Liquid" kifejezést, majd válassza a következő műveletet: **JSON átalakítása JSON-folyadékba**

   ![Likvid művelet keresése és kiválasztása](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Kattintson a szövegmezőbe, hogy **megjelenjen a dinamikus** tartalmak listája, és válassza ki a **törzs** tokenjét.
  
   ![Törzs kijelölése](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. A **Térkép** listából válassza ki a folyékony sablont, amely ebben a példában a "JsonToJsonTemplate".

   ![Térkép kiválasztása](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Ha a térképek lista üres, valószínűleg a logikai alkalmazás nincs összekapcsolva az integrációs fiókkal. 
   Ha a logikai alkalmazást a folyékony sablonnal vagy a leképezéssel rendelkező integrációs fiókhoz szeretné kapcsolni, kövesse az alábbi lépéseket:

   1. A logikai alkalmazás menüjében válassza a **munkafolyamat-beállítások**lehetőséget.

   2. Az **integrációs fiók kiválasztása** listából válassza ki az integrációs fiókot, majd válassza a **Mentés**lehetőséget.

      ![Logikai alkalmazás összekapcsolása integrációs fiókkal](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

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

* XML átalakítása JSON-ná
  
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

* [További információ a Enterprise Integration Pack] Tudnivalók (../logic-apps/logic-apps-enterprise-integration-overview.md "a Enterprise Integration Pack")  
* [További információ a mapsről] (../logic-apps/logic-apps-enterprise-integration-maps.md "További tudnivalók a vállalati integrációs térképekről")  

