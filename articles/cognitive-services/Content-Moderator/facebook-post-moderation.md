---
title: 'Oktatóanyag: Mérsékelt Facebook-tartalom - Tartalommoderátor'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban megtudhatja, hogyan használhatja a gépi tanuláson alapuló tartalommoderátort a Facebook-bejegyzések és -megjegyzések moderálásához.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 9f25c845302d62e3bc9e230b4a6f8f2669f4ac35
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774267"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Oktatóanyag: Moderálja a Facebook-bejegyzéseket és parancsokat az Azure-tartalommoderátorral

Ebben az oktatóanyagban megtudhatja, hogyan használhatja az Azure-tartalommoderátort a Facebook-oldalon található bejegyzések és megjegyzések moderálásához. A Facebook elküldi a látogatók által közzétett tartalmat a Tartalommoderátor szolgáltatásnak. Ezután a tartalommoderátor munkafolyamatai vagy közzéteszik a tartalmat, vagy véleményeket hoznak létre a Véleményezés eszközben, a tartalompontszámoktól és a küszöbértékektől függően. Tekintse meg a [Build 2017 bemutató videó](https://channel9.msdn.com/Events/Build/2017/T6033) egy működő példa erre a forgatókönyvre.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Content Moderator-csapat létrehozása.
> * A Content Moderator és a Facebook HTTP-eseményeit figyelő Azure Functions-függvények létrehozása.
> * Facebook-oldal csatolása a Tartalommoderátorhoz egy Facebook alkalmazás segítségével.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

Ez az ábra a forgatókönyv egyes összetevőit mutatja be:

![A Tartalommoderátor diagramja, amely az "FBListener" révén információt kap a Facebooktól, és információt küld a "CMListener"](images/tutorial-facebook-moderation.png)

> [!IMPORTANT]
> 2018-ban a Facebook szigorúbb ellenőrzési irányelveket vezetett be a Facebook-alkalmazásokra vonatkozóan. Nem fogod tudni végrehajtani az oktatóanyag lépéseit, ha az alkalmazást nem vizsgálta meg és nem hagyta jóvá a Facebook felülvizsgálati csapata.

## <a name="prerequisites"></a>Előfeltételek

- A Content Moderator előfizetői azonosítója. Kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) című részben található utasításokat, hogy előiratkozzon a Tartalommoderátor szolgáltatásra, és bekésezse a kulcsot.
- A [Facebook számla](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Felülvizsgálati csoport létrehozása

A [Tartalommoderátor kipróbálása a weben](quick-start.md) című rövid útmutatóban tájékán megtudhatja, hogyan regisztrálhat a [Tartalommoderátor-ellenőrző eszközre,](https://contentmoderator.cognitive.microsoft.com/) és hogyan hozhat létre felülvizsgálati csoportot. Vegye figyelembe a **Csapatazonosító** értékét a **Hitelesítő adatok** lapon.

## <a name="configure-image-moderation-workflow"></a>Képmoderálási munkafolyamat konfigurálása

Tekintse meg a [Definiálás, tesztelés és munkafolyamatok használata](review-tool-user-guide/workflows.md) útmutatót egyéni képmunkafolyamat létrehozásához. A tartalommoderátor ezt a munkafolyamatot fogja használni a képek automatikus ellenőrzésére a Facebookon, és néhányat elküld a Véleményezés eszköznek. Jegyezze fel a munkafolyamat **nevét.**

## <a name="configure-text-moderation-workflow"></a>Szövegmoderálási munkafolyamat konfigurálása

Ismét olvassa el a [Munkafolyamatok definiálása, tesztelése és használata útmutatót;](review-tool-user-guide/workflows.md) ezúttal hozzon létre egy egyéni szöveges munkafolyamatot. A tartalommoderátor ezt a munkafolyamatot fogja használni a szövegtartalom automatikus ellenőrzésére. Jegyezze fel a munkafolyamat **nevét.**

![Szövegalapú munkafolyamat konfigurálása](images/text-workflow-configure.PNG)

Tesztelje a munkafolyamatot a **Munkafolyamat végrehajtása** gombbal.

![Szövegalapú munkafolyamat tesztelése](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Azure Functions-függvény létrehozása

Jelentkezzen be az [Azure Portalra,](https://portal.azure.com/) és kövesse az alábbi lépéseket:

1. Hozzon létre egy Azure-függvényalkalmazást az [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) oldalon leírtak szerint.
1. Nyissa meg az újonnan létrehozott függvényalkalmazást.
1. Az alkalmazáson belül nyissa meg a **Platform funkciók** lapját, és válassza a **Konfiguráció**lehetőséget. A következő lap **Alkalmazásbeállítások** szakaszában válassza az **Új alkalmazás beállítás lehetőséget** a következő kulcs-/értékpárok hozzáadásához:
    
    | Alkalmazásbeállítás neve | érték   | 
    | -------------------- |-------------|
    | cm:TeamId   | A Content Moderator csapatazonosítójának beállítása  | 
    | cm:SubscriptionKey | A Content Moderator előfizetői azonosítója – lásd: [Hitelesítő adatok](review-tool-user-guide/credentials.md) |
    | cm:Region | A Content Moderator-régió neve szóközök nélkül. Ezt az Azure-erőforrás Áttekintés **lapjának** **Hely** mezőjében találja.|
    | cm:ImageWorkflow | A képek esetében futtatandó munkafolyamat neve |
    | cm:TextWorkflow | A szövegek esetében futtatandó munkafolyamat neve |
    | cm:CallbackEndpoint | Az útmutató későbbi részében létrehozni kívánt CMListener függvényalkalmazás URL-címe |
    | fb:VerificationToken | Egy titkos token, amelyet ön hoz létre, és amely a Facebook hírcsatorna eseményeire való feliratkozáshoz használható |
    | fb:PageAccessToken | A Facebook Graph API hozzáférési jogkivonatának nincs lejárati ideje, és lehetővé teszi, hogy a függvény az Ön nevében elrejtsen vagy töröljön bejegyzéseket. Ezt a jogkivonatot egy későbbi lépésben kapja meg. |

    Kattintson a lap tetején található **Mentés** gombra.

1. Visszalépés a **Platform szolgáltatásai** lapra. **+** **New function** A létrehozandó funkció eseményeket fog kapni a Facebooktól.

    ![Az Azure Functions ablaktábla kiemelt funkció hozzáadása gombbal.](images/new-function.png)

    1. Kattintson a csempe, hogy azt mondja **Http ravaszt**.
    1. Adja meg az **FBListener** nevet. Az **Engedélyszint** mező értéke legyen **Függvény**.
    1. Kattintson **a Létrehozás gombra.**
    1. Cserélje le a **run.csx** tartalmát az **FbListener/run.csx** tartalommal

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. Hozzon létre egy új **Http trigger** függvény nevű **CMListener**. Ez a függvény Content Moderator-események fogadását végzi. Cserélje le a **run.csx** fájl tartalmát a **CMListener/run.csx** fájl tartalmára

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>Facebook-oldal és -alkalmazás konfigurálása

1. Facebook-alkalmazás létrehozása.

    ![facebook fejlesztői oldal](images/facebook-developer-app.png)

    1. Lépjen a [Facebook fejlesztői webhelyére](https://developers.facebook.com/).
    1. Kattintson a **My Apps** (Saját alkalmazások) elemre.
    1. Adjon hozzá egy új alkalmazást.
    1. nevezd el valaminek
    1. **Webhookok kiválasztása -> Beállítás**
    1. Válassza a **Lap** lehetőséget a legördülő menüben, és válassza **az Előfizetés erre az objektumra** lehetőséget.
    1. Visszahívási URL-címként adja meg **FBListener URL**-címét, valamint a **Függvényalkalmazás beállításai** területen konfigurált **Ellenőrzési jogkivonatot**.
    1. Ha a feliratkozás megtörtént, görgessen le a hírcsatornához, és kattintson a **Feliratkozás** gombra.
    1. Kattintson a **tesztgombra** a **hírcsatorna** sor küldeni egy teszt üzenetet az FBListener Azure-függvény, majd nyomja meg a **Küldés a kiszolgálóra** gombot. Látnia kell a kérést, hogy megkapta a FBListener.

1. Facebook-oldal létrehozása.

    > [!IMPORTANT]
    > 2018-ban a Facebook szigorúbban ellenőrizte a Facebook-alkalmazásokat. Nem fogod tudni végrehajtani a 2., 3.

    1. Lépjen a [Facebookra](https://www.facebook.com/bookmarks/pages), és hozzon létre egy **új Facebook-oldalt**.
    1. Az alábbi lépéseket követve engedélyezze a Facebook alkalmazás számára az oldalhoz való hozzáférést:
        1. Nyissa meg a [Graph API Explorert](https://developers.facebook.com/tools/explorer/).
        1. Válassza **az Alkalmazás**lehetőséget.
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

A megoldás a Facebook-oldalon közzétett összes képet és szöveget elküldi a Content Moderatornek. Ezután a korábban konfigurált munkafolyamatokat a(z) invoked. A munkafolyamatokban meghatározott feltételeknek meg nem felelő tartalom átkerül az ellenőrzési eszköz en belüli véleményekre. A tartalom többi része automatikusan közzétételre kerül.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban beállíthat egy programot a termékképek elemzésére a terméktípus szerint címkézés céljából, és lehetővé teszi, hogy a felülvizsgálati csoport megalapozott döntéseket hozzon a tartalom moderálásáról. Ezután tudjon meg többet a képmoderálás részleteiről.

> [!div class="nextstepaction"]
> [Képmoderálás](./image-moderation-api.md)
