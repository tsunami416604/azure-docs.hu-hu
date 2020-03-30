---
title: A Hudson használata a Blob storage-tal | Microsoft dokumentumok
description: Bemutatja, hogyan használhatja a Hudson t a Build storage-t a buildösszetevők tárházaként.
services: storage
author: seguler
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 08/13/2019
ms.author: tarcher
ms.subservice: common
ms.openlocfilehash: a89439f49dd53f09d5cd40be0bf2e4981e9235d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201385"
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Az Azure Storage használata Hudson folyamatos integrációs megoldással
## <a name="overview"></a>Áttekintés
A következő információk bemutatják, hogyan használhatja a Blob storage-t a Hudson folyamatos integrációs (CI) megoldás által létrehozott buildösszetevők tárházaként, vagy a buildelő folyamatokban használható letölthető fájlok forrásaként. Az egyik forgatókönyv, ahol hasznosnak találná ezt, amikor agilis fejlesztői környezetben (Java vagy más nyelvek használatával) kódol, a buildek folyamatos integráción alapulnak, és szüksége van egy tárházra a buildösszetevőkhöz, hogy megoszthatja őket például a szervezet más tagjaival, az ügyfelekkel, vagy archiválást tarthat fenn.  Egy másik forgatókönyv, amikor maga a buildfeladat más fájlokat igényel, például a build bemenet részeként letöltő függőségeket.

Ebben az oktatóanyagban a Microsoft által elérhetővé tett Hudson CI Azure Storage beépülő modult fogja használni.

## <a name="introduction-to-hudson"></a>Bevezetés a Hudson
Hudson lehetővé teszi a folyamatos integráció ja a szoftver projekt lehetővé teszi a fejlesztők számára, hogy könnyen integrálja a kód változásokat, és épít előállított automatikusan és gyakran, ezáltal növelve a termelékenységet a fejlesztők. A buildek verziószámozottak, és a buildösszetevők különböző tárolókba tölthetők fel. Ez a cikk bemutatja, hogyan használhatja az Azure Blob storage a build összetevők tárházaként. Azt is bemutatja, hogyan tölthet le függőségeket az Azure Blob storage-ból.

