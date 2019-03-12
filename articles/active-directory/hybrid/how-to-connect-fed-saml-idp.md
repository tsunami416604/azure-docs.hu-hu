---
title: 'Azure AD Connect: SAML 2.0 identitásszolgáltató használata egyszeri bejelentkezéshez a |} A Microsoft Docs'
description: Ez a dokumentum ismerteti, hogy a megfelelő SAML 2.0 identitásszolgáltató használata egyszeri bejelentkezéshez a.
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
ms.openlocfilehash: 7b82c2261e949c724b1310ee43c6f3fe29766945
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531011"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Egyszeri bejelentkezéshez a SAML 2.0 identitásszolgáltató (IdP) használata

Ez a dokumentum információkat tartalmaz az identitásszolgáltatóval SAML 2.0-s szabványnak megfelelő SP-Lite profil-alapú, az előnyben részesített biztonsági jogkivonat-szolgáltatás (STS) / identitásszolgáltató. Ez a forgatókönyv akkor hasznos, ha már rendelkezik egy felhasználó könyvtárba, és tárolja a helyszíni SAML 2.0-val elérhető jelszót. A meglévő felhasználói címtár használható az Office 365-höz és más Azure AD által védett erőforrásokhoz való bejelentkezést. Az SAML 2.0-s SP-Lite profil a Security Assertion Markup Language (SAML) széles körben használt összevont identitás standard üzemeltetésbiztonsági bejelentkezési és attribútum exchange alapul.

>[!NOTE]
>3. fél identitásszolgáltató használatát, amely az Azure ad-ben való használatra nem tesztelt listáját lásd: a [az Azure AD összevonás kompatibilitási listája](how-to-connect-fed-compatibility.md)

A Microsoft támogatja a bejelentkezést egy Microsoft felhőszolgáltatásra, például az Office 365, a megfelelően konfigurált SAML 2.0-s profil-alapú IdP-integrációja. SAML 2.0 identitásszolgáltató harmadik féltől származó termékekre, és ezért a Microsoft nem biztosít támogatást az üzembe helyezés, konfigurálása, a hibaelhárítási ezekkel kapcsolatos ajánlott eljárásokat. Egyszer megfelelően konfigurálva, a SAML 2.0 identitásszolgáltató használatával a Microsoft-kapcsolat Analyzer eszközt, amely modellről további részleteket az alábbi megfelelő konfiguráció is tesztelve az integrációt. Az SAML 2.0-s SP-Lite, profil-alapú identitásszolgáltató kapcsolatos további részletekért kérje meg a szervezet, amely a megadott azt.

>[!IMPORTANT]
>Csak korlátozott számú ügyféllel esetünkben bejelentkezéshez a SAML 2.0 identitásszolgáltató érhető el, ez a következőket tartalmazza:

>- Web-alapú ügyfelekről, például az Outlook Web Access és a SharePoint online-hoz
- E-mailek funkciókban gazdag ügyfél, amely használja az egyszerű hitelesítés és a egy támogatott az Exchange hozzáférési módszer IMAP, a jelenléti pontra Irányíthatja, Active Sync, a MAPI, stb. (Enhanced ügyfél protokoll végpontját kötelező telepíteni), például többek között:
    - A Microsoft Outlook 2010 vagy az Outlook 2013/Outlook 2016, az Apple iPhone (különböző IOS-es verzió)
    - Különféle Google Android-eszközökhöz
    - Windows Phone 7, Windows Phone 7,8 és Windows Phone 8.0-s
    - Levelezőprogramján Windows 8 és Windows 8.1 Levelezőprogramján
    - A Windows 10-es Mail ügyfél

Bejelentkezés a példánkban a SAML 2.0 identitásszolgáltató a más ügyfelek számára nem érhetők el. Például nem sikerül bejelentkezni a szolgáltatásba a SAML 2.0 identitásszolgáltató van konfigurálva, az egyszeri bejelentkezés az a Lync 2010 asztali ügyfélhez.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Az Azure AD SAML 2.0 protokoll követelményei
Ez a dokumentum tartalmazza a protokollt és formázását, hogy a SAML 2.0 identitásszolgáltató az Azure ad-vel való bejelentkezés engedélyezése (például az Office 365) egy vagy több Microsoft felhőszolgáltatáshoz összevonásához musí implementovat üzenet részletes követelményeket. A SAML 2.0 függő entitás (SP-STS), egy Microsoft felhőszolgáltatásra, ebben a forgatókönyvben használt Azure ad-ben.

