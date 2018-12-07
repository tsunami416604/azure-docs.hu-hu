---
title: Korlátozások és ismert problémák az Azure API Management API importálása |} A Microsoft Docs
description: Ismert problémák és korlátozások az Open API, WSDL vagy WADL-formátumok használata az Azure API Management-ba való importálásuk részletei.
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
ms.openlocfilehash: c55a80749506b0a03af2f8c5f0179b67c8a78d15
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53016742"
---
# <a name="api-import-restrictions-and-known-issues"></a>API importálási korlátozások és ismert problémák
## <a name="about-this-list"></a>Ez a lista kapcsolatban
API importálásakor, előfordulhat, hogy bizonyos korlátozások bővítményeként vagy azonosíthatja a problémákat, amelyek sikeresen importálása előtt kell-e javítani kell. Ez a cikk dokumentumok, ezek szerint vannak rendezve az API-formátuma.

## <a name="open-api"> </a>OpenAPI/Swagger
Ha az OpenAPI-dokumentumok importálása hibák azért küldtük Önnek, győződjön meg arról, ellenőrzése –, vagy a Tervező használatával az Azure Portalon (- előtér - Tervező OpenAPI-specifikáció szerkesztője), vagy egy külső gyártótól származó eszköz például <a href="https://editor.swagger.io">Swagger Editor</a>.

* Csak az az OpenAPI JSON formátum támogatott.
* Elérési út és a lekérdezési paramétereket egyedi névvel kell rendelkezniük. (Az OpenAPI-paraméter neve csak egyedinek kell lennie egy helyen, például az elérési út, lekérdezés, fejléc.  Azonban az API Management lehetővé tesszük műveletek legyen hátrányos megkülönböztetés elérési út és a lekérdezési paraméterek (amely nem támogatja a OpenAPI). Ezért szükség van a teljes URL-cím sablonon belül egyedinek kell lennie a paraméterek nevei.)
* Sémák használatával hivatkozott **$ref** tulajdonságai nem tartalmazhatnak más **$ref** tulajdonságait.
* **$ref** mutatók nem hivatkozhatnak külső fájlok.
* **x-ms-elérési utak** és **x-kiszolgálók** csak támogatott bővítmények.
* Egyéni bővítmények rendszer figyelmen kívül hagyja az importálás és a nem mentett vagy megőrzi az exportálás.
* **A rekurzió** -definíciókat definiált rekurzív módon (például tekintse meg magukat) APIM által nem támogatott.

> [!IMPORTANT]
> Az OpenAPI-importálásról ebben a [dokumentumban](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/) talál fontos információt és cikkeket.

## <a name="wsdl"> </a>WSDL
WSDL-fájlt a SOAP átmenő API-k készítése vagy folyamatában SOAP – REST API-t, a háttérrendszer használja.
* **A SOAP-kötések** -stílus "dokumentumok" és "szövegkonstans" kódolás csak a SOAP-kötések támogatottak. A rendszer nem támogatja, a "rpc" style vagy a SOAP-kódolást.
* **WSDL** – Ez az attribútum nem támogatott. Ügyfeleink a import kell egyesítése egy dokumentumot.
* **A több részből üzenetek** – az ilyen típusú üzenetek nem támogatottak.
* **A WCF wsHttpBinding** -SOAP-szolgáltatások a Windows Communication Foundation létrehozott használjon basicHttpBinding – wsHttpBinding nem támogatott.
* **Zprávy MTOM** – zprávy MTOM használó szolgáltatások <em>előfordulhat, hogy</em> működik. Hivatalos támogatási jelenleg nem érhető el.
* **Rekurze** -típusokat, amelyek meghatározott rekurzív módon (például lásd tömbjét magukat) APIM által nem támogatott.

## <a name="wadl"> </a>WADL
Jelenleg nincsenek nem WADL-importálási ismert problémák.
