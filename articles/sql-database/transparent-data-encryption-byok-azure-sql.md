---
title: Ügyfél által felügyelt transzparens adattitkosítás (TDE)
description: Hozza el saját kulcsa (BYOK) támogatja az átlátszó adattitkosítást (TDE) az Azure Key Vault sql-adatbázishoz és az Azure Synapse-hoz. TDE byok áttekintéssel, előnyökkel, működésével, szempontjaival és ajánlásaival.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/18/2020
ms.openlocfilehash: 462326fb16663a6f25ff4b51ea11791201086fd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528728"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Azure SQL transzparens adattitkosítás ügyfél által kezelt kulccsal

Az Azure SQL [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) az ügyfél által felügyelt kulccsal lehetővé teszi a Saját kulcs (BYOK) forgatókönyvét az inaktív adatvédelemhez, és lehetővé teszi a szervezetek számára, hogy a kulcsok és adatok kezelésében a feladatok elkülönítését valósítsák meg. Az ügyfél által felügyelt transzparens adattitkosítással az ügyfél felelős a kulcséletciklus-kezelés (kulcslétrehozása, feltöltése, rotációja, törlése), a kulcshasználati engedélyek ért és a kulcsokon végzett műveletek naplózásáért.

Ebben a forgatókönyvben az adatbázis-titkosítási kulcs (DEK), a TDE-védő titkosításához használt kulcs egy ügyfél által felügyelt aszimmetrikus kulcs, amely et egy ügyfél által birtokolt és ügyfél által felügyelt [Azure Key Vaultban (AKV)](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)tárolnak, egy felhőalapú külső kulcskezelő rendszerben. A Key Vault magas rendelkezésre állású és skálázható biztonságos tároló az RSA kriptográfiai kulcsokhoz, opcionálisan a FIPS 140-2 Level 2 által ellenőrzött hardveres biztonsági modulokkal (HSM). Nem teszi lehetővé a tárolt kulcs hoz való közvetlen hozzáférést, de titkosítási/visszafejtési szolgáltatásokat nyújt a kulcs használatával a jogosult entitások számára. A kulcsot a kulcstartó ból hozhatja létre, importálhatja vagy [átviheti a key vaultba egy on-prem HSM-eszközről.](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)

Az Azure SQL Database és az Azure Synapse, a TDE-védő a logikai kiszolgáló szintjén van beállítva, és az adott kiszolgálóhoz társított összes titkosított adatbázis örökli. Az Azure SQL felügyelt példány, a TDE-védő a példány szintjén van beállítva, és az adott példány összes titkosított adatbázisa örökli. A *kiszolgáló* kifejezés az SQL Database logikai kiszolgálójára és a felügyelt példányra is hivatkozik ebben a dokumentumban, kivéve, ha másként van megállapítva.

> [!IMPORTANT]
> Azok számára, akik a szolgáltatás által felügyelt TDE-t használják, akik az ügyfél által felügyelt TDE-t szeretnék használni, az adatok titkosítva maradnak a váltás során, és nincs állásidő, sem az adatbázisfájlok újratitkosítása. A szolgáltatás által felügyelt kulcsról az ügyfél által felügyelt kulcsra való váltáshoz csak a DEK újratitkosítására van szükség, amely gyors és online művelet.

## <a name="benefits-of-the-customer-managed-tde"></a>Az ügyfél által kezelt TDE előnyei

Az ügyfél által felügyelt TDE a következő előnyöket nyújtja az ügyfélnek:

- A TDE-védő használatának és kezelésének teljes és részletes ellenőrzése;

- A TDE-védő használatának átláthatósága;

- A szervezeten belüli kulcsok és adatok kezelésével kapcsolatos feladatok elkülönítésének megvalósítása;

- A Key Vault rendszergazdája visszavonhatja a kulcshozzáférési engedélyeket, hogy a titkosított adatbázis elérhetetlenné váljon;

- Az AKV kulcsainak központi kezelése;

- Nagyobb bizalom a végfelhasználóktól, mivel az AKV úgy van kialakítva, hogy a Microsoft nem láthatja és nem nyerheti ki a titkosítási kulcsokat;

