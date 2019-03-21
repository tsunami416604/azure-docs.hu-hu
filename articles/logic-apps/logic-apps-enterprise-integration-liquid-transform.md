---
title: Konvertálja a JSON-adatok Liquid átalakító – Azure Logic Apps |} A Microsoft Docs
description: Átalakítások vagy a térképek speciális JSON-átalakítások használata a Logic Apps és Liquid-sablon létrehozása
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/16/2018
ms.openlocfilehash: 5472a8ce2670a34174d6d39f0d90faca8a7002ad
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58292886"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Folyékony sablonokkal speciális JSON csatlakoztatva átalakításokat hajthattak végre az Azure Logic Appsben

Hajthat végre alapszintű JSON-átalakításokat a logic Apps natív művelet műveletekhez például **összeállítás** vagy **JSON elemzése**. Speciális JSON csatlakoztatva átalakításokat hajthattak végre, létrehozhat sablonokat vagy a térképek [Liquid](https://shopify.github.io/liquid/), azaz rugalmas web Apps egy nyílt forráskódú sablon nyelvvel. Folyékony sablont határozza meg, hogyan alakíthat át, JSON-kimenet való, és támogatja a komplex JSON-átalakítás, például az ismétlések, control flow, változók és így tovább. 

Folyékony átalakítás a logikai alkalmazásban elvégzése előtt definiálnia kell a JSON az leképezhető Liquid sablont és tároló JSON-lel az integrációs fiókban. Ez a cikk bemutatja, hogyan hozhat létre, és ez Liquid-sablon vagy a térkép használata. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [kezdhet egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/). Másik lehetőségként [regisztrálhat egy használatalapú fizetéses előfizetésre](https://azure.microsoft.com/pricing/purchase-options/).

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Egy alapszintű [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Alapvető ismeretek szerezhetők [Liquid sablon nyelve.](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Folyékony sablon vagy az integrációs fiók térkép létrehozása

1. Ebben a példában létre ebben a lépésben ismertetett Liquid mintasablon. Használhatja a Liquid sablonban [folyadék szűri](https://shopify.github.io/liquid/basics/introduction/#filters), mely használata [DotLiquid](https://dotliquidmarkup.org/) és C# elnevezési konvenciói. 

   > [!NOTE]
   > Ellenőrizze, hogy a szűrő nevét használja *mondat kis-és* a sablonban. A szűrők nem fog működni.

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

2. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Az Azure fő menüjéből válassza **összes erőforrás**. A keresőmezőbe keresse meg és válassza ki az integrációs fiókot.

   ![Válassza ki az integrációs fiók](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

3.  A **összetevők**válassza **Maps**.

    ![Válassza ki a térképek](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

4. Válasszon **Hozzáadás** és a térkép meg az alábbi adatokat:

   | Tulajdonság | Érték | Leírás | 
   |----------|-------|-------------|
   | **Name (Név)** | JsonToJsonTemplate | A térkép, amely "JsonToJsonTemplate" Ebben a példában a neve | 
   | **Leképezés típusa** | **liquid** | A térkép típusa. JSON átalakítása JSON, ki kell jelölnie **liquid**. | 
   | **Térkép** | "SimpleJsonToJsonTemplate.liquid" | Egy meglévő Liquid-sablon vagy a térkép-fájl az átalakítást, amely ebben a példában "SimpleJsonToJsonTemplate.liquid". Ez a fájl található, a Fájlkereső használhatja. |
   ||| 

   ![Folyékony sablon hozzáadása](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>A for JSON-átalakítás Liquid művelet hozzáadása

1. Az Azure Portalon, az alábbi lépéseket követve [hozzon létre egy üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. A Logic App Designerben adja hozzá a [kérelem típusú trigger](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) a logikai alkalmazáshoz.

3. Az eseményindító területén válassza a **új lépés**. 
   A keresőmezőbe írja be a "folyadék" szűrőként, és válassza a következő műveletet: **JSON - folyadék JSON-átalakítás**

   ![Keresse meg és válassza ki a Liquid műveletet](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Kattintson a **tartalom** mezőre, hogy a dinamikus tartalmak listája jelenik meg, és válassza ki a **törzs** token.
  
   ![Válassza ki a szervezet](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Az a **térkép** listájához, válassza ki a Liquid sablon, amely "JsonToJsonTemplate" Ebben a példában.

   ![Válassza ki a térkép](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Ha a maps-lista üres, nagy valószínűséggel a logikai alkalmazás nem kapcsolódik az integrációs fiókban. 
   A logikai alkalmazás összekapcsolása az integrációs fiók, amely rendelkezik az Liquid-sablon vagy a térkép, kövesse az alábbi lépéseket:

   1. A logikai alkalmazás menüjében válassza **munkafolyamat-beállítások**.

   2. Az a **válasszon egy integrációs fiókot** listában válassza ki az integrációs fiókot, és válassza a **mentése**.

      ![Logikai alkalmazás összekapcsolása az integrációs fiók](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

A logikai alkalmazáshoz, a JSON-bevitelben közzététele [Postman](https://www.getpostman.com/postman) vagy egy hasonló eszközt. A logikai alkalmazás átalakított JSON-kimenet az alábbihoz hasonlít:
  
![Példa a kimenetre](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>További Liquid művelet példák
Folyékony nem korlátozódik, csak a JSON-átalakításokat. Az alábbiakban az egyéb elérhető átalakítási műveletek folyadék használó.

* JSON átalakítása szöveggé
  
  Itt látható az ebben a példában használt Liquid sablon:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Az alábbiakban minta bemeneteit és kimeneteit:
  
   ![Példa a kimenetre JSON szöveg](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* XML átalakítása JSON-ná
  
  Itt látható az ebben a példában használt Liquid sablon:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Az alábbiakban minta bemeneteit és kimeneteit:

   ![Példa a kimenetre XML JSON-ná](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* XML átalakítása szöveggé
  
  Itt látható az ebben a példában használt Liquid sablon:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Az alábbiakban minta bemeneteit és kimeneteit:

   ![Példa a kimenetre XML szöveg](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>További lépések

* [További információ az Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "megismerheti a vállalati integrációs csomag")  
* [További tudnivalók a maps](../logic-apps/logic-apps-enterprise-integration-maps.md "megismerheti a vállalati integrációs térképek")  

