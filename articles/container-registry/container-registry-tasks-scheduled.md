---
title: Oktatóanyag – ACR-feladat beosztása
description: Ebből az oktatóanyagból megtudhatja, hogyan futtathat egy Azure Container Registry feladatot egy meghatározott ütemterven egy vagy több időzítő-eseményindító beállításával
ms.topic: article
ms.date: 06/27/2019
ms.openlocfilehash: 3202b5d8c426165d81129f1affa69b3a3d515ce9
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402879"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>ACR-feladat futtatása meghatározott ütemterven

Ebből az oktatóanyagból megtudhatja, hogyan futtathat egy [ACR-feladatot](container-registry-tasks-overview.md) ütemterv szerint. Egy vagy több *időzítő-eseményindító*beállításával ütemezhet egy feladatot. Az időzítő eseményindítók önállóan vagy más feladatokkal együtt is használhatók.

Ez az oktatóanyag a feladatok ütemezését és a következőket ismerteti:

> [!div class="checklist"]
> * Feladat létrehozása időzítő-triggerrel
> * Időzítő eseményindítók kezelése

A feladatok ütemezése a következőhöz hasonló esetekben hasznos:

* A tároló munkaterhelésének futtatása ütemezett karbantartási műveletekhez. Például futtasson egy tároló alkalmazást, hogy eltávolítsa a szükségtelen lemezképeket a beállításjegyzékből.
* Futtasson teszteket egy éles rendszerképben a munkanap során az élő hely figyelésének részeként.

Az Azure CLI Azure Cloud Shell vagy helyi telepítését használhatja a cikkben szereplő példák futtatásához. Ha helyileg szeretné használni, a 2.0.68 vagy újabb verziót kötelező megadni. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].


## <a name="about-scheduling-a-task"></a>Feladat ütemezése

