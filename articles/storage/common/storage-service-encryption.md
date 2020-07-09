---
title: Inaktív adatok Azure Storage-titkosítása
description: Az Azure Storage védi az adatait úgy, hogy automatikusan titkosítja azt, mielőtt megőrzi a felhőben. A Microsoft által felügyelt kulcsokat használhatja a Storage-fiókban lévő adatok titkosításához, vagy a titkosítást a saját kulcsaival is kezelheti.
services: storage
author: tamram
ms.service: storage
ms.date: 06/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 8b4236e40e8dfbe6ce67bca007be0b6737a6e0c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84945579"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Inaktív adatok Azure Storage-titkosítása

Az Azure Storage automatikusan titkosítja az adatait, amikor azt megőrzi a felhőben. Az Azure Storage-titkosítás védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

## <a name="about-azure-storage-encryption"></a>Tudnivalók az Azure Storage-titkosításról

Az Azure Storage-ban tárolt adatai a 256 bites [AES-titkosítással](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)vannak titkosítva és visszafejtve, és az egyik legerősebb blokk titkosítási algoritmus, amely az FIPS 140-2-kompatibilis. Az Azure Storage-titkosítás hasonló a Windows rendszeren a BitLocker-titkosításhoz.

Az Azure Storage-titkosítás minden Storage-fiók esetében engedélyezve van, beleértve a Resource Manager és a klasszikus Storage-fiókokat is. Az Azure Storage-titkosítás nem tiltható le. Mivel az adatai alapértelmezés szerint biztonságosak, nem kell módosítania a kódot vagy az alkalmazásokat, hogy kihasználhassa az Azure Storage-titkosítás előnyeit.

A Storage-fiókban lévő adatok titkosítása a teljesítmény szintjétől (standard vagy prémium), a hozzáférési szinttől (gyakori vagy ritka elérésű) vagy a telepítési modelltől (Azure Resource Manager vagy klasszikus) függetlenül történik. Az archív szint összes blobja is titkosítva van. Az összes Azure Storage redundancia-beállítás támogatja a titkosítást, és az elsődleges és a másodlagos régióban lévő összes érték titkosítva van, ha a Geo-replikáció engedélyezve van. Minden Azure Storage-erőforrás titkosítva van, beleértve a blobokat, a lemezeket, a fájlokat, a várólistákat és a táblákat. Az összes objektum metaadatai is titkosítva vannak. Az Azure Storage-titkosításhoz nem jár további díj.

Az Azure Storage-ba az 2017. október 20. után írt összes blokk blob, blob hozzáfűzése vagy Page blob titkosítva van. Az ezen dátum előtt létrehozott blobokat a háttérben futó folyamat továbbra is titkosítja. A 2017. október 20. előtt létrehozott blob titkosításának kényszerítéséhez újra lehet írni a blobot. A Blobok titkosítási állapotának vizsgálatával kapcsolatban lásd [a Blobok titkosítási állapotának ellenőrzését](../blobs/storage-blob-encryption-status.md)ismertető témakört.

További információ az Azure Storage-titkosítás alapjául szolgáló kriptográfiai modulokról [: a kriptográfiai API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>A titkosítási kulcsok kezelése

Az új Storage-fiókokban lévő adatforgalom a Microsoft által felügyelt kulcsokkal van titkosítva. Hivatkozhat a Microsoft által felügyelt kulcsokra az adatok titkosításához, vagy kezelheti a titkosítást a saját kulcsaival. Ha úgy dönt, hogy a titkosítást a saját kulcsaival kezeli, két lehetőség közül választhat:

- Megadhat egy, a blob Storage-ban és a Azure Filesban található adattitkosításhoz és visszafejtéshez Azure Key Vault használó *ügyfél által felügyelt kulcsot* . <sup>az ügyfél</sup> által felügyelt kulcsokkal kapcsolatos további információkért lásd: [ügyfél által felügyelt kulcsok használata Azure Key Vault az Azure Storage-titkosítás kezeléséhez](encryption-customer-managed-keys.md).
- Megadhat egy *ügyfél által megadott kulcsot* a blob Storage-műveletekhez. A blob Storage-hoz tartozó olvasási vagy írási kérelmet készítő ügyfél tartalmazhat egy titkosítási kulcsot a kérelemben, amely részletesen szabályozza a Blobok titkosításának és visszafejtésének módját. Az ügyfél által biztosított kulcsokkal kapcsolatos további információkért lásd: [titkosítási kulcs megadása a blob Storage-hoz való kérelemben (előzetes verzió)](encryption-customer-provided-keys.md).

Az alábbi táblázat összehasonlítja az Azure Storage-titkosítás legfontosabb felügyeleti lehetőségeit.

|                                        |    Microsoft által felügyelt kulcsok                             |    Felhasználó által kezelt kulcsok                                                                                                                        |    Ügyfél által biztosított kulcsok                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Titkosítási/visszafejtési műveletek    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Az Azure Storage szolgáltatásai támogatottak    |    Mind                                                |    BLOB Storage, Azure Files<sup>1, 2</sup>                                                                                                               |    Blob Storage                                                                  |
|    Kulcstároló                         |    Microsoft Key Store    |    Azure Key Vault                                                                                                                              |    Ügyfél saját kulcstárolója                                                                 |
|    Kulcs rotációs felelőssége         |    Microsoft                                          |    Ügyfél                                                                                                                                     |    Ügyfél                                                                      |
|    Kulcs vezérlő                          |    Microsoft                                     |    Ügyfél                                                                                                                    |    Ügyfél                                                                 |

<sup>1</sup> . az ügyfél által felügyelt kulcsok üzenetsor-tárolással történő létrehozását támogató fiók létrehozásával kapcsolatos információkért lásd: [hozzon létre egy fiókot, amely támogatja az ügyfél által felügyelt kulcsokat a várólistákhoz](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> . az ügyfél által felügyelt kulcsokat tartalmazó fiók létrehozásával kapcsolatos információkért lásd: [hozzon létre egy fiókot, amely támogatja az ügyfél által felügyelt kulcsokat a táblázatokhoz](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

Az Azure Managed Disks titkosításával és kulcskezelő szolgáltatásával kapcsolatos információkért lásd: az [Azure Managed Disks szolgáltatás kiszolgálóoldali titkosítása](../../virtual-machines/windows/disk-encryption.md) Windows rendszerű virtuális gépekhez, illetve az [Azure Managed Disks szolgáltatás kiszolgálóoldali titkosítása](../../virtual-machines/linux/disk-encryption.md) Linux rendszerű virtuális gépekhez.

## <a name="next-steps"></a>További lépések

- [Mi az Azure Key Vault?](../../key-vault/general/overview.md)
- [Felhasználó által kezelt kulcsok konfigurálása az Azure Storage titkosításához az Azure Portalon](storage-encryption-keys-portal.md)
- [Felhasználó által kezelt kulcsok konfigurálása az Azure Storage titkosításához a PowerShellből](storage-encryption-keys-powershell.md)
- [Felhasználó által kezelt kulcsok konfigurálása az Azure Storage titkosításához az Azure CLI-ből](storage-encryption-keys-cli.md)
