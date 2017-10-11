---
title: "Az Azure Storage használata Jenkins folyamatos integrációt megoldás |} Microsoft Docs"
description: "Ez az oktatóanyag az Azure blob szolgáltatás használatára, mert a tára build Jenkins folyamatos integrációt megoldás által létrehozott összetevők szemléltetik."
services: storage
documentationcenter: java
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: f4e5ca75-f6cb-4f74-981b-2aa06bb8de45
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 02/28/2017
ms.author: seguler
ms.openlocfilehash: 174ac449e803ed5327468a38ea7264cb9923a877
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Az Azure Storage szolgáltatás használata Jenkins folyamatos integrációs megoldással
## <a name="overview"></a>Áttekintés
Az alábbi információk segítségével a Blob storage Jenkins folyamatos integráció (CI) megoldás által létrehozott build műtermék tára, vagy letölthető fájlok forrásaként használható a létrehozási folyamat mutatja be. Egy Ha akkor is hasznos ez a forgatókönyv akkor, ha Ön most kódolási egy gyors fejlesztési környezetben (Java vagy más nyelvek) buildek futtat alapú folyamatos integrációt és van szüksége a tárházat a build összetevők, hogy sikerült, például más szervezet tagjaival, az ügyfelek megosztására vagy archiválhatja karbantartása. Egy másik helyzet lehet, ha az összeállítási feladat maga szükséges egyéb fájlokat, például a függőségeket a bemeneti build részeként tölthető le.

Ebben az oktatóanyagban fog használni az Azure Storage beépülő modul a Microsoft által elérhetővé tett Jenkins konfigurációelemhez.

## <a name="overview-of-jenkins"></a>Jenkins áttekintése
Jenkins lehetővé teszi, hogy a szoftver projekt folyamatos integrációt azáltal, hogy a fejlesztők könnyedén integrálható a kód módosítására, és buildek előállított automatikusan és gyakran, ezáltal termelékenységének a fejlesztők számára. Buildek verzióval ellátva, és build összetevők is feltölthetők a különböző tárházak találhatók. Ez a témakör ismerteti, hogyan Azure blob storage használata a build műtermék tárházaként. Azt is megjelennek az Azure blob storage függőségek letöltése.

