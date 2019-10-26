---
title: TDE – Azure Key Vault integráció vagy Bring Your Own Key (BYOK) – Azure SQL Database | Microsoft Docs
description: Bring Your Own Key (BYOK) a transzparens adattitkosítás (TDE) támogatása a SQL Database és az adattárház Azure Key Vaultével. TDE a BYOK áttekintése, előnyei, hogyan működik, megfontolások és javaslatok.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 07/18/2019
ms.openlocfilehash: 35e768e15aae13376ca6663ed5ca5109cb0a159b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893536"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-keys-in-azure-key-vault-bring-your-own-key-support"></a>Azure SQL transzparens adattitkosítás az ügyfél által felügyelt kulcsokkal Azure Key Vault: Bring Your Own Key támogatás

A Azure Key Vault Integration [transzparens adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) lehetővé teszi az adatbázis-titkosítási kulcs (adattitkosítási kulcsot) titkosítását az ügyfél által felügyelt, TDE Protector nevű aszimmetrikus kulccsal. Ezt általában a transzparens adattitkosítás Bring Your Own Key (BYOK) támogatása is említi.  A BYOK-forgatókönyvben a TDE-védőt az Azure felhőalapú külső kulcs-felügyeleti rendszere tárolja az ügyfél által birtokolt és felügyelt [Azure Key Vaultban](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault). A TDE-védőt a Key Vault [generálhatja](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates) , vagy [áthelyezheti](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) a Key vaultba egy helyszíni HSM-eszközről. Az adatbázis rendszerindító oldalán tárolt TDE-ADATTITKOSÍTÁSI kulcsot a rendszer titkosítja és visszafejti a Azure Key Vaultban tárolt TDE-védő által, amelyet soha nem hagy.  SQL Database engedélyeket kell adni az ügyfél által birtokolt kulcstartó számára a ADATTITKOSÍTÁSI kulcsot visszafejtéséhez és titkosításához. Ha a logikai SQL Server-kiszolgáló és a kulcstartó engedélyeit visszavonják, egy adatbázis elérhetetlenné válik, a rendszer megtagadja a kapcsolatokat, és az összes adattal titkosítva lesz. Azure SQL Database esetében a TDE-védő a logikai SQL Server szintjén van beállítva, és az adott kiszolgálóhoz társított összes adatbázis örökli. Az [Azure SQL felügyelt példányai](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance)esetében a TDE-védő a példány szintjén van beállítva, és az adott példányon található összes *titkosított* adatbázis örökli. A *kiszolgáló* kifejezés a jelen dokumentumon belül a kiszolgáló és a példányra is vonatkozik, kivéve, ha másként van megadva.

> [!NOTE]
> A Azure SQL Database felügyelt példányhoz tartozó Azure Key Vault Integration (Bring Your Own Key) transzparens adattitkosítás előzetes verzióban érhető el.


A Azure Key Vault-integrációval rendelkező TDE segítségével a felhasználók vezérelhetik a kulcsfontosságú felügyeleti feladatokat, beleértve a kulcsok elforgatását, a Key Vault engedélyeit és a biztonsági mentéseket, valamint lehetővé teszik a naplózást/jelentéskészítést az összes TDE-védelemmel Azure Key Vault funkcióval. Key Vault biztosítja a központi kulcskezelő szolgáltatásokat, kihasználja a jól felügyelt hardveres biztonsági modulok (HSM-EK) használatát, és lehetővé teszi a feladatok elkülönítését a kulcsok és az adatok felügyelete között a biztonsági szabályzatoknak való megfelelés elősegítése érdekében.  

A Azure Key Vault integrációs TDE a következő előnyöket nyújtja:

- Nagyobb átláthatóság és részletes szabályozás a TDE-védő önálló kezelésének lehetőségével
- Az engedélyek visszavonásának lehetősége, hogy az adatbázis nem érhető el.
- A TDE-védők (más, más Azure-szolgáltatásokban használt kulcsok és titkok) központi kezelése Key Vault
- A kulcs-és adatkezelési feladatok elkülönítése a szervezeten belül a feladatok elkülönítésének támogatásához
- Nagyobb megbízhatóság a saját ügyfeleitől, mivel a Key Vault úgy lett kialakítva, hogy a Microsoft ne tekintse meg vagy ne bontsa ki a titkosítási kulcsokat.
- A kulcsok forgásának támogatása

