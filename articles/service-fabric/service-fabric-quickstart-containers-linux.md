---
title: Linux rendszerű tárolóalkalmazás létrehozása a Service Fabricben az Azure-ban | Microsoft Docs
description: Ebben a rövid útmutatóban egy Docker-rendszerképet készít az alkalmazásával, amelyet leküld egy tárolóregisztrációs adatbázisba, majd üzembe helyezi a tárolót egy Service Fabric-fürtön.
services: service-fabric
documentationcenter: linux
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/30/2019
ms.author: aljo,suhuruli
ms.custom: mvc
ms.openlocfilehash: 559b51b70c2bcb18d74d77fddaf928e0356435df
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804553"
---
# <a name="quickstart-deploy-linux-containers-to-service-fabric"></a>Gyors útmutató: Linux-tárolók üzembe helyezése a Service Fabric szolgáltatásban

Az Azure Service Fabric egy elosztott rendszerplatform, amely skálázható és megbízható mikroszolgáltatások és tárolók üzembe helyezésére és kezelésére szolgál.

A rövid útmutató bemutatja, hogyan helyezhet üzembe Linux-tárolók egy Service Fabric-fürtön az Azure-ban. Miután elkészült, egy szavazóalkalmazást kap, amely egy Python-alapú webes előtérrendszert és egy Redis-alapú háttérrendszert tartalmaz, amely egy Service Fabric-fürtben fut. Azt is megismeri, hogyan végezheti el egy alkalmazás feladatátvételét és hogyan skálázhatja a fürtben lévő alkalmazásokat.

![Szavazóalkalmazás weboldala][quickstartpic]

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

