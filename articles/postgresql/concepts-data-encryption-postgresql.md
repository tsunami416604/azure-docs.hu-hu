---
title: Adattitkosítás ügyfél által kezelt kulccsal - Azure Database for PostgreSQL - Single server
description: A PostgreSQL egykiszolgálós adattitkosításhoz készült Azure Database egy ügyfél által felügyelt kulccsal lehetővé teszi, hogy saját kulcsát (BYOK) hozza magával az adatvédelemhez. Azt is lehetővé teszi a szervezetek számára, hogy a kulcsok és adatok kezelésében különítsék el a feladatokat.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 20e01e681c382e3c9c69f76c95a90f709f409d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297022"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-a-customer-managed-key"></a>Azure Database for PostgreSQL Single server adattitkosítás ügyfél által kezelt kulccsal

> [!NOTE]
> Ebben az időben hozzáférést kell kérnie a funkció használatához. Ehhez lépjen kapcsolatba AskAzureDBforPostgreSQL@service.microsoft.coma .

Az Azure Database for PostgreSQL Single server ügyféláltal felügyelt kulcsokkal való adattitkosítás lehetővé teszi, hogy saját kulcsát (BYOK) hozza az adatvédelemhez. Azt is lehetővé teszi a szervezetek számára, hogy a kulcsok és adatok kezelésében különítsék el a feladatokat. Az ügyfél által felügyelt titkosítással Ön a felelős a kulcs életciklusáért, a kulcshasználati engedélyekért és a kulcsokon végzett műveletek naplózásáért, és teljes mértékben szabályozhatja azt.

Az Azure Database for PostgreSQL Single server ügyféláltal felügyelt kulcsokkal való adattitkosítás a kiszolgáló szintjén van beállítva. Egy adott kiszolgáló esetében egy ügyfél által felügyelt kulcs, az úgynevezett kulcstitkosítási kulcs (KEK) a szolgáltatás által használt adattitkosítási kulcs (DEK) titkosítására szolgál. A KEK egy aszimmetrikus kulcs, amely et egy ügyfél tulajdonában lévő és az ügyfél által felügyelt [Azure Key Vault-példány](../key-vault/key-Vault-secure-your-key-Vault.md) ban tárolnak. A kulcstitkosítási kulcs (KEK) és az adattitkosítási kulcs (DEK) a cikk későbbi részében ismertetjük részletesebben.

A Key Vault egy felhőalapú, külső kulcskezelő rendszer. Magas rendelkezésre állású, és skálázható, biztonságos tárolást biztosít az RSA kriptográfiai kulcsokszámára, opcionálisan a FIPS 140-2 Level 2 által ellenőrzött hardveres biztonsági modulokkal (HSM). Nem teszi lehetővé a tárolt kulcshoz való közvetlen hozzáférést, de titkosítási és visszafejtési szolgáltatásokat nyújt az engedélyezett entitások számára. A Key Vault létrehozhatja a kulcsot, importálhatja, vagy [átviheti egy helyszíni HSM-eszközről.](../key-vault/key-Vault-hsm-protected-keys.md)

> [!NOTE]
> Ez a funkció minden Olyan Azure-régióban elérhető, ahol az Azure Database for PostgreSQL Single server támogatja az "Általános cél" és a "Memória optimalizált" tarifacsomagokat.

## <a name="benefits"></a>Előnyök

Az Azure Database for PostgreSQL Single server adattitkosítása a következő előnyökkel jár:

* Az adathozzáférést teljes mértékben szabályozza a kulcs eltávolításának és az adatbázis elérhetetlenssé tételének képessége 
*    A kulcséletciklus teljes körű ellenőrzése, beleértve a vállalati irányelvekhez igazodó kulcs elforgatását
*    A kulcsok központi kezelése és szervezése az Azure Key Vaultban
*    A biztonsági tisztviselők, valamint a DBA és a rendszergazdák közötti feladatelkülönítés megvalósításának képessége

## <a name="terminology-and-description"></a>Terminológia és leírás

**Adattitkosítási kulcs (DEK):** Egy partíció vagy adatblokk titkosítására használt szimmetrikus AES256 kulcs. Az egyes adattömbök más kulccsal történő titkosítása megnehezíti a titkosítási elemzési támadásokat. A DEK-khez való hozzáférésre az erőforrás-szolgáltatónak vagy az alkalmazáspéldánynak szüksége van egy adott blokk titkosítására és visszafejtésére. Ha a DEK-t új kulccsal cseréli le, csak a társított blokkban lévő adatokat kell újratitkosítani az új kulccsal.

**Kulcstitkosítási kulcs (KEK)**: A DEK-k titkosítására használt titkosítási kulcs. A Key Vaultot soha elhagyó KEK lehetővé teszi, hogy a DEK-k titkosítva és vezérelve legyenek. A KEK-hez hozzáféréssel rendelkező entitás eltérhet a DEK-t igénylő entitástól. Mivel a KEK-nek vissza kell fejtenie a DEK-ket, a KEK gyakorlatilag egyetlen pont, amellyel a DEK-k hatékonyan törölhetők a KEK törlésével.