> [!IMPORTANT]
> Azoknál a szolgáltatás által felügyelt TDE, akik Key Vault szeretnék használni, a TDE továbbra is engedélyezve marad a Key Vaultban lévő TDE-oltalmazóra váltás során. Nincs leállás, és nem lesz újra titkosítva az adatbázisfájlok. Ha egy szolgáltatás által felügyelt kulcsról Key Vault kulcsra vált át, csak az adatbázis-titkosítási kulcs (ADATTITKOSÍTÁSI kulcsot) újratitkosítására van szükség, amely gyors és online művelet.

## <a name="how-does-tde-with-azure-key-vault-integration-support-work"></a>Hogyan működik a TDE Azure Key Vault integrációs támogatással

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

![A kiszolgáló hitelesítése a Key Vault](./media/transparent-data-encryption-byok-azure-sql/tde-byok-server-authentication-flow.PNG)

Ha a TDE először a Key Vault TDE-védő használatára van konfigurálva, akkor a kiszolgáló az egyes TDE-kompatibilis adatbázisok ADATTITKOSÍTÁSI kulcsot küldi el, hogy egy sortörési kérést Key Vault. Key Vault visszaadja a titkosított adatbázis titkosítási kulcsát, amely a felhasználói adatbázisban tárolódik.  

> [!IMPORTANT]
> Fontos megjegyezni, hogy **Ha a TDE-védőt Azure Key Vault tárolják, soha nem hagyja el a Azure Key Vault**. A kiszolgáló csak a Key Vaulton belüli kulcs-visszaküldési kérelmeket küldi el a TDE-védőnek, és **soha nem fér hozzá vagy gyorsítótárazza a TDE-védőt**. A Key Vault rendszergazdája bármikor visszavonhatja a kiszolgáló Key Vault engedélyeit, ebben az esetben a rendszer megtagadja az adatbázissal létesített összes kapcsolatot.

## <a name="guidelines-for-configuring-tde-with-azure-key-vault"></a>Irányelvek a TDE konfigurálásához a Azure Key Vault

### <a name="general-guidelines"></a>Általános irányelvek

