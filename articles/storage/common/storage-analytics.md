---
title: Azure Storage analytics naplók és mérőszámok adatok összegyűjtéséhez használja |} A Microsoft Docs
description: A Storage Analytics lehetővé teszi, hogy nyomon követheti a metrikák adatait a storage-szolgáltatások, és a Blob, Queue és Table storage naplóinak összegyűjtése.
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 9cd4845bcf107941f969255eb223567d4341ea41
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55508504"
---
# <a name="storage-analytics"></a>Storage Analytics

A Storage szolgáltatás analitikai funkciói naplózást végeznek, valamint elérhetővé teszik a tárfiókok mérőszámadatait. Ezekkel az adatokkal kérések követhetők nyomon, elemezhetők a használati trendek és diagnosztizálni lehet a tárfiókokkal kapcsolatos problémákat.

A Storage Analytics használatához minden egyes figyelni kívánt szolgáltatás külön-külön kell engedélyeznie. Ön is engedélyezheti a [az Azure Portal](https://portal.azure.com). További információkért lásd: [monitorozása az Azure Portal tárfiók](storage-monitor-storage-account.md). A Storage Analytics automatizáltan a REST API-t vagy az ügyféloldali kódtár is engedélyezheti. Használja a [beolvasása a Blob szolgáltatás tulajdonságai](https://msdn.microsoft.com/library/hh452239.aspx), [Queue szolgáltatás tulajdonságainak lekérése](https://msdn.microsoft.com/library/hh452243.aspx), [Table Service tulajdonságainak lekérése](https://msdn.microsoft.com/library/hh452238.aspx), és [File Service Propertieselső](https://msdn.microsoft.com/library/mt427369.aspx)minden egyes szolgáltatás a Storage Analytics engedélyezésének műveletei.

Az összesített adatokat tárolja egy jól ismert blob (naplózás) és a jól ismert táblák (a metrikákat), amely a Blob és Table szolgáltatások API-k használatával is elérhetők.

A Storage Analytics esetében a 20 TB-ig, amely független a tárfiók teljes maximális tárolt adatok mennyisége. Számlázással és az adatmegőrzési házirendek kapcsolatos további információkért lásd: [Storage Analytics és a számlázás](https://msdn.microsoft.com/library/hh360997.aspx). Információ a tárfiókok korlátairól további információkért lásd: [Azure Storage méretezhetőségi és Teljesítménycéljai](storage-scalability-targets.md).

A Storage Analytics és más eszközök használatával azonosítsa, diagnosztizálása és hibaelhárítása az Azure Storage szolgáltatással kapcsolatos problémák a részletes útmutatót lásd: [figyelése, diagnosztizálása és hibaelhárítása a Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="about-storage-analytics-logging"></a>Storage analytics naplózásával kapcsolatos
A Storage Analytics naplók sikeres és sikertelen kérések kapcsolatos részletes információk egy tárolási szolgáltatásba. Ezt az információt a figyelheti az egyes kérések és a egy storage szolgáltatással kapcsolatos problémák diagnosztizálásához használható. Kérelmek van bejelentkezve a képességeinkhez mérten biztosítjuk.

Csak akkor, ha a storage szolgáltatás tevékenység naplóbejegyzések jönnek létre. Például ha egy tárfiókot a Blob service-ben, de nem a tábla vagy üzenetsor szolgáltatásaiból, csak a Blob szolgáltatáshoz tartozó naplók létrejön.

Storage Analytics naplózás nem érhető el az Azure Files számára.

### <a name="logging-authenticated-requests"></a>Hitelesített naplózást kérelmek
A következő típusú hitelesített kérelmeket naplózza:

* Sikeres kérelmeinek száma.
* Sikertelen kérelmek, beleértve az időkorlát, a szabályozás, hálózati, engedélyezési és egyéb hibák.
* A kérelmek egy közös hozzáférésű Jogosultságkód (SAS), beleértve a sikeres és sikertelen kérelmek használatával.
* Elemzési adatok kérelmeket.

Kérést hoz létre a Storage Analytics, például a napló a létrehozás vagy törlés, a program nem naplózza. A naplózott adatok teljes listáját leírása itt található a [Storage Analytics naplózott műveletek és az állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages.md) és [Storage Analytics naplóformátum](/rest/api/storageservices/storage-analytics-log-format.md) témaköröket.

### <a name="logging-anonymous-requests"></a>Névtelen kérések naplózása
A következő típusú névtelen kérésekkel jelentkezett:

* Sikeres kérelmeinek száma.
* Kiszolgálóhibáinak száma.
* Az ügyfél- és kiszolgálói időtúllépést.
* Sikertelen GET kérelmek hibakód 304 (nem módosított).

Az összes többi sikertelen névtelen kérelmek nem jelentkezett. A naplózott adatok teljes listáját leírása itt található a [Storage Analytics naplózott műveletek és az állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages.md) és [Storage Analytics naplóformátum](/rest/api/storageservices/storage-analytics-log-format.md) témaköröket.

### <a name="how-logs-are-stored"></a>Naplók tárolási módját
Az összes napló blokkblobok $logs, amely automatikusan létrejön, amikor a storage-fiók engedélyezve van a Storage Analytics nevű tárolóban vannak tárolva. A $logs tárolóban található a tárfiók blob névtér például: `http://<accountname>.blob.core.windows.net/$logs`. Ez a tároló nem lehet törölni a Storage Analytics engedélyezése után tartalmának törlése, ha.

> [!NOTE]
> Amikor egy tároló listázása művelet hajtja végre, mint például a $logs tároló nem jelenik meg a [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx) metódust. Azt kell elérni közvetlenül. Használhatja például a [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) metódus eléréséhez a blobok a `$logs` tároló.
> Kérés naplózásra kerül, mint a Storage Analytics köztes eredményeket fogja feltöltése blokkolja. Rendszeres időközönként a Storage Analytics ezeket adatblokkokat véglegesítése és elérhetővé teheti őket blobként.
>
>

Ismétlődő rekordok léteznek előfordulhat, hogy az adott órán létrehozott naplók. Segítségével meghatározhatja, hogy ha egy rekord ellenőrzésével többször szerepel a **kérelemazonosító** és **művelet** számát.

### <a name="log-naming-conventions"></a>Napló elnevezési konvenciók
Minden napló a következő formátumban lesz írva.

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

Az alábbi táblázat ismerteti az egyes tulajdonságokat a napló nevét.

| Attribútum | Leírás |
| --- | --- |
| <service-name> |A storage szolgáltatás neve. Például: blob, tábla vagy üzenetsor. |
| ÉÉÉÉ |A négy számjegyű év a naplót. Példa: 2011. |
| MM |Az a Eseménynapló kétjegyű hónappal. Példa: 07. |
| DD |Az a Eseménynapló kétjegyű hónappal. Példa: 07. |
| hh |A két számjegyű óra, amely azt jelzi, hogy a kezdő óra 24 órás UTC formátumban, a naplókhoz. Példa: 18. |
| mm |A kétjegyű számmal, amely azt jelzi, hogy a kiindulási perc, a naplókhoz. A Storage Analytics jelenlegi verziója nem támogatja ezt az értéket, és az érték mindig 00. |
| <counter> |Egy nulla-alapú számláló 6 számjegyű, amely azt jelzi, hogy a storage szolgáltatás egy órás időszakban létrehozott naplóblobok száma. Ez a számláló a 000000 indul. Példa: 000001. |

Az alábbiakban látható egy teljes példa a neve, amely egyesíti az előző példák.

    blob/2011/07/31/1800/000001.log

Az alábbiakban látható egy minta URI, amely az előző naplóban eléréséhez használható.

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

Egy tárolási kérés naplózásakor az óra, ha a kért művelet utal. az eredményül kapott napló nevét. Például, ha a GetBlob kérelem befejeződött, 18:30 óra a 7/31/2011 a napló a következő előtaggal írt volna: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Napló metaadatai
Összes naplózási blobok metaadatait, mely tartalmazza a blob naplózási adatok azonosítására szolgáló együtt vannak tárolva. A következő táblázat ismerteti az egyes metaadat-attribútum.

| Attribútum | Leírás |
| --- | --- |
| LogType |Ismerteti, hogy a napló tartalmazza-e olvasási, írási vagy törlési műveletek kapcsolatos információk. Ez az érték tartalmazhat egy vagy három, vesszővel elválasztva kombinációja. 1. példa: írási; 2. példa: Olvasás, írás; 3. példa: Olvasás, írás, törlés. |
| Kezdés időpontja |A legkorábbi időpontot, amikor egy bejegyzést a naplóban formájában, éééé-hh-DDThh:mm:ssZ. Példa: 2011-07-31T18:21:46Z. |
| Befejezés időpontja: |Legújabb idején egy bejegyzést a naplóban formájában, éééé-hh-DDThh:mm:ssZ. Példa: 2011-07-31T18:22:09Z. |
| LogVersion |A napló formátuma verziója. Jelenleg az egyetlen támogatott érték az 1.0-t. |

A következő lista azoknak a teljes minta az előző példák metaadatait.

* LogType=write
* StartTime=2011-07-31T18:21:46Z
* EndTime=2011-07-31T18:22:09Z
* LogVersion=1.0

### <a name="accessing-logging-data"></a>Naplózási adatok elérése
Az összes adatot a `$logs` tárolót a Blob szolgáltatás API-k használatával érhető el, beleértve a .NET API-k az Azure által kínált felügyelt kódtárára. A storage-fiók rendszergazdája is olvassa el, és törölje azokat a naplókat, de nem hozható létre vagy frissíteni őket. A napló metaadatai és a napló nevét naplók lekérdezése során használható. Lehetséges, hogy egy adott óra naplók megjelenjenek az üzemen kívüli, de a metaadatok mindig adja meg a naplózási bejegyzések timespan egy naplóban. Ezért naplófájl nevét és metaadatait kombinációját használhatja fel, egy adott napló keresése.

## <a name="about-storage-analytics-metrics"></a>Tudnivalók a Storage Analytics mérőszámainak áttekintését
A Storage Analytics olyan mérőszámokat, összesített és kapacitási adatait tartalmazzák a tárolási szolgáltatáshoz érkező kérések kapcsolatban tárol. Tranzakciók jelenti az API művelet szinten, valamint a storage szolgáltatás szintjén, és a storage szolgáltatás szintjén jelentett kapacitás. Metrikaadatok is használható, elemezheti a storage szolgáltatás használatát, a storage szolgáltatás kérelmekre kapcsolatos problémák diagnosztizálása és a egy szolgáltatást használó alkalmazások teljesítményének javítása érdekében.

A Storage Analytics használatához minden egyes figyelni kívánt szolgáltatás külön-külön kell engedélyeznie. Ön is engedélyezheti a [az Azure Portal](https://portal.azure.com). További információkért lásd: [monitorozása az Azure Portal tárfiók](storage-monitor-storage-account.md). A Storage Analytics automatizáltan a REST API-t vagy az ügyféloldali kódtár is engedélyezheti. Használja a **szolgáltatás tulajdonságainak lekérése** minden egyes szolgáltatás a Storage Analytics engedélyezésének műveletei.

### <a name="transaction-metrics"></a>Tranzakció-mérőszámot
Adatok robusztus készletét minden egyes tárolási szolgáltatás óránként vagy perces időközönként rögzíti, és a kért API-művelet, beleértve a bejövő/kimenő forgalom, a rendelkezésre állás, a hibákat, és kérelmek százalékos kategorizált. A tranzakció részleteit a teljes listáját megtekintheti a [Storage Analytics mérőszámainak Táblasémáját](https://msdn.microsoft.com/library/hh343264.aspx) témakör.

Tranzakciós adatok két szinten – a szolgáltatási szint és az API-műveletek szintjén rögzíti. A szolgáltatási szintű összefoglalójához az összes statisztika kért API-műveleteket írja a program egy Táblaentitás óránként akkor is, ha nincsenek kérelmek történtek a szolgáltatás. Az API-művelet szintjén statisztikák csak írt entitásokhoz, ha a művelet adott órán belül a kért.

Például, ha hajt végre egy **GetBlob** műveletet a Blob service, a Storage Analytics mérőszámainak áttekintését a fog naplózni a kérelmet, és adja hozzá az összesített adatokat a Blob szolgáltatás, valamint a **GetBlob** a művelet. Azonban ha nem **GetBlob** művelet az adott órában van szükség, egy entitás nem ír a `$MetricsTransactionsBlob` az adott műveletnél.

Tranzakció-mérőszámot rögzíti a felhasználói kérések és a kérést hoz létre a Storage Analytics magát. Például a Storage Analytics naplók és táblaentitások írása kérések rögzíti. Hogyan számlázzuk a kérések kapcsolatos további információkért lásd: [Storage Analytics és a számlázás](https://msdn.microsoft.com/library/hh360997.aspx).

### <a name="capacity-metrics"></a>A kapacitás-metrikák
> [!NOTE]
> Kapacitási jelenleg csak a Blob szolgáltatás érhető el.
>
>

Kapacitás adatokat a rendszer naponta rögzíti a tárfiók Blob service, és két táblaentitások készültek. Egy entitás statisztika biztosít a felhasználói adatok és statisztikák kapcsolatban nyújt a másik a `$logs` Storage Analytics által használt blob-tárolóba. A `$MetricsCapacityBlob` táblázat foglalja össze a következő adatokat:

* **Kapacitás**: A tárfiók Blob service, a memória által felhasznált tárterület mennyisége.
* **ContainerCount**: A tárfiók Blob service-ben a blobtárolók száma.
* **ObjectCount**: A tárfiók Blob service-ben vállalt és a nem véglegesített blokk vagy lap blobok száma.

A kapacitási mérőszámot kapcsolatos további információkért lásd: [Storage Analytics mérőszámainak Táblasémáját](/rest/api/storageservices/storage-analytics-metrics-table-schema.md).

### <a name="how-metrics-are-stored"></a>Metrikák módjára
Fenntartva, hogy a szolgáltatás három táblában tárolt összes metrikák adatait a tárolási szolgáltatások mindegyike esetében: tranzakció adatainak egy táblát, egy tábla a percenkénti tranzakciós adatokat és kapacitás egy másik táblázatból. Tranzakció, és a perc tranzakcióinformációi áll kérelmek és válaszok adatait, és kapacitási adatainak a storage használati adatok áll. Óra típusú metrikák, a perc típusú metrikák és a tárfiók Blob szolgáltatásának kapacitás, amely az alábbi táblázatban leírtak szerint nevesített táblákat is elérhetők.

| Metrikák szint | Táblanevek | Támogatott verziók |
| --- | --- | --- |
| Óránkénti mérőszámokat, elsődleges hely |$MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue |2013-08-15 csak előtti verziók. Miközben továbbra is támogatja ezeket a neveket, váltson át az alábbi táblázatok használata ajánlott. |
| Óránkénti mérőszámokat, elsődleges hely |$MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue |Összes verzió, beleértve a 2013-08-15. |
| Perc típusú metrikák, elsődleges hely |$MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue |Összes verzió, beleértve a 2013-08-15. |
| Óránkénti mérőszámot, a másodlagos helyre |$MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue |Összes verzió, beleértve a 2013-08-15. Írásvédett georedundáns replikáció engedélyezve kell lennie. |
| Perc típusú metrikák, a másodlagos helyre |$MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue |Összes verzió, beleértve a 2013-08-15. Írásvédett georedundáns replikáció engedélyezve kell lennie. |
| Kapacitás (csak Blob szolgáltatás) |$MetricsCapacityBlob |Összes verzió, beleértve a 2013-08-15. |

Ezek a táblák a rendszer automatikusan létrehozza, ha a Storage Analytics engedélyezve van a storage-fiók. Akkor érhetők el a névtér a tárfiók, például: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>Metrikai adatok elérése
A metrikák táblákban lévő összes adatot a Table service API-k használatával érhető el, beleértve a .NET API-k az Azure által kínált felügyelt kódtárára. A storage-fiók rendszergazdája is olvassa el és törölhetők táblaentitások, de nem hozható létre vagy frissíteni őket.

## <a name="billing-for-storage-analytics"></a>A Storage Analytics díjszabása
A tárfiók szolgáltatások összes mérőszámadatokat írja. Ennek eredményeképpen minden egyes írási műveletnél a Storage Analytics által végrehajtott számlázható. Ezenkívül egyben számlázható mérőszámadatokat által felhasznált tárterület mennyisége.

A következő műveleteket végzi a Storage Analytics számlázhatók:

* Kérés létrehozása a blobok a naplózáshoz.
* Kérés létrehozása metrikákhoz táblaentitások.

Ha az adatmegőrzési házirend van beállítva, nem díjkötelesek törlési tranzakció során a Storage Analytics törli a régi naplózás és mérőszámok adatait. Azonban a törlési tranzakció egy ügyfél olyan számlázható. Adatmegőrzési házirendek kapcsolatos további információkért lásd: [Storage Analytics adatmegőrzési házirend beállítása](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Számlázható kérelmének ismertetése
Minden fiók storage szolgáltatás felé irányuló kérések, a számlázható, vagy a nem számlázható. A Storage Analytics naplók egy szolgáltatáshoz, köztük egy állapotüzenetet, amely azt jelzi, hogy a kérelem kezelése történt meg minden egyes kérelem. Ehhez hasonlóan a Storage Analytics tárolja a metrikákat egy szolgáltatás és a szolgáltatáshoz, beleértve a százalékok és bizonyos állapotüzenetek száma az API műveleteit. Együtt ezek a funkciók segítségével elemezheti a számlázható kérések, az alkalmazás továbbfejlesztése és diagnosztizálhatja problémáit a szolgáltatásokra irányuló kérések. Számlázással kapcsolatos további információkért lásd: [ismertetése az Azure Storage Billing – sávszélesség, tranzakciók és a kapacitás](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Ha megnézzük a Storage Analytics-adatait, használhatja a táblák a [Storage Analytics naplózott műveletek és az állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages.md) témakör annak meghatározására, hogy mely kérelmek számlázható. Ezután összehasonlíthatja a naplókat és az állapotüzenetek megtekintéséhez, ha meg lett díj felszámítva az egy adott kérés. Használhatja a táblák az előző témakörben tárolási szolgáltatás vagy egyéni API-művelet rendelkezésre állási vizsgálatára.

## <a name="next-steps"></a>További lépések
* [Az Azure Portal tárfiók figyelése](storage-monitor-storage-account.md)
* [A Storage Analytics naplózási](https://msdn.microsoft.com/library/hh343262.aspx)
* [Storage Analytics Metrics](https://msdn.microsoft.com/library/hh343258.aspx)
