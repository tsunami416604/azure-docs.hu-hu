---
title: Azure AD-hitelesítés & engedélyezési hibakódok
description: Ismerje meg az AADSTS hibakódokat, amelyek az Azure AD biztonsági jogkivonat-szolgáltatásból (STS) származnak.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 04/07/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 40a7406ea91c95daad2f180b9d0f4620cdbbf454
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875928"
---
# <a name="azure-ad-authentication-and-authorization-error-codes"></a>Azure AD hitelesítési és engedélyezési hibakódok

Az Azure Active Directory (Azure AD) biztonsági jogkivonat-szolgáltatásból (STS) visszaadott AADSTS-hibakódokról keres információt? Ebből a dokumentumból megtudhatja, hogy megtalálja az AADSTS-hibák leírását, javításait és néhány javasolt kerülő megoldást.

> [!NOTE]
> Ezek az információk előzetesek, és változhat a tartalmuk. Kérdése van, vagy nem találja, amit keres? Hozzon létre egy GitHub-problémát, vagy olvassa el [a Támogatási és súgólehetőségeket a fejlesztők számára,](active-directory-develop-help-support.md) hogy megismerje a segítség és támogatás egyéb lehetőségeit.
>
> Ez a dokumentáció fejlesztői és rendszergazdai útmutatáshoz szolgál, de az ügyfél nek soha nem szabad használnia. A hibakódok bármikor változhatnak annak érdekében, hogy részletesebb hibaüzeneteket adjanak, amelyek célja, hogy segítsék a fejlesztőt az alkalmazás létrehozása során. Azok az alkalmazások, amelyek a szöveg- vagy hibakódszámoktól függenek, idővel megszakadnak.

## <a name="lookup-current-error-code-information"></a>Az aktuális hibakód adatainak felkésése
A hibakódok és az üzenetek változhatnak.  A legfrissebb információkért tekintse meg az `https://login.microsoftonline.com/error` oldalt, és keresse meg az AADSTS hibaleírásokat, javításokat és néhány javasolt kerülő megoldást.  

Keresés a visszaadott hibakód numerikus részén.  Ha például az "AADSTS16000" hibakódot kapta, `https://login.microsoftonline.com/error` akkor keressen rá az "16000" kifejezésre.  Közvetlenül egy adott hibára is hivatkozhat, ha hozzáadja a hibakódszámot az URL-címhez: `https://login.microsoftonline.com/error?code=16000`.

## <a name="aadsts-error-codes"></a>AADSTS hibakódok

