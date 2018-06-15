---
title: Az Apache Spark feladat futtatása Azure Kubernetes szolgáltatás (AKS)
description: Az Apache Spark feladat futtatásához használt Azure Kubernetes szolgáltatás (AKS)
services: container-service
author: lenadroid
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/15/2018
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: cb23c21fd22a35a3e8a5920a94aa5a89fe966cfa
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33934945"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Apache Spark feladatok futó AKS

[Az Apache Spark on] [ apache-spark] egy gyors motor nagy méretű adatok feldolgozásához. A a [Spark 2.3.0 kiadás][spark-latest-release], Apache Spark támogatja a natív integráció az Kubernetes fürtökkel. Azure Kubernetes szolgáltatás (AKS) az Azure-ban futó felügyelt Kubernetes környezetben. Ez a dokumentum részletesen előkészítése és futó Apache Spark feladatok Azure Kubernetes szolgáltatás (AKS) fürtökön.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk belül lépések végrehajtásához a következők szükségesek.

* Alapvető ismeretekkel Kubernetes és [Apache Spark][spark-quickstart].
* [Docker Hub] [ docker-hub] fiókot, vagy egy [Azure tároló beállításjegyzék][acr-create].
* Az Azure CLI [telepített] [ azure-cli] a fejlesztői rendszeren.
* [JDK 8] [ java-install] telepítve a rendszeren.
* SBT ([Scala Build eszköz][sbt-install]) telepítve a rendszeren.
* Git parancssori eszközök van telepítve a rendszeren.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Spark nagy méretű adatok feldolgozásához használt, és megköveteli, hogy a külső erőforrások követelményeinek megfelelően Kubernetes csomópontok mérete. Azt javasoljuk, hogy a minimális méret `Standard_D3_v2` az Azure Kubernetes szolgáltatás (AKS) csomópontok.

Ha egy AKS fürt, amely megfelel a minimális javaslaton van szüksége, a következő parancsokat.

Hozzon létre egy erőforráscsoportot a fürthöz.

```azurecli
az group create --name mySparkCluster --location eastus
```

Hozzon létre a AKS fürtöt csomópontot, amely méretű `Standard_D3_v2`.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2
```

Csatlakozzon a AKS fürthöz.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Azure tároló beállításjegyzék (ACR) segítségével tároló lemezképeket menteni, ha AKS és ACR közötti hitelesítés konfigurálása. Tekintse meg a [ACR authentication – dokumentáció] [ acr-aks] az alábbi lépéseket.

## <a name="build-the-spark-source"></a>A Spark-adatforrás létrehozása

Spark feladatok fürtön fut egy AKS, mielőtt kell hozza létre a Spark forráskódját, és azt egy tároló lemezképpel csomag. A külső forrás tartalmaz olyan parancsfájlok, amelyek segítségével a folyamat befejezéséhez.

A fejlesztői rendszerhez a Spark projekt tárház klónozása.

```bash
git clone -b branch-2.3 https://github.com/apache/spark
```

Módosítsa a klónozott tárház a könyvtárba, és mentse a Spark-adatforrás elérési változó.

```bash
cd spark
sparkdir=$(pwd)
```

Ha több JDK verziója telepítve van, `JAVA_HOME` 8-as verzió használatára az aktuális munkamenet.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

A következő parancsot hozhat létre a Spark forráskód Kubernetes támogatásával.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Az alábbi parancsokat a Spark tároló lemezkép létrehozása, és hogy a tároló kép beállításjegyzék. Cserélje le `registry.example.com` nevű, a tároló beállításjegyzék és `v1` a címkével ellátott szeretné használni. Docker Hub használata, ha az értéke a neve. Azure tároló beállításjegyzék (ACR) használata esetén ez az érték az az ACR bejelentkezési kiszolgáló.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

A tároló kép leküldése a tároló kép beállításjegyzék.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>A Spark feladatok előkészítése

Ezt követően készítse elő a Spark feladat. A jar-fájlra a Spark feladat tárolására szolgál, és van szükség a `spark-submit` parancsot. A jar elérhetővé keresztül egy nyilvános URL-címet, vagy egy tároló lemezképben előre csomagolt. Ebben a példában egy minta jar jön létre, a Pi értékét számítja ki. Ez a jar majd feltöltése az Azure storage. Ha egy meglévő jar, nyugodtan helyettesítése

Hozzon létre egy könyvtárat, hol szeretné Spark-feladat a projekt létrehozásához.

```bash
mkdir myprojects
cd myprojects
```

Új Scala-projekt létrehozása sablon alapján.

```bash
sbt new sbt/scala-seed.g8
```

Amikor a rendszer kéri, adja meg a `SparkPi` a projekt neve.

```bash
name [Scala Seed Project]: SparkPi
```

Keresse meg az újonnan létrehozott projekt könyvtárát.

```bash
cd sparkpi
```

A következő parancsokat egy SBT beépülő modul, amely lehetővé teszi, hogy a jar-fájlra a projekt csomagolására hozzáadni.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.6")' >> project/assembly.sbt
```

