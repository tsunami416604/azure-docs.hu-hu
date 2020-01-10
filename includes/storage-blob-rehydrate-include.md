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
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780240"
---
Az archív tárolóban lévő adatok olvasásához előbb módosítania kell a blob rétegét a gyakran vagy ritkán használt adatok tárolási rétegére. Ez a folyamat rehidratálás néven ismert, és órákig elvégezhető. Javasoljuk, hogy nagy méretű blobokat biztosítson az optimális rehidratáló teljesítmény érdekében. Több kis méretű blob egyidejűleg történő rehidratálása további időt vehet igénybe. Jelenleg két rehidratált prioritás, a magas (előzetes verzió) és a standard, amely az opcionális *x-MS-rehidratálás-priority* tulajdonsággal állítható be egy [set blob-rétegen](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) , vagy [másolási blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) -művelet.

* **Standard prioritás**: a rehidratálás kérése a kapott sorrendben lesz feldolgozva, és akár 15 órát is igénybe vehet.
* **Magas prioritású (előzetes verzió)** : a rehidratálás kérése a standard kérelmekre lesz rangsorolva, és 1 órán belül befejeződik. A magas prioritás a blob méretétől és a jelenlegi igényektől függően 1 óránál hosszabb időt vehet igénybe. A magas prioritású kérelmek garantáltan elsőbbséget élveznek a Normál prioritású kérelmekhez képest.

> [!NOTE]
> A standard prioritás az archiválás alapértelmezett újraszárítási beállítása. A magas prioritású megoldás gyorsabb, mint a Normál prioritású rehidratálás, és általában a vészhelyzeti adat-visszaállítási helyzetekben való használatra van fenntartva.

A rehidratálás kérésének kezdeményezése után nem lehet megszakítani. A rehidratálás során a blob *Archív állapot* tulajdonságának ellenőrzésével bizonyosodhat meg arról, hogy a szint módosítása befejeződött-e. Az állapot a célszinttől függően „rehydrate-pending-to-hot” (rehidratálás-folyamatban-a-gyakoriba) vagy „rehydrate-pending-to-cool” (rehidratálás-folyamatban-a-ritkába) lehet. A folyamat befejeztével a „archív állapot” tulajdonság törlődik, és a „hozzáférési szint” tulajdonság mutatja, hogy az új szint a gyakran vagy a ritkán használt szint-e.
