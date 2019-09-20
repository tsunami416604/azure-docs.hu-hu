---
title: A Cafe on Azure HDInsight Spark használata elosztott mély tanuláshoz
description: Az Azure HDInsight-ben a Cafe on Apache Spark használatával elosztott Deep learning szolgáltatást használhat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/17/2017
ms.openlocfilehash: e0490913029efc17d12139378369646c286a276c
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71145710"
---
# <a name="use-caffe-on-azure-hdinsight-spark-for-distributed-deep-learning"></a>A Cafe on Azure HDInsight Spark használata elosztott mély tanuláshoz

## <a name="introduction"></a>Bevezetés

A Deep learning az egészségügyi ellátásról a gyártásba való szállításra és egyebekre is hatással van. A vállalatok mély tanulást folytatnak a problémák megoldásában, például a [képosztályozás](https://blogs.microsoft.com/next/2015/12/10/microsoft-researchers-win-imagenet-computer-vision-challenge/), a [beszédfelismerés](https://googleresearch.blogspot.jp/2015/08/the-neural-networks-behind-google-voice.html), az objektumok felismerése és a gépi fordítás terén.

[Számos népszerű keretrendszer](https://en.wikipedia.org/wiki/Comparison_of_deep_learning_software)létezik, többek között a [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/research/product/cognitive-toolkit/), a [Tensorflow](https://www.tensorflow.org/), az [Apache MXNet](https://mxnet.apache.org/), a theano stb. A [Cafe](https://caffe.berkeleyvision.org/) a legismertebb, nem szimbolikus (kötelező) neurális hálózati keretrendszerek egyike, és széles körben használatos számos területen, beleértve a számítógépes jövőképet is. Emellett a [CaffeOnSpark](https://yahoohadoop.tumblr.com/post/139916563586/caffeonspark-open-sourced-for-distributed-deep) a Cafe-t a Apache Spark-mel ötvözi, ebben az esetben a Deep learning könnyen használható egy meglévő Hadoop-fürtön. A Deep learning és a Spark ETL-folyamatok együttes használata, a rendszer bonyolultságának csökkentése és a megoldás teljes körű késése.

A [HDInsight](https://azure.microsoft.com/services/hdinsight/) egy olyan felhőalapú Apache Hadoop ajánlat, amely optimalizált, nyílt forráskódú analitikai fürtöket biztosít Apache Spark, Apache Hive, Apache Hadoop, Apache HBase, Apache Storm, Apache KAFKA és ml szolgáltatásokhoz. A HDInsight a 99,9%-os SLA-val támogatja. Ezek a big data technológiák és ISV-alkalmazások könnyen telepíthetők felügyelt fürtökként a vállalatok biztonságával és monitorozásával.

Ez a cikk bemutatja, hogyan telepítheti a [Cafe on Spark](https://github.com/yahoo/CaffeOnSpark) -t egy HDInsight-fürtre. Ez a cikk a beépített MNIST bemutatóját is bemutatja, amely azt mutatja be, hogyan használhatók az elosztott Deep learning a HDInsight Spark használatával a processzorokon.

A feladat végrehajtása négy lépésből áll:

1. A szükséges függőségek telepítése az összes csomóponton
2. Build Cafe Spark for HDInsight a fő csomóponton
3. A szükséges kódtárak terjesztése az összes munkavégző csomópont számára
4. Egy Cafe-modell összeállításával és elosztott módon történő futtatásával.

Mivel a HDInsight egy Pásti-megoldás, nagyszerű platform-funkciókat kínál, így könnyen elvégezhető néhány feladat. Az ebben a blogbejegyzésben használt egyik funkciót [parancsfájl-műveletnek](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)nevezzük, amellyel rendszerhéj-parancsokat futtathat a fürtcsomópontok (a fő csomópont, a feldolgozó csomópont vagy a peremhálózati csomópont) testreszabásához.

## <a name="step-1--install-the-required-dependencies-on-all-the-nodes"></a>1\. lépés:  A szükséges függőségek telepítése az összes csomóponton

Első lépésként telepítenie kell a függőségeket. A Cafe-hely és a [CaffeOnSpark-hely](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn) néhány hasznos wikit kínál a Spark-függőségek a fonal módban való telepítéséhez. A HDInsight a Spark on szál módot is használja. Azonban hozzá kell adnia néhány függőséget a HDInsight platformhoz. Ehhez használjon parancsfájl-műveletet, és futtassa azt az összes fő csomóponton és munkavégző csomóponton. Ez a parancsfájl körülbelül 20 percet vesz igénybe, mivel ezek a függőségek más csomagoktól is függenek. A HDInsight-fürt számára elérhető helyen kell elhelyezni, például egy GitHub-helyen vagy az alapértelmezett BLOB Storage-fiókban.

    #!/bin/bash
    #Please be aware that installing the below will add additional 20 mins to cluster creation because of the dependencies
    #installing all dependencies, including the ones mentioned in https://caffe.berkeleyvision.org/install_apt.html, as well a few packages that are not included in HDInsight, such as gflags, glog, lmdb, numpy
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

A parancsfájl művelet két lépésből áll. Első lépésként telepítse az összes szükséges tárat. Ezek a kódtárak magukban foglalják a szükséges kódtárakat a Cafe fordításához (például Gflags, GLOG) és a Cafe futtatásához (például NumPy). libatlas használ a CPU-optimalizáláshoz, de bármikor követheti a CaffeOnSpark wikit más optimalizációs könyvtárak, például a MKL vagy a CUDA (GPU) telepítésével.

A második lépés a protopuf 2.5.0 letöltése, fordítása és telepítése az futtatókörnyezethez a Runtime során. A protopuf 2.5.0 [megadása kötelező](https://github.com/yahoo/CaffeOnSpark/issues/87), azonban ez a verzió nem érhető el csomagként Ubuntu 16 rendszeren, ezért le kell fordítania azt a forráskódból. Az interneten is van néhány erőforrás a fordításához. További információ: [itt](https://jugnu-life.blogspot.com/2013/09/install-protobuf-25-on-ubuntu.html).

Első lépésként futtassa ezt a szkriptet a fürtön a munkavégző csomópontok és a fő csomópontok (HDInsight 3,5) számára. Futtathatja a parancsfájl műveleteit egy meglévő fürtön, vagy parancsfájl-műveleteket is használhat a fürt létrehozása során. A parancsfájl-műveletekkel kapcsolatos további információkért tekintse meg a dokumentációt [itt](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

![Függőségek telepítésére szolgáló parancsfájl-műveletek](./media/apache-spark-deep-learning-caffe/submit-script-action.png)

## <a name="step-2-build-caffe-on-apache-spark-for-hdinsight-on-the-head-node"></a>2\. lépés: Build Cafe Apache Spark HDInsight a fő csomóponton

A második lépés a Cafe kiépítése a átjárócsomóponthoz, majd a lefordított kódtárak terjesztése az összes munkavégző csomópontra. Ebben a lépésben az SSH- [t kell a átjárócsomóponthoz](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix). Ezt követően a [CaffeOnSpark-létrehozási folyamatnak](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn)kell megfelelnie. Az alábbi szkripttel hozhat létre CaffeOnSpark néhány további lépéssel.

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
    #always clean up the environment before building (especially when rebuiding), or there will be errors such as "failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2"
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

Előfordulhat, hogy többet kell tennie, mint amit a CaffeOnSpark dokumentációjában talál. A módosítások a következők:
- Váltson csak CPU-ra, és használja a libatlas erre az adott célra.
- Helyezze el az adatkészleteket a BLOB Storage-ba, amely egy megosztott hely, amely az összes munkavégző csomópont számára elérhető a későbbi használat érdekében.
- Helyezze a lefordított Cafe-kódtárakat a BLOB Storage-ba, majd később másolja a kódtárakat az összes csomópontra a parancsfájlok használatával, hogy elkerülje a további fordítási időt.

### <a name="troubleshooting-an-ant-buildexception-has-occurred-exec-returned-2"></a>Hibaelhárítás Egy Ant-BuildException történt: az exec visszaadott: 2

Amikor először próbál létrehozni CaffeOnSpark, néha

    failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2

A probléma megoldásához szüntesse meg a kód tárházának tisztítását, majd futtassa a "make build" parancsot, ha a megfelelő függőségekkel rendelkezik.

### <a name="troubleshooting-maven-repository-connection-time-out"></a>Hibaelhárítás Maven-tárház kapcsolatainak időtúllépése

Időnként a Maven a következő kódrészlethez hasonló időtúllépési hibát biztosít:

    Retry:
    [INFO] Downloading: https://repo.maven.apache.org/maven2/com/twitter/chill_2.11/0.8.0/chill_2.11-0.8.0.jar
    Feb 01, 2017 5:14:49 AM org.apache.maven.wagon.providers.http.httpclient.impl.execchain.RetryExec execute
    INFO: I/O exception (java.net.SocketException) caught when processing request to {s}->https://repo.maven.apache.org:443: Connection timed out (Read failed)

Néhány perc múlva újra kell próbálkoznia.

### <a name="troubleshooting-test-failure-for-caffe"></a>Hibaelhárítás Tesztelési hiba a Cafe-ban

A CaffeOnSpark végső ellenőrzésének végrehajtásakor valószínűleg sikertelen volt a teszt. Ez valószínűleg az UTF-8 kódolással kapcsolódik, de nem befolyásolhatja a Cafe használatát

    Run completed in 32 seconds, 78 milliseconds.
    Total number of tests run: 7
    Suites: completed 5, aborted 0
    Tests: succeeded 6, failed 1, canceled 0, ignored 0, pending 0
    *** 1 TEST FAILED ***

## <a name="step-3-distribute-the-required-libraries-to-all-the-worker-nodes"></a>3\. lépés: A szükséges kódtárak terjesztése az összes munkavégző csomópont számára

A következő lépés a kódtárak terjesztése (alapvetően a CaffeOnSpark-/Cafe-Public/Distribution/lib/és CaffeOnSpark/Cafe-történő/Distribute/a) összes csomópontjának kódtára. A 2. lépésben ezeket a kódtárakat a BLOB Storage-ba helyezi el, és ebben a lépésben parancsfájl-műveletek használatával másolja azt az összes fő csomópontra és munkavégző csomópontra.

Ehhez futtasson egy parancsfájl-műveletet az alábbi kódrészletben látható módon:

    #!/bin/bash
    hadoop fs -get wasb:///CaffeOnSpark /home/changetoyourusername/

Győződjön meg arról, hogy a fürthöz tartozó megfelelő helyre kell mutatnia.

Mivel a 2. lépésben a BLOB Storage-ba helyezi azt, amely az összes csomópont számára elérhető, ebben a lépésben egyszerűen másolja az összes csomópontra.

## <a name="step-4-compose-a-caffe-model-and-run-it-in-a-distributed-manner"></a>4\. lépés: Egy Cafe-modell összeállítása és elosztott módon történő futtatása

A Cafe telepítése az előző lépések futtatása után történik. A következő lépés egy Cafe-modell írása. 

A Cafe egy "kifejező architektúrát" használ, ahol a modell összeállításához csak egy konfigurációs fájlt kell definiálnia, és nem kell kódolást végeznie (a legtöbb esetben). Nézzük meg, hogy. 

Az Ön által betanított modell a MNIST képzéshez használható minta modell. A kézzel írt számjegyek MNIST-adatbázisa 60 000 példákkal és 10 000-es tesztelési készlettel rendelkezik. Ez egy, a NIST által elérhető nagyobb készlet részhalmaza. A számjegyek mérete normalizálva lett, és középre igazított, rögzített méretű ábrán. A CaffeOnSpark néhány szkripttel tölti le az adatkészletet, és a megfelelő formátumba alakítja át.

A CaffeOnSpark néhány hálózati topológiát biztosít például a MNIST betanításához. Remek terve a hálózati architektúra (a hálózat topológiája) és az optimalizálás felosztására. Ebben az esetben két fájl szükséges:

a "Solver" fájl ($ {CAFFE_ON_SPARK}/Data/lenet_memory_solver.prototxt) az optimalizálás és a paraméter-létrehozási frissítések felügyeletére szolgál. Meghatározhatja például, hogy a CPU vagy a GPU használatban van-e, mi a lendület, hány ismétlés van, stb. Azt is meghatározza, hogy a program melyik neuron-hálózati topológiát használja (ami a szükséges második fájl). A Solverrel kapcsolatos további információkért lásd: a [Cafe dokumentációja](https://caffe.berkeleyvision.org/tutorial/solver.html).

Ehhez a példához, mivel a GPU helyett CPU-t használ, az utolsó sort kell megváltoztatnia a következőre:

    # solver mode: CPU or GPU
    solver_mode: CPU

![HDInsight Cafe-konfiguráció – példa](./media/apache-spark-deep-learning-caffe/caffe-configuration1.png
)

Szükség szerint más vonalakat is módosíthat.

A második fájl ($ {CAFFE_ON_SPARK}/Data/lenet_memory_train_test.prototxt) meghatározza, hogyan néz ki a neuron-hálózat, valamint a megfelelő bemeneti és kimeneti fájl. Emellett frissítenie kell a fájlt, hogy az tükrözze a betanítási adatterületet. Módosítsa a következő részt a lenet_memory_train_test. prototxt (a fürthöz tartozó megfelelő helyre kell mutatnia):

- change the "file:/Users/mridul/bigml/demodl/mnist_train_lmdb" to "wasb:///projects/machine_learning/image_dataset/mnist_train_lmdb"
- change "file:/Users/mridul/bigml/demodl/mnist_test_lmdb/" to "wasb:///projects/machine_learning/image_dataset/mnist_test_lmdb"

![HDInsight Cafe-konfiguráció – példa](./media/apache-spark-deep-learning-caffe/caffe-configuration2.png)

A hálózat definiálásával kapcsolatos további információkért olvassa el a [Cafe dokumentációját a MNIST adatkészleten](https://caffe.berkeleyvision.org/gathered/examples/mnist.html) .

Ebben a cikkben ezt a MNIST példát használjuk. Futtassa a következő parancsokat a fő csomópontról:

    spark-submit --master yarn --deploy-mode cluster --num-executors 8 --files ${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt,${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt --conf spark.driver.extraLibraryPath="${LD_LIBRARY_PATH}" --conf spark.executorEnv.LD_LIBRARY_PATH="${LD_LIBRARY_PATH}" --class com.yahoo.ml.caffe.CaffeOnSpark ${CAFFE_ON_SPARK}/caffe-grid/target/caffe-grid-0.1-SNAPSHOT-jar-with-dependencies.jar -train -features accuracy,loss -label label -conf lenet_memory_solver.prototxt -devices 1 -connection ethernet -model wasb:///mnist.model -output wasb:///mnist_features_result

Az előző parancs elosztja a szükséges fájlokat (lenet_memory_solver. prototxt és lenet_memory_train_test. prototxt) minden egyes fonal-tárolóra. A parancs az egyes Spark-illesztők/végrehajtók megfelelő elérési útját is beállítja a LD_LIBRARY_PATH. A LD_LIBRARY_PATH az előző kódrészletben van definiálva, és arra a helyre mutat, amely a CaffeOnSpark-tárakat tartalmazta. 

## <a name="monitoring-and-troubleshooting"></a>Figyelés és hibaelhárítás

Mivel a FONÁL-fürt üzemmódot használja, ebben az esetben a Spark-illesztőprogram egy tetszőleges tárolóba (és egy tetszőleges feldolgozó csomópontra) lesz ütemezve, csak a konzolon kell megjelennie, például:

    17/02/01 23:22:16 INFO Client: Application report for application_1485916338528_0015 (state: RUNNING)

Ha tudni szeretné, hogy mi történt, általában be kell szereznie a Spark-illesztőprogram naplóját, amely további információkat tartalmaz. Ebben az esetben a fonal felhasználói felületét kell megkeresnie, hogy megtalálja a megfelelő fonal-naplókat. A fonal felhasználói felületét az alábbi URL-cím alapján érheti el:

    https://yourclustername.azurehdinsight.net/yarnui

![Apache fonál Scheduler böngésző nézet](./media/apache-spark-deep-learning-caffe/apache-yarn-window-1.png)

Megtekintheti, hogy hány erőforrás van lefoglalva az adott alkalmazáshoz. Az "ütemező" hivatkozásra kattintva láthatja, hogy ehhez az alkalmazáshoz kilenc tároló fut. a FONALat arra kéri, hogy nyolc végrehajtót adjon meg, és egy másik tárolót az illesztőprogram-folyamathoz. 

![HDI Apache FONÁL Scheduler nézet](./media/apache-spark-deep-learning-caffe/apache-yarn-scheduler.png)

Ha hiba történik, érdemes lehet megtekinteni az illesztőprogram-naplókat vagy a tároló naplóit. Illesztőprogram-naplók esetén kattintson az alkalmazás-AZONOSÍTÓra a fonal felhasználói felületén, majd kattintson a "naplók" gombra. Az illesztőprogram-naplók bekerülnek a stderr.

![Apache fonál ablak böngésző nézete](./media/apache-spark-deep-learning-caffe/apache-yarn-window-2.png)

Előfordulhat például, hogy az illesztőprogram-naplókból az alábbi hibaüzenet jelenik meg, ami azt jelzi, hogy túl sok végrehajtót foglal le.

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

Időnként előfordulhat, hogy a probléma az illesztőprogramok helyett a végrehajtókban fordul elő. Ebben az esetben ellenőriznie kell a tároló naplóit. Bármikor lekérheti a tároló naplóit, és lekérheti a sikertelen tárolót. Előfordulhat például, hogy ez a hiba a Cafe futtatásakor is megfelel.

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

Ebben az esetben le kell kérnie a sikertelen tároló AZONOSÍTÓját (a fenti esetben container_1485916338528_0008_05_000005). Ezután futtatnia kell 

    yarn logs -containerId container_1485916338528_0008_03_000005

a átjárócsomóponthoz. A tároló hibájának ellenőrzése után a rendszer GPU-módot használ (ha ehelyett CPU-módot kell használnia) a lenet_memory_solver. prototxt.

    17/02/01 07:10:48 INFO LMDB: Batch size:100
    WARNING: Logging before InitGoogleLogging() is written to STDERR
    F0201 07:10:48.309725 11624 common.cpp:79] Cannot use GPU in CPU-only Caffe: check mode.

## <a name="getting-results"></a>Eredmények beolvasása

Mivel 8 végrehajtót foglal le, és a hálózati topológia egyszerű, csak körülbelül 30 percet vesz igénybe az eredmény futtatása. A parancssorban láthatja, hogy a modellt wasb:///mnist.model, és az eredményeket egy wasb:///mnist_features_result nevű mappába helyezi.

Az eredmények a futtatásával szerezhetők be

    hadoop fs -cat hdfs:///mnist_features_result/*

az eredmény így néz ki:

    {"SampleID":"00009597","accuracy":[1.0],"loss":[0.028171852],"label":[2.0]}
    {"SampleID":"00009598","accuracy":[1.0],"loss":[0.028171852],"label":[6.0]}
    {"SampleID":"00009599","accuracy":[1.0],"loss":[0.028171852],"label":[1.0]}
    {"SampleID":"00009600","accuracy":[0.97],"loss":[0.0677709],"label":[5.0]}
    {"SampleID":"00009601","accuracy":[0.97],"loss":[0.0677709],"label":[0.0]}
    {"SampleID":"00009602","accuracy":[0.97],"loss":[0.0677709],"label":[1.0]}
    {"SampleID":"00009603","accuracy":[0.97],"loss":[0.0677709],"label":[2.0]}
    {"SampleID":"00009604","accuracy":[0.97],"loss":[0.0677709],"label":[3.0]}
    {"SampleID":"00009605","accuracy":[0.97],"loss":[0.0677709],"label":[4.0]}

A SampleID a MNIST adatkészlet AZONOSÍTÓját jelöli, a címke pedig az a szám, amelyet a modell azonosít.

## <a name="conclusion"></a>Összegzés

Ebben a dokumentációban megpróbálta telepíteni a CaffeOnSpark-t egy egyszerű példa futtatásával. A HDInsight egy teljes körűen felügyelt felhőalapú elosztott számítási platform, amely a gépi tanulás és a fejlett analitikai feladatok nagy adatkészleten való futtatására, valamint az elosztott, mély tanulásra is használható feladatok.

## <a name="seealso"></a>Lásd még:

* [Áttekintés Apache Spark az Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark a Machine Learningkal: A Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learningkal: Az élelmiszer-vizsgálati eredmények előrejelzése a Spark in HDInsight használatával](apache-spark-machine-learning-mllib-ipython.md)

### <a name="manage-resources"></a>Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
