---
title: Ebben az oktatóanyagban létrehoz egy Azure verem virtuális gép sablon alapján |} Microsoft Docs
description: Ismerteti, hogyan használható a ASDK predfined sablont és egy GitHub egyéni sablon virtuális gép létrehozása.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: e772dc41ce2cb77a03b91515cae35ffc48f5dbc3
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238413"
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>Oktatóanyag: egy közösségi sablon virtuális gép létrehozása
Egy Azure verem operátor vagy felhasználóként, hozhat létre egy virtuális gép [egyéni GitHub gyorsindítási sablonok](https://github.com/Azure/AzureStack-QuickStart-Templates) ahelyett, hogy a telepítése egy manuálisan a verem Azure piactérről.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * További tudnivalók az Azure-verem gyorsindítási sablonok 
> * Egy egyéni GitHub-sablon virtuális gép létrehozása
> * Indítsa el a minikube, és egy alkalmazás telepítése

## <a name="learn-about-azure-stack-quickstart-templates"></a>További tudnivalók az Azure-verem gyorsindítási sablonok
Az Azure verem gyorsindítási sablonok tárolódnak a [nyilvános AzureStack gyorsindítási sablonok tárházba](https://github.com/Azure/AzureStack-QuickStart-Templates) a Githubon. Ebben a tárházban Azure Resource Manager központi telepítési sablonok tesztelt együtt a Microsoft Azure verem Development Kit (ASDK) tartalmazza. Könnyebb Azure verem értékelését, és használja a ASDK környezet használhatja őket. 

Adott idő alatt számos GitHub-felhasználó hozzájárultak a tárházban, ami azt eredményezi, több mint 400 központi telepítési sablonok hatalmas gyűjteménye. Ebben a tárházban jobb megértése, hogyan telepíthet különféle környezetek Azure verem beolvasandó kiváló kiindulási pontot. 

>[!IMPORTANT]
> Ezek a sablonok némelyike jönnek létre a Közösség tagjai, és nem Microsoft által. Minden sablon a tulajdonosa, nem Microsoft licencbe licencszerződést. A Microsoft nem felelős ezeket a sablonokat, és nem ellenőrzi őket a biztonság, a kompatibilitási vagy a teljesítmény. Közösségi sablonok nem támogatottak a Microsoft támogatási program vagy szolgáltatás, és elérhetővé válnak "Adott állapotában", mindenféle jótállás nélkül.

Ha azt szeretné, az Azure Resource Manager-sablonok a GitHub közre, ügyeljen a hozzájárul a [azure-gyors üzembe helyezés-sablonok tárházba](https://github.com/Azure/AzureStack-QuickStart-Templates).

A GitHub-tárházban, és hogyan járulnak hozzá kapcsolatos további tudnivalókért tekintse meg a [tárház tartozó információs fájl](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md). 


## <a name="create-a-vm-using-a-custom-github-template"></a>Egy egyéni GitHub-sablon virtuális gép létrehozása
A példa az oktatóanyagban a [101-vm – linux-minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) Azure verem gyorsindítási sablonon AzureStack Minikube kubenetes fürt felügyeletére fut egy Ubuntu 16.04 virtuális gép központi telepítéséhez használandó.

Minikube olyan eszköz, amely megkönnyíti a Kubernetes helyi futtatásához. Minikube napi futtat egy egy csomópontos Kubernetes fürtre, a felhasználók számára szeretné Kubernetes kipróbálására, vagy fejlesszen ki azt a virtuális Gépen belül. Egy egyszerű, egy csomópontot tartalmazó fürtben futó Linux virtuális gép Kubernetes támogatja. A leggyorsabb és leginkább egyszerű módja egy teljesen működőképes Kubernetes futtató fürtre is. Lehetővé teszi a fejlesztőknek fejlesztéséhez és teszteléséhez a Kubernetes-alapú alkalmazások központi telepítéseit a helyi számítógépeken. Architektúráját Minikube VM fő és a csomópont Ügynökösszetevőt helyben fut:
- Fő csomópont összetevők, például az API-kiszolgálóhoz, az ütemező és a kiszolgáló etcd LocalKube nevű egyetlen Linux folyamat futnak.
- Csomópont ügynökösszetevőt futnak, docker tárolókba pontosan azokat a normál ügynök csomópont futna. Az alkalmazás központi telepítési szempontjából nincs különbség a alkalmazás Minikube vagy rendszeres Kubernetes fürt központi telepítésekor.

Ez a sablon a következő összetevőket telepít:

- Ubuntu 16.04 LTS méretű VM
- [Docker-CE](https://download.docker.com/linux/ubuntu) 
- [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
- [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
- xFCE4
- xRDP

> [!IMPORTANT]
> Az Ubuntu virtuális gép lemezképét (Ubuntu Server 16.04 LTS ebben a példában) kell már érhetőek el a veremben Azure piactér lépések megkezdése előtt.

1.  Kattintson a **+ új** > **egyéni** > **sablon-üzembehelyezés**.

    ![](media/azure-stack-create-vm-template/1.PNG) 

2. Kattintson a **Szerkesztés sablon**.

   ![](media/azure-stack-create-vm-template/2.PNG) 

3.  Kattintson a **gyorsindítási sablonon**.

       ![](media/azure-stack-create-vm-template/3.PNG)

4. Válassza ki **101-vm – linux-minikube** a rendelkezésre álló sablonok használatával a **válasszon olyan sablont,** legördülő listára, majd **OK**.  

   ![](media/azure-stack-create-vm-template/4.PNG)

5. Ha azt szeretné, hogy a sablon JSON ehhez, ha nem, vagy amikor végzett, kattintson a módosításokat **mentése** sablon szerkesztése párbeszédpanel bezárásához.

   ![](media/azure-stack-create-vm-template/5.PNG) 

6.  Kattintson a **paraméterek**, töltse ki vagy szükség szerint módosítsa a mezők, és kattintson a **OK**. Válassza ki az előfizetést használja, hozzon létre vagy válasszon egy meglévő erőforráscsoport-név, és kattintson a **létrehozása** sablon központi telepítésére.

       ![](media/azure-stack-create-vm-template/6.PNG)

7. Válassza ki az előfizetést használja, hozzon létre vagy válasszon egy meglévő erőforráscsoport-név, és kattintson a **létrehozása** sablon központi telepítésére.

   ![](media/azure-stack-create-vm-template/7.PNG)

8. Az erőforrás-csoport központi telepítése az egyéni sablon-alapú virtuális gép létrehozásához néhány percet vesz igénybe. A telepítési állapotukat, értesítések és az erőforrás tulajdonságai a figyelheti. 

   ![](media/azure-stack-create-vm-template/8.PNG)

   >[!NOTE]
   > A telepítés befejezésekor a virtuális Gépet fog futni. 

## <a name="start-minikube-and-install-an-application"></a>Indítsa el a minikube, és egy alkalmazás telepítése
Most, hogy a Linux virtuális gép létrehozása sikeresen befejeződött, bejelentkezhet minikube elindítása és az alkalmazás telepítését. 

1. A telepítés befejezése után kattintson **Connect** a nyilvános IP-cím, a Linux virtuális gép való kapcsolódáshoz használandó megtekintéséhez. 

   ![](media/azure-stack-create-vm-template/9.PNG)

2. Egy rendszergazda jogú parancssorból futtassa **mstsc.exe** a távoli asztali kapcsolat megnyitásához, és a Linux virtuális gép az előző lépésben felderített nyilvános IP-cím kapcsolódjon. Amikor a rendszer kéri-e jelentkezni xRDP, a virtuális gép létrehozásakor a megadott hitelesítő adatokat használja.

   ![](media/azure-stack-create-vm-template/10.PNG)

3. Nyissa meg a Terminálszolgáltatások emulátor, és adja meg a következő parancsok futtatásával kezdheti minikube:

    >    `sudo minikube start --vm-driver=none`
    >   
    >    `sudo minikube addons enable dashboard`
    >    
    >    `sudo minikube dashboard --url`

   ![](media/azure-stack-create-vm-template/11.PNG)

4. Nyissa meg a webböngészőt, és látogasson el a kubernetes irányítópult címet. Gratulálunk, most már rendelkezik egy teljes mértékben működő kubernetes telepítését minikube!

   ![](media/azure-stack-create-vm-template/12.PNG)

5. Ha meg szeretne telepíteni egy mintaalkalmazást, keresse fel a dokumentációs oldalát a kubernetes, hagyja ki a "Minikube fürt létrehozása" szakaszban, már létrehozott egy újabb. Egyszerűen Ugrás a szakasz "A Node.js-alkalmazás létrehozása" https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * További tudnivalók az Azure-verem gyorsindítási sablonok 
> * Egy egyéni GitHub-sablon virtuális gép létrehozása
> * Indítsa el a minikube, és egy alkalmazás telepítése