Javasoljuk, hogy biztosítania az SAML 2.0 identitásszolgáltató kimeneti üzenetek legyen olyan hasonló, a megadott minta nyomkövetések a lehető. A megadott attribútum értékeit is, használja az Azure AD-metaadatok ahol csak lehetséges. Ha elégedett a kimeneti üzenetek, tesztelheti a Microsoft Connectivity Analyzer az alább ismertetett.

Az Azure AD-metaadatok tölthető le az URL-cím: [ https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml ](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml).
Kínában használó ügyfelek az Office 365 China-specifikus példányát, a következő összevonási végpontot kell használni: [ https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml ](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml).

## <a name="saml-protocol-requirements"></a>SAML protokolljának követelményei
Ez a szakasz részletes hogyan a kérések és válaszok üzenet párok épülnek fel order annak érdekében, hogy az üzenetek megfelelően formázza.

Az Azure AD identitás-szolgáltatóktól, a SAML 2.0-s SP Lite profilját használó néhány meghatározott követelmények alábbi dolgozhat konfigurálható. A minta SAML kérések és válaszok üzenetek automatikus és manuális tesztelési együtt használja, használhatja az Azure ad-vel működtetéshez.

## <a name="signature-block-requirements"></a>Aláírás letiltása követelmények
Az SAML-válasz üzenet belül a az aláírás csomópont az üzenet magát a digitális aláírás információkat tartalmaz. Az aláírásblokk követelményei a következők:

1. A helyességi feltétel a csomópont önmaga alá kell írni.
2.  Az RSA-sha1 algoritmusok, a DigestMethod kell használni. Más digitális aláírási algoritmusokat a rendszer nem fogad el.
   `<ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  Az XML-dokumentum is előfordulhat, hogy jelentkezik. 
4.  Az átalakítás algoritmus meg kell egyeznie a következő mintában szereplő értékekkel:    `<ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  A SignatureMethod algoritmus meg kell egyeznie a következő mintát:   `<ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Támogatott kötések
Kötések paraméterei szállítással kapcsolatos kommunikáció szükséges. Az alábbi követelmények vonatkoznak a kötések

1. HTTPS a szükséges átviteli.
2.  Azure ad-ben HTTP POST szükséges jogkivonat beküldése a bejelentkezés során
3.  Az Azure AD a hitelesítési kérelem az identitásszolgáltató kijelentkezési üzenetet az identitásszolgáltató és az ÁTIRÁNYÍTÁSI fogja használni a HTTP POST.

## <a name="required-attributes"></a>Szükséges attribútumok
A táblázat azokat az attribútumokat követelmények az SAML 2.0-üzenetben.
 
|Attribútum|Leírás|
| ----- | ----- |
|NameID|A helyességi feltétel értéke ugyanaz, mint az Azure AD-felhasználó ImmutableID kell lennie. Legfeljebb 64 alfanumerikus karakter lehet. Minden olyan-html biztonságos karaktereket kell kódolni, például a "+" karaktert jelenik meg, mint ".2B".|
|IDPEmail|Az egyszerű felhasználónév (UPN) nevű IDPEmail elemként szerepel a SAML-válasz a felhasználó UserPrincipalName (UPN) az Azure AD és Office 365-ben. Az egyszerű felhasználónév az e-mail cím formátumú. Egyszerű felhasználónevet, a Windows Office 365-ben (Azure Active Directory).|
|Kiállító|Szükséges lehet egy URI-ját az identitásszolgáltató. A minta üzeneteket a kiállító nem használja újra. Ha több felső szintű tartomány szerepel az Azure AD-bérlőt a kibocsátó meg kell egyeznie a megadott URI-beállítás tartományonként konfigurálva.|

>[!IMPORTANT]
>Az Azure AD jelenleg támogatja a SAML 2.0:urn:oasis:names:tc:SAML:2.0:nameid NameID formátum a következő URI-formátum: állandó.

## <a name="sample-saml-request-and-response-messages"></a>Példa SAML kérés- és üzenetek
Egy kérelem-válasz üzenet pár jelenik meg a bejelentkezési üzenet Exchange-hez.
Az alábbiakban látható egy minta megfelelő kérelemüzenet, az Azure ad-ből egy minta SAML 2.0 identitásszolgáltató küldött. A minta az SAML 2.0 identitásszolgáltató az Active Directory összevonási szolgáltatások (AD FS) használatára a SAML-P protokollt konfigurált. Együttműködés a tesztelési is befejeződött az egyéb SAML 2.0 identitásszolgáltató.

    `<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" IssueInstant="2014-01-30T16:18:35Z" Version="2.0" AssertionConsumerServiceIndex="0" >
    <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
    <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>`

Az alábbiakban található egy minta válaszüzenetet, amely a minta az SAML 2.0-s szabványnak megfelelő identitásszolgáltató is küld az Azure AD és Office 365-höz.

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

## <a name="configure-your-saml-20-compliant-identity-provider"></a>A megfelelő SAML 2.0 identitásszolgáltató konfigurálása
Ez a szakasz a vonni az Azure AD egyszeri bejelentkezéses hozzáférést egy vagy több Microsoft felhőszolgáltatáshoz (például az Office 365) az SAML 2.0 protokoll használatával engedélyezéséhez az SAML 2.0 identitásszolgáltató konfigurálása irányelveket tartalmaz. Az SAML 2.0, függő entitás egy Microsoft felhőszolgáltatásra, ebben a forgatókönyvben használt Azure ad-ben.

## <a name="add-azure-ad-metadata"></a>Az Azure AD-metaadatok hozzáadása
Az SAML 2.0 identitásszolgáltató van az Azure AD függő entitás kapcsolatos információkat. Az Azure AD közzéteszi a következő helyen lévő metaadatainak https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml.

Javasoljuk, hogy mindig importálja a legújabb Azure AD-metaadatok a SAML 2.0 identitásszolgáltató konfigurálása során.

>[!NOTE]
>Az Azure AD nem az identitásszolgáltató metaadatok olvasni.

## <a name="add-azure-ad-as-a-relying-party"></a>Az Azure AD függő entitás hozzáadása
Engedélyeznie kell a SAML 2.0 identitásszolgáltató és az Azure AD közötti kommunikációt. Ez a konfiguráció függ az adott identitásszolgáltató és hozzá tartozó dokumentáció hivatkozzon. Akkor általában értékre kell állítania a függő entitás azonosítója megegyezik a entityID Azure ad-ben metaadatok alapján.

>[!NOTE]
>Ellenőrizze, hogy pontos időforrás az SAML 2.0-s szolgáltató identitáskiszolgálók órája szinkronizálva lesz. Egy pontos idő okozhat az összevont bejelentkezés meghiúsul.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Windows PowerShell telepítése bejelentkezéshez a SAML 2.0 identitásszolgáltató
Az SAML 2.0 identitásszolgáltató használata az Azure AD bejelentkezés konfigurálása után a következő lépésre, töltse le és telepítse az Azure Active Directory modul a Windows PowerShell. A telepítést követően az Azure AD-tartományok konfigurálása összevont tartományok használandó ezeket a parancsmagokat.

Az Azure Active Directory modul a Windows PowerShell az Azure AD a szervezetek adatkezelési letöltés. Ez a modul olyan parancsmagok készletét telepíti a Windows Powershellbe; azok a parancsmagok, egyszeri bejelentkezéses hozzáférést az Azure AD beállításához futtatja, és kapcsolja be az összes, a cloud Services az előfizetett. Töltse le és telepítse a parancsmagokkal kapcsolatos útmutatásért lásd: [https://technet.microsoft.com/library/jj151815.aspx](https://technet.microsoft.com/library/jj151815.aspx)

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Az identitásszolgáltató SAML és az Azure AD közötti megbízhatósági kapcsolat beállítása
Összevonás konfigurálása az Azure AD-tartomány, mielőtt konfigurált egy egyéni tartományt kell rendelkeznie. A Microsoft által biztosított alapértelmezett tartomány nem vonható össze. Az alapértelmezett tartomány a Microsoft az "onmicrosoft.com" végződik.
A Windows PowerShell parancssori felület hozzáadásához, vagy az egyszeri bejelentkezés tartományok átalakítása a parancsmagok sorozatát fog futni.

Minden egyes az SAML 2.0 identitásszolgáltató használatával összevonni kívánt Azure Active Directory-tartományban kell hozzá egyetlen tartományként bejelentkezés, vagy alakítsa általános tartományból egyetlen bejelentkezési tartományt. Hozzáadása vagy konvertálása egy tartományt állít be a SAML 2.0 identitásszolgáltató és az Azure AD közötti megbízhatósági kapcsolat.

Az alábbi eljárás végigvezeti egy meglévő standard szintű tartomány átalakítása összevont tartományt használó SAML 2.0-s SP-Lite. 

>[!NOTE]
>A tartományhoz, amely hatással van a felhasználók be 2 óra után ezzel a lépéssel kimaradás tapasztalhat.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Az Azure AD Directory összevonáshoz tartomány konfigurálása


1. Bérlői rendszergazdaként csatlakozhat az Azure AD-címtár: Connect-MsolService .
2.  Adja meg a kívánt Office 365-tartomány összevonási használatához az SAML 2.0-val: `$dom = "contoso.com" $BrandName - "Sample SAML 2.0 IDP" $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" $MyURI = "urn:uri:MySamlp2IDP" $MySigningCert = @" MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" "@ $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" $Protocol = "SAMLP" Set-MsolDomainAuthentication -DomainName $dom -FederationBrandName $dom -Authentication Federated -PassiveLogOnUri $MyURI -ActiveLogOnUri $ecpUrl -SigningCertificate $MySigningCert -IssuerUri $uri -LogOffUri $url -PreferredAuthenticationProtocol $Protocol` 

