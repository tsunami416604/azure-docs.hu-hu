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
ms.openlocfilehash: 6eb7993b4dbec3ab4901dc7071d18eae98ab8ae4
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54079255"
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

A [foglaltsága rövid forráskód](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) tartalmazza a teljes C# példa bemutatja, hogyan lehet, hogy az Azure digitális Twins felügyeleti API-k többrészes kérelmeket.