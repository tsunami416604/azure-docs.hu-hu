---
title: Azure Kubernetes Service (AKS) egy Apache Spark-feladat futtatása
description: Az Apache Spark-feladatok futtatása az Azure Kubernetes Service (AKS) használatával
services: container-service
author: lenadroid
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/15/2018
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: ddaff590fd493b430a72c30dd35cb1b891b80d84
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50414029"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Az Apache Spark-feladatok futtatása AKS-en

[Az Apache Spark] [ apache-spark] egy gyors összetevő, az adatfeldolgozás nagy méretű. A a [Spark 2.3.0-át kiadási][spark-latest-release], Apache Spark támogatja a natív integrációt olyan Kubernetes-fürtök. Az Azure Kubernetes Service (AKS) az Azure-ban futó felügyelt Kubernetes-környezetet. Ez a dokumentum részletesen, előkészítése, és az Apache Spark-feladatok futtatása az Azure Kubernetes Service (AKS)-fürtön.

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben található lépések végrehajtásához a következők szükségesek.

* Ismeri a Kubernetes alapvető és [Apache Spark][spark-quickstart].
* [A docker Hub] [ docker-hub] fiókot, vagy egy [Azure Container Registry][acr-create].
* Az Azure CLI [telepített] [ azure-cli] a fejlesztői rendszeren.
* [JDK 8] [ java-install] telepítve a rendszeren.
* SBT ([Scala hozhat létre eszköz][sbt-install]) telepítve a rendszeren.
* A Git parancssori eszközök van telepítve a rendszeren.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

A Spark nagyméretű adatfeldolgozás használja, és megköveteli, hogy a Kubernetes-csomópontok mérete a Spark-erőforrások követelmények teljesítéséhez. Azt javasoljuk, hogy a minimális méret `Standard_D3_v2` az Azure Kubernetes Service (AKS) csomópontokhoz.

Ha egy AKS-fürtöt, amely megfelel a minimális javaslat, futtassa a következő parancsokat.

Hozzon létre egy erőforráscsoportot, a fürt számára.

```azurecli
az group create --name mySparkCluster --location eastus
```

Az AKS-fürt létrehozása, amelyek méretű csomópont `Standard_D3_v2`.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2
```

Csatlakozás az AKS-fürtöt.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Tárolórendszerképek tárolása az Azure Container Registry (ACR) használ, ha konfigurálja az AKS és ACR közötti hitelesítést. Tekintse meg a [ACR-hitelesítés dokumentáció] [ acr-aks] az alábbi lépéseket.

## <a name="build-the-spark-source"></a>A Spark-adatforrás létrehozása

Mielőtt Spark feladatokat futtat egy AKS-fürtöt, meg kell hozhat létre a Spark forráskódját, valamint Becsomagolhatja azt egy tárolórendszerképbe. A Spark-forrás tartalmaz parancsprogramokat, amelyek a folyamat befejezéséhez használható.

A fejlesztői rendszerhez a Spark-projekt adattár klónozása.

```bash
git clone -b branch-2.3 https://github.com/apache/spark
```

Módosítsa a könyvtárat a klónozott adattár, és mentse a Spark-forrás elérési útját egy változóban.

```bash
cd spark
sparkdir=$(pwd)
```

Ha több telepített JDK-verziója, beállítása `JAVA_HOME` 8-as verzió használatához az aktuális munkamenet.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Futtassa a következő parancsot hozhat létre a Spark forráskód Kubernetes támogatásával.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Az alábbi parancsokat a Spark-tároló rendszerképének létrehozása, és leküldeni a rendszerképet a tárolójegyzékbe. Cserélje le `registry.example.com` a tárolóregisztrációs adatbázis nevére és `v1` címkével szeretné használni. Ha használja a Docker Hub, az értéke a beállításjegyzék neve. Ha az Azure Container Registry (ACR) használja, az értéke az ACR bejelentkezési kiszolgáló nevét.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

A tárolórendszerkép leküldése a rendszerképet a tárolójegyzékbe.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Spark-feladatok előkészítése

A következő lépésben előkészítjük egy Spark-feladatot. Egy jar-fájlt a Spark-feladatok tárolására szolgál, és van szükség a `spark-submit` parancsot. A jar férhetnek hozzá egy nyilvános URL-címmel, vagy előre csomagolt egy tárolórendszerképet belül is. Ebben a példában egy minta jar jön létre a Pi értékét számítja ki. A JAR-fájl majd töltenek fel az Azure storage. Ha rendelkezik egy meglévő jar, nyugodtan helyettesítése

Hozzon létre egy könyvtárat, hol szeretné a Spark-feladatok a projekt létrehozásához.

```bash
mkdir myprojects
cd myprojects
```

Hozzon létre egy új Scala-projektet egy sablonból.

```bash
sbt new sbt/scala-seed.g8
```

Amikor a rendszer kéri, adja meg a `SparkPi` projektnevet.

```bash
name [Scala Seed Project]: SparkPi
```

Keresse meg az újonnan létrehozott projektre könyvtárát.

```bash
cd sparkpi
```

Futtassa a következő parancsokat egy SBT beépülő modul, amely lehetővé teszi a csomagolás egy jar-fájlt a projekt hozzáadása.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.6")' >> project/assembly.sbt
```

