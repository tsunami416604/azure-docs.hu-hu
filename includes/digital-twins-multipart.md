---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
ms.custom: include file
ms.openlocfilehash: 0e7cb7e4aaa9862a2b4af51593c29793ea54dd14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111246"
---
> [!NOTE]
> A többrészes kérelmek általában három darabot igényelnek:
> * **Tartalomtípus fejléc:**
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * A **Content-Disposition:**
>   * `form-data; name="metadata"`
> * A feltöltendő fájl tartalma
>
> **A tartalomtípus** és **a tartalom-elhelyezés** a használati forgatókönyvtől függően változhat.

A többrészes kérelmek programozott módon (C#-n keresztül) tehetők rest-ügyfélen vagy olyan eszközön keresztül, mint a [Postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#make-a-multipart-post-request). A REST ügyféleszközök különböző szintű támogatást élvezhetnek az összetett többrészes kérelmekhez. A konfigurációs beállítások eszközről eszközre változhatnak. Ellenőrizze, hogy melyik eszköz felel meg leginkább az igényeinek.

> [!IMPORTANT]
> Az Azure Digital Twins Management API-khoz intézett többrészes kérelmek általában két részből állnak:
> * A **tartalomtípus** és/vagy **a tartalom-megintézkedés** által deklarált blob-metaadatok (például társított MIME-típus)
> * Blob-tartalom, amely tartalmazza a feltöltendő fájl strukturálatlan tartalmát
>
> **A** PATCH-kérelmekhez a két rész egyike sem szükséges. Mindkettő szükséges a **POST** vagy a create műveletekhez.

A [Foglaltsági rövid útmutató forráskód](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) teljes C# példákat tartalmaz, amelyek bemutatják, hogyan lehet többrészes kérelmeket az Azure Digital Twins Management API-k ellen.