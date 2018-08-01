---
title: Az Azure Storage használata Jenkins folyamatos integrációs megoldással
description: Ebben az oktatóanyagban bemutatják, hogyan használhatja az Azure blob service-adattár létrehozása a Jenkins folyamatos integrációs megoldással létrehozott összetevők.
ms.topic: article
ms.author: tarcher
author: tarcher
manager: jpconnock
services: devops
custom: jenkins
ms.date: 07/31/2018
ms.openlocfilehash: 24248b198965ddf9ed9ef8df67c34560d08d3935
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39389847"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Az Azure Storage használata Jenkins folyamatos integrációs megoldással

Ez a cikk bemutatja, hogyan használja a Blob storage vagy a Jenkins folyamatos integrációs (CI) megoldás által létrehozott, vagy letölthető fájlok forrásként használható a létrehozási folyamat. A forgatókönyvek, ahol akkor is hasznos ez a megoldás egyik, kódolási használ (a Java vagy más nyelvek) Agilis fejlesztési környezetben, a folyamatos integrációs buildek alapján futnak, és a build-összetevőket, szüksége lesz egy adattár, hogy Ön sikerült, például megoszthatók más szervezet tagjaival, az ügyfelek és fenntartása az archívumot. Egy másik helyzet lehet, ha a fordítási feladatot magán szükséges egyéb fájlokat, például a build bemeneti részeként letöltéséhez függőségek.

Ebben az oktatóanyagban fog használni az Azure Storage beépülő modul a Jenkins CI a Microsoft által készített érhető el.

## <a name="jenkins-overview"></a>Jenkins – áttekintés
A Jenkins lehetővé teszi, hogy a folyamatos integráció, a szoftver projekt azáltal, hogy a fejlesztők könnyedén integrálhatják a programkód módosítása és buildek előállított automatikusan és gyakran, ezáltal a fejlesztők a termelékenység növelése. Buildek rendszerverzióval ellátott, illetve build-összetevőket is feltölthetők a különböző adattárak. Ez a cikk bemutatja az Azure blob storage használata a tárházban, a build-összetevőket. Azt is megjelennek a függőségek letöltése az Azure blob storage-ból.

