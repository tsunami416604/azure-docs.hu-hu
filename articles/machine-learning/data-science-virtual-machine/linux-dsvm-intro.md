---
title: 'Gyors útmutató: CentOS Data Science Virtual Machine létrehozása'
titleSuffix: Azure Data Science Virtual Machine
description: A Linux (CentOS) Data Science Virtual Machine konfigurálása és létrehozása az elemzéshez és a gépi tanuláshoz.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: 73541b31125ee6e99dc2351e26f6a564a1603487
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526025"
---
# <a name="quickstart-set-up-a-centos-linux-data-science-virtual-machine-in-azure"></a>Gyors útmutató: CentOS (Linux) Data Science Virtual Machine beállítása az Azure-ban

A CentOS-alapú Data Science Virtual Machine-t használja.

## <a name="prerequisites"></a>Előfeltételek

CentOS Data Science Virtual Machine létrehozásához **Azure-előfizetéssel**kell rendelkeznie. [Hozzon létre egy ingyenes előfizetést](https://azure.com/free).

## <a name="create-your-centos-data-science-virtual-machine"></a>A CentOS-Data Science Virtual Machine létrehozása

A CentOS Data Science Virtual Machine példányának létrehozásának lépései a következők:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Előfordulhat, hogy a rendszer arra kéri, hogy jelentkezzen be az Azure-fiókjába, ha még nincs bejelentkezve. 
1. Írja be a "adatelemzési virtuális gép" kifejezést a keresőmezőbe, és válassza ki a CentOS DSVM.

    ![CentOS keresési eredmény](./media/linux-dsvm-intro/search-centos.png)

1. A következő ablakban válassza a **Létrehozás**lehetőséget.

    [![](media/linux-dsvm-intro/create-centos.png "Button to create a CentOS machine")](media/linux-dsvm-intro/create-centos-expanded.png#lightbox)

1. A rendszer átirányítja a virtuális gép létrehozása panelre.
   
   ![A CentOS virtuális géphez tartozó alapismeretek lap](./media/linux-dsvm-intro/review-create-centos.png)

1. Adja meg a következő adatokat a varázsló egyes lépéseinek konfigurálásához:

    1. **Alapismeretek**:
    
       * **Előfizetés**: Ha egynél több előfizetéssel rendelkezik, válassza ki azt a számítógépet, amelyet a gép létre fog hozni és számláz. Ehhez az előfizetéshez erőforrás-létrehozási jogosultságokkal kell rendelkeznie.
       * **Erőforráscsoport**: hozzon létre egy új csoportot, vagy használjon egy meglévőt.
       * **Virtuális gép neve**: adja meg a virtuális gép nevét. Így fog megjelenni a Azure Portalban.
       * **Régió**: válassza ki a legmegfelelőbb adatközpontot. A leggyorsabb hálózati hozzáféréshez ez az adatközpont, amely a legtöbb adattal rendelkezik, vagy a legközelebb áll a fizikai helyhez. További információ az [Azure-régiókról](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Rendszerkép**: hagyja meg az alapértelmezett értéket.
       * **Méret**: az általános számítási feladatokhoz megfelelő méretű automatikus feltöltést kell megadni. További információ a [LINUXOS virtuális gépek méretéről az Azure-ban](../../virtual-machines/linux/sizes.md).
       * **Hitelesítés típusa**: a gyorsabb telepítéshez válassza a "jelszó" lehetőséget. 
         
         > [!NOTE]
         > Ha a JupyterHub-t szeretné használni, ügyeljen arra, hogy válassza a "jelszó" lehetőséget, mivel a JupyterHub *nem* az SSH nyilvános kulcsok használatára van konfigurálva.

       * **Felhasználónév**: adja meg a rendszergazda felhasználónevét. Ezt a felhasználónevet fogja használni a virtuális gépre való bejelentkezéshez, és nem kell megegyeznie az Azure-felhasználónévvel. Ne *használjon* nagybetűs betűket.
         
         > [!NOTE]
         > Ha nagybetűs betűket használ a felhasználónévben, a JupyterHub nem fog működni, és 500 belső kiszolgálóhiba lép fel.

       * **Password (jelszó**): adja meg a virtuális gépre való bejelentkezéshez használni kívánt jelszót.    
    
   1. Válassza az **Áttekintés + létrehozás** lehetőséget.
   1. **Felülvizsgálat + létrehozás**
      * Győződjön meg arról, hogy helyesen szerepel-e a megadott összes információt. 
      * Kattintson a **Létrehozás** gombra.
    
    A kiépítés körülbelül 5 percet vesz igénybe. Az állapot megjelenik a Azure Portalban.

## <a name="how-to-access-the-centos-data-science-virtual-machine"></a>A CentOS Data Science Virtual Machine elérése

A CentOS-DSVM háromféleképpen érheti el:

  * SSH-munkamenetet a Terminálszolgáltatások
  * Grafikus munkamenetek X2Go
  * JupyterHub és JupyterLab Jupyter notebookokhoz

Data Science Virtual Machine is csatolhat, hogy Azure Notebooks Jupyter jegyzetfüzeteket futtasson a virtuális gépen, és megkerüli az ingyenes szolgáltatási szintet. További információ: [Azure Notebooks projektek kezelése és konfigurálása](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

### <a name="ssh"></a>SSH

Ha a virtuális gép létrehozása után SSH-hozzáféréssel lett konfigurálva, akkor az SSH használatával bejelentkezhet. Használja a szöveges rendszerhéj felületének 3. lépésének **alapok** szakaszában létrehozott fiók hitelesítő adatait. Windows rendszeren egy SSH-ügyfél eszközt (például a [Putty](https://www.putty.org)-t) tölthet le. Ha a grafikus asztali számítógép (X rendszer) használatát részesíti előnyben, használhat X11-továbbítást a PuTTY-on.

> [!NOTE]
> A X2Go ügyfél jobban teljesített, mint továbbítási tesztelés X11. Azt javasoljuk, hogy az asztali grafikus felület X2Go ügyfél használatával.

### <a name="x2go"></a>X2Go

A Linux rendszerű virtuális gép már ki van építve a X2Go-kiszolgálóval, és készen áll az ügyfélkapcsolatok fogadására. Ha csatlakozni szeretne a Linuxos virtuális gép grafikus asztali, az alábbi eljárással az ügyfélen:

1. Töltse le és telepítse a X2Go-ügyfelet az [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient)-ből származó ügyféloldali platformra.
1. Jegyezze fel a virtuális gép nyilvános IP-címét, amelyet az imént létrehozott virtuális gép megnyitásával talál a Azure Portal.

   ![CentOS gép IP-címe](./media/linux-dsvm-intro/centos-ip-address.png)

1. Futtassa a X2Go-ügyfelet. Ha az "új munkamenet" ablak nem jelenik meg automatikusan, ugorjon a munkamenet-> új munkamenet elemre.

1. Az eredményül kapott konfigurációs ablakban adja meg a következő konfigurációs paramétereket:
   * **Munkamenet lap**:
     * **Gazdagép**: adja meg a virtuális gép IP-címét, amelyet korábban jegyzett készített.
     * **Bejelentkezés**: adja meg a felhasználónevet a Linux rendszerű virtuális gépen.
     * **SSH-port**: hagyja meg az alapértelmezett értéket 22-én.
     * **Munkamenet típusa**: módosítsa az értéket az **Xfce**értékre. A Linux rendszerű virtuális gép jelenleg csak az XFCE Desktopot támogatja.
   * **Média lap**: kikapcsolhatja a hangtámogatást és az ügyfél-nyomtatást, ha nincs szükség rájuk.
   * **Megosztott mappák**: Ha a Linux rendszerű virtuális gépen csatlakoztatni kívánja az ügyfélgépekről származó címtárakat, adja hozzá a lapon a virtuális géppel megosztani kívánt ügyfélszámítógép-címtárakat.

   ![X2go-konfiguráció](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Kattintson az **OK** gombra.
1. A X2Go ablak jobb oldali paneljén kattintson a mezőre a virtuális gép bejelentkezési képernyőjének megjelenítéséhez.
1. Adja meg a virtuális gép jelszavát.
1. Kattintson az **OK** gombra.
1. Előfordulhat, hogy meg kell adnia a X2Go engedélyt a tűzfal megkerüléséhez a csatlakozás befejezéséhez.
1. Ekkor látnia kell a CentOS-DSVM grafikus felületét. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub és JupyterLab

A CentOS DSVM a [JupyterHub](https://github.com/jupyterhub/jupyterhub)-t, egy többfelhasználós Jupyter-kiszolgálót futtat. A kapcsolódáshoz hajtsa végre a következő lépéseket:

   1. Jegyezze fel a virtuális gép nyilvános IP-címét úgy, hogy megkeresi és kijelöli a virtuális gépet a Azure Portalban.

       ![CentOS gép IP-címe](./media/linux-dsvm-intro/centos-ip-address.png)

   1. A helyi gépen nyisson meg egy webböngészőt, és navigáljon a https:\//Your-VM-IP: 8000 értékre, és cserélje le a "Your-VM-IP" kifejezést a korábban jegyzett IP-címre.
   1. Adja meg a virtuális gép létrehozásához használt felhasználónevet és jelszót, majd jelentkezzen be. 

      ![Adja meg a Jupyter-bejelentkezést](./media/dsvm-ubuntu-intro/jupyter-login.png)

   1. Böngésszen a rendelkezésre álló számos jegyzetfüzetben.

JupyterLab, a Jupyter-notebookok és JupyterHub, következő generációja érhető el. A hozzáféréshez jelentkezzen be a JupyterHub, majd keresse meg a https:\//Your-VM-IP: 8000/User/your-username/Lab URL-címet, és cserélje le a "your-username" kifejezést a virtuális gép konfigurálásakor kiválasztott felhasználónévre.

A JupyterLab alapértelmezett jegyzetfüzet-kiszolgálóként is beállíthatja, ha hozzáadja ezt a sort a `/etc/jupyterhub/jupyterhub_config.py`hoz:

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Következő lépések

Itt látható, hogyan a tanulási és feltárásra továbbra is:

* A [linux Data Science Virtual Machine bemutatójának adatelemzési](linux-dsvm-walkthrough.md) útmutatója azt mutatja be, hogyan végezheti el a gyakori adatelemzési feladatokat az itt kiépített Linux-DSVM. 
* A jelen cikkben ismertetett eszközök kipróbálásával megismerheti a DSVM különböző adatelemzési eszközeit. Az alapszintű bevezetéshez és a DSVM telepített eszközökről további információkat a virtuális gép rendszerhéjában is futtathat `dsvm-more-info`.  
* Ismerje meg, hogyan hozhat létre rendszeresen teljes körű analitikai megoldásokat a [csoportos adatelemzési folyamat](https://aka.ms/tdsp)használatával.
* Látogasson el az Azure AI-szolgáltatásokat használó gépi tanulási és adatelemzési minták [Azure AI Gallery](https://gallery.azure.ai/) .
* Olvassa el a virtuális gép megfelelő [dokumentációját](./reference-centos-vm.md) .