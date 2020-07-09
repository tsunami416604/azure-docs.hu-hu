---
title: fájlbefoglalás
description: fájlbefoglalás
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4e5c59a5ce7c0d743a574309bb1b9276ce80bea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77597814"
---
A Azure Files két különböző szintű tárterületet kínál, amelyek lehetővé teszik a megosztások testreszabását a forgatókönyv teljesítményére és árára vonatkozó követelményekkel:

- **Prémium fájlmegosztás**: a prémium fájlmegosztás SSD-meghajtókon alapul, és a rendszer a **FileStorage Storage-fiókba** telepíti. A prémium szintű fájlmegosztás konzisztens nagy teljesítményt és kis késleltetést biztosít a legtöbb IO-művelethez, az IO-igényes munkaterhelések esetében. Ez a munkaterhelések, például az adatbázisok, a webhelyek üzemeltetése és a fejlesztői környezetek széles skálájának megfelelővé teszi. A prémium fájlmegosztás csak kiépített számlázási modellben érhető el. A prémium szintű fájlmegosztás kiépített számlázási modelljével kapcsolatos további információkért lásd a [prémium szintű fájlmegosztás kiépítés ismertetése](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares)című témakört.
- **Szabványos fájlmegosztás**: a standard fájlmegosztást merevlemez-meghajtók (HDD-k) teszik elérhetővé, és az **általános célú 2-es verziójú (GPv2) Storage-fiók** típusán vannak telepítve. A standard fájlmegosztás megbízható teljesítményt biztosít olyan IO-munkaterhelések esetében, amelyek kevésbé érzékenyek a teljesítmény változékonyságára, például az általános célú fájlmegosztás és a fejlesztési/tesztelési környezetek számára. A standard fájlmegosztás csak utólagos elszámolású számlázási modellben érhető el.