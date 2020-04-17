---
title: Bevezetés az Azure Storage használatába – Felhőalapú tárolás az Azure-ban | Microsoft Docs
description: Az Azure Storage alapvető platformja a Microsoft felhőalapú tárolási megoldása. Az Azure Storage magas rendelkezésre állású, biztonságos, tartós, méretezhető és redundáns adatobjektum-tárolást biztosít.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 6c75b9c2feeab32c139998f617cacf540733e3bd
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456345"
---
# <a name="introduction-to-the-core-azure-storage-services"></a>Bevezetés az alapvető Azure Storage-szolgáltatásokba

Az Azure Storage platform a Microsoft felhőalapú tárolási megoldása a modern adattárolási forgatókönyvekhez. Az alapvető tárolási szolgáltatások nagymértékben méretezhető objektumtárolót kínálnak az adatobjektumokhoz, az Azure virtuális gépek (VM-ek) lemezes tárolását, a felhő fájlrendszer-szolgáltatását, a megbízható üzenetküldést szolgáló üzenettárolót és a NoSQL-tárolót. A szolgáltatások a következők:

- **Tartós és magas rendelkezésre állású.** A redundancia biztosítja az adatok biztonságát átmeneti hardverhiba esetén. Az adatközpontok vagy földrajzi helyek adatait replikálhatja is a helyi vagy természeti katasztrófák elleni további védelem érdekében. Az így replikált adatok magas rendelkezésre állásúak maradnak váratlan meghibásodás esetén is.
- **Biztonságos.** Az Azure-tárfiókba írt összes adatot a szolgáltatás titkosítja. Az Azure Storage használatával részletesen szabályozhatja, hogy ki férhet hozzá az adatokhoz.
- **Méretezhető.** Az Azure Storage nagymértékben skálázható, hogy megfeleljen a mai alkalmazások adattárolási és teljesítménybeli igényeinek.
- **Felügyelt.** Az Azure kezeli a hardverkarbantartást, a frissítéseket és a kritikus problémákat.
- **Hozzáférhető.** Az Azure Storage tárterületen lévő adatok a világon bárhonnan elérhetők HTTP- vagy HTTPS-kapcsolaton keresztül. A Microsoft ügyfélkönyvtárakat biztosít az Azure Storage-hoz számos nyelven, beleértve a .NET, Java, Node.js, Python, PHP, Ruby, Go és mások, valamint egy érett REST API-t. Az Azure Storage támogatja az Azure PowerShell és az Azure CLI szkriptjeit. Az Azure Portal és az Azure Storage Explorer emellett egyszerű vizuális megoldásokat nyújt az adatok használatához.  

## <a name="core-storage-services"></a>Alapvető tárolási szolgáltatások

Az Azure Storage platform a következő adatszolgáltatásokat tartalmazza:

- [Azure Blobs](../blobs/storage-blobs-introduction.md): Nagymértékben skálázható objektumtároló szöveges és bináris adatokhoz. A Data Lake Storage Gen2-en keresztül támogatja a big data-elemzéseket is.
- [Azure Files](../files/storage-files-introduction.md): Felügyelt fájlmegosztások felhőalapú vagy helyszíni üzemelő példányokhoz.
- [Azure Queues](../queues/storage-queues-introduction.md): Az alkalmazások összetevői között megbízható üzenetkezelést biztosító üzenettároló.
- [Azure Tables](../tables/table-storage-overview.md): A strukturált adatok séma nélküli tárolására szolgáló NoSQL-tároló.
- [Azure Disks:](../../virtual-machines/windows/managed-disks-overview.md)Blokkszintű tárolási kötetek az Azure virtuális gépek.

Mindegyik szolgáltatás tárfiókon keresztül érhető el. Első lépésként lásd: [Tárfiók létrehozása](storage-account-create.md).

## <a name="example-scenarios"></a>Példaforgatókönyvek

Az alábbi táblázat összehasonlítja a fájlokat, a blobokat, a lemezeket, a várólistákat és a táblákat, és bemutatja az egyes példaforgatókönyveket.

