---
title: "Az Azure Active Directory B2C: Hivatkozni: egy felhasználó út egyéni házirendek felhasználói felület testreszabása |} Microsoft Docs"
description: "Témakör: Azure Active Directory B2C egyéni házirendek"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: joroja
ms.openlocfilehash: 68f40aa638a687398512278a0b77d1ba392859cf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>Egy felhasználó út egyéni házirendek felhasználói felület testreszabása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> Ez a cikk a felhasználói felület testreszabásával működése és a B2C egyéni házirendek, az identitás élmény keretrendszerrel engedélyezése speciális leírása


Zökkenőmentes felhasználói élményt az kulcs minden üzleti egyéni megoldás. Zökkenőmentes felhasználói élmény, amelyet azt jelenti, élményt, az eszköz vagy a böngésző, ha a szolgáltatás egy felhasználói út nem különböztethetők meg, valamint az ügyfélszolgálat használják.

## <a name="understand-the-cors-way-for-ui-customization"></a>A CORS módját a felhasználói felület testreszabásával ismertetése

Az Azure AD B2C lehetővé teszi a-és-érzetéhez, felhasználói élmény (UX), amely potenciálisan kiszolgált és az egyéni házirendek segítségével az Azure AD B2C által megjelenített különféle oldalakat a testreszabását.

