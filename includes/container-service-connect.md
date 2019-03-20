---
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: 48deeec7a2c8767ab5dbb81b622e6d40483ed455
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58093553"
---
# <a name="make-a-remote-connection-to-a-kubernetes-dcos-or-docker-swarm-cluster"></a>Távoli kapcsolat létrehozása egy Kubernetes-, DC/OS- vagy Docker Swarm-fürttel
Miután létrehozott egy Azure Container Service-fürtöt, csatlakoznia kell hozzá a számítási feladatok üzembe helyezéséhez és felügyeletéhez. Ez a cikk leírja, hogyan csatlakozhat a fürt fő virtuális gépéhez egy távoli számítógépről. 

A Kubernetes-, DC/OS- és Docker Swarm-fürtök helyi HTTP-végpontokat biztosítanak. A Kubernetes esetében ez a végpont biztonságosan van közzétéve az interneten, és az internethez csatlakozó bármely gépről elérhető a `kubectl` parancssori eszköz futtatásával. 

A DC/OS és a Docker Swarm esetében azt javasoljuk, hogy hozzon létre egy Secure Shell- (SSH-) alagutat a helyi számítógép és a fürtkezelő rendszer között. Az alagút létrehozása után futtathat HTTP-végpontokat használó parancsokat, és megtekintheti a vezénylő webes felületét (ha elérhető) a helyi rendszerről. 

## <a name="prerequisites"></a>Előfeltételek

* Egy, az [Azure Container Service](../articles/container-service/dcos-swarm/container-service-deployment.md)-ben üzembe helyezett Kubernetes-, DC/OS- vagy Docker Swarm-fürt.
* Titkos SSH RSA-kulcsfájl, amely a fürthöz az üzembe helyezéskor hozzáadott nyilvános kulcshoz tartozik. Ezek a parancsok feltételezik, hogy a titkos SSH-kulcs a következő helyen található a számítógépen: `$HOME/.ssh/id_rsa`. További információkat a [macOS és Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) rendszerre vagy a [Windows](../articles/virtual-machines/linux/ssh-from-windows.md) rendszerre vonatkozó útmutatókban találhat. Ha az SSH-kapcsolat nem működik, lehetséges, hogy [új SSH-kulcsot kell létrehoznia](../articles/virtual-machines/linux/troubleshoot-ssh-connection.md).

## <a name="connect-to-a-kubernetes-cluster"></a>Csatlakozás Kubernetes-fürthöz

Kövesse a következő lépéseket a `kubectl` telepítéséhez és konfigurálásához.

> [!NOTE] 
> Előfordulhat, hogy Linux vagy macOS rendszeren a jelen szakaszban leírt parancsokat a `sudo` használatával kell futtatni.
> 

### <a name="install-kubectl"></a>A kubectl telepítése
Ez az eszköz telepítésének egyik módja az, hogy használja a `az acs kubernetes install-cli` Azure CLI-parancsot. A parancs futtatásához, ellenőrizze, hogy Ön [telepített](/cli/azure/install-az-cli2) az Azure CLI legújabb verzióját és a egy Azure-fiókkal van bejelentkezve (`az login`).

```azurecli
# Linux or macOS
az acs kubernetes install-cli [--install-location=/some/directory/kubectl]

# Windows
az acs kubernetes install-cli [--install-location=C:\some\directory\kubectl.exe]
```

