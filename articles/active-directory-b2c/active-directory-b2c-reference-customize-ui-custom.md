---
title: Felhasználói út felhasználói FELÜLETének testreszabása Egyéni szabályzatokkal | Microsoft Docs
description: Ismerkedjen meg Azure Active Directory B2C egyéni szabályzatokkal.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7e4714de9868dbd540e2e662b22a22da6df6514b
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73147536"
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>Felhasználói kezelőfelület felhasználói felületének testreszabása Egyéni szabályzatokkal

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> Ez a cikk részletesen ismerteti, hogyan működik a felhasználói felület testreszabása, és hogyan engedélyezhető a Azure AD B2C egyéni szabályzatok használata az Identity Experience Framework használatával.


A zökkenőmentes felhasználói élmény minden vállalat és fogyasztó közötti megoldás kulcsa. A zökkenőmentes felhasználói élmény olyan élmény, amely az eszközön vagy a böngészőben van, ahol a felhasználó a szolgáltatáson keresztüli útvonala nem különböztethető meg az általuk használt ügyfél-szolgáltatástól.

## <a name="understand-the-cors-way-for-ui-customization"></a>A felhasználói felület testreszabásának CORS módjának megismerése

Azure AD B2C lehetővé teszi a felhasználói élmény (UX) megjelenését a kiszolgált és az egyéni szabályzatok által Azure AD B2C által megjelenített különböző lapokon.

