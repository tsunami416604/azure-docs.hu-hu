---
title: Az Azure Storage használata folyamatos Jenkins-integrációs megoldással
description: Ez az oktatóanyag bemutatja, hogyan használhatja az Azure blob szolgáltatást a Jenkins folyamatos integrációs megoldás által létrehozott buildösszetevők tárházaként.
keywords: jenkins, azúrkék, devops, tárolás, cicd
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: df1d59c40fd09fb055db9d7622d86ff9c82991b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624682"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Az Azure Storage használata folyamatos Jenkins-integrációs megoldással

Ez a cikk bemutatja, hogyan használhatja a Blob storage-t a Jenkins folyamatos integrációs (CI) megoldás által létrehozott buildösszetevők tárházaként, vagy a buildelőfolyamatokban használható letölthető fájlok forrásaként. Az egyik forgatókönyv, ahol hasznosnak találná ezt a megoldást, ha agilis fejlesztői környezetben (Java vagy más nyelvek használatával) kódol, a buildek folyamatos integráción alapulnak, és szüksége van egy tárházra a buildösszetevőkhöz, hogy megoszthatja őket például a szervezet más tagjaival, az ügyfeleivel, vagy archiválhat. Egy másik forgatókönyv, amikor maga a buildfeladat más fájlokat igényel, például a build bemenet részeként letöltő függőségeket.

Ebben az oktatóanyagban a Microsoft által elérhetővé tett Azure Storage Plugin for Jenkins CI-t fogja használni.

## <a name="jenkins-overview"></a>Jenkins – áttekintés

A Jenkins lehetővé teszi egy szoftverprojekt folyamatos integrációját azáltal, hogy lehetővé teszi a fejlesztők számára, hogy könnyen integrálják kódmódosításaikat, és automatikusan és gyakran készített buildekkel rendelkeznek, ezáltal növelve a fejlesztők termelékenységét. A buildek verziószámozottak, és a buildösszetevők különböző tárolókba tölthetők fel. Ez a cikk bemutatja, hogyan használhatja az Azure blob storage a build összetevők tárházaként. Azt is bemutatja, hogyan tölthet le függőségeket az Azure blob storage-ból.