- Győződjön meg arról, Azure Key Vault és Azure SQL Database/felügyelt példány ugyanazon a bérlőn lesz.  A több-bérlős kulcstartó és a kiszolgáló közötti interakciók **nem támogatottak**.
- Ha a bérlők áthelyezését tervezi, a TDE a AKV-be újra kell konfigurálni, további információ az [erőforrások áthelyezéséről](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- A TDE a Azure Key Vault-nal való konfigurálásakor fontos figyelembe venni a Key vaultban elhelyezett terhelést az ismételt becsomagolási/kicsomagolási műveletekkel. Mivel például az SQL Database-kiszolgálóhoz társított összes adatbázis ugyanazt a TDE-védőt használja, az adott kiszolgáló feladatátvétele annyi kulcsfontosságú műveletet indít el a tárolón, mint a kiszolgáló adatbázisai. Tapasztalataink és dokumentált [Key Vault szolgáltatási korlátaink](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits)alapján azt javasoljuk, hogy legalább 500 Standard/általános célú vagy 200 prémium/üzletileg kritikus adatbázist rendeljen egyetlen előfizetésben egyetlen Azure Key Vault, hogy konzisztensen magas szintű legyen rendelkezésre állás a TDE-védő a tárolóban való elérésekor.
- Ajánlott: őrizze meg a TDE-védő másolatát a helyszínen.  Ehhez egy HSM-eszközre van szükség, amely helyileg hozza létre a TDE-védőt, valamint egy kulcsfontosságú, a TDE-védő helyi példányának tárolására szolgáló letéti rendszerét.  Megtudhatja [, hogyan vihet át egy kulcsot egy helyi HSM-ből a Azure Key Vaultba](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).


### <a name="guidelines-for-configuring-azure-key-vault"></a>Irányelvek a Azure Key Vault konfigurálásához

- Hozzon létre egy Key vaultot, amely lehetővé teszi a helyreállítható [törlést](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) és a kiürítési védelmet, hogy a véletlen kulcs – vagy a Key Vault – törlése esetén védve legyen az adatvesztéstől. A Key vaulton a [CLI](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete) vagy a [PowerShell](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) használatával kell engedélyeznie a "Soft Delete" tulajdonságot (ez a lehetőség nem érhető el az AKV-portálon, de az Azure SQL-ben szükséges):  
  - A Soft Deleted-erőforrások egy meghatározott időtartamra, 90 napra vannak megőrizve, kivéve, ha azokat helyreállítják vagy törölték.
  - A **helyreállítás** és **Törlés** műveletekhez saját engedélyek tartoznak a Key Vault hozzáférési házirendjében.
- Állítson be egy erőforrás-zárolást a kulcstartóban annak vezérléséhez, hogy ki törölheti ezt a kritikus erőforrást, és segít a véletlen vagy jogosulatlan törlés megelőzésében.  [További információ az erőforrás-zárolásokról](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

- Adja meg a SQL Database-kiszolgáló hozzáférését a Key vaulthoz a saját Azure Active Directory (Azure AD) identitásának használatával.  A portál felhasználói felületének használatakor a rendszer automatikusan létrehozza az Azure AD-identitást, és a Key Vault hozzáférési engedélyeit a kiszolgáló megkapja.  Ha a PowerShell használatával konfigurálja a TDE-t a BYOK-mel, az Azure AD-identitást létre kell hozni, és a befejezést ellenőrizni kell. Lásd: a TDE és a [BYOK](transparent-data-encryption-byok-azure-sql-configure.md) konfigurálása, valamint a TDE és a [BYOK konfigurálása a felügyelt példányhoz](https://aka.ms/sqlmibyoktdepowershell) részletes útmutatást nyújt a PowerShell használatakor.

   > [!NOTE]
   > Ha az Azure AD-identitást **véletlenül törölték, vagy a kiszolgáló engedélyeit** a Key Vault hozzáférési házirendjével vagy véletlenül a kiszolgáló egy másik bérlőhöz való áthelyezésével vonja vissza, a kiszolgáló elveszti a kulcstartóhoz való hozzáférést, és TDE a titkosított adatbázisokat. a nem érhető el, és a bejelentkezések el lesznek tiltva, amíg a logikai kiszolgáló Azure AD-identitása és engedélyei nem lettek visszaállítva.  

- Ha tűzfalakat és virtuális hálózatokat használ Azure Key Vaultokkal, akkor engedélyeznie kell a megbízható Microsoft-szolgáltatások számára a tűzfal megkerülését. Válassza az Igen lehetőséget.

   > [!NOTE]
   > Ha a TDE titkosított SQL-adatbázisok elvesztik a kulcstartóhoz való hozzáférést, mert nem tudják megkerülni a tűzfalat, az adatbázisok nem lesznek elérhetők, és a bejelentkezések el lesznek tiltva, amíg a tűzfal megkerülésére vonatkozó engedélyek vissza nem állnak.

- Naplózás és jelentéskészítés engedélyezése az összes titkosítási kulcson: Key Vault olyan naplókat biztosít, amelyek könnyen beilleszthető más biztonsági információ-és eseménykezelő (SIEM) eszközökbe. Az Operations Management Suite (OMS) [log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) egy olyan szolgáltatásra mutat példát, amely már integrálva van.
- A titkosított adatbázisok magas rendelkezésre állásának biztosításához konfigurálja az egyes SQL Database-kiszolgálókat két, különböző régiókban található Azure Key Vaulttal.


### <a name="guidelines-for-configuring-the-tde-protector-asymmetric-key"></a>Irányelvek a TDE-védő konfigurálásához (aszimmetrikus kulcs)

- Helyi HSM-eszközön helyben hozza létre a titkosítási kulcsot. Győződjön meg arról, hogy ez egy aszimmetrikus, RSA 2048 vagy RSA HSM 2048 kulcs, hogy Azure Key Vault tárolható legyen.
- A kulcs zálogba való beszolgáltatása.  
- Importálja Azure Key Vaultre a titkosítási kulcs fájlját (. pfx,. byok vagy. backup).

   > [!NOTE]
   > Tesztelési célból létrehozhat egy Azure Key Vault tartalmazó kulcsot, azonban ez a kulcs nem helyezhető letétbe, mert a titkos kulcs soha nem hagyhatja el a kulcstartót.  Az élesben tárolt adatmennyiségek titkosításához mindig biztonsági mentést és letéti kulcsot kell használni, mivel a kulcs elvesztése (a kulcstartóban, a lejárat során stb.) állandó adatvesztést eredményez.

- Ha lejárati dátummal rendelkező kulcsot használ – lejárati figyelmeztetési rendszer bevezetése a kulcs lejárata előtti elforgatásához: **Ha a kulcs lejár, a titkosított adatbázisok elvesztik a TDE-védőhöz való hozzáférést,** és nem lesznek elérhetők, és a rendszer minden bejelentkezést megtagad, amíg a kulcs egy új kulccsal lett elforgatva, és a logikai SQL-kiszolgáló új kulcsaként és alapértelmezett TDE-védőként van kijelölve.
- Győződjön meg arról, hogy a kulcs engedélyezve van, és rendelkezik engedéllyel a *Get*, a *wrap Key*és a *dewrap Key* művelet végrehajtásához.
- Hozzon létre egy Azure Key Vault kulcs biztonsági mentést, mielőtt első alkalommal használja a kulcsot a Azure Key Vaultban. További információ a [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) parancsról.
- Hozzon létre egy új biztonsági mentést, ha bármilyen változás történik a kulcsban (például ACL-ek hozzáadása, Címkék hozzáadása, kulcs attribútumainak hozzáadása).
- A Key vaultban **megtartja a kulcs előző verzióit** a kulcsok elforgatásakor, így a régebbi adatbázisok biztonsági mentése visszaállítható. Ha a TDE-védőt módosítják egy adatbázisra vonatkozóan, az adatbázis régi biztonsági mentései **nem frissülnek** a legújabb TDE-oltalmazó használatára.  Minden biztonsági mentéshez szükség van a TDE-védőre, amelyet visszaállításkor hoztak létre. A kulcsok elforgatása elvégezhető a [transzparens adattitkosítás Protector a PowerShell használatával történő elforgatására](transparent-data-encryption-byok-azure-sql-key-rotation.md)vonatkozó utasítások követésével.
- A szolgáltatás által felügyelt kulcsokra való váltás után tartsa meg az összes korábban használt kulcsot a Azure Key Vault.  Ez biztosítja, hogy az adatbázis biztonsági másolatai visszaállíthatók a Azure Key Vaultban tárolt TDE-védelemmel.  A Azure Key Vaulttel létrehozott TDE-védőket fenn kell tartani, amíg az összes tárolt biztonsági másolat nem lett létrehozva a szolgáltatás által felügyelt kulcsokkal.  
- A kulcsok helyreállítható biztonsági másolatait a [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)használatával végezheti el.
- Ha egy biztonsági incidens során el szeretné távolítani egy potenciálisan feltört kulcsot az adatvesztés kockázata nélkül, kövesse a [potenciálisan feltört kulcs eltávolítása](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)című témakör lépéseit.

### <a name="guidelines-for-monitoring-the-tde-with-azure-key-vault-configuration"></a>Irányelvek a TDE Azure Key Vault-konfigurációval való figyeléséhez

Ha a logikai SQL Server nem fér hozzá a Azure Key Vault ügyfél által felügyelt TDE-oltalmazóhoz, az adatbázis megtagadja az összes kapcsolatot, és nem érhető el a Azure Portal.  Ennek leggyakoribb okai a következők:
- A Key vaultot véletlenül törölte vagy egy tűzfal mögött
- Key Vault-kulcs véletlenül törölve, letiltva vagy lejárt
- A logikai SQL Server példány AppId véletlenül törölve
- A logikai SQL Server példány AppId visszavont kulcs-specifikus engedélyek

 > [!NOTE]
 > Ha az ügyfél által felügyelt TDE-védő hozzáférését 48 órán belül visszaállítja, az adatbázis önmagát meggyógyítja és automatikusan online állapotba kerül.  Ha az adatbázis egy időszakos hálózati leállás miatt nem érhető el, nincs szükség beavatkozásra, és az adatbázisok automatikusan újra online állapotba kerülnek.
  
- A meglévő konfigurációk hibaelhárításával kapcsolatos további információkért lásd: [TDE hibaelhárítása](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde)

- Az adatbázis állapotának figyeléséhez és a TDE-védő hozzáférésének elvesztése miatti riasztások engedélyezéséhez konfigurálja a következő Azure-szolgáltatásokat:
    - [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview). Egy nem elérhető adatbázis, amely elvesztette a TDE-védőt, "nem érhető el" jelenik meg, miután megtagadták az adatbázishoz való első kapcsolódást.
    - A [tevékenység naplója](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) , ha az ügyfél által felügyelt kulcstartóban lévő TDE-védőhöz való hozzáférés meghiúsul, a rendszer hozzáadja a bejegyzéseket a tevékenység naplójához.  Az eseményekhez tartozó riasztások létrehozása lehetővé teszi, hogy a lehető leghamarabb visszaállítsa a hozzáférést.
    - A [csoportok](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) meghatározhatják, hogy az értesítések és a riasztások a beállításoknak megfelelően legyenek elküldve, például e-mail-/SMS-/leküldéses/hang-, Logic app-, ITSM-vagy Automation-Runbook.
    

## <a name="high-availability-geo-replication-and-backup--restore"></a>Magas rendelkezésre állás, földrajzi replikálás és biztonsági mentés/visszaállítás

### <a name="high-availability-and-disaster-recovery"></a>Magas rendelkezésre állás és vészhelyreállítás

A Azure Key Vault magas rendelkezésre állásának konfigurálása az adatbázis és a SQL Database kiszolgáló konfigurációjától függ, és két különböző eset esetében az ajánlott konfiguráció.  Az első eset egy önálló adatbázis vagy SQL Database-kiszolgáló, amelynek nincs beállított földrajzi redundancia.  A második eset a feladatátvételi csoportokkal vagy a Geo-redundanciával konfigurált adatbázis vagy SQL Database-kiszolgáló, ahol biztosítani kell, hogy az egyes geo-redundáns másolatok helyi Azure Key Vault a feladatátvételi csoporton belül, így biztosítva a földrajzi feladatátvételek működését.

Az első esetben, ha magas rendelkezésre állásra van szüksége egy adatbázishoz, és SQL Database a kiszolgálót, amelynek nincs beállított földrajzi redundancia, erősen ajánlott a kiszolgálót két különböző kulcstartó használatára beállítani két különböző régióban ugyanazzal a kulcsfontosságú anyagokkal. Ezt úgy teheti meg, hogy létrehoz egy TDE-védőt a SQL Database-kiszolgálóval megegyező régióban található elsődleges Key Vault együtt, és egy másik Azure-régióban lévő kulcstartóba klónozotta a kulcsot, így a kiszolgáló egy második kulcstartóhoz fér hozzá, hogy az elsődleges a Key Vault leáll, miközben az adatbázis működik. Használja a Backup-AzKeyVaultKey parancsmagot a kulcs titkosított formátumban való lekéréséhez az elsődleges Key vaultból, majd használja a Restore-AzKeyVaultKey parancsmagot, és adja meg a második régióban a kulcstartót.

![Egykiszolgálós HA és nem geo-Dr](./media/transparent-data-encryption-byok-azure-sql/SingleServer_HA_Config.PNG)

## <a name="how-to-configure-geo-dr-with-azure-key-vault"></a>Geo-DR konfigurálása Azure Key Vault

A titkosított adatbázisok magas rendelkezésre állásának fenntartása érdekében szükség van a redundáns Azure Key Vaultok konfigurálására a meglévő vagy a kívánt SQL Database feladatátvételi csoportok vagy aktív földrajzi replikálási példányok alapján.  Mindegyik geo-replikált kiszolgálónak külön kulcstartóra van szüksége, amelynek az azonos Azure-régióban található kiszolgálóval kell rendelkeznie. Ha egy adott régióban áramkimaradás miatt egy elsődleges adatbázis elérhetetlenné válik, és a feladatátvétel aktiválódik, a másodlagos adatbázis átveheti a másodlagos kulcstartót.

Földrajzilag replikált Azure SQL-adatbázisok esetén a következő Azure Key Vault konfiguráció szükséges:

- Egy elsődleges adatbázis, amely egy, a régióban található kulcstartóval és egy másodlagos adatbázissal rendelkezik.
- Legalább egy másodlagos követelmény, legfeljebb négy formátumú másodlagos zónák támogatott.
- A formátumú másodlagos zónák (láncolás) formátumú másodlagos zónák nem támogatottak.

A következő szakasz részletesebben ismerteti a beállítási és konfigurációs lépéseket.

### <a name="azure-key-vault-configuration-steps"></a>Azure Key Vault konfigurációs lépések

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) telepítése
- Hozzon létre két Azure Key Vaultot két különböző régióban a [PowerShell használatával, hogy engedélyezze a "Soft-Delete" tulajdonságot](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) a kulcstartókban (ez a lehetőség nem érhető el az AKV-portálon, de az SQL-hez szükséges).
- Mindkét Azure Key Vaultnak ugyanabban az Azure geo-ben elérhető két régióban kell lennie ahhoz, hogy a kulcsok biztonsági mentése és helyreállítása működjön.  Ha az SQL geo-DR követelményeinek kielégítéséhez két kulcstárolóra van szüksége a különböző térségek, kövesse a [BYOK folyamatot](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) , amely lehetővé teszi a kulcsok importálását a helyszíni HSM-ből.
- Hozzon létre egy új kulcsot az első Key vaultban:  
  - RSA/RSA-HSM 2048 kulcs
  - Nincs lejárati dátum
  - A kulcs engedélyezve van, és engedéllyel rendelkezik a Get, a wrap Key és a dewrap Key művelet végrehajtásához
- Az elsődleges kulcs biztonsági mentése és a kulcs visszaállítása a második kulcstartóra.  Lásd: [BackupAzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) és [Restore-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey).

### <a name="azure-sql-database-configuration-steps"></a>Azure SQL Database konfigurációs lépések

Az alábbi konfigurációs lépések eltérnek attól, hogy az új SQL-telepítéssel vagy egy már meglévő SQL geo-DR üzemelő példánytal kell-e kezdeni.  Először egy új központi telepítés konfigurációs lépéseit vázoljuk fel, majd elmagyarázjuk, hogyan rendelhet hozzá Azure Key Vault tárolt TDE-védőket egy meglévő központi telepítéshez, amelyhez már tartozik egy geo-DR hivatkozás.

**Új központi telepítés lépései**:

- Hozza létre a két SQL Database kiszolgálót ugyanabban a két régióban, mint a korábban létrehozott kulcstartókat.
- Válassza ki a SQL Database Server TDE ablaktáblát, és minden SQL Database-kiszolgálóhoz:  
  - Válassza ki a AKV ugyanabban a régióban
  - Válassza ki a TDE-védőként használandó kulcsot – minden kiszolgáló a TDE-védő helyi példányát fogja használni.
  - Ez a portálon létrehoz egy [AppID](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) a SQL Database-kiszolgálóhoz, amely a SQL Database-kiszolgáló engedélyek hozzárendelésére szolgál a kulcstartó eléréséhez – ne törölje ezt az identitást. A hozzáférés visszavonható úgy, hogy a SQL Database-kiszolgáló számára eltávolítja a Azure Key Vault engedélyeit, és a SQL Database kiszolgáló engedélyek hozzárendelésére szolgál a kulcstartó eléréséhez.
- Hozza létre az elsődleges adatbázist.
- A forgatókönyv végrehajtásához kövesse az [aktív geo-replikációs útmutatást](sql-database-geo-replication-overview.md) , ez a lépés a másodlagos adatbázist fogja létrehozni.

![Feladatátvételi csoportok és geo-Dr](./media/transparent-data-encryption-byok-azure-sql/Geo_DR_Config.PNG)

> [!NOTE]
> Fontos, hogy ugyanazokat a TDE-védőket is megmutassa mindkét kulcstartóban, mielőtt folytatná az adatbázisok közötti földrajzi kapcsolat létesítését.

**Egy meglévő SQL-adatbázis lépései a Geo-Dr üzembe helyezéssel**:

Mivel a SQL Database-kiszolgálók már léteznek, és az elsődleges és a másodlagos adatbázis már hozzá van rendelve, a Azure Key Vault konfigurálásának lépéseit a következő sorrendben kell végrehajtani:

- Kezdje a másodlagos adatbázist futtató SQL Database kiszolgálóval:
  - Az ugyanabban a régióban található kulcstartó kiosztása
  - A TDE-védő kiosztása
- Most nyissa meg az elsődleges adatbázist futtató SQL Database-kiszolgálót:
  - Válassza ki a másodlagos ADATBÁZIShoz használt TDE-védőt.

![Feladatátvételi csoportok és geo-Dr](./media/transparent-data-encryption-byok-azure-sql/geo_DR_ex_config.PNG)

> [!NOTE]
> Amikor a kulcstartót a kiszolgálóhoz rendeli, fontos, hogy a másodlagos kiszolgálóval induljon el.  A második lépésben rendelje hozzá a Key vaultot az elsődleges kiszolgálóhoz, és frissítse a TDE-védőt, a Geo-DR hivatkozás továbbra is működni fog, mivel ezen a ponton a replikált adatbázis által használt TDE-védő mindkét kiszolgáló számára elérhető.

Mielőtt a TDE-t az ügyfél által felügyelt kulcsokkal engedélyezzük a Azure Key Vault egy SQL Database geo-DR forgatókönyvhöz, fontos, hogy ugyanazon a régióban két Azure-kulcstartót hozzon létre és őrizzen meg, amelyek SQL Database geo-replikáláshoz lesznek használva.  Az "azonos tartalom" kifejezés azt jelenti, hogy mindkét kulcstárolónak tartalmaznia kell ugyanazon TDE-védő (k) másolatát, hogy mindkét kiszolgáló hozzáférhessen az összes adatbázis által használt TDE-védelemmel.  A jövőben meg kell őrizni mindkét kulcstartót, ami azt jelenti, hogy a TDE-védelemmel ellátott példányokat kell tartalmazniuk a kulcsok elforgatása után, meg kell őrizni a naplófájlok vagy biztonsági másolatok régi verzióit, a TDE-védelemmel együtt kell tartani a kulcs tulajdonságait a tárolóknak ugyanazokat az SQL-hozzáférési engedélyeket kell megtartaniuk.  

Kövesse az [aktív geo-replikáció áttekintése](sql-database-geo-replication-overview.md) című témakör lépéseit a feladatátvétel teszteléséhez és elindításához, amelyet rendszeresen kell elvégezni, hogy meggyőződjön arról, hogy az SQL mindkét kulcstartóra vonatkozó hozzáférési engedélyei megmaradtak.

### <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

Ha egy adatbázis a Key Vault használatával titkosítva van a TDE, a rendszer minden létrehozott biztonsági mentést ugyanazzal a TDE-védővel is titkosít.

A Key Vault TDE-védővel titkosított biztonsági másolat visszaállításához győződjön meg arról, hogy a kulcs anyaga még mindig az eredeti kulcs neve alatt található az eredeti tárolóban. Ha a TDE-védőt módosítják egy adatbázisra vonatkozóan, az adatbázis régi biztonsági mentései **nem** frissülnek a legújabb TDE-oltalmazó használatára. Ezért javasoljuk, hogy tartsa meg a TDE-védő összes korábbi verzióját Key Vaultban, így az adatbázis biztonsági mentése visszaállítható.

Ha a biztonsági mentés visszaállításához szükséges kulcs már nem található meg az eredeti kulcstartóban, a következő hibaüzenet jelenik meg: "a célkiszolgáló `<Servername>` nem fér hozzá az \<timestamp #1 > és \<timestamp #2 közötti összes AKV URI-hoz. > . Próbálkozzon újra a művelettel az összes AKV URI visszaállítása után. "

Ennek enyhítéséhez futtassa a [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) parancsmagot, hogy visszaadja a kiszolgálóhoz hozzáadott Key Vault kulcsok listáját (kivéve, ha egy felhasználó törölte őket). Az összes biztonsági másolat visszaállításához ellenőrizze, hogy a biztonsági mentés célkiszolgálón van-e hozzáférése az összes kulcshoz.

```powershell
Get-AzSqlServerKeyVaultKey `
  -ServerName <LogicalServerName> `
  -ResourceGroup <SQLDatabaseResourceGroupName>
```

Ha többet szeretne megtudni a SQL Database biztonsági mentésének helyreállításáról, tekintse meg [Az Azure SQL Database helyreállítása](sql-database-recovery-using-backups.md)című témakört. Ha többet szeretne megtudni a SQL Data Warehouse biztonsági mentési helyreállításáról, tekintse meg a [Azure SQL Data Warehouse helyreállítása](../sql-data-warehouse/backup-and-restore.md)című témakört.

További szempontok a naplófájlok biztonsági mentéséről: a biztonsági másolatba mentett naplófájlok továbbra is az eredeti TDE titkosítva maradnak, még akkor is, ha a TDE-védőt elforgatták, és az adatbázis mostantól egy új TDE-védőt használ.  A visszaállítás ideje alatt mindkét kulcsra szükség lesz az adatbázis visszaállításához.  Ha a naplófájl Azure Key Vaultban tárolt TDE-védőt használ, a kulcs visszaállításkor is szükséges lesz, még akkor is, ha az adatbázist úgy módosították, hogy a szolgáltatás által felügyelt TDE időközben is használhassa.
