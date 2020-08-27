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
ms.openlocfilehash: cace61739e13514bb4aba1169c51ea670a167c9d
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88944512"
---
A Azure Files négyféle tárterületet kínál, a prémium, a tranzakciós optimalizált, a gyakori és a ritka elérésű csomagokat, hogy lehetővé tegye a megosztások testreszabását a forgatókönyv teljesítményére és árára vonatkozó követelmények szerint:

- **Prémium**: a prémium szintű fájlmegosztást SSD-meghajtók teszik elérhetővé, és a **FileStorage a Storage-fiók** típusában vannak telepítve. A prémium szintű fájlmegosztás konzisztens nagy teljesítményt és kis késleltetést biztosít a legtöbb IO-művelethez, az IO-igényes munkaterhelések esetében. Ez a munkaterhelések, például az adatbázisok, a webhelyek üzemeltetése és a fejlesztői környezetek széles skálájának megfelelővé teszi. 
- **Tranzakció optimalizálva**: a tranzakciós optimalizált fájlmegosztás lehetővé teszi a tranzakciók nagy terhelését, amelyeknek nincs szükségük a prémium szintű fájlmegosztás által kínált késésre. A tranzakciós optimalizált fájlmegosztás a merevlemez-meghajtók (HDD-k) által támogatott standard szintű tárolóeszközön érhető el, és az **általános célú 2-es verziójú (GPv2) Storage-fiók** típusa szerint van üzembe helyezve. Ez a tárolási réteg hagyományosan "standard" néven is ismert, azonban ez a tárolási adathordozó típusra hivatkozik, nem pedig magára a rétegre (a gyakori és a ritka elérésű csomagok is a standard szintűek, mert a szabványos tárolási hardveren vannak).
- **Gyors**: a gyakori fájlmegosztás olyan általános célú fájlmegosztást kínál, mint például a csapat megosztása és a Azure file Sync. A gyors fájlmegosztást a HDD-k által támogatott szabványos tárolóeszközön kínáljuk, és az **általános célú 2-es verziójú (GPv2) Storage-fióktípus** telepítik.
- **Cool**: a lassú fájlmegosztás költséghatékony tárterületet kínál, amely online archiválási tárolási forgatókönyvekhez van optimalizálva. A Azure File Sync is megfelelő lehet az alacsonyabb adatforgalom-számítási feladatokhoz. A lassú fájlmegosztást a HDD-k által támogatott szabványos tárolóeszközön kínáljuk, és az **általános célú 2-es verziójú (GPv2) Storage-fiókba** vannak telepítve.

A prémium fájlmegosztás csak kiépített számlázási modellben érhető el. A prémium szintű fájlmegosztás kiépített számlázási modelljével kapcsolatos további információkért lásd a [prémium szintű fájlmegosztás kiépítés ismertetése](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares)című témakört. A standard fájlmegosztás, beleértve a tranzakcióra optimalizált, a gyakori és a ritka fájlmegosztást, a go-modellben is elérhető fizetéssel.

A gyakori és ritka elérésű fájlmegosztás jelenleg a nyilvános régiók következő részében érhető el (a tranzakciós optimalizált fájlmegosztás az összes Azure-régióban elérhető):

- Ausztrália középső régiója
- Ausztrália 2. középső régiója
- Kelet-Ausztrália
- Délkelet-Ausztrália
- Kelet-Kanada
- Közép-Kanada
- Közép-Franciaország
- Dél-Franciaország
- Észak-Németország (nyilvános)
- Középnyugat-Németország (nyilvános)
- Közép-India
- Dél-India
- Nyugat-India
- Kelet-Japán
- Nyugat-Japán
- Dél-Korea középső régiója
- Dél-Korea déli régiója
- Kelet-Norvégia
- Norvégia nyugati régiója
- Dél-Afrika északi régiója
- Dél-Afrika nyugati régiója
- Észak-Svájc
- Nyugat-Svájc
- UAE középső régiója
- Észak-Egyesült Arab
- Az Egyesült Királyság déli régiója
- Az Egyesült Királyság nyugati régiója

A gyakori vagy ritka elérésű fájlmegosztás üzembe helyezésével kapcsolatban lásd: [gyors vagy lassú fájlmegosztás létrehozása](../articles/storage/files/storage-how-to-create-file-share.md#create-a-hot-or-cool-file-share). 