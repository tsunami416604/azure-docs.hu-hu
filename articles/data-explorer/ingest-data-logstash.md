---
title: 'Gyors útmutató: Betöltési adat a Logstashből az Adatkezelőbe az Azure'
description: Ebben a rövid útmutató (betöltés) adatok betöltését az Azure Data Explorer a Logstashből
author: tamirkamara
ms.author: takamara
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 01/14/2019
ms.openlocfilehash: e0d81454e6036d09bb74af6f522063a1aed5fffe
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046410"
---
# <a name="quickstart-ingest-data-from-logstash-to-azure-data-explorer"></a>Gyors útmutató: Betöltési adat a Logstashből az Adatkezelőbe az Azure

[A Logstash](https://www.elastic.co/products/logstash) egy nyílt forráskódú, több forrásból származó adatokat fogadnak egy időben, átalakítja az adatokat, és ezután elküldi az adatokat a kívánt "stash" kiszolgálóoldali adatfeldolgozási folyamat. Ebben a rövid, küld adatokat az Azure Adatkezelőbe, ez egy gyors és hatékonyan méretezhető exploration szolgáltatás napló és a telemetriai adatok. Először hozzon létre egy tábla és a megfeleltetése egy teszt fürtben fog, és majd közvetlen Logstash adatokat küldeni a táblába, és ellenőrzik az eredményeket.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs fiókja, hozzon létre egy [ingyenes Azure-fiók](https://azure.microsoft.com/free/) megkezdése előtt.
* Az Azure Data Explorer [fürt és az adatbázis tesztelése](create-cluster-database-portal.md)
* A Logstash 6 vagy újabb [telepítési utasításokat](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html)

## <a name="create-a-table"></a>Tábla létrehozása

Miután a fürt és a egy adatbázist, ideje tábla létrehozásához.

1. Az adatbázis-lekérdezési ablak tábla létrehozásához futtassa a következő parancsot:

    ```Kusto
    .create table logs (timestamp: datetime, message: string)
    ```

1. A következő paranccsal ellenőrizheti, hogy az új tábla `logs` létrejött, ezért üresnek számít:
    ```Kusto
    logs
    | count
    ```

## <a name="create-a-mapping"></a>A hozzárendelés létrehozása

Leképezés Azure adatkezelő használják a bejövő adatok átalakítása a céloldali tábla sémáját. A következő parancs létrehoz egy új hozzárendelést nevű `basicmsg` tulajdonságok, amely kinyeri a bejövő, amint a json-ból a `path` megjeleníti őket a `column`.

A lekérdezési ablakban futtassa a következő parancsot:

```Kusto
.create table logs ingestion json mapping 'basicmsg' '[{"column":"timestamp","path":"$.@timestamp"},{"column":"message","path":"$.message"}]'
```

## <a name="install-the-logstash-output-plugin"></a>A kimeneti Logstash beépülő modul telepítése

A Logstash beépülő modul kimeneti kommunikál az Azure az adatkezelőt, és elküldi az adatokat a szolgáltatás az.
Futtassa a következő parancsot a Logstash legfelső szintű könyvtárán belül található a beépülő modul telepítéséhez:

```sh
bin/logstash-plugin install logstash-output-kusto
```

## <a name="configure-logstash-to-generate-a-sample-dataset"></a>Minta adatkészlet létrehozásához a Logstash konfigurálása

A Logstash hozhat létre, amely egy teljes körű folyamatot teszteléséhez használható minta eseményekre.
Ha már használja a Logstash és érheti el a saját eseménystream, ugorjon a következő szakaszra. 

> [!NOTE]
> A saját adatait használja, ha az előző lépések során meghatározott tábla és a leképezés objektumok módosítása

1. Egy új szöveges fájlba, amely tartalmazza a szükséges Folyamatbeállítások (vi használatával) szerkesztése:

    ```sh
    vi test.conf
    ```

1. Illessze be a következő beállításokat, amely közli a Logstash 1000 teszt eseményeket létrehozásához:

    ```ruby
    input {
        stdin { }
        generator {
            message => "Test Message 123"
            count => 1000
        }
    }
    ```

Ez a konfiguráció is magában foglalja a `stdin` bemeneti beépülő modul, amely lehetővé teszi, hogy a további üzeneteket írhat saját magának (ügyeljen arra, hogy *Enter* elküldeni őket a folyamatba).

## <a name="configure-logstash-to-send-data-to-azure-data-explorer"></a>Az adatok az Azure Data Explorer küldéséhez a Logstash konfigurálása

Illessze be a következő beállítások ugyanazt a konfigurációs fájlt az előző lépésben használt. Az összes a helyőrzőket cserélje le a megfelelő értékeket a telepítéshez. További információkért lásd: [egy AAD-alkalmazás létrehozása](/azure/kusto/management/access-control/how-to-provision-aad-app). 

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
| **elérési út** | A Logstash beépülő modul írja az eseményeket ideiglenes fájlok, mielőtt a rendszer elküldi őket az Adatkezelőbe az Azure. Ez a paraméter egy elérési utat, ahová fájlokat kell írni és a egy fájl kulcsverzióra történő aktiválása egy feltöltése az Azure Data Explorer szolgáltatásba idő kifejezést tartalmaz.|
| **ingest_url** | Az Adatbetöltési kapcsolatos kommunikáció Kusto-végpont.|
| **app_id**, **app_key**, és **app_tenant**| Azure adatkezelő való kapcsolódáshoz szükséges hitelesítő adatokat. Győződjön meg arról, betöltés jogosultsággal rendelkező alkalmazások használatára. |
| **adatbázis**| Adatbázis neve események helyezi el. |
| **tábla** | Cél táblanév események helyezi el. |
| **Leképezés** | Képezze le a bejövő események json-karakterláncot a megfelelő sor (határozza meg, melyik tulajdonság hiányzóra melyik oszlop) formátumra leképezése szolgál. |

## <a name="run-logstash"></a>Futtassa a Logstash

Most már készen állunk Logstash futtathatja és tesztelheti a beállítások.

1. A Logstash gyökérkönyvtárában futtassa a következő parancsot:

    ```sh
    bin/logstash -f test.conf
    ```

    Megtekintheti az adatok a képernyőre, és majd a minta konfigurációs által generált 1000 üzenete. Ezen a ponton, manuálisan is megadható további üzeneteket.

1. Néhány perc múlva megjelennek az üzenetek a táblázatban megadott a következő adatkezelő lekérdezés futtatásával:

    ```Kusto
    logs
    | order by timestamp desc
    ```

1. Válassza ki a Ctrl + C billentyűkombinációval kilép a Logstash

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Futtassa a következő parancsot az adatbázis karbantartása a `logs` tábla:

```Kusto
.drop table logs
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Lekérdezések írása](write-queries.md)