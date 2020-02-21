---
title: 'Hivatkozás: Ubuntu Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Az Ubuntu Data Science Virtual Machineban található eszközök részletei
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: 5c184e7f1dc828c3f9ff8d449d29ab3aaa4d1cf6
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525821"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>Hivatkozás: Ubuntu (Linux) Data Science Virtual Machine

Az Ubuntu Data Science Virtual Machine elérhető eszközeinek listáját alább találja. 

## <a name="deep-learning-libraries"></a>Deep learning-kódtárak

### <a name="cntk"></a>CNTK

A Microsoft Cognitive Toolkit egy nyílt forráskódú Deep learning-eszközkészlet. Python-kötések a legfelső szintű és py35 Conda-környezetekben érhető el. Emellett egy olyan parancssori eszközzel (CNTK) is rendelkezik, amely már szerepel az elérési úton.

Python mintafüzetek JupyterHub érhető el. Ha alapszintű mintát szeretne futtatni a parancssorban, futtassa a következő parancsokat a rendszerhéjban:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

További információkért lásd a [GitHub](https://github.com/Microsoft/CNTK) és a [CNTK wiki](https://github.com/Microsoft/CNTK/wiki)CNTK szakaszát.

### <a name="caffe"></a>Caffe

Caffe egy olyan deep learning keretrendszer, Berkeley Látástechnológiai és oktatóközpont. A/opt/caffe.-ben érhető el Példákat talál a/opt/caffe/examples.

### <a name="caffe2"></a>Caffe2

Caffe2 egy olyan deep learning keretrendszer, a Facebook, a Cafféhoz épül. A Python 2,7-es verzióban érhető el a Conda gyökérszintű környezetben. Az aktiválásához futtassa a következő parancsot a rendszerhéjból:

```bash
source /anaconda/bin/activate root
```

Néhány példa notebookok JupyterHub érhető el.

### <a name="h2o"></a>H2O

H2O egy gyors, a memóriában, elosztott gépi tanulással és prediktív elemzési platform. Egy Python-csomag telepítve van a gyökér- és a py35 Anaconda-környezetekben. Egy R-csomag is telepítve van. 

A H2O parancssorból való megnyitásához futtassa a `java -jar /dsvm/tools/h2o/current/h2o.jar`. Több [parancssori kapcsolót](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) is beállíthat. A folyamat webes felhasználói felületének eléréséhez tallózással keresse meg http://localhost:54321 a kezdéshez. JupyterHub mintafüzetek is érhetők el.

### <a name="keras"></a>Keras

A kerasz egy magas szintű neurális hálózati API a Pythonban. TensorFlow, Microsoft Cognitive Toolkit vagy theano-on is futtatható. Ez a gyökér-és py35 Python-környezetekben érhető el.

### <a name="mxnet"></a>MXNet

MXNet a teljesítmény és a rugalmasság a deep learning-keretrendszert. A dsvm-hez a tartalmazzák R és Python-kötések rendelkezik. Mintafüzetek JupyterHub szerepelnek, és a mintakódot /dsvm/samples/mxnet érhető el.

### <a name="nvidia-digits"></a>NVIDIA SZÁMJEGY

Az NVIDIA Deep learning GPU képzési rendszer, az úgynevezett SZÁMJEGYek, egy olyan rendszer, amely leegyszerűsíti a közös mély tanulási feladatokat. Ezen feladatok közé tartozik az adatok kezelése, a neurális hálózatok tervezése és betanítása a GPU-rendszereken, valamint a teljesítmény valós idejű figyelése a speciális vizualizációkkal.

A SZÁMJEGYek *számjegyek nevű*szolgáltatásként érhetők el. Indítsa el a szolgáltatást, és keresse meg http://localhost:5000 a kezdéshez.

SZÁMJEGYEK is telepítve van a Conda legfelső szintű környezetben Python modulként.

### <a name="tensorflow"></a>TensorFlow

Tensorflow-hoz a Google deep learning-kódtár. Ez egy nyílt forráskódú, az adatfolyam-diagramok használatával numerikus számításhoz használható könyvtár. Tensorflow-hoz elérhető py35 Python-környezetet, és néhány mintafüzetek JupyterHub szerepelnek.

### <a name="theano"></a>Theano

Theano egy Python-kódtár, hatékony numerikus törölje a számításhoz. Ez a gyökér-és py35 Python-környezetekben érhető el. 

### <a name="torch"></a>Torch

Torch egy olyan tudományos számítási keretrendszer gépi tanulási algoritmusok széles körű támogatásával. A/dsvm/Tools/Torch-ben érhető el, az **interaktív munkamenet** és a LuaRocks csomagkezelő pedig a parancssorban érhető el. Példák /dsvm/samples/torch érhető el.

A legfelső szintű Anaconda környezetben PyTorch is érhető el. Példák /dsvm/samples/pytorch találhatók.

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning-kiszolgáló

Az R a legnépszerűbb nyelvek adatok elemzési és gépi tanulás. Ha az R-t szeretné használni az elemzéshez, a virtuális gép Microsoft Machine Learning Server a Microsoft R Open és a Math kernel Library használatával. A matematikai kernel könyvtára az analitikai algoritmusokban közös matematikai műveleteket optimalizálja. A Microsoft R Open 100 százalékkal kompatibilis a CRAN R-vel, és a CRANban közzétett R-kódtárak bármelyike telepíthető a Microsoft R Open szolgáltatásba. 

A Machine Learning Server az R-modellek méretezését és operacionalizálási is lehetővé teszi a webszolgáltatásokban. A R-programokat egy, az az alapértelmezett szerkesztők, mint az RStudio, a vi vagy a Emacs szerkesztheti. Ha inkább a Emacs-szerkesztő használatával, előre telepítve lett. Az Emacs ESS (Emacs Speaks Statistics) csomag leegyszerűsíti az R-fájlok használatát az Emacs editoron belül.

Az R-konzol megnyitásához írja be az **r** értéket a rendszerhéjba. Ez a parancs interaktív környezetbe kerül. Az R-program fejlesztéséhez általában egy olyan szerkesztőt használ, mint például az Emacs vagy a VI, majd futtatnia kell a parancsfájlokat az R-n belül. A RStudio teljes grafikus IDE-val rendelkezik az R-program fejlesztéséhez.

Az R-szkripttel az [első 20 R-csomagot](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) is telepítheti, ha szeretné. Ezt a szkriptet akkor futtathatja, ha az R interaktív felületén van. Ahogy korábban említettük, a felületet megnyithatja az **R** megadásával a shellben.  

## <a name="python"></a>Python

Anaconda Python a Python 2.7-es és 3.5-ös környezetben van telepítve. Az 2,7-es környezet neve _root_, a 3,5-környezet neve pedig _py35_. Ehhez a terjesztéshez körülbelül 300 a legnépszerűbb matematikai, a mérnöki csapathoz és az analitikai csomagok, valamint az alap Python tartalmazza.

A py35 környezetben az alapértelmezett érték. A root (2,7) környezet aktiválásához használja a következő parancsot:

```bash
source activate root
```

A py35-környezet ismételt aktiválásához használja a következő parancsot:

```bash
source activate py35
```

A Python interaktív munkamenetének meghívásához írja be a **Pythont** a rendszerhéjba. 

További Python-kódtárak telepítése a Conda vagy a pip használatával. A pip esetében először aktiválja a megfelelő környezetet, ha nem szeretné, hogy az alapértelmezett:

```bash
source activate root
pip install <package>
```

Vagy adja meg a pip teljes elérési útja:

```bash
/anaconda/bin/pip install <package>
```

A Conda esetében mindig adja meg a környezet nevét (py35 vagy root):

```bash
conda install <package> -n py35
```

Ha grafikus felületen vagy X11-továbbítással rendelkezik, a **notebookshoz** megadásával nyithatja meg a NOTEBOOKSHOZ Python ide-t. Az alapértelmezett szövegszerkesztő is használhatja. Emellett a Spyder, a Python IDE-t is használhatja, amely az anaconda Python-disztribúciókkal együtt van csomagolva. Spyder kell egy grafikus asztali vagy X11-továbbítást. A grafikus asztali számítógép a Spyder-ra mutató parancsikont tartalmaz.

## <a name="jupyter-notebook"></a>Jupyter Notebook

A Anaconda terjesztési egy Jupyter notebookot, megosztani a kódot és az elemzési környezet is tartalmaz. A Jupyter notebook JupyterHub keresztül érhető el. A bejelentkezéshez a helyi Linux-felhasználónevet és-jelszót kell használnia.

A Jupyter notebook server Python 2, a Python 3 és az R kernelekkel előre van konfigurálva. A **Jupyter notebook** asztal ikon segítségével nyissa meg a böngészőt, és nyissa meg a notebook-kiszolgálót. Ha a virtuális gépet SSH-n vagy a X2Go-ügyfélen keresztül futtatja, akkor a Jupyter notebook-kiszolgálót is elérheti [https://localhost:8000/](https://localhost:8000/)címen.

> [!NOTE]
> Folytassa, ha bármely tanúsítvánnyal kapcsolatos figyelmeztetéseket kap.

A Jupyter notebook server bármely állomásról is elérheti. Adja meg a **https://\<virtuális gép DNS-nevét vagy IP-címét\>: 8000/** .

> [!NOTE]
> 8000-es port nyílik meg a tűzfal alapértelmezés szerint amikor a virtuális gép ki van építve. 

Becsomagoltunk egy minta jegyzetfüzetet – egy Pythonban és egy az R-ben. A minták hivatkozását a notebook kezdőlapján láthatja, miután a helyi linuxos felhasználónevével és jelszavával hitelesíti magát a Jupyter notebookon. Új jegyzetfüzet létrehozásához válassza az **új**lehetőséget, majd válassza ki a megfelelő nyelvi kernelt. Ha nem jelenik meg az **új** gomb, a bal felső sarokban található **Jupyter** ikonra kattintva lépjen a notebook-kiszolgáló kezdőlapjára.

## <a name="apache-spark-standalone"></a>Önálló Apache Spark

A Linux DSVM a Apache Spark önálló példánya van előtelepítve a Spark-alkalmazások helyi fejlesztéséhez a nagyméretű fürtökön való tesztelés és üzembe helyezésük előtt. 

A Jupyter kernel keresztül PySpark programok futtathatók. A Jupyter megnyitásakor válassza az **új** gombot, és az elérhető kernelek listáját kell megjelennie. A **Spark-Python** a PySpark kernel, amellyel Spark-alkalmazásokat hozhat létre a Python nyelv használatával. A Spark-programot a Python IDE, például a Notebookshoz vagy a Spyder használatával is létrehozhatja. 

Ebben az önálló példányban a Spark stack a hívó ügyfélprogramon belül fut. Ez a funkció a Spark-fürtön való fejlesztéshez képest gyorsabbá és egyszerűbbé teszi a problémák megoldását.

A Jupyter egy minta PySpark notebookot biztosít. A SparkML könyvtárban található a Jupyter ($HOME/notebooks/SparkML/pySpark) könyvtárában. 

Ha az R for Spark-t használja, használhatja a Microsoft Machine Learning Server, a Sparker vagy a sparklyr. 

Mielőtt a-t a Microsoft Machine Learning Server Spark-környezetében futtatja, egy egyszeri telepítési lépést kell elvégeznie, amely lehetővé teszi egy helyi egycsomópontos Hadoop-HDFS és egy fonal-példány engedélyezését. Alapértelmezés szerint Hadoop-szolgáltatásokhoz vannak telepítve, de a dsvm-hez a letiltva. Az engedélyezéshez először a következő parancsokat kell futtatnia az első alkalommal:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

A Hadoop kapcsolódó szolgáltatásokat leállíthatja, ha nincs szüksége rájuk a ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```futtatásával.

A/dsvm/samples/MRS könyvtár egy olyan mintát biztosít, amely bemutatja, hogyan fejleszthető és tesztelhető Microsoft Machine Learning Server egy távoli Spark-környezetben (az önálló Spark-példány a DSVM).

## <a name="ides-and-editors"></a>Ide-ket és a szerkesztőknek

Számos Kódszerkesztő közül választhat, például a VI/Vim, az Emacs, a Notebookshoz, a RStudio és a IntelliJ. 

A Notebookshoz, a RStudio és a IntelliJ grafikus szerkesztők. A használatához be kell jelentkeznie egy grafikus asztalra. Az asztal és az alkalmazás menü parancsikonjainak használatával nyithatja meg őket.

A Vim és az Emacs szöveg-alapú szerkesztők. Az Emacs-ben az ESS-bővítmény csomag megkönnyíti az R használatát az Emacs editoron belül. További információkat az [ESS webhelyén](https://ess.r-project.org/)talál.

A LaTex a texlive-csomagon keresztül települ, valamint egy [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html)nevű Emacs-bővítményi csomaggal. Ez a csomag egyszerűsíti a LaTex-dokumentumok készítését az Emacson belül.  

## <a name="databases"></a>Adatbázisok

### <a name="graphical-sql-client"></a>Grafikus SQL-ügyfél

A mókus SQL egy grafikus SQL-ügyfél, amely különböző adatbázisokhoz (például Microsoft SQL Server és MySQL) tud csatlakozni, és SQL-lekérdezéseket futtat. A mókus SQL-t futtathatja grafikus asztali munkamenetből (például a X2Go-ügyfélen keresztül) egy asztali ikon használatával. Vagy futtathatja az ügyfelet a következő parancs használatával a rendszerhéjban:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Az első használata előtt az illesztőprogramok és adatbázis-aliasok beállítása. A JDBC-illesztőprogramok a következő helyen találhatók:/usr/share/Java/jdbcdrivers.

További információ: [mókus SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>A Microsoft SQL Server elérése szolgáló parancssori eszközök

Az ODBC illesztőprogram-csomagot az SQL Server is két parancssori eszközöket is tartalmaz:

- **BCP**: a BCP-eszköz tömegesen másol egy Microsoft SQL Server egy példánya és egy, a felhasználó által megadott formátumban tárolt adatfájl között. A BCP eszközzel nagy számú új sort importálhat SQL Server táblákba, vagy exportálhatja a táblákból az adatokból az adatfájlokat. Az adattáblázatba való importáláshoz az adott táblához létrehozott formátumú fájlt kell használnia. Vagy tisztában kell lennie a tábla struktúrájával és az oszlopokhoz érvényes adattípusokkal.

  További információ: [Csatlakozás a BCP-vel](https://msdn.microsoft.com/library/hh568446.aspx).

- **Sqlcmd**: a Sqlcmd eszközzel megadhatja a Transact-SQL-utasításokat. A parancssorban megadhatja a rendszerfolyamatokat és a parancsfájlokat is. Ez az eszköz az ODBC-t használja a Transact-SQL-kötegek futtatásához.

  További információ: [Csatlakozás a Sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx)-hoz.

  > [!NOTE]
  > Ez az eszköz a Linux és a Windows platform közötti különbségeket is tárgyalja. A részletek dokumentációjában talál.

### <a name="database-access-libraries"></a>Adatbázis-hozzáférés kódtárak

A kódtárak az R és a Python alkalmazásban érhetők el az adatbázis-hozzáféréshez:

* Az R-ben a RODBC csomag vagy dplyr csomag használatával kérdezheti le és futtathatja az SQL-utasításokat az adatbázis-kiszolgálón.
* A Pythonban a pyodbc függvénytár adatbázis-hozzáférést biztosít az ODBC-vel az alapul szolgáló rétegként.  

## <a name="azure-tools"></a>Azure-eszközök

A következő Azure-eszközök telepítve vannak a virtuális gépen:

* **Azure CLI**: az Azure parancssori felületének használatával Azure-erőforrásokat hozhat létre és kezelhet a rendszerhéj-parancsokkal. Az Azure-eszközök megnyitásához adja meg az **Azure súgóját**. További információkért tekintse meg az [Azure CLI dokumentációs oldalát](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Azure Storage Explorer**: a Azure Storage Explorer egy grafikus eszköz, amellyel böngészhet az Azure Storage-fiókban tárolt objektumokon, valamint az Azure-blobokból és az onnan érkező adatok feltöltésével és letöltésével. Storage Explorer asztali parancsikonjára ikonra kattintva érheti el. A **StorageExplorer**megadásával is megnyithatja azt egy rendszerhéj-parancssorból. Be kell jelentkeznie egy X2Go-ügyfélről, vagy be kell állítania az X11-továbbítást.
* **Azure-kódtárak**: az alábbiakban néhány előre telepített függvénytár található.
  
  * **Python**: az Azure-hoz kapcsolódó kódtárak a Pythonban az *Azure*, a *azureml*, a *pydocumentdb*és a *pyodbc*. Az első három kódtárakat elérheti az Azure storage services, Azure Machine Learning és az Azure Cosmos DB (NoSQL-adatbázis az Azure-ban). A negyedik kódtára, pyodbc (valamint a Microsoft ODBC-illesztőprogram SQL Serverhez), lehetővé teszi a hozzáférést az SQL Server, az Azure SQL Database és az Azure SQL Data Warehouse a Python egy ODBC-felület használatával. Az összes felsorolt könyvtár megjelenítéséhez adja meg a **pip-listát** . Győződjön meg arról, a parancs futtatása a Python 2.7-es és 3.5-ös környezetben.
  * **R**: az Azure-beli, r-hez kapcsolódó kódtárak a AzureML és a RODBC.
  * **Java**: az Azure Java-kódtárak listája a virtuális gép címtárának/dsvm/SDK/AzureSDKJava található. A kulcs függvénytárak is az Azure storage és a felügyeleti API-k, az Azure Cosmos DB és a JDBC illesztőprogramok az SQL Server.  

A [Azure Portal](https://portal.azure.com) az előre telepített Firefox böngészőből érhető el. Az Azure Portalon létrehozása, kezelése és monitorozása az Azure-erőforrások.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Az Azure Machine Learning egy teljes körűen felügyelt felhőszolgáltatás, amely lehetővé teszi, hogy elkészítheti, telepítheti és oszthat meg prediktív elemzési megoldásokat. A kísérleteket és modelleket Azure Machine Learning Studio (klasszikus) használatával hozhatja létre. A Data Science Virtual Machine webböngészőjéből elérheti a [Microsoft Azure Machine learning](https://studio.azureml.net)meglátogatásával.

Miután bejelentkezett Azure Machine Learning Studioba (klasszikus), a kísérletezési vászon használatával létrehozhat egy logikai folyamatot a gépi tanulási algoritmusokhoz. A Azure Machine Learning üzemeltetett Jupyter-jegyzetfüzethez is hozzáférhet, és zökkenőmentesen dolgozhat Azure Machine Learning Studio (klasszikus) kísérletekkel. 

A machine learning-modellek, amelyek azokat a web service-felülethez alkalmazásburkoló által létrehozott működésbe. A végrehajtott Machine learning-modellek lehetővé teszik bármely nyelven írt ügyfelek számára az előrejelzések meghívását ezekből a modellből. További információ: [Machine learning dokumentáció](https://azure.microsoft.com/documentation/services/machine-learning/).

Az R-vagy Python-modelleket a virtuális gépen is létrehozhatja, majd üzembe helyezheti őket éles környezetben Azure Machine Learningon. A funkció engedélyezéséhez az R (**AzureML**) és a Python (**AzureML**) függvénytárait telepítettük.

Az R-és Python-modellek Azure Machine Learningba való üzembe helyezésével kapcsolatos információkért lásd: [tíz dolog, amit elvégezhet a Data Science Virtual Machine](vm-do-ten-things.md).

> [!NOTE]
> Ezek az utasítások a Data Science Virtual Machine Windows-verziójára lettek írva. De az Azure Machine Learning modellek üzembe helyezéséhez nincs alkalmazható a Linux rendszerű virtuális géphez megadott információk.

## <a name="machine-learning-tools"></a>Machine learning eszközök

A virtuális gép olyan gépi tanulási eszközöket és algoritmusokat tartalmaz, amelyek előre le lettek fordítva, és helyileg lettek telepítve. Ezek a következők:

* **Vowpal Wabbit**: gyors online tanulási algoritmus.
* **xgboost**: olyan eszköz, amely optimalizált, növelt fa algoritmusokat biztosít.
* **Csörgő**: egy R-alapú grafikus eszköz, amely megkönnyíti az adatfeltárást és a modellezést.
* **Python**: a anaconda Python a gépi tanulási algoritmusokkal, például a Scikit-Learn csomaggal rendelkezik. A `pip install` parancs használatával más kódtárakat is telepíthet.
* **LightGBM**: gyors, elosztott, nagy teljesítményű színátmenet-növelő keretrendszer döntési fa algoritmusok alapján.
* **R**: a gépi tanulási függvények széles tárháza érhető el az r-hez. az előre telepített kódtárak közé tartozik az LM, a GLM, a randomForest és a rpart. A következő parancs futtatásával más kódtárakat is telepíthet:
  
        install.packages(<lib name>)

Íme néhány a lista első három machine learning-eszközökkel kapcsolatos további információkat.

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit egy gépi tanulási rendszer által használt módszerek például online, kivonatoló, allreduce, csökkentésének, learning2search, aktív, és az interaktív tanulás.

Az eszköz alapszintű példán való futtatásához használja a következő parancsokat:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Nincsenek más, nagyobb bemutatók ebben a könyvtárban. A Vowpal Wabbit kapcsolatos további információkért tekintse meg a GitHub és a [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki) [jelen szakaszát](https://github.com/JohnLangford/vowpal_wabbit) .

### <a name="xgboost"></a>xgboost

A xgboost-könyvtár kialakítva és optimalizálva lett a kifejleszthető (fa) algoritmusokhoz. Ebben a könyvtárban célja a gépek a számítási korlátokat leküldése a szélsőséges szükség ahhoz, hogy nagyméretű fa kiemelése, amely méretezhető, hordozható és pontos van.

Ez a parancs parancssorként és R-függvénytárként is elérhető. Ha ezt a kódtárat szeretné használni az R-ben, elindíthat egy interaktív R-munkamenetet (az **r** beírásával a rendszerhéjban), és betöltheti a könyvtárat.

Íme egy egyszerű példa, amely az R-parancssorban is futtatható:

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

A xgboost parancssor futtatásához a következő parancsokat kell futtatni a rendszerhéjban:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

A rendszer a megadott könyvtárba írja a. Model fájlt. Erről a bemutató példáról [a githubon](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification)talál információt.

A xgboost kapcsolatos további információkért tekintse meg a [xgboost dokumentációs oldalát](https://xgboost.readthedocs.org/en/latest/) és a [GitHub-tárházat](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Rattle

A csörgő (az **R** **a**analitikus **t**OOL **t**o **L**keres **E**asily) GUI-alapú adatelemzést és modellezést használ. Jelenítse meg az adatokat, azonnal modellezhető, felügyeletlen és a felügyelt modellek adatokból hoz létre, a modellek teljesítményének grafikusan, megadja átalakítások adatok statisztikai és vizuális összegzéseket, és beállítja a pontszámok új adatokat. R-kód, a műveletek a felhasználói felület, amely közvetlenül az R futtatható, illetve kiindulási pontként Microsoft további elemzésnek veti replikálása is állít elő.

Rattle futtatásához, meg kell lenniük egy grafikus asztali bejelentkezési munkamenet. A terminálon írja be az **r** parancsot az r-környezet megnyitásához. Az R a parancssorba írja be a következő parancsokat:

```R
library(rattle)
rattle()
```

Ekkor megnyílik egy grafikus felület, amely fülekből áll. A következő rövid útmutató lépéseit használhatja a Csörgőben egy minta időjárási adathalmaz használatához és egy modell létrehozásához. Néhány lépés során a rendszer felszólítja, hogy automatikusan telepítsen és töltsön be néhány szükséges R-csomagot, amely még nem szerepel a rendszeren.

> [!NOTE]
> Ha nem rendelkezik hozzáféréssel a csomag telepítéséhez a rendszerkönyvtárban (ez az alapértelmezett beállítás), az R-konzolon megjelenik egy üzenet, amely a csomagokat a személyes tárba telepíti. Ha ezeket az utasításokat látja, válaszoljon az **y** -re.

1. Válassza a **Végrehajtás** lehetőséget.
1. Megjelenik egy párbeszédpanel, amely megkérdezi, hogy szeretné-e használni a példa időjárási adatkészletet. A példa betöltéséhez válassza az **Igen** lehetőséget.
1. Válassza a **modell** fület.
1. Döntési fa létrehozásához válassza a **végrehajtás** elemet.
1. Válassza a **Rajzolás** lehetőséget a döntési fa megjelenítéséhez.
1. Válassza az **erdő** lehetőséget, majd válassza a **végrehajtás** lehetőséget egy véletlenszerű erdő kiépítéséhez.
1. Válassza ki a **kiértékelés** lapot.
1. Válassza ki a **kockázati** lehetőséget, majd válassza a **végrehajtás** elemet a két **kockázatos (halmozott)** teljesítménybeli ábrázolás megjelenítéséhez.
1. Válassza a **napló** lapot, hogy megjelenjen az előző műveletekhez generált R-kód.
   (A csörgő jelenlegi kiadásának hibája miatt be kell szúrnia egy **#** karaktert a log szövegbe való **Exportálás** előtt.)
1. A weather_script nevű R-parancsfájl mentéséhez kattintson az **Exportálás** gombra *. Az R* -t a kezdőmappa mappájába.

Kiléphet a csörgő és az R lehetőségből. Most már módosíthatja a generált R-szkriptet. Használhatja a parancsfájlt is, és bármikor futtathatja, hogy megismételje a csörgő felhasználói felületen végzett összes műveletet. Különösen az R-ben kezdőknek, így gyorsan elvégezheti az elemzést és a gépi tanulást egy egyszerű grafikus felületen, az R-ben pedig automatikusan generálhat kódot a módosításhoz vagy a megismeréshez.

## <a name="next-steps"></a>Következő lépések

További kérdései vannak? Hozzon létre egy [támogatási jegyet](https://azure.microsoft.com/support/create-ticket/).