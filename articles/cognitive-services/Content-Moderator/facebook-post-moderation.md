---
title: 'Oktatóanyag: Facebook tartalom-jóváhagyás – az Azure Content Moderator'
titlesuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban megtudhatja, hogyan használható a machine learning-alapú Content Moderator mérsékelt Facebook-bejegyzések és megjegyzéseket.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: b43f3645a2fb9cc6ede4b27b4412b9e70e2b26f8
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55213837"
---
# <a name="tutorial-facebook-content-moderation-with-content-moderator"></a>Oktatóanyag: A Content Moderator tartalom Facebook-jóváhagyás

Ebben az oktatóanyagban megtudhatja, hogyan használható a machine learning-alapú Content Moderator mérsékelt Facebook-bejegyzések és megjegyzéseket.

Az oktatóanyag a következő lépéseken vezeti végig:

1. Content Moderator-csapat létrehozása.
2. A Content Moderator és a Facebook HTTP-eseményeit figyelő Azure Functions-függvények létrehozása.
3. Facebook-oldal és -alkalmazás létrehozása, majd annak csatlakoztatása a Content Moderatorhoz.

Miután ezzel elkészültünk, a Facebook elküldi a Content Moderator számára a látogatók által közzétett tartalmakat. A Content Moderator-munkafolyamat az egyezési küszöbérték alapján közzéteszi a tartalmat vagy felülvizsgálatot végez a felülvizsgálati eszközben. 

Az alábbi ábrán a megoldás építőelemei láthatók.

![Facebook-hozzászólások moderálása](images/tutorial-facebook-moderation.png)

## <a name="create-a-content-moderator-team"></a>Content Moderator-csapat létrehozása

Tekintse meg a [próbálja ki a Content Moderator, a weben](quick-start.md) gyors útmutató a Content Moderator regisztráljon és hozzon létre egy csapatot.

## <a name="configure-image-moderation-workflow-threshold"></a>Képmoderálási munkafolyamat konfigurálása (küszöbérték)

A [munkafolyamatokat](review-tool-user-guide/workflows.md) ismertető oldalon szereplő lépéseket követve konfiguráljon egy egyéni képmoderálási munkafolyamatot (küszöbérték). Jegyezze fel a munkafolyamat **nevét**.

## <a name="3-configure-text-moderation-workflow-threshold"></a>3. Szövegmoderálási munkafolyamat konfigurálása (küszöbérték)

A [munkafolyamatokat](review-tool-user-guide/workflows.md) ismertető oldalon szereplő lépéseket követve konfiguráljon egy egyéni szövegmoderálási küszöbértéket és munkafolyamatot. Jegyezze fel a munkafolyamat **nevét**.

![Szövegalapú munkafolyamat konfigurálása](images/text-workflow-configure.PNG)

A munkafolyamat teszteléséhez használja a „Munkafolyamat végrehajtása” gombot.

