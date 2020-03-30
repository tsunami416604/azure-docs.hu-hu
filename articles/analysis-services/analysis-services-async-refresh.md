---
title: Aszinkron frissítés az Azure Analysis Services modellekhez | Microsoft dokumentumok
description: Bemutatja, hogyan használhatja az Azure Analysis Services REST API-t a modelladatok aszinkron frissítésének kódolásához.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6457f062a40e60a491220fcf977585e8b07445b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273721"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Aszinkron frissítés a REST API-val

Bármely olyan programozási nyelv használatával, amely támogatja a REST-hívásokat, aszinkron adatfrissítési műveleteket hajthat végre az Azure Analysis Services táblázatos modelljein. Ez magában foglalja a lekérdezés horizontális felskálázásának csak olvasható kópiák szinkronizálását. 

Az adatfrissítési műveletek számos tényezőtől függően eltarthatnak, például az adatmennyiségtől, a partíciók használatával történő optimalizálás szintjétől stb. Ezeket a műveleteket hagyományosan olyan meglévő módszerekkel hívták meg, mint a [TOM](https://docs.microsoft.com/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (táblázatos objektummodell), [a PowerShell-parancsmagok](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) vagy [a TMSL](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (táblázatos modellparancsfájlnyelv). Ezek a módszerek azonban gyakran megbízhatatlan, hosszú ideig futó HTTP-kapcsolatokat igényelhetnek.

Az Azure Analysis Services REST API-ja lehetővé teszi az adatfrissítési műveletek aszinkron elvégzését. A REST API használatával az ügyfélalkalmazások hosszú ideig futó HTTP-kapcsolatok nem szükségesek. Vannak más beépített funkciók is a megbízhatóság, például az automatikus újrapróbálkozások és a kötegelt véglegesítések.

## <a name="base-url"></a>Kiindulási URL-cím

Az alap URL a következő formátumot követi:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Vegyünk például egy AdventureWorks nevű `myserver`modellt egy USA nyugati azure-régiójában található kiszolgálón. A kiszolgáló neve:

```
asazure://westus.asazure.windows.net/myserver 
```

A kiszolgálónév alap URL-címe:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Az alap URL használatával az erőforrások és műveletek a következő paraméterek alapján fűzhetők hozzá: 

![Aszinkron frissítése](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Bármi, ami **s-re** végződik, az egy gyűjtemény.
- Bármi, ami **()** végződik, függvény.
- Minden más erőforrás/objektum.

A Refreshes gyűjtemény POST műveletével például frissítheti a műveletet:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Hitelesítés

Minden hívást hitelesíteni kell egy érvényes Azure Active Directory (OAuth 2) jogkivonattal az engedélyezési fejlécben, és meg kell felelnie az alábbi követelményeknek:

- A jogkivonatnak felhasználói jogkivonatnak vagy egyszerű alkalmazásszolgáltatásnak kell lennie.
- A tokennek a megfelelő `https://*.asazure.windows.net`célközönséget kell beállítania.
- A felhasználónak vagy alkalmazásnak megfelelő engedélyekkel kell rendelkeznie a kiszolgálón vagy a modellen a kért hívás végrehajtásához. A jogosultsági szintet a modellen vagy a kiszolgálón lévő rendszergazdai csoporton belüli szerepkörök határozzák meg.

    > [!IMPORTANT]
    > Jelenleg **kiszolgálófelügyeleti** szerepkör engedélyei szükségesek.

## <a name="post-refreshes"></a>POST /frissítések

Frissítési művelet végrehajtásához a /refreshes gyűjtemény POST műveletével adjon hozzá egy új frissítési elemet a gyűjteményhez. A válasz helyfejléce tartalmazza a frissítési azonosítót. Az ügyfélalkalmazás leválaszthatja a kapcsolatot, és szükség esetén később ellenőrizheti az állapotot, mert aszinkron.

Egy modellhez egyszerre csak egy frissítési művelet et fogad el. Ha van egy aktuálisan futó frissítési művelet, és egy másik elküldésre kerül, a 409-es ütköző HTTP-állapotkódot adja vissza.

A test hasonlíthat a következő:

```
{
    "Type": "Full",
    "CommitMode": "transactional",
    "MaxParallelism": 2,
    "RetryCount": 2,
    "Objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer"
        },
        {
            "table": "DimDate"
        }
    ]
}
```

### <a name="parameters"></a>Paraméterek

Paraméterek megadása nem szükséges. A program az alapértelmezett értéket alkalmazza.

| Név             | Típus  | Leírás  |Alapértelmezett  |
|------------------|-------|--------------|---------|
| `Type`           | Felsorolás  | A végrehajtandó feldolgozás típusa. A típusok a TMSL [frissítési parancstípusokkal](https://docs.microsoft.com/analysis-services/tmsl/refresh-command-tmsl) vannak igazítva: teljes, clearValues, calculate, dataOnly, automatic és töredezettségmentesítés. A hozzáadástípusa nem támogatott.      |   Automatikus      |
| `CommitMode`     | Felsorolás  | Azt határozza meg, hogy az objektumok véglegesítése kötegekben lesz-e, vagy csak akkor, ha befejeződnek. A módok a következők: alapértelmezett, tranzakciós, részleges Batch.  |  Tranzakciós       |
| `MaxParallelism` | Int   | Ez az érték határozza meg, hogy legfeljebb hány szálon kell párhuzamosan futtatni a feldolgozási parancsokat. Ez az érték a TMSL [Sequence parancsban](https://docs.microsoft.com/analysis-services/tmsl/sequence-command-tmsl) vagy más módszerekkel beállítható MaxParallelism tulajdonsághoz igazodik.       | 10        |
| `RetryCount`     | Int   | Azt jelzi, hogy a művelet hányszor indul újra a sikertelenség előtt.      |     0    |
| `Objects`        | Tömb | Feldolgozandó objektumok tömbje. Minden objektum tartalmazza: "tábla", amikor a teljes tábla feldolgozása vagy a "tábla" és a "partíció", ha egy partíciót. Ha nincs megadva objektum, az egész modell frissül. |   A teljes modell feldolgozása      |

CommitMode egyenlő részlegesBatch. Nagy adatkészletek kezdeti betöltése kor használatos, amely órákig is eltarthat. Ha a frissítési művelet egy vagy több köteg sikeres véglegesítése után sikertelen, a sikeresen véglegesített kötegek véglegesítve maradnak (nem állítja vissza a sikeresen véglegesített kötegeket).

> [!NOTE]
> Íráskor a köteg mérete a MaxParallelism érték, de ez az érték változhat.

### <a name="status-values"></a>Állapotértékek

|Állapotérték  |Leírás  |
|---------|---------|
|`notStarted`    |   A művelet még nem kezdődött el.      |
|`inProgress`     |   A művelet folyamatban van.      |
|`timedOut`     |    A művelet időbeli eltelése a felhasználó által megadott időout alapján történt.     |
|`cancelled`     |   A műveletet a felhasználó vagy a rendszer megszakította.      |
|`failed`     |   A művelet nem sikerült.      |
|`succeeded`      |   A művelet sikerült.      |

## <a name="get-refreshesrefreshid"></a>GET /refreshs/\<refreshId>

A frissítési művelet állapotának ellenőrzéséhez használja a GET műveletet a frissítési azonosítón. Íme egy példa a választörzsre. Ha a művelet folyamatban `inProgress` van, a rendszer állapotban adja vissza.

```
{
    "startTime": "2017-12-07T02:06:57.1838734Z",
    "endTime": "2017-12-07T02:07:00.4929675Z",
    "type": "full",
    "status": "succeeded",
    "currentRefreshType": "full",
    "objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer",
            "status": "succeeded"
        },
        {
            "table": "DimDate",
            "partition": "DimDate",
            "status": "succeeded"
        }
    ]
}
```

## <a name="get-refreshes"></a>GET /refreshs

Egy modell előzményfrissítési műveleteinek listájának lekérni, használja a GET igét a /refreshes gyűjteményben. Íme egy példa a választörzsre. 

> [!NOTE]
> Íráskor a rendszer tárolja és visszaadja a frissítésutolsó 30 napjának számát, de ez a szám változhat.

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-09T01:58:04.76",
        "endTime": "2017-12-09T01:58:12.607",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T02:05:48.32",
        "endTime": "2017-12-07T02:05:54.913",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>DELETE /refreshs/\<refreshId>

Folyamatban lévő frissítési művelet megszakításához használja a DELETE műveletet a frissítési azonosítón.

## <a name="post-sync"></a>POST /sync

A frissítési műveletek végrehajtása után szükség lehet az új adatok szinkronizálására a lekérdezés horizontális felskálázásához. Egy modell szinkronizálási műveletének végrehajtásához használja a POST műveletet a /sync függvényen. A válasz helyfejléce tartalmazza a szinkronizálási művelet azonosítóját.

## <a name="get-sync-status"></a>GET /sync állapot

A szinkronizálási művelet állapotának ellenőrzéséhez használja a GET műveletet, amely paraméterként adja át a műveletazonosítót. Íme egy példa a választörzsre:

```
{
    "operationId": "cd5e16c6-6d4e-4347-86a0-762bdf5b4875",
    "database": "AdventureWorks2",
    "UpdatedAt": "2017-12-09T02:44:26.18",
    "StartedAt": "2017-12-09T02:44:20.743",
    "syncstate": 2,
    "details": null
}
```

A `syncstate`következő értékek:

- 0: Replikálás. Az adatbázisfájlok replikálása egy célmappába történik.
- 1: Rehidratáló. Az adatbázis újrahidratálódik az írásvédett kiszolgálópéldányokon.
- 2: Kész. A szinkronizálási művelet sikeresen befejeződött.
- 3: Nem sikerült. A szinkronizálási művelet nem sikerült.
- 4: Véglegesítés. A szinkronizálási művelet befejeződött, de törlési lépéseket hajt végre.

## <a name="code-sample"></a>Kódminta

Íme egy C# kódminta a kezdéshez, [RestApiSample a GitHubon.](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample)

### <a name="to-use-the-code-sample"></a>A kódminta használata

1.  Klónozza vagy töltse le a tárta. Nyissa meg a RestApiSample megoldást.
2.  Keresse meg a vonal **ügyfelet. BaseAddress = ...** és adja meg az [alap URL-t](#base-url).

A kódminta [egyszerű szolgáltatáshitelesítést](#service-principal) használ.

### <a name="service-principal"></a>Szolgáltatásnév

[Egyszerű szolgáltatás létrehozása – Az Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md) és az egyszerű szolgáltatás hozzáadása a [kiszolgálói rendszergazdai szerepkörhöz](analysis-services-addservprinc-admins.md) című témakörben további információt talál arról, hogyan állíthat be egyszerű szolgáltatást, és hogyan rendelheti hozzá a szükséges engedélyeket az Azure AS-ben. A lépések elvégzése után hajtsa végre az alábbi további lépéseket:

1.  A kódmintában keresse meg a **karakterlánc-jogosultság = ...**, cserélje le a szervezet bérlői azonosítójával. **common**
2.  Megjegyzés/megjegyzés visszavonása, hogy a ClientCredential osztály a cred objektum példányosítására szolgáljon. Győződjön \<meg arról, hogy az alkalmazásazonosító> és \<az alkalmazáskulcs> értékek biztonságos módon érhetők el, vagy használja a tanúsítványalapú hitelesítést a szolgáltatásnévi tagokhoz.
3.  Futtassa a mintát.


## <a name="see-also"></a>Lásd még

[Minták](analysis-services-samples.md)   
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)   


