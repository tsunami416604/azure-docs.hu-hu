---
title: Saját üzemeltetésű átjáró üzembe helyezése a Kubernetes-ben | Microsoft Docs
description: Ismerje meg, hogyan helyezheti üzembe az Azure API Management saját üzemeltetésű átjáró összetevőjét a Kubernetes
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 7802614540bd5e553fbf1d7a0884ffa2f7433c37
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205104"
---
# <a name="deploy-self-hosted-gateway-to-kubernetes"></a>Saját üzemeltetésű átjáró üzembe helyezése a Kubernetes-ben

Ez a cikk az Azure-API Management saját üzemeltetésű átjáró-összetevőjének Kubernetes-fürtre történő telepítésének lépéseit ismerteti.

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md)
- Hozzon létre egy Kubernetes-fürtöt.
> [!TIP]
> Az [egycsomópontos fürtök](https://kubernetes.io/docs/setup/#learning-environment) fejlesztési és értékelési célokra is jól működnek. A [Kubernetes-tanúsítvánnyal rendelkező](https://kubernetes.io/partners/#conformance) többcsomópontos fürtöket helyszíni vagy Felhőbeli környezetben is használhatja éles számítási feladatokhoz.
- [Saját üzemeltetésű átjáró-erőforrás kiépítése a API Management-példányban](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-to-kubernetes"></a>Üzembe helyezés a Kubernetesben

1. Válassza az **átjárók** lehetőséget az **üzembe helyezés és az infrastruktúra**területen.
2. Válassza ki azt a saját üzemeltetésű átjáró-erőforrást, amelyet telepíteni kíván.
3. Válassza a **telepítés**lehetőséget.
4. Vegye figyelembe, hogy a **jogkivonat** szövegmezőben lévő hozzáférési jogkivonat automatikusan lett létrehozva az alapértelmezett **lejárati** és **titkos kulcs** értékeinek használatával. Ha szükséges, válassza ki a kívánt értékeket mindkét vezérlőelemben egy új jogkivonat létrehozásához.
5. Válassza a **Kubernetes** lapot a **telepítési parancsfájlok**területen.
6. Kattintson a **<Gateway-name>. YML** fájl hivatkozásra, és töltse le a YAML fájlt.
7. Az **üzembe helyezés** szövegmező jobb alsó sarkában található `kubectl` **Másolás** ikon kiválasztásával mentheti a parancsokat a vágólapra.
8. Parancsok beillesztése a terminál (vagy parancs) ablakba. Az első parancs létrehoz egy Kubernetes titkos kulcsot, amely a 4. lépésben generált hozzáférési jogkivonatot tartalmaz. A második parancs a 6. lépésben letöltött konfigurációs fájlt alkalmazza a Kubernetes-fürtre, és elvárja, hogy a fájl megtalálható legyen az aktuális könyvtárban.
9. A parancsok végrehajtásával hozza létre a szükséges Kubernetes-objektumokat az [alapértelmezett névtérben](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) , és indítsa el a saját üzemeltetésű átjáró Pod (ka) t a Microsoft Container Registryról letöltött [tároló-rendszerképből](https://aka.ms/apim/sputnik/dhub) .
10. Az üzembe helyezés sikerességének vizsgálatához futtassa a parancsot. Vegye figyelembe, hogy az összes létrehozandó objektum és a-pod (ok) inicializálásához kis idő is eltelhet.
```console
kubectl get deployments
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
<gateway-name>   1/1     1            1           18s
```
11. A futtatási parancs látható, hogy ellenőrizze, sikeres volt-e a szolgáltatás létrehozása. Vegye figyelembe, hogy a szolgáltatás IP-címei és portjai eltérőek lesznek.
```console
kubectl get services
NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
<gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
```
12. Térjen vissza a Azure Portal, és válassza az **Áttekintés**lehetőséget.
13. **Állapot** : zöld pipa ikon, majd a YAML fájlban megadott replikák számának megfelelő csomópontok száma, amely megerősíti, hogy az üzembe helyezett saját üzemeltetésű átjáró hüvelyei sikeresen kommunikálnak a API Management szolgáltatással, és normál szívveréssel rendelkeznek.

![átjáró állapota](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> A <code>kubectl logs deployment/<gateway-name></code> parancs futtatásával megtekintheti a naplókat egy véletlenszerűen kiválasztott Pod-ból, ha egynél több van.
> Hajtsa végre <code>kubectl logs -h</code> a parancssori kapcsolók teljes készletét, például egy adott Pod vagy tároló naplóinak megtekintését.

## <a name="production-deployment-considerations"></a>Éles üzembe helyezési megfontolások

### <a name="access-token"></a>Hozzáférési jogkivonat
Egy érvényes hozzáférési jogkivonat saját üzemeltetésű átjárója nem tud hozzáférni és letölteni a konfigurációt a társított API Management szolgáltatás konfigurációs adatok végpontján. A hozzáférési jogkivonat legfeljebb 30 napig érvényes lehet. Újból elő kell állítani, és a fürtöt manuálisan vagy az automationen keresztül kell konfigurálni, mielőtt lejár. A jogkivonat-frissítés automatizálásakor ezzel a felügyeleti API- [művelettel](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway/generatetoken) létrehozott egy új jogkivonatot. A Kubernetes-titkok kezelésével kapcsolatos információkért kövesse ezt a [hivatkozást](https://kubernetes.io/docs/concepts/configuration/secret) .

### <a name="namespace"></a>Névtér
A Kubernetes- [névterek](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) segítségével egyetlen fürtöt oszthat meg több csapat, projekt vagy alkalmazás között. A névterek az erőforrások és a nevek hatókörét biztosítják, és társíthatók erőforrás-kvótával és hozzáférés-vezérlési szabályzatokkal.
A Azure Portalben megadott parancsok a saját üzemeltetésű átjáró erőforrásainak létrehozása az **alapértelmezett** névtérben, amely automatikusan létrejön, minden fürtben megtalálható, és nem törölhető.
Érdemes lehet saját üzemeltetésű átjárót [létrehozni és üzembe helyezni](https://kubernetesbyexample.com/ns/) az éles környezetben található különálló névtérben.

### <a name="number-of-replicas"></a>Replikák száma
Az éles környezetekben megfelelő replikák minimális száma kettő.

Alapértelmezés szerint a saját üzemeltetésű átjáró egy **RollingUpdate** -telepítési [stratégiával](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy)van üzembe helyezve. Tekintse át az alapértelmezett értékeket, és érdemes explicit módon beállítani a [**maxUnavailable**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) és a [**maxSurge**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) mezőket, különösen nagy replikák számának használata esetén.

### <a name="container-resources"></a>Tároló erőforrásai
Alapértelmezés szerint a Azure Portalban megadott YAML-fájl nem ad meg tároló-erőforrásra vonatkozó kérelmeket.

Nem lehet megbízhatóan megbecsülni és javasolni a tároló CPU-és memória-erőforrásainak mennyiségét, valamint az adott munkaterhelés támogatásához szükséges replikák számát, többek között a következő tényezők miatt:

- A fürtön futó adott hardver
- A virtualizáció jelenléte és típusa
- Egyidejű ügyfélkapcsolatok száma és sebessége
- Kérelmek gyakorisága
- Konfigurált szabályzatok típusa és száma
- A hasznos adatok mérete, valamint a terhelések pufferelése vagy továbbítása
- Háttérbeli szolgáltatás késése

Azt javasoljuk, hogy az erőforrás-kérelmeket 2 maggal és 2 GiB-ra állítsa be kiindulási pontként, és végezzen terhelési tesztet, valamint az eredmények alapján felfelé/lefelé vagy lefelé vagy lefelé.

### <a name="container-image-tag"></a>Tároló képcímkéje
A Azure Portalban megadott YAML-fájl a **legújabb** címkét használja, amely mindig a saját üzemeltetésű átjáró-tároló lemezképének legújabb verziójára hivatkozik.

Érdemes lehet egy adott verziójú címkét használni az éles környezetben, hogy elkerülje a véletlen frissítést egy újabb verzióra.

Ezt a [hivatkozást](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list) követve megtekintheti az elérhető címkék teljes listáját.

### <a name="dns-policy"></a>DNS-házirend
A DNS-névfeloldás kritikus szerepet játszik a saját üzemeltetésű átjárók számára az Azure-beli függőségekhez való kapcsolódáshoz és a háttér-szolgáltatásokhoz való, API-hívások küldéséhez.

A Azure Portalban megadott YAML-fájl az alapértelmezett [**ClusterFirst**](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) -házirendet alkalmazza, amely a fürt DNS által nem feloldott névfeloldási kérelmeket eredményez a csomóponttól örökölt felsőbb RÉTEGBELI DNS-kiszolgálóra való továbbításhoz.

Ezt a [hivatkozást](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service) követve megismerheti a Kubernetes névfeloldását, és a telepítőnek megfelelően érdemes lehet a [DNS-házirend](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) vagy a D[NS konfigurációjának](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config) testreszabása.

### <a name="configuration-backup"></a>Konfiguráció biztonsági mentése
Ezt a [hivatkozást](self-hosted-gateway-overview.md#connectivity-to-azure) követve megismerheti a saját üzemeltetésű átjárók viselkedését egy ideiglenes Azure-kapcsolat kimaradásának jelenlétében.
Konfigurálja a helyi tárolási kötetet a saját üzemeltetésű átjáró-tárolóhoz, így a legújabb letöltött konfiguráció biztonsági másolatát is megtarthatja, és ha a kapcsolat nem fut, akkor az újraindítás után használhatja azt. A kötet csatlakoztatási útvonalának <code>/apim/config</code>kötelezőnek kell lennie. [Itt](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml)talál példát.
A Kubernetes Storage-ban való megismeréséhez kövesse ezt a [hivatkozást](https://kubernetes.io/docs/concepts/storage/volumes/).

### <a name="local-logs-and-metrics"></a>Helyi naplók és mérőszámok
A saját üzemeltetésű átjáró telemetria küld a [Azure monitor](api-management-howto-use-azure-monitor.md) és az [Azure Application Insights](api-management-howto-app-insights.md) számára konfigurációs beállításokkal a társított API Management szolgáltatásban.
Ha az [Azure-hoz való csatlakozás](self-hosted-gateway-overview.md#connectivity-to-azure) átmenetileg megszakad, a telemetria az Azure-ba való továbbítása megszakad, és a leállás ideje alatt az adatvesztés történik.
Érdemes lehet [helyi figyelést beállítani](how-to-configure-local-metrics-logs.md) , hogy az képes legyen az API-forgalom megfigyelésére és a telemetria elvesztése az Azure-kapcsolat kimaradása során.

## <a name="next-steps"></a>További lépések

* További információ a saját üzemeltetésű átjáróról: [Azure API Management saját üzemeltetésű átjáró – áttekintés](self-hosted-gateway-overview.md)