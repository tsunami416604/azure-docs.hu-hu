---
title: Azure Container Registry-feladatok ütemezésére
description: Állítsa be a időzítők egy Azure Container Registry feladat futtatására egy meghatározott ütemezés szerint.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/27/2019
ms.author: danlep
ms.openlocfilehash: a1123a30025f9be6e994e69703f5ee1aa05d1b49
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509702"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>Egy ACR-feladat egy meghatározott ütemezés szerint fut

Ez a cikk bemutatja, hogyan futtathat egy [ACR feladat](container-registry-tasks-overview.md) ütemezés szerint. Feladat ütemezése egy vagy több beállításával *az időzítési eseményindítóktól*. 

Feladat ütemezése hasznos a következőhöz hasonló forgatókönyvek esetén:

* Futtassa a tárolóalapú számítási feladat ütemezett karbantartási műveletek elvégzéséhez. Például futtassa a tárolóalapú alkalmazás nem szükséges lemezképeket eltávolítja a beállításjegyzékből.
* Futtassa egy éles rendszerképet tesztek során a workday, a webhelyek működés közbeni figyelésének részeként.

Futtassa a példák ebben a cikkben használhatja az Azure Cloud Shell vagy az Azure parancssori felület helyi telepítése. Ha a következőhöz szeretne használni, helyileg, 2.0.68 verzió vagy újabb rendszer szükséges. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].


## <a name="about-scheduling-a-task"></a>A feladat ütemezése

