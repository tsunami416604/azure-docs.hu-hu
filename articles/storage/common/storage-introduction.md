---
title: Bevezetés az Azure Storage használatába – Felhőalapú tárolás az Azure-ban | Microsoft Docs
description: Az Azure Storage a Microsoft felhőalapú tárolási megoldása. Az Azure Storage magas rendelkezésre állású, biztonságos, tartós, méretezhető és redundáns adatobjektum-tárolást biztosít.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ae2384d0ac6773ccd362778d2913cdcaa9cb4d6c
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58446703"
---
# <a name="introduction-to-azure-storage"></a>A Microsoft Azure Storage bemutatása

Az Azure Storage a Microsoft felhőalapú tárolási megoldása a modern adattárolási forgatókönyvekhez. Az Azure Storage az adatobjektumok nagymértékben skálázható objektumtárolását, a felhő fájlrendszer-szolgáltatását, a megbízható üzenetküldést elősegítő üzenettárolást és egy NoSQL-tárolót biztosít. Az Azure Storage:

- **Tartós és magas rendelkezésre állású.** A redundancia biztosítja az adatok biztonságát átmeneti hardverhiba esetén. Az adatközpontok vagy földrajzi helyek adatait replikálhatja is a helyi vagy természeti katasztrófák elleni további védelem érdekében. Az így replikált adatok magas rendelkezésre állásúak maradnak váratlan meghibásodás esetén is. 
- **Biztonságos.** A szolgáltatás titkosítja az Azure Storage tárterületre írt összes adatot. Az Azure Storage használatával részletesen szabályozhatja, hogy ki férhet hozzá az adatokhoz.
- **Skálázható.** Az Azure Storage nagymértékben skálázható, hogy megfeleljen a mai alkalmazások adattárolási és teljesítménybeli igényeinek. 
- **Felügyelt.** A Microsoft Azure hardverkarbantartás, a frissítések és a kritikus fontosságú problémáit kezeli az Ön számára.
- **Hozzáférhető.** Az Azure Storage tárterületen lévő adatok a világon bárhonnan elérhetők HTTP- vagy HTTPS-kapcsolaton keresztül. A Microsoft számos nyelven (például .NET, Java, Node.js, Python, PHP, Ruby és Go) nyújt SDK-kat az Azure Storage-hoz, valamint egy kifinomult REST API-t is biztosít. Az Azure Storage támogatja az Azure PowerShell és az Azure CLI szkriptjeit. Az Azure Portal és az Azure Storage Explorer emellett egyszerű vizuális megoldásokat nyújt az adatok használatához.  

## <a name="azure-storage-services"></a>Azure Storage-szolgáltatások

Az Azure Storage ezeket az adatszolgáltatásokat tartalmazza: 

- [Azure-Blobok](../blobs/storage-blobs-introduction.md): Nagymértékben skálázható objektumtároló a szöveges és bináris adatokat.
- [Az Azure Files](../files/storage-files-introduction.md): Felügyelt fájlmegosztások felhőalapú vagy helyszíni üzemelő példányok.
- [Azure Queues](../queues/storage-queues-introduction.md): Alkalmazás-összetevők közötti megbízható üzenetküldést üzenetküldési tárolóban. 
- [Azure-táblák](../tables/table-storage-overview.md): NoSQL-alapú tárolót a strukturált adatok séma nélküli tárolására.

Mindegyik szolgáltatás tárfiókon keresztül érhető el. Első lépésként lásd: [Tárfiók létrehozása](storage-quickstart-create-account.md).

## <a name="blob-storage"></a>Blob Storage

Az Azure Blob Storage a Microsoft felhőalapú objektumtárolási megoldása. A Blob Storage nagy mennyiségű strukturálatlan adat, például szöveg vagy bináris adatok tárolására van optimalizálva. 

A Blob Storage a következőkhöz ideális:

* Képek vagy dokumentumok közvetlen szolgáltatása a böngészők számára.
* Fájlok tárolása megosztott hozzáféréshez.
* Video- és hangtartalom streamelése.
* Adattárolás biztonsági mentésekhez és helyreállításhoz, vészhelyreállításhoz és archiváláshoz.
* Adattárolás helyszíni vagy az Azure-ban üzemeltetett szolgáltatásban való elemzéshez.

