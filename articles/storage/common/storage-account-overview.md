---
title: Tárfiókok áttekintése
titleSuffix: Azure Storage
description: Az Azure Storage-fiók létrehozásának és használatának lehetőségei.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3694a1d04ec25f44cfcf9395bdd148e2fd3c0d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371553"
---
# <a name="storage-account-overview"></a>Tárfiókok áttekintése

Az Azure storage-fiók tartalmazza az összes Azure Storage-adatobjektumok: blobok, fájlok, várólisták, táblák és lemezek. A tárfiók egyedi névteret biztosít az Azure Storage-adatokhoz, amely a világ bármely pontjáról http- vagy HTTPS-en keresztül érhető el. Az Azure storage-fiókjában lévő adatok tartósak és magas rendelkezésre állásúak, biztonságosak és nagymértékben méretezhetők.

Az Azure-tárfiók létrehozásáról a [Tárfiók létrehozása című](storage-account-create.md)témakörben olvashat.

## <a name="types-of-storage-accounts"></a>A tárfiókok típusai

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>Általános célú v2-fiókok

Általános célú v2-es tárfiókok támogatják a legújabb Azure Storage-funkciókat, és belefoglalják az általános célú v1- és Blob storage-fiókok összes funkcióját. Az általános célú v2-es fiókok az Azure Storage esetében a legalacsonyabb gigabájtonkénti kapacitásárakat, valamint az iparágban versenyképes tranzakciós árakat biztosítják. Az általános célú v2-es tárfiókok támogatják ezeket az Azure Storage-szolgáltatásokat:

- Blobok (minden típus: Blokk, Hozzáfűzés, Oldal)
- Adatok-tó Gen2
- Fájlok
- Lemezek
- Üzenetsorok
- Táblák

> [!NOTE]
> A Microsoft azt javasolja, hogy a legtöbb esetben egy általános célú v2-es tárfiókot használjon. Az általános célú v1- vagy Blob-tárfiókot egyszerűen frissítheti egy általános célú v2-fiókra állásidő nélkül, és adatok másolása nélkül.
>
> Az általános célú v2-fiókra való frissítésről további információt a [Frissítés általános célú v2-es tárfiókra című témakörben talál.](storage-account-upgrade.md)

