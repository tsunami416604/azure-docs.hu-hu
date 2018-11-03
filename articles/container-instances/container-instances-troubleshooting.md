---
title: Hibaelhárítás az Azure Container Instances szolgáltatásban
description: Ismerje meg, az Azure Container Instances szolgáltatással kapcsolatos problémák elhárítása
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/19/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 41e3f38817abbdd0cab9ab2c72d39cb6f3f69531
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978178"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Az Azure Container Instances szolgáltatásban gyakori problémáinak elhárítása

Ez a cikk bemutatja, hogyan háríthatók el a gyakori problémák kezeléséhez, vagy az Azure Container Instances a tárolók üzembe helyezése.

## <a name="naming-conventions"></a>Elnevezési konvenciók

A tároló specifikáció meghatározásakor bizonyos paraméterek betartásának elnevezési korlátozásairól van szükség. Alább van konkrét követelmények tárolót tartalmazó tábla tulajdonságai. Az Azure elnevezési konvenciók további információkért lásd: [elnevezési konvenciók] [ azure-name-restrictions] a az Azure Architecture Centert.

| Hatókör | Hossz | Kis- és nagybetűk | Érvényes karakterek | Javasolt minta | Példa |
| --- | --- | --- | --- | --- | --- | --- |
| Tároló csoport neve | 1-64 |Kis- és nagybetűk megkülönböztetése nélkül |Alfanumerikus karakterek és kötőjel bárhol, kivéve az első vagy utolsó karakter |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Tárolónév | 1-64 |Kis- és nagybetűk megkülönböztetése nélkül |Alfanumerikus karakterek és kötőjel bárhol, kivéve az első vagy utolsó karakter |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Tárolóportok | 1 és 65535 közötti |Egész szám |1 és 65535 közötti egész szám |`<port-number>` |`443` |
| DNS-névcímke | 5 – 63 |Kis- és nagybetűk megkülönböztetése nélkül |Alfanumerikus karakterek és kötőjel bárhol, kivéve az első vagy utolsó karakter |`<name>` |`frontend-site1` |
| Környezeti változó | 1–63 |Kis- és nagybetűk megkülönböztetése nélkül |Alfanumerikus karakterek és aláhúzásjelet (_) bárhol, kivéve az első vagy utolsó karakter |`<name>` |`MY_VARIABLE` |
| Kötet neve | 5 – 63 |Kis- és nagybetűk megkülönböztetése nélkül |Kisbetűket és számokat és kötőjeleket tartalmazhat, bárhol, kivéve az első vagy utolsó karakter. Nem tartalmazhat két egymást követő kötőjelet. |`<name>` |`batch-output-volume` |

## <a name="os-version-of-image-not-supported"></a>A kép nem támogatott operációsrendszer-verzió

Ha olyan lemezképet, amely nem támogatja az Azure Container Instances, adja meg egy `OsVersionNotSupported` hibát akkor adja vissza. A hiba: hasonló, ahol a következő `{0}` a telepíteni próbált rendszerkép neve:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Ez a hiba a rendszer leggyakrabban észlelt, amikor üzembe helyezése Windows-rendszerképek a féléves csatorna (SAC) alapuló kiadási. Például Windows verziók 1709-es és 1803 SAC kiadások, és ezt követően a központi telepítési hiba jön létre.

Az Azure Container Instances támogatja a Windows-rendszerképek alapján csak hosszú távú karbantartási csatorna (LTSC). Ha Windows-tárolók üzembe helyezése a probléma megoldásához, mindig üzembe LTSC-alapú rendszerképekhez.

A Windows LTSC és SAC verzióival kapcsolatos részletekért lásd: [áttekintése a Windows Server féléves csatorna][windows-sac-overview].

## <a name="unable-to-pull-image"></a>Nem lehet lekéréses kép

Ha az Azure Container Instances első próbálkozásra nem sikerült lekérni a rendszerképet, újra megpróbálja egy ideig. Ha a kép lekérési továbbra is fennáll, az ACI idővel az üzembe helyezés sikertelen lesz, és látni egy `Failed to pull image` hiba.

A probléma megoldásához, törölje a tároló-példányt, és ismételje meg a központi telepítés. Győződjön meg arról, hogy létezik-e a képet a beállításjegyzékben, és hogy már helyesen írta be a rendszerkép nevének.

Ha a kép nem kell lekérni, az alábbihoz hasonló események kimenete látható [az container show][az-container-show]:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"microsoft/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"microsoft/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

## <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Tároló folyamatosan kilép, és újraindítja a (nincs hosszan futó folyamat)

Tárolócsoportok alapértelmezés szerint egy [újraindítási házirend](container-instances-restart-policy.md) , **mindig**, így az mindig tárolócsoportban a tárolók újraindítása után a Futtatás befejezési. Előfordulhat, hogy módosítania azt **OnFailure** vagy **soha** Ha szeretne feladat-alapú tárolók futtatásához. Ha megad **OnFailure** , és továbbra is folyamatos lásd újraindul, előfordulhat, hogy egy probléma a alkalmazás vagy a parancsfájl végrehajtása a tárolóban.

Tárolócsoportok nélkül hosszú futású folyamatok futtatásakor ismétlődő Kilépés és képekkel, például az Ubuntu vagy Alpine újraindítást jelenhet meg. Kapcsolódás a következő [EXEC](container-instances-exec.md) nem fog működni, mert a tároló egyetlen folyamat életben tartása. Megoldásához közé tartozik, hogy a tároló futtatását az üzembe helyezett tárolókat csoport a következő indítási parancsot.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image microsoft/windowsservercore:ltsc2016
 --command-line "ping -t localhost"
