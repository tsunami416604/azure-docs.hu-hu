---
title: Apache Spark-feladat futtatása az Azure Kubernetes szolgáltatással (AKS)
description: Az Azure Kubernetes-szolgáltatás (AKS) használatával apache spark-i feladatot hozhat létre és futtathat a nagyméretű adatfeldolgozáshoz.
author: lenadroid
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: 8ebd8990a2fdd43b243f5dd6feb632d782fdeb0b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632691"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Apache Spark-feladatok futtatása Az AKS-en

[Az Apache Spark][apache-spark] gyors motor a nagyléptékű adatfeldolgozáshoz. A [Spark 2.3.0 kiadása][spark-latest-release]szerint az Apache Spark támogatja a Kubernetes-fürtökkel való natív integrációt. Az Azure Kubernetes-szolgáltatás (AKS) egy felügyelt Kubernetes-környezet, amely az Azure-ban fut. Ez a dokumentum részletezi az Apache Spark-feladatok előkészítését és futtatását egy Azure Kubernetes-szolgáltatás (AKS) fürtön.

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő lépések végrehajtásához a következőkre van szükség.

* A Kubernetes és az [Apache Spark][spark-quickstart]alapvető megértése.
* [Docker Hub-fiók][docker-hub] vagy egy [Azure Container Registry.][acr-create]
* Az Azure CLI [telepítve van][azure-cli] a fejlesztői rendszeren.
* [JDK 8][java-install] telepítve van a rendszerre.
* SBT ([Scala Build Tool][sbt-install]) telepítve van a rendszerre.
* Git parancssori eszközök telepítve a rendszerre.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

A Spark nagyméretű adatfeldolgozáshoz használatos, és megköveteli, hogy a Kubernetes-csomópontok méretezése megfeleljen a Spark-erőforrások követelményeinek. Azt javasoljuk, hogy `Standard_D3_v2` az Azure Kubernetes-szolgáltatás (AKS) csomópontjainak minimális mérete.

Ha olyan AKS-fürtre van szüksége, amely megfelel ennek a minimális javaslatnak, futtassa a következő parancsokat.

Hozzon létre egy erőforráscsoportot a fürthöz.

```azurecli
az group create --name mySparkCluster --location eastus
```

Hozzon létre egy egyszerű szolgáltatás a fürthöz. Létrehozása után a szolgáltatás egyszerű alkalmazásazonosítóés jelszó a következő parancs.

```azurecli
az ad sp create-for-rbac --name SparkSP
```

Hozza létre az AKS-fürtet méretű `Standard_D3_v2`csomópontokkal, valamint az alkalmazásazonosító és a jelszó egyszerű és ügyféltitkos paraméterekként átadott értékeivel.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2 --generate-ssh-keys --service-principal <APPID> --client-secret <PASSWORD>
```

Csatlakozzon az AKS-fürthöz.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Ha az Azure Container Registry (ACR) tárolórendszerképek tárolására, konfigurálja a hitelesítést az AKS és az ACR között. Ezekről a lépésekről az [ACR-hitelesítési dokumentációban][acr-aks] olvashat.

## <a name="build-the-spark-source"></a>A Spark-forrás létrehozása

Spark-feladatok futtatása előtt egy AKS-fürtön, létre kell készítenie a Spark forráskódját, és csomagolja be egy tárolórendszerképbe. A Spark-forrás parancsfájlokat tartalmaz, amelyek a folyamat végrehajtásához használható.

Klónozza a Spark projekttárházat a fejlesztési rendszerbe.

```bash
git clone -b branch-2.4 https://github.com/apache/spark
```

Módosítsa a klónozott tárház könyvtárába, és mentse a Spark-forrás elérési útját egy változóba.

```bash
cd spark
sparkdir=$(pwd)
```

Ha több JDK-verzió van `JAVA_HOME` telepítve, állítsa be a 8-as verziót az aktuális munkamenethez.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Futtassa a következő parancsot a Spark forráskód kubernetes támogatással való létrehozásához.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

A következő parancsok létre hozzák a Spark-tároló lemezképét, és lelökik egy tárolórendszerkép-beállításjegyzékbe. Cserélje `registry.example.com` le a tároló beállításjegyzékének nevét és `v1` a használni kívánt címkét. Docker Hub használata esetén ez az érték a rendszerleíró adatbázis neve. Ha az Azure Container Registry (ACR) használatával, ez az érték az ACR bejelentkezési kiszolgáló neve.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Nyomja le a tárolólemezképet a tárolórendszerkép-beállításjegyzékbe.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Spark-feladat előkészítése

Ezután készítsen elő egy Spark-feladatot. A jar fájl tárolására használt Spark feladat, `spark-submit` és szükség van a parancs futtatásakor. Az edény nyilvános URL-en keresztül vagy egy tárolórendszerképben előre csomagolt elérhetővé tehető. Ebben a példában egy mintaedény jön létre a Pi értékének kiszámításához. Ezt a jart ezután feltölti az Azure storage.This jar is then uploaded to Azure storage. Ha van egy meglévő jar, nyugodtan helyettesítheti

Hozzon létre egy könyvtárat, ahol létre szeretné hozni a projektet egy Spark-feladathoz.

```bash
mkdir myprojects
cd myprojects
```

Hozzon létre egy új Scala-projektet egy sablonból.

```bash
sbt new sbt/scala-seed.g8
```

Amikor a rendszer `SparkPi` kéri, írja be a projekt nevét.

```bash
name [Scala Seed Project]: SparkPi
```

Nyissa meg az újonnan létrehozott projektkönyvtárat.

```bash
cd sparkpi
```

Futtassa a következő parancsokat egy SBT plugin hozzáadásához, amely lehetővé teszi a projekt jar fájlként való csomagolását.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.10")' >> project/assembly.sbt
```

