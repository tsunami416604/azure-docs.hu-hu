<properties
   pageTitle="Azure tárolószolgáltatási fürt terhelésének elosztása | Microsoft Azure"
   description="Azure tárolószolgáltatási fürt terhelésének elosztása"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Containers, Micro-services, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/18/2016"
   ms.author="rogardle"/>

# Azure tárolószolgáltatási fürt terhelésének elosztása

Ebben a cikkben üzembe helyezünk egy webes előtér-szolgáltatást, amely skálázható az Azure LB mögötti szolgáltatások biztosítására.


## Előfeltételek

[Az Azure tárolószolgáltatás egy DCOS vezénylőtípusú példányának üzembe helyezése](container-service-deployment.md), [az ügyfél és a fürt közötti kapcsolat feltételeinek megteremtése](container-service-connect.md), valamint [AZURE.INCLUDE [install the DC/OS CLI](../../includes/container-service-install-dcos-cli-include.md)].


## Terheléselosztás

A tárolószolgáltatási fürtökben két terheléselosztási réteg van: az Azure LB a nyilvános belépési pontok (a végfelhasználók által elért pontok) számára, valamint a mélyebb rétegben működő marathon-lb, amely a bejövő kéréseket a szolgáltatáskéréseket kiszolgáló tárolópéldányokhoz irányítja. A marathon-lb dinamikusan alkalmazkodik a szolgáltatást nyújtó tárolók skálázásához.

## Marathon LB 

A Marathon LB megoldás dinamikusan átkonfigurálja magát az üzembe helyezett tárolók alapján. Annak is ellenáll, ha elvész egy tároló vagy ügynök. Ilyen esetben a Mesos egyszerűen újraindítja a tárolót máshol, és átkonfigurálja a Marathon LB-t. 

A Marathon LB telepítéséhez futtassa az ügyfélszámítógépről a következő parancsot:

```bash
dcos package install marathon-lb 
``` 

Most, hogy már rendelkezésre áll a marathon-lb csomag, telepíthetünk egy egyszerű webkiszolgálót, amely a következő konfigurációt használja:


```json
{ 
  "id": "web", 
  "container": { 
    "type": "DOCKER", 
    "docker": { 
      "image": "tutum/hello-world", 
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

Ennek a következők a legfontosabb elemei: 
  * Állítsa a HAProxy_0_VHOST paramétert az ügynökök terheléselosztójának teljes tartománynevére (FQDN). Ennek formátuma: `<acsName>agents.<region>.cloudapp.azure.com`. Ha például létrehozott egy  `myacs` nevű tárolószolgáltatási fürtöt a `West US` régióban, akkor a következő lenne az FQDN: `myacsagents.westus.cloudapp.azure.com`. Úgy is megtalálhatja, hogy azt a terheléselosztót keresi, amelynek a nevében szerepel az „agent” az [Azure portálon](https://portal.azure.com), a tárolószolgáltatás számára létrehozott erőforráscsoport erőforrásai között.
  * Állítsa a servicePort értéket egy 10 000-nél nagyobb számra. Ezzel azonosítja az ebben a tárolóban futó szolgáltatást. A marathon-lb ennek alapján azonosítja, hogy mely szolgáltatásokra kiterjedően kell terheléselosztást végeznie.
  * Állítsa a HAPROXY_GROUP címkét az "external" értékre.
  * Állítsa a hostPort paramétert 0-ra. Ez azt jelenti, hogy a marathon egy tetszőleges szabad portot fog kiosztani.

Másolja ezt a JSON-kódot egy `hello-web.json` nevű fájlba, és helyezzen vele üzembe egy tárolót: 

```bash
dcos marathon app add hello-web.json 
``` 

## Azure LB 

Az Azure LB alapértelmezés szerint a 80-as, a 8080-as és a 443-as portot teszi elérhetővé. Ha ebből a három portból használja valamelyiket (mint mi a fenti példában), akkor semmi teendője sincs: ez esetben el kell tudnia érni az ügynök terheléselosztó FQDN-jét, és minden frissítés alkalmával ciklikusan a sorban következő webkiszolgálót kell elérnie. Ha viszont másik portot használ, akkor ciklikus multiplexelési szabályt és egy mintavételt kell hozzáadnia az Azure LB-n az Ön által használt porthoz. Ez az [Azure XPLAT parancssori felületen](../xplat-cli-azure-resource-manager.md), az `azure lb rule create` és az `azure lb probe create` paranccsal teheti meg.


## További helyzetek

Előfordulhat, hogy eltérő tartományokat használ különféle szolgáltatások elérhetővé tételére. Példa: 

tartomany1.com -> Azure LB:80 -> marathon-lb:10001 tarolo1:33292 ->  
tartomany2.com -> Azure LB:80 -> marathon-lb:10002 -> tarolo2:22321 

Ennek egyik módjáról a [Virtual Hosts](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/) (Virtuális gazdagépek) című cikk nyújt tájékoztatást: bemutatja, hogyan lehet a tartományokat konkrét marathon-lb elérési utakhoz társítani.

Azt is megteheti, hogy különféle portokat tesz elérhetővé, majd leképezi őket a marathon lb mögött futó szolgáltatások közül a megfelelőkre. Példa:

Azure lb:80 -> marathon-lb:10001 -> tarolo:233423  
Azure lb:8080 -> marathon-lb:1002 -> tarolo2:33432 
 

## Következő lépések

[Ebben a blogbejegyzésen](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/) részletesebb tájékoztatást olvashat a Marathon LB-ről.



<!--HONumber=Jun16_HO2--->


