---
title: Azure Key Vault az Azure Ausztráliában
description: Útmutató az ausztráliai régiókban való kulcskezelő Azure Key Vault konfigurálásához és használatához, hogy megfeleljenek az ausztráliai kormányzati szabályzatok, rendeletek és jogszabályok konkrét követelményeinek.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 263765c777f994134befc52f0c63c7f18e590b39
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602117"
---
# <a name="azure-key-vault-in-azure-australia"></a>Azure Key Vault az Azure Ausztráliában

A titkosítási kulcsok biztonságos tárolása és a titkosítási kulcs életciklusának kezelése kritikus fontosságú elemek a titkosítási rendszereken belül.  Az Azure-ban ezt a képességet biztosító szolgáltatás a Azure Key Vault. Key Vault a IRAP biztonsági hozzáférése megtörtént, és a ASCS-hitelesítés védett.  Ez a cikk ismerteti a legfontosabb szempontokat, amikor a Key Vault használatával betartja az Australian Signals Igazgatósága (ASD) [Információbiztonsági manuális vezérlőit](https://acsc.gov.au/infosec/ism/) (ISM).

A Azure Key Vault egy felhőalapú szolgáltatás, amely védi a titkosítási kulcsokat és a titkokat. Mivel ezek az adatok érzékenyek és üzleti szempontból kritikus fontosságúak, Key Vault lehetővé teszi a kulcstartók biztonságos elérését, így csak a jogosult felhasználók és alkalmazások számára. A Key Vault három fő összetevőt felügyel és vezérel:

- kulcsok
- titkos kódok
- tanúsítványok

Ez a cikk a kulcsok a Key Vault használatával történő felügyeletére koncentrál.

![Azure Key Vault](media/azure-key-vault-overview.png)

*1. ábra – Azure Key Vault*

## <a name="key-design-considerations"></a>A legfontosabb tervezési szempontok

### <a name="deployment-options"></a>Telepítési beállítások

Az Azure Key Vaultok létrehozásának két lehetősége van. Mindkét lehetőség a hardveres biztonsági modulok (HSM) Thales nShield-családját használja, a rendszer ellenőrzi a szövetségi adatfeldolgozási szabványokat (FIPS), és jóváhagyja a kulcsok a védett környezetekben való tárolását. A következő lehetőségek közül választhat:

- **Szoftveres védelemmel ellátott tárolók:** Az 1. szintű FIPS 140-2-es szint érvényesítve. A HSM-ben tárolt kulcsok. A titkosítási és a visszafejtési műveleteket az Azure számítási erőforrásai végzik.
- **HSM által védett tárolók:** Az FIPS 140-2 2. szintű ellenőrzése megtörtént. A HSM-ben tárolt kulcsok. A titkosítási és a visszafejtési műveleteket a HSM hajtja végre.

Key Vault támogatja a Rivest-Adleman (RSA) és az elliptikus görbe titkosítási (ECC) kulcsokat. Az alapértelmezett érték az RSA 2048 bites kulcsa, de az RSA 3072-bit, az RSA 4096-bit és az ECC-kulcsok esetében speciális lehetőség áll rendelkezésre.  Minden kulcs megfelel az ISM-vezérlőknek, de az elliptikus görbe kulcsai előnyben részesítettek.

### <a name="resource-operations"></a>Erőforrás-műveletek

Több személy is részt vesz a Azure Key Vaultban:

- **Key Vault rendszergazda:** Kezeli a tár életciklusát
- **Kulcs rendszergazdája:** Kezeli a kulcsok életciklusát a tárolóban.
- **Fejlesztő/operátor:** Kulcsok integrálása a tárból alkalmazások és szolgáltatások számára
- **Auditor** Kulcshasználat és hozzáférés figyelése
- **Alkalmazások** Kulcsok használata az adatok védelméhez

A Azure Key Vault két különálló csatolóval védett:

- **Felügyeleti sík:** Ez a sík a tár felügyeletét és a RBAC által biztosított védelmét tárgyalja.
- **Adatsík:** Ez a sík a tárolóban található összetevők kezelésével és elérésével foglalkozik.  Biztonságos Key Vault hozzáférési házirend használatával.

Az ISM-nek megfelelően megfelelő hitelesítésre és engedélyezésre van szükség ahhoz, hogy egy hívó (egy felhasználó vagy egy alkalmazás) előtt hozzáférjen a Key vaulthoz a sík használatával.

Az Azure RBAC egy beépített szerepkörrel rendelkezik a Key Vaulthoz, [Key Vault közreműködőhöz](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#key-vault-contributor)a kulcstartók felügyeletének vezérléséhez. Ajánlott egyéni szerepköröket létrehozni a kulcstárolók felügyeletéhez igazított részletesebb szerepkörökhöz.

>[!WARNING]
>Ha a kulcsokhoz való hozzáférés Key Vault hozzáférési házirenden keresztül van engedélyezve, akkor a felhasználó vagy az alkalmazás hozzáfér a kulcstartó összes kulcsához (például ha a felhasználó rendelkezik "Delete" hozzáféréssel, akkor törölhet minden kulcsot).  Ezért több Key vaultot kell telepíteni a biztonsági tartományokhoz/határokhoz való igazításhoz.

### <a name="networking"></a>Hálózat

Key Vault tűzfalak és virtuális hálózatok konfigurálásával szabályozhatja az adatsíkon való hozzáférést.  Engedélyezheti a felhasználókhoz vagy alkalmazásokhoz való hozzáférést a megadott hálózatokon, miközben megtagadja a hozzáférést a felhasználókhoz vagy az alkalmazásokhoz az összes többi hálózaton. A [megbízható szolgáltatások](https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints#trusted-services) kivételt képeznek a szabályozás alól, ha engedélyezve van a "megbízható szolgáltatások engedélyezése" beállítás.  A virtuális hálózatkezelési vezérlő nem vonatkozik a felügyeleti síkon.

A Key Vaultokhoz való hozzáférést explicit módon korlátozni kell azon hálózatok minimális készletére, amelyek a kulcsokhoz való hozzáférést igénylő felhasználókkal vagy alkalmazásokkal rendelkeznek.

### <a name="bring-your-own-key-byok"></a>Bring Your Own Key (BYOK)

Key Vault támogatja a BYOK.  A BYOK lehetővé teszi a felhasználók számára, hogy a meglévő kulcsfontosságú infrastruktúrából importálják a kulcsokat.  A Thales egy [ausztrál eszközkészletet](https://www.microsoft.com/download/details.aspx?id=45345) biztosít a kulcsok biztonságos átvitelének és importálásának támogatásához egy külső HSM-ből (például egy offline munkaállomás által generált kulcsokkal) a Key Vaultba.

### <a name="key-vault-auditing-and-logging"></a>Key Vault naplózás és naplózás

A ASCS megköveteli, hogy a Commonwealth-entitások a megfelelő Azure-szolgáltatásokat használják az Azure-beli számítási feladatokhoz való valós idejű monitorozás és jelentéskészítés céljából.

A naplózás engedélyezve van, ha engedélyezi a **_"AuditEvent"_** diagnosztikai beállítást a kulcsok értékein.  A naplózási események a megadott Storage-fiókba lesznek naplózva.  A **_"RetentionInDays"_** időszakot az adatmegőrzési szabályzatnak megfelelően kell beállítani.  A felügyeleti síkon és az adatsíkon [végzett műveletek](https://docs.microsoft.com/azure/key-vault/key-vault-logging#interpret) naplózása és naplózása is megtörténik. A [Azure Monitor Azure Key Vault megoldás](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault) segítségével áttekintheti Key Vault AuditEvent naplóit.  Számos egyéb Azure-szolgáltatás használható Key Vault listázásával feldolgozására és terjesztésére.

### <a name="key-rotation"></a>Kulcsrotálás

A kulcsok Key Vaultban való tárolása egyetlen pontot biztosít a kulcsok azon kívüli fenntartásához, amely lehetővé teszi a kulcsok frissítését az alkalmazások viselkedésének befolyásolása nélkül. A kulcsok Azure Key Vault való tárolása különböző stratégiákat tesz lehetővé a kulcsfontosságú rotáció támogatásához:

- Manuálisan
- Programozott módon API-kon keresztül
- Automation-parancsfájlok (például a PowerShell és a Azure Automation használata)

Ezek a lehetőségek lehetővé teszik a kulcsok rendszeres elforgatását, hogy megfeleljenek a megfelelőségi követelményeknek, vagy ha van olyan probléma, amely miatt a kulcsok biztonsága veszélyben van.

#### <a name="key-rotation-strategies"></a>Kulcsfontosságú rotációs stratégiák

Fontos a kulcstartóban tárolt kulcsok megfelelő kulcsfontosságú rotációs stratégiájának fejlesztése.  A helytelen kulcs használatával a rendszer helytelenül visszafejti az adatokat, és a kulcsok elvesztése az információkhoz való hozzáférés teljes elvesztését eredményezheti.  Példák a különböző forgatókönyvekhez tartozó kulcsfontosságú rotációs stratégiákra:

- **Fedélzeti adatok:** a rendszer két fél között továbbítja a felejtő adatokat.  A kulcsok elforgatásakor mindkét félnek rendelkeznie kell egy mechanizmussal a frissített kulcsok a kulcstartóból való lekéréséhez.
- **Adatok Rest-ként:** A felek titkosított adattárolókat tárolnak, és a későbbiekben visszafejtik a használatát.  Amikor a rendszer elforgatja a kulcsot, a régi kulccsal kell visszafejtenie az adatgyűjtést, majd az új, elforgatott kulccsal kell titkosítania.  A visszafejtési/titkosítási folyamat a kulcs-titkosítási kulcsokkal való használatának minimalizálására (lásd példa) van megközelítés.  A Microsoft az Azure Storage kulcsfontosságú rotációs folyamatának többségét kezeli (lásd:...)
- **Hozzáférési kulcsok:** számos Azure-szolgáltatás rendelkezik a Key Vault tárolhatók elérési kulcsokkal (például CosmosDB).  Az Azure-szolgáltatások elsődleges és másodlagos hozzáférési kulccsal rendelkeznek.  Fontos, hogy mindkét kulcs egyszerre ne legyen elforgatva.  Ezért az egyik kulcsot el kell forgatni egy időszak után, és a kulcs műveletét ellenőrizni kell, majd a második kulcsot el lehet forgatni.

### <a name="high-availability"></a>Magas rendelkezésre állás

Az ISM-nek számos olyan vezérlője van, amely az üzletmenet folytonosságára vonatkozik.
Azure Key Vault több, a régióban és a másodlagos, párosított régióba replikált tartalommal rendelkező [](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)redundancia-réteggel rendelkezik.

Ha a kulcstartó feladatátvételi állapotban van, az csak olvasható módban van, és az elsődleges szolgáltatás visszaállított írási és olvasási üzemmódba kerül vissza.

Az ISM-nek több, a biztonsági mentéshez kapcsolódó vezérlője van.  Fontos a tárolók és a kulcsaik megfelelő biztonsági mentési/helyreállítási csomagjainak fejlesztése és végrehajtása.

## <a name="key-lifecycle"></a>Kulcs életciklusa

### <a name="key-operations"></a>Legfontosabb műveletek

Key Vault a következő műveleteket támogatja a kulcson:

- **létrehozása** Lehetővé teszi, hogy az ügyfél Key Vault hozzon létre egy kulcsot. A kulcs értékét Key Vault és tárolja a rendszer, és az nem jelenik meg a-ügyfél számára. Az aszimmetrikus kulcsok a Key Vaultben hozhatók létre.
- **importálása** Lehetővé teszi, hogy az ügyfél egy meglévő kulcsot importáljon Key Vaultba. Az aszimmetrikus kulcsok a JWK-konstrukción belül számos különböző csomagolási módszer használatával importálhatók Key Vaultba.
- **frissítése** Lehetővé teszi, hogy az ügyfél megfelelő engedélyekkel módosítsa a korábban a Key Vault belül tárolt kulcs metaadatait (fő attribútumait).
- **törlése** Lehetővé teszi, hogy az ügyfél megfelelő engedélyekkel törölje a kulcsot a Key Vaultból.
- **listáját** Lehetővé teszi az ügyfél számára az adott Key Vault összes kulcsának listázását.
- **verziók listázása:** Lehetővé teszi az ügyfél számára egy adott kulcs összes verziójának listázását egy adott Key Vaultban.
- **Get** Lehetővé teszi, hogy az ügyfél lekérje egy adott kulcs nyilvános részeit egy Key Vault.
- **hát** Kulcs exportálása védett űrlapon.
- **visszaállítása** Egy előzőleg mentett kulcs importálása.

A felhasználók, a szolgáltatások és a Key Vault hozzáférés-vezérlési bejegyzéseket használó alkalmazások számára biztosítható az engedélyek megfelelő készlete, amelyek lehetővé teszik a kulcsfontosságú műveletek végrehajtását.

A Key Vault a törölt tárolók és kulcsok helyreállítását lehetővé tevő Soft delete funkcióval rendelkezik. Alapértelmezés szerint a **_"Soft Delete"_** nincs engedélyezve, de ha engedélyezve van, a rendszer az objektumokat 90 napra (a megőrzési időszakra), miközben a rendszer törli.  A **_"kiürítés"_** további engedélyek lehetővé teszik a kulcsok végleges törlését, ha a **_"védelem kiürítése"_** beállítás le van tiltva.

Egy meglévő kulcs létrehozása vagy importálása a kulcs új verzióját hozza létre.

### <a name="cryptographic-operations"></a>Titkosítási műveletek

A Key Vault a kulcsokat használó titkosítási műveleteket is támogatja:

- **aláírás és ellenőrzés:** ez a művelet egy "aláírási kivonat" vagy "hash ellenőrzése". A Key Vault nem támogatja a tartalom kivonatolását az aláírás létrehozásának részeként.
- **kulcs titkosítása/becsomagolása:** ez a művelet egy másik kulcs megvédésére szolgál.
- **titkosítás és visszafejtés:** a tárolt kulcs egyetlen adatblokk titkosítására vagy visszafejtésére szolgál.

A felhasználók, a szolgáltatások és a Key Vault hozzáférés-vezérlési bejegyzéseket használó alkalmazások számára biztosítható megfelelő engedélyek, amelyek lehetővé teszik a titkosítási műveletek végrehajtását.

Három fő attribútum állítható be, amelyekkel szabályozható, hogy a kulcs engedélyezve van-e, és hogy használható-e a titkosítási műveletek:

- **engedélyezve**
- **NBF:** a megadott dátum előtt nem engedélyezett
- **exp:** lejárat dátuma

## <a name="storage-and-keys"></a>Tárolás és kulcsok

Az ügyfél által felügyelt kulcsok nagyobb rugalmasságot biztosítanak, és lehetővé teszik az ellenőrzéshez szükséges kulcsok értékelését és felügyeletét. Emellett az adatvédelemhez használt titkosítási kulcsok naplózását is lehetővé teszik.
Az Key Vaultban tárolt tárolók és kulcsok három aspektusa van:

- Felügyelt Storage-fiók kulcsainak Key Vault
- Azure Storage Service Encryption (SSE) a nyugalmi állapotban lévő adatokhoz
- Felügyelt lemezek és Azure Disk Encryption

Key Vault Azure Storage-fiókjának kulcs-kezelési szolgáltatása Key Vault legfontosabb szolgáltatásának kiterjesztése, amely támogatja a Storage-fiókok kulcsainak szinkronizálását és újragenerálását (elforgatását).  [Azure Storage-integráció Azure Active Directory](https://docs.microsoft.com/azure/storage/common/storage-auth-aad) (előzetes verzió) használata ajánlott, ha a szolgáltatás kiváló biztonságot és egyszerű használatot biztosít.
Az SSE két kulcsot használ az inaktív adatok titkosításának kezeléséhez:

- Kulcs titkosítási kulcsa (KEK)
- Adattitkosítási kulcsok (ADATTITKOSÍTÁSI kulcsot)

Míg a Microsoft kezeli a DEKs, az SSE a Key Vault tárolható, ügyfél által felügyelt KEK is használhat. Ez lehetővé teszi, hogy a kulcsok elforgatása Azure Key Vault a megfelelő megfelelőségi szabályzatok szerint. A kulcsok elforgatásakor az Azure Storage újra titkosítja az adott Storage-fiókhoz tartozó fiók titkosítási kulcsát. Ez nem eredményezi az összes adattal való újratitkosítást, és nincs szükség további műveletre.

Az SSE felügyelt lemezekhez használatos, de az ügyfél által felügyelt kulcsok nem támogatottak.  A felügyelt lemezek titkosítása Azure Disk Encryption használatával végezhető el az ügyfél által felügyelt KEK-kulcsokkal Key Vaultban.

## <a name="next-steps"></a>További lépések

Tekintse át az [identitás](identity-federation.md) -összevonásról szóló cikket

További Azure Key Vault dokumentáció és oktatóanyagok áttekintése a [hivatkozási könyvtárban](reference-library.md)
