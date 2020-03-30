---
title: 'Rövid útmutató: Ubuntu data science virtuális gép létrehozása'
titleSuffix: Azure Data Science Virtual Machine
description: Konfigurálja és hozzon létre egy Data Science Virtual Machine for Linux (Ubuntu) elemzéseket és gépi tanulást.
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: quickstart
ms.date: 03/10/2020
ms.openlocfilehash: f7629b4724e85f93a8dfe3e37ac2b2155288d235
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241639"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-linux-ubuntu"></a>Rövid útmutató: Az adatelemzési virtuális gép beállítása Linuxhoz (Ubuntu)

Az Ubuntu 18.04 Data Science virtuális géppel feláll és működik.

## <a name="prerequisites"></a>Előfeltételek

Ubuntu 18.04 Data Science virtuális gép létrehozásához azure-előfizetéssel kell rendelkeznie. [Próbálja ki ingyen az Azure-t.](https://azure.com/free)
Kérjük, vegye figyelembe, hogy az ingyenes Azure-fiókok nem támogatják a GPU-kompatibilis virtuálisgép-skus-okat.

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Hozza létre az adattudomány virtuális gép linuxos

Az alábbiakban az Ubuntu 18.04 adatelemzési virtuális gép egy példányának létrehozásához szükséges lépéseket ismertetheti:

1. Nyissa meg az [Azure Portalon,](https://portal.azure.com) ha még nincs bejelentkezve, a rendszer kérheti, hogy jelentkezzen be az Azure-fiókjába.
1. Keresse meg a virtuális gép listáját írja be az "adattudomány virtuális gép", és kiválasztja a "Data Science Virtual Machine-Ubuntu 18.04"

1. A következő ablakban válassza a **Létrehozás lehetőséget.**

1. Át kell irányítani a "Virtuális gép létrehozása" panelre.
   
1. A varázsló egyes lépéseinek konfigurálásához adja meg a következő adatokat:

    1. **Alapok:**
    
       * **Előfizetés:** Ha egynél több előfizetéssel rendelkezik, válassza ki azt, amelyen a gép létrejön és kiszámlázásra kerül. Ehhez az előfizetéshez erőforrás-létrehozási jogosultságokkal kell rendelkeznie.
       * **Erőforráscsoport**: Hozzon létre egy új csoportot, vagy használjon egy meglévőt.
       * **Virtuális gép neve**: Adja meg a virtuális gép nevét. Így fog megjelenni az Azure Portalon.
       * **Régió**: Válassza ki a legmegfelelőbb adatközpontot. A leggyorsabb hálózati hozzáférés érdekében ez az adatközpont, amely a legtöbb adatot, vagy a legközelebb a fizikai helyét. További információ az [Azure-régiókról.](https://azure.microsoft.com/global-infrastructure/regions/)
       * **Kép**: Hagyja meg az alapértelmezett értéket.
       * **Méret**: Ez automatikusan feltölti az általános számítási feladatoknak megfelelő méretet. További információ a [Linux virtuális gépek azure-beli méreteiről.](../../virtual-machines/linux/sizes.md)
       * **Hitelesítés típusa**: A gyorsabb beállítás érdekében válassza a "Jelszó" lehetőséget. 
         
         > [!NOTE]
         > Ha a JupyterHubot kívánja használni, győződjön meg arról, hogy válassza a "Jelszó" lehetőséget, mivel a JupyterHub *nincs* konfigurálva az SSH nyilvános kulcsok használatára.

       * **Felhasználónév**: Adja meg a rendszergazda felhasználónevét. Ez az a felhasználónév, amelyet a virtuális gépbe való bejelentkezéshez használ, és nem kell megegyeznie az Azure-felhasználónevével. *Ne* használjon nagybetűs betűket.
         
         > [!NOTE]
         > Ha nagybetűs betűket használ a felhasználónevében, a JupyterHub nem fog működni, és 500 belső kiszolgálóhibával fog találkozni.

       * **Jelszó**: Adja meg a virtuális gépre való bejelentkezéshez használt jelszót.    
    
   1. Válassza az **Áttekintés + létrehozás** lehetőséget.
   1. **Véleményezés+létrehozás**
      * Ellenőrizze, hogy minden megadott információ helyes-e. 
      * Kattintson a **Létrehozás** gombra.
    
    A kiépítés körülbelül 5 percet vesz igénybe. Az állapot megjelenik az Azure Portalon.

## <a name="how-to-access-the-ubuntu-data-science-virtual-machine"></a>Hogyan lehet hozzáférni az Ubuntu Data Science virtuális gép

Az Ubuntu DSVM három féleképpen érhető el:

  * SSH terminál-munkamenetekhez
  * X2Go grafikus munkamenetekhez
  * JupyterHub és JupyterLab Jupyter-notebookokhoz

Egy adatelemzési virtuális gép az Azure-jegyzetfüzetek jupyter notebookok futtatásához a virtuális gépen, és megkerüli az ingyenes szolgáltatási szint korlátait. További információt az [Azure Notebook-projektek kezelése és konfigurálása](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier)című témakörben talál.

### <a name="ssh"></a>SSH

A virtuális gép létrehozása után, ha ssh-hozzáféréssel volt konfigurálva, ssh használatával bejelentkezhet. Használja a szöveges rendszerhéj felületének 3. **Basics** Windows rendszeren letöltheti az SSH kliens eszközt, például [a PuTTY-t.](https://www.putty.org) Ha inkább egy grafikus asztal (X Window System), akkor x11 továbbítás a PuTTY.

> [!NOTE]
> Az X2Go kliens jobban teljesített, mint az X11 továbbítás a tesztelés során. Javasoljuk, hogy az X2Go klienst használja grafikus asztali felülethez.

### <a name="x2go"></a>X2Go között

A Linux virtuális gép már ki van építve az X2Go Server rel, és készen áll az ügyfélkapcsolatok fogadására. A Linux VM grafikus asztalhoz való csatlakozáshoz hajtsa végre az alábbi eljárást az ügyfélen:

1. Töltse le és telepítse az X2Go klienst az ügyfélplatformjára az [X2Go-tól.](https://wiki.x2go.org/doku.php/doc:installation:x2goclient)
1. Jegyezze fel a virtuális gép nyilvános IP-címét, amely az Azure Portalon található az imént létrehozott virtuális gép megnyitásával.

   ![Ubuntu gép IP-címe](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

1. Futtassa az X2Go ügyfelet. Ha az "Új munkamenet" ablak nem jelenik meg automatikusan, lépjen a Munkamenet -> Új munkamenet ablakra.

1. Az eredményül kapott konfigurációs ablakban adja meg a következő konfigurációs paramétereket:
   * **Munkamenet lap**:
     * **Host**: Adja meg a virtuális gép IP-címét, amelyet korábban feljegyezte.
     * **Bejelentkezés**: Adja meg a felhasználónevet a Linux vm.Login : Enter the username on the Linux VM.
     * **SSH port**: Hagyja 22-nél, az alapértelmezett érték.
     * **Munkamenet típusa**: Módosítsa az értéket **XFCE**értékre. Jelenleg a Linux virtuális gép csak az XFCE asztalt támogatja.
   * **Média lap**: Kikapcsolhatja a hangtámogatást és az ügyfélnyomtatást, ha nem kell használnia őket.
   * **Megosztott mappák:** Ha azt szeretné, hogy a Linux virtuális gépre csatlakoztatott ügyfélgépek könyvtárai hozzáadódnak a lapon a virtuális géppel megosztani kívánt ügyfélgép-könyvtárakhoz.

   ![X2go konfiguráció](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Válassza **az OK gombot.**
1. Kattintson az X2Go ablak jobb oldali ablaktáblájában lévő mezőre a virtuális gép bejelentkezési képernyőjének megírásához.
1. Adja meg a virtuális gép jelszavát.
1. Válassza **az OK gombot.**
1. Előfordulhat, hogy engedélyt kell adnia az X2Go-nak a tűzfal megkerülésére a csatlakozás befejezéséhez.
1. Most látnia kell az Ubuntu DSVM grafikus felületét. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub és JupyterLab

Az Ubuntu DSVM fut [JupyterHub](https://github.com/jupyterhub/jupyterhub), egy többfelhasználós Jupyter szerver. A csatlakozáshoz tegye a következő lépéseket:

   1. Jegyezze fel a virtuális gép nyilvános IP-címét, keresse meg és válassza ki a virtuális gép ét az Azure Portalon.
      ![Ubuntu gép IP-címe](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

   1. A helyi számítógépről nyisson meg egy\/webböngészőt, és keresse meg a https: /your-vm-ip:8000 címet, és cserélje ki a "your-vm-ip" címet a korábban figyelembe vett IP-címmel.
   1. A böngésző valószínűleg megakadályozza, hogy közvetlenül megnyissa az oldalt, és jelezze, hogy tanúsítványhiba történt. A DSVM önaláírt tanúsítvánnyal nyújt biztonságot. A legtöbb böngésző lehetővé teszi, hogy kattintson át, miután ezt a figyelmeztetést. Számos böngésző továbbra is valamilyen vizuális figyelmeztetést jelenít meg a tanúsítványról a webes munkamenet során.
   1. Adja meg a virtuális gép létrehozásához használt felhasználónevet és jelszót, és jelentkezzen be. 

      ![Adja meg a Jupyter bejelentkezést](./media/dsvm-ubuntu-intro/jupyter-login.png)

   1. Böngésszen a rendelkezésre álló számos mintajegyzetfüzet között.

JupyterLab, a következő generációs Jupyter notebookok és JupyterHub, is elérhető. Eléréséhez jelentkezzen be a JupyterHubra, majd keresse\/meg az URL-címet https: /your-vm-ip:8000/user/your-username/lab, a "your-username" helyett a virtuális gép konfigurálásakor kiválasztott felhasználónév. Ismét előfordulhat, hogy egy tanúsítványhiba miatt kezdetben le van tiltva a webhely elérésének.

A JupyterLab alapértelmezett jegyzetfüzet-kiszolgálóként beállítható, `/etc/jupyterhub/jupyterhub_config.py`ha ezt a sort hozzáadja a következőhöz:

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>További lépések

Így folytathatja a tanulást és a felfedezést:

* Az [adatelemzési adatok a Data Science virtuális gép Linux-forgatókönyv](linux-dsvm-walkthrough.md) bemutatja, hogyan kell elvégezni számos gyakori adatelemzési feladatok at a Linux DSVM kiépített itt. 
* Fedezze fel a dsvm különböző adatelemzési eszközeit a cikkben ismertetett eszközök kipróbálásával. A virtuális `dsvm-more-info` gépen belül is futtathatja a rendszerhéjat egy alapvető bevezetéshez, és további információkhoz vezethet a virtuális gépre telepített eszközökről.  
* Ismerje meg, hogyan hozhat létre szisztematikusan végpontok között analitikai megoldásokat a [Team Data Science Process](https://aka.ms/tdsp)használatával.
* Látogasson el az [Azure AI-galériába](https://gallery.azure.ai/) az Azure AI-szolgáltatásokat használó gépi tanulási és adatelemzési mintákért.
* Tekintse meg a virtuális gép megfelelő [referenciadokumentációját.](./reference-ubuntu-vm.md)