A Blob Storage tárolóban lévő objektumok a világon bárhonnan elérhetők HTTP- vagy HTTPS-kapcsolaton keresztül. A felhasználók vagy ügyfélalkalmazások URL-eken, az [Azure Storage REST API-n](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), az [Azure PowerShellen](https://docs.microsoft.com/powershell/module/azure.storage), az [Azure CLI-n](https://docs.microsoft.com/cli/azure/storage) vagy Azure Storage-ügyfélkódtárakon keresztül érhetik el a blobokat. A tároló ügyfélkódtárai több nyelven érhetők el, beleértve a [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) és [Ruby](https://azure.github.io/azure-storage-ruby) nyelveket.

A Blob storage szolgáltatással kapcsolatos további információkért lásd: [Blob storage bemutatása](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>Azure Files
Az [Azure Files](../files/storage-files-introduction.md) segítségével magas rendelkezésre állású hálózati fájlmegosztásokat hozhat létre, amelyek az SMB protokollon keresztül érhetőek el. Ez azt jelenti hogy ugyanazokhoz a fájlokhoz több virtuális gép is hozzáférhet olvasási és írási jogosultsággal. A fájlokat a REST-felület vagy a Storage klienskódtáraival is olvashatja.

Az egyik dolog, ami megkülönbözteti az Azure Files szolgáltatást a fájlok a vállalati fájlmegosztásokon való tárolásától, hogy a fájlokat a világ bármely pontjáról elérheti a rájuk mutató URL-címekkel, amelyek közös hozzáférésű jogosultságkód- (SAS-) tokenekkel rendelkeznek. SAS-tokeneket Ön is létrehozhat: ezek határozott ideig biztosítanak egyedi hozzáférést a privát objektumokhoz.

A fájlmegosztások számos gyakori forgatókönyvhöz használhatók:

* Számos helyszíni alkalmazás használ fájlmegosztásokat. Ezzel a szolgáltatással könnyebb migrálni azokat az alkalmazásokat, amelyek az Azure-ban osztják meg az adatokat. Ha a fájlmegosztást ugyanarra a meghajtóhelyre csatlakoztatja, amelyet a helyszíni alkalmazás használ, az alkalmazás fájlmegosztáshoz kapcsolódó része minimális vagy semennyi módosítással használható.

* A konfigurációs fájlok fájlmegosztásokon tárolhatók, és több virtuális gépről is elérhetők. A több fejlesztő által egy csoportban használt eszközök és segédprogramok is tárolhatók fájlmegosztásokon, így mindenki elérheti őket, és mindenki ugyanazt a verziót használhatja.

* A diagnosztikai naplók, metrikák és összeomlási memóriaképek is a fájlmegosztásra írható, és később onnan feldolgozható és elemezhető adattípusok közé tartoznak.

Jelenleg az Active Directory-alapú hitelesítés és a hozzáférés-vezérlési listák (ACL-ek) még nem támogatottak, azonban a későbbiekben ez is megvalósul majd. A fájlmegosztáshoz való hozzáférés a tárfiók hitelesítő adataival hitelesíthető. Ez azt jelenti, hogy bárki, akinél a fájlmegosztás csatlakoztatva van, teljes írási/olvasási jogosultsággal rendelkezik rajta.

További információ az Azure Filesról: [Bevezetés az Azure Files használatába](../files/storage-files-introduction.md).

## <a name="queue-storage"></a>Queue Storage

Az Azure Queue szolgáltatás üzenetek tárolására és lehívására használható. Az üzenetsor üzenetei egyenként legfeljebb 64 KB méretűek lehetnek, és az üzenetsor akár több millió üzenetet is tartalmazhat. Az üzenetsorok általában aszinkron feldolgozásra szánt üzenetek listáit tárolják.

Tegyük fel például, hogy biztosítani szeretné az ügyfelei számára, hogy képeket tölthessenek fel, az egyes képekhez pedig miniatűröket szeretne létrehozni. Választhatná azt, hogy az ügyfeleknek a feltöltéskor várniuk kelljen a miniatűrök létrehozására. Alternatív megoldásként azonban használhat üzenetsort is. Amint az ügyfél befejezte a feltöltést, írjon egy üzenetet az üzenetsorba. Ezután egy Azure-függvénnyel hívja le az üzenetet az üzenetsorból, és hozza létre a miniatűrt. Ennek az eljárásnak minden egyes része külön méretezhető, és így szabadabban hangolhatja a felhasználási céloknak megfelelően.

További információ az Azure Queuesról: [Bevezetés az Azure Queues használatába](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Table Storage

Az Azure Table Storage mostantól az Azure Cosmos DB része. Az Azure Table Storage dokumentációját lásd: [Az Azure Table Storage áttekintése](../tables/table-storage-overview.md). A meglévő Azure Table Storage-szolgáltatáson kívül elérhető egy új Azure Cosmos DB Table API-ajánlat, amely teljesítményoptimalizált táblákat, globális elosztást és automatikus másodlagos indexeket is biztosít. Ha további információra van szüksége, vagy szeretné kipróbálni az új prémium ajánlatot, tekintse meg az [Azure Cosmos DB Table API](https://aka.ms/premiumtables)-t ismertető cikket.

További információ a Table Storage-ról: [Az Azure Table Storage áttekintése](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Lemezes tárolás

Az Azure Storage emellett a virtuális gépek által használt felügyelt és nem felügyelt lemez képességeket is tartalmazza. További információkért ezekkel a szolgáltatásokkal kapcsolatban lásd a [Számítási szolgáltatások dokumentációját](https://docs.microsoft.com/azure/#pivot=products&panel=Compute).

## <a name="types-of-storage-accounts"></a>A tárfiókok típusai

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

Storage-fióktípusok kapcsolatos további információkért lásd: [az Azure storage-fiók áttekintése](storage-account-overview.md). 

## <a name="securing-access-to-storage-accounts"></a>Storage-fiókokhoz való hozzáférés biztonságossá tétele

Az Azure Storage minden kérelmet kell engedélyezni. Az Azure Storage a következő hitelesítési módszereket támogatja:

- **Az Azure Active Directory (Azure AD) integrációs blob és üzenetsor adatok.** Az Azure Storage a Blob és üzenetsor szolgáltatások a szerepköralapú hozzáférés-vezérlés (RBAC) keresztül támogatja a hitelesítés és engedélyezés az Azure AD-beli hitelesítő. Az Azure AD-kérések engedélyezése kiemelkedő biztonság és a könnyű használat használata ajánlott. További információkért lásd: [hitelesítés hozzáférés az Azure-blobok és üzenetsorok az Azure Active Directoryval](storage-auth-aad.md).
- **Az Azure AD engedélyezési SMB-n keresztül az Azure Files (előzetes verzió).** Az Azure Files SMB (Server Message Block) keresztül az Azure Active Directory Domain Services identitás-alapú hitelesítést támogatja. A tartományhoz csatlakoztatott Windows virtuális gépek (VM) férhetnek hozzá az Azure-fájlmegosztásokat az Azure AD hitelesítő adatait. További információkért lásd: [áttekintése az Azure Active Directory hitelesítési SMB-n keresztül az Azure Files (előzetes verzió)](../files/storage-files-active-directory-overview.md).
- **A megosztott kulcs engedélyezési.** Az Azure Storage-Blob, Queue és Table szolgáltatások és az Azure Files hitelesítés támogatásához a megosztott Key.A ügyfél engedélyezési fejlécet minden kérelemnél, amely a tárfiók hozzáférési kulcsát használatával van aláírva adja át a megosztott kulcs használatával. További információkért lásd: [a megosztott kulcsos hitelesítés](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key).
- **A közös hozzáférésű jogosultságkódot (SAS) engedély használatával.** Közös hozzáférésű jogosultságkód (SAS) egy olyan tárolási erőforrás URI-ra hozzáfűzhető egy biztonsági jogkivonatot tartalmazó karakterlánc. A biztonsági jogkivonat például engedélyek vagy a időköz hozzáférési korlátozásokat foglalja magában. További információkért tekintse meg [használata közös hozzáférésű Jogosultságkódok (SAS)](storage-dotnet-shared-access-signature-part-1.md).
- **Tárolókhoz és blobokhoz való névtelen hozzáférés.** A blobok és a egy tároló lehet nyilvánosan elérhető. Ha megadja, hogy egy tárolót vagy blobot nyilvános, bárki névtelenül, a hitelesítés nem kötelező. További információk: [Manage anonymous read access to containers and blobs](../blobs/storage-manage-access-to-resources.md) (Tárolók és blobok névtelen olvasási hozzáférésének kezelése).

## <a name="encryption"></a>Titkosítás

A Storage-szolgáltatásokban két alapszintű titkosítási módszer áll rendelkezésre. A biztonsággal és a titkosítással kapcsolatos további információkért lásd az [Azure Storage biztonsági útmutatóját](storage-security-guide.md).

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

Az Azure Storage Service Encryption (SSE) segít az adatok biztonságos megőrzésében inaktív állapotban a vállalat által előírt biztonsági és megfelelőségi követelmények kielégítése érdekében. Ezzel a funkcióval az Azure Storage automatikusan titkosítja az adatokat a tárolás előtt, és visszafejti őket a lekérés előtt. A titkosítás, visszafejtés, és kulcskezelés teljes mértékben átlátható a felhasználók számára.


Az SSE automatikusan titkosítja minden teljesítményszint (Standard és Prémium), minden üzembehelyezési modell (Azure Resource Manager és klasszikus) és minden Azure Storage-szolgáltatás (Blob, Queue, Table és File) adatait. Az SSE nem befolyásolja az Azure Storage teljesítményét.

Az inaktív adatok SSE-titkosításával kapcsolatos további információkért látogasson el ide: [Azure Storage szolgáltatás inaktívadat-titkosítása](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Ügyféloldali titkosítás

A Storage ügyfélkódtárai tartalmaznak olyan metódusokat, amelyek meghívásával az adatok programozott módon titkosíthatóak, mielőtt az ügyfélből az Azure-ba kerülnének. Az adatok tárolása titkosítva történik, tehát inaktív állapotban is titkosítva vannak. Az adatok lehívásakor azok visszafejtése a megérkezésük után történik.

Az ügyféloldali titkosítással kapcsolatos további információkért látogasson el ide: [Ügyféloldali titkosítás a .NET for Microsoft Azure Storage szolgáltatással](storage-client-side-encryption.md).

## <a name="redundancy"></a>Redundancia

Az adatok tartósságának biztosítása érdekében az Azure Storage több példányban replikálja az adatokat. A tárfiók beállításakor válassza a redundanciát. 

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Vész-helyreállítási kapcsolatos további információkért lásd: [katasztrófa utáni helyreállítás és a tárolási fiók feladatátvételi (előzetes verzió) az Azure Storage-ban](storage-disaster-recovery-guidance.md).

## <a name="transferring-data-to-and-from-azure-storage"></a>Adatok áthelyezése az Azure Storage-ba és az Azure Storage-ból

Adatok áthelyezése vagy onnan máshová az Azure Storage számos lehetősége van. Lehetőséget választja, az adatkészlet és a hálózati sávszélesség méretétől függ. További információkért lásd: [válasszon egy Azure-megoldás az adatátvitelhez](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Díjszabás

Az Azure Storage díjszabásával kapcsolatos részletes információkért lásd az [Árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>Storage API-k, kódtárak és eszközök
Az Azure Storage-erőforrások bármely olyan nyelvvel hozzáférhetők, amelyekkel HTTP/HTTPS kérelmek indíthatók. Ezenfelül az Azure Storage számos népszerű nyelvhez biztosít programozási kódtárakat. Ezek a kódtárak sok szempontból leegyszerűsítik az Azure Storage használatát, mivel számos részletet kezelnek (például a szinkron és aszinkron hívás, műveletek kötegelése, kivételek kezelése, automatikus újrapróbálkozások, működési viselkedés stb.). Jelenleg a következő nyelvekhez és platformokhoz érhetők el kódtárak (a továbbiak összeállítása folyamatban):

### <a name="azure-storage-data-api-and-library-references"></a>Az Azure Storage adat API-ja és kódtárhivatkozásai
* [A Storage szolgáltatások REST API-ja](https://docs.microsoft.com/rest/api/storageservices/)
* [A Storage ügyféloldali kódtára a .NET-hez](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
* [A Storage ügyféloldali kódtára a Javához/Androidhoz](https://docs.microsoft.com/java/api/overview/azure/storage)
* [A Storage ügyféloldali kódtára a Node.js-hez](https://docs.microsoft.com/javascript/api/azure-storage)
* [A Storage ügyféloldali kódtára a Pythonhoz](https://github.com/Azure/azure-storage-python)
* [A Storage ügyféloldali kódtára a PHP-hez](https://github.com/Azure/azure-storage-php)
* [A Storage ügyféloldali kódtára a Rubyhoz](https://github.com/Azure/azure-storage-ruby)
* [A Storage ügyféloldali kódtára a C++ programnyelvhez](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Az Azure Storage felügyeleti API-ja és kódtárhivatkozásai
* [A Storage erőforrás-szolgáltató REST API-ja](https://docs.microsoft.com/rest/api/storagerp/)
* [Storage erőforrás-szolgáltató ügyfél a .NET-hez](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
* [A Storage szolgáltatásfelügyelet REST API-ja](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Az Azure Storage adatátviteli API-ja és kódtárhivatkozásai
* [A Storage Import/Export szolgáltatás REST API-ja](https://docs.microsoft.com/rest/api/storageimportexport/)
* [A Storage adatátviteli ügyféloldali kódtára a .NET-hez](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.datamovement)

### <a name="tools-and-utilities"></a>Eszközök és segédprogramok
* [Azure PowerShell-parancsmagok a Storage-hoz](https://docs.microsoft.com/powershell/module/az.storage)
* [Azure CLI-parancsmagok a Storage-hoz](https://docs.microsoft.com/cli/azure/storage)
* [AzCopy parancssori segédprogram](https://aka.ms/downloadazcopy)
* Az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) a Microsoft ingyenes, önálló alkalmazása, amellyel vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
* [Azure Storage-ügyféleszközök](../storage-explorers.md)
* [Azure fejlesztői eszközök](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>További lépések

Az Azure Storage gyors üzembe helyezéséhez lásd a [tárfiók létrehozásával](storage-quickstart-create-account.md) foglalkozó témakört.
