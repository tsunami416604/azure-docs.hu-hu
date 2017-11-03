---
title: "A fejlesztési folyamat létrehozása az Azure-ban Jenkins |} Microsoft Docs"
description: "Megtudhatja, hogyan Jenkins virtuális gép létrehozása, amely kéri le a Githubról. az egyes kód véglegesítési és összeállít egy új Docker-tároló az alkalmazás futtatásához Azure-ban"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 52408184c8cff53f8bb7006fa940b0db4b900db4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-development-infrastructure-on-a-linux-vm-in-azure-with-jenkins-github-and-docker"></a>A Linux virtuális gép az Azure-ban Jenkins, a Githubon és a Docker a fejlesztési infrastruktúra létrehozása
Automatizálható a build és a tesztelési fázis alkalmazásának fejlesztését, használhatja a folyamatos integrációt és a központi telepítés (CI/CD) folyamat. Ebben az oktatóanyagban létrehoz egy CI/CD folyamat egy Azure virtuális gépen történő is beleértve:

> [!div class="checklist"]
> * Jenkins virtuális gép létrehozása
> * Telepítse és konfigurálja a Jenkins
> * GitHub és Jenkins integrációjával webhook létrehozása
> * Hozzon létre és Jenkins feladatok létrehozása a Githubról eseményindító véglegesítése
> * Az alkalmazás Docker-lemezkép létrehozása
> * Ellenőrizze a Githubon véglegesíti és hozhat létre. új Docker-lemezkép alkalmazást futtató frissítések


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez az oktatóanyag van szükség, hogy futnak-e az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="create-jenkins-instance"></a>Jenkins példány létrehozása
Az oktatóanyag előző [első indításakor Linux virtuális gépek testreszabása](tutorial-automate-vm-deployment.md), megtudta, hogyan automatizálható a felhő inicializálás a virtuális gép testreszabása. Ez az oktatóanyag egy felhő-init fájl Jenkins és Docker telepítése a virtuális gép használja. Jenkins egy népszerű nyílt forráskódú fürtautomatizálási kiszolgáló, amely zökkenőmentesen integrálható az Azure-ral ahhoz, hogy folyamatos integrációt (CI) és a folyamatos kézbesítési (CD). További oktatóanyagok Jenkins használatáról, tekintse meg a [Azure központban Jenkins](https://docs.microsoft.com/azure/jenkins/).

Hozzon létre egy fájlt az aktuális rendszerhéjban *felhő-init.txt* , majd illessze be a következő konfigurációt. A felhő rendszerhéj nem a helyi számítógépen hozzon létre például a fájlt. Adja meg `sensible-editor cloud-init-jenkins.txt` hozza létre a fájlt, és elérhető szerkesztők listájának megtekintéséhez. Győződjön meg arról, hogy az egész felhő inicializálás fájl megfelelően lett lemásolva különösen az első sor:

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
  - wget -q -O - https://jenkins-ci.org/debian/jenkins-ci.org.key | apt-key add -
  - sh -c 'echo deb http://pkg.jenkins-ci.org/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
  - apt-get update && apt-get install jenkins -y
  - curl -sSL https://get.docker.com/ | sh
  - usermod -aG docker azureuser
  - usermod -aG docker jenkins
  - service jenkins restart
```

A virtuális gépek létrehozása előtt hozzon létre egy erőforráscsoportot, a [az csoport létrehozása](/cli/azure/group#create). Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroupJenkins* a a *eastus* helye:

```azurecli-interactive 
az group create --name myResourceGroupJenkins --location eastus
```

Most létrehozza a virtuális gép és [az virtuális gép létrehozása](/cli/azure/vm#create). Használja a `--custom-data` paraméter felelt meg a felhő inicializálás konfigurációs fájlban. Adja meg a teljes elérési útja *felhő-init-jenkins.txt* Ha mentette a fájlt a jelenlegi munkakönyvtár kívül.

```azurecli-interactive 
az vm create --resource-group myResourceGroupJenkins \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
```

A virtuális gépek létrehozása és konfigurálása a néhány percet vesz igénybe.

A virtuális gép elérni kívánt webes forgalom engedélyezéséhez használja [az vm-port megnyitása](/cli/azure/vm#open-port) port megnyitásához *8080-as* Jenkins forgalom és a port *1337* számára a Node.js-alkalmazás, amely egy mintaalkalmazást futtatására szolgál:

```azurecli-interactive 
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 8080 --priority 1001
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 1337 --priority 1002
```


## <a name="configure-jenkins"></a>Jenkins konfigurálása
Fér hozzá a Jenkins példányát, szerezze be a virtuális gép nyilvános IP-címe:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Biztonsági okokból meg kell adnia a kezdeti rendszergazdai jelszavát, amelyet a virtuális gép Jenkins telepítés elindításához a fájlt tárolja. Az SSH-kapcsolatot a virtuális gép számára az előző lépésben beszerzett nyilvános IP-cím használata:

```bash
ssh azureuser@<publicIps>
```

Nézet a `initialAdminPassword` a Jenkins telepítése, és másolja azt:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Ha a fájl még nem érhető el, várjon néhány percet a Jenkins és a Docker felhő inicializálás telepítse.

Most nyisson meg egy webböngészőt, és navigáljon a `http://<publicIps>:8080`. Végezze el a kezdeti Jenkins a telepítő az alábbiak szerint:

- Adja meg a *initialAdminPassword* a virtuális gép az előző lépésben beszerzett.
- Válasszon **jelölje be a beépülő modulok telepítése**
- Keresse meg *GitHub* a szövegmezőben látható, válassza ki a *GitHub beépülő modul*, majd jelölje be **telepítése**
- Jenkins felhasználói fiók létrehozása, töltse ki a kívánt módon működjenek az űrlapot. Biztonsági szempontból a Folytatás, az alapértelmezett rendszergazdai fiók helyett az első Jenkins felhasználó kell létrehoznia.
- Ha elkészült, válassza ki a **Jenkins használatának megkezdése**


## <a name="create-github-webhook"></a>GitHub webhook létrehozása
A rendszerrel történő integráció konfigurálása a GitHub, nyissa meg a [Node.js Hello World mintaalkalmazás](https://github.com/Azure-Samples/nodejs-docs-hello-world) az Azure-minták tárházból. Oszthatja ketté a tárházban saját GitHub-fiók számára, jelölje ki a **elágazás** gombra a jobb felső sarkában.

Hozzon létre egy webhook létrehozott elágazás belül:

- Válassza ki **beállítások**, majd jelölje be **integrációja és a szolgáltatások** a bal oldalon.
- Válasszon **-szolgáltatás hozzáadása a**, majd adja meg *Jenkins* a Szűrő mezőbe.
- Válassza ki *Jenkins (GitHub beépülő modul)*
- Az a **Jenkins hook URL-cím**, adja meg `http://<publicIps>:8080/github-webhook/`. Győződjön meg arról, a záró /
- Válassza ki **szolgáltatás hozzáadása**

![GitHub webhook hozzáadása a villás tárház](media/tutorial-jenkins-github-docker-cicd/github_webhook.png)


## <a name="create-jenkins-job"></a>Jenkins feladat létrehozása
Ahhoz, hogy eseményre Jenkins válaszoljon a Githubon például kód végrehajtása, hozzon létre egy Jenkins feladatot. 

Válassza ki a Jenkins webhely **hozzon létre új feladatokat** a kezdőlapról:

- Adja meg *HelloWorld* feladat neve. Válasszon **Freestyle projekt**, majd jelölje be **OK**.
- Az a **általános** szakaszban jelölje be **GitHub** projektre, és adja meg a villás tárház URL-CÍMÉT, például *https://github.com/iainfoulds/nodejs-docs-hello-world*
- A a **kód felügyeleti forrás** szakaszban jelölje be **Git**, adja meg a villás tárház *.git* URL-CÍMÉT, például a *https://github.com/iainfoulds/nodejs-docs-hello-world.git*
- Az a **Build eseményindítók** szakaszban jelölje be **GitHub hook eseményindítója a következőnek: GITscm lekérdezési**.
- Az a **Build** területen válassza a **Hozzáadás összeállítása lépés**. Válassza ki **hajtható végre a rendszerhéj**, majd adja meg `echo "Testing"` a a parancsablakban.
- Válassza ki **mentése** a feladatok ablak alján.


## <a name="test-github-integration"></a>GitHub-integráció tesztelése
Jenkins GitHub integrációja teszteléséhez véglegesítse az elágazáshoz változását. 

Vissza a Githubon webes felhasználói felülete, válassza ki a villás tárház, majd válassza ki a **index.js** fájlt. Válassza ki a fájl szerkesztése, sor: 6 beolvassa a ceruza ikonra:

```nodejs
response.end("Hello World!");
```

A módosítások véglegesítéséhez, válassza ki a **változtatások véglegesítése a határidő** panel alján.

Jenkins, az új buildverziót elindul, a a **előzmények Build** szakasza a feladat lap bal alsó sarkában. Válassza a build számú hivatkozásra, majd **a konzol kimeneti** bal mérete. Megtekintheti a Jenkins veszi, hogy a rendszer a kódot a Githubról hívja elő lépéseket, és a létrehozási művelet kiírja az üzenet `Testing` a konzolhoz. Minden alkalommal, amikor egy véglegesítési a Githubon történik a webhook egészítse ki a Jenkins és indul el, így új buildverziót.


## <a name="define-docker-build-image"></a>Adja meg a Docker build kép
Tekintse meg a GitHub véglegesítések alapján futó Node.js-alkalmazás lehetővé teszi az alkalmazás futtatásához Docker-lemezkép elkészítése. A kép össze egy Dockerfile, amely meghatározza a konfigurálása a tárolóhoz, amelybe futtatja az alkalmazást. 

Az SSH-kapcsolat a virtuális géphez módosítsa az előző lépésben létrehozott feladat elnevezve Jenkins munkaterület könyvtárba. A fenti példában, amely nevű *HelloWorld*.

```bash
cd /var/lib/jenkins/workspace/HelloWorld
```

Fájl létrehozása a könyvtár munkaterület `sudo sensible-editor Dockerfile` , majd illessze be az alábbiakat. Győződjön meg arról, hogy a teljes Dockerfile megfelelően lett lemásolva különösen az első sor:

```yaml
FROM node:alpine

EXPOSE 1337

WORKDIR /var/www
COPY package.json /var/www/
RUN npm install
COPY index.js /var/www/
```

A Dockerfile használ, a Node.js alaplemezképet Alpine Linux használatával, tesz elérhetővé port 1337, amely a Hello World alkalmazás fut, majd másolja át az alkalmazás fájljai és inicializálja azt.


## <a name="create-jenkins-build-rules"></a>Jenkins összeállítási szabályok létrehozása
Az előző lépésben létrehozott egy alapszintű Jenkins build szabályt, amely egy üzenetet, amely a konzol kimeneti. Lehetővé teszi, hogy létrehozása a build lépés a Dockerfile használatára, majd futtassa az alkalmazást.

A Jenkins példánya válassza az előző lépésben létrehozott feladat. Válassza ki **konfigurálása** a bal oldalon, és görgessen le a **Build** szakasz:

- Távolítsa el a meglévő `echo "Test"` összeállítása lépés. Válassza ki a piros kereszt a meglévő összeállítása lépés mező jobb felső sarkában.
- Válasszon **Hozzáadás összeállítása lépés**, majd jelölje be **rendszerhéj végrehajtása**
- Az a **parancs** mezőbe, írja be a következő Docker-parancsokat, majd válassza ki **mentése**:

  ```bash
  docker build --tag helloworld:$BUILD_NUMBER .
  docker stop helloworld && docker rm helloworld
  docker run --name helloworld -p 1337:1337 helloworld:$BUILD_NUMBER node /var/www/index.js &
  ```

A Docker build lépéseket kép és a Jenkins a buildszám, akkor is fenntartható a képek előzményeit címke létrehozása. Az alkalmazást futtató meglévő tárolókkal leáll, és eltávolítja majd. Új tároló majd a lemezkép használatával elindult, és a legújabb véglegesíti a Githubon alapján a Node.js-alkalmazást futtat.


## <a name="test-your-pipeline"></a>A folyamat tesztelése
A művelet a teljes folyamat megtekintéséhez szerkesztése a *index.js* újra a villás GitHub-tárház fájlt, és válassza ki **módosítás véglegesítése**. GitHub webhook meghatározásával Jenkins új feladat indítja el. A Docker-lemezképet, és indítsa el az alkalmazást egy új tároló néhány másodpercet vesz igénybe.

Szükség esetén olvassa be újra a virtuális gép nyilvános IP-címe:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Nyisson meg egy webböngészőt, és írja be `http://<publicIps>:1337`. A Node.js-alkalmazás jelenik meg, és a legújabb véglegesíti a Githubon elágazás a tükrözi az alábbiak szerint:

![Futó Node.js-alkalmazás](media/tutorial-jenkins-github-docker-cicd/running_nodejs_app.png)

Egy másik szerkesztése ellenőrizze a *index.js* fájlt a Githubon, és a módosítás véglegesítése. Várjon a feladat befejezése Jenkins néhány másodpercet, majd frissítse a webböngészőt a frissített verzió az alkalmazás fut egy új tároló az alábbiak szerint:

![Node.js-alkalmazás futtatása után egy másik GitHub véglegesítési](media/tutorial-jenkins-github-docker-cicd/another_running_nodejs_app.png)


## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban egy Docker-tároló az alkalmazás tesztelése majd alkalmaznia kell egy Jenkins összeállítási feladat futtatása, minden kód véglegesítés a Githubon konfigurálva. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Jenkins virtuális gép létrehozása
> * Telepítse és konfigurálja a Jenkins
> * GitHub és Jenkins integrációjával webhook létrehozása
> * Hozzon létre és Jenkins feladatok létrehozása a Githubról eseményindító véglegesítése
> * Az alkalmazás Docker-lemezkép létrehozása
> * Ellenőrizze a Githubon véglegesíti és hozhat létre. új Docker-lemezkép alkalmazást futtató frissítések

További információt a Visual Studio Team Services Jenkins integrálása a következő oktatóanyag továbblépés.

> [!div class="nextstepaction"]
> [Alkalmazások telepítése a Jenkins és Team Services](tutorial-build-deploy-jenkins.md)