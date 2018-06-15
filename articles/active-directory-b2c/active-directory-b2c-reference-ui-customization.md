---
title: Felhasználói felület (UI) testreszabása az Azure Active Directory B2C |} Microsoft Docs
description: A témakör a felhasználói felület (UI) testreszabási a szolgáltatások az Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 84734b89aeb95745d237c062e5654afc4d49f966
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711144"
---
# <a name="azure-active-directory-b2c-customize-the-azure-ad-b2c-user-interface-ui"></a>Az Azure Active Directory B2C: Testreszabása az Azure AD B2C felhasználói felület (UI)

Felhasználói élmény az alkalmazás elérhető az ügyfél kiemelkedő.  Az ügyfél alap nő a márka megjelenésének és arculatának felhasználói élmény létrehozásával. Az Azure Active Directory B2C (az Azure AD B2C) lehetővé teszi, hogy testre szabhatja a regisztráció, bejelentkezés, profil szerkesztése, és a jelszó-átállítási tartalmazó képpontos tökéletes vezérlő.

> [!NOTE]
> A jelen cikkben ismertetett lap felhasználói felületi testreszabási szolgáltatás nem vonatkozik a bejelentkezési egyetlen házirend, a hozzá tartozó jelszó alaphelyzetbe állítása lap és ellenőrzési e-maileket.  Ezek a funkciók használata a [vállalati arculat megjelenítése a szolgáltatás](../active-directory/customize-branding.md) helyette.
>
> Hasonlóképpen ha egy felhasználó intiates egy profil házirend módosítása *előtt* jelentkezik be, a felhasználó irányítja át egy oldal, amely segítségével testre szabható a [vállalati arculat megjelenítése a szolgáltatás](../active-directory/customize-branding.md).

Ez a cikk ismerteti a következő témaköröket:

* A lap felhasználói felületi testreszabási szolgáltatás.
* Egy eszköz a HTML-tartalmakat feltöltése az Azure Blob Storage a lap felhasználói felületének testreszabása szolgáltatással való használathoz.
* A testre szabható egymásra épülő stílus lapok (CSS) használata az Azure AD B2C által használt Kezelőfelület-elemek.
* Gyakorlati tanácsok gyakorló ezt a szolgáltatást.

## <a name="the-page-ui-customization-feature"></a>A lap felhasználói felületi testreszabási szolgáltatás

Testre szabhatja a megjelenésének és arculatának felhasználói regisztráció, bejelentkezés (lásd fent branding kapcsolódó kivételeket Megjegyzés), a jelszó alaphelyzetbe állítása és a profil szerkesztése lapok (konfigurálásával [házirendek](active-directory-b2c-reference-policies.md)). Az ügyfelek beolvasása zökkenőmentes élményt, amikor máshová az alkalmazás és az Azure AD B2C által kiszolgált lapok között.

Más szolgáltatás, amelyben felhasználói felület lehetőségei, az Azure AD B2C használ-e egy egyszerű és modern felhasználói felület testreszabásával megközelítése szemben.

