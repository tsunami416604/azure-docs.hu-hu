---
title: SAML-technikai profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: Hozzon létre egy SAML-technikai profilt egy egyéni szabályzatban Azure Active Directory B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 74666b1dc2ba4fac25aff0a56a52d048d746d465
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950935"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>SAML technikai profil definiálása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) támogatást nyújt az SAML 2,0 identitás-szolgáltatóhoz. Ez a cikk a szabványos protokollt támogató jogcím-szolgáltatóval való interakcióra szolgáló technikai profil sajátosságait ismerteti. SAML-alapú műszaki profillal összevonása az SAML-alapú identitás-szolgáltatóval, például az [ADFS](active-directory-b2c-custom-setup-adfs2016-idp.md) -vel és a [Salesforce](active-directory-b2c-setup-sf-app-custom.md)-mel. Ez az összevonás lehetővé teszi, hogy a felhasználók bejelentkezzenek a meglévő közösségi vagy vállalati identitásokkal.

## <a name="metadata-exchange"></a>Metaadatok cseréje

A metaadatok az SAML-protokollban használt információk az SAML-felek, például a szolgáltatók vagy az Identitáskezelő konfigurációjának elérhetővé tétele érdekében. A metaadatok határozzák meg a szolgáltatások helyét, például a bejelentkezést és a kijelentkezést, a tanúsítványokat, a bejelentkezési metódusokat és egyebeket. Az identitás-szolgáltató a metaadatok használatával tudja, hogyan kommunikálhat a Azure AD B2Cokkal. A metaadatok XML-formátumban vannak konfigurálva, és digitális aláírással is aláírva, így a másik fél ellenőrizheti a metaadatok integritását. Ha egy SAML-összevonja Azure AD B2C, az SAML-kérelmet kezdeményező szolgáltatóként funkcionál, és egy SAML-válaszra vár. És bizonyos esetekben elfogadja a nem kért SAML-hitelesítést, amely más néven az identitás-szolgáltató kezdeményezett.

A metaadatok mindkét fél esetében a "statikus metaadatok" vagy a "dinamikus metaadatok" is konfigurálhatók. Statikus módban másolja a teljes metaadatokat az egyik féltől, és állítsa be a másik fél számára. Dinamikus módban beállíthatja a metaadatok URL-címét, míg a másik fél dinamikusan beolvassa a konfigurációt. Az alapelvek megegyeznek a Azure AD B2C technikai profil metaadatainak megadásával az identitás-szolgáltatóban, és az identitás-szolgáltató metaadatait Azure AD B2C.

Mindegyik SAML-identitás szolgáltatójának különböző lépései vannak a szolgáltató kifejtéséhez és beállításához, ebben az esetben Azure AD B2C, majd a Azure AD B2C metaadatokat a személyazonosság-szolgáltatóban kell megadni. Tekintse meg az Identity Provider dokumentációját, amely útmutatást nyújt ennek elvégzéséhez.

Az alábbi példa egy Azure AD B2C technikai profil SAML-metaadatainak URL-címét jeleníti meg:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Cserélje le a következő értékeket:

- **saját** bérlő neve a bérlő nevével, például fabrikam.b2clogin.com.
- **az Ön** házirendje a szabályzat nevével. Használja azt a szabályzatot, amelyben az SAML-szolgáltató műszaki profilját konfigurálja, vagy egy olyan házirendet, amely az adott szabályzattól örököl.
- **az Ön-technikai profil** a SAML-identitás szolgáltatójának technikai profiljának nevével.

## <a name="digital-signing-certificates-exchange"></a>Digitális aláírási tanúsítványok cseréje

A Azure AD B2C és az SAML-identitás szolgáltatója közötti megbízhatósági kapcsolat létrehozásához meg kell adnia egy érvényes X509-tanúsítványt a titkos kulccsal. Feltölti a tanúsítványt a titkos kulccsal (. pfx fájllal) a Azure AD B2C Policy Key Store-ba. Azure AD B2C digitálisan aláírja az SAML bejelentkezési kérést az Ön által megadott tanúsítvány használatával.

A tanúsítvány a következő módokon használható:

- Azure AD B2C létrehoz és aláír egy SAML-kérelmet a tanúsítvány Azure AD B2C titkos kulcsának használatával. Az SAML-kérelmet a rendszer elküldi az identitás-szolgáltatónak, amely a tanúsítvány Azure AD B2C nyilvános kulcsával érvényesíti a kérelmet. A Azure AD B2C nyilvános tanúsítvány a technikai profil metaadatain keresztül érhető el. Azt is megteheti, hogy manuálisan feltölti a. cer fájlt az SAML-identitás szolgáltatójának.
- Az identitás-szolgáltató aláírja Azure AD B2C elküldte a tanúsítványt az Identity Provider titkos kulcsával. Azure AD B2C érvényesíti az adatforrást az identitás-szolgáltató nyilvános tanúsítványának használatával. Az egyes identitás-szolgáltatók különböző lépéseket tesznek a telepítéshez, és útmutatásért tekintse meg az Identity Provider dokumentációját. Azure AD B2C a házirendnek a tanúsítvány nyilvános kulcsát kell használnia az Identity Provider metaadataival.

Az önaláírt tanúsítványok a legtöbb esetben elfogadhatók. Éles környezetekben ajánlott a hitelesítésszolgáltató által kiadott X509-tanúsítványt használni. A dokumentum későbbi részében leírtaknak megfelelően a nem éles környezetekben mindkét oldalon le lehet tiltani az SAML-aláírást.

Az alábbi ábrán a metaadatok és a tanúsítványok cseréje látható:

![metaadatok és tanúsítványok cseréje](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)

## <a name="digital-encryption"></a>Digitális titkosítás

Az SAML-válasz kiállításának titkosításához az identitás-szolgáltató mindig egy Azure AD B2C technikai profilban lévő titkosítási tanúsítvány nyilvános kulcsát használja. Amikor Azure AD B2Cnak vissza kell fejtenie az adattitkosítást, a titkosítási tanúsítvány privát részét használja.

Az SAML-válasz kijelentésének titkosítása:

1. Töltsön fel egy érvényes X509-tanúsítványt a titkos kulccsal (. pfx fájl) a Azure AD B2C Policy Key Store-ba.
2. Adjon hozzá egy `SamlAssertionDecryption` azonosítóját tartalmazó **CryptographicKey** elemet a technikai profil **CryptographicKeys** gyűjteményéhez. Állítsa a **StorageReferenceId** az 1. lépésben létrehozott házirend-kulcs nevére.
3. Állítsa be a technikai profil metaadatainak **WantsEncryptedAssertions** `true`.
4. Frissítse az identitás-szolgáltatót az új Azure AD B2C technikai profil metaadataival. Látnia kell a **leírót** a **use** tulajdonsággal, amely a tanúsítvány nyilvános kulcsát tartalmazó `encryption`.

A következő példa a metaadatok Azure AD B2C technikai profil titkosítási szakaszát mutatja be:

```XML
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="protocol"></a>Protocol (Protokoll)

A protokoll elem **Name** attribútumát `SAML2`értékre kell állítani.

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem a `AttributeStatement` szakaszban található SAML-identitás szolgáltató által visszaadott jogcímek listáját tartalmazza. Előfordulhat, hogy le kell képeznie a szabályzatban definiált jogcím nevét az identitás-szolgáltatóban definiált névre. Olyan jogcímeket is tartalmazhat, amelyeket nem ad vissza az identitás-szolgáltató, ha beállítja a `DefaultValue` attribútumot.

Ha **az SAML-** állítást normalizált jogcímként **NamedId** szeretné olvasni, állítsa `assertionSubjectName`re a jogcím **PartnerClaimType** . Győződjön meg arról, hogy a **NameId** az érvényesítési XML első értéke. Ha egynél több kijelentést határoz meg, Azure AD B2C kiválasztja a tárgy értékét az utolsó állításból.

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

A következő példa a Facebook-identitás szolgáltatója által visszaadott jogcímeket mutatja be:

- A **issuerUserId** jogcím a **assertionSubjectName** jogcímhez van rendelve.
- Az **first_name** jogcím a **givenName** jogcímhez van rendelve.
- A **last_name** jogcím a **vezetéknév** jogcímere van leképezve.
- A **DisplayName** jogcím név leképezése nélkül.
- A név leképezése nélküli **e-mail-** jogcím.

A technikai profil az Identitáskezelő által nem visszaadott jogcímeket is visszaadja:

- Az **identityProvider** -jogcím, amely tartalmazza az identitás-szolgáltató nevét.
- A **authenticationSource** jogcím alapértelmezett **socialIdpAuthentication**-értékkel rendelkezik.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email"  />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metaadatok

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| PartnerEntity | Igen | Az SAML-identitás szolgáltatójának metaadatait tartalmazó URL-cím. Másolja az Identity Provider metaadatait, és adja hozzá a CDATA elemhez `<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | Nem | Azt jelzi, hogy a technikai profil megköveteli-e az összes kimenő hitelesítési kérelem aláírását. Lehetséges értékek: `true` vagy `false`. Az alapértelmezett érték 0.`true` Ha az érték `true`re van állítva, a rendszer a **SamlMessageSigning** titkosítási kulcsot meg kell adni, és az összes kimenő hitelesítési kérelem alá van írva. Ha az érték `false`, akkor a **SigAlg** és az **aláírás** paraméterei (lekérdezési karakterlánc vagy post paraméter) kimaradnak a kérésből. Ez a metaadatok a metaadatok **AuthnRequestsSigned** attribútumát is szabályozza, amely a Azure ad B2C az identitás-szolgáltatóval megosztott technikai profil metaadataiban található. Azure AD B2C nem írja alá a kérést, ha a technikai profil metaadatainak **WantsSignedRequests** értéke `false`, és az identitás-szolgáltató metaadatainak **WantAuthnRequestsSigned** beállítása `false` vagy nincs megadva. |
| XmlSignatureAlgorithm | Nem | Az a módszer, amelyet a Azure AD B2C az SAML-kérelem aláírására használ. Ez a metaadatok az SAML-kérelemben szereplő **SigAlg** paraméter (lekérdezési karakterlánc vagy post paraméter) értékét vezérlik. Lehetséges értékek: `Sha256`, `Sha384`, `Sha512`vagy `Sha1`. Győződjön meg arról, hogy az aláírási algoritmus mindkét oldalon ugyanazzal az értékkel van konfigurálva. Csak a tanúsítvány által támogatott algoritmust használja. |
| WantsSignedAssertions | Nem | Azt jelzi, hogy a technikai profilhoz szükséges-e az összes bejövő érvényesítés aláírása. Lehetséges értékek: `true` vagy `false`. Az alapértelmezett érték 0.`true` Ha az érték `true`re van állítva, akkor az identitás-szolgáltató által a Azure AD B2Cnak `saml:Assertion` összes érvényesítési szakaszt alá kell írni. Ha az érték `false`re van állítva, akkor az identitás-szolgáltatónak nem kell aláírnia az állításokat, de ha igen, Azure AD B2C nem ellenőrzi az aláírást. Ez a metaadatok a metaadatok jelző **WantsAssertionsSigned**is szabályozzák, amely az identitás-szolgáltatóval megosztott Azure ad B2C technikai profil metaadatainak kimenete. Ha letiltja a kijelentések érvényesítését, érdemes lehet letiltani a válasz aláírásának érvényesítését is (További információ: **ResponsesSigned**). |
| ResponsesSigned | Nem | Lehetséges értékek: `true` vagy `false`. Az alapértelmezett érték 0.`true` Ha az érték `false`re van állítva, akkor az identitás-szolgáltatónak nem kell aláírnia az SAML-választ, de ha igen, Azure AD B2C nem ellenőrzi az aláírást. Ha az érték `true`re van állítva, akkor a rendszer aláírja az identitás-Azure AD B2C szolgáltató által küldött SAML-választ, és azt ellenőrizni kell. Ha letiltja az SAML-válaszok érvényesítését, érdemes lehet letiltani az érvényesítési aláírás érvényességét (További információ: **WantsSignedAssertions**). |
| WantsEncryptedAssertions | Nem | Azt jelzi, hogy a technikai profil minden bejövő állítását titkosítani kíván-e. Lehetséges értékek: `true` vagy `false`. Az alapértelmezett érték 0.`false` Ha az érték `true`re van állítva, akkor az identitás-szolgáltató által a Azure AD B2C számára eljuttatott kijelentéseket alá kell írni, és meg kell adni a **SamlAssertionDecryption** titkosítási kulcsát. Ha az érték `true`re van állítva, a Azure AD B2C technikai profil metaadatai tartalmazzák a **titkosítási** szakaszt. Az identitás-szolgáltató beolvassa a metaadatokat, és titkosítja az SAML-válaszok érvényesítését a Azure AD B2C technikai profil metaadataiban megadott nyilvános kulccsal. Ha engedélyezi a kijelentések titkosítását, előfordulhat, hogy le kell tiltania a válasz aláírásának érvényesítését (további információért lásd: **ResponsesSigned**). |
| IdpInitiatedProfileEnabled | Nem | Azt jelzi, hogy engedélyezve van-e az egyszeri bejelentkezés munkamenet-profilja, amelyet egy SAML identitás-szolgáltatói profil kezdeményezett. Lehetséges értékek: `true` vagy `false`. A mező alapértelmezett értéke: `false`. Az identitás-szolgáltató által kezdeményezett folyamat során a rendszer külsőleg hitelesíti a felhasználót, és a rendszer kéretlen választ küld Azure AD B2Cra, amely ezután felhasználja a tokent, végrehajtja az előkészítési lépéseket, majd választ küld a függő entitás alkalmazásnak. |
| NameIdPolicyFormat | Nem | Meghatározza a kért tárgyat képviselő név azonosítójának korlátozásait. Ha nincs megadva, a rendszer a kért tárgyhoz tartozó személyazonossági szolgáltató által támogatott bármely típusú azonosítót használhatja. Például:  `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. A **NameIdPolicyFormat** használható a **NameIdPolicyAllowCreate**. Tekintse meg az Identity Provider dokumentációját, amely útmutatást nyújt arról, hogy mely név-azonosító házirendeket támogatja a rendszer. |
| NameIdPolicyAllowCreate | Nem | A **NameIdPolicyFormat**használatakor megadhatja a **NameIDPolicy**`AllowCreate` tulajdonságát is. A metaadatok értéke `true`, vagy `false` annak jelzésére, hogy az identitás-szolgáltató számára engedélyezett-e új fiók létrehozása a bejelentkezési folyamat során. Tekintse meg az Identity Provider dokumentációját, amely útmutatást nyújt ennek elvégzéséhez. |
| AuthenticationRequestExtensions | Nem | Az Azure AD BC és az identitás-szolgáltató között megállapodott opcionális protokoll-üzenet kiterjesztési elemek. A bővítmény XML formátumban jelenik meg. Az XML-adatértékeket a CDATA elem `<![CDATA[Your IDP metadata]]>`belül adja hozzá. Tekintse meg az Identitáskezelő dokumentációját, és ellenőrizze, hogy a bővítmények elem támogatott-e. |
| IncludeAuthnContextClassReferences | Nem | Egy vagy több URI-hivatkozást ad meg a hitelesítési környezet osztályainak azonosításához. Ha például engedélyezni szeretné, hogy egy felhasználó csak a felhasználónévvel és a jelszóval jelentkezzen be, állítsa `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`értékre. Ha engedélyezni szeretné a bejelentkezést a felhasználónévvel és a jelszóval védett munkameneten keresztül (SSL/TLS), adja meg a `PasswordProtectedTransport`. Tekintse meg az Identity Provider dokumentációját, amely útmutatást nyújt a támogatott **AuthnContextClassRef** URI azonosítókkal kapcsolatban. Vesszővel tagolt listaként több URI-t is meg kell adni. |
| IncludeKeyInfo | Nem | Azt jelzi, hogy az SAML-hitelesítési kérelem tartalmazza-e a tanúsítvány nyilvános kulcsát, amikor a kötés `HTTP-POST`ra van beállítva. Lehetséges értékek: `true` vagy `false`. |

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A **CryptographicKeys** elem a következő attribútumokat tartalmazza:

| Attribútum |Szükséges | Leírás |
| --------- | ----------- | ----------- |
| SamlMessageSigning |Igen | Az SAML-üzenetek aláírásához használt X509-tanúsítvány (RSA-kulcs). Azure AD B2C ezzel a kulccsal aláírja a kéréseket, és elküldi azokat az identitás-szolgáltatónak. |
| SamlAssertionDecryption |Igen | Az SAML-üzenetek visszafejtéséhez használt X509-tanúsítvány (RSA-kulcs). Ezt a tanúsítványt az azonosító szolgáltatónak kell megadnia. Azure AD B2C ezt a tanúsítványt használja a személyazonosság-szolgáltató által elküldett adatok visszafejtéséhez. |
| MetadataSigning |Nem | Az SAML-metaadatok aláírásához használt X509-tanúsítvány (RSA-kulcs). Azure AD B2C ezt a kulcsot használja a metaadatok aláírásához.  |

## <a name="next-steps"></a>Következő lépések

A következő cikkekben példákat talál az SAML-identitás-szolgáltatók használatának Azure AD B2C:

- [ADFS hozzáadása SAML-identitás-szolgáltatóként egyéni szabályzatok használatával](active-directory-b2c-custom-setup-adfs2016-idp.md)
- [Bejelentkezés Salesforce-fiókok használatával SAML-n keresztül](active-directory-b2c-setup-sf-app-custom.md)
