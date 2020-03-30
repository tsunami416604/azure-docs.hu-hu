---
title: Oktatóanyag – ACR-feladat ütemezése
description: Ebből az oktatóanyagból megtudhatja, hogyan futtathat azure container registry feladatot egy vagy több időzítőeseményindítójának beállításával.
ms.topic: article
ms.date: 06/27/2019
ms.openlocfilehash: 3202b5d8c426165d81129f1affa69b3a3d515ce9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402879"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>ACR-feladat futtatása meghatározott ütemezés szerint

Ez az oktatóanyag bemutatja, hogyan futtathatja az [ACR-feladatot](container-registry-tasks-overview.md) ütemezés szerint. Feladat ütemezése egy vagy több *időzítőesemény beállításával.* Időzítő eseményindítók lehet használni egyedül, vagy más feladat eseményindítók kombinálva.

Ebben az oktatóanyagban ismerje meg a feladatok ütemezését és a következőket:

> [!div class="checklist"]
> * Feladat létrehozása időzítő-eseményindítóval
> * Időzítő-eseményindítók kezelése

A feladatok ütemezése a következő esetekben hasznos:

* Tároló számítási feladat futtatása ütemezett karbantartási műveletekhez. Futtasson például egy tárolóba adott alkalmazást, hogy távolítsa el a szükségtelen lemezképeket a rendszerleíró adatbázisból.
* Futtasson egy tesztkészletet egy éles lemezképen a munkanap során az élő webhely figyelésének részeként.

Használhatja az Azure Cloud Shell vagy az Azure CLI helyi telepítését a cikkben szereplő példák futtatásához. Ha helyileg szeretné használni, a 2.0.68-as vagy újabb verzió szükséges. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].


## <a name="about-scheduling-a-task"></a>Feladat ütemezése

