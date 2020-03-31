---
title: A tárolási tűzfal beállításainak megkerülése
description: A tárfiók hálózati tűzfal-beállítása hibát okozhat, amikor azure Blob storage-tárolót hoz létre az Azure HPC-gyorsítótárban. Ez a cikk aszoftveres javítás ig kerülő megoldást nyújt a korlátozásra.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/7/2019
ms.author: rohogue
ms.openlocfilehash: 6643662d498db8cbcffcb120a9ceabc46cfc04cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74174407"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>A Blob Storage-fiók tűzfalbeállításainak kikerülése

A tárfiók tűzfalaiban használt adott beállítás a Blob storage-tároló létrehozása sikertelenlehet. Az Azure HPC cache csapata dolgozik egy szoftver javítás a probléma, de megoldhatja azt a cikkben található utasításokat követve.

A tűzfalbeállítás, amely csak a "kiválasztott hálózatok" hozzáférését teszi lehetővé, megakadályozhatja, hogy a gyorsítótár blobtároló-tárolót hozzon létre. Ez a konfiguráció a tárfiók **tűzfalak és virtuális hálózatok** beállításai lapon található.

A probléma az, hogy a gyorsítótárszolgáltatás az ügyfélkörnyezetektől különálló rejtett szolgáltatásvirtuális hálózatot használ. Nem lehet explicit módon engedélyezni a hálózatnak a tárfiók elérését.

Blob-tárolócél létrehozásakor a gyorsítótár-szolgáltatás ezt a hálózatot használja annak ellenőrzésére, hogy a tároló üres-e vagy sem. Ha a tűzfal nem engedélyezi a hozzáférést a rejtett hálózatról, az ellenőrzés sikertelen lesz, és a tárolócél létrehozása sikertelen lesz.

A probléma kerülő megoldásaként ideiglenesen módosítsa a tűzfal beállításait a tárolócél létrehozása közben:

1. Nyissa meg a **Tűzfalak és a virtuális hálózatok lapot,** és módosítsa a "Hozzáférés engedélyezése" beállítást a **Minden hálózatra.**
1. Hozza létre a Blob storage-tárolót az Azure HPC-gyorsítótárban.
1. A tárolási cél sikeres létrehozása után módosítsa a fiók tűzfal-beállítását **a Kijelölt hálózatokra.**

Az Azure HPC-gyorsítótár nem használja a szolgáltatás virtuális hálózatot a kész tárolási cél eléréséhez.

A megoldással kapcsolatban a [Microsoft szervizelési és támogatási szolgálatától kell segítséget kérni.](hpc-cache-support-ticket.md)