További információ a Hudson megtalálható a [Meet Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>A Blob szolgáltatás használatának előnyei
A Blob szolgáltatás agilis fejlesztési buildösszetevők üzemeltetésének előnyei a következők:

* A buildösszetevők és/vagy a letölthető függőségek magas rendelkezésre állása.
* Teljesítmény, ha a Hudson CI-megoldás feltölti a buildösszetevőket.
* Teljesítmény, ha az ügyfelek és a partnerek letöltik a buildösszetevőket.
* A felhasználói hozzáférési házirendek vezérlése a névtelen hozzáférés, az elévülési alapú megosztott hozzáférés, a privát hozzáférés stb.

## <a name="prerequisites"></a>Előfeltételek
A Blob szolgáltatás Hudson CI-megoldással való használatához a következőkre lesz szüksége:

* A Hudson folyamatos integrációs megoldás.
  
    Ha jelenleg nem rendelkezik Hudson CI-megoldással, a következő technikával futtathat Hudson CI-megoldást:
  
  1. Java-kompatibilis gépen [töltse le a Hudson WAR fájlt.](https://www.eclipse.org/hudson/download.php)
  2. A Hudson WAR-t tartalmazó mappához megnyitott parancssorból futtassa a Hudson WAR-t. Ha például a 3.1.2-es verziót töltötte le:
     
      `java -jar hudson-3.1.2.war`

  3. Nyissa meg a `http://localhost:8080/`böngészőben a . Ez megnyitja a Hudson műszerfalat.
  4. A Hudson első használata után végezze `http://localhost:8080/`el a kezdeti beállítást a.
  5. Miután befejezte a kezdeti telepítést, szakítsa meg a Hudson WAR futó példányát, indítsa újra a Hudson WAR-t, majd nyissa meg újra a Hudson irányítópultot, `http://localhost:8080/`amelyet az Azure Storage beépülő modul telepítéséhez és konfigurálásához fog használni.
     
      Míg egy tipikus Hudson CI megoldás lenne beállítva, hogy fut, mint egy szolgáltatás, fut a Hudson háború a parancssorból elegendő lesz ez a bemutató.
* Egy Azure-fiók. Az Azure-fiókot a <https://www.azure.com>segítségével regisztrálhatja.
* Egy Azure-tárfiók. Ha még nem rendelkezik tárfiókkal, létrehozhat egyet a [Tárfiók létrehozása](../common/storage-account-create.md)című lépéslépéseivel.
* A Hudson CI-megoldás ismerete ajánlott, de nem szükséges, mivel a következő tartalom egy alapvető példát használ, hogy megmutassa a szükséges lépéseket, ha a Blob szolgáltatás a Hudson CI build összetevők tárházaként való használata.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>A Blob szolgáltatás használata a Hudson CI-vel
A Blob szolgáltatás Hudson használatával telepítenie kell az Azure Storage beépülő modult, konfigurálnia kell a bővítményt a tárfiók használatára, majd létre kell hoznia egy buildelés utáni műveletet, amely feltölti a buildösszetevőket a tárfiókba. Ezeket a lépéseket a következő szakaszok ismertetik.

## <a name="how-to-install-the-azure-storage-plugin"></a>Az Azure Storage beépülő modul telepítése
1. A Hudson irányítópulton kattintson a **Hudson kezelése**gombra.
2. A **Hudson kezelése** lapon kattintson a **Bővítmények kezelése gombra.**
3. Kattintson az **Elérhető** fülre.
4. Kattintson **a Mások gombra.**
5. A **Műtermék-feltöltők** csoportban válassza a **Microsoft Azure Storage beépülő modult.**
6. Kattintson a **Telepítés gombra.**
7. A telepítés befejezése után indítsa újra a Hudsont.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Az Azure Storage beépülő modul beállítása a tárfiók használatára
1. A Hudson irányítópulton kattintson a **Hudson kezelése**gombra.
2. A **Hudson kezelése** lapon kattintson a **Rendszer konfigurálása gombra.**
3. A **Microsoft Azure storage-fiók konfigurációja** szakaszban:
   
    a. Adja meg a tárfiók nevét, amelyet az [Azure Portalon](https://portal.azure.com)szerezhet be.
   
    b. Adja meg a tárfiók kulcsát, amely az [Azure Portalon](https://portal.azure.com)is beszerezhető.
   
    c. Használja a **Blob Service Endpoint URL-cím** alapértelmezett értékét, ha a globális Azure-felhőt használja. Ha egy másik Azure-felhőt használ, használja a végpontot az [Azure Portalon](https://portal.azure.com) a tárfiókhoz megadott módon.
   
    d. Kattintson a tárfiók érvényesítéséhez a **tárfiók érvényesítése** elemre.
   
    e. [Nem kötelező] Ha további tárfiókokat szeretne elérhetővé tenni a Hudson CI számára, kattintson **a További tárfiókok hozzáadása gombra.**
   
    f. A beállítások mentéséhez kattintson a **Mentés** gombra.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Build utáni művelet létrehozása, amely feltölti a buildösszetevőket a tárfiókba
Utasítás céljából először létre kell hoznunk egy feladatot, amely több fájlt hoz létre, majd hozzá kell adni a build utáni művelethez a fájlok feltöltéséhez a tárfiókba.

1. A Hudson irányítópulton kattintson az **Új feladat gombra.**
2. Nevezze el a **feladatot A MyJob**, kattintson **a Szabad stílusú szoftverfeladat létrehozása**elemre, majd kattintson az OK **gombra.**
3. A feladatkonfiguráció **Build** szakaszában kattintson a **Build hozzáadása lépés** gombra, és válassza a Windows **kötegelt végrehajtásparancsát.**
4. A **Command parancsban**használja a következő parancsokat:

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. A feladatkonfiguráció **Buildelés utáni műveletek** szakaszában kattintson az **Összetevők feltöltése a Microsoft Azure Blob storage-ba**elemre.
6. A **tárfiók neve,** válassza ki a tárfiókot használni.
7. A **Tároló neve mezőbe**adja meg a tároló nevét. (A tároló akkor jön létre, ha még nem létezik a buildösszetevők feltöltésekénél.) Használhat környezeti változókat, ezért ebben a példában adja meg **a ${JOB_NAME}** értéket a tároló neveként.
   
    **Tipp**
   
    Az alábbiakban a **Parancs** szakasz, ahol beírt egy parancsfájlt **a Windows végrehajtása batch parancs** egy linket a környezeti változók által elismert Hudson. Kattintson erre a hivatkozásra a környezeti változók nevének és leírásának megismeréséhez. A speciális karaktereket tartalmazó környezeti változók, például a **BUILD_URL** környezeti változó, nem engedélyezettek tárolónévként vagy közös virtuális elérési útként.
8. Kattintson az **Új tároló nyilvánossá szolgáltatása alapértelmezés szerint** ebben a példában. (Ha privát tárolót szeretne használni, a hozzáférés engedélyezéséhez létre kell hoznia egy közös hozzáférésű aláírást. Ez túlmutat a jelen cikk hatályán. A megosztott hozzáférésű aláírásokról a [Megosztott hozzáférésű aláírások használata (SAS)](storage-sas-overview.md)című helyen olvashat bővebben.)
9. [Nem kötelező] Kattintson **a Tároló tisztítása a feltöltés előtt,** ha azt szeretné, hogy a tároló takarodjon a tartalomból a buildösszetevők feltöltése előtt (hagyja bejelölve, ha nem szeretné megtisztítani a tároló tartalmát).
10. A **feltöltendő összetevők listájához**írja be **a szöveget/*.txt**mezőbe.
11. A **feltöltött összetevők közös virtuális elérési útja**esetén adja meg a **\_${BUILD ID}/${BUILD\_NUMBER} értéket.**
12. A beállítások mentéséhez kattintson a **Mentés** gombra.
13. A Hudson irányítópulton kattintson a **Build Now** elemre a **MyJob**futtatásához. Vizsgálja meg a konzol kimenetét az állapot. Az Azure Storage állapotüzenetei szerepelni fognak a konzol kimenetében, amikor a buildelés utáni művelet elkezdi feltölteni a buildelési összetevőket.
14. A feladat sikeres befejezése után megvizsgálhatja a buildösszetevők et a nyilvános blob megnyitásával.
    
    a. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
    
    b. Kattintson **a Tárolás gombra.**
    
    c. Kattintson a Hudsonhoz használt tárfiók nevére.
    
    d. Kattintson a **Tárolók** lehetőségre.
    
    e. Kattintson a **myjob**nevű tárolóra, amely a Hudson-feladat létrehozásakor hozzárendelt feladatnév kisbetűs verziója. A tárolónevek és a blobnevek kisbetűsek (és a kis- és nagybetűk et is kivannak) az Azure Storage-ban. A **myjob** nevű tároló blobjainak listáján a **hello.txt** és a **date.txt**fájlnak kell lennie. Másolja az elemek valamelyikének URL-címét, és nyissa meg a böngészőben. Látni fogja a buildösszetevőként feltöltött szövegfájlt.

Feladatonként csak egy, az Azure Blob storage-ba összetevőket feltöltő, létrehozás utáni művelet hozható létre. Az azure Blob storage-ba összetevők feltöltéséhez szükséges egyetlen létrehozás utáni művelet különböző fájlokat (beleértve a helyettesítő karaktereket) és az összetevők listájában feltöltendő fájlok elérési útjait a pontosvessző elválasztóként történő **feltöltésével adhatja meg.** Ha például a Hudson-build jar-fájlokat és TXT-fájlokat hoz létre a munkaterület **buildmappájában,** és mindkettőt fel szeretné tölteni az Azure Blob storage-ba, használja az alábbiakat az összetevők listájához az érték **feltöltéséhez:** **build/\*.jar;build/\*.txt**. A kettőspont szintaxisa segítségével megadhatja a blob névben használandó elérési utat is. Ha például azt szeretné, hogy a jars-t bináris **fájlok** használatával töltse fel a blob elérési útján, és a TXT-fájlokat a blob elérési útján **lévő hirdetmények** használatával töltse fel, használja az alábbiakat az összetevők listájához az összetevők listájához az érték **feltöltéséhez:** **build/\*.jar::binaries;build/\*.txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Az Azure Blob storage-ból letöltött buildlépés létrehozása
A következő lépések bemutatják, hogyan konfigurálhat egy buildlépést az elemek Azure Blob storage-ból való letöltéséhez. Ez akkor hasznos, ha elemeket szeretne felvenni a buildbe, például az Azure Blob-tárolóban tárolt JAR-eket.

1. A **feladatkonfiguráció Build** szakaszában kattintson a **Build hozzáadása lépés** elemre, és válassza a Letöltés az Azure Blob **storage-ból lehetőséget.**
2. A **Storage-fiók neve,** válassza ki a tárfiókot használni.
3. A **Tároló neve**mezőbe írja be a letölteni kívánt blobokat tartalmazó tároló nevét. Használhatja a környezeti változókat.
4. A **Blob neve**esetén adja meg a blob nevét. Használhatja a környezeti változókat. Emellett használhat egy csillagot helyettesítő karakterként, miután megadta a blob név kezdő betűjét.Also, you can use an csillag, as a helyettesítő karakter after you specify the initial letter(s) of the blob name. A ***\\projekt**például minden olyan blobot megad, amelynek a neve **a projekttel**kezdődik.
5. [Nem kötelező] A **letöltési útvonal,** adja meg az elérési utat a Hudson-gépen, ahol fájlokat szeretne letölteni az Azure Blob storage.For Download path, specify the path on the Hudson machine where you want to download files from Azure Blob storage. Környezeti változók is használhatók. (Ha nem ad meg értéket a **letöltési útvonalhoz,** az Azure Blob storage-ból származó fájlok a feladat munkaterületére töltődnek le.)

Ha további elemeket szeretne letölteni az Azure Blob storage-ból, további buildlépéseket hozhat létre.

A build futtatása után ellenőrizheti a buildelőzmények konzolkimenetét, vagy megtekintheti a letöltési helyet, és ellenőrizheti, hogy a várt blobok letöltése sikeresen megtörtént-e.

## <a name="components-used-by-the-blob-service"></a>A Blob szolgáltatás által használt összetevők
Az alábbiakban áttekintést nyújt a Blob szolgáltatás összetevői.

* **Tárfiók:** Az Azure Storage-hoz való hozzáférés egy tárfiókon keresztül történik. Ez a névtér legmagasabb szintje a blobok eléréséhez. Egy fiók korlátlan számú tárolót tartalmazhat, amennyiben azok teljes mérete 100 TB alatt van.
* **Tároló:** A tároló blobok készletének csoportosítását biztosítja. Az összes blobnak tárolóban kell lennie. Egy fiók korlátlan számú tárolót tartalmazhat. Egy tároló korlátlan számú blob tárolására használható.
* **Blob**: Bármilyen típusú és méretű fájl. Kétféle blobok, amelyek tárolhatók az Azure Storage-ban: blokk és lap blobok. A legtöbb fájl blokkblob. Egy blokkblob mérete legfeljebb 200 GB lehet. Ez az oktatóanyag blokkblobokat használ. A lapblobok, egy másik blobtípus mérete legfeljebb 1 TB lehet, és hatékonyabbak, ha egy fájlban lévő bájtok tartományait gyakran módosítják. A blobokról a [Blokkblobok, a Hozzáfűző blobok és a Lapblobok ismertetése](https://msdn.microsoft.com/library/azure/ee691964.aspx)című témakörben talál további információt.
* **URL-formátum**: A blobok a következő URL-formátumban címezhetők:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (A fenti formátum a globális Azure-felhőre vonatkozik. Ha egy másik Azure-felhőt használ, használja a végpontot az [Azure Portalon](https://portal.azure.com) az URL-végpont meghatározásához.)
  
    A fenti formátumban `storageaccount` a tárfiók nevét, `container_name` a tároló nevét jelöli, `blob_name` és a blob nevét jelöli. A tároló névén belül több elérési út is lehet, **/** amelyeket perjel lelt el egymástól. Ebben az oktatóanyagban a példatároló neve **A MyJob**volt, és **${BUILD\_ID}/${BUILD\_NUMBER}** volt a közös virtuális elérési úthoz, aminek eredményeképpen a blob URL-címe a következő űrlap:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>További lépések
* [Ismerje meg Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [Azure Storage SDK Java-hoz](https://github.com/azure/azure-storage-java)
* [Azure Storage ügyféloldali SDK-referencia](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [Az Azure Storage-szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Az Azure Storage-csapat blogja](https://blogs.msdn.com/b/windowsazurestorage/)

További információ: [Azure Java-fejlesztőknek](/java/azure).
