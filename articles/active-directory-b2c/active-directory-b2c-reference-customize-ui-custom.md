---
title: Egyéni házirendek felhasználói út, a felhasználói felület testreszabása |} A Microsoft Docs
description: Ismerje meg az Azure Active Directory B2C-vel egyéni szabályzatok.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 88abd3d17218f1168119a66701f1474d27244acf
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438257"
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>Egyéni házirendek felhasználói út, a felhasználói felület testreszabása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> Ez a cikk egy speciális leírása felhasználói felületének testreszabását működése és engedélyezése az Azure AD B2C-vel egyéni szabályzatok, az identitás-kezelőfelületi keretrendszer használatával.


Zökkenőmentes felhasználói élményt az üzleti és ügyfél-megoldással a kulcs. Zökkenőmentes felhasználói élményt felületet nyújt, az eszközön vagy böngészőben, ahol a szolgáltatás egy felhasználó tevékenysége az, hogy az általuk használt ügyfélszolgálati megkülönböztetni.

## <a name="understand-the-cors-way-for-ui-customization"></a>Felhasználói felület testreszabása a CORS módjának ismertetése

Az Azure AD B2C segítségével-és-megjelenésének felhasználói felület (UX) a különböző oldalakon szolgálja ki és az Azure AD B2C által megjelenített testreszabhatja az egyéni szabályzatok használatával.

