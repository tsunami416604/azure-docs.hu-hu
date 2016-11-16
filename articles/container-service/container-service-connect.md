---
title: "Csatlakozás Azure tárolószolgáltatási fürthöz | Microsoft Docs"
description: "Csatlakozás Azure tárolószolgáltatási fürthöz SSH-alagút segítségével"
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, tárolók, mikroszolgáltatások, DC/OS, Azure"
ms.assetid: ff8d9e32-20d2-4658-829f-590dec89603d
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 97f74f845e19ae99cf6c5abbb9f076c7c5171993


---
# <a name="connect-to-an-azure-container-service-cluster"></a>Csatlakozás Azure tárolószolgáltatási fürthöz
Az Azure Container Service által üzembe helyezett DC/OS- és Docker Swarm-fürtök REST-végpontokat tesznek közzé. Ezek a végpontok azonban a külvilág számára nem hozzáférhetők. Ezeknek a végpontoknak a kezeléséhez létre kell hoznia egy Secure Shell- (SSH-) alagutat. Miután az SSH-alagút létrejött, kiadhat parancsokat a fürt végpontjaira, és a fürt felhasználói felületét a saját rendszerén belül, egy böngészőablakban tekintheti meg. Ez a dokumentum végigvezeti azon a folyamaton, amellyel SSH-alagutat hozhat létre a Linux, a Windows és az OS X rendszerben.

> [!NOTE]
> Fürtkezelő rendszerrel is létrehozhat SSH-munkamenetet. Ez azonban nem ajánlott. A magán a felügyeleti rendszeren végzett munka növeli a konfiguráció véletlen megváltoztatásának kockázatát.   
> 
> 

## <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>SSH-alagút létrehozása Linux vagy OS X rendszeren
Amikor Linux vagy OS X rendszeren hoz létre SSH-alagutat, először meg kell keresnie az elosztott terhelésű főkiszolgálók nyilvános DNS-nevét. Ehhez ki kell bontania az erőforráscsoportot, hogy az egyes erőforrások megjelenjenek. Keresse meg és jelölje ki a főkiszolgáló nyilvános IP-címét. Ekkor megnyílik a nyilvános IP-címre vonatkozó információkat, köztük a DNS-nevet is tartalmazó panel. Mentse ezt a nevet a későbbi felhasználásra. <br />

![Nyilvános DNS-név](media/pubdns.png)

Most nyisson meg egy kezelőfelületet, és futtassa a következő parancsot, ahol:

a **PORT** az elérhetővé tenni kívánt végpont portja. Swarm esetén ez 2375. DC/OS esetén használja a 80-as portot.  
a **USERNAME** a fürt telepítésekor megadott felhasználónév.  
a **DNSPREFIX** a fürt telepítésekor megadott DNS-előtag.  
a **REGION** az a régió, ahol az erőforráscsoport megtalálható.  
a **PATH_TO_PRIVATE_KEY** [NEM KÖTELEZŐ] a Container Service-fürt létrehozásakor megadott nyilvános kulcshoz tartozó titkos kulcs elérési útja. Ezt a beállítást az -i jelzővel együtt kell használni.

```bash
ssh -L PORT:localhost:PORT -f -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
```
> Az SSH-kapcsolat portja nem a szabványos 22-es, hanem a 2200-as port.
> 
> 

## <a name="dcos-tunnel"></a>DC/OS-alagút
A DC/OS-hez kapcsolódó végpontokhoz vezető alagút megnyitásához adjon ki egy a következőhöz hasonló parancsot:

```bash
sudo ssh -L 80:localhost:80 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

A DC/OS-hez kapcsolódó végpontok az alábbi helyeken érhetők el:

* DC/OS:`http://localhost/`
* Marathon:`http://localhost/marathon`
* Mesos:`http://localhost/mesos`

Ehhez hasonlóan az egyes alkalmazások REST API-jait is ezen az alagúton keresztül érheti el.

## <a name="swarm-tunnel"></a>Swarm-alagút
A Swarm végponthoz vezető alagút megnyitásához adjon ki egy a következőhöz hasonló parancsot:

```bash
ssh -L 2375:localhost:2375 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Most beállíthatja a DOCKER_HOST környezeti változót az alábbi módon. A Docker parancssori felületét továbbra is a szokott módon használhatja.

```bash
export DOCKER_HOST=:2375
```

## <a name="create-an-ssh-tunnel-on-windows"></a>SSH-alagút létrehozása Windows rendszeren
Windows-rendszeren az SSH-alagutak többféleképpen is létrehozhatók. Ez a dokumentum azt ismerteti, hogyan tudja ezt a PuTTY segítségével végrehajtani.

Töltse le a PuTTY alkalmazást Windows rendszerére, majd indítsa el az alkalmazást.

Adjon meg egy állomásnevet, amely a fürt rendszergazdai felhasználónevéből és a fürt első főkiszolgálójának nyilvános DNS-nevéből áll. Az **Állomásnév** a következőképpen fog kinézni: `adminuser@PublicDNS`. A **Port** mezőben adja meg a 2200-as értéket.

![A PuTTY-konfigurálásának 1. lépése](media/putty1.png)

Válassza az **SSH** és a **Hitelesítés** elemet. Adja meg a hitelesítéshez használandó titkos kulcs fájlját a Private key file for authentication mezőben.

![A PuTTY-konfigurálásának 2. lépése](media/putty2.png)

Válassza az **Alagutak** elemet, és konfigurálja az alábbi továbbított portokat:

* **Source port** (Forrásport): Igény szerint – DC/OS esetén használja a 80-as, Swarm estén a 2375-ös portot.
* **Destination** (Cél): DC/OS esetén használja a localhost:80, Swarm esetén a localhost:2375 portot.

Az alábbi példa DC/OS-re van konfigurálva, de Docker Swarm esetén is hasonló.

> [!NOTE]
> Amikor ezt az alagutat létrehozza, a 80-as port nem lehet használatban.
> 
> 

![A PuTTY-konfigurálásának 3. lépése](media/putty3.png)

Amikor elkészült, mentse a kapcsolat konfigurációját, és csatlakozzon a PuTTY-munkamenethez. A csatlakozás után a port konfigurációját a PuTTY eseménynaplójában tekintheti meg.

![A PuTTY eseménynaplója](media/putty4.png)

Ha az alagutat DC/OS-re konfigurálta, a kapcsolódó végpont a következő helyeken érhető el:

* DC/OS:`http://localhost/`
* Marathon:`http://localhost/marathon`
* Mesos:`http://localhost/mesos`

Ha az alagutat a Docker Swarmra konfigurálta, a Swarm fürtöt a Docker parancssori felületén keresztül érheti el. Először a `DOCKER_HOST` elnevezésű Windows környezeti változót kell konfigurálnia a ` :2375` értékkel.

## <a name="next-steps"></a>Következő lépések
Tárolók telepítése és felügyelete DC/OS és Swarm rendszer esetén:

* [Az Azure Container Service és a DC/OS használata](container-service-mesos-marathon-rest.md)
* [Az Azure Container Service és a Docker Swarm használata](container-service-docker-swarm.md)




<!--HONumber=Nov16_HO2-->


