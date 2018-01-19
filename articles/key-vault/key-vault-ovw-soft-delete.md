---
ms.assetid: 
title: "Az Azure Key Vault helyreállítható törlésre |} Microsoft Docs"
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 09/25/2017
ms.openlocfilehash: 01357e4fdb9b6f27e9baf5f5c8e4c7d6b582ad35
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="azure-key-vault-soft-delete-overview"></a>Az Azure Key Vault soft-törlés áttekintése

Key Vault helyreállítható törlés funkció lehetővé teszi, hogy a törölt tárolók és a tároló objektumok soft-törlés néven ismert. Pontosabban oldjuk az alábbi esetekben:

- Helyreállítható törlésre kerültek a kulcstároló támogatása
- Támogatási helyreállítható törlésre kulcstároló-objektumok (például) kulcsok, a titkos kulcsokat, a tanúsítványok)

## <a name="supporting-interfaces"></a>Kapcsolatok támogatása

A soft-törlés funkció kezdetben érhető el a többi, .NET útján / C#, PowerShell és parancssori felület felületek.

Általános információkhoz lásd: a hivatkozások ezeket a további részletekért [Key Vault hivatkozás](https://docs.microsoft.com/azure/key-vault/).

## <a name="scenarios"></a>Forgatókönyvek

Az Azure Key tárolók nyomon követett, Azure Resource Manager által kezelt erőforrások. Az Azure Resource Manager is törlésre, amely megköveteli, hogy egy sikeres törlési művelet eredménye nem érhető el többé erőforrás jól meghatározott tulajdonság határozza meg. A soft-törlés funkció a törölt objektumok helyreállítási megoldást, hogy a törlés véletlen vagy szándékos volt.

1. Az a jellemző forgatókönyv a felhasználó véletlenül törölték a kulcstároló vagy kulcstároló objektum; Ha, amely kulcs tárolóhoz vagy a kulcs tároló objektum egy előre meghatározott ideig állíthatók el, a felhasználó előfordulhat, hogy a Törlés visszavonása, és az adatok helyreállítása.

2. Más esetben egy rosszindulatú felhasználó megkísérli kulcstároló vagy kulcstároló-objektum, például egy tárolót, egy üzleti problémákat okozhat belül kulcs törlése. A Törlés a kulcstartót vagy kulcstároló objektum mappától a tényleges adatok törlését is használhatók, biztonsági intézkedésként például Adattörlés egy másik, a jogosultságok korlátozása révén megbízható szerepkört. Ez a megközelítés hatékonyan igényel kvórum egy műveletet, amelyek egyébként egy azonnali adatvesztés.

### <a name="soft-delete-behavior"></a>Helyreállítható törlési viselkedés

Ezzel a funkcióval a törlési művelet kulcstároló vagy kulcstároló objektum a soft-törlés, az erőforrások hatékony tartó egy adott megőrzési időszak alatt a megjelenítési módja, hogy az objektum törlése közben. A szolgáltatás további lehetővé teszi a helyreállítás lényegében a törlésének visszavonása a törölt objektum. 

Soft-törlés választható Key Vault viselkedése és **alapértelmezés szerint nincs engedélyezve** ebben a kiadásban. 

### <a name="key-vault-recovery"></a>Kulcstároló-helyreállítás

Kulcstároló törlése, akkor a szolgáltatás hoz létre, az előfizetésben, a helyreállítás megfelelő metaadatok hozzáadása a proxy erőforrás. A proxy erőforrás tárolt objektumot, és a törölt-tárolónak ugyanazon a helyen rendelkezésre áll. 

### <a name="key-vault-object-recovery"></a>Kulcstároló objektum-helyreállítást

Egy kulcstartót objektumot, például a kulcs törlése után a szolgáltatás helyezi el a következő objektum törölt állapotban, így teszi elérhetetlenné a bármely adatbeolvasási műveletekkel. Ebben az állapotban lévő a kulcstartót objektum is csak akkor jelenik meg, helyreállított vagy kényszerített módon/véglegesen törlődik. 

Egy időben a Key Vault a törölt kulcstároló vagy kulcstároló objektum egy előre meghatározott megőrzési időtartam után végrehajtásra megfelelő adatok törlését művelet ütemezi. A DNS-rekord, a tároló megfelelő is megőrzi a megőrzési időtartama.

### <a name="soft-delete-retention-period"></a>Soft-törlése a megőrzési időtartam

Letölthető törölt erőforrásokat a beállított időn belül, 90 nap jelennek meg. A soft-törlés megőrzési időszakban a következő apply:

- Előfordulhat, hogy lista összes kulcstárolójának és kulcstároló objektumok a soft-törlés állapotú a előfizetés, valamint a rájuk vonatkozó törlése és a helyreállítási adatok eléréséhez.
    - Csak különleges engedélyekkel rendelkező felhasználók is listázhatja törölve tárolók. Azt javasoljuk, hogy a felhasználók egyéni szerepkör létrehozása kezelési törölt tárolók ezek különleges engedélyekkel.
- Kulcstároló ugyanazzal a névvel nem hozható létre ugyanazon a helyen; Ennek megfelelően a kulcstároló objektum nem hozható létre egy adott tárolóban Ha adott kulcstároló tartalmaz egy objektumot az azonos nevű, és amely törölt állapotban van. 
- Csak a kifejezetten kiemelt felhasználó kulcstároló vagy kulcstároló objektum lehet, hogy visszaállítása a helyreállítás parancsot a megfelelő proxy erőforráson.
    - Az egyéni szerepkör tagja, a jogosultsággal az erőforráscsoportba tartozó kulcstároló létrehozása a felhasználó visszaállíthatja a tárolóban.
- Csak a kifejezetten jogosultsággal rendelkező felhasználó lehet, hogy kényszerített kulcstároló vagy kulcstároló objektum törlése a Törlés parancsot a a megfelelő proxy erőforráson.

Kivéve, ha egy kulcstartót vagy kulcstároló objektum helyre lett állítva, a megőrzési időszak végén a szolgáltatás a kiürítést végző a helyreállíthatóan törölt kulcstároló vagy a kulcstároló objektum és a benne lévő tartalom. Erőforrás törlése nem lehetséges, hogy újraütemezte.

### <a name="permitted-purge"></a>Engedélyezett kiürítése

Véglegesen törli kiürítése, kulcstároló alkalmazáson keresztül a POST műveletet az erőforrás-proxy, amely különleges jogosultságokra van szüksége. Általában csak az előfizetés tulajdonosa fog tudni kulcstároló törlése. A POST műveletet váltja ki, hogy a tároló azonnali és helyreállíthatatlan törlését. 

Kivétel ez alól a helyzet, amikor az Azure-előfizetés jelölésű *undeletable*. Csak a szolgáltatás ebben az esetben előfordulhat, hogy végezze el a tényleges törlés, és így tesz, ütemezett folyamatként. 

## <a name="next-steps"></a>További lépések

Az alábbi két útmutatók kínálnak az elsődleges használati forgatókönyvek soft-törlés használatával.

- [A Key Vault helyreállítható törlés funkciójának használata PowerShell-lel](key-vault-soft-delete-powershell.md) 
- [A Key Vault helyreállítható törlés funkciójának használata parancssori felülettel](key-vault-soft-delete-cli.md)