Ezeket a parancsokat úgy futtathatja, hogy a mintakódot az újonnan létrehozott projektbe másolja, és adja hozzá az összes szükséges függőséget.

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11"/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

Ha a projektet üvegbe szeretné csomagolni, futtassa a következő parancsot.

```bash
sbt assembly
```

A sikeres csomagolás után a következőhöz hasonló kimenetet kell látnia.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Feladat másolása a tárolóba

Hozzon létre egy Azure-tárfiókot és tárolót a jar fájl tárolásához.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Töltse fel a jar fájlt az Azure storage-fiókba a következő parancsokkal.

```azurecli
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

A `jarUrl` változó mostantól a jar fájl nyilvánosan elérhető elérési útját tartalmazza.

## <a name="submit-a-spark-job"></a>Spark-feladat beküldése

Indítsa el a kube-proxyt egy külön parancssorban a következő kóddal.

```bash
kubectl proxy
```

Keresse vissza a Spark-tárház gyökerét.

```bash
cd $sparkdir
```

Hozzon létre egy szolgáltatásfiókot, amely rendelkezik a feladat futtatásához szükséges engedélyekkel.

```bash
kubectl create serviceaccount spark
kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
```

Küldje el `spark-submit`a feladatot a használatával.

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

Ez a művelet elindítja a Spark-feladatot, amely streameli a feladat állapotát a rendszerhéj-munkamenetbe. A feladat futása közben láthatja a Spark-illesztőprogram-pod és a végrehajtó podok a kubectl get pods parancsot. A parancsok futtatásához nyisson meg egy második terminálmunkamenetet.

```console
kubectl get pods
```

```output
NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

Afeladat futása közben is elérheti a Spark felhasználói felületét. A második terminál-munkamenetben `kubectl port-forward` használja a parancsot, amely hozzáférést biztosít a Spark felhasználói felületéhez.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

A Spark felhasználói felületének `127.0.0.1:4040` eléréséhez nyissa meg a címet egy böngészőben.

![Spark felhasználói felülete](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Álláseredmények és naplók beszerezni

A feladat befejezése után az illesztőprogram-pod "Befejezett" állapotban lesz. A pod nevének bekésezése a következő paranccsal.

```bash
kubectl get pods --show-all
```

Kimenet:

```output
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Használja `kubectl logs` a parancsot a spark driver pod naplók beszerezhető. Cserélje le a pod nevét az illesztőprogram-pod nevére.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

Ezeken a naplókon belül láthatja a Spark-feladat eredményét, amely a Pi értéke.

```output
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Csomag jar konténer kép

A fenti példában a Spark jar fájl feltöltve lett az Azure storage-ba. Egy másik lehetőség a jar fájl egyéni alapú Docker-lemezképekbe való csomagolása.

Ehhez keresse meg `dockerfile` a `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` címtárban található Spark-lemezképet. `ADD` Add am utasítást `jar` a `WORKDIR` Spark-feladat valahol a logárdák `ENTRYPOINT` között.

Frissítse a jar elérési útját `SparkPi-assembly-0.1.0-SNAPSHOT.jar` a fájl helyére a fejlesztési rendszeren. Saját egyéni jar fájlt is használhat.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

A rendszerképet a mellékelt Spark-parancsfájlokkal hozhatlétre létre és kell lelökni.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

A feladat futtatásakor, ahelyett, hogy egy `local://` távoli jar URL-t, a séma használható a jar fájl elérési útját a Docker-lemezkép.

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> A Spark [dokumentáció:][spark-docs]"A Kubernetes ütemező jelenleg kísérleti. A későbbi verziókban viselkedésbeli változások lehetnek a konfiguráció, a tárolórendszerképek és a belépési pontok körül".

## <a name="next-steps"></a>További lépések

További részletekért tekintse meg a Spark dokumentációját.

> [!div class="nextstepaction"]
> [Spark dokumentáció][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: https://aka.ms/azure-jdks
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-latest-release]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: cluster-container-registry-integration.md
[acr-create]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli
[aks-quickstart]: https://docs.microsoft.com/azure/aks/
[azure-cli]: https://docs.microsoft.com/cli/azure/?view=azure-cli-latest
[storage-account]: https://docs.microsoft.com/azure/storage/common/storage-azure-cli
