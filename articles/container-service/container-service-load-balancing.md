<properties
   pageTitle="Tárolók terheléselosztása Azure Container Service-fürtben | Microsoft Azure"
   description="Több tárolóra kiterjedő terheléselosztás Azure Container Service-fürtben"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Tárolók, mikroszolgáltatások, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="rogardle"/>


# <a name="load-balance-containers-in-an-azure-container-service-cluster"></a>Tárolók terheléselosztása Azure Container Service-fürtben

Ebben a cikkben azt vizsgáljuk meg, hogyan lehet belső terheléselosztót létrehozni a Marathon-LB-vel egy a DC/OS által kezelt Azure Container Service szolgáltatásban. Ez lehetőséget nyújt az alkalmazások horizontális skálázására. Ezenkívül lehetővé teszi a nyilvános és a privát ügynökös fürtök használatát. Ehhez a terheléselosztókat a nyilvános fürtbe kell helyezni, az alkalmazástárolókat pedig a privát fürtbe.

## <a name="prerequisites"></a>Előfeltételek

[Helyezze üzembe az Azure Container Service egy példányát](container-service-deployment.md) DC/OS típusú vezénylővel, és [győződjön meg róla, hogy az ügyfél képes csatlakozni a fürthöz](container-service-connect.md). 

## <a name="load-balancing"></a>Terheléselosztás

Két terheléselosztó réteg van azon a Container Service-fürtön, amelyet ki fogunk építeni: 

  1. Az Azure Load Balancer nyilvános belépési pontokat biztosít (amelyekre a végfelhasználók rákattintanak). Ezt az Azure Container Service automatikusan biztosítja, és alapértelmezés szerint a 80-as, 443-as és 8080-as portok használatára van konfigurálva.
  2. A Marathon Load Balancer (Marathon-LB) a bejövő kéréseket olyan tárolópéldányokhoz irányítja, amelyek ezeket a kéréseket kiszolgálják. A webszolgáltatás biztosítás közben a Marathon-LB dinamikusan alkalmazkodik a tárolók méretezéséhez. Ez a terheléselosztó nincs alapértelmezés szerint megadva a Container Service-ben, de nagyon könnyű telepíteni.

## <a name="marathon-load-balancer"></a>Marathon Load Balancer

A Marathon Load Balancer dinamikusan újrakonfigurálja magát az üzembe helyezett tárolók alapján. A tárolók és az ügynökök elvesztése sem zavarja meg a működését. Ilyen esetekben az Apache Mesos egyszerűen máshol indítja újra a tárolót, és a Marathon-LB alkalmazkodik a változáshoz.

A Marathon Load Balancer telepítéséhez a DC/OS webes felhasználói felületet vagy a parancssort használhatja.

### <a name="install-marathon-lb-using-dc/os-web-ui"></a>A Marathon-LB telepítése DC/OS webes felhasználói felülettel

  1. Kattintson a Universe elemre.
  2. Keressen rá a Marathon-LB kifejezésre.
  3. Kattintson az Install (Telepítés) gombra.

![A Marathon-LB telepítése a DC/OS webes felületén keresztül](./media/dcos/marathon-lb-install.png)

### <a name="install-marathon-lb-using-the-dc/os-cli"></a>A Marathon-LB használata a DC/OS parancssori felülettel

A DC/OS parancssori felület telepítése és annak ellenőrzése után, hogy tud-e csatlakozni a fürthöz, futtassa a következő parancsot az ügyfélgépről:

```bash
dcos package install marathon-lb
```

Ez a parancs automatikusan telepíti a terheléselosztót a nyilvános ügynökök fürtjébe.

## <a name="deploy-a-load-balanced-web-application"></a>Elosztott terhelésű webalkalmazás üzembe helyezése

Most, hogy már rendelkezésre áll a marathon-lb csomag, üzembe helyezhetünk egy alkalmazástárolót, amelynek el kívánjuk osztani a terhelését. Ebben a példában egy egyszerű webkiszolgálót helyezünk üzembe, a következő konfigurációval:

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}

