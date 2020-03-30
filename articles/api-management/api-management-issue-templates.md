---
title: Problémasablonok az Azure API Management ben | Microsoft dokumentumok
description: Ismerje meg, hogyan szabhatja testre a probléma lapok tartalmát az Azure API Management fejlesztői portálján.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 47da4bb2-426e-4e53-8fa7-214ee2e3ab37
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 1dac90053797caf66af79e458b9dbb95b682cd17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249580"
---
# <a name="issue-templates-in-azure-api-management"></a>Problémasablonok az Azure API Management ben
Az Azure API Management lehetővé teszi a fejlesztői portállapok tartalmának testreszabását a tartalmukat konfiguráló sablonok használatával. A [DotLiquid](http://dotliquidmarkup.org/) szintaxis és az Ön által választott szerkesztő , például a [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), valamint a megadott honosított [karakterlánc-erőforrások,](api-management-template-resources.md#strings) [a karakterjel-erőforrások](api-management-template-resources.md#glyphs)és az [oldalvezérlők](api-management-page-controls.md)használatával nagy rugalmasságot biztosít az oldalak tartalmának beállításához, ahogy azt megfelelőnek látja ezekkel a sablonokkal.  
  
 Az ebben a szakaszban található sablonok lehetővé teszik a fejlesztői portál problémalapjainak tartalmának testreszabását.  
  
-   [Problémalista](#IssueList)  
  
> [!NOTE]
>  A minta alapértelmezett sablonjai a következő dokumentációban találhatók, de a folyamatos fejlesztések miatt változhatnak. Az élő alapértelmezett sablonokat a fejlesztői portálon megtekintheti a kívánt egyedi sablonokra való navigálással. A sablonok használatáról további információt [az API Management fejlesztői portál testreszabása sablonok használatával című](api-management-developer-portal-templates.md)témakörben talál.  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="issue-list"></a><a name="IssueList"></a>Problémalista  
 A **Problémalista** sablon lehetővé teszi a problémalista lap törzsének testreszabását a fejlesztői portálon.  
  
 ![Problémalista fejlesztői portálja](./media/api-management-issue-templates/APIM-Issue-List-Developer-Portal.png "APIM-problémalista fejlesztői portálja")  
  
### <a name="default-template"></a>Alapértelmezett sablon  
  
```xml
<div class="row">
  <div class="col-md-9">
    <h2>{% localized "IssuesStrings|WebIssuesIndexTitle" %}</h2>
  </div>
</div>
<div class="row">
  <div class="col-md-12">
    {% if issues.size > 0 %}
    <ul class="list-unstyled">
      {% capture reportedBy %}{% localized "IssuesStrings|WebIssuesStatusReportedBy" %}{% endcapture %}
      {% assign replaceString0 = '{0}' %}
      {% assign replaceString1 = '{1}' %}
      {% for issue in issues %}
      <li>
        <h3>
          <a href="/issues/{{issue.id}}">{{issue.title}}</a>
        </h3>
        <p>{{issue.description}}</p>
        <em>
          {% capture state %}{{issue.issueState}}{% endcapture %}
          {% capture devName %}{{issue.subscriptionDeveloperName}}{% endcapture %}
          {% capture str1 %}{{ reportedBy | replace : replaceString0, state }}{% endcapture %}
          {{ str1 | replace : replaceString1, devName }}
          <span class="UtcDateElement">{{ issue.reportedOn | date: "r" }}</span>
        </em>
      </li>
      {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    {% if canReportIssue %}
    <a class="btn btn-primary" id="createIssue" href="/Issues/Create">{% localized "IssuesStrings|WebIssuesReportIssueButton" %}</a>
    {% elsif isAuthenticated %}
    <hr />
    <p>{% localized "IssuesStrings|WebIssuesNoActiveSubscriptions" %}</p>
    {% else %}
    <hr />
    <p>
      {% capture signIntext %}{% localized "IssuesStrings|WebIssuesNotSignin" %}{% endcapture %}
      {% capture link %}<a href="/signin">{% localized "IssuesStrings|WebIssuesSignIn" %}</a>{% endcapture %}
      {{ signIntext | replace : replaceString0, link }}
    </p>
    {% endif %}
  </div>
</div>
```
  
### <a name="controls"></a>Vezérlők  
 A `Issue list` sablon a következő [lapvezérlőket használhatja](api-management-page-controls.md).  
  
-   [lapozás-vezérlés](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Adatmodell  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Issues`|A [kibocsátási](api-management-template-data-model-reference.md#Issue) entitások gyűjteménye.|Az aktuális felhasználó számára látható problémák.|  
|`Paging`|[Személyhívó](api-management-template-data-model-reference.md#Paging) entitás.|Az alkalmazásgyűjtemény lapozási adatai.|  
|`IsAuthenticated`|logikai|Azt jelzi, hogy az aktuális felhasználó be van-e jelentkezve a fejlesztői portálra.|  
|`CanReportIssues`|logikai|Azt jelzi, hogy az aktuális felhasználó rendelkezik-e engedéllyel a probléma benyújtásához.|  
|`Search`|sztring|Ez a tulajdonság elavult, és nem használható.|  
  
### <a name="sample-template-data"></a>Mintasablon adatai  
  
```json
{
    "Issues": [
        {
            "Id": "5702b68bb16653124c8f9ba7",
            "ApiId": "570275f1b16653124c8f9ba3",
            "Title": "I couldn't figure out how to connect my application to the API",
            "Description": "I'm having trouble connecting my application to the backend API.",
            "SubscriptionDeveloperName": "Clayton",
            "IssueState": "Proposed",
            "ReportedOn": "2016-04-04T18:46:35.64",
            "Comments": null,
            "Attachments": null,
            "Services": null
        }
    ],
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 1,
        "ShowAll": false,
        "PageCount": 1
    },
    "IsAuthenticated": true,
    "CanReportIssue": true,
    "Search": null
}
```

## <a name="next-steps"></a>További lépések
A sablonok használatáról további információt [az API Management fejlesztői portál testreszabása sablonok használatával című](api-management-developer-portal-templates.md)témakörben talál.
