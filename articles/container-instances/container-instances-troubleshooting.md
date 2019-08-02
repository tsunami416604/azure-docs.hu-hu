---
title: Hibaelhárítási Azure Container Instances
description: Ismerje meg, hogyan lehet elhárítani a Azure Container Instances kapcsolatos problémákat
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/25/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 4b41a3862341ef39c1288985d86d86667fbc5866
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325588"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Azure Container Instances gyakori problémáinak elhárítása

Ez a cikk bemutatja, hogyan lehet a tárolók Azure Container Instances való felügyeletére vagy üzembe helyezésére vonatkozó gyakori hibákat elhárítani. Lásd még: [Gyakori kérdések](container-instances-faq.md).

## <a name="naming-conventions"></a>Elnevezési konvenciók

A tároló specifikációjának meghatározásakor bizonyos paramétereknek meg kell követelniük az elnevezési korlátozásokat. Alább látható egy táblázat a Container Group tulajdonságaira vonatkozó konkrét követelményekkel. Az Azure elnevezési konvenciókkal kapcsolatos további információkért [][azure-name-restrictions] lásd: elnevezési konvenciók a Azure Architecture Center.

| Scope | Hossz | Kis- és nagybetűk | Érvényes karakterek | Javasolt minta | Példa |
| --- | --- | --- | --- | --- | --- |
| Tároló csoportjának neve | 1-64 |Kis- és nagybetűk megkülönböztetése nélkül |Alfanumerikus és kötőjel bárhol, az első vagy az utolsó karakter kivételével |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Tárolónév | 1-64 |Kis- és nagybetűk megkülönböztetése nélkül |Alfanumerikus és kötőjel bárhol, az első vagy az utolsó karakter kivételével |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Tároló portjai | 1 és 65535 között |Egész szám |1 és 65535 közötti egész szám |`<port-number>` |`443` |
| DNS name label | 5-63 |Kis- és nagybetűk megkülönböztetése nélkül |Alfanumerikus és kötőjel bárhol, az első vagy az utolsó karakter kivételével |`<name>` |`frontend-site1` |
| Környezeti változó | 1-63 |Kis- és nagybetűk megkülönböztetése nélkül |Alfanumerikus és aláhúzás (_) bárhol az első vagy az utolsó karakter kivételével |`<name>` |`MY_VARIABLE` |
| Kötet neve | 5-63 |Kis- és nagybetűk megkülönböztetése nélkül |Kisbetűk, számok és kötőjelek bárhol, az első vagy az utolsó karakter kivételével. Két egymást követő kötőjel nem szerepelhet. |`<name>` |`batch-output-volume` |

## <a name="os-version-of-image-not-supported"></a>A rendszerkép operációs rendszerének verziója nem támogatott

