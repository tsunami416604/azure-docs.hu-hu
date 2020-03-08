---
title: Egyéni e-mail-ellenőrzés
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan szabhatja testre az ügyfeleknek küldött ellenőrző e-maileket, amikor a Azure AD B2C-kompatibilis alkalmazások használatára jelentkeznek.
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
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671636"
---
# <a name="custom-email-verification-in-azure-active-directory-b2c"></a>Egyéni e-mail-ellenőrzés Azure Active Directory B2C

Az egyéni e-mailek használata Azure Active Directory B2C (Azure AD B2C) a testreszabott e-mailek küldése az alkalmazásait használó felhasználók számára. A [DisplayControls](display-controls.md) (jelenleg előzetes verzióban) és egy harmadik féltől származó e-mail-szolgáltató használatával saját e-mail-sablont használhat, *amely a* következőkből áll: cím és tárgy, valamint a honosítási és egyéni egyszeri jelszó (OTP) beállításai.

Az egyéni e-mail-ellenőrzéshez egy külső gyártótól származó e-mail-szolgáltató (például [SendGrid](https://sendgrid.com) vagy [SparkPost](https://sparkpost.com), egyéni REST API vagy bármilyen HTTP-alapú e-mail-szolgáltató használata szükséges). Ez a cikk a SendGrid-t használó megoldások beállítását ismerteti.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>SendGrid-fiók létrehozása

Ha még nem rendelkezik ilyennel, kezdje egy SendGrid-fiók beállításával (az Azure-ügyfelek minden hónapban feloldják a 25 000 ingyenes e-maileket). A beállítási utasításokért tekintse meg a [SendGrid-fiók létrehozása](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) című szakaszt, amely [bemutatja, hogyan küldhet e-mailt az Azure SendGrid használatával](../sendgrid-dotnet-how-to-send-email.md).

Mindenképpen fejezze be azt a szakaszt, amelyben [létrehoz egy SENDGRID API-kulcsot](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key). Jegyezze fel az API-kulcsot egy későbbi lépésben való használatra.

## <a name="create-azure-ad-b2c-policy-key"></a>Azure AD B2C házirend-kulcs létrehozása

Ezután tárolja a SendGrid API-kulcsot egy Azure AD B2C házirend-kulcsban a szabályzatok hivatkozásához.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a Azure ad B2C könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás**gombra.
1. A **Beállítások**területen válassza a `Manual`lehetőséget.
1. Adja meg a szabályzat kulcsának **nevét** . Például: `SendGridSecret`. A rendszer automatikusan hozzáadja a kulcs nevét a `B2C_1A_` előtaghoz.
1. A **Secret (titkos kulcs**) mezőben adja meg a korábban rögzített ügyfél-titkot.
1. A **kulcshasználat**beállításnál válassza a `Signature`lehetőséget.
1. Kattintson a **Létrehozás** gombra.

## <a name="create-sendgrid-template"></a>SendGrid-sablon létrehozása

Egy Azure AD B2C SendGrid-fiókkal létrehozott és SendGrid API-kulcs használatával hozzon létre egy SendGrid [dinamikus tranzakciós sablont](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/).

1. A SendGrid webhelyen nyissa meg a [tranzakciós sablonok](https://sendgrid.com/dynamic_templates) lapot, és válassza a **sablon létrehozása**lehetőséget.
1. Adja meg a sablon egyedi nevét, például `Verification email`, majd válassza a **Mentés**lehetőséget.
1. Az új sablon szerkesztésének megkezdéséhez válassza a **verzió hozzáadása**lehetőséget.
1. Válassza ki a **Kódszerkesztő** elemet, majd **folytassa a művelettel**.
1. A HTML-szerkesztőben illessze be a következő HTML-sablont, vagy használja a sajátját. A `{{otp}}` és `{{email}}` paramétereket a rendszer dinamikusan cseréli le az egyszeri jelszóval és a felhasználói e-mail-címmel.

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

1. Bontsa ki a bal oldalon a **Beállítások** elemet, és az **e-mailek tárgya**mezőbe írja be a `{{subject}}`.
1. Válassza a **sablon mentése**lehetőséget.
1. A vissza nyílra kattintva térjen vissza a **tranzakciós sablonok** oldalára.
1. Jegyezze fel a létrehozott sablon **azonosítóját** egy későbbi lépésben való használatra. Például: `d-989077fbba9746e89f3f6411f596fb96`. Ezt az azonosítót kell megadnia [a jogcím-átalakítás hozzáadásakor](#add-the-claims-transformation).

## <a name="add-azure-ad-b2c-claim-types"></a>Azure AD B2C jogcím-típusok hozzáadása

A szabályzatban adja hozzá a következő típusú jogcímeket a `<ClaimsSchema>` elemhez `<BuildingBlocks>`on belül.

Ezek a jogcím-típusok szükségesek az e-mail-cím egyszeri jelszavas (OTP) kóddal történő létrehozásához és ellenőrzéséhez.

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

## <a name="add-the-claims-transformation"></a>Jogcím-átalakítás hozzáadása

Ezután szükség van egy jogcím-átalakításra, amely egy JSON-karakterlánc-jogcím kimenetét adja meg, amely a kérelem törzse lesz, amelyet a SendGrid küld.

A JSON-objektum struktúráját az azonosítók a InputParameters és a Szabályzattípushoz TransformationClaimTypes határozza meg. A pont jelölésében szereplő számok tömböket jelentenek. Az értékek a Szabályzattípushoz "Values" és a InputParameters "" érték "tulajdonságaiból származnak. A JSON-jogcímek átalakításával kapcsolatos további információkért lásd a [JSON-jogcímek átalakítását](json-transformations.md)ismertető témakört.

Adja hozzá a következő jogcímek átalakítását a `<ClaimsTransformations>` elemhez `<BuildingBlocks>`on belül. Hajtsa végre a következő frissítéseket a jogcím-átalakítási XML-ben:

* Frissítse a `template_id` InputParameter értéket a [SendGrid létrehozása sablonban](#create-sendgrid-template)korábban létrehozott SendGrid tranzakciós sablon azonosítójával.
* Frissítse a `from.email`-címtartomány értékét. Érvényes e-mail-cím használatával megakadályozhatja, hogy az ellenőrző e-mailek levélszemétként legyenek megjelölve.
* Frissítse a `personalizations.0.dynamic_template_data.subject` tárgyú bemeneti paraméter értékét a szervezete számára megfelelő tárgyi vonallal.

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

## <a name="add-datauri-content-definition"></a>DataUri-definíció hozzáadása

A `<BuildingBlocks>`on belüli jogcím-átalakítások alatt adja hozzá a következő [ContentDefinition](contentdefinitions.md) a verzióra vonatkozó 2.0.0-adaturi-azonosítóra való hivatkozáshoz:

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>DisplayControl létrehozása

A rendszer ellenőrzi, hogy az e-mail-cím a felhasználónak küldött ellenőrző kóddal van-e meghatározva.

Ez a példa megjelenítési vezérlő a következőre van konfigurálva:

1. Gyűjtse össze az `email`-jogcím típusát a felhasználótól.
1. Várjon, amíg a felhasználó meg nem adja a `verificationCode` jogcím típusát a felhasználónak elküldett kóddal.
1. Visszaküldi a `email` vissza az önérvényesített technikai profilra, amely erre a megjelenítési vezérlőelemre hivatkozik.
1. A `SendCode` művelettel egy egyszeri jelszavas kódot kell előállítania, és e-mailt kell küldenie az OTP-kóddal a felhasználónak.

![Ellenőrző kód e-mail-műveletének küldése](media/custom-email/display-control-verification-email-action-01.png)

A tartalmi definíciók területen még mindig a `<BuildingBlocks>`on belül adja hozzá a következő [DisplayControl](display-controls.md) típusú [VerificationControl](display-control-verification.md) a szabályzathoz.

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

A `GenerateOtp` technikai profil létrehoz egy kódot az e-mail-cím számára. A `VerifyOtp` technikai profil ellenőrzi az e-mail-címmel társított kódot. Módosíthatja a formátum konfigurációját és az egyszeri jelszó lejárati idejét. Az OTP technikai profiljaival kapcsolatos további információkért lásd: [egyszeri jelszóval kapcsolatos technikai profil meghatározása](one-time-password-technical-profile.md).

Adja hozzá a következő technikai profilokat a `<ClaimsProviders>` elemhez.

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

## <a name="add-a-rest-api-technical-profile"></a>REST API technikai profil hozzáadása

Ez a REST API technikai profil az e-mail-tartalmat hozza létre (a SendGrid formátum használatával). További információ a REST-technikai profilokról: [Rest-technikai profil meghatározása](restful-technical-profile.md).

Az OTP technikai profiljaihoz hasonlóan adja hozzá a következő technikai profilokat a `<ClaimsProviders>` elemhez.

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

## <a name="make-a-reference-to-the-displaycontrol"></a>Hivatkozás készítése a DisplayControl

Az utolsó lépésben adjon hozzá egy hivatkozást a létrehozott DisplayControl. Cserélje le a meglévő `LocalAccountSignUpWithLogonEmail` önjelölt technikai profilt a következőre, ha a Azure AD B2C házirend korábbi verzióját használta. Ez a technikai profil `DisplayClaims`t használ a DisplayControl mutató hivatkozással.

További információ: [önérvényesített technikai profil](restful-technical-profile.md) és [DisplayControl](display-controls.md).

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

## <a name="optional-localize-your-email"></a>Választható E-mail honosítása

Az e-mail honosítása érdekében honosított karakterláncokat kell küldenie a SendGrid vagy az e-mail-szolgáltatónak. Például az e-mail-tárgy, a törzs, a kód üzenet vagy az e-mail aláírásának honosítása. Ehhez használhatja a [GetLocalizedStringsTransformation](string-transformations.md) jogcím-átalakítást a honosított karakterláncok jogcím-típusokra való másolásához. A JSON-adattartalmat generáló `GenerateSendGridRequestBody` jogcím-átalakítás során a honosított karakterláncokat tartalmazó bemeneti jogcímeket használja.

1. A szabályzatban adja meg a következő karakterlánc-jogcímeket: tulajdonos, üzenet, codeIntro és aláírás.
1. Definiáljon egy [GetLocalizedStringsTransformation](string-transformations.md) jogcím-átalakítást a honosított karakterlánc-értékek helyettesítéséhez az 1. lépésben szereplő jogcímekbe.
1. Módosítsa az `GenerateSendGridRequestBody` jogcím-átalakítást a bemeneti jogcímek használatára a következő XML-kódrészlettel.
1. Frissítse a SendGrind-sablont a dinamikus paraméterek használatára a Azure AD B2C által honosított karakterláncok helyett.

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

A GitHubon megtalálhatja például az egyéni e-mail-ellenőrzési szabályzatot:

[Egyéni e-mail-ellenőrzés – DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)

Az egyéni REST API vagy bármely HTTP-alapú SMTP e-mail-szolgáltató használatáról további információt a [Rest műszaki profil definiálása egy Azure ad B2C egyéni szabályzatban](restful-technical-profile.md)című témakörben talál.
