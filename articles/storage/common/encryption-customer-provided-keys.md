---
title: Titkosítási kulcs biztosítása a Blob storage-nak küldött kérelemre
titleSuffix: Azure Storage
description: Az Azure Blob storage-ra vonatkozó kéréseket küldő ügyfelek kérésenként (előzetes verzió) biztosíthattak titkosítási kulcsot. A titkosítási kulcs a kérelem részletes vezérlést biztosít a Blob storage-műveletek titkosítási beállításait.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c8a5555c5c33255fdc5902a115e7e9103a4e936f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79410063"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage-preview"></a>Titkosítási kulcs biztosítása a Blob storage kérésére (előzetes verzió)

Az Azure Blob storage-ra vonatkozó kéréseket küldő ügyfelek kérésenként (előzetes verzió) biztosíthattak titkosítási kulcsot. A titkosítási kulcs a kérelem részletes vezérlést biztosít a Blob storage-műveletek titkosítási beállításait. Az ügyfél által biztosított kulcsok tárolhatók az Azure Key Vaultban vagy egy másik kulcstárolóban.

## <a name="encrypting-read-and-write-operations"></a>Olvasási és írási műveletek titkosítása

Amikor egy ügyfélalkalmazás titkosítási kulcsot biztosít a kérelemhez, az Azure Storage transzparens titkosítást és visszafejtést hajt végre a blobadatok olvasása és írása közben. Az Azure Storage a blob tartalma mellett a titkosítási kulcs SHA-256 kivonatát írja. A kivonat annak ellenőrzésére szolgál, hogy a blob minden további művelete ugyanazt a titkosítási kulcsot használja-e.

Az Azure Storage nem tárolja és nem kezeli a titkosítási kulcsot, amelyet az ügyfél küld a kéréssel. A kulcs biztonságosan eldobásra kerül, amint a titkosítási vagy visszafejtési folyamat befejeződött.

Amikor egy ügyfél létrehoz vagy frissít egy blobot egy ügyfél által biztosított kulccsal a kérelemben, majd az adott blob további olvasási és írási kérelmeinek is biztosítaniuk kell a kulcsot. Ha a kulcs nem érhető el egy olyan blobra vonatkozó kérelemben, amely már titkosítva van egy ügyfél által megadott kulccsal, akkor a kérelem a 409-es (Ütközés) hibakóddal sikertelen lesz.

Ha az ügyfélalkalmazás titkosítási kulcsot küld a kérelemre, és a tárfiók is titkosítva van egy Microsoft által felügyelt kulccsal vagy egy ügyfél által felügyelt kulccsal, majd az Azure Storage a titkosítási és visszafejtési kérelemben megadott kulcsot használja.

A titkosítási kulcs küldése a kérelem részeként, az ügyfél létre kell hoznia egy biztonságos kapcsolatot az Azure Storage HTTPS használatával.

Minden blob pillanatkép rendelkezhet saját titkosítási kulccsal.

## <a name="request-headers-for-specifying-customer-provided-keys"></a>Kérelemfejlécek az ügyfél által megadott kulcsok megadásához

REST-hívások esetén az ügyfelek a következő fejlécek segítségével biztonságosan továbbíthatják a titkosítási kulcs adatait a Blob storage-ba irányuló kérésen:

|Kérelem fejléce | Leírás |
|---------------|-------------|
|`x-ms-encryption-key` |Írási és olvasási kérelmekhez egyaránt szükséges. Base64 kódolású AES-256 titkosítási kulcs érték. |
|`x-ms-encryption-key-sha256`| Írási és olvasási kérelmekhez egyaránt szükséges. A titkosítási kulcs Base64 kódolású SHA256.The Base64-coded SHA256 of the encryption key. |
|`x-ms-encryption-algorithm` | Írási kérelmekhez szükséges, olvasási kérelmek hez nem kötelező. Megadja azt az algoritmust, amelyet az adott kulccsal történő adattitkosításkor kell használni. Az AES256-nak kell lennie. |

A titkosítási kulcsok megadása a kérelemben nem kötelező. Ha azonban a fent felsorolt fejlécek egyikét adja meg egy írási művelethez, akkor mindegyiket meg kell adnia.

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Az ügyfél által biztosított kulcsokat támogató blobtárolási műveletek

A következő Blob storage-műveletek támogatják az ügyfél által biztosított titkosítási kulcsok kérésre történő küldését:

- [Put Blob](/rest/api/storageservices/put-blob)
- [Blokklista felrakása](/rest/api/storageservices/put-block-list)
- [Blokk berakása](/rest/api/storageservices/put-block)
- [Blokk felkerülése URL-címből](/rest/api/storageservices/put-block-from-url)
- [Oldal berakása](/rest/api/storageservices/put-page)
- [Oldal feltöltése URL-címből](/rest/api/storageservices/put-page-from-url)
- [Hozzáfűző blokk](/rest/api/storageservices/append-block)
- [Blob tulajdonságainak beállítása](/rest/api/storageservices/set-blob-properties)
- [Blob metaadatainak beállítása](/rest/api/storageservices/set-blob-metadata)
- [Blob beszerezése](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Blob metaadatok bekésezése](/rest/api/storageservices/get-blob-metadata)
- [Snapshot Blob](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>Ügyfél által megadott kulcsok elforgatása

Egy blob titkosításához használt titkosítási kulcs elforgatásához töltse le a blobot, majd töltse fel újra az új titkosítási kulccsal.

> [!IMPORTANT]
> Az Azure Portal nem használható olvasni vagy írni egy tárolóból vagy blobba, amely a kérelemben megadott kulccsal van titkosítva.
>
> Ügyeljen arra, hogy megvédje a titkosítási kulcsot, amely et egy biztonságos kulcstárolóban, például az Azure Key Vaultban lévő Blob storage-hoz intézett kérelem reklamációja alapján biztosít. Ha egy tárolón vagy blobon a titkosítási kulcs nélkül próbál meg írni, a művelet sikertelen lesz, és elveszíti az objektumhoz való hozzáférést.

## <a name="next-steps"></a>További lépések

- [Ügyfél által biztosított kulcs megadása a Blob storage-nak a .NET-tel kapcsolatos kérésre](../blobs/storage-blob-customer-provided-key.md)
- [Az Azure Storage titkosítása az inaktív adatokhoz](storage-service-encryption.md)
