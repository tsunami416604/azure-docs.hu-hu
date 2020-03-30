---
title: Egyéni naplók gyűjtése az Azure Monitorban | Microsoft dokumentumok
description: Az Azure Monitor eseményeket gyűjthet szöveges fájlokból windowsos és Linux os számítógépeken.  Ez a cikk ismerteti, hogyan definiálhatja az új egyéni napló és az Azure Monitorban létrehozott rekordok részleteit.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/26/2019
ms.openlocfilehash: 1e889aaef7cd01cd743e8063a8a1dd5138ba9d0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670593"
---
# <a name="custom-logs-in-azure-monitor"></a>Egyéni naplók az Azure Monitorban

Az Egyéni naplók adatforrás az Azure Monitorban lehetővé teszi, hogy eseményeket gyűjtsön szöveges fájlokból windowsos és Linux os számítógépeken. Számos alkalmazás naplózza az adatokat szöveges fájlokba a szabványos naplózási szolgáltatások, például a Windows eseménynapló vagy a Syslog helyett. Az adatgyűjtést követően elemezheti az adatokat a lekérdezések egyes mezőiben, vagy kinyerheti az adatokat az egyes mezőkbe történő adatgyűjtés során.

![Egyéni naplógyűjtés](media/data-sources-custom-logs/overview.png)

Az összegyűjtendő naplófájloknak meg kell felelniük az alábbi feltételeknek.

- A naplónak soronként egyetlen bejegyzéssel kell rendelkeznie, vagy minden bejegyzés elején az alábbi formátumok egyikének megfelelő időbélyeget kell használnia.

    YYYY-MM-DD ÓÓ:MM:SS<br>M/D/ÉÉÉÉ ÓÓP:PP:SS AM/PM<br>H DD, ÉÉÉÓÓÓ ÓCÉ:MM:SS<br />yyMMdd ÓÓ:pp:ss<br />ddMyy ÓÓ:mm:ss<br />MMM d hh:pp:ss<br />dd/MMM/ééééé:ÓÓ:pp:sS zzz<br />yyyy-MM-ddTHH:mm:sSK

- A naplófájl nem engedélyezheti a körkörös naplózást vagy a napló elforgatását, ha a fájlt új bejegyzésekkel felülírja.
- A naplófájlnak ASCII vagy UTF-8 kódolást kell használnia.  Más formátumok, például az UTF-16 nem támogatottak.

>[!NOTE]
> Ha ismétlődő bejegyzések vannak a naplófájlban, az Azure Monitor gyűjti azokat. A lekérdezés eredményei azonban inkonzisztensek lesznek, ha a szűrő eredményei több eseményt jelenítenek meg, mint az eredmények száma. Fontos, hogy ellenőrizze a naplót annak megállapítására, hogy az azt létrehozó alkalmazás okozza-e ezt a viselkedést, és ha lehetséges, az egyéni naplógyűjtemény-definíció létrehozása előtt foglalkozzon vele.  
>

>[!NOTE]
> A Log Analytics-munkaterület a következő korlátokat támogatja:
> 
> * Csak 500 egyéni napló hozható létre.
> * Egy táblázat legfeljebb 500 oszlopot támogat. 
> * Az oszlopnév legfeljebb 500 karakterből állhat. 
>

>[!IMPORTANT]
>Az egyéni naplógyűjteményhez a naplófájlt író alkalmazásnak rendszeresen ürölnie kell a naplótartalmat a lemezre. Ennek az az oka, hogy az egyéni naplógyűjtemény a nyomon követett naplófájl fájlrendszer-módosítási értesítéseitől függ.

## <a name="defining-a-custom-log"></a>Egyéni napló definiálása
Egyéni naplófájl definiálása az alábbi eljárással.  Görgessen a cikk végére az egyéni napló hozzáadásának mintájának forgatókönyvéhez.

### <a name="step-1-open-the-custom-log-wizard"></a>1. lépés Az Egyéni napló varázsló megnyitása
Az egyéni napló varázsló fut az Azure Portalon, és lehetővé teszi, hogy egy új egyéni napló t.