* **Cron-kifejezés az eseményindító** -tevékenység az időzítő eseményindító használ egy *cron-kifejezés*. A kifejezés: öt mezőkkel, a perc, óra, nap, hónap és a feladat indításához a hét napját megadó karakterlánc. Akár percenkénti gyakoriságát támogatottak. 

  Ha például a kifejezés `"0 12 * * Mon-Fri"` feladat elindítja a hét (UTC) délben. Lásd: [részletek](#cron-expressions) a cikk későbbi részében.
* **Több időzítő eseményindító** – több időzítők ad hozzá egy feladat engedélyezett, mindaddig, amíg az ütemezések eltérőek. 
    * Adjon meg több időzítő eseményindító, a feladat létrehozásakor, vagy később hozzáadhatja őket.
    * Igény szerint adjon nevet az eseményindítók a könnyebb kezelhetőség, vagy ACR feladatok biztosít alapértelmezett eseményindítók nevét.
    * Időzítő ütemezésének egyszerre átfedik egymást, ha ACR feladatokat a feladat minden egyes időzítő az ütemezett időpontban elindítja. 
* **Egyéb feladat eseményindítók** – egy időzítő által aktivált feladat esetén is engedélyezhető tevékenységeken alapuló triggerek [forrás kód véglegesítésére](container-registry-tutorial-build-task.md) vagy [lemezképe frissítéseinek kiinduló](container-registry-tutorial-base-image-update.md). Más ACR feladatokat, például is [aktiválása manuálisan][az-acr-task-run] egy ütemezett feladatot.

## <a name="create-a-task-with-a-timer-trigger"></a>Időzítő eseményindító tartalmazó feladat létrehozása

Feladat létrehozásakor a [az acr-feladat létrehozása][az-acr-task-create] parancsot opcionálisan hozzáadhat egy időzítő eseményindító. Adja hozzá a `--schedule` paraméter és a egy cron-kifejezést az időzítő pass. 

Egyszerű példaként a következő parancsot a futó eseményindítók a `hello-world` rendszerképet a Docker Hubból minden nap, 21:00 (UTC). A forráskörnyezet kód nélkül futtatja a tevékenységet.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --context /dev/null \
  --cmd hello-world \
  --schedule "0 21 * * *"
```

Futtassa a [az acr feladat megjelenítése][az-acr-task-show] paranccsal tekintheti meg, hogy az időzítő eseményindító van konfigurálva. Alapértelmezés szerint az alaplemezkép update eseményindító is engedélyezve van.

```console
$ az acr task show --name mytask --registry registry --output table
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

A feladat manuális aktiválása [az acr-feladat futtatása][az-acr-task-run] annak érdekében, hogy ez megfelelően van beállítva:

```azurecli
az acr task run --name mytask --registry myregistry
```

Ha a tároló sikeresen fut, a kimenet az alábbihoz hasonló a:

```console
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

Az ütemezett időpont után futtassa a [az acr feladat list-futtatások][az-acr-task-list-runs] paranccsal ellenőrizheti, hogy az időzítő által aktivált a feladat várt módon:

```azurecli
az acr task list runs --name mytask --registry myregistry --output table
``` 

Amikor az időzítő művelet sikeres, kimenete az alábbihoz hasonló:

```console
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```
            
## <a name="manage-timer-triggers"></a>Az időzítési eseményindítóktól kezelése

Használja a [az acr feladat időzítő][az-acr-task-timer] az időzítési eseményindítóktól egy ACR-feladat kezelésére szolgáló parancsokat.

### <a name="add-or-update-a-timer-trigger"></a>A hozzáadandó vagy frissítendő időzítő eseményindító

Feladat létrehozása után igény szerint adjon hozzá egy időzítő indítófeltételt használatával a [hozzáadása az acr feladat időzítő][az-acr-task-timer-add] parancsot. Az alábbi példa hozzáad egy időzítő eseményindító nevét *2. időzítő* való *mytask* korábban létrehozott. Ez az időzítő elindítja a feladat minden nap, 10:30 (UTC).

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Az ütemezés egy meglévő trigger frissítést, vagy módosítsa az állapotot, a [az acr feladat időzítő frissítése][az-acr-task-timer-update] parancsot. Például frissíti az eseményindítót, nevű *2. időzítő* a feladat indításához, 11:30 (UTC):

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Lista időzítő eseményindító

A [az acr feladatlista időzítő][az-acr-task-timer-list] parancs megjeleníti a feladat beállítása az időzítési eseményindítóktól:

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

### <a name="remove-a-timer-trigger"></a>Távolítsa el az időzítő eseményindító 

Használja a [az acr feladat időzítő remove][az-acr-task-timer-remove] parancs egy időzítő indítófeltételt eltávolítása egy feladatot. A következő példában eltávolítjuk a *2. időzítő* a trigger *mytask*:

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron-kifejezések

ACR-feladatok használja a [NCronTab](https://github.com/atifaziz/NCrontab) könyvtár értelmezése cron-kifejezéseket. Az ACR-feladatokban támogatott kifejezések térközt elválasztva öt kötelező mezők rendelkeznek:

`{minute} {hour} {day} {month} {day-of-week}`

A cron-kifejezésekkel használt időzónában az egyezményes világidő (UTC). Óra 24 órás formátumban vannak.

> [!NOTE]
> ACR feladatokat nem támogatja a `{second}` vagy `{year}` cron-kifejezések mező. Ha másolja egy cron-kifejezés egy másik rendszer használja, mindenképpen távolítsa el ezeket a mezőket, ha azokat használja.

Minden mező a következő típusú értékek egyike lehet:

|Típus  |Példa  |Adatvezérelt  |
|---------|---------|---------|
|Egy adott érték |<nobr>"5 * * * *"</nobr>|óránként, 5 perccel egész óra után|
|Minden érték (`*`)|<nobr>"* 5 * * *"</nobr>|az óra elején (60 naponta alkalommal) 5:00 UTC percenként|
|A tartomány (`-` operátor)|<nobr>"0 1-3 * * *"</nobr>|3-szor egy nap, 1:00 2:00, és 3:00 (UTC)|  
|Értékek egy halmazát (`,` operátor)|<nobr>"20,30,40 * * * *"</nobr>|3-szor óránként, a 20 perc, 30 percet, és 40 perccel egész óra|
|Az intervallum értéke (`/` operátor)|<nobr>"*/10 * * * *"</nobr>|6 alkalommal / óra, 10 perc, a 20 perc és így tovább, az elmúlt óra

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron-példák

|Példa|Adatvezérelt  |
|---------|---------|
|`"*/5 * * * *"`|öt percenként|
|`"0 * * * *"`|egyszer minden órában tetején|
|`"0 */2 * * *"`|két óránként egyszer|
|`"0 9-17 * * *"`|óránként egyszer 9:00-tól, 17:00 (UTC)|
|`"30 9 * * *"`|9:30 naponta UTC|
|`"30 9 * * 1-5"`|9:30: UTC minden hétköznap|
|`"30 9 * Jan Mon"`|9:30: UTC januárban minden hétfőn|


## <a name="next-steps"></a>További lépések

A példák az adatforrás kód véglegesítés vagy alaplemezkép által aktivált frissítések, tekintse meg a [ACR feladatok oktatóanyag-sorozat](container-registry-tutorial-quick-task.md).



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