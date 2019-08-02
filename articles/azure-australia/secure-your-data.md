---
title: Adatbiztonság az Azure Ausztráliában
description: Az Azure konfigurálása az ausztráliai régiókban az ausztráliai kormányzati szabályzatok, rendeletek és jogszabályok konkrét követelményeinek kielégítése érdekében.
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 0301d506fc5764dbfda496727da95b2a1f2e9c7e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608351"
---
# <a name="data-security-in-azure-australia"></a>Adatbiztonság az Azure Ausztráliában

A vásárlói adatbiztonság biztosításának átfogó alapelvei a következők:

* Adatok védelme titkosítás használatával
* Titkos kódok kezelése
* Az adathozzáférés korlátozása

## <a name="encrypting-your-data"></a>Az adatai titkosítása

Az adatok titkosítása a lemez szintjén (a REST-on), az adatbázisokban (a nyugalmi és az átviteli forgalomban), az alkalmazásokban (a forgalomban) és a hálózaton (az átvitel közben) is alkalmazható. Az Azure-ban többféleképpen lehet a titkosítást megvalósítani:

|Szolgáltatás/szolgáltatás|Leírás|
|---|---|
|Storage Service Encryption|Az Azure Storage Service Encryption a Storage-fiók szintjén engedélyezhető, ami megakadályozza, hogy a blobok és a Blobok automatikusan titkosítva legyenek az Azure Storage-ba való íráskor. Az Azure Storage-ból származó adatok beolvasása után a rendszer visszafejti a tárolási szolgáltatás által visszaadott értéket. Az SSE használatával biztonságossá teheti adatait anélkül, hogy kódot kellene módosítania vagy felvennie az alkalmazásba.|
|Azure Disk Encryption|A Azure Disk Encryption használatával titkosíthatja az Azure-beli virtuális gépek által használt operációsrendszer-lemezeket és adatlemezeket. A Azure Key Vault való integráció lehetővé teszi a lemezes titkosítási kulcsok felügyeletét és kezelését.|
|Ügyféloldali alkalmazások szintjének titkosítása|Az ügyféloldali titkosítás a Java és a .NET Storage ügyféloldali kódtárainak részét képezi, amelyek a Azure Key Vault API-kat használhatják, így egyszerű megvalósítani. A Azure Key Vault használatával hozzáférést nyerhet az egyes személyeknek a Azure Key Vault titkos kulcsaihoz Azure Active Directory használatával.|
|Titkosítás átvitel közben|Az Azure Australia-hez való kapcsolódáshoz elérhető alapszintű titkosítás támogatja a Transport Level Security (TLS) 1,2 protokollt és az X. 509 tanúsítványokat. A Federal Information Processing standard (FIPS) 140-2 1. szintű titkosítási algoritmusokat az Azure Australia-adatközpontok közötti infrastruktúra-hálózati kapcsolatokhoz is használják.  A Windows Server 2016, a Windows 10, a Windows Server 2012 R2, a Windows 8,1 és az Azure file shares a virtuális gép és a fájlmegosztás között használhat SMB 3,0 titkosítást. Az ügyféloldali titkosítással titkosíthatja az adatmennyiséget, mielőtt átviszi a tárolóba egy ügyfélalkalmazás számára, és visszafejti az adatmennyiséget, miután átvitte a tárolóból.|
|IaaS virtuális gépek|Azure Disk Encryption használata. Kapcsolja be a Storage Service Encryption a lemezek Azure Storage-ban való biztonsági mentéséhez használt VHD-fájlok titkosításához, ez azonban csak az újonnan írt adattartalom titkosítását végzi. Ez azt jelenti, hogy ha létrehoz egy virtuális gépet, majd engedélyezi Storage Service Encryption a VHD-fájlt tároló Storage-fiókon, csak a módosítások lesznek titkosítva, nem az eredeti VHD-fájl.|
|Ügyféloldali titkosítás|Ez a legbiztonságosabb módszer az adatok titkosítására, mivel a rendszer a továbbítás előtt titkosítja, és az inaktív adatok titkosítását. Azonban szükség van arra, hogy a Storage használatával programkódot vegyen fel az alkalmazásaiba, amelyet esetleg nem kíván végrehajtani. Ezekben az esetekben a HTTPS-t használhatja a tranzitban lévő adatokhoz, és Storage Service Encryption az inaktív adatok titkosításához. Az ügyféloldali titkosítás több terhelést is magában foglal az ügyfélen, ezért ezt a skálázhatósági tervekben kell figyelembe vennie, különösen akkor, ha nagy mennyiségű adat titkosítását és továbbítását végzi.|
|