Másik lehetőségként letöltheti a legújabb `kubectl`-ügyfelet közvetlenül a [Kubernetes-kiadások oldaláról](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md). További információ: [A kubectl telepítése és beállítása](https://kubernetes.io/docs/tasks/kubectl/install/).

### <a name="download-cluster-credentials"></a>A fürt hitelesítő adatainak letöltése
A `kubectl` telepítését követően át kell másolnia a fürt hitelesítő adatait a gépre. A hitelesítő adatok beszerzésének egyik módja az `az acs kubernetes get-credentials` parancs használata. Továbbítsa az erőforráscsoport nevét és a tárolószolgáltatás erőforrásának nevét:

```azurecli
az acs kubernetes get-credentials --resource-group=<cluster-resource-group> --name=<cluster-name>
```

Ez a parancs letölti a fürt hitelesítő adatait a `$HOME/.kube/config` helyre, ahol a `kubectl` megtalálja majd.

Másik lehetőségként az `scp` használatával is biztonságosan átmásolhatja a fájlt a fő virtuális gép `$HOME/.kube/config` mappájából a helyi gépre. Példa:

```bash
mkdir $HOME/.kube
scp azureuser@<master-dns-name>:.kube/config $HOME/.kube/config
```

Windows rendszeren a Windowson futó Ubuntu Bash-környezetet, a PuTTy biztonságos fájlmásoló ügyfelét vagy egy hasonló eszközt használhat.

### <a name="use-kubectl"></a>Kubectl használata

A `kubectl` konfigurálását követően a fürt csomópontjainak listázásával tesztelheti a kapcsolatot:

```bash
kubectl get nodes
```

Egyéb `kubectl` parancsokat is kipróbálhat. Például megtekintheti a Kubernetes irányítópultot. Először futtasson egy proxyt a Kubernetes API-kiszolgálóhoz:

```bash
kubectl proxy
```

A Kubernetes felhasználói felülete a következő címen érhető el: `http://localhost:8001/ui`.

További információ: [A Kubernetes gyors üzembe helyezése](http://kubernetes.io/docs/user-guide/quick-start/).

## <a name="connect-to-a-dcos-or-swarm-cluster"></a>Csatlakozás DC/OS- vagy Swarm-fürthöz

Az Azure Container Service által üzembe helyezett DC/OS- és Docker Swarm-fürtök használatához kövesse az SSH-alagút helyi Linux, macOS vagy Windows rendszerről való létrehozására vonatkozó utasításokat. 

> [!NOTE]
> A jelen útmutatások elsősorban a TCP-forgalom SSH-n keresztüli bújtatására vonatkoznak. Interaktív SSH-munkamenetet az egyik belső fürtkezelő rendszerrel is indíthat, ez azonban nem ajánlott. A közvetlenül a belső rendszeren végzett munka növeli a konfiguráció véletlen módosításának kockázatát.  
> 

### <a name="create-an-ssh-tunnel-on-linux-or-macos"></a>SSH-alagút létrehozása Linux vagy macOS rendszeren
Amikor Linux vagy macOS rendszeren hoz létre SSH-alagutat, először meg kell keresnie az elosztott terhelésű főkiszolgálók nyilvános DNS-nevét. Kövesse az alábbi lépéseket:


1. Az [Azure portálon](https://portal.azure.com) lépjen a tárolószolgáltatási fürtjét tartalmazó erőforráscsoporthoz. Bontsa ki az erőforráscsoportot, hogy az egyes erőforrások megjelenjenek. 

2. Kattintson a **Tárolószolgáltatás** erőforrására, majd az **Áttekintés** elemre. Ekkor az **Alapvető erőforrások** területen megjelenik a fürt **teljes fő tartományneve**. Mentse ezt a nevet a későbbi felhasználásra. 

    ![Nyilvános DNS-név](./media/container-service-connect/pubdns.png)

    Másik lehetőségként futtassa az `az acs show` parancsot a tárolószolgáltatáson. Keresse meg a **Master Profile:fqdn** tulajdonságot a parancskimenetben.

3. Most nyisson meg egy kezelőfelületet, és futtassa az `ssh` parancsot a következő értékek megadásával: 

    a **LOCAL_PORT** az alagút szolgáltatásoldali TCP-portja, amelyhez csatlakozni kell. Swarm esetén állítsa ezt 2375 értékre. DC/OS esetén állítsa ezt 80 értékre. 
    a **REMOTE_PORT** az elérhetővé tenni kívánt végpont portja. Swarm esetén használja a 2375-ös portot. DC/OS esetén használja a 80-as portot.  
    a **USERNAME** a fürt telepítésekor megadott felhasználónév.  
    a **DNSPREFIX** a fürt telepítésekor megadott DNS-előtag.  
    a **REGION** az a régió, ahol az erőforráscsoport megtalálható.  
    a **PATH_TO_PRIVATE_KEY** [NEM KÖTELEZŐ] a fürt létrehozásakor megadott nyilvános kulcshoz tartozó titkos kulcs elérési útja. Ezt a beállítást a `-i` jelzővel együtt kell használni.

    ```bash
    ssh -fNL LOCAL_PORT:localhost:REMOTE_PORT -p 2200 [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com
    ```
  
   > [!NOTE]
   > Az SSH-kapcsolati port 2200, és nem a szokásos 22-es port. A több fő virtuális géppel rendelkező fürtökön ez az első fő virtuális gép kapcsolódási portja.
   > 

   A parancs nem ad vissza kimenetet.

A DC/OS-re és a Swarmra vonatkozó példák a következő szakaszokban találhatók.    

### <a name="dcos-tunnel"></a>DC/OS-alagút
A DC/OS-végpontok alagútjának megnyitásához futtasson egy olyan parancsot, amely a következőhöz hasonló:

```bash
sudo ssh -fNL 80:localhost:80 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com 
```

> [!NOTE]
> Győződjön meg arról, hogy nincs másik olyan helyi folyamat, amely lekötné a 80-as portot. Ha szükséges, beállíthat más, a 80-as porttól eltérő helyi portokat is (például a 8080-as portot). Ha azonban ezt a portot használja, előfordulhat, hogy egyes webes felhasználói felületek hivatkozásai nem működnek.
>

Most már elérheti a DC/OS-hez kapcsolódó végpontokat a helyi rendszerről, az alábbi URL-címeken keresztül (ha a 80-as helyi portot használja):

* DC/OS:`http://localhost:80/`
* Marathon:`http://localhost:80/marathon`
* Mesos:`http://localhost:80/mesos`

Ehhez hasonlóan az egyes alkalmazások REST API-jait is ezen az alagúton keresztül érheti el.

### <a name="swarm-tunnel"></a>Swarm-alagút
A Swarm-végponthoz vezető alagút megnyitásához futtasson egy olyan parancsot, amely a következőhöz hasonló:

```bash
ssh -fNL 2375:localhost:2375 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com
```
> [!NOTE]
> Győződjön meg arról, hogy nincs másik olyan helyi folyamat, amely lekötné a 2375-ös portot. Ha például helyileg futtatja a Docker-démont, az alapértelmezés szerint a 2375-ös portot használja. Ha szükséges, beállíthat más, a 2375-ös porttól eltérő helyi portokat is.
>

Ekkor a Docker Swarm-fürtöt a Docker parancssori felületén (Docker CLI) keresztül érheti el a helyi rendszeren. A telepítési utasításokért lásd [a Docker telepítését](https://docs.docker.com/engine/installation/) ismertető cikket.

A DOCKER_HOST környezeti változót az alagút számára konfigurált helyi portra állíthatja be. 

```bash
export DOCKER_HOST=:2375
```

Futtasson olyan Docker-parancsokat, amelyek a Docker Swarm-fürthöz nyitnak alagutat. Példa:

```bash
docker info
```

### <a name="create-an-ssh-tunnel-on-windows"></a>SSH-alagút létrehozása Windows rendszeren
Windows-rendszeren az SSH-alagutak többféleképpen is létrehozhatók. Ubuntu Bash-környezet Windowson vagy hasonló eszköz futtatásakor a cikk korábbi részében megadott, macOS és Linux rendszerre vonatkozó, SSH-alagútkezelési utasításokat követve járhat el. Ez a szakasz ismerteti, hogyan hozható létre az alagút a PuTTY használatával, a Windows rendszeren elérhető egyik lehetséges alternatívaként.

1. [Töltse le a PuTTY alkalmazást](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) Windows rendszerére.

2. Futtassa az alkalmazást.

3. Adjon meg egy állomásnevet, amely a fürt rendszergazdai felhasználónevéből és a fürt első főkiszolgálójának nyilvános DNS-nevéből áll. A **Host Name** (Gazdagép neve) a következőhöz hasonló: `azureuser@PublicDNSName`. A **Port** mezőben adja meg a 2200-as értéket.

    ![A PuTTY-konfigurálásának 1. lépése](./media/container-service-connect/putty1.png)

4. Válassza az **SSH > Auth** (SSH > Hitelesítés) parancsot. Adja meg a hitelesítéshez használandó titkos kulcsfájl (.ppk) elérési útját. Ez a fájl a [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) vagy egy hasonló eszköz segítségével hozható létre a fürt létrehozásakor használt SSH-kulcsból.

    ![A PuTTY-konfigurálásának 2. lépése](./media/container-service-connect/putty2.png)

5. Válassza az **SSH > Tunnels** (SSH > Alagutak) elemet, és konfigurálja az alábbi továbbított portokat:

   * **Forrásport:** Használja a 80-as a DC/OS vagy a 2375-ös swarm.
   * **Cél:** Swarm használja DC/OS 2375 localhost:80.

     Az alábbi példa DC/OS-re van konfigurálva, de Docker Swarm esetén is hasonló.

     > [!NOTE]
     > Amikor ezt az alagutat létrehozza, a 80-as port nem lehet használatban.
     > 

     ![A PuTTY-konfigurálásának 3. lépése](./media/container-service-connect/putty3.png)

6. Amikor elkészült, a **Session > Save** (Munkamenet > Mentés) paranccsal mentse a kapcsolat konfigurációját.

7. A PuTTY-munkamenethez az **Open** (Megnyitás) gombra kattintva csatlakozhat. A csatlakozás után a port konfigurációját a PuTTY eseménynaplójában tekintheti meg.

    ![A PuTTY eseménynaplója](./media/container-service-connect/putty4.png)

Miután az alagutat DC/OS-re konfigurálta, a kapcsolódó végpont a következő helyeken érhető el:

* DC/OS:`http://localhost/`
* Marathon:`http://localhost/marathon`
* Mesos:`http://localhost/mesos`

Miután az alagutat Swarmra konfigurálta, nyissa meg a Windows beállításait, és konfiguráljon egy `DOCKER_HOST` elnevezésű rendszerkörnyezeti változót a `:2375` értékkel. Ezután a Swarm-fürtöt a Docker parancssori felületén keresztül érheti el.

## <a name="next-steps"></a>További lépések
Tárolók telepítése és felügyelete a fürtben:

* [Az Azure Container Service és a Kubernetes használata](../articles/container-service/kubernetes/container-service-kubernetes-ui.md)
* [Az Azure Container Service és a DC/OS használata](../articles/container-service//dcos-swarm/container-service-mesos-marathon-rest.md)
* [Az Azure Container Service és a Docker Swarm használata](../articles//container-service/dcos-swarm/container-service-docker-swarm.md)

