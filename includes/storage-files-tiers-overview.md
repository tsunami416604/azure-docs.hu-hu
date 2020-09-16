---
title: fájlbefoglalás
description: fájlbefoglalás
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e75cb7d13fb74d32191ab7f076d73ad66976503d
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90606659"
---
A Azure Files négyféle tárterületet kínál, a prémium, a tranzakciós optimalizált, a gyakori és a ritka elérésű csomagokat, hogy lehetővé tegye a megosztások testreszabását a forgatókönyv teljesítményére és árára vonatkozó követelmények szerint:

- **Prémium**: a prémium szintű fájlmegosztást SSD-meghajtók teszik elérhetővé, és a **FileStorage a Storage-fiók** típusában vannak telepítve. A prémium szintű fájlmegosztás konzisztens nagy teljesítményt és kis késleltetést biztosít a legtöbb IO-művelethez, az IO-igényes munkaterhelések esetében. A prémium szintű fájlmegosztás a munkaterhelések, például az adatbázisok, a webhelyek üzemeltetése és a fejlesztői környezetek széles skálájának megfelelő. A prémium fájlmegosztás a Server Message Block (SMB) és a Network File System (NFS) protokollokkal is használható.
- **Tranzakció optimalizálva**: a tranzakciós optimalizált fájlmegosztás lehetővé teszi a tranzakciók nagy terhelését, amelyeknek nincs szükségük a prémium szintű fájlmegosztás által kínált késésre. A tranzakciós optimalizált fájlmegosztás a merevlemez-meghajtók (HDD-k) által támogatott standard szintű tárolóeszközön érhető el, és az **általános célú 2-es verziójú (GPv2) Storage-fiók** típusa szerint van üzembe helyezve. Az optimalizált tranzakció "standard" néven szerepel, azonban ez a tárolási adathordozó típusra hivatkozik, és nem maga a réteg (a gyakori és a ritka elérési szint is a "standard", mivel ezek a szabványos tárolási hardveren találhatók).
- **Gyors**: a gyakori fájlmegosztás olyan általános célú fájlmegosztást kínál, mint például a csapat megosztása és a Azure file Sync. A gyors fájlmegosztást a HDD-k által támogatott szabványos tárolóeszközön kínáljuk, és az **általános célú 2-es verziójú (GPv2) Storage-fióktípus** telepítik.
- **Cool**: a lassú fájlmegosztás költséghatékony tárterületet kínál, amely online archiválási tárolási forgatókönyvekhez van optimalizálva. A Azure File Sync is megfelelő lehet az alacsonyabb adatforgalom-számítási feladatokhoz. A lassú fájlmegosztást a HDD-k által támogatott szabványos tárolóeszközön kínáljuk, és az **általános célú 2-es verziójú (GPv2) Storage-fiókba** vannak telepítve.

A prémium fájlmegosztás csak kiépített számlázási modellben érhető el. A prémium szintű fájlmegosztás kiépített számlázási modelljével kapcsolatos további információkért lásd a [prémium szintű fájlmegosztás kiépítés ismertetése](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares)című témakört. A standard fájlmegosztás, beleértve a tranzakciós optimalizált, a gyors és a lassú fájlmegosztást is, a díjköteles számlázással érhető el.

A gyakori és ritka elérésű fájlmegosztás az összes Azure nyilvános és Azure Government régióban elérhető. A tranzakciós optimalizált fájlmegosztás minden Azure-régióban elérhető, beleértve az Azure China-t és az Azure Germany-régiókat.

A gyakori vagy ritka elérésű fájlmegosztás üzembe helyezésével kapcsolatban lásd: [gyors vagy lassú fájlmegosztás létrehozása](../articles/storage/files/storage-how-to-create-file-share.md#create-a-hot-or-cool-file-share). 