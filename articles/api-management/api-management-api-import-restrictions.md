---
title: "Korlátozások és ismert problémákat az Azure API Management API importálási |} Microsoft Docs"
description: "Ismert problémák és korlátozások az Azure API Management használata a nyílt API-t, WSDL vagy WADL formátumú importálási részleteit."
services: api-management
documentationcenter: 
author: vladvino
manager: vlvinogr
editor: 
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: apipm
ms.openlocfilehash: 4cb6ad53b59b81f906a85027f4ff988bbb78706a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="api-import-restrictions-and-known-issues"></a>API-importálási korlátozások és ismert problémák
## <a name="about-this-list"></a>Ez a lista
Közben mindent történő esetén győződjön meg arról, hogy importálja az API-t Azure API Management, zökkenőmentes és zökkenőmentes lehetséges, a Microsoft alkalmanként ugyanazok a korlátozások vagy kapcsolatos problémákat jeleznek, amelyek sikeresen importálása előtt kell-e javítani kell. Ez a cikk dokumentálja ezeket, az API formátuma által szervezett.

## <a name="open-api"></a>Nyissa meg a Swagger/API
Általában a nyílt API-t dokumentum importálása hibák fordulnak elő, ha győződjön meg arról ellenőrzése -, vagy a-tervező használata az új Azure-portálon (Tervező - előtér - API Specification szerkesztő megnyitása), vagy az egy 3. fél eszköz például <a href="http://www.swagger.io">Swagger Editor</a>.

* **Állomásnév** a host name attribútum szükséges.
* **Elérési út kiinduló** egy alap elérési útja attribútum szükséges.
* **Sémák** kérjük, a rendszer tömb. 

## <a name="wsdl"></a>WSDL
SOAP áteresztő API-k létrehozása, vagy a SOAP-REST API háttéralkalmazás szolgálhat WSDL fájljait használja.

* **WSDL** jelenleg nem támogatott API-k ezzel az attribútummal. Az ügyfelek kell az importált elem egyesítése egy dokumentumot.
* **Több alkotórészek üzenetek** jelenleg nem támogatottak.
* **WCF wsHttpBinding** SOAP-szolgáltatások a Windows Communication Foundation létre kell használnia a basicHttpBinding - wsHttpBinding nem támogatott.
* **Az MTOM** -szolgáltatások MTOM <em>előfordulhat, hogy</em> működik. Jelenleg nem érhető hivatalos támogatást.
* **A rekurzió** , amelyek meghatározott rekurzív (pl. Lásd tömbje magukat) használata nem támogatott.

## <a name="wadl"></a>WADL
Jelenleg nincsenek ismert WADL importálási problémák.


[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to cache operation results in Azure API Management]: api-management-howto-cache.md
