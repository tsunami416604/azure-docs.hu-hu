---
title: Sablonok kiadása az Azure API Managementban | Microsoft Docs
description: Megtudhatja, hogyan szabhatja testre a probléma oldalain lévő tartalmat a fejlesztői portálon az Azure API Managementban.
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
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176643"
---
# <a name="issue-templates-in-azure-api-management"></a>Sablonok kiadása az Azure API Management
Az Azure API Management lehetővé teszi a fejlesztői portál oldalai tartalmának testreszabását a tartalmukat konfiguráló sablonok használatával. A [DotLiquid](http://dotliquidmarkup.org/) szintaxis és az Ön által választott szerkesztő használatával, például a [tervezők DotLiquid](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), valamint a honosított [karakterlánc-erőforrások](api-management-template-resources.md#strings), a karakterjel- [erőforrások](api-management-template-resources.md#glyphs)és a [lapok vezérlőelemek](api-management-page-controls.md)széles választékával nagy rugalmasságot biztosíthat a konfiguráláshoz a lapok tartalmának megjelenítése, ahogy az a sablonok használatával illik.  
  
 Az ebben a szakaszban található sablonok segítségével testre szabhatja a fejlesztői portálon a probléma oldalain található tartalmakat.  
  
-   [Problémák listája](#IssueList)  
  
> [!NOTE]
>  A minta alapértelmezett sablonjai a következő dokumentációban szerepelnek, de a folyamatos fejlődés miatt változhatnak. Az élő alapértelmezett sablonokat a fejlesztői portálon tekintheti meg, ha a kívánt egyéni sablonokat navigálja. További információ a sablonok használatáról: [API Management fejlesztői portál testreszabása sablonok használatával](api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="IssueList"></a>Problémák listája  
 A **probléma lista** sablon lehetővé teszi a fejlesztői portálon a probléma-lista oldal törzsének testreszabását.  
  
 ![A fejlesztői portálon a probléma listázása](./media/api-management-issue-templates/APIM-Issue-List-Developer-Portal.png "APIM-probléma listázása fejlesztői portál")  
  
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
 A `Issue list` sablon a következő [lap vezérlőit](api-management-page-controls.md)használhatja.  
  
-   [Lapozás – vezérlés](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Adatmodell  
  
|Tulajdonság|Type (Típus)|Leírás|  
|--------------|----------|-----------------|  
|`Issues`|[Kiállító](api-management-template-data-model-reference.md#Issue) entitások gyűjteménye.|Az aktuális felhasználó számára látható problémák.|  
|`Paging`|[Lapozófájl](api-management-template-data-model-reference.md#Paging) entitása.|Az alkalmazások gyűjteményének lapozási adatai.|  
|`IsAuthenticated`|logikai|Azt jelzi, hogy az aktuális felhasználó bejelentkezett-e a fejlesztői portálra.|  
|`CanReportIssues`|logikai|Azt határozza meg, hogy az aktuális felhasználónak van-e engedélye a probléma fájljának megadására.|  
|`Search`|sztring|Ez a tulajdonság elavult, és nem használható.|  
  
### <a name="sample-template-data"></a>Minta sablonjának adatterülete  
  
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

## <a name="next-steps"></a>Következő lépések
További információ a sablonok használatáról: [API Management fejlesztői portál testreszabása sablonok használatával](api-management-developer-portal-templates.md).
