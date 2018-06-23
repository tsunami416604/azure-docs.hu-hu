---
title: Tartalom moderálás Facebook rendelkező Azure tartalom moderátor |} Microsoft Docs
description: Mérsékelt Facebook-oldalak és gépi tanuláson alapuló tartalom moderátor
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/18/2017
ms.author: sajagtap
ms.openlocfilehash: 316c7212c30e9fe1151cdf5ceabf875439ad4c65
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347195"
---
# <a name="facebook-content-moderation-with-content-moderator"></a>Facebook tartalom moderálás a tartalom moderátor

Az oktatóanyag azt útmutató segítségével machine learning-alapú tartalom moderátor mérsékelt Facebook-bejegyzéseket és megjegyzéseit.

Az oktatóanyag végigvezeti a felhasználót az alábbi lépéseket:

1. Tartalom moderátor csoportot létrehozni.
2. Hozzon létre az Azure Functions, amely a tartalom moderátor és Facebook HTTP-események figyelésére.
3. Hozzon létre egy Facebook-oldal és alkalmazást, és csatlakoztassa tartalom moderátor.

Miután igazolnia befejezése után Facebook el fogja küldeni a tartalom a tartalom moderátor látogatóinak által közzétett. Egyezés küszöbértékek alapján, a tartalom moderátor munkafolyamatok el a tartalom közzététele vagy hozza létre a felülvizsgálati eszköz belül értékelést. 

Az alábbi ábrán a megoldás építőelemeit.

![Facebook-hozzászólások moderálása](images/tutorial-facebook-moderation.png)

## <a name="create-a-content-moderator-team"></a>Tartalom moderátor-csoport létrehozása

Tekintse meg a [gyors üzembe helyezés](quick-start.md) tartalom moderátor regisztrálhat és a csoport létrehozása lapon.

## <a name="configure-image-moderation-workflow-threshold"></a>Kép moderálás munkafolyamat (küszöb) konfigurálása

Tekintse meg a [munkafolyamatok](review-tool-user-guide/workflows.md) lapon konfigurálhatja az egyéni lemezképet munkafolyamat (küszöb). Vegye figyelembe a munkafolyamat **neve**.

## <a name="3-configure-text-moderation-workflow-threshold"></a>3. Szöveg moderálás munkafolyamat (küszöb) konfigurálása

Hasonló eljárással a [munkafolyamatok](review-tool-user-guide/workflows.md) lapon konfigurálhatja az egyéni szöveg küszöbérték és a munkafolyamat. Vegye figyelembe a munkafolyamat **neve**.

![Szöveg munkafolyamat konfigurálása](images/text-workflow-configure.PNG)

A munkafolyamat tesztelése a "Workflow hajtható végre" gomb használatával.

