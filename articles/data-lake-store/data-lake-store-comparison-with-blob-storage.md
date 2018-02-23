---
title: "Azure Storage-Blobból az Azure Data Lake Store összehasonlítása |} Microsoft Docs"
description: "Azure Storage-Blobból az Azure Data Lake Store összehasonlítása"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: b199525b-84de-4f79-9eb6-69a613b8b217
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: a8831278ef0e83571bf05d48e5d671f8dee92850
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="comparing-azure-data-lake-store-and-azure-blob-storage"></a>Az Azure Data Lake Store és az Azure Blob Storage összehasonlítása
Ebben a cikkben a táblázat összefoglalja, az Azure Data Lake Store és az Azure Blob Storage néhány kulcsfontosságú elemeit, az adatfeldolgozás nagy mentén. Az Azure Blob Storage egy általános célú, méretezhető objektum tároló, amely a tárolási forgatókönyvek széles szolgál. Azure Data Lake Store tárháza kapacitású, amely a big data elemzés munkaterhelések van optimalizálva.

|  | Azure Data Lake Store | Azure Blob Storage |
| --- | --- | --- |
| Cél |Optimalizált tárolási megoldás big data elemzés munkaterhelések |Általános célú objektum store számos különböző tárolási forgatókönyvek, beleértve a big data-elemzések |
| Használati esetek |Kötegelt, interaktív, streaming analytics és a machine learning adatok például a naplófájlok, IoT-adatokat, kattintson az adatfolyamokat, nagy adatkészletek |Szöveges vagy bináris adatok, például az alkalmazás bármilyen típusú biztonsági end, a biztonsági mentési adatokat, a adatfolyam-továbbításhoz és általános célú adatok tárolása adathordozó. Emellett teljes analytics munkaterhelések; támogatása kötegelt, interaktív, streaming analytics és a machine learning adatok például a naplófájlok, IoT-adatokat, kattintson az adatfolyamokat, nagy adatkészletek |
| Fő fogalmak |Data Lake Store-fiók mappát tartalmaz, pedig tartalmazó fájlként tárolt adatok |A tárfiók rendelkezik tárolók, melynek cserébe megvan az adatok BLOB formájában |
| struktúra |Hierarchikus fájlrendszer |Egyszerű névtér objektum tároló |
| API |REST API HTTPS-KAPCSOLATON keresztül |REST API HTTP/HTTPS-KAPCSOLATON keresztül |
| Server-side API |[WebHDFS-kompatibilis REST API-n](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[Azure Blob Storage REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Hadoop-fájl rendszer ügyfél |Igen |Igen |
| Adatok műveletek - hitelesítés |Alapján [az Azure Active Directory-identitás](../active-directory/active-directory-authentication-scenarios.md) |A közös titkokat - alapján [Tárelérési kulcsok](../storage/common/storage-create-storage-account.md#manage-your-storage-account) és [megosztott hozzáférési aláírást kulcsok](../storage/common/storage-dotnet-shared-access-signature-part-1.md). |
| Adatok műveletek - hitelesítési protokoll |OAuth 2.0-s. Hívások tartalmaznia kell egy érvényes jwt-t (JSON Web Token) Azure Active Directory által kiadott |Kivonat-alapú üzenethitelesítési kóddal (HMAC). Hívások tartalmaznia kell egy Base64-kódolású SHA-256 kivonatoló a HTTP-kérelmek egy része felett. |
| Adatok műveletek - engedélyezés |POSIX hozzáférés-vezérlési listák (ACL).  Fájl- és szintű Azure Active Directory identitások alapuló hozzáférés-vezérlési listák állítható be. |A fiók szintű engedélyezési – használjon [Tárelérési kulcsok](../storage/common/storage-create-storage-account.md#manage-your-storage-account)<br>A fiók, a tároló vagy a blob engedélyezési - használjon [megosztott hozzáférési aláírási kulcsok](../storage/common/storage-dotnet-shared-access-signature-part-1.md) |
| Adatok műveletek - naplózás |Érhető el. Lásd: [Itt](data-lake-store-diagnostic-logs.md) információt. |Elérhető |
| Inaktív adatok titkosítása |<ul><li>Átlátszó, kiszolgálóoldali</li> <ul><li>A szolgáltatás által kezelt kulcsok</li><li>Ügyfél által felügyelt Azure KeyVault kulcsokkal rendelkező</li></ul></ul> |<ul><li>Átlátszó, kiszolgálóoldali</li> <ul><li>A szolgáltatás által kezelt kulcsok</li><li>A kulcsok ügyfél által felügyelt Azure KeyVault (előzetes verzió)</li></ul><li>Ügyféloldali titkosítás</li></ul> |
| Felügyeleti műveletek (pl. fiók létrehozása) |[Szerepköralapú hozzáférés-vezérlés](../active-directory/role-based-access-control-what-is.md) felhasználóifiók-kezelés az Azure által biztosított (RBAC) |[Szerepköralapú hozzáférés-vezérlés](../active-directory/role-based-access-control-what-is.md) felhasználóifiók-kezelés az Azure által biztosított (RBAC) |
| Fejlesztői SDK-k |.NET, Java, Python, Node.js |.NET, Java, Python, Node.js, C++, Ruby, PHP, nyissa meg, Android, iOS |
| Elemzés számítási feladat teljesítményére |A párhuzamos analytics munkaterhelések optimalizált teljesítményhez. Magas teljesítmény és iops-érték. |A párhuzamos analytics munkaterhelések optimalizált teljesítményhez. |
| Méretkorlát |Nem határoz meg a fiókok méretének, a fájl méretét vagy a fájlok száma |Egyes korlátok dokumentált [Itt](../storage/common/storage-scalability-targets.md). Nagyobb fiók korlátozza lépjen kapcsolatba az [Azure-támogatás](https://azure.microsoft.com/support/faq/) |
| Georedundancia |Helyileg redundáns (adatok többszörös lemásolását, egy Azure-régióban) |Helyileg redundáns (LRS), a redundáns (ZRS), globálisan redundáns (GRS), írásvédett globálisan redundáns (RA-GRS) zónát. Lásd: [Itt](../storage/common/storage-redundancy.md) további információ |
| Szolgáltatás állapota |Általánosan elérhető |Általánosan elérhető |
| Régiónkénti rendelkezésre állás |Lásd: [Itt](https://azure.microsoft.com/regions/#services) |Minden Azure-régió érhető el |
| Ár |Lásd: [díjszabása](https://azure.microsoft.com/pricing/details/data-lake-store/) |Lásd: [díjszabása](https://azure.microsoft.com/pricing/details/storage/) |

### <a name="next-steps"></a>További lépések
* [Az Azure Data Lake Store áttekintése](data-lake-store-overview.md)
* [A Data Lake Store első lépései](data-lake-store-get-started-portal.md)

