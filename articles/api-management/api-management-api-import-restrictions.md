---
title: Korlátozások és ismert problémákat az Azure API Management API importálási |} Microsoft Docs
description: Ismert problémák és korlátozások az Azure API Management használata a nyílt API-t, WSDL vagy WADL formátumú importálási részleteit.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apipm
ms.openlocfilehash: b33c95af94c436b1069658963692242d0f905554
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="api-import-restrictions-and-known-issues"></a>API-importálási korlátozások és ismert problémák
## <a name="about-this-list"></a>Ez a lista
Ha importál egy API-t, előfordulhat, hogy során bizonyos korlátozások vonatkoznak, vagy kapcsolatos problémákat jeleznek, amelyek sikeresen importálása előtt kell-e javítani kell. Ez a cikk a dokumentum fenti szerint vannak rendszerezve az API formátuma.

## <a name="open-api"> </a>Open API/Swagger
A nyílt API-t dokumentum importálása hibák fordulnak elő, ha győződjön meg arról,-, vagy a-tervező használata az Azure portálon (Tervező - előtér - API Specification szerkesztő megnyitása), ellenőrzése, vagy egy külső gyártótól származó eszközzel, mint <a href="http://www.swagger.io">Swagger Editor</a>.

* Csak a OpenAPI JSON formátum támogatott.
* Használatával sémák **$ref** tulajdonságok nem tartalmazhat más **$ref** tulajdonságait.
* **$ref** mutatók nem hivatkozhatnak külső fájlokat.
* **x-ms-elérési utak** és **x-kiszolgálók** csak támogatott kiterjesztések az alábbiak.
* Egyéni bővítmény importálási figyelmen kívül lesznek hagyva, és nem menti és nem őrződik meg az exportált.

> [!IMPORTANT]
> Az OpenAPI-importálásról ebben a [dokumentumban](https://blogs.msdn.microsoft.com/apimanagement/2018/03/28/important-changes-to-openapi-import-and-export/) talál fontos információt és cikkeket.

## <a name="wsdl"> </a>WSDL
SOAP áteresztő API-k létrehozása, vagy a SOAP-REST API háttéralkalmazás szolgálhat WSDL fájljait használja.
* **SOAP-kötések** -style "dokumentumok" és "literal" encoding csak a SOAP-kötések támogatottak. Nincs "rpc" style vagy a SOAP-kódolás nem támogatott.
* **WSDL** -Ez az attribútum nem támogatott. Az ügyfelek a import kell egyesítése egy dokumentumot.
* **Több alkotórészek üzenetek** -üzenetek típusai nem támogatottak.
* **WCF wsHttpBinding** -SOAP-szolgáltatások a Windows Communication Foundation létre kell használnia a basicHttpBinding - wsHttpBinding nem támogatott.
* **Az MTOM** - szolgáltatásokat az MTOM <em>előfordulhat, hogy</em> működik. Jelenleg nem érhető hivatalos támogatást.
* **A rekurzió** -típusok definiált rekurzív módon (például saját magukat olyan tömbjét lásd) APIM által nem támogatott.

## <a name="wadl"> </a>WADL
Jelenleg nincsenek ismert problémák WADL importálása.
