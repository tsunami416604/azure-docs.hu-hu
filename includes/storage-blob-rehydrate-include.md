---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: 81ffc87ce97a936e693c59bca6caf721cb8599cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75780240"
---
Az archív tárolóban lévő adatok olvasásához előbb módosítania kell a blob szintjét a gyakran vagy ritkán használt adatok tárolási szintjére. Ezt a folyamatot rehidratálásnak nevezik, és órákig is eltarthat. Nagy blobméretek az optimális rehidratálási teljesítmény érdekében javasoljuk. Több kis méretű blob egyidejűleg történő rehidratálása további időt vehet igénybe. Jelenleg két rehidratálási prioritás van: magas (előzetes verzió) és standard, amelyek az opcionális *x-ms-rehidratálás-prioritású* tulajdonsággal állíthatók be egy [Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) vagy Copy [Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) műveleten.

* **Szokásos prioritás:** A rehidratálási kérelem a beérkezés sorrendjében kerül feldolgozásra, és akár 15 órát is igénybe vehet.
* **Magas prioritású (előzetes verzió)**: A rehidratálási kérelem elsőbbséget élvez a standard kérelmek, és befejezheti 1 órán belül. A blob méretétől és az aktuális igénytől függően a magas prioritású blob mérete és az aktuális igény több mint 1 órát is igénybe vehet. A magas prioritású kérelmek garantáltan elsőbbséget élveznek a standard prioritási kérelmekel szemben.

> [!NOTE]
> A normál prioritás az archiválás alapértelmezett rehidratálási lehetősége. A magas prioritású egy gyorsabb lehetőség, amely többe kerül, mint a standard prioritású rehidratálás, és általában vészhelyzeti adat-visszaállítási helyzetekben való használatra van fenntartva.

A rehidratációs kérelem kezdeményezése után nem lehet visszavonni. A rehidratálás során ellenőrizheti az *Archív állapot* blob tulajdonságot, hogy ellenőrizze, hogy a réteg megváltozott-e. Az állapot a célszinttől függően „rehydrate-pending-to-hot” (rehidratálás-folyamatban-a-gyakoriba) vagy „rehydrate-pending-to-cool” (rehidratálás-folyamatban-a-ritkába) lehet. Befejezését követően az archív állapot tulajdonság törlődik, és az *Access Tier* blob tulajdonság tükrözi az új gyakori vagy ritka elérésű szint.
