---
title: Azure Key Vault – törlés | Microsoft Docs
description: A Azure Key Vault-törléssel helyreállítható a törölt kulcstartók és a Key Vault-objektumok, például kulcsok, titkok és tanúsítványok.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/19/2019
ms.openlocfilehash: 347f8a4cf1fb95849bcf1008e91d17878f3d01f8
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598521"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Azure Key Vault: a helyreállítható törlés áttekintése

Key Vault a helyreállítható törlési funkció lehetővé teszi a törölt tárolók és tároló objektumok helyreállítását, amelyeket a rendszer a Soft delete néven is ismert. Pontosabban a következő forgatókönyvek jelennek meg:

- Key Vault helyreállítható törlésének támogatása
- A Key Vault-objektumok helyreállítható törlésének támogatása (pl. kulcsok, titkos kódok, tanúsítványok)

## <a name="supporting-interfaces"></a>Támogató felületek

A Soft-delete funkció kezdetben a [Rest](/rest/api/keyvault/), a [CLI](soft-delete-cli.md), a [PowerShell](soft-delete-powershell.md)és a [.net/C#](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet) felületen, valamint az ARM- [sablonokon](https://docs.microsoft.com/azure/templates/microsoft.keyvault/2019-09-01/vaults)keresztül érhető el.

## <a name="scenarios"></a>Forgatókönyvek

Az Azure Key Vault a Azure Resource Manager által felügyelt erőforrások nyomon követésére szolgál. A Azure Resource Manager a törlés jól definiált viselkedését is meghatározza, ami megköveteli, hogy a sikeres TÖRLÉSi művelet azt eredményezheti, hogy az erőforrás már nem érhető el. A Soft-delete szolgáltatás a törölt objektum helyreállítását kezeli, függetlenül attól, hogy a törlés véletlen vagy szándékos volt-e.

1. A tipikus esetben előfordulhat, hogy egy felhasználó véletlenül törölt egy kulcstartót vagy egy kulcstartó objektumot; Ha a Key Vault vagy a Key Vault-objektum egy előre meghatározott időszakra állítható helyre, akkor a felhasználó visszavonhatja a törlést, és helyreállíthatja az adatokat.

2. Egy másik forgatókönyvben a szélhámos felhasználó megkísérelheti törölni egy kulcstartót vagy egy kulcstartó objektumot, például egy tárolóban lévő kulcsot, hogy az üzleti fennakadást okozzon. A Key Vault vagy a Key Vault objektum törlését a mögöttes adatok tényleges törlésével lehet elválasztani biztonsági mértékként, például az adatok törlésére vonatkozó engedélyeket egy másik, megbízható szerepkörre korlátozva. Ez a megközelítés ténylegesen megköveteli a kvórumot egy olyan művelet esetében, amely egyébként azonnali adatvesztést eredményezhet.

### <a name="soft-delete-behavior"></a>Helyreállítható törlési viselkedés

Ha a helyreállítható törlés engedélyezve van, a törölt erőforrásként megjelölt erőforrások megmaradnak egy adott időszakra (alapértelmezés szerint 90 nap). A szolgáltatás továbbra is lehetővé teszi a törölt objektum helyreállítását, ami lényegében a törlés visszavonása.

Új kulcstartó létrehozásakor alapértelmezés szerint a Soft delete be van kapcsolva. A Key vaultot az [Azure CLI](soft-delete-cli.md) -n keresztül vagy a [Azure PowerShell](soft-delete-powershell.md)használatával is létrehozhatja a törlés nélkül. Ha a helyreállított törlés engedélyezve van a Key vaulton, azt nem lehet letiltani.

Az alapértelmezett megőrzési időtartam 90 nap, de a Key Vault létrehozása során lehetséges, hogy az adatmegőrzési házirend intervallumát 7 – 90 nap értékre állítja be a Azure Portal. A kiürítési védelem adatmegőrzési szabályzata ugyanazt az intervallumot használja. A beállítás után az adatmegőrzési házirend intervalluma nem módosítható.

Nem lehet újra felhasználni egy olyan kulcstartó nevét, amelyet a rendszer a megőrzési időszak lejárta után töröl.

### <a name="purge-protection"></a>Védelem kiürítése 

A védelem kiürítése opcionális Key Vaulti viselkedés, és **alapértelmezés szerint nincs engedélyezve**. A védelem kiürítése csak akkor engedélyezhető, ha a törlés engedélyezve van.  A [parancssori](soft-delete-cli.md#enabling-purge-protection) felület vagy a [PowerShell](soft-delete-powershell.md#enabling-purge-protection)használatával kapcsolható be.

Ha a védelem kiürítése be van kapcsolva, a törölt állapotban lévő tároló vagy objektum nem törölhető, amíg meg nem adta a megőrzési időszakot. A helyreállítható tárolók és objektumok továbbra is helyreállíthatók, így biztosítható, hogy az adatmegőrzési szabályzatot követni fogjuk. 

Az alapértelmezett megőrzési időtartam 90 nap, de az adatmegőrzési házirend intervalluma 7 – 90 nap értékre állítható be a Azure Portalon keresztül. Ha a megőrzési időtartam beállítása és mentése megtörtént, akkor az adott tár esetében nem módosítható. 

### <a name="permitted-purge"></a>Engedélyezett kiürítés

Végleges törlés, ürítés, kulcstartó lehetséges a proxy erőforrás utáni művelettel, és speciális jogosultságok szükségesek. Általánosságban elmondható, hogy csak az előfizetés tulajdonosa törölheti a kulcstartót. A POST művelet elindítja a tár azonnali és behajthatatlan törlését. 

Kivételek:
- Ha az Azure-előfizetés nem *törölhető*van megjelölve. Ebben az esetben csak a szolgáltatás végezheti el a tényleges törlést, és ezt ütemezett folyamatként is végrehajthatja. 
- Ha az `--enable-purge-protection flag` engedélyezve van a tárolón. Ebben az esetben Key Vault várni fog a 90 nap, amikor az eredeti titkos objektum törlésre lett megjelölve az objektum végleges törléséhez.

### <a name="key-vault-recovery"></a>Key Vault-helyreállítás

A kulcstároló törlésekor a szolgáltatás létrehoz egy proxy-erőforrást az előfizetés alatt, és elegendő metaadatokat ad hozzá a helyreállításhoz. A proxy erőforrás egy tárolt objektum, amely a Deleted Key vaulttal megegyező helyen érhető el. 

### <a name="key-vault-object-recovery"></a>Key Vault-objektum helyreállítása

A Key Vault-objektumok, például a kulcsok törlésekor a szolgáltatás törölt állapotba helyezi az objektumot, így elérhetetlenné válik a lekérési műveletekhez. Ebben az állapotban a Key Vault-objektum csak listázható, állítható helyre vagy kényszerített/véglegesen törölhető. 

Ugyanakkor Key Vault a törölt kulcstartónak vagy a Key Vault-objektumnak megfelelő mögöttes adatok törlését fogja ütemezni a végrehajtás előre meghatározott megőrzési időköze után. A tárolóhoz tartozó DNS-rekordot a megőrzési időtartam időtartama alatt is megőrzi a rendszer.

### <a name="soft-delete-retention-period"></a>Visszatartási időtartam törlése

A Soft-Deleted-erőforrások megmaradnak egy meghatározott ideig, 90 nap. A helyreállítható törlés megőrzési időköze során a következők érvényesek:

- Az előfizetéshez tartozó összes kulcstartót és kulcstartó objektumot listázhatja, valamint a hozzáférés törlését és a helyreállítási adatokat is.
    - Csak a speciális engedélyekkel rendelkező felhasználók listázhatja a törölt tárolókat. Javasoljuk, hogy a felhasználók egyéni szerepkört hozzanak létre ezekkel a speciális engedélyekkel a törölt tárolók kezelésére.
- Azonos nevű kulcstartó nem hozható létre ugyanazon a helyen; Ennek megfelelően egy Key Vault-objektum nem hozható létre egy adott tárolóban, ha a Key Vault azonos nevű objektumot tartalmaz, és amely törölt állapotban van 
- Csak egy speciális jogosultsággal rendelkező felhasználó állíthatja vissza a Key vaultot vagy a Key Vault-objektumot úgy, hogy kiállít egy Recover parancsot a megfelelő proxy erőforráson.
    - A felhasználó, az egyéni szerepkör tagja, aki rendelkezik az erőforráscsoport alatt kulcstartó létrehozásához szükséges jogosultsággal, visszaállíthatja a tárolót.
- Csak egy speciális jogosultsággal rendelkező felhasználó kényszerítheti a Key Vault vagy a Key Vault objektum törlését a megfelelő proxy erőforráson lévő delete parancs kiadásával.

Ha egy kulcstartó vagy kulcstartó-objektum helyreáll, a megőrzési időtartam végén a szolgáltatás elvégzi a nem törölt kulcstartó vagy a kulcstartó objektum és annak tartalma kiürítését. Előfordulhat, hogy az erőforrás-törlés nem lett átütemezett.

### <a name="billing-implications"></a>Számlázási következmények

Általánosságban elmondható, hogy amikor egy objektum (kulcstartó vagy kulcs vagy titkos kód) törölve állapotban van, csak két művelet lehetséges: a "kiürítés" és a "helyreállítás". Az összes többi művelet sikertelen lesz. Ezért annak ellenére, hogy az objektum létezik, nem hajtható végre művelet, és így nem kerül sor a használatra, így nincs számla. A következő kivételek vannak azonban:

- a "kiürítés" és a "helyreállítás" művelet a normál Key Vault-műveletekbe kerül, és számlázásra kerül.
- Ha az objektum egy HSM-kulcs, a "HSM-védelemmel ellátott kulcs havi díja" díjat akkor kell alkalmazni, ha az elmúlt 30 napban a kulcs verzióját használták. Ezt követően, mivel az objektum törölve állapotban van, semmilyen műveletet nem lehet végrehajtani, ezért a rendszer díjmentesen alkalmazza.

## <a name="next-steps"></a>További lépések

A következő két útmutató az elsődleges használati forgatókönyveket kínálja a Soft delete használatára.

- [A Key Vault helyreállítható törlés funkciójának használata PowerShell-lel](soft-delete-powershell.md) 
- [A Key Vault helyreállítható törlés funkciójának használata parancssori felülettel](soft-delete-cli.md)