1. Az Azure Portalon válassza a **Log Analytics-munkaterületeket,** > a munkaterületet > **a speciális beállításokat.**
2. Kattintson az **Egyéni adatok** > **naplói gombra.**
3. Alapértelmezés szerint az összes konfigurációs módosítás automatikusan lekerül az összes ügyintézőre. Linux-ügynökök esetében a rendszer konfigurációs fájlt küld a Fluentd adatgyűjtőnek.
4. Az Egyéni napló varázsló megnyitásához kattintson a **Hozzá+** gombra.

### <a name="step-2-upload-and-parse-a-sample-log"></a>2. lépés Mintanapló feltöltése és elemzése
Először töltsön fel egy mintát az egyéni naplóból.  A varázsló elemzi és megjeleníti a fájlban lévő bejegyzéseket, hogy érvényesítse.  Az Azure Monitor a megadott határolójel használatával azonosítja az egyes rekordokat.

**Az új sor** az alapértelmezett határolójel, és olyan naplófájlokhoz használatos, amelyek soronként egyetlen bejegyzéssel rendelkeznek.  Ha a sor a rendelkezésre álló formátumok egyikében dátummal és idővel kezdődik, akkor megadhat egy **időbélyeg-határolót,** amely támogatja az egynél több sort átölelő bejegyzéseket.

Ha időbélyeg-határolót használ, akkor az Azure Monitorban tárolt egyes rekordok TimeGenerated tulajdonsága a naplófájlban megadott dátummal/idővel lesz feltöltve.  Ha egy új sorhatároló t használja, majd timegenerated feltölti a dátumot és az időt, amikor az Azure Monitor összegyűjtötte a bejegyzést.

1. Kattintson **a Tallózás gombra,** és keresse meg a mintafájlt.  Ne feledje, hogy ez a gomb egyes böngészőkben **a Fájl kiválasztása** feliratú lehet.
2. Kattintson a **Tovább** gombra.
3. Az Egyéni napló varázsló feltölti a fájlt, és felsorolja az általa azonosított rekordokat.
4. Módosítsa az új rekord azonosítására használt határolójelet, és válassza ki azt a határolójelet, amely a legjobban azonosítja a naplófájlrekordjait.
5. Kattintson a **Tovább** gombra.

### <a name="step-3-add-log-collection-paths"></a>3. lépés Naplógyűjtési útvonalak hozzáadása
Meg kell határoznia egy vagy több elérési utat az ügynökön, ahol megtalálhatja az egyéni naplót.  Megadhatja a naplófájl adott elérési útját és nevét, vagy megadhat egy helyettesítő karaktert a névhez. Ez azokat az alkalmazásokat támogatja, amelyek minden nap új fájlt hoznak létre, vagy amikor egy fájl elér egy bizonyos méretet. Egyetlen naplófájlhoz több elérési utat is megadhat.

Előfordulhat például, hogy egy alkalmazás minden nap létrehoz egy naplófájlt a 20100316.txt naplóban szereplő névvel. Az ilyen napló mintája lehet *log\*.txt,* amely az alkalmazás elnevezési sémáját követő naplófájlokra vonatkozna.

Az alábbi táblázat példákat mutat be a különböző naplófájlok megadásához szükséges érvényes mintákra.

| Leírás | Útvonal |
|:--- |:--- |
| A *C:\Logs* fájl .txt kiterjesztésű összes fájlja Windows-ügynökön |C:\Naplók\\\*.txt |
| A *C:\Logs* összes fájlja naplóval kezdődő névvel és .txt kiterjesztéssel windowsos ügynökön |C:\Logs\log\*.txt |
| A */var/log/audit* összes fájlja .txt kiterjesztéssel Linux ügynökön |/var/log/audit/*.txt |
| A */var/log/audit* összes fájlja naplóval kezdődő névvel és .txt kiterjesztéssel Linux ügynökön |/var/log/audit/log\*.txt |

