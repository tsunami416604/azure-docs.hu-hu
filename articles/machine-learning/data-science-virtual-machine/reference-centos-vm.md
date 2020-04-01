---
title: 'Hivatkozás: CentOS adatelemzési virtuális gép'
titleSuffix: Azure Data Science Virtual Machine
description: A CentOS adattudományi virtuális gépben található eszközök részletei
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: 05da812023e7525b87a3d7ef1ce1940339f99791
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475483"
---
# <a name="reference-centos-linux-data-science-virtual-machine"></a>Hivatkozás: CentOS (Linux) Adattudomány virtuális gép

A Linux Data Science virtuális gép (DSVM) egy CentOS-alapú Azure virtuális gép. A Linux DSVM előre telepített eszközök gyűjteményével érkezik, amelyeket adatelemzéshez és gépi tanuláshoz használhat. 

A Linux DSVM legfontosabb szoftverösszetevői a következők:

* Linux CentOS disztribúciós operációs rendszer.
* Microsoft Machine Learning Server.
* Anaconda Python-disztribúció (3.5-ös és 2.7-es verzió), beleértve a népszerű adatelemzési kódtárakat.
* JuliaPro, a Julia nyelv és a népszerű tudományos és adatelemzési könyvtárak kurátora.
* Spark önálló példány és egycsomópontos Hadoop (HDFS, YARN).
* JupyterHub, egy többfelhasználós Jupyter notebook-kiszolgáló, amely támogatja az R, Python, PySpark és Julia kernelek.
* Az Azure Storage Explorer ben.
* Az Azure CLI, az Azure parancssori felülete az Azure-erőforrások kezeléséhez.
* PostgresSQL adatbázis.
* Gépi tanulási eszközök:
  * [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK) (CNTK), a Microsoft Research deep learning szoftvereszköztára.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit), egy gyors gépi tanulási rendszer, amely támogatja a technikákat, mint az online, hashing, allreduce, csökkentések, learning2search, aktív, és interaktív tanulás.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/), egy eszköz, amely gyors és pontos lendületet fa végrehajtását.
  * [A Rattle](https://togaware.com/rattle/)eszköz megkönnyíti az adatgyűjtés és a gépi tanulás első lépéseit az R-ben. Rattle kínál mind a GUI-alapú adatok feltárása és modellezése segítségével automatikus R-kód generálása.
* Azure SDK Java, Python, Node.js, Ruby és PHP nyelven.
* Az R és python-ban használható tárak az Azure Machine Learningben és más Azure-szolgáltatásokban.
* Fejlesztési eszközök és szerkesztők (RStudio, PyCharm, IntelliJ, Emacs, gedit, vi).

Az adatelemzés magában foglalja a feladatok sorozatának iterálását:

1. Adatok keresése, betöltése és feldolgozás előtti feldolgozása.
1. Modellek et hozhat létre és tesztel.
1. A modellek intelligens alkalmazásokban való felhasználáshoz való üzembe helyezése.

Az adatszakértők különböző eszközöket használnak a feladatok elvégzéséhez. Ez lehet időigényes, hogy megtalálják a megfelelő verziót a szoftver, majd töltse le, fordítsa le, és telepítse a szoftvert.

A Linux DSVM jelentősen enyhítheti ezt a terhet. A Linux DSVM segítségével indítsa el az elemzési projektet. A Linux DSVM segít a különböző nyelveken végzett feladatokon, például az R, Python, SQL, Java és C++ nyelven. Az Eclipse egy könnyen használható IDE-t biztosít a kód fejlesztéséhez és teszteléséhez. Az Azure SDK, a DSVM tartalmazza, segít az alkalmazások létrehozásában a Különböző linuxos szolgáltatások a Microsoft felhőplatform. Más nyelvek előtelepítettek, beleértve a Ruby, Perl, PHP és Node.js.

A DSVM-rendszerkép használatát nem terhelik további szoftverdíjak. Csak az Azure hardverhasználati díjakat, amelyek értékelése alapján a virtuális gép kiépített a DSVM-lemezkép mérete alapján. A számítási díjakról további információt az [Azure Marketplace-en található Data Science Virtual Machine for Linux listázása](https://azure.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) című témakörben talál.


## <a name="machine-learning-server"></a>Gépi tanulási kiszolgáló

Az R az adatelemzés és a gépi tanulás egyik legnépszerűbb nyelve. Ha az R-t szeretné használni az elemzéshez, a DSVM Machine Learning Server és a Microsoft R Open és a Math Kernel Library rendelkezik. A Matematikai kernelkönyvtár optimalizálja az analitikus algoritmusok ban végzett gyakori matematikai műveleteket. Az R Open teljes mértékben kompatibilis a CRAN R-rel. A CRAN-ban közzétett R-könyvtárak bármelyike telepíthető az R Open-re. 

A Machine Learning Server segítségével az R-modelleket webszolgáltatásokká méretezheti és működőképessé teheti. Az R-programokat az egyik alapértelmezett szerkesztőben szerkesztheti, például az RStudio, vi vagy Emacs programban. Az Emacs szerkesztő elő van telepítve a DSVM-en. Az Emacs ESS (Emacs Speaks Statistics) csomag leegyszerűsíti az R fájlokkal való munkát az Emacs szerkesztőben.

Az R konzol kinyitásához írja be a héjat az **R**. Ez a parancs interaktív környezetbe vezet. Az R program fejlesztéséhez általában olyan szerkesztőt használ, mint az Emacs vagy a vi, majd futtatja a parancsfájlokat az R. RStudio-ban, amely teljes grafikus IDE-t kínál az R program fejlesztéséhez.

A DSVM tartalmazza az R-parancsfájlt, amely a [20 legnagyobb R-csomag](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) telepítéséhez használható. Ezt a parancsfájlt futtathatja, ha az R interaktív felületen van. Mint korábban említettük, hogy nyissa meg a felületet, a héj, **írja**R .  

## <a name="python"></a>Python

Az Anaconda Python telepítve van a Python 3.5 és 2.7 környezetekkel. A 2,7-es környezetet _gyökérnek,_ a 3,5-ös környezetet _pedig py35-nek_hívják. Ez a disztribúció tartalmazza az alap Python mellett mintegy 300 a legnépszerűbb matematikai, mérnöki és adatelemzési csomagok.

A py35 környezet az alapértelmezett. A gyökér (2.7) környezet aktiválásához használja ezt a parancsot:

```bash
source activate root
```

A py35 környezet ismételt aktiválásához használja a következő parancsot:

```bash
source activate py35
```

Python interaktív munkamenet meghívásához írja be a **python**. 

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

Az Anaconda disztribúció is jön-val egy Jupyter Notebook, egy környezetet megosztani kódot és elemzést. A Jupyter notebook elérése a JupyterHubon keresztül. A helyi Linux-felhasználónévvel és jelszóval jelentkezik be.

A Jupyter notebook-kiszolgáló előre konfigurálva van a Python 2, Python 3 és R kernelekkel. A **Jupyter Notebook** desktop ikonnal nyissa meg a böngészőt, és férjen hozzá a Jupyter Notebook kiszolgálóhoz. Ha a DSVM-et SSH-on vagy X2Go ügyfélen keresztül éri el,\/a Jupyter Notebook kiszolgálót is elérheti a https: /localhost:8000/ címen.

> [!NOTE]
> Folytassa, ha tanúsítványra vonatkozó figyelmeztetést kap.

A Jupyter notebook-kiszolgáló bármely állomásról elérhető. Írja be **a\//\<https: DSVM DNS-nevét vagy IP-címét\>:8000/**.

> [!NOTE]
> A 8000-es port alapértelmezés szerint meg van nyitva a tűzfalban, amikor a DSVM ki van építve. 

A Microsoft csomagolt minta notebookok, egy Python és egy R. A jupyteri jegyzetfüzet kezdőlapján láthatja a mintákra mutató hivatkozást, miután a helyi Linux-felhasználónév és jelszó használatával hitelesítette a Jupyter notebookot. Új jegyzetfüzet létrehozásához válassza az **Új**lehetőséget, majd jelölje ki a használni kívánt nyelvi rendszermagot. Ha nem látható az **Új** gomb, kattintson a bal felső sarokban lévő **Jupyter** ikonra a jegyzetfüzet-kiszolgáló kezdőlapjának megugrásához.

## <a name="spark-standalone"></a>Szikra önálló 

A Spark standalone mód egy példánya elő van telepítve a Linux DSVM-en, hogy segítsen a Spark-alkalmazások helyi fejlesztésében, mielőtt tesztelene és üzembe helyezne őket nagy fürtökön. 

PySpark-programok futtathatók a Jupyter kernelen keresztül. A Jupyter megnyitásakor jelölje ki az **Új** gombot, és meg kell jelennie az elérhető kernelek listájának. **Spark – A Python** a PySpark kernel, amely lehetővé teszi a Spark-alkalmazások python-nyelv használatával történő összeállítását. A Spark-program létrehozásához használhatja a Python IDE-t is, például a PyCharm-t vagy a Spydert. 

Ebben az önálló példányban a Spark-verem a hívó ügyfélprogramban fut. Ez a funkció gyorsabbá és egyszerűbbé teszi a problémák elhárítását a Spark-fürtön való fejlesztéshez képest.

A Jupyter egy minta PySpark-jegyzetfüzetet biztosít. A SparkML-könyvtárban, a Jupyter ($HOME/notebook/SparkML/pySpark) kezdőkönyvtárában található. 

Ha az R for Spark ban programoz, használhatja a Machine Learning Server, sparkr vagy sparklyr. 

Mielőtt spark-környezetben futtatná a Machine Learning Serverben, egy egyszeri beállítási lépést kell tennie egy helyi egycsomópontos Hadoop HDFS- és YARN-példány engedélyezéséhez. Alapértelmezés szerint a Hadoop-szolgáltatások telepítve vannak, de le vannak tiltva a DSVM-en. A Hadoop-szolgáltatások engedélyezéséhez első alkalommal futtassa a következő parancsokat gyökérként:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

A Hadoopszolgáltatás leállításához, ha nincs rájuk szüksége, `systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn`a futtatásával leállíthatja.

A /dsvm/samples/MRS könyvtár egy minta, amely bemutatja, hogyan fejleszti és teszteli a Machine Learning Server egy távoli Spark-környezetben (az önálló Spark-példány a DSVM).

## <a name="ides-and-editors"></a>IDEs és szerkesztők

Számos kódszerkesztő közül választhat, például a vi/VIM, az Emacs, a Gedit, a PyCharm, az RStudio, az Eclipse, a LaTeX és az IntelliJ. 

* Gedit, Eclipse, IntelliJ, R Studio, és PyCharm grafikus szerkesztők. A használatukhoz be kell jelentkeznie egy grafikus asztalra. Az asztal és az alkalmazás menüjének parancsikonjai segítségével nyithatja meg őket.

* A Vim és az Emacs szövegalapú szerkesztők. Az Emacs-en az ESS kiegészítő csomag megkönnyíti az R-rel való munkát az Emacs szerkesztőben. További információt az [ESS honlapján](https://ess.r-project.org/)talál.

* Eclipse egy nyílt forráskódú, bővíthető IDE, amely támogatja a több nyelven. Eclipse IDE for Java Developers a DSVM-re telepített verzió. A környezet bővítéséhez több népszerű nyelvhez is telepíthet beépülő modulokat. 

  Az Azure Toolkit for Eclipse beépülő modul az Eclipse-rel van telepítve a DSVM-en. Az Azure Toolkit for Eclipse segítségével azure-alkalmazásokat hozhat létre, fejleszthet, tesztelhet és telepíthet az Eclipse fejlesztői környezetben, amely támogatja a Java nyelvet.

  Az Azure SDK Java-hoz is telepítve van az Azure Toolkit for Eclipse a DSVM.The Azure SDK for Java is installed with the Azure Toolkit for Eclipse on the DSVM. Az Azure SDK Java-hoz hozzáférést biztosít a különböző Azure-szolgáltatások egy Java-környezetben. 
  
  További információkért lásd: [Az Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

* A LaTeX a texlive csomagon keresztül van telepítve, valamint egy Emacs kiegészítő csomag, az [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Ez a csomag leegyszerűsíti a LaTeX dokumentumok emacs-i szerkesztését. 

## <a name="databases"></a>Adatbázisok

A Linux DSVM hozzáférést biztosít számos adatbázishoz és parancssori eszközhöz.

### <a name="postgressql"></a>PostgresSQL

A PostgresSQL nyílt forráskódú adatbázis elérhető a DSVM-en, a szolgáltatások futnak és az initdb befejeződött. Létre kell hoznia adatbázisokat és felhasználókat. További információt a [PostgresSQL dokumentációjában](https://www.postgresql.org/docs/)talál.  

### <a name="squirrel-sql"></a>SQuirreL SQL

A SQuirreL SQL egy grafikus SQL-ügyfél, amely különböző adatbázisokhoz tud csatlakozni (beleértve az SQL Servert, a PostgresSQL-t és a MySQL-t), és SQL-lekérdezéseket futtat. A SQuirreL SQL-t grafikus asztali munkamenetből futtathatja (például az X2Go ügyfélen keresztül) egy asztali ikon használatával. Vagy futtathatja az ügyfelet a következő paranccsal a rendszerhéjban:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh    /usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Az első használat előtt állítsa be az illesztőprogramokat és az adatbázis-aliasokat. A JDBC illesztőprogramok a /usr/share/java/jdbcdrivers helyen találhatók.

További információ: [SQuirreL SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-sql-server"></a>Parancssori eszközök az SQL Server eléréséhez

Az SQL Server ODBC illesztőprogram-csomagja két parancssori eszközt is tartalmaz:

* **bcp**: A bcp eszköz tömegesen másolja az adatokat az SQL Server egy példánya és egy, a felhasználó által megadott formátumban megadott adatfájl között. Az bcp eszközzel nagy számú új sort importálhat az SQL Server táblákba, vagy adatokat exportálhat táblákból adatfájlokba. Ha adatokat szeretne behozni egy táblába, a táblához létrehozott formátumfájlt kell használnia. Vagy meg kell értenie a tábla szerkezetét és az oszlopokra érvényes adattípusokat.

  További információ: [Connect with bcp](https://msdn.microsoft.com/library/hh568446.aspx).

* **sqlcmd**: Az sqlcmd segédprogrammal transact-SQL utasításokat, rendszereljárásokat és parancsfájlokat adhat meg a parancssorba. Az sqlcmd segédprogram AZ ODBC segítségével hajtja végre a Transact-SQL kötegeket.

  További információ: [Connect with sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Van néhány különbség ebben az eszközben a Linux és a Windows platformok között. A részleteket lásd a dokumentációban.

### <a name="database-access-libraries"></a>Adatbázis-hozzáférési tárak

Az adatbázis-hozzáférés tárai r- és python-ban érhetők el:

* Az R-ben az RODBC-csomag vagy a dplyr-csomag segítségével lekérdezheti vagy futtathatja az SQL-utasításokat az adatbázis-kiszolgálón.
* A Pythonban a pyodbc könyvtár adatbázis-hozzáférést biztosít az ODBC-vel, mint az alapul szolgáló réteggel.

## <a name="azure-tools"></a>Azure-eszközök

A következő Azure-eszközök vannak telepítve a DSVM-en:

* **Azure CLI:** Az Azure parancssori felületén létrehozhat és kezelhet Azure-erőforrásokat shell parancsokon keresztül. Az Azure-eszközök megnyitásához írja be az **azure súgóját.** További információt az [Azure CLI dokumentációs oldalán talál.](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* **Azure Storage Explorer:** Az Azure Storage Explorer egy grafikus eszköz, amellyel az Azure storage-fiókjában tárolt objektumok között böngészhet, valamint adatokat tölthet fel és tölthet le az Azure-blobokba és onnan. A Storage Explorer az asztal parancsikonjának ikonjáról érhető el. Shell parancssorból is megnyithatja a StorageExplorer ( **StorageExplorer**) parancsba való belépésével. X2Go ügyfélről kell bejelentkeznie, vagy be kell állítania az X11 továbbítást.
* **Azure-kódtárak:** A következő kódtárak elővannak telepítve a DSVM-en:
  
  * **Python**: A Python Azure-ral kapcsolatos kódtárai *az azure,* *az azureml*, *pydocumentdb*és *pyodbc.* Az első három kódtárak, elérheti az Azure storage-szolgáltatások, az Azure Machine Learning és az Azure Cosmos DB (a NoSQL-adatbázis az Azure-ban). A negyedik könyvtár, a pyodbc (az SQL Server Microsoft ODBC illesztőprogramjával együtt) odBC-felülethasználatával engedélyezi az SQL Server, az Azure SQL Database és az Azure SQL Data Warehouse elérését a Pythonból. Írja be **a pip listát** az összes felsorolt tárak megtekintéséhez. Győződjön meg róla, hogy futtassa ezt a parancsot a Python 2.7 és 3.5 környezetben.
  * **R**: Az Azure-ral kapcsolatos tárak R AzureML és RODBC.
  * **Java**: Az Azure Java-kódtárak listája megtalálható a /dsvm/sdk/AzureSDKJava könyvtárban a DSVM-en. A legfontosabb kódtárak az Azure storage és felügyeleti API-k, az Azure Cosmos DB és az SQL Server JDBC-illesztőprogramjai.  

Az Azure [Portalaz](https://portal.azure.com) előre telepített Firefox böngészőből érhető el. Az Azure Portalon azure-erőforrásokat hozhat létre, kezelhet és figyelhet.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Az Azure Machine Learning egy teljes körűen felügyelt felhőszolgáltatás, amely segítségével prediktív elemzési megoldásokat hozhat létre, helyezhet üzembe és oszthat meg. A kísérleteket és a modelleket az Azure Machine Learning Studio (klasszikus) segítségével hozhatja létre. Az Azure Machine Learning dsvm-alapú webböngészőből való eléréséhez nyissa meg a [Microsoft Azure Machine Learning](https://studio.azureml.net)et.

Miután bejelentkezik az Azure Machine Learning Studio (klasszikus), egy kísérletezési vászon segítségével logikai folyamatot hozhat létre a gépi tanulási algoritmusok. Az Azure Machine Learning üzemeltetett Jupyter-jegyzetfüzethez is hozzáférhet. A jegyzetfüzet zökkenőmentesen működhet az Azure Machine Learning Studio (klasszikus) kísérleteivel. 

Működőképessé a gépi tanulási modellek, amelyek et hoz létre a webszolgáltatás-felületen csomagolva őket. A gépi tanulási modellek üzembe írása lehetővé teszi, hogy a bármilyen nyelven írt ügyfelek meghívja az adott modellek től származó előrejelzéseket. További információt a [Machine Learning dokumentációjában](https://azure.microsoft.com/documentation/services/machine-learning/)talál.

A modellek r vagy python a DSVM, majd üzembe helyezheti őket éles környezetben az Azure Machine Learning. A Microsoft telepített könyvtárak R (**AzureML**) és python (**azureml**) támogatja ezt a funkciót.

Az R és Python modellek Azure Machine Learningben való üzembe helyezéséről [a Tíz dolog, amit az adatelemzési virtuális gépen tehet.](vm-do-ten-things.md)

> [!NOTE]
> Az [adatelemzési virtuális gépen tíz dologban](vm-do-ten-things.md) található utasításokat a DSVM Windows-verziójához írták. Azonban a modellek üzembe helyezéséről szóló információk az Azure Machine Learning is vonatkozik a Linux DSVM.

## <a name="machine-learning-tools"></a>Gépi tanulási eszközök

A DSVM néhány gépi tanulási eszközt és algoritmust tartalmaz, amelyek előre levannak fordítva és helyileg előre telepítve vannak. Ezek a következők:

* **Microsoft Cognitive Toolkit**: Mélytanulási eszközkészlet.
* **Vowpal Wabbit**: A gyors online tanulási algoritmus.
* **XGBoost**: Olyan eszköz, amely optimalizált, kiemelt fa algoritmusok.
* **Python:** Anaconda Python jön csomagban gépi tanulási algoritmusok könyvtárak, mint a Scikit-learn. A parancs segítségével más `pip install` könyvtárakat is telepíthet.
* **R**: A gépi tanulási funkciók gazdag könyvtára érhető el az R. Előtelepített könyvtárak közé tartozik lm, glm, randomForest és rpart. Más könyvtárakat is `install.packages(<lib name>)`telepíthet a futtatásával.

A Microsoft Cognitive Toolkit, vowpal Wabbit és XGBoost részletesebben tárgyalja a következő szakaszokban.

### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

A Microsoft Cognitive Toolkit egy nyílt forráskódú deep learning eszközkészlet. Ez egy parancssori eszköz (CNTK), és már a PATH.

Alapminta futtatásához futtassa a következő parancsokat a rendszerhéjban:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

További információt a [GitHub CNTK tárházban](https://github.com/Microsoft/CNTK) és a [CNTK wikiben](https://github.com/Microsoft/CNTK/wiki)talál.

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit egy gépi tanulási rendszer, amely olyan technikákat használ, mint az online, hashing, allreduce, csökkentések, learning2search, aktív és interaktív tanulás.

Ha az eszközt egy egyszerű példán szeretné futtatni, futtassa a következő parancsokat:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

A Vowpal Wabbit demo könyvtár tartalmaz más, nagyobb demók. További információ a Vowpal Wabbit, lásd a [GitHub Vowpal Wabbit repository](https://github.com/JohnLangford/vowpal_wabbit) és a [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>Xgboost

Az XGBoost könyvtárat a kiemelt (fa) algoritmusokra tervezték és optimalizálták. Az XGBoost könyvtár célja, hogy a gépek számítási korlátait a méretezhető, hordozható és pontos nagyméretű fakiemelés biztosításához szükséges szélsőségekre tolja.

Az XGBoost parancssorból és R könyvtárként érhető el.

Az R XGBoost könyvtárának használatához indítson el egy interaktív R-munkamenetet (a rendszerhéjban írja be az **R**értéket), majd töltse be a könyvtárat.

Íme egy egyszerű példa, amelyet futtathat az R parancssorban:

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

Az XGBoost parancssor futtatásához futtassa a következő parancsokat a rendszerhéjban:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

A .model fájl a megadott könyvtárba íródik. Erről a bemutatópéldáról a GitHubon a [Bináris besorolás című](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification)témakörben talál további információt.

Az XGBoost-ról további információt az [XGBoost dokumentációjában](https://xgboost.readthedocs.org/en/latest/) és az [XGBoost GitHub tárházban](https://github.com/dmlc/xgboost)talál.

### <a name="rattle"></a>Rattle

Rattle *(R* *A*nalytical *T*ool *T*o *L*keresni *E*könnyen) használ GUI-alapú adatok feltárása és modellezése. Csörgő:
- Az adatok statisztikai és vizuális összegzését mutatja be.
- Átalakítja a könnyen modellezhető adatokat.
- Felügyelet nélküli és felügyelt modelleket is készít az adatokból.
- Grafikusan mutatja be a modellek teljesítményét.
- Új adatkészletek felsorakezése.
- R-kódot hoz létre.
- Replikálja a felhasználói felületen futtatható műveleteket, amelyek közvetlenül az R-ben futtathatók, vagy további elemzés kiindulópontjaként használhatók.

A Rattle futtatásához be kell jelentkeznie egy grafikus asztali munkamenetbe. A terminálon adja meg az **R** értéket az R környezet megnyitásához. Az R parancssorba írja be a következő parancsokat:

```R
library(rattle)
rattle()
```

Megnyílik egy grafikus felület, amely lapkészlettel rendelkezik. A Rattle következő rövid útmutató lépései segítségével használjon egy időjárási adatkészletet, és hozzon létre egy modellt. Néhány lépésben a rendszer kéri, hogy automatikusan telepítsen és töltsön be néhány szükséges R-csomagot, amelyek még nem szerepelnek a rendszeren.

> [!NOTE]
> Ha nincs engedélye a csomag nak a rendszerkönyvtárba való telepítéséhez (ez az alapértelmezett), az R konzol ablakában megjelenik egy üzenet a csomagok személyes könyvtárba való telepítésére. Ha megjelennek ezek a kérések, írja be **az y**.

1. Válassza a **Végrehajtás** lehetőséget.
1. Egy párbeszédpanel en a példa időjárási adatkészletének betöltése. A példa betöltéséhez válassza az **Igen** lehetőséget.
1. Válassza a **Modell** lapot.
1. Döntési fa létrehozásához válassza a **Végrehajtás** lehetőséget.
1. A **döntési** fa megjelenítéséhez válassza a Rajz lehetőséget.
1. Válassza az **Erdő** beállítást, majd a **Végrehajtás** lehetőséget véletlenszerű erdő létrehozásához.
1. Válassza a **Kiértékelés** lapot.
1. Válassza a **Kockázat** beállítást, majd a **Végrehajtás** lehetőséget két **kockázati (göngyölt)** teljesítményminta megjelenítéséhez.
1. A **Napló** lapon megjelenik az előző műveletek létrehozott R-kódja. (A Rattle aktuális kiadásában található hiba miatt **#** be kell szúrnia egy karaktert a **napló exportálása** elé a napló szövegében.)
1. Az **Exportálás** gombra kattintva mentse a weather_script nevű R *parancsfájlt. R* a saját mappába.

Kiléphet a Rattle és R-ből. Most már módosíthatja a generált R parancsfájlt. Vagy használja a parancsfájlt, ahogy van, és futtassa bármikor, hogy ismételje meg mindent, ami történt a Rattle UI. Különösen a kezdők r, ez egy módja annak, hogy gyorsan nem elemzés és a gépi tanulás egy egyszerű grafikus felület, miközben automatikusan generál kódot R módosítani vagy a tanulás.

## <a name="next-steps"></a>További lépések

További kérdései vannak? Fontolja meg egy [támogatási jegy](https://azure.microsoft.com/support/create-ticket/)létrehozását.