| Hiba | Leírás |
|---|---|
| AADSTS16000 | SelectUserAccount – Ez egy az Azure AD által dobott megszakítás, amely nek köszönhetően a felhasználói felület, amely lehetővé teszi a felhasználó számára, hogy több érvényes Egyszeri bejelentkezési munkamenet közül válasszon. Ez a hiba meglehetősen gyakori, és `prompt=none` visszakerülhet az alkalmazásba, ha meg van adva. |
| AADSTS16001 | UserAccountSelectionInvalid - Ez a hiba akkor jelenik meg, ha a felhasználó egy olyan csempére kattint, amelyet a munkamenet-választó logika elutasított. Amikor aktiválódik, ez a hiba lehetővé teszi a felhasználó számára, hogy helyreállítsa a csempék/munkamenetek frissített listájának kiválasztásával vagy egy másik fiók kiválasztásával. Ez a hiba kódhiba vagy versenyállapot miatt fordulhat elő. |
| AADSTS16002 | AppSessionSelectionInvalid – az alkalmazás által megadott SID-követelmény nem teljesült.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant – Azt jelzi, hogy a felhasználó nem lett kifejezetten hozzáadva a bérlőhöz. |
| AADSTS17003 | CredentialKeyIningFailed – az Azure AD nem tudja kiépíteni a felhasználói kulcsot. |
| AADSTS20001 | WsFedSignInResponseError – Probléma van az összevont identitásszolgáltatóval. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS20012 | WsFedMessageInvalid – Probléma van az összevont identitásszolgáltatóval. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS20033 | FedMetadataInvalidTenantName – Probléma van az összevont identitásszolgáltatóval. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS40008 | OAuth2IdPUnreretryableServerError – Probléma van az összevont identitásszolgáltatóval. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError – Probléma van az összevont identitásszolgáltatóval. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS40010 | OAuth2IdPRetryableServerError – Probléma van az összevont identitásszolgáltatóval. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError – Probléma van az összevont identitásszolgáltatóval. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS50000 | TokenIssuanceError – Probléma van a bejelentkezési szolgáltatással. [Hozzon létre támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a probléma megoldásához. |
| AADSTS50001 | InvalidResource – Az erőforrás le van tiltva, vagy nem létezik. Ellenőrizze az alkalmazás kódját, és győződjön meg arról, hogy megadta az elérni kívánt erőforrás pontos erőforrás-URL-címét.  |
| AADSTS50002 | NotAllowedTenant – A bejelentkezés sikertelen volt a bérlő korlátozott proxy-hozzáférése miatt. Ha a saját bérlői házirend, módosíthatja a korlátozott bérlői beállításokat a probléma megoldásához. |
| AADSTS50003 | MissingSigningKey – A bejelentkezés nem sikerült, mert hiányzik az aláíró kulcs vagy tanúsítvány. Ennek az lehet az oka, hogy nem volt beállítva aláíró kulcs az alkalmazásban. Tekintse meg a felbontást [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured)a programban. Ha továbbra is problémákat lát, forduljon az alkalmazás tulajdonosához vagy egy alkalmazásadminisztrátorhoz. |
| AADSTS50005 | DevicePolicyError – A felhasználó olyan platformról próbált meg bejelentkezni egy eszközre, amelyet jelenleg nem támogat a feltételes hozzáférési házirend. |
| AADSTS50006 | Érvénytelenaláírás – az aláírás ellenőrzése érvénytelen aláírás miatt nem sikerült. |
| AADSTS50007 | PartnerEncryptionCertificateMissing – A partnertitkosítási tanúsítvány nem található ehhez az alkalmazáshoz. [Nyisson meg egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a Microsofttal, hogy ezt megjavítsa. |
| AADSTS50008 | InvalidSamlToken – SAML-feltétel hiányzik vagy helytelenül konfigurált a jogkivonatban. Forduljon az összevonási szolgáltatójához. |
| AADSTS50010 | AudienceUriValidationedFailed – Az alkalmazás közönségURI-érvényesítése sikertelen, mivel nincs tokenközönség konfigurálva. |
| AADSTS50011 | InvalidReplyTo – A válaszcím hiányzik, helytelenül van konfigurálva, vagy nem egyezik meg az alkalmazáshoz konfigurált válaszcímekkel.  Megoldásként győződjön meg arról, hogy adja hozzá ezt a hiányzó válasz címet az Azure Active Directory-alkalmazás, vagy valaki az alkalmazás kezeléséhez az Active Directoryban ezt az Ön számára.|
| AADSTS50012 | AuthenticationFailed - A hitelesítés a következő okok valamelyike miatt nem sikerült:<ul><li>Az aláíró tanúsítvány tulajdonosneve nincs engedélyezve</li><li>Nem található egyező megbízható jogosultsági házirend az engedélyezett tulajdonosnevéhez</li><li>A tanúsítványlánc érvénytelen</li><li>Az aláíró tanúsítvány érvénytelen</li><li>A házirend nincs konfigurálva a bérlőn</li><li>Az aláíró tanúsítvány ujjlenyomata nem engedélyezett</li><li>Az ügyfélállítás érvénytelen aláírást tartalmaz</li></ul> |
| AADSTS50013 | InvalidAssertion - A helyességi feltétel különböző okok miatt érvénytelen – A jogkivonat-kibocsátó nem egyezik meg az API-verzió val az érvényes időtartományon belül -expired -mform - Refresh token a feltétel nem elsődleges frissítési jogkivonat. |
| AADSTS50014 | GuestUserInPendingState - A felhasználó beváltása függőben lévő állapotban van. A vendégfelhasználói fiók még nincs teljesen létrehozva. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState - A felhasználónak jogi korcsoport-beleegyezésre van szüksége. |
| AADSTS50017 | TanúsítványérvényesítésSikertelen – a hitelesítés ellenőrzése nem sikerült, a következő okok okai:<ul><li>A kiállítási tanúsítvány nem szerepel a megbízható tanúsítványok listájában</li><li>A várt CrlSegment nem található</li><li>A kiállítási tanúsítvány nem szerepel a megbízható tanúsítványok listájában</li><li>A különbözeti CRL terjesztési pontja megfelelő CRL terjesztési pont nélkül lett konfigurálva</li><li>Nem lehet beolvasni az érvényes visszavont tanúsítványok listájának szegmenseit időkérési probléma miatt</li><li>A CRL letöltése nem sikerült</li></ul>Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50020 | UserUnauthorized – A felhasználók nem hívhatják meg ezt a végpontot. |
| AADSTS50027 | InvalidJwtToken - Érvénytelen JWT-jogkivonat a következő okok miatt:<ul><li>nem tartalmazza az egyszeri kulcs jogcímét vagy a tárgy jogcímét</li><li>tárgyazonosító eltérése</li><li>dupla jogcím az idToken-jogcímekben</li><li>nem várt kiállító</li><li>nem várt célközönség</li><li>nem az érvényes időtartományon belül van </li><li>a jogkivonat formátuma nem megfelelő</li><li>A kiállító külső azonosító jogkivonata nem felelt meg az aláírás-ellenőrzésen.</li></ul> |
| AADSTS50029 | Érvénytelen URI – a tartománynév érvénytelen karaktert tartalmaz. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50032 | WeakRsaKey - Azt jelzi, hogy a felhasználó hibás anamzóba próbált használni. |
| AADSTS50033 | RetryableError - Az adatbázis-műveletekhez nem kapcsolódó átmeneti hibát jelez. |
| AADSTS50034 | UserAccountNotFound - Az alkalmazásba való bejelentkezéshez a fiókot hozzá kell adni a címtárhoz. |
| AADSTS50042 | UnableToGeneratepairIdentifierWithMissingSalt - Elvileg hiányzik a párazonosító létrehozásához szükséges só. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50043 | Nem LehetGeneratePairIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer – az ügyfélállításban a kibocsátói jogcím tárgyát képező eltérések. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50049 | NoSuchForDiscovery – Ismeretlen vagy érvénytelen példány. |
| AADSTS50050 | Hibás discoveryrequest – a kérelem hibásan formázott. |
| AADSTS50053 | IdsLocked - A fiók zárolva van, mert a felhasználó túl sokszor próbált meg helytelen felhasználói azonosítóval vagy jelszóval bejelentkezni. |
| AADSTS50055 | InvalidPasswordExpiredPassword – A jelszó lejárt. |
| AADSTS50056 | Érvénytelen vagy null jelszó -A jelszó nem létezik a felhasználó tárolójában. |
| AADSTS50057 | UserDisabled - A felhasználói fiók le van tiltva. Egy rendszergazda letiltotta a fiókot. |
| AADSTS50058 | UserInformationNotProvided - Ez azt jelenti, hogy a felhasználó nincs bejelentkezve. Ez egy gyakori hiba, amely akkor várható, ha a felhasználó nem hitelesített, és még nem jelentkezett be.</br>Ha ezt a hibát egy sso környezetben, ahol a felhasználó korábban bejelentkezett, ez azt jelenti, hogy az SSO-munkamenet vagy nem található, vagy érvénytelen.</br>Ez a hiba visszakerülhet az alkalmazásba, ha a prompt=none nincs megadva. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided – Bérlő-azonosító információ nem található sem a kérelemben, sem a megadott hitelesítő adatok hallgatólagos. A felhasználó kapcsolatba léphet a bérlői rendszergazdával a probléma megoldásához. |
| AADSTS50061 | SignoutInvalidRequest – A kijelentkezési kérelem érvénytelen. |
| AADSTS50064 | CredentialAuthenticationError - A hitelesítő adatok hitelesítése a felhasználónév vagy a jelszó sikertelen. |
| AADSTS50068 | SignoutInitiatorNotParticipant - A kijelentkezés nem sikerült. A kijelentkezést kezdeményező alkalmazás nem vesz részt az aktuális munkamenetben. |
| AADSTS50070 | SignoutUnknownSessionIdentifier - A kijelentkezés nem sikerült. A kijelentkezési kérelem olyan névazonosítót adott meg, amely nem egyezik meg a meglévő munkamenet(eke)vel. |
| AADSTS50071 | SignoutMessageExpired – A kijelentkezési kérelem lejárt. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt - A felhasználónak be kell iratkoznia a második tényezős hitelesítéshez (interaktív). |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt - Erős hitelesítés szükséges, és a felhasználó nem felelt meg az MFA-kihívásnak. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired - A rendszergazda által végrehajtott konfigurációs módosítás miatt, vagy azért, mert új helyre költözött, a felhasználónak többtényezős hitelesítést kell használnia az erőforrás eléréséhez. Próbálkozzon újra egy új engedélyezési kérelemmel az erőforráshoz. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired - A rendszergazda által végrehajtott konfigurációs módosítás miatt, vagy azért, mert a felhasználó új helyre költözött, a felhasználónak többtényezős hitelesítést kell használnia. |
| AADSTS50085 | A frissítési jogkivonathoz közösségi identitásszolgáltatói bejelentkezés szükséges. A felhasználónak újra be kell jelentkeznie a felhasználónévvel és jelszóval |
| AADSTS50086 | SasNonRetryableHiba |
| AADSTS50087 | SasRetryableError – A szolgáltatás átmenetileg nem érhető el. próbáld újra. |
| AADSTS50089 | A folyamat jogkivonata lejárt – a hitelesítés nem sikerült. A felhasználó próbáljon meg újra bejelentkezni a felhasználónévvel -jelszóval. |
| AADSTS50097 | DeviceAuthenticationRequired – Eszközhitelesítés szükséges. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized – A JWT-aláírás érvénytelen. |
| AADSTS50105 | EntitlementGrantsNotFound - A bejelentkezett felhasználó nincs hozzárendelve az aláírt alkalmazás szerepkörhöz. Rendelje hozzá a felhasználót az alkalmazáshoz. További információ:[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role). |
| AADSTS50107 | InvalidRealmUri – A kért összevonási birodalom objektum nem létezik. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat - Probléma a JWT fejlécével. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter - A jogcímek átalakítása érvénytelen bemeneti paramétert tartalmaz. Lépjen kapcsolatba a bérlő rendszergazdájával a szabályzat frissítése érdekében. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt - A bejelentkezés jelszó-visszaállítási vagy jelszóregisztrációs bejegyzés miatt megszakadt. |
| AADSTS50126 | InvalidUserNameOrPassword - Érvénytelen felhasználónév vagy jelszó miatt hiba történt a hitelesítő adatok érvényesítése. |
| AADSTS50127 | BrokerAppNotInstalled - A felhasználónak telepítenie kell egy brókeralkalmazást, hogy hozzáférjen ehhez a tartalomhoz. |
| AADSTS50128 | Érvénytelen tartománynév – Nincs bérlő-azonosító információ található sem a kérelemben, sem a megadott hitelesítő adatok hallgatólagos. |
| AADSTS50129 | DeviceIsNotWorkplaceJoined – Munkahelyi csatlakoztatás szükséges az eszköz regisztrálásához. |
| AADSTS50131 | ConditionalAccessFailed – Különböző feltételes hozzáférési hibákat jelez, például a Windows-eszköz állapotát, a gyanús tevékenység, a hozzáférési házirend vagy a biztonsági házirend döntései miatt blokkolt kérelmeket. |
| AADSTS50132 | SsoArtifactInvalidOrExpired – A munkamenet jelszólejárat vagy a legutóbbi jelszómódosítás miatt érvénytelen. |
| AADSTS50133 | SsoArtifactRevoked – A munkamenet jelszó lejárata vagy legutóbbi jelszómódosítása miatt érvénytelen. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter – Hibás adatközpont. Az OAuth 2.0 eszközfolyamategy alkalmazás által kezdeményezett kérelem engedélyezéséhez az engedélyező félnek ugyanabban az adatközpontban kell lennie, ahol az eredeti kérelem található. |
| AADSTS50135 | PasswordChangeCompromisedPassword – A fiók kockázata miatt jelszómódosításra van szükség. |
| AADSTS50136 | RedirectMsaSessionToApp - Egyetlen MSA-munkamenet észlelve. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken – A munkamenet egy hiányzó külső frissítési jogkivonat miatt érvénytelen. |
| AADSTS50140 | KmsiInterrupt – Ez a hiba a "Bejelentkezve vagyok" megszakítás miatt történt, amikor a felhasználó bejelentkezett. [Küldjön támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a korrelációs azonosítóval, a kérelemazonosítóval és a hibakóddal a részletek megismeréséhez. |
| AADSTS50143 | Munkamenet-eltérés – A munkamenet érvénytelen, mert a felhasználó bérlője nem egyezik meg a tartományi emlékeztetővel a különböző erőforrások miatt. További részletekért  [nyisson meg egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) korrelációs azonosítóval, kérelemazonosítóval és hibakóddal. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword – a felhasználó Active Directory-jelszava lejárt. Hozzon létre egy új jelszót a felhasználó számára, vagy a felhasználó használja az önkiszolgáló alaphelyzetbe állító eszközt a jelszó alaphelyzetbe állításához. |
| AADSTS50146 | MissingCustomSigningKey – Ezt az alkalmazást egy alkalmazásspecifikus aláíró kulccsal kell konfigurálni. Az alkalmazás nem ilyennel van konfigurálva, vagy a kulcs lejárt vagy még nem érvényes. |
| AADSTS50147 | MissingCodeChallenge - A kódkérdés paraméter mérete érvénytelen. |
| AADSTS50155 | DeviceAuthenticationFailed – az eszközhitelesítés nem sikerült a felhasználó számára. |
| AADSTS50158 | ExternalSecurityChallenge – A külső biztonsági kihívás nem teljesült. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration – A külső szolgáltató által küldött jogcímek nem elegendőek, vagy a hiányzó jogcím külső szolgáltatónak kért. |
| AADSTS50166 | ExternalClaimsProviderThrottled – Nem sikerült elküldeni a kérelmet a jogcímszolgáltatónak. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired - Az ügyfél képes beszerezni egy SSO-jogkivonatot a Windows 10-fiókok bővítményen keresztül, de a token nem található a kérelemben, vagy a megadott jogkivonat lejárt. |
| AADSTS50169 | InvalidRequestBadRealm – A birodalom nem az aktuális szolgáltatásnévtér konfigurált birodalma. |
| AADSTS50170 | MissingExternalClaimsProviderMapping – Hiányzik a külső vezérlők leképezése. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers – Külső kihívás nem támogatott az átmenő felhasználók számára. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers – A munkamenet-vezérlő nem támogatott az átmenő felhasználók számára. |
| AADSTS50180 | WindowsIntegratedAuthMissing - Integrált Windows-hitelesítésre van szükség. Engedélyezze a Seamless SSO-t a bérlőn. |
| AADSTS50187 | DeviceInformationNotProvided - A szolgáltatás nem tudta végrehajtani az eszközhitelesítést. |
| AADSTS50196 | LoopDetected - a rendszer ügyfélhurkot észlelt. Ellenőrizze az alkalmazás logikáját, és győződjön meg arról, hogy a jogkivonat-gyorsítótárazás megvalósult, és hogy a hibafeltételek megfelelően vannak-e kezelve.  Az alkalmazás túl sok ugyanazt a kérelmet túl rövid idő alatt, jelezve, hogy hibás állapotban van, vagy visszaélésszerűen kér jogkivonatokat. |
| AADSTS50197 | Ütköző identitások – a felhasználó nem található. Próbáljon meg újra bejelentkezni. |
| AADSTS50199 | CmsiInterrupt – Biztonsági okokból a kérelemhez felhasználói megerősítés szükséges.  Mivel ez egy "interaction_required" hiba, az ügyfélnek interaktív hitelesítést kell végeznie.  Ennek az az oka, hogy egy rendszer webnézetet használtak egy natív alkalmazás jogkivonatának kéréséhez – a felhasználót meg kell kérni, hogy kérdezze meg, hogy valójában ez-e az az alkalmazás, amelybe be akartak jelentkezni.|
| AADSTS51000 | RequiredFeatureNotEnabled - A szolgáltatás le van tiltva. |
| AADSTS51001 | DomainHintMustbePresent – A tartományemlékeztetőnek jelen kell lennie a helyszíni biztonsági azonosítóval vagy a helyszíni UPN-nel. |
| AADSTS51004 | UserAccountNotInDirectory - A felhasználói fiók nem létezik a címtárban. |
| AADSTS51005 | TemporaryRedirect – A HTTP 307-es állapotnak felel meg, ami azt jelzi, hogy a kért információ a helyfejlécben megadott URI-n található. Ha ezt az állapotot kapja, kövesse a válaszhoz társított helyfejlécet. Amikor az eredeti kérésmetódus POST volt, az átirányított kérelem is a POST metódust fogja használni. |
| AADSTS51006 | ForceReauthDueToInsufficientAuth - Integrált Windows-hitelesítésre van szükség. A felhasználó olyan munkamenet-jogkivonathasználatával jelentkezett be, amelyből hiányzik az integrált Windows hitelesítési jogcím. Kérje meg a felhasználót, hogy jelentkezzen be újra. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn - A felhasználó nem adott beleegyezést a LinkedIn-erőforrásokhoz való hozzáféréshez. |
| AADSTS53000 | DeviceNotCompliant – A feltételes hozzáférési szabályzat hoz létre egy megfelelő eszközt, és az eszköz nem megfelelő. A felhasználónak regisztrálnia kell az eszközt egy jóváhagyott MDM-szolgáltatónál, például az Intune-ban. |
| AADSTS53001 | DeviceNotDomainJoined – A feltételes hozzáférési házirendhez tartományhoz csatlakozó eszköz szükséges, és az eszköz nem csatlakozik tartományhoz. A felhasználó használjon tartományhoz csatlakozó eszközt. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp - A használt alkalmazás nem jóváhagyott alkalmazás feltételes hozzáféréshez. A felhasználónak a jóváhagyott alkalmazások listájából származó alkalmazások egyikét kell használnia a hozzáférés hez. |
| AADSTS53003 | BlockedByConditionalAccess – A feltételes hozzáférési házirendek letiltották a hozzáférést. A hozzáférési szabályzat nem engedélyezi a tokenkiállítást. |
| AADSTS53004 | ProofUpBlockedDueToRisk - A felhasználónak be kell fejeznie a többtényezős hitelesítésregisztrációs folyamatot a tartalom elérése előtt. A felhasználónak regisztrálnia kell többtényezős hitelesítésre. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist – A felhasználó vagy a rendszergazda nem járult hozzá az alkalmazás X azonosítóval való használatához. |
| AADSTS65004 | UserDeclinedConsent - A felhasználó nem járult hozzá az alkalmazás hoz való hozzáféréshez. Kérje meg a felhasználót, hogy jelentkezzen be újra, és hagyja jóvá az alkalmazást|
| AADSTS65005 | MisconfiguredApplication – Az alkalmazás által igényelt erőforrás-hozzáférési lista nem tartalmazza az erőforrás által felderíthető alkalmazásokat, vagy Az ügyfélalkalmazás hozzáférést kért az erőforráshoz, amely nem volt megadva a szükséges erőforrás-hozzáférési listában, vagy a Graph szolgáltatás rossz kérést adott vissza, vagy az erőforrás nem található. Ha az alkalmazás támogatja az SAML-t, előfordulhat, hogy az alkalmazást nem a megfelelő azonosítóval (entitással) konfigurálta. Próbálja ki az SAML-hez felsorolt felbontást az alábbi hivatkozássegítségével:[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav) |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant - A hitelesítés nem sikerült. A frissítési jogkivonat érvénytelen. A hiba oka a következő ok lehet:<ul><li>A tokenkötés fejléce üres</li><li>A tokenkötés kivonata nem egyezik</li></ul> |
| AADSTS70001 | JogosulatlanÜgyfél – Az alkalmazás le van tiltva. |
| AADSTS70002 | InvalidClient – a hitelesítő adatok érvényesítése miatt nem történt meg. A megadott client_secret nem egyezik meg az ügyfél várt értékével. Javítsa ki a client_secret, majd próbálkozzon újra. További információ: [Az engedélyezési kód használata hozzáférési jogkivonat kéréséhez.](v2-oauth2-auth-code-flow.md#request-an-access-token) |
| AADSTS70003 | UnsupportedGrantType – Az alkalmazás nem támogatott támogatási típust adott vissza. |
| AADSTS70004 | InvalidRedirectUri – Az alkalmazás érvénytelen átirányítási URI-t adott vissza. Az ügyfél által megadott átirányítási cím nem egyezik meg egyik konfigurált címmel sem, illetve az OIDC jóváhagyási listán szereplő címekkel sem. |
| AADSTS70005 | UnsupportedResponseType – Az alkalmazás a következő okok miatt nem támogatott választípust adott vissza:<ul><li>a "token" választípus nincs engedélyezve az alkalmazáshoz</li><li>Az id_token típusú válaszhoz az OpenID hatókör szükséges – a kódolt wctx nem támogatott OAuth paraméterértéket tartalmaz</li></ul> |
| AADSTS70007 | UnsupportedResponseMode – Az alkalmazás nem `response_mode` támogatott értéket adott vissza, amikor jogkivonatot kért.  |
| AADSTS70008 | ExpiredOrRevokedGrant – A frissítési jogkivonat inaktivitás miatt lejárt. A jogkivonat xxx-en lett kiadva, és egy bizonyos ideig inaktív volt. |
| AADSTS70011 | InvalidScope – Az alkalmazás által kért hatókör érvénytelen. |
| AADSTS70012 | MsaServerError - Kiszolgálóhiba történt egy MSA (consumer) felhasználó hitelesítése közben. próbáld újra. Ha továbbra is sikertelen, [nyisson meg egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | AuthorizationPending - OAuth 2.0 eszközfolyamat-hiba. Az engedélyezés függőben van. Az eszköz újra megpróbálja lekérdezi a kérelmet. |
| AADSTS70018 | BadVerificationCode - Érvénytelen ellenőrző kód, mivel a felhasználó rossz felhasználói kódot írt be az eszközkód-folyamathoz. Az engedély nincs jóváhagyva. |
| AADSTS70019 | Lejárt kód - Az ellenőrző kód lejárt. A felhasználó próbálkozzon újra a bejelentkezési. |
| AADSTS75001 | BindingSerializationError - Hiba történt az SAML-üzenetkötés során. |
| AADSTS75003 | Nem támogatott kötési hiba – Az alkalmazás nem támogatott kötéssel kapcsolatos hibát adott vissza (az SAML protokollválasz nem küldhető http post-tól eltérő kötéseken keresztül). |
| AADSTS75005 | Saml2MessageInvalid – Az Azure AD nem támogatja az alkalmazás által az egyszeri bejelentkezéshez küldött SAML-kérelmet. |
| AADSTS75008 | RequestDeniedError – Az alkalmazástól érkező kérés megtagadva, mivel az SAML-kérelem nek nem várt célja volt. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims – Az a hitelesítési módszer, amellyel a felhasználó a szolgáltatással hitelesített, nem felel meg a kért hitelesítési módszernek. |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy - SAML2 hitelesítési kérelem érvénytelen NameIdPolicy. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable – A hitelesítési ügynök nem tud csatlakozni az Active Directoryhoz. Győződjön meg arról, hogy az ügynökkiszolgálók ugyanannak az AD-erdőnek a tagjai, mint azoknak a felhasználóknak, akiknek a jelszavát ellenőrizni kell, és csatlakozni tudnak az Active Directoryhoz. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout – A jelszóérvényesítési kérelem idővel csökkent. Győződjön meg arról, hogy az Active Directory elérhető, és válaszol az ügynökök kéréseire. |
| AADSTS80005 | OnPremisePasswordValidatorKiszámíthatatlaneWebException – Ismeretlen hiba történt a hitelesítési ügynök válaszának feldolgozása közben. Próbálkozzon újra a kéréssel. Ha továbbra is sikertelen, [nyisson meg egy támogatási jegyet,](../fundamentals/active-directory-troubleshooting-support-howto.md) hogy további részleteket kapjon a hibáról. |
| AADSTS80007 | OnPremisePasswordValidatorErroroccurredOnPrem – A hitelesítési ügynök nem tudja érvényesíteni a felhasználó jelszavát. Ellenőrizze az ügynök naplók további információt, és ellenőrizze, hogy az Active Directory működik a várt módon. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException – A hitelesítési ügynök nem tudja visszafejteni a jelszót. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours - A felhasználók az engedélyezett órákon kívül próbáltak bejelentkezni (ez az AD-ben van megadva). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew – A hitelesítési kísérlet nem fejezhető be a hitelesítési ügynököt futtató gép és az AD közötti időeltérés miatt. Javítsa ki az időszinkronizálási problémákat. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated - A Kerberos hitelesítési kísérlet nem sikerült. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported - A hitelesítési csomag nem támogatott. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader – Nem található engedélyezési fejléc. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn – A bérlő nincs engedélyezve a zökkenőmentes egyszeri bejelentkezéshez. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat – Nem lehet érvényesíteni a felhasználó Kerberos-jegyét. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid – A zökkenőmentes egyszeri bejelentkezés nem sikerült, mert a felhasználó Kerberos-jegye lejárt vagy érvénytelen. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed – A felhasználó Kerberos-jegyében szereplő információk alapján nem található felhasználói objektum. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn – Az Azure AD-be bejelentkezni próbáló felhasználó eltér az eszközre bejelentkezett felhasználótól. |
| AADSTS90002 | InvalidTenantName – A bérlő neve nem található az adattárban. Ellenőrizze, hogy rendelkezik-e a megfelelő bérlői azonosítóval. |
| AADSTS90004 | InvalidRequestFormat – A kérelem nincs megfelelően formázva. |
| AADSTS90005 | InvalidRequestWithMultipleRequirements – Nem lehet befejezni a kérést. A kérelem nem érvényes, mert az azonosító és a bejelentkezési emlékeztető nem használható együtt.  |
| AADSTS90006 | ExternalServerRetryableError – A szolgáltatás átmenetileg nem érhető el.|
| AADSTS90007 | InvalidSessionId - Hibás kérés. Az átadott munkamenet-azonosító nem elemezhető. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission – A felhasználó vagy a rendszergazda nem járult hozzá az alkalmazás használatához. Az alkalmazás legalább hozzáférést igényel az Azure AD-hez a bejelentkezési és olvasási felhasználói profil engedély megadásával. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier – Az alkalmazás saját maga jogkivonatot kér. Ez a forgatókönyv csak akkor támogatott, ha a megadott erőforrás a GUID-alapú alkalmazásazonosítót használja. |
| AADSTS90010 | NotSupported - Nem lehet létrehozni az algoritmust. |
| AADSTS90012 | RequestTimeout – a kért időkérés. |
| AADSTS90013 | InvalidUserInput - A felhasználó tól származó bemenet érvénytelen. |
| AADSTS90014 | MissingRequiredField – Ez a hibakód különböző esetekben jelenhet meg, amikor a hitelesítő adatban nem szerepel egy várt mező. |
| AADSTS90015 | QueryStringTooLong – A lekérdezési karakterlánc túl hosszú. |
| AADSTS90016 | MissingRequiredClaim – A hozzáférési jogkivonat érvénytelen. A szükséges jogcím hiányzik. |
| AADSTS90019 | MissingTenantRealm – az Azure AD nem tudta meghatározni a bérlőazonosítót a kérelemből. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat - A rendszernévtörzs érvénytelen, vagy nem `name[/host][@realm]` felel meg a várt formátumnak. A főnév kötelező, az állomás és a birodalom nem kötelező, és null értékűlehet. |
| AADSTS90023 | InvalidRequest – A hitelesítési szolgáltatáskérelem érvénytelen. |
| AADSTS9002313 | InvalidRequest – A kérelem hibásan formázott vagy érvénytelen. - A probléma itt az, mert valami baj volt a kéréssel egy bizonyos végpontra. A javaslat, hogy ezt a kérdést, hogy kap egy hegedűs nyomon követni a hiba előforduló, és keresi, hogy ha a kérelem valóban megfelelően formázott, vagy sem. |
| AADSTS90024 | RequestBudgetExceededError - Átmeneti hiba történt. próbáld újra. |
| AADSTS90033 | MsodsServiceUnavailable – A Microsoft Online Címtárszolgáltatás (MSODS) nem érhető el. |
| AADSTS90036 | MsodsServiceUnretryableFailure – Váratlan, nem újrapróbálható hiba történt az MSODS által üzemeltetett WCF-szolgáltatásból. [Küldjön támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a hiba részleteinek megismeréséhez |
| AADSTS90038 | NationalCloudTenantRedirection - A megadott bérlő "Y" tartozik a National Cloud "X". A "Z" felhőpéldány nem egyeztet x-szel. A rendszer felhőátirányítási hibát ad vissza. |
| AADSTS90043 | NationalCloudAuthCodeRedirection - A funkció le van tiltva. |
| AADSTS90051 | InvalidNationalCloudId – A nemzeti felhőazonosító érvénytelen felhőazonosítót tartalmaz. |
| AADSTS90055 | TenantThrottlingError – Túl sok bejövő kérelmek. Ez a kivétel a letiltott bérlők számára történik. |
| AADSTS90056 | BadResourceRequest - A kód beváltásához egy hozzáférési jogkivonat, `/token` az alkalmazás nak kell küldenie egy POST-kérelmet a végpontra. Ezt megelőzően meg kell adnia egy engedélyezési kódot, és `/token` el kell küldenie azt a POST-kérelemben a végpontnak. Az OAuth 2.0 engedélyezési kód folyamatának áttekintését ebben a cikkben találja: [https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code). Irányítsa a `/authorize` felhasználót a végpontra, amely egy authorization_code ad vissza. A kérelem a `/token` végpontra, a felhasználó megkapja a hozzáférési jogkivonatot. Jelentkezzen be az Azure Portalon, és ellenőrizze **az alkalmazásregisztrációk > végpontok** győződjön meg arról, hogy a két végpont megfelelően konfigurálva van. |
| AADSTS90072 | PassThroughUserMfaError – Az a külső fiók, amelybe a felhasználó bejelentkezik, nem létezik azon a bérlőn, amelybe bejelentkeztek; így a felhasználó nem tudja kielégíteni a bérlő mfa-követelményeit. A fiókot először külső felhasználóként kell hozzáadni a bérlőhöz. Jelentkezzen ki, és jelentkezzen be egy másik Azure AD-felhasználói fiókkal. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid – Hiba történt, amikor a szolgáltatás ws-federation üzenetet próbált feldolgozni. Az üzenet érvénytelen. |
| AADSTS90082 | OrgIdWsFederationNotSupported – A kérelemhez kijelölt hitelesítési házirend jelenleg nem támogatott. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed - A vendégfiókok nem engedélyezettek ehhez a helyhez. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed – A szolgáltatás nem tud jogkivonatot kiadni, mert a vállalati objektum még nincs kiépítve. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired – A felhasználói DA token lejárt. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed – Hiba történt a WS-Federation üzenet létrehozása kor az URI-ból. |
| AADSTS90090 | GraphRetryableError – A szolgáltatás átmenetileg nem érhető el. |
| AADSTS90091 | GraphServiceNem érhető el |
| AADSTS90092 | GraphNonRetryableHiba |
| AADSTS90093 | GraphUserUnauthorized - Graph vissza a kérelem tiltott hibakódot. |
| AADSTS90094 | AdminConsentRequired – Rendszergazdai hozzájárulás szükséges. |
| AADSTS900382 | A bizalmas ügyfél nem támogatott a felhőközi kérelemben. |
| AADSTS90100 | InvalidRequestParameter - A paraméter üres vagy érvénytelen. |
| AADSTS901002 | AADSTS901002: Az "erőforrás" kérelem paraméter nem támogatott. |
| AADSTS90101 | InvalidEmailAddress - A megadott adatok nem érvényes e-mail cím. Az e-mail címnek `someone@example.com`formátumban kell lennie. |
| AADSTS90102 | InvalidUriParameter - Az értéknek érvényes abszolút URI-nak kell lennie. |
| AADSTS90107 | InvalidXml - A kérelem érvénytelen. Győződjön meg arról, hogy az adatok nem tartalmaznak érvénytelen karaktereket.|
| AADSTS90114 | InvalidExpiryDate – A tömeges jogkivonat lejárati időbélyege egy lejárt jogkivonat kiadását okozza. |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode – A felhasználói kód null értékű vagy üres.|
| AADSTS90120 | InvalidDeviceFlowRequest – A kérelem már engedélyezve van vagy elutasítva. |
| AADSTS90121 | InvalidEmptyRequest - Érvénytelen üres kérelem.|
| AADSTS90123 | IdentityProviderAccessDenied – A jogkivonat nem adható ki, mert az identitás- vagy jogcímkiállítási szolgáltató megtagadta a kérést. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported – Az erőforrás nem `/common` `/consumers` támogatott a vagy végpontok. Használja `/organizations` inkább a vagy bérlő-specifikus végpontot. |
| AADSTS90125 | DebugModeEnrollTenantNotFound - A felhasználó nincs a rendszerben. Ellenőrizze, hogy helyesen adta-e meg a felhasználónevet. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred – A felhasználói típus nem támogatott ezen a végponton. A rendszer nem következtet het a felhasználó bérlője a felhasználónévből. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported – Az alkalmazás nem `/common` `/consumers` támogatott a vagy végpontok. Használja `/organizations` inkább a vagy bérlő-specifikus végpontot. |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMembername |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated - Nem újrapróbálható hiba történt.|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordchangeADadminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound - A felhasználó egyszerű nem rendelkezik az NGC-azonosító kulcs konfigurálva. |
| AADSTS130005 | NgcInvalidSignature - NGC-kulcs aláírás ellenőrzése sikertelen.|
| AADSTS130006 | NgcTransportKeyNotFound - Az NGC átviteli kulcs nincs konfigurálva az eszközön. |
| AADSTS130007 | NgcDeviceIsDisabled – Az eszköz le van tiltva. |
| AADSTS130008 | NgcDeviceIsNotFound - Az NGC-kulcs által hivatkozott eszköz nem található. |
| AADSTS135010 | Kulcs nem található |
| AADSTS140000 | InvalidRequestNonce - A kérés nem érhető el. |
| AADSTS140001 | InvalidSessionKey – A munkamenetkulcs érvénytelen.|
| AADSTS165900 | InvalidApiRequest - Érvénytelen kérelem. |
| AADSTS220450 | UnsupportedAndroidWebViewVersion - A Chrome WebView verziója nem támogatott. |
| AADSTS220501 | InvalidCrlLetöltés |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource – Az erőforrás nincs konfigurálva csak eszközre szóló jogkivonatok fogadására. |
| AADSTS240001 | BulkAADJTokenUnauthorized – A felhasználó nem jogosult eszközök regisztrálására az Azure AD-ben. |
| AADSTS240002 | RequiredClaimIsMissing – a id_token nem `urn:ietf:params:oauth:grant-type:jwt-bearer` használható támogatásként.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy – A bérlői rendszergazda olyan biztonsági házirendet állított be, amely blokkolja ezt a kérést. Ellenőrizze a bérlői szinten meghatározott biztonsági házirendeket, és állapítsa meg, hogy a kérés megfelel-e a házirend-követelményeknek. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest – Az alkalmazás nem található a címtárban/bérlőben. Ez akkor fordulhat elő, ha az alkalmazást nem a bérlő rendszergazdája telepítette, vagy a bérlő bármely felhasználója hozzájárult hozzá. Előfordulhat, hogy helytelenül konfigurálta az alkalmazás azonosítóértékét, vagy nem a megfelelő bérlőnek küldte el a hitelesítési kérelmet. |
| AADSTS700020 | Interakciószükséges – a hozzáférési támogatás hoz szükség interakciót. |
| AADSTS700022 | InvalidMultipleResourcesScope – A bemeneti paraméter hatóköréhez megadott érték nem érvényes, mert egynél több erőforrást tartalmaz. |
| AADSTS700023 | InvalidResourcelessScope – A megadott érték a bemeneti paraméter hatóköre nem érvényes, ha egy hozzáférési jogkivonatot kér. |
| AADSTS700022| InvalidClientSecretExpiredKeysProvided – A megadott ügyféltitkos kulcsok lejárt. Látogasson el az Azure Portalra, és hozzon létre új kulcsokat az alkalmazáshoz, vagy fontolja meg a tanúsítványhitelesítő adatok használatát a nagyobb biztonság érdekében:https://aka.ms/certCreds |
| AADSTS700005 | InvalidGrantRedeemAgainstWrongTenant – Feltéve, hogy az engedélyezési kód más bérlővel szemben használható, így elutasítva. Az OAuth2 engedélyezési kódot ugyanazon a bérlőn kell beváltani, amelyhez beszerezték (/közös vagy /{tenant-ID} szükség szerint) |
| AADSTS1000000 | UserNotBoundError - A Bind API megköveteli, hogy az Azure AD-felhasználó is hitelesítse magát egy külső IDP, amely még nem történt meg. |
| AADSTS1000002 | BindCompleteInterruptError - A kötés sikeresen befejeződött, de a felhasználót tájékoztatni kell. |
| AADSTS7000112 | JogosulatlanClientApplicationDisabled – Az alkalmazás le van tiltva. |
| AADSTS7500529 | Az "SAMLId-Guid" érték nem érvényes SAML-azonosító – az Azure AD ezt az attribútumot használja a visszaadott válasz InResponseTo attribútumának feltöltéséhez. Az azonosító nem kezdődhet számmal, ezért a közös stratégia az, hogy egy karakterláncot, például az "id"-t a GUID karakterlánc-ábrázolására készíti elő. Például az id6c1c178c16d486687be4aaf5e482730 érvényes azonosító. |

## <a name="next-steps"></a>További lépések

* Kérdése van, vagy nem találja, amit keres? Hozzon létre egy GitHub-problémát, vagy olvassa el [a Támogatási és súgólehetőségeket a fejlesztők számára,](active-directory-develop-help-support.md) hogy megismerje a segítség és támogatás egyéb lehetőségeit.
