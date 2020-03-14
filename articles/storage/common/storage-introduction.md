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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256067"
---
# <a name="introduction-to-azure-storage"></a>A Microsoft Azure Storage bemutatása

Az Azure Storage a Microsoft felhőalapú tárolási megoldása a modern adattárolási forgatókönyvekhez. Az Azure Storage az adatobjektumok nagymértékben skálázható objektumtárolását, a felhő fájlrendszer-szolgáltatását, a megbízható üzenetküldést elősegítő üzenettárolást és egy NoSQL-tárolót biztosít. Az Azure Storage:

- **Tartós és magas rendelkezésre állású.** A redundancia biztosítja az adatok biztonságát átmeneti hardverhiba esetén. Az adatközpontok vagy földrajzi helyek adatait replikálhatja is a helyi vagy természeti katasztrófák elleni további védelem érdekében. Az így replikált adatok magas rendelkezésre állásúak maradnak váratlan meghibásodás esetén is.
- **Biztonságos.** A szolgáltatás titkosítja az Azure Storage tárterületre írt összes adatot. Az Azure Storage használatával részletesen szabályozhatja, hogy ki férhet hozzá az adatokhoz.
- **Skálázható.** Az Azure Storage nagymértékben skálázható, hogy megfeleljen a mai alkalmazások adattárolási és teljesítménybeli igényeinek.
- **Felügyelt.** Microsoft Azure kezeli a hardveres karbantartást, a frissítéseket és a kritikus problémákat.
- **Hozzáférhető.** Az Azure Storage tárterületen lévő adatok a világon bárhonnan elérhetők HTTP- vagy HTTPS-kapcsolaton keresztül. A Microsoft számos nyelven biztosít ügyféloldali kódtárakat az Azure Storage-hoz, többek között a .NET, a Java, a Node. js, a Python, a PHP, a Ruby, a Go és mások számára, valamint egy érett REST API. Az Azure Storage támogatja az Azure PowerShell és az Azure CLI szkriptjeit. Az Azure Portal és az Azure Storage Explorer emellett egyszerű vizuális megoldásokat nyújt az adatok használatához.  

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

További információ a blob Storage-ról: [Bevezetés a blob Storage](../blobs/storage-blobs-introduction.md)-ba.

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

Tegyük fel például, hogy biztosítani szeretné az ügyfelei számára, hogy képeket tölthessenek fel, az egyes képekhez pedig miniatűröket szeretne létrehozni. Választhatná azt, hogy az ügyfeleknek a feltöltéskor várniuk kelljen a miniatűrök létrehozására. Alternatív megoldásként azonban használhat üzenetsort is. Amikor az ügyfél befejezi a feltöltést, írjon egy üzenetet a várólistára. Ezután egy Azure-függvénnyel hívja le az üzenetet az üzenetsorból, és hozza létre a miniatűrt. Ennek az eljárásnak minden egyes része külön méretezhető, és így szabadabban hangolhatja a felhasználási céloknak megfelelően.

