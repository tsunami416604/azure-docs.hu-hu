---
title: A Storage-fiók beállításainak kezelése a Azure Portal-Azure Storage-ban | Microsoft Docs
description: Megtudhatja, hogyan kezelheti a Storage-fiókok beállításait a Azure Portalban, beleértve a hozzáférés-vezérlési beállítások konfigurálását, a fiók-hozzáférési kulcsok újragenerálását, a hozzáférési réteg módosítását vagy a fiók által használt replikáció típusának módosítását. Azt is megtudhatja, hogyan törölhet egy Storage-fiókot a portálon.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 60104496006e790887dd9c4b3e4c3196e0ef6444
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671370"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>A Storage-fiók beállításainak kezelése a Azure Portal

A [Azure Portal](https://portal.azure.com)számos különböző beállítást biztosít a Storage-fiókhoz. Ez a cikk ezeket a beállításokat és azok használatát ismerteti.

## <a name="access-control"></a>Hozzáférés-vezérlés

Az Azure Storage a szerepköralapú hozzáférés-vezérlés (RBAC) használatával támogatja a blob Storage-hoz és a üzenetsor-tároláshoz Azure Active Directory engedélyezését. Az Azure AD-vel való engedélyezéssel kapcsolatos további információkért lásd: az [Azure-blobok és-várólisták hozzáférésének engedélyezése Azure Active Directory használatával](storage-auth-aad.md).

A Azure Portal **hozzáférés-vezérlési** beállításai egyszerű módszert biztosítanak a felhasználókhoz, csoportokhoz, RBAC és felügyelt identitásokhoz tartozó szerepkörök hozzárendelésére. A RBAC szerepköreinek hozzárendelésével kapcsolatos további információkért lásd: [hozzáférési jogosultságok kezelése a blob-és üzenetsor-adatokhoz a RBAC használatával](storage-auth-aad-rbac.md).

## <a name="tags"></a>Tags

Az Azure Storage támogatja Azure Resource Manager címkék használatát az Azure-erőforrások testreszabott besorolással való rendszerezéséhez. Címkéket alkalmazhat a Storage-fiókjaihoz, így logikai módon csoportosíthatja őket az előfizetésben.

A Storage-fiókok esetében a címke neve 128 karakterre van korlátozva, és a címke értéke 256 karakterre van korlátozva.

További információ: [címkék használata az Azure-erőforrások rendszerezéséhez](../../azure-resource-manager/resource-group-using-tags.md).

## <a name="access-keys"></a>Elérési kulcsok

A Storage-fiók létrehozásakor az Azure 2 512 bites Storage-fiókhoz tartozó hozzáférési kulcsokat hoz létre. Ezekkel a kulcsokkal engedélyezheti a Storage-fiókhoz való hozzáférést a megosztott kulcs használatával. Az alkalmazások megszakítása nélkül elforgathatja és újragenerálhatja a kulcsokat, és a Microsoft azt javasolja, hogy ezt rendszeresen végezze el.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

### <a name="view-account-keys-and-connection-string"></a>A fiók kulcsainak és a kapcsolatok karakterláncának megtekintése

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

### <a name="regenerate-access-keys"></a>Hozzáférési kulcsok újragenerálása

A Microsoft azt javasolja, hogy rendszeres időközönként újragenerálja a hozzáférési kulcsokat a Storage-fiók biztonságának megőrzése érdekében. Két hozzáférési kulcs van hozzárendelve, hogy el lehessen forgatni a kulcsokat. A kulcsok elforgatásakor gondoskodjon arról, hogy az alkalmazás a folyamat során fenntartsa az Azure Storage-hoz való hozzáférést. 

> [!WARNING]
> A hozzáférési kulcsok újragenerálása hatással lehet bármely olyan alkalmazásra vagy Azure-szolgáltatásra, amely a Storage-fiók kulcsával függ. Minden olyan ügyfelet, amely a fiók kulcsát használja a Storage-fiók eléréséhez, frissíteni kell az új kulcs használatára, beleértve a Media Servicest, a felhőt, az asztali és a mobil alkalmazásokat, valamint az Azure Storage-hoz készült grafikus felhasználói felületi alkalmazásokat, például a [Azure Storage Explorer ](https://azure.microsoft.com/features/storage-explorer/).

Kövesse ezt a folyamatot a Storage-fiók kulcsainak elforgatásához:

1. Frissítse a kapcsolódási karakterláncokat az alkalmazás kódjában a másodlagos kulcs használatához.
2. Generálja újra a tárfiók elsődleges elérési kulcsát. A Azure Portal **hozzáférési kulcsok** paneljén kattintson a **Key1 újragenerálása**elemre, majd az **Igen** gombra kattintva erősítse meg, hogy új kulcsot szeretne generálni.
3. Frissítse a kapcsolati sztringekat a kódban, hogy az új elsődleges tárelérési kulcsra hivatkozzanak.
4. Hasonló módon generálja újra a másodlagos elérési kulcsot.

## <a name="account-configuration"></a>Fiók konfigurációja

A Storage-fiók létrehozása után módosíthatja annak konfigurációját. Például megváltoztathatja az adatok replikálásának módját, vagy megváltoztathatja a fiók hozzáférési rétegét a gyors elérésű kapcsolaton. A [Azure Portal](https://portal.azure.com)navigáljon a Storage-fiókjához, majd a **Beállítások** területen kattintson a **konfiguráció** lehetőségre a fiók konfigurációjának megtekintéséhez és/vagy módosításához.

A Storage-fiók konfigurációjának módosítása további költségekkel járhat. További részletekért tekintse meg az [Azure Storage díjszabását](https://azure.microsoft.com/pricing/details/storage/) ismertető oldalt.

## <a name="delete-a-storage-account"></a>Tárfiók törlése

A már nem használt tárfiókok eltávolításához lépjen az [Azure Portalra](https://portal.azure.com), és kattintson a **Törlés** gombra. A tárfiókkal együtt törlődik a teljes fiók, beleértve az abban lévő összes adatot is.

> [!WARNING]
> A törölt tárfiókokat nem lehet visszaállítani, és nem lehet a törlés előtt abban tárolt tartalmakat helyreállítani. A fiók törlése előtt készítsen biztonsági másolatot minden menteni kívánt tartalomról. Ez igaz a fiókban lévő összes erőforrásra is – ha töröl egy blobot, táblát, üzenetsort vagy fájlt, az véglegesen törölve lesz.
> 

Ha egy Azure virtuális géppel társított tárfiókot próbál törölni, egy hibaüzenetet kaphat, amely szerint a tárfiók még használatban van. A hiba elhárításával kapcsolatban lásd: [Troubleshoot errors when you delete storage accounts](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md) (A tárfiókok törlésével kapcsolatos hibák elhárítása).

## <a name="next-steps"></a>További lépések

- [Az Azure Storage-fiók áttekintése](storage-account-overview.md)
- [Tárfiók létrehozása](storage-quickstart-create-account.md)
