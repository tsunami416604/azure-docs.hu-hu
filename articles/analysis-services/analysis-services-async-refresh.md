---
title: Az Azure Analysis Services modellek aszinkron frissítési |} Microsoft Docs
description: 'Útmutató: az aszinkron frissítési kód REST API használatával.'
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: d1862c5ed83033eb8de74459f26260864c646dfa
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>A REST API aszinkron frissítés
Bármely programozási nyelv, amely támogatja a REST-hívások segítségével az Azure Analysis Services rendszerbeli táblázatos modellek aszinkron adatfrissítési műveleteket végezheti el. Ez magában foglalja a lekérdezés kibővített írásvédett replikák szinkronizálását. 

Adatfrissítési műveletek eltarthat egy ideig, attól függően, hogy számos tényező, beleértve az adatmennyiség, szint használatával, partíciók, stb. Ezek a műveletek hagyományosan meghívott rendelkezik meglévő módszerrel például használatával [TOM](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (táblázatos objektummodell), [PowerShell](https://docs.microsoft.com/sql/analysis-services/powershell/analysis-services-powershell-reference) parancsmagok vagy [TMSL](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) (táblázatos modell Scripting Language). Ezek a módszerek azonban gyakran nem megbízható, hosszan futó HTTP-kapcsolatok lehet szükség.

Az Azure Analysis Services REST API lehetővé teszi, hogy az Adatfrissítés műveletek aszinkron módon kell elvégezni. A REST API használatával az ügyfélalkalmazásokból hosszan futó HTTP-kapcsolatoknál nem szükséges. Megbízhatóság, automatikus újrapróbálkozások és kötegelt véglegesítések például más beépített szolgáltatásai is vannak.

## <a name="base-url"></a>Alap URL-cím

Az alap URL-cím a következő formát követi:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Vegyük példaként a myserver nyugati Velünk Azure régióban nevű kiszolgálóra AdventureWorks nevű modell. A kiszolgáló nevének megadása:

```
asazure://westus.asazure.windows.net/myserver 
```

Ez a kiszolgálónév alap URL-je:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Az alap URL-cím segítségével, erőforrások és a műveletek is hozzáfűzi a következő paraméterek alapján: 

![Aszinkron frissítése](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Bármi, amely **s** gyűjteménye.
- Minden végződik **()** függvénye.
- Bármi más egy/objektum.

Például használhatja a FELADÁS egy vagy több műveletet a frissítések gyűjteményen a frissítési művelet végrehajtása:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Hitelesítés

Az összes hívás hitelesítési fejlécéhez érvényes Azure Active Directoryban (OAuth 2) token hitelesíteni kell, és az alábbi követelményeknek kell megfelelniük:

- A token kell lennie, vagy egy felhasználói jogkivonat, vagy egy alkalmazás egyszerű.
- A token rendelkeznie kell a megfelelő célközönség beállítása `https://*.asazure.windows.net`.
- A felhasználó vagy alkalmazás a kért híváshoz azon a kiszolgálón vagy a modell megfelelő engedélyekkel kell rendelkeznie. A modell vagy a kiszolgálón a felügyeleti csoport szerepkörét a jogosultsági szint határozza meg.

    > [!IMPORTANT]
    > Jelenleg **kiszolgálói rendszergazda** szerepköri jogosultságok szükségesek.

## <a name="post-refreshes"></a>POST /refreshes

A frissítési műveletet végrehajtani, használja a POST műveletet a /refreshes gyűjtemény egy új frissítési elem hozzáadása a gyűjteményhez. A hely egy fejléc a következő a válasz tartalmazza a frissítés azonosítóját. Az ügyfélalkalmazás válassza le, és később ellenőrizze az állapotát, ha szükséges, mert az aszinkron.

A modell egyszerre csak egy frissítési művelet elfogadható. Ha az aktuális futó frissítési műveletet, és egy másik nyújtják, a 409 ütközés HTTP-állapotkód ad vissza.

A szervezet előfordulhat, hogy az alábbiakhoz:

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
Paraméterek megadása nem kötelező. Az alapértelmezett vonatkozik.

|Name (Név)  |Típus  |Leírás  |Alapértelmezett  |
|---------|---------|---------|---------|
|Típus     |  Enum       |  A végrehajtandó feldolgozástípust típusa. A típusok összhangban legyenek a TMSL [a frissítési parancs](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/refresh-command-tmsl) típusok: full, clearValues, kiszámításához, dataOnly, automatikus adja hozzá, és töredezettségmentesítése.       |   Automatikus      |
|CommitMode     |  Enum       |  Határozza meg, ha objektumok véglegesített kötegekben telepítse, vagy csak akkor, ha teljes lesz. Módok közé tartozik: alapértelmezés szerint tranzakciós, partialBatch.  |  tranzakciós       |
|MaxParallelism     |   Int      |  A párhuzamos feldolgozás parancsok futtatására szálak maximális számát határozza meg. Ez az érték a MaxParallelism tulajdonsággal állítható be a TMSL igazítva [parancs feladatütemezési](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/sequence-command-tmsl) vagy más módszerrel.       | 10        |
|a retryCount    |    Int     |   Azt jelzi, hogy hányszor, mielőtt hibát jelentene próbálkozik újra a műveletet.      |     0    |
|Objektumok     |   Tömb      |   Feldolgozandó objektumokból álló tömb. Minden objektum tartalmaz: "table" feldolgozásakor a teljes táblázat vagy "table" és "partíció" partíció feldolgozása során. Ha egyetlen objektum meg van adva, a teljes modell frissül. |   A teljes modell folyamat      |

CommitMode partialBatch megegyezik. Szolgál, amely során egy kezdeti betöltés nagy adatkészletek óráig is eltarthat. Ha egy vagy több kötegek sikeres végrehajtása után a frissítési művelet sikertelen lesz, a sikeresen véglegesítve lett véglegesített marad (ez nem állítja vissza sikeresen véglegesítve kötegek).

> [!NOTE]
> Írásának időpontjában a Köteg mérete MaxParallelism értékét, de ez az érték módosítása sikerült.

## <a name="get-refreshesrefreshid"></a>GET /refreshes/\<refreshId >

A frissítési művelet állapotának ellenőrzéséhez használja a GET-műveletet a frissítés azonosítóját. Íme egy példa az adott válasz törzse. Ha a művelet van folyamatban, **esetbejegyzések** állapot ad vissza.

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

## <a name="get-refreshes"></a>/Refreshes beolvasása

Egy modell korábbi frissítési műveletek listájának megtekintéséhez használja a GET-verb a /refreshes gyűjteményen. Íme egy példa az adott válasz törzse. 

> [!NOTE]
> Írásának időpontjában az elmúlt 30 napban frissítési műveletek tárolása és adott vissza, de ez a szám volt módosítani.

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

## <a name="delete-refreshesrefreshid"></a>TÖRLÉS /refreshes/\<refreshId >

Egy folyamatban lévő frissítés megszakítására használja a DELETE művelet a frissítés azonosítóját.

## <a name="post-sync"></a>POST/Sync

Kellene végrehajtani a frissítési műveletek, akkor szükség lehet az új adatok szinkronizálása a lekérdezés kibővített replikáit. Egy modell synchronize művelet elvégzésére, használja a/Sync függvény a POST műveletet. A hely egy fejléc a következő a válasz tartalmazza a szinkronizálási művelet azonosítója.

## <a name="get-sync-status"></a>/ Sync állapotának beolvasása

A szinkronizálási művelet állapotának ellenőrzéséhez használja a GET műveletet, a művelet azonosító továbbításához paraméterként. Íme egy példa az adott válasz törzse:

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

- 0: végez replikációt. Adatbázis replikál a célmappába.
- 1: rehidratálása. Az adatbázis csak olvasható server-példányokat, a rehydrated van folyamatban.
- 2: befejeződött. A szinkronizálási művelet sikeresen befejeződött.
- 3: nem sikerült. A szinkronizálás nem sikerült.
- 4: véglegesítése. A szinkronizálási művelet befejeződött, de karbantartási lépést hajt végre.

## <a name="code-sample"></a>Kódminta

Ez a C# példakód az első lépésekhez, [a Githubon RestApiSample](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>A kód a minta használata

1.  Klónozza, vagy töltse le a tárházban. Nyissa meg a RestApiSample megoldást.
2.  A sorban található **ügyfél. A BaseAddress =...** és adja meg a [alap URL](#base-url).

A kódminta használható interaktív bejelentkezés, a felhasználónév/jelszó, vagy [egyszerű](#service-principal).

#### <a name="interactive-login-or-usernamepassword"></a>Interaktív bejelentkezés vagy felhasználónév és jelszó

Az űrlap-hitelesítés szükséges, az Azure-alkalmazások hozható létre a szükséges API jogosultságait. 

1.  Az Azure portálon kattintson **hozzon létre egy erőforrást** > **Azure Active Directory** > **App regisztrációk**  >   **Új alkalmazás regisztrációja**.

    ![Új alkalmazás regisztrációja](./media/analysis-services-async-refresh/aas-async-app-reg.png)


2.  A **létrehozása**, adjon meg egy nevet, válassza ki **natív** alkalmazás típusa. A **átirányítási URI-**, adja meg **urn: ietf:wg:oauth:2.0:oob**, és kattintson a **létrehozása**.

    ![Beállítások](./media/analysis-services-async-refresh/aas-async-app-reg-name.png)

3.  Válassza ki az alkalmazást majd másolja ki és mentse a **Alkalmazásazonosító**.

    ![Másolja át az Alkalmazásazonosítót](./media/analysis-services-async-refresh/aas-async-app-id.png)

4.  A **beállítások**, kattintson a **szükséges engedélyek** > **Hozzáadás**.

    ![API-hozzáférés hozzáadása](./media/analysis-services-async-refresh/aas-async-add.png)

5.  A **API kiválasztása**, típus **Azure Analysis Services** azokat a keresési mezőbe, majd állítsa be azt.

    ![API kiválasztása](./media/analysis-services-async-refresh/aas-async-select-api.png)

6.  Válassza ki **olvasása és írása az összes modell**, és kattintson a **válasszon**. Ha mindkettő be van jelölve, kattintson **végzett** a engedélyeket hozzáadni. Propagálása néhány percig is eltarthat.

    ![Válassza ki az olvasási és minden modellek írása](./media/analysis-services-async-refresh/aas-async-select-read.png)

7.  Ebben a kódmintában keresse a **UpdateToken()** metódust. Figyelje meg ezt a módszert a tartalmát.
8.  Található **clientID karakterlánc =...** , majd adja meg a **Alkalmazásazonosító** 3. lépésben másolt.
9.  Futtassa a mintát.

#### <a name="service-principal"></a>Szolgáltatásnév

Lásd: [szolgáltatásnév - Azure-portálon hozzon létre](../azure-resource-manager/resource-group-create-service-principal-portal.md) és [egyszerű szolgáltatás hozzáadása a kiszolgáló-rendszergazdai szerepkör](analysis-services-addservprinc-admins.md) állítson be egy egyszerű szolgáltatást, és rendelje hozzá a szükséges engedélyekkel, az Azure-AS módjáról további információk . Miután végrehajtotta a lépéseket, hajtsa végre az alábbi kiegészítő lépéseket:

1.  Ebben a kódmintában keresse **karakterlánc-szolgáltató =...** , cserélje le **közös** a szervezet bérlőazonosítóra azonosítóját.
2.  Megjegyzés/állítsa vissza, a SecurityMode osztály szolgál az adatok objektumpéldány. Győződjön meg arról a \<Alkalmazásazonosító > és \<Alkalmazáskulcs > biztonságos módon érik el vagy az szolgáltatásnevekről tanúsítvány alapú hitelesítést használ.
3.  Futtassa a mintát.


## <a name="see-also"></a>Lásd még

[Minták](analysis-services-samples.md)   
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)   


