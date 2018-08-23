---
title: Az Azure Content Moderator Facebook tartalommoderálási |} A Microsoft Docs
description: Mérsékelt Facebook-oldalak a machine-learning alapú Content Moderator
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/18/2017
ms.author: sajagtap
ms.openlocfilehash: 4570b514bef33c8dfa6d220ee4cd88ad068ba4d0
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "41987632"
---
# <a name="facebook-content-moderation-with-content-moderator"></a>A Content Moderator tartalom Facebook-jóváhagyás

Ebben az oktatóanyagban azt megtudhatja, hogyan használható a machine learning-alapú Content Moderator mérsékelt Facebook-bejegyzések és megjegyzéseket.

Az oktatóanyag végigvezeti a felhasználót az alábbi lépéseket:

1. A Content Moderator a csoport létrehozása.
2. Hozzon létre az Azure Functions, a Facebook és a Content Moderator HTTP-események figyelésére.
3. Hozzon létre egy Facebook-oldalon és a alkalmazást, és csatlakoztathatja azt a Content Moderator.

Miután készen vagyunk, Facebook küld a Content Moderator a látogatói által közzétett tartalom. A match küszöbértékek alapján, a Content Moderator munkafolyamatok vagy a tartalom közzététele vagy létrehozni a felülvizsgálatok belül a felülvizsgálati eszköz. 

A következő ábrán látható a megoldás építőelemei.

![Facebook-hozzászólások moderálása](images/tutorial-facebook-moderation.png)

## <a name="create-a-content-moderator-team"></a>A Content Moderator a csoport létrehozása

Tekintse meg a [rövid](quick-start.md) lapon iratkozzon fel a Content Moderator, és hozzon létre egy csapatot.

## <a name="configure-image-moderation-workflow-threshold"></a>Kép moderálás munkafolyamat (küszöb) konfigurálása

Tekintse meg a [munkafolyamatok](review-tool-user-guide/workflows.md) lapján egy egyéni rendszerkép munkafolyamat (küszöb) konfigurálása. Vegye figyelembe a munkafolyamat **neve**.

## <a name="3-configure-text-moderation-workflow-threshold"></a>3. Szöveg moderálása munkafolyamat (küszöb) konfigurálása

Hasonló lépésekkel a [munkafolyamatok](review-tool-user-guide/workflows.md) lapon konfigurálhatja egy egyéni szöveg küszöbérték és a munkafolyamat. Vegye figyelembe a munkafolyamat **neve**.

![Szöveg munkafolyamat konfigurálása](images/text-workflow-configure.PNG)

A munkafolyamat tesztelése a "Workflow hajtható végre" gombbal.

