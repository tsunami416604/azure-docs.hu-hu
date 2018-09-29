---
title: Az Azure storage-fiók áttekintése |} A Microsoft Docs
description: Ismerje meg létrehozásáról és használatáról az Azure Storage-fiók beállításai.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/13/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 0b2bf8cdb1af85e5ddbd3b18dd6dfa47bcb835b4
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432885"
---
# <a name="azure-storage-account-overview"></a>Az Azure storage-fiókok áttekintése

Egy Azure storage-fiókot tartalmazza az összes az Azure Storage-adatobjektumok: blobok, fájlok, üzenetsorok, táblák és lemezek. Az Azure storage-fiókja adatai tartós és magas rendelkezésre állású, biztonságos, nagy mértékben méretezhető és elérhető bárhol a világon HTTP vagy HTTPS. 

Ismerje meg, hogyan hozhat létre egy Azure storage-fiókot, lásd: [hozzon létre egy tárfiókot](storage-quickstart-create-account.md).

## <a name="types-of-storage-accounts"></a>A tárfiókok típusai

Az Azure Storage három típusú tárfiókok biztosít. Egyes támogatja a különböző funkciókat, és rendelkezik a saját díjszabási modell. Milyen típusú fiókot, amely a legjobb alkalmazásait a tárfiók létrehozása előtt, fontolja meg a különbségeket. A tárfiókok típusai a következők:

* **Általános célú v2** fiókok (a legtöbb forgatókönyvek esetén ajánlott)
* **Általános célú v1** fiókok
* **Blob Storage**-fiók

A következő táblázat a tárfiókok típusait és azok képességeinek:

| Tárfiók típusa | Támogatott szolgáltatások                       | Támogatott teljesítményszintek | Támogatott elérési szint               | Replikációs beállítások                                                | Üzemi modell<sup>1</sup>  | Titkosítási<sup>2</sup> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------------|--------------------------------------------------------------------|-------------------|------------|
| Általános célú V2   | BLOB, fájl, várólista, tábla és lemez       | Standard, prémium szintű           | Gyakori és ritka elérésű, archív<sup>3</sup> | LRS, ZRS<sup>4</sup>, GRS, RA-GRS | Resource Manager | Titkosított  |
| Általános célú V1   | BLOB, fájl, várólista, tábla és lemez       | Standard, prémium szintű           | –                                  | LRS, GRS, RA-GRS                                                   | Resource Manager, klasszikus  | Titkosított  |
| Blob Storage         | BLOB (blokkblobok és hozzáfűző blobok csak) | Standard                    | Gyakori és ritka elérésű, archív<sup>3</sup>                            | LRS, GRS, RA-GRS                                                   | Resource Manager  | Titkosított  |

<sup>1</sup>ajánlott az Azure Resource Manager üzemi modell használatával. Storage-fiókok a klasszikus üzemi modell használatával továbbra is hozhatja létre az egyes helyeken, és a létező klasszikus fiók továbbra is támogatottak. További információkért lásd: [Azure Resource Manager és klasszikus üzembe helyezési: üzembe helyezési modellek és az erőforrások állapotának ismertetése](../../azure-resource-manager/resource-manager-deployment-model.md).

<sup>2</sup>összes storage-fiók használatával történő Storage Service Encryption (SSE) inaktív adatok titkosítását. További információkért lásd: [Azure Storage Service Encryption az inaktív adatok](storage-service-encryption.md).

<sup>3</sup>az archív szinten érhető el, csak egy egyedi BLOB szintjén nem a storage-fiók szintjén. Csak blokkblobok és hozzáfűző blobok archiválhatók. További információkért lásd: [Azure Blob storage: gyakori, ritka és archív tárolási szintek](../blobs/storage-blob-storage-tiers.md).

<sup>4</sup>zónaredundáns tárolás (ZRS) csak a standard szintű, általános célú v2-tárfiókok esetén érhető el. A ZRS kapcsolatos további információkért lásd: [zónaredundáns tárolás (ZRS): az Azure Storage magas rendelkezésre állású alkalmazások](storage-redundancy-zrs.md). Más replikációs beállításokkal kapcsolatos további információkért lásd: [Azure Storage replikáció](storage-redundancy.md).

