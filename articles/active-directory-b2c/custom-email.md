---
title: Egyéni e-mail-ellenőrzések
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan szabhatja testre az ügyfeleknek küldött ellenőrző e-mailt, amikor regisztrálnak az Azure AD B2C-kompatibilis alkalmazások használatára.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6cc0508a63f26b955ac5e0ebf3ef58a184a35997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671636"
---
# <a name="custom-email-verification-in-azure-active-directory-b2c"></a>Egyéni e-mailek ellenőrzése az Azure Active Directory B2C-ben

Az Azure Active Directory B2C (Azure AD B2C) egyéni levelezésével személyre szabott e-maileket küldhet azoknak a felhasználóknak, akik regisztrálnak az alkalmazások használatára. A [DisplayControls](display-controls.md) (jelenleg előzetes verzióban) és egy külső e-mail szolgáltató használatával használhatja saját e-mail sablonját és *a Címzett:* címet és tárgyat, valamint támogathatja a honosítást és az egyéni egyszeri jelszó (OTP) beállításokat.

Az egyéni e-mail-ellenőrzéshez egy külső e-mail-szolgáltató, például [a SendGrid](https://sendgrid.com) vagy a [SparkPost](https://sparkpost.com), egy egyéni REST API vagy bármely HTTP-alapú e-mail szolgáltató (beleértve a sajátját is) használatát kell használnia. Ez a cikk a SendGridet használó megoldás beállítását ismerteti.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>SendGrid-fiók létrehozása

Ha még nem rendelkezik ilyentel, először hozzan létre egy SendGrid-fiókot (az Azure-ügyfelek havonta 25 000 ingyenes e-mailt oldhatnak fel). A beállítási útmutatót a SendGrid-fiók küldése az [Azure-ral című](../sendgrid-dotnet-how-to-send-email.md) [SendGrid-fiók](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) létrehozása című szakaszban találja.

Ügyeljen arra, hogy töltse ki azt a szakaszt, amelyben [SendGrid API-kulcsot hoz létre.](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key) Rögzítse az API-kulcsot egy későbbi lépésben való használatra.

## <a name="create-azure-ad-b2c-policy-key"></a>Azure AD B2C-házirendkulcs létrehozása

Ezután tárolja a SendGrid API-kulcsot egy Azure AD B2C-szabályzati kulcsban a szabályzatok hivatkozásához.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, és válassza ki az Azure AD B2C könyvtárat.
1. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
1. Az Áttekintés lapon válassza az **Identitásélmény-keretrendszer lehetőséget.**
1. Válassza a **Házirendkulcsok,** majd **a Hozzáadás**lehetőséget.
1. A **Beállítások** `Manual`területen válassza a lehetőséget.
1. Adja meg a házirendkulcs **nevét.** Például: `SendGridSecret`. Az előtag `B2C_1A_` automatikusan hozzáadódik a kulcs nevéhez.
1. A **Titkos**mezőbe írja be a korábban rögzített ügyféltitkot.
1. A Kulcs használata `Signature` **esetén**válassza a lehetőséget.
1. Kattintson a **Létrehozás** gombra.

## <a name="create-sendgrid-template"></a>SendGrid-sablon létrehozása

Az Azure AD B2C-házirendkulcsban tárolt SendGrid-fiókkal és SendGrid API-kulcsokkal hozzon létre egy SendGrid [dinamikus tranzakciós sablont.](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)

1. A SendGrid webhelyen nyissa meg a [tranzakciós sablonok](https://sendgrid.com/dynamic_templates) lapot, és válassza a **Sablon létrehozása**lehetőséget.
1. Írjon be egy `Verification email` egyedi sablonnevet, és válassza a **Mentés lehetőséget.**
1. Az új sablon szerkesztésének megkezdéséhez válassza a **Verzió hozzáadása lehetőséget.**
1. Válassza a **Kódszerkesztő,** majd a **Folytatás lehetőséget.**
1. A HTML-szerkesztőben illessze be a HTML-sablont, vagy használja a sajátját. A `{{otp}}` `{{email}}` paramétereket dinamikusan lecseréli az egyszeri jelszó értékre és a felhasználói e-mail címre.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. A **beállítások** kibontása a bal `{{subject}}`oldalon, és az **E-mail tárgya**mezőbe írja be a lehetőséget.
1. Válassza a **Sablon mentése lehetőséget.**
1. Térjen vissza a **Tranzakciós sablonok** lapra a vissza nyíl kiválasztásával.
1. Rögzítse a létrehozott sablon **azonosítóját** egy későbbi lépésben való használatra. Például: `d-989077fbba9746e89f3f6411f596fb96`. Ezt az azonosítót akkor adja meg, amikor [hozzáadja a jogcímek átalakítását.](#add-the-claims-transformation)

## <a name="add-azure-ad-b2c-claim-types"></a>Azure AD B2C jogcímtípusok hozzáadása

A házirendben adja hozzá a `<ClaimsSchema>` következő `<BuildingBlocks>`jogcímtípusokat a csoportban lévő elemhez.

Ezek a jogcímtípusok szükségesek az e-mail cím egyszeri jelszókód (OTP) használatával történő létrehozásához és ellenőrzéséhez.

```XML
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="sendGridReqBody">
  <DisplayName>SendGrid request body</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="VerificationCode">
  <DisplayName>Secondary Verification Code</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter your email verification code</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

## <a name="add-the-claims-transformation"></a>A jogcímek átalakításának hozzáadása

Ezután szükség van egy jogcímátalakítása a JSON-karakterlánc jogcím, amely a SendGridnek küldött kérelem törzse lesz.

A JSON-objektum szerkezetét az InputParameters és az InputClaims TransformationClaimTypes azonosítói határozzák meg. A dot notation-ban lévő számok tömbökre utalnak. Az értékek az InputClaims értékeiből és az InputParameters "Value" tulajdonságaiból származnak. A JSON jogcím-átalakításokról a [JSON jogcím-átalakítások](json-transformations.md)című témakörben talál további információt.

Adja hozzá a következő `<ClaimsTransformations>` jogcímek átalakítását a elemhez a területén. `<BuildingBlocks>` A jogcímátalakítási XML következő frissítései:

* Frissítse `template_id` az InputParameter értéket a [SendGrid létrehozása sablonban](#create-sendgrid-template)korábban létrehozott SendGrid tranzakciós sablon azonosítójával.
* Frissítse `from.email` a címértékét. Használjon érvényes e-mail címet, hogy megakadályozza az ellenőrző e-mail spamként való megjelölését.
* Frissítse a tárgysor bemeneti paraméterének értékét a `personalizations.0.dynamic_template_data.subject` szervezetnek megfelelő tárgyson.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
  </InputClaims>
  <InputParameters>
    <!-- Update the template_id value with the ID of your SendGrid template. -->
    <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
    <!-- Update with a subject line appropriate for your organization. -->
    <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>DataUri-tartalomdefiníció hozzáadása

A jogcímátalakítások `<BuildingBlocks>`alatt adja hozzá a következő [ContentDefinition-et](contentdefinitions.md) a 2.0.0-s verziójú adatURI-ra való hivatkozáshoz:

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>DisplayControl létrehozása

Az ellenőrző kijelző-vezérlő segítségével ellenőrizheti az e-mail címet a felhasználónak küldött ellenőrző kóddal.

Ez a példa kijelzővezérlő a következőkre van konfigurálva:

1. A `email` címjogcímtípus begyűjtése a felhasználótól.
1. Várja meg, amíg `verificationCode` a felhasználó megadja a jogcím típusát a felhasználónak küldött kóddal.
1. Térjen `email` vissza a kijelzővezérlőre hivatkozó, önérvényesítő technikai profilhoz.
1. A `SendCode` művelet használatával hozzon létre egy OTP-kódot, és küldjön egy e-mailt az OTP-kóddal a felhasználónak.

![Ellenőrző kód e-mail művelet küldése](media/custom-email/display-control-verification-email-action-01.png)

A tartalomdefiníciók alatt, még mindig `<BuildingBlocks>`belül, adja hozzá a következő [DisplayControl](display-controls.md) típusú [VerificationControl-t](display-control-verification.md) a házirendhez.

```XML
<DisplayControls>
  <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
    <DisplayClaims>
      <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
      <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
    </DisplayClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="email" />
    </OutputClaims>
    <Actions>
      <Action Id="SendCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
        </ValidationClaimsExchange>
      </Action>
      <Action Id="VerifyCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
        </ValidationClaimsExchange>
      </Action>
    </Actions>
  </DisplayControl>
</DisplayControls>
```

## <a name="add-otp-technical-profiles"></a>OTP technikai profilok hozzáadása

A `GenerateOtp` technikai profil létrehoz egy kódot az e-mail címhez. A `VerifyOtp` technikai profil ellenőrzi az e-mail címhez társított kódot. Módosíthatja a formátum konfigurációját és az egyszeri jelszó lejáratát. Az OTP technikai profiljairól további információt az [Egyszeri jelszótechnikai profil megadása című](one-time-password-technical-profile.md)témakörben talál.

Adja hozzá a következő `<ClaimsProviders>` technikai profilokat az elemhez.

```XML
<ClaimsProvider>
  <DisplayName>One time password technical profiles</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="GenerateOtp">
      <DisplayName>Generate one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">1200</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="ReuseSameCode">true</Item>
        <Item Key="MaxNumAttempts">5</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
      </OutputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="VerifyOtp">
      <DisplayName>Verify one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">VerifyCode</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
      </InputClaims>
    </TechnicalProfile>
   </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="add-a-rest-api-technical-profile"></a>REST API műszaki profil hozzáadása

Ez a REST API technikai profil hozza létre az e-mail tartalmat (a SendGrid formátumhasználatával). A RESTful technikai profilokról további információt a [RESTful technikai profil meghatározása című](restful-technical-profile.md)témakörben talál.

Az OTP technikai profilokhoz ugyanúgy adja `<ClaimsProviders>` hozzá a következő technikai profilokat az elemhez.

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendGrid">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Hivatkozás a DisplayControl vezérlőre

Az utolsó lépésben adjon hozzá egy hivatkozást a létrehozott DisplayControl vezérlőhöz. Cserélje le `LocalAccountSignUpWithLogonEmail` a meglévő, önérvényesítő technikai profilt a következőkre, ha az Azure AD B2C-szabályzat korábbi verzióját használta. Ez a `DisplayClaims` technikai profil a DisplayControl vezérlőre való hivatkozással használatos.

További információ: [Self-assert technical profile](restful-technical-profile.md) and [DisplayControl](display-controls.md).

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <DisplayName>Email signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
        <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
        <Item Key="language.button_continue">Create</Item>
        
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" Required="true" />
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" />
        <OutputClaim ClaimTypeReferenceId="newUser" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
      </ValidationTechnicalProfiles>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>[Nem kötelező] Az e-mailek honosítása

Az e-mail honosításához honosított karakterláncokat kell küldenie a SendGridnek vagy az e-mail szolgáltatónak. Például az e-mail tárgyának, törzsének, kódüzenetének vagy aláírásának honosításához. Ehhez használhatja a [GetLocalizedStringsTransformation](string-transformations.md) jogcímek átalakítása a lokalizált karakterláncok másolása jogcímtípusok. A `GenerateSendGridRequestBody` jogcímek átalakítása, amely létrehozza a JSON hasznos adat, a bemeneti jogcímek, amelyek tartalmazzák a honosított karakterláncok.

1. A szabályzatban határozza meg a következő karakterlánc-jogcímeket: tárgy, üzenet, codeIntro és aláírás.
1. [GetLocalizedStringsTransformation jogcímek](string-transformations.md) átalakítása, hogy helyettesítse a honosított karakterlánc-értékeket a jogcímek az 1.
1. Módosítsa `GenerateSendGridRequestBody` a jogcímek átalakítását úgy, hogy a bemeneti jogcímek használata a következő XML-kódrészlettel.
1. Frissítse a SendGrind-sablont, hogy dinamikus paramétereket használjon az Azure AD B2C által honosított karakterláncok helyett.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="personalizations.0.dynamic_template_data.subject" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
    <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="personalizations.0.dynamic_template_data.message" />
    <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="personalizations.0.dynamic_template_data.codeIntro" />
    <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="personalizations.0.dynamic_template_data.signature" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-1234567890" />
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="next-steps"></a>További lépések

A GitHubon példát találhat egy egyéni e-mail-ellenőrzési szabályzatra:

[Egyéni e-mail ellenőrzés - DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)

Az egyéni REST API vagy bármely HTTP-alapú SMTP-e-mail-szolgáltató használatáról az [Azure AD B2C egyéni szabályzatban restful technikai profil definiálása című témakörben](restful-technical-profile.md)talál további információt.