* **Trigger cron kifejezéssel** - Egy feladat időzítőeseménye *cron kifejezést*használ. A kifejezés egy öt mezőből álló karakterlánc, amely a tevékenység elindításához a percet, az órát, a napot, a hónapot és a hét napját adja meg. Percenként legfeljebb egyszer támogatott frekvenciák.

  A kifejezés `"0 12 * * Mon-Fri"` például minden hétköznap délben utc-kor indít el egy feladatot. A cikk későbbi részében [további részleteket](#cron-expressions) olvashat.
* **Több időzítő eseményindítók** - Több időzítő hozzáadása egy feladathoz megengedett, mindaddig, amíg az ütemezések eltérnek.
    * A feladat létrehozásakor adjon meg több időzítőeseményindítót, vagy adja hozzá őket később.
    * Szükség esetén nevezze el az eseményindítókat a könnyebb kezelés érdekében, különben az ACR-feladatok alapértelmezett eseményindítóneveket adnak meg.
    * Ha az időzítő ütemezése átfedésben van egy időben, az ACR-feladatok az egyes időzítők ütemezett időpontban indítja el a feladatot.
* **Egyéb feladateseményindítók** – Időzítő által aktivált feladatesetén engedélyezheti a [forráskód véglegesítése](container-registry-tutorial-build-task.md) vagy [az alapképfrissítések](container-registry-tutorial-base-image-update.md)alapján is. Más ACR-feladatokhoz hasonlóan manuálisan is [elindíthat][az-acr-task-run] egy ütemezett feladatot.

## <a name="create-a-task-with-a-timer-trigger"></a>Feladat létrehozása időzítő-eseményindítóval

Amikor létrehoz egy feladatot az [az acr feladat létrehozása][az-acr-task-create] paranccsal, szükség esetén hozzáadhat egy időzítő eseményindítót. Adja `--schedule` hozzá a paramétert, és adja át az időzítő cron kifejezését.

Egyszerű példaként a következő parancs elindítja a rendszerképet a Docker Hubminden nap 21:00 UTC.In example, the following command triggers running the `hello-world` image from Docker Hub every day at 21:00 UTC. A feladat forráskód-környezet nélkül fut.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --cmd hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Futtassa az [az acr feladatshow][az-acr-task-show] parancsot, és tekintse meg, hogy az időzítő eseményindító konfigurálva van.Run the the acr task show command to see that the timer trigger is configured. Alapértelmezés szerint az alapkép frissítési eseményindítója is engedélyezve van.

```azurecli
az acr task show --name mytask --registry registry --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

Indítsa el a feladatot manuálisan [az acr feladat futtatásával,][az-acr-task-run] hogy megbizonyosodjon arról, hogy megfelelően van beállítva:

```azurecli
az acr task run --name mytask --registry myregistry
```

Ha a tároló sikeresen fut, a kimenet hasonló a következőhöz:

```output
Queued a run with ID: cf2a
Waiting for an agent...
2019/06/28 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2019/06/28 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2019/06/28 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

Az ütemezett időpont után futtassa az [az acr feladatlista futtatása][az-acr-task-list-runs] parancsot annak ellenőrzéséhez, hogy az időzítő a várt módon indította-e el a feladatot:

```azurecli
az acr task list-runs --name mytask --registry myregistry --output table
```

Ha az időzítő sikeres, a kimenet hasonló a következőhöz:

```output
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Időzítő-eseményindítók kezelése

Az [az acr feladatidőzítő][az-acr-task-timer] parancsaival kezelheti az ACR-feladat időzítőeseményeit.

### <a name="add-or-update-a-timer-trigger"></a>Időzítőesemény-eseményindító hozzáadása vagy frissítése

A feladat létrehozása után szükség esetén adjon hozzá egy időzítő-eseményindítót az [az acr feladatidőzítő hozzáadása][az-acr-task-timer-add] parancs használatával. A következő példa egy időzítő eseményindító név *időzítő2-t* ad hozzá a korábban létrehozott *mytask-hez.* Ez az időzítő minden nap 10:30-kor indítja el a feladatot.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Frissítse egy meglévő eseményindító ütemezését, vagy módosítsa annak állapotát az [az acr feladatidőzítő frissítési][az-acr-task-timer-update] parancsával. Frissítse például az *időzítő2* nevű eseményindítót, hogy a feladat 11:30-kor (UTC) aktiválódjon:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Időzítő-eseményindítók listázása

Az [az acr feladatidőzítő lista][az-acr-task-timer-list] parancs a feladathoz beállított időzítő-eseményindítókat jeleníti meg:

```azurecli
az acr task timer list --name mytask --registry myregistry
```

Példa a kimenetre:

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>Időzítő eseményindítójának eltávolítása

Használja az [az acr feladat időzítő eltávolítása][az-acr-task-timer-remove] parancsot, hogy távolítsa el az időzítő eseményindító egy feladatból. A következő példa eltávolítja a *timer2* eseményindítót a *mytask-ből:*

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron kifejezések

Az ACR-feladatok az [NCronTab](https://github.com/atifaziz/NCrontab) függvénytárat használja a cron-kifejezések értelmezéséhez. Az ACR-feladatok támogatott kifejezései öt kötelező mezőt tartalmaznak, amelyeket szóközök választanak el:

`{minute} {hour} {day} {month} {day-of-week}`

A cron kifejezésekhez használt időzóna az egyezményes világidő (UTC). Az órák 24 órás formátumban vannak.

> [!NOTE]
> Az ACR-feladatok `{second}` nem `{year}` támogatják a cron kifejezések mezőjét vagy mezőjét. Ha egy másik rendszerben használt cron kifejezést másol, mindenképpen távolítsa el ezeket a mezőket, ha azokat használja.

Minden mező a következő típusú értékek egyikével rendelkezhet:

|Típus  |Példa  |Aktiváláskor  |
|---------|---------|---------|
|Egy adott érték |<nobr>`"5 * * * *"`</nobr>|óránként 5 perccel az óra után|
|Minden érték`*`( )|<nobr>`"* 5 * * *"`</nobr>|minden percaz óra elején 5:00 UTC (60-szor egy nap)|
|A tartomány`-` ( operátor)|<nobr>`"0 1-3 * * *"`</nobr>|Naponta 3 alkalommal, 1:00, 2:00 és 3:00 UTC|
|Értékhalmaz (`,` operátor)|<nobr>`"20,30,40 * * * *"`</nobr>|3 alkalommal óránként, 20 perc, 30 perc és 40 perccel az óra után|
|Intervallumérték (`/` operátor)|<nobr>`"*/10 * * * *"`</nobr>|6-szor óránként, 10 perc, 20 perc, és így tovább, az óra után

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron példák

|Példa|Aktiváláskor  |
|---------|---------|
|`"*/5 * * * *"`|ötpercenként egyszer|
|`"0 * * * *"`|óránként egyszer|
|`"0 */2 * * *"`|kétóránként egyszer|
|`"0 9-17 * * *"`|óránként egyszer 9:00 és 17:00 óra között (UTC)|
|`"30 9 * * *"`|at 9:30 UTC minden nap|
|`"30 9 * * 1-5"`|-on 9:30 UTC mind hétköznap|
|`"30 9 * Jan Mon"`|at 9:30 UTC minden hétfőn januárban|

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag-sorozatban létrehozott összes erőforrás eltávolításához, beleértve a tárolóbeállításjegyzéket vagy a beállításjegyzékeket, a tárolópéldányt, a kulcstárolót és az egyszerű szolgáltatást, adja ki a következő parancsokat:

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre Azure Container Registry feladatokat, amelyeket automatikusan egy időzítő indít el. 

Ha például egy ütemezett feladatot használ a rendszerleíró adatbázis ban lévő adattárak karbantartásához, olvassa el a [Lemezképek automatikus törlése az Azure-tároló beállításjegyzékéből című témakört.](container-registry-auto-purge.md)

Példák a forráskód véglegesítése vagy az alapképfrissítések által kiváltott feladatokra az [ACR-feladatok oktatóanyag-sorozatának](container-registry-tutorial-quick-task.md)egyéb cikkeiben.



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az-acr-task-timer-add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az-acr-task-timer-remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az-acr-task-timer-list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az-acr-task-timer-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
