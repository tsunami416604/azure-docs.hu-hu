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
ms.openlocfilehash: 7d0286b63703c165dda6cd12bb625fc64272aac1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376401"
---
A Azure Files négyféle tárterületet kínál, a prémium, a tranzakciós optimalizált, a gyakori és a ritka elérésű csomagokat, hogy lehetővé tegye a megosztások testreszabását a forgatókönyv teljesítményére és árára vonatkozó követelmények szerint:

- **Prémium**: a prémium szintű fájlmegosztást SSD-meghajtók teszik elérhetővé, és a **FileStorage a Storage-fiók** típusában vannak telepítve. A prémium szintű fájlmegosztás konzisztens nagy teljesítményt és kis késleltetést biztosít a legtöbb IO-művelethez, az IO-igényes munkaterhelések esetében. A prémium szintű fájlmegosztás a munkaterhelések, például az adatbázisok, a webhelyek üzemeltetése és a fejlesztői környezetek széles skálájának megfelelő. A prémium fájlmegosztás a Server Message Block (SMB) és a Network File System (NFS) protokollokkal is használható.
- **Tranzakció optimalizálva**: a tranzakciós optimalizált fájlmegosztás lehetővé teszi a tranzakciók nagy terhelését, amelyeknek nincs szükségük a prémium szintű fájlmegosztás által kínált késésre. A tranzakciós optimalizált fájlmegosztás a merevlemez-meghajtók (HDD-k) által támogatott standard szintű tárolóeszközön érhető el, és az **általános célú 2-es verziójú (GPv2) Storage-fiók** típusa szerint van üzembe helyezve. Az optimalizált tranzakció "standard" néven szerepel, azonban ez a tárolási adathordozó típusra hivatkozik, és nem maga a réteg (a gyakori és a ritka elérési szint is a "standard", mivel ezek a szabványos tárolási hardveren találhatók).
- **Gyors**: a gyakori fájlmegosztás olyan általános célú fájlmegosztást kínál, mint például a csapat megosztása és a Azure file Sync. A gyors fájlmegosztást a HDD-k által támogatott szabványos tárolóeszközön kínáljuk, és az **általános célú 2-es verziójú (GPv2) Storage-fióktípus** telepítik.
- **Cool**: a lassú fájlmegosztás költséghatékony tárterületet kínál, amely online archiválási tárolási forgatókönyvekhez van optimalizálva. A Azure File Sync is megfelelő lehet az alacsonyabb adatforgalom-számítási feladatokhoz. A lassú fájlmegosztást a HDD-k által támogatott szabványos tárolóeszközön kínáljuk, és az **általános célú 2-es verziójú (GPv2) Storage-fiókba** vannak telepítve.

A prémium fájlmegosztás csak kiépített számlázási modellben érhető el. A prémium szintű fájlmegosztás kiépített számlázási modelljével kapcsolatos további információkért lásd a [prémium szintű fájlmegosztás kiépítés ismertetése](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares)című témakört. A standard fájlmegosztás, beleértve a tranzakciós optimalizált, a gyors és a lassú fájlmegosztást is, a díjköteles számlázással érhető el.

A gyakori és ritka elérésű fájlmegosztás az összes Azure nyilvános és Azure Government régióban elérhető. A tranzakciós optimalizált fájlmegosztás minden Azure-régióban elérhető, beleértve az Azure China-t és az Azure Germany-régiókat.

> [!Important]  
> A fájlmegosztást a GPv2 belül is áthelyezheti (a tranzakció optimalizált, gyors és lassú). Megosztható a csomagok közötti váltások tranzakció: a melegebb szintről a hűvösebb szintre való áttéréskor a hűvösebb réteg írási tranzakciós díja a megosztás minden egyes fájljánál, míg a hűvösebb szintről a melegebb szintre való áttérés a ritka réteg olvasási tranzakciós díját terheli a megosztás összes fájlja esetében.