---
title: A fiók hozzáférési kulcsainak kezelése
titleSuffix: Azure Storage
description: Megtudhatja, hogyan tekintheti meg, kezelheti és forgathatja el a Storage-fiók hozzáférési kulcsait.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.openlocfilehash: 13adf6de420b54299d04a226dab81e75cbb9fef2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75975788"
---
# <a name="manage-storage-account-access-keys"></a>A Storage-fiók hozzáférési kulcsainak kezelése

A Storage-fiók létrehozásakor az Azure 2 512 bites Storage-fiókhoz tartozó hozzáférési kulcsokat hoz létre. Ezek a kulcsok a Storage-fiókban lévő adathozzáférés engedélyezésére használhatók a megosztott kulcsos hitelesítésen keresztül.

A Microsoft azt javasolja, hogy a Azure Key Vault segítségével kezelje a hozzáférési kulcsokat, és hogy rendszeresen elforgatja és újragenerálja a kulcsokat. A Azure Key Vault használata megkönnyíti a kulcsok elforgatását az alkalmazásokkal való megszakítás nélkül. Manuálisan is elforgathatja a kulcsokat.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-access-keys-and-connection-string"></a>Hozzáférési kulcsok és kapcsolati karakterláncok megtekintése

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>A Azure Key Vault használata a hozzáférési kulcsok kezeléséhez

A Microsoft azt javasolja, hogy a Azure Key Vault segítségével kezelje és forgassa el a hozzáférési kulcsokat. Az alkalmazás képes biztonságosan hozzáférni a kulcsaihoz Key Vaultban, így elkerülhető az alkalmazás kódjának tárolása. A kulcskezelő Key Vault használatáról a következő cikkekben talál további információt:

- [A Storage-fiók kulcsainak kezelése a Azure Key Vault és a PowerShell használatával](../../key-vault/key-vault-overview-storage-keys-powershell.md)
- [A Storage-fiók kulcsainak kezelése a Azure Key Vault és az Azure CLI használatával](../../key-vault/key-vault-ovw-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Hozzáférési kulcsok manuális elforgatása

A Microsoft azt javasolja, hogy rendszeres időközönként elforgatni a hozzáférési kulcsokat a Storage-fiók biztonságának megőrzése érdekében. Ha lehetséges, használja a Azure Key Vault a hozzáférési kulcsok kezeléséhez. Ha nem Key Vault használ, manuálisan kell elforgatnia a kulcsokat.

Két hozzáférési kulcs van hozzárendelve, hogy el lehessen forgatni a kulcsokat. A két kulcs biztosítja, hogy az alkalmazás a folyamat során fenntartsa az Azure Storage-hoz való hozzáférést.

> [!WARNING]
> A hozzáférési kulcsok újragenerálása hatással lehet bármely olyan alkalmazásra vagy Azure-szolgáltatásra, amely a Storage-fiók kulcsával függ. Minden olyan ügyfelet, amely a fiók kulcsát használja a Storage-fiók eléréséhez, frissíteni kell az új kulcs használatára, beleértve a Media Servicest, a felhőt, az asztali és a mobil alkalmazásokat, valamint az Azure Storage-hoz készült grafikus felhasználói felületi alkalmazásokat, például a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

Kövesse ezt a folyamatot a Storage-fiók kulcsainak elforgatásához:

1. Frissítse a kapcsolódási karakterláncokat az alkalmazás kódjában a másodlagos kulcs használatához.
2. Generálja újra a tárfiók elsődleges elérési kulcsát. A Azure Portal **hozzáférési kulcsok** paneljén kattintson a **Key1 újragenerálása**elemre, majd az **Igen** gombra kattintva erősítse meg, hogy új kulcsot szeretne generálni.
3. Frissítse a kapcsolati sztringekat a kódban, hogy az új elsődleges tárelérési kulcsra hivatkozzanak.
4. Hasonló módon generálja újra a másodlagos elérési kulcsot.

> [!NOTE]
> A Microsoft azt javasolja, hogy egyszerre csak az egyik kulcsot használja az összes alkalmazásban. Ha egyes helyeken és a 2. kulcsban az 1. kulcsot használja másokban, nem fogja tudni elforgatni a kulcsokat anélkül, hogy egy alkalmazás elveszíti a hozzáférést.

## <a name="next-steps"></a>Következő lépések

- [Az Azure Storage-fiók áttekintése](storage-account-overview.md)
- [Tárfiók létrehozása](storage-account-create.md)
