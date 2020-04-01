---
title: SAML technikai profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: Saml technikai profildefiniálása egyéni szabályzatban az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 83a13e0b1bb4d55b889d96e42c8f3f18ce0f2b73
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408926"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>SAML technikai profil definiálása az Azure Active Directory B2C egyéni házirendjében

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) támogatja az SAML 2.0 identitásszolgáltató. Ez a cikk a műszaki profil sajátosságait ismerteti a szabványosprotokollt támogató jogcímszolgáltatóval való interakcióhoz. Saml technikai profillal összeegyeztetheti az SAML-alapú identitásszolgáltatót, például [az ADFS-t](identity-provider-adfs2016-custom.md) és [a Salesforce-ot.](identity-provider-salesforce-custom.md) Ez az összevonás lehetővé teszi a felhasználók számára, hogy jelentkezzen be a meglévő közösségi vagy vállalati identitások.

## <a name="metadata-exchange"></a>Metaadat-csere

A metaadatok az SAML protokollban használt információk az SAML-felek, például a szolgáltató vagy az identitásszolgáltató konfigurációjának felfedésére. A metaadatok határozzák meg a szolgáltatások helyét, például a bejelentkezést és a kijelentkezést, a tanúsítványokat, a bejelentkezési módszert stb. The identity provider uses the metadata to know how to communicate with Azure AD B2C. A metaadatok XML formátumban vannak konfigurálva, és digitális aláírással aláírhatók, így a másik fél ellenőrizheti a metaadatok integritását. Amikor az Azure AD B2C összekapcsolódik egy SAML-identitásszolgáltatóval, az SAML-kérelem meghívása és saml-válaszra váró szolgáltatóként működik. És bizonyos esetekben elfogadja a kéretlen SAML-hitelesítést, amelyet identitásszolgáltatónak is neveznek.

A metaadatok mindkét félnél "Statikus metaadatok" vagy "Dinamikus metaadatok" néven konfigurálhatók. Statikus módban az egyik fél teljes metaadatait másolja, és a másik félben állítja be. Dinamikus módban az URL-címet a metaadatokra állítja be, míg a másik fél dinamikusan olvassa be a konfigurációt. Az alapelvek megegyeznek, az Azure AD B2C technikai profil metaadatait az identitásszolgáltatóban állíthatja be, és az identitásszolgáltató metaadatait az Azure AD B2C-ben állítja be.

Minden SAML-identitásszolgáltató különböző lépéseket tesz elérhetővé, és állítsa be a szolgáltatót, ebben az esetben az Azure AD B2C, és állítsa be az Azure AD B2C metaadatokat az identitásszolgáltatóban. Ennek módjáról a személyazonosság-szolgáltató dokumentációjában tájékformára vonatkozó útmutatást talál.

A következő példa egy Azure AD B2C technikai profil SAML-metaadatainak URL-címét mutatja be:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Cserélje le a következő értékeket:

- **a bérlő nevét** a bérlő nevével, például fabrikam.b2clogin.com.
- **a házirend** nevével. Használja azt a házirendet, ahol az SAML-szolgáltató technikai profilját konfigurálja, vagy egy olyan házirendet, amely aházirendből öröklődik.
- **az Ön technikai profilját** az SAML identitásszolgáltató technikai profilnevével.

## <a name="digital-signing-certificates-exchange"></a>Digitális aláíró tanúsítványok cseréje

Az Azure AD B2C és az SAML-identitásszolgáltató közötti megbízhatóság létrehozásához érvényes X509-tanúsítványt kell biztosítania a személyes kulccsal. Töltse fel a tanúsítványt a személyes kulcs (.pfx fájl) az Azure AD B2C házirendkulcs-tárolóba. Az Azure AD B2C digitálisan aláírja az SAML bejelentkezési kérelmet a megadott tanúsítvány használatával.

A tanúsítvány taszerint használjuk:

- Az Azure AD B2C saml-kérelmet hoz létre és ír alá a tanúsítvány Azure AD B2C személyes kulcsa használatával. Az SAML-kérelmet a rendszer elküldi az identitásszolgáltatónak, amely érvényesíti a kérelmet a tanúsítvány Azure AD B2C nyilvános kulcsával. Az Azure AD B2C nyilvános tanúsítvány érhető el a technikai profil metaadatok. Azt is megteheti, hogy manuálisan feltölti a .cer fájlt az SAML-identitásszolgáltatóba.
- Az identitásszolgáltató aláírja az Azure AD B2C-nek küldött adatokat az identitásszolgáltató a tanúsítvány személyes kulcsa használatával. Az Azure AD B2C érvényesíti az adatokat az identitásszolgáltató nyilvános tanúsítványával. Minden identitásszolgáltató különböző lépéseket a telepítés, tekintse meg az identitásszolgáltató dokumentációját útmutatást, hogyan kell csinálni. Az Azure AD B2C-ben a szabályzatnak hozzáférésre van szüksége a nyilvános tanúsítványkulcshoz az identitásszolgáltató metaadatai nak használatával.

