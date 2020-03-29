---
title: 'Azure AD Connect: SAML 2.0 identitásszolgáltató használata egyszeri bejelentkezéshez – Azure'
description: Ez a dokumentum az SAML 2.0-nak megfelelő Idp használatát ismerteti egyszeri bejelentkezéshez.
services: active-directory
author: billmath
manager: daveba
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff4f1b81f476159c13d3725cd3cb1766aec7dc87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70305101"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>SAML 2.0 identitásszolgáltató használata egyszeri bejelentkezéshez

Ez a dokumentum az SAML 2.0-kompatibilis SP-Lite profilalapú identitásszolgáltató előnyben részesített biztonsági jogkivonat-szolgáltatásként (STS) / identitásszolgáltatóként való használatával kapcsolatos információkat tartalmaz. Ez a forgatókönyv akkor hasznos, ha már rendelkezik egy felhasználói címtár és jelszó tároló helyszíni, amely az SAML 2.0 használatával érhető el. Ez a meglévő felhasználói címtár használható az Office 365-be és más Azure AD által védett erőforrásokba való bejelentkezéshez. Az SAML 2.0 SP-Lite profil a széles körben használt security assertion markup language (SAML) összevont identitásszabványon alapul, amely bejelentkezési és attribútumcsere-keretrendszert biztosít.

>[!NOTE]
>Az Azure AD-vel való használatra tesztelt harmadik féltől származó idpszek listáját az [Azure AD összevonási kompatibilitási listája tartalmazza.](how-to-connect-fed-compatibility.md)

A Microsoft támogatja ezt a bejelentkezési élményt, mint a Microsoft felhőszolgáltatás, például az Office 365 integrálását a megfelelően konfigurált SAML 2.0 profilalapú IdP-vel. Az SAML 2.0 identitásszolgáltatók külső termékek, ezért a Microsoft nem támogatja a telepítést, a konfigurációt és a velük kapcsolatos gyakorlati tanácsok elhárítását. A megfelelő konfigurálás után az SAML 2.0 identitásszolgáltatóval való integráció a Microsoft Connectivity Analyzer tool segítségével tesztelhető a megfelelő konfigurációérdekében, amelyet az alábbiakban részletesebben ismertetünk. Az SAML 2.0 SP-Lite profilalapú identitásszolgáltatóval kapcsolatos további információkért forduljon a szervezethez, amely azt biztosította.