Erre a célra az Azure AD B2C kód futtatása a felhasználó böngészőben, és használja a modern és szabványos megközelítés [Cross-Origin Resource Sharing (CORS)](http://www.w3.org/TR/cors/) egyéni tartalom betöltése a HTML5 vagy CSS-sablonok mutasson egyéni házirendjében megadott konkrét URL-címről. A CORS egy olyan mechanizmus, amely lehetővé teszi a korlátozott erőforrásokat, például a betűtípust, a weblapra egy másik tartomány kívül az erőforrás eredeti tartományának meg kell adniuk.

A régi hagyományos módon, ahol a megoldás, ha korlátozott szöveg megadott és a képek, ahol elrendezés és érzetéhez korlátozott irányítást felkínált zökkenőmentes eléréséhez több mint nehézségek vezető észlel, a CORS módon támogatja a HTML5-ös és a CSS, és lehetővé teszik a sablon lapok tulajdonosa képest:

- A tartalom tárolására, és a megoldás esetében az ügyféloldali parancsfájl használatával szabályozza.
- Minden képpont elrendezés és a kezelőfelület teljes hozzáféréssel rendelkeznek.

Megadhatja, hogy megfelelő HTML5/CSS fájlok létrehozásával tetszés szerinti számú tartalom oldal.

> [!NOTE]
> Biztonsági okokból a JavaScript használatát jelenleg le van tiltva a testreszabáshoz. JavaScript feloldásához használja az Azure AD B2C-bérlő egyéni tartománynév van szükség.

Minden HTML5/CSS-sablonok, adjon meg egy *horgonyzási* elem, amely megfelel a szükséges `<div id=”api”>` eleme a HTML- vagy a tartalom lap, a továbbiakban bemutatására. Az Azure AD B2C megköveteli, hogy minden tartalom oldal az adott div.

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

Az Azure AD B2C-kapcsolódó tartalom lap fog tölthető be a nézetmodellbe a div közben a lap a további valóban az Öné vezérlőre. Az Azure AD B2C JavaScript-kód lekéri a tartalmat a, és a HTML be az adott div elem esetében. Az Azure AD B2C esetében szükség szerint az alábbi funkciókat: kiválasztásakor vezérlő, a bejelentkezési vezérlők, a multi-factor Authentication (jelenleg phone-alapú) vezérlők és az attribútum gyűjtemény vezérlők fiók. Az Azure AD B2C biztosítja, hogy minden a vezérlők HTML5 megfelelő, és elérhető, az összes vezérlő teljesen stílussal, és, hogy a vezérlő verzió nem lesz amelyikre.

Az egyesített tartalmat végül jelenik meg a dinamikus dokumentum a fogyasztó számára.

Ellenőrizze a fenti működik a várt módon, a következőket kell tennie:

- Győződjön meg arról, a tartalma HTML5 a szabályzatnak megfelelő és érhető el
- Győződjön meg arról, a tartalomkiszolgáló CORS engedélyezve van.
- HTTPS-KAPCSOLATON keresztül tartalmat szolgáltat.
- Használjon abszolút URL-CÍMEI, például a https://yourdomain/content hivatkozások és a CSS-tartalom.

> [!TIP]
> Ellenőrizze, hogy a hely, a tartalom üzemeltet a CORS engedélyezése mellett, és tesztelése a CORS-kérelmeket, a webhely http://test-cors.org/ is használhat. Ez a hely környezetnek köszönhetően egyszerűen a CORS kérés küldése egy távoli kiszolgálóra (tesztelje, hogy a CORS támogatott), vagy a CORS kérés küldése egy tesztkiszolgálón (alapján megismerheti a CORS bizonyos funkcióinak).

> [!TIP]
> A hely http://enable-cors.org/ is jelent a több, mint a CORS hasznos erőforrásokhoz.

Környezetnek köszönhetően a CORS-alapú módszert használja a végfelhasználók majd lesz konzisztens lép az alkalmazás és az Azure AD B2C által kiszolgált lapok között.

## <a name="create-a-storage-account"></a>Create a storage account

Előfeltételként szükséges hozzon létre egy tárfiókot. Szüksége lesz egy Azure Blob Storage-fiók létrehozásához Azure-előfizetéssel. Regisztrálhat egy ingyenes próbaverziót: a [Azure-webhelyen](https://azure.microsoft.com/en-us/pricing/free-trial/).

1. Nyissa meg a böngésző munkamenetet, és keresse meg a [Azure-portálon](https://portal.azure.com).
2. Jelentkezzen be rendszergazdai hitelesítő adataival.
3. Kattintson a **új** > **adatok + tárolás** > **tárfiók**.  A **storage-fiók létrehozása** panel megnyílik.
4. A **neve**, adja meg például a tárfiók nevét *contoso369b2c*. Ez az érték későbbi fog hivatkozni, hogy *storageAccountName*.
5. Válassza ki a tarifacsomagot, az erőforráscsoport és az előfizetés a megfelelő beállításokat. Győződjön meg arról, hogy rendelkezik a **a kezdőpulton rögzít** beállítás be van jelölve. Kattintson a **Create** (Létrehozás) gombra.
6. Lépjen vissza a kezdőpulton, és kattintson az imént létrehozott tárfiókra.
7. Az a **szolgáltatások** kattintson **Blobok**. A **Blob szolgáltatás panelre** megnyílik.
8. Kattintson a **+ tároló**.
9. A **neve**, adja meg például a tároló nevét *b2c*. Ennek az értéknek kell lennie később az úgynevezett *containerName*.
9. Válassza ki **Blob** , a **hozzáférési típus**. Kattintson a **Create** (Létrehozás) gombra.
10. A létrehozott tároló megjelenik a listában a **Blob szolgáltatás panelre**.
11. Zárja be a **Blobok** panelen.
12. Az a **storage-fiók panelen**, kattintson a **kulcs** ikonra. Egy **elérési kulcsok paneljén** megnyílik.  
13. Jegyezze fel a értékének **key1**. Ez az érték későbbi fog hivatkozni *key1*.

## <a name="downloading-the-helper-tool"></a>A segédeszköze letöltése

1.  Töltse le a segítő eszközt [GitHub](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).
2.  Mentse a *B2C-AzureBlobStorage-ügyfél-master.zip* fájlt a helyi számítógépen.
3.  Például bontsa ki a helyi lemezen, a B2C-AzureBlobStorage-ügyfél-master.zip fájl tartalma alapján a **UI-testreszabási-csomag** mappát. Ezzel létrehoz egy *B2C-AzureBlobStorage-ügyfél-főkiszolgáló* mindenképpen a mappában.
4.  Nyissa meg a mappát, és bontsa ki a tartalmat az archív fájl *B2CAzureStorageClient.zip* belül.

## <a name="upload-the-ui-customization-pack-sample-files"></a>A felhasználói felület testreszabása-csomag minta fájlok feltöltése

1.  A Windows Intézővel keresse meg a mappát *B2C-AzureBlobStorage-ügyfél-főkiszolgáló* alatt a *UI-testreszabási-csomag* az előző szakaszban létrehozott mappába.
2.  Futtassa a *B2CAzureStorageClient.exe* fájlt. Ez a program egyszerűen csak meg kell adnia a tárfiók könyvtárban található összes fájl feltöltése, és engedélyezze a hozzáférést a CORS azokat a fájlokat.
3.  Amikor a rendszer kéri, adja meg: egy.  A tárfiók neve *storageAccountName*, például *contoso369b2c*.
    b.  Az elsődleges elérési kulcsát az azure blob storage *key1*, például *contoso369b2c*.
    c.  A tárolási blob tároló neve *containerName*, például *b2c*.
    d.  Az elérési útját a *Starter-csomag* fájlok, például minta *... \B2CTemplates\wingtiptoys*.

Ha követte a fenti lépéseket, a HTML5-ös és CSS fájlok a *UI-testreszabási-csomag* fiktív cég **tartománynév** most a tárfiók mutató lesz.  Ellenőrizheti, hogy a tartalom feltöltötte megfelelően nyissa meg a kapcsolódó tároló panel az Azure portálon. Másik lehetőségként ellenőrizheti, hogy a tartalom feltöltötte megfelelően által megnyitni a lapot a böngészőben. További információkért lásd: [Azure Active Directory B2C: egy a lap felhasználói felület (UI) testreszabási szolgáltatás bemutatására segédeszköze](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>Ellenőrizze a tárolási fiók rendelkezik-e CORS engedélyezése

A végponthoz, a tartalom betöltése az Azure AD B2C Premium engedélyezni kell a CORS (eltérő eredetű erőforrások megosztása). Ennek oka az, a tartalmak tárolása más tartományban található, mint a tartomány az Azure AD B2C prémium fog szolgál az oldal.

Annak ellenőrzéséhez, hogy rendelkezik-e a tárolási üzemeltet a tartalmat a CORS engedélyezése mellett, folytassa a következő lépéseket:

1. Nyissa meg a böngésző munkamenetet, és keresse meg a lap *unified.html* használja a teljes URL-CÍMÉT a tárfiókban lévő helyére `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Például https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. Navigáljon a http://test-cors.org. Ez a hely lehetővé teszi, hogy ellenőrizze, hogy a lap használ rendelkezik-e a CORS engedélyezése mellett.  
<!--
![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
-->

3. A **távoli URL-cím**, adja meg a teljes URL-címet a unified.html tartalmat, és kattintson a **kérés küldése**.
4. Ellenőrizze, hogy a kimenet a **eredmények** szakasz *XHR állapota: 200*. Ez azt jelzi, hogy engedélyezve van-e a CORS.
<!--
![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
-->
A tárfiók most tartalmaznia kell egy blob-tároló nevű *b2c* az ábrán látható, amely tartalmazza a következő tartománynév sablonjai közül a *Starter-csomag*.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

A következő táblázat ismerteti a fenti HTML5-lapok célját.

| HTML5-sablon | Leírás |
|----------------|-------------|
| *phonefactor.HTML* | Ezen a lapon a többtényezős hitelesítés lap sablonként használható. |
| *ResetPassword.HTML* | Ezen a lapon használható sablonként egy elfelejtett jelszó lap. |
| *selfasserted.HTML* | Ezen a lapon egy közösségi fiók regisztrációs oldalon, a helyi fiók előfizetéshez vagy egy helyi fiók bejelentkezési oldalának sablonként is használható. |
| *Unified.HTML* | Ezen a lapon egy egységes regisztráció vagy bejelentkezés lap használható sablonként. |
| *updateprofile.HTML* | Ezen a lapon egy profil frissítés lap használható sablonként. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>Hivatkozás hozzáadása a felhasználói használatában a HTML5 vagy CSS-sablonok

Adhat egy hivatkozást a HTML5 vagy CSS-sablonok a felhasználók utazás egyéni házirendet közvetlenül szerkesztésével.

Az egyéni HTML5/CSS sablonok használatára a felhasználó használatában kell megadni az adott felhasználó utak használható tartalom definíciók listájában. Erre a célra, egy nem kötelező  *<ContentDefinitions>*  XML-elem alatt deklarálni kell a  *<BuildingBlocks>*  az egyéni XML-házirendfájl szakasza.

A következő táblázat ismerteti a tartalom definition azonosítók ismeri fel a az Azure AD B2C identitás élmény motor és a hozzájuk kapcsolódó lapokat típusa.

| Tartalmi azonosító | Leírás |
|-----------------------|-------------|
| *API.Error* | **Hibalap**. Ezen a lapon megjelenik, ha kivétel, vagy hiba történt. |
| *API.idpselections* | **Identitás-szolgáltató kiválasztása lapon**. Ezen a lapon a bejelentkezés során a felhasználó választhat az identitás-szolgáltatóktól listáját tartalmazza. Ezek a vállalati identitás-szolgáltatóktól, például a Facebookhoz és a Google + közösségi Identitásszolgáltatók, vagy helyi fiókok (e-mail cím vagy a felhasználó neve alapján). |
| *API.idpselections.Signup* | **Identity provider adatgyűjtésre vonatkozó felhasználói előfizetési**. Ezen a lapon a regisztráció során a felhasználó választhat az identitás-szolgáltatóktól listáját tartalmazza. Ezek a vállalati identitás-szolgáltatóktól, például a Facebookhoz és a Google + közösségi Identitásszolgáltatók, vagy helyi fiókok (e-mail cím vagy a felhasználó neve alapján). |
| *API.localaccountpasswordreset* | **Elfelejtett jelszó lap**. Ez a lap tartalmaz, amely a felhasználó rendelkezik-e kezdeményezheti a jelszó alaphelyzetbe állítása kitöltésének űrlap.  |
| *API.localaccountsignin* | **Helyi fiók bejelentkezési oldalának**. Ez a lap tartalmaz egy bejelentkezési képernyő, amely a felhasználó rendelkezik-e a adja meg, amikor bejelentkezik egy helyi fiók, amely egy e-mail címet vagy egy felhasználónevet alapul. Az űrlap egy bemeneti szövegmező és a jelszó mező tartalmazhat. |
| *API.localaccountsignup* | **Helyi fiók előfizetéshez**. Ez a lap tartalmaz egy bejelentkezési űrlap kitöltése a felhasználó által történő adja meg, amikor regisztrál a helyi fiók, amely egy e-mail címet vagy egy felhasználónevet alapul. Az űrlap szöveg beviteli mezőt, a jelszó beviteli mezője, a választógomb, a egyetlen legördülő listák és a többszörös kiválasztási jelölőnégyzetet például különböző bemeneti vezérlőket tartalmazhat. |
| *API.phonefactor* | **Többtényezős hitelesítés lap**. Ezen a lapon felhasználók regisztráció vagy bejelentkezés során ellenőrizheti a telefonszámok (szöveges vagy hangos használatával). |
| *API.selfasserted* | **Közösségi fiók bejelentkezési oldalának**. Ez a lap tartalmaz egy bejelentkezési űrlap kitöltése a felhasználó által történő adja meg, amikor regisztrál a Facebook-on vagy a Google + például közösségi identitásszolgáltató egy meglévő fiókkal. Ezen a lapon hasonlít a fenti társadalombiztosítási fiók kivételével a jelszót a regisztrációs oldalon számbeviteli mezők. |
| *API.selfasserted.profileupdate* | **Profil update lapon**. Ez a lap tartalmaz, amely a felhasználó használhatja-e frissíteni a profilját űrlap. Ezen a lapon hasonlít a fenti társadalombiztosítási fiók kivételével a jelszót a regisztrációs oldalon számbeviteli mezők. |
| *API.signuporsignin* | **Egyesített előfizetési vagy a bejelentkezési oldal**.  Ezen a lapon is előfizetési és bejelentkezés a felhasználók, akik vállalati identitás-szolgáltatóktól, például a Facebook-on vagy a Google + és helyi fiókok közösségi Identitásszolgáltatók kezeli.

## <a name="next-steps"></a>Következő lépések
[Hivatkozás: Megérteni, hogyan egyéni házirendek a B2C az identitás élmény keretrendszere](active-directory-b2c-reference-custom-policies-understanding-contents.md)
