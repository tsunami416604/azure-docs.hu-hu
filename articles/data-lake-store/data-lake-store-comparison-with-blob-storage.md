---
title: Azure Data Lake Storage Gen1 összehasonlítása blob Storage-val
description: A Azure Data Lake Storage Gen1 és az Azure Blob Storage közötti különbségek összegzését tartalmazza.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 3565af1f3e0a002cf3852faef9c2853ff88d46e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82691725"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Azure Data Lake Storage Gen1 és az Azure Blob Storage összehasonlítása

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

A cikkben szereplő táblázat összefoglalja a Azure Data Lake Storage Gen1 és az Azure Blob Storage közötti különbségeket big data feldolgozás néhány kulcsfontosságú aspektusa mellett. Az Azure Blob Storage egy általános célú, skálázható objektum-tároló, amelyet számos tárolási forgatókönyvhöz terveztek. A Azure Data Lake Storage Gen1 big data elemzési számítási feladatokhoz optimalizált, Hyper-Scale adattár.

|  | Azure Data Lake Storage Gen1 | Azure Blob Storage |
| --- | --- | --- |
| Szerep |Optimalizált tároló big data elemzési számítási feladatokhoz |Általános célú objektumok tárolója számos különböző tárolási forgatókönyvhöz, beleértve a big data elemzéseket |
| Használati esetek |Kötegelt, interaktív, adatfolyam-elemzési és gépi tanulási adatokat, például naplófájlokat, IoT adatokat, kattintson a streamek és a nagyméretű adatkészletek elemre. |Bármilyen típusú szöveges vagy bináris adat, például alkalmazás-háttér, biztonsági mentési adat, adathordozó-tároló a folyamatos átvitelhez és az általános célú adatmennyiség. Emellett teljes körű támogatást nyújt az elemzési számítási feladatokhoz; kötegelt, interaktív, adatfolyam-elemzési és gépi tanulási adatokat, például naplófájlokat, IoT adatokat, kattintson a streamek és a nagyméretű adatkészletek elemre. |
| Fő fogalmak |Data Lake Storage Gen1 fiók olyan mappákat tartalmaz, amelyek a fájlban tárolt adatfájlok |A Storage-fiók rendelkezik tárolókkal, amelyek viszont Blobok formájában vannak tárolva. |
| Struktúra |Hierarchikus fájlrendszer |Objektum-tároló sík névtérrel |
| API |REST API HTTPS-kapcsolaton keresztül |REST API HTTP/HTTPS protokollon keresztül |
| Kiszolgálóoldali API |[WebHDFS – kompatibilis REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[Azure Blob Storage REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Hadoop fájlrendszerbeli ügyfél |Igen |Igen |
| Adatműveletek – hitelesítés |[Azure Active Directory identitások](../active-directory/develop/authentication-scenarios.md) alapján |Közös titkokon alapuló [fiók-hozzáférési kulcsok](../storage/common/storage-account-keys-manage.md) és [közös hozzáférésű aláírási kulcsok](../storage/common/storage-dotnet-shared-access-signature-part-1.md). |
| Adatműveletek – hitelesítési protokoll |OAuth 2,0. A hívásoknak tartalmazniuk kell egy Azure Active Directory által kiadott érvényes JWT (JSON Web Token) |Kivonatoló alapú üzenethitelesítő kód (HMAC). A hívásoknak Base64 kódolású SHA-256 kivonatot kell tartalmazniuk a HTTP-kérelem egy részén. |
| Adatműveletek – engedélyezés |POSIX Access Control listák (ACL-ek).  A Azure Active Directory identitások alapján megadott ACL-ek a fájl-és mappa szintjén állíthatók be. |Fiók szintű engedélyezés – [fiók-hozzáférési kulcsok](../storage/common/storage-account-keys-manage.md) használata<br>Fiók, tároló vagy blob engedélyezése esetén – [közös hozzáférésű aláírási kulcsok](../storage/common/storage-dotnet-shared-access-signature-part-1.md) használata |
| Adatműveletek – naplózás |Elérhető. További [információ:.](data-lake-store-diagnostic-logs.md) |Elérhető |
| Inaktív adatok titkosítása |<ul><li>Transzparens, kiszolgálóoldali oldal</li> <ul><li>Szolgáltatás által felügyelt kulcsokkal</li><li>Ügyfél által felügyelt kulcsokkal az Azure kulcstartóban</li></ul></ul> |<ul><li>Transzparens, kiszolgálóoldali oldal</li> <ul><li>Szolgáltatás által felügyelt kulcsokkal</li><li>Ügyfél által felügyelt kulcsokkal az Azure kulcstartóban (előzetes verzió)</li></ul><li>Ügyféloldali titkosítás</li></ul> |
| Felügyeleti műveletek (például fiók létrehozása) |Az Azure által biztosított [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) (RBAC) a fiókok felügyeletéhez |Az Azure által biztosított [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) (RBAC) a fiókok felügyeletéhez |
| Fejlesztői SDK-k |.NET, Java, Python, Node.js |.NET, Java, Python, Node.js, C++, Ruby, PHP, go, Android, iOS |
| Elemzési számítási feladatok teljesítménye |Optimalizált teljesítmény párhuzamos elemzési számítási feladatokhoz. Nagy átviteli sebesség és IOPS. |Optimalizált teljesítmény párhuzamos elemzési számítási feladatokhoz. |
| Méretkorlát |Nincs korlátozás a fiók méretére, a fájlméretre vagy a fájlok számára |A konkrét korlátokat lásd: [a standard Storage-fiókok méretezhetőségi célpontjai](../storage/common/scalability-targets-standard-account.md) , valamint [a blob Storage méretezhetőségi és teljesítményi céljai](../storage/blobs/scalability-targets.md). Nagyobb fiókokra vonatkozó korlátozások érhetők el, ha kapcsolatba lép az [Azure ügyfélszolgálatával](https://azure.microsoft.com/support/faq/) |
| Geo-redundancia |Helyileg redundáns (több példányban, egy Azure-régióban) |Helyileg redundáns (LRS), zóna redundáns (ZRS), globálisan redundáns (GRS), olvasási hozzáférés globális redundáns (RA-GRS). További [információ:](../storage/common/storage-redundancy.md) |
| Szolgáltatás állapota |Általánosan elérhető |Általánosan elérhető |
| Régiónkénti rendelkezésre állás |Lásd [itt](https://azure.microsoft.com/regions/#services) |Minden Azure-régióban elérhető |
| Price |[Díjszabás](https://azure.microsoft.com/pricing/details/data-lake-store/) megtekintése |[Díjszabás](https://azure.microsoft.com/pricing/details/storage/) megtekintése |


