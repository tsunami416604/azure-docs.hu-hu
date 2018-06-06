---
title: Azure-tárolót példányok hibaelhárítása
description: Ismerje meg az Azure-tároló példányaival kapcsolatos problémák elhárítása
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/14/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 39c43c079ea4d10686bd656ba2d451ff42aac9f6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700230"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Azure tároló példányát kapcsolatos gyakori hibák elhárítása

Ez a cikk bemutatja, hogyan kezelése vagy tárolók telepítése Azure tároló példányokhoz kapcsolatos gyakori hibák elhárítása.

## <a name="naming-conventions"></a>Elnevezési konvenciók

A tároló specification meghatározásakor bizonyos paraméterek szükséges elnevezési korlátozásairól megfelelést. Tároló konkrét követelmények tartalmazó tábla van alatt a csoport tulajdonságai.
Az Azure elnevezési konvenciókat további információkért lásd: [elnevezési konvenciói](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions) Azure architektúra közepén.

| Hatókör | Hossz | Kis- és nagybetűk | Érvényes karaktereket | Javasolt minta | Példa |
| --- | --- | --- | --- | --- | --- | --- |
| A tárolócsoport nevét | 1-64 |Kis- és nagybetűk megkülönböztetése nélkül |Alfanumerikus és kötőjel bárhol, kivéve az első vagy utolsó karaktere |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Tárolónév | 1-64 |Kis- és nagybetűk megkülönböztetése nélkül |Alfanumerikus és kötőjel bárhol, kivéve az első vagy utolsó karaktere |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Tároló portok | 1 és 65535 között |Egész szám |1 és 65535 közötti egész szám |`<port-number>` |`443` |
| DNS-névcímke | 5-63 |Kis- és nagybetűk megkülönböztetése nélkül |Alfanumerikus és kötőjel bárhol, kivéve az első vagy utolsó karaktere |`<name>` |`frontend-site1` |
| Környezeti változó | 1–63 |Kis- és nagybetűk megkülönböztetése nélkül |Alfanumerikus és a "_" chracter bárhol, kivéve az első vagy utolsó karaktere |`<name>` |`MY_VARIABLE` |
| Kötet neve | 5-63 |Kis- és nagybetűk megkülönböztetése nélkül |Kisbetűket, számokat és kötőjeleket bárhol, kivéve az első vagy utolsó karaktere. Nem tartalmazhat két egymást követő kötőjelet. |`<name>` |`batch-output-volume` |

## <a name="image-version-not-supported"></a>Lemezkép-verziója nem támogatott

Ha megad egy olyanra, amely nem támogatja az Azure tároló példányok, egy `ImageVersionNotSupported` hibát ad vissza. A hiba az érték `The version of image '{0}' is not supported.`, és a jelenleg Windows 1709 lemezképbe vonatkozik. A probléma orvoslása érdekében LTS Windows-lemezkép használata. Windows 1709 képek támogatása a feldolgozása folyamatban van.

## <a name="unable-to-pull-image"></a>Nem sikerült lekéréses kép

Nem sikerült a lemezkép kezdetben lekéréses Azure tároló példányok esetén újrapróbálja valamennyi ideje előtt esetleg sikertelenek lesznek. Ha nem kell húzni a lemezképet, a következő események jelennek meg kimenete [az tároló megjelenítése][az-container-show]:

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

Oldja meg, a tároló törlése, és ismételje meg a központi telepítés, fizető zárja be a figyelmet, hogy a lemezkép nevét helyesen adta.

## <a name="container-continually-exits-and-restarts"></a>Tároló folyamatosan kilép, és újraindul

Ha a tároló létrehozása után futtatott, és automatikusan újraindul, szükség lehet beállítani a [indítsa újra a házirend](container-instances-restart-policy.md) a **OnFailure** vagy **soha**. Ha megad **OnFailure** és továbbra is folyamatosan lásd újraindul, előfordulhat, hogy az alkalmazás vagy a parancsfájl végrehajtása a tárolóban kapcsolatos problémát.

