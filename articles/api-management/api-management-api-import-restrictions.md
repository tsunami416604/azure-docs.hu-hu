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
ms.openlocfilehash: 03d785898398cb0bcd7b43e8d7feab705bce4b34
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598470"
---
# <a name="api-import-restrictions-and-known-issues"></a>API-importálási korlátozások és ismert problémák
## <a name="about-this-list"></a>Ez a lista
Ha importál egy API-t, előfordulhat, hogy során bizonyos korlátozások vonatkoznak, vagy kapcsolatos problémákat jeleznek, amelyek sikeresen importálása előtt kell-e javítani kell. Ez a cikk a dokumentum fenti szerint vannak rendszerezve az API formátuma.

## <a name="open-api"> </a>OpenAPI/Swagger
A OpenAPI dokumentum importálása hibák fordulnak elő, ha győződjön meg arról,-, vagy a-tervező használata az Azure portálon (Tervező - előtér - OpenAPI specifikáció-szerkesztőben) ellenőrzése, vagy egy külső gyártótól származó eszközzel, mint <a href="http://www.swagger.io">Swagger Editor</a>.

* Csak a OpenAPI JSON formátum támogatott.
* Kötelező paraméterek között útvonal és a lekérdezés neve csak egyedi lehet. (A OpenAPI a paraméter neve csak egyedinek kell lennie egy helyen, például a elérési útja, a lekérdezés, a fejléc.  Azonban az API Management lehetővé hogy műveletek hátrányos kell megkülönböztetés útvonal és a lekérdezés paraméter (amely nem támogatja a OpenAPI) alapján. Ezért kérjük paraméterek nevei a teljes URL-cím sablon belül egyedinek kell lennie.)
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
