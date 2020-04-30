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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684093"
---
Az archív tárolóban lévő adatok olvasásához előbb módosítania kell a blob szintjét a gyakran vagy ritkán használt adatok tárolási szintjére. Ez a folyamat rehidratálás néven ismert, és órákig elvégezhető. Javasoljuk, hogy nagy méretű blobokat biztosítson az optimális rehidratáló teljesítmény érdekében. Több kis méretű blob egyidejűleg történő rehidratálása további időt vehet igénybe. Jelenleg két rehidratált prioritás van, a magas és a standard, amely az opcionális *x-MS-rehidratálás-priority* tulajdonsággal állítható be a [blob-rétegen](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) , vagy [másolási blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) művelet.

* **Standard prioritás**: a rehidratálás kérése a kapott sorrendben lesz feldolgozva, és akár 15 órát is igénybe vehet.
* **Magas prioritás**: a rehidratálás kérése a standard kérelmekre lesz rangsorolva, és 1 órán belül befejeződik. A magas prioritás a blob méretétől és a jelenlegi igényektől függően 1 óránál hosszabb időt vehet igénybe. A magas prioritású kérelmek garantáltan elsőbbséget élveznek a Normál prioritású kérelmekhez képest.

> [!NOTE]
> A standard prioritás az archiválás alapértelmezett újraszárítási beállítása. A magas prioritású megoldás gyorsabb, mint a Normál prioritású rehidratálás, és általában a vészhelyzeti adat-visszaállítási helyzetekben való használatra van fenntartva.

A rehidratálás kérésének kezdeményezése után nem lehet megszakítani. A rehidratálás folyamata során az *x-MS-Access-réteg* blob tulajdonsága továbbra is archívumként fog megjelenni, amíg a rehidratálás online szintre nem kerül. A rehidratálás állapotának és előrehaladásának ellenőrzéséhez hívhatja le a [blob tulajdonságait](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties) , hogy ellenőrizze az *x-MS-Archive-status* és az *x-MS-rehidratálás-prioritás* blob tulajdonságait. Az Archívum állapota a rehidratálás-függőben lévők és a "rehidratálás – függőben" állapotot olvashatja a rehidratálás rendeltetési szintjétől függően. A rehidratálás prioritása a "magas" vagy a "standard" sebességét jelzi. Befejezésekor a rendszer eltávolítja az archiválási állapotot és a rehidratált prioritási tulajdonságokat, és a hozzáférési réteg blob tulajdonsága frissül, hogy tükrözze a kiválasztott gyors vagy ritka szintet.