Általános célú v2 tárfiókok kínálnak több hozzáférési réteget az adatok tárolására a használati minták alapján. További információ: [Access tiers for block blob data.](#access-tiers-for-block-blob-data)

### <a name="general-purpose-v1-accounts"></a>Általános célú v1-fiókok

Az általános célú v1-es tárfiókok hozzáférést biztosítanak az összes Azure Storage-szolgáltatáshoz, de nem rendelkeznek a legújabb funkciókkal vagy a gigabájtonkénti legalacsonyabb díjszabással. Az általános célú v1-es tárfiókok támogatják ezeket az Azure Storage-szolgáltatásokat:

- Blobok (minden típus)
- Fájlok
- Lemezek
- Üzenetsorok
- Táblák

A legtöbb esetben általános célú v2-fiókokat kell használnia. Az általános célú v1-fiókokat a következő esetekben használhatja:

- Az alkalmazások megkövetelik az Azure klasszikus üzembe helyezési modell. Általános célú v2-fiókok és blob storage-fiókok csak az Azure Resource Manager telepítési modell t.

- Az alkalmazások tranzakcióigényesek, vagy jelentős georeplikációs sávszélességet használnak, de nem igényelnek nagy kapacitást. Ebben az esetben az általános célú v1 lehet a leggazdaságosabb választás.

- A Storage Services [REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) 2014-02-14-nél korábbi verzióját vagy 4.x-nél alacsonyabb verziójú ügyfélkódtárat használ. Nem frissítheti az alkalmazást.

### <a name="blockblobstorage-accounts"></a>BlockBlobStorage-fiókok

A BlockBlobStorage-fiók egy speciális tárfiók a prémium szintű teljesítményrétegstrukturálása strukturálatlan objektumadatok blokk blobok vagy hozzáfűző blobok tárolására. Az általános célú v2- és BlobStorage-fiókokhoz képest a BlockBlobStorage-fiókok alacsony, konzisztens konzisztens késést és magasabb tranzakciós díjakat biztosítanak.

A BlockBlobStorage-fiókok jelenleg nem támogatják a gyakori elérésű, ritka elérésű vagy archív hozzáférési szintek rétegezését. Az ilyen típusú tárfiók nem támogatja a lapblobokat, táblákat vagy várólistákat.

### <a name="filestorage-accounts"></a>FileStorage-fiókok

A FileStorage-fiók egy speciális tárfiók, amely prémium szintű fájlmegosztások tárolására és létrehozására szolgál. Ez a tárfiók-fajta támogatja a fájlokat, de nem blokkolja a blobokat, hozzáfűző blobokat, lapblobokat, táblázatokat vagy várólistákat.

FileStorage fiókok kínálnak egyedi teljesítmény dedikált jellemzők, mint például az IOPS bursting. Ezekről a jellemzőkről a Fájlok tervezési [útmutatójának Fájlmegosztási tárolási rétegei](../files/storage-files-planning.md#storage-tiers) című részében talál további információt.

## <a name="naming-storage-accounts"></a>Tárfiókok elnevezése

Ne feledje ezeket a szabályokat a tárfiók elnevezésekor:

- A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
- A tárfiók nevének egyedinek kell lennie az Azure rendszerben. Két tárfióknak nem lehet azonos neve.

## <a name="performance-tiers"></a>Teljesítményszintek

A létrehozott tárfiók típusától függően választhat a normál és a prémium szintű teljesítményszintek között.

### <a name="general-purpose-storage-accounts"></a>Általános célú tárfiókok

Az általános célú tárfiókok konfigurálhatók a következő teljesítményszintek egyikéhez:

- Blobok, fájlok, táblák, várólisták és Azure virtuálisgép-lemezek tárolására szolgáló szabványos teljesítményszint. A szabványos tárfiókok méretezhetőségi céljairól a [szabványos tárfiókok méretezhetőségi céljai című témakörben](scalability-targets-standard-account.md)talál további információt.
- Nem felügyelt virtuálisgép-lemezek tárolására szolgáló prémium szintű teljesítményszint. A Microsoft azt javasolja, hogy a felügyelt lemezeket azure-beli virtuális gépekkel használja a nem felügyelt lemezek helyett. A prémium szintű teljesítményszint méretezhetőségi céljairól további információt a [prémium szintű lapblobstorage-fiókok méretezhetőségi céljai című témakörben talál.](../blobs/scalability-targets-premium-page-blobs.md)

### <a name="blockblobstorage-storage-accounts"></a>BlockBlobStorage-tárfiókok

A BlockBlobStorage-tárfiókok prémium szintű teljesítményszintet biztosítanak a blokkblobok és a hozzáfűző blobok tárolásához. További információ: [Méretezhetőségi célok a prémium szintű blokkblob-tárfiókok.](../blobs/scalability-targets-premium-block-blobs.md)

### <a name="filestorage-storage-accounts"></a>FileStorage-tárfiókok

A FileStorage storage-fiókok prémium szintű teljesítményszintet biztosítanak az Azure-fájlmegosztásokszámára. További információ: [Azure Files méretezhetőség és teljesítménycélok.](../files/storage-files-scale-targets.md)

## <a name="access-tiers-for-block-blob-data"></a>Hozzáférési szintek a blokkblob-adatokhoz

Az Azure Storage különböző lehetőségeket biztosít a blokkblob-adatok használati minták alapján történő eléréséhez. Az Azure Storage minden hozzáférési szintje az adathasználat egy adott mintájára van optimalizálva. Az igényeinek megfelelő hozzáférési szint kiválasztásával a blokkblob-adatokat a leginkább költséghatékony módon tárolhatja.

A rendelkezésre álló hozzáférési szintek a következők:

- A **gyakori elérésű** szint. Ez a szint a tárfiókban lévő objektumok gyakori elérésére van optimalizálva. A gyakori elérésű rétegben lévő adatok elérése a legköltséghatékonyabb, míg a tárolási költségek magasabbak. Az új tárfiókok alapértelmezés szerint a gyakori elérésű rétegben jönnek létre.
- A **ritka elérésű** szint. Ez a szint nagy mennyiségű, ritkán használt és legalább 30 napig tárolt adatok tárolására van optimalizálva. Az adatok tárolása a ritka elérésű rétegben költséghatékonyabb, de az adatok elérése drágább lehet, mint a gyakori elérési szintben lévő adatok elérése.
- Az **Archív** szint. Ez a szint csak az egyes blokkblobok érhető el. Az archív szint olyan adatokra van optimalizálva, amelyek több órányi lekérési késést is elviselnek, és legalább 180 napig az archív rétegben maradnak. Az archiválási szint az adatok tárolására legköltséghatékonyabb megoldás. Azonban az adatok elérése drágább, mint a gyakori vagy ritka elérésű szintekben lévő adatok elérése.

Ha az adatok használati mintázata megváltozik, bármikor válthat a hozzáférési szintek között. A hozzáférési szintekről további információt az [Azure Blob storage: gyakori elérésű, ritka elérésű és archív hozzáférési szintek című témakörben talál.](../blobs/storage-blob-storage-tiers.md)

> [!IMPORTANT]
> Egy meglévő tárfiók vagy blob hozzáférési szintjének módosítása további díjakat vonhat maga után. További információt a [Tárfiók számlázása című szakaszban talál.](#storage-account-billing)

## <a name="redundancy"></a>Redundancia

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="encryption"></a>Titkosítás

A tárfiókban lévő összes adat titkosítva van a szolgáltatási oldalon. A titkosítással kapcsolatos további információkért lásd: [Az Azure Storage Service Encryption az inaktív adatokat.](storage-service-encryption.md)

## <a name="storage-account-endpoints"></a>Tárfiókvégpontok

A tárfiók egyedi névteret biztosít az Azure-ban az adatokszámára. Az Azure Storage-ban tárolt minden objektum rendelkezik egy címmel, amely tartalmazza az Ön egyedi fióknevét. A fióknév és az Azure Storage-szolgáltatás végpontja kombinációja a tárfiók végpontjait képezi.

Ha például az általános célú tárfiók neve *mystorageaccount,* akkor a fiók alapértelmezett végpontjai a következők:

- Blob-tároló:`https://*mystorageaccount*.blob.core.windows.net`
- Táblázat tárolása:`https://*mystorageaccount*.table.core.windows.net`
- Várólista-tárolás:`https://*mystorageaccount*.queue.core.windows.net`
- Azure-fájlok:`https://*mystorageaccount*.file.core.windows.net`

> [!NOTE]
> Blokk blob és blob storage-fiókok csak a Blob szolgáltatás végpontját teszi elérhetővé.

A tárfiókban lévő objektum eléréséhez az URL-címet úgy hozlétre, hogy hozzáfűzi az objektum helyét a tárfiókban a végponthoz. Például egy blobcím formátuma lehet a következő: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

A tárfiókot is beállíthatja, hogy egyéni tartományt használjon a blobokhoz. További információ: [Egyéni tartománynév konfigurálása az Azure Storage-fiókhoz.](../blobs/storage-custom-domain-name.md)  

## <a name="control-access-to-account-data"></a>A fiókadatokhoz való hozzáférés szabályozása

Alapértelmezés szerint a fiókban tárolt adatok csak Ön, azaz a fiók tulajdonosa számára érhetőek el. Ön szabályozhatja, hogy ki férhet hozzá az adataihoz, és milyen engedélyekkel rendelkeznek.

A tárfiókkal szemben imitációt minden kérést engedélyezni kell. A szolgáltatás szintjén a kérelemnek érvényes engedélyezési fejlécet kell *tartalmaznia.* Pontosabban ez a fejléc tartalmazza az összes szükséges információt a szolgáltatás érvényesítéséhez a kérelem végrehajtása előtt.

A tárfiókban lévő adatokhoz az alábbi módszerek bármelyikével biztosíthatja a hozzáférést:

- **Azure Active Directory:** Az Azure Active Directory (Azure AD) hitelesítő adataival hitelesítheti a felhasználót, csoportot vagy más identitást a blob- és várólista-adatokhoz való hozzáféréshez. Ha egy identitás hitelesítése sikeres, majd az Azure AD egy jogkivonatot ad vissza az Azure Blob storage vagy queue storage kérésének engedélyezéséhez. További információ: [Access to Azure Storage using Azure Active Directory](storage-auth-aad.md).
- **Megosztott kulcs engedélyezése:** A tárfiók hozzáférési kulcshasználatával hozhat létre egy kapcsolati karakterláncot, amelyet az alkalmazás futásidőben használ az Azure Storage eléréséhez. A kapcsolati karakterlánc értékei az Azure Storage-nak átadott *engedélyezési* fejléc létrehozásához használatosak. További információt az [Azure Storage kapcsolati karakterláncai konfigurálása című témakörben talál.](storage-configure-connection-string.md)
- **Megosztott hozzáférésű aláírás:** Ha nem azure AD-hitelesítést használ, használjon megosztott hozzáférés-aláírást a tárfiókban lévő erőforrásokhoz való hozzáférés delegálásához. A megosztott hozzáférésű jogosultságkód egy jogkivonat, amely magában foglalja az összes szükséges információt az Azure Storage-nak az URL-címen való engedélyezéséhez. Megadhatja a tárolási erőforrást, a megadott engedélyeket és azt az időtartamot, amely alatt az engedélyek a megosztott hozzáférésű aláírás részeként érvényesek. További információ: [A megosztott hozzáférésű aláírások (SAS) használata.](storage-sas-overview.md)

> [!NOTE]
> A felhasználók vagy alkalmazások hitelesítése az Azure AD hitelesítő adatokkal kiváló biztonságot és egyszerű használatot biztosít más engedélyezési eszközökkel szemben. Bár továbbra is használhatja a megosztott kulcs engedélyezése az alkalmazások, az Azure AD használatával megkerüli a fiók hozzáférési kulcsának a kóddal való tárolásának szükségességét. Továbbra is használhatja a megosztott hozzáférésű aláírások (SAS) a tárfiókban lévő erőforrások részletes eléréséhez való hozzáférést, de az Azure AD hasonló képességeket kínál anélkül, hogy kezelnie kellene a SAS-jogkivonatokat, vagy aggódnia kellene a feltört SAS visszavonása miatt.
>
> A Microsoft azt javasolja, hogy az Azure Storage blob és a várólista-alkalmazások, ha lehetséges, azure AD-engedélyezés használata.

## <a name="copying-data-into-a-storage-account"></a>Adatok másolása tárfiókba

A Microsoft segédprogramokat és tárakat biztosít az adatok helyszíni tárolóeszközökről vagy külső felhőalapú tárolószolgáltatókról történő importálásához. A használt megoldás az átvinni kívánt adatok mennyiségétől függ.

Ha általános célú v2-es fiókra frissít egy általános célú v1- vagy Blob-tárfiókból, a rendszer automatikusan áttelepíti az adatokat. A Microsoft ezt az utat javasolja a fiók frissítéséhez. Ha azonban úgy dönt, hogy az adatokat egy általános célú v1-fiókból egy Blob storage-fiókba helyez át, akkor manuálisan telepíti át az adatokat az alábbiakban ismertetett eszközök és tárak használatával.

### <a name="azcopy"></a>AzCopy

Az AzCopy egy Windows parancssori segédprogram, amely az adatok az Azure Storage szolgáltatásba vagy onnan máshová való nagyteljesítményű másolására lett kifejlesztve. Az AzCopy segítségével adatokat másolhat egy Blob tárfiókba egy meglévő általános célú tárfiókból, vagy adatokat tölthet fel a helyszíni tárolóeszközökről. További információt [az AzCopy parancssori segédprogrammal történő adatátvitelt](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ismertető szakaszban talál.

### <a name="data-movement-library"></a>Adatátviteli könyvtár

Az Azure Storage .NET-keretrendszerhez készült adatátviteli kódtára az AzCopyt működtető alapvető adatátviteli keretrendszeren alapul. A könyvtár az AzCopyhoz hasonló nagy teljesítményű, megbízható és könnyű adatátviteli műveletekhez készült. Az adatmozgatási tár segítségével natív módon kihasználhatja az AzCopy-funkciók előnyeit. További információ: [Azure Storage Data Movement Library for .](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>REST API vagy ügyfélkódtár

Létrehozhat egy egyéni alkalmazást, amely áttelepíti az adatokat egy általános célú v1-es tárfiókból egy Blob storage-fiókba. Használja az Azure-ügyfélkódtárak vagy az Azure storage services REST API-t. Az Azure Storage gazdag ügyfélkódtárakat biztosít több nyelvhez és platformhoz is, beleértve a következőket: .NET, Java, C++, Node.JS, PHP, Ruby és Python. Az ügyfélkódtárak olyan fejlett képességeket biztosítanak, mint az újrapróbálkozási logika, a naplózás vagy a párhuzamos feltöltések. Fejleszthet közvetlenül a REST API-n is, amely minden, HTTP-/HTTPS-kérelmek létrehozására alkalmas nyelven meghívható.

Az Azure Storage REST API-ról az [Azure Storage Services REST API-val kapcsolatos referencia című témakörben talál további](https://docs.microsoft.com/rest/api/storageservices/)információt.

> [!IMPORTANT]
> Az ügyféloldali titkosítással titkosított blobok a titkosítással kapcsolatos metaadatokat a blobbal együtt tárolják. Ha ügyféloldali titkosítással titkosított blobot másol, ügyeljen arra, hogy a másolási művelet megőrizze a blob metaadatait, különös tekintettel a titkosítással kapcsolatos metaadatokra. Ha a blobokat a titkosítási metaadatok nélkül másolja, a blob tartalma nem kérhető le újból. További információt a titkosítással kapcsolatos metaadatokról [az Azure Storage ügyféloldali titkosítását](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ismertető szakaszban talál.

## <a name="storage-account-billing"></a>Tárfiókok számlázása

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>További lépések

- [Tárfiók létrehozása](storage-account-create.md)
- [Blokkblob-tárfiók létrehozása](../blobs/storage-blob-create-account-block-blob.md)