3.  Az IDP metaadatait tartalmazó fájl szerezhet az aláíró tanúsítvány base64-kódolású karakterlánc. Példa erre a helyre lett megadva, de a megvalósítás alapján némileg eltérőek lehetnek.

    `<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"> <KeyDescriptor use="signing"> <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#"> <X509Data> <X509Certificate>MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate> </X509Data> </KeyInfo> </KeyDescriptor>` 

A "Set-MsolDomainAuthentication" kapcsolatos további információkért lásd: [ https://technet.microsoft.com/library/dn194112.aspx ](https://technet.microsoft.com/library/dn194112.aspx).

>[!NOTE]
>Használat kell futtatásakor "$ecpUrl ="https://WS2012R2-0.contoso.com/PAOS"" csak akkor, ha az identitásszolgáltató ECP bővítmény beállítása. Az Exchange Online ügyfelek, az Outlook Web Application (OWA), kivéve a POST-alapú aktív végpontot. Ha a SAML 2.0 STS valósít meg egy aktív végpontot egy aktív végpontot a Shibboleth ECP megvalósítása hasonló lehet az ezek olyan funkciógazdag ügyfeleket kommunikáljon az Exchange Online szolgáltatáshoz.

Összevonás konfigurálása után válthat vissza a "nem összevont" (vagy "felügyelt"), azonban ez a módosítás végrehajtásához akár két órát vesz igénybe, és új véletlenszerű jelszót a felhőbeli bejelentkezéshez hozzárendelése minden felhasználóhoz van szükség. Váltás vissza a "felügyeltnek" fiókdíjat bizonyos esetekben hiba a beállítások alaphelyzetbe állítása. A tartomány átalakítás további információk:: [ https://msdn.microsoft.com/library/windowsazure/dn194122.aspx ](https://msdn.microsoft.com/library/windowsazure/dn194122.aspx).

## <a name="provision-user-principals-to-azure-ad--office-365"></a>Az Azure AD egyszerű felhasználói névnek kiépítése / Office 365-höz
Hitelesítheti a felhasználókat, hogy az Office 365-höz, mielőtt a felhasználó rendszerbiztonsági tagok, amelyek megfelelnek a helyességi feltétel, a SAML 2.0 jogcímek üzembe helyezése Azure ad-ben. Ha a felhasználó rendszerbiztonsági tagok nem ismertek előzetesen Azure ad-hez, majd azok nem használható az összevont bejelentkezés. Az Azure AD Connect vagy a Windows PowerShell egyszerű felhasználói névnek kiépítéséhez használható.

Az Azure AD Connect használható egyszerű kiépítéséhez, a tartományok az Azure AD Directory a helyszíni Active Directoryból. Részletesebb információkért lásd: [a helyszíni címtárak integrálása az Azure Active Directory](whatis-hybrid-identity.md).

Windows PowerShell is használható, az új felhasználók automatikus hozzáadása az Azure AD- és szinkronizálni a módosításokat a helyszíni címtárból. A Windows PowerShell-parancsmagok használatához le kell töltenie a [Azure Active Directory-modulok](https://docs.microsoft.com/powershell/azure/install-adv2?view=azureadps-2.0).

Ez az eljárás bemutatja, hogyan egyetlen felhasználó hozzáadása az Azure ad-hez.


1. Bérlői rendszergazdaként csatlakozhat az Azure AD-címtár: Connect-MsolService.
2.  Hozzon létre egy új egyszerű: ` New-MsolUser
        -UserPrincipalName elwoodf1@contoso.com
        -ImmutableId ABCDEFG1234567890
        -DisplayName "Elwood Folk"
        -FirstName Elwood 
        -LastName Folk 
        -AlternateEmailAddresses "Elwood.Folk@contoso.com" 
        -LicenseAssignment "samlp2test:ENTERPRISEPACK" 
        -UsageLocation "US" ` 

További információ a "New-MsolUser" kivételt, [https://technet.microsoft.com/library/dn194096.aspx](https://technet.microsoft.com/library/dn194096.aspx)

>[!NOTE]
>A "UserPrinciplName" értékét meg kell egyeznie az érték, amely az SAML 2.0 jogcímet a "IDPEmail" küld, és a "ImmutableID" értékét meg kell egyeznie az értéket küldi el a "NameID" helyességi feltétel.

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Egyszeri bejelentkezés a SAML 2.0 Identitásszolgáltató-ellenőrzése
Rendszergazdaként előtt győződjön meg arról, és egyszeri bejelentkezést (is néven identitás-összevonással) kezelése, tekintse át az adatokat, és hajtsa végre a lépéseket egyszeri bejelentkezéshez a SAML 2.0-s SP-Lite alapján identitásszolgáltatóval beállításához a következő cikkeket:


1.  Tekintse át az Azure AD SAML 2.0 protokoll követelmények
2.  Az SAML 2.0 identitásszolgáltató konfigurálása
3.  Windows PowerShell telepítése az egyszeri bejelentkezés a SAML 2.0 identitásszolgáltató
4.  SAML 2.0 identitásszolgáltató és az Azure AD közötti megbízhatósági kapcsolat beállítása
5.  Üzembe helyezett egy ismert vizsgálati egyszerű az Azure Active Directoryhoz (Office 365), Windows PowerShell vagy az Azure AD Connect felületén keresztül.
6.  Konfigurálja a címtár-szinkronizálás használatával [az Azure AD Connect](whatis-hybrid-identity.md).

Után állítja be az egyszeri bejelentkezéshez a SAML 2.0-s SP-Lite-alapú identitással szolgáltató, ellenőrizze, hogy megfelelően működik-e.

>[!NOTE]
>Tartomány helyett felvétele alakította át, ha az egyszeri bejelentkezés beállítása akár 24 óráig is eltarthat.
Egyszeri bejelentkezés ellenőriznie, mielőtt kell Active Directory-szinkronizálás beállításának befejezéséhez, a címtárak szinkronizálása, és a szinkronizált felhasználók aktiválása.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Győződjön meg arról, hogy egyszeri bejelentkezés rendelkezik megfelelően be lett állítva a eszköz használatával
Győződjön meg arról, hogy egyszeri bejelentkezés beállítása helyes, hajtsa végre az alábbi eljárást, győződjön meg arról, hogy tudni jelentkezzen be a felhőszolgáltatáshoz a vállalati hitelesítő adataival.

A Microsoft közzétett egy eszköz, amellyel tesztelheti a SAML 2.0-alapú személyazonosság szolgáltató. Mielőtt futtatná a vizsgálati eszköz, konfigurálnia kell az identitásszolgáltató összevonni kívánt Azure AD-bérlővel.

>[!NOTE]
>A Connectivity Analyzer Internet Explorer 10 vagy újabb verziója szükséges.



1. Töltse le a Connectivity Analyzer, [ https://testconnectivity.microsoft.com/?tabid=Client ](https://testconnectivity.microsoft.com/?tabid=Client).
2.  Kattintson a telepítés gombra a kezdéshez letöltése és telepítése az eszközre.
3.  Válassza ki a "Nem tudok összevonás az Office 365, Azure vagy az Azure Active Directory használó egyéb szolgáltatásokba".
4.  Ha az eszközt letölti és futtatja, látni fogja a kapcsolódási diagnosztikát ablakot. Az eszköz fog végigvezeti az összevonási kapcsolat tesztelése.
5.  A Connectivity Analyzer nyílik meg, hogy jelentkezzen be, adja meg a hitelesítő adatait az egyszerű felhasználónév a tesztelt, a SAML 2.0 Identitásszolgáltató: ![SAML](./media/how-to-connect-fed-saml-idp/saml1.png)
6.  Az összevonási teszt bejelentkezési ablakban megadandó egy fiók nevét és jelszavát az Azure AD-bérlővel, amely konfigurálva van a SAML 2.0 identitásszolgáltató lesz összevonva. Az eszköz megkísérli a bejelentkezési hitelesítő adatokat használ, és a bejelentkezési kísérlet során végrehajtott tesztek részletes eredményét biztosítja kimenetként.
![SAML](./media/how-to-connect-fed-saml-idp/saml2.png)
7. Ezt az ablakot a tesztelés sikertelen eredményt jeleníti meg. Kattintva tekintse át a részletes eredmények minden egyes végrehajtott vizsgálat eredményei kapcsolatos információk jelennek meg. Az eredményeket, és annak érdekében, hogy megoszthatja azokat lemezre mentheti is.
 
>[!NOTE]
>A Connectivity analyzer is teszteli, WS * használatával aktív összevonást-alapú és a ECP/PAOS protokollokat. Ha nem használja a következő hiba figyelmen kívül hagyhatja ezeket: Az aktív bejelentkezési folyamata az identitásszolgáltató aktív összevonást végpont használatával tesztelése.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Ellenőrizze, hogy egyszeri bejelentkezés beállítása megfelelően manuálisan
Manuális ellenőrzést biztosít a további lépéseket, győződjön meg arról, hogy a SAML 2.0 identity Provider sokféle helyzetben megfelelően működik is igénybe vehet.
Annak ellenőrzéséhez, hogy egyszeri bejelentkezés rendelkezik megfelelően be lett állítva, a következő lépéseket:


1. A tartományhoz csatlakoztatott számítógépen jelentkezzen be a felhőszolgáltatáshoz a bejelentkezési névvel, amelyet használhat a vállalati hitelesítő adatok használatával.
2.  Kattintson a jelszó mezőbe. Egyszeri bejelentkezés be van állítva, ha a jelszó mező árnyékolt lesz, és a következő üzenetet láthatja: "Most kell bejelentkezni &lt;vállalata&gt;."
3.  Kattintson a bejelentkezés jelenleg &lt;vállalata&gt; hivatkozásra. Ha tudja, hogy jelentkezzen be, majd egyszeri bejelentkezéses be lett állítva.

## <a name="next-steps"></a>További lépések


- [Az Active Directory összevonási szolgáltatások kezelése és testreszabása az Azure AD Connecttel](how-to-connect-fed-management.md)
- [Az Azure AD összevonás kompatibilitási listája](how-to-connect-fed-compatibility.md)
- [Az Azure AD Connect testreszabott telepítése](how-to-connect-install-custom.md)
