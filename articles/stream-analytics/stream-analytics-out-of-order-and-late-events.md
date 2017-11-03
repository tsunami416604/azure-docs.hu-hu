---
title: "Esemény sorrendjét és az Azure Stream Analytics késedelmesség kezelése |} Microsoft Docs"
description: "Tudnivalók a Stream Analytics-soron vagy késői események adatfolyamban működéséről."
keywords: "nem megfelelő sorrendben, késői, események"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.openlocfilehash: cf57bd12a62b3de8ac49b26ce7cdc40aec0b6738
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stream-analytics-event-order-handling"></a>Az Azure Stream Analytics rendelés eseménykezelésnek

Minden esemény historikus adatfolyam események időbélyeg kapja. Az Azure Stream Analytics Timestamp típusú eseményekhez érkezésének ideje vagy alkalmazás idő rendeli hozzá. A "System.Timestamp" oszlop van rendelve az esemény időbélyegzője. Érkezésének ideje hozzá van rendelve a bemeneti forrásnál, ha az esemény eléri a forrás. Érkezési ideje EventEnqueuedTime Eseményközpont bemeneti és [blob utolsó módosítás időpontja](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) blob bemeneti. Alkalmazási hozzá van rendelve, ha az esemény jön létre, és a tartalom egy részét. Alkalmazás ideje eseményeket feldolgozását, használja a "Időbélyeg által" záradék a select lekérdezés. Hiányzik a "Időbélyeg által" záradék, ha érkezésének ideje eseményeket dolgoznak fel. Érkezésének ideje elérhető EventEnqueuedTime tulajdonság használatával az event hubs és a blob bemeneti BlobLastModified tulajdonság használatával. Az Azure Stream Analytics a Timestamp típusú sorrendben kimenetet, és néhány beállítás nem megfelelő sorrendben adatok kezelésére szolgál.

![Stream Analytics eseménykezelésnek](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

Bemeneti adatfolyamot, amelyek nincsenek sorrendben vagy szerepelnek:
* Rendezett (és ezért **késleltetett**).
* A felhasználó által megadott házirendnek megfelelően igazítva a rendszer.

A Stream Analytics eltűr késői és üzemen kívüli események által alkalmazás feldolgozása közben.

**Késő érkezés tolerancia**

* A beállítás akkor alkalmazható, csak akkor, ha alkalmazási általi feldolgozás, ellenkező esetben figyelmen kívül.
* Ez a maximális érkezésének ideje és kérelem időpont között. Ha alkalmazás idő előtt (érkezési idő - késő érkezés ablak), akkor időre van beállítva (érkezési - késő érkezés ablakban)
* Több partíciót az ugyanazon bemeneti adatfolyamból vagy több bemeneti adatfolyam együttesen együtt, késő érkezés tolerancia az a maximális időtartam minden partíció megvárja-e az új adatokat. 

Késő érkezés ablak röviden, a maximális késleltetési az eseménygenerálás és a bemeneti forrásnál esemény fogadását között.
Késő érkezés tolerancia alapján módosítás először történik, és nem megfelelő sorrendben történik mellett. A **System.Timestamp** oszlop lesz a végső időbélyeg az esemény rendelt.

**Nem megfelelő sorrendben tolerancia**

* Az események sorrendje nem, de a "üzemen kívüli tűrési" halmazában érkező **által időbélyeg átrendezésekor**. 
* Legkésőbb tolerancia érkező események **eldobni vagy módosítani**.
    * **Igazítva**: igazítva érkezett meg a legújabb elfogadható idő jelennek meg. 
    * **Eldobott**: vetve.

"Üzemen kívüli tűrési" belül fogadott események sorrendjének módosításához a lekérdezés eredménye **által üzemen kívüli tűrési késleltetett**.

**Példa**

Késő érkezés tolerancia = 10 perc<br/>
Rendelés tolerancia kívül = 3 perc<br/>
Feldolgozási idő szerint<br/>

Események:

Esemény 1 _alkalmazási_ 00:00:00 = _érkezésének ideje_ = 00:10:01, _System.Timestamp_ 00:00:01, mert igazítva = (_érkezésének ideje_  -  _Alkalmazási_) nagyobb, mint késő érkezés tolerancia.

Esemény 2 _alkalmazási_ = 00:00:01, _érkezésének ideje_ = 00:10:01, _System.Timestamp_ = 00:00:01, nem beállítani, mert a kérelem ideje a késő érkezés belül ablak.

Esemény 3 _alkalmazási_ 00:10:00 = _érkezésének ideje_ 00:10:02 = _System.Timestamp_ 00:10:00, nem módosul, mert alkalmazások idő késő érkezés ablakban = .

Esemény 4 _alkalmazási_ 00:09:00 = _érkezésének ideje_ 00:10:03 = _System.Timestamp_ = 00:09:00, elfogadott eredeti Timestamp értékkel rendelkező alkalmazás ideje területén belül rendelés tűréshatáron.

Esemény 5 _alkalmazási_ 00:06:00 = _érkezésének ideje_ 00:10:04 = _System.Timestamp_ 00:07:00, beállítani, mert a kérelem ideje régebbi, mint a nem megfelelő sorrendben = tolerancia.



## <a name="get-help"></a>Segítségkérés
Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Következő lépések
* [A Stream Analytics bemutatása](stream-analytics-introduction.md)
* [A Stream Analytics használatába](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-feladatok méretezése](stream-analytics-scale-jobs.md)
* [Stream Analytics lekérdezési nyelvi referencia](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [A Stream Analytics felügyeleti REST API-referencia](https://msdn.microsoft.com/library/azure/dn835031.aspx)