1. Válassza a Windows vagy a Linux lehetőséget a hozzáadni kívánt elérési út formátumának megadásához.
2. Írja be a görbét, és kattintson a **+** gombra.
3. Ismételje meg a folyamatot minden további elérési úton.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>4. lépés Adja meg a napló nevét és leírását
A megadott nevet a fent leírt naplótípushoz fogja használni a rendszer.  Ez mindig a _CL végződik, hogy megkülönböztesse egyéni naplóként.

1. Írja be a napló nevét.  A ** \_CL** utótag automatikusan meg lesz adva.
2. Adjon hozzá egy nem kötelező **leírást.**
3. Az egyéni naplódefiníció mentéséhez kattintson a **Tovább** gombra.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>5. lépés Az egyéni naplók gyűjtésének ellenőrzése
Akár egy órát is igénybe vehet, amíg az új egyéni naplóból származó kezdeti adatok megjelennek az Azure Monitorban.  Megkezdi a bejegyzések gyűjtését a megadott elérési útból található naplókból az egyéni napló definiálásának pontjáról.  Nem őrzi meg az egyéni napló létrehozása során feltöltött bejegyzéseket, de összegyűjti a már meglévő bejegyzéseket a megtalált naplófájlokban.

Amint az Azure Monitor elkezdi gyűjteni az egyéni naplóból, a rekordok lesz elérhető egy naplólekérdezés.  Használja az egyéni naplót megadott nevet **típusként** a lekérdezésben.

> [!NOTE]
> Ha a RawData tulajdonság hiányzik a lekérdezésből, előfordulhat, hogy be kell zárnia, majd újra meg kell nyitnia a böngészőt.

### <a name="step-6-parse-the-custom-log-entries"></a>6. lépés Az egyéni naplóbejegyzések elemzése
A teljes naplóbejegyzés egyetlen RawData nevű tulajdonságban lesz **tárolva.**  Akkor valószínűleg szeretné elkülöníteni a különböző információkat minden egyes bejegyzés az egyes tulajdonságok minden rekordot. Az [Azure Monitor ban található szöveges adatok elemzési](../log-query/parse-text.md) adataiból megtudhatja, hogy miként elemezheti a **RawData-adatokat** több tulajdonságra.

## <a name="removing-a-custom-log"></a>Egyéni napló eltávolítása
Használja a következő folyamatot az Azure Portalon egy korábban definiált egyéni napló eltávolításához.

1. A munkaterület Speciális **beállításai** **menüjében** válassza az **Egyéni naplók** lehetőséget az összes egyéni napló listázásához.
2. Az eltávolításhoz kattintson az egyéni napló melletti **Eltávolítás** gombra.

## <a name="data-collection"></a>Adatgyűjtés
Az Azure Monitor körülbelül 5 percenként gyűjti az új bejegyzéseket az egyes egyéni naplókból.  Az ügynök rögzíti a helyét minden naplófájlban, amelyből gyűjt.  Ha az ügynök egy ideig offline állapotba kerül, majd az Azure Monitor gyűjtbejegyzéseket onnan, ahol az utolsó abbahagyta, még akkor is, ha ezeket a bejegyzéseket akkor hozták létre, amikor az ügynök offline állapotban volt.

A naplóbejegyzés teljes tartalma egyetlen **RawData**nevű tulajdonságba van írva.  Tekintse meg [a szöveges adatok elemzése az Azure Monitorban](../log-query/parse-text.md) az egyes importált naplóbejegyzések több tulajdonságba történő elemzési módszereit.

## <a name="custom-log-record-properties"></a>Egyéni naplórekord tulajdonságai
Az egyéni naplórekordok típusa a megadott naplónévvel és az alábbi táblázatban szereplő tulajdonságokkal rendelkezik.