Ha olyan rendszerképet ad meg, amely Azure Container instances nem támogatja `OsVersionNotSupported` , hibaüzenetet ad vissza. A hiba az alábbihoz hasonló, ahol `{0}` a a telepíteni próbált rendszerkép neve:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Ez a hiba leggyakrabban a 1709-es vagy a 1803-es féléves csatornán alapuló Windows-lemezképek telepítésekor fordul elő, amelyek nem támogatottak. A Azure Container Instances támogatott Windows-rendszerképeit a [Gyakori kérdések](container-instances-faq.md#what-windows-base-os-images-are-supported)című témakörben tekintheti meg.

## <a name="unable-to-pull-image"></a>Nem sikerült lekérni a rendszerképet

Ha a Azure Container Instances kezdetben nem tudja lekérni a rendszerképet, a rendszer egy ideig újrapróbálkozik. Ha a lekéréses művelet továbbra is sikertelen, az ACI végül nem fogja tudni végrehajtani az üzemelő `Failed to pull image` példányt, és hibaüzenet jelenhet meg.

A probléma megoldásához törölje a tároló példányát, majd próbálja megismételni a telepítést. Győződjön meg arról, hogy a rendszerkép létezik a beállításjegyzékben, és hogy helyesen írta be a rendszerkép nevét.

Ha a képet nem lehet lehúzni, a következőhöz hasonló események jelennek meg az az [Container show][az-container-show]kimenetében:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"mcr.microsoft.com/azuredocs/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

## <a name="container-continually-exits-and-restarts-no-long-running-process"></a>A tároló folyamatosan kilép és újraindul (nem hosszan futó folyamat)

A Container groups alapértelmezett értéke **mindig**az újraindítási [szabályzat](container-instances-restart-policy.md) , így a tároló csoportba tartozó tárolók mindig újraindulnak, miután befejeződött a futtatásuk. Előfordulhat, hogy ezt a **OnFailure** vagy **soha nem** értékre kell módosítania, ha feladat-alapú tárolókat szeretne futtatni. Ha **OnFailure** ad meg, és továbbra is folyamatosan látja a folyamatos újraindításokat, a tárolóban végrehajtott alkalmazással vagy parancsfájllal kapcsolatos probléma merülhet fel.

Ha olyan tárolási csoportokat futtat, amelyek hosszú ideig futó folyamatok nélkül futnak, akkor előfordulhat, hogy a rendszer ismételten kilép, és újraindul az olyan képekkel, mint például az Ubuntu vagy az Alpine. Az [exec](container-instances-exec.md) -on keresztüli csatlakozás nem működik, mert a tároló nem tartja életben a folyamatot. A probléma megoldásához vegyen fel egy, a következőhöz hasonló indítási parancsot a tároló csoportjának üzembe helyezésével, hogy a tárolón fusson.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

A Container instances API és Azure Portal tartalmaz egy `restartCount` tulajdonságot. A tárolók újraindításának számának vizsgálatához az az [Container show][az-container-show] parancsot használhatja az Azure CLI-ben. A következő példában szereplő kimenetben (amely a rövid időpontra van rövidítve), a `restartCount` kimenet végén látható tulajdonság jelenik meg.

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
> A Linux-disztribúciók legtöbb tároló-lemezképe egy rendszerhéjt (például bash) állít be alapértelmezett parancsként. Mivel a saját felülete nem egy hosszan futó szolgáltatás, a tárolók azonnal kilépnek, és az alapértelmezett **mindig** újraindítási szabályzattal konfigurált újraindítási ciklusba esnek.

## <a name="container-takes-a-long-time-to-start"></a>A tároló hosszú időt vesz igénybe

A Azure Container Instances a tároló indítási idejéhez hozzájáruló két elsődleges tényező a következő:

* [Rendszerkép mérete](#image-size)
* [Rendszerkép helye](#image-location)

A Windows-lemezképek [további szempontokat is figyelembe](#cached-images)kell venni.

### <a name="image-size"></a>Rendszerkép mérete

Ha a tároló hosszú időt vesz igénybe, de végül sikeres, először tekintse meg a tároló rendszerképének méretét. Mivel Azure Container Instances igény szerint lekéri a tároló képét, a megjelenő indítási idő közvetlenül kapcsolódik a méretéhez.

A tároló rendszerképének `docker images` méretét a Docker CLI parancsával tekintheti meg:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

A képméret kis méretűre állításának kulcsa annak biztosítása, hogy a végső rendszerkép ne tartalmazzon olyan semmit, ami nem szükséges futásidőben. Ennek egyik módja a többfázisú [buildek][docker-multi-stage-builds]használata. A többfázisú buildek megkönnyítik annak biztosítását, hogy a végső rendszerkép csak az alkalmazáshoz szükséges összetevőket tartalmazza, és nem a felépítési időpontban szükséges további tartalmakat.

### <a name="image-location"></a>Rendszerkép helye

Egy másik lehetőség, hogy csökkentse a rendszerkép hatását a tároló indítási idejére, hogy a tároló lemezképét ugyanabban a régióban [](/azure/container-registry/) tárolja Azure Container Registryban, ahol tároló-példányokat kíván üzembe helyezni. Ez lerövidíti azt a hálózati elérési utat, amelyre a tároló rendszerképének utaznia kell, ami jelentősen lerövidíti a letöltési időt.

### <a name="cached-images"></a>Gyorsítótárazott lemezképek

A Azure Container instances egy gyorsítótárazási mechanizmust használ a [Windows](container-instances-faq.md#what-windows-base-os-images-are-supported)alaplemezképekre épülő rendszerképekhez, például `nanoserver:1809` `servercore:ltsc2019` `servercore:1809` Gyakran használt Linux `ubuntu:1604` -rendszerképek, `alpine:3.6` például a és a gyorsítótárazva is. A gyorsítótárazott képek és címkék naprakész listája a [gyorsítótárazott lemezképek listája][list-cached-images] API-t használja.

> [!NOTE]
> A Windows Server 2019-alapú rendszerképek használata a Azure Container Instances előzetes verzióban érhető el.

### <a name="windows-containers-slow-network-readiness"></a>Windows-tárolók lassú hálózati készültsége

A kezdeti létrehozáskor előfordulhat, hogy a Windows-tárolók nem rendelkeznek bejövő vagy kimenő kapcsolattal akár 30 másodpercig (vagy hosszabb, ritka esetekben). Ha a tároló alkalmazás internetkapcsolatra van szüksége, adja hozzá a késleltetés és az újrapróbálkozási logika értékét, hogy 30 másodpercet adjon meg az internetkapcsolat létrehozásához. A kezdeti beállítás után a tároló hálózatkezelésének megfelelően folytatódnia kell.

## <a name="resource-not-available-error"></a>Az erőforrás nem érhető el. hiba

Az Azure különböző regionális Erőforrás-terhelése miatt előfordulhat, hogy a következő hibaüzenetet kapja a tároló példányának telepítésekor:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Ez a hiba azt jelzi, hogy nagy terhelés miatt abban a régióban, amelyben a központi telepítést kísérli meg, a tárolóhoz megadott erőforrások nem foglalhatók le egyszerre. A probléma megoldásához használja az alábbi enyhítő lépések egyikét.

* Ellenőrizze, hogy a tároló központi telepítési beállításai a [régió rendelkezésre állása Azure Container Instancesban](container-instances-region-availability.md) meghatározott paraméterek alá esnek-e
* A tároló alacsonyabb CPU-és memória-beállításainak megadása
* Üzembe helyezés egy másik Azure-régióban
* Üzembe helyezés később

## <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Nem lehet csatlakozni a mögöttes Docker API-hoz vagy a privilegizált tárolók futtatásához.

A Azure Container Instances nem tesz elérhetővé közvetlen hozzáférést a tároló-csoportokat működtető mögöttes infrastruktúrához. Ez magában foglalja a tároló gazdagépén futó Docker API hozzáférését és a Kiemelt tárolók futtatását. Ha a Docker-interakcióra van szüksége, tekintse meg a [Rest](https://aka.ms/aci/rest) -dokumentációt, ahol megtekintheti az ACI API által támogatott műveleteket. Ha hiányzik valami, küldjön egy kérelmet az [ACI visszajelzési fórumokra](https://aka.ms/aci/feedback).

## <a name="ips-may-not-be-accessible-due-to-mismatched-ports"></a>Előfordulhat, hogy az IP-címek nem érhetők el, mert nem egyeznek a portok

A Azure Container Instances jelenleg nem támogatja a port-hozzárendelést, például a normál Docker-konfigurációt, de ez a javítás az ütemterven van. Ha úgy találja, hogy az IP-címek nem érhetők el, akkor győződjön meg róla, hogy úgy konfigurálta a tároló-rendszerképet, hogy a tároló csoportjába `ports` tartozó, a tulajdonsággal közzétett portokat figyelje.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [kérhet le tároló naplókat és eseményeket](container-instances-get-logs.md) a tárolók hibakereséséhez.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/listcachedimages
