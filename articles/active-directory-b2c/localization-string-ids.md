---
title: A honosítás karakterlánc-azonosítók – Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg a tartalomdefiníció azonosítóját az Azure Active Directory B2C egy egyéni házirendek api.signuporsignin azonosítóval.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: cbc96f7f7c5f8020472708caaead3faff7a26673
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59609217"
---
# <a name="localization-string-ids"></a>A honosítás karakterlánc azonosítók

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **honosítási** elem lehetővé teszi, hogy több területi beállításokat, illetve azokat a nyelveket támogatja a felhasználói utak esetében a házirendben. Ez a cikk azonosítóját, amely a szabályzatban használhatja a honosítás listáját jeleníti meg. Felhasználói felület honosítása megismeréséhez, tekintse meg a [honosítási](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Regisztrálási vagy bejelentkezési oldal elemek

A következő azonosítóval használt azonosítójú tartalomdefiníció `api.signuporsignin`.

| ID (Azonosító) | Alapértelmezett érték |
| -- | ------------- |
| **local_intro_email** | Jelentkezzen be meglévő fiókjával |
| **logonIdentifier_email** | E-mail-cím |
| **requiredField_email** | Adja meg e-mail-címét |
| **invalid_email** | Adjon meg egy érvényes e-mail-címet |
| **email_pattern** | ^[a-zA-Z0-9.!#$%&’'*+/=?^_\`{\|}~-]+@[a-zA-Z0-9-]+(?:\\.[a-zA-Z0-9-]+)*$ |
| **local_intro_username** | Jelentkezzen be a felhasználónevével |
| **logonIdentifier_username** | Felhasználónév |
| **requiredField_username** | Adja meg a felhasználónevét |
| **jelszó** | Jelszó |
| **requiredField_password** | Írja be a jelszót |
| **invalid_password** | A beírt jelszó nem a várt formátumú. |
| **forgotpassword_link** | Elfelejtette jelszavát? |
| **createaccount_intro** | Még nincs fiókja? |
| **createaccount_link** | Regisztráljon most |
| **divider_title** | VAGY |
| **cancel_message** | A felhasználó elfelejtette a jelszavát |
| **button_signin** | Bejelentkezés |
| **social_intro** | Bejelentkezés a közösségi fiókjával |
  **remember_me** |Bejelentkezve szeretnék maradni|
| **unknown_error** | Merült fel a beléptetés folyamatban van. Később próbálja meg újra. |

Az alábbi példában látható használatának néhány a felhasználói felület elemei a regisztrálási vagy bejelentkezési oldal:

![Regisztrálási vagy bejelentkezési felhasználói felület elemei](./media/localization-string-ids/localization-susi.png)

Az Identitásszolgáltatók azonosítója van konfigurálva, a felhasználói interakciósorozatban szereplő **ClaimsExchange** elemet. Az identitásszolgáltató címe honosítani a **ElementType** értékre van állítva `ClaimsProvider`, amíg a **elemtípusú** azonosítója értékre van állítva a `ClaimsExchange`.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Az alábbi példa a Facebook-identitásszolgáltató arab localizes:

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

## <a name="sign-up-or-sign-in-error-messages"></a>Regisztrálási vagy bejelentkezési hibaüzenetek

| ID (Azonosító) | Alapértelmezett érték |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | A jelszó helytelen. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | Nem találjuk a fiókját. |
| **UserMessageIfOldPasswordUsed** | Valószínűleg régi jelszót írt be. |  
| **DefaultMessage** | Érvénytelen felhasználónév vagy jelszó. |  
| **UserMessageIfUserAccountDisabled** | A fiókja zárolva van. Lépjen kapcsolatba zárolását, majd próbálkozzon újra a támogatási szolgálathoz. |  
| **UserMessageIfUserAccountLocked** | Fiókja jogosulatlan használatának megakadályozása érdekében ideiglenesen zárolva van. Próbálkozzon újra később. |  
| **AADRequestsThrottled** | Jelenleg nincsenek a túl sok kérelmet. Várjon egy kis ideig, és próbálkozzon újra. |  

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Regisztráció és a saját kiszolgáló által megerősített oldalak felhasználói felület elemei

Az alábbiakban azonosítójú tartalomdefiníció azonosítóját `api.localaccountsignup` vagy bármely tartalomdefiníció kezdetű `api.selfasserted`, például `api.selfasserted.profileupdate` és `api.localaccountpasswordreset`.

| ID (Azonosító) | Alapértelmezett érték |
| -- | ------------- |
| **ver_sent** | Az ellenőrzőkód elküldve ide: |
| **ver_but_default** | Alapértelmezett |
| **cancel_message** | A felhasználó megszakította az önellenőrzött adatok megadása |
| **preloader_alt** | Kis türelmet... |
| **ver_but_send** | Ellenőrzőkód küldése |
| **alert_yes** | Igen |
| **error_fieldIncorrect** | Egy vagy több mező ki van töltve helytelen. Ellenőrizze a bejegyzéseket, és próbálkozzon újra. |
| **Év** | Év |
| **verifying_blurb** | Kis türelmet, amíg a rendszer feldolgozza az adatokat. |
| **button_cancel** | Mégse |
| **ver_fail_no_retry** | Túl sok helytelen kísérletek végrehajtott. Később próbálja meg újra. |
| **Hónap** | Hónap |
| **ver_success_msg** | Az e-mail cím ellenőrizve. Most már folytathatja. |
| **months** | január, február, március, április, május, június, július, augusztus, szeptember, október, november, december |
| **ver_fail_server** | Merült fel e-mail címének ellenőrzése. Adjon meg egy érvényes e-mail címet, és próbálkozzon újra. |
| **error_requiredFieldMissing** | Hiányzik egy kötelező mező. Az összes kötelező mezőt kitöltötte, és próbálkozzon újra. |
| **initial_intro** | Adja meg a következő adatokat. |
| **ver_but_resend** | Új kódot kérek |
| **button_continue** | Létrehozás |
| **error_passwordEntryMismatch** | A bejegyzés beírt jelszavak nem egyeznek. Mindkét mezőbe ugyanazt a jelszót írja be, és próbálkozzon újra. |
| **ver_incorrect_format** | Helytelen formátumú. |
| **ver_but_edit** | E-mail módosítása |
| **ver_but_verify** | Kód ellenőrzése |
| **alert_no** | Nem |
| **ver_info_msg** | Ellenőrző kódot a rendszer elküldte a Beérkezett üzenetek mappájába. Másolja az alábbi beviteli mezőbe. |
| **nap** | Nap |
| **ver_fail_throttled** | Ez az e-mail cím ellenőrzése túl sok kérelem törölték. Kérjük Várjon egy ideig, majd próbálkozzon újra. |
| **helplink_text** | Mi ez? |
| **ver_fail_retry** | A kód helytelen. Kérjük, próbálkozzon újból. |
| **alert_title** | A részletek megadásának megszakítása |
| **required_field** | Az adat megadása kötelező. |
| **alert_message** | Biztosan megszakítja az adatai megadását? |
| **ver_intro_msg** | Ellenőrzés szükség. Kattintson a Küldés gombra. |
| **ver_input** | Ellenőrzőkód |

## <a name="sign-up-and-self-asserted-pages-error-messages"></a>Regisztráció és a saját kiszolgáló által megerősített oldalak hibaüzenetek

| ID (Azonosító) | Alapértelmezett érték |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Már létezik a megadott Azonosítóval rendelkező felhasználó. Válasszon egy másikat. |
| **UserMessageIfClaimNotVerified** | Az igényt nem ellenőrzi: {0} |
| **UserMessageIfIncorrectPattern** | Helytelen mintája: {0} |
| **UserMessageIfMissingRequiredElement** | Hiányzó kötelező elem: {0} |
| **UserMessageIfValidationError** | Hiba történt a hitelesítés szerint: {0} |
| **UserMessageIfInvalidInput** | {0} Érvénytelen bemenet rendelkezik. |
| **ServiceThrottled** | Jelenleg nincsenek a túl sok kérelmet. Várjon egy kis ideig, és próbálkozzon újra. |

Az alábbi példa néhány felhasználói felületi elemek használatát a regisztrációs oldalon látható:

![Regisztráció felhasználói felület elemei](./media/localization-string-ids/localization-sign-up.png)

Az alábbi példa bemutatja néhány felhasználói felületi elemek használatát a regisztrációs oldalon található, miután a felhasználó rákattint a küldése ellenőrző kód gombra:

![A regisztrációs oldalra e-mail ellenőrző UX elemek](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Phone factor authentication lap felhasználói felület elemei

Az alábbiakban azonosítójú tartalomdefiníció azonosítóját `api.phonefactor`. 

| ID (Azonosító) | Alapértelmezett érték |
| -- | ------------- |
| **button_verify** | Hívjon fel |
| **country_code_label** | Országkód |
| **cancel_message** | A felhasználó megszakította a multi-factor authentication szolgáltatás |
| **text_button_send_second_code** | új kód küldése |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Nyilvántartásunkban a következő számot az Ön számára. A Microsoft küldjön egy kódot SMS-ben, vagy telefon a hitelesítéséhez. |
| **intro_mixed_p** | Nyilvántartásunkban a következő számokat az Ön számára. Válasszon egy számot, hogy a Microsoft telefon, vagy küldjön egy kódot SMS-ben történő hitelesítéséhez. |
| **button_verify_code** | Kód ellenőrzése |
| **requiredField_code** | Adja meg a kapott ellenőrzőkódot |
| **invalid_code** | Adja meg az általunk küldött 6 jegyű kódot |
| **button_cancel** | Mégse |
| **local_number_input_placeholder_text** | Telefonszám |
| **button_retry** | Retry |
| **alternative_text** | Nincs nálam a telefonom |
| **intro_phone_p** | Nyilvántartásunkban a következő számokat az Ön számára. Válassza ki a számát, azt is telefon a hitelesítéséhez. |
| **intro_phone** | Nyilvántartásunkban a következő számot az Ön számára. Mi lesz phone hitelesítéséhez. |
| **enter_code_text_intro** | Adja meg az ellenőrzőkódot alább, vagy  |
| **intro_entry_phone** | Adjon megy alább egy telefonszámot, amin felhívhatjuk a hitelesítéshez. |
| **intro_entry_sms** | Adjon meg alább egy telefonszámot, amelyre SMS-ben kódot küldhetünk a hitelesítéshez. |
| **button_send_code** | Kódot kérek |
| **invalid_number** | Érvényes telefonszámot adjon meg |
| **intro_sms** | Nyilvántartásunkban a következő számot az Ön számára. Küldünk egy kódot SMS-ben történő hitelesítéséhez. |
| **intro_entry_mixed** | Adjon meg alább egy telefonszámot, amelyre küldhetünk egy kódot SMS-ben, vagy amelyen felhívhatjuk a hitelesítéshez. |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |Nyilvántartásunkban a következő számokat az Ön számára. Válasszon egy számot, hogy is küldünk egy kódot SMS-ben történő hitelesítéséhez. |
| **requiredField_countryCode** | Válassza ki az országhívószámot |
| **requiredField_number** | Adja meg a telefonszámát |
| **country_code_input_placeholder_text** |Ország vagy régió |
| **number_label** | Telefonszám |
| **error_tryagain** | A megadott telefonszám foglalt vagy nem érhető el. Ellenőrizze a számot, és próbálkozzon újra. |
| **error_incorrect_code** | A megadott Ellenőrzőkód nem egyezik meg a nyilvántartásunknak. Próbálkozzon újra, vagy kérjen új kódot. |
| **countryList** | {\"Alapértelmezett\":\"ország/régió\",\"AF\":\"Afganisztán\",\"AX\":\"Aland Szigetek\",\"AL\":\"Albánia\",\"DZ\":\"Algéria\",\"AS\":\" Amerikai Szamoa\",\"AD\":\"részéről\",\"AO\":\"Angola\",\"AI\": \"Anguilla\",\"AQ\":\"Antarktisz\",\"rendelkezésre állási csoport\":\"Antigua és Barbuda\",\"AR\":\"Argentína\",\"AM\":\"Örményország\",\"AW\":\"Aruba \",\"AU\":\"Ausztrália\",\",\":\"Ausztria\",\" AZ\":\"Azerbajdzsán\",\"BS\":\"Bahama-szigetek\",\"BH\":\" Bahrein\",\"BD\":\"Banglades\",\"BB\":\"Barbados\",\" ÁLTAL\":\"Belarusz\",\"BE\":\"Belgium\",\"BZ\":\" Belize\",\"BJ\":\"Benin\",\"BM\":\"Bermudáról\",\"BT\":\"Bhután\",\"BO\":\"Bolívia\",\"BQ\":\" Bonaire\",\"BA\":\"Bosznia-Hercegovina\",\"BW\":\"Botswana<span class="notransla class=""></span class="notransla> Külbirtokai\",\"VI\":\"Egyesült Államok Virgin-szigetek\",\"UG\":\"Uganda\",\"UA\":\"Ukrajna\",\"AE\":\" Egyesült Arab Emírségek\",\"GB\":\"Nagy-Britannia\",\"USA\":\"Egyesült államokbeli\",\"UY \":\"Uruguay\",\"UZ\":\"Üzbegisztán\",\"je\":\"Vanuatu\", \"Sebezhetőség-Felmérési\":\"Vatikánváros\",\"VE\":\"Venezuela\",\"VN\":\"Vietnam \",\"WF\":\"Wallis és Futuna\",\"YE\":\"Jemen\",\"ZM\":\"Zambia\",\"ZW\":\"Zimbabwe\"} |
| **error_448** | A megadott telefonszám nem érhető el. |
| **error_449** | A felhasználó túllépte a megengedett újrapróbálkozások számát. |
| **verification_code_input_placeholder_text** | Ellenőrzőkód |

Az alábbi példában látható használatának néhány felhasználói felületi elemekre az MFA regisztrációs oldal:

![A regisztrációs oldalra e-mail ellenőrző UX elemek](./media/localization-string-ids/localization-mfa1.png)

Az alábbi példa néhány felhasználói felületi elemek használatát a többtényezős hitelesítés érvényesítés lapon látható:

![A regisztrációs oldalra e-mail ellenőrző UX elemek](./media/localization-string-ids/localization-mfa2.png)







