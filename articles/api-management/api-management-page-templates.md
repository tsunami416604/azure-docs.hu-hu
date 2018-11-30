---
title: Azure API Management-sablonok lapon |} A Microsoft Docs
description: Ismerje meg, hogyan szabhatja testre a sablonok használatával az Azure API Management fejlesztői portál oldalainak tartalmát.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: e57df269-1019-4b74-b74d-53155b809d59
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: apimpm
ms.openlocfilehash: 1fbafcdab938a0f8653df48631d7733cc58a3668
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441910"
---
# <a name="page-templates-in-azure-api-management"></a>Az Azure API Management sablonokat
Az Azure API Management lehetővé teszi a fejlesztői portál oldalainak konfigurálása a tartalom-sablonok használatával tartalmának testreszabása. Használatával [DotLiquid](http://dotliquidmarkup.org/) szintaxist és a szerkesztő szerkesztőprogramban, például [DotLiquid tervezők számára](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), és a egy megadott készlete honosított [karakterlánc-erőforrások](api-management-template-resources.md#strings), [szimbóluma erőforrások](api-management-template-resources.md#glyphs), és [vezérlők lapon](api-management-page-controls.md), konfigurálhatja a lapok tartalmát, igény szerint ezek a sablonok használatával nagy rugalmasságot biztosít.  
  
 Ebben a szakaszban a sablonokat testre szabhatja a tartalmát a bejelentkezést, jelentkezzen be és a lap nem található a fejlesztői portálon oldalak.  
  
-   [bejelentkezés](#SignIn)  
  
-   [Regisztráció](#SignUp)  
  
-   [Az oldal nem található](#PageNotFound)  
  
> [!NOTE]
>  Alapértelmezett mintasablonokat a következő dokumentációban szerepelnek, de módosulhatnak, folyamatos fejlesztései miatt. A fejlesztői portálon az élő alapértelmezett sablont a kívánt egyéni sablonokat lépve tekintheti meg. Sablonok használatának ismertetését lásd: [testreszabása sablonok használatával, az API Management fejlesztői portálon](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="SignIn"></a> bejelentkezés  
 A **jelentkezzen be a** a sablon lehetővé teszi, hogy a bejelentkezési oldalon a fejlesztői portál testreszabása.  
  
 ![Bejelentkezési oldala](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "APIM bejelentkezési oldal fejlesztői portál sablonjainak")  
  
### <a name="default-template"></a>Alapértelmezett sablon  
  
```xml  
<h2 class="text-center">{% localized "SigninStrings|WebAuthenticationSigninTitle" %}</h2>  
{% if registrationEnabled == true %}  
<p class="text-center">{% localized "SigninStrings|WebAuthenticationNotAMember" %}</p>  
{% endif %}  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    {% if registrationEnabled == true %}  
        <p>{% localized "SigninStrings|WebAuthenticationSigininWithPassword" %}</p>  
    <basic-SignIn></basic-SignIn>  
    {% endif %}  
  </div>  
  
    {% if registrationEnabled != true and providers.size == 0 %}  
        {% localized "ProviderInfoStrings|TextboxExternalIdentitiesDisabled" %}  
  {% else %}  
        {% if providers.size > 0 %}  
      <div class="col-md-6">  
            <div class="providers-list">  
                <p class="text-left">  
                {% if registrationEnabled == true %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitation" %}  
                {% else %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitationPrimary" %}  
                {% endif %}  
                </p>  
        <providers></providers>  
            </div>  
    </div>  
        {% endif %}  
    {% endif %}  
  
  {% if userRegistrationTermsEnabled == true %}  
    <div class="col-md-6">  
        <div id="terms" class="modal" role="dialog" tabindex="-1">  
            <div class="modal-dialog">  
                <div class="modal-content">  
                    <div class="modal-header">  
                        <h4 class="modal-title">{% localized "SigninResources|DialogHeadingTermsOfUse" %}</h4>  
                    </div>  
                    <div class="modal-body break-all">{{userRegistrationTerms}}</div>  
                    <div class="modal-footer">  
                        <button type="button" class="btn btn-default" data-dismiss="modal">{% localized "CommonStrings|ButtonLabelClose" %}</button>  
                    </div>  
                </div>  
            </div>  
        </div>  
        <p>{% localized "SigninResources|TextblockUserRegistrationTermsProvided" %}</p>  
    </div>  
    {% endif %}  
</div>  
```  
  
### <a name="controls"></a>Vezérlők  
 Ez a sablon előfordulhat, hogy használja a következő [vezérlők lapon](api-management-page-controls.md).  
  
-   [Basic-bejelentkezés](api-management-page-controls.md#basic-signin)  
  
-   [Szolgáltatók](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>Adatmodell  
 [Felhasználói bejelentkezési](api-management-template-data-model-reference.md#UseSignIn) entitás.  
  
### <a name="sample-template-data"></a>Mintaadatok sablon  
  
```json  
{
    "Email": null,
    "Password": null,
    "ReturnUrl": null,
    "RememberMe": false,
    "RegistrationEnabled": true,
    "DelegationEnabled": false,
    "DelegationUrl": null,
    "SsoSignUpUrl": null,
    "AuxServiceUrl": "https://portal.azure.com/#resource/subscriptions/{subscription ID}/resourceGroups/Api-Default-West-US/providers/Microsoft.ApiManagement/service/contoso5",
    "Providers": [  
        {  
            "Properties": {  
                "AuthenticationType": "Aad",  
                "Caption": "Azure Active Directory"  
            },  
            "AuthenticationType": "Aad",  
            "Caption": "Azure Active Directory"  
        }  
        ],
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": false
}
```  
  
##  <a name="SignUp"></a> feliratkozni  
 A **regisztráció** a sablon lehetővé teszi, hogy a bejelentkezési oldal a fejlesztői portál testreszabása.  
  
 ![Regisztráljon a lap](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "lap fejlesztői portál sablonjainak APIM-regisztráció")  
  
### <a name="default-template"></a>Alapértelmezett sablon  
  
```xml  
<h2 class="text-center">{% localized "SignupStrings|PageTitleSignup" %}</h2>  
<p class="text-center">  
  {% localized "SignupStrings|WebAuthenticationAlreadyAMember" %} <a href="/signin">{% localized "SignupStrings|WebAuthenticationSigninNow" %}</a>  
</p>  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    <p>{% localized "SignupStrings|WebAuthenticationCreateNewAccount" %}</p>  
    <sign-up></sign-up>  
  </div>  
</div>  
```  
  
### <a name="controls"></a>Vezérlők  
 Ez a sablon előfordulhat, hogy használja a következő [vezérlők lapon](api-management-page-controls.md).  
  
-   [Regisztráció](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>Adatmodell  
 [Felhasználói regisztráció](api-management-template-data-model-reference.md#UserSignUp) entitás.  
  
### <a name="sample-template-data"></a>Mintaadatok sablon  
  
```json  
{  
    "PasswordConfirm": null,  
    "Password": null,  
    "PasswordVerdictLevel": 0,  
    "UserRegistrationTerms": null,  
    "UserRegistrationTermsOptions": 0,  
    "ConsentAccepted": false,  
    "Email": null,  
    "FirstName": null,  
    "LastName": null,  
    "UserData": null,  
    "NameIdentifier": null,  
    "ProviderName": null  
}  
```  
  
##  <a name="PageNotFound"></a> Az oldal nem található  
 A **oldal nem található** a sablon lehetővé teszi, hogy az oldal nem található az oldal a fejlesztői portál testreszabása.  
  
 ![Nem található az oldal](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "APIM nem található oldal fejlesztői portál sablonjainak")  
  
### <a name="default-template"></a>Alapértelmezett sablon  
  
```xml  
<h2>{% localized "NotFoundStrings|PageTitleNotFound" %}</h2>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialCause" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseOldLink" %}</li>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseMisspelledUrl" %}</li>  
</ul>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialSolution" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialSolutionRetype" %}</li>  
  <li>  
    {% capture textPotentialSolutionStartOver %}{% localized "NotFoundStrings|TextblockPotentialSolutionStartOver" %}{% endcapture %}  
    {% capture homeLink %}<a href="/">{% localized "NotFoundStrings|LinkLabelHomePage" %}</a>{% endcapture %}  
    {% assign replaceString = '{0}' %}  
  
    {{ textPotentialSolutionStartOver | replace : replaceString, homeLink }}  
  </li>  
</ul>  
  
<p>  
  {% capture textReportProblem %}{% localized "NotFoundStrings|TextReportProblem" %}{% endcapture %}  
  {% capture emailLink %}<a href="mailto:apimgmt@microsoft.com" target="_self" title="API Management Support">{% localized "NotFoundStrings|LinkLabelSendUsEmail" %}</a>{% endcapture %}  
  {% assign replaceString = '{0}' %}  
  
  {{ textReportProblem | replace : replaceString, emailLink }}  
</p>  
```  
  
### <a name="controls"></a>Vezérlők  
 Ez a sablon nem használhatja bármelyik [vezérlők lapon](api-management-page-controls.md).  
  
### <a name="data-model"></a>Adatmodell  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|referenceCode|sztring|Ha ezen a lapon azt egy belső hiba eredményeként generált kódot.|  
|Hibakód|sztring|Ha ezen a lapon azt egy belső hiba eredményeként generált kódot.|  
|emailBody|sztring|E-mail törzse, ha ezen a lapon azt egy belső hiba eredményeként jön létre.|  
|requestedUrl|sztring|Ha az oldal nem található a kért URL-címe.|  
|referrerUrl|sztring|A kért URL-címre hivatkozó URL.|  
  
### <a name="sample-template-data"></a>Mintaadatok sablon  
  
```json  
{  
    "referenceCode": null,  
    "errorCode": null,  
    "emailBody": null,  
    "requestedUrl": "https://contoso5.portal.azure-api.net:443/NotFoundPage?startEditTemplate=NotFoundPage",  
    "referrerUrl": "https://contoso5.portal.azure-api.net/signup?startEditTemplate=SignUpTemplate"  
}  
```

## <a name="next-steps"></a>További lépések
Sablonok használatának ismertetését lásd: [testreszabása sablonok használatával, az API Management fejlesztői portálon](api-management-developer-portal-templates.md).
