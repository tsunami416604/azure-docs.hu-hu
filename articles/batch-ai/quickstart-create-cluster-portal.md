---
title: Azure rövid útmutató – Batch AI-fürt létrehozása – Portal | Microsoft Docs
description: Rövid útmutató – Batch AI-fürt létrehozása gépi tanulási és mesterségesintelligencia-modellek betanításához – Azure Portal
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/15/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 78c743448a7f7439875d3598d6ba5d4eb6dc12fc
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408935"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-portal"></a>Gyors útmutató: Hozzon létre egy fürtöt, a Batch AI betanítási feladatokat az Azure portal használatával

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Ez a rövid útmutató bemutatja, hogyan hozhat létre egy mesterségesintelligencia- és gépi tanulási modellek betanítására használható Batch AI-fürtöt az Azure Portallal. A Batch AI egy felügyelt szolgáltatás az adatelemzők és MI-kutatók számára, amely lehetővé teszi, hogy nagy számban tanítsanak be mesterségesintelligencia- és gépi tanulási modelleket Azure-beli virtuális gépek fürtjein.

A fürt kezdetben egyetlen GPU-csomóponttal és egy csatlakoztatott fájlkiszolgálóval rendelkezik. A rövid útmutató befejezése után rendelkezni fog egy vertikálisan felskálázható és mélytanulási modellek betanítására használható fürttel. A betanítási feladatok fürtnek való elküldéséhez használja a Batch AI-t, az [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) eszközeit vagy a [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai) bővítményt.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-ssh-key-pair"></a>SSH-kulcspár létrehozása

A rövid útmutató elvégzéséhez egy SSH-kulcspárra lesz szüksége. Ha már rendelkezik SSH-kulcspárral, kihagyhatja ezt a lépést.

SSH-kulcspár létrehozásához futtassa a következő parancsot a Bash felületről, és kövesse a képernyőn megjelenő utasításokat. Például használhatja az [Azure Cloud Shellt](../cloud-shell/overview.md), vagy Windows rendszeren esetében a [Linux Windows alrendszerét](/windows/wsl/install-win10). A parancs kimenete tartalmazza a nyilvános kulcsfájl fájlnevét. Másolja a nyilvánoskulcs-fájl (`cat ~/.ssh/id_rsa.pub`) tartalmát a vágólapra vagy egy olyan helyre, amelyet egy későbbi lépésben elérhet.

```bash
ssh-keygen -t rsa -b 2048
```

Az SSH-kulcspárok létrehozásáról a [Nyilvános és titkos SSH-kulcspár létrehozása és használata az Azure-ban Linux rendszerű virtuális gépekhez](../virtual-machines/linux/mac-create-ssh-keys.md) című cikkben talál részletesebb információt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-batch-ai-workspace"></a>Batch AI-munkaterület létrehozása

Első lépésként hozzon létre egy Batch AI-munkaterületet a Batch AI-erőforrások rendszerezéséhez. A munkaterület egy vagy több fürtöt vagy más Batch AI-erőforrást tartalmazhat.

1. Válassza a **Minden szolgáltatás** lehetőséget, és szűrjön a **Batch AI** kifejezésre.

2. Válassza a **Munkaterület hozzáadása** lehetőséget.

3. Adjon meg értékeket a **Munkaterület neve** és az **Erőforráscsoport** mezőben. Ha szeretne, adjon meg különböző beállításokat a munkaterület **Előfizetés** és **Hely** mezőiben. Válassza a **Munkaterület létrehozása** lehetőséget.

  ![Batch AI-munkaterület létrehozása](./media/quickstart-create-cluster-portal/create-workspace.png)

Amikor megjelenik az **Üzembe helyezés sikeres** üzenet, lépjen a létrehozott erőforrásra, és válassza ki a munkaterületet.

## <a name="create-a-file-server"></a>Fájlkiszolgáló létrehozása

A Batch AI-fájlkiszolgáló egy egycsomópontos NFS, amely automatikusan csatlakoztatható fürtcsomópontokhoz. Ez az egyik módja annak, hogy tárhelyet biztosítson a betanítási feladatok bemeneti adatai és kimenete számára.

1. A munkaterületen válassza a **Fájlkiszolgáló** > **Batch AI-fájlkiszolgáló hozzáadása** elemet.

2. Adjon meg értékeket a **Fájlkiszolgáló neve** és a **Virtuális gép mérete** mezőben. Ebben a rövid útmutatóban a *Standard D1_v2* virtuálisgép-méret javasolt a fájlkiszolgálóhoz. Ha nagyobb mennyiségű bemeneti vagy kimeneti adatot kell tárolnia a betanítási feladatokhoz, válasszon egy másik méretet.

3. Adjon meg egy **Rendszergazdai felhasználónevet**, és másolja át a nyilvános SSH-kulcsfájl tartalmát az **SSH-kulcs** mezőbe. A többi értéknél fogadja el az alapértelmezett beállításokat, és válassza a **Fájlkiszolgáló létrehozása** lehetőséget.

  ![Batch AI-fájlkiszolgáló létrehozása](./media/quickstart-create-cluster-portal/create-file-server.png)

A fájlkiszolgáló üzembe helyezése néhány percet vesz igénybe.

A fájlkiszolgáló létrehozása után kattintson a **Tulajdonságok** elemre, és jegyezze fel a **Csatlakoztatási beállítások** értékét. A betanítási adatok és a kimeneti fájlok megjelölt könyvtárba (*/data*) való fel- és letöltéséhez létrehozhat egy SSH-kapcsolatot a kiszolgáló nyilvános IP-címével.

