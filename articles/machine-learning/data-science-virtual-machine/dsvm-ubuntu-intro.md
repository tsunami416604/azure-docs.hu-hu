---
title: "Rendszerű Linux (Ubuntu) adatok tudományos virtuális gép az Azure-on |} Microsoft Docs"
description: "Konfigurálja, és hozzon létre egy adatok tudományos virtuális gép a Linux (Ubuntu) Azure elemzés és gépi tanulás."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 3bab0ab9-3ea5-41a6-a62a-8c44fdbae43b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: bradsev
ms.openlocfilehash: 721b18845a3b839d59c7eb0a04646635fa8d9fe7
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="provision-the-data-science-virtual-machine-for-linux-ubuntu"></a>Az adatok tudományos virtuális gép kiépítése Linux (Ubuntu)

A Linux adatok tudományos virtuális gépen egy Ubuntu-alapú virtuálisgép-lemezkép, amellyel könnyedén mély learning Azure használatába. A részletes tanulási eszközök a következők:

  * [Caffe](http://caffe.berkeleyvision.org/): sebességét, expressivity és modularitás épített mély tanulási keretrendszer
  * [Caffe2](https://github.com/caffe2/caffe2): egy Caffe platformfüggetlen verziója
  * [Microsoft kognitív eszközkészlet](https://github.com/Microsoft/CNTK): A Microsoft Research software eszközkészletet tanulási mély
  * [H2O](https://www.h2o.ai/): egy nyílt forráskódú big Data típusú adatok platform és a grafikus felhasználói felületen
  * [Keras](https://keras.io/): egy magas szintű Neurális hálózat API Theano és TensorFlow pythonban
  * [MXNet](http://mxnet.io/): egy rugalmas, hatékony mély tanulási függvénytár, amely sok nyelvi kötések
  * [NVIDIA számjegyek](https://developer.nvidia.com/digits): egy grafikus rendszer egyszerűsíti a részletes tanulási gyakori feladatok
  * [TensorFlow](https://www.tensorflow.org/): egy nyílt forráskódú könyvtár számára a Google gép eszközintelligencia
  * [Theano](http://deeplearning.net/software/theano/): A Python kódtár meghatározása, optimalizálásához, és hatékonyan a többdimenziós tömböket tartalmazó matematikai kifejezések kiértékelése
  * [Torch](http://torch.ch/): gépi tanulási algoritmusok széles támogatása tudományos számítógépes keretrendszer
  * CUDA cuDNN és a NVIDIA illesztőprogram
  * Sok minta Jupyter notebookok

Összes olyan szalagtár szerepel a e GPU, abban az esetben, ha a Processzor terhelését is futnak.

Az adatok tudományos virtuális gép Linux is tartalmazza az adatok tudományos és fejlesztési tevékenységeket, beleértve a népszerű eszközöket:

* Microsoft R Server Developer kiadásában Microsoft R meg van nyitva
* Anaconda Python elosztási (2.7 és 3.5-ös verzióját. verzió), beleértve a népszerű adatok elemzőkönyvtárak
* JuliaPro - Ágnes nyelvi népszerű tudományos és az adatok analytics könyvtárakkal válogatott megoszlása
* Önálló Spark-példányban és egyetlen csomópont Hadoop (HDFS, Yarn)
* JupyterHub - R, Python, PySpark, Ágnes kernelek támogató többfelhasználós Jupyter notebook kiszolgáló
* Azure Storage Explorer
* Azure parancssori felület (CLI) Azure-erőforrások kezelése
* Machine learning-eszközök
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): gyors machine learning-rendszer támogatása, például a online, a kivonatoló, allreduce, csökkentése, learning2search, aktív, és interaktív tanulás
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): egy gyors és pontos súlyozott fa megvalósítási biztosító eszköz
  * [Rattle](http://rattle.togaware.com/): egy grafikus eszközt, amelynek eredményeképpen a adatelemzés és gépi tanulási R könnyen az első lépések
  * [LightGBM](https://github.com/Microsoft/LightGBM): egy gyors, elosztott, nagy teljesítményű színátmenetes keretrendszer kiemelése
* Az Azure SDK, Java, Python, node.js, a Ruby, a PHP
* Az R és Python a szalagtárak használja az Azure Machine Learning és más Azure-szolgáltatásokkal
* Fejlesztői eszközök és szerkesztők (Rstudióból, PyCharm, IntelliJ, Emacs, vim)


Adattudomány Ez magában foglalja a feladatok sorozata léptetés:

1. Keresése, betöltése, és előzetesen feldolgozni az adatokat
2. Összeállításakor és tesztelésekor modellek
3. A modelleket a fogyasztás intelligens alkalmazások telepítése

Adatszakértőkön különböző eszközök használatával ezeket a feladatokat. Ez elég időigényes lehet megtalálni a szoftver megfelelő verzióját, és töltse le, majd fordítsa le a, és ezen verziói telepítése.

Az adatok tudományos virtuális gép Linux jelentősen ezt a terhet megkönnyítése érdekében. Ezzel a analytics projekt jump-start. Ez lehetővé teszi, hogy működik a különböző nyelveken, beleértve az R, Python, SQL, Java és C++ feladatok. Az Azure SDK tartalmazza a virtuális gép segítségével különböző szolgáltatások Linux rendszeren a Microsoft cloud platform az alkalmazások létrehozását teszi lehetővé. Emellett más nyelvekre például Ruby, Perl, PHP vagy node.js előre telepített is elérhető lesz.

Nincsenek az adatok tudományos VM-lemezkép szoftver költségek. Csak az Azure hardver használati díjak kiépítése virtuális gép mérete alapján értékelni kell fizetnie. A számítási díjakat további információkat itt talál a [VM listaelem oldalon, az Azure piactéren](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Az adatok tudományos virtuális gép egyéb verziói
A [CentOS](linux-dsvm-intro.md) lemezkép érhető el, az eszközök az Ubuntu képként számos. A [Windows](provision-vm.md) kép érhető el is.

## <a name="prerequisites"></a>Előfeltételek
Linux tudományos adatok virtuális gép létrehozásához, Azure-előfizetéssel kell rendelkeznie. Egy beszerzéséről [beolvasása az Azure ingyenes próbaverzió](https://azure.microsoft.com/free/).

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Az adatok tudományos virtuális gép létrehozása Linux rendszeren
Az adatok tudományos virtuális gép példányt létrehozni a Linux lépései a következők:

1. Keresse meg a virtuális gép a listázása a [Azure-portálon](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu).
2. Kattintson a **létrehozása** (a lap alján) a varázsló elindítani.![ Konfigurálja-adatok-tudományos-vm](./media/dsvm-ubuntu-intro/configure-data-science-virtual-machine.png)
3. A következő szakaszokban az adatokat a Microsoft Data tudományos virtuális gép létrehozásához használt az egyes (számba jobb oldalán a fenti ábrán) a varázsló lépéseit. Az alábbiakban az egyes lépéseket konfigurálásához szükséges adatokat:
   
   a. **Alapvető**:
   
   * **Név**: az adatok tudományos kiszolgáló létrehozásakor nevét.
   * **Felhasználónév**: első fiók bejelentkezhet azonosítóját.
   * **Jelszó**: első fiók jelszavát (használható nyilvános SSH-kulcs jelszó helyett).
   * **Előfizetés**: Ha több előfizetéssel rendelkezik, válassza ki a amelyiken a gép létrehozását és számlázva van. Ehhez az előfizetéshez erőforrás-létrehozási jogosultsággal kell rendelkeznie.
   * **Erőforráscsoport**: hozhat létre egy új vagy meglévő csoport használata.
   * **Hely**: válassza ki a legjobban megfelelő adatközpont. Általában az adatközpont, amely rendelkezik az adatok, vagy a helynév leggyorsabb hálózati hozzáférési legközelebb.
   
   b. **Méret**:
   
   * Válassza ki a kiszolgáló típusát, amely megfelel a funkcionális és költség megkötések. Válassza ki **nézet összes** a VM-méretek további beállítások megtekintéséhez. Válassza ki a GPU képzési egy NC-osztály virtuális Gépre.
   
   c. **Beállítások**:
   
   * **Lemez típusa**: válasszon **prémium** Ha inkább egy SSD-meghajtót (SSD). Ellenkező esetben válassza **szabványos**. GPU virtuális gépeknek szüksége van egy normál lemezen.
   * **A Tárfiók**: hozzon létre egy új Azure-tárfiók az előfizetésben, vagy használjon egy meglévőt, hogy a választott ugyanazon a helyen a **alapjai** a varázsló.
   * **Más paraméterek**: A legtöbb esetben csak használja az alapértelmezett értékeket. Figyelembe kell venni a nem az alapértelmezett értékeket, mutasson az egyes mezőkkel tájékoztató mutató hivatkozást.
   
   d. **Összefoglalás**:
   
   * Győződjön meg arról, hogy az összes megadott adatok helyesek.
   
   e. **Vásároljon**:
   
   * A kiépítés elindításához kattintson **megvásárlása**. Hivatkozás a tranzakció feltételeit valósul meg. A virtuális gép nem rendelkezik a kiválasztott kiszolgáló méretéhez számítási túl további díjakat a **mérete** lépés.

A kiépítése körülbelül 5 – 10 percet kell végrehajtani. A kiépítési állapotát az Azure portálon jelenik meg.

## <a name="how-to-access-the-data-science-virtual-machine-for-linux"></a>Hogyan érhetők el az adatok tudományos virtuális gép Linux
A virtuális gép létrehozása után is bejelentkezik az ssh protokoll használatával. A létrehozott fiók hitelesítő adatait használja a **alapjai** szakasz 3. lépés a szöveg shell felületén. Windows, egy SSH-ügyfél eszköz, például letöltheti [Putty](http://www.putty.org). Grafikus asztali (X Windows rendszer) tetszés szerint használhatja a Putty továbbítási X11, de X2Go ügyfél telepítése.

> [!NOTE]
> A X2Go ügyfél jobban teljesített, mint a tesztelése továbbítás X11. Egy asztali grafikus felület a X2Go ügyfél használatát javasoljuk.
> 
> 

## <a name="installing-and-configuring-x2go-client"></a>Telepítése és konfigurálása X2Go ügyfél
A Linux virtuális gép már kiépített X2Go kiszolgálóval és készen áll kapcsolatok fogadására. A Linux virtuális gép grafikus asztali való kapcsolódáshoz az alábbi eljárással az ügyfélen:

1. Töltse le és telepítse a saját ügyfélplatformjára X2Go ügyfél [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Futtassa a X2Go ügyfél, és válassza a **új munkamenet**. Több lap egy konfigurációs ablak nyílik meg. Adja meg a következő konfigurációs paramétereket:
   * **Munkamenet lapon**:
     * **Állomás**: az állomásnév vagy IP-címét a Linux adatok tudományos virtuális Gépet.
     * **Bejelentkezési**: felhasználónév, a Linux virtuális Gépre.
     * **SSH-Port**: hagyja a mezőt, 22, az alapértelmezett érték.
     * **Munkamenet típusa**: XFCE módosítsa az értéket. A Linux virtuális gép jelenleg csak XFCE desktop támogatja.
   * **Az adathordozó lapon**: kikapcsolhatja a hang támogatása és az ügyfél nyomtatási, ha nincs szüksége is használhatja őket.
   * **Megosztott mappák**: Ha az ügyfél gépek, a Linux virtuális gép csatlakoztatott könyvtárak, az ügyfél gép címtárakat, a virtuális gép ezen a lapon megosztani kívánt fel.

Bejelentkezés után a virtuális gép SSH-ügyfél vagy a XFCE grafikus asztali a X2Go ügyfélen keresztül, készen áll az eszközök, amelyek telepítése és konfigurálása történik meg a virtuális Gépen elindítására. A XFCE látható alkalmazások parancsikonjai és asztali ikonok esetében számos eszközt.

## <a name="tools-installed-on-the-data-science-virtual-machine-for-linux"></a>Az adatok tudományos virtuális gép telepített Linux eszközök
### <a name="deep-learning-libraries"></a>A részletes tanulási függvénytárak

#### <a name="cntk"></a>CNTK
A Microsoft kognitív eszközkészlet egy nyílt forráskódú, mély eszközkészlet tanulási. A legfelső szintű és py35 Conda környezetben Python kötések érhetők el. Az elérési út már szerepel a parancssori eszközzel (cntk) is rendelkezik.

Minta Python notebookok JupyterHub érhetők el. Egy alapszintű minta futtatásához a parancssorban a rendszerhéj hajtható végre a következő parancsokat:

    cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
    cntk configFile=lr_bs.cntk makeMode=false command=Train

További információ az című CNTK [GitHub](https://github.com/Microsoft/CNTK), és a [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).

#### <a name="caffe"></a>Caffe
Caffe Berkeley stratégiai és oktatóközpont mély tanulási keretrendszer. Rendszerben elérhető /opt/caffe. Példák /opt/caffe/examples találhatók.

#### <a name="caffe2"></a>Caffe2
Caffe2 egy részletes tanulási Caffe épülő Facebook-keretrendszert. Rendszerben elérhető Python 2.7 Conda legfelső szintű környezetben. Az aktiválás a rendszerhéjból futtassa a következő:

    source /anaconda/bin/activate root

Néhány példa notebookok JupyterHub érhetők el.

#### <a name="h2o"></a>H2O
H2O egy gyors, a memóriában, elosztott gépi tanulási és prediktív elemzési platformot. A Python-csomag telepítve van, a legfelső szintű és py35 Anaconda környezetekben egyaránt. Az R csomagot is telepítve van. A parancssorból H2O indításához futtassa `java -jar /dsvm/tools/h2o/current/h2o.jar`; léteznek a különböző [parancssori kapcsolók](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) , előfordulhat, hogy szeretne konfigurálni. Az Flow webes felhasználói felületén a kezdéshez http://localhost:54321 tallózással elérhetők. A minta notebookok JupyterHub is elérhetők.

#### <a name="keras"></a>Keras
Keras egy magas szintű Neurális hálózat API, amely képes a felső részén TensorFlow vagy Theano futó a Python. Érhető el a legfelső szintű és py35 Python-környezetben. 

#### <a name="mxnet"></a>MXNet
MXNet a teljesítmény és a rugalmasságot biztosít a részletes tanulási-keretrendszert. R és Python kötések szerepel-e a DSVM rendelkezik. Minta notebookok JupyterHub szerepelnek, és mintakód a /dsvm/samples/mxnet érhető el.

#### <a name="nvidia-digits"></a>NVIDIA SZÁMJEGY
A NVIDIA mély tanulási GPU képzési, számjegyek, úgynevezett rendszer közös mély tanulási feladatokat, például az adatok, tervezésével és képzési Neurális hálózatokat GPU-rendszerek kezelése, és figyeli a valós idejű speciális képi megjelenítés leegyszerűsítése érdekében a rendszer. 

SZÁMJEGYEK szolgáltatásként, számjegyek néven érhető el. Indítsa el a szolgáltatást, és keresse meg a http://localhost:5000 a kezdéshez.

SZÁMJEGYEK is telepítve van a Conda legfelső szintű környezetben Python modulként.

#### <a name="tensorflow"></a>TensorFlow
TensorFlow Google mély tanulási könyvtárban. Egy nyílt forráskódú szoftverkönyvtár adatok folyamata diagramjait használatával numerikus számításhoz. TensorFlow érhető el a py35 Python-környezetben, és néhány példa notebookok JupyterHub szerepelnek.

#### <a name="theano"></a>Theano
Theano egy Python kódtár hatékony numerikus számításhoz. Érhető el a legfelső szintű és py35 Python-környezetben. 

#### <a name="torch"></a>Torch
Torch rendszer tudományos számítógépes keretrendszere széles támogatja a gépi tanulási algoritmus. /Dsvm/tools/torch érhető el, és a th interaktív munkamenet és luarocks Csomagkezelő érhetők el a parancssorból. Példák /dsvm/samples/torch érhetők el.

A legfelső szintű Anaconda környezetben PyTorch is érhető el. Többek között a /dsvm/samples/pytorch.

### <a name="microsoft-r-server"></a>Microsoft R Server
R egyike a legnépszerűbb nyelvet adatelemzés és a gépi tanulás. Ha azt szeretné, a analytics R használandó, a virtuális Gépnek legyen Microsoft R Server (Asszony) a Microsoft R megnyitása (MRO) és a matematikai Kernel könyvtár (MKL). A MKL matematikai műveletek közös az olyan analitikai algoritmusok optimalizálja. MRO 100 százalék CRAN-R kompatibilis, és a közzétett CRAN R könyvtárak bármelyikét a MRO is telepíthető. Asszony lehetővé teszi az méretezés és a webszolgáltatások R modellek operationalization. Az R programok az egyik az alapértelmezett szerkesztőt, például az Rstudióból, vi, illetve Emacs szerkesztheti. Ha inkább a Emacs szerkesztővel, előre telepítve lett. A Emacs csomag ELREJTÉSE (Emacs beszél statisztika) munkanapon belül a Emacs szerkesztő R fájlokkal egyszerűbbé teszi.

Indítási R konzol, csak gépelje **R** a rendszerhéj. Ezzel megnyitná interaktív környezetben. Az R program elkészítéséhez egy szövegszerkesztőben, például a Emacs vagy vi általában használja, és futtassa az r parancsfájlok Az Rstudióból az R program fejlesztéséhez grafikus IDE környezettel rendelkezik.

Is van, hogy telepítse az R-parancsfájl a [első 20 R csomagok](http://www.kdnuggets.com/2015/06/top-20-r-packages.html) irányíthatók. Ez a parancsfájl futtatása után lehetséges az R interaktív felületet is meg lehet adni (említett) írja be a **R** a rendszerhéj.  

### <a name="python"></a>Python
A fejlesztési pythonos környezetekben Anaconda Python elosztási 2.7 és 3.5-ös telepítve van. Ehhez a terjesztéshez együtt a legnépszerűbb matematikai, tervezés és adatok analytics csomagok körülbelül 300 alap Python tartalmazza. Az alapértelmezett szöveg szerkesztők is használhatja. Ezenkívül Spyder, a Python IDE Anaconda Python terjesztéseket mellékelt is használhatja. Spyder kell egy grafikus asztali vagy X11 továbbítása. A grafikus asztali Spyder parancsikon valósul meg.

Python 2.7-es és 3.5-ös verzióját is van, mert az aktuális munkamenetben a használni kívánt kívánt Python verziójával (conda környezet) kifejezetten aktiválni kell. Az aktiválási folyamat a PATH változó állítja be a Python kívánt verziójával.

A Python 2.7 conda környezet aktiválásához a következő parancsot a rendszerhéj a:

    source /anaconda/bin/activate root

Python 2.7 telepített */anaconda/bin*.

A Python 3.5 conda környezet aktiválásához a rendszerhéjból futtassa a következő:

    source /anaconda/bin/activate py35


Python 3.5 telepített */anaconda/envs/py35/bin*.

A Python interaktív munkamenet meghívni, csak gépelje **python** a rendszerhéj. Ha Ön a grafikus felület vagy X11 továbbítási állítsa be, írja **pycharm** elindíthatja az PyCharm Python IDE.

További Python-könyvtárak telepítéséhez futtatnia kell ```conda``` vagy ````pip```` a sudo parancsot, és adja meg a teljes elérési útja a Python package Manager (conda vagy pip) a megfelelő Python-környezetben való telepítéséhez. Példa:

    sudo /anaconda/bin/pip install -n <package> #for Python 2.7 environment
    sudo /anaconda/envs/py35/bin/pip install -n <package> # for Python 3.5 environment


### <a name="jupyter-notebook"></a>Jupyter notebook
A Anaconda terjesztési Jupyter notebook, egy környezet kóddal és elemzési is tartalmaz. A Jupyter notebook JupyterHub keresztül érhető el. A helyi Linux-felhasználónév és jelszó használatával bejelentkezik.

A Jupyter notebook kiszolgáló előre beállított Python 2, a Python 3 és az R kernelek. Nincs "Jupyter Notebook" Indítsa el a böngészőt a notebook kiszolgálóhoz való hozzáféréshez nevű asztali ikon. Ha a virtuális gépről SSH vagy X2Go ügyfél, meglátogathatja [https://localhost:8000 /](https://localhost:8000/) a Jupyter notebook kiszolgálóhoz való hozzáféréshez.

> [!NOTE]
> Folytassa, ha kapott tanúsítványt figyelmeztetéseket.
> 
> 

A Jupyter notebook kiszolgáló bármely állomásról érheti el. Csak gépelje *https://\<VM DNS-nevét vagy IP-cím\>: 8000 /*

> [!NOTE]
> Port 8000 meg van nyitva a tűzfalon alapértelmezett amikor a virtuális gép ki van építve.
> 
> 

A Microsoft csomagolását minta notebookok – egy a Python és egy-egy R. A notebook kezdőlapján a hivatkozásra kattintva a minták után a helyi Linux-felhasználónév és jelszó használatával bejelentkezik a Jupyter notebook tekintheti meg. Létrehozhat egy új notebook kiválasztásával **új**, és ezután a megfelelő nyelvi kernel. Ha nem látja a **új** gombra, majd a **Jupyter** ikonra a bal felső Ugrás a kezdőlapra a notebook kiszolgáló számára.

### <a name="apache-spark-standalone"></a>Apache Spark önálló 
Az Apache Spark egy önálló példányát OEM a Linux DSVM is segítséget Spark-alkalmazások helyi először tesztelése és nagy fürtjein telepítése előtt. PySpark programok segítségével a Jupyter kernel futtathatja. Ha a Jupyter megnyitásához kattintson a **új** gombra kattintva kell megjelennie az elérhető kernelek listáját. A "Spark – Python" a PySpark kernel, amely lehetővé teszi a Spark olyan alkalmazásokat hozhat létre Python nyelven. A Python IDE PyCharm vagy Spyder, Spark program létrehozásához használhatja. Óta ez egy önálló példányát, a Spark-készlet belül a hívó ügyfél programot futtatja. Így gyorsabb és egyszerűbb a Spark-fürtön fejlesztése képest problémák hibaelhárítását. 

Egy minta PySpark notebook valósul meg a Jupyter, amely a kezdőkönyvtár Jupyter ($ OTTHONI/notebookok/SparkML/pySpark) alatti "SparkML" könyvtárban található. 

Ha a Spark on az R programozási, használhatja a Microsoft R Server, SparkR vagy sparklyr. 

A Microsoft R Server Spark-környezetében fut, mielőtt kell tennie telepítő lépés ahhoz, hogy egy helyi csomóponthoz Hadoop HDFS és a Yarn csak egyszer módosítható. Alapértelmezés szerint Hadoop szolgáltatások vannak telepítve, de a DSVM a letiltva. Ahhoz, hogy az engedélyezéséhez először futtassa az alábbi parancsokat rendszergazdaként kell:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

A Hadoop leállíthatja kapcsolódó szolgáltatások, ha már nincs szükség rájuk futtatásával ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```` egy minta bemutatja, hogyan lehet fejlesztéséhez és teszteléséhez Asszony távoli Spark környezetben (amely a DSVM önálló Spark-példányhoz) egy megadott és elérhető a a `/dsvm/samples/MRS` directory. 

### <a name="ides-and-editors"></a>IDEs és szerkesztők
Azt, hogy több kód szerkesztők. Ez magában foglalja a vi/VIM, Emacs, PyCharm, Rstudióból és intellij-t. Az IntelliJ, Rstudióból PyCharm grafikus szerkesztők és kell jelentkeznie egy grafikus asztali is használhatja őket. Ezek szerkesztők rendelkezik asztal és alkalmazások parancsikonjai elindítani őket.

**VIM** és **Emacs** szöveges szerkesztők vannak. Emacs, a jelenleg telepített egy Emacs beszél statisztika (ELREJTÉSE), amely megkönnyíti a r munkanapon belül a Emacs szerkesztő nevű bővítmény csomagot. További információ található [ELREJTÉSE](http://ess.r-project.org/).

**LaTex** Emacs bővítmény mellett a texlive csomag segítségével telepíthető [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) csomagot, amely egyszerűbbé teszi a szerzői LaTex dokumentumok Emacs belül.  

### <a name="databases"></a>Adatbázisok

#### <a name="graphical-sql-client"></a>Grafikus SQL-ügyfél
**SQuirrel SQL**, grafikus SQL-ügyfélen adtak meg (például a Microsoft SQL Server, és MySQL) különböző adatbázisaihoz való kapcsolódásra, és az SQL-lekérdezések futtatásához. (Például használata X2Go ügyfél) grafikus asztali munkamenetből futtatható. SQuirrel SQL meghívni, indítsa el a az ikonra az asztalon, vagy futtassa a következő parancsot a rendszerhéj.

    /usr/local/squirrel-sql-3.7/squirrel-sql.sh

Az első használat előtt a illesztőprogramok és adatbázis-alias beállítása. A JDBC illesztőprogramokat a helyen találhatók:

*/usr/share/Java/jdbcdrivers*

További információkért lásd: [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Microsoft SQL Server eléréséhez használt parancssori eszközök
Az ODBC illesztőprogram-csomagot az SQL Server is tartalmaz két parancssori eszközei:

**BCP**: A bcp segédprogram tömeges között a Microsoft SQL Server példányának és adatfájlt adatainak másolása egy felhasználó által megadott formátumban. A bcp segédprogram használható rendszerbe való importálás nagy mennyiségű új sort az SQL Server-táblákra vagy kirakni táblák exportálása adatfájlokba. Adatok importálása egy táblába, kell, hogy a tábla létrehozott formátumú fájl használata, és a táblázat és az adatok, amelyek érvényesek az oszlopok értelmezésekor.

További információkért lásd: [összeköti a bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**Sqlcmd**: Adja meg a Transact-SQL-utasításokat az sqlcmd segédprogram, valamint a rendszer eljárások, és parancsfájlok parancsot a parancssorba. Ezt a segédprogramot ODBC végrehajtani a Transact-SQL kötegeket használja.

További információkért lásd: [csatlakozni az Sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

> [!NOTE]
> Néhány ezt a segédprogramot a Linux és Windows platformok közötti különbségek vannak. További tudnivalókat a dokumentációjában.
> 
> 

#### <a name="database-access-libraries"></a>Adatbázis-hozzáférési függvénytárak
Érhetők el kódtárak R és Python access-adatbázishoz.

* Az R a **RODBC** csomag vagy **dplyr** csomag lehetővé teszi a lekérdezést, vagy SQL-utasítások végrehajtása az adatbázis-kiszolgálón.
* A Python a **pyodbc** könyvtár az alapul szolgáló réteg, az ODBC adatbázis-hozzáférést biztosít.  

### <a name="azure-tools"></a>Azure-eszközök
A következő Azure eszközei telepítve vannak a virtuális Gépen:

* **Azure parancssori felület**: az Azure parancssori felület lehetővé teszi létrehozása és kezelése az Azure-erőforrások felületparancsokat keresztül. Az Azure-eszközök meghívni, csak gépelje **azure súgó**. További információkért lásd: a [Azure CLI-dokumentáció oldalán](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **A Microsoft Azure Tártallózó**: Microsoft Azure Tártallózó egy grafikus eszköz, amellyel az Azure-tárfiókot, a tárolt objektumok közötti tallózást és feltöltését és letöltését az adatokat, és az Azure-blobokat. A Tártallózó elérhető az asztali parancsikonra. Hívhat meg azt a rendszerhéj parancssorából írja be a **StorageExplorer**. Egy X2Go ügyfélről bejelentkezve, vagy hogy a set továbbítás be X11 kell.
* **Az Azure szalagtárak**: az alábbiakban néhány előre telepített könyvtárak.
  
  * **Python**: az Azure-kapcsolatos-függvénytárak telepített pythonban **azure**, **azureml**, **pydocumentdb**, és **pyodbc**. Az első három könyvtárakkal érheti el az Azure storage szolgáltatások, Azure Machine Learning és Azure Cosmos DB (egy NoSQL-adatbázis az Azure-on). A negyedik könyvtár pyodbc (együtt a Microsoft ODBC-illesztőprogram az SQL Server), lehetővé teszi a hozzáférést a SQL Server, az Azure SQL Database és az Azure SQL Data Warehouse Python egy ODBC-illesztő segítségével. Adja meg **pip lista** megjelenítéséhez a listában szereplő könyvtárak. Győződjön meg arról, a parancs futtatásához a Python 2.7-es és 3.5-ös környezetben.
  * **R**: az Azure-kapcsolatos-függvénytárak az R telepített **AzureML** és **RODBC**.
  * **Java**: Azure Java könyvtárak listája a címtárban található **/dsvm/sdk/AzureSDKJava** a virtuális Gépen. A kulcs könyvtárak az Azure tárolás és a felügyeleti API-k, Azure Cosmos DB és JDBC illesztőprogramok legyenek az SQL Server.  

Elérheti a [Azure-portálon](https://portal.azure.com) a előre telepített Firefox böngészőből. Az Azure-portál létrehozására, kezelésére és Azure-erőforrások figyelése.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Az Azure Machine Learning egy teljes körűen felügyelt felhőszolgáltatás, amely lehetővé teszi, hogy hozhat létre, telepíthetnek és prediktív elemzési megoldások megosztása. A kísérletek és -modellek létrehozása az Azure Machine Learning Studio. Az elérhető egy webes böngésző adatokat tudományos virtuális gépen látogasson el [Microsoft Azure Machine Learning](https://studio.azureml.net).

Miután bejelentkezik Azure Machine Learning Studióban, rendelkezik hozzáféréssel egy kísérleti vászon ahol hozhat létre egy logikai gépi tanulási algoritmusok folyamata. Is hozzáférhetnek az Azure Machine Learning szolgáltatásban üzemeltetett Jupyter notebook, és problémamentesen együttműködik a Machine Learning Studióban kísérletek. Azok a gépi tanulási modellek, amelyek már létrehozta a buildet használatával őket egy webes szolgáltatás felületén. Ez lehetővé teszi az ügyfelek bármilyen nyelven írt meghívni a gépi tanulási modellek által. További információkért lásd: a [Machine Learning dokumentációs](https://azure.microsoft.com/documentation/services/machine-learning/).

Is létrehozhatja a modellek R vagy Python nyelven a virtuális Gépre, és majd telepítenie kell azt az Azure Machine Learning éles környezetben. R szalagtárak telepített azt (**AzureML**) és a Python (**azureml**) Ez a funkció engedélyezéséhez.

Az Azure Machine Learning modellek R és Python telepítéséről további információkért lásd: [tíz lehetősége van az adatok tudományos virtuális gép](vm-do-ten-things.md) (különösen a szakasz "R vagy Python modellek létrehozása, és azok őket az Azure Machine Learning segítségével").

> [!NOTE]
> Ezek az utasítások a Windows-verzióhoz, az adatok tudományos VM készültek. De az adatokat az Azure Machine Learning modellek telepítése nincs alkalmazható a Linux virtuális gép.
> 
> 

### <a name="machine-learning-tools"></a>Machine learning-eszközök
A virtuális Gépet tartalmaz néhány gépi tanulási a eszközök és algoritmusok, előre összeállított és előre telepítve helyileg. Ezek a következők:

* **Vowpal Wabbit**: gyors online tanulási algoritmus.
* **xgboost**: olyan eszköz, amely biztosítja az optimalizált, súlyozott algoritmus.
* **Rattle**: az R-alapú grafikus eszköz egyszerűen az adatok feltárása és modellezési.
* **Python**: Anaconda Python gépi tanulási algoritmusok könyvtárakkal Scikit – ismerje meg, például a csomagolt származik. Más tárak segítségével telepítheti a `pip install` parancsot.
* **LightGBM**: keretrendszer kiemelése gyors, elosztott, nagy teljesítményű átmenetekhez a döntési fa algoritmusok alapján.
* **R**: a machine learning funkciók gazdag könyvtár érhető el a R. A szalagtár szerepel, amely előre telepítve az lm, glm, randomForest, rpart. Más szalagtárak futtatásával telepíthető:
  
        install.packages(<lib name>)

Íme, néhány további információt a lista első három machine learning eszközeivel.

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit
Vowpal Wabbit a machine learning-rendszer által használt módszerek például online, a kivonatoló, allreduce, csökkentése, learning2search, aktív, és interaktív tanulási.

Az eszköz futtatásához egy egyszerű példa, tegye a következőket:

    cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
    cd vwdemo
    vw house_dataset

Nincsenek más, nagyobb bemutatók ebben a könyvtárban. VW további információkért lásd: [ebben a szakaszban a Githubon](https://github.com/JohnLangford/vowpal_wabbit), és a [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>xgboost
Ez az egy könyvtárban tervezett, súlyozott (fa) algoritmusok optimalizálva. Ezt a szalagtárat célja a gépek számítási határain leküldése kiemelése, amelyek nagy méretű fa szükséges méretek: a méretezhető, hordozható és pontos.

Azt, a parancssorból, valamint az R tár biztosítja.

Ebben a könyvtárban, az R használandó elindíthatja egy interaktív R munkamenetet (beírásával **R** a rendszerhéj), és a könyvtár betöltéséhez.

Íme egy egyszerű példa R kér futtathatja:

    library(xgboost)

    data(agaricus.train, package='xgboost')
    data(agaricus.test, package='xgboost')
    train <- agaricus.train
    test <- agaricus.test
    bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
    pred <- predict(bst, test$data)

A xgboost parancssort futtathat, az alábbiakban a a rendszerhéj végrehajtandó, parancsokat:

    cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
    cd xgboostdemo
    xgboost mushroom.conf


A megadott könyvtár .model fájl beíródik. Ez a bemutató példa kapcsolatos információk is találhatók [a Githubon](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Xgboost kapcsolatos további információkért tekintse meg a [xgboost dokumentációs oldal](https://xgboost.readthedocs.org/en/latest/), és a [GitHub-tárházban](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Rattle
Rattle (a **R** **A**nalytical **T**öz **T**o **L**vett szolgáltatás érvényessége alatt **E**asily) GUI-alapú adatok feltárása és modellezési használ. Megjelennek a statisztikai és vizuális ezekkel az adatok, könnyen modellezhető, az adatokból felügyeletlen és a felügyelt modellek alkot, grafikus, megadja a modell teljesítményétől átalakítások adatokat, és beállítja a pontszámok új adatokat. Azt is, ami R-kód, a műveletek a felhasználói felületen kell közvetlenül R futtatását, illetve további elemzés céljából kiindulási pontként használható replikálása.

Rattle futtatásához kell lennie egy grafikus asztali bejelentkezési munkamenet. Írja be a Terminálszolgáltatások ```R``` megadását az R-környezetben. Az R parancssorba írja be a következő parancsokat:

    library(rattle)
    rattle()

Most már egy grafikus felület megnyílik lapok vannak beállítva. Az első lépések lépéseit Itt szerepelnek Rattle használjon minta időjárása és a modell létrehozása szükséges. Egyes az alábbi lépéseket megkérdezi, hogy automatikusan telepítsenek és néhány szükséges R csomagokat, amelyek még nem a rendszer betöltése.

> [!NOTE]
> Ha nem fér a csomag telepíti a rendszer könyvtárban (alapértelmezett), a kérdés jelenhet meg a csomagok telepítése a saját könyvtárukból az R konzolablakban. Válasz *y* Ha ezek az üzenetek.
> 
> 

1. Kattintson az **Execute** (Végrehajtás) parancsra.
2. Egy párbeszédpanel jelenik meg, kéri fel, ha szeretné, hogy a példa időjárási adatkészlet használja. Kattintson a **Igen** a példa betölteni.
3. Kattintson a **modell** fülre.
4. Kattintson a **Execute** hozhat létre a döntési fában.
5. Kattintson a **megrajzolásához** megjelenítéséhez a döntési fában.
6. Kattintson a **erdő** választógomb, és kattintson a **Execute** véletlenszerű erdő létrehozásához.
7. Kattintson a **Evaluate** fülre.
8. Kattintson a **kockázati** választógomb, és kattintson a **Execute** két kockázat (eloszlásfv) teljesítmény előkészítésére megjelenítéséhez.
9. Kattintson a **napló** lapon, a fenti műveletekre vonatkozó generate R kód megjelenítése.
   (Az aktuális kiadás Rattle egy hiba miatt be kell helyezni egy  *#*  karakter elé *... Ez a napló exportálása*  a szöveges napló.)
10. Kattintson a **exportálása** gombra kattintva mentse az R-parancsfájl nevű *weather_script. R* az otthoni mappába.

Kiléphet Rattle és R. Most módosítja a generált R-parancsfájl, vagy használja, mert az ismétlődő minden, ami a Rattle felhasználói felületen belül lett elvégezve bármikor futtatásához. Különösen az R kezdők, ez pedig egyszerűen gyorsan elemzést és a gépi tanulás egyszerű grafikus felületen, automatikusan az R módosítása és/vagy ismerje meg, a kód létrehozása közben.

## <a name="next-steps"></a>További lépések
Ez hogyan folytathatja a tanulási és feltárása:

* A [adattudomány meg az adatok tudományos virtuális gép Linux](linux-dsvm-walkthrough.md) forgatókönyv bemutatja, hogyan itt kiépítése Linux adatok tudományos VM több közös tudományos feladatok elvégzésére. 
* Megismerkedhet a különböző adatok tudományos eszközök az adatok tudományos VM próbálhatja ki az eszközöket, a cikkben leírtak szerint. Is futtathatja *dsvm-további-információ* meg a rendszerhéj alapszintű bevezetést és mutatókat biztosít a virtuális Gépen telepített eszközök további információ a virtuális gépen belül.  
* Ismerje meg, hogyan hozhat létre végpont elemzési megoldásokat rendszeresen használatával a [Team adatok tudományos folyamat](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).
* Látogasson el a [Cortana Analytics Gallery](http://gallery.cortanaanalytics.com) machine learning és az adatok analytics mintákat a Cortana Analytics Suite használó.

