---
title: "Azure Service Fabric-tárolóalkalmazás létrehozása Linuxon | Microsoft Docs"
description: "Hozza létre első saját, Linux-alapú tárolóalkalmazását az Azure Service Fabricban.  Az alkalmazással elkészíthet egy Docker-rendszerképet, amelyet leküldéssel továbbíthat egy tárolóregisztrációs adatbázisba, majd összeállíthat és üzembe helyezhet egy Service Fabric-tárolóalkalmazást."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: ryanwi
ms.openlocfilehash: 7623c5ac5cbc52e9fc77b1f944908b07ff43fbaf
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>Linux-alapú Azure Service Fabric-tároló üzembe helyezése az Azure-on
Az Azure Service Fabric egy elosztott rendszerplatform, amely skálázható és megbízható mikroszolgáltatások és tárolók üzembe helyezésére és kezelésére szolgál. 

Ez a rövid útmutató bemutatja, hogyan helyezheti üzembe a Linux-tárolókat Service Fabric-fürtön. Miután elkészült, egy szavazóalkalmazást kap, amely egy Python-alapú webes előtérrendszert és egy Redis-alapú háttérrendszert tartalmaz, amely egy Service Fabric-fürtben fut. 

![quickstartpic][quickstartpic]

Ezen rövid útmutató segítségével megtanulhatja a következőket:
> [!div class="checklist"]
> * Tárolók üzembe helyezése Azure Linux Service Fabric-fürtökön
> * Méretezési és feladatátvételi tárolók a Service Fabric szolgáltatásban

## <a name="prerequisite"></a>Előfeltétel
Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/en-us/free/) a virtuális gép létrehozásának megkezdése előtt.
  
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület (CLI) helyi telepítését és használatát választja, győződjön meg róla, hogy az Azure CLI 2.0.4-es vagy újabb verzióját használja. A verzió megkereséséhez futtassa a következő parancsot: az --version. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

## <a name="get-application-package"></a>Az alkalmazáscsomag beszerzése
A tárolók a Service Fabric szolgáltatásban való üzembe helyezéséhez jegyzékfájlok egy készletére lesz szükség (az alkalmazásdefinícióra), amelyek leírják az egyes tárolókat és az alkalmazást.

A Cloud Shellben a git használatával klónozással készítsen egy másolatot az alkalmazásdefinícióról.

```azurecli-interactive
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="deploy-the-containers-to-a-service-fabric-cluster-in-azure"></a>Helyezze üzembe a tárolókat egy Service Fabric-fürtön az Azure-ban
Az alkalmazás Azure-fürtön történő üzembe helyezéséhez használhat egy saját vagy egy nyilvános fürtöt is.

> [!Note]
> Az alkalmazást az Azure-ban lévő fürtre, és nem egy valamely helyi fejlesztői gépen lévő Service Fabric-fürtre kell telepíteni. 
>

A nyilvános fürtök ingyenes, korlátozott időtartamú Azure Service Fabric-fürtök. A Service Fabric csapata tartja karban ezeket, és bárki üzembe helyezhet rajtuk alkalmazásokat, illetve megismerkedhet a platform használatával. A nyilvános fürt eléréséhez [kövesse az alábbi utasításokat](http://aka.ms/tryservicefabric). 

További információk saját fürtök létrehozásáról: [Az első saját Service Fabric-fürt létrehozása az Azure-on](service-fabric-get-started-azure-cluster.md).

> [!Note]
> A webes előtérrendszer a konfigurációja szerint a 80-as porton figyeli a bejövő forgalmat. Győződjön meg róla, hogy a port nyitva van a fürtön. Ha nyilvános fürtöt használ, a port nyitva van.
>

### <a name="deploy-the-application-manifests"></a>Az alkalmazásjegyzékek üzembe helyezése 
Telepítse a [Service Fabric parancssori felületet (sfctl)](service-fabric-cli.md) a parancssori felületi környezetben

```azurecli-interactive
pip3 install --user sfctl 
export PATH=$PATH:~/.local/bin
```
Csatlakozzon az Azure Service Fabric-fürthöz az Azure CLI használatával. A végpont a fürt felügyeleti végpontja, például `http://linh1x87d1d.westus.cloudapp.azure.com:19080`.

```azurecli-interactive
sfctl cluster select --endpoint http://linh1x87d1d.westus.cloudapp.azure.com:19080
```

Használja a megadott telepítési szkriptet a szavazóalkalmazás definíciójának a fürtbe való másolásához, regisztrálja az alkalmazás típusát, és hozza létre az alkalmazás egy példányát.

```azurecli-interactive
./install.sh
```

Nyisson meg egy böngészőt, majd navigáljon a Service Fabric Explorerre a http://\<my-azure-service-fabric-cluster-url>:19080/Explorer címen – például: `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`. Bontsa ki az Alkalmazások csomópontot, és figyelje meg, hogy most már megjelenik benne egy bejegyzés a szavazóalkalmazás típusához, és az Ön által létrehozott példányhoz.

