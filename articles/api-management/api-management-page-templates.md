---
title: Az Azure API Management sablonok lapon |} Microsoft Docs
description: "Ismerje meg, hogyan szabhatja testre a sablonok használatával az Azure API Management portál lapjai fejlesztői tartalmát."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: e57df269-1019-4b74-b74d-53155b809d59
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: aca44e14ab85fcfeb9d1eb3c3eadfff7831c372f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="page-templates-in-azure-api-management"></a>Az Azure API Management sablonokat
Az Azure API Management lehetővé teszi a tartalom developer portálon lapok használatával konfigurálhatja a tartalom-sablonok testreszabása. Használatával [DotLiquid](http://dotliquidmarkup.org/) szintaxisát és az Ön által választott szerkesztőben, mint [tervezőknek DotLiquid](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), és a megadott készlete honosított [karakterlánc-erőforrások](api-management-template-resources.md#strings), [betűkép-erőforrások](api-management-template-resources.md#glyphs), és [vezérlők lapon](api-management-page-controls.md), konfigurálja a tartalmat, a lapok, ahogyan szeretné ezeket a sablonokat használ nagy rugalmasságot biztosítanak.  
  
 Ebben a szakaszban a sablonok lehetővé teszik testre szabhatja a bejelentkezést, bejelentkezési tartalma fel, és a lap nem található a lapok a fejlesztői portálján.  
  
-   [bejelentkezés](#SignIn)  
  
-   [feliratkozni](#SignUp)  
  
-   [A lap nem található](#PageNotFound)  
  
> [!NOTE]
>  Minta alapértelmezett sablonok az alábbi dokumentáció szerepelnek, de folyamatos fejlesztéseket miatt változhat. Megtekintheti az élő alapértelmezett sablonok a fejlesztői portálra nyissa meg a kívánt egyéni sablonokat. A sablonok használatának kapcsolatos további információkért lásd: [hogyan szabhatja testre a sablonok segítségével az API Management fejlesztői portálján](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
##  <a name="SignIn"></a>bejelentkezés  
 A **bejelentkezés** sablon lehetővé teszi a bejelentkezési oldal a fejlesztői portálra testreszabását.  
  
 ![Jelentkezzen be az oldal](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "APIM bejelentkezési oldal fejlesztői portál sablonok")  
  
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
 Ez a sablon lehet, hogy használja a következő [vezérlők lapon](api-management-page-controls.md).  
  
-   [Basic-bejelentkezés](api-management-page-controls.md#basic-signin)  
  
-   [szolgáltatók](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>Adatmodell  
 [A felhasználói bejelentkezési](api-management-template-data-model-reference.md#UseSignIn) entitás.  
  
### <a name="sample-template-data"></a>Mintaadatokat sablon  
  
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
    "AuxServiceUrl": "https://manage.windowsazure.com/#Workspaces/ApiManagementExtension/service/contoso5/dashboard",  
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
  
##  <a name="SignUp"></a>feliratkozni  
 A **regisztráljon** sablon lehetővé teszi a bejelentkezési oldal a fejlesztői portálra testreszabását.  
  
 ![Regisztrációs oldalra](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "APIM bejelentkezési oldal Developer-portál sablonok")  
  
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
 Ez a sablon lehet, hogy használja a következő [vezérlők lapon](api-management-page-controls.md).  
  
-   [-előfizetés](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>Adatmodell  
 [Felhasználói bejelentkezési](api-management-template-data-model-reference.md#UserSignUp) entitás.  
  
### <a name="sample-template-data"></a>Mintaadatokat sablon  
  
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
  
##  <a name="PageNotFound"></a>A lap nem található  
 A **oldal nem található** sablon teszi lehetővé testre szabhatja a lapot oldal nem található a fejlesztői portálján.  
  
 ![Nem található az oldal](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "APIM nem található oldal fejlesztői portál sablonok")  
  
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
|referenceCode|Karakterlánc|A kód jön létre, ha ezen a lapon jelent meg, egy belső hiba miatt.|  
|Hibakód|Karakterlánc|A kód jön létre, ha ezen a lapon jelent meg, egy belső hiba miatt.|  
|emailBody|Karakterlánc|Az e-mail törzsének jön létre, ha ezen a lapon jelent meg, egy belső hiba miatt.|  
|requestedUrl|Karakterlánc|Ha a lap nem található a kért URL-CÍMÉT.|  
|referrerUrl|Karakterlánc|A kért URL-címre hivatkozó URL.|  
  
### <a name="sample-template-data"></a>Mintaadatokat sablon  
  
```json  
{  
    "referenceCode": null,  
    "errorCode": null,  
    "emailBody": null,  
    "requestedUrl": "https://contoso5.portal.azure-api.net:443/NotFoundPage?startEditTemplate=NotFoundPage",  
    "referrerUrl": "https://contoso5.portal.azure-api.net/signup?startEditTemplate=SignUpTemplate"  
}  
```

## <a name="next-steps"></a>Következő lépések
A sablonok használatának kapcsolatos további információkért lásd: [hogyan szabhatja testre a sablonok segítségével az API Management fejlesztői portálján](api-management-developer-portal-templates.md).