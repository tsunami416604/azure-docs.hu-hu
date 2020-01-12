---
title: Egyetlen kiszolgálóból álló adattitkosítás Azure Database for PostgreSQL ügyfél által felügyelt kulccsal
description: Egyetlen kiszolgálóból álló adattitkosítás Azure Database for PostgreSQL ügyfél által felügyelt kulccsal
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: df2504b7e65a0087c9a8e20d94c596a19494069c
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903937"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-customer-managed-key"></a>Egyetlen kiszolgálóból álló adattitkosítás Azure Database for PostgreSQL ügyfél által felügyelt kulccsal

> [!NOTE]
> Jelenleg a funkció használatához hozzáférést kell kérnie. Ehhez vegye fel a kapcsolatot AskAzureDBforPostgreSQL@service.microsoft.com.

Azure Database for PostgreSQL az egykiszolgálós adatok titkosítása az ügyfél által felügyelt kulccsal lehetővé teszi, hogy Bring Your Own Key (BYOK) a REST-alapú adatvédelem terén, és lehetővé teszi a szervezetek számára, hogy a kulcsok és adatok kezelésében elkülönítsék a feladatokat. Az ügyfél által felügyelt titkosítással Ön felelős a kulcsok életciklusának teljes körű ellenőrzésében (kulcs létrehozása, feltöltése, forgatása, törlése), a kulcshasználat engedélyei és a kulcsok műveleteinek naplózása.

