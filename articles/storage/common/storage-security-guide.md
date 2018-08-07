---
title: Az Azure Storage biztonsági útmutatóját |} A Microsoft Docs
description: A sok védelmének biztosítása az Azure Storage, beleértve többek között a RBAC, a Storage Service Encryption, ügyféloldali titkosítás, az SMB 3.0 és az Azure Disk Encryption részletei.
services: storage
author: craigshoemaker
ms.service: storage
ms.topic: article
ms.date: 05/31/2018
ms.author: cshoe
ms.component: common
ms.openlocfilehash: 912ae17fb7bb5d5cecad0af5b53d817b2faeef02
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522201"
---
# <a name="azure-storage-security-guide"></a>Az Azure Storage biztonsági útmutatóját

Az Azure Storage biztonsági képességeket, amelyek együttesen lehetővé teszik a fejlesztők számára biztonságos alkalmazások széles választékát nyújtja:

- Azure Storage tárterületre írt összes adat automatikusan titkosítva lesznek használatával [Storage Service Encryption (SSE)](storage-service-encryption.md). További információkért lásd: [bejelentése alapértelmezés titkosításának Azure-Blobok, fájlok, Table és Queue Storage](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- Az Azure Active Directory (Azure AD) és a szerepköralapú hozzáférés-vezérlés (RBAC) támogatottak az Azure Storage erőforrás-felügyeleti műveletek és a műveletekhez, a következő:   
    - Hatóköre a storage-fiók erőforrás felügyeleti műveleteket, mint a kulcskezelés engedélyezésére rendszerbiztonsági tagok és az Azure AD RBAC-szerepkörök rendelhet hozzá.
    - Az Azure AD-integrációs támogatott műveletekhez a Blob és üzenetsor-szolgáltatás az előzetes verzióban érhető el. RBAC-szerepkör hatóköre egy előfizetés, erőforráscsoport, tárfiók, vagy egy tároló vagy üzenetsor egy rendszerbiztonsági tag vagy a felügyeltszolgáltatás-identitás rendelhet hozzá. További információkért lásd: [hitelesíti a hozzáférést az Azure Storage, Azure Active Directory (előzetes verzió) használatával](storage-auth-aad.md).   
- Adatokat is védi az alkalmazás és az Azure közötti átvitel [Client-Side Encryption](../storage-client-side-encryption.md), HTTPS és SMB 3.0-s.  
- Azure-beli virtuális gépek által használt operációsrendszer- és adatlemezek használatával titkosíthatók [az Azure Disk Encryption](../../security/azure-security-disk-encryption.md). 
- Az Azure Storage az adatobjektumok való delegált hozzáférés adható használatával [közös hozzáférési aláírások](../storage-dotnet-shared-access-signature-part-1.md).

Ez a cikk az egyes biztonsági funkció, amely használható az Azure Storage áttekintése. Hivatkozások olyan cikkeket, amely az egyes szolgáltatások részletei így könnyedén megteheti a megadott további vizsgálatra minden témában.

Az alábbiakban az ebben a cikkben tárgyalt témaköröket:

* [Felügyeleti sík biztonsági](#management-plane-security) – biztonságossá a Tárfiókhoz

  A felügyeleti sík áll, a storage-fiók kezeléséhez használt erőforrásokat. Ez a szakasz ismerteti az Azure Resource Manager üzemi modell és a szerepköralapú hozzáférés-vezérlés (RBAC) szabályozható a tárfiókok használatával. Címek is kezelése a tárfiók kulcsait és újragenerálásával őket.
* [Adatok biztonsági Adatsík](#data-plane-security) – az adathozzáférés biztonságossá tétele

  Ebben a szakaszban megnézzük, amely hozzáférést biztosít a tényleges adatok objektumok a tárfiókba, például a blobokat, fájlokat, üzenetsorokat és táblákat, közös hozzáférési aláírások és a tárolt hozzáférési szabályzatok segítségével. Ismertetjük a szolgáltatásszintű SAS és a fiókszintű SAS. Azt is láthatja, hogyan korlátozhatja a hozzáférést egy adott IP-cím (vagy IP-címtartományát), hogy hogyan legyen korlátozva használt HTTPS protokollt és lépésekben vonhatja vissza egy közös hozzáférésű Jogosultságkód lejár, várakozás nélkül.
* [Titkosítás az átvitel során](#encryption-in-transit)

  Ez a szakasz leírja, hogyan védi az adatait, átvitel során, vagy onnan máshová az Azure Storage. A HTTPS és az Azure-fájlmegosztások az SMB 3.0-s által használt titkosítási ajánlott használható tárgyaljuk. Azt a rendszer is vessen egy pillantást ügyféloldali titkosítás, mely lehetővé teszi, hogy az ügyfélalkalmazások a Storage-bA továbbított előtt titkosíthatja az adatokat, és az adatok visszafejtéséhez követően elfogyott a tárterület.
* [Titkosítás inaktív állapotban](#encryption-at-rest)

  Az előadás kapcsolatos Storage Service Encryption (SSE), amely most már automatikusan engedélyezve van az új és meglévő storage-fiókok. Emellett áttekintjük, hogyan használhatja az Azure Disk Encryption és alapvető különbség és lemeztitkosítás és ügyféloldali titkosítás és SSE eseteit. Röviden áttekintjük FIPS-kompatibilitásnak Egyesült Államok Kormányzati számítógépek.
* Használatával [Storage Analytics](#storage-analytics) az Azure Storage-hozzáférés naplózása

  Ez a szakasz bemutatja, hogyan találhatja meg információkat a storage analytics naplók kérelmek. Azt fogjuk vessen egy pillantást valós a storage analytics naplóadatokat, és lásd: how to tesz különbséget az adatok egy kérelmet a Tárfiók hívóbetűjét, a megosztott hozzáférési aláírást, az akár névtelenül és kell-e azt sikeres vagy sikertelen.
* [Böngészőalapú ügyfeleken a CORS engedélyezése](#Cross-Origin-Resource-Sharing-CORS)

  Ez a szakasz ismerteti hogyan teszi lehetővé az eltérő eredetű erőforrások megosztása (CORS). Tartományok közötti elérést, és hogyan kezelhető az Azure Storage-bA beépített CORS képességekkel rendelkező tárgyaljuk.

## <a name="management-plane-security"></a>Felügyeleti sík biztonsági
A felügyeleti sík olyan műveleteket tartalmaz, amelyek befolyásolják a magának a tárfióknak. Például, is létrehozása vagy törölheti a tárfiókokat, tárfiókok listájának lekérése az előfizetéshez, a storage-fiók kulcsainak lekérése vagy a tárfiókkulcsok újragenerálása.

Amikor létrehoz egy új tárfiókot, válassza ki a klasszikus vagy Resource Manager üzembe helyezési modell. A klasszikus modellt az Azure-erőforrások létrehozását, csak lehetővé teszi, hogy a kiküszöböléséhez hozzáférést az előfizetés, és kapcsolja be, a storage-fiókot.

Ez az útmutató a Resource Manager-modell, amely a storage-fiókok létrehozásához ajánlott módszer összpontosít. A Resource Manager-tárfiókokra helyett a teljes előfizetés, mert így hozzáférést, és szabályozhatja a hozzáférést, a felügyeleti sík szerepköralapú hozzáférés-vezérlés (RBAC) használatával több véges szinten.

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Hogyan tegye biztonságossá tárfiókját, a szerepköralapú hozzáférés-vezérlés (RBAC)
Beszéljünk RBAC mi, és hogyan használhatja azt. Minden Azure-előfizetés Azure Active Directoryval rendelkezik. Felhasználók, csoportok és alkalmazások tartalmazza a hozzáférési engedélyt kapnak az Azure-előfizetést a Resource Manager-alapú üzemi modellt használó erőforrásainak kezeléséhez. Az ilyen típusú biztonsági szerepkör alapú hozzáférés-vezérlés (RBAC) nevezzük. A hozzáférés kezeléséhez használja a [az Azure portal](https://portal.azure.com/), a [Azure CLI-eszközeit](../../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs), vagy a [Azure Storage Resource Provider REST API-k](https://msdn.microsoft.com/library/azure/mt163683.aspx).

A Resource Manager-modell helyezi a tárfiók egy erőforrás-csoport és hozzáférés a felügyeleti sík, hogy adott storage-fiók az Azure Active Directoryval való. Például adott felhasználóknak biztosíthat hozzáférést a tárfiók kulcsaihoz, más felhasználók megtekintheti a storage-fiók adatait, de nem tudja elérni a tárfiók kulcsait.

#### <a name="granting-access"></a>Hozzáférés biztosítása
A hozzáférést a megfelelő RBAC-szerepkört rendelhet a felhasználók, csoportok és alkalmazások, a megfelelő hatókörben. A teljes előfizetés hozzáférést, rendelje hozzá egy szerepkört az előfizetés szintjén. Is hozzáférést biztosít az összes olyan erőforrást egy erőforráscsoportban magát az erőforráscsoportot számára biztosított engedélyekkel. Egyes szerepkörök is rendelhet adott erőforrásokat, például a storage-fiókok.

Az alábbiakban a fő pontokat kell tudni az RBAC használatával el az Azure Storage-fiók felügyeleti műveleteit:

* Hozzáférés hozzárendelése után alapvetően egy szerepkört a fiókhoz rendelt férnek hozzá a kívánt. Szabályozhatja a hozzáférést a tárolási fiók kezeléséhez használt műveletekre, de nem az objektumok a fiókban. Például engedélyeket is (például a redundancia miatt), a tárfiók tulajdonságainak lekéréséhez, de nem egy tároló vagy belül a Blob Storage tárolóban lévő adatok.
* Rendelkezik hozzáféréssel a storage-fiókban lévő objektumok számára engedélyt adhat a azokat, olvassa el a tárfiók kulcsaihoz, és, hogy a felhasználó használhatja ezeket a kulcsokat a blobok, üzenetsorok, táblák és fájlok elérésére.
* Szerepkörök rendelhetők, egy adott felhasználói fiók, a felhasználók egy csoportjára, vagy egy adott alkalmazást.
* Minden egyes szerepkörhöz műveletek, és nem a műveletek listáját. Például a virtuális gépek Közreműködője szerepkör rendelkezik a "listkeys műveletének" művelettel, amely lehetővé teszi, hogy a tárfiók kulcsaihoz kell olvasni. A közreműködői "Nem műveletek" rendelkezik, például frissítése a felhasználók az Active Directory hozzáférését.
* Storage szerepkörök tartalmazzák (de nem korlátozódnak) a következő szerepkörök:

  * Tulajdonos – ezek mindent felügyelhetnek, beleértve a hozzáférést.
  * Közreműködő – ez nem semmit a tulajdonosi hozzáférés hozzárendelése is kivételével. Ezzel a szerepkörrel rendelkező személyek tekinthetik meg és a tárfiókkulcsok újragenerálása. A tárfiók kulcsaihoz az adatobjektumok elérhetik azt.
  * Olvasó – akkor megtekintheti a titkos kulcsok kivételével a tárfiók adatait. Például ha valaki a storage-fiók olvasói szerepköre rendel, akkor megtekintheti a storage-fiók tulajdonságait, de nem módosítja a tulajdonságokat és a storage-fiók kulcsainak megtekintéséhez.
  * Tárfiók-közreműködő – felügyelheti a storage-fiók – követően elolvashatják az előfizetéshez tartozó erőforráscsoportokat és erőforrásokat, és hozzon létre és kezelheti az előfizetést erőforráscsoportok üzemelő példányainak. A tárfiók kulcsaihoz, amelyek viszont azt jelenti, hozzáférhet az adatsík is eléréséhez.
  * Felhasználói hozzáférés rendszergazdája –, kezelheti a felhasználói hozzáférést a tárfiókhoz. Például akkor is hozzáférést olvasó egy adott felhasználó számára.
  * Virtuális gépek Közreműködője – felügyelheti a virtuális gépek, de nem a tárfiókot, amelyhez csatlakoznak. Ez a szerepkör listázhatja a tárfiók kulcsaihoz, ami azt jelenti, hogy a szerepkör hozzárendelése a felhasználó frissítheti az adatsíkhoz.

    Ahhoz, hogy a felhasználót, hogy hozzon létre egy virtuális gépet azokat kell a megfelelő VHD-fájlt létrehozni a storage-fiókban. Ehhez szükségük fogja tudni lekérni a tárfiók kulcsát, és adja át azt az API-t a virtuális gép létrehozását. Ezért, listázhatja a tárfiók kulcsaihoz, ezzel az engedéllyel kell rendelkezniük.
* Egyéni szerepkörök definiálhat funkciója, amely lehetővé teszi, hogy az elérhető Azure-erőforrásokon végrehajtott műveletek listája a műveletek.
* A felhasználó lehet előtt be kell állítania az Azure Active Directoryban hozzájuk rendelhet egy szerepkört.
* Létrehozhat egy jelentést, akik nyújtott/visszavont milyen típusú hozzáférés és-tárolókról akikkel, és milyen hatókörben, a PowerShell vagy az Azure CLI használatával.

#### <a name="resources"></a>További források
* [Azure Active Directory szerepköralapú hozzáférés-vezérlése](../../role-based-access-control/role-assignments-portal.md)

  Ez a cikk az Azure Active Directory szerepkörön alapuló hozzáférés-vezérlését és annak működési módját ismerteti.
* [RBAC: Beépített szerepkörök](../../role-based-access-control/built-in-roles.md)

  Ez a cikk részletesen összes az RBAC-ben elérhető beépített szerepkört.
* [A Resource Manager-alapú és a klasszikus üzembe helyezés ismertetése](../../azure-resource-manager/resource-manager-deployment-model.md)

  Ez a cikk a Resource Manager-alapú és klasszikus üzembe helyezési modelleket ismerteti, és ismerteti a Resource Manager és az erőforrás-csoportok használatával járó előnyöket. A Resource Manager-modellben az Azure számítási, hálózati és tárolási szolgáltatók működését ismerteti.
* [Szerepköralapú hozzáférés-vezérlés kezelése REST API-val](../../role-based-access-control/role-assignments-rest.md)

  Ez a cikk leírja, hogyan használható a REST API az RBAC kezeléséhez.
* [Az Azure Storage Resource Provider REST API-referencia](https://msdn.microsoft.com/library/azure/mt163683.aspx)

  Az API-referencia az API-k használatával programozott módon kezelheti a tárfiók ismerteti.
* [Erőforrás-kezelő használata hitelesítési API az előfizetések hozzáféréséhez](../../azure-resource-manager/resource-manager-api-authentication.md)

  Ez a cikk bemutatja, hogyan a Resource Manager API-k használatával történő hitelesítéshez.
* [Szerepköralapú hozzáférés-vezérlés az Ignite-tól a Microsoft Azure számára](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

  Ez a hivatkozás a Channel 9 2015-ös MS Ignite-konferencia videójára mutat. Ebben a részben arról beszélnek, milyen hozzáférés-kezelési és jelentési képességeket nyújt az Azure, és bemutatják az Azure-előfizetés hozzáférés-biztosításának legjobb gyakorlatait az Azure Active Directory használatával.

### <a name="managing-your-storage-account-keys"></a>A Storage-fiók kulcsok kezelése
A tárfiókkulcsok 512 bites karakterláncok hozott létre Azure-és a tárfiók nevét, például a storage-fiókban tárolt adatok objektumok elérése is használható, -blobokkal, az entitások egy táblát, üzenetsorbeli üzenetek és a egy Azure-beli megosztáson található fájlok. A tárolási fiók kulcsok szabályozza a hozzáférést az adatsík a tárfiókhoz való hozzáférés szabályozása.

Minden tárfióknak a "Kulcs 1" és "2. kulcsba" említett két kulcs van a [az Azure portal](http://portal.azure.com/) és a PowerShell-parancsmagokban. Ezek helyreállíthatja segítségével manuálisan többféle módszer áll rendelkezésre, többek között, de nem csak az egyik a [az Azure portal](https://portal.azure.com/), PowerShell, az Azure CLI- vagy programozott módon, a Storage .NET ügyféloldali kódtár vagy az Azure Storage szolgáltatások segítségével REST API-T.

Nincsenek újragenerálni a tárfiókkulcsokat számtalan.

* Előfordulhat, hogy újragenerálja őket, rendszeres biztonsági okokból.
* A tárfiók kulcsait szeretne generálni, ha valaki felügyelt alkalmazás feltörni és lekérni a kulcsot, szoftveresen kötött vagy egy konfigurációs fájlba menti a tárfiókhoz való teljes hozzáférés engedélyezése.
* A kulcs újragenerálása egy másik eset, ha a csapata használ a Storage Explorer alkalmazás, amely megtartja a tárfiók kulcsát, és a csapat tagjainak egyike. Az alkalmazás továbbra is működik, hozzáférést ad nekik a tárfiókhoz után szűnt zajlik. Hozza létre őket fiókszintű közös hozzáférési aláírások elsődleges emiatt ténylegesen – használhatja a fiókszintű SAS helyett a hozzáférési kulcsok tárolásához egy konfigurációs fájlban.

#### <a name="key-regeneration-plan"></a>Kulcs újragenerálása terv
Nem szeretne csak újragenerálja a kulcsot, néhány tervezés nélkül használja. Ha így tesz, akkor tárfiók, amely jelentős zavart okozhat levágja sikerült elérését. Ezért két kulcs létezi. Újból létre kell hoznia egy kulcsot egyszerre.

Előtt hozza létre újra a kulcsokat, lehet, hogy az összes függő a storage-fiók alkalmazás, valamint más szolgáltatásokat használ az Azure-ban listáját. Például az Azure Media Services a tárfiók függő használja, ha meg kell a hívóbetűt a media Services-után újragenerálja a kulcsot. Ha minden olyan alkalmazások, például a storage Explorert használja, szüksége lesz a ezeket az alkalmazásokat, valamint az új kulcsokkal. Ha virtuális gépeket, amelynek VHD-fájlokat a storage-fiókban vannak tárolva, azokat nem érinti a storage-fiók kulcsainak újragenerálása.

A kulcsok az Azure Portal segítségével generálja újra. Kulcsok újbóli, miután azok tárolási szolgáltatások közötti szinkronizálását akár 10 percet is igénybe vehet.

Amikor készen áll, Íme az általános folyamat részletező, hogyan kell módosítani a kulcsot. Ebben az esetben a feltételezi, hogy jelenleg használt kulcs 1, és szeretné módosítani, minden kulcs 2 használja helyette.

1. Újragenerálja a kulcsot a 2. Győződjön meg arról, hogy biztonságos. Ez az Azure Portalon teheti meg.
2. Mind a tárfiók kulcsát tároló alkalmazások módosítsa a tárfiók-kulcsot az új kulcs a 2 értéket használja. Tesztelése, és tegye közzé az alkalmazást.
3. Ha minden, az alkalmazások és szolgáltatások működik, és sikeresen fut, 1. kulcs újragenerálása. Ez biztosítja, hogy bárki, akinek meg nem kifejezetten adott az új kulcs többé nem lesz a tárfiókhoz való hozzáférést.

Ha 2 kulcs jelenleg használ, ugyanazzal az eljárással, de fordított a fájlneveket.

Minden alkalmazást, hogy használja az új kulcs módosítása, és közzéteheti keresztül néhány nap alatt, áttelepítheti. Ezek mindegyike minden kész, miután kell majd lépjen vissza és generálja újra a régi kulcsot, annak érdekében, hogy már nem.

Egy másik lehetőség az, hogy a tárfiók kulcsát helyezni egy [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) egy titkos kulcsot, és rendelkezik az alkalmazások a kulcs lekérése onnan. Ezután amikor újragenerálja a kulcsot, és frissítse az Azure Key Vault, a kérelmek nem kell kell helyeznie, mert azok kiesik az új kulcsot az Azure Key vault automatikusan. Vegye figyelembe, hogy olvassa el a kulcsot minden alkalommal, amikor szüksége lesz rá az alkalmazás is van, vagy azt gyorsítótárazza a memóriában, és ha ez nem sikerül, használatakor lekérni a kulcsot újra az Azure Key vault.

Az Azure Key Vaulttal hozzáadja a tárelérési kulcsok biztonsági egy másik szint is. Ezt a módszert használja, ha soha nem kell a storage-kulcs szoftveresen kötött a konfigurációs fájlban, amely eltávolítja a sérülésre valaki hozzáférni az adott engedély nélkül a kulcsokat a.

Az Azure Key Vault használatának másik előnye is szabályozhatja a hozzáférést a kulcsokhoz, Azure Active Directory használatával. Ez azt jelenti, hogy is hozzáférést biztosít az alkalmazások, amelyeket az Azure Key Vaulttal a kulcsok lekéréséhez, és tudja, hogy más alkalmazások nem tudják a hívóbetűk adva nekik kifejezetten engedély nélkül kell néhány.

Megjegyzés: javasoljuk egyszerre az összes alkalmazás használata csak az egyik a kulcsokat. Az egyes helyek kulcs 1 és mások a kulcs 2 használja, ha nem lesz képes a kulcsok rotálására néhány alkalmazás-hozzáférés elvesztése nélkül.

#### <a name="resources"></a>További források
* [Tudnivalók az Azure Storage-fiókok](storage-create-storage-account.md#regenerate-storage-access-keys)

  Ez a cikk áttekintést nyújt a tárfiókok, és ismerteti a megtekintése, másolása és tárelérési kulcsok újragenerálása.
* [Az Azure Storage Resource Provider REST API-referencia](https://msdn.microsoft.com/library/mt163683.aspx)

  Ez a cikk a storage-fiók kulcsainak lekérése és a tárfiók hozzáférési kulcsainak az Azure-fiók, a REST API használatával az adott cikkekre mutató hivatkozásokat tartalmaz. Megjegyzés: Ez a Resource Manager-tárfiókok esetében.
* [A storage-fiókok műveletek](https://msdn.microsoft.com/library/ee460790.aspx)

  Ez a cikk a Storage Service Manager REST API-referencia a beolvasása és a tárfiók hozzáférési kulcsainak a REST API használatával meghatározott cikkekre mutató hivatkozásokat tartalmaz. Megjegyzés: Ez csak a klasszikus tárfiókok esetében.

  Ez a cikk bemutatja, hogyan férhet hozzá az Azure Storage kulcsok Azure Key vaultban az Active Directory használatával. Azt is bemutatja, hogyan használja az Azure Automation-feladat a kulcsok óránként.

## <a name="data-plane-security"></a>Adatsík Adatbiztonság
Adatsík adatbiztonság hivatkozik az adatobjektumok tárolva az Azure Storage – a blobok, üzenetsorok, táblák és fájlok védelmére szolgáló módszerek. Úgy tapasztaltuk módszerek az adatok átvitel során az adatokat és biztonsági titkosításához, de hogyan továbblép hozzáféréssel az objektumokhoz szabályozásával kapcsolatban?

Három lehetőség közül választhat, amelyek engedélyezik a hozzáférést az adatobjektumokhoz, az Azure Storage, többek között:

- Az Azure AD-vel történő hitelesítéséhez a tárolókhoz és üzenetsorok (előzetes verzió) való hozzáférés. Az Azure AD engedélyezési, beleértve a kód a titkos kulcsok tárolására szükségtelenné más módjaival előnyöket biztosít. További információkért lásd: [hitelesíti a hozzáférést az Azure Storage, Azure Active Directory (előzetes verzió) használatával](storage-auth-aad.md). 
- A tárfiókkulcsok-vel történő elérését megosztott kulcsos hitelesítéséhez. A storage-fiók kulcsok tárolására az alkalmazás, a Microsoft azt javasolja, hogy inkább az Azure AD, ha lehetséges keresztül megosztott kulcsos engedélyezése szükséges. Az éles környezetben, vagy Azure-táblák és fájlok való hozzáférésre folytassa a megosztott kulcs használatával, amíg az Azure AD-integráció előzetes verzióként.
- Közös hozzáférési aláírások használatával bizonyos objektumokhoz szabályzott engedélyekkel az egy adott időtartam megadását.

Emellett a Blob Storage, engedélyezheti, nyilvános hozzáférés a blobok hozzáférési szint beállítása ennek megfelelően a blobokat tartalmazó tároló. Hozzáférés egy tárolót a blobon vagy tárolón állítja be, a tárolóban lévő blobokat nyilvános olvasási hozzáférést engedélyezi. Ez azt jelenti, hogy bárki egy blobot a tárolóban mutató URL-címet is nyissa meg a böngészőben egy közös hozzáférésű Jogosultságkód használatával, vagy a tárfiók kulcsaihoz kellene nélkül.

Engedélyezési keresztüli hozzáférés korlátozása, mellett is használhatja [tűzfalak és virtuális hálózatok](storage-network-security.md) hálózati szabályok alapján a tárfiókhoz való hozzáférés korlátozásához.  Ez a megközelítés lehetővé teszi, hogy megtagadja a hozzáférést a nyilvános internetes forgalmat, és adja meg hozzáférési csak meghatározott Azure virtuális hálózatok vagy nyilvános IP-címtartományok.

### <a name="storage-account-keys"></a>Tárfiókkulcsok
A tárfiókkulcsok 512 bites karakterláncokat, a storage-fiók nevével együtt használható a storage-fiókban tárolt adatok objektumok elérése az Azure által létrehozott.

Például akkor is olvasni a blobokat, írni az üzenetsorok, táblák létrehozása és fájlok módosítása. Ezek a műveletek számos elvégezhető az Azure Portalon vagy a Storage Explorer számos alkalmazás használatával. Ezek a műveletek végrehajtásához használja a REST API vagy a tároló Ügyfélkódtárai valamelyik kódot is írhat.

Az a szakaszban leírt módon a [felügyeleti sík biztonsági](#management-plane-security), a tárelérési kulcsok elérhető klasszikus tárfiók teljes hozzáférést ad az Azure-előfizetés által biztosított. A storage-fiókok az Azure Resource Manager modellel tárkulcsok való hozzáférést szerepköralapú hozzáférés-vezérlés (RBAC) szabályozható.

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Hogyan delegálása közös hozzáférési aláírások és a tárolt hozzáférési szabályzatok segítségével-fiókjában található objektumok elérése
Egy közös hozzáférésű Jogosultságkód: egy URI, amely lehetővé teszi, hogy a tárolási objektum való hozzáférés delegálására, és adja meg például az engedélyeket és a dátum/idő tartomány hozzáférési korlátozások csatolható biztonsági jogkivonatot tartalmazó karakterlánc.

Blobok, tárolók, üzenetsorbeli üzenetek, fájlok és táblák hozzáférést biztosíthat. A táblákkal megadja a partíció- és sorkulcsot kulcstartományokkal, amelyhez szeretne a felhasználó hozzáférjen a tábla entitástartományának hozzáféréssel ténylegesen megadásához. Ha például rendelkezik egy földrajzi állapot partíciókulccsal rendelkező tárolt adatokat, ha nem ad valaki a kaliforniai csak az adatokhoz való hozzáférés.

Egy másik példa, előfordulhat, hogy egy webalkalmazást, amely lehetővé teszi, hogy bejegyzéseket írni a várólista SAS-jogkivonatát adja, és adjon egy feldolgozói szerepkör alkalmazás egy SAS-token lekérése az üzeneteket az üzenetsorból, és feldolgozni azokat. Vagy nem ad egy ügyfél egy SAS-token, tölthet fel képeket egy Blob Storage-tárolóba, és adjon meg egy webes alkalmazás olvasásához szükséges engedély a képek. Mindkét esetben a kockázatok elkülönítésének van – minden alkalmazáshoz is hozzáférést kell biztosítani csak a feladataik elvégzéséhez szükséges. Ez a közös hozzáférési aláírások használatával lehetséges.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Mi indokolhatja közös hozzáférési aláírások használata
Indokolhatja a beállítás csak a tárfiók kulcsára, ami sokkal egyszerűbb bármiféle helyett SAS használandó? A tárfiók kulcsának bármiféle olyan, mint a storage Királyság kulcsainak megosztása. Teljes hozzáférést van. Valaki esetleg használja a kulcsok és azok teljes Zenetár feltölteni a tárfiókba. Ezek sikerült is a fájlok cserélje vírusos verziók, vagy az adatok ellopására. A tárfiók azonnal korlátlan hozzáférés megadását is olyasmi, nem kell venni enyhén.

Közös hozzáférésű jogosultságkódokkal biztosíthat egy ügyfél csak korlátozott mennyiségű időt szükséges engedélyekkel. Például ha valaki tölti fel a blob-fiókjába, is hozzáférést biztosít számukra írási éppen elegendő alkalommal feltöltése a blob (attól függően, a blob mérete természetesen). És Ha meggondolja magát, visszavonhatja a hozzáférést.

Ezenkívül azt is megadhatja, hogy SAS használatával kérelmek egy adott IP-cím vagy IP-címtartományt az Azure-bA külső korlátozódnak. Is megkövetelheti, hogy kérések egy adott protokollt (HTTPS vagy HTTP/HTTPS) használatával. Ez azt jelenti, ha csak szeretné lehetővé tenni a HTTPS-forgalom számára, beállíthatja a szükséges protokoll csak HTTPS és HTTP-forgalom le lesz tiltva.

#### <a name="definition-of-a-shared-access-signature"></a>Közös hozzáférésű Jogosultságkód meghatározása
Egy közös hozzáférésű Jogosultságkód olyan lekérdezési paraméterek hozzáfűzésével mutat az erőforrás URL-címre

amely ismerteti a hozzáférés engedélyezett, és mennyi ideig, amelyhez a hozzáférés engedélyezett. Íme egy példa; Ez az URI blob írásvédett hozzáférést biztosít az öt perc alatt. Vegye figyelembe, hogy a SAS lekérdezési paramétert kell URL-kódolású, például a kettőspont (:) vagy 20 %-terület % 3A.

```
http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>Hogyan a közös hozzáférésű Jogosultságkód engedélyezve van az Azure Storage szolgáltatás
A storage szolgáltatás a kérelmet kap, amikor a bemeneti lekérdezési paramétereket fogadja, és létrehoz egy ugyanazzal a módszerrel, a hívó program aláírást. Ezután a két aláírások hasonlítja össze. Ha az engedélyt megadják, majd a storage szolgáltatás ellenőrizheti a storage szolgáltatás verzió róla, hogy érvényes, ellenőrizze, hogy az aktuális dátumot és időt a megadott időszakon belül, ellenőrizze, hogy a hozzáférést a kért felel meg a kezdeményezett kérelem stb.

Például a fenti URL-Ha az URL-cím helyett egy blob, fájl lett mutató a kérelem sikertelen lesz, mert ez azt jelenti, hogy a közös hozzáférésű Jogosultságkód egy BLOB. Ha a hívott REST-parancs sikeresen frissíteni a blob, meghiúsul, mert a közös hozzáférésű Jogosultságkód Megadja, hogy a csak olvasási hozzáférést számára engedélyezett-e.

#### <a name="types-of-shared-access-signatures"></a>Közös hozzáférésű Jogosultságkódok típusai
* Egy szolgáltatásszintű SAS egy storage-fiókot az adott erőforrások eléréséhez használható. Néhány példa erre, blob letöltése, egy tábla az entitások frissítésénél, üzeneteket ad hozzá egy üzenetsorban vagy egy fájl feltöltése egy fájlmegosztásba tárolóban lévő blobok listáját kérjük le.
* Egy fiókszintű SAS, amelyeket egy szolgáltatásszintű SAS használható eléréséhez használható. Ezenkívül adhat a beállítások olyan szolgáltatásszintű SAS, például a tárolók, táblák, üzenetsorok és fájlmegosztások létrehozása nem engedélyezettek. Több szolgáltatásokhoz való hozzáférés egyszerre is megadhatja. Például előfordulhat, hogy adjon valaki hozzáférést blobok és a fájlokat a storage-fiókban.

#### <a name="creating-a-sas-uri"></a>SAS URI létrehozása
1. Hozhat létre egy URI-t igény szerinti, minden alkalommal, amikor meghatározása a lekérdezési paraméterek mindegyikét.

   Ez a megközelítés akkor rugalmas, de ha egy olyan logikai készlete, amelyek hasonló minden alkalommal, amikor paraméterek, egy tárolt hozzáférési szabályzat segítségével jobb képet.
2. Létrehozhat egy tárolt hozzáférési szabályzatot egy teljes tárolót, megosztott fájl, tábla vagy üzenetsor. Ezután ezzel alapjaként a létrehozott SAS URI-k esetében. A tárolt hozzáférési szabályzatok alapján engedélyek könnyedén visszavonhatja. Minden tároló, üzenetsor, tábla vagy fájlmegosztás definiált legfeljebb öt házirendek is rendelkezhet.

   Például ha voltak is, hogy sok ember, olvassa el az adott tárolóban lévő blobokat, létrehozhat egy tárolt hozzáférési szabályzatot, amely szerint a "olvasási hozzáférést biztosíthat", és ugyanaz lesz, minden alkalommal, amikor egyéb beállításokat. Ezután létrehozhat egy SAS URI-t a tárolt hozzáférési szabályzat beállításainak használatával, és adja meg a lejárati dátum/idő. Ennek előnye, hogy nem kell minden alkalommal adja meg az összes a lekérdezési paramétereket.

#### <a name="revocation"></a>Visszavont tanúsítványok
Tegyük fel, hogy a SAS biztonsága sérült, vagy szeretné módosítani, vállalati biztonsági és jogszabályi követelmények miatt. Hogyan visszavonja a SAS használatával erőforrásokhoz való hozzáférést? Attól függ, hogyan hozott létre az SAS URI-t.

Ha az ad-hoc URI-kat használja, három lehetősége van. SAS-jogkivonatokat kibocsátani, rövid lejárati szabályzatokkal, és várjon, amíg lejár az SAS. Nevezze át vagy törölje az erőforrást (feltéve, hogy a jogkivonat hatóköre egyetlen objektum lett) is. A tárfiók kulcsaihoz módosíthatja. Ez a beállítás utolsó lehet jelentős hatást, attól függően, hogyan számos szolgáltatás használ a tárfiók, és valószínűleg nem valami néhány tervezés nélkül szeretné.

Egy tárolt hozzáférési szabályzat származó SAS használ, eltávolíthatja a hozzáférés visszavonása a tárolt hozzáférési szabályzat által – csak módosíthatja, hogy már lejárt, vagy teljesen távolíthatja el. Ez azonnal érvénybe lép, és minden a tárolt hozzáférési szabályzat használatával létrehozott SAS érvényteleníti. Frissítése vagy eltávolítása a tárolt hozzáférési szabályzat előfordulhat, hogy hatás felhasználó adott tárolóban, a fájlmegosztást, tábla vagy üzenetsor SAS-n keresztül, de ha az ügyfelek számára készültek, így a régit érvénytelenné, amikor egy új SAS kérnek a címkevezérlőt.

Mivel egy tárolt hozzáférési szabályzat származó SAS használatával lehetővé teszi, hogy a SAS azonnal visszavonása, az ajánlott mindig használja a tárolt hozzáférési szabályzatok lehetőség.

#### <a name="resources"></a>További források
További információt a közös hozzáférési aláírások és a tárolt hozzáférési szabályzatok, példák, teljes körű használatához tekintse meg a következő cikkeket:

* Ezek azok a referencia-cikkek.

  * [Service SAS](https://msdn.microsoft.com/library/dn140256.aspx)

    Ez a cikk példákat blobok, az üzenetsorbeli üzenetek számára, a tábla tartományokkal és a fájlok szolgáltatásszintű SAS használatával.
  * [A szolgáltatásalapú SAS létrehozása](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Egy fiók SAS létrehozása](https://msdn.microsoft.com/library/mt584140.aspx)
* Ezek a közös hozzáférési aláírások és a tárolt hozzáférési szabályzatok létrehozása a .NET ügyféloldali kódtár használatával kapcsolatos.

  * [Közös hozzáférésű Jogosultságkódok (SAS) használata](../storage-dotnet-shared-access-signature-part-1.md)
  * [A közös hozzáférésű Jogosultságkódot, 2. rész: Hozzon létre, és használhatja az SAS-a Blob Service-szel](../blobs/storage-dotnet-shared-access-signature-part-2.md)

    Ez a cikk a SAS-modell példák a közös hozzáférésű Jogosultságkódokat egy leírását tartalmazza, és az ajánlott eljárás javaslatok SAS használja. Azt is ismertetjük, a megadott engedélyeket visszavonását.

* Hitelesítés

  * [Az Azure Storage szolgáltatásainak hitelesítése](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* A közös hozzáférésű Jogosultságkódot első lépéseit ismertető Oktatóanyaga

  * [SAS első lépéseket ismertető oktatóanyag](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Titkosítás az átvitel során
### <a name="transport-level-encryption--using-https"></a>Átviteli szintű titkosítást – HTTPS-en keresztül
Egy újabb lépést kell tennie az Azure Storage-adatok védelme érdekében, hogy az ügyfél és az Azure Storage között az adatok titkosításához. Az első javaslat, hogy mindig a [HTTPS](https://en.wikipedia.org/wiki/HTTPS) protokoll, amely biztosítja, hogy a biztonságos kommunikáció a nyilvános interneten keresztül.

Ahhoz, hogy biztonságos kommunikációs csatornát, mindig használjon HTTPS, ha a REST API-k hívása, vagy fér hozzá az objektumok tárolására. Ezenkívül **közös hozzáférési aláírások**, delegálása az Azure Storage-objektumokhoz való hozzáférést, adja meg, hogy csak a HTTPS protokollt használja, közös hozzáférésű Jogosultságkódokat, biztosítva, hogy bárki küldése használatakor lehetőséget is használható meg hivatkozásokat SAS-tokeneket a megfelelő protokollt fogja használni.

Ha engedélyezi a storage-fiókok a REST API-k hívása objektumok betartatásával módosíthatja a HTTPS használata [biztonságos átvitelre van szükség](../storage-require-secure-transfer.md) a tárfiókhoz. Ha ez engedélyezve van a kapcsolat HTTP-n keresztül program elutasítja.

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Azure-fájlmegosztások az átvitel során titkosítás segítségével
[Az Azure Files](../files/storage-files-introduction.md) támogatja a titkosítást a HTTPS és SMB 3.0-s keresztül, a fájl REST API használata esetén. Ha Azure-régión kívül csatlakoztatása az Azure-fájlmegosztás található, például a helyszínen vagy egy másik Azure-régióban, az SMB 3.0-titkosítás mindig szükség. Az SMB 2.1 nem támogatja a titkosítást, így alapértelmezés szerint az Azure-ban egy régión belül csak engedélyezett kapcsolatokat, de az SMB 3.0-s titkosítással kikényszeríthető [biztonságos átvitel megkövetelése](../storage-require-secure-transfer.md) a tárfiókhoz.

SMB 3.0-s titkosítással érhető el a [összes támogatott Windows és Windows Server operációs rendszerek](../files/storage-how-to-use-files-windows.md) kivéve a Windows 7 és Windows Server 2008 R2, amely csak támogatja az SMB 2.1. Szintén támogatott az SMB 3.0-s [macOS](../files/storage-how-to-use-files-mac.md) a disztribúciók [Linux](../files/storage-how-to-use-files-linux.md) használatával Linux-kernel 4.11 vagy újabb verzió. Az SMB 3.0 titkosítás támogatása is a Linux kernel számos Linux-disztribúció által régebbi verzióira backported, tekintse át [ismertetése SMB ügyfélkövetelmények](../files/storage-how-to-use-files-linux.md#smb-client-reqs).

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Az ügyféloldali titkosítással Storage küldött adatok biztonságossá tételéhez
Egy másik lehetőség, amely segítséget nyújt a győződjön meg arról, hogy az adatok biztonságos közben egy ügyfélalkalmazás és a Storage között az ügyféloldali titkosítás. Az adatok titkosítása az Azure Storage-ba kerül az átvitel előtt. Amikor az adatok beolvasása az Azure Storage-ból, az adatok az ügyféloldalon után már visszafejteni. Annak ellenére, hogy az adatok titkosított a hálózaton keresztül történik, azt javasoljuk, hogy a HTTPS PROTOKOLLT, is használja, mert adatok sértetlenségi ellenőrzések, amely segít a hálózat kapcsolatos hibák elhárítása, hogy ez hatással lenne az adatok integritását a beépített.

Ügyféloldali titkosítás a is módszer az inaktív adatok titkosításához, a titkosított formában tárolja az adatokat. Tárgyaljuk Ez a szakasz részletesen a [titkosítás inaktív állapotban](#encryption-at-rest).

## <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban
Nincsenek három Azure-funkciók nyújtanak az inaktív adatok titkosítását. Az Azure Disk Encryption az operációs rendszer és az adatlemezek IaaS virtuális gépek titkosítására szolgál. Ügyféloldali titkosítás és SSE egyaránt használhatók az Azure Storage-adatok titkosításához. 

Ügyféloldali titkosítás segítségével titkosíthatja az adatokat átvitel közben (amely a tárolási titkosítás nélkül is tárol), miközben érdemesebb lehet az átvitel közben HTTPS használatára, és rendelkezik az adatok automatikusan titkosítva tároljuk azokat, ha valamilyen módon. Kétféleképpen ehhez--az Azure Disk Encryption és SSE. Az egyik segítségével közvetlenül a virtuális gépek által használt operációsrendszer- és adatlemezek az adatok titkosításához, és a másik az Azure Blob Storage írt adatok titkosítására szolgál.

### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

Az SSE engedélyezve van az összes storage-fiók, és nem tiltható le. Az SSE automatikusan titkosítja az adatokat, azt az Azure Storage írásakor. Adatok olvasása az Azure Storage-ból, akkor visszaadná az Azure Storage által visszaadott előtt. Az SSE lehetővé teszi az adatok védelme a kód módosítása, vagy adja hozzá a kódot és más alkalmazások nélkül.

A Microsoft által felügyelt kulcsok vagy saját egyéni kulcsokat használhat. A Microsoft felügyelt kulcsokat hoz létre, és azok biztonságos tárolását, valamint azok rendszeres forgatását, belső Microsoft-szabályzat alapján kezeli. Egyéni kulcsok használatával kapcsolatos további információkért lásd: [ügyfél által kezelt kulcsok használata az Azure Key Vaultban a Storage Service Encryption](storage-service-encryption-customer-managed-keys.md).

Az SSE automatikusan titkosítja minden teljesítményszint (Standard és Prémium), minden üzembehelyezési modell (Azure Resource Manager és klasszikus) és minden Azure Storage-szolgáltatás (Blob, Queue, Table és File) adatait. 

### <a name="client-side-encryption"></a>Client-side Encryption
Ügyféloldali titkosítás az átvitel során az adatok titkosítása ismertetésekor említettük. Ez a funkció lehetővé teszi, hogy az ügyfélalkalmazás mielőtt elküldené a hálózaton keresztül az Azure Storage írható, és programozott módon visszafejteni az adatokat, az Azure Storage-ból beolvasása után az adatok programozott módon titkosíthatóak.

Ez biztosítja a titkosítás az átvitel során, de az inaktív adatok titkosítását funkcióját is biztosít. Bár az adatok átvitel közben titkosítva vannak ajánlott előnyeit, amelyekkel csökkenthető annak a hálózati hibák, hogy ez hatással lenne az adatok integritását a beépített sértetlenségi ellenőrzések HTTPS-en keresztül.

Egy példa, akkor előfordulhat, hogy ezzel, ha rendelkezik egy webalkalmazást, amely a blobok tárol és kér le a blobokat, és azt szeretné, hogy az alkalmazás és az adatok a lehető legbiztonságosabb lehet. Ebben az esetben használja az ügyféloldali titkosítás. Az ügyfél és az Azure Blob Service közötti adatforgalom a titkosított erőforrást tartalmaz, és senki sem maga értelmezheti az adatokat az átvitel során, és azt a privát blobokba pótlására.

Ügyféloldali titkosítás a Java és a .NET-tárolási-ügyfélkönyvtárak, amelyek a szerződéskötés után használhatják az Azure Key Vault API-k, így kell megvalósítania az be van építve. A folyamat, az adatok titkosítása és visszafejtése a boríték módszerrel, és a titkosítás, az egyes tárolási objektum által használt metaadatokat tárol. Ha például a blobok, tárolja, a blob metaadatai, az üzenetsorok, azt hozzáadja azt minden egyes üzenetsori üzenet.

A titkosítási magát Ön hozza létre, és a saját titkosítási kulcsok kezeléséhez. Használhatja az Azure Storage ügyféloldali kódtár által létrehozott kulcsok, vagy beállíthatja, hogy az Azure Key Vault, a kulcsok létrehozásához. A titkosítási kulcsokat a helyszíni fő storage-ban is tárolhatja, vagy tárolhatja őket egy Azure Key vaultban. Az Azure Key Vault lehetővé teszi az Azure Active Directory használatával adott felhasználók számára az Azure Key Vault titkos hozzáférést. Ez azt jelenti, hogy nem csak az Azure Key Vault olvashatja, és a használni kívánt ügyféloldali titkosítási kulcsok lekéréséhez.

#### <a name="resources"></a>További források
* [Az Azure Key Vault használatával a Microsoft Azure Storage blobok titkosítása és visszafejtése](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)

  Ez a cikk bemutatja, hogyan használhatja az ügyféloldali titkosítás az Azure Key Vault, beleértve a KEK létrehozása, és a PowerShell-lel tárolóban tárolja.
* [A Microsoft Azure Storage ügyféloldali titkosítása és az Azure Key Vault](../storage-client-side-encryption.md)

  Ez a cikk ügyféloldali titkosítás magyarázatot nyújt, és példákat talál, a storage ügyféloldali kódtár használatával titkosítása és visszafejtése a négy tárolási szolgáltatások erőforrásait. Azt is az Azure Key Vault ismerteti.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Az Azure Disk Encryption segítségével a virtuális gépek által használt lemezek titkosítása
Az Azure Disk Encryption új funkciója. Ez a funkció lehetővé teszi, hogy az operációsrendszer-lemez és adatlemezek az IaaS virtuális gép által használt titkosítását. A Windows a meghajtók titkosítottak, iparági szabványnak megfelelő BitLocker titkosítási technológia használatával. A Linux rendszerre a lemezek titkosítottak, a DM-Crypt technológia használatával. Ez az Azure Key Vault lehetővé teszi a vezérlőelemet és kezelheti a lemeztitkosítási kulcsok van integrálva.

A megoldás a következő esetekben ha engedélyezve vannak a Microsoft Azure IaaS virtuális gépeket támogatja:

* Az Azure Key Vault-integráció
* Standard szintű virtuális gépek: [A, D, DS, G, GS és egyebekben sorozatú IaaS virtuális gépek](https://azure.microsoft.com/pricing/details/virtual-machines/)
* A Windows és Linux rendszerű IaaS virtuális gépek titkosításának engedélyezése
* A Windows IaaS virtuális gépek operációsrendszer- és a titkosítás letiltása meghajtók
* Linux rendszerű IaaS virtuális gépek adatmeghajtók titkosításának letiltása
* A Windows ügyfél operációs rendszer futtató IaaS virtuális gépek titkosításának engedélyezése
* Csatlakoztatási elérési úttal köteteken titkosításának engedélyezése
* Linux rendszerű virtuális gépek, amelyeken lemez összevonása (RAID) mdadm használatával titkosításának engedélyezése
* Adatlemezek LVM használatával Linux rendszerű virtuális gépek titkosításának engedélyezése
* A tárolóhelyek segítségével konfigurált Windows virtuális gépek titkosításának engedélyezése
* Minden nyilvános Azure-régióban támogatott

A megoldás nem támogatja a következő forgatókönyvek, szolgáltatások és technológiák a kiadásban:

* Alapszintű csomag IaaS virtuális gépek
* Linux rendszerű IaaS virtuális gépek egy operációs rendszer meghajtójának titkosításának letiltása
* A klasszikus virtuális gép létrehozási módszer használatával létrehozott IaaS virtuális gépeken
* Integráció a helyszíni kulcskezelő szolgáltatás
* Az Azure Files (megosztott fájlrendszert), a hálózati fájlrendszer (NFS), a dinamikus köteteket és a Windows virtuális gépek, amelyeken a szoftveres RAID-rendszerek


> [!NOTE]
> A következő Linux-disztribúciókon jelenleg támogatott Linux operációs rendszer lemeztitkosítás: az RHEL 7.2 CentOS 7.2n és Ubuntu 16.04.
>
>

Ez a funkció biztosítja, hogy a virtuálisgép-lemezeken lévő összes adat titkosítása az Azure Storage-ban.

#### <a name="resources"></a>További források
* [Az Azure Disk Encryption Windows és Linux rendszerű IaaS virtuális gépek](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)

### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Az Azure Disk Encryption SSE és ügyféloldali titkosítás összehasonlítása

#### <a name="iaas-vms-and-their-vhd-files"></a>IaaS virtuális gépek és a VHD-fájlok

Az adatlemezek IaaS virtuális gépek által használt az Azure Disk Encryption használata javasolt. Az Azure Marketplace-lemezkép használatával a nem felügyelt lemezekkel rendelkező virtuális Gépet hoz létre, ha az Azure elvégzi a [másolási sekély](https://en.wikipedia.org/wiki/Object_copying) a kép a tároló Azure Storage, valamint a fiók nincs titkosítva még akkor is, ha SSE engedélyezve van. Miután a virtuális Gépet hoz létre, és elindítja a lemezkép frissítése, SSE megkezdi az adatok titkosításához. Emiatt tanácsos a virtuális gépeken az Azure Disk Encryption az Azure Marketplace rendszerképekből létrehozott, ha azt szeretné, hogy teljes mértékben titkosított azokat a nem felügyelt lemezek használata. Ha felügyelt lemezekkel rendelkező virtuális Gépet hoz létre, SSE titkosítja az adatokat az összes platform által felügyelt kulcsokkal alapértelmezés szerint. 

Ha egy korábban titkosított virtuális Gépet az Azure-ba, a helyszíni tenné, tudják, töltse fel a titkosítási kulcsokat az Azure Key Vaultba, és továbbra is a titkosítást használni, hogy használja a helyszíni virtuális gép. Az Azure Disk Encryption engedélyezve van ez a forgatókönyv kezelésére.

Ha nem titkosított virtuális Merevlemezt a helyi, töltse fel az egyéni rendszerkép a katalógusban, és azt a virtuális gép létrehozása. Ha erre a Resource Manager-sablonokkal, megkérheti, hogy az Azure Disk Encryption kapcsolja be a virtuális gép indításkor.

Adatlemez hozzáadása, és csatlakoztassa a virtuális gépen, ha a lemezen levő adatok bekapcsolhatja az Azure Disk Encryption. Azt titkosítani fog helyileg, adatlemez először, és hogy a klasszikus üzemi modell réteg majd tegye a Lusta írási tárterületen, a storage tartalom titkosított.

#### <a name="client-side-encryption"></a>Ügyféloldali titkosítás
Ügyféloldali titkosítás a legbiztonságosabb módszer az adatok titkosításához azért, titkosítja az átvitt adatokat.  Azonban ez szükséges kód hozzáadása az alkalmazások, a tároló, amely előfordulhat, hogy nem szeretné. Ezekben az esetekben a HTTPS használatával teheti biztonságosabbá adatait az átvitel során. Miután adatokat eléri az Azure Storage, az SSE titkosítja.

Az ügyféloldali titkosítással táblaentitások üzenetsorbeli üzenetek és blobok használatával titkosítsa. 

Ügyféloldali titkosítás teljes mértékben az alkalmazás kezeli. Ez a legbiztonságosabb módszer, de kell programozott módosítja az alkalmazás és legfontosabb felügyeleti folyamatok vezetnek be. Ez akkor érdemes használnia, ha azt szeretné, a további biztonsági átvitel során, és azt szeretné, hogy a tárolt adatok titkosításához.

Ügyféloldali titkosítás a további terhelés az ügyfélen, és számolnia ehhez a méretezhetőség tervek, különösen akkor, ha az Ön titkosításához, és nagy mennyiségű adat átvitele.

#### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

Az SSE Azure Storage kezeli. Az SSE nem biztosít az átvitel során az adatok biztonságát, de titkosítja az adatokat, az Azure Storage-írás. Az SSE nem befolyásolja az Azure Storage teljesítményét.

Bármilyen típusú adatok a storage-fiók SSE használatával titkosíthatók (blokkblobokat, hozzáfűző blobokat, a lapblobok, tábla adatait, sorban lévő adatok és fájlok).

Ha az archív vagy könyvtár VHD-fájlok, amelyek új virtuális gépek létrehozásának alapjaként használja, hozzon létre egy új tárfiókot, és ezután töltse fel a VHD-fájlokat a fiókhoz. Az Azure Storage által adott VHD-fájlok lesznek titkosítva.

Ha az Azure Disk Encryption az adatlemezek virtuális gépen engedélyezve van, majd minden újonnan írt adatok titkosítása SSE mind az Azure Disk Encryption.

## <a name="storage-analytics"></a>Storage Analytics
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>A Storage Analytics segítségével engedélyezési figyelőtípus
Minden olyan tárfiókhoz engedélyezheti az Azure Storage Analytics hajtsa végre a naplózás és mérőszámok adatok tárolására. Ez az egy remek eszköz, akkor használja, ha szeretné, hogy a teljesítmény-mérőszámait egy tárfiókot, vagy kell hárítaniuk a storage-fiók, mert teljesítmény problémák merülnek fel.

A storage analytics naplók látható egy másik adat tároló elérésekor valaki által használt hitelesítési módszert. Például a Blob Storage, megjelenik egy közös hozzáférésű Jogosultságkód vagy a tárfiók kulcsaihoz használni őket, vagy ha a blob elérhető volt nyilvános.

Ez akkor lehet hasznos, ha szorosan esetlegesen korán hozzáférést a tárolóhoz. Például Blob Storage-ban is beállította a tárolók összes személyes és megvalósítását egy SAS-szolgáltatás használata során az alkalmazások. Ellenőrizze a naplókat rendszeresen e blobok érhetők el a tárfiók kulcsaihoz, amelyek a biztonság megsértése arra utalhat, használatával, vagy ha a blobok nyilvánosak legyenek, de azokat nem lehet.

#### <a name="what-do-the-logs-look-like"></a>Hajtsa végre a naplók kinézni?
Miután engedélyezi a tárfiókok mérőszámai és naplózás az Azure Portalon keresztül, elemzési adatok megkezdik – gyorsan megnövekedhet. A naplózás és mérőszámok az egyes szolgáltatások nem azonos; a naplózás csak akkor ír, amikor a forgalom a tárfiók, míg a metrikákat percenként, óránként vagy naponta, attól függően, hogy hogyan konfigurálhat lesz naplózva.

A naplók a blokkblobok, a storage-fiókban $logs nevű tárolóban vannak tárolva. Ez a tároló automatikusan létrejön, amikor a Storage Analytics engedélyezve van. Ez a tároló létrehozása után nem törölhető, bár törölheti a tartalmát.

A $logs tárolóban található minden egyes szolgáltatás egy mappa, és akkor nincsenek almappák számára, év és hónap/nap/óra. Óra alatt a naplók számozása. Ez az, hogy a könyvtárstruktúra fog kinézni:

![Naplófájlok megtekintése](./media/storage-security-guide/image1.png)

Az Azure Storage minden kérelmet a rendszer naplózza. Íme egy naplófájl, az első néhány mezők pillanatképét.

![A naplófájlok pillanatkép](./media/storage-security-guide/image2.png)

Láthatja, hogy a naplók bármilyen típusú tárfiók-hívások nyomon követéséhez használhatja.

#### <a name="what-are-all-of-those-fields-for"></a>Mik azok a mezők az összes?
Van egy cikk szerepel, amely alatt a naplókat, és hogy mire szolgálnak a sok mezők listáját jeleníti meg. A következő mezők, sorrendben:

![A mezők egy naplófájlban pillanatkép](./media/storage-security-guide/image3.png)

GetBlob vonatkozó bejegyzéseket érdekel, ezért hogyan jogosultak, ezért ellenőriznünk kell bejegyzéseket a "Get-Blob" művelettípus, és ellenőrizze a kérelem állapotát (negyedik</sup> oszlop) és a hitelesítési típus (nyolcadik</sup> oszlop).

Például a fenti felsorolásban első néhány sor, a kérelem-állapota "Sikeres" és a hitelesítési típus "hitelesítés". Ez azt jelenti, hogy a kérelem engedélyezését a tárfiók-kulcsot használ.

#### <a name="how-is-access-to-my-blobs-being-authorized"></a>Hogyan alakul a blobok hitelesítve a hozzáférést?
Rendelkezünk, amelyek foglalkozunk három esetben.

1. A blob nyilvános, és anélkül, hogy egy közös hozzáférésű Jogosultságkód URL-címen érhető el. Ebben az esetben a kérelem-állapota "AnonymousSuccess" és az engedélyezés-típus: "névtelen".

   1.0;2015-11-17T02:01:29.0488963Z;GetBlob;**AnonymousSuccess**;200;124;37;**anonymous**;;mystorage…
2. A blob magánjellegű, és egy közös hozzáférésű Jogosultságkód volt használva. Ebben az esetben a kérelem-állapota "SASSuccess" és az engedélyezés-típus: "sas".

   1.0;2015-11-16T18:30:05.6556115Z;GetBlob;**SASSuccess**;200;416;64;**sas**;;mystorage…
3. A blob nem nyilvános, és a tárfiók kulcsát használt-e férni. Ebben az esetben a kérelem-állapota "**sikeres**"és a hitelesítési típus"**hitelesített**".

   1.0-S; 2015-11-16T18:32:24.3174537Z; GetBlob; **Sikeres**; 206; 59; 22; **hitelesített**; mystorage...

A Microsoft Message Analyzer segítségével megtekintheti és elemezheti ezeket a naplókat. Keresés és szűrés funkciót tartalmaz. Például előfordulhat, hogy szeretné keresni megtekintéséhez a várttól, azaz a használat esetén győződjön meg arról, hogy valaki nem fér hozzá a tárfiók nem megfelelően GetBlob példányait.

#### <a name="resources"></a>További források
* [Storage Analytics](../storage-analytics.md)

  Ez a cikk az áttekintése a storage analytics, és hogyan engedélyezheti őket.
* [Storage Analytics naplóformátum](https://msdn.microsoft.com/library/azure/hh343259.aspx)

  Ez a cikk a Storage Analytics naplóformátum mutatja be, és adatokat, az elérhető mezők, például hitelesítési típus, amely a kéréshez használt hitelesítés típusát jelzi.
* [Az Azure Portal Tárfiók figyelése](../storage-monitor-storage-account.md)

  Ez a cikk bemutatja, hogyan konfigurálható a metrikákat a figyelés és naplózás storage-fiókok.
* [Teljes körű hibaelhárítás az Azure Storage-mérőszámok és a naplózás, az AzCopy és a Message Analyzer használatával](../storage-e2e-troubleshooting.md)

  Ez a cikk hibaelhárítás a Storage Analytics segítségével ismerteti és bemutatja, hogyan használhatja a Microsoft Message Analyzert.
* [A Microsoft Message Analyzer üzemeltetési útmutató](https://technet.microsoft.com/library/jj649776.aspx)

  Ez a cikk referenciaként szolgál a Microsoft Message Analyzert, és a egy oktatóanyag, a rövid útmutató és összefoglalásáról mutató hivatkozásokat tartalmaz.

## <a name="cross-origin-resource-sharing-cors"></a>Eltérő eredetű erőforrások megosztása (CORS)
### <a name="cross-domain-access-of-resources"></a>Tartományok közötti elérést erőforrások
Amikor az egy adott tartományban futó böngészővel HTTP-kérést erőforrások különböző tartományból, ezt az eltérő eredetű HTTP-kérés nevezzük. Például a contoso.com szolgáltatja a HTML-lapok fabrikam.blob.core.windows.net lévő üzemeltetett JPEG-fájlok kérelmet küld. Biztonsági okokból a böngészők korlátozza a parancsprogramok, például a JavaScript belül kezdeményezett eltérő eredetű HTTP-kérelmekre. Ez azt jelenti, hogy amikor a contoso.com webhelyen néhány JavaScript-kódot, hogy a fabrikam.blob.core.windows.net jpeg, a böngésző nem engedélyezi a kérelmet.

Mi nem ezt kell tennie az Azure Storage? Jól, ha statikus objektumokat, például JSON vagy XML-adatfájlokat tárolja a Blob Storage-tárfiókok használata nevű Fabrikam, az eszközök a tartomány lesz fabrikam.blob.core.windows.net, és a contoso.com webes alkalmazás nem fogja tudni elérni őket használatával A JavaScript, mert a tartományok különböznek. Ez akkor is igaz, ha hívásához egy Azure Storage-szolgáltatás – például a Table Storage –, amely JSON a JavaScript-ügyfél által feldolgozandó adatokat adja vissza.

#### <a name="possible-solutions"></a>A lehetséges megoldásokról
One way to resolve this is to assign a custom domain like "storage.contoso.com" to fabrikam.blob.core.windows.net. A probléma oka, hogy hozzárendelhet, hogy egyéni tartományt egy storage-fiókba. Mi történik, ha az eszközök vannak tárolva több tárfiókra?

A probléma megoldásához egy másik módja, hogy a webalkalmazás-proxyként a storage-hívásokhoz. Ez azt jelenti, ha tölt fel egy fájlt a Blobtárolóba, a webalkalmazás lenne, vagy helyileg írja, majd másolja azt a Blob Storage vagy azt a memóriába minden adatot és írni a Blob Storage. Másik lehetőségként is írható egy dedikált webes alkalmazás (például egy webes API-t), amely feltölti a fájlokat helyileg, és a Blob Storage írja őket. Mindkét esetben számolnia függvény meghatározása a méretezhetőség van szükség.

#### <a name="how-can-cors-help"></a>Hogyan segít a CORS?
Az Azure Storage lehetővé teszi, hogy engedélyezze a CORS-idegen eredetű erőforrások megosztása. Minden olyan tárfiókhoz a tárfiók az erőforrások eléréséhez tartományok is megadhat. Például ebben az esetben fent vázolt hozunk is a fabrikam.blob.core.windows.net tárfiók CORS engedélyezése és konfigurálása, hogy engedélyezze a hozzáférést a contoso.com. Ezután a webes alkalmazás contoso.com közvetlenül hozzáférhet fabrikam.blob.core.windows.net lévő erőforrásokat.

Megjegyzés: az egyik dolog, hogy a CORS lehetővé teszi a hozzáférést, de nem biztosít hitelesítést, amely szükséges az összes nem-nyilvános hozzáférés tároló-erőforrások. Ez azt jelenti, hogy csak akkor férhet blobokat, ha azok nyilvános vagy egy közös hozzáférésű Jogosultságkód biztosítva a megfelelő engedélyekkel az szerepel. Táblák, üzenetsorok és fájlok nem nyilvános férhetnek és SAS igényli.

A CORS az összes szolgáltatás alapértelmezés szerint le van tiltva. A CORS szolgáltatás házirendek beállítása a módszerek meghívására a REST API vagy a storage ügyféloldali kódtár használatával engedélyezheti. Ha így tesz, meg kell adnia a CORS-szabály, amely XML-ben. Íme egy példa a CORS-szabály, amely a szolgáltatás tulajdonságainak beállítása műveletet a Blob szolgáltatás segítségével egy storage-fiók van beállítva. A storage ügyféloldali kódtára vagy a REST API-k használatával az Azure Storage-művelet elvégzése.

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Íme, mi minden egyes sor azt jelenti, hogy:

* **AllowedOrigins** azonban igen, mely nem egyező tartományok kérelem, és a storage szolgáltatás adat fogadását. Ez azt jelzi, hogy a contoso.com és fabrikam.com kérhetnek adatok Blob Storage-ból egy adott tárfiók. A helyettesítő karakter is állíthat (\*) azokat a tartományokat, a hozzáférési kérelem engedélyezéséhez.
* **AllowedMethods** Ez a metódusok (HTTP-kéréssel kapcsolatos műveletek), amely a kérés létrehozásakor használható. Ebben a példában csak a PUT és a GET használható. Beállíthatja a helyettesítő karakter (\*), hogy az összes módszer használható.
* **AllowedHeaders** Ez az a kérelem fejlécében, hogy a forrástartomány adhatja meg a kérés létrehozásakor. Ebben a példában minden metaadat fejléc kezdve az x-ms-metaadatok, x-ms-meta-célként, és az x-ms-meta-abc használata engedélyezett. A helyettesítő karakter (\*) azt jelzi, hogy engedélyezve van-e a megadott előtaggal minden fejléc kezdete.
* **ExposedHeaders** azonban igen, melyik válaszfejlécek ki lehetnek téve a böngésző megjeleníthet a kérelem kibocsátója. Ebben a példában minden fejléc kezdve "x-ms - meta-" lesz közzétéve.
* **MaxAgeInSeconds** Ez az a maximális időt, hogy a böngésző gyorsítótárazza-e a az előzetes OPTIONS kérést. (Az ellenőrzési kérés kapcsolatos további információkért ellenőrizze az alábbi első cikkre.)

#### <a name="resources"></a>További források
Cors-t és engedélyezését a kapcsolatos további információkért tekintse meg ezeket az erőforrásokat.

* [Eltérő eredetű erőforrások megosztása (CORS) támogatása az Azure Storage szolgáltatások az Azure.com webhelyen](../storage-cors-support.md)

  Ez a cikk a cors-t és a különböző tárolási szolgáltatások szabályainak beállítása áttekintést nyújt.
* [Eltérő eredetű erőforrások megosztása (CORS) támogatása az Azure Storage-szolgáltatások, az MSDN-en](https://msdn.microsoft.com/library/azure/dn535601.aspx)

  Ez az a CORS-támogatás az Azure Storage Services dokumentációja. Ez cikkek alkalmazása minden egyes storage szolgáltatásra mutató hivatkozások szerepelnek és a egy példát mutat be, és ismerteti az egyes elemei a CORS-fájlban.
* [A Microsoft Azure Storage: A CORS bemutatása](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

  Ez a CORS bejelentése, és hogyan használható a bemutató a kezdeti blog cikkre mutató hivatkozás.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Gyakori kérdések az Azure Storage biztonsága
1. **Hogyan lehet a blobok I vagy onnan máshová az Azure Storage vagyok átvitel, nem a HTTPS protokoll használata sértetlenségének ellenőrzése?**

   Ha bármilyen okból kell használni a HTTP helyett HTTPS és a blokkblobok dolgozik, használhatja a MD5-ellenőrzést a folyamatban lévő átvitelben érintett blobok sértetlenségének ellenőrzése érdekében. Ez akkor hasznos, hálózati transport layer hibák elleni védelem, de nem feltétlenül köztes támadások.

   Ha a HTTPS, amely lehetővé teszi a biztonság, akkor az MD5-ellenőrzést használata redundáns és szükségtelen is használhatja.

   További információkért tekintse meg a [Azure Blob MD5 áttekintése](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).
2. **Mi a helyzet a FIPS-megfelelőségét az Egyesült Államok Government?**

   Az Egyesült Államok Szövetségi információk feldolgozása Standard (FIPS) határozza meg az USA által jóváhagyott titkosítási algoritmusokat Szövetségi kormányzati számítógépes rendszereihez, bizalmas adatok védelme. A FIPS engedélyezése mód a Windows server vagy az asztal arra utasítja az operációs rendszer, hogy csak a FIPS szabványú titkosítási algoritmusok kell használni. Ha egy alkalmazás nem megfelelő algoritmusokat használ, az alkalmazások megszakadnak. With.NET keretrendszer-verziókat 4.5.2-es vagy újabb verziója szükséges, az alkalmazás automatikusan átvált a titkosítási algoritmusok használatát FIPS előírásainak megfelelő algoritmusok használata, ha a számítógép FIPS-módban.

   A Microsoft minden ügyfél engedélyezése a FIPS-módban kell-e legfeljebb hagyja azt. Úgy véli, hogy az ügyfelek, akik nem hatósági szabályozás alá eső FIPS-módban engedélyezése alapértelmezés szerint nem meggyőző ok.

### <a name="resources"></a>További források
* [Miért éppen nem javasoljuk "FIPS-módban" többé](https://blogs.technet.microsoft.com/secguide/2014/04/07/why-were-not-recommending-fips-mode-anymore/)

  Ez a cikk blog FIPS áttekintést, és azt ismerteti, miért nem teszik FIPS-módban alapértelmezés szerint.
* [A FIPS 140 érvényesítése](https://technet.microsoft.com/library/cc750357.aspx)

  Ez a cikk nyújt információkat a hogyan Microsoft-termékek és titkosítási moduljait megfelel a FIPS-szabvány esetében az USA Szövetségi kormányzati.
* ["Rendszer-kriptográfia: használja a FIPS szabványnak megfelelő algoritmusokat a titkosításhoz, kivonatoláshoz és aláíráshoz" biztonsági beállítások hatások a Windows XP és a Windows újabb verziói](https://support.microsoft.com/kb/811833)

  Ez a cikk ismerteti a régebbi Windows-számítógépek FIPS-módban használatát.