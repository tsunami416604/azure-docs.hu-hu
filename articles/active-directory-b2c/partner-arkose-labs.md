---
title: Arkóz Labs Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan integrálhatja Azure AD B2C hitelesítését a Arkóz Labs szolgáltatással, hogy védelmet nyújtson a robot-támadások, a fiókok átvételi támadásai és a csalárd fiókok megnyitása között.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a4d8174cd0bfdb2297099b403fb836210c5529ac
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170224"
---
# <a name="tutorial-for-configuring-arkose-labs-with-azure-active-directory-b2c"></a>Oktatóanyag a Arkóz Labs konfigurálásához a Azure Active Directory B2C

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja Azure AD B2C hitelesítését a Arkóz Labs szolgáltatással. A Arkóz Labs segítséget nyújt a szervezeteknek a robotok elleni támadásokkal, a fiókok átvételi támadásokkal és a csalárd fiókok megnyitásával kapcsolatban.  

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* [Egy Azure ad B2C bérlő](tutorial-create-tenant.md) , amely az Azure-előfizetéshez van csatolva.

## <a name="scenario-description"></a>Forgatókönyv leírása

Az alábbi ábra azt ismerteti, hogyan integrálható a Arkóz Labs Azure AD B2C.

![Arkóz Labs architektúra diagram](media/partner-arkose-labs/arkose-architecture-diagram.png)

| Lépés  | Leírás |
|---|---|
|1     | A felhasználó egy korábban létrehozott fiókkal jelentkezik be. Amikor a felhasználó kiválasztja a Submit (küldés) lehetőséget, megjelenik egy Arkóz Labs-kényszerítési kérdés. Miután a felhasználó befejezte a problémát, a rendszer elküldi az állapotot a Arkóz Labs számára a jogkivonat létrehozásához.        |
|2     |  A Arkóz Labs visszaküldi a jogkivonatot a Azure AD B2Cnak.       |
|3     |  A bejelentkezési űrlap elküldése előtt a rendszer elküldi a jogkivonatot a Arkóz Labs számára ellenőrzés céljából.       |
|4     |  A Arkóz visszaküldi a probléma sikerességi vagy meghibásodási eredményét.       |
|5     |  Ha a kihívás sikeresen befejeződött, a rendszer beküld egy bejelentkezési űrlapot Azure AD B2Cba, és Azure AD B2C befejezi a hitelesítést.       |
|   |   |

## <a name="onboard-with-arkose-labs"></a>A Arkóz Labs üzembe helyezése

