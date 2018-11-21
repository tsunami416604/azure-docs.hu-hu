---
title: Egy Linux CentOS adatelemző virtuális gép Azure-beli üzembe helyezése |} A Microsoft Docs
description: Konfigurálja, és a egy Linux rendszerű adatelemző virtuális gép létrehozása az Azure-ban, elemzési és gépi tanulás.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: 3bab0ab9-3ea5-41a6-a62a-8c44fdbae43b
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: ca3a0e9a8c63ddc9a5c2ca34faffc683d0324321
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52262553"
---
# <a name="provision-a-linux-centos-data-science-virtual-machine-on-azure"></a>Egy Linux CentOS adatelemző virtuális gép Azure-beli üzembe helyezése

A Linux rendszerű adatelemző virtuális gép, a CentOS-alapú Azure virtuális gép, amely tartalmaz egy előre telepített eszközök gyűjteményét. Ezekkel az eszközökkel gyakran használják az adatok előkészítésétől, és a gépi tanulás. A legfontosabb szoftverek összetevőit a következők:

* Operációs rendszer: Linux CentOS terjesztési.
* A Microsoft R Server Developer Edition
* Anaconda Python elosztási (2.7-es és 3.5-ös verzió), beleértve a népszerű elemzőkönyvtárak
* JuliaPro - Julia nyelvhez, tudományos és data analytics népszerű kódtárak válogatott eloszlása
* Önálló Spark-példányt, és egyetlen csomópont Hadoop (HDFS, a Yarn)
* JupyterHub - támogató R, Python, PySpark, Julia kernelekkel többfelhasználós Jupyter notebook server
* Azure Storage Explorer
* Az Azure parancssori felület (CLI) Azure-erőforrások kezeléséhez
* PostgresSQL adatbázis
* Machine learning eszközök
  * [A cognitive Toolkit](https://github.com/Microsoft/CNTK): a Microsoft Research szoftver eszközkészlet mélytanulási.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): gyors machine learning-rendszer támogató technikák, például online, kivonatoló, allreduce, csökkentésének, learning2search, aktív, és az interaktív tanulás.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): egy gyorsított fa gyors és pontos végrehajtását biztosító eszköz.
  * [Rattle](https://togaware.com/rattle/) (az R analitikai eszközt, ismerje meg, egyszerűen): olyan eszköz, amely lehetővé teszi az adatok elemzési és gépi tanulási R egyszerű, de a GUI-alapú adatfeltárás és modellezés az R-kód automatikus generálása első lépései.
* Az Azure SDK-t a Java, Python, node.js, a Ruby, PHP
* Az R és Python for kódtárak használata az Azure Machine Learning és más Azure-szolgáltatások
* Fejlesztői eszközök és a szerkesztők (RStudio, PyCharm, intellij-vel, Emacs, gedit, vi)


A feladatok egy sorozatát léptetés adatelemzésre áll:

1. Keresés, betöltés és előzetesen feldolgozni az adatokat
1. Buildelési és tesztelési célokra modellek
1. A modellek intelligens alkalmazásokban felhasználásra üzembe helyezése

Az adatszakértők a különböző eszközök segítségével ezeket a feladatokat. Ez elég sok időt vesz igénybe, keresse meg a szoftver megfelelő verzióját, és töltse le, majd fordítsa le a, és telepítse ezeket a verziókat.

A Linux rendszerű adatelemző virtuális gép közel teljes értékesítése miatt ezt a terhet megkönnyítése érdekében. Segítségével gyorsan elindíthatja az elemzési projekt. Ez lehetővé teszi, hogy a különböző nyelveken, többek között az R, Python, SQL, a Java és C++ feladatok. Az eclipse IDE fejlesztéséhez és teszteléséhez a kódot, amely egyszerűen használható biztosít. Az Azure SDK tartalmazza a virtuális gép különböző szolgáltatások használata Linux rendszeren a Microsoft cloud platform az alkalmazások készítését teszi lehetővé. Ezenfelül más nyelvekre például Ruby, a Perl, a PHP és a node.js előre telepített is elérhető lesz.

Nincsenek szoftvereket díjmentesen használható a data science Virtuálisgép-rendszerképet. Csak az Azure-beli hardveres használati díjakat, amelyek értékelni a virtuális gép, amely üzembe helyezi a Virtuálisgép-lemezkép a mérete alapján kell fizetnie. A számítási díjak a további részletek találhatók a [ajánlatuk információs oldalán virtuális gép az Azure Marketplace-en](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Az adatelemző virtuális gép más verziói
Egy [Ubuntu](dsvm-ubuntu-intro.md) rendszerképet is lehetőség, a legtöbb, a CentOS-rendszerképet és a deep learning-keretrendszerek ugyanazokat az eszközöket. A [Windows](provision-vm.md) lemezkép érhető el is.

## <a name="prerequisites"></a>Előfeltételek
Egy Linux rendszerű adatelemző virtuális gép létrehozásához, az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**: beszerzése utólag, lásd: [lekérése az Azure ingyenes próbaverzió](https://azure.microsoft.com/free/).
* **Az Azure storage-fiók**: hozzon létre egyet, tekintse meg [hozzon létre egy Azure storage-fiók](../../storage/common/storage-quickstart-create-account.md). Azt is megteheti Ha nem szeretne meglévő fiók, a storage-fiók is létrehozható, a virtuális gép létrehozása a folyamat részeként.

## <a name="create-your-linux-data-science-virtual-machine"></a>A Linux Data Science virtuális gép létrehozása
Hozzon létre egy példányt, a Linux rendszerű adatelemző virtuális gép a lépései a következők:

1. Keresse meg a virtuális gépet, az ajánlati a [az Azure portal](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm).
1. Kattintson a **létrehozás** (a lap alján) viszi, megjelenik a varázsló.![ Konfigurálja-data-adatelemzési – a virtuális gép](./media/linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
1. A következő szakaszok a bemenet minden (a jobb oldalán az előző ábrán felsorolt) a varázsló lépéseit, a Microsoft Data Science virtuális gép létrehozásához használt. Az alábbiakban a bemeneti adatok konfigurálása az egyes lépéseket:
   
   a. **Alapvető beállítások**:
   
   * **Név**: a data science kiszolgáló hoz létre nevet.
   * **Felhasználónév**: első fiók bejelentkezési azonosítója.
   * **Jelszó**: első fiók jelszavát (használhat nyilvános SSH-kulcs jelszava helyett).
   * **Előfizetés**: Ha több előfizetéssel rendelkezik, válassza ki az egyik, amelyen a gép létrehozása és a számlázás. Ehhez az előfizetéshez erőforrás-létrehozási jogosultságokkal kell rendelkeznie.
   * **Erőforráscsoport**: létrehozhat egy új vagy egy meglévő csoportot.
   * **Hely**: válassza a leginkább megfelelő adatközpontot. Általában az adatközpont, amely tartalmazza a legtöbb az adatokat, vagy a leggyorsabb hálózati hozzáféréshez a fizikai helyéhez legközelebbi.
   
   b. **Méret**:
   
   * Válassza ki a kiszolgáló típusát, amely megfelel a funkcionális és a költségek megkötések. Válassza ki **nézet összes** további lehetőségek a Virtuálisgép-méretek megtekintéséhez.
   
   c. **Beállítások**:
   
   * **Lemez típusa**: válasszon **prémium** Ha inkább olyan tartós állapotú meghajtót (SSD). Ellenkező esetben válasszon **Standard**.
   * **Storage-fiók**: hozzon létre egy új Azure-tárfiókot az előfizetésében, vagy használjon egy meglévőt, ugyanazon a helyen, amely a választott a **alapjai** , a varázsló.
   * **Más paramétereket**: A legtöbb esetben csak használja az alapértelmezett értékeket. Érdemes figyelembe venni, nem az alapértelmezett értékeket, a kurzort az egyes mezőkkel tájékoztató mutató hivatkozás.
   
   d. **Összefoglalás**:
   
   * Győződjön meg arról, hogy minden, a megadott adatok helyesek.
   
   e. **Vásároljon**:
   
   * A kiépítés indításához kattintson **vásárlása**. A tranzakció feltételeiben szerepel egy hivatkozás. A virtuális gép nem rendelkezik a kiválasztott kiszolgáló méretét a számítási túl további díjakat a **mérete** . lépés.

A kiépítés körülbelül 10 – 20 percet vesz igénybe. A kiépítési állapota jelenik meg az Azure Portalon.

## <a name="how-to-access-the-linux-data-science-virtual-machine"></a>A Linux rendszerű adatelemző virtuális gép elérése
Ha a virtuális gép létrejött, bejelentkezhet hozzá SSH használatával. A fiók hitelesítő adataival, amelyet a **alapjai** szakasz 3. lépésben a szöveg shell felületén. Windows rendszeren letölthet egy SSH-ügyféleszközt (például a [Puttyt](http://www.putty.org)). Ha inkább egy grafikus desktop (X Windows rendszer), használhatja a Putty-továbbítás X11 vagy X2Go ügyfél telepítése.

> [!NOTE]
> A X2Go ügyfél végrehajtott jelentősen jobb, mint a tesztelés továbbítás X11. Azt javasoljuk, hogy az asztali grafikus felület X2Go ügyfél használatával.
> 
> 

## <a name="installing-and-configuring-x2go-client"></a>Telepítésével és konfigurálásával X2Go ügyfél
A Linux rendszerű virtuális gép már kiépített X2Go kiszolgálóval, és készen áll kapcsolatok fogadására. A Linux rendszerű virtuális gép grafikus desktopban csatlakozni, tegye a következőt az ügyfélen:

1. Töltse le és telepítse a saját ügyfélplatformjára X2Go ügyfél [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
1. Futtassa a X2Go ügyfél, és válassza a **új munkamenet**. Több lap egy konfigurációs ablaka nyílik. Adja meg az alábbi konfigurációs paramétereket:
   * **Munkamenet lapon**:
     * **Gazdagép**: A gazdagép neve vagy a Linux rendszerű adatelemző virtuális gép IP-címét.
     * **Bejelentkezési**: felhasználónév, a Linux rendszerű virtuális gépen.
     * **SSH-Port**: meghagyhatja az alapértelmezett érték 22.
     * **Munkamenet típusa**: XFCE módosítsa az értéket. A Linux rendszerű virtuális gép jelenleg csak XFCE desktop támogatja.
   * **Az adathordozó lapon**: Ha nem szeretné használni az eredményes támogatás és a nyomtatási ügyfél, kikapcsolhatja azokat.
   * **A megosztott mappák**: Ha az ügyfél gépek csatlakoztatása Linux rendszerű virtuális gépen szeretné címtárak, adja hozzá az ügyfél gép könyvtárakat, hogy meg szeretné osztani az ezen a lapon a virtuális géppel.

Bejelentkezés után a virtuális géphez SSH-ügyfél vagy a grafikus asztali XFCE X2Go ügyfélszámítógépen keresztül, készen áll az eszközöket, amelyek telepítése és konfigurálása történik meg a virtuális gép használatának megkezdéséhez. A XFCE látható alkalmazások parancsikonjai és asztali ikonok a számos olyan eszközzel.

## <a name="tools-installed-on-the-linux-data-science-virtual-machine"></a>A Linux Data Science virtuális gépen telepített eszközök
### <a name="microsoft-r-server"></a>Microsoft R Server
Az R a legnépszerűbb nyelvek adatok elemzési és gépi tanulás. Ha szeretné az R használata a elemzéshez, a virtuális gép rendelkezik a Microsoft R Server (Asszony) a Microsoft R Open (MRO) és matematikai Kernel könyvtár (MKL). A MKL optimalizálja a matematikai műveletek gyakori az elemzési algoritmusra. MRO 100 %-os CRAN-r-kompatibilis, és az R-kódtárak a cran-en közzétett bármelyikét a MRO is telepíthető. Asszony biztosítja skálázás és az R-modellek operacionalizálás web services szolgáltatásba. A R-programokat egy, az az alapértelmezett szerkesztők, mint az RStudio, vi, Emacs vagy gedit szerkesztheti. Ha a Emacs szerkesztőt használja, vegye figyelembe, hogy a Emacs csomag REJTÉSE (Emacs beszél statisztika), amely egyszerűbbé teszi a előre telepített belül a Emacs szerkesztő R-fájljain végzett munka lett.

Az R modul indításához konzolon, egyszerűen írja be **R** a rendszerhéj. Ekkor megjelenik egy interaktív környezetet. Az R-program fejlesztéséhez, általában egy szerkesztővel például Emacs vagy a vi vagy a gedit, és futtassa az r parancsfájlok Az RStudio hogy egy teljes grafikus IDE környezet az R-programot fejlesszen.

Emellett van egy R-szkriptet, hogy telepítse a [felső 20 R-csomagok](http://www.kdnuggets.com/2015/06/top-20-r-packages.html) Ha azt szeretné. Ez a szkript futtatása után lehetséges a R interaktív felület, amely lehet megadni (ahogy említettük) írja be a **R** a rendszerhéj.  

### <a name="python"></a>Python
Python fejlesztést az Anaconda Python elosztási 2.7-es és 3.5-ös telepítve van. Ehhez a terjesztéshez körülbelül 300 a legnépszerűbb matematikai, a mérnöki csapathoz és az analitikai csomagok, valamint az alap Python tartalmazza. Az alapértelmezett szövegszerkesztő is használhatja. Emellett használhatja Spyder, a Python ide-vel, amely Anaconda Python disztribúció részét képezi. Spyder kell egy grafikus asztali vagy X11-továbbítást. A grafikus desktopban Spyder parancsikon biztosítunk.

Python 2.7-es és a 3.5-ös van, mivel a kívánt Python-verzió (conda-környezet) az aktuális munkamenetben a használni kívánt kifejezetten aktiválni kell. Az aktiválási folyamat az elérési út változó állítja be a Python kívánt verziójával.

Az aktiválás a Python 2.7-es conda-környezet, futtassa a következő parancsot a rendszerhéj:

    source /anaconda/bin/activate root

Python 2.7-es telepített */anaconda/bin*.

A Python 3.5-ös conda-környezet aktiválásához az rendszerhéj futtassa a következőt:

    source /anaconda/bin/activate py35


Python 3.5 lesz telepítve */anaconda/envs/py35/bin*.

A Python interaktív munkamenet elindításához, csak gépelje **python** a rendszerhéj. Ha, vagy a grafikus felület set-továbbítás be X11, beírhatja **pycharm** elindíthatja a PyCharm Python IDE.

További Python-kódtárakat telepítéséhez futtatni szeretné ```conda``` vagy ````pip```` a sudo paranccsal, és teljes elérési útját a Python Csomagkezelő (conda vagy pip) telepítse a megfelelő Python-környezetet. Példa:

    sudo /anaconda/bin/pip install <package> #pip for Python 2.7
    sudo /anaconda/envs/py35/bin/pip install <package> #pip for Python 3.5
    sudo /anaconda/bin/conda install [-n py27] <package> #conda for Python 2.7, default behavior
    sudo /anaconda/bin/conda install -n py35 <package> #conda for Python 3.5


### <a name="jupyter-notebook"></a>Jupyter Notebook
A Anaconda terjesztési egy Jupyter notebookot, megosztani a kódot és az elemzési környezet is tartalmaz. A Jupyter notebook JupyterHub keresztül érhető el. Jelentkezzen be a helyi Linux-felhasználónév és jelszó használatával.

A Jupyter notebook server Python 2, a Python 3 és az R kernelekkel előre van konfigurálva. Nincs "Jupyter Notebook" Indítsa el a böngészőt a jegyzetfüzet-kiszolgálóhoz való hozzáféréshez nevű asztali ikon. Ha a virtuális gép SSH- vagy X2Go ügyfél, akkor is ellátogathat [ https://localhost:8000/ ](https://localhost:8000/) a Jupyter notebook kiszolgálóhoz való hozzáféréshez.

> [!NOTE]
> Folytassa, ha bármely tanúsítvánnyal kapcsolatos figyelmeztetéseket kap.
> 
> 

A Jupyter notebook server bármely állomásról is elérheti. Egyszerűen adja meg *https://\<virtuális gép DNS-nevét vagy IP-cím\>: 8000 /*

> [!NOTE]
> 8000-es port nyílik meg a tűzfal alapértelmezés szerint amikor a virtuális gép ki van építve.
> 
> 

Azt van csomagolva mintafüzetek – egy, a Python és a egy R. Láthatja a minták mutató hivatkozást a jegyzetfüzet kezdőlap, után a helyi Linux-felhasználónév és jelszó használatával hitelesítést a Jupyter notebookot. Létrehozhat egy új jegyzetfüzetet kiválasztásával **új**, és ezután a megfelelő nyelvi kernel. Ha nem látja a **új** gombra, majd a **Jupyter** ikonra a bal felső sarokban, nyissa meg a notebook server kezdőlapja.

### <a name="apache-spark-standalone"></a>Az Apache Spark-önálló 
Az Apache Spark egy önálló példányát a Linuxos DSVM segítenek a Spark-alkalmazások helyi először tesztelése és nagy méretű fürtökön üzembe helyezése előtt van előtelepítve. A Jupyter kernel keresztül PySpark programok futtathatók. Amikor Jupyter megnyitásához, és kattintson a **új** gomb notebookokhoz elérhető kernelek listája megjelenik. A "Spark – Python", amely lehetővé teszi a Python nyelv használatával alkalmazásokat hozhat létre a Spark a PySpark kernelt. Egy Python ide Előnyeit, mint a PyCharm vagy Spyder használatával hozhat létre, a Spark-programot. Óta egy önálló példányát, mert a hívó ügyfél program belül futtatja a Spark-verem. Így gyorsabb és egyszerűbb fejlesztés a Spark-fürt képest kapcsolatos problémák elhárítása. 

A Jupyter, amely a "Könnyen használható" könyvtár kezdőkönyvtárának Jupyter ($HOME/notebookok és könnyen használható/pySpark) alatt található minta notebook PySpark biztosítunk. 

Ha a Spark az R programozási, használhatja a Microsoft R Server, SparkR vagy sparklyr. 

Mielőtt futtatná a Microsoft R Server Spark-környezetben, kell tennie a telepítő lépést ahhoz, hogy a helyi Hadoop HDFS és Yarn-példány egyetlen csomópont csak egyszer. Alapértelmezés szerint Hadoop-szolgáltatásokhoz vannak telepítve, de a dsvm-hez a letiltva. Az engedélyezéshez, először futtassa a következő parancsokat rendszergazdaként kell:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Állítsa le a Hadoop kapcsolatos szolgáltatásokat, ha nincs szükség rájuk futtatásával ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```` egy minta bemutatja, hogyan lehet fejlesztési és tesztelési Asszony (amely a különálló a dsvm-hez a Spark-példányt) távoli Spark környezetben megadott és elérhető a `/dsvm/samples/MRS` a könyvtár. 

### <a name="ides-and-editors"></a>Ide-ket és a szerkesztőknek
Van néhány kódot szerkesztők közül választhat. Ez magában foglalja a vi vagy VIM Emacs, gEdit, Notebookshoz, az RStudio, az Eclipse és IntelliJ. gEdit, Eclipse, intellij-vel, az RStudio és PyCharm grafikus szerkesztők, és meg kell adnia használhatja őket a grafikus asztali bejelentkezett. Ezek a szerkesztők rendelkezik, asztali és az alkalmazás menüből parancsikonokat indíthassák el azokat.

**VIM** és **Emacs** szöveges szerkesztők vannak. Emacs, a-bővítménycsomagot Emacs beszél statisztika (cím), amely megkönnyíti az R használata belül a Emacs szerkesztő nevű telepítette azt. További információ található [REJTÉSE](http://ess.r-project.org/).

**Az eclipse** egy nyílt forrás, bővíthető IDE, amely támogatja több nyelv. A Java-fejlesztőknek kiadása telepítve a virtuális gépen a példány. Nincsenek elérhető számos népszerű nyelvhez kiterjesztése a környezetben telepített beépülő modulok. A beépülő modul az eclipse-ben nevű telepítve van **Azure Toolkit for Eclipse**. Lehetővé teszi, hogy hozzon létre, fejlesztése, tesztelése és üzembe helyezése az Eclipse fejlesztői környezetet, amely támogatja a Java nyelvet használja az Azure-alkalmazások. Emellett van egy **Javához készült Azure SDK** , amely lehetővé teszi, hogy a különböző Azure-szolgáltatásokhoz való hozzáférés a Java környezetben. További információ az Azure-eszközkészlet az Eclipse-hez található [Azure Toolkit for Eclipse](../../azure-toolkit-for-eclipse.md).

**LaTex** a texlive package és a egy Emacs bővítmény keresztül van telepítve, [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) csomagot, amely egyszerűbbé teszi a szerzői műveletek a Emacs LaTex dokumentumokon.  

### <a name="databases"></a>Adatbázisok
#### <a name="postgres"></a>Postgres
A nyílt forráskódú adatbázis **Postgres** érhető el a virtuális gépen futó szolgáltatások és initdb már befejeződött. Továbbra is szeretné adatbázisok és a felhasználók létrehozásához. További információkért lásd: a [Postgres dokumentáció](https://www.postgresql.org/docs/).  

#### <a name="graphical-sql-client"></a>Grafikus SQL-ügyfél
**SQuirrel SQL**, grafikus SQL-ügyfelet, megadva (például a Microsoft SQL Server, Postgres és MySQL) különböző adatbázisaihoz való kapcsolódásra és az SQL-lekérdezések futtatásához. Ez egy grafikus asztali munkamenetből (például használatával a X2Go ügyfél) futtathatja. SQuirrel SQL meghívni, indítsa el az ikonra az asztalon, vagy futtassa a következő parancsot a rendszerhéj.

    /usr/local/squirrel-sql-3.7/squirrel-sql.sh

Az első használata előtt az illesztőprogramok és adatbázis-aliasok beállítása. A JDBC-illesztőprogramok a következő helyen találhatók:

*/usr/share/Java/jdbcdrivers*

További információkért lásd: [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>A Microsoft SQL Server elérése szolgáló parancssori eszközök
Az ODBC illesztőprogram-csomagot az SQL Server is két parancssori eszközöket is tartalmaz:

**BCP**: A bcp segédprogram tömeges a Microsoft SQL Server-példány és a egy adatfájlt közötti adatokat másolja egy felhasználó által megadott formátumban. A bcp segédprogram használható, nagy mennyiségű új sort importálni az SQL Server-táblákra vagy exportálni adatokat táblák tábláiból adatfájlokba. Adatok importálása egy táblába, kell használni, hogy a táblázat számára létrehozott formátumfájlt, vagy megismerheti a struktúrát a tábla-és adattípusokat és az oszlopok esetében érvényes.

További információkért lásd: [bcp-vel csatlakozás](https://msdn.microsoft.com/library/hh568446.aspx).

**Sqlcmd**: Adja meg a Transact-SQL-utasítások és az sqlcmd segédprogram, valamint a rendszer eljárások és parancsfájlok parancsot a parancssorba. Ez a segédprogram ODBC segítségével hajtsa végre a Transact-SQL-kötegek.

További információkért lásd: [az Sqlcmd-t kapcsolódás](https://msdn.microsoft.com/library/hh568447.aspx).

> [!NOTE]
> Nincsenek ezzel az eszközzel a Linux és Windows platformok közötti különbségeket. A részletek dokumentációjában talál.
> 
> 

#### <a name="database-access-libraries"></a>Adatbázis-hozzáférés kódtárak
Nincsenek elérhető, az R és Python az access-adatbázisok könyvtárak.

* Az R a **RODBC** csomag vagy **dplyr** csomag lehetővé teszi kérdezheti le vagy SQL-utasítások végrehajtása a kiszolgálón.
* A Python a **pyodbc** a könyvtárban olyan adatbázis-hozzáférés az alapul szolgáló rétegként ODBC.  

Hozzáférés **Postgres**:

* R: Használja a csomag **RPostgreSQL**.
* A Python: Használja a **psycopg2** könyvtár.

### <a name="azure-tools"></a>Azure-eszközök
A következő Azure-eszközök telepítve vannak a virtuális gépen:

* **Az Azure parancssori felület**: az Azure parancssori felület lehetővé teszi, hogy a rendszerhéj-parancsok segítségével az Azure erőforrások létrehozásához és kezeléséhez. Az Azure-eszközök meghívni, csak gépelje **azure súgó**. További információkért lásd: a [Azure CLI dokumentációjában](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **A Microsoft Azure Storage Explorer**: Microsoft Azure Storage Explorer egy grafikus eszköz, amellyel az objektumokat az Azure storage-fiókban tárolt böngészni lehet és feltöltése és letölteni az adatokat az Azure-blobokból. Storage Explorer asztali parancsikonjára ikonra kattintva érheti el. Hívhatja meg azt a rendszerhéj parancssorából beírásával **StorageExplorer**. Egy X2Go ügyfélről bejelentkezve, vagy hogy a set-továbbítás be X11 kell.
* **Azure-kódtárak**: az alábbiakban néhány előre telepített könyvtárak.
  
  * **Python**: az Azure-hoz kapcsolódó függvénytárak telepített Python nyelven is **azure**, **azureml**, **pydocumentdb**, és **pyodbc**. Az első három kódtárakat elérheti az Azure storage services, Azure Machine Learning és az Azure Cosmos DB (NoSQL-adatbázis az Azure-ban). A negyedik kódtára, pyodbc (valamint a Microsoft ODBC-illesztőprogram SQL Serverhez), lehetővé teszi a hozzáférést az SQL Server, az Azure SQL Database és az Azure SQL Data Warehouse a Python egy ODBC-felület használatával. Adja meg **pip lista** megjelenítéséhez a listában szereplő könyvtárak. Győződjön meg arról, a parancs futtatása a Python 2.7-es és 3.5-ös környezetben.
  * **R**: az Azure-hoz kapcsolódó függvénytárak az R telepített **AzureML** és **RODBC**.
  * **Java**: Azure Java-kódtárak listája a címtárban található **/dsvm/sdk/AzureSDKJava** a virtuális gépen. A kulcs függvénytárak is az Azure storage és a felügyeleti API-k, az Azure Cosmos DB és a JDBC illesztőprogramok az SQL Server.  

Elérheti a [az Azure portal](https://portal.azure.com) az előre telepített Firefox böngészőben. Az Azure Portalon létrehozása, kezelése és monitorozása az Azure-erőforrások.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Az Azure Machine Learning egy teljes körűen felügyelt felhőszolgáltatás, amely lehetővé teszi, hogy elkészítheti, telepítheti és oszthat meg prediktív elemzési megoldásokat. Az Azure Machine Learning Studio-ből hoz létre a kísérletek és a modellek. Azt is lehet megnyitásával az adatelemző virtuális gép egy webböngészőből [Microsoft Azure Machine Learning](https://studio.azureml.net).

Azure Machine Learning Studióban való bejelentkezés után hozzáférhet egy kísérleti vászonra ahol hozhat létre egy gépi tanulási algoritmusok logikai folyamatot. Is rendelkezik hozzáféréssel a Jupyter notebook az Azure Machine Learning szolgáltatásban üzemeltetett, és zavartalanul tud működni a kísérletek a Machine Learning Studióban. A machine learning-modellek, amelyek azokat a web service-felülethez alkalmazásburkoló által létrehozott működésbe. Ez lehetővé teszi az ügyfelek bármilyen nyelven fejlesztett meghívni a gépi tanulási modellek által létrehozott javaslatok. További információkért lásd: a [Machine Learning dokumentációja](https://azure.microsoft.com/documentation/services/machine-learning/).

A modellek R vagy Python nyelven is épülnek a virtuális Gépet, és majd üzembe helyezzük azt Azure Machine Learning szolgáltatásban éles környezetben. Szalagtárak rendelkezik telepítette a R (**AzureML**) és a Python használatával (**azureml**) Ez a funkció engedélyezéséhez.

Hogyan helyezheti üzembe az Azure Machine Learning modellek R és Python információkért lásd: [tíz dolog, amire alkalmas az adatelemző virtuális gép](vm-do-ten-things.md) (különösen a szakasz "a modellek R vagy Python használatával hozhat létre, és felhasználhatja őket használatával Az Azure Machine Learning").

> [!NOTE]
> Ezek az utasítások az adatelemző virtuális gép Windows verziója esetén használhatóak. De az Azure Machine Learning modellek üzembe helyezéséhez nincs alkalmazható a Linux rendszerű virtuális géphez megadott információk.
> 
> 

### <a name="machine-learning-tools"></a>Machine learning eszközök
A virtuális gép néhány machine learning-eszközökkel és előre lefordított, és előre telepített helyi algoritmusokat tartalmaz. Ezek a következők:

* **A Microsoft Cognitive Toolkit** : deep learning-eszközkészlet.
* **Vowpal Wabbit**: gyors online tanulási algoritmus.
* **xgboost**: egy eszköz, optimalizált, gyorsított fa típusú algoritmusokkal.
* **Python**: Anaconda Python gépi tanulási algoritmusok kódtárakkal Scikit-ismerje meg, például az származnak. Könyvtárak használatával telepítheti a `pip install` parancsot.
* **R**: könyvtár gazdag, machine learning-függvényekkel érhető el az r segítségével A szalagtár szerepel, amely előre telepítve lm, glm, randomForest, rpart. Könyvtárak futtatásával telepíthető:
  
        install.packages(<lib name>)

Íme néhány a lista első három machine learning-eszközökkel kapcsolatos további információkat.

#### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit
Ez a nyílt forráskódú, deep learning-eszközkészlet. Egy parancssori eszköz (cntk), és már szerepel az elérési UTAT.

Egy egyszerű minta futtatásához hajtsa végre az alábbi parancsokat a rendszerhéj:

    cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
    cntk configFile=lr_bs.cntk makeMode=false command=Train

További információkért lásd: a CNTK szakaszában [GitHub](https://github.com/Microsoft/CNTK), és a [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit
Vowpal Wabbit egy gépi tanulási rendszer által használt módszerek például online, kivonatoló, allreduce, csökkentésének, learning2search, aktív, és az interaktív tanulás.

Az eszköz futtatásához egy egyszerű példa, tegye a következőket:

    cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
    cd vwdemo
    vw house_dataset

Nincsenek más, nagyobb bemutatók ebben a könyvtárban. VW további információkért lásd: [ebben a szakaszban a GitHub](https://github.com/JohnLangford/vowpal_wabbit), és a [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>xgboost
Ez a kódtár, amely lett kialakítva és optimalizálva gyorsított (fa) algoritmusokat. Ebben a könyvtárban célja a gépek a számítási korlátokat leküldése a szélsőséges szükség ahhoz, hogy nagyméretű fa kiemelése, amely méretezhető, hordozható és pontos van.

A parancssorból, valamint egy R-kódtár, nyújtja.

Ebben a könyvtárban, az R használandó megkezdése egy interaktív relace jazyka R (csak beírásával **R** a rendszerhéj), és a könyvtár betöltéséhez.

Íme egy egyszerű példa az R-parancssorban futtathatja:

    library(xgboost)

    data(agaricus.train, package='xgboost')
    data(agaricus.test, package='xgboost')
    train <- agaricus.train
    test <- agaricus.test
    bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
    pred <- predict(bst, test$data)

A xgboost parancssor futtatásához, az alábbiakban a hajtsa végre a rendszerhéj-parancsok:

    cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
    cd xgboostdemo
    xgboost mushroom.conf


A megadott könyvtár .model fájl írása. Ez a bemutató példa kapcsolatos információk találhatók [a Githubon](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Xgboost kapcsolatos további információkért lásd: a [xgboost dokumentációs oldal](https://xgboost.readthedocs.org/en/latest/), és a hozzá tartozó [GitHub-adattár](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Rattle
Rattle (a **R** **A**analitikus **T**öz **T**o **L**megszerzésére **E** asily) használ a GUI-alapú adatáttekintés és modellezés. Jelenítse meg az adatokat, azonnal modellezhető, felügyeletlen és a felügyelt modellek adatokból hoz létre, a modellek teljesítményének grafikusan, megadja átalakítások adatok statisztikai és vizuális összegzéseket, és beállítja a pontszámok új adatokat. R-kód, a műveletek a felhasználói felület, amely közvetlenül az R futtatható, illetve kiindulási pontként Microsoft további elemzésnek veti replikálása is állít elő.

Rattle futtatásához, meg kell lenniük egy grafikus asztali bejelentkezési munkamenet. A terminalba írja be a ```R``` , adja meg az R-környezetben. Az R a parancssorba írja be a következő parancsokat:

    library(rattle)
    rattle()

Most már egy grafikus felület nyit meg lapok vannak beállítva. Rattle egy minta időjárás-adatkészlet használja, és a modell létrehozása a gyors üzembe helyezési lépéseket el. Az alábbi lépések némelyike a kéri automatikusan telepíti és betölti az egyes szükséges R-csomagokat, amelyek még nincsenek a rendszeren.

> [!NOTE]
> Nincs hozzáférése a csomag telepítéséhez a rendszer címtárban (az alapértelmezett beállítás), a parancssorba az R-konzol ablakban csomagok telepítéséhez a saját személyes tárába jelenhet meg. Válasz *y* ha látja ezeket az utasításokat.
> 
> 

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

* A [adatelemzés a Linux Data Science virtuális gépen](linux-dsvm-walkthrough.md) a bemutató ismerteti, hogyan hajthat végre számos gyakori adatelemzési feladatok és a Linux rendszerű adatelemző virtuális gép kiépítése itt. 
* Próbálja ki az ebben a cikkben leírt eszközök vizsgálja meg az adatelemző virtuális gép a különféle beépített adatelemzési eszközzel. Is futtathat *dsvm-információ – info* a a rendszerhéj belül a virtuális gép alapszintű bevezetést és további információ a virtuális gépen telepített eszközök mutatókat tartalmaznak.  
* Megtudhatja, hogyan teljes körű elemzési megoldásokat rendszeresen használatával hozhat létre a [csoportos adatelemzési folyamat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
* Látogasson el a [Cortana Analytics katalógusban](http://gallery.cortanaanalytics.com) machine learning és a data analytics minták, amely a Cortana Analytics Suite használata.

