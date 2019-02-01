---
title: Oktatóanyag – Fejlesztési folyamat létrehozása az Azure-ban a Jenkins használatával | Microsoft Docs
description: Oktatóanyag – Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre az Azure-ban egy Jenkins-alapú virtuális gépet, amely a GitHubról kéri le a kódok véglegesítését, és létrehoz egy új Docker-tárolót az alkalmazása futtatásához.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: fb47654a3eb6ea45d2aebab956b555e720faef4c
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55487115"
---
# <a name="tutorial-create-a-development-infrastructure-on-a-linux-vm-in-azure-with-jenkins-github-and-docker"></a>Oktatóanyag: Egy Linux rendszerű virtuális gépen az Azure-ban a Jenkins, GitHub és Docker-fejlesztési infrastruktúra létrehozása

Az alkalmazás fejlesztésének létrehozási és tesztelési fázisának automatizálásához használhat egy folyamatos integrációs és fejlesztési (CI/CD) folyamatot. Ebben az oktatóanyagban létrehozhat egy CI/CD folyamatot egy Azure-beli virtuális gépen, továbbá megismerkedhet a következőkkel is:

> [!div class="checklist"]
> * Jenkins virtuális gép létrehozása
> * A Jenkins telepítése és konfigurálása
> * Webhook-integráció létrehozása a Github és a Jenkins között
> * Jenkins buildfeladatok létrehozása és aktiválása a GitHub-véglegesítésekből
> * Docker-rendszerkép létrehozása az alkalmazáshoz
> * Annak ellenőrzése, hogy a GitHub-véglegesítések új Docker-rendszerképet hoznak létre és frissítik a futó alkalmazást

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-jenkins-instance"></a>Jenkins-példány létrehozása
A [Linux virtuális gép testre szabása az első rendszerbetöltéskor](tutorial-automate-vm-deployment.md) című korábbi oktatóanyagból megtudhatta, hogyan automatizálható egy virtuális gép testreszabása a cloud-init használatával. Ez az oktatóanyag egy cloud-init-fájlt használ a Jenkins és a Docker egy virtuális gépre való telepítéséhez. A Jenkins egy népszerű, nyílt forráskódú automatizáló kiszolgáló, amely zökkenőmentesen integrálható az Azure-ral a folyamatos integráció (CI) és a folyamatos továbbítás (CD) érdekében. A Jenkins további oktatóanyagait itt találja: [Jenkins az Azure-központban](https://docs.microsoft.com/azure/jenkins/).

Az aktuális felületen hozzon létre egy *cloud-init-jenkins.txt* nevű fájlt, és illessze bele a következő konfigurációt. Például hozza létre a fájlt a Cloud Shellben, és ne a helyi gépén. Írja be a `sensible-editor cloud-init-jenkins.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. Ügyeljen arra, hogy megfelelően másolja ki a teljes cloud-init-fájlt, különösen az első sort:

```yaml
#cloud-config
package_upgrade: true
write_files:
  - path: /etc/systemd/system/docker.service.d/docker.conf
    content: |
      [Service]
        ExecStart=
        ExecStart=/usr/bin/dockerd
  - path: /etc/docker/daemon.json
    content: |
      {
        "hosts": ["fd://","tcp://127.0.0.1:2375"]
      }
runcmd:
  - apt install default-jre -y
  - wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
  - sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
  - apt-get update && apt-get install jenkins -y
  - curl -sSL https://get.docker.com/ | sh
  - usermod -aG docker azureuser
  - usermod -aG docker jenkins
  - service jenkins restart
```

Mielőtt létrehozhatna egy virtuális gépet, létre kell hoznia egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroupJenkins* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive 
az group create --name myResourceGroupJenkins --location eastus
```

Most hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. Használja a `--custom-data` paramétert a cloud-init konfigurációs fájl megadásához. Adja meg a *cloud-init-jenkins.txt* teljes elérési útját, ha az aktuális munkakönyvtáron kívülre mentette.

```azurecli-interactive 
az vm create --resource-group myResourceGroupJenkins \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
```

A virtuális gép létrehozása és konfigurálása néhány percig tart.

A virtuális gép felé irányuló webes forgalom engedélyezéséhez használja az [az vm open-port](/cli/azure/vm#az_vm_open_port) parancsot a *8080*-as port megnyitásához a Jenkins forgalma előtt, és az *1337*-es port megnyitásához a Node.js-alkalmazás előtt, amelyet egy mintaalkalmazás futtatásához használunk:

```azurecli-interactive 
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 8080 --priority 1001
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 1337 --priority 1002
```


## <a name="configure-jenkins"></a>A Jenkins konfigurálása
A Jenkins-példány eléréséhez kérje le a virtuális gép nyilvános IP-címét:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Biztonsági okokból a Jenkins telepítésének megkezdéséhez meg kell adnia az eredeti rendszergazdai jelszót, amelyet egy szöveges fájl tartalmaz a virtuális gépen. Használja az előző lépésben lekért nyilvános IP-címet a virtuális gép SSH-n keresztül való eléréséhez:

```bash
ssh azureuser@<publicIps>
```

Tekintse meg a Jenkins-telepítéshez tartozó `initialAdminPassword` értéket és másolja a vágólapra:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Ha a fájl még nem elérhető, várjon néhány percet, hogy a cloud-init elkészülhessen a Jenkins és a Docker telepítésével.

Ezután indítson el egy webböngészőt és lépjen a `http://<publicIps>:8080` webhelyre. Végezze el a Jenkins kezdeti beállítását az alábbiak szerint:

- Válassza a **Select plugins to install** (Válassza ki a telepíteni kívánt beépülő modulokat) lehetőséget
- Keressen a *GitHub* kifejezésre a fenti szövegmezőben. Jelölje be a *GitHub* jelölőnégyzetét, majd válassza az **Install** (Telepítés) lehetőséget.
- Hozza létre az első rendszergazdai felhasználót. Írjon be egy felhasználónevet, például **admin**, majd adja meg a saját biztonságos jelszavát. Végül gépelje be teljes nevét és e-mail-címét.
- Válassza a **Save and Finish** (Mentés és befejezés) elemet
- Amint Jenkins készen áll, kattintson a **Start using Jenkins** (Jenkins használatának megkezdése) elemre
  - Ha a webböngésző Jenkins használatának megkezdésekor egy üres lapot jelenít meg, indítsa újra a Jenkins szolgáltatást. Az SSH-munkamenetben gépelje be a `sudo service jenkins restart` parancsot, majd frissítse a webböngészőt.
- A létrehozott felhasználónév és jelszó segítségével jelentkezzen be a Jenkins szolgáltatásba.


## <a name="create-github-webhook"></a>GitHub-webhook létrehozása
A GitHubbal való integráció konfigurálásához nyissa meg a [Node.js „Helló világ” mintaalkalmazást](https://github.com/Azure-Samples/nodejs-docs-hello-world) az Azure-példatárból. Ahhoz, hogy elágaztassa a példatárt a saját GitHub-fiókja felé, kattintson a **Fork** (Elágaztatás) gombra a jobb felső sarokban.

Hozzon létre egy webhookot a létrehozott elágazásban:

- Válassza a **Settings** (Beállítások) lehetőséget, majd az **Integrations & services** (Integrációk és szolgáltatások) lehetőséget a bal oldalon.
- Válassza az **Add service** (Szolgáltatás hozzáadása) lehetőséget, majd a szűrőmezőbe írja be a *Jenkins* kifejezést.
- Válassza ki a *Jenkins (GitHub plugin)* elemet.
- A **Jenkins hook URL**-címének írja be: `http://<publicIps>:8080/github-webhook/`. Ügyeljen rá, hogy az URL-címből ne maradjon le a „/” záró karakter.
- Válassza az **Add service** (Szolgáltatás hozzáadása) lehetőséget.

![A GitHub-webhook hozzáadása az elágaztatott adattárhoz](media/tutorial-jenkins-github-docker-cicd/github_webhook.png)


## <a name="create-jenkins-job"></a>Jenkins-feladat létrehozása
Ahhoz, hogy a Jenkins válaszoljon a GitHub eseményeire, például egy kód véglegesítésére, hozzon létre egy Jenkins-feladatot. Az URL-ek használata a saját GitHub-elágazásokhoz.

A Jenkins webhely kezdőlapján válassza a **Create new jobs** (Új feladatok létrehozása) lehetőséget.

- A feladatnak adja a *HelloWorld* nevet. Válassza a **Freestyle project** (Szabad stílusú projekt) lehetőséget, majd kattintson az **OK** gombra.
- A **General** (Általános) szakaszban válassza ki a **GitHub project** (GitHub-projekt) lehetőséget, majd adja meg az elágaztatott adattár URL-címét, például: *https://github.com/cynthn/nodejs-docs-hello-world*
- A **Source code management** (Forráskódkezelés) szakaszban válassza a **Git** elemet, majd adja meg az elágaztatott *.git*-adattár URL-címét, például: *https://github.com/cynthn/nodejs-docs-hello-world.git*
- A **Build Triggers** (Eseményindítók létrehozása) szakaszban válassza a **GitHub hook trigger for GITScm polling** (GitHub beavatkozási pont eseményindító GITScm lekérdezés esetén) lehetőséget.
- A **Build** (Létrehozás) szakaszban válassza az **Add build step** (Létrehozási lépés hozzáadása) lehetőséget. Válassza az **Execute shell** (Felület futtatása) lehetőséget, majd a parancssori ablakba írja be a következőt: `echo "Test"`.
- Kattintson a feladatablak alján található **Save** (Mentés) gombra.


## <a name="test-github-integration"></a>GitHub-integráció tesztelése
A GitHub és a Jenkins integrációjának teszteléséhez véglegesítsen egy változtatást az elágazásban. 

Lépjen vissza a GitHub webes kezelőfelületére, válassza ki az elágaztatott adattárat, majd válassza ki az **index.js** fájlt. A ceruza ikonra kattintva szerkessze a fájlt. A hatodik sor legyen a következő:

```javascript
response.end("Hello World!");
```

A módosítások véglegesítéséhez kattintson az alul lévő **Commit changes** (Módosítások véglegesítése) gombra.

A Jenkinsben elindul egy új létrehozás a feladatoldal bal alsó sarkában található **Build history** (Létrehozási előzmények) szakaszban. Válassza ki a létrehozás számának hivatkozását, majd válassza a bal oldali **Console output** (Konzolkimenet) lehetőséget. Megtekintheti a Jenkins által végrehajtott lépéseket, ahogy az lehívja a kódot a GitHubról, a létrehozási művelet pedig a következő üzenetet jeleníti meg a konzolon: `Testing`. A GitHubon végzett minden egyes véglegesítéskor a webhook kapcsolatba lép a Jenkinsszel és aktivál egy új létrehozást.


## <a name="define-docker-build-image"></a>Docker-létrehozási rendszerkép definiálása
A GitHub-véglegesítéseken alapuló Node.js-alkalmazás futásának megtekintéséhez hozzunk létre egy Docker-rendszerképet az alkalmazás futtatásához. A rendszerkép egy Docker-fájlból jön létre, amely meghatározza, hogy az alkalmazást futtató tárolót hogyan kell konfigurálni. 

A virtuális géphez vezető SSH-kapcsolaton keresztül váltson át a Jenkins-munkaterület könyvtárára, amely az előző lépésben létrehozott feladat nevét viseli. Ebben a példában a neve *HelloWorld*.

```bash
cd /var/lib/jenkins/workspace/HelloWorld
```

Hozzon létre egy fájlt ebben a munkaterület-könyvtárban a `sudo sensible-editor Dockerfile` használatával, és másolja bele a következő tartalmakat. Ügyeljen arra, hogy megfelelően másolja ki a teljes Docker-fájlt, különösen az első sort:

```yaml
FROM node:alpine

EXPOSE 1337

WORKDIR /var/www
COPY package.json /var/www/
RUN npm install
COPY index.js /var/www/
```

A Docker-fájl az alap Node.js-rendszerképet használja az Alpine Linux használatával, megnyitja az 1337-es portot, amelyen a Hello World alkalmazás fut, majd másolja az alkalmazás fájljait, és végül inicializálja az alkalmazást.


## <a name="create-jenkins-build-rules"></a>Jenkins létrehozási szabályok létrehozása
Az előző lépésben létrehoztunk egy alapszintű Jenkins létrehozási szabályt, amely egy üzenetet jelenített meg a konzolon. Hozzuk létre azt a lépést, amely a Docker-fájlunk használatával futtatja az alkalmazást.

A Jenkins-példányba visszalépve válassza ki az előző lépésben létrehozott feladatot. Válassza a **Configure** (Konfigurálás) lehetőséget a bal oldalon, majd görgessen le a **Build** (Létrehozás) szakaszig:

- Távolítsa el a meglévő `echo "Test"` létrehozási lépést. Kattintson a meglévő létrehozási lépés mezőjének jobb felső sarkában található vörös keresztre.
- Válassza az **Add build step** (Létrehozási lépés hozzáadása), majd az **Execute shell** (Felület futtatása) lehetőséget.
- A **Command** (Parancs) mezőbe írja be az alábbi Docker-parancsokat, majd kattintson a **Save** (Mentés) gombra:

  ```bash
  docker build --tag helloworld:$BUILD_NUMBER .
  docker stop helloworld && docker rm helloworld
  docker run --name helloworld -p 1337:1337 helloworld:$BUILD_NUMBER node /var/www/index.js &
  ```

A Docker-létrehozási lépések létrehoznak egy rendszerképet, és felcímkézik azt a Jenkins verziószámával, hogy nyomon lehessen követni a rendszerképek előzményeit. A rendszer az alkalmazást futtató meglévő tárolókat leállítja és eltávolítja. Ezután elindul egy új tároló, amely a rendszerképet használja és a GitHub legújabb véglegesítésein alapuló Node.js-alkalmazást futtatja.


## <a name="test-your-pipeline"></a>A folyamat tesztelése
A teljes folyamat működésének megtekintéséhez szerkessze ismét az elágaztatott GitHub-adattárban található *index.js* fájlt, majd válassza a **Commit change** (Módosítás véglegesítése) lehetőséget. A Jenkinsben elindul egy új feladat a GitHub webhookja alapján. A rendszer néhány másodperc alatt létrehozza a Docker-rendszerképet és elindítja az alkalmazást egy új tárolóban.

Szükség esetén kérje le újra a virtuális gép nyilvános IP-címét:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Nyisson meg egy webböngészőt, majd írja be a következőt: `http://<publicIps>:1337`. Megjelenik a Node.js-alkalmazás, amely az alábbiak szerint tükrözi a legújabb GitHub-véglegesítéseket:

![Node.js-alkalmazás futtatása](media/tutorial-jenkins-github-docker-cicd/running_nodejs_app.png)

Most szerkessze újra az *index.js* fájlt a GitHubban, majd véglegesítse a módosítást. Várjon néhány másodpercet, amíg a Jenkins elvégzi a feladatot, majd frissítse a böngészőt, hogy az alkalmazás új verzióját lássa, amely egy új tárolóban fut:

![Node.js-alkalmazás futtatása egy újabb GitHub-véglegesítés után](media/tutorial-jenkins-github-docker-cicd/another_running_nodejs_app.png)


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban úgy konfiguráltuk a GitHubot, hogy minden egyes kódvéglegesítéskor futtasson egy Jenkins létrehozási feladatot, majd helyezzen üzembe egy Docker-tárolót az alkalmazás teszteléséhez. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Jenkins virtuális gép létrehozása
> * A Jenkins telepítése és konfigurálása
> * Webhook-integráció létrehozása a Github és a Jenkins között
> * Jenkins buildfeladatok létrehozása és aktiválása a GitHub-véglegesítésekből
> * Docker-rendszerkép létrehozása az alkalmazáshoz
> * Annak ellenőrzése, hogy a GitHub-véglegesítések új Docker-rendszerképet hoznak létre és frissítik a futó alkalmazást

A következő oktatóanyag azt mutatja be, hogyan integrálható a Jenkins és az Azure DevOps Services.

> [!div class="nextstepaction"]
> [Alkalmazások üzembe helyezése a Jenkins és az Azure DevOps Services használatával](tutorial-build-deploy-jenkins.md)
