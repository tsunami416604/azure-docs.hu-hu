<properties
   pageTitle="Csatlakozás Azure tárolószolgáltatási fürthöz | Microsoft Azure"
   description="Csatlakozás Azure tárolószolgáltatási fürthöz SSH-alagút segítségével"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Containers, Micro-services, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/12/2016"
   ms.author="rogardle"/>


# Csatlakozás Azure tárolószolgáltatási fürthöz

Az Azure tárolószolgáltatáson telepített DC/OS- és Swarm-fürtök REST-végpontok elérését teszik lehetővé. Ezek a végpontok azonban a külvilág számára nem hozzáférhetők. Ezeknek a végpontoknak a kezeléséhez létre kell hoznia egy Secure Shell- (SSH-) alagutat. Ha az SSH-alagút létrejött, kiadhat parancsokat a fürt végpontjaira, és a fürt felhasználói felületét a saját rendszerén belül, egy böngészőablakban tekintheti meg. Ez a dokumentum végigvezeti azon a folyamaton, amellyel SSH-alagutat hozhat létre a Linux, a Windows és az OS X rendszerben.

>[AZURE.NOTE] Fürtkezelő rendszerrel is létrehozhat SSH-munkamenetet. Ez azonban nem ajánlott. A magán a felügyeleti rendszeren végzett munka növeli a konfiguráció véletlen megváltoztatásának kockázatát.   

## SSH-alagút létrehozása Linux vagy OS X rendszeren

Amikor Linux vagy OS X rendszeren hoz létre SSH-alagutat, először meg kell keresnie az elosztott terhelésű főkiszolgálók nyilvános DNS-nevét. Ehhez ki kell bontania az erőforráscsoportot, hogy az egyes erőforrások megjelenjenek. Keresse meg és jelölje ki a főkiszolgáló nyilvános IP-címét. Ekkor megnyílik a nyilvános IP-címre vonatkozó információkat, köztük a DNS-nevet is tartalmazó panel. Mentse ezt a nevet a későbbi felhasználásra. <br />


![Nyilvános DNS-név](media/pubdns.png)

Most nyisson meg egy kezelőfelületet, és futtassa a következő parancsot, ahol:

a **PORT** az elérhetővé tenni kívánt végpont portja. Swarm esetén ez 2375. DC/OS esetén használja a 80-as portot.  
a **USERNAME** a fürt telepítésekor megadott felhasználónév.  
a **DNSPREFIX** a fürt telepítésekor megadott DNS-előtag.  
a **REGION** az a régió, ahol az erőforráscsoport megtalálható.  
a **PATH_TO_PRIVATE_KEY** [NEM KÖTELEZŐ] a tárolószolgáltatási fürt létrehozásakor megadott nyilvános kulcs titkos kulcsának útvonala. Ezt a beállítást az -i jelzővel együtt kell használni.

```bash
# ssh sample

ssh -L PORT:localhost:PORT -f -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
```
> Az SSH-kapcsolati port 2200, és nem a szokásos 22.

## DC/OS-alagút

A DC/OS-hez kapcsolódó végpontokhoz vezető alagút megnyitásához adjon ki egy a következőhöz hasonló parancsot:

```bash
# ssh sample

sudo ssh -L 80:localhost:80 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

A DC/OS-hez kapcsolódó végpontok az alábbi helyeken érhetők el:

- DC/OS: `http://localhost/`
- Marathon: `http://localhost/marathon`
- Mesos: `http://localhost/mesos`

Ehhez hasonlóan az egyes alkalmazások REST API-jait is ezen az alagúton keresztül érheti el.

## Swarm-alagút

A Swarm végponthoz vezető alagút megnyitásához adjon ki egy a következőhöz hasonló parancsot:

```bash
# ssh sample

ssh -L 2375:localhost:2375 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Most már beállíthatja a DOCKER_HOST környezeti változót az alábbiak szerint, és a szokásos módon használhatja tovább a Docker parancssori felületet (CLI).

```bash
export DOCKER_HOST=:2375
```

## SSH-alagút létrehozása Windows rendszeren

Windows-rendszeren az SSH-alagutak többféleképpen is létrehozhatók. Ez a dokumentum azt ismerteti, hogyan tudja ezt a PuTTY segítségével végrehajtani.

Töltse le a PuTTY alkalmazást Windows rendszerére, majd indítsa el az alkalmazást.

Adjon meg egy állomásnevet, amely a fürt rendszergazdai felhasználónevéből és a fürt első főkiszolgálójának nyilvános DNS-nevéből áll. Az **Állomásnév** a következőképpen fog kinézni: `adminuser@PublicDNS`. A **Port** mezőben adja meg a 2200-as értéket.

![A PuTTY-konfigurálásának 1. lépése](media/putty1.png)

Válassza ki az `SSH` és a `Authentication` lehetőséget. Adja meg a hitelesítéshez használandó titkos kulcs fájlját a Private key file for authentication mezőben.

![A PuTTY-konfigurálásának 2. lépése](media/putty2.png)

Válassza ki az `Tunnels` lehetőséget, és konfigurálja a következő továbbított portokat:
- **Source port** (Forrásport): Igény szerint – DC/OS esetén használja a 80-as, Swarm estén a 2375-ös portot.
- **Destination** (Cél): DC/OS esetén használja a localhost:80, Swarm esetén a localhost:2375 portot.

Az alábbi példa DC/OS-re van konfigurálva, de Docker Swarm esetén is hasonló.

>[AZURE.NOTE] Amikor ezt az alagutat létrehozza, a 80-as port nem lehet használatban.

![A PuTTY-konfigurálásának 3. lépése](media/putty3.png)

Amikor elkészült, mentse a kapcsolat konfigurációját, és csatlakozzon a PuTTY-munkamenethez. A csatlakozás után a port konfigurációját a PuTTY eseménynaplójában tekintheti meg.

![A PuTTY eseménynaplója](media/putty4.png)

Ha az alagutat DC/OS-re konfigurálta, a kapcsolódó végpont a következő helyeken érhető el:

- DC/OS: `http://localhost/`
- Marathon: `http://localhost/marathon`
- Mesos: `http://localhost/mesos`

Ha az alagutat a Docker Swarmra konfigurálta, a Swarm fürtöt a Docker parancssori felületén keresztül érheti el. Először a `DOCKER_HOST` elnevezésű Windows környezeti változót kell konfigurálnia a ` :2375` értékkel.

## Következő lépések

Tárolók telepítése és felügyelete DC/OS és Swarm rendszer esetén.

[Az Azure tárolószolgáltatás és a DC/OS használata](container-service-mesos-marathon-rest.md)
[Az Azure tárolószolgáltatás és a Docker Swarm használata](container-service-docker-swarm.md)



<!--HONumber=Jun16_HO2-->