A tároló példányok API tartalmaz egy `restartCount` tulajdonság. Egy tároló újraindítások számát ellenőrzéséhez használja a [az tároló megjelenítése] [ az-container-show] az Azure CLI 2.0 parancsot. A következő egy példa a kimenetre (amely kivonatosan mutatja csonkolódtak), hogy a `restartCount` tulajdonság a kimeneti végén.

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
> A legtöbb tároló képek Linux terjesztésekről, bash, például a rendszerhéj állítja be az alapértelmezett parancs. Mivel a rendszerhéj önmagában nem hosszan futó szolgáltatás, ezekhez a tárolókhoz azonnal való kilépéshez és az alapértelmezett konfigurálásakor újraindítás hurok sorolhatók **mindig** indítsa újra a házirendet.

## <a name="container-takes-a-long-time-to-start"></a>Tároló start hosszú időt vesz igénybe.

A két elsődleges tényező Azure tároló példányát tároló indítási ideje befolyásolja a következők:

* [A kép mérete](#image-size)
* [Kép](#image-location)

Windows-lemezképek rendelkezik [további szempontok](#cached-windows-images).

### <a name="image-size"></a>A kép mérete

Ha a tároló indítása, de végül hosszú ideig tart a sikeres, indítsa el a tároló-lemezkép mérete alapján. Azure tároló példányok az igény szerinti kéri le a tároló lemezképet, mert az indítási idő tapasztal közvetlenül kapcsolódik a méretét.

A tároló-lemezkép mérete használatával megtekintheti a `docker images` a Docker parancssori parancsot:

```console
$ docker images
REPOSITORY                  TAG       IMAGE ID        CREATED        SIZE
microsoft/aci-helloworld    latest    7f78509b568e    13 days ago    68.1MB
```

A kulcsot a lemezkép mérete kisebb tartása annak ellenőrzése, hogy a végső kép nem tartalmaz semmit, amelyre nincs szükség a futási időben. Ehhez a egyirányú azt a [többlépcsős buildek][docker-multi-stage-builds]. Többlépcsős épít, ellenőrizze, hogy a végső kép csak az alkalmazás van szüksége az összetevők tartalmazza, és nem egy, az új tartalom, amely egyszerűen volt szükség összeállítása során.

### <a name="image-location"></a>Kép

Egy másik, csökken a kép lekéréses a tároló indítási idővel módja a tároló lemezkép a gazdagép [Azure tároló beállításjegyzék](/azure/container-registry/) ugyanabban a régióban, ahol szeretné telepíteni a tároló példányok. Ez lerövidíti a hálózati elérési útját, amelyet a tároló kép utaznak, jelentősen lerövidíteni a letöltési időt.

### <a name="cached-windows-images"></a>Gyorsítótárazott Windows-lemezképek

Az Azure tároló példányok gyorsítótárazást a sebesség tároló indítási idő alapján a bizonyos Windows-telepítési lemezképek használja.

Ahhoz, hogy a legjobb Windows tároló indítási idő, valamelyikével a **legutóbbi három** a következő verziói **két lemezképet** az alapjául szolgáló lemezképhez:

* [Windows Server 2016] [ docker-hub-windows-core] (csak LTS)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

### <a name="windows-containers-slow-network-readiness"></a>Windows tárolók lassú hálózati készültségi

Windows-tárolók járhat a bejövő vagy kimenő kapcsolat számára a kezdeti létrehozásakor legfeljebb 5 másodperc. Kezdeti telepítés után a tároló hálózatkezelés az megfelelően kell folytatni.

## <a name="resource-not-available-error"></a>Erőforrás nem érhető el hiba

Különböző területi erőforrás miatt betöltése az Azure-ban, és a következő hiba akkor fordulhat elő, amikor egy tároló-példány telepítése:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Ez a hiba azt jelzi, hogy a régióban, amelyben telepíteni kívánt túl nagy terhelés miatt a tároló megadott erőforrások nem rendelhető hozzá jelenleg. Legalább egy, a megoldás lépések segítségével a probléma megoldása érdekében.

* Ellenőrizze a definiált paraméterek tartoznak a tároló központi telepítési beállítások [kvótái és az Azure-tároló példányok régiónkénti elérhetőség](container-instances-quotas.md#region-availability)
* A tároló alacsonyabb CPU és memória beállításainak megadása
* Egy másik Azure-régiót telepítése
* Egy későbbi időpontban telepítése

## <a name="next-steps"></a>További lépések
Megtudhatja, hogyan [beolvasni a tároló naplók és események](container-instances-get-logs.md) segítségére hibakeresésben a tárolók számára.

<!-- LINKS - External -->
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az_container_show