---
title: Az ügyfél által felügyelt transzparens adattitkosítás (TDE)
description: Bring Your Own Key (BYOK) a transzparens adattitkosítás (TDE) támogatása a SQL Database és az adattárház Azure Key Vaultével. TDE a BYOK áttekintése, előnyei, hogyan működik, megfontolások és javaslatok.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 11/19/2019
ms.openlocfilehash: c8a1e2a19fa3c8691cdb381669dc3d4db189c42d
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995847"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Azure SQL transzparens adattitkosítás ügyfél által felügyelt kulccsal

Az ügyfél által felügyelt kulccsal rendelkező Azure SQL [transzparens adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) lehetővé teszi BRING Your Own Key (BYOK) forgatókönyv használatát az inaktív adatok védelméhez, és lehetővé teszi a szervezetek számára a kulcsok és adatok kezelésében felmerülő feladatok elkülönítését. Az ügyfél által felügyelt transzparens adattitkosítással az ügyfél felelős a kulcsfontosságú életciklus-felügyelet (a kulcs létrehozása, feltöltése, elforgatása, törlése), a kulcshasználat engedélyei és a kulcsokon végzett műveletek naplózása felett.

Ebben a forgatókönyvben a TDE Protector nevű adatbázis-titkosítási kulcs (ADATTITKOSÍTÁSI kulcsot) titkosításához használt kulcs az ügyfél által felügyelt aszimmetrikus kulcs, amelyet az ügyfél és az ügyfél által felügyelt [Azure Key Vault (AKV)](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), a felhőalapú külső kulcsokat kezelő rendszer tárol. Key Vault magas rendelkezésre állású és méretezhető biztonságos tárhely az RSA titkosítási kulcsokhoz, opcionálisan a FIPS 140-2 2-es szintű, ellenőrzött hardveres biztonsági modulok (HSM-EK) által támogatott. Nem engedélyezi a közvetlen hozzáférést egy tárolt kulcshoz, de a titkosítási/visszafejtési szolgáltatásokat biztosít a kulcs használatával a jogosult entitások számára. A kulcsot a Key Vault generálhatja, importálhatja vagy [áthelyezheti a Key vaultba egy helyszíni HSM-eszközről](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).

Azure SQL Database és Azure SQL Data Warehouse esetén a TDE-védő a logikai kiszolgáló szintjén van beállítva, és az adott kiszolgálóhoz társított összes titkosított adatbázis örökli. Az Azure SQL felügyelt példányai esetében a TDE-védő a példány szintjén van beállítva, és az adott példányon található összes titkosított adatbázis örökli. A *kiszolgáló* kifejezés a dokumentumban SQL Database logikai kiszolgáló és a felügyelt példányra is vonatkozik, kivéve, ha másként van megadva. 

> [!IMPORTANT]
> Azok a szolgáltatás által felügyelt TDE használó felhasználók, akik az ügyfél által felügyelt TDE szeretnék használni, az adatváltási folyamat során titkosítva maradnak, és az adatbázisfájlok nem lesznek újra titkosítva. Ha egy szolgáltatás által felügyelt kulcsról egy ügyfél által felügyelt kulcsra vált át, csak a ADATTITKOSÍTÁSI kulcsot újratitkosítására van szükség, amely gyors és online művelet.

## <a name="benefits-of-the-customer-managed-tde"></a>Az ügyfél által felügyelt TDE előnyei

Az ügyfél által felügyelt TDE a következő előnyöket biztosítja az ügyfél számára:

- A TDE-védő használatának és felügyeletének teljes és részletes szabályozása;

- A TDE-védő használatának átláthatósága;

- Lehetőség a feladatok elkülönítésének megvalósítására a szervezeten belüli kulcsok és adatok kezelésében;

- Key Vault rendszergazda visszavonhatja a kulcs-hozzáférési engedélyeket, hogy a titkosított adatbázis elérhetetlenné váljon;

- Kulcsok központi kezelése a AKV-ben;

- Nagyobb megbízhatóság a végfelhasználók számára, mivel a AKV úgy lett kialakítva, hogy a Microsoft nem láthatja és nem tudja kinyerni a titkosítási kulcsokat;