Erre a célra Azure AD B2C kódot futtat a felhasználó böngészőjében, és a modern és standard megközelítést használja az eltérő [eredetű erőforrás-megosztás (CORS)](https://www.w3.org/TR/cors/) használatával, hogy egyéni tartalmat töltsön be egy egyéni szabályzatban megadott URL-címről, hogy a HTML5/CSS-re mutasson. sablonok. A CORS olyan mechanizmus, amely lehetővé teszi a korlátozott erőforrások, például a betűkészletek használatát egy olyan weblapon, amely egy másik tartományon kívül esik azon a tartományon, amelyről az erőforrás származik.

A régi hagyományos módszerhez képest, ahol a sablon lapjait a megoldás birtokolja, ahol korlátozott számú szöveget és képet biztosított, és az elrendezés korlátozott vezérlését kínáljuk, ami több mint nehézséget jelent a zökkenőmentes működés érdekében, a CORS módot támogatja a HTML5 és a CSS használatát, és lehetővé teszi a következőket:

- Tárolja a tartalmat, és a megoldás ügyféloldali parancsfájl használatával szúrja be a vezérlőket.
- Teljes körű vezérlést tesznek az elrendezés és a működés minden képpontján.

A HTML5/CSS fájlok megfelelő módon történő elvégzésével tetszőleges számú tartalmi oldalt biztosíthat.

> [!NOTE]
> Biztonsági okokból a JavaScript használata jelenleg le van tiltva a testreszabáshoz. 

Minden HTML5/CSS-sablonban meg kell adnia egy *horgony* elemet, amely megfelel a HTML vagy a tartalom lap kötelező `<div id="api">` elemének, ahogy az a következő ábrán látható. Azure AD B2C megköveteli, hogy az összes tartalmi oldalnak legyen ez a megadott div-je.

```
<!DOCTYPE html>
<html>
  <head>
    <title>Your page content’s tile!</title>
  </head>
  <body>
    <div id="api"></div>
  </body>
</html>
```

Az oldal Azure AD B2C kapcsolódó tartalma ebbe a div-be kerül, míg az oldal többi része a tiéd. A Azure AD B2C JavaScript-kód lekéri a tartalmat, és HTML-kódot szúr be ebbe az adott div elembe. Azure AD B2C a következő vezérlőket adja meg a megfelelő módon: Account Chooser Control, log in Controls, multi-Factor (jelenleg telefon-alapú) vezérlők és attribútum-gyűjtemény vezérlők. Azure AD B2C biztosítja, hogy az összes vezérlő a HTML5-kompatibilis és elérhető legyen, és az összes vezérlő teljes stílusú legyen, és hogy a vezérlő verziója ne visszafejlődés.

Az egyesített tartalmat a rendszer végül dinamikus dokumentumként jeleníti meg a fogyasztó számára.

Annak biztosítása érdekében, hogy minden a várt módon működjön, a következőket kell tennie:

- Győződjön meg arról, hogy a tartalom HTML5-kompatibilis és elérhető
- Győződjön meg arról, hogy a CORS engedélyezve van a Content Server.
- Tartalom kézbesítése HTTPS-kapcsolaton keresztül.
- Használjon abszolút URL-címeket, például a `https://yourdomain/content` az összes hivatkozáshoz és a CSS-tartalomhoz.

> [!TIP]
> Annak ellenőrzéséhez, hogy az a hely, amelyen a tartalmat üzemelteti, engedélyezve van-e a CORS, és tesztelje a CORS kérelmeket, használhatja a site https://test-cors.org/. Ennek a helynek köszönhetően elküldheti a CORS kérést egy távoli kiszolgálónak (ha tesztelni szeretné, hogy a CORS támogatott-e), vagy elküldheti a CORS kérelmet egy tesztelési kiszolgálónak (a CORS bizonyos funkcióinak megismeréséhez).

> [!TIP]
> A webhely https://enable-cors.org/ több mint hasznos erőforrást is jelent a CORS-on.

Ennek a CORS megközelítésnek köszönhetően a végfelhasználók konzisztens tapasztalatokkal rendelkeznek az alkalmazás és a Azure AD B2C által kiszolgált lapok között.

## <a name="create-a-storage-account"></a>Create a storage account

Előfeltételként létre kell hoznia egy Storage-fiókot. Azure Blob Storage-fiók létrehozásához Azure-előfizetésre van szükség. Az [Azure webhelyén](https://azure.microsoft.com/pricing/free-trial/)regisztrálhat egy ingyenes próbaverziót.

1. Nyisson meg egy böngészési munkamenetet, és navigáljon a [Azure Portal](https://portal.azure.com).
2. Jelentkezzen be a rendszergazdai hitelesítő adataival.
3. Kattintson **az erőforrás létrehozása** > **Storage** - > **Storage-fiók**elemre.  Megnyílik A **Storage-fiók létrehozása** panel.
4. A **név mezőben**adja meg a Storage-fiók nevét (például *contoso369b2c*). Ezt az értéket később a *storageAccountName*-nek nevezzük.
5. Válassza ki a megfelelő beállításokat a díjszabási csomaghoz, az erőforráscsoporthoz és az előfizetéshez. Győződjön meg arról, hogy a **PIN-kód kezdőpulton** beállítás be van jelölve. Kattintson a  **Create** (Létrehozás) gombra.
6. Lépjen vissza a Kezdőpulton, és kattintson a létrehozott Storage-fiókra.
7. A **szolgáltatások** szakaszban kattintson a **Blobok**elemre. Megnyílik egy **blob Service panel** .
8. Kattintson a **+ tároló**elemre.
9. A **név mezőben**adja meg a tároló nevét, például: *B2C*. Ezt az értéket később *containerName*-ként nevezzük.
9. Válassza a **blob** lehetőséget **hozzáférési típusként**. Kattintson a  **Create** (Létrehozás) gombra.
10. A létrehozott tároló megjelenik a **blob Service panel**listájában.
11. A **Blobok** panel bezárásához.
12. A **Storage-fiók ablaktáblán**kattintson a **kulcs** ikonra. Megnyílik a **hozzáférési kulcsok panel** .  
13. Jegyezze fel a **key1**értékét. Ez az érték később *key1*néven is ismert.

## <a name="downloading-the-helper-tool"></a>A segítő eszköz letöltése

1.  Töltse le a segítő eszközt a [githubról](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).
2.  Mentse a *B2C-AzureBlobStorage-Client-Master. zip* fájlt a helyi gépre.
3.  Bontsa ki a B2C-AzureBlobStorage-Client-master. zip fájl tartalmát a helyi lemezen, például a **UI-customization-Pack** mappában, amely egy *B2C-AzureBlobStorage-Client-Master* mappát hoz létre az alatta.
4.  Nyissa meg a mappát, és bontsa ki a *B2CAzureStorageClient. zip* fájl tartalmát.

## <a name="upload-the-ui-customization-pack-sample-files"></a>A felhasználói felület testreszabása – csomag minta fájljainak feltöltése

1.  A Windows Intéző segítségével navigáljon az előző szakaszban létrehozott *UI-customization-Pack* mappában található *B2C-AzureBlobStorage-Client-Master* mappára.
2.  Futtassa a *B2CAzureStorageClient. exe* fájlt. A program feltölti az Ön által megadott könyvtárban lévő összes fájlt a Storage-fiókjába, és engedélyezi a CORS hozzáférését a fájlokhoz.
3.  Ha a rendszer kéri, válassza a következőket: a.  A Storage-fiók neve, *storageAccountName*, például *contoso369b2c*.
    b.  Az Azure Blob Storage ( *key1*) elsődleges hozzáférési kulcsa, például *contoso369b2c*.
    c.  A tárolási blob Storage-tároló neve, *containerName*, például *B2C*.
    d.  Az *indító-Pack* minta fájljainak elérési útja, például *.. \B2CTemplates\wingtiptoys*.

Ha követte a fenti lépéseket, a kitalált vállalati **wingtiptoys** tartozó *felhasználói felület – Testreszabás – csomag* HTML5-és CSS-fájljai mostantól a Storage-fiókra mutatnak.  A Azure Portal kapcsolódó tároló paneljének megnyitásával ellenőrizheti, hogy a tartalom megfelelően lett-e feltöltve. Azt is megteheti, hogy a tartalom megfelelően lett feltöltve, ha a lapot egy böngészőből éri el. További információ [: Azure Active Directory B2C: egy segítő eszköz, amely az oldal felhasználói felületének (UI) testreszabási funkciójának bemutatására szolgál](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>Győződjön meg arról, hogy a Storage-fiók CORS engedélyezve van

A CORS (több eredetű erőforrás-megosztás) engedélyezni kell a végponton Azure AD B2C a tartalom betöltéséhez. Ennek az az oka, hogy a tartalom egy másik tartományban található, mint a tartomány Azure AD B2C a lapot fogja kiszolgálni.

A következő lépésekkel ellenőrizheti, hogy a tartalmat üzemeltető tárolón engedélyezve van-e a CORS:

1. Nyisson meg egy tallózási munkamenetet, és a Storage-fiókjában lévő hely teljes URL-címével nyissa meg a *Unified. html* oldalt, `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Például: https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. Navigáljon https://test-cors.org. Ezen a helyen ellenőrizheti, hogy a használt lapon engedélyezve van-e a CORS.  
   <!--
   ![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
   -->

3. A **távoli URL-cím**mezőben adja meg az egyesített. html-tartalom teljes URL-címét, majd kattintson a **kérelem küldése**gombra.
4. Ellenőrizze, hogy az **eredmények** szakasz kimenete tartalmazza-e az x/o- *állapotot: 200*, amely azt jelzi, hogy a CORS engedélyezve van.
   <!--
   ![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
   -->
   A Storage-fióknak ekkor tartalmaznia kell egy *B2C* nevű BLOB-tárolót az ábrán, amely a következő wingtiptoys-sablonokat tartalmazza az *alapszintű csomagból*.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

Az alábbi táblázat az előző HTML5-lapok célját ismerteti.

| HTML5-sablon | Leírás |
|----------------|-------------|
| *phonefactor. html* | Ez a lap sablonként használható a multi-Factor Authentication oldalon. |
| *ResetPassword metódusát. html* | Ez az oldal sablonként használható az elfelejtett jelszó oldalához. |
| *selfasserted. html* | Ez a lap sablonként használható a közösségi fiók regisztrálása oldal, egy helyi fiók regisztrálása oldalon vagy egy helyi fiók bejelentkezési oldalán. |
| *egyesített. html* | Ez az oldal sablonként használható az egyesített regisztrációs vagy bejelentkezési oldalhoz. |
| *updateprofile. html* | Ez az oldal sablonként használható a profil frissítése oldalhoz. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>A HTML5/CSS-sablonokra mutató hivatkozás hozzáadása a felhasználói útra

A HTML5/CSS-sablonokra mutató hivatkozást a felhasználói útra is hozzáadhat, ha közvetlenül szerkeszt egy egyéni szabályzatot.

A felhasználói úton használni kívánt egyéni HTML5/CSS-sablonokat meg kell adni a felhasználói útvonalakban használható tartalmi definíciók listájában. Erre a célra egy opcionális *\<ContentDefinitions >* XML-elemet az egyéni házirend XML-fájljának *\<BuildingBlocks >* szakaszában kell deklarálnia.

A következő táblázat ismerteti a Azure AD B2C Identity Experience motor által felismert tartalmi definíciós azonosítókat, valamint az azokhoz kapcsolódó lapok típusát.

| Tartalom definíciójának azonosítója | Leírás |
|-----------------------|-------------|
| *API. error* | **Hiba lap**. Ez az oldal akkor jelenik meg, ha kivételt vagy hibát észlel. |
| *API. idpselections* | **Identitás-szolgáltató kiválasztása lap**. Ez az oldal azon identitás-szolgáltatók listáját tartalmazza, amelyeket a felhasználó a bejelentkezés során választhat. Ezek a szolgáltatók vállalati identitás-szolgáltatók, közösségi identitás-szolgáltatók, például a Facebook vagy a Google +, vagy helyi fiókok (e-mail-cím vagy Felhasználónév alapján). |
| *API. idpselections. regisztráció* | **Az identitás-szolgáltató kiválasztása a regisztrációhoz**. Ez az oldal azon identitás-szolgáltatók listáját tartalmazza, amelyeket a felhasználó a regisztráció során választhat. Ezek a szolgáltatók vállalati identitás-szolgáltatók, közösségi identitás-szolgáltatók, például a Facebook vagy a Google +, vagy helyi fiókok (e-mail-cím vagy Felhasználónév alapján). |
| *API. localaccountpasswordreset* | **Elfelejtett jelszó lap**. Ez a lap egy űrlapot tartalmaz, amelyet a felhasználónak ki kell töltenie a jelszó-visszaállítás megkezdéséhez.  |
| *API. localaccountsignin* | **Helyi fiók bejelentkezési lapja**. Ez az oldal olyan bejelentkezési űrlapot tartalmaz, amelyet a felhasználónak ki kell töltenie, amikor helyi fiókkal jelentkezik be egy e-mail-cím vagy egy Felhasználónév alapján. Az űrlap tartalmazhatja a szövegbeviteli és a jelszó-beviteli mezőt is. |
| *API. localaccountsignup* | **Helyi fiók regisztrációjának lapja**. Ez az oldal olyan regisztrációs űrlapot tartalmaz, amelyet a felhasználónak ki kell töltenie, amikor regisztrál egy olyan helyi fiókra, amely e-mail-cím vagy Felhasználónév alapján van regisztrálva. Az űrlap különböző beviteli vezérlőket tartalmazhat, mint például a szövegbeviteli mező, a jelszó-beviteli mező, a választógomb, az egyszeres kijelölés legördülő lista és a többszörös kijelölés jelölőnégyzet. |
| *API. phonefactor* | **Multi-Factor Authentication oldal**. Ezen a lapon a felhasználók a regisztrálás vagy a bejelentkezés során ellenőrizhetik a telefonszámokat (szöveg vagy hang használatával). |
| *API. selfasserted* | **Közösségi fiók regisztrálása oldal**. Ez az oldal egy olyan regisztrációs űrlapot tartalmaz, amelyet a felhasználónak ki kell töltenie, amikor regisztrál egy olyan meglévő fiókot egy közösségi identitás-szolgáltatóról, mint például a Facebook vagy a Google +. Ez az oldal hasonló az előző közösségi fiók regisztrációs oldalához, a jelszó-beviteli mezők kivételével. |
| *API. selfasserted. profileUpdate* | **Profil frissítése lap**. Ez a lap egy űrlapot tartalmaz, amelyet a felhasználó a profiljának frissítéséhez használhat. Ez az oldal hasonló az előző közösségi fiók regisztrációs oldalához, a jelszó-beviteli mezők kivételével. |
| *API. signuporsignin* | **Egyesített regisztrációs vagy bejelentkezési oldal**.  Ez a lap a felhasználók bejelentkezési &EIT is kezeli, akik vállalati identitás-szolgáltatókat, például Facebook-vagy Google + vagy helyi fiókokat használhatnak.

## <a name="next-steps"></a>Következő lépések
[Hivatkozás: az egyéni szabályzatok működése a B2C-beli Identity Experience keretrendszerrel](active-directory-b2c-reference-custom-policies-understanding-contents.md)
