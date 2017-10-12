---
title: "Bevezetés az Azure Storage használatába | Microsoft Docs"
description: "Bevezetés az Azure Storage, a Microsoft felhőalapú adattároló szolgáltatásának használatába."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: a4a1bc58-ea14-4bf5-b040-f85114edc1f1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/09/2017
ms.author: tamram
ms.openlocfilehash: e7b32aa2de5d6501e8d7894a936e9ab8b2f4f42f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-microsoft-azure-storage"></a>A Microsoft Azure Storage bemutatása

A Microsoft Azure Storage egy, a Microsoft által felügyelt felhőszolgáltatás, amely magas rendelkezésre állású, biztonságos, tartós, méretezhető és redundáns tárolást tesz lehetővé. A karbantartást és a kritikus problémák kezelését a Microsoft végzi el Önnek. 

Az Azure Storage a következő három adatszolgáltatást tartalmazza: Blob Storage, File Storage és Queue Storage. A Blob Storage standard és prémium szintű tárolást is kínál – a prémium tárolás kizárólag SSD meghajtókon történik a lehető leggyorsabb teljesítmény biztosítása érdekében. Egy másik szolgáltatás a ritkán használt adatok tárolása, amelynek keretében az ilyen adatok nagy mennyiségben tárolhatók alacsony költségek mellett.

Ebben a cikkben megismerkedhet a következőkkel:
* az Azure Storage szolgáltatásai,
* a tárfiókok típusai,
* a blobokhoz, üzenetsorokhoz és fájlokhoz való hozzáférés módjai,
* titkosítás
* replikáció, 
* az adatok a tárolóba vagy a tárolóból való áthelyezése,
* valamint a számos elérhető Storage ügyfélkódtár. 

Az Azure Storage gyors üzembe helyezésével kapcsolatban tekintse meg az alábbi rövid útmutatókat:
* [Storage-fiók létrehozása a PowerShell-lel](storage-quickstart-create-storage-account-powershell.md)
* [Storage-fiók létrehozása a CLI-vel](storage-quickstart-create-storage-account-cli.md)

## <a name="introducing-the-azure-storage-services"></a>Az Azure Storage szolgáltatásainak bemutatása

Az Azure Storage által biztosított szolgáltatások – Blob Storage, File Storage és Queue Storage – használatához először létre kell hoznia egy tárfiókot, majd ezután azon a fiókon belül viheti át az adatait egy adott szolgáltatásba, illetve a szolgáltatásból máshová. 

## <a name="blob-storage"></a>Blob Storage

A blobok lényegében ugyanolyan fájlok, mint amilyeneket a számítógépén (vagy táblagépén, mobileszközén stb.) is tárol. Lehetnek képek, Microsoft Excel-fájlok, HTML-fájlok, virtuális merevlemezek (VHD-k), big data (például naplók), vagy adatbázisok biztonsági másolatai – lényegében szinte bármi. A blobok tárolása tárolókban történik, amelyek a mappákhoz hasonlatosak. 

A fájlok Blob Storage tárolóban való elhelyezése után a világ bármely pontjáról elérheti azokat URL-címek, a REST-felület vagy az Azure SDK-tároló ügyfélkódtárai használatával. A tárolók ügyfélkódtárai több nyelvhez, köztük a Node.js, a Java, a PHP, a Ruby, a Python és a .NET nyelvekhez is elérhetők. 

A bloboknak három típusa létezik: a blokkblobok, a lapblobok (ezek a VHD-fájlokhoz használatosak) és a hozzáfűző blobok.

* A blokkblobokban hagyományos fájlok tárolhatók legfeljebb nagyjából 4,7 TB méretig. 
* A lapblobokban közvetlen elérésű fájlok tárolhatók legfeljebb 8 TB méretig. Ezek a virtuális gépek alapját képező VHD-fájlokhoz használatosak.
* A hozzáfűző blobok a blokkblobokhoz hasonlóan blokkokból állnak, azonban a hozzáfűzési műveletekhez vannak optimalizálva. Ezek például több virtuális gép adatainak egyazon blobba történő naplózására használhatók.

A nagy adatkészleteknél, ahol a hálózati korlátok miatt irreális lenne az adatok vezetékes le- vagy feltöltése a Blob Storage-ba, elküldhet a Microsoftnak egy teljes merevlemez-készletet, hogy az adatokat közvetlenül az adatközpontból importálják vagy oda exportálják. Lásd: [Use the Microsoft Azure Import/Export Service to Transfer Data to Blob Storage](../storage-import-export-service.md) (A Microsoft Azure Import/Export szolgáltatás használata az adatok átviteléhez a Blob Storage-ba).