Futtassa az alábbi parancsokat a mintául szolgáló kódot bemásolhatja az újonnan létrehozott projektre, és adja hozzá az összes szükséges függőséget.

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

A következő parancsot egy jar csomagolni a projektet.

```bash
sbt assembly
```

Ha sikeres csomagolást, az alábbihoz hasonló kimenet jelenik meg.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>A storage másolási feladat

Hozzon létre egy Azure storage-fiók és a tároló, amely tárolja a jar-fájlt.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Töltse fel a jar-fájlt az Azure storage-fiókot az alábbi parancsokkal.

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

Változó `jarUrl` mostantól tartalmazza a nyilvánosan elérhető-e a jar-fájl elérési útját.

## <a name="submit-a-spark-job"></a>Spark-feladatok elküldése

Indítsa el a kube-proxy egy különálló parancssori az alábbi kódra.

```bash
kubectl proxy
```

Lépjen vissza a Spark-tárház gyökérkönyvtárában.

```bash
cd $sparkdir
```

Küldje el a feladat használatával `spark-submit`.

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

Ez a művelet elindítja a Spark-feladatot, amely a feladat állapotát a rendszerhéj-munkamenethez streameli. A feladat futása közben megtekintheti a Spark-illesztő pod, és a kubectl használatával végrehajtó podok podok parancs beolvasása. Nyisson meg egy második terminál-munkamenetben, futtassa a következő parancsokat.

```console
$ kubectl get pods

NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

A feladat futtatásakor a Spark felhasználói felület is elérhető. A második terminál-munkamenetben használja a `kubectl port-forward` parancs számára hozzáférést biztosítsunk a Spark felhasználói felületén.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Hozzáférhet a Spark felhasználói felületén, nyissa meg a címet `127.0.0.1:4040` egy böngészőben.

![Spark felhasználói felület](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Feladat eredményeinek és naplók

A feladat befejezése után, az illesztőprogram-pod "Kész" állapotban lesz. Neve a következő paranccsal a pod beolvasása.

```bash
kubectl get pods --show-all
```

Kimenet:

```bash
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Használja a `kubectl logs` szerezhet be naplófájlokat a spark-illesztő pod a parancsot. Cserélje le a podnév az illesztőprogram-pod nevére.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

Ezek a naplók belül láthatja a Spark-feladat, amely a Pi értékét eredményét.

```bash
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>A tárolórendszerkép csomag jar

A fenti példában a Spark jar-fájl feltöltése az Azure storage. Egy másik lehetőség, hogy a csomag a jar-fájlt a személyre szabott Docker-rendszerképekben.

Ehhez keresse meg a `dockerfile` a Spark-rendszerkép található `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` könyvtár. Adjon hozzá vagyok `ADD` utasítás a Spark-feladat `jar` között `WORKDIR` és `ENTRYPOINT` nyilatkozatokat.

Frissítse a jar-elérési helyét az `SparkPi-assembly-0.1.0-SNAPSHOT.jar` fájlt a fejlesztői rendszeren. Használhatja saját egyéni jar-fájlt.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Hozza létre, és küldje le a rendszerképet a csomagban foglalt Spark-szkriptet.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

Jelző egy távoli jar URL-cím helyett, a feladat futtatásakor a `local://` séma használható a Docker-rendszerkép a jar-fájl elérési útját.

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
> A Spark [dokumentáció][spark-docs]: "a Kubernetes-ütemező je experimentální. jelenleg. A későbbi verziókban előfordulhat konfigurációs, tárolórendszerképekre és entrypoints viselkedésbeli változásokat".

## <a name="next-steps"></a>További lépések

Tekintse meg a Spark dokumentációjában.

> [!div class="nextstepaction"]
> [Spark-dokumentáció][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: https://aka.ms/azure-jdks
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