![Szöveg munkafolyamat tesztelése](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Az Azure Functions létrehozása

Jelentkezzen be a [Azure felügyeleti portálján](https://portal.azure.com/) létrehozása az Azure Functions. Kövesse az alábbi lépéseket:

1. Hozzon létre egy Azure-Függvényalkalmazást, mint a a [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) lapot.
2. Nyissa meg az újonnan létrehozott Függvényalkalmazás.
3. Lépjen az alkalmazáson belüli **Platform szolgáltatások -> nastavení Aplikace**
4. Adja meg a következőket [Alkalmazásbeállítások](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings):

> [!NOTE]
> A **cm: régió** (szóközök) nélkül a régió nevét kell megadni.
> Ha például **westeurope**, nem Nyugat-Európa, **westcentralus**, nem USA nyugati középső Régiója, és így tovább.
>

| Alkalmazás-beállítás | Leírás   | 
| -------------------- |-------------|
| cm:TeamId   | A Content Moderator teamid értéknek  | 
| cm:SubscriptionKey | A Content Moderator előfizetési kulcs – lásd: [hitelesítő adatok](/review-tool-user-guide/credentials.md) | 
| cm:Region | A Content Moderator régió neve, a szóközök nélkül. Lásd a fenti megjegyzést. |
| cm:ImageWorkflow | Képeken futtatásához a munkafolyamat neve |
| cm:TextWorkflow | Szöveg futtatásához a munkafolyamat neve |
| cm:CallbackEndpoint | Az útmutató későbbi részében létrehozott CMListener Függvényalkalmazás URL-címe |
| FB:VerificationToken | A titkos jogkivonat, iratkozzon fel a Facebook segítségével is hírcsatorna-esemény |
| FB:PageAccessToken | A Facebook graph api hozzáférési jogkivonat nem jár le, és lehetővé teszi, hogy a funkció elrejtése vagy törölhetnek bejegyzések az Ön nevében. |

5. Hozzon létre egy új **HttpTrigger-CSharp** nevű függvény **FBListener**. Ez a függvény facebookról fogadja az eseményeket. Ez a függvény létrehozása az alábbi lépéseket:

    1. Tartsa a [Azure Functions létrehozása](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) referenciaként lapot.
    2. Kattintson a **+** hozzáadása új függvény létrehozásához.
    3. A beépített sablonok helyett válassza ki a **használatának első lépései a saját/egyéni függvény** lehetőséget.
    4. Kattintson a csempére, amely szerint **HttpTrigger-CSharp**.
    5. Adja meg a nevét **FBListener**. A **engedélyszint** mezőt állítsa **függvény**.
    6. Kattintson a **Create** (Létrehozás) gombra.
    7. Cserélje le a tartalmát a **run.csx** rendelkező tartalmát [ **FbListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FbListener/run.csx).

6. Hozzon létre egy új **HttpTrigger-CSharp** nevű függvény **CMListener**. Ez a funkció a Content Moderator fogadja az eseményeket. Kövesse az alábbi lépéseket, ez a függvény létrehozásához.

    1. Tartsa a [Azure Functions létrehozása](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) referenciaként lapot.
    2. Kattintson a **+** hozzáadása új függvény létrehozásához.
    3. A beépített sablonok helyett válassza ki a **használatának első lépései a saját/egyéni függvény** lehetőséget.
    4. Kattintson a csempére, amely szerint **HttpTrigger-CSharp**
    5. Adja meg a nevét **CMListener**. A **engedélyszint** mezőt állítsa **függvény**.
    6. Kattintson a **Create** (Létrehozás) gombra.
    7. Cserélje le a tartalmát a **run.csx** rendelkező tartalmát [ **CMListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/CmListener/run.csx).

## <a name="configure-the-facebook-page-and-app"></a>A Facebook-oldalon és az alkalmazás konfigurálása
1. Hozzon létre egy Facebook-alkalmazást.

    1. Keresse meg a [Facebook fejlesztői webhelyre](https://developers.facebook.com/)
    2. Kattintson a **saját alkalmazások**.
    3. Adjon hozzá egy új alkalmazást.
    4. Válassza ki **Webhookok Get -> használatába**
    5. Válassza ki **lap -> az előfizetés ehhez a témakörhöz**
    6. Adja meg a **FBListener URL-cím** visszahívási URL-címként, és a **Token ellenőrzése** alatt vannak konfigurálva, a **Függvényalkalmazás-beállításokat**
    7. Miután feliratkozott, görgessen le a csatorna, és válassza ki **előfizetés**.

2. Hozzon létre egy Facebook-oldalon.

    1. Navigáljon a [Facebook](https://www.facebook.com/bookmarks/pages) , és hozzon létre egy **új Facebook-oldalon**.
    2. Lehetővé teszi a Facebook-alkalmazást a lap eléréséhez az alábbi lépéseket:
        1. Keresse meg a [Graph API-tallózó](https://developers.facebook.com/tools/explorer/).
        2. Válassza ki **alkalmazás**.
        3. Válassza ki **lap hozzáférési jogkivonat**, küldjön egy **első** kérelem.
        4. Kattintson a **Lapazonosító** a válaszban.
        5. Most már fűzze hozzá a **/subscribed_apps** az URL-cím és a küldési egy **első** (válasz üres) kérelem.
        6. Küldje el a **Post** kérelmet. A válasz való kap **sikeres: true**.

3. Hozzon létre egy nem avuló Graph API hozzáférési jogkivonatot.

    1. Keresse meg a [Graph API-tallózó](https://developers.facebook.com/tools/explorer/).
    2. Válassza ki a **alkalmazás** lehetőséget.
    3. Válassza ki a **felhasználói hozzáférési jogkivonat lekérése** lehetőséget.
    4. Alatt a **engedélyek kiválasztása**válassza **manage_pages** és **publish_pages** beállítások.
    5. Ezzel a **hozzáférési jogkivonat** (rövid Életűek Token) a következő lépésben.

4. A következő néhány lépést Postman használunk.

    1. Nyissa meg **Postman** (vagy letöltés [Itt](https://www.getpostman.com/)).
    2. Importálja a két fájlt:
        1. [Postman-gyűjtemény](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Postman-környezet](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Frissítés az ezeket a környezeti változókat.
    
    | Kulcs | Érték   | 
    | -------------------- |-------------|
    | appId   | Itt a Facebook alkalmazásazonosító beszúrása  | 
    | appSecret | Itt a Facebook-alkalmazás titkos kulcs beszúrása | 
    | short_lived_token | A rövid élettartamú felhasználói hozzáférési jogkivonatot az előző lépésben létrehozott beszúrása |
    4. Most futtassa a 3 API-k szerepel a gyűjteményben: 
        1. Válassza ki **Long-Lived hozzáférési jogkivonat létrehozása** kattintson **küldése**.
        2. Válassza ki **felhasználói azonosító beszerzése a** kattintson **küldése**.
        3. Válassza ki **állandó lap hozzáférési jogkivonat lekérése** kattintson **küldése**.
    5. Másolás a **access_token** a válaszban szereplő értéket, és rendelje hozzá az Alkalmazásbeállítás **fb:PageAccessToken**.

Ennyi az egész!

A megoldás összes képek és szöveg, a Content Moderator a Facebook-oldalon közzétett küld. A munkafolyamatok, amelyek a korábban beállított kerül meghívásra. A tartalmat, amely nem felel meg a feltételeket határozza meg a munkafolyamatok belül a vizsgálóeszközt felülvizsgálatok eredményez. A tartalmat a többi közzétételre.

## <a name="license"></a>Licenc

Minden Microsoft Cognitive Services SDK-k és minták az MIT-licenccel rendelkező rendelkezik licenccel. További részletekért lásd: [licenc](https://microsoft.mit-license.org/).

## <a name="developer-code-of-conduct"></a>Fejlesztői viselkedési szabályzat

Cognitive Services, például az ügyféloldali kódtár és az minta használatával a fejlesztők a várhatóan kövesse a "fejlesztői kódot a viselkedési szabályzattal a Microsoft Cognitive Services", címen található http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>További lépések

1. [(Videó) bemutató megtekintése](https://channel9.msdn.com/Events/Build/2017/T6033) a megoldás a Microsoft Build 2017.
1. [A Facebook-minta a Githubon](https://github.com/MicrosoftContentModerator/samples-fbPageModeration)
1. https://docs.microsoft.com/azure/azure-functions/functions-create-github-webhook-triggered-function
2. http://ukimiawz.github.io/facebook/2015/08/12/webhook-facebook-subscriptions/
3. http://stackoverflow.com/questions/17197970/facebook-permanent-page-access-token
