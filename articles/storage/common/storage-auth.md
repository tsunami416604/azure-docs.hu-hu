---
title: Adatműveletek engedélyezése
titleSuffix: Azure Storage
description: Ismerje meg az Azure Storage-hoz való hozzáférés engedélyezésének különböző módjait, beleértve az Azure Active Directoryt, a megosztott kulcs engedélyezést vagy a megosztott hozzáférésű aláírásokat (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b0f2ad7566d0204871a9c6441315d6201662d92b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616294"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Adatokhoz való hozzáférés engedélyezése az Azure Storage-ban

Minden alkalommal, amikor a tárfiókban lévő adatokhoz fér hozzá, az ügyfél HTTP/HTTPS-en keresztül kér kérelmet az Azure Storage-ba. Minden, biztonságos erőforráshoz érkező kérést engedélyezni kell, a szolgáltatás ugyanis így győződik meg arról, hogy az ügyfél rendelkezik az adatok hozzáféréséhez szükséges engedélyekkel.

Az alábbi táblázat azokat a beállításokat ismerteti, amelyeket az Azure Storage kínál az erőforrásokhoz való hozzáférés engedélyezéséhez:

|  |Megosztott kulcs (tárfiók kulcsa)  |Közös hozzáférésű jogosultságkód (SAS)  |Azure Active Directory (Azure AD)  |Active Directory (előzetes verzió) |Névtelen nyilvános olvasási hozzáférés  |
|---------|---------|---------|---------|---------|---------|
|Azure Blobs     |[Támogatott](/rest/api/storageservices/authorize-with-shared-key/)         |[Támogatott](storage-sas-overview.md)         |[Támogatott](storage-auth-aad.md)         |Nem támogatott|[Támogatott](../blobs/storage-manage-access-to-resources.md)         |
|Azure-fájlok (SMB)     |[Támogatott](/rest/api/storageservices/authorize-with-shared-key/)         |Nem támogatott         |[Támogatott, csak AAD tartományi szolgáltatásokkal](../files/storage-files-active-directory-overview.md)         |[Támogatott, a hitelesítő adatokat szinkronizálni kell az Azure AD-vel](../files/storage-files-active-directory-overview.md)|Nem támogatott         |
|Azure-fájlok (REST)     |[Támogatott](/rest/api/storageservices/authorize-with-shared-key/)         |[Támogatott](storage-sas-overview.md)         |Nem támogatott         |Nem támogatott |Nem támogatott         |
|Azure Queues     |[Támogatott](/rest/api/storageservices/authorize-with-shared-key/)         |[Támogatott](storage-sas-overview.md)         |[Támogatott](storage-auth-aad.md)         |Nem támogatott | Nem támogatott         |
|Azure-táblák     |[Támogatott](/rest/api/storageservices/authorize-with-shared-key/)         |[Támogatott](storage-sas-overview.md)         |Nem támogatott         |Nem támogatott| Nem támogatott         |

Az egyes engedélyezési lehetőségeket röviden ismertetjük az alábbiakban:

- **Az Azure Active Directory (Azure AD) integrációja** blobok és várólisták. Az Azure AD szerepköralapú hozzáférés-vezérlést (RBAC) biztosít az ügyfél egy tárfiókban lévő erőforrásokhoz való hozzáférésének részletes szabályozásához. A blobok és várólisták Azure AD-integrációjáról az [Azure-blobokhoz és várólistákhoz való hozzáférés engedélyezése az Azure Active Directory használatával című](storage-auth-aad.md)témakörben talál további információt.

- **Azure Active Directory tartományi szolgáltatások (Azure AD DS) hitelesítés** az Azure Files. Az Azure Files támogatja az identitásalapú engedélyezést a kiszolgálói üzenetblokkon (SMB) az Azure AD DS-en keresztül. Az RBAC segítségével részletesen szabályozhatja az ügyfél azure-fájlok hoz való hozzáférését egy tárfiókban. A tartományi szolgáltatások használatával történő Azure Files-hitelesítéssel kapcsolatos további információkért tekintse meg [az áttekintést.](../files/storage-files-active-directory-overview.md)

- **Active Directory (AD) hitelesítés (előzetes verzió)** az Azure Files. Az Azure Files támogatja az identitásalapú engedélyezést az SMB-n keresztül az AD-n keresztül. Az AD tartományi szolgáltatás üzemeltethető a helyszíni gépeken vagy az Azure-beli virtuális gépeken. SMB-hozzáférés a fájlokhoz a tartományhoz csatlakozó gépek AD-hitelesítő adataival támogatott, akár a helyszínen, akár az Azure-ban. Az RBAC segítségével megosztásszintű hozzáférés-vezérléshez és NTFS dakta-khoz használható a címtár-/fájlszintű engedély-kényszerítéshez. A tartományi szolgáltatások használatával történő Azure Files-hitelesítéssel kapcsolatos további információkért tekintse meg [az áttekintést.](../files/storage-files-active-directory-overview.md)

- **Megosztott kulcsengedélyezések** blobokhoz, fájlokhoz, várólistákhoz és táblákhoz. A megosztott kulcsot használó ügyfél fejlécet ad át minden olyan kéréssel, amely a tárfiók hozzáférési kulcsával van aláírva. További információt a [Megosztáskulcs engedélyezése című témakörben talál.](/rest/api/storageservices/authorize-with-shared-key/)
- **Megosztott hozzáférési aláírások** blobokhoz, fájlokhoz, várólistákhoz és táblákhoz. A megosztott hozzáférésű aláírások (SAS) korlátozott delegált hozzáférést biztosítanak a tárfiók ban lévő erőforrásokhoz. Az aláírás érvényességi idejének vagy az általa biztosított engedélyeknek a hozzákötési időkorláthozzáadása rugalmasságot biztosít a hozzáférés kezeléséhez. További információ: [A megosztott hozzáférésű aláírások (SAS) használata.](storage-sas-overview.md)
- **Névtelen nyilvános olvasási hozzáférés** a tárolók hoz és blobokhoz. Nincs szükség engedélyezésre. További információt a [Névtelen olvasási hozzáférés kezelése a tárolókhoz és blobokhoz című témakörben talál.](../blobs/storage-manage-access-to-resources.md)  

Alapértelmezés szerint az Azure Storage összes erőforrása biztonságos, és csak a fiók tulajdonosa számára érhető el. Bár a fent ismertetett engedélyezési stratégiák bármelyikével hozzáférést biztosíthat az ügyfeleknek a tárfiókban lévő erőforrásokhoz, a Microsoft azt javasolja, hogy lehetőség szerint használja az Azure AD-t a maximális biztonság és a könnyű használat érdekében.

## <a name="next-steps"></a>További lépések

- [Hozzáférés engedélyezése az Azure-blobokhoz és várólistákhoz az Azure Active Directory használatával](storage-auth-aad.md)
- [Engedélyezés megosztott kulccsal](/rest/api/storageservices/authorize-with-shared-key/)
- [Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz megosztott hozzáférésű aláírások (SAS) használatával](storage-sas-overview.md)
