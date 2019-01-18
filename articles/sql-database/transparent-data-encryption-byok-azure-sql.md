---
title: TDE - Bring Your Own Key (BYOK) – az Azure SQL Database |} A Microsoft Docs
description: Bring Your Own Key (BYOK) támogatja a transzparens adattitkosítás (TDE) az Azure Key Vault az SQL Database és a Data warehouse-bA. TDE BYOK áttekintése, előnyeit, hogyan működik, szempontokat és javaslatokat.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 01/17/2019
ms.openlocfilehash: 60c7483e698a07fcf86438798f6bb5013a7417ce
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54391141"
---
# <a name="azure-sql-transparent-data-encryption-bring-your-own-key-support"></a>Az Azure SQL transzparens adattitkosítás: Bring Your Own Key-támogatás

Bring Your Own Key (BYOK) támogatása [transzparens adattitkosítási (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) lehetővé teszi, hogy az adatbázis-titkosítási kulcs (Adattitkosítási) titkosítást a TDE-Védőhöz nevű aszimmetrikus kulccsal.  A TDE-Védőhöz tárolja az Ön kezében [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), az Azure-alapú külső kulcs kezelési rendszer. Az Azure Key Vault egy első kulcskezelő szolgáltatás, amellyel a TDE integrálva van a BYOK támogatása. A TDE adattitkosítási kulcsot, az adatbázis rendszerindító oldal található van titkosítva, és a TDE-védőhöz visszafejteni. A TDE-Védőhöz az Azure Key Vaultban tárolja, és sosem hagyja el a key vaultban. Ha visszavonja a key vaultban a kiszolgáló elérését, egy adatbázis nem lehet visszafejteni és a memóriába. Az Azure SQL Database a TDE-védőhöz a logikai kiszolgáló szintjén van beállítva, és adott kiszolgálóhoz tartozó összes adatbázis öröklik. Az Azure SQL felügyelt példánya, a TDE-védőhöz van beállítva, a példány szintjén, és azt az összes örökölt *titkosított* adatbázisokat azon a példányon. Az előfizetési időszak *kiszolgáló* mind a kiszolgáló és példány ebben a dokumentumban hivatkozik, hacsak másként.

BYOK-támogatással felhasználók szabályozhatják a legfontosabb felügyeleti tevékenységek, például a kulcsok cseréjét, a key vault-engedélyek törlése a kulcsok, valamint az Azure Key Vault szolgáltatással minden TDE védő naplózás és jelentéskészítés engedélyezéséhez. A Key Vault központi kulcskezelési biztosít, szorosan figyelt hardveres biztonsági modulokban (HSM) használ, és lehetővé teszi a feladatkörök kulcsok kezelését és az adatok között, amelyek az előírásoknak való megfelelőséget.  

A BYOK TDE az alábbi előnyöket nyújtja:

- Felhőköltségeit átláthatóan és szabályozható nőtt lehetővé teszi a TDE-védőhöz helyi kezelése
- A TDE-protectors (valamint a más kulcsok és más Azure-szolgáltatásokhoz használt titkos kulcsok) központi felügyeleti üzemeltetnie őket a Key Vaultban
- Kulcs és az adatok a szervezeten belül, a feladatkörök elkülönítésének támogatásához felügyeleti feladatkörök szétválasztása
- Nagyobb megbízhatóság a saját ügyfelekről, mivel a Key Vault célja, hogy a Microsoft nem tekintheti meg és bontsa ki a titkosítási kulcs.
- Kulcsrotálás támogatása

> [!IMPORTANT]
> Azok számára, ki szeretné elindítani a Key vault szolgáltatás által kezelt TDE használatával TDE továbbra is engedélyezni a TDE-védőhöz, a Key Vaultban való váltás folyamata során. Nincs nem nincs állásidő és nem az adatbázisfájlok újbóli titkosítása. Csak a szolgáltatás által felügyelt bérlői kulcsot naplókról a Key Vault-kulcs szükséges az adatbázis-titkosítási kulcs (adattitkosítási kulcsot), amely gyors és online művelet újbóli titkosítása.

## <a name="how-does-tde-with-byok-support-work"></a>Hogyan támogatja a TDE BYOK munkahelyi

![A Key Vaultban a kiszolgáló hitelesítése](./media/transparent-data-encryption-byok-azure-sql/tde-byok-server-authentication-flow.PNG)

TDE először a TDE-védőhöz, a Key Vault használatára van konfigurálva, amikor a kiszolgáló minden egyes TDE-kompatibilis adatbázist az adattitkosítási kulcsot a Key Vault újraindulás kulcs kérelmek küld. A Key Vault adja vissza a titkosított adatbázis-titkosítási kulcs, amely a felhasználói adatbázisban vannak tárolva.  

> [!IMPORTANT]
> Fontos megjegyezni, hogy **után az Azure Key Vaultban tárolt a TDE-Védőhöz, a az sosem hagyja el az Azure Key Vault**. A kiszolgáló csak kulcsműveletet kéréseket küldhetnek a TDE védőt megosztottkulcs-anyag belül a Key Vaultban, és **soha nem fér hozzá, vagy a TDE-védőhöz gyorsítótárazza**. A Key Vault-rendszergazda jogosult-e vonni a Key Vault-engedélyek a kiszolgáló bármely pontján, ebben az esetben a kiszolgáló felé irányuló összes kapcsolatot a rendszer levágja.

## <a name="guidelines-for-configuring-tde-with-byok"></a>TDE konfigurálásával szolgáltatást byok-val

### <a name="general-guidelines"></a>Általános irányelvek

- Győződjön meg arról, az Azure Key Vault és az Azure SQL Database/Managed Instance gazdakiszolgálói lesznek ugyanabban a bérlőben.  Több-bérlős key vault és a kiszolgáló kapcsolati **használata nem támogatott**.
- Döntse el, melyik előfizetések fogják használni a szükséges erőforrásokhoz – a kiszolgáló áthelyezése előfizetések között később egy új TDE BYOKs-beállítást igényel. Tudjon meg többet [erőforrások áthelyezése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- TDE byok konfigurálásakor fontos figyelembe venni a terhelést a key vault helyezett ismétlődő újraindulás/kicsomagolása műveletek által. Például a logikai kiszolgálóhoz tartozó összes adatbázis használja ugyanazt a TDE-védőhöz, mivel az adott kiszolgáló feladatátvevő indítja, számos kulcsfontosságú műveletek a tárolókhoz is vannak olyan adatbázisok, a kiszolgálón. Tapasztalataink alapján és dokumentált [key vault szolgáltatáskorlátjai](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits), javasoljuk, hogy legfeljebb 500 Standard társítása / általános célú vagy 200 prémium / üzletileg kritikus fontosságú adatbázisok egy Azure Key Vault egy előfizetés annak biztosítása érdekében folyamatosan magas rendelkezésre állás a TDE-védőhöz, a tároló elérésekor.
- Ajánlott: A TDE-védőhöz, a helyszínen őrizze.  Ehhez egy HSM-eszköz létrehozása a TDE-Védőhöz helyileg és a egy kulcsletét rendszer tárolja a TDE-Védőhöz helyi másolatot.  Ismerje meg, [hogyan viheti át a kulcsát a helyi HSM-ből az Azure Key Vaultba](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).

### <a name="guidelines-for-configuring-azure-key-vault"></a>Útmutató az Azure Key Vault beállítása

- A key vault létrehozása [helyreállítható Törlés](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) adatok elvesztése esetén véletlen kulcs – vagy a key vault – törlés elleni védelme érdekében engedélyezve van.  Használjon [PowerShell-lel engedélyezze a "helyreállítható törlés" tulajdonságot](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) (Ez a beállítás még nem áll az AKV portálról érhető el – azonban szükséges az Azure SQL) a kulcstartóra vonatkozó:  
  - Helyreállíthatóan törölt erőforrásokat egy megadott ideig, 90 nappal a megmaradnak, kivéve, ha azokat helyre, vagy törölve.
  - A **helyreállítása** és **kiürítése** műveletek rendelkezik saját a kulcstartó hozzáférési házirendben tartozó engedélyeket.
- Állítsa be a key vaultban, hogy ki a kritikus fontosságú erőforrás törölheti, és megakadályozza a véletlen vagy jogosulatlan törlése egy erőforrás-zárolás.  [További információ az erőforrás-zárolások](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

- A logikai kiszolgáló hozzáférést a key vault használatával az Azure Active Directory (Azure AD) identitás.  A portál felhasználói felületének használata esetén az Azure AD identity automatikusan létrejön, és a kiszolgáló számára a kulcstartó-hozzáférési engedélyek.  TDE BYOK konfigurálása a PowerShell segítségével, az Azure AD-identitásnak kell létrehozni, és befejezési ellenőrizni kell. Lásd: [BYOK TDE konfigurálása](transparent-data-encryption-byok-azure-sql-configure.md) és [TDE konfigurálása a felügyelt példány BYOK](http://aka.ms/sqlmibyoktdepowershell) részletes útmutatásért PowerShell használatakor.

  > [!NOTE]
  > Ha az Azure AD Identity **van véletlenül törölték, vagy visszavonja a kiszolgáló engedélyek** a kulcstartó hozzáférési szabályzatát használja, a kiszolgáló elveszítette a hozzáférését a key vaultban, és titkosított TDE adatbázisok eldobásakor 24 órán belül.

- Az Azure Key Vault használata a tűzfalak és virtuális hálózatok, konfigurálnia kell a következő: – lehetővé teszik a tűzfal megkerülését, megbízható Microsoft-szolgáltatások – válassza az Igen gombra

 > [!NOTE]
 > Ha a TDE titkosított SQL-adatbázisok elveszti a hozzáférését a key vaultban, mert a tűzfal nem mellőzik, a rendszer elveti az adatbázisok 24 órán belül.

- Naplózás és -jelentések az összes titkosítási kulcs engedélyezése: Key Vault lehetővé teszi a naplók egyszerűen behelyezése más biztonsági adatokat és az esemény (SIEM) eszközök. Az Operations Management Suite (OMS) [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) egy szolgáltatás, amely már integrálva van egy példát.
- Ahhoz, hogy a magas rendelkezésre állású titkosított adatbázisok, konfigurálja mindegyik logikai kiszolgáló két Azure Key vault-Kulcstartók, amelyek különböző régiókban találhatók.

### <a name="guidelines-for-configuring-the-tde-protector-asymmetric-key"></a>A TDE-Védőhöz (az aszimmetrikus kulcs) konfigurálásával

- Hozzon létre a titkosítási kulcs helyben a helyi HSM-eszközre. Győződjön meg arról, ez az aszimmetrikus, RSA 2048 bites kulccsal, hogy az Azure Key Vaultban raktározható legyen.
- Így letétbe helyezheti a kulcsot a kulcsletét rendszerben.  
- Importálja a titkosítási kulcs fájlját (.pfx, .byok vagy .backup) az Azure Key Vaultba.

   > [!NOTE]
   > Tesztelési célokra is lehet hozzon létre egy kulcsot az Azure Key Vault, azonban ezt a kulcsot nem lehet szétválasztást, mert a titkos kulcs soha nem hagyhatja a key vaultban.  Mindig készítsen biztonsági másolatot, és így letétbe helyezheti éles adatok, mint elvesztését, a kulcs titkosításához használt kulcsok (a kulcs véletlen törlés tárolót, lejárati stb.) végleges adatvesztést eredményez.

- Kulcs lejárati dátummal – nélkül használja, és soha ne állítson be egy lejárati dátuma már használatban van egy kulcs: **után lejár, a kulcsot, a titkosított adatbázis elveszíti a hozzáférést a TDE-Védőhöz, és 24 órán belül a rendszer elveti**.
- Győződjön meg arról, a kulcs engedélyezve van, és jogosult végrehajtásához *első*, *kulcs becsomagolása*, és *kulcs kicsomagolása* műveleteket.
- Hozzon létre egy Azure Key Vault-kulcs biztonsági mentése előtt először az Azure Key Vaultban kulccsal. Tudjon meg többet a [Backup-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1) parancsot.
- Hozzon létre egy új biztonsági másolatot, amikor végzett módosítások a kulcsot (például hozzáférés-vezérlési listák, adjon hozzá címkéket adhat hozzá, kulcs attribútumok hozzáadása).
- **Korábbi verziók megtartása** a kulcs a kulcsok a key vaultban, így régebbi adatbázis biztonsági másolatok visszaállíthatók. Ha a TDE-Védőhöz módosul, a régi biztonsági másolatok, az adatbázis egy adatbázis **nem frissülnek a** a legújabb TDE-Védőhöz használatára.  Minden egyes biztonsági másolat a TDE-Védőhöz lett létrehozva a helyreállításkor szüksége van. Kulcsrotálás végrehajtható megadott utasítások [elforgatása a transzparens adatok titkosítási védelmet a PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Minden korábban használt kulcsok Azure Key Vault vegye vissza a szolgáltatás által kezelt kulcsokkal módosítása után.  Ez biztosítja az adatbázisok biztonsági mentése az Azure Key vaultban tárolt a TDE-protectors vissza tudja állítani.  TDE kulcsvédők az Azure Key Vault létrehozása szükséges fenn kell tartani, amíg a szolgáltatás által kezelt kulcsokkal létrehozott összes tárolt biztonsági másolat.  
- Győződjön meg arról, ezek a kulcsok használatával helyreállítható biztonsági másolatait [Backup-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1).
- Egy esetleg feltört kulcsot egy biztonsági incidens anélkül, hogy az adatvesztést eltávolításához kövesse [egy esetleg feltört kulcs eltávolítására](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

## <a name="high-availability-geo-replication-and-backup--restore"></a>Magas rendelkezésre állás, a Georeplikáció és a biztonsági mentés / visszaállítás

### <a name="high-availability-and-disaster-recovery"></a>Magas rendelkezésre állás és vészhelyreállítás

Magas rendelkezésre állás konfigurálása az Azure Key Vault függ, hogy az adatbázis és a logikai kiszolgáló konfigurációját, és az alábbiakban két különböző esetek ajánlott konfigurációkat.  Az első esetben egy önálló adatbázis, vagy nincs beállított földrajzi redundancia logikai kiszolgálót.  A másik eset, adatbázis vagy a logikai kiszolgáló konfigurálva a feladatátvételi csoportok vagy a georedundáns tárolás, ahol biztosítani kell, hogy rendelkezik-e egy helyi Azure Key Vault belül a feladatátvételi csoporthoz annak biztosítása érdekében a geo-feladatátvétel munkahelyi minden georedundáns példányon.

Az első esetben ha szüksége van egy adatbázis és a logikai kiszolgáló georedundanciával nincs konfigurálva, magas rendelkezésre állásának ajánlott konfigurálása az azonos megosztottkulcs-anyag a két különböző régióban két különböző kulcstartók használni kívánt kiszolgálót. Ez az elsődleges és a logikai kiszolgáló ugyanabban a régióban elhelyezve a Key vault védőelem a TDE létrehozásával valósítható és a Klónozás a kulcsot egy másik Azure-régióban, kulcstartóba, úgy, hogy a kiszolgáló egy második key vaulthoz való hozzáférés kell az elsődleges kulcs v Beállítás alapértelmezettként élmény kimaradás, miközben az adatbázis működik-e. A Backup-AzureKeyVaultKey-parancsmag használatával a kulcs titkosított formátumban le az elsődleges a key vault használja a Restore-AzureKeyVaultKey parancsmagot és adja meg a key vault a második régióban.

![Egyetlen kiszolgálóból álló, és HA nincs geo-Dr-beli](./media/transparent-data-encryption-byok-azure-sql/SingleServer_HA_Config.PNG)

## <a name="how-to-configure-geo-dr-with-azure-key-vault"></a>Az Azure Key Vault Geo-Dr-beli konfigurálása

A TDE-Protectors titkosított adatbázisok magas rendelkezésre állásának fenntartása fontos, hogy konfigurálja a redundáns Azure Key vault-Kulcstartók a meglévő vagy a kívánt SQL Database feladatátvételi csoportok vagy az aktív georeplikáció példányok alapján.  Minden egyes georeplikált kiszolgáló megköveteli egy külön kulcstartót, közös elhelyezésű a kiszolgálóval azonos Azure-régióban kell lennie. Kell egy elsődleges adatbázis leállás miatt elérhetetlenné egy adott régióban, és akkor aktiválódik, a feladatátvétel, a másodlagos adatbázis viszont átveszi a másodlagos key vault használatával.

Geo-Replicated Azure SQL-adatbázisok a következő Azure Key Vault-konfigurációra szükség:

- Egy elsődleges adatbázis használata a key vaulttal a régiót és a egy másodlagos adatbázis a key vault régióban.
- Legalább egy másodlagos szükség, legfeljebb négy másodlagos példány hozható létre támogatottak.
- Másodlagos példány hozható létre (láncolási) másodlagos példány hozható létre, nem támogatottak.

A következő szakaszban fog haladnak át a beállítási és konfigurációs lépéseket részletesen.

### <a name="azure-key-vault-configuration-steps"></a>Az Azure Key Vault konfigurációs lépések

- Telepítés [PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azurermps-5.6.0)
- Hozzon létre két Azure Key vault-Kulcstartók használatával két különböző régióban [PowerShell-lel engedélyezze a "helyreállítható törlés" tulajdonságot](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) a a kulcstartók (Ez a beállítás még nem áll az AKV portálról érhető el – azonban szükséges az SQL).
- Mind az Azure Key vault-Kulcstartók a két rendelkezésre ahhoz, hogy a biztonsági mentési és helyreállítási kulcsok működéséhez ugyanabban az Azure a földrajzi régióban kell elhelyezkednie.  Ha a Geo-Dr-beli SQL-követelményeinek, hajtsa végre a különböző helyeken található két fő tárolóra van szüksége a [BYOK folyamat](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) , amely lehetővé teszi, hogy a kulcsok importálni egy helyszíni HSM-ből.
- Hozzon létre egy új kulcsot az első kulcstárolóját:  
  - RSA/RSA-HSA 2048 key
  - Nincs lejárati dátuma
  - Kulcs engedélyezve van, és jogosult végrehajtani a get, wrap key és kulcsműveletek kicsomagolása
- Az elsődleges kulcs biztonsági mentése, és állítsa vissza a kulcsot a második key vaulthoz.  Lásd: [BackupAzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1) és [Restore-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-5.5.0).

### <a name="azure-sql-database-configuration-steps"></a>Az Azure SQL Database konfigurációs lépések

Az alábbi konfigurációs lépéseket eltér-e egy új SQL-telepítés kezdve, vagy ha már meglévő SQL Geo-DR-környezet használata.  Azt a konfigurációs lépések egy új központi telepítés először körvonalazzák, és ezután azt ismertetik, hogyan rendelje hozzá a TDE-Protectors egy meglévő üzemelő, amely már rendelkezik egy Geo-DR-kapcsolat létrejött az Azure Key Vaultban tárolt.

**Egy új üzembe helyezés lépései**:

- Hozzon létre két logikai SQL-kiszolgálók a korábban létrehozott kulcstartók azonos két régióban.
- Válassza ki a logikai kiszolgáló TDE ablaktáblán, és minden egyes logikai SQL-kiszolgáló:  
  - Válassza ki az AKV ugyanabban a régióban
  - Válassza ki a kulcsot használja, mint a TDE-Védőhöz – minden kiszolgálón a helyi példány a TDE-Védőhöz fogja használni.
  - Ezzel a portálon létrehoz egy [AppID](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) a logikai SQL-kiszolgálón, amelyet a logikai SQL Server engedélyeket az eléréséhez a key vault – ne törölje ezt az identitást. Engedélyeinek eltávolításával az Azure Key Vaultban inkább a logikai SQL-kiszolgálón, amely a logikai SQL Server-engedélyeket a key vault eléréséhez használható visszavonhatja a hozzáférést.
- Az elsődleges adatbázis létrehozásához.
- Kövesse a [aktív georeplikáció útmutatást](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) szeretné próbálni a folyamatot, ebben a lépésben létrehozza a másodlagos adatbázis.

![Feladatátvételi csoportok és a geo-Dr-beli](./media/transparent-data-encryption-byok-azure-sql/Geo_DR_Config.PNG)

> [!NOTE]
> Fontos győződjön meg arról, hogy ugyanazt a TDE-Protectors mindkét kulcstartók, mielőtt folytatná a geo-kapcsolat az adatbázisok között.

**Meglévő SQL-Adatbázist a Geo-Dr-beli üzembe helyezési lépései**:

A logikai SQL-kiszolgáló már létezik, és az elsődleges és másodlagos adatbázisok vannak már hozzá van rendelve, mert az Azure Key Vault konfigurálásának lépései a következő sorrendben kell végrehajtani:

- Indítsa el a másodlagos adatbázist üzemeltető logikai SQL-kiszolgálón:
  - A key vault ugyanabban a régióban található hozzárendelése
  - Rendelje hozzá a TDE-Védőhöz
- Most nyissa meg a logikai SQL-kiszolgáló, amelyen az elsődleges adatbázis:
  - Válassza ki ugyanazt a TDE-Védőhöz használt a másodlagos adatbázis

![Feladatátvételi csoportok és a geo-Dr-beli](./media/transparent-data-encryption-byok-azure-sql/geo_DR_ex_config.PNG)

>[!NOTE]
>A key vault rendel a kiszolgáló, esetén fontos, hogy a másodlagos kiszolgálón indítsa el.  A második lépésben hozzárendelés a kulcs az elsődleges kiszolgálóra tárolóját, és frissítse a TDE-Védőhöz a Geo-DR-hivatkozás továbbra is működik, mert ezen a ponton a TDE-Védőhöz, a replikált adatbázis által használt érhető el mindkét kiszolgálón.

Mielőtt TDE engedélyezése az ügyfél által felügyelt egy SQL Database Geo-DR-forgatókönyv az Azure Key Vault kulcsait, fontos két Azure Key vault-Kulcstartók azonos régióban lesz használható az SQL Database georeplikációja való létrehozásához és kezeléséhez.  "Azonos tartalmak" kifejezetten azt jelenti, hogy mindkét kulcstartók tartalmaznia kell az azonos TDE Protector(s) példányát, hogy mindkét kiszolgáló fér hozzá a TDE-Protectors használja minden adatbázis.  Továbbítja, szükség esetén mindkét kulcstartók szinkronban, ami azt jelenti, hogy a kulcs kulcsrotálás után tartalmazniuk kell a TDE-Protectors azonos példányait karbantartása régi verzióinak naplófájlok használt kulcsok vagy kell tartania a biztonsági mentés, a TDE-Protectors a ugyanazon tulajdonságok és a kulcs tárolók SQL azonos hozzáférési engedélyeket kell fenntartani.  

Kövesse a [aktív georeplikáció áttekintése](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) teszteléséhez, és a feladatátvétel, amely rendszeres időközönként ellenőrizze mindkét kulcstartókhoz SQL a hozzáférési engedélyeket kell elvégezni megmaradnak.

### <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

Ha egy adatbázist a TDE a Key Vaultból egy kulccsal titkosított, minden létrehozott biztonsági is titkosítva van az a azonos TDE-Védőhöz.

Visszaállítása titkosított együtt a TDE-Védőhöz a Key Vaultból, győződjön meg arról, hogy a megosztottkulcs-anyag továbbra is az eredeti nevű eredeti tárban. Ha a TDE-Védőhöz módosul, a régi biztonsági másolatok, az adatbázis egy adatbázis **nem** frissítve a legújabb TDE-Védőhöz használatára. Ezért azt javasoljuk, hogy őrizze meg az összes régi verziók a TDE-Védőhöz a Key Vaultban, így állíthatók az adatbázisok biztonsági mentése.

Ha egy kulcsot, akkor lehet szükség, a biztonsági másolat visszaállítása már nem az eredeti a key vault, a következő hibaüzenetet ad vissza: "Célkiszolgáló `<Servername>` nem rendelkezik hozzáféréssel az összes AKV URI-k között < időbélyeg 1 > és < időbélyeg 2 >. Ismételje meg a művelet minden AKV URI-k visszaállítását követően."

A hiba elhárítása érdekében futtassa a [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) parancsmag használatával adja vissza a listán szereplő kulcsokra a Key Vaultból, mint a kiszolgáló (kivéve, ha a felhasználó törölve lettek). Annak érdekében, hogy az összes biztonsági másolatok visszaállíthatók, győződjön meg arról, hogy a biztonsági mentés a célkiszolgáló hozzáfér az összes ezeket a kulcsokat.

```powershell
Get-AzureRmSqlServerKeyVaultKey `
  -ServerName <LogicalServerName> `
  -ResourceGroup <SQLDatabaseResourceGroupName>
```

További információ a biztonsági mentési helyreállítási SQL Database-hez, lásd: [egy Azure SQL database helyreállítása](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups). További információ a biztonsági mentési helyreállítási SQL Data warehouse-hoz, lásd: [helyreállítása az Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-overview).

További szempontok a biztonsági mentés naplófájlokat: Mentett napló fájlokat az eredeti TDE Encryptor az titkosítva maradnak, még akkor is, ha a TDE-Védőhöz lett-e forgatni, és az adatbázis most új TDE-Védőhöz használatával.  A visszaállításkor mindkét kulcsot az adatbázis visszaállítása szükség lesz.  Ha a naplófájlt használ az Azure Key Vaultban tárolt TDE-Védőhöz, ezt a kulcsot szükség lesz a visszaállításkor, még akkor is, ha az adatbázis addig használandó szolgáltatás által kezelt TDE módosítva lett.
