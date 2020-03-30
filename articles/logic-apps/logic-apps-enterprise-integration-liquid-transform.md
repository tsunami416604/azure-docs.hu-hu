---
title: JSON-adatok konvertálása folyadékátalakítókkal
description: A Logic Apps és a Liquid sablon használatával átalakításokat vagy térképeket hozhat létre a fejlett JSON-átalakításokhoz
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/16/2018
ms.openlocfilehash: fb9f9cfdba07ebe0bc5800def6d93950869e9727
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456642"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Speciális JSON-átalakítások végrehajtása Liquid-sablonokkal az Azure Logic Apps alkalmazásban

Alapvető JSON-átalakításokat hajthat végre a logikai alkalmazásokban olyan natív adatműveletekkel, mint a **Compose** vagy **a Parse JSON.** A speciális JSON-átalakítások végrehajtásához sablonokat vagy térképeket hozhat létre a [Liquid](https://shopify.github.io/liquid/)segítségével, amely a rugalmas webalkalmazások nyílt forráskódú sablonnyelve. A Liquid sablon határozza meg a JSON-kimenet átalakítását, és támogatja az összetettebb JSON-átalakításokat, például az iterációkat, a vezérlési folyamatokat, a változókat stb. 

Mielőtt egy Liquid-átalakítást végrehajthatna a logikai alkalmazásban, először meg kell határoznia a JSON-jSON-leképezést egy Liquid-sablonnal, és tárolnia kell ezt a leképezést az integrációs fiókban. Ez a cikk bemutatja, hogyan hozhat létre és használhatja ezt a Liquid sablont vagy térképet. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik [előfizetéssel, kezdheti egy ingyenes Azure-fiókkal.](https://azure.microsoft.com/free/) Vagy [regisztráljon egy kiosztón-fel- már-előfordulhat rakoncátor előfizetésre.](https://azure.microsoft.com/pricing/purchase-options/)

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Alapvető [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Alapvető ismeretek a [Liquid sablon nyelvéről](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Liquid sablon vagy térkép létrehozása az integrációs fiókhoz

1. Ebben a példában hozza létre az ebben a lépésben ismertetett Liquid mintasablont. A Folyékony sablonban [használhatja a Folyékony szűrőket,](https://shopify.github.io/liquid/basics/introduction/#filters)amelyek [DotLiquid](https://dotliquidmarkup.org/) és C# elnevezési konvenciókat használnak. 

   > [!NOTE]
   > Győződjön meg arról, hogy a szűrőnevek *mondatburkolatot* használnak a sablonban. Ellenkező esetben a szűrők nem fognak működni. A térképek [fájlméret-korlátokkal is rendelkeznek.](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)

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

2. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Az Azure főmenüjében válassza az **Összes erőforrás lehetőséget.** A keresőmezőben keresse meg és válassza ki az integrációs fiókot.

   ![Integrációs fiók kiválasztása](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

3.  Az **Összetevők csoportban**válassza a **Térképek**lehetőséget.

    ![Térképek kiválasztása](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

4. Válassza **a Hozzáadás** és adja meg a térképhez az alábbi adatokat:

   | Tulajdonság | Érték | Leírás | 
   |----------|-------|-------------|
   | **Név** | JsonToJsonSablon | A térkép neve, amely ebben a példában a "JsonToJsonTemplate" | 
   | **Térkép típusa** | **Folyékony** | A térkép típusa. A JSON-JSON transzformációhoz **folyadék**lehetőséget kell választania. | 
   | **Térkép** | "SimpleJsonToJsonTemplate.liquid" | Egy meglévő Liquid sablon vagy térképfájl átalakításhoz, amely ebben a példában a "SimpleJsonToJsonTemplate.liquid" ebben a példában. A fájl megkereséséhez használhatja a fájlválasztót. A térképméret-korlátokról a [Korlátok és konfiguráció témakörben](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)látható. |
   ||| 

   ![Folyékony sablon hozzáadása](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>A JSON-transzformáció likvid műveletének hozzáadása

1. Az Azure Portalon az alábbi lépésekkel [hozzon létre egy üres logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

2. A Logic App Designer, adja hozzá a [kérelem eseményindító](../connectors/connectors-native-reqres.md#add-request) a logikai alkalmazáshoz.

3. Az eseményindító alatt válassza az **Új lépés lehetőséget.** 
   A keresőmezőbe írja be szűrőként a "folyadék" szót, és válassza a következő műveletet: **A JSON átalakítása JSON - Liquid**

   ![A Folyékony művelet megkeresése és kiválasztása](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Kattintson a **Tartalom** mezőre, hogy megjelenjen a dinamikus tartalomlista, és válassza a **Törzs** token lehetőséget.
  
   ![Törzs kijelölése](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. A **Térkép** listából válassza ki a Liquid sablont, amely ebben a példában a "JsonToJsonTemplate" .

   ![Térkép kiválasztása](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Ha a térképek listája üres, valószínűleg a logikai alkalmazás nem kapcsolódik az integrációs fiókhoz. 
   Ha a logikai alkalmazást a Liquid sablonnal vagy térképpel rendelkező integrációs fiókhoz szeretné kapcsolni, kövesse az alábbi lépéseket:

   1. A logikai alkalmazás menüjében válassza a **Munkafolyamat-beállítások lehetőséget.**

   2. Az **Integrációs fiók kiválasztása** listában válassza ki az integrációs fiókot, és válassza a **Mentés gombot.**

      ![Logic app csatolása az integrációs fiókhoz](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Tegye a JSON-bevitelt a logikai alkalmazásába [a Postman-től](https://www.getpostman.com/postman) vagy egy hasonló eszköztől. A logikai alkalmazás átalakított JSON-kimenete így néz ki:
  
![Példa kimenetre](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>További folyékony akciópéldák
A folyadék nem korlátozódik csak a JSON transzformációkra. Az alábbiakban további elérhető átalakítási műveleteket, amelyek a Liquid.

* A JSON átalakítása szöveggé
  
  Itt van a folyékony sablon, amelyet ebben a példában használnak:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Itt vannak a minta bemenetek és kimenetek:
  
   ![Példa kimeneti JSON szövegre](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* XML átalakítása JSON-ra
  
  Itt van a folyékony sablon, amelyet ebben a példában használnak:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Itt vannak a minta bemenetek és kimenetek:

   ![Példa kimeneti XML-fájlra JSON-ra](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* XML átalakítása szöveggé
  
  Itt van a folyékony sablon, amelyet ebben a példában használnak:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Itt vannak a minta bemenetek és kimenetek:

   ![Példa kimeneti XML-szövegre](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>További lépések

* [További információ az Enterprise Integration Pack csomagról](../logic-apps/logic-apps-enterprise-integration-overview.md "További információ a vállalati integrációs csomagról")  
* [További információ a térképekről](../logic-apps/logic-apps-enterprise-integration-maps.md "További információ a vállalati integrációs térképekről")  

