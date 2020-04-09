---
title: JSON-adatok konvertálása folyadékátalakítókkal
description: A Logic Apps és a Liquid sablon használatával átalakításokat vagy térképeket hozhat létre a fejlett JSON-átalakításokhoz
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 04/01/2020
ms.openlocfilehash: d2598dfe9d7972dcb764abf4a1239613a1e8417a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879173"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Speciális JSON-átalakítások végrehajtása Liquid-sablonokkal az Azure Logic Apps alkalmazásban

Alapvető JSON-átalakításokat hajthat végre a logikai alkalmazásokban olyan natív adatműveletekkel, mint a **Compose** vagy **a Parse JSON.** A speciális JSON-átalakítások végrehajtásához sablonokat vagy térképeket hozhat létre a [Liquid](https://shopify.github.io/liquid/)segítségével, amely a rugalmas webalkalmazások nyílt forráskódú sablonnyelve. A Liquid sablon határozza meg a JSON-kimenet átalakítását, és támogatja az összetettebb JSON-átalakításokat, például az iterációkat, a vezérlési folyamatokat, a változókat stb.

Mielőtt egy Liquid-átalakítást végrehajthatna a logikai alkalmazásban, először meg kell határoznia a JSON-jSON-leképezést egy Liquid-sablonnal, és tárolnia kell ezt a leképezést az integrációs fiókban. Ez a cikk bemutatja, hogyan hozhat létre és használhatja ezt a Liquid sablont vagy térképet.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Alapvető [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Alapvető ismeretek a [Liquid sablon nyelvéről](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Liquid sablon vagy térkép létrehozása az integrációs fiókhoz

1. Ebben a példában hozza létre az ebben a lépésben ismertetett Liquid mintasablont. A Folyékony sablonban [használhatja a Folyékony szűrőket,](https://shopify.github.io/liquid/basics/introduction/#filters)amelyek [DotLiquid](https://github.com/dotliquid/dotliquid) és C# elnevezési konvenciókat használnak.

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

1. Az [Azure Portalon](https://portal.azure.com)az Azure keresőmezőjéből írja be a t, `integration accounts`és válassza az **Integrációs fiókok lehetőséget.**

   !["Integrációs fiókok" keresése](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. Keresse meg és válassza ki az integrációs fiókot.

   ![Integrációs fiók kiválasztása](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. Az **Áttekintés** ablaktábla **Összetevők csoportban**válassza a **Térképek**lehetőséget.

    ![Válassza a "Térképek" csempe](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. A **Térképek** ablaktáblán válassza a **Hozzáadás** és a térkép hez való részletek megadását:

   | Tulajdonság | Érték | Leírás | 
   |----------|-------|-------------|
   | **Név** | `JsonToJsonTemplate` | A térkép neve, amely ebben a példában a "JsonToJsonTemplate" | 
   | **Térkép típusa** | **Folyékony** | A térkép típusa. A JSON-JSON transzformációhoz **folyadék**lehetőséget kell választania. | 
   | **Térkép** | `SimpleJsonToJsonTemplate.liquid` | Egy meglévő Liquid sablon vagy térképfájl átalakításhoz, amely ebben a példában a "SimpleJsonToJsonTemplate.liquid" ebben a példában. A fájl megkereséséhez használhatja a fájlválasztót. A térképméret-korlátokról a [Korlátok és konfiguráció témakörben](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)látható. |
   ||| 

   ![Folyékony sablon hozzáadása](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>A JSON-transzformáció likvid műveletének hozzáadása

1. Az Azure Portalon az alábbi lépésekkel [hozzon létre egy üres logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

1. A Logic App Designer, adja hozzá a [kérelem eseményindító](../connectors/connectors-native-reqres.md#add-request) a logikai alkalmazáshoz.

1. Az eseményindító alatt válassza az **Új lépés lehetőséget.** A keresőmezőbe írja `liquid` be szűrőként, és válassza a következő műveletet: **A JSON átalakítása JSON - Liquid**

   ![A Folyékony művelet megkeresése és kiválasztása](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. Nyissa meg a **Térkép** listát, és válassza ki a Liquid sablont, amely ebben a példában a "JsonToJsonTemplate" .

   ![Térkép kiválasztása](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Ha a térképek listája üres, valószínűleg a logikai alkalmazás nem kapcsolódik az integrációs fiókhoz. 
   Ha a logikai alkalmazást a Liquid sablonnal vagy térképpel rendelkező integrációs fiókhoz szeretné kapcsolni, kövesse az alábbi lépéseket:

   1. A logikai alkalmazás menüjében válassza a **Munkafolyamat-beállítások lehetőséget.**

   1. Az **Integrációs fiók kiválasztása** listában válassza ki az integrációs fiókot, és válassza a **Mentés gombot.**

      ![Logic app csatolása az integrációs fiókhoz](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. Most adja hozzá a **Content** tulajdonságot ehhez a művelethez. Nyissa **meg** az Új paraméter hozzáadása listát, és válassza a **Tartalom**lehetőséget.

   !["Tartalom" tulajdonság hozzáadása a művelethez](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. A **Content** tulajdonság értékének beállításához kattintson a **Tartalom** mezőben, hogy a dinamikus tartalomlista megjelenjen. Válassza ki a **Törzs** jogkivonatot, amely az eseményindító törzstartalom-kimenetét jelöli.

   ![Válassza ki a "Törzs" jogkivonatot a "Content" tulajdonságértékhez.](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   Ha elkészült, a művelet a következő példához hasonlít:

   ![Kész "Átalakítás JSON jSON" akció](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

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

