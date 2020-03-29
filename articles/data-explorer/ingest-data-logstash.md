---
title: Adatok betöltése a Logstash szolgáltatásból az Azure Data Explorerbe
description: Ebben a cikkben megtudhatja, hogyan töltheti be (töltheti be) az adatokat az Azure Data Explorerbe a Logstash szolgáltatásból
author: tamirkamara
ms.author: takamara
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 86f6732cbf2409d3c79a3d7709100e8af24988a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66494543"
---
# <a name="ingest-data-from-logstash-to-azure-data-explorer"></a>Adatok betöltése a Logstash szolgáltatásból az Azure Data Explorerbe

[Logstash](https://www.elastic.co/products/logstash) egy nyílt forráskódú, kiszolgáló oldali adatfeldolgozási folyamat, amely betöltése adatokat több forrásból egyszerre, átalakítja az adatokat, majd elküldi az adatokat a kedvenc "rejtjük". Ebben a cikkben elküldi ezeket az adatokat az Azure Data Explorer, amely egy gyors és jól méretezhető adatfeltárási szolgáltatás napló- és telemetriai adatok. Először hozzon létre egy táblát és adatleképezést egy tesztfürtben, majd irányítsa a Logstash-ot, hogy adatokat küldjön a táblába, és ellenőrizze az eredményeket.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik ilyen, hozzon létre egy [ingyenes Azure-fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
* Az Azure Data Explorer [tesztfürtje és adatbázisa](create-cluster-database-portal.md)
* Logstash 6+-os verzió+ [Telepítési utasítások](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html)

## <a name="create-a-table"></a>Tábla létrehozása

Miután rendelkezik egy fürttel és egy adatbázissal, itt az ideje, hogy hozzon létre egy táblát.

1. Tábla létrehozásához futtassa a következő parancsot az adatbázis-lekérdezési ablakban:

    ```Kusto
    .create table logs (timestamp: datetime, message: string)
    ```

1. Futtassa a következő parancsot `logs` annak ellenőrzéséhez, hogy az új tábla létrejött-e, és hogy üres-e:
    ```Kusto
    logs
    | count
    ```

## <a name="create-a-mapping"></a>Leképezés létrehozása

Az Azure Data Explorer által használt leképezés a bejövő adatok céltábla sémájává alakításához. A következő parancs létrehoz `basicmsg` egy új leképezés nevű, amely kinyeri `path` a tulajdonságokat `column`a bejövő json által megjegyezte, és kiadja őket a .

Futtassa a következő parancsot a lekérdezésablakban:

```Kusto
.create table logs ingestion json mapping 'basicmsg' '[{"column":"timestamp","path":"$.@timestamp"},{"column":"message","path":"$.message"}]'
```

## <a name="install-the-logstash-output-plugin"></a>A Logstash kimeneti bővítmény telepítése

A Logstash kimeneti beépülő modul kommunikál az Azure Data Explorerrel, és elküldi az adatokat a szolgáltatásnak.
Futtassa a következő parancsot a Logstash gyökérkönyvtárában a bővítmény telepítéséhez:

```sh
bin/logstash-plugin install logstash-output-kusto
```

## <a name="configure-logstash-to-generate-a-sample-dataset"></a>A Logstash konfigurálása mintaadatkészlet létrehozásához

A Logstash létrehozhat mintaeseményeket, amelyek egy végpontok között futó folyamat tesztelésére használhatók.
Ha már használja a Logstash-ot, és hozzáférhet a saját eseményfolyamához, ugorjon a következő szakaszra. 

> [!NOTE]
> Ha saját adatokat használ, módosítsa az előző lépésekben definiált táblát és leképezési objektumokat.

1. A szükséges folyamatbeállításokat tartalmazó új szövegfájl szerkesztése (vi használatával):

    ```sh
    vi test.conf
    ```

1. Illessze be a következő beállításokat, amelyek 1000 tesztesemény létrehozásához megmondja a Logstash-nak:

    ```ruby
    input {
        stdin { }
        generator {
            message => "Test Message 123"
            count => 1000
        }
    }
    ```

Ez a konfiguráció `stdin` tartalmazza a bemeneti plugint is, amely lehetővé teszi, hogy több üzenetet írjon egyedül (ügyeljen arra, hogy az *Enter* használatával küldje el őket a folyamatba).

## <a name="configure-logstash-to-send-data-to-azure-data-explorer"></a>A Logstash konfigurálása adatok küldéséhez az Azure Data Explorer nek

Illessze be a következő beállításokat az előző lépésben használt konfigurációs fájlba. Cserélje le az összes helyőrzőt a beállításhoz tartozó értékekre. További információt az [AAD-alkalmazás létrehozása](/azure/kusto/management/access-control/how-to-provision-aad-app)című témakörben talál. 

```ruby
output {
    kusto {
            path => "/tmp/kusto/%{+YYYY-MM-dd-HH-mm-ss}.txt"
            ingest_url => "https://ingest-<cluster name>.kusto.windows.net/"
            app_id => "<application id>"
            app_key => "<application key/secret>"
            app_tenant => "<tenant id>"
            database => "<database name>"
            table => "<target table>" # logs as defined above
            mapping => "<mapping name>" # basicmsg as defined above
    }
}
```

| Paraméter neve | Leírás |
| --- | --- |
| **Elérési út** | A Logstash beépülő modul az eseményeket ideiglenes fájlokba írja, mielőtt elküldené őket az Azure Data Explorerbe. Ez a paraméter tartalmaz egy elérési utat, ahol a fájlokat kell írni, és egy időkifejezés a fájl rotációs az Azure Data Explorer szolgáltatásba való feltöltés aktiválásához.|
| **ingest_url** | A Kusto végpont a benyeléssel kapcsolatos kommunikációhoz.|
| **app_id,** **app_key**és **app_tenant**| Az Azure Data Explorerhez való csatlakozáshoz szükséges hitelesítő adatok. Ügyeljen arra, hogy egy alkalmazás betöltési jogosultságokkal. |
| **Adatbázis**| Az események helyéhez adatbázis neve. |
| **Táblázat** | A céltábla neve események et helyezhet el. |
| **Hozzárendelés** | A leképezés a bejövő esemény json karakterláncának a megfelelő sorformátumba történő leképezésére szolgál (azt határozza meg, hogy melyik tulajdonság melyik oszlopba kerüljön). |

## <a name="run-logstash"></a>Logstash futtatása

Most már készen állunk a Logstash futtatására és a beállítások tesztelésére.

1. A Logstash gyökérkönyvtárában futtassa a következő parancsot:

    ```sh
    bin/logstash -f test.conf
    ```

    Meg kell jelennie a képernyőre nyomtatott információknak, majd a mintakonfigurációnk által generált 1000 üzenetnek. Ezen a ponton manuálisan is megadhat több üzenetet.

1. Néhány perc múlva futtassa a következő Data Explorer lekérdezést a megadott táblában lévő üzenetek megtekintéséhez:

    ```Kusto
    logs
    | order by timestamp desc
    ```

1. A Logstash ból való kilépéshez válassza a Ctrl+C billentyűkombinációt

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A tábla karbantartásához futtassa `logs` a következő parancsot az adatbázisban:

```Kusto
.drop table logs
```

## <a name="next-steps"></a>További lépések

* [Lekérdezések írása](write-queries.md)