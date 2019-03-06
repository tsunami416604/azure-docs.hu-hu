---
title: Az Azure Portal – Azure Storage tárfiók-beállítások kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti a tárfiók-beállítások az Azure Portalon, beleértve a hozzáférés-vezérlési beállítások újragenerálása a fiók hozzáférési kulcsait, a hozzáférési szint módosítása, vagy használja a fiók replikációs típusának konfigurálása. Is megtudhatja, hogyan törölni egy tárfiókot a portálon.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/11/2018
ms.author: tamram
ms.openlocfilehash: d3f6009c77618bc043586771a6f67442533b2a0d
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444055"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>Az Azure Portal tárfiók-beállítások kezelése

Számos beállítást a tárfiók számára érhetők el a [az Azure portal](https://portal.azure.com). Ez a cikk ismerteti az egyes ezeket a beállításokat és azok használatát.

## <a name="access-control"></a>Hozzáférés-vezérlés

Az Azure Storage a Blob storage és Queue storage (előzetes verzió) keresztül szerepköralapú hozzáférés-vezérlés (RBAC) az Azure Active Directory hitelesítési támogatja. Az Azure AD-hitelesítéssel kapcsolatos további információkért lásd: [hitelesítés hozzáférés az Azure-blobok és üzenetsorok az Azure Active Directory (előzetes verzió) használatával](storage-auth-aad.md).

A **hozzáférés-vezérlés** beállítások az Azure Portalon rendeljen RBAC-szerepköröket a felhasználók, csoportok, az egyszerű szolgáltatások és a felügyelt identitásokból egyszerű módszert kínálnak. További információ az RBAC-szerepkörök hozzárendelése: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) a blob és üzenetsor adatok](storage-auth-aad-rbac.md).

> [!NOTE]
> Felhasználók vagy az Azure AD hitelesítő adatait használó alkalmazások hitelesítése készít felső szintű biztonság és a könnyű használat más engedélyezési módot. Miközben továbbra is megosztott kulcsos engedélyezési használata az alkalmazások, Azure AD-vel megkerüli ügyféladataik tárolásának a kód a hozzáférési kulcsára. Közös hozzáférésű jogosultságkódok (SAS) használata a minden részletre kiterjedő hozzáférést biztosítani a tárfiókban lévő erőforrásokhoz folytathatja, de az Azure AD kezelése SAS-tokeneket vagy sérült biztonságú SAS visszavonása foglalkoznia kellene hasonló funkciókat kínál. 

## <a name="tags"></a>Címkék

Az Azure Storage támogatja az Azure Resource Manager-címkék a testre szabott besorolás az Azure-erőforrások rendszerezéséhez. A tárfiókok is címkékkel, így csoportosíthatja őket az adott előfizetéshez tartozó logikai módon. 

Storage-fiókok egy címke neve legfeljebb 128 karakter, és a egy címke értéke legfeljebb 256 karakter hosszúságú lehet.

További információkért lásd: [címkék használata az Azure-erőforrások rendszerezéséhez](../../azure-resource-manager/resource-group-using-tags.md).

## <a name="access-keys"></a>Elérési kulcs

Storage-fiók létrehozásakor az Azure létrehoz két 512 bites tárfiók hozzáférési kulcsainak. Ezek a kulcsok elérésének a tárfiókba megosztott kulcsos hitelesítéséhez használható. Elforgatás, és az alkalmazások megszakítás nélkül médiaszolgáltatással, és a Microsoft azt javasolja, hogy rendszeresen ezt tegye.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

### <a name="view-and-copy-access-keys"></a>Megtekintheti és hozzáférési kulcsok másolása

A tárfiók hitelesítő adatainak megtekintéséhez a következőt kell tennie:

