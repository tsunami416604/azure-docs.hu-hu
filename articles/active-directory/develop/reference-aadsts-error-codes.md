---
title: Azure AD-hitelesítés & engedélyezési hibakódok
description: Ismerje meg az Azure AD biztonságijogkivonat-szolgáltatás (STS) által visszaadott AADSTS hibakódokat.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/18/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1de85e2c075d343a72fcc9fd29c0358d0054350
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548135"
---
# <a name="azure-ad-authentication-and-authorization-error-codes"></a>Azure AD-hitelesítési és-engedélyezési hibakódok

Az Azure Active Directory (Azure AD) biztonsági jogkivonat-szolgáltatás (STS) által visszaadott AADSTS hibakódok információit keresi? Olvassa el ezt a dokumentumot a AADSTS hibák leírásának, javításának és egyes javasolt megkerülő megoldásoknak a megkereséséhez.

> [!NOTE]
> Ezek az információk előzetesek, és változhat a tartalmuk. Kérdése van, vagy nem találja, amit keres? Hozzon létre egy GitHub-problémát, vagy tekintse meg a [fejlesztők támogatási és támogatási lehetőségeit](active-directory-develop-help-support.md) , amelyekkel megismerheti a Súgó és támogatás egyéb lehetőségeit.
>
> Ez a dokumentáció a fejlesztői és a rendszergazdai útmutatóhoz készült, de az ügyfél soha nem használhatja. A hibakódok bármikor megváltozhatnak, hogy részletesebb hibaüzeneteket szolgáltasson, amelyek célja, hogy az alkalmazás kiépítése során segítsenek a fejlesztőnek. A szöveg-vagy hibakódokat használó alkalmazások az idő múlásával lesznek megszakítva.

## <a name="lookup-current-error-code-information"></a>Aktuális hibakód információinak keresése
A hibakódok és az üzenetek változhatnak.  A legfrissebb információkért tekintse meg a [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) oldalt a AADSTS-hibák leírásának, javításának és néhány javasolt megkerülő megoldás megkereséséhez.  