> [!IMPORTANT]
> Ebben a bejelentkezési forgatókönyvben csak korlátozott számú ügyfél érhető el az SAML 2.0 identitásszolgáltatókkal, ez a következőket foglalja magában:
> 
> - Webalapú ügyfelek, például az Outlook Web Access és a SharePoint Online
> - Az egyszerű hitelesítést és a támogatott Exchange-hozzáférési módszert ( Például IMAP, POP, Active Sync, MAPI stb.
>     - Microsoft Outlook 2010/Outlook 2013/Outlook 2016, Apple iPhone (különböző iOS-verziók)
>     - Különböző Google Android eszközök
>     - Windows Phone 7, Windows Phone 7.8 és Windows Phone 8.0
>     - Windows 8 mail ügyfél és Windows 8.1 levelezőprogram
>     - Windows 10 levelezőprogram

Az összes többi ügyfél nem érhető el ebben a bejelentkezési forgatókönyvben az SAML 2.0 identitásszolgáltatóval. Az asztali Lync 2010-ügyfél például nem tud bejelentkezni a szolgáltatásba az egyszeri bejelentkezésre beállított SAML 2.0 identitásszolgáltatóval.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Az Azure AD SAML 2.0 protokollkövetelményei
Ez a dokumentum részletes követelményeket tartalmaz a protokoll és az üzenet formázását, amely az SAML 2.0 identitásszolgáltató nak végre kell hajtania az összeegyeztetés az Azure AD-vel, hogy lehetővé tegye a bejelentkezést egy vagy több Microsoft felhőszolgáltatások (például az Office 365). Az SAML 2.0 függő entitás (SP-STS) egy ebben a forgatókönyvben használt Microsoft felhőszolgáltatás az Azure AD.

Javasoljuk, hogy győződjön meg arról, hogy az SAML 2.0 identitásszolgáltató kimeneti üzenetek a lehető legközelebb állnak a megadott minta-nyomkövetések. Emellett adott attribútumértékeket is használhat a megadott Azure AD-metaadatokból, ahol lehetséges. Miután elégedett a kimeneti üzenetekkel, tesztelheti a Microsoft Connectivity Analyzer-rel az alábbiak szerint.

Az Azure AD metaadatai letölthetők [https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml)erről az URL-címről: .
Az Office 365 kínaspecifikus példányát használó kínai ügyfelek esetében a következő [https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml)összevonási végpontot kell használni: . .

## <a name="saml-protocol-requirements"></a>SAML protokollkövetelmények
Ez a szakasz részletezi, hogy a kérés- és válaszüzenetpárok hogyan vannak összerakva, hogy az üzenetek et megfelelően formázhassa.

Az Azure AD beállítható úgy, hogy az SAML 2.0 SP Lite-profilt használó identitásszolgáltatókkal működjön az alábbiakban felsorolt speciális követelményekkel. A minta SAML kérés- és válaszüzenetek, valamint az automatikus és manuális tesztelés használatával az Azure AD-vel való együttműködés elérésén munkálkodhat.

## <a name="signature-block-requirements"></a>Aláírási blokk követelményei
Az SAML-válasz üzenetben az aláírási csomópont információkat tartalmaz magáról az üzenethez tartozó digitális aláírásról. Az aláírásblokk követelményei a következők:

1. Magát az állítási csomópontot alá kell írni
2.  Az RSA-sha1 algoritmust kell használni digestmethod ként. Más digitális aláírási algoritmusok at nem fogadunk el.
   `<ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  Az XML-dokumentumot is aláírhatja. 
4.  Az átalakítási algoritmusnak meg kell egyeznie a következő minta értékeivel:`<ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  A SignatureMethod algoritmusnak meg kell egyeznie a következő mintával:`<ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Támogatott kötések
A kötések a szükséges átviteli kommunikációs paraméterek. A következő követelmények vonatkoznak a kötésekre

1. A HTTPS a szükséges átvitel.
2.  Az Azure AD-nek HTTP POST-ra lesz szüksége a tokenek bejelentkezéskori elküldéséhez.
3.  Az Azure AD http-post-ot fog használni az identitásszolgáltatónak küldött hitelesítési kérelemhez, és a REDIRECT-et az identitásszolgáltatónak küldött kijelentkezési üzenethez.

## <a name="required-attributes"></a>Kötelező attribútumok
Ez a táblázat az SAML 2.0 üzenet adott attribútumainak követelményeit mutatja be.
 
|Attribútum|Leírás|
| ----- | ----- |
|Névazonosító|Ennek a feltételnek meg kell egyeznie az Azure AD-felhasználó ImmutableID értékét. Legfeljebb 64 alfa numerikus karakter lehet. Minden nem html biztonságos karaktert kódolni kell, például egy "+" karakter ".2B" néven jelenik meg.|
|IDPEmail|Az egyszerű felhasználónév (UPN) az SAML válaszban az IDPEmail nevű elemként jelenik meg A felhasználó UserPrincipalName (UPN) névvel az Azure AD/Office 365-ben. Az upn e-mail cím formátumban van. Upn érték a Windows Office 365 (Azure Active Directory).|
|Kiállító|Az identitásszolgáltató URI-jának kell lennie. Ne használja fel újra a kiállítót a mintaüzenetekből. Ha az Azure AD-bérlők több legfelső szintű tartománnyal rendelkezik, a kiállítónak meg kell egyeznie a tartományonként konfigurált megadott URI-beállítással.|

>[!IMPORTANT]
>Az Azure AD jelenleg a következő NameID formátumú URI-t támogatja az SAML 2.0:urn:oázis:names:tc:SAML:2.0:nameid-format:persistent számára.

## <a name="sample-saml-request-and-response-messages"></a>Minta SAML kérés- és válaszüzenetek
A bejelentkezési üzenetváltáshoz egy kérés- és válaszüzenetpár jelenik meg.
Az alábbiakban egy mintakérelem-üzenet, amely az Azure AD-ből egy minta SAML 2.0 identitásszolgáltató. A minta SAML 2.0 identitásszolgáltató az Active Directory összevonási szolgáltatások (AD FS) SAML-P protokoll használatára konfigurálva. Az interoperabilitási tesztelés más SAML 2.0 identitásszolgáltatókkal is befejeződött.

    `<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" IssueInstant="2014-01-30T16:18:35Z" Version="2.0" AssertionConsumerServiceIndex="0" >
    <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
    <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>`

Az alábbiakban egy mintaválasz-üzenet, amely a minta SAML 2.0-kompatibilis identitásszolgáltató tól az Azure AD / Office 365 küldi el.

    `<samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
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

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Az SAML 2.0-kompatibilis identitásszolgáltató konfigurálása
Ez a szakasz az SAML 2.0 identitásszolgáltató azure AD-vel való összekötésének konfigurálására vonatkozó irányelveket tartalmaz, amelyek lehetővé teszik egy vagy több Microsoft felhőszolgáltatás (például az Office 365) egyszeri bejelentkezését az SAML 2.0 protokoll használatával. Az SAML 2.0 függő entitás egy Microsoft felhőszolgáltatás ebben a forgatókönyvben használt az Azure AD.

## <a name="add-azure-ad-metadata"></a>Azure AD-metaadatok hozzáadása
Az SAML 2.0 identitásszolgáltatónak meg kell felelnie az Azure AD függő entitásával kapcsolatos információknak. Az Azure AD metaadatokat tesz közzé a-ban. https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml

Javasoljuk, hogy mindig importálja a legújabb Azure AD metaadatokat az SAML 2.0 identitásszolgáltató konfigurálásakor.

>[!NOTE]
>Az Azure AD nem olvassa be a metaadatokat az identitásszolgáltatótól.

## <a name="add-azure-ad-as-a-relying-party"></a>Az Azure AD hozzáadása függő entitásként
Engedélyeznie kell a saml 2.0 identitásszolgáltató és az Azure AD közötti kommunikációt. Ez a konfiguráció az adott identitásszolgáltatótól függ, és olvassa el a dokumentációt. Általában állítsa be a függő entitás azonosítóját, hogy ugyanaz, mint az entitásazonosító az Azure AD metaadatok.

>[!NOTE]
>Ellenőrizze, hogy az SAML 2.0 identitásszolgáltató kiszolgálójának órája pontos időforrással van-e szinkronizálva. A pontatlan óraidő az összevont bejelentkezések sikertelensítését okozhatja.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>A Windows PowerShell telepítése saml 2.0-s identitásszolgáltatóval való bejelentkezéshez
Miután konfigurálta az SAML 2.0 identitásszolgáltatót az Azure AD bejelentkezéshez való használatra, a következő lépés az Azure Active Directory-modul windows PowerShellhez való letöltése és telepítése. A telepítés után ezeket a parancsmagokat fogja használni az Azure AD-tartományok összevont tartományokként történő konfigurálásához.

Az Azure Active Directory-modul a Windows PowerShell egy letöltés a szervezetek adatainak kezelésére az Azure AD-ben. Ez a modul parancsmagok készletét telepíti a Windows PowerShellbe; ezeket a parancsmagokat futtatva egyetlen bejelentkezési hozzáférést állíthat be az Azure AD-hez, és viszont az összes olyan felhőszolgáltatáshoz, amelyre előfizetett. A parancsmagok letöltésével és telepítésével kapcsolatos tudnivalókat a[https://technet.microsoft.com/library/jj151815.aspx](https://technet.microsoft.com/library/jj151815.aspx)

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Az SAML-identitásszolgáltató és az Azure AD közötti bizalmi kapcsolat beállítása
Az összevonás konfigurálása előtt egy Azure AD-tartományban, egyéni tartomány konfigurálva kell rendelkeznie. A Microsoft által biztosított alapértelmezett tartomány nem használható. A Microsoft alapértelmezett tartománya "onmicrosoft.com" végződik.
Parancsmagsorozatot fog futtatni a Windows PowerShell parancssori felületén az egyszeri bejelentkezéshez tartomány hozzáadása vagy konvertálása érdekében.

Minden Egyes Azure Active Directory-tartományt, amelyet az SAML 2.0 identitásszolgáltatóhasználatával össze kíván vonni, vagy egyetlen bejelentkezési tartományként kell hozzáadni, vagy úgy kell konvertálni, hogy egyetlen bejelentkezési tartomány legyen egy szabványos tartományból. A tartomány hozzáadása vagy átalakítása létrehoz egy megbízhatósági kapcsolatot az SAML 2.0 identitásszolgáltató és az Azure AD között.

Az alábbi eljárás bemutatja, hogy egy meglévő szabványos tartományt konvertál-nak összevont tartománysá az SAML 2.0 SP-Lite használatával. 

>[!NOTE]
>A tartomány előfordulhat, hogy kimaradás, amely hatással van a felhasználók akár 2 órával a lépés után.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Tartomány konfigurálása az Azure AD címtárban összevonásra


1. Csatlakozzon az Azure AD címtárhoz bérlői rendszergazdaként: Connect-MsolService.
2.  Konfigurálja a kívánt Office 365-tartományt úgy, hogy az SAML 2.0-s összevonását használja:`$dom = "contoso.com" $BrandName - "Sample SAML 2.0 IDP" $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" $MyURI = "urn:uri:MySamlp2IDP" $MySigningCert = @" MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" "@ $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" $Protocol = "SAMLP" Set-MsolDomainAuthentication -DomainName $dom -FederationBrandName $BrandName -Authentication Federated -PassiveLogOnUri $LogOnUrl -ActiveLogOnUri $ecpUrl -SigningCertificate $MySigningCert -IssuerUri $MyURI -LogOffUri $LogOffUrl -PreferredAuthenticationProtocol $Protocol` 

3.  Az aláíró tanúsítvány base64 kódolású karakterláncát az IDP metaadatfájlból szerezheti be. Erre a helyre példa állt rendelkezésre, de a megvalósítástól függően kis mértékben eltérhet.

    `<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"> <KeyDescriptor use="signing"> <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#"> <X509Data> <X509Certificate>MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate> </X509Data> </KeyInfo> </KeyDescriptor>` 

A "Set-MsolDomainAuthentication" beállításról további [https://technet.microsoft.com/library/dn194112.aspx](https://technet.microsoft.com/library/dn194112.aspx)információt a következő témakörben talál: .

>[!NOTE]
>A használatot `$ecpUrl = "https://WS2012R2-0.contoso.com/PAOS"` csak akkor kell futtatnia, ha eCP-bővítményt állít be az identitásszolgáltatóhoz. Az Exchange Online-ügyfelek az Outlook Web Application (OWA) kivételével a POST alapú aktív végpontra támaszkodnak. Ha az SAML 2.0 STS egy aktív végpontot valósít meg, amely hasonló a Shibboleth ECP-megvalósításához, akkor ezek a gazdag ügyfelek kapcsolatba léphetnek az Exchange Online szolgáltatással.

Az összevonás konfigurálása után visszaválthat "nem összevont" (vagy "felügyelt" értékre), azonban ez a módosítás akár két órát is igénybe vehet, és új véletlenszerű jelszavakat kell hozzárendelnie a felhőalapú bejelentkezéshez minden felhasználóhoz. A beállításokban bekövetkező hiba visszaállításához bizonyos esetekben szükség lehet a "felügyelt" üzemmódra való váltásra. A tartománykonvertálásról további [https://msdn.microsoft.com/library/windowsazure/dn194122.aspx](https://msdn.microsoft.com/library/windowsazure/dn194122.aspx)információt a következő témakörben talál: .

## <a name="provision-user-principals-to-azure-ad--office-365"></a>Felhasználói egyszerű felhasználók kiépítése az Azure AD/Office 365 szolgáltatásba
Mielőtt hitelesítené a felhasználókat az Office 365-ben, az Azure AD-t olyan felhasználónévvel kell üzembe állítania, amely megfelel az SAML 2.0-s jogcímben lévő állításnak. Ha ezek a felhasználói egyszerű stacs nem ismert az Azure AD előre, majd nem lehet használni az összevont bejelentkezéshez. Az Azure AD Connect vagy a Windows PowerShell felhasználói egyszerű felhasználók kiépítésére használható.

Az Azure AD Connect segítségével az Azure AD-címtárban lévő tartományok babérjaira lehet építeni a helyszíni Active Directoryból. További információt a [Helyszíni könyvtárak integrálása az Azure Active Directoryval című témakörben talál.](whatis-hybrid-identity.md)

A Windows PowerShell segítségével automatizálhatja az új felhasználók azure AD-hez való hozzáadását, és szinkronizálhatja a helyszíni címtárból származó módosításokat. A Windows PowerShell-parancsmagok használatához le kell töltenie az [Azure Active Directory-modulokat.](https://docs.microsoft.com/powershell/azure/install-adv2?view=azureadps-2.0)

Ez az eljárás bemutatja, hogyan adhat hozzá egyetlen felhasználót az Azure AD-hez.


1. Csatlakozzon az Azure AD-címtárhoz bérlői rendszergazdaként: Connect-MsolService.
2.  Új egyszerű felhasználó létrehozása:
    ```powershell
    New-MsolUser
      -UserPrincipalName elwoodf1@contoso.com
      -ImmutableId ABCDEFG1234567890
      -DisplayName "Elwood Folk"
      -FirstName Elwood 
      -LastName Folk 
      -AlternateEmailAddresses "Elwood.Folk@contoso.com" 
      -LicenseAssignment "samlp2test:ENTERPRISEPACK" 
      -UsageLocation "US" 
    ```

A "New-MsolUser" pénztárról további[https://technet.microsoft.com/library/dn194096.aspx](https://technet.microsoft.com/library/dn194096.aspx)

>[!NOTE]
>A "UserPrinciplName" értéknek meg kell egyeznie az "IDPEmail" címkén az "IDPEmail" címkén, és az "ImmutableID" értéknek meg kell egyeznie a "NameID" címkén küldött értékkel.

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Egyszeri bejelentkezés ellenőrzése az SAML 2.0 IDP-vel
Rendszergazdaként az egyszeri bejelentkezés (más néven identitás-összevonás) ellenőrzése és kezelése előtt tekintse át az információkat, és hajtsa végre az alábbi cikkekben leírt lépéseket az SAML 2.0 SP-Lite alapú identitásszolgáltatóval való egyszeri bejelentkezés beállításához:


1.  Áttekintette az Azure AD SAML 2.0 protokollkövetelményeit
2.  Beállította az SAML 2.0 identitásszolgáltatót
3.  A Windows PowerShell telepítése az SAML 2.0 identitásszolgáltatóval való egyszeri bejelentkezéshez
4.  Megbízhatóság beállítása az SAML 2.0 identitásszolgáltató ja- és az Azure AD között
5.  Egy ismert tesztfelhasználó takarásában az Azure Active Directory (Office 365) a Windows PowerShell vagy az Azure AD Connect.
6.  Címtár-szinkronizálás konfigurálása az [Azure AD Connect használatával.](whatis-hybrid-identity.md)

Miután beállítja az egyszeri bejelentkezést az SAML 2.0 SP-Lite alapú identitásszolgáltatóval, ellenőrizze, hogy megfelelően működik-e.

>[!NOTE]
>Ha egy tartományt konvertált, és nem adott hozzá egyet, akár 24 órát is igénybe vehet az egyszeri bejelentkezés beállítása.
Az egyszeri bejelentkezés ellenőrzése előtt fejezze be az Active Directory szinkronizálásának beállítását, szinkronizálja a könyvtárakat, és aktiválja a szinkronizált felhasználókat.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Az eszköz használata az egyszeri bejelentkezés megfelelő beállításának ellenőrzésére
Annak ellenőrzéséhez, hogy az egyszeri bejelentkezés megfelelően van-e beállítva, az alábbi eljárással ellenőrizheti, hogy képes-e a felhőszolgáltatásba a vállalati hitelesítő adatokkal való bejelentkezéshez.

A Microsoft biztosított egy eszközt, amellyel tesztelheti az SAML 2.0 alapú identitásszolgáltatót. A teszteszköz futtatása előtt konfigurálnia kell egy Azure AD-bérlőt, hogy összeegyeztesse az identitásszolgáltatóval.

>[!NOTE]
>A Kapcsolódási elemző használatához internet Explorer 10-es vagy újabb verzió szükséges.



1. Töltse le a Connectivity [https://testconnectivity.microsoft.com/?tabid=Client](https://testconnectivity.microsoft.com/?tabid=Client)Analyzer-t a alkalmazásból: .
2.  Kattintson a Telepítés gombra az eszköz letöltésének és telepítésének megkezdéséhez.
3.  Válassza a "Nem állítható be összevonás az Office 365-tel, az Azure-ral vagy más Azure Active Directoryt használó szolgáltatásokkal" lehetőséget.
4.  Az eszköz letöltése és futtatása után megjelenik a Konciadiagnika ablak. Az eszköz végigvezeti az összevonási kapcsolat tesztelésén.
5.  A Kapcsolódási analizáló megnyitja az SAML 2.0 IDP-t a bejelentkezéshez, adja ![meg a tesztelt felhasználó névvel kapcsolatos hitelesítő adatokat: SAML](./media/how-to-connect-fed-saml-idp/saml1.png)
6.  Az összevonási teszt bejelentkezési ablakban meg kell adnia egy fiók nevét és jelszavát az Azure AD-bérlő, amely konfigurálva van, hogy az SAML 2.0 identitásszolgáltatóval összeegyeztetve. Az eszköz megkísérli a bejelentkezést ezekkel a hitelesítő adatokkal, és a bejelentkezési kísérlet során végrehajtott tesztek részletes eredményeit adja meg kimenetként.
![SAML](./media/how-to-connect-fed-saml-idp/saml2.png)
7. Ebben az ablakban a tesztelés sikertelen eredménye látható. A Részletes eredmények áttekintése gombra kattintva az egyes elvégzett tesztek eredményeiről tájékoztatódhat. Az eredményeket lemezre is mentheti, hogy megoszthassa őket.
 
>[!NOTE]
>A kapcsolatelemző a WS*alapú és az ECP/PAOS protokollok használatával is teszteli az aktív összevonást. Ha nem használja ezeket, figyelmen kívül hagyhatja a következő hibát: Az aktív bejelentkezési folyamat tesztelése az identitásszolgáltató aktív összevonási végpontjával.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Az egyszeri bejelentkezés megfelelő beállítása manuálisan
A manuális ellenőrzés további lépéseket tartalmaz annak érdekében, hogy az SAML 2.0 identitásszolgáltató javunkra működjön számos esetben.
Az egyszeri bejelentkezés megfelelő beállításának ellenőrzéséhez hajtsa végre az alábbi lépéseket:


1. Tartományhoz csatlakozott számítógépen jelentkezzen be a felhőszolgáltatásba a vállalati hitelesítő adatokhoz használt bejelentkezési névvel.
2.  Kattintson a jelszó mezőbe. Ha egyszeres bejelentkezés van beállítva, a jelszó mező árnyékolva lesz, és a következő üzenet jelenik &lt;meg: "Most már be kell jelentkeznie a vállalatnál."&gt;
3.  Kattintson a Bejelentkezés &lt;a&gt; vállalatnál hivatkozásra. Ha be tud jelentkezni, akkor az egyszeri bejelentkezés be van állítva.

## <a name="next-steps"></a>Következő lépések


- [Az Active Directory összevonási szolgáltatások kezelése és testreszabása az Azure AD Connect segítségével](how-to-connect-fed-management.md)
- [Az Azure AD összevonás kompatibilitási listája](how-to-connect-fed-compatibility.md)
- [Az Azure AD Connect egyéni telepítése](how-to-connect-install-custom.md)