1. Hozzon létre egy [ingyenes Azure-fiók](https://azure.microsoft.com/free/) mielőtt elkezdené, ha nem rendelkezik előfizetéssel.

2. Telepítse az [Azure CLI-t](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)

3. Telepítse a [Service Fabric SDK és a parancssori felület](service-fabric-get-started-linux.md#installation-methods)

4. Telepítés [Git](https://git-scm.com/)


## <a name="get-the-application-package"></a>Az alkalmazáscsomag beszerzése

A tárolók a Service Fabric szolgáltatásban való üzembe helyezéséhez jegyzékfájlok egy készletére lesz szükség (az alkalmazásdefinícióra), amelyek leírják az egyes tárolókat és az alkalmazást.

A konzolon a git használatával klónozással készítsen egy másolatot az alkalmazásdefinícióról; majd módosítsa a könyvtárakat a `Voting` könyvtárra a klónban.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Service Fabric-fürt létrehozása

Ha az alkalmazást üzembe szeretné helyezni az Azure-ban, szüksége lesz egy Service Fabric-fürtre a futtatásához. A következő parancsokat egy ötcsomópontos fürt létrehozása az Azure-ban.  A parancsok is létrehozhat egy önaláírt tanúsítványt, key vault hozzáadja, és letölti helyben a tanúsítványt. Az új tanúsítvány segítségével a fürt védelme, amikor üzembe helyezi és -ügyfelek hitelesítésére szolgál.

```azurecli
#!/bin/bash

# Variables
ResourceGroupName="containertestcluster" 
ClusterName="containertestcluster" 
Location="eastus" 
Password="q6D7nN%6ck@6" 
Subject="containertestcluster.eastus.cloudapp.azure.com" 
VaultName="containertestvault" 
VmPassword="Mypa$$word!321"
VmUserName="sfadminuser"

# Login to Azure and set the subscription
az login

az account set --subscription <mySubscriptionID>

# Create resource group
az group create --name $ResourceGroupName --location $Location 

# Create secure five node Linux cluster. Creates a key vault in a resource group
# and creates a certficate in the key vault. The certificate's subject name must match 
# the domain that you use to access the Service Fabric cluster.  The certificate is downloaded locally.
az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $ResourceGroupName --vm-password $VmPassword --vm-user-name $VmUserName
```

> [!Note]
> A webes előtérrendszer a konfigurációja szerint a 80-as porton figyeli a bejövő forgalmat. Alapértelmezés szerint 80-as port meg nyitva a virtuális gépek és az Azure load balancer.
>

## <a name="configure-your-environment"></a>A környezet konfigurálása

A Service Fabric számos eszközt nyújt, amelyekkel kezelheti a fürtöket és azok alkalmazásait:

- A Service Fabric Explorert, amely egy böngészőalapú eszköz.
- A Service Fabric parancssori felületet (CLI-t), amely az Azure CLI-n fut. 
- PowerShell-parancsokat.

Ebben a rövid útmutatóban használhatja a Service Fabric parancssori felület és a Service Fabric Explorer (egy webalapú eszköz). A Service Fabric Explorer használatához, importálnia kell a tanúsítvány PFX-fájlt a böngészőben. Alapértelmezés szerint a PFX-fájl nem tartozik jelszó.

Mozilla Firefox az alapértelmezett böngésző az Ubuntu 16.04. A tanúsítvány a Firefoxba importálásához kattintson a böngésző jobb felső sarkában lévő menügombra, majd a **Beállítások** gombra. A **Beállítások** oldalon a keresőmezővel keressen rá a „tanúsítványok” kifejezésre. Kattintson a **Tanúsítványkezelő** gombra, válassza a **Saját tanúsítványok** fület, kattintson az **Importálás** lehetőségre, és kövesse az utasításokat a tanúsítvány importálásához.

   ![Tanúsítvány telepítése Firefoxon](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png)

## <a name="deploy-the-service-fabric-application"></a>A Service Fabric-alkalmazás üzembe helyezése

1. A Service Fabric-fürtön az Azure-ban a parancssori felület használatával csatlakozhat. A végpont a fürt felügyeleti végpontja. A PEM-fájlt az előző szakaszban hozta létre. 

    ```bash
    sfctl cluster select --endpoint https://containertestcluster.eastus.cloudapp.azure.com:19080 --pem containertestcluster22019013100.pem --no-verify
    ```

2. Használja a telepítési szkriptet a szavazóalkalmazás definíciójának a fürtbe való másolásához, regisztrálja az alkalmazás típusát, és hozza létre az alkalmazás egy példányát.  A PEM-tanúsítványfájlt ugyanabban a címtárban kell elhelyezni a *install.sh* fájlt.

    ```bash
    ./install.sh
    ```

3. Nyisson meg egy webböngészőt, majd navigáljon a fürt Service Fabric Explorer-végpontjára. A végpont formátuma a következő: **https://\<my-azure-service-fabric-cluster-url>:19080/Explorer**, például: `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`. </br>

4. Bontsa ki az **Alkalmazások** csomópontot, és figyelje meg, hogy most már megjelenik benne egy bejegyzés a szavazóalkalmazás típusához, és az Ön által létrehozott példányhoz.

    ![Service Fabric Explorer][sfx]

5. A futó tárolóhoz való csatlakozáshoz nyisson meg egy webböngészőt, és navigáljon a fürt URL-címére – például: `http://containertestcluster.eastus.cloudapp.azure.com:80`. Ekkor a szavazóalkalmazásnak kell megjelennie a böngészőben.

    ![Szavazóalkalmazás weboldala][quickstartpic]

> [!NOTE]
> A Docker Compose használatával is üzembe helyezhet Service Fabric-alkalmazásokat. A következő paranccsal például üzembe helyezheti és telepítheti az alkalmazást a fürtön a Docker Compose használatával.
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>Tároló feladatátvétele a fürtben

A Service Fabric biztosítja, hogy meghibásodás esetén a tárolópéldányok automatikusan áthelyeződjenek a fürt más csomópontjaira. Manuálisan is ürítheti a csomópontokat a tárolók tekintetében, és azokat zökkenőmentesen helyezheti át a fürt más csomópontjaira. A Service Fabric több módot nyújt a szolgáltatások méretezésére. A következő lépésekben a Service Fabric Explorert használjuk.

Az előtértároló feladatátvételéhez hajtsa végre a következő lépéseket:

1. Nyissa meg a Service Fabric Explorert a fürtben – például: `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`.
2. Kattintson a **fabric:/Voting/azurevotefront** csomópontra a fanézetben, és bontsa ki a partíciós csomópontot (egy GUID jelöli). Figyelje meg a csomópont nevét a fanézetben, amely azokat a csomópontokat mutatja, amelyeken a tároló jelenleg fut; például: `_nodetype_1`.
3. Bontsa ki a **Csomópontok** csomópontot a fanézetben. Kattintson a három pontra (...) a tárolót futtató csomópont mellett.
4. Válassza az **Újraindítás** lehetőséget a csomópont újraindításához, majd erősítse meg az újraindítási műveletet. Az újraindítás kikényszeríti a tároló feladatátvételét a fürt egy másik csomópontjára.

    ![Csomópont nézet a Service Fabric Explorerben][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Alkalmazások és szolgáltatások méretezése a fürtökben

A Service Fabric-szolgáltatások könnyen méretezhetők egy adott fürtben a szolgáltatások terhelésének megfelelően. A szolgáltatások méretezése a fürtben futó példányok számának módosításával történik.

A webes előtér-szolgáltatás méretezéséhez hajtsa végre a következő lépéseket:

1. Nyissa meg a Service Fabric Explorert a fürtben – például: `https://containertestcluster.eastus.cloudapp.azure.com:19080`.
2. Kattintson a három pontra a fanézetben a **fabric:/Voting/azurevotefront** csomópont mellett, és válassza a **Szolgáltatás méretezése** lehetőséget.

    ![A Service Fabric Explorer méretezési szolgáltatásának elindítása][containersquickstartscale]

    Most már méretezheti a webes előtér-szolgáltatás példányainak számát.

3. Módosítsa a számot **2**-re, és kattintson a **Szolgáltatás méretezése** gombra.
4. Kattintson a **fabric:/Voting/azurevotefront** csomópontra a fanézetben, és bontsa ki a partíciós csomópontot (egy GUID jelöli).

    ![A Service Fabric Explorer méretezési szolgáltatása befejezve][containersquickstartscaledone]

    Most láthatja, hogy a szolgáltatás két példánnyal rendelkezik. A fanézetben megtekintheti, hogy a példányok melyik csomópontokon futnak.

Ezzel az egyszerű felügyeleti eljárással megduplázta az előtér-szolgáltatás számára a felhasználói terhelések feldolgozásához rendelkezésre álló erőforrások mennyiségét. Fontos tudni, hogy nincs szükség több példányra ahhoz, hogy a szolgáltatás megbízhatóan fusson. Ha egy szolgáltatás meghibásodik, a Service Fabric gondoskodik róla, hogy egy új szolgáltatáspéldány elinduljon a fürtben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Használja a sablonban megadott eltávolítási szkriptet (uninstall.sh) az alkalmazáspéldánynak a fürtről történő törléséhez, és törölje az alkalmazástípus regisztrációját. A szkriptnek némi időre van szüksége a példány kiürítéséhez, ezért a telepítési szkriptet nem érdemes azonnal ezen szkript után futtatni. A Service Fabric Explorerrel megállapíthatja, hogy mikor lett eltávolítva a példány és mikor lett megszüntetve az alkalmazástípus regisztrációja.

```bash
./uninstall.sh
```

A fürt és az összes általa használt erőforrás törlésének legegyszerűbb módja az erőforráscsoport törlése.

Jelentkezzen be az Azure-ba, és válassza ki azon előfizetés azonosítóját, amelyikkel el szeretné távolítani a fürtöt. Az Azure Portalra jelentkezik be az előfizetés-Azonosítóját is megtalálhatja. Törölje az erőforráscsoportot és a fürt erőforrásai használatával az [az group delete parancs](/cli/azure/group?view=azure-cli-latest).

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="containertestcluster"
az group delete --name $ResourceGroupName
```

Ha végzett a fürt használatával, eltávolíthatja a tanúsítványt a tanúsítványtárolóból. Példa:
- Windows rendszeren: Használja a [tanúsítványok beépülő MMC-modulban](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in). Ügyeljen arra, hogy a **Saját felhasználói fiókot** válassza a beépülő modul hozzáadásakor. Lépjen a `Certificates - Current User\Personal\Certificates` helyre, és távolítsa el a tanúsítványt.
- Mac gépen: Használja a Keychain alkalmazást.
- On Ubuntu: Kövesse a lépéseket, amellyel megtekintheti a tanúsítványok, és távolítsa el a tanúsítványt.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy Linux-alapú tárolóalkalmazást egy Service Fabric-fürtön az Azure-ban, elvégezte az alkalmazás feladatátvételét és méretezte azt a fürtben. Ha bővebb információra van szüksége a Linux-tárolók Service Fabricben való használatával kapcsolatban, lépjen tovább a Linux-tárolóalkalmazásokról szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Linux rendszerű tárolóalkalmazás létrehozása](./service-fabric-tutorial-create-container-images.md)

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