## <a name="azure-files"></a>Azure Files
Az [Azure Files](../files/storage-files-introduction.md) segítségével magas rendelkezésre állású hálózati fájlmegosztásokat hozhat létre, amelyek az SMB protokollon keresztül érhetőek el. Ez azt jelenti hogy ugyanazokhoz a fájlokhoz több virtuális gép is hozzáférhet olvasási és írási jogosultsággal. A fájlokat a REST-felület vagy a Storage klienskódtáraival is olvashatja. 

Az egyik dolog, ami megkülönbözteti az Azure Files szolgáltatást a fájlok a vállalati fájlmegosztásokon való tárolásától, hogy a fájlokat a világ bármely pontjáról elérheti a rájuk mutató URL-címekkel, amelyek közös hozzáférésű jogosultságkód- (SAS-) tokenekkel rendelkeznek. SAS-tokeneket Ön is létrehozhat: ezek határozott ideig biztosítanak egyedi hozzáférést a privát objektumokhoz. 

A fájlmegosztások számos gyakori forgatókönyvhöz használhatók: 

* Számos helyszíni alkalmazás használ fájlmegosztásokat. Ezzel a szolgáltatással könnyebb migrálni azokat az alkalmazásokat, amelyek az Azure-ban osztják meg az adatokat. Ha a fájlmegosztást ugyanarra a meghajtóhelyre csatlakoztatja, amelyet a helyszíni alkalmazás használ, az alkalmazás fájlmegosztáshoz kapcsolódó része minimális vagy semennyi módosítással használható.

* A konfigurációs fájlok fájlmegosztásokon tárolhatók, és több virtuális gépről is elérhetők. A több fejlesztő által egy csoportban használt eszközök és segédprogramok is tárolhatók fájlmegosztásokon, így mindenki elérheti őket, és mindenki ugyanazt a verziót használhatja.

* A diagnosztikai naplók, metrikák és összeomlási memóriaképek is a fájlmegosztásra írható, és később onnan feldolgozható és elemezhető adattípusok közé tartoznak.

Jelenleg az Active Directory-alapú hitelesítés és a hozzáférés-vezérlési listák (ACL-ek) még nem támogatottak, azonban a későbbiekben ez is megvalósul majd. A fájlmegosztáshoz való hozzáférés a tárfiók hitelesítő adataival hitelesíthető. Ez azt jelenti, hogy bárki, akinél a fájlmegosztás csatlakoztatva van, teljes írási/olvasási jogosultsággal rendelkezik rajta.

## <a name="queue-storage"></a>Queue Storage

Az Azure Queue szolgáltatás üzenetek tárolására és lehívására használható. Az üzenetsor üzenetei egyenként legfeljebb 64 KB méretűek lehetnek, és az üzenetsor akár több millió üzenetet is tartalmazhat. Az üzenetsorok általában aszinkron feldolgozásra szánt üzenetek listáit tárolják. 

Tegyük fel például, hogy biztosítani szeretné az ügyfelei számára, hogy képeket tölthessenek fel, az egyes képekhez pedig miniatűröket szeretne létrehozni. Választhatná azt, hogy az ügyfeleknek a feltöltéskor várniuk kelljen a miniatűrök létrehozására. Alternatív megoldásként azonban használhat üzenetsort is. Amint az ügyfél befejezte a feltöltést, írjon egy üzenetet az üzenetsorba. Ezután egy Azure-függvénnyel hívja le az üzenetet az üzenetsorból, és hozza létre a miniatűrt. Ennek az eljárásnak minden egyes része külön méretezhető, és így szabadabban hangolhatja a felhasználási céloknak megfelelően.

## <a name="table-storage"></a>Table Storage