Az Azure titkosítási lehetőségeivel kapcsolatos további információkért lásd a [Storage biztonsági útmutatóját](https://docs.microsoft.com/azure/storage/storage-security-guide).

## <a name="protecting-data-by-managing-secrets"></a>Adatok védelme a titkok kezelésével

A biztonságos kulcskezelő szolgáltatás elengedhetetlen a felhőben tárolt adatok védelméhez. Az ügyfeleknek törekedniük kell a kulcsfontosságú felügyelet egyszerűsítésére és a Felhőbeli alkalmazások és szolgáltatások által használt kulcsok felügyeletének fenntartására az adattitkosítás érdekében.

### <a name="managing-secrets"></a>Titkos kódok kezelése

* A Key Vault használatával csökkentheti a nehezen megfejthető konfigurációs fájlokon, parancsfájlokon vagy forráskódokon keresztül elérhető titkok kockázatait. A Azure Key Vault titkosítja a kulcsokat (például a Azure Disk Encryption titkosítási kulcsait) és a titkokat (például a jelszavakat), ha az FIPS 140-2 2. szintű ellenőrzött hardveres biztonsági modulokban (HSM) tárolja őket. A kiegészítő garanciához kulcsokat importálhat vagy generálhat ezekben a HSM.
* Az alkalmazás kódja és sablonjai csak a titkos kulcsokra mutató URI-hivatkozásokat tartalmazhatják (ami azt jelenti, hogy a tényleges titkok nem a kód, a konfiguráció vagy a forráskód-tárházban találhatók). Ez megakadályozza, hogy a belső vagy külső repók, például a betakarítási robotok a GitHubon a fő adathalászat elleni támadásokat.
* Erős RBAC-vezérlőket használhat Key Vaulton belül. Ha egy megbízható szolgáltató elhagyja a vállalatot, vagy egy új csoportba viszi át a vállalaton belül, meg kell akadályozni a titkok elérését.  

További információ: [Azure Key Vault](azure-key-vault.md)

## <a name="isolation-to-restrict-data-access"></a>Az adathozzáférés korlátozásának elkülönítése

Az elkülönítés lényege, hogy a határok, szegmentálás és tárolók használatával korlátozza az adathozzáférést csak a jogosult felhasználókhoz, szolgáltatásokhoz és alkalmazásokhoz. Például a bérlők közötti elkülönítés egy alapvető biztonsági mechanizmus a több-bérlős felhőalapú platformokhoz, mint például a Microsoft Azure. A logikai elkülönítés segít megakadályozni, hogy az egyik bérlő bármilyen más bérlő műveletét zavarja.

#### <a name="per-customer-isolation"></a>Felhasználónkénti elkülönítés

Az Azure hálózati hozzáférés-vezérlést és elkülönítést valósít meg a 2. rétegbeli VLAN elkülönítés, a hozzáférés-vezérlési listán, a terheléselosztó és az IP-szűrők használatával.

Az ügyfelek továbbra is elkülönítik erőforrásaikat az előfizetések, az erőforráscsoportok, a virtuális hálózatok és az alhálózatok között.

A Microsoft Azure elkülönítésével kapcsolatos további információkért tekintse meg az [elkülönítés az Azure nyilvános felhőben](../security/fundamentals/isolation-choices.md)című témakört.

## <a name="next-steps"></a>További lépések

Tekintse át a cikket az [Azure VPN Gateway](vpn-gateway.md)