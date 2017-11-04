---
title: "Egyéni API-k és összekötők ismertetik a Postman - Azure Logic Apps |} Microsoft Docs"
description: "Postman gyűjtemények leíró, csoport, és rendszerezheti egyéni összekötők és API-k létrehozása"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 072d544e5d29c4abb3d69651e53cfb33d5e0c9e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="describe-custom-apis-and-custom-connectors-with-postman"></a>Egyéni API-k és egyéni összekötők ismertetik a Postman

Hogy fejlesztése [egyéni API-k](../logic-apps/logic-apps-create-api-app.md) és [egyéni összekötők](../logic-apps/custom-connector-overview.md) gyorsabb és egyszerűbb, létrehozhat [Postman](https://www.getpostman.com/) OpenAPI dokumentumok, leíró helyett gyűjtemények a API-k és összekötők. Postman gyűjtemények súgó rendezheti és csoportosíthatja a kapcsolódó API-kérelmek. Használhatja például Postman Azure Logic Apps, a Microsoft Flow vagy a Microsoft PowerApps összekötők létrehozásakor. 

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy [Postman gyűjtemény](https://www.getpostman.com/docs/postman/collections/creating_collections) használatával a [észlelése nyelvi API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) a [kognitív Azure-szolgáltatások Szövegelemzések](https://azure.microsoft.com/services/cognitive-services/text-analytics/) a példaként. Ez az API a nyelv, a céggel kapcsolatos véleményeket és a szöveg, adja meg az API a legfontosabb kifejezések azonosítja.

## <a name="prerequisites"></a>Előfeltételek

* Ha most ismerkedik a Postman, [a Postman alkalmazástelepítési](https://www.getpostman.com/apps).

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, Kezdésként használhatja az egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/). Ellenkező esetben regisztráljon egy [használatalapú fizetés előfizetés](https://azure.microsoft.com/pricing/purchase-options/).

* Ha rendelkezik Azure-előfizetéssel, Regisztráljon a szöveg Analytics API-k elvégzésével [1. feladat Itt](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-signup.md). 

## <a name="create-a-postman-collection"></a>Postman gyűjtemény létrehozása

Létrehozhat egy gyűjteményt, mielőtt egy HTTP-kérelem az API-végpont létrehozása 

### <a name="create-an-http-request-for-your-api"></a>Az API-HTTP-kérelem létrehozása

1. Nyissa meg a Postman alkalmazást, így hozhat létre egy [HTTP-kérelem](https://www.getpostman.com/docs/postman/sending_api_requests/requests) a API-végponthoz. További információkért lásd: a Postman [dokumentáció kérelmek](https://www.getpostman.com/docs/postman/sending_api_requests/requests).

   1. Az a **jelentéskészítő** lapon válassza ki a HTTP-metódus, adja meg a kérelem URL-címet a API-végpontot, és válassza ki a hitelesítési protokoll, ha van ilyen. 
   Ha elkészült, válassza ki a **paraméterei**.

      Ebben az oktatóanyagban ezeket a beállításokat is használhatja az ebben a példában:

      ![Kérelem létrehozása: "HTTP-metódus", "URL kérelmet", "Engedélyezés"](./media/custom-connector-api-postman-collection/01-create-api-http-request.png)

      | Paraméter | Ajánlott érték | 
      | --------- | --------------- | 
      | **HTTP-metódus** | POST | 
      | **Kérelem URL-címe** | `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages` | | 
      | **Engedélyezés** | "Nincs hitelesítés" | | 
      ||| 

   2. Most kívánja használni, mint a kérelem URL-címében lekérdezés vagy az elérési út paraméterek kulcs-érték pár is megadhatja. Postman ezeket az elemeket együtt egyesíti a lekérdezési karakterlánc.
   Amikor elkészült, válassza ki a **fejlécek**.

      ![Továbbra is kérelem: "-paraméterek](./media/custom-connector-api-postman-collection/02-create-api-http-request-params.png)

      | Paraméter | Ajánlott érték | 
      | --------- | --------------- | 
      | **Paraméterei** | **Kulcs**: "numberOfLanguagesToDetect" </br>**Érték**: "1" | 
      ||| 

   3. Adja meg a kérelem fejlécében kulcs-érték párokat. 
   A fejléc neveként adja meg a kívánt karakterláncot. A gyakori HTTP-fejléceket a legördülő listából kiválaszthatja. Amikor elkészült, válassza ki a **törzs**. 
   
      ![Továbbra is kérelem: "Fejléc"](./media/custom-connector-api-postman-collection/03-create-api-http-request-header.png)

      | Paraméter | Érték | 
      | --------- | ----- | 
      | **Fejlécek** | **Kulcs**: "Ocp-Apim-előfizetés-kulcsot" </br>**Érték**: *saját API-előfizetés-kulcs*, amely a kognitív szolgáltatások fiókjában található <p>**Kulcs**: "Content-Type" </br> **Érték**: "application/json" | 
      ||| 

   4. Adja meg a tartalmát szeretné küldeni a kérés törzsében. 
   Ellenőrizze, hogy működik-e a kérelem által vissza választ kap, válassza a **küldése**. 
   
      ![Továbbra is kérelem: "Törzs"](./media/custom-connector-api-postman-collection/04-create-api-http-request-body.png)

      | Paraméter | Ajánlott érték | 
      | --------- | --------------- |    
      | **Törzs** | ```{"documents": [{ "id": "1", "text": "Hello World"}]}``` | 
      ||| 

      A válasz mező tartalmazza az API-t, beleértve az eredmény teljes válaszát, vagy, ha bármilyen hiba történt.

      ![GET kérés-válasz](./media/custom-connector-api-postman-collection/05-create-api-http-request-response.png)

2. Miután már be van jelölve, hogy a kérelem works Postman gyűjtemény mentse a kérelmet. 

   1. Válassza a **Mentés** elemet. 
      
      ![Válassza ki a "Mentés"](./media/custom-connector-api-postman-collection/06a-save-request.png)
 
   2. A **mentése kérelem**, adjon meg egy **kérés neve** és szükség esetén egy **leírás kérelem**. 

      > [!NOTE]
      > Az egyéni összekötő később használja ezeket az értékeket. Igen győződjön meg arról, hogy megadta-e ugyanazokat az értékeket, amelyek később használják az egyéni API-művelet összefoglaló és leírását.

   3. Válasszon **+ létrehozás gyűjtemény** , és adjon meg egy gyűjtemény nevét. 
   Válassza ki, majd kattintson a pipa jelre, egy gyűjtemény mappát hoz létre, amely **mentése *-Postman-gyűjtemény-név***.

      ![Kérelem mentése](./media/custom-connector-api-postman-collection/06b-save-request.png)

### <a name="save-the-request-response"></a>Mentse a kérés-válasz

A kérés mentése után a válasz is mentheti. Ily módon a válasz jelenik meg példaként a kérést később betölteni.

1. Fent a válasz ablakban válassza ki a **mentése válasz**. 

   ![Válasz mentése](./media/custom-connector-api-postman-collection/07-create-api-http-request-save-response.png)

   Egyéni összekötők támogatja a kérés csak egy választ. 
   Ha több válaszok kérelmenként menti, csak az elsőt használja.

2. Adjon meg egy nevet a példától, és válassza a **mentése példa**.

3. Folytatják a Postman gyűjtemény minden további kérelmeit és válaszait.

### <a name="export-your-postman-collection"></a>Postman gyűjtemény exportálása

1. Amikor elkészült, a gyűjtemény egy JSON-fájlba exportálása.

   ![Gyűjtemény exportálása](./media/custom-connector-api-postman-collection/08-export-http-request.png)

2. Válassza ki a **gyűjtemény v1** formátumban exportálja, és keresse meg a helyet, ahol a JSON-fájlt menteni szeretné.

   > [!NOTE]
   > Jelenleg csak a V1 egyéni összekötők működik.

   ![Válassza ki az exportálási formátumát: "Gyűjtemény v1"](./media/custom-connector-api-postman-collection/09-export-format.png)
   
Most már használhatja a Postman gyűjteményhez egyéni API-k és összekötők létrehozásához. Miután exportálta a gyűjteményt, a JSON-fájl importálhatja a Logic Apps, a folyamat vagy a powerapps segítségével.

> [!IMPORTANT]
> Ha egyéni összekötő létrehozása egy Postman gyűjteményből, győződjön meg arról, hogy távolítsa el a `Content-type` műveletek és eseményindítók fejléc. A célként megadott szolgáltatás, például folyamata, automatikusan hozzáadja ezt a fejlécet. Távolítsa el a hitelesítési fejléceket a `securityDefintions` szakaszának, műveletek és eseményindítók.

## <a name="next-steps"></a>Következő lépések

* [A Logic Apps: Egyéni összekötők regisztrálása](../logic-apps/logic-apps-custom-connector-register.md)
* [Folyamat: Az összekötő regisztrálása](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [PowerApps: Az összekötő regisztrálása](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)
* [Egyéni összekötő – gyakori kérdések](../logic-apps/custom-connector-faq.md)
