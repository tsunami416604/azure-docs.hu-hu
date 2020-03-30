---
title: Felhasználói profilsablonok az Azure API Management ben | Microsoft dokumentumok
description: Ismerje meg, hogyan szabhatja testre a felhasználói profil lapjainak tartalmát az Azure API Management fejlesztői portálján.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2e3b73ef-d223-44fe-9280-c3af3fd4a030
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 4e3e430874f9d5b64f717b6ebb1cacb0eae46b1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243951"
---
# <a name="user-profile-templates-in-azure-api-management"></a>Felhasználói profilsablonok az Azure API Management ben
Az Azure API Management lehetővé teszi a fejlesztői portállapok tartalmának testreszabását a tartalmukat konfiguráló sablonok használatával. A [DotLiquid](http://dotliquidmarkup.org/) szintaxis és az Ön által választott szerkesztő , például a [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), valamint a megadott honosított [karakterlánc-erőforrások,](api-management-template-resources.md#strings) [a karakterjel-erőforrások](api-management-template-resources.md#glyphs)és az [oldalvezérlők](api-management-page-controls.md)használatával nagy rugalmasságot biztosít az oldalak tartalmának beállításához, ahogy azt megfelelőnek látja ezekkel a sablonokkal.  
  
 Az ebben a szakaszban található sablonok lehetővé teszik a felhasználói profil lapok tartalmának testreszabását a fejlesztői portálon.  
  
-   [Profil](#Profile)  
  
-   [Előfizetések](#Subscriptions)  
  
-   [Alkalmazások](#Applications)  
  
-   [Fiókadatok frissítése](#UpdateAccountInfo)  
  
> [!NOTE]
>  A minta alapértelmezett sablonjai a következő dokumentációban találhatók, de a folyamatos fejlesztések miatt változhatnak. Az élő alapértelmezett sablonokat a fejlesztői portálon megtekintheti a kívánt egyedi sablonokra való navigálással. A sablonok használatáról további információt [az API Management fejlesztői portál testreszabása sablonok használatával című](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)témakörben talál.  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="profile"></a><a name="Profile"></a>Profil  
 A **profilsablon** lehetővé teszi a felhasználói profil szakaszának testreszabását a fejlesztői portálon.  
  
 ![Felhasználói profil lapja](./media/api-management-user-profile-templates/APIM-User-Profile-Page.png "APIM felhasználói profil lapja")  
  
### <a name="default-template"></a>Alapértelmezett sablon  
  
```xml  
<div class="pull-right">  
  {% if canChangePassword == true %}  
  <a class="btn btn-default" id="ChangePassword" role="button" href="{{changePasswordUrl}}">{% localized "UserProfile|ButtonLabelChangePassword" %}</a>  
  {% endif %}  
  <a id="changeAccountInfo" href="{{changeNameOrEmailUrl}}" class="btn btn-default">  
    <span class="glyphicon glyphicon-user"></span>  
    <span>{% localized "UserProfile|ButtonLabelChangeAccountInfo" %}</span>  
  </a>  
</div>  
<h2>{% localized "SubscriptionStrings|PageTitleDeveloperProfile" %}</h2>  
<div class="container-fluid">  
  <div class="row">  
    <div class="col-sm-3">  
      <label for="Email">{% localized "UserProfile|TextboxLabelEmail" %}</label>  
    </div>  
    <div class="col-sm-9" id="Email">{{email}}</div>  
  </div>  
  
  {% if isSystemUser != true %}  
  <div class="row">  
    <div class="col-sm-3">  
      <label for="FirstName">{% localized "UserProfile|TextboxLabelEmailFirstName" %}</label>  
    </div>  
    <div class="col-sm-9" id="FirstName">{{FirstName}}</div>  
  </div>  
  <div class="row">  
    <div class="col-sm-3">  
      <label for="LastName">{% localized "UserProfile|TextboxLabelEmailLastName" %}</label>  
    </div>  
    <div class="col-sm-9" id="LastName">{{LastName}}</div>  
  </div>  
  {% else %}  
  <div class="row">  
    <div class="col-sm-3">  
      <label for="CompanyName">{% localized "UserProfile|TextboxLabelOrganizationName" %}</label>  
    </div>  
    <div class="col-sm-9" id="CompanyName">{{CompanyName}}</div>  
  </div>  
  <div class="row">  
    <div class="col-sm-3">  
      <label for="AddresserEmail">{% localized "UserProfile|TextboxLabelNotificationsSenderEmail" %}</label>  
    </div>  
    <div class="col-sm-9" id="AddresserEmail">{{AddresserEmail}}</div>  
  </div>  
  {% endif %}  
  
</div>  
```  
  
### <a name="controls"></a>Vezérlők  
 Előfordulhat, hogy ez a sablon nem használ [oldalvezérlőket.](api-management-page-controls.md)  
  
### <a name="data-model"></a>Adatmodell  
  
> [!NOTE]
>  A [Profil](#Profile), [Alkalmazások](#Applications)és [Előfizetések](#Subscriptions) sablonok ugyanazt az adatmodellt osztják meg, és ugyanazokat a sablonadatokat kapják.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`firstName`|sztring|Az aktuális felhasználó utóneve.|  
|`lastName`|sztring|Az aktuális felhasználó vezetékneve.|  
|`companyName`|sztring|Az aktuális felhasználó vállalatneve.|  
|`addresserEmail`|sztring|Az aktuális felhasználó e-mail-címe.|  
|`developersUsageStatisticsLink`|sztring|Relatív URL-cím az aktuális felhasználó elemzésének megtekintéséhez.|  
|`subscriptions`|[Előfizetésentitások](api-management-template-data-model-reference.md#Subscription) gyűjteménye.|Az aktuális felhasználó előfizetései.|  
|`applications`|[Alkalmazásentitások](api-management-template-data-model-reference.md#Application) gyűjteménye.|Az aktuális felhasználó alkalmazásai.|  
|`changePasswordUrl`|sztring|Az aktuális felhasználó jelszavának módosításához tartozó relatív URL-cím.|  
|`changeNameOrEmailUrl`|sztring|Az aktuális felhasználó nevének és e-mailjének módosításához tartozó relatív URL-cím.|  
|`canChangePassword`|logikai|Azt jelzi, hogy az aktuális felhasználó módosíthatja-e a jelszavát.|  
|`isSystemUser`|logikai|Azt jelzi, hogy az aktuális felhasználó tagja-e a beépített [csoportok egyikének.](api-management-key-concepts.md#groups)|  
  
### <a name="sample-template-data"></a>Mintasablon adatai  
  
```json  
{  
    "firstName": "Administrator",  
    "lastName": "",  
    "companyName": "Contoso",  
    "addresserEmail": "apimgmt-noreply@mail.windowsazure.com",  
    "email": "admin@live.com",  
    "developersUsageStatisticsLink": "/Developer/Analytics",  
    "subscriptions": [  
        {  
            "Id": "57026e30de15d80041070001",  
            "ProductId": "57026e30de15d80041060001",  
            "ProductTitle": "Starter",  
            "ProductDescription": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "ProductDetailsUrl": "/Products/57026e30de15d80041060001",  
            "State": "Active",  
            "DisplayName": "Starter  (default)",  
            "CreatedDate": "2016-04-04T13:37:52.847",  
            "CanBeCancelled": true,  
            "IsAwaitingApproval": false,  
            "StartDate": null,  
            "ExpirationDate": null,  
            "NotificationDate": null,  
            "PrimaryKey": "b6b2870953d04420a4e02c58f2c08e74",  
            "SecondaryKey": "cfe28d5a1cd04d8abc93f48352076ea5",  
            "UserId": 1,  
            "CanBeRenewed": false,  
            "HasExpired": false,  
            "IsRejected": false,  
            "CancelUrl": "/Subscriptions/57026e30de15d80041070001/Cancel",  
            "RenewUrl": "/Subscriptions/57026e30de15d80041070001/Renew"  
        },  
        {  
            "Id": "57026e30de15d80041070002",  
            "ProductId": "57026e30de15d80041060002",  
            "ProductTitle": "Unlimited",  
            "ProductDescription": "Subscribers have completely unlimited access to the API. Administrator approval is required.",  
            "ProductDetailsUrl": "/Products/57026e30de15d80041060002",  
            "State": "Active",  
            "DisplayName": "Unlimited  (default)",  
            "CreatedDate": "2016-04-04T13:37:52.923",  
            "CanBeCancelled": true,  
            "IsAwaitingApproval": false,  
            "StartDate": null,  
            "ExpirationDate": null,  
            "NotificationDate": null,  
            "PrimaryKey": "8fe7843c36de4cceb4728e6cae297336",  
            "SecondaryKey": "96c850d217e74acf9b514ff8a5b38551",  
            "UserId": 1,  
            "CanBeRenewed": false,  
            "HasExpired": false,  
            "IsRejected": false,  
            "CancelUrl": "/Subscriptions/57026e30de15d80041070002/Cancel",  
            "RenewUrl": "/Subscriptions/57026e30de15d80041070002/Renew"  
        }  
    ],  
    "applications": [],  
    "changePasswordUrl": "/account/password/change",  
    "changeNameOrEmailUrl": "/account/update",  
    "canChangePassword": false,  
    "isSystemUser": true  
}  
```  
  
##  <a name="subscriptions"></a><a name="Subscriptions"></a>Előfizetések  
 Az **Előfizetések** sablon lehetővé teszi a felhasználói profil lap előfizetések szakaszának testreszabását a fejlesztői portálon.  
  
 ![Felhasználói előfizetés i.](./media/api-management-user-profile-templates/APIM-User-Subscription-Page.png "APIM-felhasználó előfizetési lapja")  
  
### <a name="default-template"></a>Alapértelmezett sablon  
  
```xml  
<div class="ap-account-subscriptions">  
  <a href="{{developersUsageStatisticsLink}}" id="UsageStatistics" class="btn btn-default pull-right">  
    <span class="glyphicon glyphicon-stats"></span>  
    <span>{% localized "SubscriptionListStrings|WebDevelopersUsageStatisticsLink" %}</span>  
  </a>  
  
  <h2>{% localized "SubscriptionListStrings|WebDevelopersYourSubscriptions" %}</h2>  
  
  <table class="table">  
    <thead>  
      <tr>  
        <th>Subscription details</th>  
        <th>Product</th>  
        <th>{% localized "SubscriptionListStrings|WebDevelopersSubscriptionTableStateHeader" %}</th>  
        <th>Action</th>  
      </tr>  
    </thead>  
    <tbody>  
      {% if subscriptions.size == 0 %}  
      <tr>  
        <td class="text-center" colspan="4">  
          {% localized "CommonResources|NoItemsToDisplay" %}  
        </td>  
      </tr>  
      {% else %}  
      {% for subscription in subscriptions %}  
      <tr id="{{subscription.id}}" {% if subscription.hasExpired %} class="expired" {% endif %}>  
        <td>  
          <div class="row">  
            <label class="col-lg-3">{% localized "SubscriptionListStrings|SubscriptionPropertyLabelName" %}</label>  
            <div class="col-lg-6">  
              {{ subscription.displayName }}  
            </div>  
            <div class="col-lg-2">  
              <a class="btn-link" href="/Subscriptions/{{subscription.id}}/Rename">Rename</a>  
            </div>  
            <div class="clearfix"></div>  
          </div>  
          {% if subscription.isAwaitingApproval %}  
          <div class="row">  
            <label class="col-lg-3">{% localized "SubscriptionListStrings|SubscriptionPropertyLabelRequestedDate" %}</label>  
            <div class="col-lg-6">  
              {{ subscription.createdDate | date:"MM/dd/yyyy" }}  
            </div>  
          </div>  
          {% else %}  
          {% if subscription.isRejected == false %}  
          {% if subscription.startDate %}  
          <div class="row">  
            <label class="col-lg-3">{% localized "SubscriptionListStrings|SubscriptionPropertyLabelStartedDate" %}</label>  
            <div class="col-lg-6">  
              {{ subscription.startDate | date:"MM/dd/yyyy" }}  
            </div>  
          </div>  
          {% endif %}  
  
          <!-- ko with: Developers.Account.Root.account.key('{{subscription.primaryKey}}', '{{subscription.id}}', true) -->  
          <div class="row">  
            <label class="col-lg-3">{% localized "SubscriptionListStrings|WebDevelopersPrimaryKey" %}</label>  
            <div class="col-lg-6">  
              <code data-bind="text: $data.displayKey()" id="primary_{{subscription.id}}"></code>  
            </div>  
            <div class="col-lg-2">  
              <!-- ko if: !requestInProgress() -->  
              <div class="nowrap">  
                <a href="#" class="btn-link" id="togglePrimary_{{subscription.id}}" data-bind="click: toggleKeyDisplay, text: toggleKeyLabel"></a>  
                |  
                <a href="#" class="btn-link" id="regeneratePrimary_{{subscription.id}}" data-bind="click: regenerateKey, text: regenerateKeyLabel"></a>  
              </div>  
              <!-- /ko -->  
              <!-- ko if: requestInProgress() -->  
              <div class="progress progress-striped active">  
                <div class="progress-bar" role="progressbar" aria-valuenow="100" aria-valuemin="0" aria-valuemax="100" style="width: 100%">  
                  <span class="sr-only"></span>  
                </div>  
              </div>  
              <!-- /ko -->  
            </div>  
            <div class="clearfix"></div>  
          </div>  
          <!-- /ko -->  
          <!-- ko with: Developers.Account.Root.account.key('{{subscription.secondaryKey}}', '{{subscription.id}}', false) -->  
          <div class="row">  
            <label class="col-lg-3">{% localized "SubscriptionListStrings|WebDevelopersSecondaryKey" %}</label>  
            <div class="col-lg-6">  
              <code data-bind="text: $data.displayKey()" id="secondary_{{subscription.id}}"></code>  
            </div>  
            <div class="col-lg-2">  
              <div class="nowrap">  
                <a href="#" class="btn-link" id="toggleSecondary_{{subscription.id}}" data-bind="click: toggleKeyDisplay, text: toggleKeyLabel">{% localized "SubscriptionListStrings|ButtonLabelShowKey" %}</a>  
                |  
                <a href="#" class="btn-link" id="regenerateSecondary_{{subscription.id}}" data-bind="click: regenerateKey, text: regenerateKeyLabel">{% localized "SubscriptionListStrings|WebDevelopersRegenerateLink" %}</a>  
              </div>  
            </div>  
            <div class="clearfix"> </div>  
          </div>  
          <!-- /ko -->  
          {% endif %}  
          {% endif %}  
        </td>  
        <td>  
          <a href="{{subscription.productDetailsUrl}}">{{subscription.productTitle}}</a>  
        </td>  
        <td>  
          <strong>  
            {{subscription.state}}  
          </strong>  
        </td>  
        <td>  
          <div class="nowrap">  
            {% if subscription.canBeCancelled %}  
            <subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }"></subscription-cancel>  
            {% endif %}  
          </div>  
        </td>  
      </tr>  
      {% endfor %}  
      {% endif %}  
    </tbody>  
  </table>  
</div>  
```  
  
### <a name="controls"></a>Vezérlők  
 Ez a sablon a következő [lapvezérlőket használhatja.](api-management-page-controls.md)  
  
-   [előfizetés-lemondás](api-management-page-controls.md#subscription-cancel)  
  
### <a name="data-model"></a>Adatmodell  
  
> [!NOTE]
>  A [Profil](#Profile), [Alkalmazások](#Applications)és [Előfizetések](#Subscriptions) sablonok ugyanazt az adatmodellt osztják meg, és ugyanazokat a sablonadatokat kapják.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`firstName`|sztring|Az aktuális felhasználó utóneve.|  
|`lastName`|sztring|Az aktuális felhasználó vezetékneve.|  
|`companyName`|sztring|Az aktuális felhasználó vállalatneve.|  
|`addresserEmail`|sztring|Az aktuális felhasználó e-mail-címe.|  
|`developersUsageStatisticsLink`|sztring|Relatív URL-cím az aktuális felhasználó elemzésének megtekintéséhez.|  
|`subscriptions`|[Előfizetésentitások](api-management-template-data-model-reference.md#Subscription) gyűjteménye.|Az aktuális felhasználó előfizetései.|  
|`applications`|[Alkalmazásentitások](api-management-template-data-model-reference.md#Application) gyűjteménye.|Az aktuális felhasználó alkalmazásai.|  
|`changePasswordUrl`|sztring|Az aktuális felhasználó jelszavának módosításához tartozó relatív URL-cím.|  
|`changeNameOrEmailUrl`|sztring|Az aktuális felhasználó nevének és e-mailjének módosításához tartozó relatív URL-cím.|  
|`canChangePassword`|logikai|Azt jelzi, hogy az aktuális felhasználó módosíthatja-e a jelszavát.|  
|`isSystemUser`|logikai|Azt jelzi, hogy az aktuális felhasználó tagja-e a beépített [csoportok egyikének.](api-management-key-concepts.md#groups)|  
  
### <a name="sample-template-data"></a>Mintasablon adatai  
  
```json  
{  
    "firstName": "Administrator",  
    "lastName": "",  
    "companyName": "Contoso",  
    "addresserEmail": "apimgmt-noreply@mail.windowsazure.com",  
    "email": "admin@live.com",  
    "developersUsageStatisticsLink": "/Developer/Analytics",  
    "subscriptions": [  
        {  
            "Id": "57026e30de15d80041070001",  
            "ProductId": "57026e30de15d80041060001",  
            "ProductTitle": "Starter",  
            "ProductDescription": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "ProductDetailsUrl": "/Products/57026e30de15d80041060001",  
            "State": "Active",  
            "DisplayName": "Starter  (default)",  
            "CreatedDate": "2016-04-04T13:37:52.847",  
            "CanBeCancelled": true,  
            "IsAwaitingApproval": false,  
            "StartDate": null,  
            "ExpirationDate": null,  
            "NotificationDate": null,  
            "PrimaryKey": "b6b2870953d04420a4e02c58f2c08e74",  
            "SecondaryKey": "cfe28d5a1cd04d8abc93f48352076ea5",  
            "UserId": 1,  
            "CanBeRenewed": false,  
            "HasExpired": false,  
            "IsRejected": false,  
            "CancelUrl": "/Subscriptions/57026e30de15d80041070001/Cancel",  
            "RenewUrl": "/Subscriptions/57026e30de15d80041070001/Renew"  
        },  
        {  
            "Id": "57026e30de15d80041070002",  
            "ProductId": "57026e30de15d80041060002",  
            "ProductTitle": "Unlimited",  
            "ProductDescription": "Subscribers have completely unlimited access to the API. Administrator approval is required.",  
            "ProductDetailsUrl": "/Products/57026e30de15d80041060002",  
            "State": "Active",  
            "DisplayName": "Unlimited  (default)",  
            "CreatedDate": "2016-04-04T13:37:52.923",  
            "CanBeCancelled": true,  
            "IsAwaitingApproval": false,  
            "StartDate": null,  
            "ExpirationDate": null,  
            "NotificationDate": null,  
            "PrimaryKey": "8fe7843c36de4cceb4728e6cae297336",  
            "SecondaryKey": "96c850d217e74acf9b514ff8a5b38551",  
            "UserId": 1,  
            "CanBeRenewed": false,  
            "HasExpired": false,  
            "IsRejected": false,  
            "CancelUrl": "/Subscriptions/57026e30de15d80041070002/Cancel",  
            "RenewUrl": "/Subscriptions/57026e30de15d80041070002/Renew"  
        }  
    ],  
    "applications": [],  
    "changePasswordUrl": "/account/password/change",  
    "changeNameOrEmailUrl": "/account/update",  
    "canChangePassword": false,  
    "isSystemUser": true  
}  
```  
  
##  <a name="applications"></a><a name="Applications"></a>Alkalmazások  
 Az **Alkalmazások** sablon lehetővé teszi a felhasználói profil lap előfizetések szakaszának testreszabását a fejlesztői portálon.  
  
 ![Felhasználói fiókok alkalmazásai lap](./media/api-management-user-profile-templates/APIM-User-Account-Applications-Page.png "APIM felhasználói fiók alkalmazások lap")  
  
### <a name="default-template"></a>Alapértelmezett sablon  
  
```xml  
<div class="ap-account-applications">  
  <a id="RegisterApplication" href="/Developer/Applications/Register" class="btn btn-success pull-right">  
    <span class="glyphicon glyphicon-plus"></span>  
    <span>{% localized "ApplicationListStrings|WebDevelopersRegisterAppLink" %}</span>  
  </a>  
  <h2>{% localized "ApplicationListStrings|WebDevelopersYourApplicationsHeader" %}</h2>  
  
  <table class="table">  
    <thead>  
      <tr>  
        <th class="col-md-8">{% localized "ApplicationListStrings|WebDevelopersAppTableNameHeader" %}</th>  
        <th class="col-md-2">{% localized "ApplicationListStrings|WebDevelopersAppTableCategoryHeader" %}</th>  
        <th class="col-md-2" colspan="2">{% localized "ApplicationListStrings|WebDevelopersAppTableStateHeader" %}</th>  
      </tr>  
    </thead>  
    <tbody>  
  
      {% if applications.size == 0 %}  
  
      <tr>  
        <td class="col-md-12 text-center" colspan="4">  
          {% localized "CommonResources|NoItemsToDisplay" %}  
        </td>  
      </tr>  
  
      {% else %}  
  
      {% for app in applications %}  
      <tr>  
        <td class="col-md-8">  
          {{app.title}}  
        </td>  
        <td class="col-md-2">  
          {{app.categoryName}}  
        </td>  
        <td class="col-md-2">  
          <strong>  
            {% case app.state %}  
            {% when ApplicationStateModel.Registered %}  
            {% localized "ApplicationListStrings|WebDevelopersAppNotSubmitted" %}  
  
            {% when ApplicationStateModel.Unpublished %}  
            {% localized "ApplicationListStrings|WebDevelopersAppNotPublished" %}  
  
            {% else %}  
            {{ app.state }}  
            {% endcase %}  
          </strong>  
        </td>  
        <td class="col-md-1">  
          <div class="nowrap">  
            {% if app.state != ApplicationStateModel.Submitted and app.state != ApplicationStateModel.Published %}  
            <app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
            {% endif %}  
          </div>  
        </td>  
      </tr>  
      {% endfor %}  
  
      {% endif %}  
    </tbody>  
  </table>  
</div>  
```  
  
### <a name="controls"></a>Vezérlők  
 Ez a sablon a következő [lapvezérlőket használhatja.](api-management-page-controls.md)  
  
-   [alkalmazás-műveletek](api-management-page-controls.md#app-actions)  
  
### <a name="data-model"></a>Adatmodell  
  
> [!NOTE]
>  A [Profil](#Profile), [Alkalmazások](#Applications)és [Előfizetések](#Subscriptions) sablonok ugyanazt az adatmodellt osztják meg, és ugyanazokat a sablonadatokat kapják.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`firstName`|sztring|Az aktuális felhasználó utóneve.|  
|`lastName`|sztring|Az aktuális felhasználó vezetékneve.|  
|`companyName`|sztring|Az aktuális felhasználó vállalatneve.|  
|`addresserEmail`|sztring|Az aktuális felhasználó e-mail-címe.|  
|`developersUsageStatisticsLink`|sztring|Relatív URL-cím az aktuális felhasználó elemzésének megtekintéséhez.|  
|`subscriptions`|[Előfizetésentitások](api-management-template-data-model-reference.md#Subscription) gyűjteménye.|Az aktuális felhasználó előfizetései.|  
|`applications`|[Alkalmazásentitások](api-management-template-data-model-reference.md#Application) gyűjteménye.|Az aktuális felhasználó alkalmazásai.|  
|`changePasswordUrl`|sztring|Az aktuális felhasználó jelszavának módosításához tartozó relatív URL-cím.|  
|`changeNameOrEmailUrl`|sztring|Az aktuális felhasználó nevének és e-mailjének módosításához tartozó relatív URL-cím.|  
|`canChangePassword`|logikai|Azt jelzi, hogy az aktuális felhasználó módosíthatja-e a jelszavát.|  
|`isSystemUser`|logikai|Azt jelzi, hogy az aktuális felhasználó tagja-e a beépített [csoportok egyikének.](api-management-key-concepts.md#groups)|  
  
### <a name="sample-template-data"></a>Mintasablon adatai  
  
```json  
{  
    "firstName": "Administrator",  
    "lastName": "",  
    "companyName": "Contoso",  
    "addresserEmail": "apimgmt-noreply@mail.windowsazure.com",  
    "email": "admin@live.com",  
    "developersUsageStatisticsLink": "/Developer/Analytics",  
    "subscriptions": [  
        {  
            "Id": "57026e30de15d80041070001",  
            "ProductId": "57026e30de15d80041060001",  
            "ProductTitle": "Starter",  
            "ProductDescription": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "ProductDetailsUrl": "/Products/57026e30de15d80041060001",  
            "State": "Active",  
            "DisplayName": "Starter  (default)",  
            "CreatedDate": "2016-04-04T13:37:52.847",  
            "CanBeCancelled": true,  
            "IsAwaitingApproval": false,  
            "StartDate": null,  
            "ExpirationDate": null,  
            "NotificationDate": null,  
            "PrimaryKey": "b6b2870953d04420a4e02c58f2c08e74",  
            "SecondaryKey": "cfe28d5a1cd04d8abc93f48352076ea5",  
            "UserId": 1,  
            "CanBeRenewed": false,  
            "HasExpired": false,  
            "IsRejected": false,  
            "CancelUrl": "/Subscriptions/57026e30de15d80041070001/Cancel",  
            "RenewUrl": "/Subscriptions/57026e30de15d80041070001/Renew"  
        },  
        {  
            "Id": "57026e30de15d80041070002",  
            "ProductId": "57026e30de15d80041060002",  
            "ProductTitle": "Unlimited",  
            "ProductDescription": "Subscribers have completely unlimited access to the API. Administrator approval is required.",  
            "ProductDetailsUrl": "/Products/57026e30de15d80041060002",  
            "State": "Active",  
            "DisplayName": "Unlimited  (default)",  
            "CreatedDate": "2016-04-04T13:37:52.923",  
            "CanBeCancelled": true,  
            "IsAwaitingApproval": false,  
            "StartDate": null,  
            "ExpirationDate": null,  
            "NotificationDate": null,  
            "PrimaryKey": "8fe7843c36de4cceb4728e6cae297336",  
            "SecondaryKey": "96c850d217e74acf9b514ff8a5b38551",  
            "UserId": 1,  
            "CanBeRenewed": false,  
            "HasExpired": false,  
            "IsRejected": false,  
            "CancelUrl": "/Subscriptions/57026e30de15d80041070002/Cancel",  
            "RenewUrl": "/Subscriptions/57026e30de15d80041070002/Renew"  
        }  
    ],  
    "applications": [],  
    "changePasswordUrl": "/account/password/change",  
    "changeNameOrEmailUrl": "/account/update",  
    "canChangePassword": false,  
    "isSystemUser": true  
}  
```  
  
##  <a name="update-account-info"></a><a name="UpdateAccountInfo"></a>Fiókadatok frissítése  
 A **Fiókadatok frissítése** sablon lehetővé teszi a **Fiókadatok frissítése** lap testreszabását a fejlesztői portálon.  
  
 ![Felhasználói fiók adatai lapfejlesztői portálsablonjai](./media/api-management-user-profile-templates/APIM-User-Account-Info-Page-Developer-Portal-Templates.png "APIM-felhasználói fiók adatai lapfejlesztői portálsablonjai")  
  
### <a name="default-template"></a>Alapértelmezett sablon  
  
```xml  
<div class="row">  
  <div class="col-sm-6 col-md-6">  
    <div class="form-group">  
      <label for="Email">{% localized "SigninResources|TextboxLabelEmail" %}</label>  
      <input autofocus="autofocus" class="form-control" id="Email" name="Email" type="text" value="{{email}}">  
    </div>  
    <div class="form-group">  
      <label for="FirstName">{% localized "SigninResources|TextboxLabelEmailFirstName" %}</label>  
      <input class="form-control" id="FirstName" name="FirstName" type="text" value="{{firstName}}">  
    </div>  
    <div class="form-group">  
      <label for="LastName">{% localized "SigninResources|TextboxLabelEmailLastName" %}</label>  
      <input class="form-control" id="LastName" name="LastName" type="text" value="{{lastName}}">  
    </div>  
    <div class="form-group">  
      <label for="Password">{% localized "SigninResources|WebAuthenticationSigninPasswordLabel" %}</label>  
      <input class="form-control" id="Password" name="Password" type="password">  
    </div>  
  </div>  
</div>  
  
<button type="submit" class="btn btn-primary" id="UpdateProfile">  
  {% localized "UpdateProfileStrings|ButtonLabelUpdateProfile" %}  
</button>  
<a class="btn btn-default" href="/developer" role="button">  
  {% localized "CommonStrings|ButtonLabelCancel" %}  
</a>  
```  
  
### <a name="controls"></a>Vezérlők  
 Előfordulhat, hogy ez a sablon nem használ [oldalvezérlőket.](api-management-page-controls.md)  
  
### <a name="data-model"></a>Adatmodell  
 [Felhasználói fiók információs](api-management-template-data-model-reference.md#UserAccountInfo) entitása.  
  
### <a name="sample-template-data"></a>Mintasablon adatai  
  
```json  
{  
    "FirstName": "Administrator",  
    "LastName": "",  
    "Email": "admin@live.com",  
    "Password": null,  
    "NameIdentifier": null,  
    "ProviderName": null,  
    "IsBasicAccount": false  
}  
```

## <a name="next-steps"></a>További lépések
A sablonok használatáról további információt [az API Management fejlesztői portál testreszabása sablonok használatával című](api-management-developer-portal-templates.md)témakörben talál.
