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
ms.date: 09/29/2017
ms.author: apipm
ms.openlocfilehash: 758babce3ed387ed4864f1934650cf701bda788f
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="api-import-restrictions-and-known-issues"></a>API-importálási korlátozások és ismert problémák
## <a name="about-this-list"></a>Ez a lista
Ha importál egy API-t, előfordulhat, hogy során bizonyos korlátozások vonatkoznak, vagy kapcsolatos problémákat jeleznek, amelyek sikeresen importálása előtt kell-e javítani kell. Ez a cikk a dokumentum fenti szerint vannak rendszerezve az API formátuma.

## <a name="open-api"></a>Nyissa meg a Swagger/API
A nyílt API-t dokumentum importálása hibák fordulnak elő, ha győződjön meg arról,-, vagy a-tervező használata az Azure portálon (Tervező - előtér - API Specification szerkesztő megnyitása), ellenőrzése, vagy egy külső gyártótól származó eszközzel, mint <a href="http://www.swagger.io">Swagger Editor</a>.

* **Állomásnév** APIM igényli, hogy a gazdagép neve attribútum.
* **Elérési út kiinduló** APIM egy alap elérési útja attribútumot igényel.
* **Sémák** APIM egy sémát tartalmazó tömb szükséges. 

## <a name="wsdl"></a>WSDL
SOAP áteresztő API-k létrehozása, vagy a SOAP-REST API háttéralkalmazás szolgálhat WSDL fájljait használja.

* **WSDL** -jelenleg APIM nem támogatja a API-k ezzel az attribútummal. Az ügyfelek kell az importált elem egyesítése egy dokumentumot.
* **Több alkotórészek üzenetek** jelenleg APIM nem támogatja az ilyen típusú üzenetek.
* **WCF wsHttpBinding** SOAP-szolgáltatások a Windows Communication Foundation létre kell használnia a basicHttpBinding - wsHttpBinding nem támogatott.
* **Az MTOM** -szolgáltatások MTOM <em>előfordulhat, hogy</em> működik. Jelenleg nem érhető hivatalos támogatást.
* **A rekurzió** , amelyek meghatározott rekurzív módon (például saját magukat olyan tömbjét lásd) APIM által nem támogatott.

## <a name="wadl"></a>WADL
Jelenleg nincsenek ismert problémák WADL importálása.
