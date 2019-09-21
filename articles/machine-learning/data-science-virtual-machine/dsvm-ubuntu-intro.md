---
title: 'Gyors útmutató: Ubuntu rendszerű DSVM létrehozása'
description: A Linux (Ubuntu) Data Science Virtual Machine konfigurálása és létrehozása az elemzéshez és a gépi tanuláshoz.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/10/2019
ms.openlocfilehash: 5b50355391180c4a499a0f4e77c3897f8969cdb8
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170953"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-linux-ubuntu"></a>Gyors útmutató: A Linux rendszerhez készült Data Science Virtual Machine beállítása (Ubuntu)

Megkezdheti az Ubuntu Data Science Virtual Machine futtatását.

## <a name="prerequisites"></a>Előfeltételek

Windows Data Science Virtual Machine létrehozásához Azure-előfizetéssel kell rendelkeznie. [Próbálja ki ingyenesen az Azure](https://azure.com/free)-t.

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Az adatelemző virtuális gép létrehozása Linux rendszeren

Az alábbiakban a Linux-példány, az adatelemző virtuális gép létrehozásához szükséges lépéseket:

1. Ha még nem jelentkezett be, lépjen a [Azure Portalba](https://portal.azure.com) , ahol a rendszer kérni fogja, hogy jelentkezzen be az Azure-fiókjába.
1. Keresse meg a virtuális gép listáját az "adatelemzési virtuális gép" beírásával, és válassza a "Data Science Virtual Machine for Linux (Ubuntu)" lehetőséget.
    
    ![Ubuntu VM-lista](./media/dsvm-ubuntu-intro/search-ubuntu.png)

1. A következő ablakban válassza a **Létrehozás**lehetőséget.

    ![Varázsló virtuális gép létrehozásához](./media/dsvm-ubuntu-intro/create-linux.png)

1. A rendszer átirányítja a virtuális gép létrehozása panelre.
   
   ![Az Ubuntu virtuális géphez tartozó alapismeretek lap](./media/dsvm-ubuntu-intro/review-create-ubuntu.png)

1. Adja meg a következő adatokat a varázsló egyes lépéseinek konfigurálásához:

    1. **Alapvető beállítások**:
    
       * **Előfizetés**: Ha egynél több előfizetéssel rendelkezik, válassza ki azt a számítógépet, amelyet a gép létrehoz és számláz. Ehhez az előfizetéshez erőforrás-létrehozási jogosultságokkal kell rendelkeznie.
       * **Erőforráscsoport**: Hozzon létre egy új csoportot, vagy használjon egy meglévőt.
       * **Virtuális gép neve**: Adja meg a virtuális gép nevét. Így fog megjelenni a Azure Portalban.
       * **Régió**: Válassza ki a legmegfelelőbb adatközpontot. A leggyorsabb hálózati hozzáféréshez ez az adatközpont, amely a legtöbb adattal rendelkezik, vagy a legközelebb áll a fizikai helyhez. További információ az [Azure-régiókról](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Rendszerkép**: Ne módosítsa az alapértelmezett értéket.
       * **Méret**: Az automatikus feltöltésnek az általános számítási feladatokhoz megfelelő méretűnek kell lennie. További információ a [LINUXOS virtuális gépek méretéről az Azure-ban](../../virtual-machines/linux/sizes.md).
       * **Hitelesítés típusa**: A gyorsabb telepítéshez válassza a "jelszó" lehetőséget. 
         
         > [!NOTE]
         > Ha a JupyterHub-t szeretné használni, ügyeljen arra, hogy válassza a "jelszó" lehetőséget, mivel a JupyterHub *nem* az SSH nyilvános kulcsok használatára van konfigurálva.

       * **Felhasználónév**: Adja meg a rendszergazda felhasználónevét. Ezt a felhasználónevet fogja használni a virtuális gépre való bejelentkezéshez, és nem kell megegyeznie az Azure-felhasználónévvel. Ne *használjon* nagybetűs betűket.
         
         > [!NOTE]
         > Ha nagybetűs betűket használ a felhasználónévben, a JupyterHub nem fog működni, és 500 belső kiszolgálóhiba lép fel.

       * **Jelszó**: Adja meg azt a jelszót, amelyet a virtuális gépre való bejelentkezéshez használni fog.    
    
   1. Válassza az **Áttekintés + létrehozás** lehetőséget.
   1. **Felülvizsgálat + létrehozás**
      * Győződjön meg arról, hogy helyesen szerepel-e a megadott összes információt. 
      * Kattintson a **Létrehozás** gombra.
    
    A kiépítés körülbelül 5 percet vesz igénybe. Az állapot megjelenik a Azure Portalban.

## <a name="how-to-access-the-ubuntu-data-science-virtual-machine"></a>Az Ubuntu Data Science Virtual Machine elérése

Az Ubuntu DSVM háromféle módon érheti el:

  * SSH-munkamenetet a Terminálszolgáltatások
  * Grafikus munkamenetek X2Go
  * JupyterHub és JupyterLab Jupyter notebookokhoz

Data Science Virtual Machine is csatolhat, hogy Azure Notebooks Jupyter jegyzetfüzeteket futtasson a virtuális gépen, és megkerüli az ingyenes szolgáltatási szintet. További információ: [Azure Notebooks projektek kezelése és konfigurálása](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

### <a name="ssh"></a>SSH

Ha a virtuális gép létrehozása után SSH-hozzáféréssel lett konfigurálva, akkor az SSH használatával bejelentkezhet. A fiók hitelesítő adataival, amelyet a **alapjai** szakasz 3. lépésben a szöveg shell felületén. Windows rendszeren egy SSH-ügyfél eszközt (például a [Putty](https://www.putty.org)-t) tölthet le. Ha a grafikus asztali számítógép (X rendszer) használatát részesíti előnyben, használhat X11-továbbítást a PuTTY-on.

> [!NOTE]
> A X2Go ügyfél jobban teljesített, mint továbbítási tesztelés X11. Azt javasoljuk, hogy az asztali grafikus felület X2Go ügyfél használatával.

### <a name="x2go"></a>X2Go

A Linux rendszerű virtuális gép már ki van építve a X2Go-kiszolgálóval, és készen áll az ügyfélkapcsolatok fogadására. Ha csatlakozni szeretne a Linuxos virtuális gép grafikus asztali, az alábbi eljárással az ügyfélen:

1. Töltse le és telepítse a saját ügyfélplatformjára X2Go ügyfél [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Jegyezze fel a virtuális gép nyilvános IP-címét, amelyet az imént létrehozott virtuális gép megnyitásával talál a Azure Portal.

   ![Ubuntu gép IP-címe](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

1. Futtassa a X2Go-ügyfelet. Ha az "új munkamenet" ablak nem jelenik meg automatikusan, ugorjon a munkamenet-> új munkamenet elemre.

1. Az eredményül kapott konfigurációs ablakban adja meg a következő konfigurációs paramétereket:
   * **Munkamenet lapon**:
     * **Gazdagép**: Adja meg a virtuális gép IP-címét, amelyet korábban feljegyzést készített.
     * **Bejelentkezés**: Adja meg a felhasználónevet a Linux rendszerű virtuális gépen.
     * **SSH-port**: Hagyja meg az alapértelmezett értéket 22-én.
     * **Munkamenet típusa**: Módosítsa az értéket az **Xfce**értékre. A Linux rendszerű virtuális gép jelenleg csak az XFCE Desktopot támogatja.
   * **Média lap**: Ha nem szükséges, ki is kapcsolhatja a hangtámogatást és az ügyfél-nyomtatást.
   * **Megosztott mappák**: Ha a Linux rendszerű virtuális gépen csatlakoztatni kívánja a címtárakat az ügyfélgépekről, adja hozzá azokat az ügyfélszámítógép-könyvtárakat, amelyeket meg szeretne osztani a virtuális géppel ezen a lapon.

   ![X2go-konfiguráció](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Kattintson az **OK** gombra.
1. A X2Go ablak jobb oldali paneljén kattintson a mezőre a virtuális gép bejelentkezési képernyőjének megjelenítéséhez.
1. Adja meg a virtuális gép jelszavát.
1. Kattintson az **OK** gombra.
1. Előfordulhat, hogy meg kell adnia a X2Go engedélyt a tűzfal megkerüléséhez a csatlakozás befejezéséhez.
1. Ekkor megjelenik az Ubuntu-DSVM grafikus felülete. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub és JupyterLab

Az Ubuntu DSVM egy többfelhasználós Jupyter-kiszolgálót futtat [JupyterHub](https://github.com/jupyterhub/jupyterhub). A kapcsolódáshoz hajtsa végre a következő lépéseket:

   1. Jegyezze fel a virtuális gép nyilvános IP-címét úgy, hogy megkeresi és kijelöli a virtuális gépet a Azure Portalban.
      ![Ubuntu gép IP-címe](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

   1. A helyi gépen nyisson meg egy webböngészőt, és navigáljon a\/https:/Your-VM-IP: 8000 fájlhoz, és cserélje le a "Your-VM-IP" kifejezést a korábban jegyzett IP-címre.
   1. Adja meg a virtuális gép létrehozásához használt felhasználónevet és jelszót, majd jelentkezzen be. 

      ![Adja meg a Jupyter-bejelentkezést](./media/dsvm-ubuntu-intro/jupyter-login.png)

   1. Böngésszen a rendelkezésre álló számos jegyzetfüzetben.

JupyterLab, a Jupyter-notebookok és JupyterHub, következő generációja érhető el. A hozzáféréshez jelentkezzen be a JupyterHub, majd keresse meg a https:\//Your-VM-IP: 8000/User/your-username/Lab URL-címet, és cserélje le a "your-username" kifejezést a virtuális gép konfigurálásakor kiválasztott felhasználónévre.

A JupyterLab az alapértelmezett jegyzetfüzet- `/etc/jupyterhub/jupyterhub_config.py`kiszolgálóként is beállíthatja, ha hozzáadja ezt a sort a következőhöz:

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>További lépések

Itt látható, hogyan a tanulási és feltárásra továbbra is:

* A [Linux rendszerhez készült Data Science Virtual Machine adatelemzés](linux-dsvm-walkthrough.md) azt mutatja be, hogyan végezhető el számos általános adatelemzési feladat az itt kiépített Linux-DSVM. 
* A jelen cikkben ismertetett eszközök kipróbálásával megismerheti a DSVM különböző adatelemzési eszközeit. A virtuális gépen található `dsvm-more-info` rendszerhéjon is futtathatja az alapszintű bevezetést és a mutatókat, hogy további információkhoz lehessen jutni a virtuális gépre telepített eszközökről.  
* Megtudhatja, hogyan teljes körű elemzési megoldásokat rendszeresen használatával hozhat létre a [csoportos adatelemzési folyamat](https://aka.ms/tdsp).
* Látogasson el a [Azure AI-katalógusban](https://gallery.azure.ai/) machine learning és a data analytics minták, amelyek használják az Azure AI-szolgáltatások.
* Olvassa el a virtuális gép megfelelő [dokumentációját](./reference-ubuntu-vm.md) .
