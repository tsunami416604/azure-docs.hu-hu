---
title: Felhőalapú tárhely, amely rendkívül biztonságos, tartós, méretezhető alkalmazásokat hozhat létre az Azure Storage segítségével
description: Ismerje meg a nagy strukturált és nem strukturált mobilalkalmazás-adatok felhőben való tárolásához nyújtott szolgáltatásokat.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 682f06936480d9018d759b4df3bed80be1206faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240976"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Felhőalapú tárolás a rendkívül biztonságos, tartós, méretezhető alkalmazásokhoz az Azure Storage segítségével
[Az Azure Storage](https://azure.microsoft.com/services/storage/) a Microsoft felhőalapú tárolási megoldása a modern alkalmazások számára, amely masszívan méretezhető objektumtárolót kínál az adatobjektumokszámára, egy fájlrendszer-szolgáltatást a felhőhöz, egy üzenettárolót a megbízható üzenetküldéshez és egy NoSQL-tárolót. Az Azure Storage:
- **Tartós és magas rendelkezésre állású:** A redundancia biztosítja, hogy az adatok biztonságban legyenek átmeneti hardverhibák esetén. Az adatközpontok vagy földrajzi helyek adatait replikálhatja is a helyi vagy természeti katasztrófák elleni további védelem érdekében. Az így replikált adatok magas rendelkezésre állásúak maradnak váratlan meghibásodás esetén is.
- **Biztonságos:** Az Azure Storage-ba írt összes adatot a szolgáltatás titkosítja. Az Azure Storage használatával részletesen szabályozhatja, hogy ki férhet hozzá az adatokhoz.
- **Méretezhető:** A szolgáltatások úgy vannak kialakítva, hogy nagymértékben méretezhetők legyenek, hogy megfeleljenek a mai alkalmazások adattárolási és teljesítményigényeinek.
- **Kezelt:** Az Azure kezeli a hardverkarbantartást, a frissítéseket és a kritikus problémákat.
- **Elérhető:** Az adatok a világ bármely pontjáról elérhetők HTTP-n vagy HTTPS-en keresztül. A Microsoft ügyfélkönyvtárakat számos nyelven biztosít, például .NET, Java, Node.js, Python, PHP, Ruby és Go, valamint egy érett REST API-t. A parancsfájlok futtatását az Azure PowerShell vagy az Azure CLI támogatja. Az Azure Portal és az Azure Storage Explorer egyszerű vizuális megoldásokat kínál az adatokkal való munkához.

Az alábbi szolgáltatásokkal engedélyezheti a felhőalapú tárhelyet a mobilalkalmazásokban.

## <a name="azure-blob-storage"></a>Azure Blob Storage
[Az Azure Blob storage](https://azure.microsoft.com/services/storage/blobs/) objektumtárolási megoldást kínál a felhőhöz. A Blob storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva, amely nem felel meg egy adott adatmodellnek vagy definíciónak, például szövegnek vagy binárisnak. Támogatja a különböző nyelvek, hogy az ügyfél könyvtárak használata. A Blob storage célja a következők:
- Képeket vagy dokumentumokat szolgáljon ki közvetlenül a böngészőben.
- Fájlok tárolása az elosztott hozzáféréshez.
- Videó és hang streamelése.
- Írjon naplófájlokba.
- Adatok tárolása biztonsági mentéshez és visszaállításhoz, vészhelyreállításhoz és archiváláshoz.
- Adatok tárolása elemzésegy helyszíni vagy Az Azure által üzemeltetett szolgáltatás.

**Referencia**
- [Azure-portál](https://portal.azure.com)
- [Az Azure Blob Storage dokumentációja](/azure/storage/blobs/storage-blobs-introduction)
- [Rövid útmutatók](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Minták](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure Table Storage
[Az Azure Table storage](https://azure.microsoft.com/services/storage/tables/) egy olyan szolgáltatás, amely strukturált NoSQL-adatokat tárol a felhőben, és egy séma nélküli kialakítású kulcsot vagy attribútumtárolót biztosít. Az Azure Table Storage nagy mennyiségű strukturált adat tárolására alkalmas. A szolgáltatás egy NoSQL-adattár, amely fogadja a hitelesített hívásokat az Azure-felhőn belülről és kívülről. Az Azure-táblák ideálisak strukturált, nem kapcsolati adatok tárolására. A táblatárolás általában a következőkre szolgál:
- A webméretű alkalmazások kiszolgálására alkalmas strukturált adatok terabájtjainak tárolása.
- Olyan adatkészleteket tárolhat, amelyek nem igényelnek összetett illesztéseket, idegen kulcsokat vagy tárolt eljárásokat, és denormalizálhatók a gyors hozzáférés hez.
- Gyorsan lekérdezheti az adatokat fürtözött index használatával.
- Adatok elérése az OData protokoll és a LINQ-lekérdezések használatával a Windows Communication Foundation (WCF) Data Services .NET kódtárakkal.

A Table storage segítségével hatalmas strukturált, nem relációs adatokat tárolhat és kérdezhet le. A táblázatok az igény növekedésével méretezhetők.

**Referencia**
- [Azure-portál](https://portal.azure.com)
- [Az Azure Table Storage dokumentációja](/azure/storage/tables/table-storage-overview)
- [Minták](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Rövid útmutatók](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure Files
Az [Azure Files](https://azure.microsoft.com/services/storage/files/)segítségével magas rendelkezésre állású hálózati fájlmegosztásokat állíthat be, amelyek a szabványos Server Message Block (SMB) protokoll használatával érhetők el. Több virtuális gép is megoszthatja ugyanazokat a fájlokat olvasási és írási hozzáféréssel. A fájlokat a REST-felület vagy a tárolóügyfél-tárak használatával is elolvashatja. A fájlokat a világ bármely pontjáról elérheti egy OLYAN URL-cím használatával, amely a fájlra mutat, és tartalmaz egy SAS-jogkivonatot. SAS-jogkivonatok at hozhat létre. Meghatározott ideig lehetővé teszik a magáneszközhöz való konkrét hozzáférést.

Az Azure-fájlmegosztások az alábbiakra használhatók:
- **Helyszíni fájlkiszolgálók cseréje vagy kiegészítése:** A népszerű operációs rendszerek, például a Windows, a macOS és a Linux közvetlenül csatlakoztathatják az Azure fájlmegosztásokat, bárhol is legyenek a világon. Az Azure-fájlmegosztások az Azure File Sync használatával replikálhatók helyszíni vagy felhőalapú Windows Serverekre, az adatok a használat helyéhez közeli nagy teljesítményű és elosztott gyorsítótárazása érdekében.
- **Emelő- és műszakalkalmazások:** Telepítse át azokat az alkalmazásokat a felhőbe, amelyek fájlmegosztást várnak a fájlalkalmazások vagy a felhasználói adatok tárolására.
- **Egyszerűsítse a felhőfejlesztést:** Az Azure Files számos módon is használható az új felhőfejlesztési projektek egyszerűsítésére. Példa:
    - **Megosztott alkalmazásbeállítások:** Az elosztott alkalmazások gyakori mintája az, hogy a konfigurációs fájlok egy központi helyen, ahol számos alkalmazáspéldányból érhetők el. Az alkalmazáspéldányok betölthetik a konfigurációjukat a File REST API-n keresztül. A felhasználók szükség szerint elérhetik őket az SMB-megosztás helyi csatlakoztatásával.
    - **Diagnosztikai megosztás:** Az Azure-fájlmegosztás egy kényelmes hely a felhőalapú alkalmazások számára a naplók, metrikák és összeomlási memóriaképek írására. A naplókat az alkalmazáspéldányok a File REST API-n keresztül írhatják. A fejlesztők úgy érhetik el őket, hogy a fájlmegosztást a helyi számítógépükre szerelik. Ez a képesség nagy rugalmasságot tesz lehetővé. A fejlesztők anélkül is felkarolhatják a felhőfejlesztést, hogy el kellene hagyniuk az általuk ismert meglévő eszközökkel.

**Referencia**
- [Azure-portál](https://portal.azure.com)
- [Az Azure Files dokumentációja](/azure/storage/files/storage-files-introduction)
- [Rövid útmutatók](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Azure Queue Storage
[Az Azure Queue storage](https://azure.microsoft.com/services/storage/queues/) egy szolgáltatás nagy számú üzenetek tárolására. Az üzeneteket a világ bármely pontjáról elérheti hitelesített hívásokon keresztül HTTP vagy HTTPS használatával. A várólista-üzenet mérete legfeljebb 64 KB lehet. Egy várólista több millió üzenetet tartalmazhat, a tárfiók teljes kapacitáskorlátjáig. A várólistákat gyakran használják az aszinkron feldolgozáshoz használt munka hátralékának létrehozására.

**Referencia**
- [Azure-portál](https://portal.azure.com)
- [Azure Queue Storage-dokumentáció](/azure/storage/queues/)
- [Rövid útmutatók](/azure/storage/queues/storage-quickstart-queues-portal)
- [Minták](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