## <a name="how-customer-managed-tde-works"></a>Az ügyfél által felügyelt TDE működése

![Az ügyfél által felügyelt TDE beállítása és működése](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

Ahhoz, hogy a kiszolgáló az AKV-ban tárolt TDE protectort használhassa a DEK titkosításához, a kulcstároló rendszergazdájának a következő hozzáférési jogokat kell megadnia a kiszolgálónak az egyedi AAD-identitáshasználatával:

- **get** - a kulcs nyilvános részének és tulajdonságainak beolvasásához a Key Vaultban

- **wrapKey** - ahhoz, hogy megvédje (titkosítja) DEK

- **kicsomagolásKey** - ahhoz, hogy unprotect (visszafejtés) DEK

A key vault-rendszergazda is [engedélyezheti a key vault naplózási események naplózását,](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault)így azok később is naplózhatók.

Ha a kiszolgáló úgy van beállítva, hogy az AKV-tól tresz-védőt használjon, a kiszolgáló elküldi az egyes TDE-kompatibilis adatbázisok DEK-jét a kulcstárolónak titkosításra. A key vault visszaadja a titkosított DEK-t, amely ezután a felhasználói adatbázisban tárolódik.

Szükség esetén a kiszolgáló védett DEK-t küld a kulcstartóba visszafejtésre.

Auditorok az Azure Monitor segítségével tekintse át a kulcstartó auditevent naplók, ha a naplózás engedélyezve van.

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Az ügyfél által felügyelt TDE konfigurálásának követelményei

### <a name="requirements-for-configuring-akv"></a>Az AKV konfigurálásának követelményei

- A key vault és az SQL Database/felügyelt példány ugyanahhoz az Azure Active Directory-bérlőhöz kell tartoznia. A bérlők közötti kulcstartó és a kiszolgáló közötti interakciók nem támogatottak. Az erőforrások utólagi áthelyezéséhez az AKV-val rendelkező TDE-t újra kell konfigurálni. További információ az [erőforrások áthelyezéséről.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)

- [A helyreállítható törlési](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) funkciót engedélyezni kell a key vaultban, hogy megóvja az adatvesztés véletlen kulcs (vagy key vault) törlés történik. A helyreállíthatóan törölt erőforrások 90 napig megmaradnak, kivéve, ha az ügyfél időközben visszatér vagy kiürít. A *helyreállítási* és *kiürítési* műveletek saját engedélyekkel rendelkeznek egy key vault hozzáférési szabályzatban társítva. A soft-delete funkció alapértelmezés szerint ki van kapcsolva, és a [PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) vagy [a CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete)segítségével engedélyezhető. Nem engedélyezhető az Azure Portalon keresztül.  

- Adja meg az SQL Database-kiszolgáló vagy a felügyelt példány hozzáférést a key vault (get, wrapKey, unwrapKey) segítségével az Azure Active Directory identitását. Az Azure Portal használatakor az Azure AD-identitás automatikusan létrejön. PowerShell vagy CLI használata esetén az Azure AD-identitásexplicit módon létre kell hozni, és a befejezést ellenőrizni kell. A PowerShell használatakor a [TDE konfigurálása BYOK-tal](transparent-data-encryption-byok-azure-sql-configure.md) és a [TDE konfigurálása a FELÜGYELT példányhoz](https://aka.ms/sqlmibyoktdepowershell) című témakörben talál részletes, részletes, lépésenkénti utasításokat.

- Ha tűzfalat használ a KV-vel, engedélyeznie kell a *Megbízható Microsoft-szolgáltatások megkerülése a tűzfal megkerülésének engedélyezése*lehetőséget.

### <a name="requirements-for-configuring-tde-protector"></a>A TDE-védő konfigurálásának követelményei

- A TDE protector csak aszimmetrikus, RSA 2048 vagy RSA HSM 2048 kulcs lehet.

- A kulcs aktiválásának dátumának (ha be van állítva) a múltban dátumnak és időpontnak kell lennie. A lejárati dátumnak (ha be van állítva) jövőbeli dátumnak és időnek kell lennie.

- A kulcsnak *engedélyezett* állapotban kell lennie.

- Ha meglévő kulcsot importál a key vaultba, győződjön meg arról, hogy a támogatott fájlformátumokban (.pfx, .byok vagy .backup) biztosítja azt.

## <a name="recommendations-when-configuring-customer-managed-tde"></a>Javaslatok az ügyfél által felügyelt TDE konfigurálásakor

### <a name="recommendations-when-configuring-akv"></a>Javaslatok az AKV konfigurálásakor

- Legbőleg500 általános célú vagy 200 üzleti legkritikusabb adatbázist társítson egyetlen előfizetésben lévő kulcstartóval, hogy magas rendelkezésre állást biztosítson, amikor a kiszolgáló hozzáfér a kulcstartóTDE-védőhöz. Ezek a számok a tapasztalaton alapulnak, és dokumentálva vannak a [key vault szolgáltatás korlátaiban.](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits) A cél itt az, hogy megakadályozzák a problémákat a kiszolgáló feladatátvételután, mivel annyi kulcsműveletet indít el a tároló ellen, mint adatbázisok a kiszolgálón.

- Állítson be egy erőforrás-zárolást a key vaultban annak szabályozására, hogy ki törölheti ezt a kritikus erőforrást, és megakadályozhatja a véletlen vagy jogosulatlan törlést. További információ az [erőforrászárolásokról.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

- Engedélyezze a naplózást és a jelentéskészítést az összes titkosítási kulcson: A kulcstartó olyan naplókat biztosít, amelyek könnyen beadhatók más biztonsági információkba és eseménykezelő eszközökbe. Operations Management Suite [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) egy példa egy szolgáltatás, amely már integrált.

- A titkosított adatbázisok magas rendelkezésre állásának biztosítása érdekében minden kiszolgálót összekapcsolhat két, különböző régiókban található kulcstartóval, és ugyanazzal a kulcsanyaggal rendelkezik. Csak a kulcs megjelölése a kulcstartóból ugyanabban a régióban, mint egy TDE-védő. A rendszer automatikusan átvált a távoli régióban lévő key vaultra, ha a key vaultot ugyanabban a régióban kimaradás érinti.

### <a name="recommendations-when-configuring-tde-protector"></a>Javaslatok a TDE-védő konfigurálásakor
- Őrizze meg a TDE-védő egy példányát egy biztonságos helyen, vagy helyezze el a letéti szolgáltatásnak.

- Ha a kulcs a key vaultban jön létre, hozzon létre egy kulcsbiztonsági másolatot, mielőtt először használná a kulcsot az AKV-ban. Biztonsági mentés csak az Azure Key Vault visszaállítható. További információ a [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) parancsról.

- Hozzon létre egy új biztonsági másolatot, amikor bármilyen változás történik a kulcson (pl. kulcsattribútumok, címkék, ACL-k).

- A kulcsok elforgatása kor tartsa a kulcs **korábbi verzióit** a kulcstárolóban, hogy a régebbi adatbázis-biztonsági mentések visszaállíthatók legyenek. Ha egy adatbázis TDE-védőjét módosítja, az adatbázis régi biztonsági másolatai **nem frissülnek** a legújabb TDE-védő használatára. Visszaállításkor minden biztonsági mentéshez szüksége van a létrehozáskor titkosítva lévő TDE-védőnek. A kulcselforgatások az [Átlátszó adattitkosítás-védő elforgatása a PowerShell használatával](transparent-data-encryption-byok-azure-sql-key-rotation.md)című útmutató utasításait követve hajthatók végre.

- A szolgáltatás által felügyelt kulcsokra való váltás után is tartsa meg az összes korábban használt kulcsot az AKV-ban. Ez biztosítja, hogy az adatbázis biztonsági mentései visszaállíthatók az AKV-ban tárolt TDE-védőkkel.  Az Azure Key Vault használatával létrehozott TDE-védők karbantartást kell, hogy legyenek, amíg az összes többi tárolt biztonsági mentés létre nem jön a szolgáltatás által felügyelt kulcsokkal. Készítsen helyreállítható biztonsági másolatot ezekről a kulcsokról a [Backup-AzKeyVaultKey segítségével.](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)

- Ha egy biztonsági incidens során egy potenciálisan feltört kulcsot szeretne eltávolítani az adatvesztés kockázata nélkül, kövesse a [Potenciálisan sérült kulcs eltávolítása](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)című gomb lépéseit.

## <a name="inaccessible-tde-protector"></a>Megközelíthetetlen TDE-védő

Ha az átlátszó adattitkosítás ügyfél által felügyelt kulcs használatára van konfigurálva, az adatbázis online állapotának megőrzéséhez folyamatos hozzáférés szükséges a TDE-védőhöz. Ha a kiszolgáló elveszíti a hozzáférést az ügyfél által felügyelt TDE-védőhöz az AKV-ban, az adatbázis legfeljebb 10 percen belül elkezdi megtagadni a megfelelő hibaüzenettel való összes kapcsolatot, és az állapotát *Elérhetetlenre módosítja.* Az adatbázison csak a törlés engedélyezett művelet.

> [!NOTE]
> Ha az adatbázis időszakos hálózati kimaradás miatt nem érhető el, nincs szükség műveletre, és az adatbázisok automatikusan újra online állapotba kerülnek.

A kulcshoz való hozzáférés visszaállítása után az adatbázis online állapotba hozása további időt és lépéseket igényel, amelyek a kulcshoz való hozzáférés nélkül eltelt időtől és az adatbázisban lévő adatok méretétől függően változhatnak:

- Ha a kulcshozzáférés 8 órán belül helyreáll, az adatbázis egy órán belül automatikusan meggyógyul.

- Ha a kulcshozzáférés több mint 8 óra elteltével helyreáll, az automatikus helyreállítás nem lehetséges, és az adatbázis visszahozása további lépéseket igényel a portálon, és az adatbázis méretétől függően jelentős időt vehet igénybe. Ha az adatbázis újra online állapotba kerül, a korábban konfigurált kiszolgálószintű beállítások, például a [feladatátvételi csoport](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) konfigurációja, a pont-in-time-visszaállítási előzmények és a címkék **elvesznek.** Ezért ajánlott egy értesítési rendszer bevezetése, amely lehetővé teszi az alapul szolgáló kulcsfontosságú hozzáférési problémák 8 órán belüli azonosítását és kezelését.

### <a name="accidental-tde-protector-access-revocation"></a>Véletlen TDE-védő hozzáférés visszavonása

Előfordulhat, hogy a kulcstartóhoz elegendő hozzáférési jogosultsággal rendelkező személy véletlenül letiltja a kiszolgáló hozzáférését a kulcshoz:

- a kulcstartó *bekéselésének*, *wrapKey*, *kicsomagolásKulcs-engedélyek* visszavonása a kiszolgálóról

- törlés, kulcs

- törlés a kulcstartóból

- módosítás, a kulcstartó tűzfalszabályai

- a kiszolgáló felügyelt identitásának törlése az Azure Active Directoryban

További információ [az adatbázis elérhetetlenné válásának gyakori okairól.](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible)

## <a name="monitoring-of-the-customer-managed-tde"></a>Az ügyfél által kezelt TDE ellenőrzése

Az adatbázis állapotának figyeléséhez és a TDE-védelem hozzáférésének elvesztésére vonatkozó riasztás engedélyezéséhez konfigurálja a következő Azure-funkciókat:
- [Az Azure-erőforrások állapota](https://docs.microsoft.com/azure/service-health/resource-health-overview). A TDE-védőhöz való hozzáférésük megszakadt, és az adatbázishoz való első kapcsolat megtagadása után "Nem érhető el" felirat jelenik meg.
- [Tevékenységnapló,](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) ha az ügyfél által felügyelt kulcstartóban a TDE-védőhöz való hozzáférés sikertelen, a rendszer bejegyzéseket ad hozzá a tevékenységnaplóhoz.  Ha riasztásokat hoz létre ezekhez az eseményekhez, a lehető leghamarabb visszaállíthatja a hozzáférést.
- [A műveletcsoportok](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) definiálhatók úgy, hogy értesítéseket és riasztásokat küldjenek önnek a beállítások alapján, például e-mail/SMS/Push/Voice, Logic App, Webhook, ITSM vagy Automation Runbook alapján.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Adatbázis biztonsági mentése és visszaállítása az ügyfél által felügyelt TDE-vel

Miután egy adatbázis tde-vel titkosítva van a Key Vault kulcsa segítségével, az újonnan létrehozott biztonsági mentések is ugyanazzal a TDE-védővel lesztitkosítva. A TDE-védő módosításakor az adatbázis régi biztonsági másolatai **nem frissülnek** a legújabb TDE-védő használatához.

A Key Vaultból származó TDE-védelővel titkosított biztonsági másolat visszaállításához győződjön meg arról, hogy a kulcsanyag elérhető a célkiszolgáló számára. Ezért azt javasoljuk, hogy a TDE-védő összes régi verzióját a key vaultban tartsa, így az adatbázis biztonsági mentései visszaállíthatók.

> [!IMPORTANT]
> A kiszolgálóhoz bármikor nem lehet egynél több TDE-védő. Ez a kulcs jelölt "A kulcs az alapértelmezett TDE-védő" az Azure Portal panelen. Azonban több további kulcs is csatolható egy kiszolgálóhoz anélkül, hogy TDE-védőként jelölnék őket. Ezek a kulcsok nem a DEK védelmére szolgálnak, de a biztonsági másolatból való visszaállítás során is használhatók, ha a biztonsági másolat a megfelelő ujjlenyomattal van titkosítva a kulccsal.

Ha a biztonsági másolat visszaállításához szükséges kulcs már nem érhető el a célkiszolgáló számára, a visszaállítási kísérlet a következő hibaüzenetet adja vissza: "A célkiszolgáló `<Servername>` nem fér hozzá az időbélyeg #1> és \< \<az időbélyeg #2> között létrehozott összes AKV-hoz. Próbálkozzon újra a művelettel az összes AKV URI visszaállítása után."

A mérséklés érdekében futtassa a [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) parancsmalapot a cél SQL Database logikai kiszolgálóhoz vagy a [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) a cél felügyelt példányhoz, hogy visszaadja az elérhető kulcsok listáját, és azonosítsa a hiányzókat. Annak érdekében, hogy az összes biztonsági másolat visszaállítható legyen, győződjön meg arról, hogy a visszaállítás célkiszolgálója hozzáfér az összes szükséges kulcshoz. Ezeket a kulcsokat nem kell TDE-védőként megjelölni.

Ha többet szeretne tudni az SQL Database biztonsági mentésének helyreállításáról, olvassa [el az Azure SQL-adatbázis helyreállítása](sql-database-recovery-using-backups.md)című témakört. Ha többet szeretne tudni az SQL Pool biztonsági mentésének helyreállításáról, olvassa el [az SQL-készlet helyreállítása című témakört.](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) Az SQL Server natív biztonsági másolata/visszaállítása felügyelt példányokkal című [témakörben található: Adatbázis visszaállítása felügyelt példányra](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore)

A naplófájlok további megfontolása: A naplófájlok biztonsági mentése titkosítva marad az eredeti TDE-védéssel, még akkor is, ha elforgatták, és az adatbázis most egy új TDE-védőt használ.  Visszaállításkor mindkét kulcsra szükség lesz az adatbázis visszaállításához.  Ha a naplófájl az Azure Key Vaultban tárolt TDE-védőt használja, akkor visszaállításkor szükség lesz erre a kulcsra, még akkor is, ha az adatbázist időközben a szolgáltatás által felügyelt TDE használatára módosították.

## <a name="high-availability-with-customer-managed-tde"></a>Magas rendelkezésre állás az ügyfél által felügyelt TDE-vel

Még azokban az esetekben is, amikor nincs konfigurálva a georedundancia a kiszolgáló, erősen ajánlott beállítani a kiszolgálót, hogy két különböző key vaultkét különböző régióban azonos kulcsanyaggal. Ez úgy valósítható meg, hogy létrehoz egy TDE-védőt az elsődleges kulcstartó használatával, amely ugyanabban a régióban található, mint a kiszolgáló, és a kulcs egy másik Azure-régióban lévő kulcstartóba klónozva, így a kiszolgáló hozzáfér egy második kulcstartóhoz, ha az elsődleges kulcstartó kimaradást tapasztal, miközben az adatbázis működik.

A Backup-AzKeyVaultKey parancsmag segítségével kérje le a kulcsot titkosított formátumban az elsődleges kulcstartóból, majd használja a Restore-AzKeyVaultKey parancsmast, és adjon meg egy key vault-ot a második régióban a kulcs klónozásához. Másik lehetőségként az Azure Portal használatával biztonsági másolatot készíthet és állíthat vissza kulcsról. A kulcs a másodlagos kulcstartóban se más régióban nem kell megjelölni tde-védőként, és ez még csak nem is engedélyezett.

 Ha van egy kimaradás érintő elsődleges kulcstartó, és csak akkor, a rendszer automatikusan átvált a másik csatolt kulcs ugyanazzal az ujjlenyomattal a másodlagos kulcstartóban, ha létezik. Ne feledje azonban, hogy a kapcsoló nem fog megtörténni, ha a TDE-védő a visszavont hozzáférési jogok miatt nem érhető el, vagy mert a kulcs vagy a kulcstartó törlődik, mivel ez azt jelezheti, hogy az ügyfél szándékosan akarta korlátozni a kiszolgáló hozzáférését a kulcshoz.

![Egykiszolgálós HA](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Geo-DR és ügyfél által kezelt TDE

Az [aktív georeplikációs](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication) és [feladatátvételi csoportok](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) forgatókönyvek, minden érintett kiszolgáló igényel külön kulcstartót, amelynek együtt kell elhelyezni a kiszolgálóugyanabban az Azure-régióban. Az ügyfél felelős azért, hogy a kulcsanyag a kulcstartók között konzisztens maradjon, így a geomásodlagos tartalom szinkronban van, és ugyanazt a kulcsot használhatja a helyi kulcstartóból, ha az elsődleges elérhetetlenné válik a régióban egy kimaradás miatt, és a feladatátvétel aktiválódik . Legfeljebb négy másodlagos konfigurálható, és a láncolat (másodlagos másodlagos) nem támogatott.

A nem teljes kulcsanyag miatti georeplikációs vagy a georeplikáció során felmerülő problémák elkerülése érdekében az ügyfél által felügyelt TDE konfigurálásakor fontos, hogy kövesse ezeket a szabályokat:

- Minden érintett kulcstartónak azonos tulajdonságokkal és hozzáférési jogokkal kell rendelkeznie a megfelelő kiszolgálókhoz.

- Minden érintett kulcstartónak azonos kulcsanyagot kell tartalmaznia. Nem csak az aktuális TDE-védőre vonatkozik, hanem az összes korábbi TDE-védőre, amely a biztonsági másolatfájljaiban használható.

- A TDE-védő kezdeti beállítását és elforgatását először a másodlagos, majd az elsődleges beállításon kell elvégezni.

![Feladatátvételi csoportok és geo-dr](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

Feladatátvétel teszteléséhez kövesse az [Aktív georeplikáció áttekintése](sql-database-geo-replication-overview.md)című témakör lépéseit. Rendszeresen meg kell tenni, hogy mindkét kulcstartók sql hozzáférési engedélyeit fenntartották.

## <a name="next-steps"></a>További lépések

Érdemes lehet a következő PowerShell-mintparancsfájlokat is ellenőrizni az ügyfél által felügyelt TDE-vel végzett gyakori műveletekhez:

- [Az SQL-adatbázis transzparens adattitkosítás-védőjének elforgatása a PowerShell használatával](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [Átlátszó adattitkosítási (TDE) védő eltávolítása az SQL Database-hez a PowerShell használatával](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [Transzparens adattitkosítás kezelése felügyelt példányban saját kulccsal a PowerShell használatával](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)
