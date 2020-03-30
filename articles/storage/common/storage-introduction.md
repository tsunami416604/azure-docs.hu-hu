---
title: Bevezetés az Azure Storage használatába – Felhőalapú tárolás az Azure-ban | Microsoft Docs
description: Az Azure Storage a Microsoft felhőalapú tárolási megoldása. Az Azure Storage magas rendelkezésre állású, biztonságos, tartós, méretezhető és redundáns adatobjektum-tárolást biztosít.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5bab70b6b023a4e6510e32368d407a38388cde2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256067"
---
# <a name="introduction-to-azure-storage"></a>A Microsoft Azure Storage bemutatása

Az Azure Storage a Microsoft felhőalapú tárolási megoldása a modern adattárolási forgatókönyvekhez. Az Azure Storage az adatobjektumok nagymértékben skálázható objektumtárolását, a felhő fájlrendszer-szolgáltatását, a megbízható üzenetküldést elősegítő üzenettárolást és egy NoSQL-tárolót biztosít. Az Azure Storage:

- **Tartós és magas rendelkezésre állású.** A redundancia biztosítja az adatok biztonságát átmeneti hardverhiba esetén. Az adatközpontok vagy földrajzi helyek adatait replikálhatja is a helyi vagy természeti katasztrófák elleni további védelem érdekében. Az így replikált adatok magas rendelkezésre állásúak maradnak váratlan meghibásodás esetén is.
- **Biztonságos.** A szolgáltatás titkosítja az Azure Storage tárterületre írt összes adatot. Az Azure Storage használatával részletesen szabályozhatja, hogy ki férhet hozzá az adatokhoz.
- **Méretezhető.** Az Azure Storage nagymértékben skálázható, hogy megfeleljen a mai alkalmazások adattárolási és teljesítménybeli igényeinek.
- **Felügyelt.** A Microsoft Azure kezeli a hardverkarbantartást, a frissítéseket és a kritikus problémákat.
- **Hozzáférhető.** Az Azure Storage tárterületen lévő adatok a világon bárhonnan elérhetők HTTP- vagy HTTPS-kapcsolaton keresztül. A Microsoft ügyfélkönyvtárakat biztosít az Azure Storage-hoz számos nyelven, beleértve a .NET, Java, Node.js, Python, PHP, Ruby, Go és mások, valamint egy érett REST API-t. Az Azure Storage támogatja az Azure PowerShell és az Azure CLI szkriptjeit. Az Azure Portal és az Azure Storage Explorer emellett egyszerű vizuális megoldásokat nyújt az adatok használatához.  

## <a name="azure-storage-services"></a>Azure Storage-szolgáltatások

Az Azure Storage ezeket az adatszolgáltatásokat tartalmazza:

- [Azure Blobs](../blobs/storage-blobs-introduction.md): Nagymértékben skálázható objektumtároló szöveges és bináris adatokhoz.
- [Azure Files](../files/storage-files-introduction.md): Felügyelt fájlmegosztások felhőalapú vagy helyszíni üzemelő példányokhoz.
- [Azure Queues](../queues/storage-queues-introduction.md): Az alkalmazások összetevői között megbízható üzenetkezelést biztosító üzenettároló.
- [Azure Tables](../tables/table-storage-overview.md): A strukturált adatok séma nélküli tárolására szolgáló NoSQL-tároló.

Mindegyik szolgáltatás tárfiókon keresztül érhető el. Első lépésként lásd: [Tárfiók létrehozása](storage-account-create.md).

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

Jelenleg az Active Directory-alapú hitelesítés és a hozzáférés-vezérlési listák (ACL-ek) még nem támogatottak, azonban a későbbiekben ez is megvalósul majd. A fájlmegosztáshoz való hozzáférés a tárfiók hitelesítő adataival hitelesíthető. Ez azt jelenti, hogy bárki, akinél a fájlmegosztás csatlakoztatva van, teljes írási/olvasási jogosultsággal rendelkezik rajta.

További információ az Azure Filesról: [Bevezetés az Azure Files használatába](../files/storage-files-introduction.md).

## <a name="queue-storage"></a>Queue Storage

Az Azure Queue szolgáltatás üzenetek tárolására és lehívására használható. Az üzenetsor üzenetei egyenként legfeljebb 64 KB méretűek lehetnek, és az üzenetsor akár több millió üzenetet is tartalmazhat. Az üzenetsorok általában aszinkron feldolgozásra szánt üzenetek listáit tárolják.

