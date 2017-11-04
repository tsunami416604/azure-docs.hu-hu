---
title: "Azure Storage Analytics segítségével naplók és a metrikák adatgyűjtés |} Microsoft Docs"
description: "Tárolási analitika lehetővé teszi a tárolási szolgáltatások metrikai adatok nyomon követésére, valamint gyűjtött naplók tárolására Blob, Queue és Table storage."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 7894993b-ca42-4125-8f17-8f6dfe3dca76
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: tamram
ms.openlocfilehash: 9ae9dd0b078911a695d441cd3891be720dc204ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="storage-analytics"></a>Storage Analytics

A Storage szolgáltatás analitikai funkciói naplózást végeznek, valamint elérhetővé teszik a tárfiókok mérőszámadatait. Ezekkel az adatokkal kérések követhetők nyomon, elemezhetők a használati trendek és diagnosztizálni lehet a tárfiókokkal kapcsolatos problémákat.

A tárolási analitika használatához engedélyeznie kell azt egyedileg minden egyes figyelni kívánt szolgáltatás. Akkor is engedélyezheti a [Azure Portal](https://portal.azure.com). További információkért lásd: [figyelése az Azure portálon tárfiók](storage-monitor-storage-account.md). Tárolási analitika programozott módon a REST API vagy ügyfélkódtár keresztül is engedélyezheti. Használja a [Blob szolgáltatástulajdonságok](https://msdn.microsoft.com/library/hh452239.aspx), [várólista-tulajdonságok beolvasása](https://msdn.microsoft.com/library/hh452243.aspx), [tábla szolgáltatástulajdonságok](https://msdn.microsoft.com/library/hh452238.aspx), és [fájl szolgáltatástulajdonságainak](https://msdn.microsoft.com/library/mt427369.aspx) tárolási analitika minden egyes szolgáltatás engedélyezésének műveletei.

Az összesített adatok tárolását a jól ismert blob (naplózás), és jól ismert táblában (a metrika), előfordulhat, hogy elérhetők, a Blob és Table szolgáltatások API-k használatával.

Tárolási analitika 20 TB maximális rendelkezzen, amely független a tárfiók teljes korlátját tárolt adatok mennyisége. További információ a számlázással és az adatmegőrzési házirendek: [tárolási analitika és számlázási](https://msdn.microsoft.com/library/hh360997.aspx). Információ a tárfiókok korlátairól további információkért lásd: [Azure Storage méretezhetőségi és teljesítménycéloknak](storage-scalability-targets.md).

A részletes útmutatót a tárolási analitika és más eszközök segítségével azonosíthatja, diagnosztizálása és Azure tárolással kapcsolatos problémák elhárításához, lásd: [figyelése, diagnosztizálása és elhárítása a Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="about-storage-analytics-logging"></a>Tárolási analitika naplózásról
Storage Analytics egy társzolgáltatás sikeres és sikertelen kérelmekkel kapcsolatos részletes információkat naplózza. Ezeket az információkat egyes kérelmeket a figyelheti és egy tárolási szolgáltatással kapcsolatos problémák diagnosztizálásához használható. Kérelmek is be vannak jelentkezve a legjobb alapul.

Naplóbejegyzéseket tárolási szolgáltatás tevékenység esetén csak jönnek létre. Például ha a tárfiók tevékenység a Blob szolgáltatás, de nem a tábla- vagy várólista szolgáltatásai, csak a Blob szolgáltatáshoz tartozó naplók létrejön.

Storage Analytics naplózás nincs Azure-fájlok esetén érhető el.

### <a name="logging-authenticated-requests"></a>Naplózási hitelesített kérelmek
A következő típusú hitelesített kérelmek bejelentkezett:

* A sikeres kérelmek száma.
* Sikertelen kérelmek, beleértve az időtúllépés, a sávszélesség-szabályozás, hálózati, engedélyezési és egyéb hibák.
* A kérelem egy közös hozzáférésű Jogosultságkód (SAS), például és a sikertelen kérelmek használatával.
* Az analitikai adatok kérelmeket.

Tárolási analitika, például a napló létrehozásakor vagy törlésekor, kérelmét a rendszer nem naplózza. A naplózott adatok teljes listáját részletes ismertetését lásd: a [Storage Analytics naplózott műveletekkel és az állapotüzenetek](https://msdn.microsoft.com/library/hh343260.aspx) és [Storage Analytics naplóformátumban](https://msdn.microsoft.com/library/hh343259.aspx) témaköröket.

### <a name="logging-anonymous-requests"></a>Naplózási névtelen kérésekkel
A következő típusú névtelen kérésekkel bejelentkezett:

* A sikeres kérelmek száma.
* Server hibákat.
* Az ügyfél és kiszolgáló egyaránt időtúllépést.
* Sikertelen GET kérelmek hibakód 304 (nem módosított).

Minden más sikertelen névtelen kérelmek nem naplózza a rendszer. A naplózott adatok teljes listáját részletes ismertetését lásd: a [Storage Analytics naplózott műveletekkel és az állapotüzenetek](https://msdn.microsoft.com/library/hh343260.aspx) és [Storage Analytics naplóformátumban](https://msdn.microsoft.com/library/hh343259.aspx) témaköröket.

### <a name="how-logs-are-stored"></a>Naplófájlok tárolási módját
Összes napló tároló $logs, amely automatikusan jön létre, ha tárolási analitika engedélyezve van a tárfiókon nevű blokkblobok vannak tárolva. A $logs tárolóban található a tárfiók a blob névtérben például: `http://<accountname>.blob.core.windows.net/$logs`. Ebben a tárolóban nem lehet törölni tárolási analitika engedélyezése után abban az esetben, ha tartalmának törlése.

> [!NOTE]
> A $logs tároló nem akkor látható, ha egy művelet listázása tároló hajtja végre, mint például a [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx) metódust. Akkor kell érhető el közvetlenül. Használhatja például a [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) módszer a blobokhoz való hozzáférést a `$logs` tároló.
> Kérések naplózása, mint tárolási analitika fel kell töltenie köztes eredményeket, blokkolja. Rendszeres időközönként tárolási analitika ezeket adatblokkokat véglegesítése és blob-ként elérhetővé teszi azokat.
> 
> 

Ismétlődő rekordok az azonos órához létrehozott naplók előfordulhat, hogy létezik. Meghatározhatja, hogy-e rekord duplikált ellenőrzésével a **kérelemazonosító** és **művelet** számát.

### <a name="log-naming-conventions"></a>Napló elnevezési konvenciók
Minden egyes napló a következő formátumban lesz írva.

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

A következő táblázat ismerteti az egyes attribútumot a napló nevét.

| Attribútum | Leírás |
| --- | --- |
| < szolgáltatásnév > |A storage szolgáltatás nevét. Például: blob, table vagy várólista. |
| ÉÉÉÉ |A napló négyjegyű év. Például: 2011. |
| MM |A napló kétjegyű hónap. Például: 07. |
| DD |A napló kétjegyű hónap. Például: 07. |
| hh |A kétjegyű óra azt jelzi, hogy a kezdő óra a naplókhoz, 24 órás UTC formátumban. Például: 18. |
| mm |A kétjegyű szám, amely jelzi a kiindulási perc, a naplók. Ez az érték tárolási analitika jelenlegi verziója nem támogatott, és az érték mindig lesz 00. |
| <counter> |A nulla alapú számláló hat számjegyükkel, hogy a társzolgáltatás egy óra alatt időszakban előállított napló bináris objektumok számát jelzi. Ez a számláló a 000000 indul. Például: 000001. |

A teljes minta a neve, amely egyesíti az előző példák a következő:

    blob/2011/07/31/1800/000001.log

Az alább látható egy minta URI, amelyet az előző naplóban eléréséhez használható.

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

A tárolási kérés naplózásakor az eredményül kapott napló neve felel meg az órát, amikor a kért műveletet végrehajtani. Például, ha egy GetBlob kérés végre lett hajtva, 6:30-kor: a 7 31/2011 a napló tartalmazná meg a következő előtaggal:`blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Napló metaadatai
Összes naplózási BLOB milyen naplózási adatokat tartalmaz a blob azonosítására használt metaadatok tárolódnak. A következő táblázat ismerteti az egyes metaadat-attribútum.

| Attribútum | Leírás |
| --- | --- |
| LogType |Ismerteti, hogy a napló tartalmaz-e olvasási, írási vagy törlési műveletek alkalmas adatok. Ez az érték egy típus vagy három, vesszővel elválasztva kombinációja tartalmazhatnak. 1. példa: írási; 2. példa: Olvasás, írás; 3. példa: Olvasás, írás, törölje. |
| Kezdő időpont |A legkorábbi időpontot, amikor egy bejegyzés, éééé formájában-hh-SSz. Például: 2011-07-31T18:21:46Z. |
| Befejezés időpontja |A naplóban éééé formájában bejegyzés legutóbbi időpont-hh-SSz. Például: 2011-07-31T18:22:09Z. |
| LogVersion |A naplófájl formátumának verziója. Jelenleg az egyetlen támogatott érték 1.0. |

Az alábbi listában megjeleníti az előző példák segítségével teljes mintát metaadatait.

* LogType írási =
* StartTime = 2011-07-31T18:21:46Z
* EndTime = 2011-07-31T18:22:09Z
* LogVersion = 1.0

### <a name="accessing-logging-data"></a>Naplózási adatok elérése
Az összes adat a `$logs` tároló a Blob szolgáltatás API-k használatával is elérhetők, beleértve a .NET API-k, az Azure által biztosított felügyelt kódtárára. A tárolási fiók rendszergazdájához olvassa el és törölheti naplókat, de nem létrehozása vagy frissítse azokat. A napló metaadatai és a napló nevét is használható az naplók lekérdezésekor. Lehetséges, hogy egy adott óra naplók sorrendje, de a metaadatok mindig adja meg a naplóbejegyzések timespan egy naplóban. Ezért használható naplófájl nevét és a metaadatok az adott naplók keresésekor.

## <a name="about-storage-analytics-metrics"></a>Tárolási analitika metrikák kapcsolatos
Storage Analytics képes tárolni, amely tartalmazza az összevont tranzakció statisztikák és a kapacitás adatok tárolási szolgáltatás kérelmekkel kapcsolatos metrikákat. Tranzakciók jelentett, a API művelet szinten, valamint a tárolás szolgáltatás szintjén, és a kapacitás jelenti a tárolás szolgáltatás szintjén. Metrikai adatok használható tárolási szolgáltatás használati elemzése, eseményadatokat az által, a tárolás szolgáltatás ellen, valamint egy szolgáltatást használó alkalmazások teljesítményének javítása érdekében.

A tárolási analitika használatához engedélyeznie kell azt egyedileg minden egyes figyelni kívánt szolgáltatás. Akkor is engedélyezheti a [Azure Portal](https://portal.azure.com). További információkért lásd: [figyelése az Azure portálon tárfiók](storage-monitor-storage-account.md). Tárolási analitika programozott módon a REST API vagy ügyfélkódtár keresztül is engedélyezheti. Használja a **szolgáltatás Tulajdonságok beolvasása** tárolási analitika minden egyes szolgáltatás engedélyezésének műveletei.

### <a name="transaction-metrics"></a>Tranzakció metrikák
Egy robusztus, az adatok óránkénti vagy perces időközönként minden egyes tárolási szolgáltatás rögzítése, és API műveletet kért, beleértve a be-és kilépési, a rendelkezésre állási, a hibákat, és kategóriába sorolt kérelmek százalékos aránya. A tranzakció részleteit teljes listáját megtekintheti a [Storage Analytics metrikák táblaséma](https://msdn.microsoft.com/library/hh343264.aspx) témakör.

Két szinten – a szolgáltatási szint és az API-művelet szintű tranzakciós adatokat rögzíti. A szolgáltatás szintjén összefoglalójához összes statisztika kért API műveletek kerülnek egy táblaentitássá óránként akkor is, ha a szolgáltatás kérelmek nem történtek. Az API-művelet szinten statisztikák csak kerülnek egy entitás Ha a műveletre vonatkozó kérelem érkezett, hogy órán belül.

Ha például egy **GetBlob** művelettel a Blob szolgáltatás Storage Analytics Metrics naplózni a kérelmet, és adja hozzá a összesített adatai a Blob szolgáltatás, valamint a **GetBlob** művelet. Azonban ha nem **GetBlob** műveletet a órán belül van szükség, egy entitás nem írandó a `$MetricsTransactionsBlob` , hogy a művelethez.

Tranzakció metrikák tárolja a felhasználói kérelmek és a tárolási analitika maga kérelmeire. Például tárolási analitika naplókat, valamint a táblaentitásokat írási kérések rögzítését. Ezek a kérelmek számlázása hogyan kapcsolatos további információkért lásd: [tárolási analitika és számlázási](https://msdn.microsoft.com/library/hh360997.aspx).

### <a name="capacity-metrics"></a>A kapacitás metrikák
> [!NOTE]
> Teljesítmény-mérőszámait jelenleg csak a Blob szolgáltatáshoz érhető el. A Table szolgáltatás és a Queue szolgáltatás kapacitás metrikáját tárolási analitika jövőbeli verzióiban elérhető lesz.
> 
> 

Kapacitástervezési adatok naponta rögzítik egy tárfiók a Blob szolgáltatás, és két tábla entitás készültek. Egy entitás nyújt statisztikai adatokat a felhasználói adatok és egyéb kapcsolatban nyújt statisztikai adatokat a `$logs` tárolási analitika által használt blob-tároló. A `$MetricsCapacityBlob` táblázat tartalmazza a következő adatokat:

* **Kapacitás**: a tárfiók a Blob szolgáltatás bájtban által használt tárolókapacitást.
* **ContainerCount**: a tárfiók a Blob szolgáltatás blob tárolók száma.
* **ObjectCount**: véglegesített és a nem véglegesített blokk vagy a lap blobot, amely a tárfiók a Blob szolgáltatás száma.

A teljesítmény-mérőszámait kapcsolatos további információkért lásd: [Storage Analytics metrikák táblaséma](https://msdn.microsoft.com/library/hh343264.aspx).

### <a name="how-metrics-are-stored"></a>Metrikák tárolási módját
A tárolási szolgáltatások mindegyikének összes metrikai adatok három táblát, hogy a szolgáltatás számára fenntartott tárolja: tranzakció információt egy tábla, egy perc tranzakció információt, egy másik táblázat és kapacitás információt. Tranzakció és percben tranzakció információinak kérelem-válasz adatok áll, és kapacitásadatait tárolási használati adatok áll. Óra metrikákat, percenkénti metrikákat és a tárfiók a Blob szolgáltatás kapacitását érhetők el a következő táblázatban ismertetett módon nevű tábla.

| Metrikák szint | Tábla neve | Támogatott verziók |
| --- | --- | --- |
| Óránkénti metrika, elsődleges hely |$MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue |2013-08-15 csak korábbi verziók. Ezeket a neveket továbbra is támogatottak, váltson az alább felsorolt táblák használata ajánlott. |
| Óránkénti metrika, elsődleges hely |$MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue |Az összes verzió, beleértve a 2013-08-15. |
| Percenkénti metrikákat, elsődleges hely |$MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue |Az összes verzió, beleértve a 2013-08-15. |
| Óránkénti metrika, másodlagos hely |$MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue |Az összes verzió, beleértve a 2013-08-15. Írásvédett georedundáns replikáció engedélyezve kell lennie. |
| Percenkénti metrikákat, másodlagos hely |$MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue |Az összes verzió, beleértve a 2013-08-15. Írásvédett georedundáns replikáció engedélyezve kell lennie. |
| Kapacitás (csak a Blob szolgáltatás) |$MetricsCapacityBlob |Az összes verzió, beleértve a 2013-08-15. |

Ezek a táblázatok jönnek létre automatikusan tárolási analitika engedélyezve van a tárfiók. Hozzáférhetők keresztül a névtér a tárfiók, például:`https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>Metrikai adatok elérése
A metrikák táblák összes adatot a Table szolgáltatás API-k használatával is elérhetők, beleértve a .NET API-k, az Azure által biztosított felügyelt kódtárára. A tárolási fiók rendszergazdájához olvassa el és törölheti táblaentitásokat, de nem létrehozása vagy frissítse azokat.

## <a name="billing-for-storage-analytics"></a>A tárolási analitika számlázási
A tárfiók szolgáltatások összes metrikai adatok írja. Ennek eredményeképpen minden egyes tárolási analitika által végrehajtott írási művelet számlázható. Emellett a metrikai adatok által használt tároló mérete is számlázható.

Az alábbi tárolási analitika által végrehajtott műveletek a következők számlázható:

* A naplózás blobok létrehozásához kérelmek. 
* Kérelmek táblaentitásokat metrikáihoz létrehozásához.

Az adatmegőrzési házirend van beállítva, ha van nem szó, a delete tranzakciók tárolási analitika régi naplózás és a metrikák adatok törlését. Azonban egy ügyfél delete-tranzakciók le számlázható. Adatmegőrzési kapcsolatos további információkért lásd: [Storage Analytics az adatmegőrzési házirend beállítása](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Számlázható kérelmek ismertetése
Egy fiók társzolgáltatás intézett kérelmek számlázható, vagy nem számlázható. Storage Analytics szolgáltatás, beleértve egy állapotüzenetet, amely jelzi, hogy a kérelem kezelése történt meg minden egyes kérelmet naplózza. Hasonlóképpen tárolási analitika tárolja a metrikák egy szolgáltatási és az API műveleteit, hogy, beleértve a szolgáltatásajánlatokat és bizonyos állapotüzenetek száma. Együtt ezeket a szolgáltatásokat segítséget a számlázható kérelmek elemzése, fejlesztései adja meg az alkalmazás a, és és a szolgáltatások eseményadatokat. Számlázással kapcsolatos további információkért lásd: [az Azure Storage számlázási - sávszélesség, a tranzakciók és a kapacitás](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Tárolási analitikai adatok megnézi, használhatja a táblázatok a [Storage Analytics naplózott műveletekkel és az állapotüzenetek](https://msdn.microsoft.com/library/azure/hh343260.aspx) annak meghatározására, hogy mely kérelmek számlázható témakör. Ezután összehasonlíthatja a naplókat és az állapotüzenetek megtekintéséhez, ha Ön volt szó, a egy adott kérés metrikák adatokat. Vizsgálja meg a rendelkezésre állási tárolási szolgáltatás vagy egyéni API-művelet az előző témakörben is használhatja a táblák.

## <a name="next-steps"></a>Következő lépések
### <a name="setting-up-storage-analytics"></a>Tárolási analitika beállítása
* [A figyelő egy tárfiókot az Azure portálon](storage-monitor-storage-account.md)
* [Engedélyezése és konfigurálása a tárolási analitika](https://msdn.microsoft.com/library/hh360996.aspx)

### <a name="storage-analytics-logging"></a>Storage Analytics naplózás
* [Storage Analytics naplózásról](https://msdn.microsoft.com/library/hh343262.aspx)
* [Storage Analytics naplóformátumban](https://msdn.microsoft.com/library/hh343259.aspx)
* [Tárolási analitika naplózott műveletek és az állapotüzenetek](https://msdn.microsoft.com/library/hh343260.aspx)

### <a name="storage-analytics-metrics"></a>Storage Analytics metrikák
* [Storage Analytics metrikák kapcsolatos](https://msdn.microsoft.com/library/hh343258.aspx)
* [Storage Analytics metrikák táblaséma](https://msdn.microsoft.com/library/hh343264.aspx)
* [Tárolási analitika naplózott műveletek és az állapotüzenetek](https://msdn.microsoft.com/library/hh343260.aspx)  