![Szövegalapú munkafolyamat tesztelése](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Azure Functions-függvény létrehozása

Azure Functions-függvény létrehozásához jelentkezzen be az [Azure felügyeleti portáljára](https://portal.azure.com/). Kövesse az alábbi lépéseket:

1. Hozzon létre egy Azure-függvényalkalmazást az [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) oldalon leírtak szerint.
2. Nyissa meg az újonnan létrehozott függvényalkalmazást.
3. Az alkalmazásban lépjen a **Platformfunkciók -> Alkalmazásbeállítások** területre.
4. Adja meg az alábbi [alkalmazásbeállításokat](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings):

> [!NOTE]
> A **cm: Régió** (szóközök) nélkül a régió nevét kell megadni.
> Például a West Europe helyett a **westeurope**, a West Central US helyett pedig a **westcentralus** értéket adja meg.
>

| Alkalmazásbeállítás | Leírás   | 
| -------------------- |-------------|
| cm:TeamId   | A Content Moderator csapatazonosítójának beállítása  | 
| cm:SubscriptionKey | A Content Moderator előfizetői azonosítója – lásd: [Hitelesítő adatok](review-tool-user-guide/credentials.md) | 
| cm:Region | A Content Moderator-régió neve szóközök nélkül. Lásd a fenti megjegyzést. |
| cm:ImageWorkflow | A képek esetében futtatandó munkafolyamat neve |
| cm:TextWorkflow | A szövegek esetében futtatandó munkafolyamat neve |
| cm:CallbackEndpoint | A jelen útmutató későbbi részében létrehozandó CMListener függvényalkalmazás URL-címe |
| fb:VerificationToken | Titkos jogkivonat, amely a Facebook-hírcsatorna eseményeire való feliratkozásra is szolgál |
| fb:PageAccessToken | A Facebook Graph API hozzáférési jogkivonatának nincs lejárati ideje, és lehetővé teszi, hogy a függvény az Ön nevében elrejtsen vagy töröljön bejegyzéseket. |

5. Hozzon létre egy új **HttpTrigger-CSharp** függvényt **FBListener** néven. Ez a függvény Facebook-események fogadását végzi. A függvény létrehozásához kövesse az alábbi lépéseket:

    1. Hagyja megnyitva a [függvényalkalmazás létrehozását](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) bemutató oldalt referenciaként.
    2. Új függvény létrehozásához kattintson a **+** hozzáadás elemre.
    3. A beépített sablonok helyett válassza a **Saját vagy egyéni függvény létrehozásának első lépései** lehetőséget.
    4. Kattintson a **HttpTrigger-CSharp** feliratú csempére.
    5. Adja meg az **FBListener** nevet. Az **Engedélyszint** mező értéke legyen **Függvény**.
    6. Kattintson a **Create** (Létrehozás) gombra.
    7. Cserélje le a **run.csx** fájl tartalmát az [**FbListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FbListener/run.csx) fájl tartalmára.

6. Hozzon létre egy új **HttpTrigger-CSharp** függvényt **CMListener** néven. Ez a függvény Content Moderator-események fogadását végzi. Kövesse az alábbi lépéseket a függvény létrehozásához.

    1. Hagyja megnyitva a [függvényalkalmazás létrehozását](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) bemutató oldalt referenciaként.
    2. Új függvény létrehozásához kattintson a **+** hozzáadás elemre.
    3. A beépített sablonok helyett válassza a **Saját vagy egyéni függvény létrehozásának első lépései** lehetőséget.
    4. Kattintson a **HttpTrigger-CSharp** feliratú csempére.
    5. Adja meg a **CMListener** nevet. Az **Engedélyszint** mező értéke legyen **Függvény**.
    6. Kattintson a **Create** (Létrehozás) gombra.
    7. Cserélje le a **run.csx** fájl tartalmát az [**CMListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/CmListener/run.csx) fájl tartalmára.

## <a name="configure-the-facebook-page-and-app"></a>Facebook-oldal és -alkalmazás konfigurálása
1. Facebook-alkalmazás létrehozása.

    1. Lépjen a [Facebook fejlesztői webhelyére](https://developers.facebook.com/).
    2. Kattintson a **My Apps** (Saját alkalmazások) elemre.
    3. Adjon hozzá egy új alkalmazást.
    4. Válassza a **Webhooks -> Get Started** (Webhookok -> Első lépések) lehetőséget.
    5. Válassza a **Page -> Subscribe to this topic**  (Oldal -> Feliratkozás erre a témakörre) lehetőséget.
    6. Visszahívási URL-címként adja meg **FBListener URL**-címét, valamint a **Függvényalkalmazás beállításai** területen konfigurált **Ellenőrzési jogkivonatot**.
    7. Ha a feliratkozás megtörtént, görgessen le a hírcsatornához, és kattintson a **Feliratkozás** gombra.

2. Facebook-oldal létrehozása.

    1. Lépjen a [Facebookra](https://www.facebook.com/bookmarks/pages), és hozzon létre egy **új Facebook-oldalt**.
    2. Az alábbi lépéseket követve engedélyezze a Facebook alkalmazás számára az oldalhoz való hozzáférést:
        1. Nyissa meg a [Graph API Explorert](https://developers.facebook.com/tools/explorer/).
        2. Válassza az **Application** (Alkalmazás) elemet.
        3. Válassza a **Page Access Token** (Oldalhozzáférési jogkivonat) elemet, majd küldjön egy **GET** kérést.
        4. A válaszban kattintson a **Page ID** (Oldal azonosítója) elemre.
        5. Fűzze hozzá a **/subscribed_apps** sztringet az URL-címhez, majd küldjön egy **GET** (üres válasz) kérést.
        6. Küldjön el egy **POST** kérést. A következő választ fogja kapni: **success: true**.

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
    
    | Kulcs | Value   | 
    | -------------------- |-------------|
    | appId   | Ide szúrja be a Facebook-alkalmazás azonosítóját  | 
    | appSecret | Ide szúrja be a Facebook-alkalmazás titkos kódját | 
    | short_lived_token | Ide szúrja be az előző lépésben létrehozott rövid élettartamú felhasználói hozzáférési jogkivonatot |
    4. Futtassa a gyűjteményben lévő három API-t: 
        1. Válassza a **Generate Long-Lived Access Token** (Hosszú élettartamú hozzáférési jogkivonat létrehozása) elemet, majd kattintson a **Send** (Küldés) gombra.
        2. Válassza a **Get User ID** (Felhasználói azonosító lekérése) lehetőséget, majd kattintson a **Send** (Küldés) gombra.
        3. Válassza a **Get Permanent Page Access Token** (Állandó oldalhozzáférési jogkivonat lekérése) elemet, majd kattintson a **Send** (Küldés) gombra.
    5. Másolja ki a válaszból az **access_token** értékét, és rendelje hozzá az **fb:PageAccessToken** alkalmazásbeállításhoz.

Ennyi az egész!

A megoldás a Facebook-oldalon közzétett összes képet és szöveget elküldi a Content Moderatornek. A rendszer meghívja a korábban konfigurált munkafolyamatokat. A munkafolyamatokban meghatározott feltételeknek meg nem felelő tartalmak felülvizsgálatokat eredményeznek a felülvizsgálati eszközben. A tartalom többi részét a rendszer közzéteszi.

## <a name="license"></a>Licenc

Az összes Microsoft Cognitive Services SDK és -minta licencelése MIT-licenccel történik. További információ: [LICENC](https://microsoft.mit-license.org/).

## <a name="next-steps"></a>További lépések

1. [Tekintsen meg egy bemutatót (videó)](https://channel9.msdn.com/Events/Build/2017/T6033) a megoldásról, amely a Microsoft Build 2017 alkalmával volt látható.
1. [A Facebook-minta a Githubon](https://github.com/MicrosoftContentModerator/samples-fbPageModeration)
1. https://docs.microsoft.com/azure/azure-functions/functions-create-github-webhook-triggered-function
2. http://ukimiawz.github.io/facebook/2015/08/12/webhook-facebook-subscriptions/
3. http://stackoverflow.com/questions/17197970/facebook-permanent-page-access-token
