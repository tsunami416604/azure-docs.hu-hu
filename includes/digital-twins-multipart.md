---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 01/02/2019
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 1c6579776b86decb78c172578cbe55a66c05d78f
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54026553"
---
> [!NOTE]
> Több részből álló kérelmeket általában a művelethez három adatra van szükség:
> * A **Content-Type** fejléc:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * A **tartalomtípus-szabályozó**:
>   * `form-data; name="metadata"`
> * A feltöltendő fájl tartalma
>
> **A Content-Type** és **tartalomtípus-szabályozó** használat forgatókönyvének függően változhat.

Több részből álló kéréseket programozott módon lehet tenni (keresztül C#), egy REST-ügyféllel, vagy az eszköz, például [Postman](https://www.getpostman.com/). Előfordulhat, hogy a REST-ügyféleszköz különböző szintű összetett többrészes kérelmek támogatása. Ellenőrizze, hogy melyik eszköz legjobban az igényeinek.

> [!IMPORTANT]
> Az Azure digitális Twins felügyeleti API-k többrészes kérelmek két részből rendelkezik:
> * BLOB metaadatai (például egy kapcsolódó MIME-típus), amely nem deklarál **Content-Type** és **tartalom-törlése**
> * BLOB tartalmát, többek között a strukturálatlan tartalmát egy feltölteni kívánt fájlt
>
> A két részből egyike sem nem szükséges **javítására** kérelmeket. Mindkettő szükséges **POST** vagy -létrehozási műveletek.
