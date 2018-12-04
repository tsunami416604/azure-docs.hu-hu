---
title: Egy egyéni házirendek az Azure Active Directory B2C egy SAML-alapú technikai profilban meghatározása |} A Microsoft Docs
description: Adja meg az Azure Active Directory B2C egyéni házirendet egy SAML-alapú technikai profilban.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 47c6ee9c30e183b9991d1b670e96e937ade19d5f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52838514"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Egyéni Azure Active Directory B2C-házirendek egy SAML-alapú technikai profilban meghatározása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory (Azure AD) B2C támogatja az SAML 2.0 identitásszolgáltató. Ez a cikk ismerteti, amely támogatja a szabványos protokoll Jogcímszolgáltatók folytatott interakcióra szolgáló technikai profil adatait. Az SAML technikai profilban, akkor is összevonható az egy SAML-alapú identitásszolgáltató, például [ADFS](active-directory-b2c-custom-setup-adfs2016-idp.md) és [Salesforce](active-directory-b2c-setup-sf-app-custom.md), amely lehetővé teszi a felhasználóknak, hogy jelentkezzen be a meglévő közösségi vagy vállalati identitásokat.

## <a name="metadata-exchange"></a>Metaadatok exchange

Metaadatai elérhetővé egy SAML entitás, például a szolgáltató vagy az identitásszolgáltató konfigurációja a SAML protokoll használt információkat. Metaadatok helyét, a szolgáltatások, például a bejelentkezési és kijelentkezési, tanúsítványok, a bejelentkezési módszer, és egyéb határozza meg. Az identitásszolgáltató a metaadatok segítségével tudja, hogyan kell az Azure AD B2C-vel folytatott kommunikációhoz. A metaadatok XML formátumban van beállítva, és előfordulhat, hogy lehet egy digitális aláírásával, hogy a másik fél is a metaadatok integritásának ellenőrzését. Az Azure AD B2C-vel összevonja a SAML identitásszolgáltatóval, amikor egy SAML-kérelem indítása és SAML válaszra való várakozás a szolgáltató funkcionál. És néhány esetben elfogadja a kéretlen SAML-hitelesítés, amely más néven az identitásszolgáltató által kezdeményezett. 

A metaadatok "Statikus metaadatok" vagy "Dinamikus metaadatok" mindkét fél konfigurálható. Statikus módban a teljes metaadatokat másolja át az egyik fél, és állítsa be a másik fél. Dinamikus üzemmódban be az URL-cím metaadat, míg a másik fél dinamikusan beolvassa a konfiguráció. Az ismertetett alapelvek megegyeznek, az identitásszolgáltató beállítása a metaadatokat az Azure AD B2C-vel technikai profil, és a metaadatokat az identitásszolgáltató beállítása az Azure AD B2C-t.

Minden egyes SAML identitásszolgáltató tegye elérhetővé és a service provider, ebben az esetben az Azure AD B2C-vel és az identitásszolgáltató beállítása az Azure AD B2C-metaadatok különböző lépésből áll. Tekintse meg az identitásszolgáltató dokumentáció hogyan ehhez.

Az alábbi példa bemutatja egy Azure AD B2C-vel technikai profil SAML-metaadataira mutató URL-címet:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Cserélje le a következő értékeket:

- **a bérlő neve** a bérlő neve például fabrikam.b2clogin.com együtt.
- **a szabályzat** a házirend neve. Használja a házirend, konfigurálhatja a SAML-alapú technikai profilban szolgáltató, vagy egy szabályzatot, amely örökli az adott házirendnek.
- **a technikai profil** a SAML identity provider technikai profil neve.

## <a name="digital-signing-certificates-exchange"></a>Digitális aláíró tanúsítványok exchange

