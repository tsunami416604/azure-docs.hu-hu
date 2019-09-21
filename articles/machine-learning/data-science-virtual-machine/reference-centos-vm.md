---
title: 'Leírások: CentOS DSVM'
description: A CentOS Data Science Virtual Machineban található eszközök részletei
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: 9366eb1bde05d80b8882ee28aa9eb03a75f75964
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174720"
---
# <a name="reference-centos-linux-data-science-virtual-machine"></a>Leírások: CentOS (Linux) Data Science Virtual Machine

A Linux Data Science Virtual Machine (DSVM) egy CentOS-alapú Azure-beli virtuális gép. A Linux DSVM olyan előre telepített eszközöket tartalmaz, amelyeket az adatelemzéshez és a gépi tanuláshoz használhat. 

A Linux-DSVM található legfontosabb szoftver-összetevők a következők:

* Linux CentOS Distribution operációs rendszer.
* Microsoft Machine Learning Server.
* Anaconda Python-disztribúció (3,5-es és 2,7-es verziók), beleértve a népszerű adatelemző kódtárakat.
* JuliaPro, a Júlia nyelvének és a népszerű tudományos és adatelemzési könyvtáraknak a kurátori eloszlása.
* Spark önálló példány és egy csomópontos Hadoop (HDFS, fonal).
* JupyterHub, egy többfelhasználós Jupyter notebook-kiszolgáló, amely támogatja az R, Python, PySpark és Julia kerneleket.
* Azure Storage Explorer.
* Az Azure CLI, az Azure parancssori felülete az Azure-erőforrások kezeléséhez.
* PostgresSQL-adatbázis.
* Gépi tanulási eszközök:
  * [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK) (CNTK), a Microsoft Research mély tanulási szoftveres eszközkészlete.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit), egy gyors gépi tanulási rendszer, amely olyan technikákat támogat, mint például az online, a kivonatolás, a allreduce, a csökkenés, a learning2search, az aktív és az interaktív tanulás.
  * A [XGBoost](https://xgboost.readthedocs.org/en/latest/)egy olyan eszköz, amely gyors és pontos, lendületes faszerkezetes megvalósítást biztosít.
  * A [Rattle](https://togaware.com/rattle/)egy olyan eszköz, amely megkönnyíti az adatelemzést és a gépi tanulást az R Easy szolgáltatásban. A Rattle a grafikus felhasználói felületen alapuló adatelemzést és-modellezést is biztosít az R-kód automatikus generálásával.
* Azure SDK a Java, a Python, a Node. js, a Ruby és a PHP használatával.
* Az R-és Python-kódtárak Azure Machine Learning és más Azure-szolgáltatásokban való használatra.
* Fejlesztői eszközök és szerkesztők (RStudio, Notebookshoz, IntelliJ, Emacs, gedit, VI).

Az adatelemzés magában foglalja a feladatok egy sorozatát léptetés:

1. Megkeresheti, betöltheti és előre feldolgozhatja az adatfeldolgozást.
1. Hozhat létre, és tesztelni tudtuk.
1. A modellek intelligens alkalmazásokban felhasználásra üzembe helyezése.

Az adatszakértők a különböző eszközök segítségével ezeket a feladatokat. Időigényes lehet a szoftver megfelelő verzióinak megkeresése, majd a szoftver letöltése, fordítása és telepítése.

A Linux-DSVM jelentősen megkönnyíti ezt a terhet. A Linux DSVM használatával ugorjon az elemzési projektre. A Linux-DSVM különböző nyelveken, például az R, a Python, az SQL, a Java és C++a-ben végzett feladatok elvégzésében nyújt segítséget. Az Eclipse egy könnyen használható IDE-kódot biztosít a kód fejlesztéséhez és teszteléséhez. Az Azure SDK, amely a DSVM része, a Microsoft Cloud platformon különböző Linux-szolgáltatások használatával hozhat létre alkalmazásokat. Más nyelvek előre telepítve vannak, például a Ruby, a Perl, a PHP és a Node. js.

A DSVM-rendszerképhez nem tartoznak szoftveres díjak. A DSVM-lemezképpel kiépített virtuális gép méretétől függően csak az Azure-beli hardver használati díját kell megfizetnie. A számítási díjakkal kapcsolatos további információkért tekintse meg az Azure Marketplace-en [Data Science Virtual Machine for Linux (CentOS) listázását](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) .


## <a name="machine-learning-server"></a>Machine Learning Server

Az R a legnépszerűbb nyelvek adatok elemzési és gépi tanulás. Ha az R-t szeretné használni az elemzéshez, a DSVM Machine Learning Server a Microsoft R Open és a Math kernel Library használatával. A matematikai kernel könyvtára a gyakori matematikai műveleteket az analitikai algoritmusokban optimalizálja. Az r Open teljes mértékben kompatibilis a CRAN R-vel. A CRAN-ben közzétett R-kódtárak bármelyike telepíthető R Openre. 

Az R-modellek webszolgáltatásokban való méretezésére és működővé tenni használhatja a Machine Learning Server. A R-programokat egy, az az alapértelmezett szerkesztők, mint az RStudio, a vi vagy a Emacs szerkesztheti. Az Emacs szerkesztő előre telepítve van a DSVM. Az Emacs ESS (Emacs Speaks Statistics) csomag leegyszerűsíti az R-fájlok használatát az Emacs szerkesztőben.

Az R-konzol megnyitásához a rendszerhéjban írja be az **r**értéket. Ez a parancs interaktív környezetbe kerül. Az R-program fejlesztéséhez általában egy olyan szerkesztőt használ, mint az Emacs vagy a VI, majd az R. RStudio futtatja a szkripteket az r. teljes grafikus IDE-t kínál az R-program fejlesztéséhez.

Az [első 20 r csomag](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) telepítéséhez használható r-szkriptet a DSVM tartalmazza. Ezt a szkriptet akkor futtathatja, ha az R interaktív felületen dolgozik. Ahogy korábban említettük, az interfész megnyitásához a rendszerhéjban írja be az **R**értéket.  

## <a name="python"></a>Python

A anaconda Python a Python 3,5-es és 2,7-as környezetekkel van telepítve. Az 2,7-es környezet neve _root_ , a 3,5-környezet neve _py35_. Ehhez a terjesztéshez körülbelül 300 a legnépszerűbb matematikai, a mérnöki csapathoz és az analitikai csomagok, valamint az alap Python tartalmazza.

A py35 környezetben az alapértelmezett érték. A root (2,7) környezet aktiválásához használja a következő parancsot:

```bash
source activate root
```

A py35-környezet ismételt aktiválásához használja a következő parancsot:

```bash
source activate py35
```

Egy Python interaktív munkamenet meghívásához a rendszerhéjban írja be a **Python**értéket. 

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

## <a name="jupyter-notebook"></a>Jupyter notebook

Az anaconda-disztribúció egy Jupyter Notebook és egy olyan környezetet is tartalmaz, amely megoszthatja a kódot és az elemzést. A Jupyter Notebook elérése a JupyterHub keresztül. A bejelentkezéshez a helyi Linux-felhasználónevet és-jelszót kell használnia.

A Jupyter Notebook-kiszolgáló előre konfigurálva van a Python 2, a Python 3 és az R kernel segítségével. A **Jupyter notebook** asztal ikon segítségével nyissa meg a böngészőt, és nyissa meg a Jupyter notebook-kiszolgálót. Ha a DSVM SSH-n vagy a X2Go-ügyfélen keresztül éri el, a https:\//localhost: 8000/. címen is elérheti a Jupyter notebook-kiszolgálót.

> [!NOTE]
> Folytassa, ha bármely tanúsítvánnyal kapcsolatos figyelmeztetéseket kap.

A Jupyter notebook server bármely állomásról is elérheti. Adja meg a **\/https:/\<DSVM DNS-név\>vagy IP-cím: 8000/** .

> [!NOTE]
> Az 8000-es port a tűzfalon alapértelmezés szerint meg van nyitva a DSVM kiépítve. 

A Microsoft csomagolt jegyzetfüzeteket tartalmaz, amelyek közül egy a Pythonban és egy az R-ben található. A Jupyter Notebook kezdőlapján található mintákra mutató hivatkozást a helyi Linux-felhasználónevet és-jelszót használva a Jupyter Notebook való hitelesítés után tekintheti meg. Új jegyzetfüzet létrehozásához válassza az **új**lehetőséget, majd válassza ki a használni kívánt nyelvi kernelt. Ha nem jelenik meg az **új** gomb, a bal felső sarokban található **Jupyter** ikonra kattintva lépjen a notebook-kiszolgáló kezdőlapjára.

## <a name="spark-standalone"></a>Spark önálló 

A Spark önálló üzemmód egy példánya előre telepítve van a Linux DSVM, így a nagy méretű fürtökön történő tesztelés és üzembe helyezésük előtt helyileg fejlesztheti a Spark-alkalmazásokat. 

A Jupyter kernel keresztül PySpark programok futtathatók. A Jupyter megnyitásakor válassza az **új** gombot, és az elérhető kernelek listáját kell megjelennie. A **Spark-Python** a PySpark kernel, amellyel Spark-alkalmazásokat hozhat létre a Python nyelv használatával. A Spark-programot a Python IDE, például a Notebookshoz vagy a Spyder használatával is létrehozhatja. 

Ebben az önálló példányban a Spark stack a hívó ügyfélprogramban fut. Ez a funkció gyorsabbá és egyszerűbbé teszi a Spark-fürtön való fejlesztéshez képest felmerülő problémák megoldását.

A Jupyter egy minta PySpark notebookot biztosít. A SparkML könyvtárban található a Jupyter ($HOME/notebooks/SparkML/pySpark) könyvtárában. 

Ha az R for Spark-t használja, használhatja a Machine Learning Server, a Sparker vagy a sparklyr. 

Mielőtt a-t a Machine Learning Server Spark-környezetében futtatja, végre kell hajtania egy egyszeri telepítési lépést, amely lehetővé teszi a helyi egycsomópontos Hadoop-HDFS és a fonal-példányok engedélyezését. Alapértelmezés szerint Hadoop-szolgáltatásokhoz vannak telepítve, de a dsvm-hez a letiltva. A Hadoop-szolgáltatások engedélyezéséhez futtassa a következő parancsokat root-ként az első alkalommal:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

A Hadoop kapcsolódó szolgáltatásokat leállíthatja, ha a futtatásával `systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn`nincs szüksége rájuk.

A/dsvm/samples/MRS könyvtár egy olyan mintát biztosít, amely bemutatja, hogyan fejleszthető és tesztelhető Machine Learning Server egy távoli Spark-környezetben (az önálló Spark-példány a DSVM).

## <a name="ides-and-editors"></a>Ide-ket és a szerkesztőknek

Számos Kódszerkesztő közül választhat, például a VI/VIM, az Emacs, a gedit, a Notebookshoz, a RStudio, az Eclipse, a LaTeX és a IntelliJ. 

* a gedit, az Eclipse, a IntelliJ, az R Studio és a Notebookshoz grafikus szerkesztők. A használatához be kell jelentkeznie egy grafikus asztalra. Az asztal és az alkalmazás menü parancsikonjainak használatával nyithatja meg őket.

* A Vim és az Emacs szöveg-alapú szerkesztők. Az EBS-ben az ESS-bővítmény csomag megkönnyíti az R használatát az Emacs szerkesztőben. További információkat az [ESS webhelyén](https://ess.r-project.org/)talál.

* Az Eclipse egy nyílt forráskódú, bővíthető IDE, amely több nyelvet támogat. A Java-fejlesztőknek készült Eclipse IDE a DSVM telepített verzió. Több népszerű nyelvhez is telepíthet beépülő modulokat a környezet kiterjesztéséhez. 

  A Azure Toolkit for Eclipse beépülő modul az Eclipse-sel van telepítve a DSVM. Az Azure-alkalmazások létrehozásához, fejlesztéséhez, teszteléséhez és üzembe helyezéséhez használhatja a Azure Toolkit for Eclipse alkalmazást, amely támogatja a Java-hoz hasonló nyelveket támogató Eclipse fejlesztői környezetet.

  A Javához készült Azure SDK is telepítve van a DSVM Azure Toolkit for Eclipse. A Javához készült Azure SDK hozzáférést biztosít különböző Azure-szolgáltatásokhoz Java-környezeten belül. 
  
  További információ: [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

* A LaTeX a texlive-csomagon keresztül települ, valamint egy [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html)nevű Emacs-bővítményi csomaggal. Ez a csomag egyszerűsíti a LaTeX-dokumentumok készítését az Emacs-ben. 

## <a name="databases"></a>Adatbázisok

A Linux DSVM számos adatbázishoz és parancssori eszközhöz biztosít hozzáférést.

### <a name="postgressql"></a>PostgresSQL

A nyílt forráskódú adatbázis PostgresSQL elérhető a DSVM, és a initdb-t futtató szolgáltatások befejeződtek. Adatbázisokat és felhasználókat kell létrehoznia. További információkért tekintse meg a [PostgresSQL dokumentációját](https://www.postgresql.org/docs/).  

### <a name="squirrel-sql"></a>Mókus SQL

A mókus SQL egy grafikus SQL-ügyfél, amely különböző adatbázisokhoz (többek között a SQL Server, a PostgresSQL és a MySQL-hez) és SQL-lekérdezések futtatásához is csatlakozhat. A mókus SQL-t futtathatja grafikus asztali munkamenetből (például a X2Go-ügyfélen keresztül) egy asztali ikon használatával. Vagy futtathatja az ügyfelet a következő parancs használatával a rendszerhéjban:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh /usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Az első használata előtt az illesztőprogramok és adatbázis-aliasok beállítása. A JDBC-illesztőprogramok a következő helyen találhatók:/usr/share/Java/jdbcdrivers.

További információ: [mókus SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-sql-server"></a>SQL Serverhoz való hozzáféréshez használható parancssori eszközök

Az ODBC illesztőprogram-csomagot az SQL Server is két parancssori eszközöket is tartalmaz:

* **bcp**: A BCP eszköz tömegesen másol egy SQL Server egy példánya és egy, a felhasználó által megadott formátumban tárolt adatfájl között. A BCP eszközzel nagy számú új sort importálhat SQL Server táblákba, vagy exportálhatja a táblákból az adatokból az adatfájlokat. Az adattáblázatba való importáláshoz az adott táblához létrehozott formátumú fájlt kell használnia. Vagy tisztában kell lennie a tábla struktúrájával és az oszlopokhoz érvényes adattípusokkal.

  További információ: [kapcsolat a BCP-vel](https://msdn.microsoft.com/library/hh568446.aspx).

* **sqlcmd**: A Sqlcmd segédprogrammal megadhatja a Transact-SQL-utasításokat, a rendszerfolyamatokat és a parancsfájlokat a parancssorban. A Sqlcmd segédprogram ODBC-t használ a Transact-SQL-kötegek végrehajtásához.

  További információ: [a Sqlcmd való kapcsolat](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Ez az eszköz a Linux és a Windows platform közötti különbségeket is tárgyalja. A részletek dokumentációjában talál.

### <a name="database-access-libraries"></a>Adatbázis-hozzáférés kódtárak

Az adatbázis-hozzáférés kódtárai az R és a Python alkalmazásban érhetők el:

* Az R-ben a RODBC csomag vagy dplyr csomag használatával kérdezheti le és futtathatja az SQL-utasításokat az adatbázis-kiszolgálón.
* A Pythonban a pyodbc függvénytár adatbázis-hozzáférést biztosít az ODBC-vel az alapul szolgáló rétegként.

## <a name="azure-tools"></a>Azure-eszközök

A következő Azure-eszközök vannak telepítve a DSVM:

* **Azure CLI**: Az Azure parancssori felületének használatával Azure-erőforrásokat hozhat létre és kezelhet a rendszerhéj-parancsokkal. Az Azure-eszközök megnyitásához adja meg az **Azure súgóját**. További információkért lásd: a [Azure CLI dokumentációjában](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Azure Storage Explorer**: A Azure Storage Explorer egy grafikus eszköz, amellyel böngészhet az Azure Storage-fiókban tárolt objektumokon, valamint az Azure-blobokból és azokból történő adatok feltöltésével és letöltésével. Storage Explorer asztali parancsikonjára ikonra kattintva érheti el. A **StorageExplorer**megadásával is megnyithatja azt egy rendszerhéj-parancssorból. Be kell jelentkeznie egy X2Go-ügyfélről, vagy be kell állítania az X11-továbbítást.
* **Azure-kódtárak**: A következő kódtárak vannak előtelepítve a DSVM:
  
  * **Python**: Az Azure-hoz kapcsolódó kódtárak az *Azure*, a *azureml*, a *pydocumentdb*és a *pyodbc*. Az első három kódtárakat elérheti az Azure storage services, Azure Machine Learning és az Azure Cosmos DB (NoSQL-adatbázis az Azure-ban). A negyedik kódtára, pyodbc (valamint a Microsoft ODBC-illesztőprogram SQL Serverhez), lehetővé teszi a hozzáférést az SQL Server, az Azure SQL Database és az Azure SQL Data Warehouse a Python egy ODBC-felület használatával. Adja meg **pip lista** megjelenítéséhez a listában szereplő könyvtárak. Győződjön meg arról, a parancs futtatása a Python 2.7-es és 3.5-ös környezetben.
  * **R**: Az Azure-ban található, az R-ben kapcsolódó kódtárak a AzureML és a RODBC.
  * **Java**: Az Azure Java-kódtárak listája a DSVM címtár/dsvm/sdk/AzureSDKJava található. A kulcs függvénytárak is az Azure storage és a felügyeleti API-k, az Azure Cosmos DB és a JDBC illesztőprogramok az SQL Server.  

A [Azure Portal](https://portal.azure.com) az előre telepített Firefox böngészőből érheti el. A Azure Portal az Azure-erőforrások létrehozását, kezelését és figyelését teszi elérhetővé.

## <a name="azure-machine-learning"></a>Azure Machine Learning

A Azure Machine Learning egy teljes körűen felügyelt felhőalapú szolgáltatás, amely prediktív elemzési megoldások készítésére, üzembe helyezésére és megosztására használható. Az Azure Machine Learning Studio-ből hoz létre a kísérletek és a modellek. A Azure Machine Learning webböngészőből való eléréséhez nyissa meg a következőt: [Microsoft Azure Machine learning](https://studio.azureml.net)DSVM.

Miután bejelentkezett Azure Machine Learning Studioba, a kísérletezési vászon használatával létrehozhat egy logikai folyamatot a gépi tanulási algoritmusokhoz. Egy Azure Machine Learning-on üzemeltetett Jupyter Notebookhoz is hozzáférhet. A jegyzetfüzet zökkenőmentesen használható a Machine Learning Studioban található kísérleteknél. 

Működővé tenni azokat a gépi tanulási modelleket, amelyeket a webszolgáltatási felületen való becsomagolással épít ki. A végrehajtott Machine learning-modellek lehetővé teszik bármely nyelven írt ügyfelek számára az előrejelzések meghívását ezekből a modellből. További információkért lásd: a [Machine Learning dokumentációja](https://azure.microsoft.com/documentation/services/machine-learning/).

Az R-vagy Python-modelleket az DSVM is létrehozhatja, majd éles környezetben helyezheti üzembe azokat Azure Machine Learning. A Microsoft az R (**AzureML**) és a Python (**AzureML**) függvénytárait telepítette a funkció támogatásához.

További információ az R-és Python-modellek Azure Machine Learning való üzembe helyezéséről: [tíz dolog, amit elvégezhet a Data Science Virtual Machine](vm-do-ten-things.md).

> [!NOTE]
> A DSVM Windows-verziójára vonatkozóan a Data Science Virtual Machineon elvégezhető [tíz dologra](vm-do-ten-things.md) vonatkozó utasításokat. A modellek Azure Machine Learning való üzembe helyezésére vonatkozó információk azonban a Linux-DSVM is érvényesek.

## <a name="machine-learning-tools"></a>Machine learning eszközök

A DSVM néhány gépi tanulási eszközt és algoritmust tartalmaz, amelyek előre le vannak fordítva, és helyileg vannak előtelepítve. Ezek a következők:

* **Microsoft Cognitive Toolkit**: Mélyreható tanulási eszközkészlet.
* **Vowpal Wabbit**: Egy gyors online tanulási algoritmus.
* **XGBoost**: Olyan eszköz, amely optimalizált, növelt fa algoritmusokat biztosít.
* **Python**: A anaconda Python a gépi tanulási algoritmusokkal, például a Scikit-Learn csomaggal rendelkezik. Könyvtárak használatával telepítheti a `pip install` parancsot.
* **R**: Az R. előre telepített kódtárak többek között az LM, a GLM, a randomForest és a rpart. A további könyvtárakat a futtatásával `install.packages(<lib name>)`is telepítheti.

A következő szakaszokban részletesen ismertetjük a Microsoft Cognitive Toolkit, a Vowpal Wabbit és a XGBoost.

### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

A Microsoft Cognitive Toolkit egy nyílt forráskódú Deep learning-eszközkészlet. Ez egy parancssori eszköz (CNTK), amely már szerepel az elérési úton.

Alapszintű minta futtatásához futtassa a következő parancsokat a rendszerhéjban:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

További információ: [GITHUB CNTK adattár](https://github.com/Microsoft/CNTK) és a [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

A Vowpal Wabbit olyan gépi tanulási rendszer, amely online, kivonatoló, allreduce, csökkentéseket, learning2search, aktív és interaktív tanulási módszereket használ.

Az eszköz alapszintű példán való futtatásához futtassa a következő parancsokat:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

A Vowpal Wabbit bemutató könyvtára más, nagyobb bemutatókat is tartalmaz. További információ a Vowpal Wabbit: [GitHub Vowpal Wabbit adattár](https://github.com/JohnLangford/vowpal_wabbit) és a [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>XGBoost

A XGBoost-könyvtár kialakítva és optimalizálva lett a kifejleszthető (fa) algoritmusokhoz. A XGBoost-könyvtár célja, hogy leküldse a gépek számítási korlátait a nagy léptékű faszerkezetes növekedéshez, amely méretezhető, hordozható és pontos.

A XGBoost parancssori és R-függvénytárként van megadva.

Ha az R-ben a XGBoost-könyvtárat szeretné használni, indítson el egy interaktív R-munkamenetet (a rendszerhéjban írja be az **r**-t), majd töltse be a könyvtárat.

Az alábbi egyszerű példa az R parancssorban futtatható:

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

A XGBoost parancssor futtatásához futtassa a következő parancsokat a rendszerhéjban:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

A rendszer a megadott könyvtárba írja a. Model fájlt. További információ a GitHubon futó bemutatóról: [bináris besorolás](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

A XGBoost kapcsolatos további információkért tekintse meg a [XGBoost dokumentációját](https://xgboost.readthedocs.org/en/latest/) és a [XGBoost GitHub-tárházat](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Rattle

A csörgő (*R* *A*analitikus *t*OOL *t*o *L*keres *E*asily) GUI-alapú adatelemzést és modellezést használ. Csörgő
- Statisztikai és vizuális összefoglalókat jelenít meg.
- A könnyen modellezhető adattípusokat alakítja át.
- A nem felügyelt és felügyelt modelleket is létrehoz az adatokból.
- Grafikusan mutatja be a modellek teljesítményét.
- Az új adatkészletek pontszáma.
- R-kód generálása.
- Azokat a műveleteket replikálja a felhasználói felületen, amelyek közvetlenül az R-ben futtathatók, vagy további elemzés céljából használható kiindulási pontként.

A csörgő futtatásához be kell jelentkeznie egy grafikus asztali munkamenetbe. A terminálban az **r** érték megadásával nyissa meg az r-környezetet. Az R a parancssorba írja be a következő parancsokat:

```R
library(rattle)
rattle()
```

Megnyílik egy grafikus felület, amely egy készlettel rendelkezik. A következő rövid útmutató lépéseit használhatja a Csörgőben egy minta időjárási adathalmaz használatához és egy modell létrehozásához. Néhány lépés során a rendszer felszólítja, hogy automatikusan telepítsen és töltsön be néhány szükséges R-csomagot, amely még nem szerepel a rendszeren.

> [!NOTE]
> Ha nincs engedélye a csomag telepítésére a rendszerkönyvtárban (ez az alapértelmezett beállítás), az R-konzolon megjelenik egy üzenet, hogy csomagokat telepítsen a személyes tárba. Ha ezeket az utasításokat látja, írja be az **y**értéket.

1. Válassza a **Végrehajtás** lehetőséget.
1. Megjelenik egy párbeszédpanel, amely a példa időjárási adatkészletének betöltését kéri. A példa betöltéséhez válassza az **Igen** lehetőséget.
1. Válassza a **modell** fület.
1. Döntési fa létrehozásához válassza a **végrehajtás** elemet.
1. Válassza a **Rajzolás** lehetőséget a döntési fa megjelenítéséhez.
1. Válassza az **erdő** lehetőséget, majd válassza a **végrehajtás** lehetőséget egy véletlenszerű erdő kiépítéséhez.
1. Válassza ki a **kiértékelés** lapot.
1. Válassza ki a **kockázat** lehetőséget, majd válassza a **végrehajtás** elemet a két **kockázatos (halmozott)** teljesítménybeli ábrázolás megjelenítéséhez.
1. Válassza a **napló** lapot, hogy megjelenjen az előző műveletekhez generált R-kód. (A csörgő jelenlegi kiadásának hibája miatt be kell szúrnia egy **#** karaktert a napló **exportálásához** a napló szövege elé.)
1. A weather_script nevű R-parancsfájl mentéséhez kattintson az **Exportálás** gombra *. Az R* -t a kezdőmappa mappájába.

Kiléphet a csörgő és az R lehetőségből. Most már módosíthatja a generált R-szkriptet. Használhatja a parancsfájlt is, és bármikor futtathatja, hogy megismételje a csörgő felhasználói felületen végzett összes műveletet. Különösen az R-ben kezdők számára, így gyorsan végezheti el az elemzést és a gépi tanulást egy egyszerű grafikus felületen, miközben az R-ben automatikusan generál kódot a módosításhoz vagy a tanuláshoz.
