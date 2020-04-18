---
title: Az Azure Key Vault helyreállítható törlés | Microsoft dokumentumok
description: Az Azure Key Vault ban a törlés helyreállíthatóan lehetővé teszi a törölt kulcstartók és kulcstartó-objektumok, például kulcsok, titkos kulcsok és tanúsítványok helyreállítását.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/19/2019
ms.openlocfilehash: be4f124863da39cc9f6a61ebe054d451b438e8c3
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617753"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Azure Key Vault: a helyreállítható törlés áttekintése

A Key Vault helyreállítható törlési funkciója lehetővé teszi a törölt tárolók és a tárolóobjektumok, más néven a helyreállítható törlés helyreállítását. Konkrétan a következő forgatókönyvekkel foglalkozunk:

- A kulcstartó helyreállítható törlésének támogatása
- Kulcstartó-objektumok helyreállítható törlésének támogatása (pl. kulcsok, titkok, tanúsítványok)

## <a name="supporting-interfaces"></a>Támogató interfészek

A helyreállítható törlés i. szolgáltatás kezdetben a [REST,](/rest/api/keyvault/) [CLI](soft-delete-cli.md), [PowerShell](soft-delete-powershell.md) és [.NET/C#](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet) felületeken keresztül érhető el.

## <a name="scenarios"></a>Forgatókönyvek

Az Azure Key Vaults az Azure Resource Manager által felügyelt erőforrások nyomon követhető. Az Azure Resource Manager is meghatározza a jól meghatározott viselkedés törlés, amely megköveteli, hogy egy sikeres DELETE művelet et kell eredményeznie, hogy az erőforrás nem érhető el többé. A helyreállítható törlés funkció a törölt objektum helyreállítását kezeli, függetlenül attól, hogy a törlés véletlen vagy szándékos volt-e.

1. A tipikus forgatókönyv, a felhasználó véletlenül törölt egy key vault vagy egy key vault objektumot; ha a key vault vagy key vault objektum volt, hogy egy előre meghatározott ideig helyreállítható, a felhasználó visszavonhatja a törlést, és helyreállíthatja az adatokat.

2. Egy másik forgatókönyv esetén egy engedélyezetlen felhasználó megpróbálhatja törölni a key vault vagy egy key vault objektum, például egy kulcs a tárolóban, üzleti zavarokat okozhat. A key vault vagy a key vault objektum törlésének és az alapul szolgáló adatok tényleges törlésének elkülönítése biztonsági intézkedésként használható, például az adatok törlésére vonatkozó engedélyek egy másik, megbízható szerepkörre korlátozásával. Ez a megközelítés hatékonyan megköveteli a kvórum egy művelet, amely egyébként azonnali adatvesztést eredményezhet.

### <a name="soft-delete-behavior"></a>A törlés viselkedése

Ha a helyreállítható törlés engedélyezve van, a törölt erőforrásként megjelölt erőforrások egy meghatározott ideig (alapértelmezés szerint 90 napig) megmaradnak. A szolgáltatás továbbá lehetővé teszi a törölt objektum helyreállítását, lényegében a törlés visszavonása.

Új kulcstartó létrehozásakor a helyreállítható törlés alapértelmezés szerint be van kapcsolva. Az [Azure CLI-n](soft-delete-cli.md) vagy az Azure [PowerShellen](soft-delete-powershell.md)keresztül is létrehozhat egy kulcstartót a helyreállítható törlés nélkül. Ha a helyreállítható törlés engedélyezve van egy kulcstartóban, az nem tiltható le.

Az alapértelmezett megőrzési időszak 90 nap, de a key vault létrehozása során az adatmegőrzési szabályzat időköze az Azure Portalon keresztül 7 és 90 nap közötti értékre állítható be. A kiürítési védelem megőrzési házirend ugyanazt az időtartamot használja. A beállítás után az adatmegőrzési házirend-időköz nem módosítható.

A megőrzési időszak lejártáig nem használhatja fel újra a helyreállíthatóan törölt kulcstartó nevét.

### <a name="purge-protection"></a>Tisztítási védelem 

A védelem kiürítése a Key Vault nem kötelező viselkedése, és **alapértelmezés szerint nincs engedélyezve.** Cli-n vagy [CLI](soft-delete-cli.md#enabling-purge-protection) [PowerShellen](soft-delete-powershell.md#enabling-purge-protection)keresztül kapcsolható be.

Ha a kiürítési védelem be van kapcsolva, a tároló vagy a törölt állapotban lévő objektum nem törölhető, amíg a megőrzési időszak le nem telt. A helyreállítható tárolók és objektumok továbbra is helyreállíthatók, biztosítva, hogy az adatmegőrzési szabály betartatható legyen. 

Az alapértelmezett megőrzési időszak 90 nap, de az Azure Portalon keresztül 7 és 90 nap közötti értékre állítható be az adatmegőrzési házirend-időköz. Miután az adatmegőrzési házirend-időköz be van állítva, és mentette, hogy nem módosítható az adott tárolóban. 

### <a name="permitted-purge"></a>Engedélyezett tisztítás

A proxyerőforrás postaműveletén keresztül véglegesen törlött, kiürítést, kulcstartó tãing néven érhető el, és speciális jogosultságokat igényel. Általában csak az előfizetés tulajdonosa lesz képes kiüríteni a key vault. A POST művelet elindítja a páncélterem azonnali és helyrehozhatatlan törlését. 

Kivételek a következők:
- Ha az Azure-előfizetés *thetetlenként*van megjelölve. Ebben az esetben csak a szolgáltatás hajthatja végre a tényleges törlést, és ezt ütemezett folyamatként teszi. 
- Ha `--enable-purge-protection flag` a engedélyezve van a tárolóban is. Ebben az esetben a Key Vault 90 napot vár, attól kezdve, hogy az eredeti titkos objektumot törlésre jelölték meg az objektum végleges törléséhez.

### <a name="key-vault-recovery"></a>Kulcstartó helyreállítása

A key vault törlésekor a szolgáltatás létrehoz egy proxy erőforrást az előfizetés alatt, elegendő metaadatot adva a helyreállításhoz. A proxyerőforrás egy tárolt objektum, amely ugyanazon a helyen érhető el, mint a törölt kulcstartó. 

### <a name="key-vault-object-recovery"></a>Kulcstartó objektumának helyreállítása

Egy kulcstartó objektum, például egy kulcs törlésekor a szolgáltatás az objektumot törölt állapotba helyezi, így az nem érhető el a lekérési műveletekhez. Ebben az állapotban a key vault objektum csak akkor lehet felsorolni, helyreállított, vagy erőteljesen/véglegesen törölt. 

Ezzel egy időben a Key Vault ütemezi a törölt key vaultnak vagy key vault-objektumnak megfelelő alapul szolgáló adatok törlését egy előre meghatározott megőrzési időköz után. A tárolónak megfelelő DNS-rekord is megmarad a megőrzési időköz időtartamára.

### <a name="soft-delete-retention-period"></a>Ideiglenes törlés megőrzési időszaka

A helyreállíthatóan törölt erőforrások egy meghatározott ideig, 90 napig megmaradnak. A visszazáró időtartam alatt a következők érvényesek:

- Az összes key vaults és key vault objektumok az előfizetés helyreállítható an-törölt állapotban, valamint a velük kapcsolatos törlési és helyreállítási információk.
    - Csak a speciális engedélyekkel rendelkező felhasználók sorolhatják fel a törölt tárolókat. Azt javasoljuk, hogy a felhasználók hozzon létre egy egyéni szerepkört ezekkel a speciális engedélyekkel a törölt tárolók kezelésére.
- Azonos nevű kulcstartó nem hozható létre ugyanazon a helyen; ennek megfelelően egy key vault objektum nem hozható létre egy adott tárolóban, ha a kulcstartó azonos nevű objektumot tartalmaz, és amely törölt állapotban van 
- Csak egy speciálisan kiemelt jogosultsággal rendelkező felhasználó állíthatja vissza a key vault vagy a key vault objektum ot a megfelelő proxyerőforrás helyreállítási parancs kiadásával.
    - A felhasználó, az egyéni szerepkör tagja, aki rendelkezik azzal a jogosultsággal, hogy hozzon létre egy key vault az erőforráscsoport alatt visszaállíthatja a tárolót.
- Csak egy speciálisjogosultságú felhasználó törölheti erőszakkal a key vault vagy a key vault objektum ot a megfelelő proxy erőforrás törlési parancs ának kiadásával.

Kivéve, ha egy key vault vagy key vault objektum helyreáll, a megőrzési időköz végén a szolgáltatás elvégzi a dekett-törölt key vault vagy key vault objektum és annak tartalmát. Az erőforrás-törlés nem ütemezhető át.

### <a name="billing-implications"></a>Számlázási vonatkozások

Általában, ha egy objektum (egy kulcstartó vagy egy kulcs vagy titkos kulcs) törölt állapotban van, csak két művelet lehetséges: "kiürítés" és "helyreállítás". Az összes többi művelet sikertelen lesz. Ezért annak ellenére, hogy az objektum létezik, nem lehet műveleteket végrehajtani, és így nem történik használat, így nincs számla. Vannak azonban a következő kivételek:

- A "kiürítési" és a "helyreállítási" műveletek beleszámítanak a normál kulcstároló-műveletekbe, és számlázásra kerülnek.
- Ha az objektum HSM-kulcs, a "HSM védett kulcs" díj kulcsverziónként és havonta terhelés t, akkor lesz érvényes, ha az elmúlt 30 napban kulcsverziót használtak. Ezt követően, mivel az objektum törölt állapotban van, nem lehet műveleteket végrehajtani ellene, így nem számítunk fel díjat.

## <a name="next-steps"></a>További lépések

Az alábbi két útmutatók kínálnak az elsődleges használati forgatókönyvek a helyreállítható törlés.

- [A Key Vault helyreállítható törlés funkciójának használata PowerShell-lel](soft-delete-powershell.md) 
- [A Key Vault helyreállítható törlés funkciójának használata parancssori felülettel](soft-delete-cli.md)

