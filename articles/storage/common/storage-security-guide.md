---
title: Az Azure Storage biztonsági útmutatója | Microsoft Docs
description: Részletesen ismerteti az Azure Storage biztonságossá tételének számos módszerét, beleértve a RBAC, a Storage Service Encryptiont, az ügyféloldali titkosítást, az SMB 3,0 és a Azure Disk Encryption.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 72e695762f2e45309787e6f62fa97aae4c959f34
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598093"
---
# <a name="azure-storage-security-guide"></a>Az Azure Storage biztonsági útmutatója

Az Azure Storage egy átfogó biztonsági képességeket kínál, amelyek lehetővé teszik a fejlesztők számára, hogy biztonságos alkalmazásokat hozzanak létre:

- Az Azure Storage-ba írt összes adat (beleértve a metaadatokat is) [Storage Service encryption (SSE)](storage-service-encryption.md)használatával automatikusan titkosítva lesz. További információ: az [Azure Blobok, fájlok, táblák és Queue Storage alapértelmezett titkosításának bejelentése](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- Az Azure Storage-ban a Azure Active Directory (Azure AD) és a szerepköralapú Access Control (RBAC) is támogatott az erőforrás-kezelési műveletek és az adatműveletek esetében, az alábbiak szerint:   
    - A RBAC szerepköröket hozzárendelheti a rendszerbiztonsági tagekhez, és az Azure AD használatával engedélyezheti az erőforrás-kezelési műveleteket, például a kulcskezelő műveleteket.
    - Az Azure AD-integráció a blob-és üzenetsor-adatműveletek esetében támogatott. RBAC-szerepköröket rendelhet hozzá egy előfizetéshez, egy erőforráscsoporthoz, egy Storage-fiókhoz vagy egy adott tárolóhoz vagy várólistához egy rendszerbiztonsági tag vagy egy felügyelt identitás számára az Azure-erőforrások számára. További információ: az [Azure Storage hozzáférésének hitelesítése Azure Active Directory használatával](storage-auth-aad.md).   
- Az alkalmazások és az Azure között az [ügyféloldali titkosítás](../storage-client-side-encryption.md), a HTTPS vagy az SMB 3,0 használatával lehet biztosítani az adatátvitelt.  
- Az Azure-beli virtuális gépek által használt operációs rendszer és adatlemezek [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md)használatával titkosíthatók.
- Az Azure Storage-beli adatobjektumokhoz delegált hozzáférés a közös hozzáférési aláírás használatával adható meg. További információ: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](storage-sas-overview.md).

Ez a cikk áttekintést nyújt az Azure Storage szolgáltatással használható biztonsági funkciókról. A hivatkozások olyan cikkek számára érhetők el, amelyek részletesen ismertetik az egyes funkciók részleteit, így könnyebben végezhet további vizsgálatot az egyes témakörökben.

A cikk a jelen cikkben tárgyalt témaköröket tartalmazza:

* [Felügyeleti sík biztonsága](#management-plane-security) – a Storage-fiók biztonságossá tétele

  A felügyeleti sík a Storage-fiók kezeléséhez használt erőforrásokból áll. Ez a szakasz ismerteti a Azure Resource Manager üzembe helyezési modellt, valamint a szerepköralapú Access Control (RBAC) használatát a Storage-fiókokhoz való hozzáférés szabályozásához. Emellett a Storage-fiók kulcsainak kezelésével és azok újralétrehozásával foglalkozik.
* [Adatközpontok biztonsága](#data-plane-security) – az adataihoz való hozzáférés biztosítása

  Ebben a szakaszban bemutatjuk, hogyan lehet hozzáférést engedélyezni a Storage-fiókban lévő tényleges adatobjektumokhoz, például blobokhoz, fájlokhoz, várólistákhoz és táblákhoz, közös hozzáférési aláírások és tárolt hozzáférési szabályzatok használatával. A szolgáltatási szintű SAS és a fiók szintű SAS is kiterjed. Azt is megtudhatja, hogyan korlátozhatja a hozzáférést egy adott IP-címhez (vagy IP-tartományhoz), hogyan korlátozhatja a HTTPS-hez használt protokollt, és hogyan vonhatja vissza a közös hozzáférésű aláírásokat anélkül, hogy a lejárat előtt kellene várnia.
* [Titkosítás az átvitel során](#encryption-in-transit)

  Ebből a szakaszból megtudhatja, hogyan védheti meg az adatok védelmét, ha az Azure Storage-ba vagy onnan helyezi át. A HTTPS javasolt használatát és az SMB 3,0 Azure-fájlmegosztás által használt titkosítását is tárgyaljuk. Emellett megtekintheti az ügyféloldali titkosítást is, amely lehetővé teszi az adattitkosítást, mielőtt a rendszer átmásolja az ügyfél-alkalmazásba a tárolóba, és visszafejti az adatmennyiséget a tárterületről való átadást követően.
* [Titkosítás inaktív állapotban](#encryption-at-rest)

  Az új és a meglévő Storage-fiókok számára automatikusan elérhetővé vált Storage Service Encryption (SSE). Azt is megvizsgáljuk, hogyan használhatók a Azure Disk Encryption és megismerhetik a lemez titkosításának alapvető különbségeit és eseteit, valamint az SSE-t és az ügyféloldali titkosítást. Röviden megvizsgáljuk az Egyesült államokbeli kormányzati számítógépek FIPS-megfelelőségét.
* Az Azure Storage hozzáférésének naplózása [Storage Analytics](#storage-analytics) használatával

  Ez a szakasz azt ismerteti, hogyan lehet információt megkeresni a Storage Analytics naplóiban egy kérelemhez. Tekintse meg a valódi Storage Analytics-naplófájlok részletes ismertetését, és Ismerje meg, hogyan állapítható meg, hogy a rendszer a Storage-fiók kulcsával, közös hozzáférésű aláírással vagy névtelenül, illetve sikeres vagy sikertelen kéréssel kezdeményezett-e kérelmet.
* [Böngészőalapú ügyfelek engedélyezése a CORS használatával](#cross-origin-resource-sharing-cors)

  Ez a szakasz a több eredetű erőforrás-megosztás (CORS) engedélyezését ismerteti. Az Azure Storage-ba épített CORS képességekkel foglalkozunk a tartományok közötti hozzáféréssel, valamint az azok kezelésével.

## <a name="management-plane-security"></a>Felügyeleti sík biztonsága
A felügyeleti sík olyan műveletekből áll, amelyek hatással vannak a Storage-fiókra. Létrehozhat vagy törölhet például egy Storage-fiókot, lekérheti az előfizetéshez tartozó Storage-fiókok listáját, lekérheti a Storage-fiók kulcsait, vagy újragenerálhatja a Storage-fiók kulcsait.

Új Storage-fiók létrehozásakor ki kell választania a klasszikus vagy a Resource Manager üzembe helyezési modelljét. Az Azure-beli erőforrások létrehozásának klasszikus modellje csak az előfizetéshez való teljes vagy semmis hozzáférést teszi lehetővé, viszont a Storage-fiókot.

Ez az útmutató a Resource Manager-modellre összpontosít, amely a Storage-fiókok létrehozásához ajánlott módszer. A Resource Manager-alapú Storage-fiókokkal a teljes előfizetéshez nem biztosít hozzáférést, a szerepköralapú Access Control (RBAC) használatával szabályozhatja a hozzáférést a felügyeleti síkon.

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>A Storage-fiók biztonságossá tétele szerepköralapú Access Control (RBAC) használatával
Beszéljünk arról, hogy mi a RBAC, és hogyan használható. Minden Azure-előfizetés Azure Active Directoryval rendelkezik. A címtárban lévő felhasználók, csoportok és alkalmazások hozzáférést biztosíthatnak a Resource Manager-alapú üzemi modellt használó Azure-előfizetés erőforrásainak kezeléséhez. Az ilyen típusú biztonságot szerepköralapú Access Control (RBAC) nevezzük. A hozzáférés kezeléséhez használhatja a [Azure Portal](https://portal.azure.com/), az [Azure CLI-eszközöket](../../cli-install-nodejs.md), a [PowerShellt](/powershell/azureps-cmdlets-docs)vagy az [Azure Storage erőforrás-szolgáltató REST API](https://msdn.microsoft.com/library/azure/mt163683.aspx)-jait.

A Resource Manager-modellel a Storage-fiókot egy erőforráscsoporthoz helyezheti el, és a Azure Active Directory használatával szabályozhatja az adott Storage-fiók felügyeleti síkjával való hozzáférést. Megadhatja például, hogy bizonyos felhasználók hozzáférjenek a Storage-fiók kulcsaihoz, míg más felhasználók megtekinthetik a Storage-fiók adatait, de nem férhetnek hozzá a Storage-fiók kulcsaihoz.

#### <a name="granting-access"></a>Hozzáférés biztosítása
A hozzáférés megadásához a megfelelő RBAC-szerepkört kell hozzárendelni a felhasználókhoz, csoportokhoz és alkalmazásokhoz a megfelelő hatókörben. Ahhoz, hogy hozzáférést biztosítson a teljes előfizetéshez, hozzá kell rendelnie egy szerepkört az előfizetési szinten. Az erőforráscsoport összes erőforrásához hozzáférést biztosíthat, ha az engedélyt az erőforráscsoporthoz is megadja. Adott erőforrásokhoz, például tárolási fiókokhoz is hozzárendelhet meghatározott szerepköröket.

Az alábbi fő pontok szükségesek az Azure Storage-fiók felügyeleti műveleteinek eléréséhez a RBAC használatával:

* Amikor hozzáférést rendel hozzá, alapvetően hozzárendel egy szerepkört ahhoz a fiókhoz, amelyhez hozzáférést szeretne adni. Szabályozhatja a Storage-fiók kezeléséhez használt műveletekhez való hozzáférést, de a fiókban lévő adatobjektumokhoz nem. Engedélyezheti például a Storage-fiók tulajdonságainak beolvasását (például a redundancia), de a tárolók vagy a Blob Storage belül lévő tárolóban lévő adattárolók számára is.
* Ahhoz, hogy valaki hozzáférhessen az adatobjektumokhoz a Storage-fiókban, engedélyt adhat nekik a Storage-fiók kulcsainak olvasásához, és a felhasználók ezeket a kulcsokat használhatják a Blobok, várólisták, táblák és fájlok eléréséhez.
* Szerepkörök rendelhetők egy adott felhasználói fiókhoz, egy felhasználói csoporthoz vagy egy adott alkalmazáshoz.
* Minden szerepkör tartalmaz egy listát a műveletekről, és nem végez műveleteket. Például a virtuális gép közreműködői szerepkör "Listkeys műveletének beolvasása" művelettel rendelkezik, amely lehetővé teszi a Storage-fiók kulcsainak olvasását. A közreműködő "nem műveletekkel" rendelkezik, például a felhasználók hozzáférésének frissítése a Active Directoryban.
* A Storage szerepkörei közé tartoznak a következő szerepkörök (de nincsenek korlátozva):

  * Tulajdonos – mindent kezelhet, beleértve a hozzáférést is.
  * Közreműködő – a tulajdonos bármit elvégezhet, kivéve a hozzáférés hozzárendelését. A szerepkörrel rendelkező személy megtekintheti és újragenerálhatja a Storage-fiók kulcsait. A Storage-fiók kulcsainak segítségével hozzáférhetnek az adatobjektumokhoz.
  * Olvasó – a titkokat kivéve a Storage-fiókra vonatkozó információkat tekinthetik meg. Ha például a Storage-fiókhoz hozzárendel egy olvasói engedélyekkel rendelkező szerepkört valakinek, megtekinthetik a Storage-fiók tulajdonságait, de nem módosíthatják a tulajdonságokat, vagy megtekinthetik a Storage-fiók kulcsait.
  * Storage-fiók közreműködői – kezelhetik a Storage-fiókot – elolvashatják az előfizetés erőforrás-csoportjait és erőforrásait, valamint előfizetési erőforráscsoport-telepítéseket hozhatnak létre és kezelhetnek. Emellett hozzáférhetnek a Storage-fiók kulcsaihoz is, ami azt jelenti, hogy hozzáférhetnek az adatsíkon.
  * Felhasználói hozzáférés rendszergazdája – a Storage-fiókhoz való felhasználói hozzáférést is kezelhetik. Megadhatják például, hogy az olvasó hozzáférjen egy adott felhasználóhoz.
  * Virtuális gép közreműködői – a virtuális gépeket kezelhetik, nem pedig azt a Storage-fiókot, amelyhez csatlakoznak. Ez a szerepkör felsorolja a Storage-fiók kulcsait, ami azt jelenti, hogy a szerepkört hozzárendelő felhasználó frissítheti az adatsíkon.

    Ahhoz, hogy egy felhasználó virtuális gépet hozzon létre, létre kell hoznia a megfelelő VHD-fájlt egy Storage-fiókban. Ehhez le kell tudniuk kérni a Storage-fiók kulcsát, és át kell adni a virtuális gépet létrehozó API-nak. Ezért rendelkezniük kell ezzel az engedéllyel, hogy fel tudják sorolni a Storage-fiók kulcsait.
* Az egyéni szerepkörök definiálásának lehetősége egy olyan szolgáltatás, amely lehetővé teszi, hogy az Azure-erőforrásokon végrehajtható elérhető műveletek listájából hozzon létre műveleteket.
* Ahhoz, hogy szerepkört rendeljen hozzá, a felhasználónak be kell állítania a Azure Active Directory.
* Létrehozhat egy jelentést, amely a PowerShell vagy az Azure CLI használatával/kitől és milyen hatókörön keresztül engedélyezte vagy visszavonta a hozzáférést.

#### <a name="resources"></a>Segédanyagok és eszközök
* [Azure Active Directory szerepköralapú hozzáférés-vezérlése](../../role-based-access-control/role-assignments-portal.md)

  Ez a cikk az Azure Active Directory szerepkörön alapuló hozzáférés-vezérlését és annak működési módját ismerteti.
* [RBAC: Beépített szerepkörök](../../role-based-access-control/built-in-roles.md)

  Ez a cikk a RBAC-ben elérhető összes beépített szerepkört részletezi.
* [A Resource Manager-alapú és a klasszikus üzembe helyezés ismertetése](../../azure-resource-manager/resource-manager-deployment-model.md)

  Ez a cikk a Resource Manager-alapú és a klasszikus üzembe helyezési modelleket ismerteti, valamint ismerteti a Resource Manager és az erőforráscsoportok használatának előnyeit. Elmagyarázza, hogy az Azure számítási, hálózati és tárolási szolgáltatói hogyan működnek a Resource Manager-modellben.
* [Szerepköralapú hozzáférés-vezérlés kezelése REST API-val](../../role-based-access-control/role-assignments-rest.md)

  Ez a cikk leírja, hogyan használható a REST API az RBAC kezeléséhez.
* [Az Azure Storage erőforrás-szolgáltató REST API-ja – referencia](https://msdn.microsoft.com/library/azure/mt163683.aspx)

  Ez az API-hivatkozás azokat a API-kat ismerteti, amelyek segítségével a Storage-fiók programozott módon kezelhető.

* [Szerepköralapú hozzáférés-vezérlés az Ignite-tól a Microsoft Azure számára](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

  Ez a hivatkozás a Channel 9 2015-ös MS Ignite-konferencia videójára mutat. Ebben a részben arról beszélnek, milyen hozzáférés-kezelési és jelentési képességeket nyújt az Azure, és bemutatják az Azure-előfizetés hozzáférés-biztosításának legjobb gyakorlatait az Azure Active Directory használatával.

### <a name="managing-your-storage-account-keys"></a>A Storage-fiók kulcsainak kezelése
A Storage-fiók kulcsai az Azure által létrehozott 512 bites karakterláncok, amelyek a Storage-fiók nevével együtt használhatók a Storage-fiókban tárolt adatobjektumok eléréséhez, például Blobok, táblákon belüli entitások, üzenetsor-üzenetek és egy Azure-fájlmegosztás fájljai. A Storage-fiók kulcsaihoz való hozzáférés szabályozása szabályozza az adott Storage-fiók adatsíkjainak elérését.

Minden egyes Storage-fiókhoz két kulcs tartozik: "Key 1" és "Key 2" a [Azure Portal](https://portal.azure.com/) és a PowerShell-parancsmagokban. Ezek manuálisan is újrahozhatók a több módszer egyikével, például a [Azure Portal](https://portal.azure.com/), a PowerShell, az Azure CLI vagy programozott módon, a .net Storage ügyféloldali kódtára vagy az Azure storage Services REST API használatával.

A Storage-fiók kulcsainak újragenerálása tetszőleges számú oka lehet.

* Biztonsági okokból rendszeres időközönként újra létrehozhatja őket.
* A Storage-fiók kulcsainak újragenerálása akkor történik meg, ha valaki egy alkalmazásba próbál beolvasni, és beolvassa a konfigurációs fájlban hardcoded vagy mentett kulcsot, így teljes hozzáférést biztosít számukra a Storage-fiókhoz.
* A kulcs újragenerálásának egy másik esete, ha a csapata olyan Storage Explorer alkalmazást használ, amely megőrzi a Storage-fiók kulcsát, és az egyik csapattag elhagyja a csapatot. Az alkalmazás továbbra is működni fog, és hozzáférést biztosít számukra a Storage-fiókhoz, miután elfogytak. Ez valójában az elsődleges ok, amiért fiók szintű közös hozzáférési aláírásokat hoztak létre – a hozzáférési kulcsok egy konfigurációs fájlban való tárolása helyett használhat fiók szintű SAS-t.

#### <a name="key-regeneration-plan"></a>Kulcs újragenerálási terve
Nem szeretné, hogy csak az Ön által használt kulcsot újragenerálta a tervezés nélkül. Ha ezt teszi, kihasználhatja a Storage-fiókhoz való összes hozzáférést, ami jelentős fennakadást okozhat. Ezért van két kulcs. Egyszerre csak egy kulcsot kell újrakészítenie.

A kulcsok újragenerálása előtt győződjön meg arról, hogy a Storage-fióktól függő összes alkalmazás, valamint az Azure-ban használt egyéb szolgáltatások listája szerepel. Ha például olyan Azure Media Services használ, amelyek a Storage-fióktól függenek, a kulcs újragenerálása után újra kell szinkronizálnia a hozzáférési kulcsokat a Media szolgáltatással. Ha olyan alkalmazásokat használ, mint például a Storage Explorer, az új kulcsokat is meg kell adnia az alkalmazások számára. Ha olyan virtuális gépekkel rendelkezik, amelyek VHD-fájljai a Storage-fiókban tárolódnak, a Storage-fiók kulcsainak újragenerálása nem érinti őket.

A kulcsok újragenerálása a Azure Portal. A kulcsok újragenerálása után akár 10 percet is igénybe vehet, hogy szinkronizálni lehessen a tárolási szolgáltatások között.

Ha elkészült, az általános folyamat részletesen ismerteti, hogyan kell módosítani a kulcsot. Ebben az esetben feltételezhető, hogy jelenleg az 1. kulcsot használja, és mindent megváltoztat a 2. kulcs használatához.

1. A 2. kulcs újragenerálta annak érdekében, hogy biztonságos legyen. Ezt megteheti a Azure Portalban.
2. Az összes olyan alkalmazásban, ahol a tárolási kulcs tárolva van, módosítsa a Storage-kulcsot a 2. kulcs új értékének használatára. Tesztelje és tegye közzé az alkalmazást.
3. Miután az összes alkalmazás és szolgáltatás sikeresen működik, újragenerálta az 1. kulcsot. Ezzel biztosíthatja, hogy bárki, akinek nem adta meg kifejezetten az új kulcsot, többé nem lesz hozzáférése a Storage-fiókhoz.

Ha jelenleg a 2. kulcsot használja, ugyanazt a folyamatot használhatja, de megfordíthatja a kulcsok nevét.

Több napig is áttelepítheti az alkalmazást, és az egyes alkalmazásokat az új kulcs használatára és közzétételre módosíthatja. Miután mindegyik elkészült, térjen vissza, és újból létrehozza a régi kulcsot, így már nem működik.

Egy másik lehetőség, hogy a Storage-fiók kulcsát egy titkos [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) helyezi el, és az alkalmazásokból lekéri a kulcsot. Ezután a kulcs újbóli létrehozása és a Azure Key Vault frissítése után az alkalmazásoknak nem kell újratelepíteniük, mert az új kulcsot a Azure Key Vault automatikusan átveszik. Vegye figyelembe, hogy az alkalmazásnak minden alkalommal el kell olvasnia a kulcsot, vagy gyorsítótárazhatja a memóriában, és ha nem sikerül a használata során, kérje le újra a kulcsot a Azure Key Vault.

A Azure Key Vault használata emellett további biztonsági szintet is biztosít a tárolási kulcsok számára. Ha ezt a módszert használja, a hardcoded soha nem fog megjelenni a tárolási kulcs, ami eltávolítja azt a lehetőséget, hogy valaki hozzáférést kap a kulcsokhoz adott engedély nélkül.

A Azure Key Vault használatának egy másik előnye, hogy a kulcsok elérését Azure Active Directory használatával is szabályozhatja. Ez azt jelenti, hogy hozzáférést adhat a néhány olyan alkalmazáshoz, amelyeknek le kell kérnie a kulcsokat a Azure Key Vaultból, és tudnia kell, hogy más alkalmazások nem fognak tudni hozzáférni a kulcsokhoz anélkül, hogy az engedélyt kifejezetten megadják.

> [!NOTE]
> A Microsoft azt javasolja, hogy egyszerre csak az egyik kulcsot használja az összes alkalmazásban. Ha egyes helyeken és a 2. kulcsban az 1. kulcsot használja másokban, nem fogja tudni elforgatni a kulcsokat anélkül, hogy egy alkalmazás elveszíti a hozzáférést.

#### <a name="resources"></a>Segédanyagok és eszközök

* [Tárfiók beállításainak kezelése az Azure Portalon](storage-account-manage.md)
* [Az Azure Storage erőforrás-szolgáltató REST API-ja – referencia](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>Adatsík biztonsága
Az adatsík biztonsága az Azure Storage-ban tárolt adatobjektumok (Blobok, várólisták, táblák és fájlok) védelméhez használt módszerekre utal. Láttuk az adatátvitel során az adatforgalom titkosítására és a biztonságra vonatkozó módszereket, de hogyan lehet az objektumokhoz való hozzáférés szabályozására?

Három lehetőség közül választhat az Azure Storage-beli adatobjektumokhoz való hozzáférés engedélyezéséhez, beleértve a következőket:

- Az Azure AD használata a tárolók és a várólisták elérésének engedélyezéséhez. Az Azure AD számos előnyt biztosít az engedélyezés más módszereihez képest, beleértve a kód titkos kódjának tárolásának szükségességét is. További információ: az [Azure Storage hozzáférésének hitelesítése Azure Active Directory használatával](storage-auth-aad.md). 
- A Storage-fiók kulcsainak használata a megosztott kulcson keresztüli hozzáférés engedélyezéséhez. A megosztott kulcson keresztüli engedélyezéshez a Storage-fiók kulcsainak tárolása szükséges az alkalmazásban, ezért a Microsoft az Azure AD használatát javasolja, ha lehetséges.
- Közös hozzáférésű aláírások használatával meghatározott időtartamra vonatkozó szabályozott engedélyeket adhat meg bizonyos adatobjektumokhoz.

Emellett a Blob Storage esetében engedélyezheti a Blobok nyilvános elérését a blobokat tartalmazó tároló hozzáférési szintjének beállításával. Ha egy tárolóhoz hozzáférést állít be Blobhoz vagy tárolóhoz, az lehetővé teszi a nyilvános olvasási hozzáférést a tárolóban lévő blobokhoz. Ez azt jelenti, hogy a tárolóban lévő blobra mutató URL-címmel bárki megnyithatja azt egy böngészőben közös hozzáférési aláírás vagy a Storage-fiók kulcsainak használata nélkül.

Az engedélyezéssel való hozzáférés korlátozása mellett a [tűzfalak és virtuális hálózatok](storage-network-security.md) használatával a hálózati szabályok alapján korlátozhatja a hozzáférést a Storage-fiókhoz.  Ez a megközelítés lehetővé teszi a nyilvános internetes forgalom elérésének megtagadását, és csak bizonyos Azure-beli virtuális hálózatokhoz vagy nyilvános internetes IP-címtartományok elérését.

### <a name="storage-account-keys"></a>Tárfiókkulcsok
A Storage-fiók kulcsai az Azure által létrehozott 512 bites karakterláncok, amelyek a Storage-fiók nevével együtt használhatók a Storage-fiókban tárolt adatobjektumokhoz való hozzáféréshez.

Például elolvashatja a blobokat, írhat a várólistákba, táblákat hozhat létre, és módosíthatja a fájlokat. A műveletek többsége elvégezhető a Azure Portalon keresztül, vagy számos Storage Explorer alkalmazás valamelyikét használva. A műveletek elvégzéséhez kódot is írhat a REST API vagy a Storage ügyféloldali kódtárainak használatához.

A [felügyeleti sík biztonsága](#management-plane-security)című szakaszban leírtak szerint a klasszikus Storage-fiókhoz tartozó Storage-kulcsokhoz való hozzáférés az Azure-előfizetés teljes hozzáférését biztosíthatja. A Storage-fiókok Azure Resource Manager modell használatával történő elérését szerepköralapú Access Control (RBAC) segítségével lehet vezérelni.

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>A fiók objektumaihoz való hozzáférés delegálása közös hozzáférésű aláírások és tárolt hozzáférési szabályzatok használatával
A közös hozzáférésű aláírás egy olyan karakterlánc, amely egy olyan biztonsági jogkivonatot tartalmaz, amely olyan URI-hoz csatolható, amely lehetővé teszi a tároló objektumokhoz való hozzáférés delegálását, valamint olyan korlátozásokat határoz meg, mint például az engedélyek és a hozzáférési dátum/idő tartománya.

Hozzáférést adhat a blobokhoz, tárolóhoz, üzenetsor-üzenetekhez, fájlokhoz és táblákhoz. A táblázatokkal ténylegesen engedélyt kaphat a táblában lévő entitások tartományához való hozzáférésre. ehhez meg kell adnia a partíció és a sor kulcsának tartományait, amelyekhez hozzáférést szeretne adni a felhasználónak. Ha például egy földrajzi állapotú partíciós kulccsal tárolt adatmennyiséggel rendelkezik, akkor valaki hozzáférést adhat a Kalifornia-beli adatszolgáltatáshoz.

Egy másik példa arra, hogy egy webalkalmazásnak egy SAS-tokent ad, amely lehetővé teszi, hogy bejegyzéseket írjon egy várólistába, és egy feldolgozói szerepkört adjon egy SAS-tokennek, hogy üzeneteket kapjon a sorból, és feldolgozza azokat. Az is lehet, hogy egy ügyfél egy SAS-tokent ad, amellyel képeket tölthet fel egy tárolóba Blob Storageban, és webalkalmazási engedélyt adhat a képek olvasásához. Mindkét esetben fennáll a probléma elkülönítése – az egyes alkalmazások csak a számukra szükséges hozzáférést kaphatják meg a feladatuk elvégzéséhez. Ez a közös hozzáférésű aláírások használatával lehetséges.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Miért érdemes közös hozzáférési aláírásokat használni
Miért érdemes SAS-t használni ahelyett, hogy csak a Storage-fiók kulcsát adná meg, ami sokkal egyszerűbb? A Storage-fiók kulcsainak megadásával például megoszthatja a Storage-Királyság kulcsait. Teljes hozzáférést biztosít. Valaki használhatja a kulcsait, és feltöltheti a teljes zenei könyvtárat a Storage-fiókjába. A fájlokat vírussal fertőzött verziókkal is lecserélhetik, vagy ellopják az adataikat. Korlátlan hozzáférést biztosítanak a Storage-fiókhoz, ami nem vehető fel enyhén.

A közös hozzáférésű aláírásokkal csak a korlátozott időtartamra vonatkozó engedélyeket adhat meg az ügyfeleknek. Ha például valaki egy blobot tölt fel a fiókjába, az írási hozzáférést csak annyi idő után adhatja meg, hogy feltöltse a blobot (természetesen a blob méretétől függően). Ha meggondolja magát, visszavonhatja a hozzáférést.

Azt is megadhatja, hogy a SAS használatával létrehozott kérések egy adott IP-cím vagy az Azure-on kívüli IP-címtartomány számára legyenek korlátozva. Azt is megkövetelheti, hogy a kérések egy adott protokoll (HTTPS vagy HTTP/HTTPS) használatával legyenek létrehozva. Ez azt jelenti, hogy ha csak HTTPS-forgalmat szeretne engedélyezni, beállíthatja a szükséges protokollt csak HTTPS-re, és a HTTP-forgalom le lesz tiltva.

#### <a name="definition-of-a-shared-access-signature"></a>Közös hozzáférésű aláírás definíciója
A közös hozzáférésű aláírás a lekérdezési paraméterek egy halmaza, amely az erőforrásra mutató URL-címhez van hozzáfűzve

amely információt nyújt a hozzáférés engedélyezett állapotáról, valamint azt az időtartamot, ameddig a hozzáférés engedélyezett. Példa: Ez az URI öt percig olvasási hozzáférést biztosít egy blobhoz. Vegye figyelembe, hogy SAS-lekérdezési paramétereknek URL-kódolással kell rendelkezniük, például:% 3A a kettősponthoz (:) vagy a (z) %20 egy szóközzel.

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

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>A megosztott hozzáférés aláírásának engedélyezése az Azure Storage szolgáltatásban
Amikor a tárolási szolgáltatás megkapja a kérést, a bemeneti lekérdezési paramétereket veszi át, és létrehoz egy aláírást ugyanazzal a módszerrel, mint a hívó program. Ezután összehasonlítja a két aláírást. Ha megegyeznek, a tárolási szolgáltatás ellenőrizheti a tárolási szolgáltatás verzióját, hogy az érvényes-e, ellenőrizze, hogy az aktuális dátum és idő a megadott ablakban van-e, győződjön meg arról, hogy a kért hozzáférés megfelel a kérelemnek, stb.

Ha például a fenti URL-cím egy blob helyett egy fájlra mutat, ez a kérelem meghiúsul, mert megadja, hogy a megosztott elérési aláírás blobhoz van-e megadva. Ha a meghívott REST-parancs egy blob frissítését végzi, a művelet meghiúsul, mert a közös hozzáférési aláírás azt adja meg, hogy csak olvasási hozzáférés engedélyezett.

#### <a name="types-of-shared-access-signatures"></a>A közös hozzáférésű aláírások típusai
* A szolgáltatási szintű SAS használható egy Storage-fiókban lévő egyes erőforrások elérésére. Ilyenek például a tárolóban lévő Blobok listájának beolvasása, a Blobok letöltése, az entitások frissítése egy táblában, üzenetek hozzáadása egy várólistához vagy egy fájl feltöltése a fájlmegosztásba.
* A fiók szintű SAS minden olyan szolgáltatáshoz használható, amelyhez szolgáltatási szintű SAS használható. Emellett olyan erőforrás-lehetőségeket is biztosíthat, amelyek nem engedélyezettek a szolgáltatási szintű SAS-vel, például tárolók, táblák, várólisták és fájlmegosztás létrehozására való képesség. Egyszerre több szolgáltatáshoz is megadhat hozzáférést. Előfordulhat például, hogy valaki hozzáférést ad a Storage-fiókban található blobokhoz és fájlokhoz.

#### <a name="creating-a-sas-uri"></a>SAS URI létrehozása
1. Igény szerint létrehozhat egy URI-t, amely minden alkalommal meghatározza az összes lekérdezési paramétert.

   Ez a megközelítés rugalmas, de ha olyan logikai készlettel rendelkezik, amely minden alkalommal hasonló, a tárolt hozzáférési szabályzatok használatával jobb ötlet.
2. Létrehozhat egy tárolt hozzáférési szabályzatot a teljes tárolóhoz, a fájlmegosztáshoz, a táblához vagy a várólistához. Ezt követően ezt használhatja a létrehozott SAS URI-k alapjául. A tárolt hozzáférési szabályzatokon alapuló engedélyek egyszerűen visszavonhatók. Az egyes tárolók, várólisták, táblák vagy fájlmegosztás esetében legfeljebb öt házirendet lehet definiálni.

   Ha például sok embernek kell elolvasnia a blobokat egy adott tárolóban, létrehozhat egy tárolt hozzáférési szabályzatot, amely szerint az "olvasási hozzáférés biztosítása" és minden egyéb olyan beállítás, amely minden alkalommal azonos lesz. Ezután létrehozhat egy SAS URI-t a tárolt hozzáférési szabályzat beállításaival, és megadhatja a lejárat dátumát és időpontját. Ennek az az előnye, hogy az összes lekérdezési paramétert nem kell minden alkalommal megadnia.

#### <a name="revocation"></a>Visszavont
Tegyük fel, hogy az SAS biztonsága sérült, vagy módosítani szeretné a vállalati biztonsági vagy szabályozási megfelelőségi követelmények miatt. Hogyan vonhatja vissza az erőforrásokhoz való hozzáférést az adott SAS használatával? Ez attól függ, hogyan hozta létre az SAS URI-t.

Ha ad hoc URI-t használ, három lehetőség közül választhat. Az SAS-jogkivonatokat rövid lejáratú házirendekkel is kiállíthatja, és megvárhatja, amíg az SAS lejár. Átnevezheti vagy törölheti az erőforrást (feltéve, hogy a token egyetlen objektumra van korlátozva). Módosíthatja a Storage-fiók kulcsait. Ez az utolsó lehetőség jelentős hatással lehet, attól függően, hogy hány szolgáltatás használja ezt a Storage-fiókot, és valószínűleg nem valami, amit a tervezés nélkül szeretne elvégezni.

Ha egy tárolt hozzáférési szabályzatból származtatott SAS-t használ, akkor a hozzáférését a tárolt hozzáférési szabályzat visszavonásával távolíthatja el – egyszerűen megváltoztathatja azt, hogy már lejárt, vagy távolítsa el az egészet. Ez azonnal érvénybe lép, és érvényteleníti a tárolt hozzáférési szabályzattal létrehozott összes SAS-t. A tárolt hozzáférési szabályzat frissítése vagy eltávolítása hatással lehet arra, hogy az SAS-n keresztül hozzáférjenek az adott tárolóhoz, a fájlmegosztáshoz, a táblához vagy a várólistához, de ha az ügyfelek úgy vannak írva, hogy új SAS-t kérnek, amikor a régi érvénytelenné válik, ez a művelet rendben fog működni.

Mivel a tárolt hozzáférési házirendből származtatott SAS-t használva azonnal visszavonhatja az SAS-t, az ajánlott eljárás, hogy mindig a tárolt hozzáférési szabályzatokat használja, ha lehetséges.

#### <a name="resources"></a>Segédanyagok és eszközök
A megosztott hozzáférési aláírások és a tárolt hozzáférési szabályzatok használatával kapcsolatos részletesebb információkért tekintse meg a következő cikkeket:

* Ezek a hivatkozási cikkek.

  * [Szolgáltatás SAS](https://msdn.microsoft.com/library/dn140256.aspx)

    Ez a cikk példákat mutat be a Blobokkal, üzenetsor-üzenetekkel, táblázat-tartományokkal és fájlokkal rendelkező szolgáltatási szintű SAS használatára.
  * [Szolgáltatás SAS-építése](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Fiók SAS létrehozása](https://msdn.microsoft.com/library/mt584140.aspx)

* Hitelesítés

  * [Hitelesítés az Azure Storage-szolgáltatásokhoz](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Közös hozzáférési aláírások Első lépések oktatóanyag

  * [SAS Első lépések oktatóanyag](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Titkosítás átvitel közben
### <a name="transport-level-encryption--using-https"></a>Átviteli szintű titkosítás – HTTPS használatával
Egy másik lépést kell megtennie, hogy az Azure Storage-beli adatai biztonságban legyenek, hogy titkosítsa az ügyfelet és az Azure Storage-t. Az első javaslat az, hogy mindig a [https](https://en.wikipedia.org/wiki/HTTPS) protokollt használja, amely biztosítja a biztonságos kommunikációt a nyilvános interneten keresztül.

Biztonságos kommunikációs csatorna esetén mindig HTTPS protokollt kell használnia a REST API-k meghívásakor vagy a tárolóban lévő objektumok eléréséhez. Emellett a **közös hozzáférésű aláírások**, amelyek az Azure Storage-objektumokhoz való hozzáférés delegálására használhatók, egy beállítással megadható, hogy csak a HTTPS protokollt lehessen használni a megosztott hozzáférési aláírások használatakor, így biztosítható, hogy bárki kiküldje a hivatkozásokat sas-jogkivonatokkal a megfelelő protokollt fogja használni.

A HTTPS használatát akkor kényszerítheti, ha a REST API-k meghívásával fér hozzá a Storage-fiókokban lévő objektumokhoz a Storage-fiókhoz [szükséges biztonságos átvitel](../storage-require-secure-transfer.md) engedélyezésével. Ha ez a beállítás engedélyezve van, a rendszer elutasítja a HTTP protokollal létesített kapcsolatokat.

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Titkosítás használata az Azure file shares szolgáltatással történő átvitel során
[Azure Files](../files/storage-files-introduction.md) támogatja a titkosítást az SMB 3,0 és a HTTPS protokollon keresztül, ha a fájl REST API. Ha az Azure-régión kívül helyezi üzembe az Azure-fájlmegosztást, például a helyszínen vagy egy másik Azure-régióban, az SMB 3,0 titkosítással mindig szükséges. Az SMB 2,1 nem támogatja a titkosítást, ezért az alapértelmezett kapcsolatok csak az Azure ugyanazon régiójában engedélyezettek, de a titkosítással rendelkező SMB 3,0 kényszeríthető a Storage-fiók [biztonságos átvitelének megkövetelésével](../storage-require-secure-transfer.md) .

A titkosítással rendelkező SMB 3,0 az [összes támogatott Windows és Windows Server operációs rendszeren](../files/storage-how-to-use-files-windows.md) elérhető, kivéve a Windows 7 és a windows Server 2008 R2 rendszert, amely csak az SMB 2,1-ot támogatja. A [MacOS](../files/storage-how-to-use-files-mac.md) és a [Linux](../files/storage-how-to-use-files-linux.md) rendszerű Linux kernel 4,11-es vagy újabb verziójának használatával az SMB 3,0 is támogatott. Az SMB 3,0 titkosítási támogatása a Linux-kernel régebbi verzióira is backported számos Linux-disztribúcióval, az SMB- [ügyfél követelményeinek megismerése](../files/storage-how-to-use-files-linux.md#smb-client-reqs)című részből tájékozódhat.

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Ügyféloldali titkosítás használata a tárolóba küldött adatvédelmek biztonságossá tételéhez
Egy másik lehetőség, amely segít gondoskodni arról, hogy az ügyfél-alkalmazás és a tárterület közötti adatátvitel biztonságos legyen az ügyféloldali titkosítás. Az adattitkosítás az Azure Storage-ba való átvitel előtt történik. Az Azure Storage-ból származó adatok beolvasása során a rendszer visszafejti az adatok visszafejtését, miután az megérkezett az ügyfél oldalán. Annak ellenére, hogy az adatok titkosítva lesznek a huzalon, javasoljuk, hogy használja a HTTPS protokollt is, mivel a beépített adatintegritás-ellenőrzésekkel az adatok integritását befolyásoló hálózati hibák enyhítését segíti.

Az ügyféloldali titkosítás az adatok inaktív állapotban történő titkosítására is használható, mivel az adatok titkosított formában vannak tárolva. Erről részletesebben a inaktív [titkosításról](#encryption-at-rest)szóló szakaszban fogunk beszélni.

## <a name="encryption-at-rest"></a>Titkosítás nyugalmi állapotban
Három Azure-szolgáltatás áll rendelkezésre, amelyek titkosítást biztosítanak a nyugalmi állapotban. A Azure Disk Encryption az operációs rendszer és az adatlemezek titkosítására szolgál a IaaS-Virtual Machinesokban. Az ügyféloldali titkosítás és az SSE egyaránt az Azure Storage-ban tárolt adat titkosítására szolgál. 

Míg az ügyféloldali titkosítás használatával titkosíthatja az átvitel során továbbított adatok (amelyek tárolása titkosított formában is megtörténik a tárolóban), érdemes lehet HTTPS-t használni az átvitel során, és valamilyen módon az adatok automatikusan titkosítva lesznek a tároláskor. Ezt kétféleképpen teheti meg: Azure Disk Encryption és SSE. Az egyik a virtuális gépek által használt operációs rendszereken és adatlemezeken található, a másik pedig az Azure Blob Storageba írt adatai titkosítására szolgál.

### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

Az SSE engedélyezve van az összes Storage-fiókhoz, és nem tiltható le. Az SSE automatikusan titkosítja az adatait az Azure Storage-ba való íráskor. Az Azure Storage-ból származó adatok beolvasása után az Azure Storage visszafejti azt a visszatérés előtt. Az SSE lehetővé teszi az adatai védelmét anélkül, hogy kódot kellene módosítania, vagy programkódot kell hozzáadnia bármely alkalmazáshoz.

Használhatja a Microsoft által felügyelt kulcsokat vagy a saját egyéni kulcsait is. A Microsoft a belső Microsoft-házirend által meghatározott módon felügyelt kulcsokat hoz létre, és kezeli a biztonságos tárhelyet, valamint a normál rotációt. További információ az egyéni kulcsok használatáról: [Storage Service encryption az ügyfél által felügyelt kulcsok használata a Azure Key Vaultban](storage-service-encryption-customer-managed-keys.md).

Az SSE automatikusan titkosítja minden teljesítményszint (Standard és Prémium), minden üzembehelyezési modell (Azure Resource Manager és klasszikus) és minden Azure Storage-szolgáltatás (Blob, Queue, Table és File) adatait. 

### <a name="client-side-encryption"></a>Ügyféloldali titkosítás
A továbbítás során megemlítjük az ügyféloldali titkosítást, amikor megbeszéljük az adatátvitel titkosítását. Ez a funkció lehetővé teszi, hogy programozott módon titkosítsa az adatokat egy ügyfélalkalmazás számára, mielőtt elküldené azt az Azure Storage-ba, és programozott módon visszafejtse az adatokat az Azure Storage-ból történő beolvasása után.

Ez biztosítja a titkosítást az átvitel során, de a titkosítást is biztosítja a nyugalmi állapotban. Noha az adatok átvitel közben titkosítva vannak, továbbra is javasoljuk a HTTPS használatát, hogy kihasználhassa a beépített adatintegritási ellenőrzéseket, amelyek segítenek az adatok integritását befolyásoló hálózati hibák enyhítésében.

Példa arra, hogy hol lehet ezt használni, ha olyan webalkalmazással rendelkezik, amely blobokat tárol és lekéri a blobokat, és azt szeretné, hogy az alkalmazás és az adatmennyiség legyen a lehető legbiztonságosabb. Ebben az esetben az ügyféloldali titkosítást kellene használnia. Az ügyfél és az Azure Blob szolgáltatás közötti adatforgalom a titkosított erőforrást tartalmazza, és senki nem tudja értelmezni az átvitt adatokat, és a saját blobokra alakítja azt.

Az ügyféloldali titkosítás a Java és a .NET Storage ügyféloldali kódtáraba van beépítve, amely a Azure Key Vault API-kat használja, ami megkönnyíti a megvalósítását. Az adatok titkosításának és visszafejtésének folyamata a burkológörbe technikáját használja, és a titkosítás által az egyes tárolási objektumokban használt metaadatokat tárolja. A Blobok esetében például a blob metaadataiban tárolja azt, míg a várólisták esetében hozzáadja azt az egyes üzenetsor-üzenetekhez.

A titkosításhoz saját titkosítási kulcsokat is létrehozhat és kezelhet. Az Azure Storage ügyféloldali kódtár által generált kulcsokat is használhat, de a Azure Key Vault is létrehozhatja a kulcsokat. A titkosítási kulcsokat a helyszíni kulcstárolóban tárolhatja, vagy tárolhatja egy Azure Key Vaultban is. A Azure Key Vault lehetővé teszi, hogy a Azure Active Directory használatával hozzáférést biztosítson a titkos kulcsokhoz Azure Key Vault adott felhasználók számára. Ez azt jelenti, hogy nem csak bárki olvashatja a Azure Key Vault, és lekérheti az ügyféloldali titkosításhoz használt kulcsokat.

#### <a name="resources"></a>Segédanyagok és eszközök
* [Blobok titkosítása és visszafejtése Microsoft Azure Storage használatával Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)

  Ez a cikk bemutatja, hogyan használhatja az ügyféloldali titkosítást Azure Key Vaultokkal, beleértve a KEK létrehozását és tárolását a tárolóban a PowerShell használatával.
* [Ügyféloldali titkosítás és Azure Key Vault a Microsoft Azure Storage](../storage-client-side-encryption.md)

  Ez a cikk magyarázatot nyújt az ügyféloldali titkosításról, és példákat tartalmaz arra, hogy a Storage ügyféloldali kódtár használatával titkosítsa és visszafejtse az erőforrásokat a négy tárolási szolgáltatásból. Emellett Azure Key Vault is beszél.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>A virtuális gépek által használt lemezek titkosítása Azure Disk Encryption használatával
Azure Disk Encryption lehetővé teszi a IaaS virtuális gépek által használt operációsrendszer-lemezek és adatlemezek titkosítását. A Windows rendszerben a meghajtók titkosítása az iparági szabványnak megfelelő BitLocker titkosítási technológiával történik. A Linux rendszerben a lemezek titkosítása a DM-Crypt technológiával történik. Ez integrálva van Azure Key Vault, hogy lehetővé tegye a lemezes titkosítási kulcsok szabályozását és kezelését.

A megoldás a következő forgatókönyveket támogatja a IaaS virtuális gépekhez, amikor azok engedélyezve vannak Microsoft Azureban:

* Integráció a Azure Key Vault
* Standard szintű virtuális gépek: [A, D, DS, G, GS és így tovább sorozatú IaaS virtuális gépek](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Titkosítás engedélyezése Windows és Linux rendszerű IaaS virtuális gépeken
* Az operációs rendszer és az adatmeghajtók titkosításának letiltása Windows IaaS virtuális gépeken
* A Linux IaaS-alapú virtuális gépek adatmeghajtóinak titkosításának letiltása
* A titkosítás engedélyezése a Windows ügyfél operációs rendszerét futtató IaaS virtuális gépeken
* A kötetek titkosításának engedélyezése csatlakoztatási útvonalakkal
* A titkosítás engedélyezése a lemezes csíkozással (RAID) konfigurált Linux rendszerű virtuális gépeken a mdadm használatával
* Linux rendszerű virtuális gépek titkosításának engedélyezése az LVM használatával adatlemezek esetén
* A titkosítás engedélyezése a tárolóhelyek használatával konfigurált Windows rendszerű virtuális gépeken
* Az összes Azure-beli nyilvános régió támogatott

A megoldás nem támogatja az alábbi forgatókönyveket, szolgáltatásokat és technológiákat a kiadásban:

* Alapszintű IaaS virtuális gépek
* A Linux IaaS virtuális gépekhez tartozó operációsrendszer-meghajtók titkosításának letiltása
* A klasszikus virtuálisgép-létrehozási módszer használatával létrehozott IaaS virtuális gépek
* Integráció a helyszíni kulcskezelő szolgáltatással
* Azure Files (megosztott fájlrendszer), a hálózati fájlrendszer (NFS), a dinamikus kötetek és a szoftveres RAID-rendszerekkel konfigurált Windows-alapú virtuális gépek


> [!NOTE]
> A Linux operációsrendszer-lemez titkosítása jelenleg a következő Linux-disztribúciók esetében támogatott: RHEL 7,2, CentOS 7.2 n és Ubuntu 16,04.
>
>

Ez a funkció biztosítja, hogy a virtuálisgép-lemezeken lévő összes adatok titkosítva legyenek az Azure Storage-ban.

#### <a name="resources"></a>Segédanyagok és eszközök
* [Azure Disk Encryption Windows és Linux rendszerű IaaS virtuális gépekhez](../../security/fundamentals/encryption-overview.md)

### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Azure Disk Encryption, SSE és ügyféloldali titkosítás összehasonlítása

#### <a name="iaas-vms-and-their-vhd-files"></a>IaaS virtuális gépek és VHD-fájljaik

A IaaS-alapú virtuális gépek által használt adatlemezek esetében Azure Disk Encryption ajánlott. Ha nem felügyelt lemezekkel rendelkező virtuális gépet hoz létre az Azure Marketplace-ről származó rendszerkép használatával, az Azure a rendszerkép egy [sekély másolatát](https://en.wikipedia.org/wiki/Object_copying) hajtja végre a Storage-fiókban az Azure Storage-ban, és nem titkosítja, még akkor is, ha az SSE engedélyezve van. Miután létrehozta a virtuális gépet, és megkezdi a rendszerkép frissítését, az SSE megkezdi az adatok titkosítását. Ezért érdemes az Azure Marketplace-en található rendszerképekből létrehozott, nem felügyelt lemezekkel rendelkező virtuális gépeken Azure Disk Encryptiont használni, ha azt szeretné, hogy teljes mértékben titkosítva legyenek. Ha Managed Disks-val hoz létre virtuális gépet, az SSE a platform által felügyelt kulcsok használatával alapértelmezés szerint titkosítja az összes adathalmazt. 

Ha egy előre titkosított virtuális gépet helyez üzembe az Azure-ba a helyszíni környezetből, feltöltheti a titkosítási kulcsokat a Azure Key Vaultba, és folytathatja az adott virtuális gép titkosításának használatát, amelyet a helyszínen használt. A Azure Disk Encryption engedélyezve van ennek a forgatókönyvnek a kezelésére.

Ha nem titkosított virtuális merevlemezt használ a helyszínen, feltöltheti azt a katalógusba egyéni rendszerképként, és kiépítheti belőle a virtuális gépet. Ha ezt a Resource Manager-sablonok használatával hajtja végre, megkérheti, hogy kapcsolja be Azure Disk Encryption, amikor elindítja a virtuális gépet.

Ha adatlemezt ad hozzá, és csatlakoztatja a virtuális géphez, bekapcsolhatja a Azure Disk Encryptiont az adatlemezen. Először titkosítja az adatlemezt, majd a klasszikus üzemi modell rétege lusta írást hajt végre a tárterületen, így a tároló tartalma titkosítva lesz.

#### <a name="client-side-encryption"></a>Ügyféloldali titkosítás
Az ügyféloldali titkosítás a legbiztonságosabb módszer az adattitkosításhoz, mivel az adatátvitel előtt titkosítja az adatforgalmat.  Azonban szükség van arra, hogy a Storage használatával programkódot vegyen fel az alkalmazásaiba, amelyet esetleg nem kíván végrehajtani. Ezekben az esetekben a HTTPS használatával biztonságossá teheti az adatátvitelt. Az Azure Storage-ba való adatátvitel után az SSE titkosítva van.

Az ügyféloldali titkosítással titkosíthatja a táblák entitásait, üzenetsor-üzeneteit és blobokat. 

Az ügyféloldali titkosítást teljes mértékben az alkalmazás kezeli. Ez a legbiztonságosabb megközelítés, de megköveteli, hogy programozott módosításokat hajtson végre az alkalmazásban, és hogy a legfontosabb felügyeleti folyamatokat helyezze üzembe. Ezt akkor érdemes használni, ha az átvitel során az extra biztonságot szeretné használni, és szeretné, hogy a tárolt adatai titkosítva legyenek.

Az ügyféloldali titkosítás nagyobb terhelést eredményez az ügyfélen, és ezt a méretezhetőségi tervekben kell figyelembe vennie, különösen akkor, ha nagy mennyiségű adat titkosítását és továbbítását kívánja átvinni.

#### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

Az SSE-t az Azure Storage kezeli. Az SSE nem biztosítja az átvitt adatforgalom biztonságát, de az Azure Storage-ba írt módon titkosítja az adatforgalmat. Az SSE nem befolyásolja az Azure Storage teljesítményét.

A Storage-fiók bármilyen típusú adattitkosítása az SSE használatával titkosítható (a Blobok, a Blobok hozzáfűzése, a Blobok, a tábla adatai, a várólista adatai és a fájlok).

Ha rendelkezik az új virtuális gépek létrehozásához használt VHD-fájlok archívumával vagy könyvtárával, hozzon létre egy új Storage-fiókot, majd töltse fel a VHD-fájlokat a fiókba. Ezeket a VHD-fájlokat az Azure Storage fogja titkosítani.

Ha Azure Disk Encryption engedélyezve van a virtuális gép lemezein, akkor az újonnan írt adatforgalom az SSE és a Azure Disk Encryption között is titkosítva lesz.

## <a name="storage-analytics"></a>Storage Analytics
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>Storage Analytics használata az engedélyezési típus figyeléséhez
Minden egyes Storage-fiók esetében engedélyezheti a Azure Storage Analytics a metrikák naplózásának és tárolásának elvégzéséhez. Ez egy nagyszerű eszköz, amelyet akkor érdemes használni, ha szeretné megtekinteni egy Storage-fiók teljesítmény-metrikáit, vagy egy Storage-fiók hibaelhárítását, mivel teljesítménnyel kapcsolatos problémákat tapasztal.

A Storage Analytics naplóiban látható egy másik adat, amelyet a felhasználó a tárolóhoz való hozzáféréskor használ. Például a Blob Storage használatával megtekintheti, hogy megosztott hozzáférési aláírást vagy a Storage-fiók kulcsait használták-e, vagy ha az elérni kívánt blob nyilvános volt.

Ez akkor lehet hasznos, ha szorosan védelmet biztosít a tárolóhoz való hozzáféréshez. Blob Storage például beállíthatja az összes tárolót a magánjellegűre, és egy SAS-szolgáltatást alkalmazhat az alkalmazásokban. Ezután rendszeresen megtekintheti a naplókat, és megtekintheti, hogy a Blobok elérhetők-e a Storage-fiók kulcsainak használatával, ami a biztonság megsértését jelezheti, vagy ha a Blobok nyilvánosak, de nem.

#### <a name="what-do-the-logs-look-like"></a>Hogyan néznek ki a naplók?
Miután engedélyezte a Storage-fiók metrikáit és a Azure Portali naplózást, az elemzési adatok gyorsan összegyűlnek. Az egyes szolgáltatások naplózása és metrikái külön vannak elkülönítve. a naplózás csak akkor íródik, ha tevékenység van a Storage-fiókban, a metrikák pedig percenként, óránként, vagy naponta lesznek naplózva attól függően, hogy hogyan konfigurálja azt.

A naplófájlokat a rendszer a Storage-fiókban $logs nevű tárolóban tárolja a blokk blobokban. Ez a tároló automatikusan létrejön, ha Storage Analytics engedélyezve van. A tároló létrehozása után nem törölheti, de törölheti is a tartalmát.

A $logs tárolóban minden szolgáltatáshoz tartozik egy mappa, majd az év/hónap/nap/óra almappája van. Az óra alatt a naplók számozása. A könyvtár szerkezete így fog kinézni:

![Naplófájlok megtekintése](./media/storage-security-guide/image1.png)

Minden Azure Storage-kérelem naplózva van. Íme egy pillanatkép egy naplófájlról, amely az első néhány mezőt mutatja.

![Naplófájl pillanatképe](./media/storage-security-guide/image2.png)

Láthatja, hogy a naplók segítségével nyomon követheti a Storage-fiókok bármilyen típusú hívását.

#### <a name="what-are-all-of-those-fields-for"></a>Mik ezek a mezők?
Az alábbi forrásokban szerepel egy cikk, amely a naplók számos mezőjének listáját és azok használatát tartalmazza. Az alábbi lista a mezők sorrendjét tartalmazza:

![Naplófájl mezőinek pillanatképe](./media/storage-security-guide/image3.png)

Érdeklik a GetBlob vonatkozó bejegyzések és azok engedélyezése, ezért meg kell keresni a "Get-blob" típusú bejegyzéseket, és ellenőriznie kell a kérelem állapotát (negyedik </sup> oszlop) és az engedélyezési típust (nyolcadik </sup> oszlop).

Például a fenti lista első néhány sorában a kérelem állapota "sikeres", az engedélyezési típus pedig "hitelesített". Ez azt jelenti, hogy a kérés a Storage-fiók kulcsa alapján lett engedélyezve.

#### <a name="how-is-access-to-my-blobs-being-authorized"></a>Hogyan lehet engedélyezni a Blobok hozzáférését?
Három esetben érdekli a szolgáltatás.

1. A blob nyilvános, és megosztott hozzáférési aláírás nélküli URL-cím használatával érhető el. Ebben az esetben a kérelem állapota "AnonymousSuccess", az engedélyezési típus pedig "Anonymous".

   1.0; 2015-11-17T02:01:29.0488963 Z; GetBlob **AnonymousSuccess**; 200; 124; 37; **Névtelen**;; mystorage...
2. A blob privát, és közös hozzáférési aláírással lett használva. Ebben az esetben a kérelem állapota "SASSuccess", az engedélyezési típus pedig "sas".

   1.0; 2015-11-16T18:30:05.6556115 Z; GetBlob **SASSuccess**; 200; 416; 64; **sas**;; mystorage...
3. A blob magán, és a rendszer a tárolási kulcsot használta a hozzáféréshez. Ebben az esetben a kérelem állapota "**sikeres**", az engedélyezési típus pedig "**hitelesített**".

   1.0; 2015-11-16T18:32:24.3174537 Z; GetBlob **Sikeres**; 206; 59; 22; **hitelesített**; mystorage...

A Microsoft Message Analyzer segítségével megtekintheti és elemezheti ezeket a naplókat. Keresési és szűrési képességeket is tartalmaz. Előfordulhat például, hogy meg kívánja keresni a GetBlob példányait, hogy megtudja, a használat mire vár, vagyis hogy valaki nem fér hozzá megfelelően a Storage-fiókhoz.

#### <a name="resources"></a>Segédanyagok és eszközök
* [Storage Analytics](../storage-analytics.md)

  Ez a cikk áttekintést nyújt a Storage Analytics szolgáltatásról és azok engedélyezéséről.
* [Storage Analytics naplózási formátum](https://msdn.microsoft.com/library/azure/hh343259.aspx)

  Ez a cikk a Storage Analytics napló formátumát mutatja be, és részletezi az ott elérhető mezőket, beleértve a hitelesítési típust is, amely a kérelemhez használt hitelesítés típusát jelzi.
* [Storage-fiók figyelése a Azure Portal](../storage-monitor-storage-account.md)

  Ez a cikk bemutatja, hogyan konfigurálhatja a metrikák és a naplózási fiókok figyelését a Storage-fiókhoz.
* [Végpontok közötti hibaelhárítás az Azure Storage-metrikák és-naplózás, a AzCopy és az Message Analyzer használatával](../storage-e2e-troubleshooting.md)

  Ez a cikk a Storage Analyticsával kapcsolatos hibaelhárítást és a Microsoft Message Analyzer használatát mutatja be.
* [Microsoft Message Analyzer – üzemeltetési útmutató](https://technet.microsoft.com/library/jj649776.aspx)

  Ez a cikk a Microsoft Message Analyzer hivatkozása, amely az oktatóanyagra, a gyors üzembe helyezésre és a funkciók összegzésére mutató hivatkozásokat tartalmaz.

## <a name="cross-origin-resource-sharing-cors"></a>Eltérő eredetű erőforrások megosztása (CORS)
### <a name="cross-domain-access-of-resources"></a>Erőforrás-tartományok közötti hozzáférés
Ha az egyik tartományban futó webböngésző HTTP-kérést végez egy másik tartományból származó erőforráshoz, akkor ezt a rendszer az eltérő eredetű HTTP-kérelemnek nevezzük. A contoso.com-ből kiszolgált HTML-lapok például a fabrikam.blob.core.windows.net-on tárolt JPEG-fájlok kérését teszik lehetővé. Biztonsági okokból a böngészők korlátozzák a parancsfájlokból (például a JavaScriptből) kezdeményezett, több eredetű HTTP-kérelmeket. Ez azt jelenti, hogy ha a contoso.com található weblapon egyes JavaScript-kódok a fabrikam.blob.core.windows.net JPEG-fájlját kérik, a böngésző nem engedélyezi a kérést.

Mit kell tennie az Azure Storage-ban? Ha olyan statikus eszközöket tárol, mint például a JSON-vagy XML-adatfájlok Blob Storage egy Fabrikam nevű Storage-fiók használatával, az eszközök tartománya fabrikam.blob.core.windows.net lesz, és a contoso.com webalkalmazás nem fog tudni hozzáférni a következő használatával: JavaScript, mert a tartományok eltérőek. Ez akkor is igaz, ha az egyik Azure Storage-szolgáltatást (például Table Storage) próbálja meg hívni, amely a JavaScript-ügyfél által feldolgozandó JSON-adatmennyiséget küld vissza.

#### <a name="possible-solutions"></a>Lehetséges megoldások
Ennek egyik módja, ha egy egyéni tartományt (például "storage.contoso.com") rendel a fabrikam.blob.core.windows.net. A probléma az, hogy ezt az egyéni tartományt csak egy Storage-fiókhoz rendelheti hozzá. Mi a teendő, ha a rendszer több Storage-fiókban tárolja az eszközöket?

A megoldás egy másik módja, hogy a webalkalmazás proxyként működjön a tárolási hívásokhoz. Ez azt jelenti, hogy ha egy fájlt tölt fel Blob Storageba, a webalkalmazás helyileg írni fogja, majd átmásolja Blob Storagere, vagy beolvassa az összes memóriát a memóriába, majd a Blob Storagebe írja azt. Másik lehetőségként írhat egy dedikált webalkalmazást (például webes API-t), amely helyileg feltölti a fájlokat, és beírja őket a Blob Storageba. Mindkét esetben a méretezhetőségi igények meghatározásakor ezt a funkciót kell figyelembe vennie.

#### <a name="how-can-cors-help"></a>Hogyan segíthet a CORS?
Az Azure Storage lehetővé teszi, hogy engedélyezze a CORS – több eredetű erőforrás-megosztást. Minden egyes Storage-fiókhoz megadhat olyan tartományokat, amelyek hozzáférhetnek a Storage-fiók erőforrásaihoz. Például a fent leírt módon engedélyezheti a CORS a fabrikam.blob.core.windows.net Storage-fiókon, és beállíthatja, hogy engedélyezze a hozzáférést a contoso.com. Ezután a webalkalmazás-contoso.com közvetlenül hozzáférhetnek az erőforrásokhoz a fabrikam.blob.core.windows.net-ben.

Az egyik fontos megjegyezni, hogy a CORS engedélyezi a hozzáférést, de nem biztosít hitelesítést, ami szükséges a tárolási erőforrások összes nem nyilvános eléréséhez. Ez azt jelenti, hogy csak akkor férhet hozzá a blobokhoz, ha nyilvánosak, vagy egy közös hozzáférési aláírással rendelkezik, amely megadja a megfelelő engedélyeket. A táblák, a várólisták és a fájlok nem rendelkeznek nyilvános hozzáféréssel, és SAS-t igényelnek.

Alapértelmezés szerint a CORS le van tiltva az összes szolgáltatáson. A CORS a REST API vagy a Storage ügyféloldali kódtár használatával engedélyezheti, hogy az egyik módszert hívja meg a szolgáltatási házirendek beállításához. Ha ezt teszi, egy XML-ben található CORS-szabályt is tartalmaz. Íme egy példa egy olyan CORS-szabályra, amely egy Storage-fiókhoz tartozó szolgáltatás tulajdonságainak beállítása művelet használatával lett beállítva. Ezt a műveletet a Storage ügyféloldali kódtár vagy az Azure Storage REST API-jai használatával végezheti el.

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

Az egyes sorok a következők:

* **AllowedOrigins** Ez azt jelzi, hogy mely nem egyező tartományokat igényelhet és fogadhat adatok a Storage szolgáltatástól. Ez azt mondja, hogy a contoso.com és a fabrikam.com is kérheti a Blob Storage adatait egy adott Storage-fiókhoz. Ezt a helyettesítő karaktert (\*) is beállíthatja úgy, hogy az összes tartomány hozzáférjen a kérelmekhez.
* **AllowedMethods** Azon metódusok (HTTP-kérési műveletek) listája, amelyeket a rendszer a kérelem végrehajtásakor használhat. Ebben a példában csak a PUT és a GET engedélyezett. Ezt beállíthatja helyettesítő karakter (\*) értékre, hogy az összes módszert használni lehessen.
* **AllowedHeaders** Ez az a kérelem fejléce, amelyet a forrás tartomány meghatároz a kérelem végrehajtásakor. Ebben a példában az x-MS-Meta-adat, x-MS-meta-Target és x-MS-meta-ABC kezdetű metaadat-fejlécek engedélyezettek. A helyettesítő karakter (\*) azt jelzi, hogy a megadott előtaggal kezdődő fejlécek engedélyezettek.
* **ExposedHeaders** Ez azt jelzi, hogy a böngészőnek mely válasz-fejléceket kell megtennie a kérés kiállítójának. Ebben a példában az "x-MS-meta-" kezdetű fejlécek lesznek kitéve.
* **MaxAgeInSeconds** Ez az a maximális időtartam, ameddig egy böngésző gyorsítótárazza az elővizsgálati beállítások kérését. (Az elővizsgálati kérelemmel kapcsolatos további információkért olvassa el az alábbi első cikket.)

#### <a name="resources"></a>Segédanyagok és eszközök
A CORS és annak engedélyezésével kapcsolatos további információkért tekintse meg ezeket az erőforrásokat.

* [A Azure.com-alapú Azure Storage-szolgáltatások CORS-támogatásának több eredetű erőforrás-megosztási támogatása](../storage-cors-support.md)

  Ez a cikk áttekintést nyújt a CORS és a különböző tárolási szolgáltatások szabályainak beállításáról.
* [Az Azure Storage szolgáltatásainak több eredetű erőforrás-megosztási (CORS) támogatása az MSDN-ben](https://msdn.microsoft.com/library/azure/dn535601.aspx)

  Ez az Azure Storage-szolgáltatások CORS támogatását ismertető dokumentáció. Ez az egyes tárolási szolgáltatásokra vonatkozó cikkekre mutató hivatkozásokat tartalmaz, és egy példát mutat be, és megmagyarázza a CORS fájl egyes elemeit.
* [Microsoft Azure Storage: a CORS bemutatása](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

  Ez egy hivatkozás a kezdeti blogra, amely bejelenti a CORS, és bemutatja, hogyan használhatja azt.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Az Azure Storage biztonságával kapcsolatos gyakori kérdések
1. **Hogyan tudom ellenőrizni, hogy az Azure Storage-ba vagy onnan átvitt Blobok integritása nem tudom használni a HTTPS protokollt?**

   Ha bármilyen okból HTTPS helyett HTTP-t kell használnia, és a blokk-Blobokkal dolgozik, az MD5-ellenőrzés segítségével ellenőrizheti az átvitt Blobok integritását. Ez segít a hálózati/szállítási rétegbeli hibák elleni védelemben, de nem feltétlenül a közbenső támadásokkal szemben.

   Ha a HTTPS protokollt használja, amely átviteli szintű biztonságot biztosít, akkor az MD5-ellenőrzés redundáns és szükségtelen.

   További információkért tekintse meg az [Azure Blob MD5 áttekintését](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).
2. **Mi a helyzet a FIPS-megfelelőséggel az Egyesült Államok kormányának?**

   A Egyesült Államok Federal Information Processing standard (FIPS) olyan titkosítási algoritmusokat határoz meg, amelyeket a rendszer a bizalmas adatok védelme érdekében az Egyesült Államok szövetségi kormányzati számítógépei számára engedélyezett. A FIPS mód Windows Server vagy Desktop rendszeren való engedélyezése azt jelzi, hogy az operációs rendszer csak a FIPS-érvényesített titkosítási algoritmusokat használja. Ha egy alkalmazás nem megfelelő algoritmusokat használ, a rendszer megtöri az alkalmazásokat. A With.NET-keretrendszer 4.5.2-es vagy újabb verzióiban az alkalmazás automatikusan átváltja a titkosítási algoritmusokat az FIPS-kompatibilis algoritmusok használatára, ha a számítógép FIPS módban van.

   A Microsoft minden ügyfélnek elhagyja az FIPS-mód engedélyezését. Hiszünk abban, hogy a nem kormányzati szabályozás hatálya alá tartozó ügyfelek számára nincs olyan kényszerítő ok, amely alapértelmezés szerint engedélyezi az FIPS üzemmódot.

### <a name="resources"></a>Segédanyagok és eszközök
* [Miért nem ajánljuk többé a "FIPS mód" használatát](https://blogs.technet.microsoft.com/secguide/2014/04/07/why-were-not-recommending-fips-mode-anymore/)

  Ez a blogbejegyzés áttekintést nyújt a FIPS-ről, és ismerteti, hogy alapértelmezés szerint miért nem engedélyezik az FIPS üzemmódot.
* [FIPS 140 érvényesítése](https://technet.microsoft.com/library/cc750357.aspx)

  Ez a cikk tájékoztatást nyújt arról, hogy a Microsoft-termékek és-titkosítási modulok hogyan felelnek meg az Egyesült Államok szövetségi kormányának FIPS szabványának.
* ["Rendszerkriptográfia: FIPS szabványnak megfelelő algoritmusok használata titkosításhoz, kivonatoláshoz és aláíráshoz" biztonsági beállítások hatása a Windows XP és a Windows újabb verzióiban](https://support.microsoft.com/kb/811833)

  Ez a cikk a FIPS üzemmód használatát ismerteti a régebbi Windows-számítógépeken.