Hogyan működik ez: Azure AD B2C az ügyfél böngészőjében kód fut, és a modern megközelítés nevű [Cross-Origin Resource Sharing (CORS)](http://www.w3.org/TR/cors/).  Tartalom futásidőben, be van töltve a házirendben megadott URL-címről. Másik URL-címéből különböző oldalain adhatja meg. Tartalom betöltődnek az URL-cím egy HTML-részlet szúrja be az Azure AD B2C egyesített, miután az ügyfél az oldal jelenik meg. Ehhez van szüksége:

1. Hozzon létre egy üres tartalom szabályos HTML5 `<div id="api"></div>` elem található valahol az `<body>`. A elem jelöli, ahol az Azure AD B2C tartalom egészül ki.
1. A tartalmat a HTTPS-végpontnak állomás (a CORS engedélyezett). Vegye figyelembe, mind az beszerzése és beállítások kérelem módszerek engedélyezni kell a CORS konfigurálása során.
1. CSS használatával az Azure AD B2C beszúrása felhasználói felületi elemeket stílus.

### <a name="a-basic-example-of-customized-html"></a>Egy alapszintű testreszabott HTML – példa

A következő példa egy a legalapvetőbb HTML-tartalmakat, amelyek segítségével tesztelheti ezt a képességet. Használja a [segédeszköze](active-directory-b2c-reference-ui-customization-helper-tool.md) feltöltése és konfigurálása az Azure Blob Storage tárolóban ezt a tartalmat. Ezután ellenőrizheti, hogy az egyszerű, nem stilizált gombok & űrlapmezők minden oldalon megjelenített és megfelelően működik.

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

## <a name="test-out-the-ui-customization-feature"></a>Tesztelje a felhasználói felület testreszabása szolgáltatás

Szeretné, hogy a minta HTML és CSS tartalom használatával a felhasználói felület testreszabása a szolgáltatás kipróbálásához?  Ön mellékelt [egy segédeszköze](active-directory-b2c-reference-ui-customization-helper-tool.md) , amely feltölti és minta tartalom konfigurálja az Azure Blob Storage tárolóban.

> [!NOTE]
> A felhasználói felület tartalom bárhol tárolhatja: a webkiszolgálók, a tartalomtovábbító, AWS S3, megosztási fájlrendszerek stb. Mindaddig, amíg a CORS engedélyezése mellett a tartalmak tárolása egy nyilvánosan elérhető HTTPS-végponton, akkor áll. Azure Blob Storage tárolóban csak szemléltető célokra használjuk.
>

## <a name="the-ui-fragments-embedded-by-azure-ad-b2c"></a>A felhasználói felület töredék Azure AD B2C beágyazott

Az alábbi szakaszok tartalmazzák a HTML5-töredék, amely az Azure AD B2C egyesíti a `<div id="api"></div>` elem található a tartalmat. **Ne jelölje be ezeket a töredékeket a HTML 5 tartalom.** Az Azure AD B2C szolgáltatás futásidőben beszúrja azokat. Használja referenciának ezeket a töredékeket, a saját egymásra épülő stílus lapok (CSS) tervezésekor.

### <a name="fragment-inserted-into-the-identity-provider-selection-page"></a>A "identitás szolgáltató kiválasztása lapon" beszúrt töredék

Ezen a lapon a regisztráció vagy bejelentkezés során a felhasználó választhat az identitás-szolgáltatóktól listáját tartalmazza. Az ilyen gombokat közé tartozik a közösségi Identitásszolgáltatók, például a Facebook-on és Google + és helyi fiókok (e-mail cím vagy a felhasználó neve alapján).

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

### <a name="fragment-inserted-into-the-local-account-sign-up-page"></a>A "helyi fiók előfizetési page" beszúrt töredék

Ez a lap tartalmaz egy e-mail címet vagy egy felhasználónevet alapuló helyi fiók regisztrációs űrlap. Az űrlap szöveg beviteli mezőt, a jelszó beviteli mezője, a választógomb, a egyetlen legördülő listák és a többszörös kiválasztási jelölőnégyzetet például különböző bemeneti vezérlőket tartalmazhat.

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

### <a name="fragment-inserted-into-the-social-account-sign-up-page"></a>A "" közösségi fiók bejelentkezési oldalán"beszúrt töredék

Ezen a lapon jelenhet meg, ha regisztrál a Facebook-on vagy a Google + például közösségi identitásszolgáltató egy meglévő fiókkal.  Ha további információkat kell gyűjteni a regisztrációs űrlap használatával a végfelhasználó használható. Ezen a lapon hasonlít a helyi fiók regisztrációs oldalon (az előző szakaszban látható) a jelszó számbeviteli mezők kivételével.

### <a name="fragment-inserted-into-the-unified-sign-up-or-sign-in-page"></a>A "egyesített regisztráció vagy bejelentkezés page" beszúrt töredék

Ezen a lapon a regisztrációs és bejelentkezés ügyfelek, akik használhatják a közösségi Identitásszolgáltatók, például a Facebook-on vagy a Google + és helyi fiókok kezeli.

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

### <a name="fragment-inserted-into-the-multi-factor-authentication-page"></a>A "multi-factor authentication-page" beszúrt töredék

Ezen a lapon felhasználók regisztráció vagy bejelentkezés során ellenőrizheti a telefonszámok (szöveges vagy hangos használatával).

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

### <a name="fragment-inserted-into-the-error-page"></a>A "" hibalap"beszúrt töredék

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

## <a name="localizing-your-html-content"></a>A HTML-tartalmakat azaz

Két módon lehet localize a HTML-tartalmakat. Egyirányú kapcsolva a [nyelvi testreszabási](active-directory-b2c-reference-language-customization.md). Ez a funkció lehetővé teszi, hogy az Azure AD B2C az Open ID Connect paraméter továbbítására `ui-locales`, a végpontnak.  A tartalomkiszolgáló Ez a paraméter használatával adja meg a testreszabott HTML-lapok nyelvspecifikus.

Azt is megteheti hogy is lekérni a tartalmat használt terület alapján különböző helyein. A CORS-kompatibilis végpont állíthat be egy mappastruktúrát állomás tartalom bizonyos nyelveken. A megfelelőt értesítjük telefonon, ha a helyettesítő karakteres érték használata `{Culture:RFC5646}`.  Tegyük fel például, ezt követően az egyéni oldalát URI:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
A lap betöltése `fr`. Az oldal HTML- és CSS tartalmat kéri le, amikor a rendszer húzza a:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="things-to-remember-when-building-your-own-content"></a>Megjegyzendő tudnivalók a saját tartalom felépítése közben

Ha azt tervezi, a lap felhasználói felületének testreszabása szolgáltatás használatához, tekintse át az alábbi gyakorlati tanácsokat:

* Nem az Azure AD B2C alapértelmezett tartalmát, és próbálja meg módosítani. Érdemes a HTML5 tartalom teljesen új összeállításához és alapértelmezett tartalom használni.
* Biztonsági okok miatt jelenleg nem engedélyezi, hogy bármely JavaScript szerepeljenek a tartalmat. A legtöbb mire van szüksége a kezdő verzióról elérhetőnek kell lennie. Ha nem, [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c) kérjen új funkciókat.
* A támogatott böngészőkben:
  * Az Internet Explorer 11, 10, a peremhálózati
  * Az Internet Explorer 9 böngészőben 8 korlátozott támogatása
  * Google Chrome 42.0 vagy újabb verzió
  * Mozilla Firefox 38.0 vagy újabb verzió
* Győződjön meg arról, hogy nem tartalmaz `<form>` , ez a POST műveletet, az Azure AD B2C injected HTML hozott létre zavarja a HTML-címkéket.
