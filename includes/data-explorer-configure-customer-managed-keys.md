---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 7f5c02c6c009e8916ed063454e0ae6049892e95c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297904"
---
Az Azure Data Explorer titkosítja az összes adatot egy tárolófiókban inaktív. Alapértelmezés szerint az adatok microsoftáltal kezelt kulccsal vannak titkosítva. A titkosítási kulcsok további szabályozásához megadhat ügyfél által felügyelt kulcsokat az adattitkosításhoz. 

Az ügyfél által felügyelt kulcsokat egy [Azure Key Vaultban](/azure/key-vault/key-vault-overview)kell tárolni. Létrehozhatja saját kulcsait, és tárolhatja őket egy key vaultban, vagy használhatja az Azure Key Vault API-t a kulcsok létrehozásához. Az Azure Data Explorer-fürt és a key vault kell ugyanabban a régióban, de lehet nek különböző előfizetések. Az ügyfelek által felügyelt kulcsok részletes magyarázatát az [Azure Key Vault ügyfél által felügyelt kulcsok című témakörben ismeri el.](/azure/storage/common/storage-service-encryption) 

Ez a cikk bemutatja, hogyan konfigurálható az ügyfél által felügyelt kulcsok.

## <a name="configure-azure-key-vault"></a>Az Azure Key Vault konfigurálása

Az ügyfelek által felügyelt kulcsok Azure Data Explorer használatával való konfigurálásához két tulajdonságot kell [beállítania a key vaultban:](/azure/key-vault/key-vault-ovw-soft-delete) **Ideiglenes törlés** és **Ne ürítse ki**a rendszert. Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve. Ezeka tulajdonságok engedélyezéséhez végezze **el a helyreállítható törlés engedélyezése** és a **kiürítési védelem engedélyezése** a [PowerShellben](/azure/key-vault/key-vault-soft-delete-powershell) vagy az [Azure CLI-ben](/azure/key-vault/key-vault-soft-delete-cli) egy új vagy meglévő key vaulton. Csak a 2048-as méretű RSA-kulcsok támogatottak. A kulcsokról további információt a [Key Vault-kulcsok című](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)témakörben talál.

> [!NOTE]
> Az ügyfél által kezelt kulcsokat használó adattitkosítás nem támogatott a [vezető és követő fürtökön.](/azure/data-explorer/follower) 

## <a name="assign-an-identity-to-the-cluster"></a>Identitás hozzárendelése a fürthöz

A fürt ügyfél által felügyelt kulcsainak engedélyezéséhez először rendeljen hozzá egy rendszeráltal hozzárendelt felügyelt identitást a fürthöz. Ezt a felügyelt identitást fogja használni a fürt engedélyek megadásához a key vault eléréséhez. A rendszeráltal hozzárendelt felügyelt identitások konfigurálásához olvassa el a [felügyelt identitások című témakört.](/azure/data-explorer/managed-identities)