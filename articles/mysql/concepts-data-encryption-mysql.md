---
title: Adattitkosítás Azure Database for MySQL ügyfél által felügyelt kulccsal
description: Az ügyfél által felügyelt kulccsal Azure Database for MySQL adattitkosítás lehetővé teszi, hogy Bring Your Own Key (BYOK) adatvédelmet biztosítson a REST-alapú adatvédelem érdekében. Lehetővé teszi a szervezetek számára, hogy a kulcsok és adatok kezelésében a feladatok elkülönítését is megvalósítsa.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 1d4153ac5e02d28d054034f33859332158d5a555
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162361"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>Adattitkosítás Azure Database for MySQL ügyfél által felügyelt kulccsal

> [!NOTE]
> Jelenleg a funkció használatához hozzáférést kell kérnie. Ehhez forduljon AskAzureDBforMySQL@service.microsoft.comhoz.

A Azure Database for MySQL ügyfél által felügyelt kulcsaival történő adattitkosítás lehetővé teszi, hogy a qwn kulcsot (BYOK) a REST-alapú adatvédelem érdekében. Lehetővé teszi a szervezetek számára, hogy a kulcsok és adatok kezelésében a feladatok elkülönítését is megvalósítsa. Az ügyfél által felügyelt titkosítással Ön felelős a kulcs életciklusa, a kulcsfontosságú használati engedélyek és a kulcsok működésének ellenőrzése terén.

A Azure Database for MySQL ügyfél által felügyelt kulcsaival rendelkező adattitkosítás a kiszolgáló szintjén van beállítva. Egy adott kiszolgáló esetében a szolgáltatás által használt adattitkosítási kulcs (ADATTITKOSÍTÁSI kulcsot) titkosítására a Key encryption Key (KEK) nevű ügyfél által felügyelt kulcs szolgál. A KEK egy, az ügyfél és az ügyfél által felügyelt [Azure Key Vault](../key-vault/key-Vault-secure-your-key-Vault.md) -példányban tárolt aszimmetrikus kulcs. A kulcs titkosítási kulcsát (KEK) és az adattitkosítási kulcsot (ADATTITKOSÍTÁSI kulcsot) a cikk későbbi részében részletesebben ismertetjük.

A Key Vault egy felhőalapú, külső kulcsokat kezelő rendszer. Magas rendelkezésre állású, és méretezhető, biztonságos tárolást biztosít az RSA titkosítási kulcsokhoz, opcionálisan a FIPS 140-2 2-es szintű, ellenőrzött hardveres biztonsági modulok (HSM-k) által támogatottak. Nem engedélyezi a közvetlen hozzáférést egy tárolt kulcshoz, de biztosítja a titkosítási és visszafejtési szolgáltatásokat a jogosult entitások számára. Key Vault a kulcsot létrehozhatja, importálhatja vagy [áthelyezheti egy helyszíni HSM-eszközről](../key-vault/key-Vault-hsm-protected-keys.md).

> [!NOTE]
> Ez a funkció minden olyan Azure-régióban elérhető, ahol a Azure Database for MySQL támogatja a "általános célú" és a "memória-optimalizált" árképzési szintet.

## <a name="benefits"></a>Előnyök

A Azure Database for MySQL adattitkosítása a következő előnyöket nyújtja:

* Az adathozzáférést teljes mértékben a rendszer ellenőrzi, hogy el tudja-e távolítani a kulcsot, és elérhetetlenné teszi az adatbázist 
* Teljes hozzáférés a kulcs-életciklushoz, beleértve a kulcs rotációját a vállalati házirendekkel való összehangoláshoz
* A Azure Key Vaultban található kulcsok központi felügyelete és szervezete
* A feladatok elkülönítésének lehetősége a biztonsági tisztviselők, valamint a DBA és a rendszergazdák között


## <a name="terminology-and-description"></a>Terminológia és leírás