| Szolgáltatás | Leírás | A következő esetekben használja |
|--------------|-------------|-------------|
| **Azure Files** |Teljes körűen felügyelt felhőfájl-megosztásokat kínál, amelyek bárhonnan elérhetők az iparági szabványnak megfelelő SMB protokollon keresztül.<br><br>Az Azure-fájlmegosztásokat a Windows, Linux és macOS felhőbeli vagy helyszíni központi telepítéseiből csatlakoztathatja. | Azt szeretné, hogy "lift and shift" egy alkalmazást a felhőbe, amely már használja a natív fájlrendszer API-k at adatok megosztására közte és más azure-ban futó alkalmazások között.<br/><br/>A helyszíni fájlkiszolgálókat vagy NAS-eszközöket le szeretné cserélni vagy ki szeretné egészíteni.<br><br> Olyan fejlesztői és hibakeresési eszközöket szeretne tárolni, amelyeket számos virtuális gépről kell elérni. |
| **Azure Blobs** | Lehetővé teszi a strukturálatlan adatok tárolását és elérését tömeges en blokkblobokban.<br/><br/>Támogatja az [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) vállalati big data-elemzési megoldásokat is. | Azt szeretné, hogy az alkalmazás támogatja a streamelési és véletlenszerű hozzáférésű forgatókönyvek.<br/><br/>Bárhonnan hozzá szeretne férni az alkalmazásadatokhoz.<br/><br/>Nagyvállalati adattót szeretne építeni az Azure-on, és big data-elemzéseket szeretne végezni. |
| **Azure Disks** | Lehetővé teszi az adatok állandó tárolását és elérését egy csatlakoztatott virtuális merevlemezről. | Azt szeretné, hogy "lift and shift" alkalmazások, amelyek natív fájlrendszer API-k olvasására és írására adatokat állandó lemezekre.<br/><br/>Olyan adatokat szeretne tárolni, amelyekhez nem szükséges hozzáférni azon a virtuális gépen kívülről, amelyhez a lemez csatlakozik. |
| **Azure Queues** | Lehetővé teszi az alkalmazás-összetevők közötti aszinkron üzenetsor-várólistát. | Le szeretné választani az alkalmazás-összetevőket, és aszinkron üzenetküldést szeretne használni a közöttük való kommunikációhoz.<br><br>A Várólista-tároló és a Service Bus-várólisták használatának időpontjáról a [Tárolási várólisták és a Service Bus-várólisták összehasonlítása és kontrasztos témaköre.](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted) |
| **Azure-táblák** | Lehetővé teszi strukturált NoSQL-adatok tárolását a felhőben, így egy kulcs/attribútum tároló egy séma nélküli design. | Rugalmas adatkészleteket szeretne tárolni, például webes alkalmazások felhasználói adatait, címjegyzékeket, eszközadatokat vagy más típusú metaadatokat, amelyeket a szolgáltatás igényel. <br/><br/>A Table Storage és az Azure Cosmos DB Table API használatával kapcsolatos útmutatásért olvassa el a [Fejlesztés az Azure Cosmos DB Table API-val és az Azure Table storage-tal című témakört.](../../cosmos-db/table-support.md) |

## <a name="blob-storage"></a>Blob Storage

Az Azure Blob Storage a Microsoft felhőalapú objektumtárolási megoldása. A Blob Storage nagy mennyiségű strukturálatlan adat, például szöveg vagy bináris adatok tárolására van optimalizálva.

A Blob Storage a következőkhöz ideális:

- Képek vagy dokumentumok közvetlen szolgáltatása a böngészők számára.
- Fájlok tárolása megosztott hozzáféréshez.
- Video- és hangtartalom streamelése.
- Adattárolás biztonsági mentésekhez és helyreállításhoz, vészhelyreállításhoz és archiváláshoz.
- Adattárolás helyszíni vagy az Azure-ban üzemeltetett szolgáltatásban való elemzéshez.

