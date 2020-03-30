---
title: Alkalmazássablonok az Azure API Management ben | Microsoft dokumentumok
description: Ismerje meg, hogyan szabhatja testre az alkalmazáslapok tartalmát az Azure API Management fejlesztői portálján.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: f3122c4d-e10e-4cdf-977b-36e8f4133fc8
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: d635950c8b34986cd5824660166017317948cbd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73176905"
---
# <a name="application-templates-in-azure-api-management"></a>Alkalmazássablonok az Azure API Management ben
Az Azure API Management lehetővé teszi a fejlesztői portállapok tartalmának testreszabását a tartalmukat konfiguráló sablonok használatával. A [DotLiquid](http://dotliquidmarkup.org/) szintaxis és az Ön által választott szerkesztő , például a [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), valamint a megadott honosított [karakterlánc-erőforrások,](api-management-template-resources.md#strings) [a karakterjel-erőforrások](api-management-template-resources.md#glyphs)és az [oldalvezérlők](api-management-page-controls.md)használatával nagy rugalmasságot biztosít az oldalak tartalmának beállításához, ahogy azt megfelelőnek látja ezekkel a sablonokkal.  
  
 Az ebben a szakaszban található sablonok lehetővé teszik az alkalmazáslapok tartalmának testreszabását a fejlesztői portálon.  
  
-   [Alkalmazáslista](#ProductList)  
  
-   [Alkalmazás](#Application)  
  
> [!NOTE]
>  A minta alapértelmezett sablonjai a következő dokumentációban találhatók, de a folyamatos fejlesztések miatt változhatnak. Az élő alapértelmezett sablonokat a fejlesztői portálon megtekintheti a kívánt egyedi sablonokra való navigálással. A sablonok használatáról további információt [az API Management fejlesztői portál testreszabása sablonok használatával című](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)témakörben talál.  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="application-list"></a><a name="ProductList"></a>Alkalmazáslista  
 Az **alkalmazáslista** sablon lehetővé teszi az alkalmazáslista-lap törzsének testreszabását a fejlesztői portálon.  
  
 ![Alkalmazáslista lapfejlesztői portálsablonjai](./media/api-management-application-templates/APIM-Application-List-Page-Developer-Portal-Templates.png "APIM-alkalmazáslista lapfejlesztői portálsablonjai")  
  
### <a name="default-template"></a>Alapértelmezett sablon  
  
```xml  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "AppStrings|WebApplicationsHeader" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if applications.size > 0 %}  
        <ul class="list-unstyled">  
        {% for app in applications %}  
            <li>  
            {% if app.application.icon.url != "" %}  
                <aside>  
                    <a href="/applications/details/{{app.application.id}}"><img src="{{app.application.icon.url}}" alt="App Icon" /></a>  
                </aside>  
            {% endif %}  
                <h3><a href="/applications/details/{{app.application.id}}">{{app.application.title}}</a></h3>  
                {{app.application.description}}  
            </li>     
        {% endfor %}  
        </ul>  
        <paging-control></paging-control>  
    {% else %}  
    {% localized "CommonResources|NoItemsToDisplay" %}  
    {% endif %}  
    </div>  
</div>  
```  
  
### <a name="controls"></a>Vezérlők  
 A `Product list` sablon a következő [lapvezérlőket használhatja](api-management-page-controls.md).  
  
-   [lapozás-vezérlés](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Adatmodell  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Paging`|[Személyhívó](api-management-template-data-model-reference.md#Paging) entitás.|Az alkalmazásgyűjtemény lapozási adatai.|  
|`Applications`|[Alkalmazásentitások](api-management-template-data-model-reference.md#Application) gyűjteménye.|Az aktuális felhasználó számára látható alkalmazások.|  
|`CategoryName`|sztring|Az alkalmazás kategóriája.|  
  
### <a name="sample-template-data"></a>Mintasablon adatai  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 1,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Applications": [  
        {  
            "Application": {  
                "Id": "5702b96fb16653124c8f9ba8",  
                "Title": "Contoso Calculator",  
                "Description": "A simple online calculator.",  
                "Url": null,  
                "Version": null,  
                "Requirements": "Free application with no requirements.",  
                "State": 2,  
                "RegistrationDate": "2016-04-04T18:59:00",  
                "CategoryId": 5,  
                "DeveloperId": "5702b5b0b16653124c8f9ba4",  
                "Attachments": [  
                    {  
                        "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
                        "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
                        "Type": "Icon",  
                        "ContentType": "image/png"  
                    },  
                    {  
                        "UniqueId": "2b4fa5dd-00ff-4a8f-b1b7-51e715849ede",  
                        "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/2b4fa5dd-00ff-4a8f-b1b7-51e715849ede.png",  
                        "Type": "Screenshot",  
                        "ContentType": "image/png"  
                    }  
                ],  
                "Icon": {  
                    "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
                    "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
                    "Type": "Icon",  
                    "ContentType": "image/png"  
                }  
            },  
            "CategoryName": "Finance"  
        }  
    ]  
}  
```  
  
##  <a name="application"></a><a name="Application"></a>Alkalmazás  
 Az **alkalmazássablon** lehetővé teszi az alkalmazáslap törzsének testreszabását a fejlesztői portálon.  
  
 ![Alkalmazáslap-fejlesztői portál sablonjai](./media/api-management-application-templates/APIM-Application-Page-Developer-Portal-Templates.png "APIM-alkalmazáslap-fejlesztői portálsablonjai")  
  
### <a name="default-template"></a>Alapértelmezett sablon  
  
```xml  
<h2>{{title}}</h2>  
{% if icon.url != "" %}  
<aside class="applications_aside">  
  <div class="image-placeholder">  
    <img src="{{icon.url}}" alt="Application Icon" />  
  </div>  
</aside>  
{% endif %}  
  
<article>  
  {% if url != "" %}  
    <a target="_blank" href="{{url}}">{{url}}</a>  
  {% endif %}  
  
  <p>{{description}}</p>  
  
  {% if requirements != null %}  
  <h3>{% localized "AppDetailsStrings|WebApplicationsRequirementsHeader" %}</h3>  
  <p>{{requirements}}</p>  
  {% endif %}  
  
  {% if attachments.size > 0 %}  
  <h3>{% localized "AppDetailsStrings|WebApplicationsScreenshotsHeader" %}</h3>  
    {% for screenshot in attachments %}  
      {% if screenshot.type != "Icon" %}  
      <a href="{{screenshot.url}}" data-lightbox="example-set">  
          <img src="/Developer/Applications/Thumbnail?url={{screenshot.url}}" alt='{% localized "AppDetailsStrings|WebApplicationsScreenshotAlt" %}' />  
      </a>  
      {% endif %}  
    {% endfor %}  
  {% endif %}  
</article>  
  
```  
  
### <a name="controls"></a>Vezérlők  
 A `Application` sablon nem teszi lehetővé [az oldalvezérlők használatát.](api-management-page-controls.md)  
  
### <a name="data-model"></a>Adatmodell  
 [Alkalmazás](api-management-template-data-model-reference.md#Application) entitás.  
  
### <a name="sample-template-data"></a>Mintasablon adatai  
  
```json  
{  
    "Id": "5702b96fb16653124c8f9ba8",  
    "Title": "Contoso Calculator",  
    "Description": "A simple online calculator.",  
    "Url": null,  
    "Version": null,  
    "Requirements": "Free application with no requirements.",  
    "State": 2,  
    "RegistrationDate": "2016-04-04T18:59:00",  
    "CategoryId": 5,  
    "DeveloperId": "5702b5b0b16653124c8f9ba4",  
    "Attachments": [  
        {  
            "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
            "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
            "Type": "Icon",  
            "ContentType": "image/png"  
        },  
        {  
            "UniqueId": "2b4fa5dd-00ff-4a8f-b1b7-51e715849ede",  
            "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/2b4fa5dd-00ff-4a8f-b1b7-51e715849ede.png",  
            "Type": "Screenshot",  
            "ContentType": "image/png"  
        }  
    ],  
    "Icon": {  
        "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
        "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
        "Type": "Icon",  
        "ContentType": "image/png"  
    }  
}  
```

## <a name="next-steps"></a>További lépések
A sablonok használatáról további információt [az API Management fejlesztői portál testreszabása sablonok használatával című](api-management-developer-portal-templates.md)témakörben talál.
