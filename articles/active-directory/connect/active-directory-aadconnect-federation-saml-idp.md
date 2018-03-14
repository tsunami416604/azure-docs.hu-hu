---
title: "Az Azure AD Connect: Használja egyszeri bejelentkezéshez a SAML 2.0-s identitásszolgáltató az |} Microsoft Docs"
description: "Ez a témakör ismerteti az egyszeri bejelentkezéshez a SAML 2.0-s megfelelő Idp használ."
services: active-directory
author: billmath
manager: mtillman
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 46c65e0efdc91b70c5d0d2afdf83d7205efc8057
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2018
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Egyszeri bejelentkezéshez a SAML 2.0-Identitásszolgáltatóként (IdP) használata

Ez a témakör ismerteti a SAML 2.0 használatával megfelelő SP-Lite profil alapul, az előnyben részesített biztonsági jogkivonat-szolgáltatás (STS) identitásszolgáltató / identitásszolgáltató. Ez akkor hasznos, ha már rendelkezik a felhasználó és a jelszót a helyszíni SAML 2.0 használatával elérhető tárolja. A meglévő felhasználói könyvtár használható bejelentkezésre az Office 365 és más Azure AD által védett erőforrásokhoz. A SAML 2.0 SP-Lite profil való bejelentkezéshez, és a attribútum exchange keretrendszert biztosítson a széles körben használt Security Assertion Markup Language (SAML) összevont identitás normál alapul.

>[!NOTE]
>3. fél használható az Azure ad-val tesztelt Idps listájáért lásd: a [az Azure AD összevonás kompatibilitási listája](active-directory-aadconnect-federation-compatibility.md)

A Microsoft támogatja a bejelentkezést, az integráció a Microsoft felhőalapú szolgáltatás, például az Office 365, a megfelelően konfigurált SAML 2.0-s profil IdP alapján. SAML 2.0 identitás-szolgáltatóktól harmadik féltől származó termékekre, és ezért a Microsoft nem támogatja a központi telepítés konfigurációs, ajánlott eljárások ezekkel kapcsolatos hibaelhárítás. Egyszer megfelelően konfigurálva, az identitásszolgáltató tesztelhető a megfelelő konfigurációt a Microsoft kapcsolat Analyzer eszközt, amely az alábbi további részletes leírását lásd a SAML 2.0 integrációja. További információ a SAML 2.0 SP-Lite profil alapján identitásszolgáltató kérje meg a szervezet, amely megadja azt.

>[!IMPORTANT]
>Csak korlátozott számú ügyféllel érhető el, a bejelentkezés forgatókönyvet, és a SAML 2.0 Identitásszolgáltatók, ehhez a következőket:

>- Web-alapú ügyfelek, például az Outlook Web Access és a SharePoint online-hoz
- E-mailek gazdag ügyfél, amely használja az egyszerű hitelesítés és a támogatott Exchange hozzáférési módszer IMAP, POP-ra, ActiveSync, MAPI, stb. (a fokozott ügyfél protokoll végpontját kell telepíteni), például többek között:
    - A Microsoft Outlook 2010 vagy az Outlook 2013/Outlook 2016, Apple iPhone (különböző iOS verzió)
    - Különféle Google Android-eszközök
    - Windows Phone 7, Windows Phone 7,8 és Windows Phone 8.0
    - Levelezőprogram Windows 8 és Windows 8.1 levelezőprogram
    - Windows 10 levelezőprogram

Más ügyfelek a bejelentkezés forgatókönyvet, és a SAML 2.0 identitásszolgáltató nem érhetők el. Például a Lync 2010 asztali ügyfél nem nem tud bejelentkezni a szolgáltatásba a SAML 2.0 identitásszolgáltató az egyszeri bejelentkezés beállítása.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Azure AD SAML 2.0 protokoll követelmények
Ez a témakör részletes követelményeket a protokoll és, hogy a SAML 2.0 identitásszolgáltató kell valósítania az Azure AD-bejelentkezés engedélyezése (például az Office 365) egy vagy több Microsoft felhőszolgáltatások összevonni kívánt formázási üzenetet. A SAML 2.0 függő entitás (SP-STS), egy Microsoft felhőszolgáltatásra, ebben a forgatókönyvben használt az Azure AD.