A standard Azure Table Storage mostantól a Cosmos DB része. A dokumentációt lásd: [Az Azure Table Storage áttekintése](../../cosmos-db/table-storage-overview.md). Emellett az Azure Table Storage prémium táblákra vonatkozó ajánlata is elérhető, teljesítményoptimalizált táblákkal, globális elosztással és automatikus másodlagos indexekkel. Ha további információra van szüksége, vagy szeretné kipróbálni az új prémium ajánlatot, tekintse meg az [Azure Cosmos DB: tábla API](https://aka.ms/premiumtables)-t ismertető cikket.

## <a name="disk-storage"></a>Lemezes tárolás

Az Azure Storage emellett a virtuális gépek által használt felügyelt és nem felügyelt lemez képességeket is tartalmazza. További információkért ezekkel a szolgáltatásokkal kapcsolatban lásd a [Számítási szolgáltatások dokumentációját](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).

## <a name="types-of-storage-accounts"></a>A tárfiókok típusai 

Ez a táblázat a tárfiókok típusait és az egyes típusokkal használható objektumokat mutatja be.

|**Tárfiók típusa**|**Általános célú standard**|**Általános célú prémium**|**Blob Storage, a gyakran és a ritkán használt adatok tárolási rétegei**|
|-----|-----|-----|-----|
|**Támogatott szolgáltatások**| Blob, File és Queue szolgáltatások | Blob szolgáltatás | Blob szolgáltatás|
|**Támogatott blobtípusok**|Blokkblobok, lapblobok és hozzáfűző blobok | Lapblobok | Blokkblobok és hozzáfűző blobok|

### <a name="general-purpose-storage-accounts"></a>Általános célú tárfiókok

Kétféle általános célú tárfiók létezik. 

#### <a name="standard-storage"></a>Standard szintű Storage 

A legszélesebb körben használt tárfiók a standard tárfiók, amely bármilyen típusú adathoz használható. A standard szintű tárfiókok az adatokat mágneses adathordozókon tárolják.

#### <a name="premium-storage"></a>Prémium szintű Storage

A prémium szintű tárfiók nagy teljesítményű tárolást biztosít a lapblobokhoz, amelyek elsősorban VHD-fájlokhoz használatosak. A prémium szintű tárfiókok az adatokat SSD meghajtókon tárolják. A Microsoft minden virtuális gépéhez a prémium szintű Storage szolgáltatást ajánlja.

### <a name="blob-storage-accounts"></a>Blob Storage-fiókok

A Blob Storage-fiók egy specializált tárfiók blokkblobok és hozzáfűző blobok tárolásához. Az ilyen fiókokban nem lehet lapblobokat tárolni, így VHD-fájlokat sem. Ezekben a fiókokban beállíthatja a hozzáférési szintet (gyakran vagy ritkán használt adatok). Ez a szint bármikor módosítható. 

A gyakran használt adatok hozzáférési szintje a gyakran elérni kívánt adatok tárolására szolgál – a tárolási költségek magasabbak, a blobok hozzáférési költsége azonban sokkal alacsonyabb. A ritkán használt adatok hozzáférési szintjén tárolt blobok hozzáférési költsége magasabb, a tárolási költség azonban sokkal alacsonyabb.

## <a name="accessing-your-blobs-files-and-queues"></a>A blobok, fájlok és üzenetsorok elérése

Mindegyik tárfiók két hitelesítési kulccsal rendelkezik, amely bármelyike használható bármilyen művelethez. Mivel két kulcs van, időről-időre leválthatja ezeket, így növelhető a biztonság. A kulcsok biztonságba helyezése kritikus fontosságú, mivel a fiók nevével együtt korlátlan hozzáférést biztosítanak a tárfiókban tárolt összes adathoz. 

Ebben a szakaszban két módját ismertetjük a Storage-fiók és a benne lévő adatok védelmének. A tárfiók és a benne lévő adatok védelmével kapcsolatos további információkért lásd az [Azure Storage biztonsági útmutatóját](storage-security-guide.md).

### <a name="securing-access-to-storage-accounts-using-azure-ad"></a>A tárfiókokhoz való hozzáférés biztonságossá tétele az Azure AD használatával

A tároló adataihoz való hozzáférés biztonságossá tételének egyik módja a tárfiók kulcsaihoz való hozzáférés szabályozása. A Resource Manager szerepköralapú hozzáférés-vezérlésének (RBAC) használatával szerepköröket rendelhet felhasználókhoz, csoportokhoz és alkalmazásokhoz. Ezek a szerepkörök engedélyezett vagy nem engedélyezett műveletek csoportjaihoz vannak rendelve. Ha az RBAC használatával ad hozzáférést egy tárfiókhoz, azzal csak a tárfiók felügyeleti műveleteit, például a hozzáférési szint módosítását engedélyezi. Az RBAC használatával nem adhat hozzáférést az adatobjektumokhoz, például az egyes tárolókhoz vagy fájlmegosztásokhoz. Mindazonáltal, az RBAC használatával hozzáférést adhat a tárfiók kulcsaihoz, amelyek lehetővé teszik az adatobjektumok olvasását. 

### <a name="securing-access-using-shared-access-signatures"></a>Hozzáférés biztonságossá tétele közös hozzáférésű jogosultságkódok használatával 

A közös hozzáférésű jogosultságkódok és a tárolt hozzáférési szabályzatok használatával gondoskodhat az adatobjektumok védelméről. A közös hozzáférésű jogosultságkód (SAS) egy biztonsági jogkivonatot tartalmazó karakterlánc, amelyet egy adategység URI azonosítójához csatolva hozzáférés adható adott tárolóobjektumokhoz, valamint korlátozások határozhatók meg, például engedélyek vagy a hozzáférés dátum-/időtartománya. Ez a szolgáltatás kiterjedt képességeket biztosít. Részletes információkért lásd: [Using Shared Access Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md) (Közös hozzáférésű jogosultságkódok (SAS) használata).

### <a name="public-access-to-blobs"></a>Nyilvános hozzáférés a blobokhoz

A Blob szolgáltatás segítségével nyilvános hozzáférést adhat a tárolókhoz és annak blobjaihoz, vagy egy adott blobhoz. Ha egy tárolót vagy blobot nyilvánosként jelöl meg, bárki névtelenül, hitelesítés nélkül megtekintheti. Például akkor lehet érdemes ezt alkalmaznia, ha a webhelyén blobtárolóban lévő képeket, videókat vagy dokumentumokat használ. További információk: [Manage anonymous read access to containers and blobs](../blobs/storage-manage-access-to-resources.md) (Tárolók és blobok névtelen olvasási hozzáférésének kezelése). 

## <a name="encryption"></a>Titkosítás

A Storage-szolgáltatásokban több alapszintű titkosítási módszer áll rendelkezésre. 

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban 

A Storage Service Encryption (SSE) az Azure tárfiókhoz tartozó Files szolgáltatáson (előzetes verzió) vagy Blob szolgáltatáson engedélyezhető. Engedélyezésével az adott szolgáltatásba írt összes adat titkosítva lesz az írást megelőzően. Az adatok olvasásakor a rendszer visszafejti az adatokat, mielőtt visszaadná azokat. 

### <a name="client-side-encryption"></a>Ügyféloldali titkosítás

A Storage ügyfélkódtárai tartalmaznak olyan metódusokat, amelyek meghívásával az adatok programozott módon titkosíthatóak, mielőtt az ügyfélből az Azure-ba kerülnének. Az adatok tárolása titkosítva történik, tehát inaktív állapotban is titkosítva vannak. Az adatok lehívásakor azok visszafejtése a megérkezésük után történik. 

### <a name="encryption-in-transit-with-azure-file-shares"></a>Titkosítás az átvitel során az Azure-fájlmegosztásokkal

További információ a közös hozzáférésű jogosultságkódokról: [Using Shared Access Signatures (SAS)](../storage-dotnet-shared-access-signature-part-1.md) (Közös hozzáférésű jogosultságkódok (SAS) használata). További információ a tárfiók biztonságos hozzáféréséről: [Manage anonymous read access to containers and blobs](../blobs/storage-manage-access-to-resources.md) (Tárolók és blobok névtelen olvasási hozzáférésének kezelése) és [Az Azure Storage szolgáltatásainak hitelesítése](https://msdn.microsoft.com/library/azure/dd179428.aspx).

A Storage-fiók védelmével és a titkosítással kapcsolatos további információkért lásd az [Azure Storage biztonsági útmutatóját](storage-security-guide.md).

## <a name="replication"></a>Replikáció

Az adatok tartósságának biztosítása érdekében az Azure Storage egyszerre több példányban képes tárolni (és kezelni) az adatokat. Ezt replikációnak vagy más néven redundanciának nevezzük. A Storage-fiók üzembe helyezésekor meg kell választania a replikáció típusát. A legtöbb esetben ez a beállítás a tárfiók üzembe helyezését követően módosítható. 

Mindegyik tárfiókban elérhető a **helyileg redundáns tárolás (LRS)**. Ez azt jelenti, hogy az Azure Storage három példányban tárolja az adatokat a tárfiók beállításakor megadott adatközpontban. Az adatok egyik példányának módosításakor a rendszer frissíti a másik két példányt is, mielőtt sikeres eredményt adna vissza. Ez azt jelenti, hogy a három replika mindig szinkronban van. Emellett a rendszer a három példányt külön tartalék tartományokban és frissítési tartományokban tárolja, aminek következtében az adatok akkor is elérhetők maradnak, ha az azokat tároló valamelyik tárolócsomópont meghibásodik, vagy frissítés következtében offline állapotba kerül. 

**Helyileg redundáns tárolás (LRS)**

Amint azt fent kifejtettük, az LRS esetében az adatok három példányban vannak tárolva egyetlen adatközpontban. Így az adatok akkor is elérhetőek maradnak, ha egy tárolócsomópont meghibásodik vagy frissítés következtében offline állapotba kerül, akkor viszont nem, ha a teljes adatközpont elérhetetlenné válik.

**Zónaredundáns tárolás (ZRS)**

A zónaredundáns tárolás (ZRS) is három helyi példányt őriz az adatokból, azonban emellett egy másik hárompéldányos készlet is rendelkezésre áll. A második három példányból álló készlet aszinkron módon van replikálva egy vagy két másik régióban lévő adatközpontokba. Megjegyzendő, hogy a ZRS kizárólag általános célú tárfiókokban lévő blokkblobokhoz érhető el. Továbbá, miután létrehozott egy tárfiókot és kiválasztotta a ZRS-t, már nem módosíthatja a replikáció típusát más típusra, és ugyanez fordítva is igaz.

A ZRS-fiókok nagyobb tartósságot biztosítanak az LRS-fiókoknál, azonban nem rendelkeznek metrikákkal vagy naplózási funkciókkal. 

**Georedundáns tárolás (GRS)**

A georedundáns tárolási (GRS) szolgáltatás is három helyi példányt őriz az adatokból egy elsődleges régióban, valamint emellett egy másik hárompéldányos készletet egy másodlagos régióban több száz kilométerre az elsődleges régiótól. Ha az elsődleges régióban hiba történne, az Azure Storage feladatátvételt hajt végre a másodlagos régióba. 

**Írásvédett georedundáns tárolás (RA-GRS)** 

Az írásvédett georedundáns tárolás megegyezik a GRS tárolással, annyi különbséggel, hogy itt olvasási jogokat kap a másodlagos helyen lévő adatokhoz. Ha az elsődleges adatközpont ideiglenesen elérhetetlenné válna, továbbra is olvashatja az adatokat a másodlagos helyről. Ezt rendkívül hasznos lehet. Például lehet egy olyan webalkalmazása, amely ilyenkor írásvédett módba vált, és a másodlagos példányra mutat, így valamilyen szinten továbbra is hozzáférést biztosít, még ha a frissítsek nem is érhetők el. 

> [!IMPORTANT]
> A tárfiók létrehozása után módosíthatja az adatok replikálási módját, kivéve ha a fiók létrehozásakor a ZRS módot választotta. Azonban ügyeljen arra, hogy ha az  LRS-ről  GRS-re vagy RA- GRS-re vált, akkor lehetséges, hogy egyszeri adatátviteli díjat kell fizetnie.
>

A replikációval kapcsolatos további információk: [Azure Storage replication](storage-redundancy.md) (Az Azure Storage replikációja).

A vészhelyreállítással kapcsolatos információk: [What to do if an Azure Storage outage occurs](storage-disaster-recovery-guidance.md) (Mi a teendő az Azure Storage leállása esetén?).

A magas rendelkezésre állás az RA-GRS tároló kiaknázásával történő biztosításával kapcsolatos példáért lásd: [Designing Highly Available Applications using RA-GRS](storage-designing-ha-apps-with-ragrs.md) (Magas rendelkezésre állású alkalmazások tervezése az RA-GRS használatával).

## <a name="transferring-data-to-and-from-azure-storage"></a>Adatok áthelyezése az Azure Storage-ba és az Azure Storage-ból

Az AzCopy parancssori segédprogrammal blob- és fájladatokat másolhat egy tárfiókon belül vagy tárfiókok között. Segítségért lásd a következők cikkek valamelyikét:

* [Transfer data with AzCopy for Windows](storage-use-azcopy.md) (Adatok áthelyezése az AzCopy segédprogrammal Windows rendszeren)
* [Transfer data with AzCopy for Linux](storage-use-azcopy-linux.md) (Adatok áthelyezése az AzCopy segédprogrammal Linux rendszeren)

Az AzCopy az [Azure Adatátviteli könyvtárra](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/) épül, amelynek jelenleg az előzetes verziója érhető el.

Az Azure Import/Export szolgáltatás használatával nagy mennyiségben importálhat és exportálhat blobadatokat a tárfiókjába/tárfiókjából. Készítse elő és küldje el postán az adatokat tartalmazó merevlemezeket valamely Azure adatközpontnak, ahol az adatokat átmásolják a merevlemezekről/merevlemezekre, majd visszaküldik azokat az Ön számára. További információ az Import/Export szolgáltatásról: [Use the Microsoft Azure Import/Export Service to Transfer Data to Blob Storage](../storage-import-export-service.md) (A Microsoft Azure Import/Export szolgáltatás használata az adatok átviteléhez a Blob Storage-ba).

## <a name="pricing"></a>Díjszabás

Az Azure Storage díjszabásával kapcsolatos részletes információkért lásd az [Árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>Storage API-k, kódtárak és eszközök
Az Azure Storage-erőforrások bármely olyan nyelvvel hozzáférhetők, amelyekkel HTTP/HTTPS kérelmek indíthatók. Ezenfelül az Azure Storage számos népszerű nyelvhez biztosít programozási kódtárakat. Ezek a kódtárak sok szempontból leegyszerűsítik az Azure Storage használatát, mivel számos részletet kezelnek (például a szinkron és aszinkron hívás, műveletek kötegelése, kivételek kezelése, automatikus újrapróbálkozások, működési viselkedés stb.). Jelenleg a következő nyelvekhez és platformokhoz érhetők el kódtárak (a továbbiak összeállítása folyamatban):

### <a name="azure-storage-data-services"></a>Azure Storage-adatszolgáltatások
* [A Storage szolgáltatások REST API-ja](/rest/api/storageservices/)
* [A Storage ügyféloldali kódtára a .NET-hez](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [A Storage ügyféloldali kódtára a C++ programnyelvhez](https://github.com/Azure/azure-storage-cpp)
* [A Storage ügyféloldali kódtára a Javához/Androidhoz](https://azure.microsoft.com/develop/java/)
* [A Storage ügyféloldali kódtára a Node.js-hez](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [A Storage ügyféloldali kódtára a PHP-hez](https://azure.microsoft.com/develop/php/)
* [A Storage ügyféloldali kódtára a Pythonhoz](https://azure.microsoft.com/develop/python/)
* [A Storage ügyféloldali kódtára a Rubyhoz](https://azure.microsoft.com/develop/ruby/)
* [Storage-parancsmagok a PowerShellhez](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)
* [Storage-parancsok a CLI 2.0-hoz](/cli/azure/storage)

## <a name="next-steps"></a>Következő lépések

* [További tudnivalók a Blob Storage-ról](../blobs/storage-blobs-introduction.md)
* [További tudnivalók a File Storage-ról](../storage-files-introduction.md)
* [További tudnivalók a Queue Storage-ról](../queues/storage-queues-introduction.md)

Az Azure Storage gyors üzembe helyezésével kapcsolatban tekintse meg az alábbi rövid útmutatókat:
* [Storage-fiók létrehozása a PowerShell-lel](storage-quickstart-create-storage-account-powershell.md)
* [Storage-fiók létrehozása a CLI-vel](storage-quickstart-create-storage-account-cli.md)

<!-- FIGURE OUT WHAT TO DO WITH ALL THESE LINKS.

Azure Storage resources can be accessed by any language that can make HTTP/HTTPS requests. Additionally, Azure Storage offers programming libraries for several popular languages. These libraries simplify many aspects of working with Azure Storage by handling details such as synchronous and asynchronous invocation, batching of operations, exception management, automatic retries, operational behavior and so forth. Libraries are currently available for the following languages and platforms, with others in the pipeline:

### Azure Storage data services
* [Storage Services REST API](https://docs.microsoft.com/rest/api/storageservices/)
* [Storage Client Library for .NET](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp)
* [Storage Client Library for Java/Android](https://azure.microsoft.com/develop/java/)
* [Storage Client Library for Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Storage Client Library for PHP](https://azure.microsoft.com/develop/php/)
* [Storage Client Library for Python](https://azure.microsoft.com/develop/python/)
* [Storage Client Library for Ruby](https://azure.microsoft.com/develop/ruby/)
* [Storage Cmdlets for PowerShell](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)

### Azure Storage management services
* [Storage Resource Provider REST API Reference](/rest/api/storagerp/)
* [Storage Resource Provider Client Library for .NET](/dotnet/api/microsoft.azure.management.storage)
* [Storage Resource Provider Cmdlets for PowerShell 1.0](/powershell/module/azure.storage)
* [Storage Service Management REST API (Classic)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### Azure Storage data movement services
* [Storage Import/Export Service REST API](../storage-import-export-service.md)
* [Storage Data Movement Client Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### Tools and utilities
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) is a free, standalone app from Microsoft that enables you to work visually with Azure Storage data on Windows, macOS, and Linux.
* [Azure Storage Client Tools](../storage-explorers.md)
* [Azure SDKs and Tools](https://azure.microsoft.com/tools/)
* [Azure Storage Emulator](http://www.microsoft.com/download/details.aspx?id=43709)
* [Azure PowerShell](/powershell/azure/overview)
* [AzCopy Command-Line Utility](http://aka.ms/downloadazcopy)

## Next steps
To learn more about Azure Storage, explore these resources:

### Documentation
* [Azure Storage Documentation](https://azure.microsoft.com/documentation/services/storage/)
* [Create a storage account](../storage-create-storage-account.md)

-->

### <a name="for-administrators"></a>Rendszergazdáknak
* [Using Azure PowerShell with Azure Storage (Az Azure PowerShell és az Azure Storage együttes használata)](storage-powershell-guide-full.md)
* [Using the Azure CLI with Azure Storage (Az Azure CLI és az Azure Storage együttes használata)](../storage-azure-cli.md)

### <a name="for-net-developers"></a>.NET-fejlesztőknek
* [Az Azure Blob Storage használatának első lépései a .NET-keretrendszerrel](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Fejlesztés az Azure Files szolgáltatáshoz a .NET-keretrendszerrel](../files/storage-dotnet-how-to-use-files.md)
* [Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Az Azure Queue Storage használatának első lépései a .NET-keretrendszerrel](../storage-dotnet-how-to-use-queues.md)

### <a name="for-javaandroid-developers"></a>Java/Android-fejlesztőknek
* [How to use Blob storage from Java (A Blob Storage használata Javával)](../blobs/storage-java-how-to-use-blob-storage.md)
* [Fejlesztés az Azure Files szolgáltatáshoz Javával](../files/storage-java-how-to-use-file-storage.md)
* [How to use Table storage from Java (A Table Storage használata Javával)](../../cosmos-db/table-storage-how-to-use-java.md)
* [How to use Queue Storage from Java (A Queue Storage használata Javával)](../storage-java-how-to-use-queue-storage.md)

### <a name="for-nodejs-developers"></a>Node.js-fejlesztőknek
* [How to use Blob storage from Node.js (A Blob Storage használata Node.js-sel)](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [How to use Table storage from Node.js (A Table Storage használata Node.js-sel)](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [How to use Queue storage from Node.js (A Queue Storage használata Node.js-sel)](../storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>PHP-fejlesztőknek
* [How to use Blob storage from PHP (A Blob Storage használata PHP-val)](../blobs/storage-php-how-to-use-blobs.md)
* [How to use Table storage from PHP (A Table Storage használata PHP-val)](../../cosmos-db/table-storage-how-to-use-php.md)
* [How to use Queue storage from PHP (A Queue Storage használata PHP-val)](../storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>Ruby-fejlesztőknek
* [How to use Blob storage from Ruby (A Blob Storage használata Rubyval)](../blobs/storage-ruby-how-to-use-blob-storage.md)
* [How to use Table storage from Ruby (A Table Storage használata Rubyval)](../../cosmos-db/table-storage-how-to-use-ruby.md)
* [How to use Queue storage from Ruby (A Queue Storage használata Rubyval)](../storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Python-fejlesztőknek
* [How to use Blob storage from Pythonnal (A Blob Storage használata Pythonnal)](../blobs/storage-python-how-to-use-blob-storage.md)
* [Fejlesztés az Azure Files szolgáltatáshoz Pythonnal](../files/storage-python-how-to-use-file-storage.md)
* [How to use Table storage from Python (A Table Storage használata Pythonnal)](../../cosmos-db/table-storage-how-to-use-python.md)
* [How to use Queue storage from Python (A Queue Storage használata Pythonnal)](../storage-python-how-to-use-queue-storage.md)