### <a name="general-purpose-v2-accounts"></a>Általános célú v2-fiókok

Általános célú v2-tárfiókok a legújabb Azure Storage-szolgáltatások támogatásához, és építse be az összes funkcióját, általános célú v1 és Blob storage-fiókok. Általános célú v2 fiókok kapacitás árak nyújthat a legalacsonyabb gigabájtonkénti az Azure Storage, valamint iparági versenyképes tranzakciós. Általános célú v2-tárfiókok ezeket az Azure tárolási szolgáltatások támogatják:

- Blobok (minden esetében)
- Fájlok
- Lemezek
- Üzenetsorok
- Táblák

A Microsoft azt javasolja, általános célú v2-tárfiókok használata a legtöbb forgatókönyvhöz. Könnyedén frissíthet egy általános célú v1- vagy Blob storage-fiók egy általános célú v2 fiók nincs állásidő és alkalmazás újraírások, és nincs szükség az adatok másolásához. Általános célú v2 fiókra frissítésével kapcsolatos további információkért lásd: [frissítsen egy általános célú v2-tárfiók](storage-account-upgrade.md). 

Általános célú v2-tárfiókok ajánlat több elérési szint a használati minták alapján adatainak tárolásához. További információkért lásd: [elérési szint a block blob adatok](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Általános célú v1-fiókok

Általános célú v1 fiókokat adja meg az összes Azure Storage-szolgáltatásokhoz való hozzáférés, de nem lehet a legújabb funkciók vagy a legalacsonyabb gigabájtonkénti díjszabás. Általános célú v1-tárfiókok ezeket az Azure tárolási szolgáltatások támogatják:

- Blobok (minden esetében)
- Fájlok
- Lemezek
- Üzenetsorok
- Táblák

Míg az általános célú v2-fiókok a legtöbb esetben ajánlott, általános célú v1 fiókok legalkalmasabbak arra, ezek a forgatókönyvek: 

* Az alkalmazásainak szükségük van a klasszikus Azure üzemi modellből. Általános célú v2 és Blob storage-fiókok támogatják az Azure Resource Manager üzemi modell.

* Az alkalmazások tranzakció-intenzív vagy jelentős georeplikációs sávszélességet használja, de nem igényelnek nagy kapacitású. Ebben az esetben az általános célú v1 lehet a leggazdaságosabb.

* A [Storage szolgáltatások REST API felülete](https://msdn.microsoft.com/library/azure/dd894041.aspx) 2014. 02. 14-nél korábbi verzióját vagy egy 4.x-nél korábbi verziójú ügyfélkódtárat használ, és nem tudja frissíteni az alkalmazást.

### <a name="blob-storage-accounts"></a>Blob Storage-fiókok

Blob storage-fiók egy specializált tárfiók blokkblobok formájában strukturálatlan adatok tárolására. BLOB storage-fiókok azonos tartósságot, rendelkezésre állási, méretezhetőségi és teljesítménnyel kapcsolatos szolgáltatások, az általános célú v2-tárfiókok tartalmazó adja meg. BLOB storage-fiókok tárolását blokkblobokat támogatja, és a hozzáfűző blobokat, de a lapblobokat nem.

BLOB storage-fiókok ajánlat több elérési szint a használati minták alapján adatainak tárolásához. További információkért lásd: [elérési szint a block blob adatok](#access-tiers-for-block-blob-data).

## <a name="naming-storage-accounts"></a>Tárfiókok elnevezési

Ne feledje ezeket a szabályokat a tárfiók elnevezésekor:

- A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
- A tárfiók nevének egyedinek kell lennie az Azure rendszerben. Két tárfióknak nem lehet azonos neve.

## <a name="performance-tiers"></a>Teljesítményszintek

Általános célú tárfiókok esetében az alábbi teljesítményszintek valamelyike konfigurálható:

* Standard teljesítményszint blobok, fájlok, táblák, üzenetsorok és Azure virtuális gépek lemezeinek tárolására.
* A premium teljesítményszint csak Azure-beli virtuálisgép-lemezek tárolására. A Premium Storage részletesebb áttekintéséért lásd: [Premium Storage: High-performance Storage for Azure Virtual Machine Workloads](../../virtual-machines/windows/premium-storage.md) (Premium Storage: Nagy teljesítményű tárterület az Azure virtuális gépek számítási feladataihoz).

## <a name="access-tiers-for-block-blob-data"></a>A blokkblobok adataival elérési szint

Az Azure Storage eléréséhez a blokkblobok adataival használati mintái alapján különböző lehetőségeket biztosít. Az Azure Storage-ban minden egyes hozzáférési szint egy adott minta adathasználatának van optimalizálva. Az igényeinek megfelelő hozzáférési rétegének kiválasztásával a blokkblobok adataival a leginkább költséghatékony módon tárolhatja.

A rendelkezésre álló hozzáférési szintek a következők:

* A **gyakori elérésű** hozzáférési szint, amely a gyakori hozzáférés a tárfiókban tárolt objektumok számára van optimalizálva. A gyakori elérésű szint adatok elérése a leginkább költséghatékony, amíg némileg magasabb tárolási költségek. Új tárfiókok alapértelmezés szerint a gyakori elérésű szint jönnek létre.
* A **ritkán használt adatok** hozzáférési szint, amely nagy mennyiségű, ritkán elért és legalább 30 nappal a tárolt adatok tárolására van optimalizálva. Adatok tárolása a lassú elérési szint költséghatékonyabban, de valamelyest drágábbak, mint a gyakori elérésű szint adatok elérése, hogy az adatok elérése lehet.
* A **archív** szint, amely csak egyedi blokkblobokhoz érhető el. Az archív szinten az adatokat, amelyeket lekérés több órás késése legalább 180 napig maradnak az archív szinten van optimalizálva. Az archív szinten adatok tárolására szolgáló a leginkább költséghatékony lehetőséget, de az adatok elérése drágább, mint a gyors vagy lassú elérésű szinteken adatok elérése. 

> [!NOTE]
> A [prémium szintű hozzáférési szint](../blobs/storage-blob-storage-tiers.md#premium-access-tier) egy helyileg redundáns tárolás (LRS) fiókot az Észak-Európa, USA 2. keleti régiója, USA középső régiója és USA nyugati régiójában régiókban korlátozott előzetes verzióban érhető el. Ismerje meg, hogyan kell regisztrálni az előzetes verzióra, lásd: [Azure prémium szintű Blob Storage bemutatása](http://aka.ms/premiumblob).

Ha változik az adatok használati módja, bármikor hozzáférési szintek között válthat. 

> [!IMPORTANT]
> Egy meglévő tárfiókot vagy blob esetében a hozzáférési szint módosítása további díjakat vonhat.

A hozzáférési rétegek kapcsolatos további információkért lásd: [Azure Blob storage: prémium szintű (előzetes verzió), gyakori, ritka és archív tárolási szintek](../blobs/storage-blob-storage-tiers.md).

## <a name="replication"></a>Replikáció

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Storage-replikációval kapcsolatos további információkért lásd: [Azure Storage replikáció](storage-redundancy.md).

## <a name="encryption"></a>Titkosítás

A tárfiókban lévő összes adat Szolgáltatásoldali van titkosítva. Titkosításával kapcsolatos további információkért lásd: [Azure Storage Service Encryption az inaktív adatok](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Tárfiókvégpontok

Storage-fiók az adatok az Azure-ban egy egyedi névteret biztosít. Az Azure Storage-ban minden egyes objektum, amely tartalmazza az egyedi fióknevet címmel rendelkezik. A fiók nevét és az Azure Storage-szolgáltatásvégpont kombinációja képezi a tárfiók végpontjait.

Például, ha az általános célú tárfiók neve *mystorageaccount*, akkor a fiókhoz tartozó alapértelmezett végpontok a következők:

* A BLOB storage: http://*mystorageaccount*. blob.core.windows.net
* Táblatároló: http://*mystorageaccount*. table.core.windows.net
* Queue storage: http://*mystorageaccount*. queue.core.windows.net
* Az Azure Files: http://*mystorageaccount*. file.core.windows.net

> [!NOTE]
> Blob storage-fiók csak a Blob szolgáltatásvégpontot mutatja meg.

A tárfiókban lévő objektumok eléréséhez szükséges URL-CÍMÉT a storage-fiókban az objektum tárfiókbeli helyének a végponthoz való hozzáfűzésével épül. Például egy blobcím formátuma lehet a következő: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

A tárfiók blobok használandó egyéni tartományt is konfigurálhatja. További információkért lásd: [az Azure Storage-fiókhoz tartozó egyéni tartománynév beállítása](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Fiók adatokhoz való hozzáférés vezérlése

Alapértelmezés szerint a fiókban tárolt adatok csak Ön, azaz a fiók tulajdonosa számára érhetőek el. Hogy ki lehet, hogy elérhetők az adatok és milyen engedélyekkel rendelkeznek.

Felé irányuló a storage-fiók összes kérelmet kell engedélyezni. A szolgáltatás szinten, a kérésnek tartalmaznia kell egy érvényes *engedélyezési* fejlécet, amely tartalmazza az összes információt, a szolgáltatást, hogy a kérelem érvényesítéséhez, végrehajtása előtt.

A következő módszerek bármelyikével a tárfiókban lévő biztosíthat hozzáférést az adatokhoz:

- **Az Azure Active Directory:** használata az Azure Active Directory (Azure AD) hitelesítő adatait, a felhasználó, csoport vagy más identitást a következő blob és üzenetsor adatokhoz (előzetes verzió) való hozzáférés hitelesítéséhez. Ha az identitás a hitelesítés sikeres, az Azure AD engedélyezése a kérést az Azure Blob storage és Queue storage használata egy tokent ad vissza. További információkért lásd: [hitelesíti a hozzáférést az Azure Storage, Azure Active Directory (előzetes verzió) használatával](storage-auth-aad.md).
- **Megosztott kulcs engedélyezési:** a fiók tárelérési kulcs használatával hozhat létre egy kapcsolati karakterláncot, amely az alkalmazás használ futtatáskor az Azure Storage eléréséhez. Az értékeket a kapcsolati karakterlánc segítségével hozza létre a *engedélyezési* fejlécet, amely az Azure Storage átadott. További információkért lásd: [konfigurálása az Azure Storage kapcsolati karakterláncok](storage-configure-connection-string.md).
- **Közös hozzáférésű jogosultságkód:** közös hozzáférésű jogosultságkódot használja a tárfiókban lévő erőforrásokhoz való hozzáférés delegálására, ha nem használja az Azure AD-hitelesítés. Közös hozzáférésű jogosultságkód egy jogkivonatot, amely magában foglalja az összes az Azure Storage, az URL-címet a kérelem engedélyezéséhez szükséges információkat. Megadhatja a tárolási erőforrások, jogosultságaitól és az időközt, amely fölött a engedélyek érvényesek a közös hozzáférésű jogosultságkód részeként. További információkért lásd: [a közös hozzáférésű jogosultságkód (SAS)](storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Felhasználók vagy az Azure AD hitelesítő adatait használó alkalmazások hitelesítése készít felső szintű biztonság és a könnyű használat más engedélyezési módot. Miközben továbbra is megosztott kulcsos engedélyezési használata az alkalmazások, Azure AD-vel megkerüli ügyféladataik tárolásának a kód a hozzáférési kulcsára. Közös hozzáférésű jogosultságkódok (SAS) használata a minden részletre kiterjedő hozzáférést biztosítani a tárfiókban lévő erőforrásokhoz folytathatja, de az Azure AD kezelése SAS-tokeneket vagy sérült biztonságú SAS visszavonása foglalkoznia kellene hasonló funkciókat kínál. 
>
> A Microsoft javasolja, hogy az Azure AD-hitelesítés használata az Azure Storage blob és üzenetsor alkalmazások amikor csak lehetséges.

## <a name="copying-data-into-a-storage-account"></a>Adatok másolása a storage-fiókra

A Microsoft segédprogramok és kódtárak kínál az adatok importálása a helyszíni tárolási eszközökről vagy a külső szolgáltatók. Melyik megoldás használja, a viszi adatmennyiség függ. 

Amikor frissít egy általános célú v2 fiók egy általános célú v1-től vagy a Blob storage-fiók, az adatok automatikusan áttelepítése. Microsoft azt javasolja, hogy ez a fiók frissítéséhez szükséges út. Azonban, ha úgy dönt, hogy helyezze át az adatokat egy általános célú v1 fiók Blob storage-fiók, akkor telepítenie kell az adatok manuális áttelepítésével, az eszköz és könyvtár használatával az alábbiakban. 

### <a name="azcopy"></a>AzCopy

Az AzCopy egy Windows parancssori segédprogram, amely az adatok az Azure Storage szolgáltatásba vagy onnan máshová való nagyteljesítményű másolására lett kifejlesztve. Használhatja az AzCopy használatával adatait átmásolhatja egy meglévő általános célú storage-fiók Blob storage-fiókra, vagy feltölthet adatokat a helyszíni. További információt [az AzCopy parancssori segédprogrammal történő adatátvitelt](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ismertető szakaszban talál.

### <a name="data-movement-library"></a>Adatátviteli könyvtár

Az Azure Storage .NET-keretrendszerhez készült adatátviteli kódtára az AzCopyt működtető alapvető adatátviteli keretrendszeren alapul. A könyvtár az AzCopyhoz hasonló nagy teljesítményű, megbízható és könnyű adatátviteli műveletekhez készült. Így az AzCopy által kínált szolgáltatások funkcióit teljes mértékben, natív módon kiaknázhatja az alkalmazásában anélkül, hogy ehhez az AzCopy külső példányait kellene futtatnia vagy felügyelnie. További információt [az Azure Storage a .Net-keretrendszerhez készült adatátviteli kódtárát](https://github.com/Azure/azure-storage-net-data-movement) ismertető szakaszban talál.

### <a name="rest-api-or-client-library"></a>REST API vagy ügyfélkódtár

Az adatok a Blob Storage-fiókra való áttelepítéséhez létrehozhat egy egyéni alkalmazást az Azure ügyfélkódtárai vagy az Azure Storage szolgáltatások REST API felülete segítségével. Az Azure Storage gazdag ügyfélkódtárakat biztosít több nyelvhez és platformhoz is, beleértve a következőket: .NET, Java, C++, Node.JS, PHP, Ruby és Python. Az ügyfélkódtárak olyan fejlett képességeket biztosítanak, mint az újrapróbálkozási logika, a naplózás vagy a párhuzamos feltöltések. Fejleszthet közvetlenül a REST API-n is, amely minden, HTTP-/HTTPS-kérelmek létrehozására alkalmas nyelven meghívható.

Az Azure Storage REST API kapcsolatos további információkért lásd: [Azure Storage szolgáltatások REST API-referencia](https://docs.microsoft.com/rest/api/storageservices/). 

> [!IMPORTANT]
> Az ügyféloldali titkosítással titkosított blobok a titkosítással kapcsolatos metaadatokat a blobbal együtt tárolják. Ha ügyféloldali titkosítással titkosított blobot másol, ügyeljen arra, hogy a másolási művelet megőrizze a blob metaadatait, különös tekintettel a titkosítással kapcsolatos metaadatokra. Ha a blobokat a titkosítási metaadatok nélkül másolja, a blob tartalma nem kérhető le újból. További információt a titkosítással kapcsolatos metaadatokról [az Azure Storage ügyféloldali titkosítását](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ismertető szakaszban talál.

### <a name="azure-importexport-service"></a>Azure Import/Export szolgáltatás

Ha egy nagy mennyiségű adatot a tárfiókhoz való importálásához, fontolja meg az Azure Import/Export szolgáltatás. Az Import/Export szolgáltatás egy Azure-adatközpontba szállítási lemezmeghajtók biztonságosan nagy mennyiségű adatot importálhat az Azure Blob storage és az Azure Files használják. 

Az Import/Export szolgáltatás adatainak átvitelét az Azure Blob storage-ból a merevlemez-meghajtók, és küldje el a helyszíni hely a használatával is lehet. Egy vagy több lemezmeghajtót származó adatok importálhatók, vagy az Azure Blob storage vagy az Azure Files. További információkért lásd: [Mi az Azure Import/Export szolgáltatást?](https://docs.microsoft.com/azure/storage/common/storage-import-export-service).

## <a name="storage-account-billing"></a>Tárfiókok számlázása

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan hozhat létre egy Azure storage-fiókot, lásd: [hozzon létre egy tárfiókot](storage-quickstart-create-account.md).
* Kezelheti, vagy töröl egy meglévő tárfiókot, lásd: [kezelése az Azure storage-fiókok](storage-account-manage.md).