További információ a Jenkins megtalálható meet [jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>A Blob szolgáltatás használatának előnyei

A Blob szolgáltatás agilis fejlesztési buildösszetevők üzemeltetésének előnyei a következők:

* A buildösszetevők és/vagy a letölthető függőségek magas rendelkezésre állása.
* Teljesítmény, ha a Jenkins CI-megoldás feltölti a buildösszetevőket.
* Teljesítmény, ha az ügyfelek és a partnerek letöltik a buildösszetevőket.
* A felhasználói hozzáférési házirendek vezérlése a névtelen hozzáférés, az elévülési alapú megosztott hozzáférés, a privát hozzáférés stb.

## <a name="prerequisites"></a>Előfeltételek

* A Jenkins folyamatos integrációs megoldás.
  
    Ha jelenleg nem rendelkezik Jenkins CI-megoldással, futtathat egy Jenkins CI-megoldást a következő technikával:
  
  1. Java-kompatibilis gépen töltse le a <https://jenkins-ci.org>jenkins.war fájlból a alkalmazásból.
  2. A jenkins.war mappát tartalmazó mappához megnyitott parancssorból futtassa a következőparancsot:
     
      `java -jar jenkins.war`

  3. A böngészőben `http://localhost:8080/` nyissa meg a Jenkins-irányítópultot, amellyel telepítheti és konfigurálhatja az Azure Storage beépülő modult.
     
      Míg egy tipikus Jenkins CI-megoldás lenne beállítva, hogy szolgáltatásként fusson, a Jenkins-háború parancssorban futtatásához elegendő lesz ehhez az oktatóanyaghoz.
* Egy Azure-fiók. Az Azure-fiókot a <https://www.azure.com>segítségével regisztrálhatja.
* Egy Azure-tárfiók. Ha még nem rendelkezik tárfiókkal, létrehozhat egyet a [Tárfiók létrehozása](../storage/common/storage-account-create.md)című lépéslépéseivel.
* A Jenkins CI-megoldás ismerete ajánlott, de nem szükséges, mivel a következő tartalom egy alapvető példát használ, hogy megmutassa a szükséges lépéseket, ha a Blob szolgáltatás a Jenkins CI build összetevők tárházaként való használata.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>A Blob szolgáltatás használata a Jenkins CI-vel
A Blob szolgáltatás Jenkins használatával telepítenie kell az Azure Storage beépülő modult, konfigurálnia kell a bővítményt a tárfiók használatára, majd létre kell hoznia egy buildelés utáni műveletet, amely feltölti a buildösszetevőket a tárfiókba. Ezeket a lépéseket a következő szakaszok ismertetik.

## <a name="how-to-install-the-azure-storage-plugin"></a>Az Azure Storage beépülő modul telepítése
1. A Jenkins irányítópulton válassza a **Manage Jenkins (Manage Jenkins) lehetőséget.**
2. A **Manage Jenkins (Jenkins kezelése)** lapon válassza a **Bővítmények kezelése**lehetőséget.
3. Válassza az **Available** (Elérhető) lapot.
4. A **Műtermék-feltöltők** csoportban ellenőrizze a **Microsoft Azure Storage beépülő modult.**
5. Válassza a **Telepítés újraindítás nélkül** vagy a Letöltés most **lehetőséget, és telepítse az újraindítás után.**
6. Indítsa újra a Jenkinst.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Az Azure Storage beépülő modul beállítása a tárfiók használatára
1. A Jenkins irányítópulton válassza a **Manage Jenkins (Manage Jenkins) lehetőséget.**
2. A **Manage Jenkins (Jenkins kezelése)** lapon válassza a **Rendszer konfigurálása**lehetőséget.
3. A **Microsoft Azure storage-fiók konfigurációja** szakaszban:
   1. Adja meg a tárfiók nevét, amelyet az [Azure Portalon](https://portal.azure.com)szerezhet be.
   2. Adja meg a tárfiók kulcsát, amely az [Azure Portalon](https://portal.azure.com)is beszerezhető.
   3. Használja a **Blob Service Endpoint URL-cím** alapértelmezett értékét, ha a globális Azure-felhőt használja. Ha egy másik Azure-felhőt használ, használja a végpontot az [Azure Portalon](https://portal.azure.com) a tárfiókhoz megadott módon. 
   4. Válassza a tárfiók érvényesítéséhez válassza a tárfiók érvényesítése tárolási **hitelesítő adatok érvényesítéséhez** lehetőséget. 
   5. [Nem kötelező] Ha további tárfiókokkal rendelkezik, amelyeket elérhetővé szeretne tenni a Jenkins CI számára, válassza **a További tárfiókok hozzáadása**lehetőséget.
   6. A beállítások mentéséhez válassza a **Mentés** gombot.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Build utáni művelet létrehozása, amely feltölti a buildösszetevőket a tárfiókba
Oktatási célokra először létre kell hoznia egy feladatot, amely több fájlt hoz létre, majd hozzá kell adnia a build elkönyvelési művelethez a fájlok feltöltéséhez a tárfiókba.

1. A Jenkins irányítópulton válassza az **Új elem lehetőséget.**
2. Nevezze el a **feladatot A MyJob**, válassza **a Szabad stílusú szoftverprojekt létrehozása**lehetőséget, majd kattintson az OK **gombra.**
3. A feladatkonfiguráció **Build** szakaszában válassza a **Build hozzáadása lépés lehetőséget,** és válassza a Windows **kötegelt parancs végrehajtása parancsot.**
4. A **Command parancsban**használja a következő parancsokat:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. A feladatkonfiguráció **Létrehozás utáni műveletek** szakaszában válassza a Build utáni művelet hozzáadása lehetőséget, és válassza **az Összetevők feltöltése az Azure Blob** **storage-ba** lehetőséget.
6. A **Storage-fiók neve,** válassza ki a tárfiókot használni.
7. A **Tároló neve**mezőbe adja meg a tároló nevét. (A tároló akkor jön létre, ha még nem létezik a buildösszetevők feltöltésekénél.) Használhat környezeti változókat, ezért ebben `${JOB_NAME}` a példában adja meg a tároló nevét.
   
    **Tipp**
   
    A **Parancs** szakasz alatt, ahol beírt egy parancsfájlt a **Windows végrehajtása kötegelt parancshoz,** a Jenkins által felismert környezeti változókra mutató hivatkozás található. Jelölje ki ezt a hivatkozást a környezeti változók nevének és leírásának megismeréséhez. A speciális karaktereket tartalmazó környezeti változók, például a **BUILD_URL** környezeti változó, nem engedélyezettek tárolónévként vagy közös virtuális elérési útként.
8. Válassza az **Új tároló nyilvánossá tévővé beállítást** alapértelmezés szerint ebben a példában. (Ha privát tárolót szeretne használni, létre kell hoznia egy közös hozzáférésű aláírást a hozzáférés engedélyezéséhez, amely túlmutat a jelen cikk hatókörén. A megosztott hozzáférésű aláírásokról a [Megosztott hozzáférésű aláírások használata (SAS)](../storage/common/storage-sas-overview.md)című helyen olvashat bővebben.)
9. [Nem kötelező] Válassza a **Tároló tisztítása a feltöltés előtt** lehetőséget, ha azt szeretné, hogy a tároló takarodjon a tartalomból a buildösszetevők feltöltése előtt (hagyja bejelölve, ha nem szeretné megtisztítani a tároló tartalmát).
10. A **feltöltendő összetevők listájához**írja be a be a mezőbe `text/*.txt`a.
11. A **feltöltött összetevők gyakori virtuális elérési útja**esetén az `${BUILD\_ID}/${BUILD\_NUMBER}`oktatóanyag alkalmazásában írja be a következőt: .
12. A beállítások mentéséhez válassza a **Mentés** gombot.
13. A Jenkins irányítópulton válassza a **Build Now lehetőséget** a **MyJob**futtatásához. Vizsgálja meg a konzol kimenetét az állapot. Az Azure storage állapotüzenetei szerepelni fognak a konzol kimenetében, amikor a létrehozás utáni művelet elkezdi feltölteni a buildelési összetevőket.
14. A feladat sikeres befejezése után megvizsgálhatja a buildösszetevők et a nyilvános blob megnyitásával.
    1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
    2. Válassza a **Tárolás**lehetőséget.
    3. Válassza ki a storage-fiók nevét, amelyet a Jenkins használt.
    4. Válassza a **Tárolók**lehetőséget.
    5. Válassza ki a **myjob**nevű tárolót, amely a Jenkins-feladat létrehozásakor hozzárendelt feladatnév kis-és nagybetűs verziója. A tárolónevek és a blobnevek kisbetűsek (és a kis- és nagybetűk et is kivannak) az Azure storage-ban. A **myjob**nevű tároló blobjainak listáján megjelenik a **hello.txt** és a **date.txt**. Másolja az elemek valamelyikének URL-címét, és nyissa meg a böngészőben. Látni fogja a buildösszetevőként feltöltött szövegfájlt.

Feladatonként csak egy, az Azure blobstorage-ba összetevőket feltöltő, létrehozás utáni művelet hozható létre. Az azure blobstorage-ba összetevők feltöltéséhez szükséges egyetlen létrehozás utáni művelet különböző fájlokat (beleértve a helyettesítő karaktereket) és az összetevők listájában feltöltendő fájlok elérési útjait a pontosvessző elválasztóként történő **feltöltésével adhatja meg.** Ha például a Jenkins-build JAR-fájlokat és TXT-fájlokat hoz létre a munkaterület buildmappájában, és mindkettőt fel szeretné tölteni `build/\*.jar;build/\*.txt`az Azure blobstorage-ba, használja a következő értéket a **feltöltendő összetevők listája** beállításhoz: . **build** A kettőspont szintaxisa segítségével megadhatja a blob névben használandó elérési utat is. Ha például azt szeretné, hogy a jars-t bináris **fájlok** használatával töltse fel a blob elérési útján, és a TXT-fájlokat a blob elérési útján lévő **hirdetmények** használatával töltse fel, használja a következő értéket a **feltöltendő összetevők listája** beállításhoz: `build/\*.jar::binaries;build/\*.txt::notices`.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Az Azure blob storage-ból letöltő buildlépés létrehozása
A következő lépések azt mutatják, hogy konfigurálja a build lépés az elemek letöltéséhez az Azure blob storage, amely akkor hasznos, ha azt szeretné, hogy az elemek a build. A minta használatával például a JARs, amely érdemes megmaradni az Azure blob storage.

1. A **feladatkonfiguráció Build** szakaszában válassza a **Build hozzáadása lépés lehetőséget,** és válassza **a Letöltés az Azure Blob storage-ból**lehetőséget.
2. A **Storage-fiók neve,** válassza ki a tárfiókot használni.
3. A **Tároló neve**mezőbe írja be a letölteni kívánt blobokat tartalmazó tároló nevét. Használhatja a környezeti változókat.
4. A **Blob neve**esetén adja meg a blob nevét. Használhatja a környezeti változókat. Emellett használhat egy csillagot helyettesítő karakterként, miután megadta a blob név kezdő betűjét.Also, you can use an csillag, as a helyettesítő karakter after you specify the initial letter(s) of the blob name. A ***\\projekt**például minden olyan blobot megad, amelynek a neve **a projekttel**kezdődik.
5. [Nem kötelező] A **letöltési útvonal,** adja meg az elérési utat a Jenkins-gépen, ahol fájlokat szeretne letölteni az Azure blob storage.For Download path, specify the path on the Jenkins machine where you want to download files from Azure blob storage. Környezeti változók is használhatók. (Ha nem ad meg értéket a **letöltési útvonalhoz,** az Azure blob storage-ból származó fájlok a feladat munkaterületére töltődnek le.)

Ha további elemeket szeretne letölteni az Azure blob storage-ból, további buildlépéseket hozhat létre.

A build futtatása után ellenőrizheti a buildelőzmények konzolkimenetét, vagy megtekintheti a letöltési helyet, és ellenőrizheti, hogy a várt blobok letöltése sikeresen megtörtént-e.  

## <a name="components-used-by-the-blob-service"></a>A Blob szolgáltatás által használt összetevők
Ez a szakasz áttekintést nyújt a Blob szolgáltatás összetevőiről.

* **Tárfiók:** Az Azure Storage-hoz való minden hozzáférés egy tárfiókon keresztül történik. A tárfiók a névtér legmagasabb szintje a blobok eléréséhez. Egy fiók korlátlan számú tárolót tartalmazhat, amennyiben azok teljes mérete 100 TB alatt van.
* **Tároló:** A tároló blobok készletének csoportosítását biztosítja. Az összes blobnak tárolóban kell lennie. Egy fiók korlátlan számú tárolót tartalmazhat. Egy tároló korlátlan számú blob tárolására használható.
* **Blob**: Bármilyen típusú és méretű fájl. Kétféle blobok, amelyek tárolhatók az Azure Storage-ban: blokk és lap blobok. A legtöbb fájl blokkblob. Egy blokkblob mérete legfeljebb 200 GB lehet. Ez az oktatóanyag blokkblobokat használ. A lapblobok, egy másik blobtípus mérete legfeljebb 1 TB lehet, és hatékonyabbak, ha egy fájlban lévő bájtok tartományait gyakran módosítják. A blobokról a [Blokkblobok, a Hozzáfűző blobok és a Lapblobok ismertetése](https://msdn.microsoft.com/library/azure/ee691964.aspx)című témakörben talál további információt.
* **URL-formátum**: A blobok a következő URL-formátumban címezhetők:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (A fenti formátum a globális Azure-felhőre vonatkozik. Ha egy másik Azure-felhőt használ, használja a végpontot az [Azure Portalon](https://portal.azure.com) az URL-végpont meghatározásához.)
  
    A fenti formátumban `storageaccount` a tárfiók nevét, `container_name` a tároló nevét jelöli, `blob_name` és a blob nevét jelöli. A tároló névén belül több elérési út is lehet, **/** amelyeket perjel lelt el egymástól. Az oktatóanyaghoz használt példatároló neve **A MyJob**volt, és **${BUILD\_ID}/${BUILD\_NUMBER}** volt a közös virtuális elérési úthoz, aminek következtében a blob URL-címe a következő:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>A Jenkins beépülő modul hibáinak elhárítása

Ha a Jenkins beépülő modulok használata során bármilyen hibát tapasztal, jelentse be a problémát az adott összetevő [Jenkins JIRA](https://issues.jenkins-ci.org/) felületén.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Jenkins az Azure-on](/azure/Jenkins/)