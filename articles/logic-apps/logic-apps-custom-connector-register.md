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
ms.openlocfilehash: e019b990a3f79eca0b4119e97d44eee3632e8a33
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="register-custom-connectors-in-azure-logic-apps"></a>Egyéni összekötők regisztrálni kell az Azure Logic Apps

A REST API létrehozásához, hitelesítés beállítása és Postman gyűjteményét, illetve a OpenAPI csomagdefiníciós fájl beolvasása után készen áll az összekötő regisztrálására. 

## <a name="prerequisites"></a>Előfeltételek

Az egyéni összekötő regisztrálásához kell ezeket az elemeket:

* Az összekötő regisztrálása az Azure, például a nevét, a Azure-előfizetéssel, az Azure-erőforráscsoportot és a helyre, amely a használni kívánt részletei

* Az API-t leíró Postman gyűjteményét, illetve egy OpenAPI (Swagger) fájlt

  A jelen oktatóanyag esetében is használhatja a [mintafájl Azure-erőforrások Manager OpenAPI](http://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json).

* Az összekötőt jelölő ikon

* Az összekötő a rövid leírása

* Az API-állomás helyét

## <a name="1-create-your-connector"></a>1. Saját összekötő létrehozása

1. Az Azure portálon, a fő Azure menüben válasszon **hozzon létre egy erőforrást**. Írja be a „logic apps összekötő” kifejezést szűrőként a keresőmezőbe, majd nyomja le az ENTER billentyűt.

   ![A „logic apps összekötő” kifejezés keresése az Új menüpont alatt](./media/logic-apps-custom-connector-register/create-logic-apps-connector.png)

2. Az eredmények listájában válassza a **Logic Apps-összekötő** > **Létrehozás** lehetőséget.

   ![Logic Apps-összekötő létrehozása](./media/logic-apps-custom-connector-register/choose-logic-apps-connector.png)

3. Adja meg az összekötő regisztrálásához szükséges adatokat a táblázatban leírtak szerint. Ha elkészült, válassza a **Rögzítés az irányítópulton** > **Létrehozás** lehetőséget.

   ![Egyéni Logic Apps-összekötő adatai](./media/logic-apps-custom-connector-register/logic-apps-connector-details.png)

   | Tulajdonság | Ajánlott érték | Leírás | 
   | -------- | --------------- | ----------- | 
   | **Name (Név)** | *egyéni-összekötő-neve* | Adja meg az összekötő nevét. | 
   | **Előfizetés** | *Azure-előfizetés-neve* | Válassza ki az Azure-előfizetését. | 
   | **Erőforráscsoport** | *Azure-erőforráscsoport-neve* | Hozzon létre vagy válasszon ki egy Azure-erőforráscsoportot az Azure-erőforrások rendszerezéséhez. | 
   | **Hely** | *üzembehelyezési-régió* | Válassza ki a régiót az összekötő üzembe helyezéséhez. | 
   |||| 

   Miután az Azure üzembe helyezte az összekötőt, az egyéni összekötő menüje automatikusan megnyílik. 
   Ha mégsem nyílna meg, válassza ki az egyéni összekötőt az Azure irányítópultján.

## <a name="2-define-your-connector"></a>2. Összekötő definiálása

Mostantól megadhatja a OpenAPI fájl vagy a Postman gyűjtemény létrehozásához az összekötő, a hitelesítés, amely az összekötőt használja, a műveletek és az egyéni összekötő biztosító eseményindítók és műveletek és eseményindítók által használt paraméterek.

### <a name="2a-specify-the-openapi-file-or-postman-collection-for-your-connector"></a>2a. Adja meg a OpenAPI fájlt vagy az összekötő Postman gyűjtemény

1. Ha még nem lenne kiválasztva, az összekötő menüjében válassza a **Logic Apps-összekötő** lehetőséget. Az eszköztáron válassza a **Szerkesztés** elemet.

   ![Egyéni összekötő szerkesztése](./media/logic-apps-custom-connector-register/edit-custom-connector.png)

2. Válasszon **általános** , hogy a táblázatokban adatainak megadása létrehozása, biztonságossá tétele és a műveletek és az egyéni összekötő eseményindítók meghatározása.

   1. A **egyéni összekötők**, válassza ki a beállítást, megadhatja a OpenAPI (Swagger) fájl, mely leírja az API-t.

      ![Adja meg a OpenAPI fájlt az API-hoz](./media/logic-apps-custom-connector-register/provide-openapi-file.png)

      | Beállítás | Formátum |Leírás | 
      | ------ | ------ | ----------- | 
      | **Egy OpenAPI-fájl feltöltése** | *OpenAPI (Swagger)-json-file* | Keresse meg a OpenAPI elérési útját, és válassza ki a fájlt. | 
      | **Egy OpenAPI URL-cím használata** | http://*path-to-swagger-json-file* | Adja meg az URL-címet a API OpenAPI fájlt. | 
      | **Postman gyűjtemény V1 feltöltése** | *exported-Postman-collection-V1-file* | Keresse meg a helyet az exportált Postman gyűjtemény V1 formátumban. | 
      |||| 

   2. Az **Általános információk** területen töltsön fel egy ikont az összekötőhöz. 
   Általában a **leírás**, **állomás**, és **alap URL-cím** mező automatikusan feltöltődik értékkel a OpenAPI fájlból. 
   Ha mégsem lennének kitöltve, adja meg az adatokat a táblázat szerint, majd kattintson a **Folytatás** gombra. 

      ![Összekötő adatai](./media/logic-apps-custom-connector-register/add-connector-details.png)

      | Beállítás | Formátum | Leírás | 
      | ----------------- | ------ | ----------- | 
      | **Ikon feltöltése** | *png-vagy-jpg-fájl-1-MB-alatt* | Az összekötőt jelölő ikon <p>Szín: Ideális esetben egy fehér embléma színes háttér előtt. <p>Méretek: Egy ~160 pixeles embléma egy 230 pixeles négyzetben. | 
      | **Ikon háttérszíne** | *ikon-arculati-színének-hexadecimális-kódja* | <p>Az ikon mögötti szín, amely egyezik az ikonfájl háttérszínével. <p>Formátum: hexadecimális. A #007ee5 kód például a kék színt jelenti. | 
      | **Leírás** | *összekötő-leírása* | Adja meg az összekötő rövid leírását. | 
      | **Gazdagép** | *összekötő-gazdagépe* | Adja meg a gazdagép tartomány a webes API-hoz. | 
      | **Kiindulási URL-cím** | *összekötő-kiindulási-URL-címe* | Az alap URL-címet adja meg a webes API. | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. Az összekötő által használt hitelesítés leírása

1. Most válassza a **Biztonság** lehetőséget, hogy áttekinthesse vagy megadhassa az összekötő által használt hitelesítési módot. A hitelesítés segítségével biztosíthatja, hogy a rendszer zökkenőmentesen kommunikálja a felhasználók azonosságait a szolgáltatás és az ügyfelek közt.

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
     | **ügyfél-azonosító** | *application-ID-for-connector-Azure-AD-app* | A korábban mentett az Azure AD-alkalmazás az összekötő azonosítója | 
     | Ügyfélkulcs | *client-key-for-connector-Azure-AD-app* | Az összekötő az Azure AD alkalmazás ügyfél kulcsa | 
     | **Bejelentkezési URL-címe** | `https://login.windows.net` | | 
     | **Forrás URL-cím** | `https://management.core.windows.net/` | Ellenőrizze, hogy a megadott URL-CÍMRE pontosan, beleértve a záró perjelet. | 
     |||| 

   * A Postman gyűjteményt, amely automatikusan létrehozza az OpenAPI fájl meg, automatikusan feltölti a hitelesítési típus *csak* támogatott hitelesítési típusok, például az OAuth 2.0-s vagy Basic használatakor.

2. Ha szeretné menteni az összekötőt a biztonsági adatok megadása után, válassza a lap tetején lévő **Összekötő frissítése**, majd a **Folytatás** lehetőséget. 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. Összekötő műveleteinek és eseményindítóinak áttekintése, frissítése és definiálása

1. A **Definíció** elemre kattintva áttekintheti, szerkesztheti és definiálhatja a műveleteket és eseményindítókat, amelyeket a felhasználók hozzáadhatnak munkafolyamataikhoz.

   Műveletek és eseményindítók alapulnak, amely automatikusan feltölti a OpenAPI fájl vagy a Postman gyűjtemény definiált műveletek a **Definition** lapon, és a kérés- és értékeket tartalmaznak. Így, ha a szükséges műveletek már megjelennek itt, az oldal módosítása nélkül léphet tovább a regisztrációs folyamat következő lépésére.

   ![Összekötő definíciója](./media/logic-apps-custom-connector-register/definition.png)

2. Nem kötelező Ha szeretné szerkeszteni a meglévő műveletek és eseményindítók, vagy újakat vehet fel, folytassa ezeket a lépéseket.

<a name="add-action-or-trigger"></a> 

#### <a name="edit-or-add-actions-for-your-connector"></a>Az összekötő műveletek hozzáadása vagy szerkesztése

1. Egy meglévő művelet szerkesztéséhez adja meg az adott művelethez. Amely nem szerepel a OpenAPI fájl vagy a Postman gyűjteményt, a művelet hozzáadása **műveletek**, válassza a **új művelet**.

2. A **általános**, adja meg, vagy ez a művelet adatainak szerkesztése:
   
   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | **Összefoglalás** | *művelet – neve* | A művelet a cím | 
   | **Leírás** | *művelet – Leírás* | Ez a művelet leírását. <p>**Tipp**: Győződjön meg arról, hogy a leírás végződő. |
   | **Műveletazonosító** | *operation-identifier* | Ez a művelet azonosítására szolgáló egyedi nevet. -És nagybetűhasználattal teve, például: "GetPullRequest" | 
   |**Láthatóság**| **nincs**, **speciális**, **belső**, vagy **fontos** | A felhasználók számára is elérhető, és könnyebben ezt a műveletet. További információkért lásd: [OpenAPI bővítmények](../logic-apps/custom-connector-openapi-extensions.md#visibility). | 
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
   Válassza az **Alapértelmezett válasz hozzáadása** lehetőséget.

   2. Az a **minta importálás** lapon, illessze be a mintát választ, majd válassza a **importálási**.

5. Végezetül a **érvényesítési**tekintse meg és hárítsa el a művelet azonosított potenciális problémákat.

#### <a name="edit-or-add-triggers-for-your-connector"></a>Az összekötő eseményindítókat hozzáadása vagy szerkesztése

1. Egy meglévő eseményindító módosításához adja meg, hogy az eseményindító. Egy eseményindítót, amely nem szerepel a OpenAPI fájl vagy a Postman gyűjteményt, a hozzáadandó **eseményindítók**, válassza a **eseményindító**.

2. A **általános**, adja meg, és ezt az információt az eseményindító szerkesztése:

   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | **Összefoglalás** | *művelet – neve* | Az eseményindító cím | 
   | **Leírás** | *művelet – Leírás* | Ehhez az eseményindítóhoz leírása. <p>**Tipp**: Győződjön meg arról, hogy a leírás végződő. | 
   | **Műveletazonosító** | *operation-identifier* | Ehhez az eseményindítóhoz azonosító egyedi nevét. -És nagybetűhasználattal teve, például: "TriggerOnGitHubPushEvent" | 
   |**Láthatóság**| **nincs**, **speciális**, **belső**, vagy **fontos** | A felhasználók számára is elérhető, és könnyebben ehhez az eseményindítóhoz. További információkért lásd: [OpenAPI bővítmények](../logic-apps/custom-connector-openapi-extensions.md#visibility). | 
   | **Eseményindító típusa** | **Webhook** vagy **lekérdezési** | Az eseményindító típusa. Például egy webhook eseményindító indítási előtt megtörténjen-e egy adott esemény vár. Lekérdezési eseményindító rendszeresen ellenőrzi a megadott intervallumban és gyakorisága alapján szolgáltatásvégpont. <p>További információ a webhook és a lekérdezési eseményindító minták: [hozzon létre egyéni API-k](../logic-apps/logic-apps-create-api-app.md). | 
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

   **Webhook trigger**
   1. A **Webhook válasz**, válassza a **minta importálás**. 

   2. Az a **minta importálás** lapon, illessze be a mintát választ, majd válassza a **importálási**. Egy példa egy válasz megtekintéséhez lásd: a [GitHub API-referencia a webhook létrehozása](https://developer.github.com/v3/repos/hooks/#create-a-hook).

   3. A **eseményindító konfigurációs**, jelölje ki a paramétert a webhook létrehozása kérelem használatára. A Logic Apps feltöltése a visszahívási az eseményindító kommunikálni a szolgáltatás által használt URL-címet a paraméter értékét használja.

   **Lekérdezési eseményindító**
   1. A **válasz**, alapértelmezett választ adhat meg. 
   Válassza az **Alapértelmezett válasz hozzáadása** lehetőséget.

   2. Az a **minta importálás** lapon, illessze be a mintát választ, majd válassza a **importálási**.

   3. A **eseményindító konfigurációs**, adja meg a következő lekérdezésparaméter, felelt meg a paraméter értékét és egy *eseményindító mutató* , amely megadja, hogy a megfelelő, a következő kérelem lekérdezési időközt.

5. Végezetül a **érvényesítési**tekintse meg és hárítsa el a az eseményindító azonosított potenciális problémákat.

#### <a name="review-reference-definitions-for-your-connector"></a>Felülvizsgálati hivatkozás definíciókat az összekötőhöz

A **hivatkozások** szakasz automatikusan tölti fel az API leírása a, és minden paraméter mezőt hivatkozó műveletek és eseményindítók is ismerteti. 

1. A **hivatkozások**, válassza ki a referencia-definíciót, amely meg szeretné tekinteni a számot.

2. A **neve**, tekintse át, vagy frissítse a referencia-definíció nevét.

3. A **érvényesítési**tekintse meg és hárítsa el a a referencia-definíciójának azonosított potenciális problémákat.

## <a name="3-finish-creating-your-connector"></a>3. Az összekötő létrehozásának befejezése

Ha elkészült, válassza ki a **létrehozása** , hogy az összekötőt. Ha egy meglévő összekötő frissítése folyamatban van, válassza a **összekötő frissítése**.

Gratulálunk! Amikor létrehoz egy logikai alkalmazást, mostantól megtalálja az összekötőt a Logic Apps Designerben, és hozzáadhatja a logikai alkalmazáshoz.

![Összekötő keresése a Logic Apps Designerben](./media/logic-apps-custom-connector-register/custom-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>Összekötő megosztása más Logic Apps-felhasználókkal

A regisztrált, de nem tanúsított egyéni összekötők a Microsoft által felügyelt összekötőként működnek, azonban *kizárólag* a szerző és az olyan felhasználók számára láthatók és elérhetők, akik ugyanazzal az Azure Active Directory-bérlővel és Azure-előfizetéssel rendelkeznek a logikai alkalmazásokhoz abban a régióban, ahol az alkalmazások üzembe vannak helyezve. Bár a megosztás nem kötelező, valószínűleg adódnak olyan alkalmazási helyzetek, amikor meg kívánja osztani az összekötőit más felhasználókkal. 

> [!IMPORTANT]
> Miután megoszt egy összekötőt, lehet, hogy mások egy idő után számítani fognak rá. 
> ***Az összekötő törlésével az összekötővel való összes kapcsolatot is törli.***
 
Az összekötő kívül a határok, például külső felhasználókkal való megosztása összes Logic Apps, a folyamat és a PowerApps felhasználók, [küldje el a Microsoft-tanúsítvány-összekötő](../logic-apps/custom-connector-submit-certification.md).

## <a name="faq"></a>GYIK

**K:** Rendelkeznek az egyéni összekötők valamilyen korlátozással? </br>
**A:** Igen. [Az egyéni összekötők korlátait itt tekintheti meg](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits).

## <a name="get-support"></a>Támogatás kérése

* A fejlesztéssel és a bevezetéssel kapcsolatos támogatásért, illetve a regisztrációs varázslóban nem elérhető funkciók igénylésével kapcsolatban írjon a következő címre: [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com). A Microsoft figyelemmel kíséri az erre a címre küldött, fejlesztéssel kapcsolatos kérdéseket és problémákat, és továbbítja azokat az illetékes csapatoknak.

* Látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps), ahol kérdéseket tehet fel és válaszolhat meg, valamint megtudhatja, mivel foglalkoznak az Azure Logic Apps más felhasználói.

* Ha szeretne segíteni a Logic Apps fejlesztésében, szavazzon vagy küldje el javaslatait a [Logic Apps felhasználói visszajelzések oldalon](http://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>További lépések

* [Választható lehetőség: Az összekötő tanúsítás](../logic-apps/custom-connector-submit-certification.md)
* [Egyéni összekötő – gyakori kérdések](../logic-apps/custom-connector-faq.md)