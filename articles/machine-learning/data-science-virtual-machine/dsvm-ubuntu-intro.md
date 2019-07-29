---
title: Egy Ubuntu Linux rendszerű adatelemző virtuális gép létrehozása
titleSuffix: Azure
description: Állítsa be, és hozzon létre egy Data Science virtuális gép a Linux (Ubuntu) az Azure-ban, elemzési és gépi tanulás.
services: machine-learning
documentationcenter: ''
author: vijetajo
ms.author: vijetaj
manager: cgronlun
ms.custom: seodec18
ms.assetid: 3bab0ab9-3ea5-41a6-a62a-8c44fdbae43b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/16/2018
ms.openlocfilehash: 8a19f414f31c307111edad876ed973ff4027d907
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68591931"
---
# <a name="provision-the-data-science-virtual-machine-for-linux-ubuntu"></a>Az adatelemző virtuális gép kiépítése Linux (Ubuntu)

Az adatelemző virtuális gép Linux Ubuntu-alapú virtuálisgép-lemezkép, amely megkönnyíti a machine learninggel, többek között a deep learning, az Azure-ban – első lépések. Deep learning eszközök a következők:

* [Cafe](https://caffe.berkeleyvision.org/): Mély tanulási keretrendszer, amely sebességre, expressivity és modularitásra épül
* [Caffe2](https://github.com/caffe2/caffe2): A Cafe platformfüggetlen verziója
* [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK): A Microsoft Research mély tanulást szolgáló szoftveres eszközkészlete
* [H2O](https://www.h2o.ai/): Nyílt forráskódú big data platform és grafikus felhasználói felület
* [Kerasz](https://keras.io/): Magas szintű neurális hálózati API a Pythonban a TensorFlow, a Microsoft Cognitive Toolkit és a theano
* [MXNet](https://mxnet.io/): Rugalmas, hatékony, mély tanulási könyvtár számos nyelvi kötéssel
* [NVIDIA](https://developer.nvidia.com/digits)-SZÁMJEGYEK: Egy grafikus rendszer, amely leegyszerűsíti a közös mély tanulási feladatokat
* [PyTorch](https://pytorch.org/): Magas szintű Python-függvénytár, amely támogatja a dinamikus hálózatokat
* [TensorFlow](https://www.tensorflow.org/): Nyílt forráskódú kódtár a Google-tól származó gépi intelligenciához
* [Theano](http://deeplearning.net/software/theano/): Python-könyvtár a többdimenziós tömböket érintő matematikai kifejezések definiálásához, optimalizálásához és hatékony kiértékeléséhez
* [Zseblámpa](http://torch.ch/): A gépi tanulási algoritmusok széles körű támogatását biztosító tudományos számítástechnikai keretrendszer
* CUDA, cuDNN, és az NVIDIA illesztőprogram
* Számos minta Jupyter notebookok

Minden függvénytárak is a GPU-verziók, bár a Processzor terhelését is futnak.

Az adatelemző virtuális gép Linux rendszeren is adatelemzési és fejlesztési tevékenységekhez, beleértve a népszerű eszközöket tartalmaz:

* A Microsoft R Server Developer Edition szoftverhez a Microsoft R Open
* Anaconda Python elosztási (2.7-es és 3.5-ös verzió), beleértve a népszerű elemzőkönyvtárak
* JuliaPro - Julia nyelvhez, tudományos és data analytics népszerű kódtárak válogatott eloszlása
* Önálló Spark-példányt, és egyetlen csomópont Hadoop (HDFS, a Yarn)
* JupyterHub - támogató R, Python, PySpark, Julia kernelekkel többfelhasználós Jupyter notebook server
* Azure Storage Explorer
* Az Azure parancssori felület (CLI) Azure-erőforrások kezeléséhez
* Machine learning eszközök
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): Gyors gépi tanulási rendszer, amely olyan technikákat támogat, mint például az online, a kivonatolás, a allreduce, a csökkentés, a learning2search, az aktív és az interaktív tanulás
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): Egy olyan eszköz, amely gyors és pontos, növelt faszerkezetes megvalósítást biztosít
  * [Csörgő](https://togaware.com/rattle/): Egy grafikus eszköz, amely megkönnyíti az adatelemzést és a gépi tanulást az R Easy szolgáltatásban
  * [LightGBM](https://github.com/Microsoft/LightGBM): Gyors, elosztott, nagy teljesítményű színátmenet-növelő keretrendszer
* Az Azure SDK-t a Java, Python, node.js, a Ruby, PHP
* Az R és Python for kódtárak használata az Azure Machine Learning és más Azure-szolgáltatások
* Fejlesztői eszközök és a szerkesztők (RStudio, PyCharm, intellij-vel, Emacs, vim)

A feladatok egy sorozatát léptetés adatelemzésre áll:

1. Keresés, betöltés és előzetesen feldolgozni az adatokat
1. Buildelési és tesztelési célokra modellek
1. A modellek intelligens alkalmazásokban felhasználásra üzembe helyezése

Az adatszakértők a különböző eszközök segítségével ezeket a feladatokat. Ez elég sok időt vesz igénybe, keresse meg a szoftver megfelelő verzióját, és töltse le, majd fordítsa le a, és telepítse ezeket a verziókat.

Az adatelemző virtuális gép Linux esetén egyaránt ezt a terhet megkönnyítése érdekében. Segítségével gyorsan elindíthatja az elemzési projekt. Ez lehetővé teszi, hogy a különböző nyelveken, többek között az R, Python, SQL, a Java és C++ feladatok. Az Azure SDK tartalmazza a virtuális gép különböző szolgáltatások használata Linux rendszeren a Microsoft cloud platform az alkalmazások készítését teszi lehetővé. Ezenfelül más nyelvekre például Ruby, a Perl, a PHP és a node.js előre telepített is elérhető lesz.

Nincsenek szoftvereket díjmentesen használható a data science Virtuálisgép-rendszerképet. Csak az Azure-beli hardveres használati díjakat, amelyek értékelni a kiosztott virtuális gép mérete alapján kell fizetnie. A számítási díjak a további részletek találhatók a [ajánlatuk információs oldalán virtuális gép az Azure Marketplace-en](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Az adatelemző virtuális gép más verziói

A [CentOS](linux-dsvm-intro.md) lemezkép érhető el, a legtöbb ugyanazokat az eszközöket, mint az Ubuntu-rendszerkép. A [Windows](provision-vm.md) lemezkép érhető el is.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt létrehozna egy adatelemző virtuális gép Linux, Azure-előfizetéssel kell rendelkeznie. Szerezzen be egyet, tekintse meg [lekérése az Azure ingyenes próbaverzió](https://azure.microsoft.com/free/).

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Az adatelemző virtuális gép létrehozása Linux rendszeren

Az alábbiakban a Linux-példány, az adatelemző virtuális gép létrehozásához szükséges lépéseket:

1. Keresse meg a virtuális gépet, az ajánlati a [az Azure portal](https://portal.azure.com/#create/microsoft-dsvm.linux-data-science-vm-ubuntulinuxdsvmubuntu). Előfordulhat, hogy a rendszer felszólítja, hogy jelentkezzen be az Azure-fiókjába, ha még nincs bejelentkezve. 
1. Kattintson a **létrehozás** (a lap alján) viszi, megjelenik a varázsló.![ Konfigurálja-data-adatelemzési – a virtuális gép](./media/dsvm-ubuntu-intro/configure-data-science-virtual-machine.png)
1. A következő szakaszok a bemenet minden (a jobb oldalán az előző ábrán felsorolt) a varázsló lépéseit, a Microsoft Data Science virtuális gép létrehozásához használt. Az alábbiakban a bemeneti adatok konfigurálása az egyes lépéseket:

   a. **Alapvető beállítások**:

   * **Név**: A létrehozandó adatelemzési kiszolgáló neve.
   * **VM-lemez típusa**: Ha SSD-meghajtót szeretne, válassza a **prémium SSD** lehetőséget. Ellenkező esetben válasszon **Standard HDD**. 
   * **Felhasználónév**: Az első fiók bejelentkezési azonosítója.
   * **Jelszó**: Első fiók jelszava (a jelszó helyett nyilvános SSH-kulcsot használhat).
   * **Előfizetés**: Ha egynél több előfizetéssel rendelkezik, válassza ki az egyik, amelyen a gép létrehozása és a számlázás. Ehhez az előfizetéshez erőforrás-létrehozási jogosultságokkal kell rendelkeznie.
   * **Erőforráscsoport**: Létrehozhat egy új vagy egy meglévő csoportot.
   * **Hely**: Válassza ki a legmegfelelőbb adatközpontot. Általában az adatközpont, amely tartalmazza a legtöbb az adatokat, vagy a leggyorsabb hálózati hozzáféréshez a fizikai helyéhez legközelebbi.

   b. **Méret**:

   * Válassza ki a kiszolgáló típusát, amely megfelel a funkcionális és a költségek megkötések. A GPU-alapú virtuálisgép-példányok esetében válasszon ki egy NC vagy ND-osztályú virtuális gépet. A [elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) lap felsorolja a régiók gpu-k használatával.

   c. **Beállítások**:

   * A legtöbb esetben csak az alapértelmezett értékeket is használhat. Érdemes figyelembe venni, nem az alapértelmezett értékeket, a kurzort az egyes mezőkkel tájékoztató mutató hivatkozás.

   d. **Összefoglalás**:

   * Győződjön meg arról, hogy minden, a megadott adatok helyesek. Egy hivatkozást a használati feltételeket. A virtuális gép nem rendelkezik a kiválasztott kiszolgáló méretét a számítási túl további díjakat a **mérete** . lépés. A kiépítés indításához kattintson **létrehozás**. 

A kiépítés körülbelül 5 percet vesz igénybe. A kiépítési állapota jelenik meg az Azure Portalon.

## <a name="how-to-access-the-data-science-virtual-machine-for-linux"></a>Az adatelemző virtuális gép elérése Linux rendszeren

Az Ubuntu DSVM három módszerrel érhető el:

1. SSH-munkamenetet a Terminálszolgáltatások
1. Grafikus munkamenetek X2Go
1. JupyterHub és JupyterLab Jupyter notebookokhoz

Data Science VM is csatolhat, hogy Azure Notebooks Jupyter jegyzetfüzeteket futtasson a virtuális gépen, és megkerüli az ingyenes szolgáltatási szintet. További információ: jegyzetfüzetek [projektjeinek kezelése és konfigurálása – számítási réteg](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

### <a name="ssh"></a>SSH

Ha a virtuális gép létrejött, bejelentkezhet hozzá SSH használatával. A fiók hitelesítő adataival, amelyet a **alapjai** szakasz 3. lépésben a szöveg shell felületén. Windows rendszeren letölthet egy SSH-ügyféleszközt (például a [Puttyt](https://www.putty.org)). Ha inkább egy grafikus desktop (X Windows rendszer), használhatja a Putty-továbbítás X11 vagy X2Go ügyfél telepítése.

> [!NOTE]
> A X2Go ügyfél jobban teljesített, mint továbbítási tesztelés X11. Azt javasoljuk, hogy az asztali grafikus felület X2Go ügyfél használatával.

### <a name="x2go"></a>X2Go

A Linux rendszerű virtuális gép már kiépített X2Go kiszolgálóval, és készen áll kapcsolatok fogadására. Ha csatlakozni szeretne a Linuxos virtuális gép grafikus asztali, az alábbi eljárással az ügyfélen:

1. Töltse le és telepítse a saját ügyfélplatformjára X2Go ügyfél [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Futtassa a X2Go ügyfél, és válassza a **új munkamenet**. Több lap egy konfigurációs ablaka nyílik. Adja meg az alábbi konfigurációs paramétereket:
   * **Munkamenet lapon**:
     * **Gazdagép**: A linuxos Data Science VM állomásneve vagy IP-címe.
     * **Bejelentkezés**: A Linux rendszerű virtuális gépen a Felhasználónév.
     * **SSH-port**: Hagyja meg az alapértelmezett értéket 22-én.
     * **Munkamenet típusa**: Módosítsa az értéket az XFCE értékre. A Linux rendszerű virtuális gép jelenleg csak XFCE desktop támogatja.
   * **Média lap**: Ha nem szükséges, ki is kapcsolhatja a hangtámogatást és az ügyfél-nyomtatást.
   * **Megosztott mappák**: Ha a Linux rendszerű virtuális gépen csatlakoztatni kívánja a címtárakat az ügyfélgépekről, adja hozzá azokat az ügyfélszámítógép-könyvtárakat, amelyeket meg szeretne osztani a virtuális géppel ezen a lapon.

Bejelentkezés után a virtuális géphez SSH-ügyfél vagy a grafikus asztali XFCE X2Go ügyfélszámítógépen keresztül, készen áll az eszközöket, amelyek telepítése és konfigurálása történik meg a virtuális gép használatának megkezdéséhez. A XFCE látható alkalmazások parancsikonjai és asztali ikonok a számos olyan eszközzel.

### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub és JupyterLab

Az Ubuntu DSVM fut [JupyterHub](https://github.com/jupyterhub/jupyterhub), többfelhasználós Jupyter kiszolgáló. A kapcsolódáshoz keresse meg a https\/:/Your-VM-IP: 8000-et a laptopon vagy az asztalon, adja meg a virtuális gép létrehozásához használt felhasználónevet és jelszót, majd jelentkezzen be. Számos mintafüzetek érhetők el, hogy a Tallózás gombra, és próbálja ki.

JupyterLab, a Jupyter-notebookok és JupyterHub, következő generációja érhető el. A hozzáféréshez jelentkezzen be a JupyterHub, majd keresse meg a https:\//Your-VM-IP: 8000/User/your-username/Lab URL-címet. A JupyterLab alapértelmezett jegyzetfüzet-kiszolgálóként is beállíthatja, ha hozzáadja ezt a sort a */etc/jupyterhub/jupyterhub_config.py*:

```python
c.Spawner.default_url = '/lab'
```

## <a name="tools-installed-on-the-data-science-virtual-machine-for-linux"></a>A Data Science virtuális gépen telepített Linux-eszközök

### <a name="deep-learning-libraries"></a>Deep Learning-kódtárak

#### <a name="cntk"></a>CNTK

A Microsoft Cognitive Toolkit deep learning-eszközkészlet egy, nyílt forráskódú. Python-kötések a legfelső szintű és py35 Conda-környezetekben érhető el. Az elérési út már szerepel a parancssori eszközzel (cntk) is rendelkezik.

Python mintafüzetek JupyterHub érhető el. Ha alapszintű mintát szeretne futtatni a parancssorban, hajtsa végre a következő parancsokat a rendszerhéjban:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

További információkért lásd: a CNTK szakaszában [GitHub](https://github.com/Microsoft/CNTK), és a [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).

#### <a name="caffe"></a>Caffe

Caffe egy olyan deep learning keretrendszer, Berkeley Látástechnológiai és oktatóközpont. /Opt/caffe érhető el. Példák /opt/caffe/examples található.

#### <a name="caffe2"></a>Caffe2

Caffe2 egy olyan deep learning keretrendszer, a Facebook, a Cafféhoz épül. Érhető el a Python 2.7-t a Conda legfelső szintű környezetben. Az aktiválásához futtassa a következő parancsot a rendszerhéjból:

```bash
source /anaconda/bin/activate root
```

Néhány példa notebookok JupyterHub érhető el.

#### <a name="h2o"></a>H2O

H2O egy gyors, a memóriában, elosztott gépi tanulással és prediktív elemzési platform. Egy Python-csomag telepítve van a gyökér- és a py35 Anaconda-környezetekben. Egy R-csomag is telepítve van. A H2O indításához futtassa `java -jar /dsvm/tools/h2o/current/h2o.jar`; vannak a különböző [parancssori kapcsolók](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) , előfordulhat, hogy szeretne konfigurálni. A Flow webes felhasználói felületen navigáljon az elérhető http://localhost:54321 a kezdéshez. JupyterHub mintafüzetek is érhetők el.

#### <a name="keras"></a>Keras

A kerasz egy magas szintű neurális hálózati API a Pythonban, amely képes a TensorFlow, a Microsoft Cognitive Toolkit vagy a theano-on való futtatásra. A legfelső szintű és py35 Python-környezetekben érhető el.

#### <a name="mxnet"></a>MXNet

MXNet a teljesítmény és a rugalmasság a deep learning-keretrendszert. A dsvm-hez a tartalmazzák R és Python-kötések rendelkezik. Mintafüzetek JupyterHub szerepelnek, és a mintakódot /dsvm/samples/mxnet érhető el.

#### <a name="nvidia-digits"></a>NVIDIA SZÁMJEGY

Az NVIDIA Deep Learning GPU képzési rendszert, számjegyek, más néven egy rendszer gyakori deep learning feladatokat, mint a adatait, tervezési és képzési Neurális hálózatokkal a GPU-rendszerek kezelése és figyelése a fejlett Vizualizáció valós idejű teljesítmény leegyszerűsítése érdekében.

SZÁMJEGYEK számjegyek nevű szolgáltatásként érhető el. Indítsa el a szolgáltatást, és keresse meg a http://localhost:5000 a kezdéshez.

SZÁMJEGYEK is telepítve van a Conda legfelső szintű környezetben Python modulként.

#### <a name="tensorflow"></a>TensorFlow

Tensorflow-hoz a Google deep learning-kódtár. Ez egy nyílt forráskódú, az adatfolyam-diagramok használatával numerikus számításra szolgáló könyvtár. Tensorflow-hoz elérhető py35 Python-környezetet, és néhány mintafüzetek JupyterHub szerepelnek.

#### <a name="theano"></a>Theano

Theano egy Python-kódtár, hatékony numerikus törölje a számításhoz. A legfelső szintű és py35 Python-környezetekben érhető el. 

#### <a name="torch"></a>Torch

Torch egy olyan tudományos számítási keretrendszer gépi tanulási algoritmusok széles körű támogatásával. /Dsvm/tools/torch érhető el, és az interaktív munkamenet th és luarocks Csomagkezelő érhetők el a parancssorból. Példák /dsvm/samples/torch érhető el.

A legfelső szintű Anaconda környezetben PyTorch is érhető el. Példák /dsvm/samples/pytorch találhatók.

### <a name="microsoft-r-server"></a>Microsoft R Server

Az R a legnépszerűbb nyelvek adatok elemzési és gépi tanulás. Ha szeretné az R használata a elemzéshez, a virtuális gép rendelkezik a Microsoft R Server (Asszony) a Microsoft R Open (MRO) és matematikai Kernel könyvtár (MKL). A MKL optimalizálja a matematikai műveletek gyakori az elemzési algoritmusra. MRO 100 %-os CRAN-r-kompatibilis, és az R-kódtárak a cran-en közzétett bármelyikét a MRO is telepíthető. Asszony biztosítja skálázás és az R-modellek operacionalizálás web services szolgáltatásba. A R-programokat egy, az az alapértelmezett szerkesztők, mint az RStudio, a vi vagy a Emacs szerkesztheti. Ha inkább a Emacs-szerkesztő használatával, előre telepítve lett. A Emacs csomag REJTÉSE (Emacs beszél statisztika) leegyszerűsíti az R-fájlok a Emacs szerkesztő belül.

Az R modul indításához konzolon, egyszerűen írja be **R** a rendszerhéj. Ez a parancs interaktív környezetbe kerül. Az R-program fejlesztéséhez, általában egy szerkesztővel például Emacs vagy vi, és futtassa az r parancsfájlok Az RStudio hogy egy teljes grafikus IDE környezet az R-programot fejlesszen.

Emellett van egy R-szkriptet, hogy telepítse a [felső 20 R-csomagok](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) Ha azt szeretné. Ez a szkript futtatása után lehetséges a R interaktív felület, amely lehet megadni (ahogy említettük) írja be a **R** a rendszerhéj.  

### <a name="python"></a>Python

Anaconda Python a Python 2.7-es és 3.5-ös környezetben van telepítve. A 2.7-es környezetben nevezzük _legfelső szintű_, és a 3.5-ös környezetben nevezzük _py35_. Ehhez a terjesztéshez körülbelül 300 a legnépszerűbb matematikai, a mérnöki csapathoz és az analitikai csomagok, valamint az alap Python tartalmazza.

A py35 környezetben az alapértelmezett érték. A legfelső szintű (2.7-es) környezet aktiválása:

```bash
source activate root
```

A py35 környezet újra aktiválása:

```bash
source activate py35
```

A Python interaktív munkamenet elindításához, csak gépelje **python** a rendszerhéj. 

Telepítse a használatával további Python-kódtárakat ```conda``` vagy ```pip``` . A pip aktiválja a megfelelő környezetben először Ha nem szeretné, hogy az alapértelmezett:

```bash
source activate root
pip install <package>
```

Vagy adja meg a pip teljes elérési útja:

```bash
/anaconda/bin/pip install <package>
```

Conda, mindig a környezet nevét kell megadnia (_py35_ vagy _legfelső szintű_):

```bash
conda install <package> -n py35
```

Ha, vagy a grafikus felület set-továbbítás be X11, beírhatja **pycharm** elindíthatja a PyCharm Python IDE. Az alapértelmezett szövegszerkesztő is használhatja. Emellett használhatja Spyder, a Python ide-vel, amely Anaconda Python disztribúció részét képezi. Spyder kell egy grafikus asztali vagy X11-továbbítást. A grafikus desktopban Spyder parancsikon biztosítunk.

### <a name="jupyter-notebook"></a>Jupyter Notebook

A Anaconda terjesztési egy Jupyter notebookot, megosztani a kódot és az elemzési környezet is tartalmaz. A Jupyter notebook JupyterHub keresztül érhető el. Jelentkezzen be a helyi Linux-felhasználónév és jelszó használatával.

A Jupyter notebook server Python 2, a Python 3 és az R kernelekkel előre van konfigurálva. Nincs "Jupyter Notebook" Indítsa el a böngészőt a jegyzetfüzet-kiszolgálóhoz való hozzáféréshez nevű asztali ikon. Ha a virtuális gép SSH- vagy X2Go ügyfél, akkor is ellátogathat [ https://localhost:8000/ ](https://localhost:8000/) a Jupyter notebook kiszolgálóhoz való hozzáféréshez.

> [!NOTE]
> Folytassa, ha bármely tanúsítvánnyal kapcsolatos figyelmeztetéseket kap.

A Jupyter notebook server bármely állomásról is elérheti. Egyszerűen adja meg *https://\<virtuális gép DNS-nevét vagy IP-cím\>: 8000 /*

> [!NOTE]
> 8000-es port nyílik meg a tűzfal alapértelmezés szerint amikor a virtuális gép ki van építve. 

Azt van csomagolva mintafüzetek – egy, a Python és a egy R. Láthatja a minták mutató hivatkozást a jegyzetfüzet kezdőlap, után a helyi Linux-felhasználónév és jelszó használatával hitelesítést a Jupyter notebookot. Létrehozhat egy új jegyzetfüzetet kiválasztásával **új**, és ezután a megfelelő nyelvi kernel. Ha nem látja a **új** gombra, majd a **Jupyter** ikonra a bal felső sarokban, nyissa meg a notebook server kezdőlapja.

### <a name="apache-spark-standalone"></a>Az Apache Spark-önálló

Az Apache Spark egy önálló példányát a Linuxos DSVM segítenek a Spark-alkalmazások helyi először tesztelése és nagy méretű fürtökön üzembe helyezése előtt van előtelepítve. A Jupyter kernel keresztül PySpark programok futtathatók. Amikor megnyitja a Jupyter, kattintson a **új** gombra kattintva kell notebookokhoz elérhető kernelek listájának megtekintéséhez. A "Spark – Python", amely lehetővé teszi a Python nyelv használatával alkalmazásokat hozhat létre a Spark a PySpark kernelt. A Spark-programot a Python IDE, például a Notebookshoz vagy a Spyder használatával is létrehozhatja. Ebben az önálló példányban a Spark stack a hívó ügyfélprogramon belül fut, ami gyorsabbá és egyszerűbbé teszi a Spark-fürtön való fejlesztéshez képest felmerülő problémák megoldását.

A Jupyter, amely a "Könnyen használható" könyvtár kezdőkönyvtárának Jupyter ($HOME/notebookok és könnyen használható/pySpark) alatt található minta notebook PySpark biztosítunk. 

Ha az R for Spark szolgáltatásban végez programozást, használhatja a Microsoft R Server, a Sparker vagy a sparklyr. 

Mielőtt futtatná a Microsoft R Server Spark-környezetben, kell tennie a telepítő lépést ahhoz, hogy a helyi Hadoop HDFS és Yarn-példány egyetlen csomópont csak egyszer. Alapértelmezés szerint Hadoop-szolgáltatásokhoz vannak telepítve, de a dsvm-hez a letiltva. Az engedélyezéshez, először futtassa a következő parancsokat rendszergazdaként kell:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Ha nincs szüksége rájuk, leállíthatja a Hadoop kapcsolódó szolgáltatásait```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```

Egy minta, amely bemutatja, hogyan fejlesztheti és tesztelheti a MRS-t a távoli Spark-környezetben (amely a DSVM önálló Spark-példánya), és elérhető a */dsvm/Samples/Mrs* könyvtárban.

### <a name="ides-and-editors"></a>Ide-ket és a szerkesztőknek

Számos kód-szerkesztő közül választhat, beleértve a következőket: VI/VIM, Emacs, Notebookshoz, RStudio és IntelliJ. Intellij-vel, az RStudio és a PyCharm grafikus szerkesztők, és meg kell adnia használhatja őket a grafikus asztali bejelentkezett. Ezek a szerkesztők rendelkezik, asztali és az alkalmazás menüből parancsikonokat indíthassák el azokat.

**VIM** és **Emacs** szöveges szerkesztők vannak. Emacs, a-bővítménycsomagot Emacs beszél statisztika (cím), amely megkönnyíti az R használata belül a Emacs szerkesztő nevű telepítette azt. További információ található [REJTÉSE](https://ess.r-project.org/).

**LaTex** a texlive package és a egy Emacs bővítmény keresztül van telepítve, [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) csomagot, amely egyszerűbbé teszi a szerzői műveletek a Emacs LaTex dokumentumokon.  

### <a name="databases"></a>Adatbázisok

#### <a name="graphical-sql-client"></a>Grafikus SQL-ügyfél

**SQuirrel SQL**, grafikus SQL-ügyfelet, megadva (például Microsoft SQL Server, és MySQL) különböző adatbázisaihoz való kapcsolódásra és az SQL-lekérdezések futtatásához. A mókus SQL-t futtathatja grafikus asztali munkamenetből (például a X2Go-ügyféllel) egy asztali ikon használatával vagy a következő parancs használatával a rendszerhéjban:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Az első használata előtt az illesztőprogramok és adatbázis-aliasok beállítása. A JDBC-illesztőprogramok a következő helyen találhatók:

*/usr/share/Java/jdbcdrivers*

További információkért lásd: [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>A Microsoft SQL Server elérése szolgáló parancssori eszközök

Az ODBC illesztőprogram-csomagot az SQL Server is két parancssori eszközöket is tartalmaz:

**bcp**: A BCP-segédprogram tömegesen másol egy Microsoft SQL Server egy példánya és egy, a felhasználó által megadott formátumban tárolt adatfájl közötti Adatmásolást. A bcp segédprogram használható, nagy mennyiségű új sort importálni az SQL Server-táblákra vagy exportálni adatokat táblák tábláiból adatfájlokba. Adatok importálása egy táblába, kell használni, hogy a táblázat számára létrehozott formátumfájlt, vagy megismerheti a struktúrát a tábla-és adattípusokat és az oszlopok esetében érvényes.

További információkért lásd: [bcp-vel csatlakozás](https://msdn.microsoft.com/library/hh568446.aspx).

**sqlcmd**: Megadhatja a Transact-SQL-utasításokat a Sqlcmd segédprogrammal, valamint a rendszerfolyamatokat és a parancsfájlokat a parancssorban. Ez a segédprogram ODBC segítségével hajtsa végre a Transact-SQL-kötegek.

További információkért lásd: [az Sqlcmd-t kapcsolódás](https://msdn.microsoft.com/library/hh568447.aspx).

> [!NOTE]
> Nincsenek ezzel az eszközzel a Linux és Windows platformok közötti különbségeket. A részletek dokumentációjában talál.

#### <a name="database-access-libraries"></a>Adatbázis-hozzáférés kódtárak

Nincsenek elérhető, az R és Python az access-adatbázisok könyvtárak.

* Az R a **RODBC** csomag vagy **dplyr** csomag lehetővé teszi kérdezheti le vagy SQL-utasítások végrehajtása a kiszolgálón.
* A Python a **pyodbc** a könyvtárban olyan adatbázis-hozzáférés az alapul szolgáló rétegként ODBC.  

### <a name="azure-tools"></a>Azure-eszközök

A következő Azure-eszközök telepítve vannak a virtuális gépen:

* **Azure parancssori felület**: Az Azure CLI lehetővé teszi az Azure-erőforrások rendszerhéj-parancsokkal történő létrehozását és kezelését. Az Azure-eszközök meghívni, csak gépelje **azure súgó**. További információkért lásd: a [Azure CLI dokumentációjában](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Microsoft Azure Storage Explorer**: A Microsoft Azure Storage Explorer egy grafikus eszköz, amely az Azure Storage-fiókban tárolt objektumok tallózására, valamint az Azure-Blobok adatainak feltöltésére és letöltésére szolgál. Storage Explorer asztali parancsikonjára ikonra kattintva érheti el. Hívhatja meg azt a rendszerhéj parancssorából beírásával **StorageExplorer**. Be kell jelentkeznie egy X2Go-ügyfélről, vagy be kell állítania az X11-továbbítást.
* **Azure**-kódtárak: A következőkben néhány előre telepített függvénytár található.
  
  * **Python**: Az Azure-hoz kapcsolódó Python-kódtárak az **Azure**, a **azureml**, a **pydocumentdb**és a **pyodbc**. Az első három kódtárakat elérheti az Azure storage services, Azure Machine Learning és az Azure Cosmos DB (NoSQL-adatbázis az Azure-ban). A negyedik kódtára, pyodbc (valamint a Microsoft ODBC-illesztőprogram SQL Serverhez), lehetővé teszi a hozzáférést az SQL Server, az Azure SQL Database és az Azure SQL Data Warehouse a Python egy ODBC-felület használatával. Adja meg **pip lista** megjelenítéséhez a listában szereplő könyvtárak. Győződjön meg arról, a parancs futtatása a Python 2.7-es és 3.5-ös környezetben.
  * **R**: A telepített R-beli Azure-beli kódtárak a **AzureML** és a **RODBC**.
  * **Java**: Az Azure Java-kódtárak listája a virtuális gép címtárának **/dsvm/SDK/AzureSDKJava** található. A kulcs függvénytárak is az Azure storage és a felügyeleti API-k, az Azure Cosmos DB és a JDBC illesztőprogramok az SQL Server.  

Elérheti a [az Azure portal](https://portal.azure.com) az előre telepített Firefox böngészőben. Az Azure Portalon létrehozása, kezelése és monitorozása az Azure-erőforrások.

### <a name="azure-machine-learning"></a>Azure Machine Learning

Az Azure Machine Learning egy teljes körűen felügyelt felhőszolgáltatás, amely lehetővé teszi, hogy elkészítheti, telepítheti és oszthat meg prediktív elemzési megoldásokat. Az Azure Machine Learning Studio-ből hoz létre a kísérletek és a modellek. Azt is lehet megnyitásával az adatelemző virtuális gép egy webböngészőből [Microsoft Azure Machine Learning](https://studio.azureml.net).

Azure Machine Learning Studióban való bejelentkezés után hozzáférhet egy kísérleti vászonra ahol hozhat létre egy gépi tanulási algoritmusok logikai folyamatot. Is rendelkezik hozzáféréssel a Jupyter notebook az Azure Machine Learning szolgáltatásban üzemeltetett, és zavartalanul tud működni a kísérletek a Machine Learning Studióban. A machine learning-modellek, amelyek azokat a web service-felülethez alkalmazásburkoló által létrehozott működésbe. A végrehajtott Machine learning-modellek lehetővé teszik bármely nyelven írt ügyfelek számára az előrejelzések meghívását ezekből a modellből. További információkért lásd: a [Machine Learning dokumentációja](https://azure.microsoft.com/documentation/services/machine-learning/).

A modellek R vagy Python nyelven is épülnek a virtuális Gépet, és majd üzembe helyezzük azt Azure Machine Learning szolgáltatásban éles környezetben. Szalagtárak rendelkezik telepítette a R (**AzureML**) és a Python használatával (**azureml**) Ez a funkció engedélyezéséhez.

Hogyan helyezheti üzembe az Azure Machine Learning modellek R és Python információkért lásd: [tíz dolog, amire alkalmas az adatelemző virtuális gép](vm-do-ten-things.md) (különösen a szakasz "a modellek R vagy Python használatával hozhat létre, és felhasználhatja őket használatával Az Azure Machine Learning").

> [!NOTE]
> Ezek az utasítások az adatelemző virtuális gép Windows verziója esetén használhatóak. De az Azure Machine Learning modellek üzembe helyezéséhez nincs alkalmazható a Linux rendszerű virtuális géphez megadott információk.

### <a name="machine-learning-tools"></a>Machine learning eszközök

A virtuális gép néhány machine learning-eszközökkel és előre lefordított, és előre telepített helyi algoritmusokat tartalmaz. Ezek a következők:

* **Vowpal Wabbit**: Egy gyors online tanulási algoritmus.
* **xgboost**: Olyan eszköz, amely optimalizált, növelt fa algoritmusokat biztosít.
* **Csörgő**: Egy R-alapú grafikus eszköz, amely megkönnyíti az adatfeltárást és a modellezést.
* **Python**: A anaconda Python a gépi tanulási algoritmusokkal, például a Scikit-Learn csomaggal rendelkezik. Könyvtárak használatával telepítheti a `pip install` parancsot.
* **LightGBM**: Gyors, elosztott, nagy teljesítményű színátmenet-növelő keretrendszer döntési fa algoritmusok alapján.
* **R**: Az R-hez a Machine learning-függvények gazdag könyvtára érhető el. Az előre telepített tárak némelyike az LM, a GLM, a randomForest, a rpart. Könyvtárak futtatásával telepíthető:
  
        install.packages(<lib name>)

Íme néhány a lista első három machine learning-eszközökkel kapcsolatos további információkat.

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit egy gépi tanulási rendszer által használt módszerek például online, kivonatoló, allreduce, csökkentésének, learning2search, aktív, és az interaktív tanulás.

Az eszköz alapszintű példán való futtatásához használja a következő parancsokat:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Nincsenek más, nagyobb bemutatók ebben a könyvtárban. VW további információkért lásd: [ebben a szakaszban a GitHub](https://github.com/JohnLangford/vowpal_wabbit), és a [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>xgboost

Ez a kódtár, amely lett kialakítva és optimalizálva gyorsított (fa) algoritmusokat. Ebben a könyvtárban célja a gépek a számítási korlátokat leküldése a szélsőséges szükség ahhoz, hogy nagyméretű fa kiemelése, amely méretezhető, hordozható és pontos van.

A parancssorból, valamint egy R-kódtár, nyújtja.

Ebben a könyvtárban, az R használandó megkezdése egy interaktív relace jazyka R (csak beírásával **R** a rendszerhéj), és a könyvtár betöltéséhez.

Íme egy egyszerű példa az R-parancssorban futtathatja:

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

A xgboost parancssor futtatásához, az alábbiakban a hajtsa végre a rendszerhéj-parancsok:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

A megadott könyvtár .model fájl írása. Ez a bemutató példa kapcsolatos információk találhatók [a Githubon](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Xgboost kapcsolatos további információkért lásd: a [xgboost dokumentációs oldal](https://xgboost.readthedocs.org/en/latest/), és a hozzá tartozó [GitHub-adattár](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Rattle

Rattle (a **R** **A**analitikus **T**öz **T**o **L**megszerzésére **E** asily) használ a GUI-alapú adatáttekintés és modellezés. Jelenítse meg az adatokat, azonnal modellezhető, felügyeletlen és a felügyelt modellek adatokból hoz létre, a modellek teljesítményének grafikusan, megadja átalakítások adatok statisztikai és vizuális összegzéseket, és beállítja a pontszámok új adatokat. R-kód, a műveletek a felhasználói felület, amely közvetlenül az R futtatható, illetve kiindulási pontként Microsoft további elemzésnek veti replikálása is állít elő.

Rattle futtatásához, meg kell lenniük egy grafikus asztali bejelentkezési munkamenet. A terminalba írja be a ```R``` , adja meg az R-környezetben. Az R a parancssorba írja be a következő parancsokat:

```R
library(rattle)
rattle()
```

Most már egy grafikus felület nyit meg lapok vannak beállítva. Itt láthatók a Csörgőben a minta időjárási adathalmazok használatához és a modell létrehozásához szükséges gyors útmutató lépései. Az alábbi lépések némelyike a kéri automatikusan telepíti és betölti az egyes szükséges R-csomagokat, amelyek még nincsenek a rendszeren.

> [!NOTE]
> Nincs hozzáférése a csomag telepítéséhez a rendszer címtárban (az alapértelmezett beállítás), a parancssorba az R-konzol ablakban csomagok telepítéséhez a saját személyes tárába jelenhet meg. Válasz *y* ha látja ezeket az utasításokat.

1. Kattintson az **Execute** (Végrehajtás) parancsra.
1. Egy párbeszédpanel jelenik meg, amely felkéri, hogy használja az időjárási példa adatkészlet e. Kattintson a **Igen** betölteni a példa.
1. Kattintson a **modell** fülre.
1. Kattintson a **Execute** döntési fa hozhat létre.
1. Kattintson a **rajzolása** a döntési fa megjelenítéséhez.
1. Kattintson a **erdő** választógombot, majd kattintson az **Execute** hozhat létre egy véletlenszerű erdőben.
1. Kattintson a **Evaluate** fülre.
1. Kattintson a **kockázati** választógombot, majd kattintson az **Execute** két kockázati (eloszlásfv) teljesítmény grafikon megjelenítése.
1. Kattintson a **Log** fülre, és az a fenti műveletek létrehozása az R-kód megjelenítése.
   (A jelenlegi kiadásban, Rattle egy hiba miatt kell beszúrnia egy *#* karakter elé *... Ez a napló exportálása*  a napló a szövegben.)
1. Kattintson a **exportálása** gombra kattintva mentse a R-parancsfájl tárolásához nevű *weather_script. R* az otthoni mappába.

A kilépéshez Rattle és az r segítségével Mostantól módosíthatja a létrehozott R-szkriptet, vagy futtatása bármikor mindent, ami a Rattle felhasználói felületén belül végzett ismétlődő módon használhatja. Különösen az R kezdőknek, ez az könnyedén gyors elemzéseket végezhet, és a gépi tanulás egy egyszerű grafikus felületen során automatikusan R módosítása és/vagy további kódot generál.

## <a name="next-steps"></a>További lépések

Itt látható, hogyan a tanulási és feltárásra továbbra is:

* A [a az adatelemző virtuális gép Linux Data science](linux-dsvm-walkthrough.md) a bemutató ismerteti, hogyan hajthat végre számos gyakori adatelemzési feladatok és a Linux rendszerű adatelemző virtuális gép kiépítése itt. 
* Próbálja ki az ebben a cikkben leírt eszközök vizsgálja meg az adatelemző virtuális gép a különféle beépített adatelemzési eszközzel. Is futtathat *dsvm-információ – info* a a rendszerhéj belül a virtuális gép alapszintű bevezetést és további információ a virtuális gépen telepített eszközök mutatókat tartalmaznak.  
* Megtudhatja, hogyan teljes körű elemzési megoldásokat rendszeresen használatával hozhat létre a [csoportos adatelemzési folyamat](https://aka.ms/tdsp).
* Látogasson el a [Azure AI-katalógusban](https://gallery.azure.ai/) machine learning és a data analytics minták, amelyek használják az Azure AI-szolgáltatások.
