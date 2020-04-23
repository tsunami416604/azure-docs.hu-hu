---
title: Az Azure Storage használata Jenkins folyamatos integrációs megoldással
description: Ez az oktatóanyag bemutatja, hogyan használható az Azure Blob Service tárházként a Jenkins folyamatos integrációs megoldással létrehozott összetevők létrehozásához.
keywords: Jenkins, Azure, devops, Storage, vel
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: df1d59c40fd09fb055db9d7622d86ff9c82991b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624682"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Az Azure Storage használata Jenkins folyamatos integrációs megoldással

Ez a cikk bemutatja, hogyan használhatja a blob Storage-t egy Jenkins folyamatos integrációs (CI) megoldás által létrehozott Build-összetevők tárháza, vagy a felépítési folyamat során használni kívánt letölthető fájlok forrásaként. Az egyik forgatókönyv, ahol hasznosnak találta ezt a megoldást, ha egy agilis fejlesztési környezetben (Java vagy más nyelv használatával) végez kódolást, a buildek folyamatos integráción alapulnak, és szüksége van a Build-összetevők tárházára, így például megoszthatja őket más szervezet tagjaival, ügyfeleivel, vagy megtarthatja az archívumot. Egy másik forgatókönyv, ha a felépítési feladatnak más fájlokra is szüksége van, például a Build bemenet részeként letölteni kívánt függőségekre.

Ebben az oktatóanyagban a Microsoft által elérhető Jenkins CI-hez készült Azure Storage beépülő modult fogja használni.

## <a name="jenkins-overview"></a>A Jenkins áttekintése

A Jenkins lehetővé teszi egy szoftveres projekt folyamatos integrálását azáltal, hogy lehetővé teszi a fejlesztők számára, hogy egyszerűen integrálják a kód módosításait, és automatikusan és gyakran hozzanak létre buildeket, ezáltal növelve a fejlesztők termelékenységét. A buildek verziója, és az összetevők feltölthetők különböző adattárakba. Ez a cikk bemutatja, hogyan használható az Azure Blob Storage a Build-összetevők tárháza. Azt is bemutatja, hogyan tölthetők le az Azure Blob Storage függőségei.

A Jenkins szolgáltatással kapcsolatos további információkért tekintse [meg a Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)című témakört.

## <a name="benefits-of-using-the-blob-service"></a>A Blob service használatának előnyei

A Blob service használatának előnyei az agilis fejlesztői Build-összetevők üzemeltetéséhez:

* A Build-összetevők és/vagy a letölthető függőségek magas rendelkezésre állása.
* Teljesítmény, amikor a Jenkins CI-megoldás feltölti a Build-összetevőket.
* Teljesítmény, amikor az ügyfelek és a partnerek letöltik a Build-összetevőket.
* A felhasználói hozzáférési házirendek vezérlése a névtelen hozzáférés, a lejáraton alapuló közös hozzáférésű aláírás-hozzáférés, a privát hozzáférés stb. között.

## <a name="prerequisites"></a>Előfeltételek

* A Jenkins folyamatos integrációs megoldása.
  
    Ha jelenleg nem rendelkezik Jenkins CI-megoldással, a következő módszer használatával futtathat egy Jenkins CI-megoldást:
  
  1. Egy Java-kompatibilis gépen töltse le a Jenkins. War- <https://jenkins-ci.org>t.
  2. A Jenkins. War nevű mappában megnyitott parancssorban futtassa a következőt:
     
      `java -jar jenkins.war`

  3. A böngészőben `http://localhost:8080/` nyissa meg a Jenkins-irányítópultot, amelyet az Azure Storage beépülő modul telepítéséhez és konfigurálásához fog használni.
     
      Habár egy tipikus Jenkins CI-megoldás úgy lesz beállítva, hogy szolgáltatásként fusson, a Jenkins-háború parancssorban való futtatása elegendő lesz ehhez az oktatóanyaghoz.
