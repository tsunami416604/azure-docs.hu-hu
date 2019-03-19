---
title: Azure Analysis Services-modellekhez aszinkron Adatfrissítés |} A Microsoft Docs
description: Ismerje meg, hogyan lehet REST API-val aszinkron Adatfrissítés code.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5e9558eae43b351aa198b64bb2a7903c756064c2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58168017"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>A REST API-val aszinkron Adatfrissítés

Bármely más REST-hívásokat támogató programnyelv használatával az Azure Analysis Services rendszerbeli táblázatos modellek az aszinkron Adatfrissítés műveleteket hajthat végre. Ez magában foglalja a lekérdezések kiterjesztése kibővítése csak olvasható replikák szinkronizálása. 

Adatfrissítés műveletek számos tényező befolyásolja, többek között az adatmennyiség, szintű optimalizálást használatával a partíció, és így tovább függően hosszabb időt is igénybe vehet. Ezek a műveletek hagyományosan meghívása rendelkezik meglévő módszerrel például használatával [TOM](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (táblázatos objektummodell), [PowerShell](https://docs.microsoft.com/sql/analysis-services/powershell/analysis-services-powershell-reference) parancsmagokról, vagy [TMSL](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) (táblázatos modell Scripting Language). Ezek a módszerek azonban gyakran megbízhatatlan, hosszan futó HTTP-kapcsolatokat is szükséges.

A REST API az Azure Analysis Services lehetővé teszi, hogy a data-frissítési műveletek aszinkron módon kell elvégezni. A REST API-val, az ügyfélalkalmazások hosszú ideig futó HTTP-kapcsolatok nem szükséges. Megbízhatóság, például az automatikus újrapróbálkozásokat és kötegelt véglegesítéseket más beépített funkcióit is vannak.

## <a name="base-url"></a>Alap URL-cím

Az alap URL-cím ezt a formátumot követi:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Vegyük példaként egy kiszolgálón, a West US Azure-régióban található, myserver nevű AdventureWorks nevű modell. A kiszolgáló nevét a következő:

```
asazure://westus.asazure.windows.net/myserver 
```

Ez a kiszolgálónév alap URL-je:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Az alap URL-címet használ, erőforrások és műveletek fűzhető alapján a következő paraméterekkel: 

![Az aszinkron frissítése](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Bármi is, amely **s** gyűjteménye.
- Bármit végződő **()** függvénye.
- Bármi más egy erőforrás vagy objektumot.

Például használhatja a POST művelet a frissítések gyűjtemény egy frissítési művelet végrehajtásához:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Authentication

Összes hívás az engedélyezési fejléc érvényes Azure Active Directory (OAuth 2) tokennel kell hitelesíteni, és az alábbi követelményeknek kell megfelelnie:

- A token vagy egy felhasználói jogkivonat, vagy egy alkalmazás egyszerű szolgáltatást kell lennie.
- A jogkivonat rendelkeznie kell a megfelelő célközönség beállítása `https://*.asazure.windows.net`.
- A felhasználónak vagy alkalmazásnak a kért hívást a kiszolgálón vagy a modell megfelelő engedélyekkel kell rendelkeznie. A modell vagy a felügyeleti csoportnak azon a kiszolgálón lévő szerepkörök a jogosultsági szintet határozza meg.

    > [!IMPORTANT]
    > Jelenleg **kiszolgáló-rendszergazdai** szerepköri jogosultságok szükségesek.

## <a name="post-refreshes"></a>POST /refreshes

A frissítési műveletet végre, a /refreshes gyűjteményen a POST művelet használatával egy új frissítési elem hozzáadása a gyűjteményhez. A válasz Location fejlécébe tartalmazza a frissítés azonosítóját. Az ügyfélalkalmazás is válassza le, és később ellenőrizheti állapotát, ha szükséges, mert az aszinkron.

Modellhez tartozó egyszerre csak egy frissítési művelet elfogadható. Aktuális futó frissítési műveletet, és egy másik elküldve, ha a 409 ütköző HTTP-állapotkódot adott vissza.

A szervezet előfordulhat, hogy a következőhöz hasonlóan:

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

Paraméterek megadása nem kötelező. Alapértelmezés szerint a rendszer alkalmazza.

| Name (Név)             | Típus  | Leírás  |Alapértelmezett  |
|------------------|-------|--------------|---------|
| `Type`           | Enum  | A típus feldolgozástípus meghatározásához. A típusok összhangban legyenek a TMSL [a refresh parancs](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/refresh-command-tmsl) típusok: teljes, clearValues, kiszámíthatja, dataOnly, automatikus, és töredezettségének. Adja hozzá a típus nem támogatott.      |   Automatikus      |
| `CommitMode`     | Enum  | Azt határozza meg, ha objektumok lesznek véglegesítve, és kötegekben, vagy csak ha végzett. Módok közé tartozik: alapértelmezett, tranzakciós partialBatch.  |  tranzakciós       |
| `MaxParallelism` | Int   | Ez az érték a párhuzamos feldolgozás parancsok futtatására szálak maximális száma határozza meg. Ez az érték a TMSL beállítható MaxParallelism tulajdonság igazítva [parancs feladatütemezési](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/sequence-command-tmsl) vagy más módszerrel.       | 10        |
| `RetryCount`     | Int   | Azt jelzi, hogy hányszor, mielőtt hibát jelentene próbálkozik újra a műveletet.      |     0    |
| `Objects`        | Tömb | A feldolgozásra objektumok egy tömbjét. Minden objektumot tartalmazza: "table" feldolgozásakor a teljes táblázatot vagy a "table" és "partíció" partíció feldolgozása során. Ha nincs objektum meg van adva, a teljes modellt frissülnek. |   A teljes modell feldolgozása      |

CommitMode partialBatch megegyezik. Szolgál, amely során egy kezdeti betöltése a nagyméretű adathalmazok óráig is eltarthat. A frissítési művelet nem sikerül, egy vagy több köteg sikeresen véglegesítése után, ha sikeresen véglegesítve köteg véglegesített marad (ez nem állítja vissza sikeresen véglegesítve kötegek).

> [!NOTE]
> Írásának időpontjában a Köteg mérete a MaxParallelism érték, de ez az érték módosítása sikerült.

## <a name="get-refreshesrefreshid"></a>GET /refreshes/\<refreshId>

A frissítési művelet állapotának ellenőrzéséhez használja a GET-verb a frissítés azonosítóját. Íme egy példa a válasz törzse. Ha a művelet van folyamatban, **inProgress** állapotát adja vissza.

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

## <a name="get-refreshes"></a>GET /refreshes

Modellhez tartozó korábbi frissítési műveletek listáját, amelyet a GET-verb a /refreshes gyűjteményen. Íme egy példa a válasz törzse. 

> [!NOTE]
> Írásának időpontjában a frissítési műveletek az elmúlt 30 napban tárolja, és adja vissza, de ez a szám sikerült módosítani.

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

## <a name="delete-refreshesrefreshid"></a>DELETE /refreshes/\<refreshId>

Egy folyamatban lévő frissítési művelet visszavonására, használja a DELETE utasítás a frissítés azonosítóját.

## <a name="post-sync"></a>POST /sync

Adatfrissítési műveletek elvégzése, akkor lehet szükség az új adatok szinkronizálását lekérdezés horizontális felskálázás replikái. A szinkronizálási művelet egy modell használja a POST műveletet a/Sync függvény. A válasz Location fejlécébe magában foglalja a szinkronizálási művelet azonosítója.

## <a name="get-sync-status"></a>/ Sync állapotának beolvasása

A szinkronizálási művelet állapotának ellenőrzéséhez használja a GET-verb paraméterként a művelet azonosító továbbításához. Íme egy példa a válasz törzse:

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

Értékei `syncstate`:

- 0: Replikálás alatt. Adatbázisfájlok replikálva egy célmappát.
- 1: Rehidratálása. Az adatbázis csak olvasható server-példány a van folyamatban rehydrated.
- 2: Elkészült. A szinkronizálási művelet sikeresen befejeződött.
- 3: Sikertelen. A szinkronizálási művelet sikertelen volt.
- 4: Véglegesítése. A szinkronizálási művelet befejeződött, de működik-e törléshez szükséges lépéseket.

## <a name="code-sample"></a>Kódminta

Íme egy C#-kódmintát az első lépésekhez, [a Githubon RestApiSample](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>A kód a minta használata

1.  Klónozza, vagy töltse le a tárházban. Nyissa meg a RestApiSample megoldást.
2.  Keresse meg a sort **ügyfél. BaseAddress =...** Adja meg a [alap URL](#base-url).

A kódminta használhatja interaktív bejelentkezéshez, a felhasználónév/jelszó, vagy [szolgáltatásnév](#service-principal).

#### <a name="interactive-login-or-usernamepassword"></a>Interaktív bejelentkezés vagy a felhasználónév/jelszó

Az ilyen típusú hitelesítés szükséges Azure-alkalmazások hozhatók létre a szükséges API-engedélyek rendelve. 

1.  Az Azure Portalon, kattintson a **erőforrás létrehozása** > **Azure Active Directory** > **alkalmazásregisztrációk**  >   **Új alkalmazás regisztrálása**.

    ![Új alkalmazás regisztrálása](./media/analysis-services-async-refresh/aas-async-app-reg.png)


2.  A **létrehozás**, adjon meg egy nevet, válassza ki **natív** alkalmazástípus. A **átirányítási URI-t**, adja meg **urn: ietf:wg:oauth:2.0:oob**, és kattintson a **létrehozás**.

    ![Beállítások](./media/analysis-services-async-refresh/aas-async-app-reg-name.png)

3.  Válassza ki az alkalmazást majd másolja és mentse a **Alkalmazásazonosító**.

    ![Másolja az alkalmazás azonosítója](./media/analysis-services-async-refresh/aas-async-app-id.png)

4.  A **beállítások**, kattintson a **szükséges engedélyek** > **Hozzáadás**.

    ![API-hozzáférés hozzáadása](./media/analysis-services-async-refresh/aas-async-add.png)

5.  A **API kiválasztása**, típus **Azure Analysis Services** be a keresési mezőbe, majd válassza ki azt.

    ![API kiválasztása](./media/analysis-services-async-refresh/aas-async-select-api.png)

6.  Válassza ki **olvasása és írása az összes modellt**, és kattintson a **kiválasztása**. Amikor mindkét van kijelölve, kattintson a **kész** az engedélyek hozzáadásához. Propagálása néhány percig is eltarthat.

    ![Válassza ki az olvasási és az összes modellek írása](./media/analysis-services-async-refresh/aas-async-select-read.png)

7.  A kódminta, keresse meg a **UpdateToken()** metódust. Tekintse át ezt a módszert a tartalmát.
8.  Keresés **clientID karakterlánc =...** , majd adja meg a **Alkalmazásazonosító** 3. lépésben kimásolt.
9.  Futtassa a mintát.

#### <a name="service-principal"></a>Szolgáltatásnév

Lásd: [szolgáltatásnév létrehozása – Azure portal](../active-directory/develop/howto-create-service-principal-portal.md) és [szolgáltatásnév hozzáadása kiszolgálói rendszergazdai szerepkörhöz](analysis-services-addservprinc-admins.md) további információ az egyszerű szolgáltatás beállítása és a szükséges engedélyeket az Azure-AS . Miután végrehajtotta a lépéseket, hajtsa végre az alábbi kiegészítő lépéseket:

1.  A kódminta található **karakterlánc-szolgáltató =...** , cserélje le **közös** a szervezet a bérlő azonosítóját.
2.  Megjegyzés/állítsa vissza úgy az ClientCredential osztály segítségével az adatok objektumpéldány. Győződjön meg, hogy a \<Alkalmazásazonosító > és \<Alkalmazáskulcs > értékek érhetők el biztonságos módon, vagy Tanúsítványalapú hitelesítés használata az egyszerű szolgáltatások.
3.  Futtassa a mintát.


## <a name="see-also"></a>Lásd még

[Minták](analysis-services-samples.md)   
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)   