Erre a célra az Azure AD B2C-t futtatja a kódot a fogyasztói böngészőben, és a modern és szabványos megközelítést használ [eltérő eredetű erőforrások megosztása (CORS)](https://www.w3.org/TR/cors/) átirányítása egyéni szabályzatokban megadott konkrét URL-címről egyéni tartalom betöltése a HTML5-alapú/CSS-sablonokat. A CORS olyan mechanizmus, amely lehetővé teszi, hogy a korlátozott erőforrások, például a betűtípus, egy weblap, egy másik tartomány, amelyből az erőforrás adja meg a tartományon kívüli kell kérni a rendszer.

A régi hagyományos módszerétől, ahol sablon oldalak tulajdonosa a megoldás, ahol a megadott korlátozott szöveget és képeket, ahol az elrendezés és működését érintő korlátozott szabályozása a CORS módja egy zökkenőmentes eléréséhez több mint nehézségek vezető által kínált támogatja a HTML5- és CSS, és lehetővé teszi:

- A tartalom tárolására, és a megoldás kódtárba annak vezérlőelemeinek ügyféloldali parancsfájlok használatával.
- Elrendezés és működését érintő minden képpont teljes hozzáféréssel rendelkeznek.

Megadhatja, hogy annyi tartalom oldalak tetszés szerint elvégezte a megfelelő HTML5/CSS-fájlokat.

> [!NOTE]
> Biztonsági okokból JavaScript használata jelenleg le van tiltva a testreszabáshoz. 

Minden, a HTML5-alapú/CSS-sablonok, adjon meg egy *forráshorgony* elem, amely megfelel a szükséges `<div id=”api”>` elem a HTML-kódot vagy a tartalom lap, a továbbiakban mutatják be. Az Azure AD B2C megköveteli, hogy minden tartalom lap az adott div.

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

Az oldal az Azure AD B2C-vel kapcsolatos tartalom a kártevő program férkőzik be a div közben az oldal többi valóban az Öné szabályozhatja. Az Azure AD B2C-vel JavaScript-kód lekéri a tartalmat a, és HTML kódtárba be az adott div elembe. Az Azure AD B2C-kódtárba szükség szerint a következő vezérlőkre: fiók kiválasztásakor vezérlő, jelentkezzen be a vezérlőket, multi-factor Authentication (jelenleg telefonos alapú) vezérlőkkel és attribútum gyűjtemény. Az Azure AD B2C biztosítja, hogy minden a vezérlők, amelyek HTML5 megfelelő és elérhető-e az összes vezérlő teljes stílusának és, amely egy vezérlő verziója nem amelyikre.

Az egyesített tartalmat végül a fogyasztó a dinamikus dokumentum jelenik meg.

Annak érdekében, hogy minden megfelelően működik-e, tegye a következőket:

- Győződjön meg, hogy a tartalom HTML5 megfelelő és elérhető-e
- Győződjön meg arról, a webtartalom-kiszolgáló a CORS engedélyezve van.
- HTTPS-kapcsolaton keresztül tartalmat szolgálnak ki.
- Használjon abszolút URL-CÍMEK például `https://yourdomain/content` hivatkozások és a CSS-tartalom.

> [!TIP]
> Ellenőrizze, hogy a hely üzemelteti a tartalmat a CORS-támogatással rendelkezik-e, és tesztelése a CORS-kérések, használhatja a hely https://test-cors.org/. Ez a hely köszönhetően is a CORS-kérést küld egy távoli kiszolgálóra (tesztelje, hogy a CORS támogatott), vagy a CORS-kérést küld a kiszolgáló (az egyes CORS funkcióinak bemutatása).

> [!TIP]
> A hely https://enable-cors.org/ egy több, mint a CORS hasznos forrásokat is jelent.

Köszönhetően ez a CORS-alapú megközelítés a végfelhasználók rendelkezik az alkalmazás és az Azure AD B2C által üzemeltetett oldalak közötti egységes felületeket.

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Előfeltételként hozzon létre egy tárfiókot kell. Azure-előfizetéssel, hozzon létre egy Azure Blob Storage-fiók van szüksége. Regisztrálhat egy ingyenes próbaidőszakot a [Azure-webhelyen](https://azure.microsoft.com/pricing/free-trial/).

1. Nyisson meg egy böngészési munkamenetet, és keresse meg a [az Azure portal](https://portal.azure.com).
2. Jelentkezzen be a rendszergazdai hitelesítő adataival.
3. Kattintson a **erőforrás létrehozása** > **tárolási** > **tárfiók**.  A **storage-fiók létrehozása** panel megnyílik.
4. A **neve**, adja meg például a tárfiók nevét *contoso369b2c*. Ezt az értéket később a neve, *storageAccountName*.
5. Válassza ki a tarifacsomagot, az erőforráscsoport és az előfizetés a szükséges beállításokat. Győződjön meg arról, hogy rendelkezik-e a **kezdőpulton rögzít** lehetőség be van jelölve. Kattintson a **Create** (Létrehozás) gombra.
6. Lépjen vissza a kezdőpulton, és kattintson a létrehozott tárfiókot.
7. Az a **szolgáltatások** területén kattintson **Blobok**. A **Blob service panel** nyit meg.
8. Kattintson a **+ tároló**.
9. A **neve**, adja meg például a tároló nevét *b2c*. Ezt az értéket később nevezzük *containerName*.
9. Válassza ki **Blob** , a **típus eléréséhez**. Kattintson a **Create** (Létrehozás) gombra.
10. A listában megjelenik a létrehozott tárolót a **Blob service panel**.
11. Zárja be a **Blobok** ablaktáblán.
12. Az a **storage-fiók panelen**, kattintson a **kulcs** ikonra. Egy **hozzáférési kulcsok panel** nyit meg.  
13. Jegyezze fel az értékét **key1**. Ezt az értéket később nevezett *key1*.

## <a name="downloading-the-helper-tool"></a>A segédeszköz letöltése

1.  Töltse le a segítő eszközt [GitHub](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).
2.  Mentse a *B2C-Azure BLOB Storage szolgáltatásról-ügyfél-master.zip* fájlt a helyi gépen.
3.  A tartalom a B2C-Azure BLOB Storage szolgáltatásról-ügyfél-master.zip fájl a helyi lemezen lévő például alatt bontsa ki a **felhasználói felület-testreszabási-csomag** mappába, amely létrehoz egy *B2C-Azure BLOB Storage szolgáltatásról – ügyfél-master*mappa alatt.
4.  Megnyithatja a mappát, és a tartalom az archív fájl kibontása *B2CAzureStorageClient.zip* benne.

## <a name="upload-the-ui-customization-pack-sample-files"></a>A felhasználói felület-testreszabási-csomag minta fájlok feltöltése

1.  A Windows Intézővel keresse meg a mappát *B2C-Azure BLOB Storage szolgáltatásról – ügyfél-master* alatt a *felhasználói felület-testreszabási-csomag* az előző szakaszban létrehozott mappára.
2.  Futtassa a *B2CAzureStorageClient.exe* fájlt. Ezt a programot, hogy adja meg, hogy a tárfiók, és engedélyezze a CORS-elérését az ilyen fájlok a könyvtárban lévő összes fájlt tölt fel.
3.  Amikor a rendszer kéri, adja meg: egy.  A tárfiók nevére *storageAccountName*, például *contoso369b2c*.
    b.  Az elsődleges elérési kulcsot az azure blob Storage *key1*, például *contoso369b2c*.
    c.  A storage blob storage-tároló, neve *containerName*, például *b2c*.
    d.  Az elérési útját a *-Kezdőcsomag* fájlok, például minta *... \B2CTemplates\wingtiptoys*.

Ha követte a fenti lépéseket, a HTML5- és CSS-fájlok, a *felhasználói felület-testreszabási-csomag* fiktív cég **tartománynév** a tárfiók már mutat-e.  Ellenőrizheti, hogy a tartalom megfelelően lett feltöltve az Azure Portalon nyissa meg a kapcsolódó tároló panelen. Másik lehetőségként ellenőrizheti, hogy a tartalom megfelelően lett feltöltve az oldal egy böngészőben elérésével. További információkért lásd: [Azure Active Directory B2C: Egy lap felhasználói felületének (UI) testreszabási funkcióról bemutatható segédeszköze](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>A storage-fiók rendelkezzen a CORS-támogatással

A végponthoz tartozó tartalom betöltése az Azure AD B2C CORS (eltérő eredetű erőforrások megosztása) engedélyezni kell. Ennek oka az, a tartalmat egy másik tartományban a tartományhoz, az oldal az Azure AD B2C-vel fog szolgáltató-ban üzemel.

Annak ellenőrzéséhez, hogy rendelkezik-e a tárterület üzemelteti a tartalmat a CORS-támogatással, folytassa a következő lépéseket:

1. Nyisson meg egy böngészési munkamenetet, és nyissa meg a lapot *unified.html* használatával a teljes URL-címét a helyet a tárfiókban lévő `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Például: https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. Nyissa meg a https://test-cors.org címet. Ez a hely ellenőrizze, hogy használja az oldal rendelkezik-e a CORS-támogatással teszi lehetővé.  
   <!--
   ![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
   -->

3. A **távoli URL-cím**, adja meg a teljes URL-címet a unified.html tartalom, és kattintson a **kérés küldése**.
4. Ellenőrizze, hogy a kimenetet a a **eredmények** szakasz tartalmaz *XHR állapota: 200-as*, ami azt jelenti, hogy a CORS engedélyezve van-e.
   <!--
   ![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
   -->
   A storage-fiókot most tartalmaznia kell egy blobtárolót *b2c* az ábrán is látható, amely tartalmazza az alábbi, a tartománynév-sablonok a *-Kezdőcsomag*.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

A következő táblázat ismerteti az előző HTML5-oldalak célját.

| HTML5-sablon | Leírás |
|----------------|-------------|
| *phonefactor.html* | Ezen a lapon a multi-factor authentication-oldal sablonként használható. |
| *resetpassword.html* | Ezen a lapon is használható a sablont egy elfelejtett jelszó oldal. |
| *selfasserted.html* | Ezen a lapon egy sablont a közösségi fiók regisztráció oldal, egy helyi fiók regisztrációs oldala vagy egy helyi fiók bejelentkezési oldala is használhatók. |
| *unified.html* | Ezen a lapon egy egyesített regisztrációs vagy bejelentkezési oldal sablonként is használható. |
| *updateprofile.html* | Ezen a lapon egy frissítés profillapján sablonként használható. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>A felhasználói út a HTML5-alapú/CSS sablonokat mutató hivatkozás hozzáadása

Is hozzáadhat egy hivatkozást a HTML5-alapú/CSS-sablonok a felhasználói interakciósorozat egy egyéni házirendet közvetlen szerkesztésével.

Az egyéni HTML5/CSS sablonok használata a felhasználói interakciósorozatban szereplő kell tartalomdefiníciók használható az adott felhasználói utak listájának adható meg. Erre a célra egy nem kötelező  *\<ContentDefinitions >* XML-elem alatt kell deklarálni a  *\<BuildingBlocks >* az egyéni XML-házirendfájl szakaszában.

A következő táblázat ismerteti a szerepkördefiníció-azonosítók ismeri fel az Azure AD B2C identitáskezelési élmény motor és a hozzájuk kapcsolódó oldalak típusa tartalom készletét.

| Tartalomdefiníció azonosítója | Leírás |
|-----------------------|-------------|
| *api.error* | **Hibalap**. Ez az oldal jelenik meg kivételt vagy hibát észlelt. |
| *api.idpselections* | **Identitásszolgáltató kiválasztása oldal**. Ezen a lapon identitásszolgáltatókat, amelyek a felhasználó kiválaszthat a bejelentkezés során listáját tartalmazza. Ezek a szolgáltatók a következők: vagy a vállalati identitás-szolgáltatóktól, közösségi Identitásszolgáltatók, például a Facebook és a Google + vagy a helyi fiókok (e-mail-cím vagy a felhasználó neve alapján). |
| *api.idpselections.signup* | **Identity provider adatgyűjtésre vonatkozó felhasználói előfizetési**. Ezen a lapon identitásszolgáltatókat, amelyek a felhasználók választhatnak a regisztráció során listáját tartalmazza. Ezek a szolgáltatók a következők: vagy a vállalati identitás-szolgáltatóktól, közösségi Identitásszolgáltatók, például a Facebook és a Google + vagy a helyi fiókok (e-mail-cím vagy a felhasználó neve alapján). |
| *api.localaccountpasswordreset* | **Elfelejtett jelszó oldal**. Ezen az oldalon, amely a felhasználó adja meg a jelszó-visszaállítás kezdeményezése űrlapot tartalmaz.  |
| *api.localaccountsignin* | **Helyi fiók bejelentkezési oldal**. Ez az oldal tartalmaz egy bejelentkezési képernyő, meg kell adni egy e-mail-cím vagy felhasználónév alapján helyi fiókkal való bejelentkezéskor a felhasználó rendelkezik. Az űrlap egy bemeneti szövegmezőt és a jelszó mező tartalmazhat. |
| *api.localaccountsignup* | **Helyi fiók regisztrálási oldala**. Ezen az oldalon, amely a felhasználónak meg kell adni egy helyi fiók egy e-mail-cím vagy felhasználónév alapján való feliratkozáskor van regisztrációs űrlapot tartalmaz. Az űrlap különböző bemeneti vezérlőelemekből, például a bemeneti szövegmezőt, bejegyzés mezőben, választógomb, egyszeri kiválasztásos legördülő listák és többszörös kiválasztási jelölőnégyzetet is tartalmazhat. |
| *api.phonefactor* | **A multi-factor authentication-oldal**. Ezen a lapon felhasználók regisztrálási vagy bejelentkezési alatt ellenőrizheti azok telefonszámok (a szöveges vagy szóbeli). |
| *api.selfasserted* | **Közösségi fiók regisztrálására szolgáló oldal**. Ezen az oldalon, amely a felhasználónak meg kell adni egy meglévő fiókkal egy közösségi identitásszolgáltatót például Facebook vagy a Google + a feliratkozáskor van regisztrációs űrlapot tartalmaz. Ezen a lapon a hasonló az előző közösségi fiók regisztrálására szolgáló oldal a jelszó számbeviteli mezők kivételével. |
| *api.selfasserted.profileupdate* | **Frissítés profillapján**. Ez az oldal, amely a felhasználó használhatja, hogy a profil frissítése űrlapot tartalmaz. Ezen a lapon a hasonló az előző közösségi fiók regisztrálására szolgáló oldal a jelszó számbeviteli mezők kivételével. |
| *api.signuporsignin* | **Egyesített regisztrálási vagy bejelentkezési oldal**.  Ezen a lapon mindkét regisztrációs kezeli, és jelentkezzen be a vállalati identitás-szolgáltatóktól, például Facebook vagy a Google + vagy a helyi fiókok közösségi identitásszolgáltatókat használó felhasználók.

## <a name="next-steps"></a>További lépések
[Hivatkozás: Egyéni szabályzatok ismertetése dolgozhat a B2C-ben az identitás-kezelőfelületi keretrendszer](active-directory-b2c-reference-custom-policies-understanding-contents.md)
