---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 01/10/2019
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 47f66fc61fdd397350efb934bf21f488960a27a2
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54233059"
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

Több részből álló kéréseket programozott módon lehet tenni (keresztül C#), egy REST-ügyféllel, vagy az eszköz, például [Postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#multi). Előfordulhat, hogy a REST-ügyféleszköz különböző szintű összetett többrészes kérelmek támogatása. Ellenőrizze, hogy melyik eszköz számára az igényeinek a leginkább megfelelő-e.

> [!IMPORTANT]
> Több részből álló érkező kérelmeket az Azure digitális Twins felügyeleti API-k általában van két részből áll:
> * BLOB metaadatai (például egy kapcsolódó MIME-típus), amely nem deklarál **Content-Type** és **tartalom-törlése**
> * BLOB tartalmát, többek között a strukturálatlan tartalmát egy feltölteni kívánt fájlt
>
> A két részből egyike sem nem szükséges **javítására** kérelmeket. Mindkettő szükséges **POST** vagy -létrehozási műveletek.

A [foglaltsága rövid forráskód](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) tartalmazza a teljes C# példa bemutatja, hogyan lehet, hogy az Azure digitális Twins felügyeleti API-k többrészes kérelmeket.