További információ az Azure Queuesról: [Bevezetés az Azure Queues használatába](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Table Storage

Az Azure Table Storage mostantól az Azure Cosmos DB része. Az Azure Table Storage dokumentációját lásd: [Az Azure Table Storage áttekintése](../tables/table-storage-overview.md). A meglévő Azure Table Storage-szolgáltatáson kívül elérhető egy új Azure Cosmos DB Table API-ajánlat, amely teljesítményoptimalizált táblákat, globális elosztást és automatikus másodlagos indexeket is biztosít. Ha további információra van szüksége, vagy szeretné kipróbálni az új prémium ajánlatot, tekintse meg az [Azure Cosmos DB Table API](https://aka.ms/premiumtables)-t ismertető cikket.

További információ a Table Storage-ról: [Az Azure Table Storage áttekintése](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Lemezes tárolás

Az Azure Managed Disk egy virtuális merevlemez (VHD). Úgy gondolhatja, mint egy fizikai lemez egy helyszíni kiszolgálón, de virtualizált. Az Azure Managed Disks szolgáltatás blobként tárolódik, amely az Azure-ban egy véletlenszerű IO Storage-objektum. A felügyelt lemezeket nevezzük felügyelt, mivel ez egy absztrakt lap Blobok, blob-tárolók és Azure Storage-fiókok esetében. A felügyelt lemezekkel mindössze annyit kell tennie, hogy kiépíti a lemezt, az Azure pedig gondoskodik a többiről.

A felügyelt lemezekkel kapcsolatos további információkért lásd: [Bevezetés az Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md)szolgáltatásba.

## <a name="types-of-storage-accounts"></a>A tárfiókok típusai

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

További információ a Storage-fiókok típusairól: az [Azure Storage-fiók áttekintése](storage-account-overview.md).

## <a name="securing-access-to-storage-accounts"></a>A Storage-fiókokhoz való hozzáférés biztonságossá tétele

Minden Azure Storage-kérelemnek engedélyezve kell lennie. Az Azure Storage a következő engedélyezési módszereket támogatja:

- **Azure Active Directory (Azure AD) integráció a blob-és üzenetsor-adatkezeléshez.** Az Azure Storage a szerepköralapú hozzáférés-vezérlés (RBAC) révén támogatja az Azure AD-vel történő hitelesítést és engedélyezést a blob-és üzenetsor-szolgáltatásokhoz. A kérések Azure AD-vel való engedélyezése ajánlott a kiváló biztonság és a könnyű használat érdekében. További információ: az [Azure-blobok és-várólisták hozzáférésének engedélyezése Azure Active Directory használatával](storage-auth-aad.md).
- **Azure AD-hitelesítés SMB-en keresztül Azure Files (előzetes verzió).** Azure Files támogatja az identitás-alapú hitelesítést SMB-n keresztül (Server Message Block) a Azure Active Directory Domain Serviceson keresztül. A tartományhoz csatlakoztatott Windows rendszerű virtuális gépek (VM-EK) Azure AD-beli hitelesítő adatokkal érhetik el az Azure-fájlmegosztást. További információkért lásd: [az SMB-en keresztüli Azure Active Directory engedélyezésének áttekintése Azure Files (előzetes verzió)](../files/storage-files-active-directory-overview.md).
- **Hitelesítés megosztott kulccsal.** A Azure Storage Blob, a várólista és a Table Services és a Azure Files támogatja a megosztott kulccsal való engedélyezést. A megosztott kulcsos hitelesítéssel rendelkező ügyfelek a Storage-fiók elérési kulcsával aláírt minden kérelemmel továbbítanak egy fejlécet. További információ: [Engedélyezés megosztott kulccsal](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key).
- **Hitelesítés közös hozzáférésű aláírásokkal (SAS).** A közös hozzáférésű aláírás (SAS) egy olyan karakterlánc, amely egy olyan biztonsági jogkivonatot tartalmaz, amely egy tárolási erőforrás URI azonosítójának hozzáfűzésére használható. A biztonsági jogkivonat olyan korlátozásokat ágyaz be, mint az engedélyek és a hozzáférés intervalluma. További információt a [közös hozzáférésű aláírások (SAS) használata](storage-sas-overview.md)című témakörben talál.
- **A tárolók és Blobok névtelen hozzáférése.** Előfordulhat, hogy a tároló és a Blobok nyilvánosan elérhetők. Ha megadja, hogy a tároló vagy a blob nyilvános, akkor bárki névtelenül olvashatja. nincs szükség hitelesítésre. További információk: [Manage anonymous read access to containers and blobs](../blobs/storage-manage-access-to-resources.md) (Tárolók és blobok névtelen olvasási hozzáférésének kezelése).

## <a name="encryption"></a>Titkosítás

A Storage-szolgáltatásokban két alapszintű titkosítási módszer áll rendelkezésre. A biztonsággal és a titkosítással kapcsolatos további információkért lásd az [Azure Storage biztonsági útmutatóját](../blobs/security-recommendations.md).

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

Az Azure Storage-titkosítás megvédi és védi az adatait, hogy megfeleljen a szervezeti biztonsági és megfelelőségi kötelezettségeknek. Az Azure Storage automatikusan titkosítja az összes adathalmazt a Storage-fiók megőrzése előtt, és visszafejti azt a lekérés előtt. A titkosítás, a visszafejtés és a kulcskezelő folyamat teljes mértékben átlátható a felhasználók számára. Az ügyfelek a Azure Key Vault használatával is dönthetnek saját kulcsaik kezeléséhez. További információ: az [Azure Storage titkosítása inaktív adatokhoz](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Ügyféloldali titkosítás

Az Azure Storage ügyféloldali kódtárai módszerekkel titkosítják az adatokat az ügyfél könyvtárából, mielőtt elküldené a hálózaton, és visszafejti a választ. Az ügyféloldali titkosításon keresztül titkosított adatok az Azure Storage szolgáltatásban is titkosítva vannak. Az ügyféloldali titkosítással kapcsolatos további információkért lásd: [ügyféloldali titkosítás az Azure Storage-hoz készült .net-](storage-client-side-encryption.md)tel.

## <a name="redundancy"></a>Redundancia

Az Azure Storage több példányban tárolja az adatait, így biztosítva, hogy az adatai tartósak legyenek. A Storage-fiók beállításakor ki kell választania egy redundancia beállítást.

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="transferring-data-to-and-from-azure-storage"></a>Adatok áthelyezése az Azure Storage-ba és az Azure Storage-ból

Az Azure Storage-ba vagy az-ba való adatáthelyezésre több lehetőség is rendelkezésre áll. A választott lehetőség az adatkészlet méretétől és a hálózati sávszélességtől függ. További információ: Azure- [alapú megoldás kiválasztása adatátvitelhez](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Díjszabás

Az Azure Storage díjszabásával kapcsolatos részletes információkért lásd az [Árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>Storage API-k, kódtárak és eszközök

Az Azure Storage-erőforrások bármely olyan nyelvvel hozzáférhetők, amelyekkel HTTP/HTTPS kérelmek indíthatók. Ezenfelül az Azure Storage számos népszerű nyelvhez biztosít programozási kódtárakat. Ezek a kódtárak sok szempontból leegyszerűsítik az Azure Storage használatát, mivel számos részletet kezelnek (például a szinkron és aszinkron hívás, műveletek kötegelése, kivételek kezelése, automatikus újrapróbálkozások, működési viselkedés stb.). Jelenleg a következő nyelvekhez és platformokhoz érhetők el kódtárak (a továbbiak összeállítása folyamatban):

### <a name="azure-storage-data-api-and-library-references"></a>Az Azure Storage adat API-ja és kódtárhivatkozásai

- [Azure Storage-REST API](https://docs.microsoft.com/rest/api/storageservices/)
- [Azure Storage ügyféloldali kódtára a .NET-hez](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
- [Azure Storage ügyféloldali kódtár Java/Android rendszerhez](https://docs.microsoft.com/java/api/overview/azure/storage)
- [Azure Storage ügyféloldali kódtár a Node. js-hez](https://docs.microsoft.com/javascript/api/overview/azure/storage-overview)
- [Az Azure Storage ügyféloldali kódtára a Pythonhoz](https://github.com/Azure/azure-storage-python)
- [Az Azure Storage ügyféloldali kódtára a PHP-hez](https://github.com/Azure/azure-storage-php)
- [Azure Storage ügyféloldali kódtár a Rubyhoz](https://github.com/Azure/azure-storage-ruby)
- [Azure Storage ügyféloldali kódtár a következőhöz:C++](https://github.com/Azure/azure-storage-cpp)

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

## <a name="next-steps"></a>Következő lépések

Az Azure Storage gyors üzembe helyezéséhez lásd a [tárfiók létrehozásával](storage-account-create.md) foglalkozó témakört.
