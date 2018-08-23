---
title: Ebben az oktatóanyagban létrehozott egy Azure Stack Virtuálisgép-sablon használatával |} A Microsoft Docs
description: Ismerteti, hogyan hozhat létre virtuális Gépet egy predfined sablon és a egy GitHub-egyéni sablont a ASDK használandó.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 5026a7a753ec744d281266b2fb30a70a66a7f9db
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139433"
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>Oktatóanyag: közösségi sablon segítségével virtuális gép létrehozása
Azure Stack-operátorokról vagy felhasználóként, létrehozhat egy virtuális gépet [egyéni GitHub gyorsindítási sablonok](https://github.com/Azure/AzureStack-QuickStart-Templates) egyet manuálisan az Azure Stack piactéren telepítése helyett.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * További tudnivalók az Azure Stack gyorsindítási sablonok 
> * Hozzon létre egy virtuális Gépet egy egyéni GitHub-sablon használatával
> * Minikube indítása és a egy alkalmazás telepítése

## <a name="learn-about-azure-stack-quickstart-templates"></a>További tudnivalók az Azure Stack gyorsindítási sablonok
Az Azure Stack-gyorssablonok vannak tárolva a [AzureStack gyorsindítási sablonok nyilvános tárházban](https://github.com/Azure/AzureStack-QuickStart-Templates) a Githubon. Ez a tárház, amely az a Microsoft Azure Stack Development Kit (ASDK) teszteltük az Azure Resource Manager központi telepítési sablonok tartalmazza. Hogy egyszerűbb legyen, hogy az Azure Stack értékelje ki és használja a ASDK környezetet használhatja őket. 

Idővel számos GitHub-felhasználókat van a tárházhoz, összesen 400-nál több központi telepítési sablonok hatalmas gyűjteménye eredményez. Ez a tárház kiváló kiindulási pontot annak jobban megismerheti, hogyan telepíthet különféle környezetek az Azure Stackhez. 

>[!IMPORTANT]
> Ezeket a sablonokat nem a Microsoft, hanem a Közösség tagjai által jön létre. A tulajdonosa, nem a Microsoft egyes sablonok licencelése licencszerződés keretében. A Microsoft nem felelős a sablonokért, és nem vállal biztonság, kompatibilitás és teljesítmény. Közösségi sablonok bármely Microsoft terméktámogatási programja vagy szolgáltatása nem támogatja, és elérhető "," bármiféle garancia nélkül.

Közreműködés a githubon az Azure Resource Manager-sablonokkal szeretne, ha a hozzájárulását, győződjön meg a [azure-gyorssablonok tárház](https://github.com/Azure/AzureStack-QuickStart-Templates).

A GitHub-tárházat, és hogyan működhet közre a, kapcsolatos további információkért tekintse meg a [tárház információs fájl](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md). 


## <a name="create-a-vm-using-a-custom-github-template"></a>Hozzon létre egy virtuális Gépet egy egyéni GitHub-sablon használatával
Ebben a példában az oktatóanyagban a [101-vm-linux-minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) Azure Stack gyorsindítási sablon segítségével üzembe egy Ubuntu 16.04 virtuális gépet az AzureStack futó Minikube kubenetes-fürtök kezeléséhez.

Minikube olyan eszköz, amely megkönnyíti a Kubernetes helyi futtatásához. Minikube napi futtat egy virtuális gépen, próbálja ki a Kubernetes, illetve fejlesztheti a felhasználók számára egy egy csomópontos Kubernetes-fürt. Egy egyszerű, a Linux rendszerű virtuális gépeken futó egyik csomóponton Kubernetes-fürt támogatja. A leggyorsabb és leginkább egyszerű módja egy teljesen működőképes futtatott Kubernetes-fürt. A szolgáltatással fejlesztheti és tesztelheti a Kubernetes-alapú telepítések a helyi számítógépeken. Tekintve Minikube VM fő és ügynök csomópont-összetevőinek helyben fut:
- Fő csomópont összetevők, például az API-kiszolgálóhoz, az ütemező és a kiszolgáló etcd LocalKube nevű egyetlen Linux folyamat futnak.
- Az ügynök csomópont-összetevőinek módon futtatja a docker-tárolókon belüli pontosan lenne egy normál ügynököt a csomóponton futnak. Alkalmazás központi telepítési szempontjából nincs különbség Minikube vagy rendszeres Kubernetes-fürt az alkalmazás központi telepítésekor.

Ez a sablon a következő összetevőket telepíti:

- Ubuntu 16.04 LTS VM
- [Docker-CE](https://download.docker.com/linux/ubuntu) 
- [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
- [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
- xFCE4
- xRDP

> [!IMPORTANT]
> Az Ubuntu Virtuálisgép-rendszerképet (Ubuntu Server 16.04 LTS ebben a példában) kell már hozzáadni az Azure Stack piactéren, a lépések megkezdése előtt.

1.  Kattintson a **+ új** > **egyéni** > **sablonalapú telepítés**.

    ![](media/azure-stack-create-vm-template/1.PNG) 

2. Kattintson a **szerkesztési sablon**.

   ![](media/azure-stack-create-vm-template/2.PNG) 

3.  Kattintson a **gyorsindítási sablon**.

       ![](media/azure-stack-create-vm-template/3.PNG)

4. Válassza ki **101-vm-linux-minikube** használatával a rendelkezésre álló sablonok közül a **válasszon ki egy sablont** legördülő listában, és kattintson a **OK**.  

   ![](media/azure-stack-create-vm-template/4.PNG)

5. Ha azt szeretné, a sablon JSON-JÁT, ehhez, ha nem, vagy amikor végzett, kattintson a módosításokat **mentése** gombra kattintva zárja be a sablon szerkesztése párbeszédpanelen.

   ![](media/azure-stack-create-vm-template/5.PNG) 

6.  Kattintson a **paraméterek**, adja meg, vagy a rendelkezésre álló mezőkben, szükség esetén módosítása, és kattintson **OK**. Válassza ki az előfizetést használja, hozzon létre vagy válasszon egy meglévő erőforráscsoport nevét, majd kattintson a **létrehozás** kezdeményezése a sablon üzembe helyezéséhez.

       ![](media/azure-stack-create-vm-template/6.PNG)

7. Válassza ki az előfizetést használja, hozzon létre vagy válasszon egy meglévő erőforráscsoport nevét, majd kattintson a **létrehozás** kezdeményezése a sablon üzembe helyezéséhez.

   ![](media/azure-stack-create-vm-template/7.PNG)

8. Az erőforráscsoportok üzembe helyezése az egyéni sablon-alapú virtuális gép létrehozása több percet vesz igénybe. Figyelemmel kísérheti a telepítési állapotát, értesítéseket és az erőforráscsoport-tulajdonságai a. 

   ![](media/azure-stack-create-vm-template/8.PNG)

   >[!NOTE]
   > Az üzembe helyezés befejeződésekor a virtuális gép fog futni. 

## <a name="start-minikube-and-install-an-application"></a>Minikube indítása és a egy alkalmazás telepítése
Most, hogy a Linux rendszerű virtuális gép sikeresen létrejött, bejelentkezhet minikube elindításához, és a egy alkalmazás telepítése. 

1. Az üzembe helyezés befejezése után kattintson a **Connect** a Linux rendszerű virtuális Géphez való csatlakozáshoz használt nyilvános IP-címének megtekintéséhez. 

   ![](media/azure-stack-create-vm-template/9.PNG)

2. Egy rendszergazda jogú parancssorból futtassa **mstsc.exe** távoli asztali kapcsolat megnyitásához, és a Linux rendszerű virtuális gép nyilvános IP-címet az előző lépésben felderített való kapcsolódáshoz. Amikor a rendszer kéri, jelentkezzen be a xRDP, a virtuális gép létrehozásakor megadott hitelesítő adatokat használja.

   ![](media/azure-stack-create-vm-template/10.PNG)

3. Nyissa meg a terminált emulátor, és adja meg az alábbi parancsok futtatásával kezdheti minikube:

    >    `sudo minikube start --vm-driver=none`
    >   
    >    `sudo minikube addons enable dashboard`
    >    
    >    `sudo minikube dashboard --url`

   ![](media/azure-stack-create-vm-template/11.PNG)

4. Nyissa meg a webböngészőjét, és látogasson el a kubernetes-irányítópult címe. Gratulálunk, most már teljes mértékben kubernetes telepített és működő verziójával minikube használatával!

   ![](media/azure-stack-create-vm-template/12.PNG)

5. Ha szeretné üzembe helyezünk egy mintaalkalmazást, a dokumentációs oldalán a kubernetes, hagyja ki a "Minikube fürt létrehozása" szakaszban, a már létrehozott egy újabb. Egyszerűen lépjen a szakasz "A Node.js-alkalmazás létrehozása" címen https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * További tudnivalók az Azure Stack gyorsindítási sablonok 
> * Hozzon létre egy virtuális Gépet egy egyéni GitHub-sablon használatával
> * Minikube indítása és a egy alkalmazás telepítése