Futtassa az alábbi parancsokat a mintakódot másolása az újonnan létrehozott projektben, és adja hozzá az összes szükséges függőségek.

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11",/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

A projekt csomagolni egy jar, futtassa a következő parancsot.

```bash
sbt assembly
```

Sikeres csomagolás után az alábbihoz hasonló kimenetet kell látnia.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Másolási feladat tárhelyre

Hozzon létre egy Azure-tárfiók és tároló ahhoz, hogy a jar-fájlra.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Töltse fel az Azure storage-fiók, az alábbi parancsokkal a jar-fájlra.

```bash
CONTAINER_NAME=jars
BLOB_NAME=SparkPi-assembly-0.1.0-SNAPSHOT.jar
FILE_TO_UPLOAD=target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar

echo "Creating the container..."
az storage container create --name $CONTAINER_NAME
az storage container set-permission --name $CONTAINER_NAME --public-access blob

echo "Uploading the file..."
az storage blob upload --container-name $CONTAINER_NAME --file $FILE_TO_UPLOAD --name $BLOB_NAME

jarUrl=$(az storage blob url --container-name $CONTAINER_NAME --name $BLOB_NAME | tr -d '"')
```

Változó `jarUrl` már tartalmazza a nyilvánosan elérhető elérési útját a jar-fájlra.

## <a name="submit-a-spark-job"></a>Spark feladat elküldése

Indítsa el a kube-proxy egy különálló parancssori a következő kóddal.

```bash
kubectl proxy
```

Lépjen vissza a Spark-tárház gyökérkönyvtárában.

```bash
cd $sparkdir
```

Küldje el a feladat használ `spark-submit`.

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

Ez a művelet elindítja a Spark feladatot, amely az adatfolyamokat, feladat állapota a rendszerhéj-munkamenethez. A feladat futása közben megtekintheti a Spark illesztőprogram pod és végrehajtó három munkaállomás-csoporttal használatával a kubectl három munkaállomás-csoporttal parancs beolvasása. Nyisson meg egy második terminál-munkamenetet a parancsok futtatásához.

```console
$ kubectl get pods

NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

A feladat futása közben is elérheti a Spark felhasználói felületén. A második terminál-munkamenetet, használja a `kubectl port-forward` parancs hozzáférést biztosítson a külső felhasználói felület.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Külső felhasználói felületének megnyitásához nyissa meg a címet `127.0.0.1:4040` a böngészőben.

![Külső felhasználói felület](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Feladat eredményeinek és a naplókat

A feladat befejeződését követően illesztőprogram fogyasztanak egy "Befejezve" állapotba kerül. El a következő paranccsal fogyasztanak neve.

```bash
kubectl get pods --show-all
```

Kimenet:

```bash
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Használja a `kubectl logs` naplók lekérése spark illesztőprogram fogyasztanak parancsot. Cserélje le az illesztőprogram pod neve pod nevét.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

Ezek a naplók belül meg az eredményt a Spark feladat, amely a Pi értékét.

```bash
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Csomag jar tároló képpel

A fenti példában a külső jar-fájlt az Azure storage feltöltve. Lehetősége a jar-fájlra csomagolni egyedi Docker-lemezképeket.

Ehhez keresse a `dockerfile` a Spark-lemezképet a mappában lévő `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` könyvtár. Adja hozzá vagyok `ADD` utasítás a Spark feladat `jar` között `WORKDIR` és `ENTRYPOINT` nyilatkozatok.

Frissítse a jar-útvonalát helyét a `SparkPi-assembly-0.1.0-SNAPSHOT.jar` fájl a fejlesztői rendszeren. A saját egyéni jar-fájlra is használható.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Hozza létre, és a kép és a mellékelt parancsfájlok Spark leküldéses.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

A feladat jelző egy távoli jar URL-cím helyett a `local://` séma használható az elérési útját a jar-fájlra a Docker-lemezképben.

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> A Spark [dokumentáció][spark-docs]: "a Kubernetes Feladatütemező jelenleg kísérleti. A későbbi verziókban előfordulhat konfiguráció, a tároló képek és entrypoints viselkedési módosítások".

## <a name="next-steps"></a>További lépések

Tekintse meg a Spark dokumentációjában.

> [!div class="nextstepaction"]
> [A Spark-dokumentáció][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-latest-release]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks
[acr-create]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli
[aks-quickstart]: https://docs.microsoft.com/azure/aks/
[azure-cli]: https://docs.microsoft.com/cli/azure/?view=azure-cli-latest
[storage-account]: https://docs.microsoft.com/azure/storage/common/storage-azure-cli