A DEK-k, titkosított a KEKs, külön tárolják. Csak a KEK-hez hozzáféréssel rendelkező entitás tudja visszafejteni ezeket a DEK-ket. További információ: [Biztonság](../security/fundamentals/encryption-atrest.md)in titkosítás in in in .

## <a name="how-data-encryption-with-a-customer-managed-key-works"></a>Az ügyfél által kezelt kulccsal kapcsolatos adattitkosítás működése

![A Saját kulcs készítése című diagram](media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png)

Ahhoz, hogy egy PostgreSQL-kiszolgáló a DeK titkosításához a Key Vaultban tárolt ügyfél által kezelt kulcsokat használhassa, a Key Vault rendszergazdája a következő hozzáférési jogokat adja a kiszolgálónak:

* **beolvasás:** A kulcs nyilvános részének és tulajdonságainak beolvasása a key vaultban.
* **wrapKey**: A DEK titkosításához.
* **kicsomagoláskulcs:** A DEK visszafejtéséhez.

A key vault-rendszergazda is [engedélyezheti a Key Vault naplózási események](../azure-monitor/insights/azure-key-vault.md), így azok később is naplózható.

Ha a kiszolgáló úgy van beállítva, hogy a kulcstartóban tárolt ügyfél által kezelt kulcsot használja, a kiszolgáló elküldi a DEK-t a kulcstartónak titkosításra. A Key Vault visszaadja a titkosított DEK-t, amely a felhasználói adatbázisban van tárolva. Hasonlóképpen, ha szükséges, a kiszolgáló elküldi a védett DEK a key vault visszafejtésre. Auditorok az Azure Monitor segítségével áttekintheti a Key Vault naplózási eseménynaplók, ha a naplózás engedélyezve van.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Az Azure Database for PostgreSQL Single server adattitkosításának konfigurálásának követelményei

A Key Vault konfigurálásának követelményei az alábbiak:

* Key Vault és az Azure Database for PostgreSQL single server kell tartoznia ugyanahhoz az Azure Active Directory (Azure AD) bérlő. A bérlők közötti kulcstartó és a kiszolgáló közötti interakciók nem támogatottak. Az erőforrások utólagi áthelyezéséhez újra kell konfigurálnia az adattitkosítást.
* Engedélyeznie kell a soft-delete funkció a key vault, az adatvesztés elleni védelem érdekében, ha véletlen kulcs (vagy Key Vault) törlés történik. A helyreállíthatóan törölt erőforrások 90 napig megmaradnak, kivéve, ha a felhasználó időközben helyreállítja vagy törli azokat. A helyreállítási és kiürítési műveletek saját engedélyekkel rendelkeznek a Key Vault hozzáférési szabályzatban társítva. A helyreállítható törlés funkció alapértelmezés szerint ki van kapcsolva, de engedélyezheti a PowerShellen vagy az Azure CLI-n keresztül (vegye figyelembe, hogy az Azure Portalon keresztül nem engedélyezheti).
* Adja meg az Azure Database for PostgreSQL single server hozzáférést a key vault a get, wrapKey és unwrapKey engedélyek segítségével az egyedi felügyelt identitás. Az Azure Portalon az egyedi identitás automatikusan létrejön, ha az adatok titkosítása engedélyezve van a PostgreSQL single kiszolgálón. Tekintse meg [az Azure Database for PostgreSQL Single server adattitkosítását az Azure Portal használatával](howto-data-encryption-portal.md) részletes, lépésről-lépésre szóló útmutatásért az Azure Portal használatakor.

* Ha a Key Vault segítségével tűzfalat használ, engedélyeznie kell a **Megbízható Microsoft-szolgáltatások megkerülése a tűzfal megkerülésének engedélyezése**lehetőséget.

Az ügyfél által felügyelt kulcs konfigurálásának követelményei a következők:

* A DEK titkosításához használandó ügyfél által felügyelt kulcs csak aszimmetrikus RSA 2028 lehet.
* A kulcs aktiválásának dátumának (ha be van állítva) a múltban dátumnak és időpontnak kell lennie. A lejárati dátumnak (ha be van állítva) jövőbeli dátumnak és időnek kell lennie.
* A kulcsnak *engedélyezett* állapotban kell lennie.
* Ha meglévő kulcsot importál a key vaultba, győződjön meg arról, hogy`.pfx` `.byok`azt `.backup`a támogatott fájlformátumokban ( , , , ) adja meg.

## <a name="recommendations"></a>Javaslatok

Ha ügyfél által felügyelt kulccsal használ adattitkosítást, az alábbiakban a Key Vault konfigurálásához ajánlott:

* Állítson be egy erőforrás-zárolást a Key Vaulton annak szabályozására, hogy ki törölheti ezt a kritikus erőforrást, és megakadályozhatja a véletlen vagy jogosulatlan törlést.
* Engedélyezze az összes titkosítási kulcs naplózását és jelentését. A Key Vault olyan naplókat biztosít, amelyek könnyen beadhatók más biztonsági információkba és eseménykezelő eszközökbe. Az Azure Monitor Log Analytics egy példa egy szolgáltatás, amely már integrált.