Azure Database for PostgreSQL egyetlen kiszolgáló esetén az adattitkosítás a kiszolgáló szintjén van beállítva. Az adattitkosítás ezen formáját kihasználva a kulcs az adatbázis-titkosítási kulcs (ADATTITKOSÍTÁSI kulcsot) titkosításához használatos, amely egy, az ügyfél által felügyelt aszimmetrikus kulcs, amely egy, az ügyfelek által felügyelt [Azure Key Vault (AKV)](https://docs.microsoft.com/azure/key-Vault/key-Vault-secure-your-key-Vault), egy felhőalapú külső kulcs-felügyeleti rendszer. A AKV magas rendelkezésre állású, és méretezhető biztonságos tárhelyet biztosít az RSA titkosítási kulcsokhoz, opcionálisan a FIPS 140-2 2. szintű, ellenőrzött hardveres biztonsági modulok (HSM) által támogatott. Nem engedélyezi a közvetlen hozzáférést egy tárolt kulcshoz, de a titkosítási/visszafejtési szolgáltatásokat biztosít a kulcs használatával a jogosult entitások számára. A kulcsot létrehozhatja a Key Vault, importálhatja vagy [átviheti a Key Vault egy helyszíni HSM-eszközről](https://docs.microsoft.com/azure/key-Vault/key-Vault-hsm-protected-keys).

> [!NOTE]
> Ez a funkció minden olyan Azure-régióban elérhető, ahol Azure Database for PostgreSQL egyetlen kiszolgáló támogatja a általános célú és a memóriához optimalizált díjszabási szintet.

## <a name="benefits"></a>Előnyök

Azure Database for PostgreSQL egyetlen kiszolgáló adattitkosítása a következő előnyöket biztosítja:
* A titkosítási kulcs nagyobb átláthatósága és részletes szabályozása és kezelése 
* A központi felügyelet és a kulcsok megszervezése a Azure Key Vault-ban. 
* A feladatok elkülönítésének lehetősége a szervezeten belüli kulcsok és adatok kezelésében
* Az adatkezeléstől a szervezeten belül elkülöníthető a kulcskezelő szolgáltatás, így Key Vault rendszergazda visszavonhatja a kulcs-hozzáférési engedélyeket, hogy a titkosított adatbázis elérhetetlenné váljon 
* Nagyobb megbízhatóság a végfelhasználók számára, mivel Azure Key Vault úgy tervezték, hogy a Microsoft nem láthatja és nem tudja kinyerni a titkosítási kulcsokat

## <a name="terminology-and-description"></a>Terminológia és leírás

**Adattitkosítási kulcs (adattitkosítási kulcsot)** – egy partíció vagy adatblokk titkosításához használt szimmetrikus AES256-kulcs. Az egyes adatblokkok egy másik kulccsal való titkosítása nehezebbé teszi a titkosítási elemzési támadásokat. A DEKs való hozzáférésre az erőforrás-szolgáltató vagy az alkalmazás-példány szükséges, amely egy adott blokk titkosítását és visszafejtését végzi. Ha egy ADATTITKOSÍTÁSI kulcsot egy új kulccsal van lecserélve, csak a hozzá tartozó blokkban lévő adatmennyiséget kell újra titkosítani az új kulccsal.

**Kulcs titkosítási kulcsa (KEK)** – az adattitkosítási kulcsok titkosításához használt titkosítási kulcs. Olyan kulcs-titkosítási kulcs használata, amely soha nem hagy Key Vault, lehetővé teszi, hogy az adattitkosítási kulcsok titkosítva és szabályozva legyenek. Előfordulhat, hogy a KEK-hez hozzáférő entitás nem azonos a ADATTITKOSÍTÁSI kulcsot igénylő entitással. Mivel a KEK a DEKs visszafejtéséhez szükséges, a KEK gyakorlatilag egyetlen pont, amellyel a DEKs hatékonyan törölhető a KEK törlésével.

A titkosítási kulcsokkal titkosított adattitkosítási kulcsokat a rendszer külön tárolja, és csak a kulcs titkosítási kulcshoz hozzáféréssel rendelkező entitások tudják visszafejteni ezeket az adattitkosítási kulcsokat. További részletekért tekintse meg a [titkosítás biztonsága a REST-ben](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)című témakört.

## <a name="how-data-encryption-with-customer-managed-key-works"></a>Az ügyfél által felügyelt kulccsal való adattitkosítás működése

![Saját kulcs áttekintése](media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png)

Ahhoz, hogy egy PostgreSQL-kiszolgáló használhassa a AKV-ben tárolt, az ügyfél által felügyelt kulcsokat a ADATTITKOSÍTÁSI kulcsot titkosításához, egy Key Vault rendszergazdának a következő hozzáférési jogosultságokat kell biztosítania a kiszolgálóhoz az egyedi identitása használatával:
* **Get** – a nyilvános rész és a kulcs tulajdonságainak lekérése a Key Vault
* **wrapKey** – a adattitkosítási kulcsot védelme (titkosítása)
* **unwrapKey** – a adattitkosítási kulcsot feloldása (visszafejtés)

Key Vault [a rendszergazda engedélyezheti Key Vault naplózási események naplózását](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-Vault)is, így később is naplózhatja őket.

Ha a kiszolgáló úgy van konfigurálva, hogy a Key Vault tárolt ügyfél által felügyelt kulcsot használja, a kiszolgáló a ADATTITKOSÍTÁSI kulcsot a titkosítások Key Vault küldi el. Key Vault a felhasználói adatbázisban tárolt titkosított ADATTITKOSÍTÁSI kulcsot adja vissza. Hasonlóképpen, ha szükséges, a kiszolgáló védett ADATTITKOSÍTÁSI kulcsot küld a Key Vault a visszafejtéshez. A rendszernaplók a Azure Monitor segítségével ellenőrizhetik Key Vault AuditEvent-naplókat, ha engedélyezve van a naplózás.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Az Azure Database for PostgreSQL egyetlen kiszolgáló adattitkosításának konfigurálására vonatkozó követelmények

### <a name="requirements-for-configuring-akv"></a>A AKV konfigurálásának követelményei

* Key Vault és Azure Database for PostgreSQL egyetlen kiszolgálónak ugyanahhoz a Azure Active Directory (HRE) bérlőhöz kell tartoznia. A több-bérlős Key Vault és a kiszolgálói interakciók nem támogatottak. Az erőforrások áthelyezését követően újra kell konfigurálnia az adattitkosítást. További információ az erőforrások áthelyezéséről.
* A Soft-delete szolgáltatást engedélyezni kell a Key Vaulton, hogy védve legyen az adatvesztés véletlen kulcsának (vagy Key Vault) törlésének. A Soft-Deleted erőforrásokat 90 napig őrzi meg a rendszer, kivéve, ha az ügyfél nem állítja helyre vagy nem törli őket addig. A helyreállítás és törlés műveletekhez saját engedélyek tartoznak egy Key Vault hozzáférési házirendben. A Soft-delete funkció alapértelmezés szerint ki van kapcsolva, és a PowerShell vagy a CLI használatával engedélyezhető. Azure Portal használatával nem engedélyezhető.
* Az egyedi felügyelt identitás használatával adja meg az Azure Database for PostgreSQL egyetlen kiszolgáló hozzáférését a Key Vaulthoz a **Get, wrapKey, unwrapKey** engedélyekkel. Azure Portal használatakor az egyedi azonosító automatikusan létrejön, ha a PostgreSQL-kiszolgálón engedélyezve van az adattitkosítás. A Azure Portal használatakor részletes útmutatásért lásd: az [adattitkosítás konfigurálása a PostgreSQL-hez egyetlen kiszolgáló](howto-data-encryption-portal.md) .

* Ha a AKV használatával tűzfalat használ, engedélyeznie kell *a megbízható Microsoft-szolgáltatások engedélyezése beállítást a tűzfal megkerüléséhez*.

### <a name="requirements-for-configuring-customer-managed-key"></a>Az ügyfél által felügyelt kulcs konfigurálásának követelményei
* A ADATTITKOSÍTÁSI kulcsot titkosításához használt ügyfél által felügyelt kulcs csak aszimmetrikus, RSA 2028 lehet.
* A kulcs aktiválási dátumát (ha be van állítva) a múltban dátumnak és időpontnak kell lennie. A lejárati dátumnak (ha be van állítva) jövőbeli dátumnak és időpontnak kell lennie.
* A kulcsnak *engedélyezett* állapotban kell lennie.

* Ha meglévő kulcsot importál a Key Vaultba, győződjön meg arról, hogy a támogatott fájlformátumokban (`.pfx`, `.byok`, `.backup`) Megadja azt.

## <a name="recommendations-when-using-data-encryption-using-customer-managed-key"></a>Javaslatok az ügyfél által felügyelt kulcs használatával történő adattitkosítás használatakor

### <a name="recommendation-for-configuring-akv"></a>Javaslat a AKV konfigurálásához

* A Key Vault erőforrás-zárolásának beállításával szabályozhatja, hogy ki törölheti ezt a kritikus erőforrást, és megelőzheti a véletlen vagy jogosulatlan törlést. További információ az erőforrás-zárolásokról.
* Naplózás és jelentéskészítés engedélyezése az összes titkosítási kulcson: Key Vault olyan naplókat biztosít, amelyek könnyen beilleszthető más biztonsági információkba és az eseménykezelő eszközeibe. Azure Monitor Log Analytics egy olyan szolgáltatásra mutat, amely már integrálva van.

* Győződjön meg arról, hogy a Key Vault és a Azure Database for PostgreSQL egyetlen kiszolgáló ugyanabban a régióban található, hogy gyorsabb hozzáférést biztosítson a ADATTITKOSÍTÁSI kulcsot wrap/unwrap műveletekhez. 

### <a name="recommendation-for-configuring-customer-managed-key"></a>Az ügyfél által felügyelt kulcs konfigurálására vonatkozó javaslat

* Őrizze meg az ügyfél által felügyelt kulcs (KEK) másolatát egy biztonságos helyen, vagy küldje el a letéti szolgáltatásnak.

* Ha a kulcsot a Key Vault generálja, hozzon létre egy kulcsos biztonsági mentést, mielőtt első alkalommal használja a kulcsot a AKV-ben. A biztonsági másolat csak Azure Key Vault lehet visszaállítható. További információ a [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey) parancsról. 

## <a name="inaccessible-customer-managed-key-condition"></a>Nem érhető el az ügyfél által felügyelt kulcs állapota

Ha az adattitkosítás ügyfél által felügyelt kulccsal van konfigurálva a Azure Key Vaultban (AKV), a kiszolgálónak folyamatos hozzáférésre van szükség ahhoz, hogy a kiszolgáló online maradjon. Ha a kiszolgáló nem fér hozzá az ügyfél által felügyelt kulcshoz a AKV-ben, 10 percen belül a kiszolgáló megkezdi az összes kapcsolat megtagadását a megfelelő hibaüzenettel, és a kiszolgáló állapotát nem **elérhetőre**módosítja. Az egyetlen olyan művelet, amely nem elérhető állapotban van, nem érhető el.

### <a name="accidental-key-access-revocation-from-the-azure-key-vault-akv"></a>Véletlen kulcs-hozzáférés visszavonása a Azure Key Vault (AKV)

Előfordulhat, hogy az Key Vault számára megfelelő hozzáférési jogokkal rendelkező személy véletlenül letiltja a kiszolgáló hozzáférését a kulcshoz az alábbiak szerint:
* Key Vault Get, wrapKey, unwrapKey engedélyeinek visszavonása a kiszolgálóról
* a kulcs törlése
* a Key Vault törlése
* Key Vault tűzfalszabály módosítása

* a kiszolgáló felügyelt identitásának törlése Azure Active Directory

## <a name="monitoring-of-the-customer-managed-key-in-the-key-vault"></a>Az ügyfél által felügyelt kulcs figyelése a Key Vault

Az adatbázis állapotának figyeléséhez és a TDE-védő hozzáférésének elvesztése miatti riasztások engedélyezéséhez konfigurálja a következő Azure-szolgáltatásokat:

* [Azure Resource Health](../service-health/resource-health-overview.md) – az ügyfél kulcsához hozzáférő nem elérhető adatbázis nem érhető el, mert a rendszer megtagadta az adatbázishoz való első kapcsolódást.
* [Műveletnapló](../service-health/alerts-activity-log-service-notifications.md) – ha az ügyfél által felügyelt Key Vault nem sikerül hozzáférni az ügyfél kulcsához, a rendszer hozzáadja a bejegyzéseket a tevékenység naplójához. Az eseményekhez tartozó riasztások létrehozása lehetővé teszi, hogy a lehető leghamarabb visszaállítsa a hozzáférést.

* A [csoportok](../azure-monitor/platform/action-groups.md) meghatározhatják, hogy az értesítések és a riasztások a beállításoknak megfelelően legyenek elküldve, például e-mail-/SMS-/leküldéses/hang-, Logic app-, ITSM-vagy Automation-Runbook.

## <a name="restore-and-replica-with-customers-managed-key-in-the-key-vault"></a>Visszaállítás és replika az ügyfél felügyelt kulcsával Key Vault

Ha egy Azure Database for PostgreSQL-kiszolgáló az ügyfél felügyelt kulcsával van titkosítva a Key Vaultban, akkor a kiszolgáló újonnan létrehozott példánya, bár a helyi vagy a Geo-visszaállítási művelet, illetve az olvasási replikák is titkosítva vannak ugyanazzal az ügyféllel felügyelt kulcs. Azonban módosíthatók úgy, hogy tükrözzék az új ügyfél felügyelt kulcsát a titkosításhoz. Az ügyfél által felügyelt kulcs megváltozása után a kiszolgáló régi biztonsági mentései a legújabb kulcsot fogják használni.

Ha el szeretné kerülni, hogy az ügyfél által felügyelt adattitkosítás beállítása a visszaállítás vagy az olvasási replika létrehozása során ne legyen probléma, fontos, hogy kövesse ezeket a lépéseket a Master és a Restore/replika kiszolgálón:

* Kezdeményezzen visszaállítási vagy olvasási replika-létrehozási folyamatot a fő Azure Database for PostgreSQL egyetlen kiszolgálóról.
* Az újonnan létrehozott kiszolgáló (visszaállított/replika) nem elérhető állapotú, mert az egyedi identitása még nem kapott engedélyt a Azure Key Vault (AKV)
* A visszaállított/replika kiszolgálón ellenőrizze újra az ügyfél által felügyelt kulcsot az adattitkosítási beállításokban annak érdekében, hogy az újonnan létrehozott kiszolgáló a AKV-ben tárolt kulcshoz sortörési/kicsomagolási engedélyeket kapjon.

* A fenti lépéseket is meg kell tenni annak biztosításához, hogy az adattitkosítás megmaradjon a főkiszolgálón, valamint a visszaállított/replika kiszolgálón.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan telepítheti az adattitkosítást az Azure Database for PostgreSQL-hez a [Azure Portal](howto-data-encryption-portal.md)használatával az ügyfél által felügyelt kulccsal.
