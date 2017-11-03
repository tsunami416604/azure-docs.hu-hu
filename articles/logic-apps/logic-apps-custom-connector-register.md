---
title: "Egyéni összekötők - Azure Logic Apps regisztrálása |} Microsoft Docs"
description: "Az Azure Logic Apps használható egyéni összekötők beállítása"
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
ms.openlocfilehash: 9e3d88abe751b37700590cc68c458f208d5868d2
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="register-custom-connectors-in-azure-logic-apps"></a>Egyéni összekötők regisztrálni kell az Azure Logic Apps

A REST API létrehozásához, hitelesítés beállítása és Postman gyűjteményét, illetve a OpenAPI csomagdefiníciós fájl beolvasása után készen áll az összekötő regisztrálására. 

## <a name="prerequisites"></a>Előfeltételek

Az egyéni összekötő regisztrálásához kell ezeket az elemeket:

* Az összekötő regisztrálása az Azure, például a nevét, a Azure-előfizetéssel, az Azure-erőforráscsoportot és a helyre, amely a használni kívánt részletei

* Az API-t leíró Postman gyűjteményét, illetve egy OpenAPI (Swagger) fájlt

  A jelen oktatóanyag esetében is használhatja a [mintafájl Azure-erőforrások Manager OpenAPI](http://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json).

* Az összekötő jelölő ikon

* Az összekötő a rövid leírása

* Az API-állomás helyét

## <a name="1-create-your-connector"></a>1. Az összekötő létrehozása

1. Az Azure portálon, a fő Azure menüben válasszon **új**. A keresési mezőbe írja be a "logic apps-összekötők" szűrőként, és nyomja le az ENTER billentyűt.

   ![Új keressen a "logic apps-összekötők"](./media/logic-apps-custom-connector-register/create-logic-apps-connector.png)

2. Az eredmények listájában válassza ki a **Logic Apps-összekötők** > **létrehozása**.

   ![A Logic Apps-összekötő létrehozása](./media/logic-apps-custom-connector-register/choose-logic-apps-connector.png)

3. Adja meg a részleteket az az összekötő regisztrálása a táblázatban ismertetett módon. Amikor elkészült, válassza ki a **rögzítés az irányítópulton** > **létrehozása**.

   ![Logic App egyéni connector részletei](./media/logic-apps-custom-connector-register/logic-apps-connector-details.png)

   | Tulajdonság | Ajánlott érték | Leírás | 
   | -------- | --------------- | ----------- | 
   | **Name (Név)** | *egyéni-összekötő-neve* | Adja meg az összekötő nevét. | 
   | **Előfizetés** | *Azure-előfizetés-neve* | Válassza ki az Azure-előfizetéshez. | 
   | **Erőforráscsoport** | *Azure-erőforrás-csoportnév* | Hozzon létre vagy válassza ki az Azure-csoportok a az Azure-erőforrások rendszerezéséhez. | 
   | **Hely** | *központi telepítés-terület* | Az összekötő telepítési régiót. | 
   |||| 

   Miután az Azure az összekötőt telepíti, az egyéni összekötő menü automatikusan megnyílik. 
   Ha nem, válassza ki az egyéni összekötő az Azure irányítópulton.

## <a name="2-define-your-connector"></a>2. Adja meg az összekötő

Mostantól megadhatja a OpenAPI fájl vagy a Postman gyűjtemény létrehozásához az összekötő, a hitelesítés, amely az összekötőt használja, a műveletek és az egyéni összekötő biztosító eseményindítók és műveletek és eseményindítók által használt paraméterek.

### <a name="2a-specify-the-openapi-file-or-postman-collection-for-your-connector"></a>2a. Adja meg a OpenAPI fájlt vagy az összekötő Postman gyűjtemény

1. Az összekötő menü, ha nincs bejelölve, válasszon **Logic Apps-összekötők**. Az eszköztáron válassza **szerkesztése**.

   ![Egyéni összekötő szerkesztése](./media/logic-apps-custom-connector-register/edit-custom-connector.png)

2. Válasszon **általános** , hogy a táblázatokban adatainak megadása létrehozása, biztonságossá tétele és a műveletek és az egyéni összekötő eseményindítók meghatározása.

   1. A **egyéni összekötők**, válassza ki a beállítást, megadhatja a OpenAPI (Swagger) fájl, mely leírja az API-t.

      ![Adja meg a OpenAPI fájlt az API-hoz](./media/logic-apps-custom-connector-register/provide-openapi-file.png)

      | Beállítás | Formátumban |Leírás | 
      | ------ | ------ | ----------- | 
      | **Egy OpenAPI-fájl feltöltése** | *OpenAPI (Swagger) - json - fájl* | Keresse meg a OpenAPI elérési útját, és válassza ki a fájlt. | 
      | **Egy OpenAPI URL-cím használata** | http://*elérési--swagger-json-fájlba* | Adja meg az URL-címet a API OpenAPI fájlt. | 
      | **Postman gyűjtemény V1 feltöltése** | *exportált-Postman-gyűjtemény-V1-fájl* | Keresse meg a helyet az exportált Postman gyűjtemény V1 formátumban. | 
      |||| 

   2. A **általános információkat**, töltse fel az összekötő ikont. 
   Általában a **leírás**, **állomás**, és **alap URL-cím** mező automatikusan feltöltődik értékkel a OpenAPI fájlból. 
   De ha még nem, adja hozzá ezeket az információkat a táblázatban ismertetett módon, és válassza a **Folytatás**. 

      ![Connector részletei](./media/logic-apps-custom-connector-register/add-connector-details.png)

      | A beállítás vagy beállítás | Formátumban | Leírás | 
      | ----------------- | ------ | ----------- | 
      | **Töltse fel az ikon** | *PNG-or-jpg-File-under-1-MB* | Az összekötő jelölő ikon <p>Szín: Lehetőleg fehér szín háttér emblémát. <p>Dimenziók: ~ 160 képpontos embléma 230 képpontos négyzet belül | 
      | **Ikon háttérszíne** | *ikon-márka-szín-hexadecimális-kód* | <p>Az ikon, amely megfelel az ikonfájl háttérszíne hátterének színét. <p>Formátum: hexadecimális. #007ee5 például a kék szín jelöli. | 
      | **Leírás** | *összekötő-leírása* | Adjon meg egy rövid leírást a Connector. | 
      | **Állomás** | *összekötő-állomás* | Adja meg a gazdagép tartomány a webes API-hoz. | 
      | **Alap URL-cím** | *összekötő-alap-URL-cím* | Az alap URL-címet adja meg a webes API. | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. A hitelesítés az összekötőt használó leírása

1. Válasszon **biztonsági** , tekintse át, vagy a hitelesítés az összekötőt használó ismertetik. Hitelesítési gondoskodik arról, hogy a felhasználók identitását megfelelően a szolgáltatás és az ügyfelek közötti áramlását.

   ![Hitelesítés típusa](./media/logic-apps-custom-connector-register/security.png)

   * Egy OpenAPI-fájl feltöltése, a regisztrációs varázsló automatikusan észleli a hitelesítési típus, amely a webes API-t használja, tölti fel automatikusan a **biztonsági** szakasz alapján varázslóban a `securityDefinitions` objektum, amely a fájl. Például itt található egy szakaszt, amely meghatározza a OAuth2.0 használatával:

     ``` json
     "securityDefinitions": {
       "AAD": {
       "type": "oauth2",
       "flow": "accessCode",
       "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
       "tokenUrl": "https://login.windows.net/common/oauth2/token",
       "scopes": {}
       }
     },
     ```

   * Ha a OpenAPI fájl nem tölthető fel, a hitelesítési típus és tulajdonságait, válassza a **szerkesztése** , ezeket az információkat is hozzáadhat. 
   
     Például ha korábban [ebben az oktatóanyagban az Azure AD-alapú hitelesítés beállítása](../logic-apps/custom-connector-azure-active-directory-authentication.md), az Azure AD-alkalmazások biztonságossá tétele a Web API és a connector létrehozta. 
     Most megadhatja az alkalmazás Azonosítóját és az ügyfél billentyű korábban mentett:
    
     | Beállítás | Ajánlott érték | Leírás | 
     | ------- | --------------- | ----------- | 
     | **Hitelesítés típusa** | OAuth 2.0 | | 
     | **Identitásszolgáltató** | Azure Active Directory | | 
     | **Ügyfél-azonosító** | *application-ID-for-connector-Azure-AD-app* | A korábban mentett az Azure AD-alkalmazás az összekötő azonosítója | 
     | **Ügyfélkulcs** | *client-key-for-connector-Azure-AD-app* | Az összekötő az Azure AD alkalmazás ügyfél kulcsa | 
     | **Bejelentkezési URL-címe** | `https://login.windows.net` | | 
     | **Forrás URL-cím** | `https://management.core.windows.net/` | Ellenőrizze, hogy a megadott URL-CÍMRE pontosan, beleértve a záró perjelet. | 
     |||| 

   * A Postman gyűjteményt, amely automatikusan létrehozza az OpenAPI fájl meg, automatikusan feltölti a hitelesítési típus *csak* támogatott hitelesítési típusok, például az OAuth 2.0-s vagy Basic használatakor.

2. Az összekötő mentéséhez a biztonsági adatokat a lap tetején megadása után válassza **összekötő frissítése**, majd válassza a **Folytatás**. 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. Tekintse át, a frissítést, vagy a műveletek és a Connector eseményindítók definiálása

1. Válasszon **Definition** áttekintheti, szerkesztése vagy új műveletek és eseményindítókat, amelyek felhasználókat adhat hozzá a munkafolyamatok megadása.

   Műveletek és eseményindítók alapulnak, amely automatikusan feltölti a OpenAPI fájl vagy a Postman gyűjtemény definiált műveletek a **Definition** lapon, és a kérés- és értékeket tartalmaznak. Ezért ha a szükséges műveletek már itt jelenik meg, megnyithatja a regisztrációs folyamat során a következő lépéssel ezen az oldalon módosítások nélkül.

   ![Összekötő meghatározása](./media/logic-apps-custom-connector-register/definition.png)

2. Nem kötelező Ha szeretné szerkeszteni a meglévő műveletek és eseményindítók, vagy újakat vehet fel, folytassa ezeket a lépéseket.

<a name="add-action-or-trigger"></a> 

#### <a name="edit-or-add-actions-for-your-connector"></a>Az összekötő műveletek hozzáadása vagy szerkesztése

1. Egy meglévő művelet szerkesztéséhez adja meg az adott művelethez. Amely nem szerepel a OpenAPI fájl vagy a Postman gyűjteményt, a művelet hozzáadása **műveletek**, válassza a **új művelet**.

2. A **általános**, adja meg, vagy ez a művelet adatainak szerkesztése:
   
   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | **Összefoglalás** | *művelet – neve* | A művelet a cím | 
   | **Leírás** | *művelet – Leírás* | Ez a művelet leírását. <p>**Tipp**: Győződjön meg arról, hogy a leírás végződő. |
   | **A művelet azonosítója** | *a művelet-azonosító* | Ez a művelet azonosítására szolgáló egyedi nevet. -És nagybetűhasználattal teve, például: "GetPullRequest" | 
   |**Látható**| **nincs**, **speciális**, **belső**, vagy **fontos** | A felhasználók számára is elérhető, és könnyebben ezt a műveletet. További információkért lásd: [OpenAPI bővítmények](../logic-apps/custom-connector-openapi-extensions.md#visibility). | 
   |||| 

3. Most adja meg a kérelmet a művelethez.
  
   1. Az a **kérelem** területen válasszon **minta importálás**. 

   2. Az a **minta importálás** lapon, és beillesztheti egy minta kérelem. 

      Minta kérelmek általában hol segítséget az API-dokumentáció elérhető a **művelet**, **URL-cím**, **fejlécek**, és **törzs**mezőket. Például tekintse meg a [szöveg Analytics API-JÁNAK dokumentációja](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

      ![Importálás mintakérelem](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > Ha az összekötő létrehozása egy Postman gyűjteményből, győződjön meg arról, hogy eltávolítja a `Content-type` műveletek és eseményindítók fejléc. A Logic Apps automatikusan hozzáadja ezt a fejlécet. Távolítsa el a megadott hitelesítési fejlécek a `Security` műveletek és eseményindítók szakasz.

      Speciális OpenAPI biztosít, lásd: [egyéni összekötők OpenAPI kiterjesztéseinek](../logic-apps/custom-connector-openapi-extensions.md).

   3. A kérelem definíciója befejezéséhez válassza ki a **importálási**.

4. Most adja meg a választ, a művelet.

   1. A **válasz**, alapértelmezett választ adhat meg. 
   Válasszon **alapértelmezett válasz hozzáadása**.

   2. Az a **minta importálás** lapon, illessze be a mintát választ, majd válassza a **importálási**.

5. Végezetül a **érvényesítési**tekintse meg és hárítsa el a művelet azonosított potenciális problémákat.

#### <a name="edit-or-add-triggers-for-your-connector"></a>Az összekötő eseményindítókat hozzáadása vagy szerkesztése

1. Egy meglévő eseményindító módosításához adja meg, hogy az eseményindító. Egy eseményindítót, amely nem szerepel a OpenAPI fájl vagy a Postman gyűjteményt, a hozzáadandó **eseményindítók**, válassza a **eseményindító**.

2. A **általános**, adja meg, és ezt az információt az eseményindító szerkesztése:

   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | **Összefoglalás** | *művelet – neve* | Az eseményindító cím | 
   | **Leírás** | *művelet – Leírás* | Ehhez az eseményindítóhoz leírása. <p>**Tipp**: Győződjön meg arról, hogy a leírás végződő. | 
   | **A művelet azonosítója** | *a művelet-azonosító* | Ehhez az eseményindítóhoz azonosító egyedi nevét. -És nagybetűhasználattal teve, például: "TriggerOnGitHubPushEvent" | 
   |**Látható**| **nincs**, **speciális**, **belső**, vagy **fontos** | A felhasználók számára is elérhető, és könnyebben ehhez az eseményindítóhoz. További információkért lásd: [OpenAPI bővítmények](../logic-apps/custom-connector-openapi-extensions.md#visibility). | 
   | **Indítási típus** | **Webhook** vagy **lekérdezési** | Az eseményindító típusa. Például egy webhook eseményindító indítási előtt megtörténjen-e egy adott esemény vár. Lekérdezési eseményindító rendszeresen ellenőrzi a megadott intervallumban és gyakorisága alapján szolgáltatásvégpont. <p>További információ a webhook és a lekérdezési eseményindító minták: [hozzon létre egyéni API-k](../logic-apps/logic-apps-create-api-app.md). | 
   |||| 

3. Most adja meg a kérelemhez, amely az eseményindítót hoz létre. 

   1. Az a **kérelem** területen válasszon **minta importálás**.

   2. Az a **minta importálás** lapon, és beillesztheti egy minta kérelem. 

      Minta kérelmek általában hol segítséget az API-dokumentáció elérhető a **művelet**, **URL-cím**, **fejlécek**, és **törzs**mezőket. Például tekintse meg a [szöveg Analytics API-JÁNAK dokumentációja](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

      ![Importálás mintakérelem](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > Ha az összekötő létrehozása egy Postman gyűjteményből, győződjön meg arról, hogy eltávolítja a `Content-type` műveletek és eseményindítók fejléc. A Logic Apps automatikusan hozzáadja ezt a fejlécet. Távolítsa el a megadott hitelesítési fejlécek a `Security` műveletek és eseményindítók szakasz.

      Speciális OpenAPI biztosít, lásd: [egyéni összekötők OpenAPI kiterjesztéseinek](../logic-apps/custom-connector-openapi-extensions.md).

   3. A kérelem definíciója befejezéséhez válassza ki a **importálási**. 

4. Most határozza meg az eseményindító válaszát. Az a **válasz** szakasz alapján az eseményindító, kövesse az alábbi lépéseket:

   **Webhook eseményindító**
   1. A **Webhook válasz**, válassza a **minta importálás**. 

   2. Az a **minta importálás** lapon, illessze be a mintát választ, majd válassza a **importálási**. Egy példa egy válasz megtekintéséhez lásd: a [GitHub API-referencia a webhook létrehozása](https://developer.github.com/v3/repos/hooks/#create-a-hook).

   3. A **eseményindító konfigurációs**, jelölje ki a paramétert a webhook létrehozása kérelem használatára. A Logic Apps feltöltése a visszahívási az eseményindító kommunikálni a szolgáltatás által használt URL-címet a paraméter értékét használja.

   **Lekérdezési eseményindító**
   1. A **válasz**, alapértelmezett választ adhat meg. 
   Válasszon **alapértelmezett válasz hozzáadása**.

   2. Az a **minta importálás** lapon, illessze be a mintát választ, majd válassza a **importálási**.

   3. A **eseményindító konfigurációs**, adja meg a következő lekérdezésparaméter, felelt meg a paraméter értékét és egy *eseményindító mutató* , amely megadja, hogy a megfelelő, a következő kérelem lekérdezési időközt.

5. Végezetül a **érvényesítési**tekintse meg és hárítsa el a az eseményindító azonosított potenciális problémákat.

#### <a name="review-reference-definitions-for-your-connector"></a>Felülvizsgálati hivatkozás definíciókat az összekötőhöz

A **hivatkozások** szakasz automatikusan tölti fel az API leírása a, és minden paraméter mezőt hivatkozó műveletek és eseményindítók is ismerteti. 

1. A **hivatkozások**, válassza ki a referencia-definíciót, amely meg szeretné tekinteni a számot.

2. A **neve**, tekintse át, vagy frissítse a referencia-definíció nevét.

3. A **érvényesítési**tekintse meg és hárítsa el a a referencia-definíciójának azonosított potenciális problémákat.

## <a name="3-finish-creating-your-connector"></a>3. Az összekötő létrehozásának befejezéséhez

Ha elkészült, válassza ki a **létrehozása** , hogy az összekötőt. Ha egy meglévő összekötő frissítése folyamatban van, válassza a **összekötő frissítése**.

Gratulálunk! Most logikai alkalmazás létrehozása, amikor is megkeresi a connector Logic Apps-tervezőben, és adja hozzá ezt az összekötőt a Logic Apps alkalmazást.

![Logic Apps Designer keresse meg az összekötő](./media/logic-apps-custom-connector-register/custom-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>Az összekötő megosztása más Logic Apps felhasználókkal

Regisztrált, de a nem hitelesített egyéni összekötők úgy működik, mint a Microsoft által felügyelt összekötők, de látható és *csak* az összekötő Szerző és azonos Azure Active Directory-bérlőt és Azure-előfizetéssel rendelkező felhasználók a logic apps a régióban, ahol az alkalmazások vannak telepítve. Bár megosztása nem kötelező, lehetséges, hogy a forgatókönyvek az összekötők megosztása más felhasználókkal helyét. 

> [!IMPORTANT]
> Ha megosztott egy összekötőt, mások is elindíthatják összekötőt függ. 
> ***Az összekötő törlése az összes kapcsolatot az összekötőt.***
 
Az összekötő kívül a határok, például külső felhasználókkal való megosztása összes Logic Apps, a folyamat és a PowerApps felhasználók, [küldje el a Microsoft-tanúsítvány-összekötő](../logic-apps/custom-connector-submit-certification.md).

## <a name="faq"></a>GYIK

**K:** vannak-e bármilyen egyéni összekötők korlátai? </br>
**V:** Igen, tekintse meg a [egyéni összekötő korlátozza Itt](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits).

## <a name="get-support"></a>Támogatás kérése

* Forduljon a támogatási fejlesztési és bevezetési, vagy a kérelem-szolgáltatások, amelyek nem érhetők el a regisztrációs varázslót a, [ condevhelp@microsoft.com ](mailto:condevhelp@microsoft.com). A Microsoft developer kérdések és problémák ehhez a fiókhoz figyeli, és továbbítja a dokumentumokat a megfelelő csapat.

* Kérje meg vagy kérdésekre, vagy más Azure Logic Apps felhasználók tevékenységeit megtekintéséhez keresse fel a [Azure Logic Apps-fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* A Logic Apps javítása érdekében, szavazhatnak, vagy küldje el a következő ötleteket a [Logic Apps felhasználói visszajelzési webhelyet](http://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Következő lépések

* [Választható lehetőség: Az összekötő tanúsítás](../logic-apps/custom-connector-submit-certification.md)
* [Egyéni összekötő – gyakori kérdések](../logic-apps/custom-connector-faq.md)