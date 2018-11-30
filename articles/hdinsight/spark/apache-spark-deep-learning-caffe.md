---
title: Caffe elosztott deep learning az Azure HDInsight Spark használata
description: Caffe elosztott deep learning az Azure HDInsight Spark használata
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/17/2017
ms.openlocfilehash: c63e2e3ec922d2cf26603fe19606008b1e8d3f45
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52498175"
---
# <a name="use-caffe-on-azure-hdinsight-spark-for-distributed-deep-learning"></a>Caffe elosztott deep learning az Azure HDInsight Spark használata


## <a name="introduction"></a>Bevezetés

Deep learning negatív hatással van, a szállítás fejlesszenek healthcare és egyebek. Vállalatok átállniuk, deep learning-megoldani a problémákat, például a [besorolási kép](https://blogs.microsoft.com/next/2015/12/10/microsoft-researchers-win-imagenet-computer-vision-challenge/), [beszédfelismerés](http://googleresearch.blogspot.jp/2015/08/the-neural-networks-behind-google-voice.html), felismerés objektum és a gépi fordítás. 

Nincsenek [számos népszerű keretrendszereket](https://en.wikipedia.org/wiki/Comparison_of_deep_learning_software), többek között [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/research/product/cognitive-toolkit/), [Tensorflow](https://www.tensorflow.org/), [Apache MXNet](https://mxnet.apache.org/), Theano, stb. [Caffe](http://caffe.berkeleyvision.org/) egyik a leghíresebb nem szimbolikus (imperatív) Neurális hálózat keretrendszereket, és számos készültséget többek között a számítógépes látástechnológiai széles körben használt. Ezenkívül [CaffeOnSpark](http://yahoohadoop.tumblr.com/post/139916563586/caffeonspark-open-sourced-for-distributed-deep) ötvözi az Apache Spark, ebben az esetben a mély tanulás a caffe használatával egyszerűen is használható egy már meglévő Hadoop-fürtön. Teljes körű megoldást learning mély tanulás a Spark ETL-adatcsatornák, és csökkenti rendszert, és a késleltetés együtt használható.

[HDInsight](https://azure.microsoft.com/services/hdinsight/) Apache Hadoop felhőalapú kínál, amely optimalizált, nyílt forráskódú analitikus fürtöket biztosít az Apache Spark, az Apache Hive, az Apache Hadoop, az Apache HBase, Apache Storm, Apache Kafka és Machine Learning-szolgáltatások. HDInsight 99,9 %-os szolgáltatói szerződés alapját. Ezen big data-technológiák és ISV-alkalmazások mindegyike könnyen üzembe helyezhető, a biztonsággal és figyeléssel kínál a nagyvállalatoknak felügyelt fürtként.

Ez a cikk bemutatja, hogyan telepítheti [Spark Caffe](https://github.com/yahoo/CaffeOnSpark) egy HDInsight-fürt. Ez a cikk is használja a MNIST beépített demót elosztott Deep Learning HDInsight Spark használatával a processzorokat használata megjelenítéséhez.

A feladatnak a négy lépésben történik:

1. Az összes csomóponton a szükséges függőségek telepítése
2. A Spark for HDInsight Caffe létrehozásához, az átjárócsomópont
3. A feldolgozó csomópontok, a szükséges kódtárak terjesztése
4. Caffe-modellek összeállítása, és elosztott módon futtathatja.

Mivel a HDInsight egy PaaS-megoldás, kínál nagyszerű platformot szolgáltatások – így egyszerűen az egyes feladatok elvégzéséhez. Ebben a blogbejegyzésben találhat használt funkcióinak egyike a nevezzük [Script Action](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux), amellyel rendszerhéjparancsokat szabhatja testre a fürtcsomópontok (átjárócsomóponthoz, munkavégző csomópont vagy élcsomópont) hajthat végre.

## <a name="step-1--install-the-required-dependencies-on-all-the-nodes"></a>1. lépés: A szükséges függőségek telepítése az összes csomóponton

Első lépésként telepítse a függőségeket kell. A Caffe-hely és [CaffeOnSpark hely](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn) kínál néhány hasznos wiki a függőségek telepítése a Spark a YARN módot. HDInsight Spark is használ a YARN módot. Azonban hozzá kell néhány további függőségek HDInsight platformon. Ehhez egy parancsfájlművelettel, és futtassa az átjárócsomópontokhoz és a feldolgozó csomópontokat. A parancsprogram-művelet körülbelül 20 percet vesz igénybe, ezeket a függőségeket is függenek más csomagokat. Az egy helyre, amely hozzáférhető annak a HDInsight-fürthöz, például egy GitHub-helyre vagy az alapértelmezett BLOB storage-fiók akkor kell helyezni.

    #!/bin/bash
    #Please be aware that installing the below will add additional 20 mins to cluster creation because of the dependencies
    #installing all dependencies, including the ones mentioned in http://caffe.berkeleyvision.org/install_apt.html, as well a few packages that are not included in HDInsight, such as gflags, glog, lmdb, numpy
    #It seems numpy will only needed during compilation time, but for safety purpose you install them on all the nodes

    sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler maven libatlas-base-dev libgflags-dev libgoogle-glog-dev liblmdb-dev build-essential  libboost-all-dev python-numpy python-scipy python-matplotlib ipython ipython-notebook python-pandas python-sympy python-nose

    #install protobuf
    wget https://github.com/google/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz
    sudo tar xzvf protobuf-2.5.0.tar.gz -C /tmp/
    cd /tmp/protobuf-2.5.0/
    sudo ./configure
    sudo make
    sudo make check
    sudo make install
    sudo ldconfig
    echo "protobuf installation done"


A szkriptműveletek két lépésből áll. Az első lépés, hogy a szükséges kódtárak telepítése. Ezek a könyvtárak tartalmazzák a szükséges kódtárak (például gflags, glog) Caffe fordítása és Caffe fut (például numpy). CPU-optimalizálás libatlas használ, de CaffeOnSpark wiki mindig követheti más optimalizálási könyvtárat, például MKL vagy CUDA (a GPU) telepítésével.

A második lépés, hogy töltse le, fordítsa le és telepítheti a protopuf 2.5.0 Caffe a Futtatás ideje alatt. A Protopuf 2.5.0 [szükséges](https://github.com/yahoo/CaffeOnSpark/issues/87), azonban ez a verzió nem érhető el a 16, az Ubuntu-csomagként így fordítsa le a forráskód szükséges. Vannak még néhány erőforrások az interneten, hogy hogyan. További információkért lásd: [Itt](http://jugnu-life.blogspot.com/2013/09/install-protobuf-25-on-ubuntu.html).

Első lépésként mindössze futtathatja a parancsprogram-művelet a fürtre küldött, a munkavégző csomópontok és a fő csomópontok (a HDInsight 3.5). A szkriptműveletek futtathat egy meglévő fürt, vagy parancsprogram-műveletek használata a fürt létrehozása során. A szkriptműveletek további információkért lásd: a dokumentáció [Itt](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux#view-history-promote-and-demote-script-actions).

![Szkriptműveletek függőségek telepítése](./media/apache-spark-deep-learning-caffe/Script-Action-1.png)


## <a name="step-2-build-caffe-on-apache-spark-for-hdinsight-on-the-head-node"></a>2. lépés: Épülnek Caffe Apache Spark for HDInsight az átjárócsomóponthoz

A második lépéseként, az átjárócsomópont Caffe buildet, és a lefordított tárak a feldolgozó csomópontokat, majd terjesztheti. Ebben a lépésben kell [ssh, az átjárócsomópont](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix). Ezt követően kell követnie a [CaffeOnSpark összeállítása folyamatban](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn). Alább a parancsfájl segítségével CaffeOnSpark készíthet néhány további lépést van. 

    #!/bin/bash
    git clone https://github.com/yahoo/CaffeOnSpark.git --recursive
    export CAFFE_ON_SPARK=$(pwd)/CaffeOnSpark

    pushd ${CAFFE_ON_SPARK}/caffe-public/
    cp Makefile.config.example Makefile.config
    echo "INCLUDE_DIRS += ${JAVA_HOME}/include" >> Makefile.config
    #Below configurations might need to be updated based on actual cases. For example, if you are using GPU, or using a different BLAS library, you may want to update those settings accordingly.
    echo "CPU_ONLY := 1" >> Makefile.config
    echo "BLAS := atlas" >> Makefile.config
    echo "INCLUDE_DIRS += /usr/include/hdf5/serial/" >> Makefile.config
    echo "LIBRARY_DIRS += /usr/lib/x86_64-linux-gnu/hdf5/serial/" >> Makefile.config
    popd

    #compile CaffeOnSpark
    pushd ${CAFFE_ON_SPARK}
    #always clean up the environment before building (especially when rebuiding), or there will be errors such as "failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occured: exec returned: 2"
    make clean 
    #the build step usually takes 20~30 mins, since it has a lot maven dependencies
    make build 
    popd
    export LD_LIBRARY_PATH=${CAFFE_ON_SPARK}/caffe-public/distribute/lib:${CAFFE_ON_SPARK}/caffe-distri/distribute/lib

    hadoop fs -mkdir -p wasb:///projects/machine_learning/image_dataset

    ${CAFFE_ON_SPARK}/scripts/setup-mnist.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/mnist_*_lmdb wasb:///projects/machine_learning/image_dataset/

    ${CAFFE_ON_SPARK}/scripts/setup-cifar10.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/cifar10_*_lmdb wasb:///projects/machine_learning/image_dataset/

    #put the already compiled CaffeOnSpark libraries to wasb storage, then read back to each node using script actions. This is because CaffeOnSpark requires all the nodes have the libarries
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-public/distribute/lib/
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-distri/distribute/lib/* /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-public/distribute/lib/* /CaffeOnSpark/caffe-public/distribute/lib/

Szükség lehet nagyobb, mint CaffeOnSpark a dokumentáció szerint. A változások a következők:
- CPU csak módosítsa, majd libatlas használni erre a célra.
- Helyezze az adatkészletek a BLOB Storage, amely egy megosztott hely, amely hozzáférhető annak összes munkavégző csomóponton későbbi használatra.
- Helyezze a lefordított Caffe-kódtárakat a BLOB storage, és később másolja ezeket a kódtárakat parancsfájlműveletekkel további fordítási idő elkerülése érdekében minden csomópont számára.


### <a name="troubleshooting-an-ant-buildexception-has-occurred-exec-returned-2"></a>Hibaelhárítás: Az Ant BuildException történt: exec adott vissza: 2

Amikor először próbálja CaffeOnSpark összeállítása, néha ugyanakkor

    failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occured: exec returned: 2

Tisztítsa meg a kódtárat a "tiszta ellenőrizze", majd az futtatási "márka build", a probléma megoldásához, mindaddig, amíg a megfelelő függőségekkel rendelkezik.

### <a name="troubleshooting-maven-repository-connection-time-out"></a>Hibaelhárítás: Maven tárház kapcsolat időkorlátja

Egyes esetekben a maven biztosít a kapcsolat időtúllépési hiba, az alábbi kódrészlethez hasonló:

    Retry:
    [INFO] Downloading: https://repo.maven.apache.org/maven2/com/twitter/chill_2.11/0.8.0/chill_2.11-0.8.0.jar
    Feb 01, 2017 5:14:49 AM org.apache.maven.wagon.providers.http.httpclient.impl.execchain.RetryExec execute
    INFO: I/O exception (java.net.SocketException) caught when processing request to {s}->https://repo.maven.apache.org:443: Connection timed out (Read failed)

Kell néhány perc múlva próbálkozzon újra.


### <a name="troubleshooting-test-failure-for-caffe"></a>Hibaelhárítás: Tesztelje a hiba a caffe használatával

Valószínűleg látni teszt hiba esetén a végső CaffeOnSpark ellenőrzését. Ez valószínűleg kapcsolódó UTF-8 kódolást, de nem érinti a Caffe használatát

    Run completed in 32 seconds, 78 milliseconds.
    Total number of tests run: 7
    Suites: completed 5, aborted 0
    Tests: succeeded 6, failed 1, canceled 0, ignored 0, pending 0
    *** 1 TEST FAILED ***

## <a name="step-3-distribute-the-required-libraries-to-all-the-worker-nodes"></a>3. lépés: A feldolgozó csomópontok, a szükséges kódtárak terjesztése

A következő lépés az, hogy a tárak elosztása (alapvetően a könyvtárak a CaffeOnSpark/caffe-nyilvános/terjesztése/lib/és CaffeOnSpark/caffe-hozzárendelése/terjesztése/lib /), az összes csomóponton. A 2. lépés a tárak elhelyezése a BLOB storage, és ebben a lépésben másolja azt a fő csomópontból és a feldolgozó csomópontok használhatja parancsfájlműveletekkel.

Ehhez futtassa egy parancsprogram-művelet az alábbi kódrészletben látható módon:

    #!/bin/bash
    hadoop fs -get wasb:///CaffeOnSpark /home/changetoyourusername/

Ellenőrizze, hogy kell a megfelelő helyre pont adott a fürthöz)

A 2. lépésben, akkor helyezi azt a BLOB storage, amely elérhető az összes csomóponthoz, mivel ebben a lépésben, egyszerűen másolja a csomópontokon.

## <a name="step-4-compose-a-caffe-model-and-run-it-in-a-distributed-manner"></a>4. lépés: A Caffe modell összeállítása, és futtassa azt egy elosztott módon

Caffe telepítve van az előző lépések futtatása után. A következő lépés az írási modell Caffe. 

Caffe-"kifejező architektúra használatával", ahol a modellek összeállítása, egyszerűen adja meg a konfigurációs fájlt, és nélkül kódolási minden (a legtöbb esetben). Ezért Tekintsünk meg ott. 

A modell betanítását, MNIST képzéshez mintamodell. A kézírásos számjegyből MNIST adatbázis rendelkezik egy képzési 60 000 példákat, és egy tesztelési 10 000 példák. A korábbiakhoz NIST elérhető egy része. A számjegyek normalizált méret és a egy rögzített méretű kép középre törölték. CaffeOnSpark rendelkezik néhány parancsprogramot, töltse le az adatkészlet és a megfelelő formátumba alakítsa át.

CaffeOnSpark bizonyos hálózati topológiák például MNIST képzést biztosít. A megosztással a hálózati architektúra (a hálózati topológia) és optimalizálás a már jól néz kis tervezési rendelkezik. Ebben az esetben van szükség két fájlt: 

a "Solver" fájlt (${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt) paraméter frissítések egyik és környezet optimalizálása szolgál. Például azt határozza hogy CPU vagy GPU használatos, mi az a hír, az ismétlések számát a rendszer, stb. Azt is meghatározza melyik idegsejt hálózati topológia használjon a program (amely a második fájl szükséges). Solver kapcsolatos további információkért lásd: [Caffe dokumentáció](http://caffe.berkeleyvision.org/tutorial/solver.html).

Ebben a példában óta használja GPU, hanem CPU módosítania kell az utolsó sora:

    # solver mode: CPU or GPU
    solver_mode: CPU

![Caffe-Config](./media/apache-spark-deep-learning-caffe/Caffe-1.png)

Egyéb vonalat igény szerint módosíthatók.

A második fájl ({CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt$) határozza meg, hogyan néz ki a idegsejt hálózathoz, például a, és a megfelelő bemeneti és kimeneti fájl. is frissíteni szeretné a fájlt, hogy tükrözze a betanítási adatok helye. A következő részben lenet_memory_train_test.prototxt (kell, hogy adott a fürthöz a megfelelő helyre mutasson) történő módosítása:

- Módosítsa a "file:/Users/mridul/bigml/demodl/mnist_train_lmdb" "wasb: / / / projektek/machine_learning/image_dataset/mnist_train_lmdb"
- "file:/Users/mridul/bigml/demodl/mnist_test_lmdb/" módosítsa "wasb: / / / projektek/machine_learning/image_dataset/mnist_test_lmdb"

![Caffe-Config](./media/apache-spark-deep-learning-caffe/Caffe-2.png)

A hálózat definiálása további információkért ellenőrizze a [MNIST adatkészlet Caffe-dokumentáció](http://caffe.berkeleyvision.org/gathered/examples/mnist.html)

Ez a cikk céljából MNIST ebben a példában használja. Futtassa a következő parancsokat az átjárócsomóponthoz:

    spark-submit --master yarn --deploy-mode cluster --num-executors 8 --files ${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt,${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt --conf spark.driver.extraLibraryPath="${LD_LIBRARY_PATH}" --conf spark.executorEnv.LD_LIBRARY_PATH="${LD_LIBRARY_PATH}" --class com.yahoo.ml.caffe.CaffeOnSpark ${CAFFE_ON_SPARK}/caffe-grid/target/caffe-grid-0.1-SNAPSHOT-jar-with-dependencies.jar -train -features accuracy,loss -label label -conf lenet_memory_solver.prototxt -devices 1 -connection ethernet -model wasb:///mnist.model -output wasb:///mnist_features_result

Az előző parancs minden egyes YARN tárolóba osztja el a szükséges fájlokat (lenet_memory_solver.prototxt és lenet_memory_train_test.prototxt). A parancs is állítja be a megfelelő ELÉRÉSI útját minden Spark-illesztő/végrehajtó LD_LIBRARY_PATH. Az előző kódrészletben és, amely rendelkezik CaffeOnSpark könyvtárak a helyre mutat, LD_LIBRARY_PATH van meghatározva. 

## <a name="monitoring-and-troubleshooting"></a>Megfigyelés és hibaelhárítás

YARN-fürt üzemmód használja, mivel a Spark-illesztő lesz ütemezve egy tetszőleges tárolóhoz (és a egy tetszőleges munkavégző csomópont) ebben az esetben csak kell megjelennie a konzolon szerint kiírta volna az alábbihoz hasonló:

    17/02/01 23:22:16 INFO Client: Application report for application_1485916338528_0015 (state: RUNNING)

Ha szeretné tudni, hogy mi történt, általában szüksége a Spark vezető naplót, amely tartalmaz további információkat lekérni. Ebben az esetben kell nyissa meg a YARN felhasználói felületén található a releváns YARN-naplóit. A YARN felhasználói felületén az URL-cím szerint kaphat: 

    https://yourclustername.azurehdinsight.net/yarnui
   
![YARN FELHASZNÁLÓI FELÜLETÉN](./media/apache-spark-deep-learning-caffe/YARN-UI-1.png)

Tekintse meg az adott alkalmazás hány erőforrások lefoglalásának is igénybe vehet. "A Scheduler" hivatkozásra kattint, és ezután látni fogja, hogy a jelen alkalmazás esetében nincsenek kilenc futtató tárolókat. YARN nyolc végrehajtóval biztosításához tegye fel, és egy másik tárolóba illesztőprogram a folyamatot. 

![YARN-ütemező](./media/apache-spark-deep-learning-caffe/YARN-Scheduler.png)

Érdemes ellenőrizni az illesztőprogram-naplók vagy a tároló naplóinak van-e hibák. Az illesztőprogram-naplók kattintson az Alkalmazásazonosítót a YARN felhasználói felületén, majd kattintson a "Naplózza" gombra. Az illesztőprogram-naplók az stderr készültek.

![A YARN FELHASZNÁLÓI FELÜLETÉN 2](./media/apache-spark-deep-learning-caffe/YARN-UI-2.png)

Például előfordulhat, hogy megjelenik a hiba az illesztőprogram-naplók, az alábbi néhány arról túl sok végrehajtóval foglal le.

    17/02/01 07:26:06 ERROR ApplicationMaster: User class threw exception: java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
    java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
        at com.yahoo.ml.caffe.CaffeOnSpark.trainWithValidation(CaffeOnSpark.scala:261)
        at com.yahoo.ml.caffe.CaffeOnSpark$.main(CaffeOnSpark.scala:42)
        at com.yahoo.ml.caffe.CaffeOnSpark.main(CaffeOnSpark.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.spark.deploy.yarn.ApplicationMaster$$anon$2.run(ApplicationMaster.scala:627)

Egyes esetekben a probléma akkor fordulhat elő, illesztőprogramok helyett végrehajtóval. Ebben az esetben kell ellenőrizze a tároló naplóit. Mindig az a tároló naplóinak beolvasása, és kérje le a sikertelen tárolót. Ez a hiba például Caffe futtatásakor előfordulhat, hogy megfelel.

    17/02/01 07:12:05 WARN YarnAllocator: Container marked as failed: container_1485916338528_0008_05_000005 on host: 10.0.0.14. Exit status: 134. Diagnostics: Exception from container-launch.
    Container id: container_1485916338528_0008_05_000005
    Exit code: 134
    Exception message: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

    Stack trace: ExitCodeException exitCode=134: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

        at org.apache.hadoop.util.Shell.runCommand(Shell.java:933)
        at org.apache.hadoop.util.Shell.run(Shell.java:844)
        at org.apache.hadoop.util.Shell$ShellCommandExecutor.execute(Shell.java:1123)
        at org.apache.hadoop.yarn.server.nodemanager.DefaultContainerExecutor.launchContainer(DefaultContainerExecutor.java:225)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:317)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:83)
        at java.util.concurrent.FutureTask.run(FutureTask.java:266)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)


    Container exited with a non-zero exit code 134

Ebben az esetben kell beszereznie a sikertelen Tárolóazonosító (a fenti esetben container_1485916338528_0008_05_000005). Akkor kell futtatnia 

    yarn logs -containerId container_1485916338528_0008_03_000005

az átjárócsomópont a. Tároló hiba az ellenőrzés után okozza (ahol érdemes inkább CPU-üzemmódra) GPU mód használata a lenet_memory_solver.prototxt.

    17/02/01 07:10:48 INFO LMDB: Batch size:100
    WARNING: Logging before InitGoogleLogging() is written to STDERR
    F0201 07:10:48.309725 11624 common.cpp:79] Cannot use GPU in CPU-only Caffe: check mode.


## <a name="getting-results"></a>Eredmények beolvasása

8 végrehajtóval osztja fel, és a hálózati topológia egyszerű, mivel kell csak igénybe körülbelül 30 percet futtatni az eredmény. A parancssorból, láthatja, hogy a modell használatba wasb:///mnist.model, és jelenítse meg az eredményeket nevű wasb: / / / mnist_features_result.

Az eredmények lekéréséhez futtassa

    hadoop fs -cat hdfs:///mnist_features_result/*

és az eredmény a következőhöz hasonló:

    {"SampleID":"00009597","accuracy":[1.0],"loss":[0.028171852],"label":[2.0]}
    {"SampleID":"00009598","accuracy":[1.0],"loss":[0.028171852],"label":[6.0]}
    {"SampleID":"00009599","accuracy":[1.0],"loss":[0.028171852],"label":[1.0]}
    {"SampleID":"00009600","accuracy":[0.97],"loss":[0.0677709],"label":[5.0]}
    {"SampleID":"00009601","accuracy":[0.97],"loss":[0.0677709],"label":[0.0]}
    {"SampleID":"00009602","accuracy":[0.97],"loss":[0.0677709],"label":[1.0]}
    {"SampleID":"00009603","accuracy":[0.97],"loss":[0.0677709],"label":[2.0]}
    {"SampleID":"00009604","accuracy":[0.97],"loss":[0.0677709],"label":[3.0]}
    {"SampleID":"00009605","accuracy":[0.97],"loss":[0.0677709],"label":[4.0]}

A SampleID MNIST adatkészlet Azonosítóját jelöli, a címke pedig a modell azonosító szám.


## <a name="conclusion"></a>Összegzés

Ebben a dokumentációban próbált CaffeOnSpark telepítheti egy egyszerű példa futtatása. HDInsight egy teljes körű felügyelt felhőbeli elosztott számítási platform és a legjobb hely a machine learning és a fejlett elemzési számítási feladatok futtatása nagyméretű, és elosztott mély tanulás, segítségével Caffe HDInsight Spark hajtsa végre a deep learning a feladatok.


## <a name="seealso"></a>Lásd még:
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Az Apache Spark és Machine Learning: a Spark on HDInsight HVAC-adatok épület-hőmérséklet elemzésére a használata](apache-spark-ipython-notebook-machine-learning.md)
* [Az Apache Spark és Machine Learning: használja a Spark on HDInsight az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)

