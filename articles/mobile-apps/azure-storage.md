---
title: Biztonságos, tartós, méretezhető alkalmazások létrehozása az Azure Storage-ban felhőalapú tárolással
description: Ismerkedjen meg a szolgáltatásokkal, amelyekkel nagyméretű strukturált és nem strukturált mobil alkalmazások adatait tárolhatja a felhőben.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 01407f843ef36095fc87feb3722e85dc477ad8bb
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795652"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Felhőalapú tárolás biztonságos, tartós és méretezhető alkalmazások számára az Azure Storage szolgáltatással
Az [Azure Storage](https://azure.microsoft.com/services/storage/) a Microsoft felhőalapú tárolási megoldása a modern alkalmazásokhoz, amelyek nagymértékben méretezhető objektumokat tárolnak az adatobjektumokhoz, egy fájlrendszer-szolgáltatást a felhőhöz, egy üzenetküldési tárolót a megbízható üzenetküldéshez és egy NoSQL-tárolót. Az Azure Storage:
- **Tartós és nagy rendelkezésre állású** – a redundancia biztosítja, hogy az adatai biztonságban legyenek az átmeneti hardverhiba esetén. Az adatközpontok vagy földrajzi helyek adatait replikálhatja is a helyi vagy természeti katasztrófák elleni további védelem érdekében. Az így replikált adatok magas rendelkezésre állásúak maradnak váratlan meghibásodás esetén is.
- **Biztonságos** – az Azure Storage-ba írt összes adathalmazt a szolgáltatás titkosítja. Az Azure Storage használatával részletesen szabályozhatja, hogy ki férhet hozzá az adatokhoz.
- A **skálázható** szolgáltatások nagy mértékben méretezhetők, hogy megfeleljenek a mai alkalmazások adattárolási és teljesítménybeli igényeinek.
- **Felügyelt** – az Azure kezeli a hardveres karbantartást, a frissítéseket és a kritikus problémákat.
- Az adatok a világon bárhonnan **elérhetők** http-n vagy HTTPS-en keresztül. A Microsoft számos nyelven biztosít ügyféloldali kódtárakat, többek között a .NET, a Java, a Node. js, a Python, a PHP, a Ruby, a Go és mások számára, valamint egy érett REST API. A parancsfájlok Azure PowerShell vagy az Azure CLI-ben támogatottak, és a Azure Portal és a Azure Storage Explorer egyszerű vizuális megoldásokat kínálnak az adataikkal való munkához.

A következő szolgáltatások segítségével engedélyezheti a felhőalapú tárolást a Mobile apps szolgáltatásban.

## <a name="azure-blob-storage"></a>Azure Blob Storage
Az [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) a felhőhöz kínál objektum-tárolási megoldást, és olyan nagy mennyiségű strukturálatlan adat tárolására van optimalizálva, amely nem felel meg egy adott adatmodellnek vagy definíciónak, például a szövegnek vagy a bináris fájlnak. Az ügyfél-kódtárak különböző nyelveket támogat, és a következőket tervezték:
- Képek vagy dokumentumok közvetlen szolgáltatása a böngészők számára.
- Fájlok tárolása megosztott hozzáféréshez.
- Video- és hangtartalom streamelése.
- Írás naplófájlokba.
- Adattárolás biztonsági mentésekhez és helyreállításhoz, vészhelyreállításhoz és archiváláshoz.
- Adattárolás helyszíni vagy az Azure-ban üzemeltetett szolgáltatásban való elemzéshez.

**Hivatkozik**
- [Azure Portalra](https://portal.azure.com)
- [Dokumentáció](/azure/storage/blobs/storage-blobs-introduction)
- [Rövid útmutatók](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Minták](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure Table Storage
Az [Azure Table Storage](https://azure.microsoft.com/services/storage/tables/) egy olyan szolgáltatás, amely strukturált NoSQL-adattárolást biztosít a felhőben, amely egy, a séma nélküli kialakítással rendelkező kulcs-és attribútum-tárolót tartalmaz. Az Azure Table Storage nagy mennyiségű strukturált adat tárolására alkalmas. A szolgáltatás egy NoSQL-adattár, amely az Azure-felhőn belüli és kívüli hitelesített hívásokat fogadja el. Az Azure-táblák strukturált, nem relációs adatok tárolására alkalmasak. A Table Storage gyakori használati módjai:
- A web-Scale-alkalmazások kiszolgálására alkalmas, a strukturált adathalmazok tárolása.
- Olyan adatkészletek tárolására, amelyek nem igényelnek összetett illesztéseket, külső kulcsokat vagy tárolt eljárásokat, és a gyors hozzáférés érdekében denormalizálható.
- Az adatlekérdezés fürtözött index használatával gyorsan lekérdezhető.
- Hozzáférés az adatokhoz a OData protokoll és a LINQ-lekérdezések használatával a WCF adatszolgáltatási .NET-kódtárakkal.

A Table Storage strukturált, nem relációs adatok hatalmas készleteinek tárolására és lekérdezésére alkalmas, a táblák pedig az igények növekedésével együtt nőnek.

**Hivatkozik**
- [Azure Portalra](https://portal.azure.com)
- [Dokumentáció](/azure/storage/tables/table-storage-overview)
- [Minták](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Rövid útmutatók](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure Files
Az [Azure Files](https://azure.microsoft.com/services/storage/files/) segítségével magas rendelkezésre állású hálózati fájlmegosztásokat hozhat létre, amelyek az SMB protokollon keresztül érhetőek el. Több virtuális gép is megoszthatja ugyanazt a fájlt olvasási és írási hozzáféréssel. A fájlokat a REST-felület vagy a Storage klienskódtáraival is olvashatja. A fájlokat a világ bármely pontjáról elérheti a fájlra mutató URL-címmel, és egy közös hozzáférésű aláírás (SAS) tokent is tartalmaz. SAS-tokeneket Ön is létrehozhat: ezek határozott ideig biztosítanak egyedi hozzáférést a privát objektumokhoz.

Az Azure-fájlmegosztások az alábbiakra használhatók:
- Helyszíni fájlkiszolgálók cseréje vagy pótlása: a népszerű operációs rendszerek, például a Windows, a macOS és a Linux közvetlenül csatlakoztathatók az Azure-fájlmegosztás számára, bárhol is legyenek a világon. Az Azure-fájlmegosztások az Azure File Sync használatával replikálhatók helyszíni vagy felhőalapú Windows Serverekre, az adatok a használat helyéhez közeli nagy teljesítményű és elosztott gyorsítótárazása érdekében.
- Olyan **alkalmazásokat** helyezhet át a felhőbe, amelyek egy fájlmegosztást várnak a fájl-alkalmazás vagy a felhasználói adatok tárolásához.
- A **felhőalapú fejlesztés egyszerűsítése**: Azure Files számos módon használható az új felhőalapú fejlesztési projektek egyszerűsítéséhez. Példa:
    - Megosztott Alkalmazásbeállítások: az elosztott alkalmazások közös mintája, hogy a konfigurációs fájlok központi helyen legyenek, ahol számos alkalmazás-példányból elérhetők. Az alkalmazáspéldányok a konfigurációjukat a Fájl REST API-val tudják betölteni, és a felhasználók az SMB-megosztások helyi csatlakoztatásával érhetik el őket igény szerint.
    - Diagnosztikai megosztás: az Azure-fájlmegosztás kényelmes hely a Felhőbeli alkalmazások számára a naplók, a mérőszámok és az összeomlási memóriaképek írásához. Az alkalmazáspéldányok a Fájl REST API-val írhatják a naplókat, a fejlesztők pedig a fájlmegosztás a helyi gépükön való csatlakoztatásával érhetik el őket. Ez nagy rugalmasságot biztosít, mivel a fejlesztők anélkül használhatják ki a felhő előnyeit, hogy le kellene mondaniuk az általuk használt és kedvelt eszközökről.

**Hivatkozik**
- [Azure Portalra](https://portal.azure.com)
- [Dokumentáció](/azure/storage/files/storage-files-introduction)
- [Rövid útmutatók](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queues"></a>Azure Queues
Az [Azure Queue Storage](https://azure.microsoft.com/services/storage/queues/) nagy mennyiségű üzenet tárolására szolgál. A HTTP-vagy HTTPS-t használó hitelesített hívásokon keresztül érheti el az üzeneteket a világ bármely pontján. Egy üzenetsor-üzenet akár 64 KB méretű is lehet, és a várólista akár több millió üzenetet is tartalmazhat, akár egy Storage-fiók teljes kapacitási korlátját. A várólistákat általában arra használják, hogy egy várakozó munkafolyamatot hozzon létre aszinkron feldolgozásra.

**Hivatkozik**
- [Azure Portalra](https://portal.azure.com)
- [Dokumentáció](/azure/storage/queues/)
- [Rövid útmutatók](/azure/storage/queues/storage-quickstart-queues-portal)
- [Minták](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
