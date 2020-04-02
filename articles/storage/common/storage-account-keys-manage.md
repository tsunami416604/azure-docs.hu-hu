---
title: A fiók hozzáférési kulcsainak kezelése
titleSuffix: Azure Storage
description: Ismerje meg, hogyan tekintheti meg, kezelheti és forgathatja a tárfiók hozzáférési kulcsait.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2020
ms.author: tamram
ms.openlocfilehash: b4e91aa59168deb18375bf86ae77f655ca3dab47
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521269"
---
# <a name="manage-storage-account-access-keys"></a>Tárfiók hozzáférési kulcsainak kezelése

Amikor létrehoz egy tárfiókot, az Azure két 512 bites tárfiók-hozzáférési kulcsot hoz létre. Ezek a kulcsok a tárfiókban lévő adatokhoz való hozzáférés engedélyezésére használhatók a megosztott kulcs engedélyezése révén.

A Microsoft azt javasolja, hogy az Azure Key Vault használatával kezelje a hozzáférési kulcsokat, és rendszeresen forgassa el és forgassa újra a kulcsokat. Az Azure Key Vault használatával egyszerűen elforgathatja a kulcsokat az alkalmazások megszakítása nélkül. A billentyűket manuálisan is elforgathatja.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-access-keys-and-connection-string"></a>Hozzáférési kulcsok és kapcsolati karakterlánc megtekintése

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>A hozzáférési kulcsok kezelése az Azure Key Vault használatával

A Microsoft azt javasolja, hogy az Azure Key Vault használatával kezelje és forgassa el a hozzáférési kulcsokat. Az alkalmazás biztonságosan hozzáférhet a kulcsaihoz a Key Vaultban, így elkerülheti azok tárolását az alkalmazáskóddal. A Key Vault kulcskezeléshez való használatáról az alábbi cikkekben talál további információt:

- [Tárfiók-kulcsok kezelése az Azure Key Vault és a PowerShell használatával](../../key-vault/key-vault-overview-storage-keys-powershell.md)
- [Tárfiók-kulcsok kezelése az Azure Key Vault és az Azure CLI segítségével](../../key-vault/key-vault-ovw-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Hozzáférési billentyűk manuális elforgatása

A Microsoft azt javasolja, hogy rendszeresen forgassa el a hozzáférési kulcsokat a tárfiók biztonságának megőrzése érdekében. Ha lehetséges, használja az Azure Key Vault a hozzáférési kulcsok kezeléséhez. Ha nem használja a Key Vaultot, manuálisan kell elforgatnia a kulcsokat.

Két hozzáférési kulcs van hozzárendelve, így elforgathatja a kulcsokat. Két kulcs biztosítja, hogy az alkalmazás a folyamat során is fenntartja az Azure Storage-hoz való hozzáférést.

> [!WARNING]
> A hozzáférési kulcsok újragenerálása hatással lehet minden olyan alkalmazásra vagy Azure-szolgáltatásra, amely a tárfiók kulcsától függ. Minden olyan ügyfelet, amely a fiókkulcsot használja a tárfiók eléréséhez, frissíteni kell az új kulcs használatához, beleértve a médiaszolgáltatásokat, a felhőalapú, asztali és mobilalkalmazásokat, valamint az Azure Storage grafikus felhasználói felületi alkalmazásait, például az [Azure Storage Explorert.](https://azure.microsoft.com/features/storage-explorer/)

Kövesse ezt a folyamatot a tárfiók kulcsainak elforgatásához:

1. Frissítse az alkalmazáskódban lévő kapcsolati karakterláncokat a másodlagos kulcs használatához.
2. Generálja újra a tárfiók elsődleges elérési kulcsát. Az **Azure** Portalon az Access Keys panelen kattintson a **Kulcs újragenerálása1**, majd az **Igen** gombra annak megerősítéséhez, hogy új kulcsot szeretne létrehozni.
3. Frissítse a kapcsolati sztringekat a kódban, hogy az új elsődleges tárelérési kulcsra hivatkozzanak.
4. Hasonló módon generálja újra a másodlagos elérési kulcsot.

> [!NOTE]
> A Microsoft azt javasolja, hogy egyszerre csak az egyik kulcsot használja az összes alkalmazásban. Ha egyes helyeken az 1-es kulcsot, másokban a 2-es kulcsot használja, akkor nem tudja elforgatni a billentyűket anélkül, hogy néhány alkalmazás elveszítené a hozzáférést.

Egy fiók hozzáférési kulcsainak elforgatásához a felhasználónak szolgáltatás-rendszergazdának kell lennie, vagy olyan RBAC szerepkört kell hozzárendelnie, amely tartalmazza a **Microsoft.Storage/storageAccounts/regeneratekey/action műveletet.** Néhány beépített RBAC szerepkör, amely tartalmazza ezt a műveletet a **tulajdonos,** **közreműködő**és **a tárfiók kulcsoperátori szolgáltatás szerepkör** szerepkörei. A Service Administrator szerepkörrel kapcsolatos további információkért lásd: [Klasszikus előfizetéses rendszergazdai szerepkörök, Azure RBAC-szerepkörök és Azure AD-szerepkörök.](../../role-based-access-control/rbac-and-directory-admin-roles.md) Az Azure Storage beépített RBAC szerepköreiről az [Azure RBAC beépített szerepkörei](../../role-based-access-control/built-in-roles.md#storage)tárolási **szakaszában** talál részletes információt.

## <a name="next-steps"></a>További lépések

- [Azure storage-fiók – áttekintés](storage-account-overview.md)
- [Tárfiók létrehozása](storage-account-create.md)
