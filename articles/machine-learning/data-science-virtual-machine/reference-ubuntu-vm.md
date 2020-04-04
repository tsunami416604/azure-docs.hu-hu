---
title: 'Referencia: Ubuntu Data Science virtuális gép'
titleSuffix: Azure Data Science Virtual Machine
description: Részletek az Ubuntu Data Science virtuális gépben található eszközökről
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: 830668a78929a5e6a7e131ade5c62b81e6d725c3
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631366"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>Referencia: Ubuntu (Linux) Data Science virtuális gép

Az ubuntudata-adatelemzési virtuális gépen elérhető eszközök listáját alább találhatja meg. 

## <a name="deep-learning-libraries"></a>Mélytanulási könyvtárak

### <a name="cntk"></a>CNTK

A Microsoft Cognitive Toolkit egy nyílt forráskódú deep learning eszközkészlet. Python-kötések a gyökér- és py35 Conda-környezetekben érhetők el. Azt is rendelkezik egy parancssori eszköz (CNTK), amely már az elérési úton.

Minta Python-jegyzetfüzetek a JupyterHubon érhetők el. Ha egy alapmintát a parancssorból szeretne futtatni, futtassa a következő parancsokat a rendszerhéjban:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

További információt a [GitHub](https://github.com/Microsoft/CNTK) CNTK részében és a [CNTK wikiben](https://github.com/Microsoft/CNTK/wiki)talál.

### <a name="caffe"></a>Caffe

A Caffe a Berkeley Vision and Learning Center mélytanulási keretrendszere. A /opt/caffe kapcsolóban érhető el. A /opt/caffe/examples című részben találhat példákat.

### <a name="caffe2"></a>Caffe2

Caffe2 egy mély tanulási keretet a Facebook, amely épül Caffe. Python 2.7-es verzióban érhető el a Conda gyökérkörnyezetben. Az aktiváláshoz futtassa a következő parancsot a rendszerhéjból:

```bash
source /anaconda/bin/activate root
```

Néhány példa notebookok érhetők el jupyterHub.

### <a name="h2o"></a>H2o

A H2O egy gyors, memórián belüli, elosztott gépi tanulás és prediktív elemzési platform. A Python-csomag a gyökér- és a py35 Anaconda környezetben is telepítve van. Egy R csomag is telepítve van. 

Ha a H2O parancsot `java -jar /dsvm/tools/h2o/current/h2o.jar`a parancssorból szeretné megnyitni, futtassa a parancsot. Vannak különböző [parancssori kapcsolók,](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) amelyeket konfigurálni szeretne. A Folyamat webes felhasználói felületét a http://localhost:54321 kezdéshez böngészve érheti el. Minta notebookok is elérhetőj a JupyterHub.

### <a name="keras"></a>Keras

A Keras egy magas szintű neurális hálózati API a Pythonban. Futtatható a TensorFlow, a Microsoft Cognitive Toolkit vagy a Theano. A gyökér- és py35 Python-környezetekben érhető el.

### <a name="mxnet"></a>MXNet

Az MXNet egy mélytanulási keretrendszer, amelyet a hatékonyság és a rugalmasság érdekében terveztek. R és Python kötések szerepelnek a DSVM.It has R and Python bindings included on the DSVM. A mintajegyzetfüzetek a JupyterHub részét képezik, a mintakód pedig a /dsvm/samples/mxnet kapcsolóban érhető el.

### <a name="nvidia-digits"></a>NVIDIA számjegyek

Az NVIDIA Deep Learning GPU training system, más néven DIGITS egy olyan rendszer, amely egyszerűsíti a közös mélytanulási feladatokat. Ezek a feladatok közé tartozik az adatok kezelése, a neurális hálózatok tervezése és betanítása A GPU-rendszereken, valamint a teljesítmény valós idejű figyelése fejlett vizualizációval.

A DIGITS *számjegyek számjegyek*nevű szolgáltatásként érhetők el. Indítsa el a http://localhost:5000 szolgáltatást, és tallózással kezdje a kezdéshez.

Digits is telepítve van, mint egy Python modul a Conda gyökér környezetben.

### <a name="tensorflow"></a>TensorFlow

A TensorFlow a Google mélytanulási könyvtára. Ez egy nyílt forráskódú szoftverkönyvtár numerikus számításokhoz, adatfolyam-grafikonok használatával. A TensorFlow a py35 Python környezetben érhető el, és néhány mintajegyzetfüzet a JupyterHub része.

### <a name="theano"></a>Theano

A Theano egy Python-könyvtár a hatékony numerikus számításhoz. A gyökér- és py35 Python-környezetekben érhető el. 

### <a name="torch"></a>Torch

A Torch egy tudományos számítástechnikai keretrendszer, amely széles körben támogatja a gépi tanulási algoritmusokat. A /dsvm/tools/torch kapcsolóban érhető el, a **th** interactive session és a LuaRocks csomagkezelő pedig a parancssorból érhető el. A példák a /dsvm/samples/torch kapcsolóban érhetők el.

PyTorch is elérhető a gyökér Anaconda környezetben. Ilyenek például a /dsvm/samples/pytorch.

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning-kiszolgáló

Az R az adatelemzés és a gépi tanulás egyik legnépszerűbb nyelve. Ha az R-t szeretné használni az elemzéshez, a virtuális gép rendelkezik a Microsoft Machine Learning Server microsoft r open és matematikai kernel tárral. A Matematikai kernelkönyvtár optimalizálja az analitikus algoritmusokban gyakori matematikai műveleteket. A Microsoft R Open 100 százalékban kompatibilis a CRAN R-rel, és a CRAN-ban közzétett R-könyvtárak bármelyike telepíthető a Microsoft R Open szolgáltatásra. 

A Machine Learning Server lehetővé teszi az R-modellek webszolgáltatásokba való méretezését és üzembe izálását. Az R-programokat az egyik alapértelmezett szerkesztőben szerkesztheti, például az RStudio, vi vagy Emacs programban. Ha inkább az Emacs szerkesztőt szeretné használni, azt előre telepítették. Az Emacs ESS (Emacs Speaks Statistics) csomag leegyszerűsíti az R fájlokkal való munkát az Emacs szerkesztőben.

Az R konzol megnyitásához írja be az **R** értéket a héjba. Ez a parancs interaktív környezetbe vezet. Az R program fejlesztéséhez általában olyan szerkesztőt kell használnia, mint az Emacs vagy a vi, majd futtatja a parancsfájlokat az R-en belül. Az RStudio, van egy teljes grafikus IDE, hogy fejlessze az R programot.

Van egy R szkript is, hogy telepítse a [Top 20 R csomagokat,](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) ha akarod. Ezt a parancsfájlt az R interaktív felületen való beírásután is futtathatja. Mint korábban említettük, akkor nyissa meg, hogy a felület beírásával **R** a héj.  

## <a name="python"></a>Python

Az Anaconda Python Python 2.7-es és 3.5-ös környezetekkel van telepítve. A 2,7-es környezetet _gyökérnek_, a 3,5-ös környezetet _pedig py35-nek_nevezik. Ez a disztribúció tartalmazza az alap Python mellett mintegy 300 a legnépszerűbb matematikai, mérnöki és adatelemzési csomagok.

A py35 környezet az alapértelmezett. A gyökér (2.7) környezet aktiválásához használja ezt a parancsot:

```bash
source activate root
```

A py35 környezet ismételt aktiválásához használja a következő parancsot:

```bash
source activate py35
```

Python interaktív munkamenet meghívásához írja be a **python** a rendszerhéjba. 

További Python-kódtárak telepítése conda vagy pip használatával. Pip esetén először aktiválja a megfelelő környezetet, ha nem szeretné az alapértelmezett értéket:

```bash
source activate root
pip install <package>
```

Vagy adja meg a pip teljes elérési útját:

```bash
/anaconda/bin/pip install <package>
```

Conda esetén mindig adja meg a környezet nevét (py35 vagy gyökér):

```bash
conda install <package> -n py35
```

Ha grafikus felületen vagy X11 továbbítási beállításokkal rendelkezik, a PyCharm Python IDE megnyitásához **pycharm-t** adhat meg. Használhatja az alapértelmezett szövegszerkesztőket. Ezenkívül használhatja a Spyder, egy Python IDE, amely az Anaconda Python disztribúciók csomagban. Spyder szüksége van egy grafikus asztali vagy X11 továbbítás. A grafikus asztal egy parancsikont Spyder.

## <a name="jupyter-notebook"></a>Jupyter notebook

Az Anaconda disztribúció is jön-val egy Jupyter notebook, egy környezetet megosztani kódot és elemzést. A Jupyter notebook jupyterhubon keresztül érhető el. A helyi Linux-felhasználónévvel és jelszóval jelentkezik be.

A Jupyter notebook-kiszolgáló előre konfigurálva van python 2, Python 3 és R kernelekkel. A **Jupyter Notebook** desktop ikonnal nyissa meg a böngészőt, és férjen hozzá a jegyzetfüzet-kiszolgálóhoz. Ha a virtuális gépet SSH-n vagy az X2Go kliensen keresztül, [https://localhost:8000/](https://localhost:8000/)a Jupyter notebook kiszolgálót is elérheti a rendszerben.

> [!NOTE]
> Folytassa, ha tanúsítványra vonatkozó figyelmeztetést kap.

A Jupyter notebook-kiszolgáló bármely állomásról elérhető. Adja meg **https://\<vm\>DNS-nevét vagy IP-címét :8000/**.

> [!NOTE]
> A 8000-es port alapértelmezés szerint meg van nyitva a tűzfalban, amikor a virtuális gép ki van építve. 

Van csomagolt minta notebook - egy Python és egy R. A mintákra mutató hivatkozást a jegyzetfüzet kezdőlapján láthatja, miután hitelesítette a Jupyter notebookot a helyi Linux-felhasználónév és jelszó használatával. Új jegyzetfüzetet úgy hozhat létre, hogy az **Új**lehetőséget, majd a megfelelő nyelvi kernelt választja. Ha nem látható az **Új** gomb, kattintson a bal felső sarokban lévő **Jupyter** ikonra a jegyzetfüzet-kiszolgáló kezdőlapjának megugrásához.

## <a name="apache-spark-standalone"></a>Apache Spark önálló

Az Apache Spark egy önálló példánya elő van telepítve a Linux DSVM-en, hogy segítsen a Spark-alkalmazások helyi fejlesztésében, mielőtt tesztelne és üzembe helyezne őket nagy fürtökön. 

PySpark-programok futtathatók a Jupyter kernelen keresztül. A Jupyter megnyitásakor jelölje ki az **Új** gombot, és meg kell jelennie az elérhető kernelek listájának. **Spark – A Python** a PySpark kernel, amely lehetővé teszi a Spark-alkalmazások python-nyelv használatával történő összeállítását. A Spark-program létrehozásához használhatja a Python IDE-t is, például a PyCharm-t vagy a Spydert. 

Ebben az önálló példányban a Spark-verem a hívó ügyfélprogramon belül fut. Ez a funkció gyorsabbá és egyszerűbbé teszi a problémák elhárítását a Spark-fürtön való fejlesztéshez képest.

A Jupyter egy minta PySpark-jegyzetfüzetet biztosít. A SparkML-könyvtárban, a Jupyter ($HOME/notebook/SparkML/pySpark) kezdőkönyvtárában található. 

Ha az R for Spark ban programoz, használhatja a Microsoft Machine Learning Servert, a SparkR-t vagy a Sparklyrt. 

Mielőtt spark-környezetben futtatná a Microsoft Machine Learning Serverben, egy egyszeri beállítási lépést kell végeznie egy helyi egycsomópontos Hadoop HDFS- és fonalpéldány engedélyezéséhez. Alapértelmezés szerint a Hadoop-szolgáltatások telepítve vannak, de le vannak tiltva a DSVM-en. Az engedélyezéshez első alkalommal a következő parancsokat kell root-ként futtatnia:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

A Hadoopszolgáltatás leállításához, ha nincs rájuk szüksége, ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```a futtatásával leállíthatja.

A /dsvm/samples/MRS könyvtár egy minta, amely bemutatja, hogyan fejleszti és teszteli a Microsoft Machine Learning Server egy távoli Spark környezetben (az önálló Spark-példány a DSVM).

## <a name="ides-and-editors"></a>IDEs és szerkesztők

Van egy választás több kód szerkesztők, beleértve a vi / Vim, Emacs, PyCharm, RStudio, és IntelliJ. 

A PyCharm, az RStudio és az IntelliJ grafikus szerkesztők. A használatukhoz be kell jelentkeznie egy grafikus asztalra. Az asztal és az alkalmazás menüjének parancsikonjai segítségével nyithatja meg őket.

A Vim és az Emacs szövegalapú szerkesztők. Az Emacs-en az ESS kiegészítő csomag megkönnyíti az R-rel való munkát az Emacs szerkesztőn belül. További információt az [ESS honlapján](https://ess.r-project.org/)talál.

LaTex van telepítve a texlive csomagot, valamint egy Emacs add-on csomag nevű [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Ez a csomag leegyszerűsíti a LaTex dokumentumok szerkesztését az Emacs-en belül.  

## <a name="databases"></a>Adatbázisok

### <a name="graphical-sql-client"></a>Grafikus SQL-ügyfél

A SQuirrel SQL, egy grafikus SQL-ügyfél, különböző adatbázisokhoz (például Microsoft SQL Server és MySQL) tud csatlakozni, és SQL-lekérdezéseket futtathat. Futtathatja squirrel SQL egy grafikus asztali munkamenet (az X2Go kliens, például) segítségével egy asztali ikon. Vagy futtathatja az ügyfelet a következő paranccsal a rendszerhéjban:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Az első használat előtt állítsa be az illesztőprogramokat és az adatbázis-aliasokat. A JDBC illesztőprogramok a /usr/share/java/jdbcdrivers helyen találhatók.

További információ: [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Parancssori eszközök a Microsoft SQL Server eléréséhez

Az SQL Server ODBC illesztőprogram-csomagja két parancssori eszközt is tartalmaz:

- **bcp**: A bcp eszköz tömegesen másolja az adatokat a Microsoft SQL Server egy példánya és egy, a felhasználó által megadott formátumban megadott adatfájl között. Az bcp eszközzel nagy számú új sort importálhat az SQL Server táblákba, vagy adatokat exportálhat táblákból adatfájlokba. Ha adatokat szeretne behozni egy táblába, a táblához létrehozott formátumfájlt kell használnia. Vagy meg kell értenie a tábla szerkezetét és az oszlopokra érvényes adattípusokat.

  További információ: [Csatlakozás a bcp-hez.](https://msdn.microsoft.com/library/hh568446.aspx)

- **sqlcmd**: Az sqlcmd eszközzel transact-SQL utasításokat adhat meg. A rendszereljárásokat és a parancsfájlfájlokat a parancssorba is megadhatja. Ez az eszköz az ODBC segítségével futtatja a Transact-SQL kötegeket.

  További információ: [Csatlakozás az sqlcmd-hez.](https://msdn.microsoft.com/library/hh568447.aspx)

  > [!NOTE]
  > Van néhány különbség ebben az eszközben a Linux és a Windows platformok között. A részleteket lásd a dokumentációban.

### <a name="database-access-libraries"></a>Adatbázis-hozzáférési tárak

A tárak az R és python nyelven érhetők el az adatbázis-hozzáféréshez:

* Az R-ben az RODBC-csomag vagy a dplyr-csomag segítségével lekérdezheti vagy futtathatja az SQL-utasításokat az adatbázis-kiszolgálón.
* A Pythonban a pyodbc könyvtár adatbázis-hozzáférést biztosít az ODBC-vel, mint az alapul szolgáló réteggel.  

## <a name="azure-tools"></a>Azure-eszközök

A következő Azure-eszközök vannak telepítve a virtuális gépre:

* **Azure CLI:** Az Azure parancssori felületén létrehozhat és kezelhet Azure-erőforrásokat shell parancsokon keresztül. Az Azure-eszközök megnyitásához írja be az **azure súgóját.** További információt az [Azure CLI dokumentációs oldalán talál.](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* **Azure Storage Explorer:** Az Azure Storage Explorer egy grafikus eszköz, amellyel az Azure storage-fiókjában tárolt objektumok között böngészhet, valamint adatokat tölthet fel és tölthet le az Azure-blobokba és onnan. A Storage Explorer az asztal parancsikonjának ikonjáról érhető el. Shell parancssorból is megnyithatja a StorageExplorer ( **StorageExplorer**) parancsba való belépésével. X2Go ügyfélről kell bejelentkeznie, vagy be kell állítania az X11 továbbítást.
* **Azure-kódtárak:** Az alábbiakban néhány előre telepített könyvtárak.
  
  * **Python**: A Python Azure-ral kapcsolatos kódtárai *az azure,* *az azureml*, *pydocumentdb*és *pyodbc.* Az első három kódtárak, elérheti az Azure storage-szolgáltatások, az Azure Machine Learning és az Azure Cosmos DB (a NoSQL-adatbázis az Azure-ban). A negyedik könyvtár, a pyodbc (az SQL Server Microsoft ODBC illesztőprogramjával együtt) odBC-felülethasználatával engedélyezi az SQL Server, az Azure SQL Database és az Azure SQL Data Warehouse elérését a Pythonból. Írja be **a pip listát** az összes felsorolt tárak megtekintéséhez. Győződjön meg róla, hogy futtassa ezt a parancsot a Python 2.7 és 3.5 környezetben.
  * **R**: Az Azure-ral kapcsolatos tárak R AzureML és RODBC.
  * **Java**: Az Azure Java-kódtárak listája megtalálható a virtuális gép /dsvm/sdk/AzureSDKJava címtárában. A legfontosabb kódtárak az Azure storage és felügyeleti API-k, az Azure Cosmos DB és az SQL Server JDBC-illesztőprogramjai.  

Az [Azure](https://portal.azure.com) Portalaz előre telepített Firefox böngészőből érhető el. Az Azure Portalon azure-erőforrásokat hozhat létre, kezelhet és figyelhet.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Az Azure Machine Learning egy teljes körűen felügyelt felhőszolgáltatás, amely lehetővé teszi a prediktív elemzési megoldások készítését, üzembe helyezését és megosztását. Az Azure Machine Learning stúdióban (előzetes verzió) hozhat létre kísérleteket és modelleket. Az adatelemzési virtuális gép webböngészőjéből a [Microsoft Azure Machine Learning](https://ml.azure.com)webhelyen keresztül érhető el.

Miután bejelentkezett az Azure Machine Learning-stúdióba, egy kísérletezési vászon használatával logikai folyamatot hozhat létre a gépi tanulási algoritmusokhoz. Az Azure Machine Learning üzemeltetett Jupyter-jegyzetfüzethez is hozzáférhet, és zökkenőmentesen dolgozhat az Azure Machine Learning-stúdióban végzett kísérletekkel. 

Működőképessé a gépi tanulási modellek, amelyek et épített a webszolgáltatás felületén csomagolva őket. A gépi tanulási modellek üzembe írása lehetővé teszi, hogy a bármilyen nyelven írt ügyfelek meghívja az adott modellek től származó előrejelzéseket. További információt a [Machine Learning dokumentációjában](https://azure.microsoft.com/documentation/services/machine-learning/)talál.

A modellek r vagy python a virtuális gépen, majd üzembe helyezheti őket éles környezetben az Azure Machine Learning. Telepítettük a könyvtárakat az R (**AzureML**) és a Python (**azureml**) a funkció engedélyezéséhez.

Az R és Python modellek Azure Machine Learningbe való üzembe helyezéséről [a Tíz dolog, amit az adatelemzési virtuális gépen tehet.](vm-do-ten-things.md)

> [!NOTE]
> Ezek az utasítások az adatelemzési virtuális gép Windows-verziójához készültek. De az azure Machine Learning-alapú modellek üzembe helyezésével kapcsolatos információk a Linux virtuális gépre vonatkoznak.

## <a name="machine-learning-tools"></a>Gépi tanulási eszközök

A virtuális gép gépi tanulási eszközökkel és algoritmusokkal rendelkezik, amelyeket előre lefordítottak és helyben előre telepítettek. Ezek a következők:

* **Vowpal Wabbit**: A gyors online tanulási algoritmus.
* **xgboost**: Olyan eszköz, amely optimalizált, kiemelt fa algoritmusok.
* **Rattle**: Egy R-alapú grafikus eszköz az egyszerű adatok feltárása és modellezése.
* **Python:** Anaconda Python jön csomagban gépi tanulási algoritmusok könyvtárak, mint a Scikit-learn. A parancs segítségével más `pip install` könyvtárakat is telepíthet.
* **LightGBM**: Gyors, elosztott, nagy teljesítményű gradiens-növelő keretrendszer döntési fa algoritmusok alapján.
* **R**: A gépi tanulási funkciók gazdag könyvtára érhető el az R számára. A parancs futtatásával más könyvtárakat is telepíthet:
  
        install.packages(<lib name>)

Az alábbiakban néhány további információt talál a listában szereplő első három gépi tanulási eszközről.

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit egy gépi tanulási rendszer, amely olyan technikákat használ, mint az online, hashing, allreduce, csökkentések, learning2search, aktív, és interaktív tanulás.

Ha az eszközt alappéldán szeretné futtatni, használja a következő parancsokat:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Vannak más, nagyobb demók is a könyvtárban. További információ a Vowpal Wabbit, lásd [ezt a szakaszt a GitHub](https://github.com/JohnLangford/vowpal_wabbit) és a [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>xgboost

Az xgboost könyvtárat a kiemelt (fa) algoritmusokra tervezték és optimalizálták. A könyvtár célja, hogy a gépek számítási korlátait a méretezhető, hordozható és pontos nagyméretű fakiemelés biztosításához szükséges szélsőségekre tolja.

Parancssorként és R könyvtárként van megadva. Ha ezt a könyvtárat az R-ben szeretné használni, elindíthat egy interaktív R-munkamenetet (az **R** beírásával a rendszerhéjba), és betöltheti a könyvtárat.

Íme egy egyszerű példa, amelyet r-üzenetben futtathat:

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

Az xgboost parancssor futtatásához az alábbiakat kell futtatni a parancsértelmezőben:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

A .model fájl a megadott könyvtárba íródik. Erről a bemutató példáról a [GitHubon](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification)talál információt.

Az xgboost-ról további információt az [xgboost dokumentációs oldalán](https://xgboost.readthedocs.org/en/latest/) és a [GitHub-tárházban](https://github.com/dmlc/xgboost)talál.

### <a name="rattle"></a>Rattle

Rattle (az **R** **A**nalytical **T**ool **T**o **L**keresni **E**könnyen) használ GUI-alapú adatok feltárása és modellezése. Bemutatja az adatok statisztikai és vizuális összefoglalóit, átalakítja a könnyen modellezhető adatokat, felügyelet nélküli és felügyelt modelleket is létrehoz az adatokból, grafikusan mutatja be a modellek teljesítményét, és új adatkészleteket készít. R-kódot is generál, replikálja a felhasználói felületen lévő műveleteket, amelyek közvetlenül az R-ben futtathatók, vagy további elemzés kiindulópontjaként használhatók.

A Rattle futtatásához egy grafikus asztali bejelentkezési munkamenetben kell lennie. A terminálon adja meg az **R** értéket az R környezet megnyitásához. Az R parancssorba írja be a következő parancsokat:

```R
library(rattle)
rattle()
```

Most egy grafikus felület nyílik meg egy sor fülek. A Rattle következő rövid útmutató lépései segítségével használjon egy időjárási adatkészletet, és hozzon létre egy modellt. Néhány lépésben a rendszer kéri, hogy automatikusan telepítsen és töltsön be néhány szükséges R-csomagot, amelyek még nem szerepelnek a rendszeren.

> [!NOTE]
> Ha nincs hozzáférése a csomag telepítéséhez a rendszerkönyvtárba (ez az alapértelmezett), az R konzol ablakában megjelenhet egy üzenet a csomagok személyes könyvtárba való telepítésére. Válaszoljon **y-ra,** ha ezeket az utasításokat látja.

1. Válassza a **Végrehajtás** lehetőséget.
1. Megjelenik egy párbeszédpanel, amely megkérdezi, hogy szeretné-e használni a példa időjárási adatkészletet. A példa betöltéséhez válassza az **Igen** lehetőséget.
1. Válassza a **Modell** lapot.
1. Döntési fa létrehozásához válassza a **Végrehajtás** lehetőséget.
1. A **döntési** fa megjelenítéséhez válassza a Rajz lehetőséget.
1. Válassza az **Erdő** lehetőséget, majd a **Végrehajtás** lehetőséget véletlenszerű erdő létrehozásához.
1. Válassza a **Kiértékelés** lapot.
1. Válassza a **Kockázat** lehetőséget, majd a **Végrehajtás** lehetőséget két **kockázati (göngyölt)** teljesítményminta megjelenítéséhez.
1. A **Napló** lapon megjelenik az előző műveletek létrehozott R-kódja.
   (A Rattle aktuális kiadásában található hiba miatt be **#** kell szúrnia egy karaktert a **napló exportálása** elé a napló szövegében.)
1. Az **Exportálás** gombra kattintva mentse a weather_script nevű R *parancsfájlt. R* a saját mappába.

Kiléphet a Rattle és R-ből. Most már módosíthatja a generált R parancsfájlt. Vagy használja a parancsfájlt, ahogy van, és futtassa bármikor, hogy ismételje meg mindent, ami történt a Rattle UI. Különösen a kezdők r, ez egy módja annak, hogy gyorsan nem elemzés és a gépi tanulás egy egyszerű grafikus felület, miközben automatikusan generál kódot R módosítani vagy tanulni.

## <a name="next-steps"></a>További lépések

További kérdései vannak? Fontolja meg egy [támogatási jegy](https://azure.microsoft.com/support/create-ticket/)létrehozását.