---
title: fájlbefoglalás
description: fájlbefoglalás
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 73c2b742ede21a4e86d717d994f8ebc4f16389c9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77157218"
---
A Storage-fiók redundancia-beállításai a következők:

* Helyileg redundáns tárolás (LRS): egyszerű, alacsony költséghatékonyságú redundancia-stratégia. Az Adatmásolást az elsődleges régión belül háromszor kell szinkronizálni.
* Zone-redundáns tárolás (ZRS): a magas rendelkezésre állást igénylő forgatókönyvek redundancia. Az Adatmásolás szinkron módon történik az elsődleges régió három Azure-beli rendelkezésre állási zónájában.
* Geo-redundáns tárolás (GRS): régiók közötti redundancia a regionális kimaradások elleni védelemhez. Az Adatmásolást az elsődleges régióban háromszor szinkronizálja a rendszer, majd aszinkron módon másolja át a másodlagos régióba. A másodlagos régióban lévő adatolvasási hozzáférés engedélyezéséhez engedélyezze az olvasási hozzáférésű geo-redundáns tárolást (RA-GRS).
* Geo-Zone-redundáns tárolás (GZRS) (előzetes verzió): a magas rendelkezésre állást és a maximális tartósságot igénylő forgatókönyvek redundancia. Az Adatmásolást a rendszer szinkron módon másolja az elsődleges régió három Azure-beli rendelkezésre állási zónájában, majd aszinkron módon másolja át a másodlagos régióba. Ha olvasási hozzáférést szeretne a másodlagos régióban lévő adathozzáféréshez, engedélyezze az olvasási hozzáférésű geo-Zone-redundáns tárolást (RA-GZRS).

Az Azure Storage redundancia-lehetőségeivel kapcsolatos további információkért lásd: [Azure Storage redundancia](../articles/storage/common/storage-redundancy.md).
