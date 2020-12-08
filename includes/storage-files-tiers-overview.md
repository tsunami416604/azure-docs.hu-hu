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
ms.openlocfilehash: 741ace82a5ec0b44215e4a2df6c64f5b92e0c275
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2020
ms.locfileid: "96842707"
---
A Azure Files négyféle tárterületet kínál, a prémium, a tranzakciós optimalizált, a gyakori és a ritka elérésű csomagokat, hogy lehetővé tegye a megosztások testreszabását a forgatókönyv teljesítményére és árára vonatkozó követelmények szerint:

- **Prémium**: a prémium szintű fájlmegosztást SSD-meghajtók biztosítják, és konzisztens, nagy teljesítményű és kis késleltetésű, egyszámjegyű ezredmásodperceket biztosítanak a legtöbb IO-művelethez az IO-igényes munkaterhelések esetében. A prémium szintű fájlmegosztás a munkaterhelések, például az adatbázisok, a webhelyek üzemeltetése és a fejlesztői környezetek széles skálájának megfelelő. A prémium fájlmegosztás a Server Message Block (SMB) és a Network File System (NFS) protokollokkal is használható.
- **Tranzakció optimalizálva**: a tranzakciós optimalizált fájlmegosztás lehetővé teszi a tranzakciók nagy terhelését, amelyeknek nincs szükségük a prémium szintű fájlmegosztás által kínált késésre. A tranzakciós optimalizált fájlmegosztás a merevlemez-meghajtók (HDD-k) által támogatott szabványos tároló hardveren érhető el. Az optimalizált tranzakció "standard" néven szerepel, azonban ez a tárolási adathordozó típusra hivatkozik, és nem maga a réteg (a gyakori és a ritka elérési szint is a "standard", mivel ezek a szabványos tárolási hardveren találhatók).
- **Gyors**: a gyakori fájlmegosztás olyan általános célú fájlmegosztást kínál, mint például a csapat megosztása és a Azure file Sync. A gyors fájlmegosztást a HDD-k által támogatott szabványos tároló hardveren kínáljuk.
- **Cool**: a lassú fájlmegosztás költséghatékony tárterületet kínál, amely online archiválási tárolási forgatókönyvekhez van optimalizálva. A Azure File Sync is megfelelő lehet az alacsonyabb adatforgalom-számítási feladatokhoz. A lassú fájlmegosztást a HDD-k által támogatott szabványos tárolóeszközön kínáljuk.

A prémium szintű fájlmegosztás üzembe helyezése a **FileStorage-fiókban** történik, és csak kiépített számlázási modellben érhetők el. A prémium szintű fájlmegosztás kiépített számlázási modelljével kapcsolatos további információkért lásd a [prémium szintű fájlmegosztás kiépítés ismertetése](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares)című témakört. A standard fájlmegosztás, beleértve a tranzakcióra optimalizált, a gyakori és a ritka fájlmegosztást, üzembe kerül az **általános célú 2-es verziójú (GPv2) Storage-fiókban** , és a számlázás után is elérhető. A gyakori és ritka elérésű fájlmegosztás az összes Azure nyilvános és Azure Government régióban elérhető. A tranzakciós optimalizált fájlmegosztás minden Azure-régióban elérhető, beleértve az Azure China-t és az Azure Germany-régiókat.

A számítási feladatok tárolási szintjeinek kiválasztásakor vegye figyelembe a teljesítményre és a használatra vonatkozó követelményeket. Ha a számítási feladatok esetében egyszámjegyű késésre van szükség, vagy ha a helyszínen SSD-tárolóeszközt használ, a prémium szint valószínűleg a legmegfelelőbb. Ha az alacsony késés nem annyira fontos, mint például az Azure-ból a helyszínen vagy a helyszínen gyorsítótárazott csoportos megosztások a Azure File Sync használatával, a standard szintű tárterület jobban illeszkedik a költségmegtakarításhoz.

Az optimalizált, a gyakori és a ritka elérésű csomagokat a rendszer pontosan ugyanazon a standard szintű tároló hardveren tárolja. Ebben a három szinten a fő különbség az, hogy az adatok a REST-alapú tárolás árai, amelyek alacsonyabbak a hűvösebb szinteken, és a tranzakciós árak, amelyek magasabbak a hűvösebb szinteken. Ez a következőkkel jár:

- A tranzakció optimalizált, ahogy a név is jelenti, optimalizálja a nagy tranzakciós számítási feladatok árát. Az optimalizált tranzakció a legmagasabb adattárolási ár, de a legalacsonyabb tranzakciós árak.
- A gyakran használt olyan aktív munkaterhelések esetében, amelyek nem tartalmaznak nagy számú tranzakciót, és valamivel alacsonyabb adattárolási díjszabással, de valamivel magasabb tranzakciós árral rendelkeznek, mint az optimalizált tranzakciók.
- A ritkán használt munkaterhelések ára optimalizálja a nagy mennyiségű tevékenységgel nem rendelkező számítási feladatok árát, a legalacsonyabb adattárolási költséget, de a legmagasabb tranzakciós árakat.

A számítási feladatok és a tevékenységek szintje határozza meg a normál fájlmegosztás legköltséghatékonyabb szintjét. A [Azure Files díjszabási oldalán](https://azure.microsoft.com/pricing/details/storage/files/)megtekintheti a kívánt régió, a redundancia és a pénznem pontos árát.

Miután létrehozott egy fájlmegosztást egy Storage-fiókban, nem helyezheti át a rétegekbe kizárólag a különböző Storage-fiókokra. Ha például egy tranzakció optimalizált fájlmegosztást a prémium szintre szeretné áthelyezni, létre kell hoznia egy új fájlmegosztást egy FileStorage-fiókban, és az eredeti megosztás adatait át kell másolnia egy új fájlmegosztásba a FileStorage-fiókban. Azt javasoljuk, hogy a AzCopy használatával másolja az Azure-fájlmegosztás közötti Adatmásolást, de használhat olyan eszközöket is, mint például `robocopy` a Windows, a `rsync` MacOS és a Linux. 

A GPv2 Storage-fiókokban üzembe helyezett fájlmegosztás a standard szint (a tranzakció optimalizált, a gyors és a lassú) között helyezhető át, új Storage-fiók létrehozása és az adatok áttelepítése nélkül, de a csomag módosításakor a tranzakciós költségek is felmerülnek. Ha a megosztást egy melegebb szintről egy hűvösebb szintre helyezi át, akkor a rendszer a megosztás összes fájljának esetében a hűvösebb szint írási tranzakciós díját fogja felszámítani. Ha a fájlmegosztást egy hűvösebb rétegből a melegebb szintre helyezi át, a rendszer a megosztás összes fájljának olvasási tranzakciós díját fogja viselni.