Az Azure AD B2C-vel és az SAML-identitásszolgáltató közötti megbízhatósági kapcsolat létrehozásához, meg kell adnia egy érvényes X509 tanúsítványt a titkos kulccsal. A tanúsítvány feltöltése az Azure AD B2C-szabályzat kulcstároló, a titkos kulccsal (.pfx-fájl). Az Azure AD B2C digitális aláírással a SAML bejelentkezési kérelmet az Ön által megadott tanúsítványt használja. 

A tanúsítvány használható a következő módon:

- Az Azure AD B2C-vel hoz létre, és aláírja a SAML-kérelem használatával az Azure AD B2C-t a tanúsítvány titkos kulcsához. Az SAML-kérelmet küld az identitásszolgáltató, amely ellenőrzi a tanúsítvány nyilvános kulcsát az Azure AD B2C használatával. Az Azure AD B2C nyilvános tanúsítvány technikai profil metaadataiban keresztül érhető el. A .cer fájl azt is megteheti, a SAML-identitásszolgáltató manuális feltöltése.
- Az identitásszolgáltató az Azure AD B2C-vel történő adatküldés aláírja a tanúsítványt titkos kulcsát az identitásszolgáltató használatával. Az Azure AD B2C ellenőrzi a nyilvános tanúsítvány az identitásszolgáltató használatával. Minden egyes identitásszolgáltató a telepítő különböző lépésből áll, tekintse át az identitás-szolgáltatóktól dokumentációját ehhez útmutatást. Az Azure AD B2C-vel a házirendet el kell érnie a tanúsítvány nyilvános kulcsát az identitásszolgáltató metaadatok segítségével.

Önaláírt tanúsítvány egy feltételei a legtöbb forgatókönyvhöz. Az éles környezetekhez, javasoljuk, hogy egy X509 használja egy hitelesítésszolgáltató által kiállított tanúsítvány. Emellett ez a dokumentum későbbi szakaszában leírtak nem éles környezetben letilthatja a SAML-aláíró mindkét oldalon.

Az alábbi ábrán látható, a metaadatok és a tanúsítvány exchange:

![metaadatok és a tanúsítvány exchange](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)


## <a name="digital-encryption"></a>Digitális titkosítása

Az SAML-válasz előfeltétel titkosítását, az identitásszolgáltató mindig használja a titkosítási tanúsítványhoz tartozó nyilvános kulcs az Azure AD B2C-vel technikai profilban. Az Azure AD B2C-t az adatok visszafejtéséhez van szüksége, használja a titkosítási tanúsítvány titkos része.

Az SAML-válasz előfeltétel titkosítása:

1. Töltse fel egy érvényes X509 tanúsítványt a titkos kulcsot (.pfx-fájl), az Azure AD B2C-szabályzat kulcstároló is.
2. Adjon hozzá egy **CryptographicKey** elem azonosítója, amelyet az `SamlAssertionDecryption` a műszaki profilba **CryptographicKeys** gyűjtemény. Állítsa be a **StorageReferenceId** az 1. lépésben létrehozott házirendkulccsal nevére.
3. Állítsa be a technikai profil metaadataiban **WantsEncryptedAssertions** való `true`.
4. Az identitásszolgáltató frissítése az új Azure AD B2C-vel technikai profil metaadataiban. Megtekintheti a **KeyDescriptor** az a **használata** tulajdonság `encryption` a tanúsítvány nyilvános kulcsát tartalmazó.

Az alábbi példa bemutatja az Azure AD B2C-vel technikai profil encryption szakaszban a metaadatok:

```XML
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="identity-provider-initiated-flow"></a>Identitás-szolgáltató által kezdeményezett folyamat

Egyszeri bejelentkezés munkamenetben (kéretlen kérelem) az Identitásszolgáltató által kezdeményezett kéretlen SAML-válasz érkezik a szolgáltató ebben az esetben egy Azure AD B2C-vel technikai profil. Ezt a folyamatot a felhasználó nem halad át a webalkalmazás első, de az identitásszolgáltató van irányítva. A kérelem elküldésekor egy hitelesítési oldalra a felhasználó által biztosított az identitásszolgáltató. A felhasználó elvégzi a bejelentkezés, és majd a kérést a rendszer átirányítja az Azure AD B2C-vel és a egy SAML-válasz, amely tartalmazza a helyességi feltételek. Az Azure AD B2C olvassa be a helyességi feltételek és egy új SAML-jogkivonatot, és ezután átirányítja a felhasználót a függő entitás alkalmazásnak. Az átirányítások kell elvégeznie a **AssertionConsumerService** elem **hely** tulajdonság.


![Által kezdeményezett SAML-Identitásszolgáltató](media/saml-technical-profile/technical-profile-idp-saml-idp-initiated.png) 

Vegye figyelembe a következő házirend követelményeinek, amikor a folyamat létrehozása egy identitásszolgáltató által kezdeményezett:

- Az első vezénylési lépés kell lennie egy jogcím-exchange mutató egy SAML-alapú technikai profilban.
- A technikai profil rendelkeznie kell egy elem nevű metaadat **IdpInitiatedProfileEnabled** beállítása `true`.
- A függő entitás házirendet kell lennie egy függő entitás SAML.
- A függő entitás házirendnek rendelkeznie kell nevű metaadat **IdpInitiatedProfileEnabled** beállítása `true`.
- A kéretlen választ kell kell küldeni a `/your-tenant/your-policy/samlp/sso/assertionconsumer` végpont. Bármely továbbítási állapot, a válasz tartalmazza a függő entitás van továbbítani. Cserélje le a következő értékeket: **a bérlő** a bérlő neve. **a szabályzat** a függő entitás házirend neve.
    
## <a name="protocol"></a>Protokoll

A **neve** a protokoll elem attribútuma kell beállítani `SAML2`. 

## <a name="metadata"></a>Metaadatok

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| PartnerEntity | Igen | Az identitásszolgáltató SAML-metaadatok URL-címe. Az identity provider metaadatok másolja, majd adja hozzá a CDATA elemen belül `<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | Nem | Azt jelzi, hogy szükséges-e összes kimenő hitelesítési kérelmet kell aláírni a technikai profil. A lehetséges értékek: `true` vagy `false`. Az alapértelmezett érték `true`. Ha az értéke `true`, a **SamlMessageSigning** kriptográfiai kulcsot meg kell határozni, illetve az összes kimenő hitelesítési kérelmet jelentkezett. Ha az értéke `false`, a **SigAlg** és **aláírás** paraméterek (lekérdezés-karakterlánc, vagy ossza meg a paraméter) hiányoznak a kérésből. A metaadatok is szabályozza a metaadatok **AuthnRequestsSigned** attribútuma, amely egy kimenet az Azure AD B2C az identitásszolgáltatónál történő megosztott technikai profil metaadataiban. |
| XmlSignatureAlgorithm | Nem | A módszer, amely az SAML-kérelmet aláírásához használt Azure AD B2C-t. A metaadat értéke szabályozza a **SigAlg** paraméter (lekérdezés-karakterlánc, vagy ossza meg a paraméter) a SAML-kérelem. A lehetséges értékek: `Sha256`, `Sha384`, `Sha512`, vagy `Sha1`. Ellenőrizze, hogy az aláírási algoritmus konfigurálása mindkét oldalán, az ugyanazt az értéket. Csak az algoritmus, amely támogatja a tanúsítványt használja. | 
| WantsSignedAssertions | Nem | Azt jelzi, hogy szükséges-e a technikai profil minden bejövő helyességi feltételek aláírva. A lehetséges értékek: `true` vagy `false`. Az alapértelmezett érték `true`. Ha az értéke `true`, minden helyességi feltételek szakasz `saml:Assertion` küldött által az identitás az Azure AD B2C-szolgáltatót kell aláírni. Ha az értéke `false`, az identitásszolgáltató nem jelentkezzen a helyességi feltételek, de még akkor is, ha igen, az Azure AD B2C-vel nem érvényesíteni az aláírást. A metaadatok is szabályozza a metaadatok jelző **WantsAssertionsSigned**, azaz a kimenet az Azure AD B2C az identitásszolgáltatónál történő megosztott technikai profil metaadataiban. Ha letiltja a helyességi feltételek érvényesítése, előfordulhat, hogy szeretné is, a válasz aláírás ellenőrzésének letiltása (további információkért lásd: **ResponsesSigned**). |
| ResponsesSigned | Nem | A lehetséges értékek: `true` vagy `false`. Az alapértelmezett érték `true`. Ha az értéke `false`, az identitásszolgáltató SAML-válasz nem aláírásához, de még akkor is, ha igen, az Azure AD B2C-vel nem érvényesíteni az aláírást. Ha az értéke `true`, az Azure AD B2C az identitásszolgáltató által küldött SAML-válasz alá van írva, és össze kell vetni. Az SAML-válasz érvényesítés letiltása esetén is érdemes letiltani a helyességi feltétel aláírás-ellenőrzése (további információkért lásd: **WantsSignedAssertions**). |
| WantsEncryptedAssertions | Nem | Azt jelzi, hogy szükséges-e a technikai profil minden bejövő helyességi feltételek titkosítását. A lehetséges értékek: `true` vagy `false`. Az alapértelmezett érték `false`. Ha az értéke `true`, alá kell írni a helyességi feltételek Azure AD B2C az identitásszolgáltató által küldött és az **SamlAssertionDecryption** kriptográfiai kulcsot meg kell határozni. Ha az értéke `true`, a metaadatokat az Azure AD B2C-vel technikai profil tartalmazza a **titkosítási** szakaszban. Az identitásszolgáltató beolvassa a metaadatokat, és az SAML-válasz előfeltétel titkosítja az Azure AD B2C-vel technikai profil metaadataiban megadott nyilvános kulccsal. Ha engedélyezte a helyességi feltételek titkosítás, is szükség lehet a válasz aláírás ellenőrzésének letiltása (további információkért lásd: **ResponsesSigned**). | 
| IdpInitiatedProfileEnabled | Nem |Jelzi, hogy egy egyszeri bejelentkezés munkamenet-profilt, amely egy SAML-alapú identitás-szolgáltató profilban kezdeményezte engedélyezett. A lehetséges értékek: `true` vagy `false`. A mező alapértelmezett értéke: `false`. A folyamat az identitásszolgáltató által kezdeményezett a felhasználó hitelesítése külsőleg, és a nem kért választ küld az Azure AD B2C-t, majd használ fel a tokent, hajtja végre a vezénylési lépéseket, és ezután elküldi az adott válasz a függő gyártótól származó alkalmazás. |

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A **CryptographicKeys** elem tartalmazza a következő attribútumokat:

| Attribútum |Szükséges | Leírás |
| --------- | ----------- | ----------- |
| SamlMessageSigning |Igen | A X509 (RSA key set) tanúsítványt használja, az SAML-üzenetek aláírásához. Az Azure AD B2C a kérelmek és küldje el azokat az identitásszolgáltató ezt a kulcsot használ. |
| SamlAssertionDecryption |Igen | A X509 tanúsítvány (RSA key set) visszafejtésére SAML használatával. Az identitásszolgáltató által ezt a tanúsítványt kell biztosítani. Az Azure AD B2C ezt a tanúsítványt használja az identitásszolgáltató által küldött adatok visszafejtéséhez. |
| MetadataSigning |Nem | A X509 bejelentkezhet a SAML-metaadatokat (RSA key set) tanúsítványt. Az Azure AD B2C a metaadatok aláírásához használja ezt a kulcsot.  |

## <a name="examples"></a>Példák

- [AD FS egyéni szabályzatok használatával egy SAML-identitásszolgáltató hozzáadása](active-directory-b2c-custom-setup-adfs2016-idp.md)
- [Jelentkezzen be Salesforce-fiókok SAML-n keresztül](active-directory-b2c-setup-sf-app-custom.md)













