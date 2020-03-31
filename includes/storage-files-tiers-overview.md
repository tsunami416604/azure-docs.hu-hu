---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4e5c59a5ce7c0d743a574309bb1b9276ce80bea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597814"
---
Az Azure Files két különböző tárolási szintet kínál, prémium és standard, hogy lehetővé tegye, hogy a megosztásokat a forgatókönyv teljesítmény- és árkövetelményeihez igazítsa:

- **Prémium fájlmegosztások:** A prémium fájlmegosztásokat ssd-meghajtók (SSD-k) támogatja, és a **FileStorage tárfiók** típusában telepítik őket. A prémium szintű fájlmegosztások konzisztens nagy teljesítményt és alacsony késleltetést biztosítanak, a legtöbb I/O-művelet esetében egyszámjegyű ezredmásodpercen belül, az Io-igényes számítási feladatokhoz. Ez leteszi őket a legkülönbözőbb számítási feladatok, például adatbázisok, website hosting, és a fejlesztési környezetekben. Prémium szintű fájlmegosztások csak egy kiépített számlázási modellben érhetők el. A prémium fájlmegosztások kiépített számlázási modelljéről a [Prémium fájlmegosztások kiépítésének ismertetése című témakörben talál](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares)további információt.
- **Szabványos fájlmegosztások**: A szabványos fájlmegosztásokat merevlemez-meghajtók (HDD-k) biztosítják, és az **általános célú 2-es verziójú (GPv2) tárfiók-típusban** vannak telepítve. A szabványos fájlmegosztások megbízható teljesítményt nyújtanak az IO-számítási feladatok számára, amelyek kevésbé érzékenyek a teljesítmény változékonyságára, például az általános célú fájlmegosztásokra és a fejlesztési/tesztelési környezetekre. A normál fájlmegosztások csak a felosztó-kiosztó számlázási modellben érhetők el.