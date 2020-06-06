---
title: Biztonságos, tartós, méretezhető alkalmazások létrehozása az Azure Storage-ban felhőalapú tárolással
description: Ismerkedjen meg a szolgáltatásokkal, amelyekkel nagyméretű strukturált és nem strukturált mobil alkalmazások adatait tárolhatja a felhőben.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 6f8ed02659f1720435df3ff98817233a5a46dbcb
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450989"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Felhőalapú tárolás biztonságos, tartós és méretezhető alkalmazások számára az Azure Storage szolgáltatással
Az [Azure Storage](https://azure.microsoft.com/services/storage/) a Microsoft felhőalapú tárolási megoldása a modern alkalmazásokhoz, amelyek nagymértékben méretezhető objektumokat tárolnak az adatobjektumokhoz, egy fájlrendszer-szolgáltatást a felhőhöz, egy üzenetküldési tárolót a megbízható üzenetküldéshez és egy NoSQL-tárolót. Az Azure Storage:
- **Tartós és magasan elérhető:** A redundancia biztosítja, hogy az adatai biztonságban legyenek az átmeneti hardverhiba esetén. Az adatközpontok vagy földrajzi helyek adatait replikálhatja is a helyi vagy természeti katasztrófák elleni további védelem érdekében. Az így replikált adatok magas rendelkezésre állásúak maradnak váratlan meghibásodás esetén is.
- **Biztonságos:** Az Azure Storage-ba írt összes adathalmazt a szolgáltatás titkosítja. Az Azure Storage használatával részletesen szabályozhatja, hogy ki férhet hozzá az adatokhoz.
- **Skálázható:** A szolgáltatások nagy mértékben méretezhetők, hogy megfeleljenek a mai alkalmazások adattárolási és teljesítménybeli igényeinek.
- **Felügyelt:** Az Azure kezeli a hardveres karbantartást, a frissítéseket és a kritikus problémákat.
- **Elérhető:** Az adatok a világon bárhonnan elérhetők HTTP-n vagy HTTPS-en keresztül. A Microsoft különböző nyelveken nyújt ügyféloldali kódtárakat, például a .NET, a Java, a Node. js, a Python, a PHP, a Ruby és a Go és egy érett REST API. A parancsfájlok Azure PowerShell vagy az Azure CLI-ben támogatottak. A Azure Portal és Azure Storage Explorer egyszerű vizuális megoldásokat kínál az adataival való munkához.

A következő szolgáltatások segítségével engedélyezheti a felhőalapú tárolást a Mobile apps szolgáltatásban.

## <a name="azure-blob-storage"></a>Azure Blob Storage
Az [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) egy objektum-tárolási megoldást kínál a felhőhöz. A blob Storage olyan nagy mennyiségű strukturálatlan adat tárolására van optimalizálva, amely nem felel meg egy adott adatmodellnek vagy definíciónak, például a szövegnek vagy a bináris fájlnak. Támogatja az ügyféloldali kódtárak által használt különböző nyelvek használatát. A blob Storage a következőhöz lett tervezve:
- Képek vagy dokumentumok közvetlen kiszolgálása egy böngészőben.
- Fájlok tárolása elosztott hozzáféréshez.
- Videó és hang továbbítása.
- Írás a naplófájlba.
- Tárolja a biztonsági mentést és visszaállítást, a vész-helyreállítást és az archiválást.
- Az adattárolást helyszíni vagy Azure által üzemeltetett szolgáltatás alapján elemezheti.

**Referencia**
- [Azure Portal](https://portal.azure.com)
- [Az Azure Blob Storage dokumentációja](/azure/storage/blobs/storage-blobs-introduction)
- [Rövid útmutatók](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Példák](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure Table Storage
Az [Azure Table Storage](https://azure.microsoft.com/services/storage/tables/) egy olyan szolgáltatás, amely strukturált NoSQL-adattárakat tárol a felhőben, és egy kulcs-vagy attribútum-tárolót biztosít a séma nélküli kialakításhoz. Az Azure Table Storage nagy mennyiségű strukturált adat tárolására alkalmas. A szolgáltatás egy NoSQL-adattár, amely az Azure-felhőn belüli és kívüli hitelesített hívásokat fogadja el. Az Azure Tables ideális a strukturált, nem kapcsolódó adatértékek tárolására. A Table Storage jellemzően a következőkre használható:
- A web-Scale alkalmazások kiszolgálására képes, terabájtos strukturált adatmennyiséget tárol.
- Olyan adatkészleteket tárolhat, amelyek nem igényelnek összetett illesztéseket, külső kulcsokat vagy tárolt eljárásokat, és a gyors hozzáférés érdekében denormalizálható.
- Az adatlekérdezés fürtözött index használatával gyorsan lekérdezhető.
- A OData protokoll és a LINQ-lekérdezések használatával férhet hozzá az Windows Communication Foundation (WCF) Data Services .NET-kódtárak szolgáltatáshoz.

A Table Storage használatával nagy mennyiségű strukturált, nem összehasonlítható adat tárolható és kérdezhető le. A táblák méretezése igény szerint növekszik.

**Referencia**
- [Azure Portal](https://portal.azure.com)
- [Az Azure Table Storage dokumentációja](/azure/storage/tables/table-storage-overview)
- [Példák](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Rövid útmutatók](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure Files
A [Azure Files](https://azure.microsoft.com/services/storage/files/)segítségével olyan, a szabványos SMB protokollal elérhető hálózati fájlmegosztás állítható be, amelyek elérhetők. Több virtuális gép is megoszthatja ugyanazt a fájlt olvasási és írási hozzáféréssel. A fájlokat a REST-felület vagy a Storage ügyféloldali kódtárak használatával is elolvashatja. A fájlokat a világ bármely pontjáról elérheti egy olyan URL-címmel, amely a fájlra mutat, és tartalmazza a közös hozzáférésű aláírás (SAS) tokenjét is. Létrehozhat SAS-jogkivonatokat. Egy adott időtartamra vonatkozóan lehetővé teszik a saját adategységhez való hozzáférést.

Az Azure-fájlmegosztások az alábbiakra használhatók:
- Helyszíni **fájlkiszolgálók cseréje vagy pótlása:** A népszerű operációs rendszerek, például a Windows, a macOS és a Linux közvetlenül csatlakoztathatók az Azure-fájlmegosztáshoz, bárhol is legyenek a világon. Az Azure-fájlmegosztások az Azure File Sync használatával replikálhatók helyszíni vagy felhőalapú Windows Serverekre, az adatok a használat helyéhez közeli nagy teljesítményű és elosztott gyorsítótárazása érdekében.
- **Alkalmazások átemelése és átirányítása:** Alkalmazások migrálása a felhőbe, amely egy fájlmegosztás vagy felhasználói adatok tárolására vár.
- A **felhőalapú fejlesztés egyszerűsítése:** Azure Files számos módon felhasználható az új felhőalapú fejlesztési projektek egyszerűsítésére is. Például:
    - **Megosztott alkalmazás beállításai:** Az elosztott alkalmazások közös mintája, hogy a konfigurációs fájlok központi helyen legyenek, ahol számos alkalmazás-példányból elérhetők. Az alkalmazás példányai a REST API fájlon keresztül tölthetik be a konfigurációt. A felhasználók szükség szerint hozzáférhetnek hozzájuk az SMB-megosztás helyi csatlakoztatásával.
    - **Diagnosztikai megosztás:** Az Azure-fájlmegosztás kényelmes hely a Felhőbeli alkalmazások számára a naplók, a mérőszámok és az összeomlási memóriaképek írásához. A naplókat az alkalmazás példányai a REST API fájl segítségével írhatják. A fejlesztők a fájlmegosztást a helyi gépen csatlakoztatva érhetik el. Ez a képesség nagy rugalmasságot tesz lehetővé. A fejlesztők a felhő fejlesztését anélkül vehetik figyelembe, hogy le kellene vonniuk a meglévő eszközöket.

**Referencia**
- [Azure Portal](https://portal.azure.com)
- [Az Azure Files dokumentációja](/azure/storage/files/storage-files-introduction)
- [Rövid útmutatók](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Azure Queue Storage
Az [Azure üzenetsor-tároló](https://azure.microsoft.com/services/storage/queues/) szolgáltatás nagy számú üzenet tárolására szolgál. A hitelesített hívásokkal HTTP vagy HTTPS használatával férhet hozzá a világ bármely pontján lévő üzenetekhez. Egy üzenetsor-üzenet akár 64 KB méretű is lehet. Egy üzenetsor akár több millió üzenetet is tartalmazhat, a Storage-fiók teljes kapacitásának korlátja alapján. A várólistákat általában arra használják, hogy egy várakozó munkafolyamatot hozzon létre aszinkron feldolgozásra.

**Referencia**
- [Azure Portal](https://portal.azure.com)
- [Azure Queue Storage-dokumentáció](/azure/storage/queues/)
- [Rövid útmutatók](/azure/storage/queues/storage-quickstart-queues-portal)
- [Példák](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
