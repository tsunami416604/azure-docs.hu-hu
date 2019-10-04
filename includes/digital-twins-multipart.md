---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 10/02/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 76a05817ae91bb49240e5d72988c776ce67ec1c0
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71845469"
---
> [!NOTE]
> A többrészes kérelmek általában három darabot igényelnek:
> * Egy **Content-Type** fejléc:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * Egy **Content-hajlam**:
>   * `form-data; name="metadata"`
> * A feltölteni kívánt fájl tartalma
>
> A **tartalom típusa** és a **tartalomra való hajlam** a használati forgatókönyvtől függően változhat.

A többrészes kérelmek programozott módon (átmenő C#), Rest-ügyféllel vagy más eszközzel (például [Poster](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#make-a-multipart-post-request)) hozhatók létre. A REST-ügyfél eszközei eltérő szintű támogatást nyújthatnak az összetett többrészes kérelmekhez. A konfigurációs beállítások az eszköztől az eszköztől némileg eltérőek lehetnek. Ellenőrizze, hogy melyik eszköz megfelel az igényeinek.

> [!IMPORTANT]
> Az Azure digitális Twins felügyeleti API-khoz küldött többrészes kérelmek általában két részből állnak:
> * A blob metaadatainak (például a hozzá tartozó MIME-típus), amelyet a **tartalom típusa** és/vagy a **tartalom-elrendezés** deklarál
> * A feltölteni kívánt fájl strukturálatlan tartalmát tartalmazó blob tartalma
>
> A **javítási** kérelmek esetében a két rész egyikének sem kell lennie. Mindkettő szükséges a **post** vagy a Create műveletekhez.

A foglaltság gyors üzembe helyezési [forráskódja](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) olyan teljes C# példákat tartalmaz, amelyek bemutatják, hogyan lehet többrészes kérelmeket készíteni az Azure digitális Twins felügyeleti API-