| Tulajdonság | Leírás |
|:--- |:--- |
| TimeGenerated |A rekord Azure Monitor általi gyűjtésének dátuma és időpontja.  Ha a napló időalapú határolót használ, akkor ez a bejegyzésből begyűjtött idő. |
| SourceSystem |A rekord által gyűjtött ügynök típusa. <br> OpsManager – Windows ügynök, közvetlen csatlakozás vagy System Center Operations Manager <br> Linux – Minden Linux ügynök |
| Nyers adatok |Az összegyűjtött bejegyzés teljes szövege. Valószínűleg [ezeket az adatokat egyedi tulajdonságokra](../log-query/parse-text.md)szeretné elemezni. |
| ManagementGroupName |A System Center műveleti műveletek kezelése ügynökök felügyeleti csoportjának neve.  Más ügynökök esetében ez az\<AOI-munkaterület-azonosító\> |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Egyéni napló hozzáadásának forgatókönyve
A következő szakasz egy példa az egyéni napló létrehozására.  Az összegyűjtött mintanapló minden sorban egyetlen bejegyzéssel rendelkezik, amely egy dátummal és idővel kezdődik, majd vesszővel tagolt mezőkkel a kódhoz, az állapothoz és az üzenethez.  Az alábbiakban több mintabejegyzés látható.

    2019-08-27 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2019-08-27 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2019-08-27 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2019-08-27 01:38:22 302,Error,Application could not connect to database
    2019-08-27 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Mintanapló feltöltése és elemzése
Mi biztosítja az egyik naplófájlokat, és láthatjuk az eseményeket, hogy lesz gyűjtése.  Ebben az esetben az Új sor elegendő határolójel.  Ha a napló egyetlen bejegyzése több sorra is kiterjedhet, akkor időbélyeg-határolót kell használni.

![Mintanapló feltöltése és elemzése](media/data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Naplógyűjtési útvonalak hozzáadása
A naplófájlok a *C:\MyApp\Logs mappában*találhatók.  Minden nap új fájl jön létre egy névvel, amely tartalmazza a dátumot a minta *appYYYYYMMDD.log*.  A napló megfelelő mintája a *C:\MyApp\Logs\\\*.log*.

![Naplógyűjtési útvonal](media/data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Adja meg a napló nevét és leírását
A MyApp_CL nevet *használjuk,* és beírjuk a **Leírást.**

![Napló neve](media/data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Az egyéni naplók gyűjtésének ellenőrzése
Az összegyűjtött naplóból származó összes rekord visszaadásához *MyApp_CL* egyszerű lekérdezését használjuk.

![Lekérdezés naplózása egyéni mezők nélkül](media/data-sources-custom-logs/query-01.png)


## <a name="alternatives-to-custom-logs"></a>Az egyéni naplók alternatívái
Bár az egyéni naplók akkor hasznosak, ha az adatok megfelelnek a felsorolt feltételeknek, de vannak olyan esetek, mint például a következő, ahol egy másik stratégiára van szükség:

- Az adatok nem felelnek meg a szükséges struktúrának, például az időbélyeg más formátumban.
- A naplófájl nem felel meg az olyan követelményeknek, mint a fájlkódolás vagy a nem támogatott mappastruktúra.
- Az adatok gyűjtése előtt előfeldolgozást vagy szűrést igényelnek. 

Azokban az esetekben, amikor az adatok nem gyűjthetők egyéni naplókkal, fontolja meg a következő alternatív stratégiákat:

- Egyéni parancsfájl vagy más módszer használatával adatokat írhat a [Windows-események](data-sources-windows-events.md) vagy [a Syslog,](data-sources-syslog.md) amelyek az Azure Monitor által gyűjtött. 
- Küldje el az adatokat közvetlenül az Azure Monitorhttp [Data Collector API használatával.](data-collector-api.md) 

## <a name="next-steps"></a>További lépések
* Tekintse meg [a szöveges adatok elemzése az Azure Monitorban](../log-query/parse-text.md) az egyes importált naplóbejegyzések több tulajdonságba történő elemzési módszereit.
* Ismerje meg a [naplólekérdezéseket](../log-query/log-query-overview.md) az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez.