1. Először vegye fel a kapcsolatot a [Arkóz Labs szolgáltatással](https://www.arkoselabs.com/book-a-demo/) , és hozzon létre egy fiókot.

2. A fiók létrehozása után navigáljon a következőhöz: https://dashboard.arkoselabs.com/login .

3. Az irányítópulton navigáljon a hely beállításaihoz, és keresse meg a nyilvános kulcsot és a titkos kulcsot. Erre az információra később szükség lesz a Azure AD B2C konfigurálásához.

## <a name="integrate-with-azure-ad-b2c"></a>Integrálás az Azure AD B2C szolgáltatással

### <a name="part-1--create-blob-storage-to-store-the-custom-html"></a>1. rész – blob Storage létrehozása az egyéni HTML-fájl tárolásához

A Storage-fiók létrehozásához kövesse az alábbi lépéseket:  

1. Jelentkezzen be a Azure Portalba.

2. Ügyeljen arra, hogy az Azure-előfizetését tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki az előfizetést tartalmazó könyvtárat. Ez a könyvtár nem azonos az Azure B2C-bérlőt tartalmazó címtárral.

3. A Azure Portal bal felső sarkában válassza a **minden szolgáltatás** elemet, majd keresse meg és válassza ki a  **Storage-fiókokat**.

4. Válassza a  **Hozzáadás** lehetőséget.

5. Az **erőforráscsoport**területen válassza az **új létrehozása**elemet, adja meg az új erőforráscsoport nevét, majd kattintson **az OK gombra**.

6. Adja meg a tárfiók nevét. A választott névnek egyedinek kell lennie az Azure-ban, 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.

7. Válassza ki a Storage-fiók helyét, vagy fogadja el az alapértelmezett helyet.

8. Fogadja el az összes többi alapértelmezett értéket, válassza a  **felülvizsgálat & létrehozás**  >  **létrehozása lehetőséget**.

9. A Storage-fiók létrehozása után válassza az  **Ugrás erőforráshoz**lehetőséget.

#### <a name="create-a-container"></a>Tároló létrehozása

1. A Storage-fiók áttekintés lapján válassza a  **Blobok**lehetőséget.

2. Válassza a  **tároló**elemet, adja meg a tároló nevét, válassza a  **blob** (névtelen olvasási hozzáférés csak blobokhoz) lehetőséget, majd kattintson **az OK gombra**.

#### <a name="enable-cross-origin-resource-sharing-cors"></a>Több eredetű erőforrás-megosztás engedélyezése (CORS)

A böngészőben Azure AD B2C kód egy modern és standard megközelítést használ a felhasználói folyamatokban megadott URL-címek egyéni tartalmának betöltésére. A CORS lehetővé teszi a weblapokon lévő korlátozott erőforrások kérését más tartományokból.

1. A menüben válassza a  **CORS**lehetőséget.

2. Az  **engedélyezett eredetek**mezőben adja meg a következőt:  `https://your-tenant-name.b2clogin.com` . Cserélje le a-bérlő nevét a Azure AD B2C bérlő nevére. Például:  `https://fabrikam.b2clogin.com`. A bérlő nevének megadásakor használja az összes kisbetűt.

3. Az **engedélyezett módszereknél**válassza a **beolvasás**, a **put**és a **Beállítások lehetőséget**.

4. Az **engedélyezett fejlécek**mezőbe írjon be egy csillagot (*).

5. A közzétett **fejlécek**esetében írjon be egy csillagot (*).

6. A **Max Age**értéknél adja meg a 200 értéket.

   ![Arkóz Labs-regisztráció és bejelentkezés](media/partner-arkose-labs/signup-signin-arkose.png)

7. Válassza a **Mentés** lehetőséget.

### <a name="part-2--set-up-a-back-end-server"></a>2. rész – háttér-kiszolgáló beállítása

Töltse le a git Basht, és kövesse az alábbi lépéseket:

1. Kövesse az utasításokat [egy webalkalmazás létrehozásához](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-php), egészen addig, amíg az üzenet "gratulál!Az első PHP-alkalmazás üzembe helyezése App Service "jelenik meg.

2. Nyissa meg a helyi mappát, és nevezze át az **index. php** fájlt a **Verify-token. php**fájlba.

3. Nyissa meg az újonnan átnevezt Verify-token. php fájlt, és:

   a. Cserélje le a tartalmat a [GitHub-tárházban](https://github.com/ArkoseLabs/Azure-AD-B2C)található Verify-token. php fájl tartalmára.

   b. Cserélje le <private_key> a 3. sorban a Arkóz Labs irányítópultról beszerzett titkos kulcsra.

4. A helyi terminál ablakban véglegesítse a módosításokat a git-ben, majd küldje el a kód módosításait az Azure-ba úgy, hogy beírja a következőt a git Bashbe:

   ``git commit -am "updated output"``

   ``git push azure master``  

### <a name="part-3---final-setup"></a>3. rész – végső beállítás

#### <a name="store-the-custom-html"></a>Az egyéni HTML-fájl tárolása

1. Nyissa meg a [GitHub-tárházban](https://github.com/ArkoseLabs/Azure-AD-B2C)tárolt index.html fájlt.

2. Cserélje le az összes példányát a `<tenantname>` b2C-bérlő nevére (más szóval `<tenantname>.b2clogin.com` ). Négy példánynak kell lennie.

3. Cserélje le a `<appname>` alkalmazást a 2. részben létrehozott alkalmazás nevére, 1. lépés.

   ![A Arkóz Labs Azure CLI-t bemutató képernyőkép](media/partner-arkose-labs/arkose-azure-cli.png)

4. Cserélje le az `<public_key>` 64-es sort a Arkóz Labs irányítópultról beszerzett nyilvános kulcsra.

5. Töltse fel a index.html fájlt a fent létrehozott blob Storage-ba.

6. Ugrás a **Storage**-  >  **tároló**  >  **feltöltése**.

#### <a name="set-up-azure-ad-b2c"></a>Azure AD B2C beállítása

> [!NOTE]
> Ha még nem rendelkezik ilyennel, [hozzon létre egy Azure ad B2C bérlőt](tutorial-create-tenant.md) , amely az Azure-előfizetéshez van csatolva.

1. Hozzon létre egy felhasználói folyamatot az [itt](tutorial-create-user-flows.md)látható információk alapján. Állítsa le a **felhasználói folyamat tesztelése**szakaszt.

2. JavaScript engedélyezése a [felhasználói folyamatokban](user-flow-javascript-overview.md).

3. Ugyanebben a felhasználói folyamat lapon engedélyezze az egyéni lap URL-címét: ugrás a **felhasználói folyamat**  >  **lap Elrendezés**  >  **Egyéni oldal tartalma**  =  **Igen**,  >  **Egyéni lap URL-címének beszúrása**.
Ezt az egyéni oldal URL-címét a blob Storage-ban lévő index.html fájl helyéről szerzi be a rendszer.  

   ![A Arkóz Labs tárolási URL-címét bemutató képernyőfelvétel](media/partner-arkose-labs/arkose-storage-url.png)

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Nyissa meg a Azure AD B2C bérlőt, és a **házirendek**területen válassza a **felhasználói folyamatok**elemet.

2. Válassza ki a korábban létrehozott felhasználói folyamatot.

3. Válassza a **felhasználói folyamat futtatása** lehetőséget, és válassza ki a beállításokat:

   a. **Alkalmazás** – válassza ki a regisztrált alkalmazást (minta: JWT).

   b. **Válasz URL-címe** – válassza ki az átirányítási URL-címet.

   c. Válassza a **felhasználói folyamat futtatása**lehetőséget.

4. Ugorjon végig a regisztrációs folyamaton, és hozzon létre egy fiókot.

5. Jelentkezzen ki.

6. Ugorjon végig a bejelentkezési folyamaton.

7. A **Folytatás**gombra kattintva megjelenik egy Arkóz Labs-puzzle.

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket:

- [Egyéni szabályzatok az Azure AD B2C-ben](custom-policy-overview.md)

- [Ismerkedés az egyéni szabályzatokkal Azure AD B2C](custom-policy-get-started.md?tabs=applications)
