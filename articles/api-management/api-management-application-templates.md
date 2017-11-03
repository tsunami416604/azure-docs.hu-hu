---
title: "Az Azure API Management alkalmazássablonok |} Microsoft Docs"
description: "Ismerje meg, hogyan szabhatja testre a fejlesztői portálra az Azure API Management az alkalmazás lapok tartalmát."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: f3122c4d-e10e-4cdf-977b-36e8f4133fc8
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 40a4d159afff4db78e27f89b448e21b1d94cd378
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="application-templates-in-azure-api-management"></a>Alkalmazássablonok az Azure API Management
Az Azure API Management lehetővé teszi a tartalom developer portálon lapok használatával konfigurálhatja a tartalom-sablonok testreszabása. Használatával [DotLiquid](http://dotliquidmarkup.org/) szintaxisát és az Ön által választott szerkesztőben, mint [tervezőknek DotLiquid](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), és a megadott készlete honosított [karakterlánc-erőforrások](api-management-template-resources.md#strings), [betűkép-erőforrások](api-management-template-resources.md#glyphs), és [vezérlők lapon](api-management-page-controls.md), konfigurálja a tartalmat, a lapok, ahogyan szeretné ezeket a sablonokat használ nagy rugalmasságot biztosítanak.  
  
 Ebben a szakaszban a sablonok engedélyezi, hogy testre szabhatja a fejlesztői portálra az alkalmazás lapok tartalmát.  
  
-   [– Alkalmazáslista](#ProductList)  
  
-   [Alkalmazás](#Application)  
  
> [!NOTE]
>  Minta alapértelmezett sablonok az alábbi dokumentáció szerepelnek, de folyamatos fejlesztéseket miatt változhat. Megtekintheti az élő alapértelmezett sablonok a fejlesztői portálra nyissa meg a kívánt egyéni sablonokat. A sablonok használatának kapcsolatos további információkért lásd: [hogyan szabhatja testre a sablonok segítségével az API Management fejlesztői portálján](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
##  <a name="ProductList"></a>– Alkalmazáslista  
 A **Alkalmazáslista** sablon lehetővé teszi a fejlesztői portálra az alkalmazás lista lap törzsét testreszabását.  
  
 ![Lista lap Developer portálon alkalmazássablonok](./media/api-management-application-templates/APIM-Application-List-Page-Developer-Portal-Templates.png "APIM lista lap Developer portálon alkalmazássablonok")  
  
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
 A `Product list` sablon előfordulhat, hogy használja a következő [vezérlők lapon](api-management-page-controls.md).  
  
-   [lapozófájl-vezérlő](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Adatmodell  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Lapozás|[Lapozás](api-management-template-data-model-reference.md#Paging) entitás.|Az alkalmazások gyűjtemény lapozás adatait.|  
|Alkalmazások|A gyűjtemény [alkalmazás](api-management-template-data-model-reference.md#Application) entitásokat.|Az aktuális felhasználó számára látható alkalmazásokat.|  
|CategoryName|Karakterlánc|A kategória az alkalmazás.|  
  
### <a name="sample-template-data"></a>Mintaadatokat sablon  
  
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
  
##  <a name="Application"></a>Alkalmazás  
 A **alkalmazás** sablon lehetővé teszi a fejlesztői portálra az alkalmazás lap törzsét testreszabását.  
  
 ![Alkalmazássablonok lap Developer portálon](./media/api-management-application-templates/APIM-Application-Page-Developer-Portal-Templates.png "APIM lap Developer portálon alkalmazássablonok")  
  
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
 A `Application` sablon nem teszi lehetővé a használata [vezérlők lapon](api-management-page-controls.md).  
  
### <a name="data-model"></a>Adatmodell  
 [Alkalmazás](api-management-template-data-model-reference.md#Application) entitás.  
  
### <a name="sample-template-data"></a>Mintaadatokat sablon  
  
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

## <a name="next-steps"></a>Következő lépések
A sablonok használatának kapcsolatos további információkért lásd: [hogyan szabhatja testre a sablonok segítségével az API Management fejlesztői portálján](api-management-developer-portal-templates.md).