1. Lépjen az [Azure Portalra](https://portal.azure.com).
2. Keresse meg a Storage-fiókját.
3. A tárfiók áttekintésének **Beállítások** szakaszában válassza a **Hozzáférési kulcsok** elemet. Megjelennek a fiókhoz tartozó hozzáférési kulcsok, valamint az egyes kulcsokhoz tartozó kapcsolati sztringek.
4. Keresse meg a **Kulcs** értéket a **key1** területen, majd kattintson a **Másolás** gombra a fiókkulcs másolásához.
5. Másik lehetőségként másolhatja a teljes kapcsolati karakterlánc. Keresse meg a **Kapcsolati sztring** értéket a **key1** területen, és kattintson a **Másolás** gombra a kapcsolati sztring másolásához.

    ![Képernyőfelvétel: a tárelérési kulcsok megtekintése az Azure Portalon](media/storage-manage-account/portal-connection-string.png)

### <a name="regenerate-access-keys"></a>Elérési kulcsok újragenerálása

A Microsoft azt javasolja, hogy Ön újragenerálja a hozzáférési kulcsokat rendszeres időközönként a storage-fiókja biztonsága érdekében. Két kulcsot, hogy meg tudja-e forgatni a kulcsok vannak hozzárendelve. A kulcsok rotálására, biztosíthatja, hogy az alkalmazás megőrzi a folyamat során az Azure Storage-hozzáférés. 

> [!WARNING]
> A tárelérési kulcsok újragenerálása hatással lehet a alkalmazások és Azure-szolgáltatások, amelyek a tárfiók kulcsát függenek. Az ügyfelek, amelyek a fiókkulcs a tárfiók eléréséhez frissíteni kell, hogy az új kulccsal, többek között például a media services, felhőalapú, asztali és mobil alkalmazások és grafikus felhasználói felület alkalmazásokat az Azure Storage, [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). 

Kövesse a következő eljárást a storage-fiók kulcsainak rotálása:

1. Frissítse a kapcsolati karakterláncokkal az alkalmazás kódjában a másodlagos kulcs használatára.
2. Generálja újra a tárfiók elsődleges elérési kulcsát. Az a **Tárelérési kulcsok** panel az Azure Portalon kattintson a **1. kulcs újragenerálása**, és kattintson a **Igen** annak ellenőrzéséhez, hogy szeretné-e egy új kulcsot létrehozni.
3. Frissítse a kapcsolati sztringekat a kódban, hogy az új elsődleges tárelérési kulcsra hivatkozzanak.
4. Hasonló módon generálja újra a másodlagos elérési kulcsot.

## <a name="account-configuration"></a>Fiók konfigurációja

Miután létrehozott egy tárfiókot, módosíthatja annak konfigurációját. Például módosíthatja az adatok hogyan a rendszer replikálja, vagy a fiók hozzáférési szintjének módosítása gyors elérésűről lassú elérésűre módosítja. Az a [az Azure portal](https://portal.azure.com), lépjen a tárfiókhoz, keresse meg, és kattintson a **konfigurációs** alatt **beállítások** megtekintéséhez és/vagy a fiók konfigurációjának módosítása.

A storage-fiók konfigurációjának módosítása a hozzáadott költségeket eredményezhet. További részletekért tekintse meg a [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/) lapot.

## <a name="delete-a-storage-account"></a>Tárfiók törlése
A már nem használt tárfiókok eltávolításához lépjen az [Azure Portalra](https://portal.azure.com), és kattintson a **Törlés** gombra. A tárfiókkal együtt törlődik a teljes fiók, beleértve az abban lévő összes adatot is.

> [!WARNING]
> A törölt tárfiókokat nem lehet visszaállítani, és nem lehet a törlés előtt abban tárolt tartalmakat helyreállítani. A fiók törlése előtt készítsen biztonsági másolatot minden menteni kívánt tartalomról. Ez igaz a fiókban lévő összes erőforrásra is – ha töröl egy blobot, táblát, üzenetsort vagy fájlt, az véglegesen törölve lesz.
> 

Ha egy Azure virtuális géppel társított tárfiókot próbál törölni, egy hibaüzenetet kaphat, amely szerint a tárfiók még használatban van. A hiba elhárításával kapcsolatban lásd: [Troubleshoot errors when you delete storage accounts](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md) (A tárfiókok törlésével kapcsolatos hibák elhárítása).

## <a name="next-steps"></a>További lépések

- [Az Azure storage-fiókok áttekintése](storage-account-overview.md)
- [Tárfiók létrehozása](storage-quickstart-create-account.md)
