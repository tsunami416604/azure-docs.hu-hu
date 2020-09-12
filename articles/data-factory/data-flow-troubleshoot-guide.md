---
title: Az adatfolyamatok hibáinak megoldása
description: Megtudhatja, hogyan lehet elhárítani a Azure Data Factory az adatfolyamokkal kapcsolatos problémákat.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.openlocfilehash: 6f2bf98e1c527be27ba0f08a43785ae7d3aea726
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89594151"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Azure Data Factory adatforgalmának hibáinak megoldása

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk a Azure Data Factory adatforgalmának gyakori hibaelhárítási módszereit vizsgálja.

## <a name="common-errors-and-messages"></a>Gyakori hibák és üzenetek

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Hibakód: DF-végrehajtó-SourceInvalidPayload
- **Üzenet**: az adatelőnézet, a hibakeresés és a folyamat adatforgalmának végrehajtása nem sikerült, mert a tároló nem létezik
- **Okok**: Ha az adatkészlet olyan tárolót tartalmaz, amely nem létezik a tárolóban
- **Javaslat**: Ellenőrizze, hogy az adatkészletben hivatkozott tároló létezik vagy elérhető-e.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Hibakód: DF-végrehajtó-SystemImplicitCartesian

- **Üzenet**: az implicit Descartes-termék belső illesztéshez nem támogatott, használja a Cross JOIN parancsot. Az illesztésben használt oszlopoknak egyedi kulcsot kell létrehozniuk a sorokhoz.
- **OK**: a logikai csomagok közötti belső illesztéshez implicit Descartes termék nem támogatott. Ha az illesztésben használt oszlopok létrehozzák az egyedi kulcsot, a kapcsolat mindkét oldalán legalább egy oszlopot kötelező megadni.
- **Javaslat**: a nem egyenlőségen alapuló összekapcsolásokhoz egyéni kereszt-illesztést kell választania.

### <a name="error-code-df-executor-systeminvalidjson"></a>Hibakód: DF-végrehajtó-SystemInvalidJson

- **Üzenet**: JSON-elemzési hiba, nem támogatott kódolás vagy többsoros
- **OK**: a JSON-fájllal kapcsolatos lehetséges problémák: nem támogatott kódolás, sérült bájtok vagy JSON-forrás használata több beágyazott sorban lévő egyetlen dokumentumként
- **Javaslat**: Ellenőrizze, hogy a JSON-fájl kódolása támogatott-e. A JSON-adatkészletet használó forrás-átalakításnál bontsa ki a "JSON-beállítások" elemet, és kapcsolja be az "egyetlen dokumentum" beállítást.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Hibakód: DF-végrehajtó-BroadcastTimeout

- **Üzenet**: szórásos csatlakozás időtúllépési hibája, győződjön meg arról, hogy a szórásos adatfolyam 60 másodpercen belül, hibakeresési futtatások és 300 a feladat futtatásakor
- **OK**: a szórás a hibakeresési és a 300-es mp-es alapértelmezett időkorlátot (60 mp) tartalmaz a feladatok futtatásához. Úgy tűnik, hogy a szórásra kiválasztott stream nagy mennyiségű adatmennyiséget hoz létre ezen a korláton belül.
- **Javaslat**: a csatlakozáshoz, a létezéshez és a kereséshez tekintse meg az optimalizálás lapot az adatfolyam-transzformációk lapon. A szórás alapértelmezett beállítása az "Auto". Ha ez be van állítva, vagy ha manuálisan állítja be a bal vagy a jobb oldali szórást a "rögzített" érték alatt, akkor beállíthat nagyobb Azure Integration Runtime konfigurációt, vagy kikapcsolhatja a szórást. Az Adatáramlások legjobb teljesítményének ajánlott megközelítése, hogy a Spark az "Auto" használatával közvetítse az "automatikus", és használja a memóriára optimalizált Azure IR.

Ha hibakeresési folyamat futása során hajtja végre az adatáramlást hibakeresési teszt végrehajtása során, akkor gyakrabban futtathatja ezt az állapotot. Ennek az az oka, hogy az ADF leszabályozza a szórási időtúllépést 60 mp-re a gyorsabb hibakeresési élmény fenntartása érdekében. Ha azt szeretné, hogy a rendszer kiterjessze a kiváltott futtatások 300 mp-s időtúllépését, használja a Debug > a tevékenység-futtatókörnyezet használata lehetőséget, hogy kihasználja az adatáramlási folyamat végrehajtása tevékenységben definiált Azure IR.

