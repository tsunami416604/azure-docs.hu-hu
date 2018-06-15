---
title: Rendszerű Linux CentOS adatok tudományos virtuális gép az Azure-on |} Microsoft Docs
description: Konfigurálja és Linux adatok tudományos virtuális gép létrehozása Azure-elemzés és gépi tanulás.
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
ms.openlocfilehash: 1a201974749acbbb9607e42e67d1935f437f9ca1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31421790"
---
# <a name="provision-a-linux-centos-data-science-virtual-machine-on-azure"></a>A Linux CentOS adatok tudományos virtuális gépet az Azure telepítéséhez

A Linux adatok tudományos virtuális gép CentOS-alapú Azure virtuális gép a előre telepített eszközök gyűjteménye. Ezek az eszközök a adatelemzés gyakran használják, és a gépi tanulás. A kulcs szoftverösszetevők listáját tartalmazza a következők:

* Operációs rendszer: Linux CentOS terjesztési.
* Microsoft R Server Developer Edition
* Anaconda Python elosztási (2.7 és 3.5-ös verzióját. verzió), beleértve a népszerű adatok elemzőkönyvtárak
* JuliaPro - Ágnes nyelvi népszerű tudományos és az adatok analytics könyvtárakkal válogatott megoszlása
* Önálló Spark-példányban és egyetlen csomópont Hadoop (HDFS, Yarn)
* JupyterHub - R, Python, PySpark, Ágnes kernelek támogató többfelhasználós Jupyter notebook kiszolgáló
* Azure Storage Explorer
* Azure parancssori felület (CLI) Azure-erőforrások kezelése
* PostgresSQL adatbázis
* Machine learning-eszközök
  * [Kognitív eszközkészlet](https://github.com/Microsoft/CNTK): A Microsoft Research software eszközkészletet tanulási mély.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): gyors machine learning-rendszer támogatása, például a online, a kivonatoló, allreduce, csökkentése, learning2search, aktív, és interaktív tanulási.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): gyors és pontos súlyozott fa megvalósítási biztosító eszközt.
  * [Rattle](http://rattle.togaware.com/) (az R analitikai eszköz a további könnyen): olyan eszköz, amely lehetővé teszi az első lépések adatelemzés és a gép R GUI-alapú adatok feltárása, ezzel megkönnyítik a tanulási, és az R-kód automatikus generálása modellezési.
* Az Azure SDK, Java, Python, node.js, a Ruby, a PHP
* Az R és Python a szalagtárak használja az Azure Machine Learning és más Azure-szolgáltatásokkal
* Fejlesztői eszközök és szerkesztők (Rstudióból, PyCharm, IntelliJ, Emacs, gedit, VI.)


Adattudomány Ez magában foglalja a feladatok sorozata léptetés:

1. Keresése, betöltése, és előzetesen feldolgozni az adatokat
2. Összeállításakor és tesztelésekor modellek
3. A modelleket a fogyasztás intelligens alkalmazások telepítése

Adatszakértőkön különböző eszközök használatával ezeket a feladatokat. Ez elég időigényes lehet megtalálni a szoftver megfelelő verzióját, és töltse le, majd fordítsa le a, és ezen verziói telepítése.

A Linux adatok tudományos virtuális gép jelentősen ezt a terhet megkönnyítése érdekében. Ezzel a analytics projekt jump-start. Ez lehetővé teszi, hogy működik a különböző nyelveken, beleértve az R, Python, SQL, Java és C++ feladatok. Eclipse biztosít egy IDE fejlesztéséhez és teszteléséhez a kódot, amely könnyen használható. Az Azure SDK tartalmazza a virtuális gép segítségével különböző szolgáltatások Linux rendszeren a Microsoft cloud platform az alkalmazások létrehozását teszi lehetővé. Emellett más nyelvekre például Ruby, Perl, PHP vagy node.js előre telepített is elérhető lesz.

Nincsenek az adatok tudományos VM-lemezkép szoftver költségek. Csak az Azure hardver használati díjak értékelni a virtuális gépet, akkor adja meg a Virtuálisgép-lemezkép mérete alapján kell fizetnie. A számítási díjakat további információkat itt talál a [VM listaelem oldalon, az Azure piactéren](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Az adatok tudományos virtuális gép egyéb verziói
Egy [Ubuntu](dsvm-ubuntu-intro.md) lemezkép érhető el, legtöbb a a CentOS kép túl mély keretrendszerek tanulási azonos eszközök. A [Windows](provision-vm.md) kép érhető el is.

## <a name="prerequisites"></a>Előfeltételek
Adatok tudományos Linux virtuális gép létrehozásához, az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**: egy beszerzéséről [beolvasása az Azure ingyenes próbaverzió](https://azure.microsoft.com/free/).
* **Azure-tárfiók**: szeretne létrehozni egyet, lásd: [az Azure storage-fiók létrehozása](../../storage/common/storage-create-storage-account.md#create-a-storage-account). Azt is megteheti Ha nem szeretné, hogy egy meglévő fiókkal, a tárolási fiók is létrehozható a virtuális gép létrehozásának folyamatán részeként.

## <a name="create-your-linux-data-science-virtual-machine"></a>A Linux-adatok tudományos virtuális gép létrehozása
Az alábbiakban egy példányát, a Linux adatok tudományos virtuális gép létrehozásához szükséges lépéseket:

1. Keresse meg a virtuális gép a listázása a [Azure-portálon](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm).
2. Kattintson a **létrehozása** (a lap alján) a varázsló elindítani.![ Konfigurálja-adatok-tudományos-vm](./media/linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
3. A következő szakaszokban az adatokat a Microsoft Data tudományos virtuális gép létrehozásához használt az egyes (számba jobb oldalán a fenti ábrán) a varázsló lépéseit. Az alábbiakban az egyes lépéseket konfigurálásához szükséges adatokat:
   
   a. **Alapvető**:
   
   * **Név**: az adatok tudományos kiszolgáló létrehozásakor nevét.
   * **Felhasználónév**: első fiók bejelentkezhet azonosítóját.
   * **Jelszó**: első fiók jelszavát (használható nyilvános SSH-kulcs jelszó helyett).
   * **Előfizetés**: Ha több előfizetéssel rendelkezik, válassza ki a amelyiken a gép létrehozását és számlázva van. Ehhez az előfizetéshez erőforrás-létrehozási jogosultsággal kell rendelkeznie.
   * **Erőforráscsoport**: hozhat létre egy új vagy meglévő csoport használata.
   * **Hely**: válassza ki a legjobban megfelelő adatközpont. Általában az adatközpont, amely rendelkezik az adatok, vagy a helynév leggyorsabb hálózati hozzáférési legközelebb.
   
   b. **Méret**:
   
   * Válassza ki a kiszolgáló típusát, amely megfelel a funkcionális és költség megkötések. Válassza ki **nézet összes** a VM-méretek további beállítások megtekintéséhez.
   
   c. **Beállítások**:
   
   * **Lemez típusa**: válasszon **prémium** Ha inkább egy SSD-meghajtót (SSD). Ellenkező esetben válassza **szabványos**.
   * **A Tárfiók**: hozzon létre egy új Azure-tárfiók az előfizetésben, vagy használjon egy meglévőt, hogy a választott ugyanazon a helyen a **alapjai** a varázsló.
   * **Más paraméterek**: A legtöbb esetben csak használja az alapértelmezett értékeket. Figyelembe kell venni a nem az alapértelmezett értékeket, mutasson az egyes mezőkkel tájékoztató mutató hivatkozást.
   
   d. **Összefoglalás**:
   
   * Győződjön meg arról, hogy az összes megadott adatok helyesek.
   
   e. **Vásároljon**:
   
   * A kiépítés elindításához kattintson **megvásárlása**. Hivatkozás a tranzakció feltételeit valósul meg. A virtuális gép nem rendelkezik a kiválasztott kiszolgáló méretéhez számítási túl további díjakat a **mérete** lépés.

A kiépítése körülbelül 10-20 percet kell végrehajtani. A kiépítési állapotát az Azure portálon jelenik meg.

## <a name="how-to-access-the-linux-data-science-virtual-machine"></a>Hogyan érhetők el a Linux adatok tudományos virtuális gép
A virtuális gép létrehozása után is bejelentkezik az ssh protokoll használatával. A létrehozott fiók hitelesítő adatait használja a **alapjai** szakasz 3. lépés a szöveg shell felületén. Windows, egy SSH-ügyfél eszköz, például letöltheti [Putty](http://www.putty.org). Grafikus asztali (X Windows rendszer) tetszés szerint használhatja a Putty továbbítási X11, de X2Go ügyfél telepítése.

> [!NOTE]
> A X2Go ügyfél végre jelentősen jobb, mint a tesztelése továbbítás X11. Egy asztali grafikus felület a X2Go ügyfél használatát javasoljuk.
> 
> 

## <a name="installing-and-configuring-x2go-client"></a>Telepítése és konfigurálása X2Go ügyfél
A Linux virtuális gép már kiépített X2Go kiszolgálóval és készen áll kapcsolatok fogadására. Csatlakozás a Linux virtuális gép grafikus asztal, tegye a következőket az ügyfélen:

1. Töltse le és telepítse a saját ügyfélplatformjára X2Go ügyfél [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Futtassa a X2Go ügyfél, és válassza a **új munkamenet**. Több lap egy konfigurációs ablak nyílik meg. Adja meg a következő konfigurációs paramétereket:
   * **Munkamenet lapon**:
     * **Állomás**: az állomásnév vagy IP-címét a Linux adatok tudományos virtuális Gépet.
     * **Bejelentkezési**: felhasználónév, a Linux virtuális Gépre.
     * **SSH-Port**: hagyja a mezőt, 22, az alapértelmezett érték.
     * **Munkamenet típusa**: XFCE módosítsa az értéket. A Linux virtuális gép jelenleg csak XFCE desktop támogatja.
   * **Az adathordozó lapon**: Ha nem szeretné használni a hang támogatás és nyomtatási ügyfél, kikapcsolhatja azokat.
   * **Megosztott mappák**: Ha az ügyfél gépek, a Linux virtuális gép csatlakoztatott könyvtárak, az ügyfél gép címtárakat, a virtuális gép ezen a lapon megosztani kívánt fel.

Bejelentkezés után a virtuális gép SSH-ügyfél vagy a XFCE grafikus asztali a X2Go ügyfélen keresztül, készen áll az eszközök, amelyek telepítése és konfigurálása történik meg a virtuális Gépen elindítására. A XFCE látható alkalmazások parancsikonjai és asztali ikonok esetében számos eszközt.

## <a name="tools-installed-on-the-linux-data-science-virtual-machine"></a>A Linux adatok tudományos virtuálisgép telepített eszközök
### <a name="microsoft-r-server"></a>Microsoft R Server
R egyike a legnépszerűbb nyelvet adatelemzés és a gépi tanulás. Ha azt szeretné, a analytics R használandó, a virtuális Gépnek legyen Microsoft R Server (Asszony) a Microsoft R megnyitása (MRO) és a matematikai Kernel könyvtár (MKL). A MKL matematikai műveletek közös az olyan analitikai algoritmusok optimalizálja. MRO 100 százalék CRAN-R kompatibilis, és a közzétett CRAN R könyvtárak bármelyikét a MRO is telepíthető. Asszony lehetővé teszi az méretezés és a webszolgáltatások R modellek operationalization. Az R programok az egyik az alapértelmezett szerkesztőt, például a Rstudióból, vi, Emacs vagy gedit szerkesztheti. A Emacs szerkesztő használatakor vegye figyelembe, hogy a Emacs csomag ELREJTÉSE (Emacs beszél statisztika), amely egyszerűbbé teszi a előre telepített R fájlok belül Emacs szerkesztő használatával lett.

Indítási R konzol, csak gépelje **R** a rendszerhéj. Ezzel megnyitná interaktív környezetben. Az R program elkészítéséhez egy szövegszerkesztőben, például a Emacs vagy vi vagy gedit általában használja, és futtassa az r parancsfájlok Az Rstudióból az R program fejlesztéséhez grafikus IDE környezettel rendelkezik.

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

    sudo /anaconda/bin/pip install <package> #pip for Python 2.7
    sudo /anaconda/envs/py35/bin/pip install <package> #pip for Python 3.5
    sudo /anaconda/bin/conda install [-n py27] <package> #conda for Python 2.7, default behavior
    sudo /anaconda/bin/conda install -n py35 <package> #conda for Python 3.5


### <a name="jupyter-notebook"></a>Jupyter notebook
A Anaconda terjesztési Jupyter notebook, egy környezet kóddal és elemzési is tartalmaz. A Jupyter notebook JupyterHub keresztül érhető el. A helyi Linux-felhasználónév és jelszó használatával bejelentkezik.

A Jupyter notebook kiszolgáló előre beállított Python 2, a Python 3 és az R kernelek. Nincs "Jupyter Notebook" Indítsa el a böngészőt a notebook kiszolgálóhoz való hozzáféréshez nevű asztali ikon. Ha a virtuális gépről SSH vagy X2Go ügyfél, meglátogathatja [ https://localhost:8000/ ](https://localhost:8000/) a Jupyter notebook kiszolgálóhoz való hozzáféréshez.

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
Az Apache Spark egy önálló példányát OEM a Linux DSVM is segítséget Spark-alkalmazások helyi először tesztelése és nagy fürtjein telepítése előtt. PySpark programok segítségével a Jupyter kernel futtathatja. Ha nyissa meg a Jupyter, és kattintson a **új** gomb, elérhető kernelek listáját kell megjelennie. A "Spark – Python" a PySpark kernel, amely lehetővé teszi a Spark olyan alkalmazásokat hozhat létre Python nyelven. A Python IDE PyCharm vagy Spyder, Spark program létrehozásához használhatja. Óta ez egy önálló példányát, a Spark-készlet belül a hívó ügyfél programot futtatja. Így gyorsabb és egyszerűbb a Spark-fürtön fejlesztése képest problémák hibaelhárítását. 

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
Azt, hogy több kód szerkesztők. Ez magában foglalja a vi/VIM, Emacs, gEdit, PyCharm, Rstudióból, eclipse-ben és az IntelliJ. gEdit, eclipse-ben, az IntelliJ, Rstudióból és PyCharm grafikus szerkesztőt, és kell jelentkeznie egy grafikus asztali is használhatja őket. Ezek szerkesztők rendelkezik asztal és alkalmazások parancsikonjai elindítani őket.

**VIM** és **Emacs** szöveges szerkesztők vannak. Emacs, a jelenleg telepített egy Emacs beszél statisztika (ELREJTÉSE), amely megkönnyíti a r munkanapon belül a Emacs szerkesztő nevű bővítmény csomagot. További információ található [ELREJTÉSE](http://ess.r-project.org/).

**Eclipse** a megnyitott forrás, amely támogatja több nyelv bővíthető IDE. Java fejlesztői verzióját a virtuális Gépen telepített példány. Nincsenek elérhető számos népszerű nyelvhez kiterjesztése a környezetben telepített beépülő modulok. Az eclipse-ben nevű telepített beépülő modul is tudunk **Eclipse Azure eszköztára**. Ez lehetővé teszi, fejleszthet, teszteléséhez, alkalmazások létrehozása és telepítése az Azure használata az Eclipse fejlesztői környezetet, amely támogatja a például a Java nyelven. Szerepel továbbá egy **Javához készült Azure SDK** , amely lehetővé teszi a különböző Azure-szolgáltatásokhoz való hozzáférés a Java-környezeten belül. További információk az Azure eszközkészlet az eclipse-ben található [Eclipse Azure eszköztára](../../azure-toolkit-for-eclipse.md).

**LaTex** Emacs bővítmény mellett a texlive csomag segítségével telepíthető [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) csomagot, amely egyszerűbbé teszi a szerzői LaTex dokumentumok Emacs belül.  

### <a name="databases"></a>Adatbázisok
#### <a name="postgres"></a>Postgres
A nyílt forráskódú adatbázis **Postgres** érhető el a virtuális Gépre, és a futó és initdb már befejeződött. Továbbra is szeretné létrehozni az adatbázisok és a felhasználók. További információkért lásd: a [Postgres dokumentáció](https://www.postgresql.org/docs/).  

#### <a name="graphical-sql-client"></a>Grafikus SQL-ügyfél
**SQuirrel SQL**, grafikus SQL-ügyfélen adtak meg (például a Microsoft SQL Server Postgres és MySQL) különböző adatbázisaihoz való kapcsolódásra, és az SQL-lekérdezések futtatásához. (Például használata X2Go ügyfél) grafikus asztali munkamenetből futtatható. SQuirrel SQL meghívni, indítsa el a az ikonra az asztalon, vagy futtassa a következő parancsot a rendszerhéj.

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

Hozzáférés **Postgres**:

* R: Használja a csomag **RPostgreSQL**.
* A Python: Használja a **psycopg2** könyvtárban.

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

* **Microsoft kognitív eszközkészlet** : egy eszközkészlet tanulási mély.
* **Vowpal Wabbit**: gyors online tanulási algoritmus.
* **xgboost**: olyan eszköz, amely biztosítja az optimalizált, súlyozott algoritmus.
* **Python**: Anaconda Python gépi tanulási algoritmusok könyvtárakkal Scikit – ismerje meg, például a csomagolt származik. Más tárak segítségével telepítheti a `pip install` parancsot.
* **R**: a machine learning funkciók gazdag könyvtár érhető el a R. A szalagtár szerepel, amely előre telepítve az lm, glm, randomForest, rpart. Más szalagtárak futtatásával telepíthető:
  
        install.packages(<lib name>)

Íme, néhány további információt a lista első három machine learning eszközeivel.

#### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit
Ez az egy nyílt forráskódú, mély eszközkészlet tanulási. A parancssori eszköz (cntk), és az elérési út már szerepel.

Egy alapszintű minta futtatásához a rendszerhéj hajtható végre a következő parancsokat:

    cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
    cntk configFile=lr_bs.cntk makeMode=false command=Train

További információ az című CNTK [GitHub](https://github.com/Microsoft/CNTK), és a [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).

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
   (Az aktuális kiadás Rattle egy hiba miatt be kell helyezni egy *#* karakter elé *... Ez a napló exportálása*  a szöveges napló.)
10. Kattintson a **exportálása** gombra kattintva mentse az R-parancsfájl nevű *weather_script. R* az otthoni mappába.

Kiléphet Rattle és R. Most módosítja a generált R-parancsfájl, vagy használja, mert az ismétlődő minden, ami a Rattle felhasználói felületen belül lett elvégezve bármikor futtatásához. Különösen az R kezdők, ez pedig egyszerűen gyorsan elemzést és a gépi tanulás egyszerű grafikus felületen, automatikusan az R módosítása és/vagy ismerje meg, a kód létrehozása közben.

## <a name="next-steps"></a>További lépések
Ez hogyan folytathatja a tanulási és feltárása:

* A [adattudomány lévő Linux adatok tudományos virtuálisgép](linux-dsvm-walkthrough.md) forgatókönyv bemutatja, hogyan itt kiépítése Linux adatok tudományos VM több közös tudományos feladatok elvégzésére. 
* Megismerkedhet a különböző adatok tudományos eszközök az adatok tudományos VM próbálhatja ki az eszközöket, a cikkben leírtak szerint. Is futtathatja *dsvm-további-információ* meg a rendszerhéj alapszintű bevezetést és mutatókat biztosít a virtuális Gépen telepített eszközök további információ a virtuális gépen belül.  
* Ismerje meg, hogyan hozhat létre végpont elemzési megoldásokat rendszeresen használatával a [Team adatok tudományos folyamat](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).
* Látogasson el a [Cortana Analytics Gallery](http://gallery.cortanaanalytics.com) machine learning és az adatok analytics mintákat a Cortana Analytics Suite használó.