További információ a Jenkins található [megfelelnek Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>A Blob szolgáltatás használatának előnyei
A gyors fejlesztés build összetevők futtatására a Blob szolgáltatás használatának előnyei a következők:

* Magas rendelkezésre állás a build összetevők és/vagy letölthető függőségek.
* Ha a Jenkins CI megoldás feltölti a build összetevők teljesítményét.
* Ha az ügyfelek és partnerek töltse le a build összetevők teljesítményét.
* Felhasználói hozzáférési házirendeket, a névtelen hozzáférés, lejárati-alapú megosztott hozzáférési aláírást hozzáférés, saját közötti választás felett ellenőrzést hozzáférés, stb.

## <a name="prerequisites"></a>Előfeltételek
A Jenkins CI-megoldás a Blob szolgáltatás használatához a következőkre lesz szüksége:

* A folyamatos integrációs Jenkins megoldás.
  
    Jelenleg nem rendelkezik olyan Jenkins CI megoldást, ha egy Jenkins CI megoldást a következő eljárás segítségével is futtathatja:
  
  1. A Java-kompatibilis gépen töltse le a jenkins.war <http://jenkins-ci.org>.
  2. Egy jenkins.war tartalmazó mappa megnyitott parancssorban futtassa:
     
      `java -jar jenkins.war`

  3. A böngészőben nyissa meg a `http://localhost:8080/`. Ekkor megnyílik a Jenkins irányítópult, mert ezzel fogja telepítése és konfigurálása az Azure Storage beépülő modul.
     
      Amíg egy tipikus Jenkins CI megoldás értékre van beállítva szolgáltatásként, a Jenkins war fut a parancssorból futtassa a jelen oktatóanyag elegendő lesz.
* Egy Azure-fiók. Azure-fiókot, regisztrálhat <http://www.azure.com>.
* Egy Azure-tárfiók. Ha még nem rendelkezik egy tárfiókot, létrehozhat egyet a következő lépések [hozzon létre egy Tárfiókot](../common/storage-create-storage-account.md#create-a-storage-account).
* A tesztkörnyezet Jenkins CI-megoldással ajánlott, de nem szükséges, a következő tartalmat használja egy egyszerű példa, a lépéseken, amikor a Blob szolgáltatás használata a tárház Jenkins CI build összetevők.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>A Blob szolgáltatás használata Jenkins CI
A Blob szolgáltatás használata Jenkins, lesz szüksége az Azure Storage beépülő modul telepítését, a beépülő modul a tárfiók használatára konfigurálja, és hozzon létre egy utáni műveletet, amely a build összetevők feltölti a tárfiókhoz. A következő szakaszok ismertetik ezeket a lépéseket.

## <a name="how-to-install-the-azure-storage-plugin"></a>Az Azure Storage beépülő modul telepítése
1. Belül Jenkins irányítópulton kattintson **kezelése Jenkins**.
2. Az a **kezelése Jenkins** kattintson **kezelése beépülő modulok**.
3. Kattintson a **elérhető** fülre.
4. Az a **összetevő Uploaders** területen ellenőrizze **Microsoft Azure Storage beépülő modul**.
5. Jelölje be az **újraindítása nélküli** vagy **letöltése és telepítése az újraindítást követően**.
6. Indítsa újra a Jenkins.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>A tárfiók használatára az Azure Storage beépülő modul konfigurálása
1. Belül Jenkins irányítópulton kattintson **kezelése Jenkins**.
2. Az a **kezelése Jenkins** kattintson **rendszer konfigurálása**.
3. Az a **Microsoft Azure Storage-fiók konfigurációja** szakasz:
   1. Adja meg a tárfiók neve, amely szerezhet be a [Azure Portal](https://portal.azure.com).
   2. Adja meg a tárfiók kulcsára, történő is szerezhető a [Azure Portal](https://portal.azure.com).
   3. Használja az alapértelmezett értéket a **Blob-Szolgáltatásvégpont URL-** használata a nyilvános Azure felhőbe. Ha egy másik Azure felhőbe használja, használja a végpontot a a [Azure Portal](https://portal.azure.com) a tárfiók. 
   4. Kattintson a **tárolási fiók hitelesítő adatainak érvényesítéséhez** a tárfiók érvényesítése. 
   5. [Választható] Ha további tárfiókok kívánt érhetik el a Jenkins CI, kattintson a **adja hozzá a további Tárfiókok**.
   6. Kattintson a **mentése** a beállítások mentéséhez.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Egy utáni műveletet, amely a build összetevők feltölti a tárfiók létrehozása
Utasítás célokra először azt kell hozzon létre egy feladatot, amely több-fájlok létrehozása, majd adja hozzá a tárfiókhoz a fájlok feltöltéséhez utáni műveletnél.

1. A Jenkins irányítópulton kattintson az **Új elem** lehetőségre.
2. A feladat neve **MyJob**, kattintson a **szabad stílusú szoftver a projekt létrehozása**, és kattintson a **OK**.
3. A a **Build** szakasz a feladat-konfiguráció, kattintson a **Hozzáadás összeállítása lépés** válassza **hajtható végre Windows kötegelt parancs**.
4. A **parancs**, az alábbi parancsokat használja:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. A a **utáni műveletek** szakaszban kattintson a feladat konfiguráció **utáni műveletének hozzáadása** , és válassza **összetevők feltöltése az Azure Blob storage**.
6. A **tárfióknév**, válassza ki a tárfiókot használni.
7. A **Tárolónév**, adja meg a tároló nevét. (A tároló jön létre, ha még nem létezik a build összetevők feltöltése során.) Környezeti változók használata, így ehhez a példához írja be **${JOB_NAME}** , a tároló neve.
   
    **Tipp**
   
    Alább a **parancs** egy parancsfájlt megadásánál szakasz **hajtható végre Windows kötegelt parancs** a környezeti változók ismeri fel Jenkins mutató hivatkozás. Kattintson a hivatkozásra a környezeti változó neve és leírása. Vegye figyelembe, hogy környezeti változókat, amelyek tartalmazhat különleges karaktereket, például a **BUILD_URL** környezeti változó, a tároló neve vagy a közös virtuális elérési út nem tartalmazhat.
8. Kattintson a **új tároló alapértelmezés szerint nyilvánosságra** ehhez a példához. (Ha egy személyes tárolót használni kívánt, szüksége lesz egy közös hozzáférésű jogosultságkód hozzáférést létrehozásához. Ez az túlmutat a jelen témakör. A megosztott hozzáférési aláírásokkal kapcsolatos részletesebb [használatával megosztott hozzáférési aláírásokkal (SAS)](../storage-dotnet-shared-access-signature-part-1.md).)
9. [Választható] Kattintson a **feltöltés előtt tiszta tároló** Ha azt szeretné, hogy a tároló tartalmának törölni kell, mielőtt build összetevők feltöltése (hagyja bejelölve Ha nem szeretné, hogy a tároló tartalmának törlése).
10. A **összetevők lista feltöltéséhez**, adja meg  **szöveg /*.txt**.
11. A **feltöltött összetevők a közös virtuális elérési út**, írja be a jelen oktatóanyag **${BUILD\_azonosító} / ${BUILD\_szám}**.
12. Kattintson a **mentése** a beállítások mentéséhez.
13. Jenkins irányítópultján kattintson **Build most** futtatásához **MyJob**. Vizsgálja meg a konzol kimeneti az állapotot. Az Azure storage-állapotüzeneteinek szerepelni fog a konzol kimeneti build összetevők feltölteni a létrehozás után végrehajtandó művelet indításakor.
14. A feladat sikeres befejezését követően a build összetevők a nyilvános blob megnyitásával ellenőrizheti.
    1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com).
    2. Kattintson a **tárolási**.
    3. Kattintson a tárfiók nevét, amelyet Jenkins használt.
    4. Kattintson a **tárolók**.
    5. Kattintson a tároló **myjob**, amely a feladat nevét, amely a Jenkins feladat létrehozásakor rendelt kis verziója telepítve. A tároló nevének és a blob nevének, kisbetű (és kis-és nagybetűket) az Azure-tárfiókba. Blobok nevű tároló listáján **myjob** kell megjelennie **hello.txt** és **date.txt**. Az URL-Címének másolása ezek az elemek egyikét, majd nyissa meg a böngészőben. A build összetevő, látni fogja a feltöltött szövegfájl.

Az összetevők feltölti az Azure blob storage csak egy utáni végrehajtandó feladat / hozhatók létre. Vegye figyelembe, hogy a egyetlen utáni műveletet az összetevők feltöltése az Azure blob storage megadhat különböző fájlok (beleértve a helyettesítő karaktereket) és a megadott elérési utakon található fájl **összetevők lista feltöltéséhez** pontosvesszővel kell elválasztani őket egy használatával. Például ha a Jenkins előállító JAR és TXT-fájlok a munkaterületen **build** mappa, és szeretne feltölteni mindkettőt az Azure blob storage, használja a következő a **összetevők lista feltölteni** érték: **build /\*.jar; build /\*.txt**. Dupla kettőspont szintaxis segítségével adjon meg egy elérési utat a blob nevének belül. Például, ha azt szeretné, hogy a JAR-fájlok kivételével az beszerzése feltöltött használatával **bináris** a blob elérési út és a TXT-fájlok használatával beolvasása feltöltött **értesítéseket** a blob elérési út a következőt használja a **összetevők lista feltöltéséhez** érték: **build /\*. jar::binaries; build /\*. txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>A build lépést, amely letölti az Azure blob storage létrehozása
A következő lépések bemutatják egy build lépéssel töltheti le a cikkek az Azure blob storage konfigurálása. Akkor hasznos, ha azt szeretné, elemek szerepeljenek a build, például JAR-fájlok kivételével az Azure-ban tárolt blob-tároló.

1. A a **Build** szakaszban kattintson a feladat-konfiguráció **Hozzáadás összeállítása lépés** , és válassza **töltse le az Azure Blob storage**.
2. A **tárfióknév**, válassza ki a tárfiókot használni.
3. A **Tárolónév**, adja meg, amely rendelkezik a blobok szeretné letölteni a tároló nevét. Környezeti változókat is használhat.
4. A **Blob neve**, adja meg a blob neve. Környezeti változókat is használhat. Is használhatja a csillag karakter, a kezdeti betűjét vagy a blob nevének megadása után helyettesítő karakter. Például **projekt\***  kellene megadnia kezdődő összes BLOB **projekt**.
5. [Választható] A **letöltési mappa elérési útját**, az Azure blob storage fájlok letöltésére ahová Jenkins gépen elérési útjának megadása. Környezeti változók is használható. (Ha nem ad meg értéket **letöltési mappa elérési útját**, a feladatok munkaterületet letölti a fájlokat az Azure blob storage.)

Ha le szeretné tölteni az Azure blob storage további elemek, build további lépéseket is létrehozhat.

Miután lefuttatta a build, ellenőrizheti a build előzmények konzol kimeneti, vagy nézze meg a letöltési hely megtekintéséhez, hogy a várt blobok letöltése sikeresen megtörtént.  

## <a name="components-used-by-the-blob-service"></a>A Blob szolgáltatás által használt összetevők
A következő áttekintést a Blob szolgáltatás-összetevők.

* **Storage-fiók**: Minden Azure Storage-hozzáférés tárfiókon keresztül valósítható meg. Ez a legmagasabb szintű névterét a blobok elérése. Egy fiók tartalmazhat egy korlátlan számú tárolót, mindaddig, amíg a teljes mérete 100TB alatt.
* **Tároló**: A tárolók blobok blobkészletek csoportosítását biztosítja. Az összes blobnak tárolóban kell lennie. Egy fiók korlátlan számú tárolót tartalmazhat. Egy tároló korlátlan számú blob tárolására használható.
* **A BLOB**: bármilyen típusú és bármekkora méretű fájl. Az Azure Storage tárolható blobok két típusa van: blokkblobok és lapblobok. A legtöbb fájlok blokk blobokat. Egyetlen blokkblob legfeljebb 200GB méretű lehet. Ez az oktatóanyag a blokkblobokhoz használja. Lapblobokat, egy másik blob típushoz, legfeljebb 1TB méretű, és a hatékonyabb lehet, ha egy fájlban bájt tartományok gyakran módosítják. Blobok kapcsolatos további információkért lásd: [ismertetése Blokkblobokat, hozzáfűző blobokat és Lapblobokat](http://msdn.microsoft.com/library/azure/ee691964.aspx).
* **URL-formátum**: Blobok olyan a következő URL-formátummal:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (A fenti formátum a nyilvános Azure felhőben vonatkozik. Ha egy másik Azure felhőbe használ, használja a végpont belül a [Azure Portal](https://portal.azure.com) az URL-végpontjának meghatározására.)
  
    A fenti formátumban `storageaccount` a tárfiók nevét jelöli `container_name` jelenti. a tároló neve és `blob_name` rendre a blob nevét jelöli. A tároló neve belül, akkor is több elérési út, szóközzel elválasztva perjellel,  **/** . Ebben az oktatóanyagban példa tároló neve **MyJob**, és **${BUILD\_azonosító} / ${BUILD\_szám}** lett a közös virtuális elérési útja, ami azt eredményezi, hogy a blob, hogy egy URL-cím a következő:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="next-steps"></a>Következő lépések
* [Jenkins felel meg](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)
* [Az Azure Storage Java SDK](https://github.com/azure/azure-storage-java)
* [Az Azure Storage ügyfél SDK-dokumentáció](http://dl.windowsazure.com/storage/javadoc/)
* [Az Azure Storage-szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Az Azure Storage csapat blogja](http://blogs.msdn.com/b/windowsazurestorage/)

További információ: [Azure Java-fejlesztőknek](/java/azure).