![Service Fabric Explorer][sfx]

Csatlakozzon a futó tárolóhoz.  Nyissa meg egy webböngészőben a fürt URL-címét – például: `http://linh1x87d1d.westus.cloudapp.azure.com:80`. Ekkor a szavazóalkalmazásnak kell megjelennie a böngészőben.

![quickstartpic][quickstartpic]

## <a name="fail-over-a-container-in-a-cluster"></a>Tároló feladatátvétele a fürtben
A Service Fabric biztosítja, hogy meghibásodás esetén a tárolópéldányok automatikusan áthelyeződjenek a fürt más csomópontjaira. Manuálisan is ürítheti a csomópontokat a tárolók tekintetében, és azokat zökkenőmentesen helyezheti át a fürt más csomópontjaira. A szolgáltatásokat többféleképpen is méretezheti – ebben a példában a Service Fabric Explorert használjuk.

Az előtértároló feladatátvételéhez hajtsa végre a következő lépéseket:

1. Nyissa meg a Service Fabric Explorert a fürtben – például: `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`.
2. Kattintson a **fabric:/Voting/azurevotefront** csomópontra a fanézetben, és bontsa ki a partíciós csomópontot (egy GUID jelöli). Figyelje meg a csomópont nevét a fanézetben, amely azokat a csomópontokat mutatja, amelyeken a tároló jelenleg fut – például: `_nodetype_4`.
3. Bontsa ki a **Csomópontok** csomópontot a fanézetben. Kattintson a három pontra a tárolót futtató csomópont mellett.
4. Válassza az **Újraindítás** lehetőséget a csomópont újraindításához, majd erősítse meg az újraindítási műveletet. Az újraindítás kikényszeríti a tároló feladatátvételét a fürt egy másik csomópontjára.

![sfxquickstartshownodetype][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Alkalmazások és szolgáltatások méretezése a fürtökben
A Service Fabric-szolgáltatások könnyen méretezhetők egy adott fürtben a szolgáltatások terhelésének megfelelően. A szolgáltatások méretezése a fürtben futó példányok számának módosításával történik.

A webes előtér-szolgáltatás méretezéséhez hajtsa végre a következő lépéseket:

1. Nyissa meg a Service Fabric Explorert a fürtben – például: `http://linh1x87d1d.westus.cloudapp.azure.com:19080`.
2. Kattintson a három pontra a fanézetben a **fabric:/Voting/azurevotefront** csomópont mellett, és válassza a **Szolgáltatás méretezése** lehetőséget.

    ![containersquickstartscale][containersquickstartscale]

  Most már méretezheti a webes előtér-szolgáltatás példányainak számát.

3. Módosítsa a számot **2**-re, és kattintson a **Szolgáltatás méretezése** gombra.
4. Kattintson a **fabric:/Voting/azurevotefront** csomópontra a fanézetben, és bontsa ki a partíciós csomópontot (egy GUID jelöli).

    ![containersquickstartscaledone][containersquickstartscaledone]

    Most láthatja, hogy a szolgáltatás két példánnyal rendelkezik. A fanézetben megtekintheti, hogy a példányok melyik csomópontokon futnak.

Ezzel az egyszerű felügyeleti eljárással megdupláztuk az előtér-szolgáltatás számára a felhasználói terhelések feldolgozásához rendelkezésre álló erőforrások mennyiségét. Fontos megérteni, hogy nincs szükség több példányra ahhoz, hogy a szolgáltatás megbízhatóan fusson. Ha egy szolgáltatás meghibásodik, a Service Fabric gondoskodik róla, hogy egy új szolgáltatáspéldány kezdjen futni a fürtben.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
Használja a sablonban megadott eltávolítási szkriptet az alkalmazáspéldánynak a fürtről történő törléséhez, és törölje az alkalmazástípus regisztrációját. A parancsnak némi időre van szüksége, hogy kiürítse a példányt, ezért az „install'sh” parancsot nem érdemes azonnal a szkript után futtatni. 

```bash
./uninstall.sh
```

## <a name="next-steps"></a>Következő lépések
Ennek a rövid útmutatónak a segítségével megtanulta a következőket:
> [!div class="checklist"]
> * Linux-alapú tárolóalkalmazás üzembe helyezése az Azure-ban
> * Tároló feladatátvétele Service Fabric-fürtben
> * Tároló méretezése Service Fabric-fürtben

* További információk a [tárolók futtatásáról a Service Fabricban](service-fabric-containers-overview.md).
* További információk a Service Fabric [alkalmazásainak élettartamáról](service-fabric-application-lifecycle.md).
* A [Service Fabric tárolók mintakódjainak](https://github.com/Azure-Samples/service-fabric-containers) megtekintése a GitHubon.

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