```

  * Állítsa a `HAProxy_0_VHOST` értékét az ügynökök terheléselosztójának FQDN-jére. Ez az `<acsName>agents.<region>.cloudapp.azure.com` elemben szerepel. Ha például `myacs` nevű tárolószolgáltatás-fürtöt hoz létre a `West US` régióban, az FQDN a következő: `myacsagents.westus.cloudapp.azure.com`. Ezt úgy is megtalálhatja, ha a nevében az „ügynök” kifejezést tartalmazó terheléselosztót keres, amikor áttekinti az [Azure Portalon](https://portal.azure.com) a Container Service-hez létrehozott erőforráscsoportban lévő erőforrásokat.
  * Állítsa a servicePort értéket egy 10 000-nél nagyobb számra. Ez azonosítja a tárolóban futtatott szolgáltatást – a Marathon-LB ezzel azonosítja a kiegyenlítendő szolgáltatásokat.
  * Állítsa a `HAPROXY_GROUP` címkét „external” értékre.
  * Állítsa a `hostPort` elemet 0 értékre. Ez azt jelenti, hogy a Marathon véletlenszerűen foglal le egy elérhető portot.
  * Állítsa az `instances` elemet a létrehozni kívánt példányok számára. Az értéket később bármikor növelheti vagy csökkentheti.

Megjegyzendő, hogy a Marathon alapértelmezés szerint a privát fürtöt helyezi üzembe; ez azt jelenti, hogy a fenti üzemelő példányt csak a terheléselosztón keresztül lehet elérni, ez pedig általában pontosan az a működésmód, amit szeretnénk.

### <a name="deploy-using-the-dc/os-web-ui"></a>Üzembe helyezés a DC/OS webes felhasználói felületével

  1. Látogasson el a Marathon oldalára a http://localhost/marathon címen (az [SSH-alagút](container-service-connect.md) beállítása után), és kattintson a következőre: `Create Appliction`
  2. A `New Application` párbeszédpanelen kattintson a `JSON Mode` elemre a jobb felső sarokban.
  3. Illessze be a fenti JSON-t a szerkesztőbe.
  4. Kattintson a következőre: `Create Appliction`

### <a name="deploy-using-the-dc/os-cli"></a>Üzembe helyezés a DC/OS parancssori felülettel

Ha ezt az alkalmazást a DC/OS parancssori felülettel szeretné telepíteni, egyszerűen másolja a fenti JSON-t egy `hello-web.json` nevű fájlba, és futtassa a következőt:

```bash
dcos marathon app add hello-web.json
```

## <a name="azure-load-balancer"></a>Azure Load Balancer

Alapértelmezés szerint az Azure Load Balancer a 80-as, 8080-as és 443-as portokat teszi elérhetővé. Ha ezen portok egyikét használja (ahogyan a fenti példában is), akkor semmit nem kell tennie. Képesnek kell lennie arra, hogy rákattintson az ügynöke terheléselosztójának FQDN-jére, és minden frissítéskor ciklikusan fogja elérni a három webkiszolgáló egyikét. De ha másik portot használ, ciklikus szabályt és egy hálózatfigyelőt kell hozzáadnia a terheléselosztón a használt porthoz. Ezt az [Azure parancssori felületén](../xplat-cli-azure-resource-manager.md) teheti meg az `azure network lb rule create` és `azure network lb probe create` parancsokkal. A műveleteket az Azure Portalon is végrehajthatja.


## <a name="additional-scenarios"></a>További helyzetek

Előfordulhat, hogy eltérő tartományokat használ különféle szolgáltatások elérhetővé tételére. Példa:

tartomany1.com -> Azure LB:80 -> marathon-lb:10001 tarolo1:33292 ->  
tartomany2.com -> Azure LB:80 -> marathon-lb:10002 -> tarolo2:22321

Ehhez nézze át a [virtuális gazdagépekre](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/) vonatkozó tudnivalókat, amelyekkel konkrét marathon-lb elérési utakhoz társíthatja a tartományokat.

Azt is megteheti, hogy különféle portokat tesz elérhetővé, majd leképezi őket a marathon-lb mögött futó szolgáltatások közül a megfelelőkre. Példa:

Azure lb:80 -> marathon-lb:10001 -> tarolo:233423  
Azure lb:8080 -> marathon-lb:1002 -> tarolo2:33432


## <a name="next-steps"></a>Következő lépések

A [Marathon-LB-re](https://dcos.io/docs/1.7/usage/service-discovery/marathon-lb/) vonatkozó további információért tekintse meg a DC/OS dokumentációt.



<!--HONumber=Oct16_HO3-->