Keresse meg a visszaadott hibakód numerikus részét.  Ha például a "AADSTS16000" hibakódot kapta, akkor keressen rá [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) a "16000" kifejezésre.  Közvetlenül egy adott hibához is kapcsolódhat, ha a hibakódot hozzáadja a következő URL-címhez: [https://login.microsoftonline.com/error?code=16000](https://login.microsoftonline.com/error?code=16000).

## <a name="aadsts-error-codes"></a>AADSTS-hibakódok

| Hiba | Leírás |
|---|---|
| AADSTS16000 | SelectUserAccount – ez az Azure AD által kiváltott megszakítás, ami olyan felhasználói felületet eredményez, amely lehetővé teszi a felhasználó számára, hogy több érvényes SSO-munkamenet közül válasszon. Ez a hiba meglehetősen gyakori, és a `prompt=none` megadása esetén visszatérhet az alkalmazáshoz. |
| AADSTS16001 | UserAccountSelectionInvalid – ez a hiba akkor jelenik meg, ha a felhasználó egy olyan csempére kattint, amelyet a munkamenetben a logika elutasította. Ha aktiválódik, ez a hiba lehetővé teszi a felhasználó számára a helyreállítást a csempék/munkamenetek frissített listájából, vagy egy másik fiók kiválasztásával. Ez a hiba a kód hibája vagy a versenyhelyzet miatt fordulhat elő. |
| AADSTS16002 | AppSessionSelectionInvalid – az alkalmazás által megadott SID-követelmény nem teljesült.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant – azt jelzi, hogy a felhasználó nem lett explicit módon hozzáadva a bérlőhöz. |
| AADSTS17003 | CredentialKeyProvisioningFailed – az Azure AD nem tudja kiépíteni a felhasználói kulcsot. |
| AADSTS20001 | WsFedSignInResponseError – probléma van az összevont identitás-szolgáltatóval. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS20012 | WsFedMessageInvalid – probléma van az összevont identitás-szolgáltatóval. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS20033 | FedMetadataInvalidTenantName – probléma van az összevont identitás-szolgáltatóval. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS40008 | OAuth2IdPUnretryableServerError – probléma van az összevont identitás-szolgáltatóval. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError – probléma van az összevont identitás-szolgáltatóval. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS40010 | OAuth2IdPRetryableServerError – probléma van az összevont identitás-szolgáltatóval. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError – probléma van az összevont identitás-szolgáltatóval. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS50000 | TokenIssuanceError – probléma van a bejelentkezési szolgáltatással. [Hozzon létre támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a probléma megoldásához. |
| AADSTS50001 | InvalidResource – az erőforrás le van tiltva vagy nem létezik. Ellenőrizze az alkalmazás kódjában, hogy megadta-e az elérni kívánt erőforrás URL-címét.  |
| AADSTS50002 | NotAllowedTenant – a bejelentkezés sikertelen volt, mert a bérlőn korlátozott a proxy hozzáférése. Ha saját bérlői szabályzata van, a probléma megoldásához módosíthatja a korlátozott bérlői beállításokat. |
| AADSTS50003 | MissingSigningKey – a bejelentkezés nem sikerült, mert hiányzik az aláíró kulcs vagy tanúsítvány. Ennek az lehet az oka, hogy az alkalmazásban nem volt konfigurálva aláíró kulcs. Tekintse meg a következő helyen ismertetett megoldásokat: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Ha továbbra is problémákat tapasztal, lépjen kapcsolatba az alkalmazás tulajdonosával vagy az alkalmazás rendszergazdájával. |
| AADSTS50005 | DevicePolicyError – a felhasználó olyan platformról próbált meg bejelentkezni egy eszközre, amelyet jelenleg nem támogat a feltételes hozzáférési házirend. |
| AADSTS50006 | Az InvalidSignature-aláírás ellenőrzése sikertelen volt, mert érvénytelen az aláírás. |
| AADSTS50007 | PartnerEncryptionCertificateMissing – a partner titkosítási tanúsítványa nem található ehhez az alkalmazáshoz. [Nyisson meg egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a Microsofttal a rögzített megoldás eléréséhez. |
| AADSTS50008 | InvalidSamlToken – az SAML-állítás hiányzik vagy helytelenül van konfigurálva a jogkivonatban. Forduljon az összevonási szolgáltatójához. |
| AADSTS50010 | AudienceUriValidationFailed – a célközönség URI-ellenőrzése nem sikerült az alkalmazáshoz, mert nem lettek konfigurálva jogkivonat-célközönségek. |
| AADSTS50011 | InvalidReplyTo – a válasz címe hiányzik, helytelenül van konfigurálva, vagy nem felel meg az alkalmazáshoz konfigurált válaszoknak.  A probléma megoldásához adja hozzá ezt a hiányzó választ a Azure Active Directory alkalmazáshoz, vagy ha valakinek van engedélye az alkalmazás kezeléséhez Active Directory tegye ezt meg.|
| AADSTS50012 | AuthenticationFailed – a következő okok egyike miatt sikertelen volt a hitelesítés:<ul><li>Az aláíró tanúsítvány tulajdonosának neve nincs engedélyezve</li><li>Nem található megfelelő megbízható szolgáltatói szabályzat a meghatalmazott tulajdonos nevéhez</li><li>A tanúsítványlánc érvénytelen.</li><li>Az aláíró tanúsítvány érvénytelen.</li><li>A házirend nincs konfigurálva a bérlőn</li><li>Az aláíró tanúsítvány ujjlenyomata nincs engedélyezve</li><li>Az ügyfél-érvényesítés érvénytelen aláírást tartalmaz</li></ul> |
| AADSTS50013 | A InvalidAssertion-állítás különböző okok miatt érvénytelen – a jogkivonat kiállítója nem felel meg az API-verziónak az érvényes időtartományon belül – a lejárt – hibásan formázott – frissítési jogkivonat az állításban nem elsődleges frissítési jogkivonat. |
| AADSTS50014 | GuestUserInPendingState – a felhasználó beváltási állapota függőben van. A vendég felhasználói fiók még nincs teljesen létrehozva. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState – a felhasználónak jogi korhatárt kell adnia. |
| AADSTS50017 | CertificateValidationFailed – a minősítés ellenőrzése nem sikerült, a következő okok miatt:<ul><li>A kiállítási tanúsítvány nem szerepel a megbízható tanúsítványok listájában</li><li>A várt CrlSegment nem található</li><li>A kiállítási tanúsítvány nem szerepel a megbízható tanúsítványok listájában</li><li>A különbözeti CRL terjesztési pontja megfelelő CRL terjesztési pont nélkül lett konfigurálva</li><li>Időtúllépési probléma miatt nem sikerült beolvasni az érvényes CRL-szegmenseket</li><li>A CRL letöltése nem sikerült</li></ul>Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50020 | UserUnauthorized – a felhasználók nem jogosultak a végpont meghívására. |
| AADSTS50027 | InvalidJwtToken – érvénytelen JWT-jogkivonat a következő okok miatt:<ul><li>nem tartalmazza az egyszeri kulcs jogcímét vagy a tárgy jogcímét</li><li>tárgyazonosító eltérése</li><li>dupla jogcím az idToken-jogcímekben</li><li>nem várt kiállító</li><li>nem várt célközönség</li><li>nem az érvényes időtartományon belül van </li><li>a jogkivonat formátuma nem megfelelő</li><li>A kiállító külső azonosító jogkivonata nem felelt meg az aláírás-ellenőrzésen.</li></ul> |
| AADSTS50029 | Érvénytelen URI – a tartománynév érvénytelen karaktert tartalmaz. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50032 | WeakRsaKey – azt jelzi, hogy a hibás felhasználó megpróbál-e gyenge RSA-kulcsot használni. |
| AADSTS50033 | RetryableError – az adatbázis-műveletekhez nem kapcsolódó átmeneti hibát jelez. |
| AADSTS50034 | UserAccountNotFound – az alkalmazásba való bejelentkezéshez a fiókot hozzá kell adni a címtárhoz. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt – a páros-azonosító létrehozásához szükséges só hiányzik az alapelve. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer – a tulajdonosi jogcímek nem egyeznek a kiállítói jogcímek nevével. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50049 | NoSuchInstanceForDiscovery – ismeretlen vagy érvénytelen példány. |
| AADSTS50050 | MalformedDiscoveryRequest – a kérelem helytelen formátumú. |
| AADSTS50053 | IdsLocked – a fiók zárolva van, mert a felhasználó túl sokszor próbált meg bejelentkezni egy helytelen felhasználói AZONOSÍTÓval vagy jelszóval. |
| AADSTS50055 | InvalidPasswordExpiredPassword – a jelszó lejárt. |
| AADSTS50056 | Érvénytelen vagy Null jelszó-a jelszó nem létezik a tárolóban ehhez a felhasználóhoz. |
| AADSTS50057 | UserDisabled – a felhasználói fiók le van tiltva. Egy rendszergazda letiltotta a fiókot. |
| AADSTS50058 | UserInformationNotProvided – ez azt jelenti, hogy a felhasználó nincs bejelentkezve. Ez egy gyakori hiba, amely akkor várható, ha egy felhasználó nem hitelesített, és még nincs bejelentkezve.</br>Ha ezt a hibát egy olyan SSO-környezet javasolja, amelyben a felhasználó korábban bejelentkezett, ez azt jelenti, hogy az SSO-munkamenet nem található vagy érvénytelen.</br>Ezt a hibát akkor lehet visszaadni az alkalmazásnak, ha a prompt = none érték van megadva. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided – a bérlők azonosítására szolgáló információ nem található a kérelemben vagy a megadott hitelesítő adatokban. A felhasználó felveheti a kapcsolatot a bérlő rendszergazdájával a probléma megoldásához. |
| AADSTS50061 | SignoutInvalidRequest – a kijelentkezési kérelem érvénytelen. |
| AADSTS50064 | CredentialAuthenticationError – a Felhasználónév vagy jelszó hitelesítő adatainak érvényesítése sikertelen volt. |
| AADSTS50068 | A SignoutInitiatorNotParticipant-kijelentkezés sikertelen volt. A kijelentkezés kezdeményező alkalmazás nem résztvevő az aktuális munkamenetben. |
| AADSTS50070 | A SignoutUnknownSessionIdentifier-kijelentkezés sikertelen volt. A kijelentkezés kérelem olyan nevet adott meg, amely nem felel meg a meglévő munkamenet (ek) nek. |
| AADSTS50071 | SignoutMessageExpired – a kijelentkezési kérelem lejárt. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt – a felhasználónak regisztrálnia kell a második tényezős hitelesítéshez (interaktív). |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt – erős hitelesítésre van szükség, és a felhasználó nem adta át az MFA-feladatot. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired – a rendszergazda által végrehajtott konfigurációs változás miatt, vagy ha új helyre költözött, a felhasználónak többtényezős hitelesítést kell használnia az erőforrás eléréséhez. Próbálkozzon újra egy új engedélyezési kéréssel az erőforráshoz. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired – a rendszergazda által végrehajtott konfigurációs változás miatt, vagy mert a felhasználó új helyre költözött, a felhasználónak a többtényezős hitelesítést kell használnia. |
| AADSTS50085 | A frissítési jogkivonathoz közösségi identitásszolgáltatói bejelentkezés szükséges. A felhasználónak újra be kell jelentkeznie a felhasználónévvel és jelszóval |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError – a szolgáltatás átmenetileg nem érhető el. próbáld újra. |
| AADSTS50089 | A folyamat jogkivonata lejárt – a hitelesítés nem sikerült. Próbálkozzon újra a felhasználóval a Felhasználónév-jelszó megadásával. |
| AADSTS50097 | DeviceAuthenticationRequired-eszköz hitelesítésre van szükség. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized – a JWT aláírása érvénytelen. |
| AADSTS50105 | EntitlementGrantsNotFound – a bejelentkezett felhasználó nincs hozzárendelve szerepkörhöz a bejelentkezett alkalmazáshoz. Rendelje hozzá a felhasználót az alkalmazáshoz. További információ:[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role). |
| AADSTS50107 | InvalidRealmUri – a kért összevonási tartomány objektum nem létezik. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat – probléma a JWT fejléctel. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter – a jogcím-átalakítás érvénytelen bemeneti paramétert tartalmaz. Lépjen kapcsolatba a bérlő rendszergazdájával a szabályzat frissítése érdekében. |
| AADSTS50125 | A PasswordResetRegistrationRequiredInterrupt-bejelentkezés a jelszó-visszaállítási vagy a jelszó-regisztrációs bejegyzés miatt megszakadt. |
| AADSTS50126 | InvalidUserNameOrPassword – hiba történt a hitelesítő adatok érvényesítése esetén, mert érvénytelen Felhasználónév vagy jelszó van. |
| AADSTS50127 | BrokerAppNotInstalled – a felhasználónak telepítenie kell egy közvetítő alkalmazást, hogy hozzáférjen ehhez a tartalomhoz. |
| AADSTS50128 | Érvénytelen tartománynév – a kérelemben vagy a megadott hitelesítő adatokban nem szereplő bérlő által azonosított információk nem találhatók. |
| AADSTS50129 | DeviceIsNotWorkplaceJoined – a munkahelyi csatlakoztatás szükséges az eszköz regisztrálásához. |
| AADSTS50131 | ConditionalAccessFailed – különböző feltételes hozzáférési hibákat jelez, például a hibás Windows-eszköz állapotát, a gyanús tevékenység, a hozzáférési szabályzat vagy a biztonsági házirend határozatai miatt blokkolt kéréseket. |
| AADSTS50132 | SsoArtifactInvalidOrExpired – a munkamenet a jelszó lejárata vagy a legutóbbi jelszó módosítása miatt nem érvényes. |
| AADSTS50133 | SsoArtifactRevoked – a munkamenet a jelszó lejárata vagy a legutóbbi jelszó módosítása miatt nem érvényes. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter – nem megfelelő adatközpont. Az OAuth 2,0-es eszköz folyamatában egy alkalmazás által kezdeményezett kérelem engedélyezéséhez az engedélyező félnek ugyanabban az adatközpontban kell lennie, ahol az eredeti kérelem található. |
| AADSTS50135 | PasswordChangeCompromisedPassword – a jelszó módosítása a fiók kockázata miatt szükséges. |
| AADSTS50136 | RedirectMsaSessionToApp – egy MSA-munkamenetet észlelt a rendszer. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken – a munkamenet a hiányzó külső frissítési jogkivonat miatt érvénytelen. |
| AADSTS50140 | KmsiInterrupt – ez a hiba a felhasználó bejelentkezésekor a "Bejelentkezés megtartása" megszakítás miatt történt. [Küldjön támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a korrelációs azonosítóval, a kérelemazonosítóval és a hibakóddal a részletek megismeréséhez. |
| AADSTS50143 | A munkamenet nem egyezik – a munkamenet érvénytelen, mert a felhasználói bérlő nem felel meg a tartománynak a különböző erőforrások miatti célzásnak. További részletekért  [Nyisson meg egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) KORRELÁCIÓs azonosítóval, kérelem-azonosítóval és hibakódtal. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword – a felhasználó Active Directory jelszavának érvényessége lejárt. Hozzon létre egy új jelszót a felhasználó számára, vagy használja a felhasználó az önkiszolgáló visszaállítási eszközt a jelszavának alaphelyzetbe állításához. |
| AADSTS50146 | MissingCustomSigningKey – az alkalmazásnak alkalmazásspecifikus aláíró kulccsal kell konfigurálnia. Az alkalmazás nem ilyennel van konfigurálva, vagy a kulcs lejárt vagy még nem érvényes. |
| AADSTS50147 | MissingCodeChallenge – a Code Challenge paraméter mérete érvénytelen. |
| AADSTS50155 | DeviceAuthenticationFailed – az eszköz hitelesítése nem sikerült ehhez a felhasználóhoz. |
| AADSTS50158 | ExternalSecurityChallenge – a külső biztonsági probléma nem teljesült. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration – a külső szolgáltató által eljuttatott jogcímek nem elegendőek vagy hiányoznak a külső szolgáltatóhoz kért jogcímek. |
| AADSTS50166 | ExternalClaimsProviderThrottled – nem sikerült elküldeni a kérést a jogcím-szolgáltatónak. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired – az ügyfél képes egyszeri bejelentkezéses jogkivonat beszerzésére a Windows 10-es fiókok bővítményen keresztül, de a jogkivonat nem található a kérelemben, vagy lejárt a megadott jogkivonat. |
| AADSTS50169 | InvalidRequestBadRealm – a tartomány nem az aktuális szolgáltatási névtér konfigurált tartománya. |
| AADSTS50170 | MissingExternalClaimsProviderMapping – a külső vezérlők leképezése hiányzik. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers – az átadó felhasználók nem támogatják a külső kihívásokat. |
| AADSTS50178 | A SessionControlNotSupportedForPassthroughUsers nem támogatja az áteresztő felhasználók számára a munkamenet-vezérlést. |
| AADSTS50180 | WindowsIntegratedAuthMissing – integrált Windows-hitelesítésre van szükség. Engedélyezze a Seamless SSO-t a bérlőn. |
| AADSTS50187 | DeviceInformationNotProvided – a szolgáltatás nem tudta végrehajtani az eszköz hitelesítését. |
| AADSTS50196 | LoopDetected – A rendszer egy ügyfél-hurkot észlelt. Ellenőrizze az alkalmazás logikáját, és győződjön meg arról, hogy a jogkivonat-gyorsítótárazás implementálva van, és hogy a hibákra vonatkozó feltételek megfelelően vannak kezelve.  Az alkalmazás túl sok olyan kérést hajtott végre túl rövid ideig, amely azt jelzi, hogy hibás állapotban van, vagy a visszaélésszerűen kéri a jogkivonatokat. |
| AADSTS50199 | CmsiInterrupt – felhasználói beavatkozás szükséges ehhez a hitelesítéshez.  Mivel ez egy "interaction_required" hiba, az ügyfélnek interaktív hitelesítést kell végeznie.  Ennek az az oka, hogy a rendszer webnézete egy natív alkalmazás jogkivonatának igénylésére lett felhasználva – a felhasználónak meg kell kérnie, hogy megkérdezze, valóban volt-e az alkalmazás, amelyet be kellett jelentkeznie.|
| AADSTS51000 | RequiredFeatureNotEnabled – a szolgáltatás le van tiltva. |
| AADSTS51001 | A DomainHintMustbePresent-tartományhoz a helyszíni biztonsági azonosítóval vagy a helyszíni egyszerű felhasználónévvel kell rendelkeznie. |
| AADSTS51004 | UserAccountNotInDirectory – a felhasználói fiók nem létezik a címtárban. |
| AADSTS51005 | TemporaryRedirect – egyenértékű a 307-as HTTP-állapottal, amely azt jelzi, hogy a kért információk a Location fejlécben megadott URI-n találhatók. Ha ezt az állapotot kapja, kövesse a válaszhoz tartozó Location fejlécet. Az eredeti kérelmezési módszer után az átirányított kérelem is a POST metódust fogja használni. |
| AADSTS51006 | ForceReauthDueToInsufficientAuth – integrált Windows-hitelesítésre van szükség. A felhasználó olyan munkamenet-token használatával jelentkezett be, amelyből hiányzik az integrált Windows-hitelesítési jogcím. Kérje meg a felhasználót, hogy jelentkezzen be újra. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn – a felhasználó nem kapott engedélyt a LinkedIn-erőforrásokhoz való hozzáférésre. |
| AADSTS53000 | DeviceNotCompliant – a feltételes hozzáférési szabályzatnak megfelelő eszközt kell megadnia, és az eszköz nem megfelelő. A felhasználónak regisztrálnia kell az eszközét egy jóváhagyott MDM-szolgáltatóval, például az Intune-nal. |
| AADSTS53001 | DeviceNotDomainJoined – a feltételes hozzáférési szabályzathoz tartományhoz csatlakoztatott eszköz szükséges, és az eszköz nincs tartományhoz csatlakoztatva. A felhasználó egy tartományhoz csatlakoztatott eszközt használ. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp – a használt alkalmazás nem jóváhagyott alkalmazás a feltételes hozzáféréshez. A felhasználónak az egyik alkalmazást kell használnia a jóváhagyott alkalmazások listájáról, hogy hozzáférjen a szolgáltatáshoz. |
| AADSTS53003 | A BlockedByConditionalAccess a feltételes hozzáférési házirendek letiltották a hozzáférést. A hozzáférési házirend nem engedélyezi a jogkivonatok kiadását. |
| AADSTS53004 | ProofUpBlockedDueToRisk – a tartalom elérése előtt a felhasználónak be kell fejeznie a multi-Factor Authentication regisztrációs folyamatát. A felhasználónak regisztrálnia kell többtényezős hitelesítésre. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist – a felhasználó vagy a rendszergazda nem egyezett bele az alkalmazás X AZONOSÍTÓval való használatára. küldjön egy interaktív engedélyezési kérést ehhez a felhasználóhoz és erőforráshoz. |
| AADSTS65004 | UserDeclinedConsent – a felhasználó beleegyezett az alkalmazás elérésére. Kérje meg a felhasználót, hogy jelentkezzen be újra, és hagyja jóvá az alkalmazást|
| AADSTS65005 | MisconfiguredApplication – az alkalmazáshoz szükséges erőforrás-hozzáférési lista nem tartalmazza az erőforrás által észlelt alkalmazásokat, vagy az ügyfélalkalmazás hozzáférést kért az erőforráshoz, amely nem lett megadva a szükséges erőforrás-hozzáférési listán vagy a Graph szolgáltatásban rosszul tért vissza. a kérelem vagy az erőforrás nem található. Ha az alkalmazás támogatja az SAML-t, előfordulhat, hogy az alkalmazást nem megfelelő azonosítóval (entitással) konfigurálta. Próbálja ki az SAML-lel kapcsolatos megoldásokat, amelyek a következő hivatkozáson találhatók: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav) |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant – a hitelesítés sikertelen volt. A frissítési jogkivonat érvénytelen. A hiba oka a következő lehet:<ul><li>A jogkivonat-kötési fejléc üres</li><li>A jogkivonat kötési kivonata nem egyezik</li></ul> |
| AADSTS70001 | UnauthorizedClient – az alkalmazás le van tiltva. |
| AADSTS70002 | InvalidClient – hiba történt a hitelesítő adatok érvényesítése során. A megadott client_secret nem felel meg az ügyfél várt értékének. Javítsa ki a client_secret, és próbálkozzon újra. További információ: [hozzáférési jogkivonat igénylése az engedélyezési kód használatával](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token). |
| AADSTS70003 | UnsupportedGrantType – az alkalmazás nem támogatott engedélyezési típust adott vissza. |
| AADSTS70004 | InvalidRedirectUri – az alkalmazás érvénytelen átirányítási URI-t adott vissza. Az ügyfél által megadott átirányítási cím nem egyezik meg egyik konfigurált címmel sem, illetve az OIDC jóváhagyási listán szereplő címekkel sem. |
| AADSTS70005 | UnsupportedResponseType – az alkalmazás a következő okok miatt nem támogatott választ adott vissza:<ul><li>a "token" típusú válasz nem engedélyezett az alkalmazáshoz</li><li>Az id_token típusú válaszhoz az OpenID hatókör szükséges – a kódolt wctx nem támogatott OAuth paraméterértéket tartalmaz</li></ul> |
| AADSTS70007 | UnsupportedResponseMode – az alkalmazás a token kérésekor `response_mode` nem támogatott értéket adott vissza.  |
| AADSTS70008 | ExpiredOrRevokedGrant – a frissítési token inaktivitás miatt lejárt. A tokent XXX-ben adták ki, és bizonyos ideig inaktív volt. |
| AADSTS70011 | InvalidScope – az alkalmazás által igényelt hatókör érvénytelen. |
| AADSTS70012 | MsaServerError – kiszolgálóhiba történt egy MSA (fogyasztói) felhasználó hitelesítése közben. próbáld újra. Ha továbbra is sikertelen, [Nyisson meg egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | AuthorizationPending – OAuth 2,0 – eszköz folyamatának hibája. Az engedélyezés függőben van. Az eszköz újra megpróbálja lekérdezni a kérést. |
| AADSTS70018 | BadVerificationCode – érvénytelen ellenőrző kód, mert a felhasználó helytelen felhasználói kódot adott meg az eszköz kódjának adatforgalmához. Az engedélyezés nincs jóváhagyva. |
| AADSTS70019 | CodeExpired – az ellenőrző kód lejárt. A felhasználó próbálja megismételni a bejelentkezést. |
| AADSTS75001 | BindingSerializationError – hiba történt az SAML-üzenetek kötése közben. |
| AADSTS75003 | UnsupportedBindingError – az alkalmazás a nem támogatott kötéshez kapcsolódó hibát adott vissza (az SAML protokoll válasza nem küldhető el a HTTP-POSTon kívüli kötéseken keresztül). |
| AADSTS75005 | Saml2MessageInvalid – az Azure AD nem támogatja az alkalmazás által az SSO-hoz továbbított SAML-kérelmet. |
| AADSTS75008 | RequestDeniedError – az alkalmazástól érkező kérést a rendszer megtagadta, mert az SAML-kérelem nem várt céllal volt. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims – az a hitelesítési módszer, amellyel a felhasználó által hitelesített hitelesítés nem felel meg a kért hitelesítési módszernek. |
| AADSTS75016 | A Saml2AuthenticationRequestInvalidNameIDPolicy-egy SAML2 hitelesítési kérelem érvénytelen NameIdPolicy rendelkezik. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable – a hitelesítési ügynök nem tud kapcsolódni a Active Directoryhoz. Győződjön meg arról, hogy az ügynök-kiszolgálók ugyanahhoz az AD-erdőhöz tartoznak, mint azok a felhasználók, akiknek a jelszavát érvényesíteni kell, és képesek csatlakozni a Active Directoryhoz. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout – a jelszó-érvényesítési kérelem időkorlátja lejárt. Győződjön meg arról, hogy a Active Directory elérhető, és válaszol az ügynökök kéréseire. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException – ismeretlen hiba történt a hitelesítési ügynök válaszának feldolgozása során. Próbálja megismételni a kérelmet. Ha a művelet továbbra is sikertelen, [Nyisson meg egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) , hogy további részleteket kapjon a hibáról. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem – a hitelesítési ügynök nem tudja érvényesíteni a felhasználó jelszavát. További információért tekintse meg az ügynök naplóit, és ellenőrizze, hogy a Active Directory a várt módon működik-e. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException – a hitelesítési ügynök nem tudja visszafejteni a jelszót. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours – a felhasználók az engedélyezett órákon kívül próbáltak bejelentkezni (ez az AD-ben van megadva). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew – a hitelesítési kísérletet nem lehetett befejezni, mert a hitelesítési ügynököt és az AD-t futtató gép közötti idő elferdíti az időeltérést. Javítsa az idő szinkronizálási problémáit. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated – a Kerberos-hitelesítés kísérlete sikertelen volt. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported – a hitelesítési csomag nem támogatott. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader – nem található engedélyezési fejléc. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn – a bérlő nincs engedélyezve a zökkenőmentes egyszeri bejelentkezéshez. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat – nem lehet érvényesíteni a felhasználó Kerberos-jegyét. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid – a zökkenőmentes egyszeri bejelentkezés sikertelen volt, mert a felhasználó Kerberos-jegye lejárt vagy érvénytelen. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed – a felhasználói objektum nem található a felhasználó Kerberos-jegyében található információk alapján. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn – az Azure AD-be bejelentkezni próbáló felhasználó különbözik az eszközbe bejelentkezett felhasználótól. |
| AADSTS90002 | InvalidTenantName – a bérlő neve nem található az adattárban. Győződjön meg arról, hogy megfelelő bérlői AZONOSÍTÓval rendelkezik. |
| AADSTS90004 | InvalidRequestFormat – a kérelem formátuma nem megfelelő. |
| AADSTS90005 | InvalidRequestWithMultipleRequirements – nem lehet befejezni a kérést. A kérelem érvénytelen, mert az azonosító és a bejelentkezési mutató nem használható együtt.  |
| AADSTS90006 | ExternalServerRetryableError – a szolgáltatás átmenetileg nem érhető el.|
| AADSTS90007 | InvalidSessionId – hibás kérés. Az átadott munkamenet-azonosító nem elemezhető. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission – a felhasználó vagy a rendszergazda nem egyezett bele az alkalmazás használatára. Legalább az alkalmazásnak az Azure AD-hez való hozzáférésre van szüksége a bejelentkezés és a felhasználói profil olvasása engedély megadásával. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier – az alkalmazás jogkivonatot kér magához. Ez a forgatókönyv csak akkor támogatott, ha a megadott erőforrás a GUID-alapú alkalmazás AZONOSÍTÓját használja. |
| AADSTS90010 | NotSupported – nem sikerült létrehozni az algoritmust. |
| AADSTS90012 | RequestTimeout – a kért időkorlát túllépte az időkorlátot. |
| AADSTS90013 | InvalidUserInput – a felhasználó által megadott bemenet érvénytelen. |
| AADSTS90014 | MissingRequiredField – ez a hibakód különböző esetekben jelenhet meg, ha egy várt mező nem szerepel a hitelesítő adatokban. |
| AADSTS90015 | QueryStringTooLong – a lekérdezési karakterlánc túl hosszú. |
| AADSTS90016 | MissingRequiredClaim – a hozzáférési jogkivonat érvénytelen. A szükséges jogcím hiányzik. |
| AADSTS90019 | MissingTenantRealm – az Azure AD nem tudta megállapítani a bérlő azonosítóját a kérelemből. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat – a egyszerű név formátuma érvénytelen, vagy nem felel meg a várt `name[/host][@realm]` formátumnak. Az egyszerű név megadása kötelező, a gazdagép és a tartomány nem kötelező, és lehet null értékű. |
| AADSTS90023 | InvalidRequest – a hitelesítési szolgáltatás kérése érvénytelen. |
| AADSTS9002313 | InvalidRequest – a kérelem helytelen formátumú vagy érvénytelen. – A probléma oka az, hogy hiba történt egy adott végpontra vonatkozó kérelemben. A probléma előfeltétele, hogy beszerezze a hiba Hegedűs-nyomkövetését, és megtekinti, hogy a kérelem formátuma megfelelő-e. |
| AADSTS90024 | RequestBudgetExceededError – átmeneti hiba történt. próbáld újra. |
| AADSTS90033 | MsodsServiceUnavailable – a Microsoft Online Directory szolgáltatás (MSODS) nem érhető el. |
| AADSTS90036 | MsodsServiceUnretryableFailure – váratlan, nem újrapróbálkozást lehetővé tevő hiba történt a MSODS által üzemeltetett WCF szolgáltatásban. [Küldjön támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a hiba részleteinek megismeréséhez |
| AADSTS90038 | NationalCloudTenantRedirection – a megadott "Y" bérlő az "X" nemzeti felhőhöz tartozik. A jelenlegi "Z" felhőalapú példány nem összevonása az X-szel. A rendszer Felhőbeli átirányítási hibát adott vissza. |
| AADSTS90043 | NationalCloudAuthCodeRedirection – a szolgáltatás le van tiltva. |
| AADSTS90051 | InvalidNationalCloudId – a nemzeti felhő azonosítója érvénytelen Felhőbeli azonosítót tartalmaz. |
| AADSTS90055 | TenantThrottlingError – túl sok bejövő kérelem van. Ez a kivétel a letiltott bérlők esetében fordul elő. |
| AADSTS90056 | BadResourceRequest – a hozzáférési token kódjának beváltásához az alkalmazásnak POST-kérést kell küldenie az `/token`-végpontnak. Ezt megelőzően meg kell adnia egy engedélyezési kódot, és el kell küldenie a POST kérelemben a `/token` végpontnak. Tekintse át ezt a cikket a OAuth 2,0-es engedélyezési kód folyamatának áttekintéséhez: [https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code). Irányítsa a felhasználót a `/authorize` végpontra, amely egy authorization_code ad vissza. A `/token`-végpontra irányuló kérelem elküldésekor a felhasználó megkapja a hozzáférési jogkivonatot. Jelentkezzen be a Azure Portalba, és ellenőrizze **Alkalmazásregisztrációk > végpontokat** , és győződjön meg arról, hogy a két végpont megfelelően lett konfigurálva. |
| AADSTS90072 | PassThroughUserMfaError – az a külső fiók, amelyre a felhasználó bejelentkezik, nem létezik azon a bérlőn, amelyre bejelentkezett. így a felhasználó nem tudja kielégíteni a bérlő MFA-követelményeit. Először hozzá kell adni a fiókot külső felhasználóként a bérlőhöz. Jelentkezzen ki, és jelentkezzen be egy másik Azure AD-felhasználói fiókkal. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid – hiba történt, amikor a szolgáltatás megkísérelte feldolgozni a WS-Federation üzenetet. Az üzenet érvénytelen. |
| AADSTS90082 | OrgIdWsFederationNotSupported – a kérelemhez megadott hitelesítési házirend jelenleg nem támogatott. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed – a vendég fiókjai nem engedélyezettek ehhez a helyhez. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed – a szolgáltatás nem tudja kiállítani a jogkivonatot, mert a vállalati objektum még nincs kiépítve. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired – a felhasználó DA jogkivonata lejárt. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed – hiba történt a WS-Federation üzenetnek az URI-ból való létrehozásakor. |
| AADSTS90090 | GraphRetryableError – a szolgáltatás átmenetileg nem érhető el. |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized – a kérelemhez tiltott hibakódtal visszaadott gráf. |
| AADSTS90094 | AdminConsentRequired – rendszergazdai engedély szükséges. |
| AADSTS900382 | A bizalmas ügyfél nem támogatott a felhőalapú kérelemben. |
| AADSTS90100 | InvalidRequestParameter – a paraméter üres vagy érvénytelen. |
| AADSTS901002 | AADSTS901002: a "Resource" kérési paraméter nem támogatott. |
| AADSTS90101 | InvalidEmailAddress – a megadott érték nem érvényes e-mail-cím. Az e-mail-címnek `someone@example.com`formátumúnak kell lennie. |
| AADSTS90102 | InvalidUriParameter – az értéknek érvényes abszolút URI-nak kell lennie. |
| AADSTS90107 | InvalidXml – a kérelem érvénytelen. Győződjön meg arról, hogy az adatai nem rendelkeznek érvénytelen karakterekkel.|
| AADSTS90114 | InvalidExpiryDate – a tömeges jogkivonat lejárati időbélyegzője lejárt jogkivonat kiosztását eredményezi. |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode – a felhasználói kód null értékű vagy üres.|
| AADSTS90120 | InvalidDeviceFlowRequest – a kérést már jóváhagyták vagy elutasították. |
| AADSTS90121 | InvalidEmptyRequest – érvénytelen üres kérelem.|
| AADSTS90123 | IdentityProviderAccessDenied – a jogkivonat nem állítható ki, mert az Identity vagy a jogcím-kiállítási szolgáltató megtagadta a kérést. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported – az erőforrás nem támogatott a `/common` vagy `/consumers` végpontokon. Ehelyett használja a `/organizations` vagy a bérlő-specifikus végpontot. |
| AADSTS90125 | DebugModeEnrollTenantNotFound – a felhasználó nincs a rendszeren. Győződjön meg arról, hogy helyesen adta meg a felhasználónevet. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred – a felhasználó típusa nem támogatott ezen a végponton. A rendszer nem következtetheti ki a felhasználó bérlőjét a felhasználónévből. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported – az alkalmazás nem támogatott a `/common` vagy `/consumers` végpontokon. Ehelyett használja a `/organizations` vagy a bérlő-specifikus végpontot. |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated – újrapróbálkozást lehetővé nem tevő hiba történt.|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound – a felhasználó rendszerbiztonsági tag nem rendelkezik a konfigurált NGC azonosító kulccsal. |
| AADSTS130005 | NgcInvalidSignature – az NGC kulcs aláírásának ellenőrzése sikertelen volt.|
| AADSTS130006 | NgcTransportKeyNotFound – az NGC szállítási kulcs nincs konfigurálva az eszközön. |
| AADSTS130007 | NgcDeviceIsDisabled – az eszköz le van tiltva. |
| AADSTS130008 | NgcDeviceIsNotFound – az NGC-kulcs által hivatkozott eszköz nem található. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce – a kérelem nem lett megadva. |
| AADSTS140001 | InvalidSessionKey – a munkamenetkulcs érvénytelen.|
| AADSTS165900 | InvalidApiRequest – érvénytelen kérés. |
| AADSTS220450 | UnsupportedAndroidWebViewVersion – a Chrome Webview verziója nem támogatott. |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource – az erőforrás nincs konfigurálva csak eszköz-tokenek fogadására. |
| AADSTS240001 | BulkAADJTokenUnauthorized – a felhasználónak nincs engedélye az eszközök regisztrálására az Azure AD-ben. |
| AADSTS240002 | RequiredClaimIsMissing – a id_token nem használható `urn:ietf:params:oauth:grant-type:jwt-bearer`-támogatásként.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy – a bérlői rendszergazda olyan biztonsági szabályzatot állított be, amely blokkolja ezt a kérelmet. Ellenőrizze a bérlő szintjén definiált biztonsági házirendeket annak megállapításához, hogy a kérés megfelel-e a szabályzat követelményeinek. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest – az alkalmazás nem található a címtárban/bérlőben. Ez akkor fordulhat elő, ha az alkalmazást nem a bérlő rendszergazdája telepítette, vagy a bérlő bármelyik felhasználója beleegyezett. Előfordulhat, hogy helytelenül konfigurálta az alkalmazás azonosító értékét, vagy nem a megfelelő bérlőnek küldi el a hitelesítési kérést. |
| AADSTS700020 | InteractionRequired – a hozzáférési engedélyhez interakció szükséges. |
| AADSTS700022 | InvalidMultipleResourcesScope – a bemeneti paraméter hatóköréhez megadott érték érvénytelen, mert egynél több erőforrást tartalmaz. |
| AADSTS700023 | InvalidResourcelessScope – a bemeneti paraméter hatóköréhez megadott érték érvénytelen, ha hozzáférési jogkivonatot kér. |
| AADSTS1000000 | UserNotBoundError – a kötési API megköveteli, hogy az Azure AD-felhasználó egy külső IDENTITÁSSZOLGÁLTATÓ is hitelesítse magát, ami még nem történt meg. |
| AADSTS1000002 | BindCompleteInterruptError – a kötés sikeresen befejeződött, de a felhasználót tájékoztatni kell. |
| AADSTS7000112 | UnauthorizedClientApplicationDisabled – az alkalmazás le van tiltva. |

## <a name="next-steps"></a>Következő lépések

* Kérdése van, vagy nem találja, amit keres? Hozzon létre egy GitHub-problémát, vagy tekintse meg a [fejlesztők támogatási és támogatási lehetőségeit](active-directory-develop-help-support.md) , amelyekkel megismerheti a Súgó és támogatás egyéb lehetőségeit.