## <a name="how-customer-managed-tde-works"></a>Az ügyfél által felügyelt TDE működése

![Az ügyfél által felügyelt TDE beállítása és működése](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

Ahhoz, hogy a kiszolgáló használhassa a AKV-ben tárolt TDE-kezelőt a ADATTITKOSÍTÁSI kulcsot titkosításához, a Key Vault rendszergazdájának a következő hozzáférési jogosultságokat kell biztosítania a kiszolgálóhoz az egyedi HRE-identitása használatával:

- **Get** – a nyilvános rész és a kulcs tulajdonságainak lekérése a Key Vault

- **wrapKey** – a adattitkosítási kulcsot védelme (titkosítása)

- **unwrapKey** – a adattitkosítási kulcsot feloldása (visszafejtés)

A Key Vault rendszergazdája [engedélyezheti a Key Vault naplózási eseményeinek naplózását](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault)is, így később is naplózhatja őket.

Ha a kiszolgáló úgy van konfigurálva, hogy a AKV TDE-védőt használ, a kiszolgáló az egyes TDE-kompatibilis adatbázisok ADATTITKOSÍTÁSI kulcsot küldi a kulcstárolóba a titkosításhoz. A Key Vault a titkosított ADATTITKOSÍTÁSI kulcsot adja vissza, amelyet a rendszer a felhasználói adatbázisban tárol.

Szükség esetén a kiszolgáló a védett ADATTITKOSÍTÁSI kulcsot a kulcstárolóba küldi a visszafejtéshez.

A rendszernaplók a Azure Monitor használatával ellenőrizhetik a Key Vault AuditEvent naplóit, ha a naplózás engedélyezve van.


## <a name="requirements-for-configuring-customer-managed-tde"></a>Az ügyfél által felügyelt TDE konfigurálásának követelményei

### <a name="requirements-for-configuring-akv"></a>A AKV konfigurálásának követelményei

- A Key vaultnak és a SQL Database/felügyelt példánynak ugyanahhoz a Azure Active Directory bérlőhöz kell tartoznia. A több-bérlős kulcstartó és a kiszolgáló közötti interakciók nem támogatottak. Ha ezt követően szeretné áthelyezni az erőforrásokat, újra kell konfigurálni a TDE a AKV. További információ az [erőforrások áthelyezéséről](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

- A Key vaulton engedélyezni kell a [Soft delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) funkciót, hogy az adatvesztést okozó véletlen kulcs (vagy Key Vault) törlésével védve legyen. A Soft-Deleted erőforrásokat 90 napig őrzi meg a rendszer, kivéve, ha az ügyfél nem állítja helyre vagy nem törli őket addig. A *helyreállítás* és *Törlés* műveletekhez saját engedélyek tartoznak a Key Vault hozzáférési házirendjében. A Soft-delete funkció alapértelmezés szerint ki van kapcsolva, és a [PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) vagy a [CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete)használatával engedélyezhető. Azure Portal használatával nem engedélyezhető.  

- Adja meg a SQL Database-kiszolgáló vagy a felügyelt példány hozzáférését a Key vaulthoz (get, wrapKey, unwrapKey) a Azure Active Directory identitás használatával. Azure Portal használatakor az Azure AD-identitás automatikusan létrejön. A PowerShell vagy a CLI használatakor az Azure AD-identitást explicit módon létre kell hozni, és ellenőrizni kell a befejezést. Lásd: a TDE és a [BYOK](transparent-data-encryption-byok-azure-sql-configure.md) konfigurálása, valamint a TDE és a [BYOK konfigurálása a felügyelt példányhoz](https://aka.ms/sqlmibyoktdepowershell) részletes útmutatást nyújt a PowerShell használatakor.

- Ha a AKV használatával tűzfalat használ, engedélyeznie kell *a megbízható Microsoft-szolgáltatások engedélyezése beállítást a tűzfal megkerüléséhez*.

### <a name="requirements-for-configuring-tde-protector"></a>A TDE-védő konfigurálásának követelményei

- A TDE-védő csak aszimmetrikus, RSA 2048 vagy RSA HSM 2048 kulcs lehet.

- A kulcs aktiválási dátumát (ha be van állítva) a múltban dátumnak és időpontnak kell lennie. A lejárati dátumnak (ha be van állítva) jövőbeli dátumnak és időpontnak kell lennie.

- A kulcsnak *engedélyezett* állapotban kell lennie.

- Ha meglévő kulcsot importál a kulcstartóba, győződjön meg arról, hogy a támogatott fájlformátumokat (. pfx,. byok vagy. backup) adja meg.

## <a name="recommendations-when-configuring-customer-managed-tde"></a>Javaslatok az ügyfél által felügyelt TDE konfigurálásához

### <a name="recommendations-when-configuring-akv"></a>Javaslatok a AKV konfigurálásakor

- A magas rendelkezésre állás biztosítása érdekében a kiszolgáló a legfontosabb 500 általános célú vagy 200 üzletileg kritikus adatbázisokat egyetlen előfizetéshez tartozó kulcstartóval társítja, így biztosítva, hogy a kiszolgáló a kulcstartóban hozzáfér a TDE-védőhöz. Ezek az adatok a [Key Vault szolgáltatási korlátain](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits)alapuló tapasztalatok alapján jelennek meg. A cél az, hogy megakadályozza a hibák elhárítását a kiszolgáló feladatátvétele után, mivel a kiszolgáló számos kulcsfontosságú műveletét indítja el a tárolóban, mivel vannak adatbázisok az adott kiszolgálón. 

- Állítsa be a Key Vault erőforrás-zárolását annak szabályozására, hogy ki törölheti a kritikus erőforrást, és megelőzheti a véletlen vagy jogosulatlan törlést. További információ az [erőforrás-zárolásokról](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

- Naplózás és jelentéskészítés engedélyezése az összes titkosítási kulcson: a Key Vault olyan naplókat biztosít, amelyek könnyen beilleszthető más biztonsági információkba és az eseménykezelő eszközeibe. Az Operations Management Suite [log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) egy olyan szolgáltatásra mutat példát, amely már integrálva van.

- A titkosított adatbázisok magas rendelkezésre állásának biztosítása érdekében az egyes kiszolgálókat két, különböző régiókban található kulcstartóval kapcsolja össze, és ugyanazokat a kulcsfontosságú anyagokat kell tárolnia. Csak a Key vaultban lévő kulcs megjelölése a TDE-védővel megegyező régióban. A rendszer fogja használni

### <a name="recommendations-when-configuring-tde-protector"></a>Javaslatok a TDE-védő konfigurálásakor
- Őrizze meg a TDE Protector egy másolatát egy biztonságos helyen, vagy küldje el a letéti szolgáltatásnak. 

- Ha a kulcsot a Key vaultban hozza létre, hozzon létre egy kulcsos biztonsági mentést, mielőtt első alkalommal használja a kulcsot a AKV-ben. A biztonsági másolat csak Azure Key Vault lehet visszaállítható. További információ a [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) parancsról.

- Hozzon létre egy új biztonsági mentést, ha bármilyen változás történik a kulcsban (például a legfontosabb attribútumok, címkék, ACL-ek).

- A Key vaultban **megtartja a kulcs előző verzióit** a kulcsok elforgatásakor, így a régebbi adatbázisok biztonsági mentése visszaállítható. Ha a TDE-védőt módosítják egy adatbázisra vonatkozóan, az adatbázis régi biztonsági mentései **nem frissülnek** a legújabb TDE-oltalmazó használatára. A visszaállítás ideje alatt minden biztonsági mentéshez szükség van a TDE-védőre, amelyet a létrehozáskor titkosítottak. A kulcsok elforgatása elvégezhető a [transzparens adattitkosítás Protector a PowerShell használatával történő elforgatására](transparent-data-encryption-byok-azure-sql-key-rotation.md)vonatkozó utasítások követésével.

- A korábban használt kulcsok megtartása a AKV még a szolgáltatás által felügyelt kulcsokra váltás után is. Gondoskodik arról, hogy az adatbázis biztonsági másolatai visszaállíthatók legyenek a AKV-ben tárolt TDE-védelemmel.  A Azure Key Vaulttel létrehozott TDE-védőket fenn kell tartani, amíg az összes többi tárolt biztonsági másolat nem lett létrehozva a szolgáltatás által felügyelt kulcsokkal. A kulcsok helyreállítható biztonsági másolatait a [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)használatával végezheti el.

- Ha egy biztonsági incidens során el szeretné távolítani egy potenciálisan feltört kulcsot az adatvesztés kockázata nélkül, kövesse a [potenciálisan feltört kulcs eltávolítása](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)című szakasz lépéseit.

## <a name="inaccessible-tde-protector"></a>Elérhetetlen TDE-védő

Ha az transzparens adattitkosítás ügyfél által felügyelt kulcs használatára van konfigurálva, a TDE-védő folyamatos hozzáférésre van szükség ahhoz, hogy az adatbázis online maradjon. Ha a kiszolgáló nem fér hozzá az ügyfél által felügyelt TDE-védőhöz a AKV-ben, egy adatbázis akár 10 percen belül megtagadja az összes kapcsolatot a megfelelő hibaüzenettel, és nem *érhető*el az állapota. Az egyetlen olyan művelet, amely nem elérhető állapotban van, nem érhető el.

> [!NOTE]
> Ha az adatbázis egy időszakos hálózati leállás miatt nem érhető el, nincs szükség beavatkozásra, és az adatbázisok automatikusan újra online állapotba kerülnek.

A kulcshoz való hozzáférés visszaállítását követően az adatbázis visszahívása további időt és lépéseket igényel, amelyek a kulcshoz való hozzáférés és az adatbázisbeli adatmennyiség függvényében eltelt idő alapján változhatnak:

- Ha a kulcs-hozzáférés 8 órán belül helyreáll, az adatbázis a következő órában automatikusan meggyógyítható.

- Ha a kulcs-hozzáférés 8 óránál hosszabb idő elteltével helyreáll, az automatikus gyógyulás nem lehetséges, és az adatbázis visszaállítása jelentős időt vehet igénybe az adatbázis méretétől függően, és egy támogatási jegyet kell megnyitnia. Miután az adatbázis ismét online állapotba került, korábban konfigurálta a kiszolgálói szintű beállításokat, például a [feladatátvételi csoport](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) konfigurációját, az időponthoz tartozó visszaállítási előzményeket, a címkék pedig elvesznek. Ezért javasoljuk olyan értesítési rendszer megvalósítását, amely lehetővé teszi, hogy 8 órán belül azonosítsa és kezelje az alapul szolgáló kulcsfontosságú hozzáférési problémákat.

### <a name="accidental-tde-protector-access-revocation"></a>Véletlen TDE-védő hozzáférés visszavonása

Előfordulhat, hogy a Key vaulthoz megfelelő hozzáférési jogokkal rendelkező személy véletlenül letiltja a kiszolgáló hozzáférését a kulcshoz:

- a Key Vault *Get*, *wrapKey*, *unwrapKey* engedélyeinek visszavonása a kiszolgálóról

- a kulcs törlése

- a Key Vault törlése

- a Key Vault tűzfalszabály-szabályainak módosítása

- a kiszolgáló felügyelt identitásának törlése Azure Active Directory

További információ [az adatbázisok elérhetetlenné válásának gyakori okairól](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible).

## <a name="monitoring-of-the-customer-managed-tde"></a>Az ügyfél által felügyelt TDE figyelése

Az adatbázis állapotának figyeléséhez és a TDE-védő hozzáférésének elvesztése miatti riasztások engedélyezéséhez konfigurálja a következő Azure-szolgáltatásokat:
- [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview). Egy nem elérhető adatbázis, amely elvesztette a TDE-védőt, "nem érhető el" jelenik meg, miután megtagadták az adatbázishoz való első kapcsolódást.
- A [tevékenység naplója](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) , ha az ügyfél által felügyelt kulcstartóban lévő TDE-védőhöz való hozzáférés meghiúsul, a rendszer hozzáadja a bejegyzéseket a tevékenység naplójához.  Az eseményekhez tartozó riasztások létrehozása lehetővé teszi, hogy a lehető leghamarabb visszaállítsa a hozzáférést.
- A [csoportok](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) meghatározhatják, hogy az értesítések és a riasztások a beállításoknak megfelelően legyenek elküldve, például e-mail-/SMS-/leküldéses/hang-, Logic app-, ITSM-vagy Automation-Runbook.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Adatbázis biztonsági mentése és visszaállítása az ügyfél által felügyelt TDE

Ha egy adatbázis a Key Vault használatával titkosítva van a TDE, az újonnan létrehozott biztonsági másolatok is ugyanazzal a TDE-védővel lesznek titkosítva. A TDE-védő módosításakor az adatbázis régi biztonsági mentései **nem frissülnek** a legújabb TDE-oltalmazó használatára.

A Key Vault TDE-védővel titkosított biztonsági másolat visszaállításához ellenőrizze, hogy a fő anyag elérhető-e a célkiszolgálón. Ezért javasoljuk, hogy tartsa meg a TDE-védő összes korábbi verzióját a Key vaultban, így az adatbázis biztonsági mentése visszaállítható. 

> [!IMPORTANT]
> Egy adott kiszolgálón nem lehet egynél több TDE-védőt beállítani. Ez az a kulcs, amelynek a kulcsa az alapértelmezett TDE-védő legyen a Azure Portal panelen. Azonban több további kulcs is összekapcsolható egy kiszolgálóval anélkül, hogy TDE-védőként kellene megjelölni őket. Ezek a kulcsok nem használhatók a ADATTITKOSÍTÁSI kulcsot védelmére, de a biztonsági másolatból történő visszaállítás során használhatók, ha a biztonságimásolat-fájl a megfelelő ujjlenyomattal rendelkező kulccsal van titkosítva.

Ha a biztonsági mentés visszaállításához szükséges kulcs már nem érhető el a célkiszolgálón, a visszaállítási próbálkozáskor a következő hibaüzenet jelenik meg: "a célkiszolgáló `<Servername>` nem fér hozzá az \<timestamp #1 > és \<timestamp #2 > között létrehozott összes AKV URI azonosítóhoz. Próbálkozzon újra a művelettel az összes AKV URI visszaállítása után. "

Ennek enyhítéséhez futtassa a [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) parancsmagot a cél SQL Database logikai kiszolgáló vagy a [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) a célként felügyelt példányhoz, hogy visszaadja a rendelkezésre álló kulcsok listáját, és azonosítsa a hiányzókat. Annak érdekében, hogy az összes biztonsági másolat visszaállítható, győződjön meg arról, hogy a visszaállításhoz használt célkiszolgáló hozzáfér az összes szükséges kulcshoz. Ezeket a kulcsokat nem kell TDE-védőként megjelölni.

Ha többet szeretne megtudni a SQL Database biztonsági mentésének helyreállításáról, tekintse meg [Az Azure SQL Database helyreállítása](sql-database-recovery-using-backups.md)című témakört. Ha többet szeretne megtudni a SQL Data Warehouse biztonsági mentési helyreállításáról, tekintse meg a [Azure SQL Data Warehouse helyreállítása](../sql-data-warehouse/backup-and-restore.md)című témakört. SQL Server natív biztonsági mentése/visszaállítása felügyelt példánnyal: gyors útmutató [: adatbázis visszaállítása felügyelt példányra](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) 

További szempontok a naplófájlok számára: a biztonsági másolatba mentett naplófájlok továbbra is titkosítva maradnak az eredeti TDE-védővel, még akkor is, ha azt elforgatták, és az adatbázis mostantól új TDE-védőt használ.  A visszaállítás ideje alatt mindkét kulcsra szükség lesz az adatbázis visszaállításához.  Ha a naplófájl Azure Key Vaultban tárolt TDE-védőt használ, a kulcs visszaállításkor is szükséges lesz, még akkor is, ha az adatbázist úgy módosították, hogy a szolgáltatás által felügyelt TDE időközben is használhassa.

## <a name="high-availability-with-customer-managed-tde"></a>Magas rendelkezésre állás az ügyfél által felügyelt TDE

Olyan esetekben, amikor nincs konfigurálva geo-redundancia a kiszolgáló számára, javasoljuk, hogy konfigurálja úgy a kiszolgálót, hogy két különböző Key vaultot használjon két különböző régióban ugyanazzal a kulcsfontosságú anyaggal. A TDE-védő létrehozásával a-kiszolgálóval megegyező régióban található elsődleges Key Vault, a kiszolgálót pedig egy másik Azure-régióban lévő kulcstartóba klónozással lehet elérni, hogy a kiszolgáló hozzáférjen a második kulcstartóhoz, hogy az elsődleges Key Vault szakértelmet ience egy leállás, miközben az adatbázis működik. 

Használja a Backup-AzKeyVaultKey parancsmagot a kulcs titkosított formátumban való lekéréséhez az elsődleges Key vaultból, majd használja a Restore-AzKeyVaultKey parancsmagot, és adja meg a második régióban található kulcstartót a kulcs klónozásához. Másik lehetőségként a Azure Portal is használhatja a kulcs biztonsági mentéséhez és visszaállításához. Az se más régióban található másodlagos Key vaultban lévő kulcs nem lehet TDE-védőként megjelölve, és nem is engedélyezett.

 Ha van olyan leállás, amely hatással van az elsődleges Key vaultra, és csak ezt követően, a rendszer automatikusan átvált a másik csatolt kulcsra ugyanazzal az ujjlenyomattal a másodlagos kulcstartóban, ha van ilyen. Vegye figyelembe, hogy ha a TDE-védelem visszavont hozzáférési jogosultságok miatt nem érhető el, vagy mert a kulcs vagy kulcstartó törölve van, mivel előfordulhat, hogy az ügyfél szándékosan szeretné korlátozni a kiszolgáló hozzáférését a kulcshoz.

![Egy kiszolgáló, HA](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Geo-DR és ügyfél által felügyelt TDE

Az [aktív geo-replikálási](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication) és [feladatátvételi csoportok](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) esetében az összes érintett kiszolgálónak külön kulcstartóra van szüksége, amelynek a kiszolgálóval azonos Azure-régióban kell lennie. Az ügyfél feladata, hogy megőrizze a kulcsfontosságú anyagokat a kulcstartók között, így a Geo-másodlagos szinkronban van, és átveheti ugyanazt a kulcsot a helyi kulcstartóból, ha az elsődleges elérhetetlenné válik a régió meghibásodása miatt, és a feladatátvétel aktiválva van. . Akár négy formátumú másodlagos zónák is konfigurálható, és a láncolás (formátumú másodlagos zónák of formátumú másodlagos zónák) nem támogatott.

Ha el szeretné kerülni, hogy a kulcsfontosságú anyagok hiányában a Geo-replikáció létrehozásakor vagy során problémák kerüljenek, fontos, hogy az ügyfél által felügyelt TDE konfigurálásakor kövesse ezeket a szabályokat:

- Az összes érintett kulcstárolónak azonos tulajdonságokkal kell rendelkeznie, és ugyanazokat a hozzáférési jogokat kell megadnia a megfelelő kiszolgálókhoz.

- Az összes érintett kulcstartónak azonos kulcsfontosságú anyagot kell tartalmaznia. Ez nem csak a jelenlegi TDE-védőre vonatkozik, hanem a biztonságimásolat-fájlokban esetlegesen használt összes korábbi TDE-védelemmel.

- A TDE-védő kezdeti beállítását és rotációját a másodlagos elsőn, majd az elsődlegesen kell elvégezni.

![Feladatátvételi csoportok és geo-Dr](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

A feladatátvétel teszteléséhez kövesse az [aktív geo-replikáció áttekintése](sql-database-geo-replication-overview.md)című témakör lépéseit. Rendszeres időközönként meg kell győződni arról, hogy az SQL mindkét kulcstartóra vonatkozó hozzáférési engedélyei megmaradtak.

## <a name="next-steps"></a>Következő lépések

A következő PowerShell-parancsfájlokat is érdemes megtekinteni az ügyfél által felügyelt TDE tartozó általános műveletekhez:

- [A SQL Database transzparens adattitkosítás Protector elforgatása a PowerShell használatával](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [Transzparens adattitkosítás (TDE) Protector eltávolítása a SQL Database PowerShell használatával](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [Felügyelt példányok transzparens adattitkosítás kezelése a saját kulccsal a PowerShell használatával](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)
