---
title: Ügyféláltal felügyelt kulcsok használata az Azure HPC-gyorsítótárban lévő adatok titkosításához
description: Az Azure Key Vault és az Azure HPC cache használata a titkosítási kulcselérésének szabályozására az alapértelmezett Microsoft által felügyelt titkosítási kulcsok használata helyett
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: a31979763dd1ab5d8f289deef0e30cce27bb0df4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538868"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Ügyfél által felügyelt titkosítási kulcsok használata az Azure HPC-gyorsítótárhoz

Az Azure Key Vault segítségével szabályozhatja az azure-beli HPC-gyorsítótárban az adatok titkosításához használt kulcsok tulajdonjogát. Ez a cikk azt ismerteti, hogyan használható az ügyfél által felügyelt kulcsok a gyorsítótár-adatok titkosításához.

> [!NOTE]
> Az Azure-ban tárolt összes adat, beleértve a gyorsítótárlemezeket is, alapértelmezés szerint a Microsoft által felügyelt kulcsok használatával titkosítva van. Csak akkor kell végrehajtania a cikkben ismertetett lépéseket, ha az adatok titkosításához használt kulcsokat szeretné kezelni.

Ez a funkció csak ezekben az Azure-régiókban érhető el:

* USA keleti régiója
* USA déli középső régiója
* USA nyugati régiója, 2.

Az Azure HPC-gyorsítótár ban három lépésben engedélyezheti az ügyfelek által felügyelt kulcstitkosítást:

1. Állítson be egy Azure Key Vault a kulcsok tárolására.
1. Az Azure HPC-gyorsítótár létrehozásakor válassza ki az ügyfél által felügyelt kulcstitkosítást, és adja meg a kulcstartót és a használni kívánt kulcsot.
1. A gyorsítótár létrehozása után engedélyezze, hogy hozzáférjen a key vaulthoz.

A titkosítás csak az újonnan létrehozott gyorsítótárból való engedélyezés után van teljesen beállítva (3. lépés). Ennek az az oka, hogy át kell adnia a gyorsítótár identitását a key vaultnak, hogy az jogosult felhasználó legyen. Ezt a gyorsítótár létrehozása előtt nem teheti meg, mert az identitás nem létezik, amíg a gyorsítótár létre nem jön.