Az önaláírt tanúsítvány a legtöbb esetben elfogadható. Éles környezetben ajánlott egy hitelesítésszolgáltató által kiállított X509-tanúsítvány használata. Is, ahogy azt a dokumentum későbbi részében, egy nem éles környezetben letilthatja az SAML aláírása mindkét oldalon.

Az alábbi ábra a metaadatokat és a tanúsítványcserét mutatja be:

![metaadatok és tanúsítványcsere](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)

## <a name="digital-encryption"></a>Digitális titkosítás

Az SAML-válasz feltétel titkosításához az identitásszolgáltató mindig egy nyilvános kulcsot használ egy titkosítási tanúsítvány egy Azure AD B2C technikai profil. Ha az Azure AD B2C-nek vissza kell fejtenie az adatokat, a titkosítási tanúsítvány privát részét használja.

Az SAML válasz állításának titkosítása:

1. Töltsön fel egy érvényes X509-tanúsítványt a személyes kulccsal (.pfx fájl) az Azure AD B2C házirendkulcstárolóba.
2. A **Kriptográfiai kulcs** `SamlAssertionDecryption` elem hozzáadása a **Kriptográfiai kulcsok** gyűjteményhez azonosítóval. Állítsa be a **StorageReferenceId-ot** az 1.
3. Állítsa be a **wantsEncryptedAssertions** technikai `true`profil metaadatait a beállításra.
4. Frissítse az identitásszolgáltatót az új Azure AD B2C technikai profil metaadataival. Meg kell jelennie a **KeyDescriptor** `encryption` a **használat** tulajdonság beállítása, amely tartalmazza a nyilvános kulcsot a tanúsítvány.

A következő példa az Azure AD B2C technikai profil titkosítási szakasza a metaadatok:

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

A Protokoll elem **Name** attribútumát a `SAML2`parancsra kell állítani.

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem az SAML-identitásszolgáltató által a `AttributeStatement` szakasz ban visszaadott jogcímek listáját tartalmazza. Előfordulhat, hogy a házirendben definiált jogcím nevét hozzá kell képeznie az identitásszolgáltatóban megadott névhez. Az identitásszolgáltató által vissza nem adott jogcímeket is megadhat, amíg beállítja az `DefaultValue` attribútumot.

### <a name="subject-name-output-claim"></a>Tulajdonos neve kimeneti jogcím

Ha normalizált jogcímként szeretné olvasni a **Tulajdonos** **névazonosítójának SAML-állítását,** `SPNameQualifier` állítsa a **PartnerClaimType** jogcímet az attribútum értékére. Ha `SPNameQualifier`az attribútum nem jelenik meg, állítsa a `NameQualifier` **PartnerClaimType** jogcím értékét az attribútum értékére. 


SAML állítás: 

```XML
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
    <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
      <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

Kimeneti igény:

```XML
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

Ha `SPNameQualifier` mindkettő `NameQualifier` vagy attribútum nem jelenik meg az SAML-ben, állítsa a **PartnerClaimType** jogcím beállítását a beállításra. `assertionSubjectName` Győződjön meg arról, hogy a **NameId** az első érték a helyességi helyességi feltétel XML-ben. Ha egynél több feltételt definiál, az Azure AD B2C kiválasztja a tárgy értékét az utolsó helyességi feltételből.

A következő példa az SAML-identitásszolgáltató által visszaadott jogcímeket mutatja be:

- A **issuerUserId** jogcím az **állításSubjectName** jogcímhez van rendelve.
- A **first_name** jogcím a **givenName** jogcímhez van rendelve.
- A **last_name** követelés a **vezetéknév-követelésre** van leképezve.
- A **displayName** jogcím névleképezés nélkül.
- Az **e-mail** jogcím névleképezés nélkül.

A technikai profil olyan jogcímeket is visszaad, amelyeket az identitásszolgáltató nem ad vissza:

- Az identitásszolgáltató nevét tartalmazó **identityProvider** jogcím.
- A **authenticationSource** jogcím a **socialIdpAuthentication**alapértelmezett értékével.

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

A **OutputClaimsTransformations** elem tartalmazhat **OutputClaimsTransformations** elemek gyűjteményét, amelyek a kimeneti jogcímek módosítására vagy újak létrehozására szolgálnak.

## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Partnerentitás | Igen | Az SAML-identitásszolgáltató metaadatainak URL-címe. Az identitásszolgáltató metaadatainak másolása és hozzáadása a CDATA elemhez`<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | Nem | Azt jelzi, hogy a technikai profil megköveteli-e az összes kimenő hitelesítési kérelem alánem írt. Lehetséges `true` értékek: `false`vagy . Az alapértelmezett érték `true`. Ha az érték `true`értéke, a **SamlMessageSigning** kriptográfiai kulcsot meg kell adni, és az összes kimenő hitelesítési kérelmet alá kell írni. Ha az érték `false`értéke, a **SigAlg** és **az Aláírás** paraméterek (lekérdezési karakterlánc vagy postaparaméter) kimaradnak a kérelemből. Ez a metaadat is szabályozza a metaadatok **AuthnRequestsSigned** attribútum, amely kimeneti az Azure AD B2C technikai profil, amely meg van osztva az identitásszolgáltatóval. Az Azure AD B2C nem írja alá a kérelmet, ha a **WantsSignedRequests** értéke a technikai profil metaadataiban be van állítva, `false` és az identitásszolgáltató metaadatai **WantAuthnRequestsSigned** értéke `false` vagy nincs megadva. |
| XmlSignatureAlgorithm | Nem | Az Azure AD B2C által az SAML-kérelem aláírásához használt módszer. Ez a metaadat szabályozza a **SigAlg** paraméter (lekérdezési karakterlánc vagy postaparaméter) értékét az SAML-kérelemben. Lehetséges `Sha256`értékek: `Sha384` `Sha512`, `Sha1`, , vagy . Győződjön meg arról, hogy az aláírási algoritmust mindkét oldalon azonos értékkel konfigurálja. Csak a tanúsítvány által támogatott algoritmust használja. |
| WantsSignedAssertions | Nem | Azt jelzi, hogy a technikai profil megköveteli-e az összes bejövő állítás aláállítását. Lehetséges `true` értékek: `false`vagy . Az alapértelmezett érték `true`. Ha az érték `true`értéke, akkor `saml:Assertion` az identitásszolgáltató által az Azure AD B2C-nek küldött összes kijelentési szakaszt alá kell írni. Ha az érték `false`értéke, az identitásszolgáltató nem írja alá a állításokat, de még ha igen, az Azure AD B2C nem érvényesíti az aláírást. Ez a metaadatok is szabályozza a metaadat-jelző **WantsAssertionsSigned**, amely kimeneti az Azure AD B2C technikai profil, amely meg van osztva az identitásszolgáltatóval. Ha letiltja az állítások érvényesítését, akkor érdemes lehet letiltani a válaszaláírás-érvényesítést is (további információért lásd: **VálaszokAláírva).** |
| Válaszok aláírva | Nem | Lehetséges `true` értékek: `false`vagy . Az alapértelmezett érték `true`. Ha az érték `false`értéke, az identitásszolgáltató nem írja alá az SAML-választ, de még ha igen, az Azure AD B2C nem érvényesíti az aláírást. Ha az érték `true`értéke, az identitásszolgáltató által az Azure AD B2C-nek küldött SAML-válasz alá van írva, és ellenőrizni kell. Ha letiltja az SAML-válasz érvényesítését, akkor érdemes lehet letiltani a helyességi feltétel aláírásának érvényesítését is (további információért **lásd: WantsSignedAssertions**). |
| WantsEncryptedAssertions (WantsEncryptedAssertions) | Nem | Azt jelzi, hogy a technikai profil megköveteli-e az összes bejövő állítás titkosítását. Lehetséges `true` értékek: `false`vagy . Az alapértelmezett érték `false`. Ha az érték `true`értéke, az identitásszolgáltató által az Azure AD B2C-nek küldött állításokat alá kell írni, és meg kell adni a **SamlAssertionDecryption titkosítási** kriptográfiai kulcsot. Ha az érték `true`értéke, az Azure AD B2C technikai profil metaadatai tartalmazzák a **titkosítási** szakaszt. Az identitásszolgáltató beolvassa a metaadatokat, és titkosítja az SAML-válasz állítását az Azure AD B2C technikai profil metaadataiban megadott nyilvános kulccsal. Ha engedélyezi a helyességi kiállítások titkosítását, akkor előfordulhat, hogy le kell tiltania a válaszaláírás-érvényesítést (további információért lásd: **VálaszokAláírva).** |
| IdpInitiatedProfileEnabled | Nem | Azt jelzi, hogy engedélyezve van-e egy SAML-identitásszolgáltatói profil által kezdeményezett egyszeri bejelentkezési munkamenet-profil. Lehetséges `true` értékek: `false`vagy . A mező alapértelmezett értéke: `false`. Az identitásszolgáltató által kezdeményezett folyamata a felhasználó külső hitelesítése, és egy kéretlen választ küld az Azure AD B2C, amely ezután felhasználja a jogkivonatot, vezénylési lépéseket hajt végre, majd küld választ a függő entitás alkalmazás. |
| NameIdPolicyformat | Nem | Megadja a kért tulajdonos ábrázolására használandó névazonosító megkötéseit. Ha nincs megadva, a kért tulajdonos identitásszolgáltatója által támogatott bármilyen típusú azonosító használható. Például:  `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **A NameIdPolicyFormat** használható **a NameIdPolicyAllowCreate**programmal. Tekintse meg az identitásszolgáltató dokumentációját, hogy útmutatást talál arról, hogy mely névazonosító-házirendek támogatottak. |
| NameIdPolicyAllowCreate | Nem | A **NameIdPolicyFormat használataesetén**megadhatja `AllowCreate` a **NameIDPolicy**tulajdonságát is. A metaadatok értéke `true` vagy `false` annak jelzése, hogy az identitásszolgáltató létrehozhat-e új fiókot a bejelentkezési folyamat során. Ennek módjáról a személyazonosság-szolgáltató dokumentációjában tájékformára vonatkozó útmutatást talál. |
| AuthenticationRequestExtensions (HitelesítésKérőDnekbővítményei) | Nem | Az Azure AD BC és az identitásszolgáltató között elfogadott választható protokollüzenet-bővítmény elemek. A kiterjesztés XML formátumban jelenik meg. Az XML-adatokat a CDATA elemhez `<![CDATA[Your IDP metadata]]>`adja hozzá. Ellenőrizze az identitásszolgáltató dokumentációjában, hogy a bővítményelem támogatott-e. |
| IncludeAuthnContextClassReferences | Nem | Egy vagy több, a hitelesítési környezetosztályt azonosító URI-hivatkozást ad meg. Ha például azt szeretné, hogy a felhasználó csak felhasználónévvel `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`és jelszóval jelentkezhetbe, állítsa az értéket a értékre. Ha engedélyezni szeretné a bejelentkezést a felhasználónévvel és a jelszóval egy `PasswordProtectedTransport`védett munkameneten (SSL/TLS) keresztül, adja meg a lehetőséget. Tekintse meg az identitásszolgáltató dokumentációját a támogatott **AuthnContextClassRef** URI-kkal kapcsolatos útmutatásért. Adjon meg több URI-t vesszővel tagolt listaként. |
| IncludeKeyInfo | Nem | Azt jelzi, hogy az SAML hitelesítési kérelem tartalmazza-e `HTTP-POST`a tanúsítvány nyilvános kulcsát, ha a kötés beállítása a. Lehetséges `true` értékek: `false`vagy . |
| IncludeClaimResolvingInClaimsHandling  | Nem | Bemeneti és kimeneti jogcímek esetén megadja, hogy a [jogcímek feloldása](claim-resolver-overview.md) szerepel-e a technikai profilban. Lehetséges értékek: `true` `false`  vagy (alapértelmezett). Ha a technikai profilban jogcímfeloldót szeretne `true`használni, állítsa ezt a beállításra. |

## <a name="cryptographic-keys"></a>Kriptográfiai kulcsok

A **Kriptográfiai kulcsok** elem a következő attribútumokat tartalmazza:

| Attribútum |Kötelező | Leírás |
| --------- | ----------- | ----------- |
| SamlMessageAláírása |Igen | Az X509-tanúsítvány (RSA-kulcskészlet), amelyet saml üzenetek aláírására használ. Az Azure AD B2C ezt a kulcsot használja a kérelmek aláírásához és az identitásszolgáltatónak való elküldéséhez. |
| SamlAssertionDetitkosítás |Igen | Az SAML-üzenetek visszafejtéséhez használt X509-tanúsítvány (RSA-kulcskészlet). Ezt a tanúsítványt az identitásszolgáltatónak kell megadnia. Az Azure AD B2C ezt a tanúsítványt használja az identitásszolgáltató által küldött adatok visszafejtésére. |
| Metaadat-aláírás |Nem | Az SAML metaadatok aláírásához használt X509-tanúsítvány (RSA-kulcskészlet). Az Azure AD B2C ezt a kulcsot használja a metaadatok aláírásához.  |

## <a name="next-steps"></a>További lépések

Az alábbi cikkekben példákat talál az AZURE AD B2C SAML-identitásszolgáltatóinak használatára:

- [AADFS hozzáadása SAML-identitásszolgáltatóként egyéni házirendek használatával](identity-provider-adfs2016-custom.md)
- [Bejelentkezés Salesforce-fiókok használatával SAML-en keresztül](identity-provider-salesforce-custom.md)
