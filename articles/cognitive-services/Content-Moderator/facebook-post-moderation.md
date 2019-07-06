---
title: 'Oktatóanyag: Facebook-tartalom - Content Moderator mérsékelt'
titlesuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban megtudhatja, hogyan használható a machine learning-alapú Content Moderator mérsékelt Facebook-bejegyzések és megjegyzéseket.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: dd06330e82850cc44bc0f4d36ba7caf596ace939
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603516"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Oktatóanyag: Mérsékelt Facebook-bejegyzések és -parancsok használata az Azure Content Moderator

Ebben az oktatóanyagban, megtudhatja, hogyan segít a bejegyzések és a egy Facebook-oldalon megjegyzéseket közepes szintű Azure Content Moderator használatával. A webhelyek látogatóit, a Content Moderator szolgáltatás által közzétett tartalmat Facebook küldi el. A Content Moderator munkafolyamatok majd lesz vagy a tartalom közzététele, vagy hozzon létre értékelések a vizsgálóeszközt, attól függően, a tartalom pontszámokat és a küszöbértékek belül. Tekintse meg a [Build 2017 – bemutató videó](https://channel9.msdn.com/Events/Build/2017/T6033) ebben a forgatókönyvben egy működő példát.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Content Moderator-csapat létrehozása.
> * A Content Moderator és a Facebook HTTP-eseményeit figyelő Azure Functions-függvények létrehozása.
> * Egy Facebook-oldalhoz összekapcsolása a Content Moderator Facebook-alkalmazás használatával.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Az alábbi ábra az ebben a forgatókönyvben minden egyes összetevő:

![A Content Moderator információt kap a Facebookon keresztül "FBListener" és "CMListener" keresztül adatokat küld ábrája](images/tutorial-facebook-moderation.png)

> [!IMPORTANT]
> A 2018 Facebook megvalósítva, Facebook-alkalmazások több szigorú kérelmek. Nem lehet tudni, ez az oktatóanyag lépéseit végrehajtania, ha az alkalmazás nem lett felülvizsgálva és a Facebook csapatától jóvá.

## <a name="prerequisites"></a>Előfeltételek

- A Content Moderator előfizetői azonosítója. Kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Content Moderator szolgáltatás és a kulcs beszerzése.
- A [Facebook-fiókban](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Tekintse át a csoport létrehozása

Tekintse meg a [próbálja ki a Content Moderator, a weben](quick-start.md) regisztrálni létrehozásával kapcsolatos útmutatást a rövid útmutatóban a [Content Moderator, tekintse át az eszköz](https://contentmoderator.cognitive.microsoft.com/) , és tekintse át a csoport létrehozása. Jegyezze fel a **Csapatazonosító** érték a **hitelesítő adatok** lapot.

## <a name="configure-image-moderation-workflow"></a>Kép moderálás munkafolyamat konfigurálása

Tekintse meg a [definiálása, tesztelési és használja a munkafolyamatok](review-tool-user-guide/workflows.md) útmutató egy egyéni rendszerkép munkafolyamat létrehozásához. A Content Moderator a munkafolyamat használatával automatikusan Facebook-rendszerképek keresése és néhány, a felülvizsgálati eszköz küldése. Jegyezze fel a munkafolyamat **neve**.

## <a name="configure-text-moderation-workflow"></a>Szöveg moderálása munkafolyamat konfigurálása

Ismét tekintse meg a [definiálása, tesztelési és használja a munkafolyamatok](review-tool-user-guide/workflows.md) útmutató; ez alkalommal, hozzon létre egy egyéni szövegtulajdonságot a munkafolyamat. A Content Moderator automatikus keresése a szöveges tartalom fogja használni ezt a munkafolyamatot. Jegyezze fel a munkafolyamat **neve**.

![Szövegalapú munkafolyamat konfigurálása](images/text-workflow-configure.PNG)

A munkafolyamat segítségével tesztelheti a **munkafolyamat végrehajtása** gombra.

![Szövegalapú munkafolyamat tesztelése](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Azure Functions-függvény létrehozása

Jelentkezzen be a [az Azure portal](https://portal.azure.com/) , és kövesse az alábbi lépéseket:

1. Hozzon létre egy Azure-függvényalkalmazást az [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) oldalon leírtak szerint.
1. Nyissa meg az újonnan létrehozott Függvényalkalmazás.
1. Az alkalmazásban nyissa meg a **platformfunkciók** lapot, és válasszon **konfigurációs**. Az a **Alkalmazásbeállítások** szakaszban a következő lap, válassza ki **új Alkalmazásbeállítás** az alábbi kulcs-érték párok hozzáadásához:
    
    | Alkalmazásbeállítás neve | value   | 
    | -------------------- |-------------|
    | cm:TeamId   | A Content Moderator csapatazonosítójának beállítása  | 
    | cm:SubscriptionKey | A Content Moderator előfizetői azonosítója – lásd: [Hitelesítő adatok](review-tool-user-guide/credentials.md) |
    | cm:Region | A Content Moderator-régió neve szóközök nélkül. |
    | cm:ImageWorkflow | A képek esetében futtatandó munkafolyamat neve |
    | cm:TextWorkflow | A szövegek esetében futtatandó munkafolyamat neve |
    | cm:CallbackEndpoint | Az útmutató későbbi részében létrehozott CMListener Függvényalkalmazás URL-címe |
    | fb:VerificationToken | Titkos jogkivonat, amely hoz létre, a Facebook-hírcsatorna-esemény előfizetéséhez használt |
    | fb:PageAccessToken | A Facebook Graph API hozzáférési jogkivonatának nincs lejárati ideje, és lehetővé teszi, hogy a függvény az Ön nevében elrejtsen vagy töröljön bejegyzéseket. Megjelenik a jogkivonatot egy későbbi lépésben. |

    Kattintson a **mentése** gombra a lap tetején.

1. Lépjen vissza a **platformfunkciók** fülre. Használja a **+** gombra a bal oldali panelen viszi, megjelenik a **új függvény** ablaktáblán. A létrehozni kívánt függvény kapja Facebook szolgáltatásból.

    ![Az Azure Functions, a függvény hozzáadása gomb kiemelésével panel.](images/new-function.png)

    1. Kattintson a csempére, amely szerint **Http-eseményindító**.
    1. Adja meg az **FBListener** nevet. Az **Engedélyszint** mező értéke legyen **Függvény**.
    1. Kattintson a **Create** (Létrehozás) gombra.
    1. Cserélje le a tartalmát a **run.csx** rendelkező tartalmát **FbListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. Hozzon létre egy új **Http-eseményindító** nevű függvény **CMListener**. Ez a függvény Content Moderator-események fogadását végzi. Cserélje le a tartalmát a **run.csx** rendelkező tartalmát **CMListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>Facebook-oldal és -alkalmazás konfigurálása

1. Facebook-alkalmazás létrehozása.

    ![Facebook-fejlesztői oldalon](images/facebook-developer-app.png)

    1. Lépjen a [Facebook fejlesztői webhelyére](https://developers.facebook.com/).
    1. Kattintson a **My Apps** (Saját alkalmazások) elemre.
    1. Adjon hozzá egy új alkalmazást.
    1. nevet ad meg
    1. Válassza ki **Webhookok beállítása -> mentése**
    1. Válassza ki **oldal** a legördülő menüben, és válasszon **fizessen elő az objektum**
    1. Visszahívási URL-címként adja meg **FBListener URL**-címét, valamint a **Függvényalkalmazás beállításai** területen konfigurált **Ellenőrzési jogkivonatot**.
    1. Ha a feliratkozás megtörtént, görgessen le a hírcsatornához, és kattintson a **Feliratkozás** gombra.
    1. Kattintson a a **tesztelése** gomb a **hírcsatorna** sor egy tesztüzenetet küldéséhez az FBListener Azure-függvény, majd nyomja le az **küldeni a saját kiszolgáló** gombra. A kérelem fogadását a FBListener kell megjelennie.

1. Facebook-oldal létrehozása.

    > [!IMPORTANT]
    > A 2018 Facebook megvalósítva, Facebook-alkalmazások több szigorú kérelmek. Nem hajtható végre a szakaszokat, 2, 3, 4, ha az alkalmazás nem lett felülvizsgálva és a Facebook csapatától jóvá.

    1. Lépjen a [Facebookra](https://www.facebook.com/bookmarks/pages), és hozzon létre egy **új Facebook-oldalt**.
    1. Az alábbi lépéseket követve engedélyezze a Facebook alkalmazás számára az oldalhoz való hozzáférést:
        1. Nyissa meg a [Graph API Explorert](https://developers.facebook.com/tools/explorer/).
        1. Válassza az **Application** (Alkalmazás) elemet.
        1. Válassza a **Page Access Token** (Oldalhozzáférési jogkivonat) elemet, majd küldjön egy **GET** kérést.
        1. A válaszban kattintson a **Page ID** (Oldal azonosítója) elemre.
        1. Fűzze hozzá a **/subscribed_apps** sztringet az URL-címhez, majd küldjön egy **GET** (üres válasz) kérést.
        1. Küldjön el egy **POST** kérést. A következő választ fogja kapni: **success: true**.

3. Lejárati idővel nem rendelkező Graph API hozzáférési jogkivonat létrehozása.

    1. Nyissa meg a [Graph API Explorert](https://developers.facebook.com/tools/explorer/).
    2. Válassza az **Application** (Alkalmazás) elemet.
    3. Válassza a **Get User Access Token** (Felhasználói hozzáférési jogkivonat beszerzése) lehetőséget.
    4. A **Select Permissions** (Engedélyek kiválasztása) területen válassza a **manage_pages** és a **publish_pages** elemeket.
    5. A **hozzáférési jogkivonatot** (Rövid élettartamú jogkivonat) a következő lépésben fogjuk használni.

4. A következő lépésekhez a Postmant használjuk.

    1. Nyissa meg a **Postmant** (vagy szerezze be [innen](https://www.getpostman.com/)).
    2. Importálja a következő két fájlt:
        1. [Postman Collection](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json) (Postman-gyűjtemény)
        2. [Postman Environment](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json) (Postman-környezet)       
    3. Frissítse a következő környezeti változókat:
    
        | Kulcs | Érték   | 
        | -------------------- |-------------|
        | appId   | Ide szúrja be a Facebook-alkalmazás azonosítóját  | 
        | appSecret | Ide szúrja be a Facebook-alkalmazás titkos kódját | 
        | short_lived_token | Ide szúrja be az előző lépésben létrehozott rövid élettartamú felhasználói hozzáférési jogkivonatot |
    4. Futtassa a gyűjteményben lévő három API-t: 
        1. Válassza a **Generate Long-Lived Access Token** (Hosszú élettartamú hozzáférési jogkivonat létrehozása) elemet, majd kattintson a **Send** (Küldés) gombra.
        2. Válassza a **Get User ID** (Felhasználói azonosító lekérése) lehetőséget, majd kattintson a **Send** (Küldés) gombra.
        3. Válassza a **Get Permanent Page Access Token** (Állandó oldalhozzáférési jogkivonat lekérése) elemet, majd kattintson a **Send** (Küldés) gombra.
    5. Másolja ki a válaszból az **access_token** értékét, és rendelje hozzá az **fb:PageAccessToken** alkalmazásbeállításhoz.

A megoldás a Facebook-oldalon közzétett összes képet és szöveget elküldi a Content Moderatornek. Ezután a munkafolyamatok, amelyek a korábban beállított kerül meghívásra. A tartalom, amely nem felel meg a feltételeknek, a munkafolyamatok meghatározott belül a vizsgálóeszközt felülvizsgálatok át. A tartalmat a többi automatikusan közzétételre.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban beállította egy program Termékképek céljából megtekintett terméktípus, és lehetővé teszi a tartalom-jóváhagyás megalapozott döntéseket csapattal elemzéséhez. Ezután további információ a képmoderálás részleteit.

> [!div class="nextstepaction"]
> [Képmoderálás](./image-moderation-api.md)
