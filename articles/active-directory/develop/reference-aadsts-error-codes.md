---
title: Az Azure Active Directory hitelesítési és engedélyezési hibakódok |} A Microsoft Docs
description: Ismerje meg, hogy a rendszer adja vissza az Azure AD biztonsági jogkivonat-szolgáltatás (STS) AADSTS hibakódok.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/23/2019
ms.author: celested
ms.reviewer: hirsin, justhu
ms.custom: aaddev
ms.openlocfilehash: e550eeba9b154e271cdad9df5cdfe9a308efbbe9
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54820308"
---
# <a name="authentication-and-authorization-error-codes"></a>Hitelesítés és hitelesítési hibakódok

Az Azure Active Directory (Azure AD) biztonsági jogkivonat-szolgáltatás (STS) a visszaadott AADSTS hibakódok kapcsolatos keres? Olvassa el ebben a dokumentumban található AADSTS hibaleírások, javításokat és néhány javasolt megoldások.

> [!NOTE]
> Ezek az információk előzetesek, még változhatnak. Kérdése van, vagy nem találja, amit keres? Hozzon létre egy GitHub-problémát, vagy tekintse meg [fejlesztők támogatás és súgó lehetőségei](active-directory-develop-help-support.md) egyéb módjaival kapcsolatos kaphat, és támogatja.

## <a name="aadsts-error-codes"></a>AADSTS hibakódok