* **Trigger a cron kifejezéssel** – a feladatok időzítő triggere cron- *kifejezést*használ. A kifejezés egy öt mezőt tartalmazó karakterlánc, amely a feladat elindításához a percet, az órát, a napot, a hónapot és a hét napját adja meg. A percenkénti gyakoriságok támogatottak.

  Az `"0 12 * * Mon-Fri"` kifejezés például az egyes munkanapokon a (z) (UTC) időpontban indítja el a feladatot. A [részleteket](#cron-expressions) a cikk későbbi részében találja.
* **Több időzítő eseményindító** – több időzítő hozzáadása egy feladathoz, ha az ütemtervek eltérnek.
    * A feladat létrehozásakor több időzítő eseményindítót adjon meg, vagy később adja hozzá őket.
    * Szükség esetén megadhatja az eseményindítókat az egyszerűbb kezelés érdekében, vagy az ACR-feladatok alapértelmezett eseményindító-neveket biztosítanak.
    * Ha az időzítő ütemezése egyszerre van átfedésben, az ACR-feladatok minden időzítő esetében az ütemezett időpontban indítja el a feladatot.
* **Egyéb feladat-eseményindítók** – egy időzítő által aktivált feladatban engedélyezheti az eseményindítókat a [forráskód-véglegesítő](container-registry-tutorial-build-task.md) vagy az [alaprendszerkép frissítései](container-registry-tutorial-base-image-update.md)alapján is. Más ACR-feladatokhoz hasonlóan [manuálisan is aktiválhat][az-acr-task-run] egy ütemezett feladatot.

## <a name="create-a-task-with-a-timer-trigger"></a>Feladat létrehozása időzítő-triggerrel

Amikor az [az ACR Task Create][az-acr-task-create] paranccsal hoz létre egy feladatot, lehetősége van egy időzítő-trigger hozzáadására. Adja hozzá a `--schedule` paramétert, és adjon meg egy cron-kifejezést az időzítőhöz.

Egyszerű példaként a következő parancs elindítja a `hello-world` rendszerképet a Docker hub-ból minden nap 21:00 UTC időpontban. A feladat forráskód-környezet nélkül fut.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --cmd hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Az az [ACR Task show][az-acr-task-show] parancs futtatásával ellenőrizze, hogy az időzítő trigger konfigurálva van-e. Alapértelmezés szerint az alapszintű rendszerkép frissítésének triggere is engedélyezve van.

```azurecli
az acr task show --name mytask --registry registry --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

A feladat manuális elindítása az [az ACR Task Run][az-acr-task-run] paranccsal, hogy megfelelően legyen beállítva:

```azurecli
az acr task run --name mytask --registry myregistry
```

Ha a tároló sikeresen fut, a kimenet a következőhöz hasonló:

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

Az ütemezett időpont után futtassa az az [ACR Task List-][az-acr-task-list-runs] Run parancsot annak ellenőrzéséhez, hogy az időzítő a várt módon aktiválta-e a feladatot:

```azurecli
az acr task list-runs --name mytask --registry myregistry --output table
```

Ha az időzítő sikeres, a kimenet a következőhöz hasonló:

```output
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Időzítő eseményindítók kezelése

Az az [ACR Task Timer][az-acr-task-timer] parancs használatával kezelheti az ACR-feladatok időzítő eseményindítóit.

### <a name="add-or-update-a-timer-trigger"></a>Időzítő-trigger hozzáadása vagy frissítése

Egy feladat létrehozása után opcionálisan hozzáadhat egy időzítő-triggert az az [ACR Task Timer Add][az-acr-task-timer-add] paranccsal. A következő példa egy időzítő-trigger nevét adja hozzá a korábban létrehozott *mytask* - *timer2* . Ez az időzítő minden nap 10:30 UTC időpontban indítja el a feladatot.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Frissítse egy meglévő trigger ütemtervét, vagy módosítsa annak állapotát az az [ACR Task Timer Update][az-acr-task-timer-update] parancs használatával. Frissítse például a *timer2* nevű triggert, hogy aktiválja a feladatot a 11:30 UTC időpontban:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Időzítő eseményindítók listázása

Az az [ACR Task Timer List][az-acr-task-timer-list] parancs megjeleníti az adott feladathoz beállított időzítő eseményindítókat:

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

### <a name="remove-a-timer-trigger"></a>Időzítő-trigger eltávolítása

Az az [ACR Task Timer Remove][az-acr-task-timer-remove] paranccsal távolíthatja el egy időzítő triggert egy feladatból. Az alábbi példa eltávolítja a *timer2* triggert a *mytask*-ből:

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron-kifejezések

Az ACR-feladatok a [NCronTab](https://github.com/atifaziz/NCrontab) könyvtár használatával értelmezik a cron-kifejezéseket. Az ACR-feladatok támogatott kifejezései öt kötelező mezőt foglalnak el szóközzel elválasztva:

`{minute} {hour} {day} {month} {day-of-week}`

A cron-kifejezésekkel használt időzóna egyezményes világidő (UTC) szerint van megadva. Az órák 24 órás formátumban jelennek meg.

> [!NOTE]
> Az ACR-feladatok nem támogatják a `{second}` vagy `{year}` mezőt a cron-kifejezésekben. Ha egy másik rendszeren használt cron-kifejezést másol, ne felejtse el eltávolítani ezeket a mezőket, ha azok használatban vannak.

Minden mezőhöz a következő típusú értékek tartozhatnak:

|Típus  |Példa  |Aktiváláskor  |
|---------|---------|---------|
|Egy adott érték |<nobr>`"5 * * * *"`</nobr>|minden órában, 5 perccel az óra múltán|
|Minden érték (`*`)|<nobr>`"* 5 * * *"`</nobr>|az óra 5:00 UTC-től számított percenként (naponta 60 alkalommal)|
|Tartomány (`-` operátor)|<nobr>`"0 1-3 * * *"`</nobr>|naponta 3 alkalommal, 1:00, 2:00 és 3:00 UTC|
|Értékek halmaza (`,` operátor)|<nobr>`"20,30,40 * * * *"`</nobr>|óránként 3 alkalommal, 20 perc, 30 perc és 40 perccel elmúlt|
|Intervallum érték (`/` operátor)|<nobr>`"*/10 * * * *"`</nobr>|óránként 6 alkalommal, 10 perc, 20 perc és így tovább, az óra vége

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron-példák

|Példa|Aktiváláskor  |
|---------|---------|
|`"*/5 * * * *"`|öt percenként|
|`"0 * * * *"`|egyszer minden óra elején|
|`"0 */2 * * *"`|két óránként egyszer|
|`"0 9-17 * * *"`|óránként, 9:00 és 17:00 UTC között|
|`"30 9 * * *"`|minden nap 9:30 UTC-kor|
|`"30 9 * * 1-5"`|minden hétköznap 9:30 UTC-kor|
|`"30 9 * Jan Mon"`|Január 9:30-kor, minden hétfőn|

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani az oktatóanyag-sorozatban létrehozott összes erőforrást, beleértve a tároló-beállításjegyzéket vagy a jegyzékeket, a tároló-példányt, a kulcstartót és a szolgáltatásnevet, adja ki a következő parancsokat:

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre olyan Azure Container Registry feladatokat, amelyeket egy időzítő automatikusan indít el. 

Ha például egy ütemezett feladatot szeretne használni a beállításjegyzékben lévő adattárak törléséhez, olvassa el a [lemezképek automatikus kitakarítása Azure Container registryből](container-registry-auto-purge.md)című témakört.

A forráskód-végrehajtás vagy az alaprendszerkép frissítései által aktivált feladatok esetében tekintse meg az [ACR feladatok oktatóanyag-sorozatának](container-registry-tutorial-quick-task.md)egyéb cikkeit.



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
