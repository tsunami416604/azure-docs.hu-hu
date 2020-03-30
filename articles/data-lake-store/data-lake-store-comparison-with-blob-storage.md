---
title: Az Azure Data Lake Storage Gen1 összehasonlítása az Azure Storage Blobgal | Microsoft dokumentumok
description: Az Azure Data Lake Storage Gen1 összehasonlítása az Azure Storage Blobmal
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: b199525b-84de-4f79-9eb6-69a613b8b217
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 7c958c3ed4d6ddaabd87f053005fcfc1eba8c842
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438723"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Az Azure Data Lake Storage Gen1 és az Azure Blob Storage összehasonlítása

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

Ebben a cikkben található táblázat összefoglalja az Azure Data Lake Storage Gen1 és az Azure Blob Storage közötti különbségeket a big data-feldolgozás néhány kulcsfontosságú szempontja mellett. Az Azure Blob Storage egy általános célú, méretezhető objektumtároló, amely a legkülönbözőbb tárolási forgatókönyvek. Az Azure Data Lake Storage Gen1 egy nagy méretű tárház, amely big data-elemzési számítási feladatokra van optimalizálva.

|  | 1. generációs Azure Data Lake Storage | Azure Blob Storage |
| --- | --- | --- |
| Cél |Optimalizált tárhely big data-elemzési számítási feladatokhoz |Általános célú objektumtárolás a legkülönbözőbb tárolási forgatókönyvekhez, beleértve a big data-elemzéseket is |
| Használati esetek |Kötegelt, interaktív, streamelési elemzési és gépi tanulási adatok, például naplófájlok, IoT-adatok, kattintási adatfolyamok, nagy adatkészletek |Bármilyen típusú szöveg vagy bináris adat, például alkalmazás háttérrendszer, biztonsági mentési adatok, adatfolyam-tárolás streameléshez és általános célú adatok. Emellett teljes mértékben támogatja az elemzési számítási feladatokat; kötegelt, interaktív, streamelési elemzési és gépi tanulási adatok, például naplófájlok, IoT-adatok, kattintási adatfolyamok, nagy adatkészletek |
| Fő fogalmak |A Data Lake Storage Gen1 fiók mappákat tartalmaz, amelyek viszont fájlokként tárolt adatokat tartalmaznak |A tárfiók tárolókkal rendelkezik, amelyek viszont blobok formájában tartalmaznak adatokat |
| Struktúra |Hierarchikus fájlrendszer |Objektumtároló sima névtérrel |
| API |REST API HTTPS-en keresztül |REST API HTTP/HTTPS protokollon keresztül |
| Kiszolgálóoldali API |[WebHDFS-kompatibilis REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[Az Azure Blob Storage REST API-ja](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Hadoop fájlrendszer-ügyfél |Igen |Igen |
| Adatműveletek - Hitelesítés |Az [Azure Active Directory-identitások](../active-directory/develop/authentication-scenarios.md) alapján |Közös titkok – [Fiókhozzáférési kulcsok](../storage/common/storage-account-keys-manage.md) és [megosztott hozzáférésű aláírási kulcsok](../storage/common/storage-dotnet-shared-access-signature-part-1.md)alapján. |
| Adatműveletek – Hitelesítési protokoll |OAuth 2.0. A hívásoknak tartalmazniuk kell az Azure Active Directory által kiadott érvényes JWT (JSON Web Token) |Kivonat-alapú üzenethitelesítési kód (HMAC). A hívásoknak base64-kódolású SHA-256 kivonatot kell tartalmazniuk a HTTP-kérelem egy része felett. |
| Adatműveletek - engedélyezés |POSIX hozzáférés-vezérlési listák (ACL-ok).  Az Azure Active Directory-identitásokon alapuló ACL-ek fájl- és mappaszinten állíthatók be. |Fiókszintű engedélyezéshez – [Fiókhozzáférési kulcsok](../storage/common/storage-account-keys-manage.md) használata<br>Fiók- vagy tároló- vagy blobengedélyezéshez – [Megosztott hozzáférésű aláírási kulcsok](../storage/common/storage-dotnet-shared-access-signature-part-1.md) használata |
| Adatműveletek - Naplózás |Elérhető. Lásd [itt](data-lake-store-diagnostic-logs.md) az információkat. |Elérhető |
| Inaktív titkosítási adatok |<ul><li>Átlátszó, kiszolgálói oldal</li> <ul><li>Szolgáltatás által felügyelt kulcsokkal</li><li>Ügyfél által felügyelt kulcsokkal az Azure KeyVault-ban</li></ul></ul> |<ul><li>Átlátszó, kiszolgálói oldal</li> <ul><li>Szolgáltatás által felügyelt kulcsokkal</li><li>Ügyfél által felügyelt kulcsokkal az Azure KeyVaultban (előzetes verzió)</li></ul><li>Ügyféloldali titkosítás</li></ul> |
| Kezelési műveletek (pl. fiók létrehozása) |Az Azure által [fiókkezeléshez biztosított szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) (RBAC) |Az Azure által [fiókkezeléshez biztosított szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) (RBAC) |
| Fejlesztői SDK-k |.NET, Java, Python, Node.js |.Net, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS |
| Analytics számítási feladatok teljesítménye |Optimalizált teljesítmény párhuzamos elemzési számítási feladatokhoz. Nagy átviteli sebességű és IOPS. |Optimalizált teljesítmény párhuzamos elemzési számítási feladatokhoz. |
| Méretkorlátok |Nincs korlátozva a fiókméret, a fájlméret vagy a fájlok száma |A konkrét korlátokat lásd: [Méretezhetőségi célok a szabványos tárfiókok](../storage/common/scalability-targets-standard-account.md) és [a Méretezhetőség és a blob storage teljesítménycélok.](../storage/blobs/scalability-targets.md) Nagyobb fiókkorlátok érhetők el az [Azure-támogatással](https://azure.microsoft.com/support/faq/) való kapcsolatfelvételkor |
| Georedundancia |Helyileg redundáns (több adatmásolat egy Azure-régióban) |Helyileg redundáns (LRS), zónaredundáns (ZRS), globálisan redundáns (GRS), olvasási hozzáférés globálisan redundáns (RA-GRS). További [here](../storage/common/storage-redundancy.md) információ itt |
| Szolgáltatás állapota |Általánosan elérhető |Általánosan elérhető |
| Régiónkénti rendelkezésre állás |Lásd [itt](https://azure.microsoft.com/regions/#services) |Minden Azure-régióban elérhető |
| Price |Lásd: [Árképzés](https://azure.microsoft.com/pricing/details/data-lake-store/) |Lásd: [Árképzés](https://azure.microsoft.com/pricing/details/storage/) |