**Adattitkosítási kulcs (adattitkosítási kulcsot)** : egy partíció vagy adatblokk titkosításához használt szimmetrikus AES256-kulcs. Az egyes adatblokkok egy másik kulccsal való titkosítása nehezebbé teszi a titkosítási elemzési támadásokat. A DEKs való hozzáférésre az erőforrás-szolgáltató vagy az alkalmazás-példány szükséges, amely egy adott blokk titkosítását és visszafejtését végzi. Amikor új kulccsal cserél le egy ADATTITKOSÍTÁSI kulcsot, csak a hozzá tartozó blokkban lévő adatmennyiséget kell újra titkosítani az új kulccsal.

**Kulcs titkosítási kulcsa (KEK)** : a DEKs titkosításához használt titkosítási kulcs. Egy KEK, amely soha nem hagy Key Vault lehetővé teszi, hogy a DEKs titkosítva és vezérelve legyenek. Előfordulhat, hogy a KEK-hez hozzáférő entitás nem azonos a ADATTITKOSÍTÁSI kulcsot igénylő entitással. Mivel a KEK a DEKs visszafejtéséhez szükséges, a KEK gyakorlatilag egyetlen pont, amellyel a DEKs hatékonyan törölhető a KEK törlésével.

A KEK titkosított DEKs külön tárolja a rendszer. Csak egy KEK-hozzáféréssel rendelkező entitás képes visszafejteni ezeket a DEKs. További információ: [Biztonság a titkosításban a REST-ben](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-a-customer-managed-key-works"></a>Az ügyfél által felügyelt kulccsal való adattitkosítás működése

![A Bring Your Own Key áttekintését bemutató diagram](media/concepts-data-access-and-security-data-encryption/mysqloverview.png)

Ahhoz, hogy egy MySQL-kiszolgáló a ADATTITKOSÍTÁSI kulcsot titkosításához Key Vaultban tárolt ügyfél által felügyelt kulcsokat használjon, a Key Vault rendszergazdája a következő hozzáférési jogosultságokat biztosítja a kiszolgálóhoz:

* **beolvasás: a**Key vaultban lévő kulcs nyilvános részének és tulajdonságainak lekérése.
* **wrapKey**: a adattitkosítási kulcsot titkosítása.
* **unwrapKey**: a adattitkosítási kulcsot visszafejtéséhez.

A Key Vault rendszergazdája [engedélyezheti Key Vault naplózási események naplózását](../azure-monitor/insights/azure-key-vault.md)is, így később is naplózhatja őket.

Ha a kiszolgáló a Key vaultban tárolt ügyfél által felügyelt kulcs használatára van konfigurálva, a kiszolgáló elküldi a ADATTITKOSÍTÁSI kulcsot a titkosításhoz a Key vaultba. Key Vault a felhasználói adatbázisban tárolt titkosított ADATTITKOSÍTÁSI kulcsot adja vissza. Hasonlóképpen, ha szükséges, a kiszolgáló a védett ADATTITKOSÍTÁSI kulcsot a Key vaultba küldi a visszafejtéshez. A könyvvizsgálók a Azure Monitor segítségével ellenőrizhetik Key Vault naplózási eseménynaplókat, ha engedélyezve van a naplózás.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Az Azure Database for MySQL adattitkosításának konfigurálására vonatkozó követelmények

A Key Vault konfigurálásának követelményei a következők:

* Key Vault és Azure Database for MySQL ugyanahhoz a Azure Active Directory (Azure AD) bérlőhöz kell tartoznia. A több-bérlős Key Vault és a kiszolgálói interakciók nem támogatottak. Az erőforrások áthelyezését követően újra kell konfigurálnia az adattitkosítást.
* Ha véletlen kulcs (vagy Key Vault) törlése történik, az adatvesztés elleni védelem érdekében engedélyeznie kell a Soft-delete szolgáltatást a kulcstartóban. A Soft-Deleted erőforrásokat 90 napig őrzi meg a rendszer, hacsak a felhasználó addig nem helyreállítja vagy törli őket. A helyreállítás és törlés műveletekhez saját engedélyek tartoznak egy Key Vault hozzáférési házirendben. A Soft-delete funkció alapértelmezés szerint ki van kapcsolva, de a PowerShell vagy az Azure CLI használatával is engedélyezhető (vegye figyelembe, hogy nem engedélyezheti a Azure Portal).
* Adja meg az Azure Database for MySQL hozzáférést a Key vaulthoz a Get, a wrapKey és a unwrapKey engedélyekkel az egyedi felügyelt identitás használatával. A Azure Portalban az egyedi identitás automatikusan létrejön, ha engedélyezve van az adattitkosítás a MySQL-ben. Lásd: az [adattitkosítás konfigurálása a MySQL](howto-data-encryption-portal.md) -hez részletes, lépésenkénti útmutatás a Azure Portal használatakor.

* Ha Key Vault tűzfallal rendelkező tűzfalat használ, engedélyeznie kell a **megbízható Microsoft-szolgáltatások számára a tűzfal megkerülésének engedélyezése**beállítást.

Az ügyfél által felügyelt kulcs konfigurálásának követelményei a következők:

* A ADATTITKOSÍTÁSI kulcsot titkosításához használt ügyfél által felügyelt kulcs csak aszimmetrikus, RSA 2028 lehet.
* A kulcs aktiválási dátumát (ha be van állítva) a múltban dátumnak és időpontnak kell lennie. A lejárati dátumnak (ha be van állítva) jövőbeli dátumnak és időpontnak kell lennie.
* A kulcsnak *engedélyezett* állapotban kell lennie.
* Ha meglévő kulcsot importál a kulcstartóba, győződjön meg arról, hogy a támogatott fájlformátumokban (`.pfx`, `.byok`, `.backup`) Megadja azt.

## <a name="recommendations"></a>Javaslatok

Ha ügyfél által felügyelt kulccsal használja az adattitkosítást, a Key Vault konfigurálására vonatkozó ajánlásokat itt találja:

* Állítsa be Key Vault erőforrás-zárolását annak szabályozására, hogy ki törölheti ezt a kritikus erőforrást, és megelőzheti a véletlen vagy jogosulatlan törlést.
* Az összes titkosítási kulcs naplózásának és jelentéskészítésének engedélyezése. A Key Vault olyan naplókat biztosít, amelyek könnyen beilleszthető más biztonsági információkba és az eseménykezelő eszközeibe. Azure Monitor Log Analytics egy olyan szolgáltatás, amely már integrálva van.

* Győződjön meg arról, hogy a Key Vault és a Azure Database for MySQL ugyanabban a régióban található, így biztosítva a gyorsabb hozzáférést a ADATTITKOSÍTÁSI kulcsot wrap és a kicsomagolási műveletek számára.

Az ügyfél által felügyelt kulcs konfigurálására vonatkozó javaslatok:

* Őrizze meg az ügyfél által felügyelt kulcs másolatát egy biztonságos helyen, vagy helyezze letétbe a letéti szolgáltatásban.

* Ha Key Vault generálja a kulcsot, hozzon létre egy kulcsos biztonsági másolatot, mielőtt első alkalommal használja a kulcsot. A biztonsági mentést csak Key Vaultra állíthatja vissza. A Backup parancsról további információt a [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey)című témakörben talál.

## <a name="inaccessible-customer-managed-key-condition"></a>Nem érhető el az ügyfél által felügyelt kulcs állapota

Ha az adattitkosítást Key Vault ügyfél által felügyelt kulccsal konfigurálja, akkor a kiszolgálóhoz való folyamatos hozzáférés szükséges ahhoz, hogy a kiszolgáló online maradjon. Ha a kiszolgáló nem fér hozzá az ügyfél által felügyelt kulcshoz Key Vault, a kiszolgáló 10 percen belül megtagadja az összes kapcsolatot. A kiszolgáló kiadja a megfelelő hibaüzenetet, és a kiszolgáló állapotát nem *elérhetőre*módosítja. Ebben az állapotban az egyetlen, az adott adatbázisban engedélyezett művelet törli azt.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Véletlen kulcs-hozzáférés visszavonása a Key Vault

Előfordulhat, hogy a megfelelő hozzáférési jogokkal rendelkező személy Key Vault véletlenül letiltja a kiszolgáló hozzáférését a kulcshoz:

* A Key Vault Get, wrapKey és unwrapKey engedélyeinek visszavonása a kiszolgálóról.
* A kulcs törlése.
* A Key Vault törlése.
* A Key Vault tűzfalszabályok módosítása.

* A kiszolgáló felügyelt identitásának törlése az Azure AD-ben.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Az ügyfél által felügyelt kulcs figyelése Key Vault

Az adatbázis állapotának figyeléséhez, valamint az átlátható adattitkosítás-hozzáférés elvesztését jelző riasztások engedélyezéséhez konfigurálja a következő Azure-szolgáltatásokat:

* [Azure Resource Health](../service-health/resource-health-overview.md): egy nem elérhető adatbázis, amely nem fér hozzá az ügyfél kulcsához, az adatbázishoz való első kapcsolódás megtagadása után "nem érhető el" érték jelenik meg.
* [Műveletnapló](../service-health/alerts-activity-log-service-notifications.md): Ha az ügyfél által felügyelt Key Vault nem sikerül hozzáférni az ügyfél kulcsához, a rendszer hozzáadja a bejegyzéseket a tevékenységi naplóhoz. Ha riasztásokat hoz létre ezekhez az eseményekhez, a lehető leghamarabb visszaállíthatja a hozzáférést.

* [Műveleti csoportok](../azure-monitor/platform/action-groups.md): ezeket a beállításokat úgy definiálhatja, hogy a beállítások alapján küldje el az értesítéseket és a riasztásokat.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Visszaállítás és replikálás az ügyfél felügyelt kulcsával Key Vault

Miután Azure Database for MySQL titkosítása megtörténik a Key Vault tárolt ügyfél felügyelt kulcsával, a kiszolgáló minden újonnan létrehozott példánya is titkosítva lesz. Ezt az új másolatot helyi vagy geo-visszaállítási művelettel, illetve olvasási replikák használatával is elvégezheti. Azonban a másolat módosítható úgy, hogy az új ügyfél felügyelt kulcsát tükrözze a titkosításhoz. Az ügyfél által felügyelt kulcs megváltozása után a kiszolgáló régi biztonsági mentései a legújabb kulcsot használják.

Ha el szeretné kerülni az ügyfél által felügyelt adattitkosítás beállításakor a visszaállítás vagy a replika olvasása során, fontos, hogy kövesse ezeket a lépéseket a fő és a visszaállított/replika kiszolgálókon:

* Kezdeményezzen helyreállítási vagy olvasási replika-létrehozási folyamatot a fő Azure Database for MySQL.
* Az újonnan létrehozott kiszolgáló (visszaállított/replika) nem elérhető állapotban marad, mert az egyedi identitása még nem kapott engedélyt a Key Vault.
* A visszaállított/replika kiszolgálón ellenőrizze újra az ügyfél által felügyelt kulcsot az adattitkosítási beállításokban. Ez biztosítja, hogy az újonnan létrehozott kiszolgáló becsomagolja és kicsomagolja az engedélyeket a Key Vaultban tárolt kulcshoz.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [állíthatja be az adattitkosítást a MySQL-hez készült Azure-adatbázishoz tartozó ügyfél által felügyelt kulccsal a Azure Portal használatával](howto-data-encryption-portal.md).