![Szöveg munkafolyamat tesztelése](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Az Azure Functions létrehozása

Jelentkezzen be a [Azure felügyeleti portálon](https://portal.azure.com/) az Azure Functions létrehozásához. Kövesse az alábbi lépéseket:

1. Hozzon létre egy Azure-függvény alkalmazást, mint a a [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) lap.
2. Nyissa meg az újonnan létrehozott függvény alkalmazást.
3. Lépjen az alkalmazáson belüli **Platform szolgáltatások -> Alkalmazásbeállítások**
4. Adja meg a következőket [Alkalmazásbeállítások](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings):

> [!NOTE]
> A **cm: régió** a régió (nélkül szóközt) névnek kell lennie.
> Például **westeurope**, nem Nyugat-Európában, **westcentralus**, nem nyugati középső Régiójában, és így tovább.
>

| Alkalmazás beállítása | Leírás   | 
| -------------------- |-------------|
| cm:TeamId   | A tartalom moderátor csoportazonosító  | 
| cm:SubscriptionKey | A tartalom moderátor előfizetés kulcs – lásd: [hitelesítő adatok](/review-tool-user-guide/credentials.md) | 
| cm:Region | A tartalom moderátor régió nevét, a szóközök nélkül. Lásd az előző megjegyzést. |
| cm:ImageWorkflow | Lemezképek futtatásához a munkafolyamat nevét |
| cm:TextWorkflow | Szöveg futtatásához a munkafolyamat nevét |
| cm:CallbackEndpoint | Ez az útmutató későbbi szakaszában létrehozott CMListener függvény alkalmazás URL-címe |
| FB:VerificationToken | A titkos, a Facebook előfizetni is használt token adatcsatorna-események |
| FB:PageAccessToken | A Facebook graph api hozzáférési jogkivonat nem jár le, és lehetővé teszi, hogy a funkció elrejtése vagy törlése bejegyzéseket az Ön nevében. |

5. Hozzon létre egy új **HttpTrigger-c Sharp** függvény **FBListener**. Ez a funkció eseményeket fogad Facebook-on. Ez a függvény létrehozása a következő lépések végrehajtásával:

    1. Tartsa a [Azure Functions létrehozása](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) lapon nyissa meg a hivatkozást.
    2. Kattintson a **+** hozzáadása az új függvény létrehozásához.
    3. A beépített sablonok helyett válassza a **a saját/egyéni függvény az első lépések** lehetőséget.
    4. Kattintson a csempére, amely szerint a **HttpTrigger-c Sharp**.
    5. Adja meg a nevét **FBListener**. A **jogosultsági szint** mezőt állítsa **függvény**.
    6. Kattintson a **Create** (Létrehozás) gombra.
    7. Cserélje le a tartalmát a **run.csx** a tartalmával [ **FbListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FbListener/run.csx).

6. Hozzon létre egy új **HttpTrigger-c Sharp** függvény **CMListener**. Ez a funkció eseményeket fogad Facebook-on. Kövesse az alábbi lépéseket, a függvény létrehozásához.

    1. Tartsa a [Azure Functions létrehozása](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) lapon nyissa meg a hivatkozást.
    2. Kattintson a **+** hozzáadása az új függvény létrehozásához.
    3. A beépített sablonok helyett válassza a **a saját/egyéni függvény az első lépések** lehetőséget.
    4. Kattintson a csempére, amely szerint a **HttpTrigger-c Sharp**
    5. Adja meg a nevét **CMListener**. A **jogosultsági szint** mezőt állítsa **függvény**.
    6. Kattintson a **Create** (Létrehozás) gombra.
    7. Cserélje le a tartalmát a **run.csx** a tartalmával [ **CMListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/CmListener/run.csx).

## <a name="configure-the-facebook-page-and-app"></a>A Facebook-oldal és az alkalmazás konfigurálása
1. Facebook-alkalmazás létrehozása.

    1. Keresse meg a [Facebook fejlesztői hely](https://developers.facebook.com/)
    2. Kattintson a **alkalmazásaimat**.
    3. Adjon hozzá egy új alkalmazást.
    4. Válassza ki **Webhookok Get -> indítása**
    5. Válassza ki **lapján az előfizetés ehhez a témakörhöz ->**
    6. Adja meg a **FBListener URL-cím** az visszahívási URL-címet és a **Token ellenőrzése** alatt vannak konfigurálva, a **függvény Alkalmazásbeállítások**
    7. Miután előfizetett, görgessen le a hírcsatorna, és válassza **előfizetés**.

2. Facebook-oldal létrehozása.

    1. Navigáljon a [Facebook](https://www.facebook.com/bookmarks/pages) , és hozzon létre egy **új Facebook-oldal**.
    2. Engedélyezi a Facebook-alkalmazást a lap eléréséhez a következő lépések végrehajtásával:
        1. Keresse meg a [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
        2. Válassza ki **alkalmazás**.
        3. Válassza ki **lap Access Token**, küldjön egy **beolvasása** kérelmet.
        4. Kattintson a **Lapazonosító** a válaszban.
        5. Most hozzáfűzése a **/subscribed_apps** az URL-cím és a küldés egy **beolvasása** kérelem (válasz üres).
        6. Küldje el a **Post** kérelmet. A válasz kap **sikeres: true**.

3. Hozzon létre egy nem járó Graph API-hozzáférési tokent.

    1. Keresse meg a [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
    2. Válassza ki a **alkalmazás** lehetőséget.
    3. Válassza ki a **felhasználói hozzáférési jogkivonat beszerzése** lehetőséget.
    4. Az a **Select engedélyeket**, jelölje be **manage_pages** és **publish_pages** beállítások.
    5. Ezzel a **hozzáférési jogkivonat** (rövid élt Token) a következő lépésben.

4. Postman a következő néhány lépést a használjuk.

    1. Nyissa meg **Postman** (vagy töltse le innen [Itt](https://www.getpostman.com/)).
    2. Importálja a két fájlt:
        1. [Postman gyűjtemény](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Postman környezet](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Ezek a környezeti változók frissítése:
    
    | Kulcs | Érték   | 
    | -------------------- |-------------|
    | appId   | Itt a Facebook alkalmazásazonosító beszúrása  | 
    | appSecret | Itt a Facebook-alkalmazást titkos kulcs beszúrása | 
    | short_lived_token | A rövid élettartamú felhasználói jogkivonat az előző lépésben létrehozott beszúrása |
    4. Most futtassa a 3 API-k, szerepel a gyűjteményben: 
        1. Válassza ki **Long-Lived hozzáférési jogkivonat készítése** kattintson **küldése**.
        2. Válassza ki **felhasználói azonosító beszerzése** kattintson **küldése**.
        3. Válassza ki **állandó lap hozzáférési jogkivonat beszerzése** kattintson **küldése**.
    5. Másolás a **access_token** a válaszban szereplő értékét, és rendelje hozzá az Alkalmazásbeállítás **fb:PageAccessToken**.

Ennyi az egész!

A megoldás összes képek és szövegek tartalom moderátornak a Facebook-oldal oldalkérelmekkel küld. A munkafolyamatok, amelyek a korábban konfigurált hívják. A tartalom, amely nem felel meg a feltételeket határozza meg a munkafolyamatok tekintse át a segédprogrammal értékelést eredményez. A tartalmat a többi közzétételre.

## <a name="license"></a>Licenc

Az összes Microsoft kognitív Services SDK-k és minták a MIT licenccel rendelkező licencét. További részletekért lásd: [licenc](https://microsoft.mit-license.org/).

## <a name="developer-code-of-conduct"></a>Fejlesztői viselkedési szabályzat

A fejlesztők kognitív szolgáltatásokkal, beleértve az ügyféloldali kódtár & minta, hajtsa végre a "fejlesztői kódot, végezze el a Microsoft kognitív szolgáltatások", a következő címen található várhatóan http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>További lépések

1. [Tekintse meg a bemutató (videó)](https://channel9.msdn.com/Events/Build/2017/T6033) a megoldás a Microsoft Build 2017.
1. [A Facebook-mintát a Githubon](https://github.com/MicrosoftContentModerator/samples-fbPageModeration)
1. https://docs.microsoft.com/azure/azure-functions/functions-create-github-webhook-triggered-function
2. http://ukimiawz.github.io/facebook/2015/08/12/webhook-facebook-subscriptions/
3. http://stackoverflow.com/questions/17197970/facebook-permanent-page-access-token
