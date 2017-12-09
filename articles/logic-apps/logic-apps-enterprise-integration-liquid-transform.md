---
title: "Alakítsa át a JSON-adatok folyékony átalakító - Azure Logic Apps |} Microsoft Docs"
description: "Hozzon létre átalakítások vagy maps speciális JSON átalakítások Logic Apps és folyékony sablon használatával."
services: logic-apps
documentationcenter: 
author: divyaswarnkar
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: LADocs; divswa
ms.openlocfilehash: cd177b1ebcb5d236ce265dc153ee6a02125a69df
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="advanced-json-transformations-using-liquid-template"></a>Speciális JSON átalakítások folyékony sablonnal
Az Azure Logic Apps alapvető JSON átalakítások natív adatbázis-művelet műveletek például Compose vagy elemzése JSON keresztül támogatja. A Logic Apps mostantól támogatja a speciális JSON átalakítások folyékony sablonok is. [Folyékony](https://shopify.github.io/liquid/) egy nyílt forráskódú sablon nyelv rugalmas webes alkalmazásokhoz.
 
Ebből a cikkből megtudhatja, hogyan az folyékony térkép vagy a sablon, amely képes támogatni az összetettebb JSON átalakítások, például az ismétlés, vezérlési forgalom, változók és így tovább. Az ezen a térképen folyékony JSON hozzárendelést JSON megadása, és tárolja a térkép integrációs fiókja előtt folyékony átalakítása a Logic Apps alkalmazást végezheti el.

## <a name="prerequisites"></a>Előfeltételek
Az alábbiakban a folyékony művelet használatának előfeltételei:

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [kezdhet egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/). Egyéb esetben [regisztrálhat használatalapú fizetéses előfizetésre](https://azure.microsoft.com/pricing/purchase-options/).

* Alapszintű ismerete [létrehozása a logic apps](../logic-apps/logic-apps-create-a-logic-app.md).

* Alapvető [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md).


## <a name="create-and-add-liquid-template-or-map-to-integration-account"></a>Hozzon létre és folyékony sablon hozzáadása vagy integrációs fiókhoz rendelve – és

1. Ebben a példában a minta folyékony sablon létrehozása. A folyékony sablon meghatározza, hogy hogyan átalakítására JSON-bevitelben itt leírt módon:

   ```
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

3. A fő Azure menüben válassza a **összes erőforrás**. 

4. A keresési mezőbe adjon meg a integrációs fiókot. Válassza ki a fiókját.

   ![Integráció fiók kiválasztása](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

5.  A fiók integrációs csempére, válassza ki a **Maps**.

   ![Válassza ki a maps](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

6. Válasszon **Hozzáadás** és a térkép meg az alábbi adatokat:
  * **Név**: a térképre, amely "JsontoJsonTemplate" Ebben a példában a nevét.
  * **Leképezéstípus**: a leképezés típus. A JSON átalakítása JSON, ki kell választania **folyékony**.
  * **Térkép**: egy létező folyékony sablon vagy a térkép fájlt használjon az átalakításhoz, amely "SimpleJsonToJsonTemplate.liquid" Ebben a példában. A fájlválasztó segítségével ez a fájl található.

    ![Folyékony sablon hozzáadása](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

    
## <a name="add-the-liquid-action-to-transform-json-in-your-logic-app"></a>A Logic Apps alkalmazást a JSON átalakítására folyékony művelet hozzáadása

1. [Logikai alkalmazás létrehozása](logic-apps-create-a-logic-app.md).

2. Adja hozzá a [kérelem eseményindító](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) a logikai alkalmazáshoz.

3. Válasszon **+ új lépés > Új művelet**. Keresse meg *folyékony* be a keresőmezőbe. Válassza ki **folyadék - átalakítási JSON JSON**.

  ![Keresési művelet-likvid](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Az a **tartalom** mezőben válassza **törzs** dinamikus tartalom listába vagy paraméterek listából, amelyik akkor jelenik meg. 
  
  ![SELECT-szervezet](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Az a **térkép** legördülő listára, válassza ki a folyékony sablont, amely JsonToJsonTemplate ebben a példában.

  ![SELECT-leképezés](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Ha a lista üres, a logikai alkalmazás valószínűleg integrációs fiókját nem kapcsolódik. A logikai alkalmazás összekapcsolása a folyékony sablonból vagy a térkép rendelkező integrációs fiók, kövesse az alábbi lépéseket:

   1. Válassza a logic app menü **munkafolyamat-beállításokat**. 
   2. Az a **válassza ki a integrációs fiókot** listában válassza ki a integrációs fiókját, és válassza a **mentése**.

     ![Hivatkozás logikai alkalmazás integrációs fiók](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)


## <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

   A logikai alkalmazáshoz a JSON-bevitelben utáni [Postman](https://www.getpostman.com/postman) vagy hasonló eszköz. A Logic Apps alkalmazást az átalakítani kívánt JSON-kimenetét ebben a példában néz ki:
  
   ![Példa a kimenetre](./media/logic-apps-enterprise-integration-liquid-transform/example-output.png)


## <a name="next-steps"></a>Következő lépések
* [További tudnivalók a vállalati integrációs csomag](../logic-apps/logic-apps-enterprise-integration-overview.md "további információ a vállalati integrációs csomag")  
* [További információ a maps](../logic-apps/logic-apps-enterprise-integration-maps.md "vállalati integrációs maps ismertetése")  