A Blob Storage tárolóban lévő objektumok a világon bárhonnan elérhetők HTTP- vagy HTTPS-kapcsolaton keresztül. A felhasználók vagy ügyfélalkalmazások URL-eken, az [Azure Storage REST API-n](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), az [Azure PowerShellen](https://docs.microsoft.com/powershell/module/azure.storage), az [Azure CLI-n](https://docs.microsoft.com/cli/azure/storage) vagy Azure Storage-ügyfélkódtárakon keresztül érhetik el a blobokat. A tároló ügyfélkódtárai több nyelven érhetők el, beleértve a [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet), [Java](https://docs.microsoft.com/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) és [Ruby](https://azure.github.io/azure-storage-ruby) nyelveket.

A Blob storage szolgáltatásról további információt a Blob storage – Bevezetés című [témakörben talál.](../blobs/storage-blobs-introduction.md)

## <a name="azure-files"></a>Azure Files

Az [Azure Files](../files/storage-files-introduction.md) segítségével magas rendelkezésre állású hálózati fájlmegosztásokat hozhat létre, amelyek az SMB protokollon keresztül érhetőek el. Ez azt jelenti hogy ugyanazokhoz a fájlokhoz több virtuális gép is hozzáférhet olvasási és írási jogosultsággal. A fájlokat a REST-felület vagy a Storage klienskódtáraival is olvashatja.

Az egyik dolog, ami megkülönbözteti az Azure Files szolgáltatást a fájlok a vállalati fájlmegosztásokon való tárolásától, hogy a fájlokat a világ bármely pontjáról elérheti a rájuk mutató URL-címekkel, amelyek közös hozzáférésű jogosultságkód- (SAS-) tokenekkel rendelkeznek. SAS-tokeneket Ön is létrehozhat: ezek határozott ideig biztosítanak egyedi hozzáférést a privát objektumokhoz.

A fájlmegosztások számos gyakori forgatókönyvhöz használhatók:

- Számos helyszíni alkalmazás használ fájlmegosztásokat. Ezzel a szolgáltatással könnyebb migrálni azokat az alkalmazásokat, amelyek az Azure-ban osztják meg az adatokat. Ha a fájlmegosztást ugyanarra a meghajtóhelyre csatlakoztatja, amelyet a helyszíni alkalmazás használ, az alkalmazás fájlmegosztáshoz kapcsolódó része minimális vagy semennyi módosítással használható.

- A konfigurációs fájlok fájlmegosztásokon tárolhatók, és több virtuális gépről is elérhetők. A több fejlesztő által egy csoportban használt eszközök és segédprogramok is tárolhatók fájlmegosztásokon, így mindenki elérheti őket, és mindenki ugyanazt a verziót használhatja.

- A diagnosztikai naplók, metrikák és összeomlási memóriaképek is a fájlmegosztásra írható, és később onnan feldolgozható és elemezhető adattípusok közé tartoznak.

További információ az Azure Filesról: [Bevezetés az Azure Files használatába](../files/storage-files-introduction.md).

Egyes SMB-funkciók nem alkalmazhatók a felhőre. További információ: [Az Azure File szolgáltatás által nem támogatott szolgáltatások.](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)

## <a name="queue-storage"></a>Queue Storage

Az Azure Queue szolgáltatás üzenetek tárolására és lehívására használható. Az üzenetsor üzenetei egyenként legfeljebb 64 KB méretűek lehetnek, és az üzenetsor akár több millió üzenetet is tartalmazhat. Az üzenetsorok általában aszinkron feldolgozásra szánt üzenetek listáit tárolják.

Tegyük fel például, hogy biztosítani szeretné az ügyfelei számára, hogy képeket tölthessenek fel, az egyes képekhez pedig miniatűröket szeretne létrehozni. Választhatná azt, hogy az ügyfeleknek a feltöltéskor várniuk kelljen a miniatűrök létrehozására. Alternatív megoldásként azonban használhat üzenetsort is. Amikor az ügyfél befejezi a feltöltést, írjon egy üzenetet a várólistába. Ezután egy Azure-függvénnyel hívja le az üzenetet az üzenetsorból, és hozza létre a miniatűrt. Ennek az eljárásnak minden egyes része külön méretezhető, és így szabadabban hangolhatja a felhasználási céloknak megfelelően.

További információ az Azure Queuesról: [Bevezetés az Azure Queues használatába](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Table Storage

Az Azure Table Storage mostantól az Azure Cosmos DB része. Az Azure Table Storage dokumentációját lásd: [Az Azure Table Storage áttekintése](../tables/table-storage-overview.md). A meglévő Azure Table Storage-szolgáltatáson kívül elérhető egy új Azure Cosmos DB Table API-ajánlat, amely teljesítményoptimalizált táblákat, globális elosztást és automatikus másodlagos indexeket is biztosít. Ha többet szeretne megtudni, és kipróbálhatják az új prémium szintű élményt, olvassa el az [Azure Cosmos DB Table API című témakört.](https://aka.ms/premiumtables)

További információ a Table Storage-ról: [Az Azure Table Storage áttekintése](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Lemezes tárolás

Az Azure által kezelt lemez egy virtuális merevlemez (VHD). Úgy is gondolhat rá, mint egy fizikai lemezre egy helyszíni kiszolgálón, de virtualizált. Az Azure által felügyelt lemezek lapblobokként tárolódnak, amelyek az Azure-ban egy véletlenszerű I/O-tárolóobjektum. Felügyelt lemezt "felügyeltnek" hívunk, mert ez egy absztrakció a lapblobok, blobtárolók és Azure storage-fiókok felett. Felügyelt lemezek, mindössze annyit kell tennie, hogy a lemez kiépítése, és az Azure gondoskodik a többi.

A felügyelt lemezekről további információt az Azure által felügyelt lemezek – bevezetés című [témakörben talál.](../../virtual-machines/windows/managed-disks-overview.md)

## <a name="types-of-storage-accounts"></a>A tárfiókok típusai

Az Azure Storage többféle tárfiókot kínál. Minden típus támogatja a különböző funkciókat, és saját díjszabási modellel rendelkezik. A tárfiók-típusokról az [Azure storage-fiók áttekintése című témakörben olvashat bővebben.](storage-account-overview.md)

## <a name="secure-access-to-storage-accounts"></a>Biztonságos hozzáférés a tárfiókokhoz

Az Azure Storage-ba irányuló minden kérést engedélyezni kell. Az Azure Storage a következő engedélyezési módszereket támogatja:

- **Azure Active Directory (Azure AD) integráció blob és várólista-adatok.** Az Azure Storage támogatja a hitelesítést és az engedélyezést az Azure AD-vel a Blob és a Queue szolgáltatások szerepköralapú hozzáférés-vezérlés (RBAC) keresztül. A kérelmek engedélyezése az Azure AD-vel a kiváló biztonság és a könnyű használat érdekében ajánlott. További információ: [Hozzáférés engedélyezése az Azure-blobokhoz és várólistákhoz az Azure Active Directory használatával című témakörben.](storage-auth-aad.md)
- **Azure AD-hitelesítés SMB-n keresztül az Azure Files számára.** Az Azure Files támogatja az identitásalapú engedélyezést Az SMB (Server Message Block) keresztül vagy az Azure Active Directory tartományi szolgáltatások (Azure AD DS) vagy a helyszíni Active Directory tartományi szolgáltatások (előzetes verzió). A tartományhoz csatlakozó Windows virtuális gépek az Azure AD-hitelesítő adatok használatával érhetik el az Azure-fájlmegosztásokat. További információ: [Az Azure Files identitásalapú hitelesítési támogatásáttekintése az SMB-hozzáféréshez](../files/storage-files-active-directory-overview.md) és [az Azure Files telepítésének megtervezése](../files/storage-files-planning.md#identity)című témakörben olvashat.
- **Engedélyezés megosztott kulccsal.** Az Azure Storage Blob, Files, Queue és Table services támogatja az engedélyezést a megosztott kulcs. A megosztott kulcs engedélyezést használó ügyfél fejlécet ad át minden olyan kérelemmel, amely a tárfiók hozzáférési kulcsával van aláírva. További információt a [Megosztáskulcs engedélyezése című témakörben talál.](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)
- **Engedélyezés megosztott hozzáférésű aláírásokkal (SAS).** A megosztott hozzáférésű aláírás (SAS) egy olyan karakterlánc, amely egy biztonsági jogkivonatot tartalmaz, amely hozzáfűzhető egy tárolási erőforrás URI-jához. A biztonsági jogkivonat korlátozásokat, például engedélyeket és a hozzáférés időközét ágyazbe. További információt a [Megosztott hozzáférésű aláírások (SAS) használata](storage-sas-overview.md)című témakörben talál.
- **Névtelen hozzáférés a tárolókhoz és blobokhoz.** Egy tároló és a blobok nyilvánosan elérhető lehet. Ha azt adja meg, hogy egy tároló vagy blob nyilvános, bárki névtelenül olvashatja el; nincs szükség hitelesítésre. További információt a [Névtelen olvasási hozzáférés kezelése a tárolókhoz és blobokhoz című témakörben talál.](../blobs/storage-manage-access-to-resources.md)

## <a name="encryption"></a>Titkosítás

Az alapvető tárolási szolgáltatásokhoz kétféle titkosítás érhető el. A biztonsággal és a titkosítással kapcsolatos további információkért lásd az [Azure Storage biztonsági útmutatóját](../blobs/security-recommendations.md).

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

Az Azure Storage titkosítása védi és védi az adatokat, hogy megfeleljen a szervezeti biztonsági és megfelelőségi kötelezettségvállalásoknak. Az Azure Storage automatikusan titkosítja az összes adatot, mielőtt a tárfiókban maradna, és visszafejti azt a lekérés előtt. A titkosítási, visszafejtési és kulcskezelési folyamatok a felhasználók számára átláthatóak. Az ügyfelek is választhatnak, hogy saját kulcsokat az Azure Key Vault használatával kezelhetik. További információ: [Azure Storage titkosítás a nyugalmi adatok.](storage-service-encryption.md)

### <a name="client-side-encryption"></a>Ügyféloldali titkosítás

Az Azure Storage-ügyfélkódtárak az ügyfélkönyvtárból származó adatok titkosítására szolgálnak, mielőtt elküldenék azadatokat a hálózaton keresztül, és visszafejtik a választ. Az ügyféloldali titkosítással titkosított adatokat az Azure Storage is titkosítja. Az ügyféloldali titkosításról további információt a [.NET for Azure Storage ügyféloldali titkosításcímű témakörben talál.](storage-client-side-encryption.md)

## <a name="redundancy"></a>Redundancia

Annak érdekében, hogy az adatok tartósak legyenek, az Azure Storage az adatok több példányát tárolja. A tárfiók beállításakor egy redundancialehetőséget választ. További információ: [Azure Storage-redundancia](/azure/storage/common/storage-redundancy?toc=/azure/storage/blobs/toc.json).

## <a name="transfer-data-to-and-from-azure-storage"></a>Adatok átvitele az Azure Storage-ba és onnan

Számos lehetősége van az adatok áthelyezésére vagy az Azure Storage-ból való ki- vagy kimozgatására. A választott beállítás az adatkészlet méretétől és a hálózati sávszélességtől függ. További információ: [Válassza ki az Azure-megoldást adatátvitelhez.](storage-choose-data-transfer-solution.md)

## <a name="pricing"></a>Díjszabás

Amikor az adatok tárolásával és elérésével kapcsolatos döntéseket hoz, figyelembe kell vennie a felmerülő költségeket is. További információ: [Azure Storage-díjszabás.](https://azure.microsoft.com/pricing/details/storage/)

## <a name="storage-apis-libraries-and-tools"></a>Storage API-k, kódtárak és eszközök

A tárfiókban lévő erőforrásokat bármely olyan nyelven elérheti, amely HTTP/HTTPS-kérelmeket tud bebiztosítani. Emellett az alapvető Azure Storage-szolgáltatások programozási könyvtárakat kínálnak számos népszerű nyelvhez. Ezek a kódtárak sok szempontból leegyszerűsítik az Azure Storage használatát, mivel számos részletet kezelnek (például a szinkron és aszinkron hívás, műveletek kötegelése, kivételek kezelése, automatikus újrapróbálkozások, működési viselkedés stb.). Jelenleg a következő nyelvekhez és platformokhoz érhetők el kódtárak (a továbbiak összeállítása folyamatban):

### <a name="azure-storage-data-api-and-library-references"></a>Az Azure Storage adat API-ja és kódtárhivatkozásai

- [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/)
- [Azure Storage-ügyfélkönyvtár a .NET-hez](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
- [Azure Storage-ügyfélkönyvtár Java/Android hoz](https://docs.microsoft.com/java/api/overview/azure/storage)
- [Azure Storage-ügyfélkönyvtár a Node.js fájlhoz](https://docs.microsoft.com/javascript/api/overview/azure/storage-overview)
- [Azure Storage-ügyfélkódtár pythonhoz](https://github.com/Azure/azure-storage-python)
- [Azure Storage-ügyfélkönyvtár PHP-hez](https://github.com/Azure/azure-storage-php)
- [Azure Storage-ügyfélkönyvtár a Ruby számára](https://github.com/Azure/azure-storage-ruby)
- [C++ azure storage-ügyfélkönyvtár](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Az Azure Storage felügyeleti API-ja és kódtárhivatkozásai

- [A Storage erőforrás-szolgáltató REST API-ja](https://docs.microsoft.com/rest/api/storagerp/)
- [Storage erőforrás-szolgáltató ügyfél a .NET-hez](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
- [A Storage szolgáltatásfelügyelet REST API-ja](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Az Azure Storage adatátviteli API-ja és kódtárhivatkozásai

- [A Storage Import/Export szolgáltatás REST API-ja](https://docs.microsoft.com/rest/api/storageimportexport/)
- [A Storage adatátviteli ügyféloldali kódtára a .NET-hez](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Eszközök és segédprogramok

- [Azure PowerShell-parancsmagok a Storage-hoz](https://docs.microsoft.com/powershell/module/az.storage)
- [Azure CLI-parancsmagok a Storage-hoz](https://docs.microsoft.com/cli/azure/storage)
- [AzCopy parancssori segédprogram](https://aka.ms/downloadazcopy)
- Az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) a Microsoft ingyenes, önálló alkalmazása, amellyel vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
- [Azure Storage-ügyféleszközök](../storage-explorers.md)
- [Azure fejlesztői eszközök](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>További lépések

Az alapvető Azure Storage-szolgáltatások létrehozásáról a [Tárfiók létrehozása című](storage-account-create.md)témakörben nyújt be.