![A fájlkiszolgáló tulajdonságai](./media/quickstart-create-cluster-portal/file-server-properties.png)

## <a name="create-a-cluster"></a>Fürt létrehozása

Az alábbi lépésekkel egy egyetlen GPU-csomóponttal rendelkező fürtöt hozhat létre. A fürtcsomópont egy alapértelmezett Ubuntu Server-rendszerképet futtat a tárolóalapú alkalmazások üzemeltetéséhez, amelyet a legtöbb betanítási számítási feladathoz használhat. A fürtcsomópont a csatlakozási pontján csatlakoztatja a fájlkiszolgálót. 

1. A Batch AI-munkaterületen válassza a **Fürt** > **Batch AI-fürt hozzáadása** lehetőséget.

2. Adja meg a **Fürt neve** mező értékét és a következő beállításokat. Az ajánlott virtuálisgép-méret egy NVIDIA Tesla K80 GPU-val rendelkezik.
  
   |Beállítás  |Érték  |
   |---------|---------|
   |**Virtuális gép mérete**     |Standard NC6|
   |**Csomópontok célszáma**     |1|

3. Adjon meg egy **Rendszergazdai felhasználónevet**, és másolja át a nyilvános SSH-kulcsfájl tartalmát az **SSH-kulcs** mezőbe. Fogadja el a fennmaradó értékek ezen a lapon alapértelmezett értéket, és válassza ki **tovább: Csomópont beállítása**.

   ![A fürt alapvető adatainak megadása](./media/quickstart-create-cluster-portal/create-cluster.png)

4. A **Kötetek csatlakoztatása** területen válassza a **Fájlkiszolgálói hivatkozások** > **Hozzáadás** lehetőséget. Válassza ki a korábban létrehozott fájlkiszolgálót. Adjon meg egy **Relatív csatlakoztatási útvonalat**, ahol a fájlkiszolgáló csatlakoztatva van az egyes fürtcsomópontokhoz. Válassza a **Mentés és folytatás** lehetőséget.

   ![Fájlkiszolgálói hivatkozás hozzáadása](./media/quickstart-create-cluster-portal/file-server-reference.png)

Mentse a csomópont-beállítást, és válassza a **Fürt létrehozása** lehetőséget.

Eltarthat néhány percig, mire a Batch AI lefoglalja a csomópontot. Eközben a fürt **Lefoglalási állapota** **Átméretezés**. Néhány perc elteltével a fürt állapota **Stabil** értékre változik, és elindul a csomópont.

![A fürt elindul](./media/quickstart-create-cluster-portal/cluster-resizing.png)

Válassza ki a fürt nevét a csomópont állapotának megtekintéséhez. Ha egy csomópont állapota **Tétlen**, készen áll a betanítási feladatok futtatására.

### <a name="list-cluster-nodes"></a>Fürtcsomópontok listázása

Ha csatlakoznia kell a fürtcsomópontokhoz (ebben az esetben egyetlen csomóponthoz) az alkalmazások telepítéséhez vagy a karbantartás elvégzéséhez, kérje le a kapcsolati adatokat a portálon. A fürt létrehozása után kattintson a **Csomópontok** lehetőségre, és jegyezze fel az SSH **kapcsolati** beállításait (IP-cím és portszám).

![Fürtcsomópontok](./media/quickstart-create-cluster-portal/cluster-nodes.png)

Ezeket az adatokat használhatja a csomóponttal való SSH-kapcsolat létesítéséhez. Például helyettesítse be a csomópont megfelelő IP-címét és portszámát a következő parancsba:

```bash
ssh myusername@137.135.82.15 -p 50000
``` 

### <a name="resize-the-cluster"></a>A fürt átméretezése

Ha a fürtöt egy modell betanítására használja, előfordulhat, hogy több számítási erőforrásra lesz szüksége. Például ha 2 csomópontra szeretné növelni a méretet egy elosztott betanítási feladathoz, válassza a **Skálázás** lehetőséget, és állítsa a **Csomópontok célszámát** 2-re. Mentse a konfigurációt.

![Fürt méretezése](./media/quickstart-create-cluster-portal/scale-cluster.png)

A fürt átméretezése néhány percet vesz igénybe.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha folytatni szeretné a Batch AI-oktatóanyagok és -minták használatát, használja az ebben a rövid útmutatóban létrehozott Batch AI-munkaterületet, -fájlkiszolgálót és -fürtöt.

A Batch AI-fürtért és -fájlkiszolgálóért díjat számítunk fel, amíg a mögöttes virtuális gépek futnak, még akkor is, ha nincsenek feladatok ütemezve. Ha meg szeretné tartani a fürtkonfigurációt, amikor nem futtat feladatot, méretezze át a fürtöt 0 csomópontra. Később méretezze át őket 1 vagy több csomópontra a feladatok futtatásához. 

Ha már nincs rá szükség, törölje a fürtöt és a fájlkiszolgálót tartalmazó Batch AI-munkaterületet. Ehhez válassza ki a Batch AI-munkaterületet, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban bemutattuk, hogyan hozhat létre Batch AI-fürtöt és egy csatlakoztatott fájlkiszolgálót az Azure Portal használatával. Ha meg szeretné tudni, hogyan használhatja a Batch AI-fürtöt a modellek betanítására, folytassa a mélytanulási modellek betanításával foglalkozó oktatóanyaggal.

> [!div class="nextstepaction"]
> [Mélytanulási modell betanítása](./quickstart-tensorflow-training-cli.md)