* Győződjön meg arról, hogy a Key Vault és az Azure Database for PostgreSQL Single server ugyanabban a régióban található, a DEK wrap és kicsomagolásműveletek gyorsabb elérésének biztosítása érdekében.

Az ügyfél által felügyelt kulcs konfigurálása az alábbiakban található:

* Őrizze meg az ügyfél által felügyelt kulcs egy példányát biztonságos helyen, vagy helyezze el a letéti szolgáltatás.

* Ha a Key Vault létrehozza a kulcsot, hozzon létre egy kulcsbiztonsági másolatot a kulcs első használata előtt. Csak visszaállíthatja a biztonsági másolatot a Key Vault. A biztonsági mentési paranccsal kapcsolatos további tudnivalókért olvassa el a [Biztonsági másolat azAzKeyVaultKey billentyűt.](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey)

## <a name="inaccessible-customer-managed-key-condition"></a>Az ügyfél által felügyelt kulcsfeltétel nem érhető el

Ha a Key Vaultban ügyfél által felügyelt kulccsal konfigurálja az adattitkosítást, a kiszolgáló online állapotának megőrzéséhez folyamatos hozzáférésszükséges ehhez a kulcshoz. Ha a kiszolgáló elveszíti a hozzáférést az ügyfél által felügyelt kulcshoz a Key Vaultban, a kiszolgáló 10 percen belül elkezdi megtagadni az összes kapcsolatot. A kiszolgáló megfelelő hibaüzenetet ad ki, és a kiszolgáló állapotát *Elérhetetlenre módosítja.* Az adatbázison csak a törlés engedélyezett művelet.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Véletlen kulcshozzáférés visszavonása a Key Vaultból

Előfordulhat, hogy a Key Vaulthoz megfelelő hozzáférési jogosultsággal rendelkező személy véletlenül letiltja a kiszolgáló hozzáférését a kulcshoz:

* A kulcstartó le- és wrapkey-engedélyeinek visszavonása a kiszolgálóról.
* A kulcs törlése.
* A kulcstartó törlése.
* A kulcstartó tűzfalszabályainak módosítása.

* A kiszolgáló felügyelt identitásának törlése az Azure AD-ben.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Az ügyfél által felügyelt kulcs figyelése a Key Vaultban

Az adatbázis állapotának figyeléséhez és az átlátszó adattitkosítás-védelmi hozzáférés elvesztésének engedélyezéséhez konfigurálja a következő Azure-funkciókat:

* [Azure Resource Health:](../service-health/resource-health-overview.md)Egy elérhetetlen adatbázis, amely elvesztette a hozzáférést az ügyfélkulcshoz, "Nem érhető el" állapotban jelenik meg az adatbázishoz való első kapcsolat megtagadása után.
* [Tevékenységnapló:](../service-health/alerts-activity-log-service-notifications.md)Ha az ügyfélkulcshoz való hozzáférés az ügyfél által felügyelt key vaultban sikertelen, a bejegyzések hozzáadódnak a tevékenységnaplóhoz. A lehető leghamarabb visszaállíthatja a hozzáférést, ha riasztásokat hoz létre ezekhez az eseményekhez.

* [Műveletcsoportok](../azure-monitor/platform/action-groups.md): Ezeket úgy határozhatja meg, hogy a beállítások alapján értesítéseket és riasztásokat küldjön Önnek.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Visszaállítás és replikálás az ügyfél felügyelt kulcsával a Key Vaultban

Miután az Azure Database for PostgreSQL Single server titkosítva van az ügyfél által a Key Vaultban tárolt kezelt kulccsal, a kiszolgáló újonnan létrehozott példányai is titkosítva lesznek. Ezt az új másolatot helyi vagy geo-visszaállítási művelettel, illetve olvasási replikákkal is elkészítheti. A másolat azonban módosítható, hogy tükrözze az új ügyfél felügyelt titkosítási kulcsát. Az ügyfél által felügyelt kulcs módosításakor a kiszolgáló régi biztonsági másolatai a legújabb kulccsal kezdik el használni.

Az ügyfél által felügyelt adattitkosítás visszaállítása vagy olvasása során az ügyfelek által felügyelt adattitkosítás beállítása során felmerülő problémák elkerülése érdekében fontos, hogy kövesse az alábbi lépéseket a főkiszolgálón és a visszaállított/replikakiszolgálókon:

* Indítsa el a replika létrehozásának folyamatát a postgreSQL single kiszolgálófő Azure-adatbázisából.
* Tartsa az újonnan létrehozott kiszolgáló (visszaállított/replika) nem érhető el, mert az egyedi identitás még nem kapott engedélyt a Key Vault.
* A visszaállított/replikakiszolgálón érvényesítse újra az ügyfél által felügyelt kulcsot az adattitkosítási beállításokban. Ez biztosítja, hogy az újonnan létrehozott kiszolgáló kap wrap és kicsomagolás engedélyeket a key vaultban tárolt kulcs.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [állíthatja be az adattitkosítást egy ügyfél által felügyelt kulccsal a PostgreSQL Single server azure-adatbázisához az Azure Portal használatával.](howto-data-encryption-portal.md)

