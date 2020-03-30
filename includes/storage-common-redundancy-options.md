---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 73c2b742ede21a4e86d717d994f8ebc4f16389c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77157218"
---
A tárfiók redundanciabeállításai a következők:

* Helyileg redundáns tárolás (LRS): Egyszerű, alacsony költségű redundancia-stratégia. Az adatok másolása szinkron módon háromszor az elsődleges régión belül történik.
* Zónaredundáns tárolás (ZRS): Redundancia a magas rendelkezésre állást igénylő forgatókönyvek. Az adatok szinkron módon másolva három Azure rendelkezésre állási zónában az elsődleges régióban.
* Georedundáns tárolás (GRS): Régiók közötti redundancia a regionális kimaradások elleni védelem érdekében. Az adatokat a rendszer háromszor, majd aszinkron módon másolja a másodlagos régióba. A másodlagos régióban lévő adatokolvasási hozzáférés engedélyezéséhez engedélyezze az olvasási hozzáférésű georedundáns tárolást (RA-GRS).
* Geozónaredundáns tárolás (GZRS) (előzetes verzió): Redundancia olyan forgatókönyvek esetén, amelyek magas rendelkezésre állást és maximális tartósságot igényelnek. Az adatok at szinkron módon másolja az elsődleges régió három Azure-rendelkezésre állási zónájába, majd aszinkron módon a másodlagos régióba. A másodlagos régióban lévő adatokolvasási hozzáférés engedélyezéséhez engedélyezze az olvasási hozzáférésű geozónaredundáns tárolást (RA-GZRS).

Az Azure Storage redundanciabeállításairól az [Azure Storage redundanciája](../articles/storage/common/storage-redundancy.md)című témakörben talál további információt.
