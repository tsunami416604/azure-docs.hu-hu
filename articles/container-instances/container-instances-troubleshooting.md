---
title: Gyakori problémák megoldása
description: Megtudhatja, hogy miként háríthatja el az Azure Container Instances telepítése, futtatása és kezelése során felmerülő gyakori problémákat
ms.topic: article
ms.date: 09/25/2019
ms.custom: mvc
ms.openlocfilehash: 07cdbfb27aaf9076e726ebda861ed24996e10135
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533391"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Az Azure Container Instances gyakori hibáinak elhárítása

Ez a cikk bemutatja, hogyan háríthatja el a tárolók azure-tárolópéldányok kezelésére vagy üzembe helyezésére vonatkozó gyakori problémák elhárítása. Lásd [még: Gyakran ismételt kérdések](container-instances-faq.md).

Ha további támogatásra van szüksége, tekintse meg az elérhető **Súgó + támogatási** lehetőségeket az Azure [Portalon.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

## <a name="issues-during-container-group-deployment"></a>Problémák a tárolócsoport telepítése során
### <a name="naming-conventions"></a>Elnevezési konvenciók

A tároló specifikációjának meghatározásakor bizonyos paraméterek megkövetelik az elnevezési korlátozások betartását. Az alábbi táblázat a tárolócsoport tulajdonságaira vonatkozó speciális követelményeket tartalmazza. Az Azure elnevezési konvencióiról az Azure Architecture Center [elnevezési konvencióiról][azure-name-restrictions] további információt talál.

| Hatókör | Hossz | Kis- és nagybetűk | Érvényes karakterek | Javasolt minta | Példa |
| --- | --- | --- | --- | --- | --- |
| Tárolócsoport neve | 1-64 |Kis- és nagybetűk megkülönböztetése nélkül |Alfanumerikus és kötőjel bárhol, kivéve az első vagy az utolsó karaktert |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Tárolónév | 1-64 |Kis- és nagybetűk megkülönböztetése nélkül |Alfanumerikus és kötőjel bárhol, kivéve az első vagy az utolsó karaktert |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Konténerportok | 1 és 65535 között |Egész szám |Egész szám 1 és 65535 között |`<port-number>` |`443` |
| DNS-névcímke | 5-63 |Kis- és nagybetűk megkülönböztetése nélkül |Alfanumerikus és kötőjel bárhol, kivéve az első vagy az utolsó karaktert |`<name>` |`frontend-site1` |
| Környezeti változó | 1–63 |Kis- és nagybetűk megkülönböztetése nélkül |Alfanumerikus és aláhúzás (_) bárhol, kivéve az első vagy az utolsó karaktert |`<name>` |`MY_VARIABLE` |
| Kötet neve | 5-63 |Kis- és nagybetűk megkülönböztetése nélkül |Kisbetűk és számok, valamint kötőjelek bárhol, kivéve az első vagy az utolsó karaktert. Két egymást követő kötőjel nem tartalmazhat. |`<name>` |`batch-output-volume` |

### <a name="os-version-of-image-not-supported"></a>A lemezkép operációs rendszerének verziója nem támogatott

Ha olyan lemezképet ad meg, amelyet `OsVersionNotSupported` az Azure Container Instances nem támogat, a rendszer hibát ad vissza. A hiba hasonló a `{0}` következőhöz, hol található a telepíteni kívánt lemezkép neve:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Ez a hiba leggyakrabban a 1709-es vagy 1803-as féléves csatornakiadáson alapuló Windows-lemezképek telepítésekor fordul elő, amelyek nem támogatottak. A támogatott Windows-lemezképek az Azure Container Instances, [lásd: Gyakori kérdések](container-instances-faq.md#what-windows-base-os-images-are-supported).

### <a name="unable-to-pull-image"></a>Nem lehet lehúzni a képet

Ha az Azure Container Instances kezdetben nem tudja lekérni a lemezképet, egy ideig újrapróbálkozik. Ha a rendszerkép lekéréses művelet továbbra is sikertelen, ACI végül nem a központi telepítés, és előfordulhat, hogy egy `Failed to pull image` hiba.

A probléma megoldásához törölje a tárolópéldányt, és próbálkozzon újra a központi telepítéssel. Győződjön meg arról, hogy a lemezkép megtalálható a beállításjegyzékben, és hogy helyesen írta be a lemezkép nevét.

Ha a kép nem húzható le, az alábbihoz hasonló események jelennek meg az [az container show][az-container-show]kimenetében:

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
### <a name="resource-not-available-error"></a>Az erőforrás nem érhető el hiba

Az Azure változó regionális erőforrás-terhelése miatt a következő hibaüzenet jelenhet meg egy tárolópéldány üzembe helyezésekor:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Ez a hiba azt jelzi, hogy a régióban, ahol üzembe helyezni próbál, a tárolóhoz megadott erőforrások nem rendelhetők ki abban az időben. A probléma megoldásához kövesse az alábbi kockázatcsökkentési lépések egyikét vagy többet.

* Ellenőrizze, hogy a tároló telepítési beállításai az [Azure Container Instances régióelérhetőségében](container-instances-region-availability.md) megadott paraméterek között vannak-e
* Alacsonyabb processzor- és memóriabeállítások megadása a tárolóhoz
* Üzembe helyezés egy másik Azure-régióban
* Telepítés későbbi időpontban

## <a name="issues-during-container-group-runtime"></a>Problémák a Container Group futásideje során
### <a name="container-continually-exits-and-restarts-no-long-running-process"></a>A tároló folyamatosan kilép és újraindul (nincs hosszú ideig futó folyamat)

A tárolócsoportok alapértelmezés szerint **mindig** [újraindítják a házirendet,](container-instances-restart-policy.md) így a tárolócsoportban lévő tárolók mindig újraindulnak a befejezésig való futtatásután. Előfordulhat, hogy ezt onfailure vagy **soha** módosítására kell **módosítania,** ha feladatalapú tárolókat kíván futtatni. Ha megadja **az OnFailure értéket,** és továbbra is folyamatos újraindításokat lát, előfordulhat, hogy probléma van a tárolóban végrehajtott alkalmazással vagy parancsfájllal.

Ha hosszú ideig futó folyamatok nélküli tárolócsoportokat futtat, ismétlődő kilépéseket és újraindításokat láthat olyan képekkel, mint az Ubuntu vagy az Alpine. Az [EXEC-en](container-instances-exec.md) keresztül történő csatlakoztatás nem fog működni, mivel a tartálynak nincs folyamata, amely életben tartaná. A probléma megoldásához adjon meg egy indítási parancsot, mint például a tárolócsoport központi telepítése a tároló futtatásához.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

A Container Instances API és `restartCount` az Azure Portal tartalmaz egy tulajdonságot. Egy tároló újraindításainak számának ellenőrzéséhez használhatja az [az container show][az-container-show] parancsot az Azure CLI-ben. A következő példában kimenet (amely röviden csonkolt), láthatja `restartCount` a tulajdonság végén a kimenet.

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
> A Legtöbb tárolórendszerképek Linux disztribúciók meg egy rendszerhéj, például a bash, mint az alapértelmezett parancs. Mivel a rendszerhéj önmagában nem egy hosszú ideig futó szolgáltatás, ezek a tárolók azonnal kilép, és egy újraindítási ciklusba esnek, ha az alapértelmezett **Mindig** indítsa újra a házirendet.

### <a name="container-takes-a-long-time-to-start"></a>A tartály indítása hosszú időt vesz igénybe

A három fő tényező, amely hozzájárul a tároló indítási idő az Azure Container Instances a következők:

* [Képméretet](#image-size)
* [Kép helye](#image-location)
* [Gyorsítótárazott képek](#cached-images)

A Windows-lemezképek [további szempontokat is figyelembe adnak.](#cached-images)

#### <a name="image-size"></a>Képméretet

Ha a tároló indítása hosszú időt vesz igénybe, de végül sikerül, kezdje a tárolórendszerkép méretének megtekintésével. Mivel az Azure Container Instances igény szerint lekéri a tárolólemezképet, a látható indítási idő közvetlenül kapcsolódik a méretéhez.

A tárolórendszerkép méretét a Docker `docker images` CLI parancsával tekintheti meg:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

A képméretek kicsikényezésének kulcsa annak biztosítása, hogy a végső lemezkép ne tartalmazzon semmit, ami futásidőben nem szükséges. Ennek egyik módja a [többlépcsős buildek][docker-multi-stage-builds]. A többlépcsős buildek megkönnyítik annak biztosítását, hogy a végső lemezkép csak az alkalmazáshoz szükséges összetevőket tartalmazza, és a létrehozási időben szükséges további tartalmakat nem.

#### <a name="image-location"></a>Kép helye

Egy másik módja annak, hogy csökkentse a rendszerkép lekérése a tároló indítási ideje, hogy a tárolórendszerkép az [Azure Container Registry](/azure/container-registry/) ugyanabban a régióban, ahol tárolópéldányok üzembe helyezéséhez. Ez lerövidíti a hálózati útvonalat, amelyet a tárolóképnek meg kell haladnia, jelentősen lerövidítve a letöltési időt.

#### <a name="cached-images"></a>Gyorsítótárazott képek

Az Azure Container Instances gyorsítótárazási mechanizmust használ a tárolók indítási `servercore:ltsc2019`idejének `servercore:1809`felgyorsítására a gyakori [Windows alaplemezképekre](container-instances-faq.md#what-windows-base-os-images-are-supported)épülő lemezképekhez , beleértve `nanoserver:1809`a , és a. Gyakran használt Linux-lemezképek, például, `ubuntu:1604` és `alpine:3.6` a gyorsítótárban is. A gyorsítótárazott képek és címkék naprakész listájához használja a [Gyorsítótáras képek listázása API-t.][list-cached-images]

> [!NOTE]
> A Windows Server 2019-alapú lemezképek használata az Azure Container Instances előzetes verzióban érhető el.

#### <a name="windows-containers-slow-network-readiness"></a>A Windows-tárolók lassítják a hálózati készenlétet

A kezdeti létrehozáskor előfordulhat, hogy a Windows-tárolók legfeljebb 30 másodpercig (vagy ritka esetekben hosszabb ideig) nem rendelkeznek bejövő vagy kimenő kapcsolattal. Ha a tárolóalkalmazásnak internetkapcsolatra van szüksége, adjon hozzá késleltetési és újrapróbálkozási logikát, hogy 30 másodperc et vehessítsen az internetkapcsolat létesítésére. A kezdeti beállítás után a tárolóhálózat megfelelően folytatódik.

### <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Nem lehet csatlakozni az alapul szolgáló Docker API-hoz, vagy nem lehet emelt szintű tárolókat futtatni

Az Azure Container Instances nem teszi elérhetővé a tárolócsoportokat tárolócsoportokat tárolócsoportokat tárolócsoportokat tárolócsoportokat tárolókat tartalmazó mögöttes infrastruktúrához való közvetlen hozzáférést. Ez magában foglalja a hozzáférést a Docker API-t a tároló gazdagépén futó és a kiemelt tárolók futtatása. Ha Docker-interakcióra van szüksége, tekintse meg a [REST-referenciadokumentációt,](https://aka.ms/aci/rest) hogy mit támogat az ACI API. Ha valami hiányzik, nyújtson be egy kérelmet az [ACI visszajelzési fórumokon](https://aka.ms/aci/feedback).

### <a name="container-group-ip-address-may-not-be-accessible-due-to-mismatched-ports"></a>Előfordulhat, hogy a tárolócsoport IP-címe nem érhető el a nem egyező portok miatt

Az Azure Container Instances még nem támogatja a portleképezés, mint a rendszeres docker-konfiguráció. Ha úgy találja, hogy egy tárolócsoport IP-címe nem érhető el, amikor úgy gondolja, hogy lennie kell, győződjön `ports` meg arról, hogy a tárolórendszerképet úgy állította be, hogy ugyanazokat a portokat figyelje, amelyeket a tárolócsoportban a tulajdonsággal elérhetővé tett.

Ha azt szeretné, hogy az Azure Container Instances figyelheti a tárolólemezképben `aci-helloworld` konfigurált porton, tesztelje a rendszerkép, amely a portot elérhetővé teszi. Futtassa `aci-helloworld` az alkalmazást úgy is, hogy az a porton figyelje. `aci-helloworld`elfogad egy választható `PORT` környezeti változót, amely felülbírálja a figyelt 80-as alapértelmezett portot. Például a 9000-es port teszteléséhez állítsa be a [környezeti változót](container-instances-environment-variables.md) a tárolócsoport létrehozásakor:

1. Állítsa be a tárolócsoportot a 9000-es port felfedésére, és adja meg a portszámot a környezeti változó értékeként. A példa a Bash rendszerhéjhoz van formázva. Ha egy másik rendszerhéjat, például a PowerShellt vagy a parancssort szeretné előnyben részesíteni, ennek megfelelően módosítania kell a változó-hozzárendelést.
    ```azurecli
    az container create --resource-group myResourceGroup \
    --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ip-address Public --ports 9000 \
    --environment-variables 'PORT'='9000'
    ```
1. Keresse meg a tárolócsoport IP-címét `az container create`a parancskimenetében. Keresse meg az értékét **ip**. 
1. A tároló sikeres kiépítése után keresse meg a tárolóalkalmazás IP-címét és portját a böngészőben, például: `192.0.2.0:9000`. 

    Meg kell jelennie a "Welcome to Azure Container Instances!" a webalkalmazás által megjelenített üzenet.
1. Ha végzett a tárolóval, távolítsa el `az container delete` a következő paranccsal:

    ```azurecli
    az container delete --resource-group myResourceGroup --name mycontainer
    ```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [kérheti le a tárolónaplókat és -eseményeket](container-instances-get-logs.md) a tárolók hibakereséséhez.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/listcachedimages