```

A Container Instances API és az Azure portal tartalmaz egy `restartCount` tulajdonság. Ellenőrizze a tárolóhoz újraindítások számát, használhatja a [az container show] [ az-container-show] parancsot az Azure CLI-ben. A kimeneti (amely csonkolta kivonatosan) a következő példában látható a `restartCount` tulajdonság kimenetének a végén.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> Linux-disztribúciókra vonatkozó legtöbb tárolórendszerképeket egy shell bash, például az alapértelmezett parancs állítja be. Mivel egy önállóan rendszerhéj nem egy hosszú ideig futó szolgáltatás, ezek a tárolók azonnal lépjen ki, és az alapértelmezett konfigurálásakor újraindítás hurkot sorolhatók **mindig** újraindítási házirend.

## <a name="container-takes-a-long-time-to-start"></a>Tároló indítása hosszú időt vesz igénybe.

A két elsődleges olyan tényezőt, amelyek hozzájárulnak a tároló indítási ideje az Azure Container Instances szolgáltatásban a következők:

* [A kép mérete](#image-size)
* [Lemezkép helyét](#image-location)

Windows-rendszerképek [további szempontok](#cached-windows-images).

### <a name="image-size"></a>A kép mérete

Ha a tároló indítása, de idővel hosszú ideig tart, sikeres indítása azáltal, hogy megtekinti a tárolórendszerkép méretben. Azure Container Instances szolgáltatásban az igény szerinti kér le a tárolórendszerkép, mert az indítási idő látja közvetlenül kapcsolódik ahhoz a méretét.

Megtekintheti a tároló rendszerképének mérete használatával a `docker images` a Docker parancssori parancsot:

```console
$ docker images
REPOSITORY                  TAG       IMAGE ID        CREATED        SIZE
microsoft/aci-helloworld    latest    7f78509b568e    13 days ago    68.1MB
```

A kulcs gondoskodik a lemezkép mérete kisebb, ellenőriznie kell, hogy a végső lemezkép nem tartalmaz semmit, amely nem szükséges futásidőben. Ehhez a egyirányú Ez az a [többlépcsős buildek][docker-multi-stage-builds]. Többlépcsős épít fel, ellenőrizze, hogy a végső rendszerkép csak azokat az összetevőket, szüksége lesz az alkalmazás tartalmaz, és nem egy, az új tartalom, amely egyszerűen volt szükség összeállítás során.

### <a name="image-location"></a>Lemezkép helyét

A kép lekérési a tároló indítási idő csökkentésében is, hogy a tároló rendszerképét az üzemelteti [Azure Container Registry](/azure/container-registry/) ugyanabban a régióban, ahol szeretné telepíteni a container Instances szolgáltatásban. Ez lerövidíti a hálózati elérési útját, amelyet a tároló rendszerképét az utazás jelentősen lerövidíteni a letöltési időt.

### <a name="cached-windows-images"></a>Windows-rendszerképek gyorsítótárazott

Az Azure Container Instances egy gyorsítótárazási mechanizmust a tároló az egyes Windows-rendszerképek alapján indítási idő csökkentéséhez használja.

Ahhoz, hogy a leggyorsabb Windows tároló indítási ideje, használja az egyik a **legutóbbi három** verziók a következők **két lemezképet** , az alap rendszerképet:

* [A Windows Server 2016] [ docker-hub-windows-core] (csak LTS)
* [A Windows Server 2016 Nano Server][docker-hub-windows-nano]

### <a name="windows-containers-slow-network-readiness"></a>Windows-tárolók lassú hálózati készültségi

Kezdeti létrehozás esetén Windows-tárolók lehetnek nincs bejövő vagy kimenő kapcsolat akár 30 másodpercig (vagy hosszabb, a ritka esetben). A tárolóalkalmazás internetkapcsolatra van szüksége, ha késleltetés hozzáadása és az újrapróbálkozási logika, hogy az internetes kapcsolat 30 másodperc. Kezdeti telepítés után a tárolóalapú hálózatkezelés az megfelelően kell folytatódik.

## <a name="resource-not-available-error"></a>Erőforrás nem érhető el hiba

Különböző regionális erőforrás miatt betöltése az Azure-ban, előfordulhat, hogy a következő hibaüzenetet kapja, amikor próbál üzembe helyezéséhez:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Ez a hiba azt jelzi, hogy a régióban, amelyben telepíteni kívánt nagy terhelés miatt az a tároló megadott erőforrások nem osztható ki jelenleg. Egy vagy több, a következő kockázatcsökkentési lépések segítségével a probléma megoldása érdekében.

* Ellenőrizze a definiált paraméterek tartoznak a tároló üzembe helyezési beállítások [kvóták és régiók rendelkezésre állása az Azure Container Instances szolgáltatásban](container-instances-quotas.md#region-availability)
* Adja meg alacsonyabb Processzor- és a tároló beállításait
* Más Azure-régióban való üzembe helyezése
* Egy későbbi időpontban központi telepítése

## <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Nem lehet alapul szolgáló Docker API-t kapcsolódás vagy futtassa emelt szintű tároló

Az Azure Container Instances nem az alapul szolgáló infrastruktúra, amely futtatja a tárolócsoportok közvetlen hozzáférést biztosít. Ez magában foglalja a Docker API-nak a tároló-gazdagépen futó és a futó tárolók emelt szintű hozzáférés. Ha a Docker-interakció van szüksége, ellenőrizze a [REST dokumentációja](https://aka.ms/aci/rest) megtekintéséhez az ACI API támogatja. Ha valami hiányzik, a vonatkozó kérelem küldése a [ACI visszajelzés – fórumok](https://aka.ms/aci/feedback).

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [beolvasni a tároló naplóinak és események](container-instances-get-logs.md) hibakeresése a tárolók segítségével.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
