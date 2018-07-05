---
title: Felhasználói felület (UI) testreszabása az Azure Active Directory B2C |} A Microsoft Docs
description: Az Azure Active Directory B2C felhasználói felületének (UI) funkciók egy adott üzenettémához.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/16/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 385c13194063761d6449fafa49714d8627f6c6fc
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37447053"
---
# <a name="azure-active-directory-b2c-customize-the-azure-ad-b2c-user-interface-ui"></a>Az Azure Active Directory B2C: Testre szabhatja az Azure AD B2C felhasználói felületének (UI)

Az alkalmazás rendelkező ügyfél létfontosságú a felhasználói élmény.  Növelheti ügyfélkörét létrehozásával felhasználói élményt biztosítsunk a saját márkáját megjelenését és működését. Az Azure Active Directory B2C (Azure AD B2C-vel) lehetővé teszi a regisztrációs, bejelentkezési, profilszerkesztést testreszabása, és új jelszó kérésére vonatkozó oldalak képpontról vezérlővel.

> [!NOTE]
> Ebben a cikkben leírt lap felhasználói felületének testreszabási funkcióról nem vonatkozik a bejelentkezési egyetlen házirend, a hozzájuk tartozó jelszó alaphelyzetbe állítására szolgáló lap és ellenőrzési e-maileket.  Ezek a funkciók használata a [vállalati arculat megjelenítése a szolgáltatás](../active-directory/fundamentals/customize-branding.md) helyette.
>
> Hasonlóképpen ha egy felhasználó egy Profilszerkesztési szabályzatot intiates *előtt* jelentkezik be, a felhasználó át lesz irányítva egy oldal, amely segítségével testre szabható a [vállalati arculat megjelenítése a szolgáltatás](../active-directory/fundamentals/customize-branding.md).

Ez a cikk ismerteti a következő témaköröket:

* A lap felhasználói felületének testreszabási funkcióról.
* Egy eszköz a HTML-tartalom feltöltése az Azure Blob Storage a lap felhasználói felületének testreszabása szolgáltatással való használathoz.
* A felhasználói felületi elemeket, amelyek testre szabhatók alapú egymásra épülő stíluslapok (CSS) használatával az Azure AD B2C által használt.
* Ha ez a funkció gyakorló ajánlott eljárások.

## <a name="the-page-ui-customization-feature"></a>A lap felhasználói felületének testreszabási funkcióról

Testre szabhatja a megjelenését és működését a felhasználói regisztrációt, bejelentkezést (lásd fent kivételekhez márkajelzési kapcsolatos megjegyzés), a jelszó-visszaállítás és a profil szerkesztése oldal (konfigurálásával [házirendek](active-directory-b2c-reference-policies.md)). Az ügyfelek zökkenőmentesen kaphat, ha a Navigálás az alkalmazás és az Azure AD B2C által üzemeltetett lapok között.

Ellentétben más szolgáltatások, amelyben a felhasználói felület lehetőségei az Azure AD B2C egy egyszerű, modern felhasználói felületének testreszabását megközelítést használ.