### <a name="error-code-df-executor-conversion"></a>Hibakód: DF-végrehajtó – konverzió

- **Üzenet**: egy érvénytelen karakter miatt nem sikerült a dátumra vagy időpontra konvertálása.
- **Okok**: az adatértékek nem a várt formátumban vannak
- **Javaslat**: használja a megfelelő adattípust

### <a name="error-code-df-executor-invalidcolumn"></a>Hibakód: DF-végrehajtó-InvalidColumn

- **Üzenet**: az oszlop nevét meg kell adni a lekérdezésben, egy aliast kell beállítania, ha SQL-függvényt használ
- **Okok**: nincs megadva oszlopnév
- **Javaslat**: alias beállítása, ha olyan SQL-függvényt használ, mint a min ()/Max (), stb.

### <a name="error-code-getcommand-outputasync-failed"></a>Hibakód: a GetCommand OutputAsync sikertelen

- **Üzenet**: az adatfolyam hibakeresése és az adatelőnézet: a GetCommand OutputAsync sikertelen volt...
- **Okok**: ez egy háttér-szolgáltatási hiba. Próbálja megismételni a műveletet, és indítsa újra a hibakeresési munkamenetet.
- **Javaslat**: Ha az Újrapróbálkozás és az újraindítás nem oldja meg a problémát, forduljon az ügyfél-támogatási szolgálathoz.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>Hibakód: váratlan kivétel történt, és a végrehajtás nem sikerült

- **Üzenet**: az adatfolyam tevékenységének végrehajtása során: váratlan kivétel történt, és a végrehajtás nem sikerült.
- **Okok**: ez egy háttér-szolgáltatási hiba. Próbálja megismételni a műveletet, és indítsa újra a hibakeresési munkamenetet.
- **Javaslat**: Ha az Újrapróbálkozás és az újraindítás nem oldja meg a problémát, forduljon az ügyfél-támogatási szolgálathoz.

### <a name="error-code-debug-data-preview-no-output-data-on-join"></a>Hibakód: az adatelőnézet hibakeresése kimeneti adatokat nem a csatlakozáskor

- **Üzenet**: nagy számú Null érték vagy hiányzó érték áll rendelkezésre, amelyek oka lehet a túl kevés sor mintavételének. Próbálja meg frissíteni a hibakeresési sor korlátját, és frissítse az adatforrást.
- **Okok**: az illesztési feltétel nem felelt meg egyetlen sornak sem, vagy nagy MENNYISÉGű nullát eredményezett az adatelőnézet során.
- **Javaslat**: lépjen a hibakeresési beállítások menüpontra, és növelje a sorok számát a Sorforrás korlátján belül. Győződjön meg róla, hogy kiválasztott és Azure IR egy nagy mennyiségű adatfolyam-fürtöt az adatkezeléshez.


## <a name="general-troubleshooting-guidance"></a>Általános hibaelhárítási útmutató

1. Keresse meg az adatkészlet kapcsolatainak állapotát. Minden forrás-és fogadó-átalakításban keresse fel a társított szolgáltatást minden Ön által használt adatkészlet esetében, és tesztelje a kapcsolatokat.
1. Győződjön meg róla, hogy a fájl és a tábla kapcsolatainak állapota az adatfolyam-tervezőben található. Kapcsolja be a hibakeresést, és kattintson az adatelőnézetre a forrás-átalakításokban, és győződjön meg arról, hogy képes hozzáférni az adataihoz.
1. Ha minden jól látható az adatok előnézetében, ugorjon a folyamat-tervezőbe, és helyezze át az adatfolyamatot egy folyamat tevékenységbe. Egy végpontok közötti teszt folyamatának hibakeresése.

## <a name="next-steps"></a>Következő lépések

További hibaelhárítási segítségért próbálja ki ezeket az erőforrásokat:
*  [Data Factory blog](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/videos)
*  [Microsoft Q&egy kérdés oldal](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Data Factory Stack Overflow fóruma](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Az ADF-leképezés adatforgalmának teljesítmény-útmutatója](concepts-data-flow-performance.md)