További információ a Jenkins található [felel meg a Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>A Blob szolgáltatás használatának előnyei
A Blob service használata az Agilis fejlesztési build-összetevők futtatásához előnyei a következők:

* Magas rendelkezésre állás, a build-összetevőket és/vagy letölthető függőségek.
* Teljesítmény, ha a Jenkins CI-megoldás a build-összetevőket tölt fel.
* Amikor az ügyfelek és partnerek töltse le a build-összetevők teljesítményét.
* Felhasználói hozzáférési házirendeket, a névtelen hozzáférés, lejárati-alapú közös hozzáférésű jogosultságkód hozzáférést, privát választhat felett ellenőrzést hozzáférés, stb.

## <a name="prerequisites"></a>Előfeltételek
* A Jenkins folyamatos integrációs megoldással.
  
    Ha jelenleg nem rendelkezik a Jenkins CI-megoldás, a Jenkins CI megoldás a következő eljárás használatával futtathatók:
  
  1. Egy Java-kompatibilis gépen töltse le a jenkins.war <http://jenkins-ci.org>.
  2. Egy parancssori ablakot, amely a mappába, amelyben jenkins.war meg van nyitva futtassa a következő:
     
      `java -jar jenkins.war`

  3. A böngészőben nyissa meg a `http://localhost:8080/` a Jenkins-irányítópult, amelynek használatával telepítheti és konfigurálhatja az Azure Storage beépülő modul megnyitásához.
     
      Bár egy tipikus a Jenkins CI-megoldások volna úgy, hogy a szolgáltatás, a Jenkins war futtatása a parancssorból futtassa a jelen oktatóanyag elegendő lesz.
* Egy Azure-fiók. Iratkozzon fel a következő Azure-fiókot <http://www.azure.com>.
* Egy Azure-tárfiók. Ha a tárfiók már nem rendelkezik, létrehozhat egyet a következő lépések használatával [hozzon létre egy Tárfiókot](../common/storage-create-storage-account.md#create-a-storage-account).
* A Jenkins CI megoldás ismeretét ajánlott, de nem szükséges, az alábbi tartalommal fogja használni egy alapszintű példa mutatni, ha a Blob szolgáltatás használ a Jenkins CI vagy a szükséges lépéseket build-összetevőket.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>A Blob service használata a Jenkins CI
A Blob service használata a jenkins-szel, szüksége lesz az Azure Storage beépülő modul telepítéséhez, a storage-fiók használata a beépülő modul konfigurálásához és majd a felépítés utáni művelet, amely a build-összetevőket tölt fel a tárfiók létrehozása. A következő szakaszok ezeket a lépéseket ismerteti.

## <a name="how-to-install-the-azure-storage-plugin"></a>Az Azure Storage beépülő modul telepítése
1. A Jenkins irányítópultján válassza **Jenkins kezelése**.
2. Az a **Jenkins kezelése** lapon jelölje be **beépülő modulok kezelése**.
3. Válassza ki a **elérhető** fülre.
4. Az a **összetevő Feltöltők** területen ellenőrzés **a Microsoft Azure Storage beépülő modul**.
5. Ezek közül bármelyikre **újraindítás nélküli** vagy **letöltése és telepítése után indítsa újra**.
6. Indítsa újra a Jenkins.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>A tárfiók használata az Azure Storage beépülő modul konfigurálása
1. A Jenkins irányítópultján válassza **Jenkins kezelése**.
2. Az a **Jenkins kezelése** lapon jelölje be **rendszer konfigurálása**.
3. Az a **a Microsoft Azure Storage-fiók konfigurációja** szakaszban:
   1. Adja meg a tárfiók nevét, amely szerezhet be a [az Azure Portal](https://portal.azure.com).
   2. Adja meg a tárfiók kulcsára, is megrendelhető az [az Azure Portal](https://portal.azure.com).
   3. Használja az alapértelmezett értéket a **Blob Service-végpont URL-címe** Ha használja a globális Azure-felhőben. Ha egy másik Azure-felhőben használja, használja a végponthoz megadott a [az Azure Portal](https://portal.azure.com) a tárfiók számára. 
   4. Válassza ki **tároló hitelesítő adatainak ellenőrzésére** ellenőrzése a storage-fiókjában. 
   5. [Opcionális] Ha további tárfiókokat, amelyeket szeretne elérhetővé a Jenkins CI, válassza ki a **további Tárfiókok hozzáadása**.
   6. Válassza ki **mentése** a beállítások mentéséhez.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>A felépítés utáni művelet, amely a build-összetevőket tölt fel a tárfiók létrehozása
Oktatási célokra, először hozzon létre egy feladatot, amely több, a fájl létrehozásához, majd adja hozzá a felépítés utáni műveletben, a fájlok feltöltése a storage-fiók.

1. A Jenkins irányítópultján válassza **új elem**.
2. Nevezze el a feladat **MyJob**, jelölje be **egy szoftver szabad stílusú projekt**, majd válassza ki **OK**.
3. Az a **hozhat létre** a feladat-konfiguráció, válassza ki a szakasz **felépítési lépés hozzáadása** , és válassza ki **végrehajtása Windows kötegparanccsal**.
4. A **parancs**, használja a következő parancsokat:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. Az a **felépítés utáni műveletekben** szakaszában a feladat konfigurálása, jelölje be **felépítés utáni művelet hozzáadása** válassza **összetevők feltöltése az Azure Blob storage-**.
6. A **tárfióknevet**, válassza ki a használandó tárfiókot.
7. A **Tárolónév**, adja meg a tároló nevét. (A tároló fog hozható létre, ha ezt még nem létezik a build-összetevőket feltöltésekor.) Környezeti változók használata is, így ebben a példában adja meg `${JOB_NAME}` a tároló neve.
   
    **Tipp**
   
    Alább a **parancs** szkript megadásánál szakasz **végrehajtása Windows kötegparanccsal** a környezeti változók ismeri fel a Jenkins mutató hivatkozás. Válassza ki a hivatkozást a környezeti változók neve és leírása. Például különleges karaktereket tartalmazó környezeti változókat a **BUILD_URL** környezeti változót, a tároló neve vagy a közös virtuális elérési út nem engedélyezettek.
8. Válassza ki **alapértelmezés szerint az új tároló nyilvánosságra** ebben a példában. (Ha szeretne egy privát tároló használatára, szüksége engedélyezze a hozzáférést, amely ebben a cikkben hatókörén kívül esik a közös hozzáférésű jogosultságkód létrehozása. További információ a közös hozzáférésű jogosultságkódok [használatával a közös hozzáférésű Jogosultságkódok (SAS)](../storage-dotnet-shared-access-signature-part-1.md).)
9. [Opcionális] Válassza ki **tiszta tároló feltöltése előtt** Ha azt szeretné, hogy a tároló tartalmát törölni kell, mielőtt feltöltésekor a rendszer a build-összetevőket (ne jelölje ezt, ha nem szeretné, hogy a tároló tartalmának törlése).
10. A **összetevők lista feltöltéséhez**, adja meg `text/*.txt`.
11. A **feltöltött összetevők közös virtuális elérési út**, adja meg a jelen oktatóanyag esetén `${BUILD\_ID}/${BUILD\_NUMBER}`.
12. Válassza ki **mentése** a beállítások mentéséhez.
13. A Jenkins irányítópultján válassza **hozhat létre most** futtatásához **MyJob**. Vizsgálja meg a konzol kimenete az állapotát. Az Azure storage-állapotüzeneteinek fog szerepelni a konzol kimenete feltölteni a build-összetevőket a felépítés utáni művelet indításakor.
14. A feladat sikeres telepítést vizsgálja meg a build-összetevőket a nyilvános blob megnyitásával.
    1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
    2. Válassza a **Storage** lehetőséget.
    3. Válassza ki a tárfiók nevét, a Jenkins is használ.
    4. Válassza ki **tárolók**.
    5. Válassza ki a tárolót nevű **myjob**, amely az, hogy a feladat neve, amely a Jenkins-feladat létrehozásakor hozzárendelt kis verziója. A tároló nevének és a blob nevét is kisbetű (és a kis-és nagybetűket) az Azure storage-ban. A tárolót a blobok listáját belül **myjob**, megtekintheti az **hello.txt** és **date.txt**. Ezek az elemek egyikét másolja az URL-címet, és nyissa meg a böngészőben. Szöveges fájlt töltött fel, mint a buildösszetevőt jelenik meg.

Csak egy felépítés utáni művelet, amely feltölti az összetevők az Azure blob storage feladatonként hozható létre. Megadhatja, hogy az egyetlen felépítés utáni művelet összetevők feltöltése az Azure blob storage (beleértve a helyettesítő karaktereket) a különböző fájlok és a belül fájlok elérési útjait **összetevők lista feltöltéséhez** pontosvesszővel az elválasztó használatával. Például, ha a Jenkins felépítési eredményez JAR-fájlok és a TXT-fájlokat a munkaterületen **hozhat létre** mappát, és szeretné feltölteni, mind az Azure blob storage, használja a következő értéket a **listán az összetevőkfeltöltése** lehetőség: `build/\*.jar;build/\*.txt`. Dupla kettőspont szintaxis segítségével adja meg egy elérési útját használja a blob neveként belül. Például, ha azt szeretné, hogy a JAR-fájlok kivételével az első feltöltött használatával **bináris** a blob elérési útja és a TXT-fájlokat feltölteni lekérése használatával **hirdetmények** a blob elérési út a következő értéket használja a **listáját A feltöltendő összetevők** lehetőség: `build/\*.jar::binaries;build/\*.txt::notices`.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Hogyan hozhat létre, amely az Azure blob storage-ból letöltött buildelési lépést
A következő lépések bemutatják egy lépést, amely elemek letöltése az Azure blob storage, amely akkor hasznos, ha fel szeretne venni az elemek a buildek konfigurálása. Egy példa ezt a mintát használja, a JAR-fájlok kivételével, amelyeket érdemes az Azure blob storage-ban megőrizni.

1. Az a **hozhat létre** szakaszában a feladat konfigurálása, jelölje be **felépítési lépés hozzáadása** válassza **töltse le az Azure Blob storage-ból**.
2. A **tárfióknevet**, válassza ki a használandó tárfiókot.
3. A **Tárolónév**, adja meg a tároló, amelyben a blobokat letölteni kívánt nevét. Környezeti változókat is használhat.
4. A **blobnév**, adja meg a blob nevét. Környezeti változókat is használhat. Is használhatja egy csillagot helyettesítő karakter a kezdeti betűjét a blob nevének megadása után. Ha például **projekt\***  kellene megadnia kezdődő összes BLOB **projekt**.
5. [Opcionális] A **letöltési mappa elérési útját**, ahol szeretné tölteni a fájlokat az Azure blob storage-ból a Jenkins gépen elérési útjának megadásához. A környezeti változók is használható. (Ha nem ad meg értéket **letöltési mappa elérési útját**, a feladatok munkaterület a fájlok az Azure blob storage-ból letöltött.)

Ha további elemek szeretné tölteni az Azure blob storage-ból, létrehozhat további létrehozási lépések.

Miután lefuttatta a build, ellenőrizze a korábbi konzol kimenete, vagy tekintse meg a letöltési helyét, megtekintheti, hogy a várt blobok letöltése sikeresen megtörtént.  

## <a name="components-used-by-the-blob-service"></a>A Blob szolgáltatás által használt összetevők
Ez a szakasz a Blob szolgáltatás-összetevők áttekintést nyújt.

* **Storage-fiók**: Minden Azure Storage-hozzáférés tárfiókon keresztül valósítható meg. Blobok elérése a névteret a legmagasabb szintű. Egy fiók korlátlan számú tárolók, tartalmazhat, mindaddig, amíg azok teljes mérete 100 TB-os alatt.
* **Tároló**: tároló blobokat áll csoportosítását biztosítja. Az összes blobnak tárolóban kell lennie. Egy fiók korlátlan számú tárolót tartalmazhat. Egy tároló korlátlan számú blob tárolására használható.
* **BLOB**: bármilyen típusú és méretű fájl. A blobok Azure Storage-ban tárolt két típusa van: blokkblobok és lapblobok. Fájlok a legtöbb esetben blokkblobok. Egyetlen blokkblob akár 200 GB méretű lehet. Ebben az oktatóanyagban a blokkblobok használatát támogatják. Lapblobok és a egy másik blob típusú tárfiók, akár 1 TB-ig a mérete, és kiváló hatékonyabb lehet, ha egy fájlban bájtos címtartományok gyakran módosítják. Blobok kapcsolatos további információkért lásd: [Understanding Block Blobs, hozzáfűző blobokat és Lapblobokat](http://msdn.microsoft.com/library/azure/ee691964.aspx).
* **URL-formátum**: Blobok legyenek a következő URL-formátummal:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (A fenti formátum vonatkozik a globális Azure-felhőben. Egy másik Azure-felhőben használja, ha a végpont belül használja a [az Azure Portal](https://portal.azure.com) meghatározni az URL-végpontot.)
  
    A fenti formátumban `storageaccount` pedig a tárfiók nevét `container_name` a tároló nevét jelöli, és `blob_name` rendre a blob nevét jelöli. A tároló nevének belül használhat több elérési útnak perjellel elválasztva **/**. A példa tároló neve ebben az oktatóanyagban használt **MyJob**, és **${hozhat létre\_azonosító} / ${hozhat létre\_szám}** használták a közös virtuális elérési utat, a blob URL-címet kellene eredményez a következő formátumot követi:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>Hibaelhárítás a Jenkins beépülő modul

Ha bármely hibái a Jenkins beépülő modulok, a fájl be a problémát a [Jenkins JIRA](https://issues.jenkins-ci.org/) az adott összetevő.

## <a name="next-steps"></a>További lépések
* [A Jenkins felel meg](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)
* [Az Azure Storage-hoz készült Java SDK](https://github.com/azure/azure-storage-java)
* [Azure Storage ügyféloldali SDK-referencia](http://dl.windowsazure.com/storage/javadoc/)
* [Az Azure Storage-szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Az Azure Storage csapat blogja](http://blogs.msdn.com/b/windowsazurestorage/)

További információ: [Azure Java-fejlesztőknek](/java/azure).