Tegyük fel például, hogy biztosítani szeretné az ügyfelei számára, hogy képeket tölthessenek fel, az egyes képekhez pedig miniatűröket szeretne létrehozni. Választhatná azt, hogy az ügyfeleknek a feltöltéskor várniuk kelljen a miniatűrök létrehozására. Alternatív megoldásként azonban használhat üzenetsort is. Amikor az ügyfél befejezi a feltöltést, írjon egy üzenetet a várólistába. Ezután egy Azure-függvénnyel hívja le az üzenetet az üzenetsorból, és hozza létre a miniatűrt. Ennek az eljárásnak minden egyes része külön méretezhető, és így szabadabban hangolhatja a felhasználási céloknak megfelelően.

További információ az Azure Queuesról: [Bevezetés az Azure Queues használatába](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Table Storage

Az Azure Table Storage mostantól az Azure Cosmos DB része. Az Azure Table Storage dokumentációját lásd: [Az Azure Table Storage áttekintése](../tables/table-storage-overview.md). A meglévő Azure Table Storage-szolgáltatáson kívül elérhető egy új Azure Cosmos DB Table API-ajánlat, amely teljesítményoptimalizált táblákat, globális elosztást és automatikus másodlagos indexeket is biztosít. Ha további információra van szüksége, vagy szeretné kipróbálni az új prémium ajánlatot, tekintse meg az [Azure Cosmos DB Table API](https://aka.ms/premiumtables)-t ismertető cikket.

További információ a Table Storage-ról: [Az Azure Table Storage áttekintése](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Lemezes tárolás

Az Azure által kezelt lemez egy virtuális merevlemez (VHD). Úgy is gondolhat rá, mint egy fizikai lemezre egy helyszíni kiszolgálón, de virtualizált. Az Azure által kezelt lemezek lapblobokként tárolódnak, amelyek az Azure-ban egy véletlenszerű I/O-tárolóobjektum. Felügyelt lemezt "felügyeltnek" hívunk, mert ez egy absztrakció a lapblobok, blobtárolók és Azure storage-fiókok felett. Felügyelt lemezek, mindössze annyit kell tennie, hogy a lemez kiépítése, és az Azure gondoskodik a többi.

A felügyelt lemezekről további információt az Azure által felügyelt lemezek – bevezetés című [témakörben talál.](../../virtual-machines/windows/managed-disks-overview.md)

## <a name="types-of-storage-accounts"></a>A tárfiókok típusai

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

A tárfiók-típusokról az [Azure storage-fiók áttekintése című témakörben olvashat bővebben.](storage-account-overview.md)

## <a name="securing-access-to-storage-accounts"></a>A tárfiókokhoz való hozzáférés védelme

Az Azure Storage-ba irányuló minden kérést engedélyezni kell. Az Azure Storage a következő engedélyezési módszereket támogatja:

- **Azure Active Directory (Azure AD) integráció blob és várólista-adatok.** Az Azure Storage támogatja a hitelesítést és az engedélyezést az Azure AD-vel a Blob és a Queue szolgáltatások szerepköralapú hozzáférés-vezérlés (RBAC) keresztül. A kérelmek engedélyezése az Azure AD-vel a kiváló biztonság és a könnyű használat érdekében ajánlott. További információ: [Hozzáférés engedélyezése az Azure-blobokhoz és várólistákhoz az Azure Active Directory használatával című témakörben.](storage-auth-aad.md)
- **Azure AD-hitelesítés SMB-n keresztül az Azure Files (előzetes verzió).** Az Azure Files támogatja az identitásalapú engedélyezést az SMB-n (Kiszolgálói üzenetblokkon) keresztül az Azure Active Directory tartományi szolgáltatásokon keresztül. A tartományhoz csatlakozó Windows virtuális gépek (VM-ek) azure-beli fájlmegosztások azure AD hitelesítő adatokkal férhetnek hozzá. További információ: [Az Azure Active Directory-hitelesítés áttekintése Az Azure Files smb-n keresztül (előzetes verzió) című témakörben olvashat.](../files/storage-files-active-directory-overview.md)
- **Engedélyezés megosztott kulccsal.** Az Azure Storage Blob, Queue és Table services és az Azure Files támogatja az engedélyezést a megosztott kulcsú ügyféllel megosztott kulcs engedélyezési halad egy fejléc minden kérelmet, amely a tárfiók hozzáférési kulcs használatával aláírt. További információt a [Megosztáskulcs engedélyezése című témakörben talál.](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)
- **Engedélyezés megosztott hozzáférésű aláírásokkal (SAS).** A megosztott hozzáférésű aláírás (SAS) egy olyan karakterlánc, amely egy biztonsági jogkivonatot tartalmaz, amely hozzáfűzhető egy tárolási erőforrás URI-jához. A biztonsági jogkivonat korlátozásokat, például engedélyeket és a hozzáférés időközét ágyazbe. További információt a [Megosztott hozzáférésű aláírások (SAS) használata](storage-sas-overview.md)című dokumentumban talál.
- **Névtelen hozzáférés a tárolókhoz és blobokhoz.** Egy tároló és a blobok nyilvánosan elérhető lehet. Ha azt adja meg, hogy egy tároló vagy blob nyilvános, bárki névtelenül olvashatja el; nincs szükség hitelesítésre. További információk: [Manage anonymous read access to containers and blobs](../blobs/storage-manage-access-to-resources.md) (Tárolók és blobok névtelen olvasási hozzáférésének kezelése).

## <a name="encryption"></a>Titkosítás

A Storage-szolgáltatásokban két alapszintű titkosítási módszer áll rendelkezésre. A biztonsággal és a titkosítással kapcsolatos további információkért lásd az [Azure Storage biztonsági útmutatóját](../blobs/security-recommendations.md).

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

Az Azure Storage titkosítása védi és védi az adatokat, hogy megfeleljen a szervezeti biztonsági és megfelelőségi kötelezettségvállalásoknak. Az Azure Storage automatikusan titkosítja az összes adatot, mielőtt a tárfiókban maradna, és visszafejti azt a lekérés előtt. A titkosítási, visszafejtési és kulcskezelési folyamatok teljesen átláthatóak a felhasználók számára. Az ügyfelek is választhatnak, hogy saját kulcsokat az Azure Key Vault használatával kezelhetik. További információ: [Azure Storage titkosítás a nyugalmi adatok.](storage-service-encryption.md)

### <a name="client-side-encryption"></a>Ügyféloldali titkosítás

Az Azure Storage-ügyfélkódtárak az ügyfélkönyvtárból származó adatok titkosítására szolgálnak, mielőtt elküldenék azadatokat a hálózaton keresztül, és visszafejtik a választ. Az ügyféloldali titkosítással titkosított adatokat az Azure Storage is titkosítja. Az ügyféloldali titkosításról további információt a [.NET for Azure Storage ügyféloldali titkosításcímű témakörben talál.](storage-client-side-encryption.md)

## <a name="redundancy"></a>Redundancia

Annak érdekében, hogy az adatok tartósak legyenek, az Azure Storage az adatok több példányát tárolja. A tárfiók beállításakor egy redundancialehetőséget választ.

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="transferring-data-to-and-from-azure-storage"></a>Adatok áthelyezése az Azure Storage-ba és az Azure Storage-ból

Számos lehetősége van az adatok áthelyezésére vagy az Azure Storage-ból való ki- vagy kimozgatására. A választott beállítás az adatkészlet méretétől és a hálózati sávszélességtől függ. További információ: [Válassza ki az Azure-megoldást adatátvitelhez.](storage-choose-data-transfer-solution.md)

## <a name="pricing"></a>Díjszabás

Az Azure Storage díjszabásával kapcsolatos részletes információkért lásd az [Árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>Storage API-k, kódtárak és eszközök

Az Azure Storage-erőforrások bármely olyan nyelvvel hozzáférhetők, amelyekkel HTTP/HTTPS kérelmek indíthatók. Ezenfelül az Azure Storage számos népszerű nyelvhez biztosít programozási kódtárakat. Ezek a kódtárak sok szempontból leegyszerűsítik az Azure Storage használatát, mivel számos részletet kezelnek (például a szinkron és aszinkron hívás, műveletek kötegelése, kivételek kezelése, automatikus újrapróbálkozások, működési viselkedés stb.). Jelenleg a következő nyelvekhez és platformokhoz érhetők el kódtárak (a továbbiak összeállítása folyamatban):

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

Az Azure Storage gyors üzembe helyezéséhez lásd a [tárfiók létrehozásával](storage-account-create.md) foglalkozó témakört.