* Egy Azure-fiók. Regisztrálhat egy Azure-fiókot a következő címen: <https://www.azure.com>.
* Egy Azure-tárfiók. Ha még nem rendelkezik Storage-fiókkal, létrehozhat egyet a [Storage-fiók létrehozása](../storage/common/storage-account-create.md)című témakör lépéseit követve.
* A Jenkins CI-megoldás ismerete ajánlott, de nem kötelező, mivel az alábbi tartalom egy alapszintű példát mutat be a Blob service a Jenkins CI-Build összetevőinek tárházként való használatakor szükséges lépések megjelenítéséhez.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>A Blob service használata a Jenkins CI-vel
Ahhoz, hogy a Blob service a Jenkins használatával használhassa, telepítenie kell az Azure Storage beépülő modult, konfigurálnia kell a beépülő modult, hogy használhassa a Storage-fiókját, majd létre kell hoznia egy létrehozás utáni műveletet, amely feltölti a Build-összetevőket a Storage-fiókjába. Ezeket a lépéseket a következő szakaszokban ismertetjük.

## <a name="how-to-install-the-azure-storage-plugin"></a>Az Azure Storage beépülő modul telepítése
1. A Jenkins irányítópultján válassza a **Jenkins kezelése**lehetőséget.
2. A **Jenkins kezelése** lapon válassza a **kezelés beépülő**modult.
3. Válassza az **Available** (Elérhető) lapot.
4. Az összetevő- **feltöltők** szakaszban keresse meg **Microsoft Azure Storage beépülő modult**.
5. Válassza a **telepítés újraindítás nélkül** vagy **a letöltés lehetőséget, és telepítse újra a telepítést**.
6. A Jenkins újraindítása.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Az Azure Storage beépülő modul konfigurálása a Storage-fiók használatára
1. A Jenkins irányítópultján válassza a **Jenkins kezelése**lehetőséget.
2. A **Jenkins kezelése** lapon válassza a **számítógép konfigurálása**lehetőséget.
3. A **Microsoft Azure Storage fiók konfigurációja** szakaszban:
   1. Adja meg a Storage-fiók nevét, amelyet a [Azure Portal](https://portal.azure.com)szerezhet be.
   2. Adja meg a Storage-fiók kulcsát, amely a [Azure Portal](https://portal.azure.com)is beszerezhető.
   3. Ha a globális Azure-felhőt használja, használja az alapértelmezett értéket a **blob Service-végpont URL-címéhez** . Ha más Azure-felhőt használ, használja a [Azure Portalban](https://portal.azure.com) megadott végpontot a Storage-fiókjához. 
   4. A Storage-fiók érvényesítéséhez válassza a **tárolási hitelesítő adatok ellenőrzése** lehetőséget. 
   5. Választható Ha további, a Jenkins CI számára elérhetővé tenni kívánt Storage-fiókkal rendelkezik, válassza a **további Storage-fiókok hozzáadása**lehetőséget.
   6. A beállítások mentéséhez kattintson a **Mentés** gombra.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Build-összetevők feltöltése a Storage-fiókba feltöltés utáni művelet létrehozása
Útmutatásként először létre kell hoznia egy feladatot, amely több fájlt hoz létre, majd a létrehozás utáni művelettel felveszi a fájlokat a Storage-fiókjába.

1. A Jenkins irányítópultján válassza az **új elem elemet**.
2. Nevezze el a feladatot **MyJob**, válassza **a szabad stílusú szoftveres projekt létrehozása**lehetőséget, majd kattintson **az OK gombra**.
3. **A feladatütemezés összeállítás szakaszában válassza** a **létrehozási lépés hozzáadása** elemet, és válassza a **Windows batch-parancs végrehajtása parancsot**.
4. A **parancsban**használja a következő parancsokat:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. A feladatok konfigurációjának **létrehozás utáni műveletek** szakaszában válassza a **Hozzáadás utáni művelet hozzáadása** lehetőséget, és válassza az összetevők **feltöltése az Azure Blob Storage**-ba lehetőséget.
6. A **Storage-fiók neve**mezőben válassza ki a használni kívánt Storage-fiókot.
7. A **tároló neve**mezőben adja meg a tároló nevét. (A tároló akkor jön létre, ha még nem létezik a Build-összetevők feltöltésekor.) Használhat környezeti változókat, így ebben a példában a tároló `${JOB_NAME}` neveként adja meg a nevet.
   
    **Tipp**
   
    A **parancs** alatt, ahol a **Windows batch futtatása parancshoz** megadott egy parancsfájlt, a Jenkins által felismert környezeti változókra mutató hivatkozás. Válassza ki a hivatkozást a környezeti változók nevének és leírásának megismeréséhez. A speciális karaktereket (például a **BUILD_URL** környezeti változót) tartalmazó környezeti változók nem engedélyezettek tároló neveként vagy közös virtuális elérési úton.
8. Ehhez a példához válassza az **új tároló nyilvánosvé tétele alapértelmezésben** lehetőséget. (Ha privát tárolót szeretne használni, létre kell hoznia egy közös hozzáférési aláírást a hozzáférés engedélyezéséhez, amely meghaladja a jelen cikk hatókörét. A közös hozzáférési aláírásokról a [közös hozzáférési aláírások (SAS) használatával](../storage/common/storage-sas-overview.md)tájékozódhat.)
9. Választható Ha azt szeretné, hogy a tárolót az összetevők feltöltése előtt törölje, válassza a **tisztítás** a tárolóban lehetőséget, ha azt szeretné, hogy a tároló tartalma ne legyen feltöltve (ne jelölje be a jelölőnégyzetet, ha nem szeretné törölni a tároló tartalmát).
10. A **feltölteni kívánt összetevők listájához**írja be `text/*.txt`a következőt:.
11. A **feltöltött összetevők közös virtuális elérési útját**a jelen oktatóanyag esetében írja be `${BUILD\_ID}/${BUILD\_NUMBER}`.
12. A beállítások mentéséhez kattintson a **Mentés** gombra.
13. A Jenkins irányítópultján válassza a **Létrehozás most** lehetőséget a **MyJob**futtatásához. Ellenőrizze a konzol kimenetét az állapothoz. Az Azure Storage-hoz tartozó állapotüzenetek a konzol kimenetében jelennek meg, amikor a Build utáni művelet elindítja a Build-összetevők feltöltését.
14. A feladatok sikeres befejezését követően a nyilvános blob megnyitásával ellenőrizheti a Build-összetevőket.
    1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
    2. Válassza a **tárterület**lehetőséget.
    3. Válassza ki a Jenkins-hez használt Storage-fiók nevét.
    4. Válassza a **tárolók**lehetőséget.
    5. Válassza ki a **myjob**nevű tárolót, amely a Jenkins-feladatok létrehozásakor hozzárendelt feladatnév kisbetűs verziója. A tárolók neve és a blob neve kisbetűs (és kis-és nagybetűket megkülönböztető) az Azure Storage-ban. A **myjob**nevű tároló Blobok listáján belül a **Hello. txt** és a **Date. txt fájlt**kell látnia. Másolja az egyik elem URL-címét, és nyissa meg a böngészőben. Ekkor megjelenik a Build-összetevőként feltöltött szövegfájl.

Feladatokból csak egy, az Azure Blob Storage-ba feltöltés utáni művelet hozható létre. Az összetevők Azure Blob Storage-ba való feltöltésének egyetlen utólagos felépítési művelete különböző fájlokat (beleértve a helyettesítő karaktereket) és a fájlok elérési útját is megadhatja egy pontosvesszővel elválasztó karakterrel **feltöltött összetevők listáján** belül. Ha például a Jenkins-Build JAR-fájlokat és TXT-fájlokat hoz létre a munkaterület **Build** mappájában, és az Azure Blob Storage-ba szeretne feltölteni, akkor a következő értéket használja a **feltöltendő összetevők listájához** : `build/\*.jar;build/\*.txt`. A dupla kettőspontos szintaxist is használhatja a blob nevében használandó elérési út megadásához. Ha például azt szeretné, hogy az adattégelyek a blob elérési útjában **bináris** fájlok használatával legyenek feltöltve, és a txt-fájlok a blob elérési útjában található **megjegyzésekkel** töltődnek fel, a következő értéket kell használni `build/\*.jar::binaries;build/\*.txt::notices`a **feltöltendő összetevők listájához** :.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Az Azure Blob Storage-ból letöltött Build lépés létrehozása
A következő lépések bemutatják, hogyan állíthat be egy Build lépést az elemek Azure Blob Storage-ból való letöltéséhez, ami akkor hasznos, ha elemeket kíván felvenni a buildbe. Ennek a mintának a használatára példa az olyan tégelyek, amelyeket érdemes lehet megőrizni az Azure Blob Storage-ban.

1. **A feladatütemezés összeállítás szakaszában válassza** a **létrehozási lépés hozzáadása** elemet, és válassza a **Letöltés az Azure Blob Storage-ból**lehetőséget.
2. A **Storage-fiók neve**mezőben válassza ki a használni kívánt Storage-fiókot.
3. A **tároló neve**mezőben adja meg annak a tárolónak a nevét, amely a letölteni kívánt blobokat tartalmazni kívánja. Környezeti változókat is használhat.
4. A **blob neve**mezőben adja meg a blob nevét. Környezeti változókat is használhat. Azt is megteheti, hogy egy csillagot is használhat helyettesítő karakterként, miután megadta a blob nevének kezdeti betűjelét (ke) t. A **\\Project*** például minden olyan blobot megad, amelynek a neve a **projekttel**kezdődik.
5. Választható A **letöltési útvonal**mezőben határozza meg a Jenkins-gép azon elérési útját, amelyen le szeretné tölteni a fájlokat az Azure Blob Storage-ból. A környezeti változók is használhatók. (Ha nem ad meg értéket a **letöltési útvonalhoz**, a rendszer letölti az Azure Blob Storage-ból származó fájlokat a feladatok munkaterületére.)

Ha további elemeket szeretne letölteni az Azure Blob Storage-ból, további létrehozási lépéseket is létrehozhat.

A Build futtatása után megtekintheti a létrehozási előzmények konzoljának kimenetét, vagy megtekintheti a letöltési helyet, és megtekintheti, hogy a várt Blobok letöltése sikeres volt-e.  

## <a name="components-used-by-the-blob-service"></a>Az Blob service által használt összetevők
Ez a szakasz áttekintést nyújt a Blob service összetevőkről.

* **Storage-fiók**: az Azure Storage-hoz való összes hozzáférés egy Storage-fiókon keresztül történik. A Storage-fiók a névtér legmagasabb szintje a Blobok eléréséhez. Egy fiók korlátlan számú tárolót tartalmazhat, feltéve, hogy a teljes méret 100 TB.
* **Tároló**: a tároló Blobok egy csoportját biztosítja. Az összes blobnak tárolóban kell lennie. Egy fiók korlátlan számú tárolót tartalmazhat. Egy tároló korlátlan számú blob tárolására használható.
* **Blob**: bármilyen típusú és méretű fájl. Az Azure Storage-ban két típusú blob tárolható: a blokk-és a Blobok. A legtöbb fájl blokkolja a blobokat. Egy blokkos blob mérete legfeljebb 200 GB lehet. Ez az oktatóanyag blokk-blobokat használ. Az oldal Blobok, egy másik blob típusa akár 1 TB méretű is lehet, és hatékonyabb, ha a fájlban lévő bájtok tartományait gyakran módosítják. A Blobokkal kapcsolatos további információkért lásd: [a Blobok, a blobok és a Blobok hozzáfűzésének ismertetése](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **URL-formátum**: a Blobok a következő URL-formátummal érhetők el:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (A fenti formátum a globális Azure-felhőre vonatkozik. Ha más Azure-felhőt használ, az URL-végpont meghatározásához használja a [Azure Portal](https://portal.azure.com) belüli végpontot.)
  
    A fenti `storageaccount` formátumban a Storage-fiók `container_name` nevét jelöli, a pedig a tároló nevét, valamint `blob_name` a blob nevét jelöli. A tároló neve alatt több elérési út is lehet, a továbbítási perjelekkel elválasztva **/**. Az oktatóanyaghoz használt példa **MyJob**volt, és a (z) **{Build\_ID}/$ {Build\_Number}** lett használva a közös virtuális elérési úthoz, ami a blob URL-címét a következő formában adja meg:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>A Jenkins beépülő modul hibáinak elhárítása

Ha a Jenkins beépülő modulok használata során bármilyen hibát tapasztal, jelentse be a problémát az adott összetevő [Jenkins JIRA](https://issues.jenkins-ci.org/) felületén.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Jenkins az Azure-on](/azure/Jenkins/)