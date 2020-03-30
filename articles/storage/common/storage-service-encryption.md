---
title: Az Azure Storage titkosítása az inaktív adatokhoz
description: Az Azure Storage úgy védi az adatokat, hogy automatikusan titkosítja azokat, mielőtt a felhőben maradna. A tárfiókban lévő adatok titkosításához a Microsoft által felügyelt kulcsokra támaszkodhat, vagy saját kulcsaival kezelheti a titkosítást.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f8f6f40f8ce8297b3cbfe6b3afcbf10df4db6572
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409830"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Az Azure Storage titkosítása az inaktív adatokhoz

Az Azure Storage automatikusan titkosítja az adatokat, ha a felhőben megőrzött. Az Azure Storage titkosítása védi az adatokat, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

## <a name="about-azure-storage-encryption"></a>Az Azure Storage titkosítása

Az Azure Storage-ban tárolt adatok titkosítása és visszafejtése transzparens módon történik 256 bites [AES titkosítással,](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)amely az egyik legerősebb elérhető blokktitkosítás, és fips 140-2-kompatibilis. Az Azure Storage titkosítása hasonló a Windows BitLocker titkosításához.

Az Azure Storage-titkosítás engedélyezve van az összes tárfiókhoz, beleértve az Erőforrás-kezelőt és a klasszikus tárfiókokat is. Az Azure Storage titkosítása nem tiltható le. Mivel az adatok alapértelmezés szerint védettek, nem kell módosítania a kódot vagy az alkalmazásokat az Azure Storage titkosításának kihasználásához.

A tárfiókban lévő adatok a teljesítményszinttől (standard vagy prémium szintű), a hozzáférési szinttől (gyakori vagy ritka elérésű) vagy a központi telepítési modelltől (Azure Resource Manager vagy klasszikus) függetlenül titkosítva vannak. Az archív szint összes blobja is titkosítva van. Az Azure Storage összes redundancia-beállítástámogatja a titkosítást, és az elsődleges és a másodlagos régiókban lévő összes adat titkosítva van, ha a georeplikáció engedélyezve van. Az Azure Storage összes erőforrása titkosítva van, beleértve a blobokat, lemezeket, fájlokat, várólistákat és táblákat. Az összes objektum metaadata is titkosítva van. Az Azure Storage-titkosításért nincs további költség.

2017. október 20-a után az Azure Storage-ba írt minden blokkblob, hozzáfűző blob vagy lapblob titkosítva van. Az ezt a dátum előtt létrehozott blobokat továbbra is egy háttérfolyamat titkosítja. 2017. október 20-a előtt létrehozott blob titkosításának kényszerítéséhez átírhatja a blobot. A blob okainak ellenőrzéséről a [Blob titkosítási állapotának ellenőrzése című](../blobs/storage-blob-encryption-status.md)témakörben olvashat.

Az Azure Storage titkosításalapjául szolgáló kriptográfiai modulokról a [Kriptográfiai API: Következő generáció című témakörben talál](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)további információt.

## <a name="about-encryption-key-management"></a>Titkosítási kulcs kezelése –

Alapértelmezés szerint a tárfiókban lévő adatok microsoftáltal kezelt kulccsal vannak titkosítva. Az adatok titkosításához a Microsoft által felügyelt kulcsokra támaszkodhat, vagy saját kulcsokkal kezelheti a titkosítást. Ha úgy dönt, hogy a titkosítást saját kulcsokkal kezeli, két lehetősége van:

- Megadhat egy *ügyfél által felügyelt kulcsot* az Azure Key Vault használatával a Blob storage-ban és az Azure Files-ban lévő adatok titkosításához és visszafejtéséhez. <sup>1,2</sup> Az ügyfelek által felügyelt kulcsokról az [Azure Storage-titkosítás kezeléséhez az Ügyfél által felügyelt kulcsok használata az Azure Key Vault használatával című](encryption-customer-managed-keys.md)témakörben talál további információt.
- Megadhatja *az ügyfél által megadott kulcsot* a Blob storage-műveletekhez. A Blob storage-tároló ellen olvasási vagy írási kérelmet bekérő ügyfél titkosítási kulcsot tartalmazhat a blobadatok titkosításának és visszafejtésének részletes szabályozására vonatkozó kérelemhez. Az ügyfél által biztosított kulcsokról további információt a [Titkosítási kulcs kérésekor a Blob storage (előzetes verzió) című témakörben talál.](encryption-customer-provided-keys.md)

Az alábbi táblázat összehasonlítja az Azure Storage-titkosítás kulcskezelési beállításait.

|                                        |    Microsoft által felügyelt kulcsok                             |    Felhasználó által kezelt kulcsok                                                                                                                        |    Az ügyfél által megadott kulcsok                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Titkosítási/visszafejtési műveletek    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Támogatott Azure Storage-szolgáltatások    |    Összes                                                |    Blob storage, Azure Files<sup>1,2</sup>                                                                                                               |    Blob Storage                                                                  |
|    Kulcstárolás                         |    Microsoft kulcstároló    |    Azure Key Vault                                                                                                                              |    Az Azure Key Vault vagy bármely más kulcstároló                                                                 |
|    A legfontosabb rotációs felelősség         |    Microsoft                                          |    Ügyfél                                                                                                                                     |    Ügyfél                                                                      |
|    Kulcs használat                           |    Microsoft                                          |    Azure portal, Storage Resource Provider REST API, Azure Storage felügyeleti kódtárak, PowerShell, CLI        |    Azure Storage REST API (Blob Storage), Azure Storage-ügyféltárak    |
|    Kulcshozzáférés                          |    Csak Microsoft                                     |    Microsoft, Ügyfél                                                                                                                    |    Csak vevő                                                                 |

<sup>1</sup> Az ügyfél által felügyelt kulcsok várólistával történő használatát támogató fiók létrehozásáról a [Várólisták ügyféláltal kezelt kulcsait támogató fiók létrehozása című](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)témakörben talál további információt.<br />
<sup>2</sup> Az ügyfél által felügyelt kulcsok táblatárolóval való használatát támogató fiók létrehozásáról a [Táblák ügyféláltal kezelt kulcsait támogató fiók létrehozása című](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)témakörben talál további információt.

## <a name="next-steps"></a>További lépések

- [Mi az Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Felhasználó által kezelt kulcsok konfigurálása az Azure Storage titkosításához az Azure Portalon](storage-encryption-keys-portal.md)
- [Felhasználó által kezelt kulcsok konfigurálása az Azure Storage titkosításához a PowerShellből](storage-encryption-keys-powershell.md)
- [Felhasználó által kezelt kulcsok konfigurálása az Azure Storage titkosításához az Azure CLI-ből](storage-encryption-keys-cli.md)
