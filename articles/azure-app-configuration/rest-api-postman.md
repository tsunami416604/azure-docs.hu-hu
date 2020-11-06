---
title: Azure Active Directory REST API – tesztelés a Poster használatával
description: Az Azure-alkalmazás konfigurációjának tesztelése a Poster használatával REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 9690678fc7b794c694e588a7993cb131d8264a72
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424279"
---
# <a name="test-the-azure-app-configuration-rest-api-using-postman"></a>Az Azure-alkalmazás konfigurációjának tesztelése a Poster használatával REST API

A REST API a [Poster](https://www.getpostman.com/)használatával történő teszteléséhez meg kell adnia a kérések [hitelesítéséhez](./rest-api-authentication-hmac.md) szükséges HTTP-fejléceket. A következő lépésekkel konfigurálhatja a Poster-t a REST API teszteléséhez, a hitelesítési fejlécek automatikus létrehozásával:

1. Új [kérelem](https://learning.getpostman.com/docs/postman/sending_api_requests/requests/) létrehozása
1. Adja hozzá a `signRequest` függvényt a [JavaScript hitelesítési mintából](./rest-api-authentication-hmac.md#javascript) a kérelem [előkérési parancsfájljában](https://learning.getpostman.com/docs/postman/scripts/pre_request_scripts/)
1. Adja hozzá a következő kódot az előkérési parancsfájl végéhez. A hozzáférési kulcs frissítése a TODO-Megjegyzés által jelzett módon

    ```js
    // TODO: Replace the following placeholders with your access key
    var credential = "<Credential>"; // Id
    var secret = "<Secret>"; // Value

    var isBodyEmpty = pm.request.body === null || pm.request.body === undefined || pm.request.body.isEmpty();

    var headers = signRequest(
        pm.request.url.getHost(),
        pm.request.method,
        pm.request.url.getPathWithQuery(),
        isBodyEmpty ? undefined : pm.request.body.toString(),
        credential,
        secret);

    // Add headers to the request
    headers.forEach(header => {
        pm.request.headers.upsert({key: header.name, value: header.value});
    })
    ```

1. A kérelem elküldése