Javasoljuk, hogy ellenőrizze-e a SAML 2.0 identitásszolgáltató kimeneti üzenetek legyen olyan hasonló, a megadott minta nyomkövetések lehető. A megadott egyedi attribútum értékeinek is, használja az Azure AD-metaadatok ahol csak lehetséges. Ha elégedett a kimeneti üzenetek, tesztelheti a Microsoft kapcsolat elemző eszközt az alább ismertetett.

Az Azure AD-metaadatok tölthető le: az URL-cím: [ https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml ](http://https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml).
A kínai ügyfelek számára az Office 365 Kína-specifikus példányát használja, a következő összevonási végpontot kell használni: [ https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml ](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml).

## <a name="saml-protocol-requirements"></a>SAML protokoll követelmények
Ez a szakasz hogyan a kérelem-válasz üzenet párok alkotják részletek rendezése érdekében, hogy az üzenetek megfelelő formátumú.

Az Azure AD beállítható úgy, hogy a SAML 2.0 SP Lite profilt használja az egyes konkrét követelmények, az alább felsorolt identitás-szolgáltatóktól dolgozni. A minta SAML kérés- és üzenetek automatikus és manuális tesztelési együtt használva dolgozhat működtetéshez az Azure ad-val.

## <a name="signature-block-requirements"></a>Aláírás blokk követelmények
A SAML-válasz üzenetben az aláírás-csomópont a magát az üzenet digitális aláírás információkat tartalmaz. Az aláírásblokk követelményei a következők:

1. A helyességi feltétel a csomópont önmaga rendelkeznie kell aláírással.
2.  Az RSA-sha1 algoritmusok kell használni, mint a DigestMethod értékét. Más digitális aláírási algoritmus használata nem engedélyezett.
   `<ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  Előfordulhat, hogy is regisztrál az XML-dokumentumot. 
4.  Az átalakító algoritmus meg kell egyeznie a következő mintában szereplő értékekkel:    `<ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  A SignatureMethod algoritmus meg kell egyeznie a következő mintát:   `<ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Támogatott kötések
Kötések az átvitel kapcsolatos kommunikációs paramétereket, amelyek szükségesek. Az alábbi követelmények vonatkoznak a kötések

1. HTTPS a szükséges átviteli.
2.  Az Azure AD HTTP POST szüksége van a bejelentkezéskor token küldésének
3.  Az Azure AD a hitelesítési kérelmet az identitásszolgáltató és ÁTIRÁNYÍTÁSI az identitásszolgáltató kijelentkezési üzenet HTTP POST fogja használni.

## <a name="required-attributes"></a>Szükséges attribútumok
Az alábbi táblázatban megadott attribútumokat követelményei a SAML 2.0 üzenetben.
 
|Attribútum|Leírás|
| ----- | ----- |
|NameID|A helyességi feltétel értéke ugyanaz, mint az Azure AD-felhasználó ImmutableID kell lennie. Lehet, hogy a legfeljebb 64 alfanumerikus karakterből állhat. A HTML nem biztonságos karaktereket kell kódolni, például a "+" karaktert jelenik meg, mint ".2B".|
|IDPEmail|Az egyszerű felhasználónév (UPN) szerepel a SAML-válasz IDPEmail nevű elemeként Ez az a felhasználó UserPrincipalName (UPN) az Azure AD vagy Office 365-ben. Az egyszerű felhasználónév az e-mail cím formátumú. Egyszerű felhasználónév értéke Windows Office 365 (az Azure Active Directory).|
|Kiállító|Ez szükséges az identitásszolgáltató URI lehet. A minta üzenetekből a kibocsátó nem szabad újra. Ha több felső szintű tartomány szerepel az Azure AD-bérlő a kibocsátó meg kell egyeznie a megadott URI-beállítás konfigurálva tartományonként.|

>[!IMPORTANT]
>Jelenleg az Azure AD SAML 2.0:urn:oasis:names:tc:SAML:2.0:nameid támogatja a NameID formátuma a következő URI-formátum: állandó.

## <a name="sample-saml-request-and-response-messages"></a>Példa SAML kérés- és üzenetek
Egy kérelem-válasz üzenet pár jelenik meg a bejelentkezés üzenet exchange-hez.
Ez az egy minta-üzenet, az Azure AD egy minta SAML 2.0 identitásszolgáltató küldött. A minta SAML 2.0 identitásszolgáltató az Active Directory összevonási szolgáltatások (AD FS) SAML-P protokoll használatára konfigurálva. Együttműködés ellenőrzésére is befejeződött egyéb SAML 2.0 identitás-szolgáltatóktól.

    `<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" IssueInstant="2014-01-30T16:18:35Z" Version="2.0" AssertionConsumerServiceIndex="0" >
    <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
    <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>`

Ez a minta SAML 2.0-s megfelelő identitásszolgáltató az Azure AD által küldött minta válaszüzenetet az / Office 365.

    `<samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
        <ds:X509Data>
          <ds:X509Certificate>MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyhBREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDTE1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9ybWVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/+3ZWxd9T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEMb2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcCAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvyJOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBySx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==</ds:X509Certificate>
        </ds:X509Data>
      </KeyInfo>
    </ds:Signature>
    <Subject>
      <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">ABCDEG1234567890</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" NotOnOrAfter="2014-01-31T15:41:31.357Z" Recipient="https://login.microsoftonline.com/login.srf" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2014-01-31T15:36:31.263Z" NotOnOrAfter="2014-01-31T16:36:31.263Z">
      <AudienceRestriction>
        <Audience>urn:federation:MicrosoftOnline</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="IDPEmail">
        <AttributeValue>administrator@contoso.com</AttributeValue>
      </Attribute>
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2014-01-31T15:36:30.200Z" SessionIndex="_7e3c1bcd-f180-4f78-83e1-7680920793aa">
      <AuthnContext>
        <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
    </Assertion>
    </samlp:Response>`

## <a name="configure-your-saml-20-compliant-identity-provider"></a>A SAML 2.0-s megfelelő identitásszolgáltató konfigurálása
Ez a témakör a SAML 2.0 Identitásszolgáltatóként az Azure AD-egyszeri bejelentkezés hozzáférésének engedélyezése egy vagy több Microsoft felhőszolgáltatás (például az Office 365) a SAML 2.0 protokoll segítségével összevonni kívánt konfigurálásával kapcsolatos útmutatást tartalmaz. A függő entitás egy Microsoft felhőszolgáltatásra, ebben a forgatókönyvben használt SAML 2.0 az Azure AD.

## <a name="add-azure-ad-metadata"></a>Az Azure AD-metaadatok hozzáadása
A SAML 2.0 identitásszolgáltató kell igazodnia kell az Azure AD függő entitás kapcsolatos információkat. Az Azure AD közzéteszi a metaadatok https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml.

Ajánlott mindig importálni a legújabb Azure AD-metaadatok a SAML 2.0 identitásszolgáltató konfigurálásakor. Vegye figyelembe, hogy az Azure AD nem képes metaadatokat az identitásszolgáltató által.

## <a name="add-azure-ad-as-a-relying-party"></a>Az Azure AD hozzáadni egy függő entitás
A SAML 2.0 identitásszolgáltató és az Azure AD közötti kommunikáció engedélyezésére van. Ez a konfiguráció a megadott identitásszolgáltató függ, és azt dokumentációjában tájékozódhat. Akkor általában értékre kell állítania a függő entitás azonosítója megegyezik a entityid beállítást az Azure AD metaadatok alapján.

>[!NOTE]
>Ellenőrizze, hogy a SAML 2.0 identitás szolgáltató kiszolgálón az óra szinkronizálva van a pontos idő forráshoz. A pontos idő okozhat az összevont bejelentkezés sikertelen.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Telepítse a Windows PowerShell bejelentkezéshez a SAML 2.0 identitásszolgáltató
Miután konfigurálta a SAML 2.0 identitásszolgáltató használható az Azure AD bejelentkezés, a következő lépéssel letöltése és telepítése az Active Directory modul Windows Powershellhez készült Azure. A telepítést követően ezek a parancsmagok használandó összevont tartományt az Azure AD-tartományok konfigurálása.

Az Active Directory modul Windows Powershellhez készült Azure egy letölthető kezeléséhez a szervezeti adatokat az Azure ad-ben. Ez a modul-parancsmagok egy gyűjteményét telepíti a Windows PowerShell; egyszeri bejelentkezéses hozzáférést az Azure AD beállítása adott parancsmagjainak a futtatását, és az összes olyan felhőszolgáltatáshoz, kapcsolja az előfizetett. Töltse le és telepítse a parancsmagokkal kapcsolatos útmutatásért lásd: [http://technet.microsoft.com/library/jj151815.aspx](http://technet.microsoft.com/library/jj151815.aspx)

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>A SAML-Identitásszolgáltatóként és az Azure AD közötti megbízhatósági kapcsolat beállítása
Összevonás konfigurálása az Azure AD-tartomány, mielőtt konfigurált egyéni tartományt kell rendelkeznie. A Microsoft által biztosított alapértelmezett tartomány nem vonható össze. Az alapértelmezett tartomány, a Microsoft az "onmicrosoft.com" végződik.
A Windows PowerShell parancssori felület segítségével tartományok egyszeri bejelentkezéshez, vagy adja hozzá a parancsmagok sorozatát kell futtatnia.

Minden a SAML 2.0 identitásszolgáltató használatával összevonni kívánt Azure Active Directory-tartomány vagy fel van véve egy egyszeri bejelentkezéses tartománnyá vagy kell általános tartományból egyszeri bejelentkezés tartományként konvertálni. Hozzáadása vagy konvertálása tartomány állít be a SAML 2.0 identitásszolgáltató és az Azure AD közötti megbízhatósági kapcsolat.

Az alábbi eljárás bemutatja, hogyan használja a SAML 2.0 SP-Lite összevont tartományhoz egy meglévő szabványos tartomány átalakításának lépésein. Vegye figyelembe, hogy a tartomány, amely hatással van a felhasználók fel 2 óra után ezzel a lépéssel kimaradás problémákat tapasztalhat.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Az Azure Active Directory összevonási a tartománynév beállítása


1. Bérlői rendszergazdaként az Azure AD-címtár csatlakozni: csatlakozás MsolService.
2.  Adja meg a kívánt Office 365 tartományt összevonási a SAML 2.0: `$dom = "contoso.com" $BrandName - "Sample SAML 2.0 IDP" $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" $MyURI = "urn:uri:MySamlp2IDP" $MySigningCert = @" MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" "@ $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" $Protocol = "SAMLP" Set-MsolDomainAuthentication -DomainName $dom -FederationBrandName $dom -Authentication Federated -PassiveLogOnUri $MyURI -ActiveLogOnUri $ecpUrl -SigningCertificate $MySigningCert -IssuerUri $uri -LogOffUri $url -PreferredAuthenticationProtocol $Protocol` 

3.  Az aláíró tanúsítvány a base64 kódolású karakterlánc kérhetnek a kiállító terjesztési hely metaadatait tartalmazó fájl. Ezen a helyen például adtak meg, de előfordulhat, hogy a megvalósítás alapján némileg eltérőek lehetnek.

    `<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"> <KeyDescriptor use="signing"> <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#"> <X509Data> <X509Certificate>MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate> </X509Data> </KeyInfo> </KeyDescriptor>` 

További információ a "Set-MsolDomainAuthentication":: [ http://technet.microsoft.com/library/dn194112.aspx ](http://technet.microsoft.com/library/dn194112.aspx).

>[!NOTE]
>Futtatnia kell a használata "$ecpUrl ="https://WS2012R2-0.contoso.com/PAOS"" csak akkor, ha az identitásszolgáltató beállított ECP kiterjesztéssel. Exchange Online ügyfelek, az Outlook Web Application (OWA), kivéve a FELADÁS egy vagy több alapú aktív végpontot. Ha a SAML 2.0 STS megvalósítja egy aktív végpontot egy aktív végpontot ECP végrehajtásának Shibboleth meg hasonló lehet a a funkciógazdag ügyfeleket, az Exchange Online szolgáltatással folytatott interakciót.

Összevonási konfigurálása után átválthatja vissza a "nem összevont" (vagy "kezelt"), azonban ez a módosítás befejezéséhez akár két órát vesz igénybe, és új véletlenszerű jelszót a felhőalapú bejelentkezéshez hozzárendelése minden felhasználóhoz van szükség. Váltás vissza a "kezelt" lehet kell bizonyos esetekben hibát jelez a beállítások visszaállítására. További információ a tartomány átalakítás:: [ http://msdn.microsoft.com/library/windowsazure/dn194122.aspx ](http://msdn.microsoft.com/library/windowsazure/dn194122.aspx).

## <a name="provision-user-principals-to-azure-ad--office-365"></a>Az Azure AD-felhasználó rendszerbiztonsági kiépítése vagy Office 365
Mielőtt a felhasználók az Office 365 felhasználói rendszerbiztonsági tagok, amelyek megfelelnek a helyességi feltétel a SAML 2.0 jogcímek kell ellátása az Azure AD képes hitelesíteni. Ha a felhasználó rendszerbiztonsági tagok nem ismert előre az Azure ad majd azok nem használható az összevont bejelentkezés. Az Azure AD Connect vagy a Windows PowerShell felhasználói rendszerbiztonsági tagok kiépítéséhez használható.

Az Azure AD Connect segítségével rendszerbiztonsági tagok kiépíteni az Azure Active Directoryban a tartományokhoz a helyszíni Active Directoryból. További részletekért lásd: [integrálása a helyszíni címtárakat az Azure Active Directoryval](active-directory-aadconnect.md).

Windows PowerShell is használható, az új felhasználók automatikus hozzáadása az Azure AD- és szinkronizálni a módosításokat a helyszíni címtárból. Le kell töltenie a Windows PowerShell-parancsmagok használatához a [Azure Active Directory modulok](https://docs.microsoft.com/powershell/azure/install-adv2?view=azureadps-2.0).

Ez az eljárás bemutatja, hogyan egyetlen felhasználó hozzáadása az Azure ad Szolgáltatásba.


1. Bérlői rendszergazdaként az Azure AD-címtár csatlakozni: csatlakozás MsolService.
2.  Hozzon létre egy új egyszerű: ` New-MsolUser
        -UserPrincipalName elwoodf1@contoso.com
        -ImmutableId ABCDEFG1234567890
        -DisplayName "Elwood Folk"
        -FirstName Elwood 
        -LastName Folk 
        -AlternateEmailAddresses "Elwood.Folk@contoso.com" 
        -LicenseAssignment "samlp2test:ENTERPRISEPACK" 
        -UsageLocation "US" ` 

További információ a "New-MsolUser" kivétel, [http://technet.microsoft.com/library/dn194096.aspx](http://technet.microsoft.com/library/dn194096.aspx)

>[!NOTE]
>A "UserPrinciplName" értéket meg kell egyeznie az értéket, amelyet el szeretne küldeni az "IDPEmail" a SAML 2.0 jogcímek, és a "ImmutableID" értéket meg kell egyeznie a "NameID" helyességi feltétel az elküldött értékkel.

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Egyszeri bejelentkezéshez a SAML 2.0 IDP rendelkező ellenőrzése
A rendszergazdának előtt győződjön meg arról, és az egyszeri bejelentkezés (is néven identitás-összevonási) kezeléséhez, tekintse át az adatokat, és hajtsa végre a lépéseket a következő cikkekben egyszeri bejelentkezéshez a SAML 2.0 SP-Lite alapján identitásszolgáltatóval beállítása:


1.  Tekintse át az Azure AD SAML 2.0 protokoll követelmények
2.  A SAML 2.0 identitásszolgáltató konfigurált
3.  Telepítse a Windows PowerShell egyszeri bejelentkezéshez a SAML 2.0 identitásszolgáltatóval
4.  SAML 2.0 identitásszolgáltató és az Azure AD közötti megbízhatósági kapcsolat beállítása
5.  Windows PowerShell vagy az Azure AD Connect üzembe helyezve az Azure Active Directoryba (Office 365) ismert teszt felhasználó rendszerbiztonsági tag.
6.  Konfigurálja a címtár-szinkronizálás használatával [az Azure AD Connect](active-directory-aadconnect.md).

Beállítása után egyszeri bejelentkezéshez a SAML 2.0 SP-Lite alapú identitású szolgáltató, ellenőrizze, hogy megfelelően működik-e.

>[!NOTE]
>Ha egy tartományhoz, nem pedig hozzáadott alakította át, az egyszeri bejelentkezés beállítása akár 24 óráig is eltarthat.
Egyszeri bejelentkezés ellenőriznie, mielőtt kell Active Directory-szinkronizálás beállításának befejezéséhez, a címtárak szinkronizálása, és a szinkronizált felhasználók aktiválása.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Az eszköz segítségével győződjön meg arról, hogy az egyszeri bejelentkezés beállítása megfelelően
Annak ellenőrzéséhez, hogy az egyszeri bejelentkezés beállítása megfelelően, bizonyosodjon meg arról, hogy a felhőalapú szolgáltatáshoz, a vállalati hitelesítő adataival jelentkezhet be a következő eljárás végezheti el.

A Microsoft közzétett egy eszközt, amely segítségével a SAML 2.0 alapján identitásszolgáltató tesztelése. A vizsgálati eszköz futtatása előtt konfigurálnia kell az identitásszolgáltató összevonni kívánt Azure AD-bérlő.

>[!NOTE]
>A kapcsolat Analyzer Internet Explorer 10 vagy újabb verzió szükséges.



1. Töltse le a kapcsolat Analyzer programban, [ https://testconnectivity.microsoft.com/?tabid=Client ](https://testconnectivity.microsoft.com/?tabid=Client).
2.  Kattintson a telepítés gombra a kezdéshez letöltése és telepítése az eszközt.
3.  Válassza ki a "I nem állítható be az Office 365, Azure vagy az Azure Active Directory használó más szolgáltatásokba összevonásának".
4.  Ha az eszköz letöltése és futtatása, a kapcsolat Diagnostics ablakban jelenik meg. Az eszköz rendszer végigvezeti a összevonási kapcsolat tesztelése.
5.  A kapcsolat Analyzer csak akkor nyílik meg, hogy a bejelentkezés, adja meg a hitelesítő adatait egyszerű teszteli a SAML 2.0 IDP: ![SAML](media/active-directory-aadconnect-federation-saml-idp/saml1.png)
6.  Bejelentkezési ablakban összevonási tesztelési meg kell megadása fióknevet és jelszót a SAML 2.0 identitásszolgáltatóval összevonáshoz konfigurált Azure AD-bérlő. Az eszköz megkísérli jelentkezzen be ezen hitelesítő adatok használatával, és a bejelentkezési kísérlet során végrehajtott ellenőrzések részletes eredményét nyújtanak output típusúként.
![SAML](media/active-directory-aadconnect-federation-saml-idp/saml2.png)
7. Ebben az ablakban látható a vizsgálat sikertelen eredményt. Kattintson a tekintse át a részletes adatok jelennek meg a-eredmény az egyes teszteket, hogy végre lett hajtva. Az eredmények ahhoz, hogy megoszthatja őket lemezre mentheti is.
 
>[!NOTE]
>A kapcsolat analyzer is teszteli, használja a WS * aktív összevonási-alapú és a ECP/PAOS protokollokat. Ha nem használja ezeket figyelmen kívül hagyhatja a következő hiba: az aktív bejelentkezési folyamat használja az identitásszolgáltató aktív összevonási végpont tesztelése.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Ellenőrizze, hogy az egyszeri bejelentkezés beállítása megfelelően manuálisan
Manuális ellenőrzést biztosít a további lépésekről győződjön meg arról, hogy a SAML 2.0 identity Provider számos forgatókönyvben megfelelően működik.
Győződjön meg arról, hogy az egyszeri bejelentkezés beállítása helyes, végezze el a következő lépéseket:


1. A tartományhoz csatlakoztatott számítógépen jelentkezzen be a felhőalapú szolgáltatás, a bejelentkezési névvel, amelyekkel a vállalati hitelesítő adatok használatával.
2.  Kattintson a jelszó mezőbe. Ha egyszeri bejelentkezésre van beállítva, a jelszó mező árnyékolt lesz, és a következő üzenet jelenik meg: "már követelmény jelentkezzen be a(z) <your company>."
3.  Kattintson a bejelentkezés a következő <your company> hivatkozásra. Ha jelentkezhetnek be, majd egyszeri bejelentkezést be van állítva.

## <a name="next-steps"></a>További lépések


- [Active Directory összevonási szolgáltatások kezelése és testreszabása az Azure AD Connecttel](active-directory-aadconnect-federation-management.md)
- [Az Azure AD összevonás kompatibilitási listája](active-directory-aadconnect-federation-compatibility.md)
- [Az Azure AD Connect egyéni telepítése](active-directory-aadconnect-get-started-custom.md)
