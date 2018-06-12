---
title: Alakítsa át a JSON-adatok folyékony átalakító - Azure Logic Apps |} Microsoft Docs
description: Hozzon létre átalakítások vagy maps speciális JSON átalakítások Logic Apps és folyékony sablon használatával.
services: logic-apps
documentationcenter: ''
author: divyaswarnkar
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 42a102c9b2663380a93d56cc5f8fb82abaa83b74
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299511"
---
# <a name="perform-advanced-json-transformations-with-a-liquid-template"></a>Hajtsa végre a speciális JSON átalakítások folyékony sablonnal

Az Azure Logic Apps támogatja alapvető JSON átalakítások keresztül natív művelet műveletek, mint **Compose** vagy **elemzése JSON**. Speciális JSON átalakítások a a logic apps folyékony sablonokat használhat. 
[Folyékony](https://shopify.github.io/liquid/) egy nyílt forráskódú sablon nyelv rugalmas webes alkalmazásokhoz.
 
Ebből a cikkből megtudhatja, hogyan az folyékony térkép vagy a sablont, amely támogatja a JSON összetettebb átalakítások, például az ismétlés, vezérlési forgalom, változók és így tovább. Folyékony átalakítása a Logic Apps alkalmazást az elvégzése előtt definiálni kell a JSON formátumból leképezési JSON egy folyékony térkép és tároljuk, amelyek kapcsolódnak az integráció-fiókban.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [kezdhet egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/). Egyéb esetben [regisztrálhat használatalapú fizetéses előfizetésre](https://azure.microsoft.com/pricing/purchase-options/).

* Alapszintű ismerete [logic Apps alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Alapvető [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md)


## <a name="create-a-liquid-template-or-map-for-your-integration-account"></a>Térkép integrációs fiókjához vagy folyékony sablon létrehozása

1. Ebben a példában a minta folyékony sablon létrehozása. A folyékony sablon meghatározza, hogy hogyan átalakítására JSON-bevitelben itt leírt módon:

   ``` json
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
   > [!NOTE]
   > Ha a folyékony sablont használja, minden [szűrők](https://shopify.github.io/liquid/basics/introduction/#filters), ezek a szűrők kell kezdődnie nagybetűk. 

2. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

3. A fő Azure menüben válasszon **összes erőforrás**. 

4. A keresési mezőbe keresse meg és jelölje ki a integrációs fiókját.

   ![Integráció fiók kiválasztása](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

5.  A fiók integrációs csempére, válassza ki a **Maps**.

   ![Válassza ki a maps](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

6. Válasszon **Hozzáadás** és a térkép meg az alábbi adatokat:

   * **Név**: A nevét, amely "JsontoJsonTemplate" Ebben a példában a térképre
   * **Leképezéstípus**: a leképezés típus. A JSON átalakítása JSON, ki kell választania **folyékony**.
   * **Térkép**: egy létező folyékony sablon vagy a térkép fájlt használjon az átalakításhoz, amely "SimpleJsonToJsonTemplate.liquid" Ebben a példában. Ez a fájl található, a fájlválasztó is használhat.

   ![Folyékony sablon hozzáadása](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>A JSON-átalakítási folyékony művelet hozzáadása

1. [Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Adja hozzá a [kérelem eseményindító](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) a logikai alkalmazáshoz.

3. Válasszon **+ új lépés > Új művelet**. A keresési mezőbe, írja be a *folyékony*, és válassza ki **folyadék - átalakítási JSON JSON**.

   ![Keresse meg és jelölje ki a folyékony művelet](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Az a **tartalom** mezőben válassza **törzs** dinamikus tartalom listába vagy paraméterek listából, amelyik akkor jelenik meg.
  
   ![Válassza ki a szervezet](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Az a **térkép** listára, válassza ki a folyékony sablont, amely "JsonToJsonTemplate" Ebben a példában.

   ![Válassza ki a térkép](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Ha a lista üres, a logikai alkalmazás valószínűleg nem kapcsolódik integrációs fiókját. 
   A logikai alkalmazás összekapcsolása a folyékony sablonból vagy a térkép rendelkező integrációs fiók, kövesse az alábbi lépéseket:

   1. Válassza a logic app menü **munkafolyamat-beállításokat**.
   2. Az a **válassza ki a integrációs fiókot** listában válassza ki a integrációs fiókját, és válassza a **mentése**.

   ![Hivatkozás logikai alkalmazás integrációs fiók](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

A logikai alkalmazáshoz a JSON-bevitelben utáni [Postman](https://www.getpostman.com/postman) vagy hasonló eszköz. A Logic Apps alkalmazást az átalakítani kívánt JSON-kimenetét ebben a példában néz ki:
  
![Példa a kimenetre](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>További folyékony művelet példák
Folyadék csak JSON átalakítások nem korlátozódik. Az alábbiakban más elérhető átalakítási műveletek folyadék használó.

* Átalakítás JSON szöveg
  
  Itt az ebben a példában használt folyékony sablon:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Az alábbiakban a minta bemeneti és kimeneti:
  
   ![Példa a kimenetre JSON szöveg](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* XML átalakítása JSON-ná
  
  Itt az ebben a példában használt folyékony sablon:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Az alábbiakban a minta bemeneti és kimeneti:

   ![Példa a kimenetre JSON XML](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Átalakítás XML szöveg
  
  Itt az ebben a példában használt folyékony sablon:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Az alábbiakban a minta bemeneti és kimeneti:

   ![Egy példa a kimenetre XML szöveg](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>További lépések

* [További tudnivalók a vállalati integrációs csomag](../logic-apps/logic-apps-enterprise-integration-overview.md "további információ a vállalati integrációs csomag")  
* [További információ a maps](../logic-apps/logic-apps-enterprise-integration-maps.md "vállalati integrációs maps ismertetése")  

