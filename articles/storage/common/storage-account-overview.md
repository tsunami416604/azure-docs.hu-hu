---
title: Tárfiókok áttekintése
titleSuffix: Azure Storage
description: Megismerheti az Azure Storage-fiókok létrehozásának és használatának lehetőségeit.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5034aaaee335bbd87e7ea42b448e4e8fbf6aacca
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274534"
---
# <a name="storage-account-overview"></a>Tárfiókok áttekintése

Egy Azure Storage-fiók tartalmazza az összes Azure Storage-adatobjektumot: blobokat, fájlokat, várólistákat, táblákat és lemezeket. A Storage-fiók egy egyedi névteret biztosít az Azure Storage-adatok számára, amely a világon bárhonnan elérhető HTTP-vagy HTTPS-kapcsolaton keresztül. Az Azure Storage-fiókban tárolt adatai tartósak, a biztonságos és a nagy mértékben méretezhetők.

Az Azure Storage-fiókok létrehozásával kapcsolatos további információkért lásd: [Storage-fiók létrehozása](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>A tárfiókok típusai

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>Általános célú v2-fiókok

Az általános célú v2 Storage-fiókok támogatják az Azure Storage legújabb funkcióit, és az általános célú v1-és blob Storage-fiókok összes funkcióját beépítik. Az általános célú v2-fiókok az Azure Storage-ban a legalacsonyabb/GB-os kapacitást biztosítják, valamint az iparágban versenyképes tranzakciós árakat. Az általános célú v2 Storage-fiókok támogatják ezeket az Azure Storage-szolgáltatásokat:

- Blobok (az összes típus: letiltás, Hozzáfűzés, lap)
- Data Lake Gen2
- Fájlok
- Lemezek
- Üzenetsorok
- Táblák

> [!NOTE]
> A Microsoft az általános célú v2 Storage-fiók használatát javasolja a legtöbb forgatókönyvhöz. Az általános célú v1-vagy blob Storage-fiókok egyszerűen, leállás nélkül frissíthetők egy általános célú v2-fiókra, anélkül, hogy adatmásolásra lenne szükség.
>
> Az általános célú v2-fiókra való frissítéssel kapcsolatos további információkért lásd: [frissítés általános célú v2 Storage-fiókra](storage-account-upgrade.md).

Az általános célú v2 Storage-fiókok több hozzáférési szintet is kínálnak az adatok használati minták alapján történő tárolásához. További információ: [hozzáférési szintek a Blobok adatainak blokkolásához](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Általános célú v1-fiókok

Az általános célú v1 Storage-fiókok hozzáférést biztosítanak az összes Azure Storage-szolgáltatáshoz, de nem rendelkezhetnek a legújabb szolgáltatásokkal, illetve a legalacsonyabb/gigabájt díjszabással. Az általános célú v1 Storage-fiókok támogatják ezeket az Azure Storage-szolgáltatásokat:

- Blobok (az összes típus)
- Fájlok
- Lemezek
- Üzenetsorok
- Táblák

A legtöbb esetben az általános célú v2-fiókokat kell használnia. Az alábbi forgatókönyvekhez használhatja az általános célú v1-fiókokat:

- Alkalmazásaihoz a klasszikus Azure-telepítési modell szükséges. Az általános célú v2-fiókok és a blob Storage-fiókok csak a Azure Resource Manager telepítési modellt támogatják.

- Az alkalmazások tranzakció-igényesek, vagy jelentős földrajzi replikációs sávszélességet használnak, de nem igényelnek nagy kapacitást. Ebben az esetben az általános célú v1 lehet a leggazdaságosabb választás.

- A [Storage REST API Services](https://msdn.microsoft.com/library/azure/dd894041.aspx) olyan verzióját használja, amely a 2014-02-14-nál korábbi, vagy a 4. x-nél alacsonyabb verziószámú ügyféloldali kódtár. Az alkalmazás nem frissíthető.

### <a name="blockblobstorage-accounts"></a>BlockBlobStorage-fiókok

A BlockBlobStorage-fiók egy speciális Storage-fiók a prémium szintű teljesítmény szintjén a strukturálatlan objektumok adatai blokk Blobok vagy hozzáfűzési Blobok tárolására. Az általános célú v2-és BlobStorage-fiókokkal összehasonlítva a BlockBlobStorage-fiókok alacsony, konzisztens késést és magasabb tranzakciós sebességet biztosítanak.

A BlockBlobStorage-fiókok jelenleg nem támogatják a leválasztást a gyakori, ritka elérésű vagy archív hozzáférési szintekre. Ez a típusú tárolási fiók nem támogatja a Blobok, táblák vagy várólisták oldalát.

### <a name="filestorage-accounts"></a>FileStorage-fiókok

A FileStorage-fiók a prémium fájlmegosztás tárolására és létrehozására szolgáló speciális Storage-fiók. Ez a Storage-fióktípus támogatja a fájlokat, de nem blokkolja a blobokat, hozzáfűzi a blobokat, a blobokat, a táblákat és a várólistákat

A FileStorage-fiókok egyedi teljesítménnyel kapcsolatos jellemzőket kínálnak, például a IOPS-kitörést. A jellemzőkkel kapcsolatos további információkért tekintse meg a fájlok tervezési útmutató [fájlmegosztás teljesítmény szintjei](../files/storage-files-planning.md#file-share-performance-tiers) című szakaszát.

## <a name="naming-storage-accounts"></a>Tárolási fiókok elnevezése

Ne feledje ezeket a szabályokat a tárfiók elnevezésekor:

- A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
- A tárfiók nevének egyedinek kell lennie az Azure rendszerben. Két tárfióknak nem lehet azonos neve.

## <a name="performance-tiers"></a>Teljesítményszintek

A létrehozott Storage-fiók típusától függően választhat a standard és a prémium szintű teljesítményszint közül is.

### <a name="general-purpose-storage-accounts"></a>Általános célú tárfiókok

Az általános célú Storage-fiókok a következő teljesítményszint-szintek bármelyikéhez konfigurálhatók:

- Szabványos teljesítményszint Blobok, fájlok, táblák, várólisták és Azure-beli virtuális gépek lemezei tárolásához. A standard szintű Storage-fiókok méretezhetőségi céljaival kapcsolatos további információkért lásd [a standard szintű Storage-fiókok méretezhetőségi céljait](scalability-targets-standard-account.md)ismertető témakört.
- Prémium szintű teljesítményszint a nem felügyelt virtuális gépek lemezeinek tárolásához. A Microsoft azt javasolja, hogy felügyelt lemezeket használjon az Azure Virtual Machines szolgáltatással nem felügyelt lemezek helyett. A prémium szintű teljesítményszint skálázhatósági céljaival kapcsolatos további információkért lásd: a [prémium lap blob Storage-fiókok méretezhetőségi célpontjai](../blobs/scalability-targets-premium-page-blobs.md).

### <a name="blockblobstorage-storage-accounts"></a>BlockBlobStorage

A BlockBlobStorage Storage-fiókok prémium szintű teljesítményt biztosítanak a blokkos Blobok tárolásához és a Blobok hozzáfűzéséhez. További információ: [a prémium szintű blokk blob Storage-fiókok méretezhetőségi célpontjai](../blobs/scalability-targets-premium-block-blobs.md).

### <a name="filestorage-storage-accounts"></a>FileStorage

Az FileStorage Storage-fiókok prémium szintű teljesítményt biztosítanak az Azure-fájlmegosztás számára. További információ: [Azure Files skálázhatósági és teljesítménybeli célok](../files/storage-files-scale-targets.md).

## <a name="access-tiers-for-block-blob-data"></a>Hozzáférési szintek a blob-adatblokkok számára

Az Azure Storage különböző lehetőségeket biztosít a Blobok adatok használati minták alapján történő eléréséhez. Az Azure Storage-ban az egyes hozzáférési szintek egy adott adattípusra vannak optimalizálva. Az igényeinek megfelelő hozzáférési réteg kiválasztásával a legtöbb költséghatékony módon tárolhatja a blokk blob-adatait.

Az elérhető hozzáférési szintek a következők:

- A **gyors** elérési szint. Ez a szintet a Storage-fiókban lévő objektumok gyakori elérésére optimalizáltuk. A gyors elérésű rétegben az adatok elérése a leghatékonyabb, a tárolási költségek pedig magasabbak. Alapértelmezés szerint a rendszer új Storage-fiókokat hoz létre a gyors elérésű rétegben.
- A **lassú** elérési szint. Ez a csomag olyan nagy mennyiségű, ritkán használt és tárolt adatok tárolására van optimalizálva, amelyek legalább 30 napig vannak tárolva. Az adatok lassú elérésű szinten való tárolása költséghatékonyabb, de az adatokhoz való hozzáférés drágább lehet, mint a gyors elérésű rétegben lévő adatok elérése.
- Az **archiválási** szint. Ez a szintet csak az egyes blokk-Blobok esetében és a Blobok hozzáfűzésével érheti el. Az archiválási szint olyan adatokra van optimalizálva, amelyek több órányi lekérési késést tudnak tolerálni, és amelyek legalább 180 napig maradnak az archiválási szinten. Az archiválási szint az adatok tárolására leginkább költséghatékony megoldás. Azonban az adatokhoz való hozzáférés drágább, mint az adatokhoz való hozzáférés a gyors vagy a lassú elérésű szinteken.

Ha módosul az adatok használati mintája, akkor bármikor válthat a hozzáférési szintek között. A hozzáférési szintekkel kapcsolatos további információkért lásd [: Azure Blob Storage: gyakori, ritka elérésű és archív hozzáférési szintek](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> Egy meglévő Storage-fiók vagy blob hozzáférési rétegének módosítása további díjakat eredményezhet. További információt a [Storage-fiók számlázási szakasza](#storage-account-billing)tartalmaz.

## <a name="replication"></a>Replikáció

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

További információ a tárolási replikálásról: az [Azure Storage replikációja](storage-redundancy.md).

## <a name="encryption"></a>Titkosítás

A Storage-fiókban lévő összes adat titkosítva van a szolgáltatás oldalán. A titkosítással kapcsolatos további információkért lásd: [Azure-Storage Service encryption a REST-adatokhoz](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Tárfiókvégpontok

A Storage-fiók egyedi névteret biztosít az Azure-ban az adataihoz. Az Azure Storage-ban tárolt összes objektumhoz tartozik egy olyan címe, amely tartalmazza az egyedi fióknevet. A fiók neve és az Azure Storage szolgáltatás végpontja a Storage-fiókhoz tartozó végpontokat képezi le.

Ha például az általános célú Storage-fiók neve *mystorageaccount*, akkor az adott fiókhoz tartozó alapértelmezett végpontok a következők:

- BLOB Storage: `https://*mystorageaccount*.blob.core.windows.net`
- Table Storage: `https://*mystorageaccount*.table.core.windows.net`
- Üzenetsor-tárolás: `https://*mystorageaccount*.queue.core.windows.net`
- Azure Files: `https://*mystorageaccount*.file.core.windows.net`

> [!NOTE]
> A blob-és blob Storage-fiókok csak a Blob service végpontot teszik elérhetővé.

Hozzon létre egy objektum elérésére szolgáló URL-címet egy Storage-fiókban úgy, hogy hozzáfűzi az objektum helyét a Storage-fiókban a végponthoz. Például egy blobcím formátuma lehet a következő: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

A Storage-fiók a Blobok egyéni tartományának használatára is konfigurálható. További információ: [Egyéni tartománynév beállítása az Azure Storage-fiókhoz](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Fiókadatok hozzáférésének szabályozása

Alapértelmezés szerint a fiókban tárolt adatok csak Ön, azaz a fiók tulajdonosa számára érhetőek el. Ön szabályozhatja, hogy ki férhet hozzá az adataihoz, és milyen engedélyekkel rendelkezik.

Minden, a Storage-fiókra vonatkozó kérelmet engedélyezni kell. A szolgáltatás szintjén a kérelemnek tartalmaznia kell egy érvényes *engedélyezési* fejlécet. Pontosabban, ez a fejléc tartalmazza az összes olyan információt, amely ahhoz szükséges, hogy a szolgáltatás a végrehajtás előtt érvényesítse a kérést.

A következő módszerek bármelyikével biztosíthat hozzáférést a Storage-fiókban található adathoz:

- **Azure Active Directory:** Használjon Azure Active Directory (Azure AD) hitelesítő adatokat egy felhasználó, csoport vagy más identitás hitelesítéséhez a blob-és üzenetsor-adatokhoz való hozzáféréshez. Ha az identitás hitelesítése sikeres, az Azure AD visszaadja az Azure Blob Storage-ba vagy a várólista-tárolóba való kérelem engedélyezéséhez használandó tokent. További információ: az [Azure Storage hozzáférésének hitelesítése Azure Active Directory használatával](storage-auth-aad.md).
- **Megosztott kulcs engedélyezése:** Használja a Storage-fiók hozzáférési kulcsát olyan kapcsolati karakterlánc létrehozásához, amelyet az alkalmazás a futásidőben használ az Azure Storage eléréséhez. A kapcsolódási karakterláncban szereplő értékek az Azure Storage-nak átadott *engedélyezési* fejléc összeállítására szolgálnak. További információ: az [Azure Storage-beli kapcsolatok karakterláncának konfigurálása](storage-configure-connection-string.md).
- **Közös hozzáférési aláírás:** Ha nem az Azure AD-hitelesítést használja, közös hozzáférésű aláírással delegálhatja a Storage-fiók erőforrásaihoz való hozzáférést. A közös hozzáférésű aláírások olyan tokenek, amelyek az URL-címen az Azure Storage-ba irányuló kérések engedélyezéséhez szükséges összes információt beágyazzák. Megadhatja a tárolási erőforrást, a megadott engedélyeket, valamint azt az időközt, ameddig az engedélyek érvényesek a közös hozzáférési aláírás részeként. További információ: [Shared Access Signatures (SAS) használata](storage-sas-overview.md).

> [!NOTE]
> Az Azure AD-beli hitelesítő adatokkal rendelkező felhasználók vagy alkalmazások hitelesítése kiváló biztonságot és könnyű használatot biztosít más engedélyezési módokon. Habár továbbra is használhatja a megosztott kulcsos engedélyezést az alkalmazásokkal, az Azure AD megkerülésével megkerüli a fiók hozzáférési kulcsának a kóddal való tárolásának szükségességét. Továbbra is használhatja a közös hozzáférésű aláírásokat (SAS) a Storage-fiók erőforrásaihoz való részletes hozzáférés biztosításához, de az Azure AD hasonló képességeket kínál, anélkül, hogy az SAS-tokeneket kellene kezelnie, vagy nem kell aggódnia a sérült SAS visszavonásával kapcsolatban.
>
> A Microsoft az Azure AD-hitelesítés használatát javasolja az Azure Storage blob-és üzenetsor-alkalmazásaihoz, ha lehetséges.

## <a name="copying-data-into-a-storage-account"></a>Adatok másolása egy Storage-fiókba

A Microsoft segédprogramokat és könyvtárakat biztosít az adatok helyszíni tárolóeszközökből vagy külső felhőalapú tárolási szolgáltatóktól történő importálásához. Az Ön által használt megoldás az átvitt adatok mennyiségétől függ.

Ha egy általános célú v1-vagy blob Storage-fiókból egy általános célú v2-fiókra frissít, az adatok automatikusan áttelepülnek. A Microsoft ezt az utat javasolja a fiók frissítéséhez. Ha azonban úgy dönt, hogy egy általános célú v1-fiók adatait egy blob Storage-fiókba helyezi át, akkor manuálisan kell áttelepítenie az adatait az alább ismertetett eszközök és kódtárak használatával.

### <a name="azcopy"></a>AzCopy

Az AzCopy egy Windows parancssori segédprogram, amely az adatok az Azure Storage szolgáltatásba vagy onnan máshová való nagyteljesítményű másolására lett kifejlesztve. A AzCopy használatával egy meglévő általános célú Storage-fiókból másolhatja át az adatait egy blob Storage-fiókba, vagy feltölthet adatokkal a helyszíni tárolóeszközökből. További információt [az AzCopy parancssori segédprogrammal történő adatátvitelt](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ismertető szakaszban talál.

### <a name="data-movement-library"></a>Adatátviteli könyvtár

Az Azure Storage .NET-keretrendszerhez készült adatátviteli kódtára az AzCopyt működtető alapvető adatátviteli keretrendszeren alapul. A könyvtár az AzCopyhoz hasonló nagy teljesítményű, megbízható és könnyű adatátviteli műveletekhez készült. Az adatáthelyezési függvénytár segítségével natív módon kihasználhatja a AzCopy funkcióit. További információ: [Azure Storage adatátviteli függvénytár a .net-hez](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>REST API vagy ügyfélkódtár

Létrehozhat egy egyéni alkalmazást, amellyel áttelepítheti az adatait egy általános célú v1 Storage-fiókból egy blob Storage-fiókba. Használja az egyik Azure-ügyfél kódtárat vagy az Azure Storage Services REST API. Az Azure Storage gazdag ügyfélkódtárakat biztosít több nyelvhez és platformhoz is, beleértve a következőket: .NET, Java, C++, Node.JS, PHP, Ruby és Python. Az ügyfélkódtárak olyan fejlett képességeket biztosítanak, mint az újrapróbálkozási logika, a naplózás vagy a párhuzamos feltöltések. Fejleszthet közvetlenül a REST API-n is, amely minden, HTTP-/HTTPS-kérelmek létrehozására alkalmas nyelven meghívható.

Az Azure Storage REST APIával kapcsolatos további információkért lásd: az [Azure Storage Services REST API referenciája](https://docs.microsoft.com/rest/api/storageservices/).

> [!IMPORTANT]
> Az ügyféloldali titkosítással titkosított blobok a titkosítással kapcsolatos metaadatokat a blobbal együtt tárolják. Ha ügyféloldali titkosítással titkosított blobot másol, ügyeljen arra, hogy a másolási művelet megőrizze a blob metaadatait, különös tekintettel a titkosítással kapcsolatos metaadatokra. Ha a blobokat a titkosítási metaadatok nélkül másolja, a blob tartalma nem kérhető le újból. További információt a titkosítással kapcsolatos metaadatokról [az Azure Storage ügyféloldali titkosítását](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ismertető szakaszban talál.

## <a name="storage-account-billing"></a>Tárfiókok számlázása

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>Következő lépések

- [Tárfiók létrehozása](storage-account-create.md)
- [Blokkblob-tárfiók létrehozása](../blobs/storage-blob-create-account-block-blob.md)