Íme a működési elv: Azure AD B2C az ügyfél böngészőjében kódja fut, és egy modern néven ismert megközelítés [eltérő eredetű erőforrások megosztása (CORS)](http://www.w3.org/TR/cors/).  Futásidőben a tartalom betöltése a házirendben megadott URL-címről. Különböző URL-eket különböző oldalain adhatja meg. Az URL-címről betöltött tartalmat egyesített fejlécelemhez egy HTML-részlet beszúrni az Azure AD B2C-ből, az oldal megjelenik el az ügyfelének. Ehhez szüksége van:

1. Hozzon létre egy üres tartalom megfelelően formázott HTML5 `<div id="api"></div>` elem található valahol a `<body>`. A elem jelek, ahol az Azure AD B2C-tartalom egészül ki.
1. A tartalmakat a HTTPS-végpont állomás (a CORS engedélyezve). Vegye figyelembe a LEKÉRÉSE is, és a beállítások kérési metódusainak engedélyezni kell a CORS konfigurálása során.
1. A CSS segítségével stílus, amely az Azure AD B2C-vel szúr be a felhasználói felületi elemeket.

### <a name="a-basic-example-of-customized-html"></a>Egy alapszintű példa testre szabott HTML

Az alábbi példa a legalapvetőbb HTML-tartalmakat, amellyel tesztelheti ezt a képességet. Használja a [segédeszköze](active-directory-b2c-reference-ui-customization-helper-tool.md) feltöltése és konfigurálása a tartalom az Azure Blob storage-be. Ezután ellenőrizheti, hogy az alapszintű, nem stilizált gombok & űrlapmezők minden oldalon megjelenített és megfelelően működik.

```HTML
<!DOCTYPE html>
<html>
    <head>
        <title>!Add your title here!</title>
    </head>
    <body>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
    </body>
</html>
```

## <a name="test-out-the-ui-customization-feature"></a>A felhasználói felület testreszabása a szolgáltatás kipróbálásához

Próbálja ki a felhasználói felület-testreszabási funkcióról a minta HTML és CSS-tartalom használatával szeretne?  Megadtuk, hogy [egy segédeszköze](active-directory-b2c-reference-ui-customization-helper-tool.md) , amely feltölti és mintatartalmakat próbálhat konfigurálja az Azure Blob storage.

> [!NOTE]
> A felhasználói felület tartalom bárhol üzemeltetheti: webkiszolgálók, CDN, az AWS S3, megosztott fájlrendszereket, és így tovább. Mindaddig, amíg a tartalom a CORS engedélyezése mellett egy nyilvánosan elérhető HTTPS-végpontokat is üzemel, akkor készen áll. Az Azure Blob storage csak szemléltető céllal használjuk.
>

## <a name="the-ui-fragments-embedded-by-azure-ad-b2c"></a>A felhasználói felület szilánkok embedded az Azure AD B2C által

Az alábbi szakaszok tartalmazzák a HTML5-alapú szilánkok, amely az Azure AD B2C-vel egyesíti a `<div id="api"></div>` elem található a tartalmat. **A HTML 5 tartalomban lévő töredékek nem beilleszteni.** Az Azure AD B2C-szolgáltatás beszúrja azokat az futásidőben. A saját alapú egymásra épülő stíluslapok (CSS) tervezésekor használja a töredékek referenciaként.

### <a name="fragment-inserted-into-the-identity-provider-selection-page"></a>Beszúrja a "Identitásszolgáltató kiválasztása oldal" töredék

Ezen az oldalon megtalálhatja az Identitásszolgáltatók, amelyek a felhasználói regisztrálási vagy bejelentkezési során közül választhat. Ezekre a gombokra közé tartozik a közösségi Identitásszolgáltatók, például a Facebook és a Google + vagy helyi fiókot (e-mail-cím vagy a felhasználó neve alapján).

```HTML
<div id="api" data-name="IdpSelections">
    <div class="intro">
         <p>Sign up</p>
    </div>

    <div>
        <ul>
            <li>
                <button class="accountButton" id="FacebookExchange">Facebook</button>
            </li>
            <li>
                <button class="accountButton" id="GoogleExchange">Google+</button>
            </li>
            <li>
                <button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
            </li>
        </ul>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-local-account-sign-up-page"></a>Beszúrja a "helyi fiók regisztrálási oldala" töredék

Ezen a lapon egy e-mail-cím vagy felhasználónév alapján helyi fiók regisztrációs űrlapot tartalmaz. Az űrlap különböző bemeneti vezérlőelemekből, például a bemeneti szövegmezőt, bejegyzés mezőben, választógomb, egyszeri kiválasztásos legördülő listák és többszörös kiválasztási jelölőnégyzetet is tartalmazhat.

```HTML
<div id="api" data-name="SelfAsserted">
    <div class="intro">
        <p>Create your account by providing the following details</p>
    </div>

    <div id="attributeVerification">
        <div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
        <div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
        <div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
        <div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
        <div class="attr" id="attributeList">
            <ul>
                <li>
                    <div class="attrEntry validate">
                        <div>
                            <div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
                            <div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
                            <div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
                            <div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
                            <div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
                            <div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
                            <div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
                        </div>

                    <div class="helpText show">This information is required</div>
                        <label>Email</label>
                        <input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

                    <div class="buttons verify" claim_id="email">
                        <div id="email_ver_wait" class="working" style="display: none;"></div>
                            <label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
                            <input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
                            <button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
                            <button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
                            <button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
                            <button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
                            <button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
                        </div>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
                        <label>Enter password</label>
                        <input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ &quot; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"> This information is required</div>
                        <label>Reenter password</label>
                        <input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Name</label>
                        <input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Gender</label>
                        <input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
                        <label for="extension_Gender_F">Female</label>
                        <input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
                        <label for="extension_Gender_M">Male</label>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Loyalty number</label>
                        <input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>State</label>
                        <select class="dropdown_single" id="state">
                            <option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
                            <option value="WA">Washington</option>
                            <option value="NY">New York</option>
                            <option value="CA">California</option>
                        </select>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Zip code</label>
                        <input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
                    </div>
                </li>
            </ul>
        </div>
        <div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
    </div>
    <div class="verifying-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="verifying_blurb"></div>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-social-account-sign-up-page"></a>Beszúrja a "közösségi fiók regisztrálására szolgáló oldal" töredék

Ezen a lapon jelenhet meg, ha regisztrál egy közösségi identitásszolgáltatót például Facebook vagy a Google + a meglévő fiók használata.  Ha további információt kell gyűjteni a regisztrációs űrlap segítségével végfelhasználói olyan szolgál. Ez az oldal hasonlít a helyi fiók regisztrálási oldala (az előző szakaszban látható) a jelszó számbeviteli mezők kivételével.

### <a name="fragment-inserted-into-the-unified-sign-up-or-sign-in-page"></a>Az "egyesített regisztrálási vagy bejelentkezési oldal" beszúrta töredék

Ezen a lapon mind előfizetési, és jelentkezzen be az ügyfelek, például Facebook vagy a Google + vagy a helyi fiókok közösségi identitásszolgáltatókat használó kezeli.

```HTML
<div id="api" data-name="Unified">
        <div class="social" role="form">
               <div class="intro">
                       <h2>Sign in with your social account</h2>
               </div>
               <div class="options">
                       <div><button class="accountButton firstButton" id="MicrosoftAccountExchange" tabindex="1">msa</button></div>
                       <div><button class="accountButton" id="FacebookExchange" tabindex="1">fb</button></div>
               </div>
        </div>
        <div class="divider">
               <h2>OR</h2>
        </div>
        <div class="localAccount" role="form">
               <div class="intro">
                       <h2>Sign in with your existing account</h2>
               </div>
               <div class="error pageLevel" aria-hidden="true" style="display: none;">
                       <p role="alert"></p>
               </div>
               <div class="entry">
                       <div class="entry-item">
                               <label for="logonIdentifier">Email Address</label> 
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="email" id="logonIdentifier" name="LogonIdentifier" pattern="^[a-zA-Z0-9.!#$%&amp;’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" placeholder="LogonIdentifier" value="" tabindex="1">
                       </div>
                       <div class="entry-item">
                               <div class="password-label"> <label for="password">Password</label><a id="forgotPassword" tabindex="2">Forgot your password?</a></div>
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="password" id="password" name="Password" placeholder="Password" tabindex="1">
                       </div>
                       <div class="working"></div>
                       <div class="buttons"> <button id="next" tabindex="1">Sign in</button> </div>
               </div>
               <div class="divider">
                       <h2>OR</h2>
               </div>
               <div class="create">
                       <p>Don't have an account?<a id="createAccount" tabindex="1">Sign up now</a> </p>
               </div>
        </div>
</div>
```

### <a name="fragment-inserted-into-the-multi-factor-authentication-page"></a>Beszúrja a "multi-factor authentication-oldal" töredék

Ezen a lapon felhasználók regisztrálási vagy bejelentkezési alatt ellenőrizheti azok telefonszámok (a szöveges vagy szóbeli).

```HTML
<div id="api" data-name="Phonefactor">
    <div id="phonefactor_initial">
        <div class="intro">
            <p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
        </div>
        <div class="errorText" id="errorMessage" style="display:none"></div>
        <div class="phoneEntry" id="phoneEntry">
            <div class="phoneNumber">
                <select id="countryCode" style="display:inline-block">
                    <option value="+93">Afghanistan (+93)</option>
                    <!-- Not all country codes listed -->
                    <option value="+44">United Kingdom (+44)</option>
                    <option value="+1" selected="">United States (+1)</option>
                    <!-- Not all country codes listed -->
                </select>
            </div>
            <div class="phoneNumber">
                <input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
            </div>
        </div>
        <div id="codeVerification" style="display:none">
            <div>
                <p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
                <input type="text" id="verificationCode" placeholder="Verification code">
            </div>
        </div>
        <div class="buttons">
            <button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
            <button id="verifyPhone" style="display:inline-block">Call Me</button>
            <button id="cancel" style="display:inline-block">Cancel</button>
        </div>
    </div>
    <div class="dialing-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="dialing_blurb"></div><div id="dialing_number"></div>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-error-page"></a>Fragment szúr be a "hiba"lap

```HTML
<div id="api" class="error-page-content" data-name="GlobalException">
    <h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
    <div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
    <div class="error-page-messagedetails">
        <div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
        <div>Timestamp:2015-09-14 23:22:35Z</div>
        <div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
    </div>
</div>
```

## <a name="localizing-your-html-content"></a>A HTML-tartalom honosítása során

A HTML-tartalom honosítása két módon lehet. Egyik módja az, hogy kapcsolja be a [nyelvi testreszabás](active-directory-b2c-reference-language-customization.md). Ez a funkció lehetővé teszi, hogy az Azure AD B2C-vel továbbítja az Open ID Connect paraméter `ui-locales`, a végponthoz.  A webtartalom-kiszolgáló a paraméter használatával adja meg a testre szabott HTML-lapok nyelvspecifikus.

Azt is megteheti a használt területi beállítása alapján különböző helyekről származó tartalom kérheti le. A CORS-kompatibilis végponthoz az állíthat be egy mappaszerkezet gazdagép tartalom bizonyos nyelveken. Felhívjuk a megfelelőt, ha a helyettesítő karaktert tartalmazó értéket használja `{Culture:RFC5646}`.  Például tegyük fel, hogy ez az egyéni oldal URI-ja:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
Az oldal betöltése `fr`. Az oldal HTML és CSS tartalmat kér le, amikor azt a van lehetőség:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="things-to-remember-when-building-your-own-content"></a>Megjegyzendő tudnivalók a saját tartalmak készítése során

Ha azt tervezi, a lap felhasználói felület testreszabása funkció használatához, tekintse át az alábbi gyakorlati tanácsokat:

* Ne másolja az Azure AD B2C alapértelmezett, és kísérelje meg módosítani azt. Célszerű a HTML5-tartalmak előzmények összeállításához és alapértelmezett tartalmát használja hivatkozásként van listázva.
* Biztonsági okokból nem lehetővé tesszük, hogy bármilyen JavaScript felvenni a tartalom. Mire van szüksége a legtöbb beépített elérhetőnek kell lennie. Ha nem, használja a [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c) kérhet új funkciókat.
* Támogatott böngésző verziója esetén:
  * Az Internet Explorer 11., 10, a peremhálózati
  * Korlátozottan támogatja az Internet Explorer 9, 8
  * Google Chrome 42.0 vagy újabb verzió
  * Mozilla Firefox 38.0 vagy újabb verzió
* Győződjön meg arról, hogy nem tartalmaznak `<form>` , ez a POST műveletek az Azure AD B2C-ből az injektált HTML által generált zavarja a HTML-címkék.
