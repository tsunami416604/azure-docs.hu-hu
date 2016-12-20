---
title: "A fejlesztési környezet beállítása Mac OS X-en | Microsoft Docs"
description: "Telepítse a futtatókörnyezetet, az SDK-t és az eszközöket, majd hozzon létre egy helyi fejlesztési fürtöt. A beállítás befejezése után készen áll az alkalmazások Mac OS X-en történő létrehozására."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 7e33e00a676f4aa7143cede3380adb58ba1d11e4
ms.openlocfilehash: 80e0ae758f02b7647a1d61344799bbc500449a04


---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>A fejlesztési környezet beállítása Mac OS X-en
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
>  

Linux-fürtökön futó Service Fabric-alkalmazásokat hozhat létre a Mac OS X-en. Ez a cikk bemutatja, hogyan állíthatja be Mac gépét a fejlesztéshez.

## <a name="prerequisites"></a>Előfeltételek
A Service Fabric nem fut natív módon az OS X-en. A helyi Service Fabric-fürt futtatásához egy Vagrant és VirtualBox alkalmazásokat használó, előre konfigurált Ubuntu-virtuális gépet biztosítunk. A kezdés előtt a következőkre lesz szüksége:

* [Vagrant (1.8.4-es vagy újabb verzió)](http://wwww.vagrantup.com/downloads)
* [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

## <a name="create-the-local-vm"></a>A helyi virtuális gép létrehozása
Egy 5 csomópontos Service Fabric-fürtöt tartalmazó virtuális gép létrehozásához tegye a következőket:

1. Klónozza a Vagrantfile-adattárat
   
    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```
2. Navigáljon az adattár helyi klónjához
   
    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```
3. (Nem kötelező) Módosítsa a virtuális gép alapértelmezett beállításait
   
    Alapértelmezés szerint a helyi virtuális gép konfigurációja a következő:
   
   * 3 GB lefoglalt memória
   * A 192.168.50.50 IP-címre konfigurált privát gazdagép-hálózat, amely átengedi a Mac-gazdagépről érkező forgalmat
     
     A Vagrantfile-lal bármelyik beállítást módosíthatja és egyéb konfigurációkat adhat a virtuális géphez. A konfigurációs beállítások teljes listájáért tekintse meg a [Vagrant dokumentációját](http://www.vagrantup.com/docs).
4. Virtuális gép létrehozása
   
    ```bash
    vagrant up
    ```
   
    Ebben a lépésben megtörténik az előre konfigurált virtuálisgép-rendszerkép letöltése, helyi indítása, és végül egy helyi Service Fabric-fürt beállítása. Ez eltarthat néhány percig. Ha a beállítás sikeresen megtörtént, a kimenetben megjelenik egy üzenet, amely jelzi, hogy a fürt indítása folyamatban van.
   
    ![A fürt beállításának megkezdése a virtuális gép kiépítése után][cluster-setup-script]
5. Keresse fel a Service Fabric Explorert a http://192.168.50.50:19080/Explorer címen (feltéve, hogy megtartotta az alapértelmezett magánhálózati IP-címet) a fürt megfelelő beállításának teszteléséhez.
   
    ![A Service Fabric Explorer a Mac gazdagépről megtekintve][sfx-mac]

## <a name="install-the-service-fabric-plugin-for-eclipse-neon-optional"></a>Az Eclipse Neonhoz készült Service Fabric beépülő modul telepítése (nem kötelező)
A Service Fabric egy beépülő modult biztosít az Eclipse Neon IDE-hez, amely leegyszerűsítheti a Java-szolgáltatások létrehozásának és üzembe helyezésének folyamatát.

1. Az Eclipse-ben győződjön meg arról, hogy telepítve van a Buildship 1.0.17-es vagy újabb verziója. A telepített összetevők verzióját a **Help > Installation Details** (Súgó > Telepítés részletei) lehetőség kiválasztásával ellenőrizheti. Az [itt][buildship-update] található utasítások alapján frissítheti a Buildshipet.
2. A Service Fabric beépülő modul telepítéséhez válassza a **Help > Install New Software...** (Súgó > Új szoftver telepítése) lehetőséget.
3. A Work with (Használat) szövegbeviteli mezőbe írja be a következőt: http://dl.windowsazure.com/eclipse/servicefabric.
4. Kattintson az Add (Hozzáadás) parancsra.
   
    ![Eclipse Neon beépülő modul a Service Fabrichez][sf-eclipse-plugin-install]
5. Válassza ki a Service Fabric beépülő modult, majd kattintson a Next (Tovább) gombra.
6. Folytassa a telepítést, és fogadja el a végfelhasználói licencszerződést.

## <a name="next-steps"></a>Következő lépések
* [Az első Service Fabric-alkalmazás létrehozása Linuxra](service-fabric-create-your-first-linux-application-with-java.md)

<!-- Links -->

* [Service Fabric-fürt létrehozása az Azure Portalon](service-fabric-cluster-creation-via-portal.md)
* [Service Fabric-fürt létrehozása az Azure Resource Managerrel](service-fabric-cluster-creation-via-arm.md)
* [A Service Fabric-alkalmazásmodell megismerése](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship



<!--HONumber=Nov16_HO4-->


