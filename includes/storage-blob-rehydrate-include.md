---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: fc5f4d2c10cac23600025a72fedf7fe2cec5a34e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684093"
---
Az archív tárolóban lévő adatok olvasásához előbb módosítania kell a blob szintjét a gyakran vagy ritkán használt adatok tárolási szintjére. Ezt a folyamatot rehidratálásnak nevezik, és órákig is eltarthat. Nagy blobméretek az optimális rehidratálási teljesítmény érdekében javasoljuk. Több kis méretű blob egyidejűleg történő rehidratálása további időt vehet igénybe. Jelenleg két rehidratálási prioritás, a Magas és a Normál, amely az opcionális *x-ms-rehidratálás-prioritású* tulajdonsággal állítható be egy [Blob-szint](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) vagy [blob másolása](https://docs.microsoft.com/rest/api/storageservices/copy-blob) műveleten keresztül.

* **Szokásos prioritás:** A rehidratálási kérelem a beérkezés sorrendjében kerül feldolgozásra, és akár 15 órát is igénybe vehet.
* **Kiemelt fontosságú:** A rehidratálási kérelem elsőbbséget élvez a standard kérelmek, és befejezheti 1 órán belül befejeződhet. A blob méretétől és az aktuális igénytől függően a magas prioritású blob mérete és az aktuális igény több mint 1 órát is igénybe vehet. A magas prioritású kérelmek garantáltan elsőbbséget élveznek a standard prioritási kérelmekel szemben.

> [!NOTE]
> A normál prioritás az archiválás alapértelmezett rehidratálási lehetősége. A magas prioritású egy gyorsabb lehetőség, amely többe kerül, mint a standard prioritású rehidratálás, és általában vészhelyzeti adat-visszaállítási helyzetekben való használatra van fenntartva.

A rehidratációs kérelem kezdeményezése után nem lehet visszavonni. A rehidratálási folyamat során az *x-ms-access-tier* blob tulajdonság továbbra is megjelenik az archívum, amíg a rehidratálás befejeződött egy online réteg. A rehidratálás állapotának és előrehaladásának megerősítéséhez hívhatja a [Blob-tulajdonságok begetése parancsot](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties) az *x-ms-archív állapot* és az *x-ms-rehidratálás-prioritású* blob tulajdonságok ellenőrzéséhez. Az archívum állapota a rehidratálási célszinttől függően a "rehidratálás-függőben lévő-forró" vagy a "rehidratálás-függőben lévő-to-cool" felirat olvasható. A rehidratálási prioritás a "Magas" vagy "Standard" sebességet jelzi. Befejezését követően az archív állapot és a prioritási tulajdonságok újrahidratálása, és a hozzáférési szint blob tulajdonság frissül, hogy tükrözze a kiválasztott gyakori vagy ritka elérésű szint.