A gyorsítótár létrehozása után nem válthat az ügyfél által kezelt kulcsok és a Microsoft által kezelt kulcsok között. Ha azonban a gyorsítótár ügyfél által kezelt kulcsokat használ, szükség szerint [módosíthatja](#update-key-settings) a titkosítási kulcsot, a kulcsverziót és a kulcstartót.

## <a name="understand-key-vault-and-key-requirements"></a>A kulcstartó és a kulcskövetelmények ismertetése

A key vault és a kulcs meg kell felelnie ezeknek a követelményeknek az Azure HPC-gyorsítótárral való munka.

A kulcstartó tulajdonságai:

* **Előfizetés** – Használja ugyanazt az előfizetést, amely a gyorsítótárban van.
* **Régió** – A key vault kell ugyanabban a régióban, mint az Azure HPC-gyorsítótár.
* **Díjszabási szint** – Standard szintű elegendő az Azure HPC-gyorsítótár használata.
* **Helyreállítható törlés** – Az Azure HPC cache lehetővé teszi a helyreállítható törlést, ha még nincs konfigurálva a key vaultban.
* **Tisztítási védelem** - A kiürítési védelmet engedélyezni kell.
* **Hozzáférési házirend** – Az alapértelmezett beállítások elegendőek.
* **Hálózati kapcsolat** – Az Azure HPC cache-nek a megadott végpontbeállításoktól függetlenül hozzá kell tudnia férni a key vaulthoz.

A legfontosabb tulajdonságok:

* **Kulcs típusa** - RSA
* **RSA kulcs mérete** - 2048
* **Engedélyezve** - Igen

Kulcstartó hozzáférési engedélyei:

* Az Azure HPC-gyorsítótárat létrehozó felhasználónak a [Key Vault közreműködői szerepkörrel](../role-based-access-control/built-in-roles.md#key-vault-contributor)egyenértékű engedélyekkel kell rendelkeznie. Ugyanazok az engedélyek szükségesek az Azure Key Vault beállításához és kezeléséhez.

  További információért olvassa el [a biztonságos hozzáférés a kulcstartóhoz](../key-vault/key-vault-secure-your-key-vault.md) való hozzáférést.

## <a name="1-set-up-azure-key-vault"></a>1. Az Azure Key Vault beállítása

A gyorsítótár létrehozása előtt beállíthat egy kulcstartót és egy kulcsot, vagy ezt a gyorsítótár létrehozása részeként teheti meg. Győződjön meg arról, hogy ezek az erőforrások megfelelnek a [fent](#understand-key-vault-and-key-requirements)vázolt követelményeknek.

A gyorsítótár létrehozásakor meg kell adnia egy tárolót, kulcsot és kulcsverziót a gyorsítótár titkosításához.

A részleteket az [Azure Key Vault dokumentációjában](../key-vault/key-vault-overview.md) olvashatja el.

> [!NOTE]
> Az Azure Key Vault kell használni ugyanazt az előfizetést, és ugyanabban a régióban, mint az Azure HPC-gyorsítótár. Használja a cikk elején felsorolt támogatott területek egyikét.

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. A gyorsítótár létrehozása az ügyfél által kezelt kulcsokkal engedélyezve

Az Azure HPC-gyorsítótár létrehozásakor meg kell adnia a titkosítási kulcs forrását. Kövesse az [Azure HPC-gyorsítótár létrehozása](hpc-cache-create.md)című útmutatóutasításait, és adja meg a kulcstárolót és a kulcsot a **Lemeztitkosítási kulcsok** lapon. Létrehozhat egy új kulcstartót és kulcsot a gyorsítótár létrehozása során.

> [!TIP]
> Ha a **Lemeztitkosítási kulcsok** lap nem jelenik meg, győződjön meg arról, hogy a gyorsítótár a támogatott területek egyikében található.

A gyorsítótárat létrehozó felhasználónak a [Key Vault közreműködői szerepkörhöz](../role-based-access-control/built-in-roles.md#key-vault-contributor) vagy magasabb szintű jogosultságokkal kell rendelkeznie.

1. A privát felügyelt kulcsok engedélyezéséhez kattintson a gombra. A beállítás módosítása után megjelennek a kulcstartó beállításai.

1. Kattintson **a Kulcstartó kiválasztása** elemre a kulcsválasztó lap megnyitásához. Válassza ki vagy hozza létre a kulcstartót és a kulcsot a gyorsítótár lemezein lévő adatok titkosításához.

   Ha az Azure Key Vault nem jelenik meg a listában, ellenőrizze az alábbi követelményeket:

   * A gyorsítótár ugyanabban az előfizetésben van, mint a key vault?
   * A gyorsítótár ugyanabban a régióban van, mint a key vault?
   * Van-e hálózati kapcsolat az Azure Portal és a key vault között?

1. A tároló kiválasztása után válassza ki az egyes kulcsot a rendelkezésre álló lehetőségek közül, vagy hozzon létre egy új kulcsot. A kulcsnak 2048 bites RSA-kulcsnak kell lennie.

1. Adja meg a kijelölt kulcs verzióját. További információ a verziószámozásról az [Azure Key Vault dokumentációjában.](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning)

Folytassa a többi specifikációval, és hozza létre a gyorsítótárat az [Azure HPC-gyorsítótár létrehozása](hpc-cache-create.md)című részben leírtak szerint.

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. Az Azure Key Vault titkosításának engedélyezése a gyorsítótárból
<!-- header is linked from create article, update if changed -->

Néhány perc múlva az új Azure HPC-gyorsítótár jelenik meg az Azure Portalon. Az **Áttekintés** lapon engedélyezheti, hogy hozzáférjen az Azure Key Vaulthoz, és engedélyezze az ügyfél által felügyelt kulcstitkosítást. (A gyorsítótár megjelenhet az erőforrások listájában, mielőtt a "telepítés folyamatban" üzenetek egyértelműek.)

Ez a kétlépéses folyamat azért szükséges, mert az Azure HPC cache-példánynak szüksége van egy identitásaz Azure Key Vault az engedélyezéshez. A gyorsítótár-identitás csak a kezdeti létrehozási lépések befejezése után létezik.

> [!NOTE]
> A gyorsítótár létrehozását követő 90 percen belül engedélyeznie kell a titkosítást. Ha nem hajtja végre ezt a lépést, a gyorsítótár időtúllépés és sikertelen lesz. A sikertelen gyorsítótárat újra létre kell hozni, nem lehet kijavítani.

A gyorsítótár a **Kulcsra való várakozás**állapotát jeleníti meg. Kattintson a **titkosítás engedélyezése** gombra a lap tetején, hogy engedélyezze a gyorsítótár számára a megadott kulcstartó elérését.

![képernyőkép a portál gyorsítótárátátátáttartalmazó lapról, a Titkosítás engedélyezése gomb (felső sor) és az Állapot: Kulcsra való várakozás](media/waiting-for-key.png)

Kattintson **a Titkosítás engedélyezése** gombra, majd az **Igen** gombra kattintva engedélyezze a gyorsítótárnak a titkosítási kulcs használatát. Ez a művelet lehetővé teszi a helyreállítható törlést és a kiürítési védelmet (ha még nincs engedélyezve) a key vaulton.

![képernyőkép a portál gyorsítótárátátátáttartalmazó lapról, felül egy szalagcímüzenettel, amely az igen gombra kattintva kéri a felhasználót a titkosítás engedélyezésére](media/enable-keyvault.png)

Miután a gyorsítótár hozzáférést kér a key vaulthoz, létrehozhatja és titkosíthatja a gyorsítótárazott adatokat tároló lemezeket.

A titkosítás engedélyezése után az Azure HPC-gyorsítótár még néhány percen keresztül a titkosított lemezek és a kapcsolódó infrastruktúra létrehozásához.

## <a name="update-key-settings"></a>Kulcsbeállítások frissítése

Módosíthatja a kulcstartót, a kulcsot vagy a gyorsítótár kulcsverzióját az Azure Portalon. Kattintson a gyorsítótár **Titkosítási** beállítások hivatkozására az **Ügyfélkulcs beállításai** lap megnyitásához. (Az ügyfél által kezelt kulcsok és a rendszer által kezelt kulcsok közötti gyorsítótár nem módosítható.)

![képernyőkép az "Ügyfélkulcsok beállításai" lapról, amelyet az Azure Portal gyorsítótárlapjáról a Beállítások > titkosítás elemre kattintva értek el](media/change-key-click.png)

Kattintson a **Kulcs módosítása** hivatkozásra, majd **a Kulcsválasztó, -kulcs vagy -verzió módosítása** parancsra a kulcsválasztó megnyitásához.

![képernyőkép a "Kulcs kiválasztása az Azure Key Vaultból" lapról, amelyen három legördülő választó található a kulcstartó, a kulcs és a verzió kiválasztásához](media/select-new-key.png)

A gyorsítótárral azonos előfizetésben és régióban lévő kulcstartók a listában jelennek meg.

Miután kiválasztotta az új titkosítási kulcsértékeket, kattintson a **Kijelölés gombra.** Megjelenik egy megerősítő oldal az új értékekkel. A kijelölés véglegesítéséhez kattintson a **Mentés** gombra.

![képernyőkép a megerősítést kérő lapról a Mentés gombbal a bal felső sarokban](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>További információ az ügyfelek által felügyelt kulcsokról az Azure-ban

Ezek a cikkek további ismertetik az Azure Key Vault és az ügyfelek által felügyelt kulcsok használatával az Azure-beli adatok titkosítását:

* [Azure-tárolótitkosítás – áttekintés](../storage/common/storage-service-encryption.md)
* [Lemeztitkosítás ügyfél által felügyelt kulcsokkal](../virtual-machines/linux/disk-encryption.md#customer-managed-keys) – Dokumentáció az Azure Key Vault és a felügyelt lemezek használatához, amely hasonló az Azure HPC cache-hez használt folyamathoz

## <a name="next-steps"></a>További lépések

Miután létrehozta az Azure HPC-gyorsítótárat és az engedélyezett Key Vault-alapú titkosítást, továbbra is állítsa be a gyorsítótárat az adatforrásokhoz való hozzáférés sel.

* [Céltárak hozzáadása](hpc-cache-add-storage.md)