| Hiba | Leírás |
|---|---|
| AADSTS16000 | SelectUserAccount – Ez a felhasználói felület, amely lehetővé teszi a felhasználó számára több érvényes egyszeri bejelentkezési munkamenetek fájlnévben eredményez, amelyek az Azure AD által okozott megszakítást. Ez a hiba viszonylag gyakori, ha az alkalmazás visszaadott `prompt=none` van megadva. |
| AADSTS16001 | UserAccountSelectionInvalid - láthatja ezt a hibát, ha a felhasználó rákattint egy csempére, hogy a munkamenet válassza logika elutasította. Adatvezérelt, ez a hiba a felhasználó helyreállítása kiadási csempék-munkamenetből álló egy frissített listából, vagy egy másik fiók kiválasztásával. Ez a hiba akkor fordulhat elő, egy kód hibás vagy versenyt állapota miatt. |
| AADSTS16002 | AppSessionSelectionInvalid – az alkalmazás által megadott SID követelmény nem teljesült.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant - azt jelzi, hogy a felhasználó nincs explicit módon hozzáadva a bérlőben. |
| AADSTS17003 | CredentialKeyProvisioningFailed – Azure ad-ben nem sikerült kiépíteni a felhasználói kulcsot. |
| AADSTS20001 | WsFedSignInResponseError - az összevont identitásszolgáltatóval probléma van. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS20012 | WsFedMessageInvalid - az összevont identitásszolgáltatóval probléma van. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS20033 | FedMetadataInvalidTenantName - az összevont identitásszolgáltatóval probléma van. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS40008 | OAuth2IdPUnretryableServerError - az összevont identitásszolgáltatóval probléma van. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError - az összevont identitásszolgáltatóval probléma van. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS40010 | OAuth2IdPRetryableServerError - az összevont identitásszolgáltatóval probléma van. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError - az összevont identitásszolgáltatóval probléma van. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS50000 | TokenIssuanceError – a bejelentkezési szolgáltatással probléma van. [Hozzon létre támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a probléma megoldásához. |
| AADSTS50001 | Az erőforrás InvalidResource - le van tiltva, vagy nem létezik. Ellenőrizze az alkalmazás kódját, győződjön meg arról, hogy az elérni kívánt erőforrást pontos erőforrás URL-CÍMÉT adta.  |
| AADSTS50002 | NotAllowedTenant - bejelentkezési miatt nem sikerült egy korlátozott proxyn keresztüli elérés, a bérlő. Ha a saját bérlő-házirend, módosíthatja a probléma megoldásához a korlátozott bérlői beállításokat. |
| AADSTS50003 | MissingSigningKey - bejelentkezési miatt nem sikerült egy hiányzó aláíró kulcs vagy a tanúsítványt. Ez lehet, mivel nincs konfigurálva az alkalmazásban aláíró kulcs nem állt. Tekintse meg a következő helyen ismertetett megoldásokat: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Ha problémák továbbra is megjelenik, forduljon az alkalmazás tulajdonosa vagy az alkalmazás rendszergazdája. |
| AADSTS50005 | DevicePolicyError - felhasználó próbált eszköz egy platform, amely jelenleg nem támogatja a feltételes hozzáférési szabályzat segítségével történő bejelentkezést. |
| AADSTS50006 | InvalidSignature - aláírás-ellenőrzése nem sikerült, mert az érvénytelen aláírása. |
| AADSTS50007 | PartnerEncryptionCertificateMissing – a partner titkosítási tanúsítvány nem található ehhez az alkalmazáshoz. [Hozzon létre egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a Microsofttal a probléma kijavítva. |
| AADSTS50008 | InvalidSamlToken - SAML-előfeltétel nem található vagy a jogkivonat rosszul van konfigurálva. Forduljon az összevonási szolgáltatójához. |
| AADSTS50010 | AudienceUriValidationFailed - célközönség URI-t az alkalmazás érvényesítése sikertelen volt, mert nincs jogkivonat célközönség lettek konfigurálva. |
| AADSTS50011 | InvalidReplyTo – a válaszcím hiányzik, hibásan van konfigurálva, vagy nem felel meg az alkalmazáshoz konfigurált válaszcímekkel. Tekintse meg a következő helyen ismertetett megoldást: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Ha problémák továbbra is megjelenik, forduljon az alkalmazás tulajdonosa vagy alkalmazást rendszergazdaként. |
| AADSTS50012 | AuthenticationFailed - hitelesítés meghiúsult a következő okok valamelyike:<ul><li>Az aláíró tanúsítvány tulajdonosának neve nem engedélyezett.</li><li>Egy megfelelő megbízható hatóság házirend nem található a jogosult tulajdonos neve</li><li>A tanúsítványlánc nem érvényes</li><li>Az aláíró tanúsítvány nem érvényes</li><li>A házirend nincs konfigurálva, a bérlő:</li><li>Az aláíró tanúsítvány ujjlenyomata nem engedélyezett.</li><li>Ügyféltény érvénytelen aláírást tartalmaz</li></ul> |
| AADSTS50013 | InvalidAssertion – a különböző okok miatt érvénytelen helyességi feltétel – a jogkivonat kibocsátója nem felel meg az api-verzió a érvényes időn belül, tartomány - lejárt – hibás - frissítési jogkivonat a a helyességi feltétel nem egy elsődleges frissítési jogkivonatot. |
| AADSTS50014 | GuestUserInPendingState – a felhasználó érvényesítési függő állapotban van. A Vendég felhasználói fiókhoz nem teljes mértékben jön még. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState – a felhasználó jogi korcsoport csoport jóváhagyást igényel. |
| AADSTS50017 | CertificateValidationFailed - minősítési érvényesítése sikertelen, a következő okok miatt okok miatt:<ul><li>A kiállítási tanúsítvány nem szerepel a megbízható tanúsítványok listájában</li><li>A várt CrlSegment nem található</li><li>A kiállítási tanúsítvány nem szerepel a megbízható tanúsítványok listájában</li><li>A különbözeti CRL terjesztési pontja megfelelő CRL terjesztési pont nélkül lett konfigurálva</li><li>Nem sikerült beolvasni érvényes visszavonási lista szegmensek egy időtúllépés miatt</li><li>A CRL letöltése nem sikerült</li></ul>Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50020 | UserUnauthorized -, a felhasználók nem engedélyezett, ez a végpont meghívására. |
| AADSTS50027 | InvalidJwtToken – érvénytelen JWT jogkivonat a következő okok miatt:<ul><li>nem tartalmazza az egyszeri kulcs jogcímét vagy a tárgy jogcímét</li><li>tárgyazonosító eltérése</li><li>dupla jogcím az idToken-jogcímekben</li><li>nem várt kiállító</li><li>nem várt célközönség</li><li>nem az érvényes időtartományon belül van </li><li>a jogkivonat formátuma nem megfelelő</li><li>A kiállító külső azonosító jogkivonata nem felelt meg az aláírás-ellenőrzésen.</li></ul> |
| AADSTS50029 | Érvénytelen URI – a tartománynév érvénytelen karaktert tartalmaz. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50032 | WeakRsaKey - azt jelzi, hogy a hibás felhasználói kísérlet a gyenge RSA-kulcsok használata. |
| AADSTS50033 | RetryableError - azt jelzi, hogy egy átmeneti hiba nem kapcsolódik az adatbázis-műveletek. |
| AADSTS50034 | UserAccountNotFound – jelentkezzen be az alkalmazáshoz, hogy a fiókot hozzá kell adni a könyvtárhoz. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt - elő az páros azonosítónak a védőérték hiányzik a rendszerbiztonsági tag. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer - tulajdonos eltérést kibocsátó ügyféltény igényt. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50049 | NoSuchInstanceForDiscovery – ismeretlen vagy érvénytelen példány. |
| AADSTS50050 | MalformedDiscoveryRequest – a kérelem formátuma hibás. |
| AADSTS50053 | IdsLocked – a fiók zárolva van, mert a felhasználó próbált meg bejelentkezni túl sokszor helytelen felhasználói Azonosítóval vagy jelszóval. |
| AADSTS50055 | InvalidPasswordExpiredPassword – a jelszó lejárt. |
| AADSTS50056 | Érvénytelen vagy null értékű jelszó – jelszó nem létezik a tárolóban, a felhasználó számára. |
| AADSTS50057 | UserDisabled – a felhasználói fiók le van tiltva. Egy rendszergazda letiltotta a fiókot. |
| AADSTS50058 | UserInformationNotProvided – Ez azt jelenti, hogy a felhasználó nem jelentkezett be. Ez a gyakori hiba, amikor a felhasználó nincs hitelesítve, és nem jelentkezett még várt.</br>Ha ezt a hibát egy egyszeri Bejelentkezést a környezetben, ahol a felhasználó korábban bejelentkezett számára javasolt, ez azt jelenti, amely az egyszeri bejelentkezés munkamenet vagy nem található vagy érvénytelen volt.</br>Előfordulhat, hogy vissza ezt a hibát az alkalmazás, ha azonnali = nincs megadva. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided - bérlő-azonosítási adatok nem található vagy a kérelem vagy hallgatólagos által a megadott hitelesítő adatokat. A felhasználó kapcsolatba léphet a bérlői rendszergazdát, hogy a probléma megoldásához. |
| AADSTS50061 | SignoutInvalidRequest – a kijelentkezési kérés érvénytelen. |
| AADSTS50064 | CredentialAuthenticationError – felhasználónév vagy jelszó a hitelesítő adatok érvényesítése nem sikerült. |
| AADSTS50068 | Kijelentkezés SignoutInitiatorNotParticipant – nem sikerült. Az alkalmazás által kezdeményezett kijelentkezés nem a jelenlegi munkamenet résztvevő. |
| AADSTS50070 | Kijelentkezés SignoutUnknownSessionIdentifier – nem sikerült. A Kijelentkezés kérelemben megadott egy név segítségével azonosítja, amelyek nem feleltek meg a meglévő munkamenet. |
| AADSTS50071 | SignoutMessageExpired – a kijelentkezési kérés lejárt. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt - felhasználónak kell igényelnie (interaktív) kéttényezős hitelesítést. |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt – erős hitelesítésre szükség, és a felhasználó nem felelt meg az MFA-hitelesítést. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired - konfiguráció megváltozása miatt a rendszergazda által végzett, vagy helyezte át az új helyét, mert a felhasználó multi-factor Authentication hitelesítést kell használnia az erőforrás eléréséhez. Próbálkozzon újra egy új engedélyezés kérelmet az erőforráshoz. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired - konfiguráció megváltozása miatt a rendszergazdája által végzett, vagy mert a felhasználó egy új helyre áthelyezni, a felhasználó a multi-factor authentication szolgáltatás használatához szükséges. |
| AADSTS50085 | A frissítési jogkivonathoz közösségi identitásszolgáltatói bejelentkezés szükséges. A felhasználónak újra be kell jelentkeznie a felhasználónévvel és jelszóval |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError – a szolgáltatás nem átmenetileg nem érhető el. Próbálja meg újra. |
| AADSTS50089 | A folyamat jogkivonata lejárt – a hitelesítés nem sikerült. A felhasználónak, próbálja meg újra a felhasználónévvel történő bejelentkezéssel-jelszót. |
| AADSTS50097 | DeviceAuthenticationRequired - eszközhitelesítési szükség. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized – a JWT-aláírása érvénytelen. |
| AADSTS50105 | EntitlementGrantsNotFound – a bejelentkezett felhasználó nincs hozzárendelve egy szerepkörhöz az aláírt, az alkalmazás. Rendelje hozzá a felhasználót az alkalmazáshoz. További információ:[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role). |
| AADSTS50107 | InvalidRealmUri – a kért összevonási kezdőtartomány objektum nem létezik. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat - probléma a JWT-fejléc. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter - jogcímek átalakítását tartalmazza a bemeneti paraméter érvénytelen. Lépjen kapcsolatba a bérlő rendszergazdájával a szabályzat frissítése érdekében. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt – jelentkezzen be egy új jelszó kérése vagy jelszó-regisztrációs bejegyzés miatt megszakadt. |
| AADSTS50126 | InvalidUserNameOrPassword - hiba miatt érvénytelen felhasználónév vagy jelszó hitelesítő adatainak ellenőrzése. |
| AADSTS50127 | BrokerAppNotInstalled - felhasználónak kell telepíteni a közvetítő alkalmazás a tartalmakhoz való hozzáférést. |
| AADSTS50128 | Érvénytelen tartománynév - információ a bérlő-azonosító nem található vagy a kérelem, vagy hallgatólagos által a megadott hitelesítő adatokat. |
| AADSTS50129 | DeviceIsNotWorkplaceJoined - munkahelyi csatlakoztatás nem szükséges regisztrálni az eszközt. |
| AADSTS50131 | ConditionalAccessFailed - azt jelzi, hogy a különböző feltételes hozzáférési hibákban például rossz Windows eszköz állapotát, a kérés gyanús tevékenység, a hozzáférési szabályzat vagy a biztonsági házirendet érintő döntések miatt blokkolva. |
| AADSTS50132 | SsoArtifactInvalidOrExpired – a munkamenet érvénytelen nem lejárati dátumát vagy a legutóbbi jelszó módosítása miatt. |
| AADSTS50133 | SsoArtifactRevoked – a munkamenet érvénytelen nem lejárati dátumát vagy a legutóbbi jelszó módosítása miatt. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter – nem megfelelő adatközpontot. Az OAuth 2.0 eszköz flow-ban az alkalmazás által kezdeményezett kérelem engedélyezéséhez, ahol az eredeti kérés található ugyanabban az adatközpontban engedélyező fél kell lennie. |
| AADSTS50135 | PasswordChangeCompromisedPassword - a jelszó módosítása miatt a fiók kockázati szükség. |
| AADSTS50136 | RedirectMsaSessionToApp – önálló MSA-tevékenységet észlelt. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken – a munkamenet érvénytelen egy hiányzó külső frissítési jogkivonat miatt. |
| AADSTS50140 | KmsiInterrupt - e hiba történt "Bejelentkezve szeretnék maradni" megszakítás miatt a felhasználó volt a bejelentkezés. [Küldjön támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a korrelációs azonosítóval, a kérelemazonosítóval és a hibakóddal a részletek megismeréséhez. |
| AADSTS50143 | Munkamenet eltérő - munkamenet érvénytelen, mert felhasználói bérlő nem egyezik meg a tartomány mutató különböző erőforrás miatt.  [Hozzon létre egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a korrelációs Azonosítót, a kérelem azonosítója és a hiba kódja további információért. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword - felhasználó Active Directory jelszava lejárt. A felhasználó egy új jelszót létrehozni, vagy a felhasználó új jelszót kérnek az önkiszolgáló visszaállítási eszközzel rendelkezik. |
| AADSTS50146 | MissingCustomSigningKey – Ez az alkalmazás kötelező megadni egy alkalmazás-specifikus aláírási kulccsal kell konfigurálni. Az alkalmazás nem ilyennel van konfigurálva, vagy a kulcs lejárt vagy még nem érvényes. |
| AADSTS50147 | MissingCodeChallenge – az ellenőrző kód paraméter mérete nem érvényes. |
| AADSTS50155 | DeviceAuthenticationFailed - eszközhitelesítési ehhez a felhasználóhoz nem sikerült. |
| AADSTS50158 | Nem teljesült ExternalSecurityChallenge - külső biztonsági kérdést tettünk fel. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration - külső szolgáltató által küldött jogcímek nem elég vagy hiányzó jogcím kért külső szolgáltatóhoz. |
| AADSTS50166 | A kérést küldhet a jogcímszolgáltató ExternalClaimsProviderThrottled – nem sikerült. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired – az ügyfél nem képes a Windows 10-es fiókok bővítményével az SSO-jogkivonat beszerzése, de a jogkivonat nem található a kérelemben, vagy a megadott jogkivonat lejárt. |
| AADSTS50169 | InvalidRequestBadRealm – a tartomány nem az aktuális szolgáltatási névtér beállított tartomány. |
| AADSTS50170 | A külső vezérlők leképezési MissingExternalClaimsProviderMapping - hiányzik. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers - külső challenge csatlakoztatott felhasználók számára nem támogatott. |
| AADSTS50178 | Csatlakoztatott felhasználók SessionControlNotSupportedForPassthroughUsers - munkamenet-vezérlő nem támogatott. |
| AADSTS50180 | WindowsIntegratedAuthMissing – integrált Windows-hitelesítés szükséges. Engedélyezze a Seamless SSO-t a bérlőn. |
| AADSTS50187 | DeviceInformationNotProvided – a szolgáltatás nem tudta végrehajtani az eszközhitelesítés. |
| AADSTS51000 | RequiredFeatureNotEnabled – a szolgáltatás le van tiltva. |
| AADSTS51001 | DomainHintMustbePresent - tartományemlékeztető szerepelnie kell a helyszíni biztonsági azonosítót vagy a helyszíni egyszerű Felhasználónévvel. |
| AADSTS51004 | UserAccountNotInDirectory – a felhasználói fiókot a címtárban nem létezik. |
| AADSTS51005 | TemporaryRedirect - egyenértékű a HTTP-állapot, a 307, amely azt jelzi, hogy a kért adatokat az URI-t a helyfejléc megadott helyen található. Ha ez az állapot jelenik meg, hajtsa végre a társított a válasz location fejlécébe. Ha az eredeti kérelmi metódus volt a POST, az átirányított a kéréseket is használják a POST-metódus. |
| AADSTS51006 | ForceReauthDueToInsufficientAuth – integrált Windows-hitelesítés szükséges. A felhasználó bejelentkezett az integrált Windows-hitelesítés jogcím hiányzik egy munkamenet-jogkivonat használatával. A kérelem a felhasználónak újra be kell jelentkeznie. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn – a felhasználó nem adta a LinkedIn-erőforrásokhoz való hozzáféréshez. |
| AADSTS53000 | DeviceNotCompliant – feltételes hozzáférési szabályzat van szükség a megfelelő eszköz, és az eszköz nem megfelelő. A felhasználó regisztrálniuk kell az eszközt egy jóváhagyott, például az Intune MDM-szolgáltató. |
| AADSTS53001 | DeviceNotDomainJoined – feltételes hozzáférési szabályzat megköveteli egy tartományba léptetett eszköz, és az eszköz nem tartományhoz csatlakozó-e. Rendelkezik felhasználói használata egy tartományhoz csatlakoztatott eszköz. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp - használt nem áll egy feltételes hozzáféréshez jóváhagyott alkalmazáshoz. Felhasználói kell használnia a jóváhagyott alkalmazások listájáról az alkalmazások eléréséhez használhat. |
| AADSTS53003 | BlockedByConditionalAccess - hozzáférés blokkolta a feltételes hozzáférési szabályzatok. A hozzáférési házirend nem engedélyezi a kiállítási. |
| AADSTS53004 | ProofUpBlockedDueToRisk - felhasználónak kell, ez a tartalom elérése előtt a multi-factor authentication regisztrációs folyamat végrehajtásához. A felhasználónak regisztrálnia kell többtényezős hitelesítésre. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist - felhasználó vagy rendszergazda nem egyezett bele az alkalmazás azonosítója x küldjön egy interaktív engedélyezési kérést ehhez a felhasználóhoz és erőforráshoz használni. |
| AADSTS65004 | UserDeclinedConsent – a felhasználó elutasította az alkalmazás eléréséhez jóváhagyást. Kérje meg a felhasználót, hogy jelentkezzen be újra, és hagyja jóvá az alkalmazást|
| AADSTS65005 | MisconfiguredApplication – az alkalmazás szükséges erőforrás-hozzáférési lista nem tartalmazza az erőforrás által felderíthető alkalmazásokat, vagy az ügyfélalkalmazás-erőforrás, amely nincs megadva a szükséges erőforrás-hozzáférési listán, vagy a Graph szolgáltatás rossz visszaadott hozzáférést igényelt kérelem vagy az erőforrás nem található. Ha az alkalmazás támogatja az SAML, előfordulhat, hogy konfigurálta az alkalmazást a megfelelő azonosítóval (entitás). Próbálja ki az SAML-lel kapcsolatos megoldásokat, amelyek a következő hivatkozáson találhatók: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list) |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant - hitelesítés nem sikerült. A frissítési token nem érvényes. Hiba történt a következő okok miatt lehet:<ul><li>Token kötelező fejléc hiányzik.</li><li>Token kötés kivonata nem egyezik</li></ul> |
| AADSTS70001 | UnauthorizedClient – az alkalmazás le van tiltva. |
| AADSTS70002 | InvalidClient - hiba történt a hitelesítő adatok ellenőrzése. A megadott titkos ügyfélkódot az ügyfél számára nem egyezik a várt értékkel. Javítsa a titkos ügyfélkódot, és próbálkozzon újra. További információ: [engedélyezési kódot használva a hozzáférési jogkivonat kérése](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token). |
| AADSTS70003 | UnsupportedGrantType – az alkalmazás nem támogatott engedélyezési típust adott vissza. |
| AADSTS70004 | InvalidRedirectUri – az alkalmazás egy érvénytelen átirányítási URI-t adott vissza. Az ügyfél által megadott átirányítási cím nem egyezik meg egyik konfigurált címmel sem, illetve az OIDC jóváhagyási listán szereplő címekkel sem. |
| AADSTS70005 | UnsupportedResponseType – az alkalmazás a következő okok miatt nem támogatott a válasz típust adott vissza:<ul><li>az alkalmazás nincs engedélyezve a "token" választípus</li><li>Az id_token típusú válaszhoz az OpenID hatókör szükséges – a kódolt wctx nem támogatott OAuth paraméterértéket tartalmaz</li></ul> |
| AADSTS70007 | UnsupportedResponseMode – az alkalmazás a nem támogatott értéket adott vissza `response_mode` jogkivonat kérése során.  |
| AADSTS70008 | ExpiredOrRevokedGrant – a frissítési jogkivonat inaktivitás miatt lejárt. A jogkivonat XXX lett kiadva, és a egy bizonyos ideig inaktív volt. |
| AADSTS70011 | Az alkalmazás által kért hatókörrel InvalidScope – érvénytelen. |
| AADSTS70012 | MsaServerError – A kiszolgáló hitelesítése során hiba történt az MSA (felhasználói) felhasználó. Próbálja meg újra. Ha továbbra sem sikerül, [hozzon létre egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | AuthorizationPending – az OAuth 2.0 eszköz folyamat hiba. Engedélyezési függőben. Az eszköz ismét megkísérli a kérelem lekérdezési. |
| AADSTS70018 | BadVerificationCode – a felhasználó beírja a helytelen felhasználói kódban az eszköz kódfolyamat miatt érvénytelen ellenőrző kódot. Engedélyezési nem engedélyezett. |
| AADSTS70019 | CodeExpired - ellenőrző kód lejárt. Ismételje meg a bejelentkezéskor a felhasználó rendelkezik. |
| AADSTS75001 | BindingSerializationError - hiba történt a SAML-üzenet kötés során. |
| AADSTS75003 | UnsupportedBindingError - a az alkalmazás nem támogatott kötést kapcsolatos hibát adott vissza (SAML-protokoll válasz nem lehet elküldeni eltérő HTTP POST kötéseit keresztül). |
| AADSTS75005 | Saml2MessageInvalid – az Azure AD nem támogatja az egyszeri bejelentkezés az alkalmazás által küldött SAML-kérelmet. |
| AADSTS75008 | RequestDeniedError - az alkalmazásból a kérelem visszautasítva, mivel az SAML-kérelmet egy váratlan cél volt. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims – a hitelesítési módszer, amely szerint a szolgáltatással hitelesített felhasználó nem egyezik a kért hitelesítési módszert. |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy – egy SAML2 hitelesítési kérelem érvénytelen NameIdPolicy rendelkezik. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable – a hitelesítési ügynök nem tud csatlakoznia az Active Directoryhoz. Ellenőrizze, hogy az ügynök kiszolgálókat AD helykiszolgálóéval azonos a felhasználókat, amelyeknek a jelszava kell érvényesíteni tagjai, és azok tud csatlakozni az Active Directory. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout – jelszó-érvényesítési kérése túllépte az időkorlátot. Győződjön meg arról, hogy az Active Directory elérhető és válaszol a kérelmekre az ügynököktől származó. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException – ismeretlen hiba történt a hitelesítési ügynök érkező válasz feldolgozása közben. Ismételje meg a kérelmet. Ha továbbra sem sikerül, [hozzon létre egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) kattintva további részleteket jeleníthet meg a hibát. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem – a hitelesítési ügynök nem tudja ellenőrizni a felhasználó jelszavát. További információ az ügynök naplókat, és győződjön meg arról, hogy a várt módon működik-e az Active Directory. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException – a hitelesítési ügynök nem tudja visszafejteni a jelszót. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours – a felhasználó próbált meg bejelentkezni kívül esik a megengedett óra (ez van megadva az ad-ben). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew – a hitelesítési kísérlet után nem fejeződött be esedékes időt torzulása a hitelesítési ügynök és az AD futtató gép között. Javítsa ki az idő szinkronizálása a problémákat. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated - Kerberos authentication attempt failed. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported – a hitelesítési csomag nem támogatott. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader – nincs engedélyeztetési fejléc található. |
| AADSTS81007 | Közvetlen egyszeri bejelentkezés DesktopSsoTenantIsNotOptIn – a bérlő nincs engedélyezve. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat – nem sikerült érvényesíteni a felhasználó Kerberos-jegye. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid – közvetlen egyszeri bejelentkezés nem sikerült, mert a felhasználó Kerberos-jegye lejárt vagy érvénytelen. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed – nem található a felhasználói objektum a felhasználó Kerberos-jegye információi alapján. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn – a felhasználó megpróbál bejelentkezni az Azure AD különbözik az eszközbe jelentkezett felhasználótól. |
| AADSTS90002 | InvalidTenantName – a bérlő neve nem található az adattárban. Ellenőrizze, hogy rendelkezik a megfelelő bérlő azonosítója. |
| AADSTS90004 | InvalidRequestFormat – a kérelem nem megfelelő formátumú. |
| AADSTS90005 | InvalidRequestWithMultipleRequirements – nem sikerült végrehajtani a kérést. A kérés érvénytelen, mert a azonosító és a bejelentkezési mutató nem használható együtt.  |
| AADSTS90006 | ExternalServerRetryableError – a szolgáltatás nem átmenetileg nem érhető el.|
| AADSTS90007 | InvalidSessionId – hibás kérés. Az átadott munkamenet-azonosító nem elemezhető. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission - felhasználó vagy rendszergazda nem egyezett bele az alkalmazás. Legalább az alkalmazás Azure ad-ben hozzáférésre van szüksége a bejelentkezéshez adja meg, és Olvasás engedély a felhasználói profil. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier – az alkalmazás által kért jogkivonatot magának. Ezt a forgatókönyvet csak akkor, ha a megadott erőforrás használja a GUID-alapú alkalmazás azonosítóját. |
| AADSTS90010 | NotSupported – nem sikerült létrehozni az algoritmust. |
| AADSTS90012 | RequestTimeout – a kérés túllépte az időkorlátot. |
| AADSTS90013 | InvalidUserInput – a felhasználói bevitel érvénytelen nem. |
| AADSTS90014 | MissingRequiredField – erről a hibakódról jelenhetnek meg különböző esetek, amikor egy várt mezőt nem szerepel a hitelesítő adatokat. |
| AADSTS90015 | QueryStringTooLong – a lekérdezési karakterlánc neve túl hosszú. |
| AADSTS90016 | MissingRequiredClaim – a hozzáférési jogkivonat nem érvényes. A szükséges jogcím nem található. |
| AADSTS90019 | MissingTenantRealm - a az Azure AD nem tudta meghatározni a kérelemből a bérlőazonosító. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat – az egyszerű nevének formátuma nem érvényes, vagy nem felel meg a várt `name[/host][@realm]` formátumban. Az egyszerű felhasználónév megadása kötelező, a gazdagép és a tartomány megadása nem kötelező, és lehet null értékű. |
| AADSTS90023 | InvalidRequest |
| AADSTS90024 | RequestBudgetExceededError – átmeneti hiba történt. Próbálja meg újra. |
| AADSTS90033 | MsodsServiceUnavailable – nem érhető el a Microsoft Online Directory Service (MSODS). |
| AADSTS90036 | MsodsServiceUnretryableFailure - MSODS üzemelteti a WCF szolgáltatás váratlan, – Újrapróbálkozást lehetővé nem tevő hiba történt. [Küldjön támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a hiba részleteinek megismeréséhez |
| AADSTS90038 | NationalCloudTenantRedirection – a megadott tenant "Y" 'X' az országos Felhőbeli tartozik. Jelenlegi felhőalapú "Z" nem vonhat össze x példányt. Egy átirányítási hibát ad vissza. |
| AADSTS90043 | NationalCloudAuthCodeRedirection – a szolgáltatás le van tiltva. |
| AADSTS90051 | InvalidNationalCloudId – az országos felhőbeli azonosító egy érvénytelen felhő-azonosítót tartalmazza. |
| AADSTS90055 | TenantThrottlingError – nincsenek túl sok a bejövő kérelmeket. Ez a kivétel a letiltott bérlők számára. |
| AADSTS90056 | BadResourceRequest - Beváltásához hozzáférési tokent, az alkalmazás kódja egy POST kérelmet kell küldenie a `/token` végpont. Emellett ezen verzió előtt kell egy engedélyezési kód és a POST kérelemben küldje el a `/token` végpont. Ez a cikk az OAuth 2.0 hitelesítési kódfolyamat áttekintését lásd: [ https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code). A felhasználót a `/authorize` végpont, amely egy authorization_code adja vissza. Kérelem küldésével a `/token` végpont, a felhasználó élvezheti a hozzáférési jogkivonatot. Jelentkezzen be az Azure Portalon, és ellenőrizze **alkalmazásregisztrációk > végpontok** annak ellenőrzéséhez, hogy a két végpontot helyesen lettek-e konfigurálva. |
| AADSTS90072 | PassThroughUserMfaError – a külső fiókban, amely a felhasználó által a bejelentkezéshez nem létezik a bérlőn, azok bejelentkeztek; így a felhasználó nem tudja teljesíteni a bérlőt többtényezős hitelesítés követelményei. A fiókot először a bérlőn külső felhasználóként kell hozzáadni. Jelentkezzen ki, és jelentkezzen be egy másik Azure AD-felhasználói fiókot. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid - hiba történt a szolgáltatás a WS-Federation üzenet feldolgozása során. Az üzenet nem érvényes. |
| AADSTS90082 | OrgIdWsFederationNotSupported – a kiválasztott hitelesítési házirendet a kérelem jelenleg nem támogatott. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed - vendégfiókok ezen a helyen nem engedélyezett. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed – a szolgáltatás nem tud jogkivonatok kiállítása, mert a vállalati objektum még nem lett kiépítve. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired - The user DA token is expired. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed - hiba történt az URI-t a WS-Federation üzenetet hoz létre. |
| AADSTS90090 | GraphRetryableError – a szolgáltatás nem átmenetileg nem érhető el. |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized - grafikon a kéréshez a rendszer tiltott hibakódú adott vissza. |
| AADSTS90094 | AdminConsentRequired – a rendszergazdai jóváhagyást szükség. |
| AADSTS90100 | InvalidRequestParameter - paraméter üres vagy érvénytelen. |
| AADSTS90101 | InvalidEmailAddress – a megadott adatok nem egy érvényes e-mail címet. Az e-mail-cím a következő formátumban kell lennie `someone@example.com`. |
| AADSTS90102 | InvalidUriParameter - az érték egy érvényes abszolút URI Azonosítónak kell lennie. |
| AADSTS90107 | InvalidXml – a kérelem érvénytelen. Győződjön meg arról, hogy az adatok nem tartalmazhat érvénytelen karaktereket.|
| AADSTS90114 | A csoportos jogkivonat lejárati időbélyeg InvalidExpiryDate - miatt egy lejárt jogkivonatot kellene kibocsátani. |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode – a felhasználói kód null értékű vagy üres.|
| AADSTS90120 | InvalidDeviceFlowRequest – a kérelem már engedélyezett vagy próbálkozásai sikertelenek lesznek. |
| AADSTS90121 | InvalidEmptyRequest – üres kérelem érvénytelen.|
| AADSTS90123 | IdentityProviderAccessDenied – a jogkivonat nem adhatók ki, mert az identitás- vagy jogcímek kiállítási szolgáltató elutasította a kérést. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported – az erőforrás keresztül nem támogatott a `/common` vagy `/consumers` végpontok. Használja a `/organizations` vagy bérlőspecifikus végpont helyett. |
| AADSTS90125 | DebugModeEnrollTenantNotFound – a felhasználó nem a rendszerben. Győződjön meg róla, hogy a felhasználó nevét helyesen. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred – a felhasználó típusa nem támogatott ezen a végponton. A rendszer a felhasználónév a felhasználó bérlőjéhez nelze odvodit. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported – az alkalmazás használatával nem támogatott a `/common` vagy `/consumers` végpontok. Használja a `/organizations` vagy bérlőspecifikus végpont helyett. |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated - – Újrapróbálkozást lehetővé nem tevő hiba történt.|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound – a felhasználó rendszerbiztonsági tag nem rendelkezik a konfigurált NGC azonosító kulcsa. |
| AADSTS130005 | NgcInvalidSignature - NGC kulcs aláírás ellenőrzése sikertelen volt.|
| AADSTS130006 | NgcTransportKeyNotFound – a NGC átviteli kulcs az eszközön nincs konfigurálva. |
| AADSTS130007 | NgcDeviceIsDisabled – az eszköz le van tiltva. |
| AADSTS130008 | Az eszköz a NGC kulcs által hivatkozott NgcDeviceIsNotFound – nem található. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce - kérelmet egyszeri nincs megadva. |
| AADSTS140001 | InvalidSessionKey – a munkamenet-kulcs érvénytelen.|
| AADSTS165900 | InvalidApiRequest - kérelem érvénytelen. |
| AADSTS220450 | UnsupportedAndroidWebViewVersion – a Chrome WebView-verzió nem támogatott. |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource - erőforrás nincs konfigurálva csak jogkivonatokat fogadni. |
| AADSTS240001 | BulkAADJTokenUnauthorized – a felhasználó nem jogosult az eszközök regisztrálása az Azure ad-ben. |
| AADSTS240002 | RequiredClaimIsMissing – a id_token nem használható `urn:ietf:params:oauth:grant-type:jwt-bearer` megadása.|
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest – a címtárbérlőben/az alkalmazás nem található. Ez akkor fordulhat elő, ha az alkalmazás még nem a bérlő rendszergazdája telepítette vagy nem fogadta el a bérlő a egyetlen felhasználója sem. Előfordulhat, hogy van konfigurálva az azonosító értékét az alkalmazás vagy rossz bérlőhöz küldte a a hitelesítési kérést. |
| AADSTS700020 | A hozzáférési engedély InteractionRequired - műveletet igényel. |
| AADSTS700022 | InvalidMultipleResourcesScope – a bemeneti paraméter hatókör megadott értéke érvénytelen, mert egynél több erőforrást tartalmaz. |
| AADSTS700023 | InvalidResourcelessScope - a megadott érték érvénytelen bemeneti paraméter hatókörébe tartozó Ha kérelem egy hozzáférési jogkivonatot. |
| AADSTS1000000 | UserNotBoundError – a kötési API megköveteli az Azure AD-felhasználók is, amely még nem történt külső Identitásszolgáltató-hitelesítésre. |
| AADSTS1000002 | BindCompleteInterruptError – a kötési sikeresen befejeződött, de a felhasználónak kell tájékoztatni. |

## <a name="next-steps"></a>További lépések

* Kérdése van, vagy nem találja, amit keres? Hozzon létre egy GitHub-problémát, vagy tekintse meg [fejlesztők támogatás és súgó lehetőségei](active-directory-develop-help-support.md) egyéb módjaival kapcsolatos kaphat, és támogatja.
