---
title: A Hudson használata blob Storage-tárolóval | Microsoft Docs
description: Leírja, hogyan használható a Hudson az Azure Blob Storage-ban a Build-összetevők tárháza.
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
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Az Azure Storage használata egy Hudson folyamatos integrációs megoldással
## <a name="overview"></a>Áttekintés
Az alábbi információk azt mutatják be, hogyan használhatók a blob Storage a Hudson folyamatos integrációs (CI) megoldás által létrehozott Build-összetevők tárháza, vagy a felépítési folyamat során felhasználható letölthető fájlok forrásaként. Az egyik forgatókönyv, ahol hasznosnak találta ezt a megoldást, ha egy agilis fejlesztési környezetben (Java vagy más nyelv használatával) végez kódolást, a buildek folyamatos integráción alapulnak, és szüksége van a Build-összetevők tárházára, így például megoszthatja őket más szervezet tagjaival, ügyfeleivel, vagy megtarthatja az archívumot.  Egy másik forgatókönyv, ha a felépítési feladatnak más fájlokra is szüksége van, például a Build bemenet részeként letölteni kívánt függőségekre.

Ebben az oktatóanyagban a Microsoft által elérhető Hudson CI-hez készült Azure Storage beépülő modult fogja használni.

## <a name="introduction-to-hudson"></a>A Hudson bemutatása
A Hudson lehetővé teszi a szoftveres projektek folyamatos integrálását azáltal, hogy lehetővé teszi a fejlesztők számára, hogy egyszerűen integrálják a kód módosításait, és automatikusan és gyakran hozzanak létre buildeket, ezáltal növelve a fejlesztők termelékenységét. A buildek verziója, és az összetevők feltölthetők különböző adattárakba. Ez a cikk bemutatja, hogyan használható az Azure Blob Storage a Build-összetevők tárháza. Azt is bemutatja, hogyan tölthetők le az Azure Blob Storage függőségei.

A Hudson-vel kapcsolatos további információkért tekintse [meg](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)a következőt:.

## <a name="benefits-of-using-the-blob-service"></a>A Blob service használatának előnyei
A Blob service használatának előnyei az agilis fejlesztői Build-összetevők üzemeltetéséhez:

* A Build-összetevők és/vagy a letölthető függőségek magas rendelkezésre állása.
* Teljesítmény, ha a Hudson CI-megoldás feltölti a Build-összetevőket.
* Teljesítmény, amikor az ügyfelek és a partnerek letöltik a Build-összetevőket.
* A felhasználói hozzáférési házirendek vezérlése a névtelen hozzáférés, a lejáraton alapuló közös hozzáférésű aláírás-hozzáférés, a privát hozzáférés stb. között.

## <a name="prerequisites"></a>Előfeltételek
A következőkre lesz szüksége a Blob service és a Hudson CI megoldás használatához:

* Hudson folyamatos integrációs megoldás.
  
    Ha jelenleg nem rendelkezik Hudson CI-megoldással, a következő módszer használatával futtathat egy Hudson CI-megoldást:
  
  1. A Java-kompatibilis gépen [töltse le a Hudson War-fájlt](https://www.eclipse.org/hudson/download.php).
  2. Nyisson meg egy parancssort, amely a Hudson WAR-t tartalmazó mappához van megnyitva. Futtassa a Hudson WAR-t. Ha például letöltötte a 3.1.2-es verziót:
     
      `java -jar hudson-3.1.2.war`

  3. A böngészőben nyissa meg `http://localhost:8080/`a t. Ekkor megnyílik a Hudson-irányítópult.
  4. A Hudson első használatakor végezze el a kezdeti telepítést `http://localhost:8080/`a következő címen:.
  5. A kezdeti beállítás befejezése után szakítsa meg a Hudson-háború futó példányát, indítsa el újra a Hudson-HÁBORÚt, majd nyissa meg `http://localhost:8080/`újra a Hudson-irányítópultot, amelyet az Azure Storage beépülő modul telepítéséhez és konfigurálásához fog használni.
     
      Habár egy tipikus Hudson CI-megoldás úgy lesz beállítva, hogy szolgáltatásként fusson, a Hudson War a parancssorban való futtatása elegendő lesz ehhez az oktatóanyaghoz.
* Egy Azure-fiók. Regisztrálhat egy Azure-fiókot a következő címen: <https://www.azure.com>.
* Egy Azure-tárfiók. Ha még nem rendelkezik Storage-fiókkal, létrehozhat egyet a [Storage-fiók létrehozása](../common/storage-account-create.md)című témakör lépéseit követve.
* A Hudson CI-megoldás ismerete ajánlott, de nem szükséges, mivel az alábbi tartalom egy alapszintű példát mutat be a Blob service a Hudson CI-Build-összetevők tárházként való használatakor szükséges lépések megjelenítéséhez.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>A Blob service használata a Hudson CI-vel
Ahhoz, hogy a Blob servicet a Hudson használatával használhassa, telepítenie kell az Azure Storage beépülő modult, konfigurálnia kell a beépülő modult a Storage-fiók használatára, majd létre kell hoznia egy utólagos felépítési műveletet, amely feltölti a Build-összetevőket a Storage-fiókjába. Ezeket a lépéseket a következő szakaszokban ismertetjük.

## <a name="how-to-install-the-azure-storage-plugin"></a>Az Azure Storage beépülő modul telepítése
1. A Hudson-irányítópulton kattintson a **Hudson kezelése**lehetőségre.
2. A **Hudson kezelése** lapon kattintson a **beépülő modulok kezelése**lehetőségre.
3. Kattintson a **rendelkezésre álló** fülre.
4. Kattintson a **mások**gombra.
5. Az összetevő- **feltöltők** szakaszban válassza **Microsoft Azure Storage beépülő modult**.
6. Kattintson a **telepítés**gombra.
7. A telepítés befejezése után indítsa újra a Hudson-t.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Az Azure Storage beépülő modul konfigurálása a Storage-fiók használatára
1. A Hudson-irányítópulton kattintson a **Hudson kezelése**lehetőségre.
2. A **Hudson kezelése** lapon kattintson a **rendszer konfigurálása**elemre.
3. A **Microsoft Azure Storage fiók konfigurációja** szakaszban:
   
    a. Adja meg a Storage-fiók nevét, amelyet a [Azure Portal](https://portal.azure.com)szerezhet be.
   
    b. Adja meg a Storage-fiók kulcsát, amely a [Azure Portal](https://portal.azure.com)is beszerezhető.
   
    c. Ha a globális Azure-felhőt használja, használja az alapértelmezett értéket a **blob Service-végpont URL-címéhez** . Ha más Azure-felhőt használ, használja a [Azure Portalban](https://portal.azure.com) megadott végpontot a Storage-fiókjához.
   
    d. A Storage-fiók érvényesítéséhez kattintson a **tárolási hitelesítő adatok ellenőrzése** elemre.
   
    e. Választható Ha további Storage-fiókokat szeretne elérhetővé tenni a Hudson CI számára, kattintson a **további Storage-fiókok hozzáadása**lehetőségre.
   
    f. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Build-összetevők feltöltése a Storage-fiókba feltöltés utáni művelet létrehozása
Útmutatásként először létre kell hozni egy olyan feladatot, amely több fájlt hoz létre, majd a létrehozás utáni művelettel felveszi a fájlokat a Storage-fiókjába.

1. A Hudson-irányítópulton kattintson az **új feladatok**elemre.
2. Adja meg a **MyJob**nevet, kattintson a **szabad stílusú szoftver felépítése**elemre, majd kattintson **az OK**gombra.
3. A feladatok konfigurációjának **build (létrehozás** ) részében kattintson a **Build lépés hozzáadása** elemre, majd válassza a **Windows batch-parancs végrehajtása parancsot**.
4. A **parancsban**használja a következő parancsokat:

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. A feladatok konfigurációjának **létrehozás utáni műveletek** szakaszában kattintson az összetevők feltöltése elemre **Microsoft Azure Blob Storage-hoz**.
6. A **Storage-fiók neve**mezőben válassza ki a használni kívánt Storage-fiókot.
7. A **tároló neve**mezőben adja meg a tároló nevét. (A tároló akkor jön létre, ha még nem létezik a Build-összetevők feltöltésekor.) Használhat környezeti változókat, így ebben a példában a **$ {JOB_NAME}** értéket adja meg a tároló neveként.
   
    **Tipp**
   
    A **parancs** alatt, ahol a **Windows batch futtatása parancshoz** megadott egy parancsfájlt, a Hudson által felismert környezeti változókra mutató hivatkozás. Kattintson erre a hivatkozásra a környezeti változók nevének és leírásának megismeréséhez. A speciális karaktereket (például a **BUILD_URL** környezeti változót) tartalmazó környezeti változók nem engedélyezettek tároló neveként vagy közös virtuális elérési úton.
8. Ehhez a példához kattintson az **új tároló nyilvánosvé tétele alapértelmezés szerint** lehetőségre. (Ha privát tárolót szeretne használni, létre kell hoznia egy közös hozzáférési aláírást a hozzáférés engedélyezéséhez. Ez meghaladja a jelen cikk hatókörét. A közös hozzáférési aláírásokról a [közös hozzáférési aláírások (SAS) használatával](storage-sas-overview.md)tájékozódhat.)
9. Választható Ha azt szeretné, hogy a tárolót az összetevők feltöltése előtt törölje, kattintson a tároló **tisztítása** elemre, ha azt szeretné, hogy a tároló tartalma ne legyen feltöltve (ne jelölje be a jelölőnégyzetet, ha nem szeretné törölni a tároló tartalmát).
10. A **feltölteni kívánt összetevők listájához**írja be a **Text/*. txt fájlt**.
11. A **feltöltött összetevők közös virtuális elérési útjának**megadásához írja be a következőt: **\_$\_{Build ID}/$ {Build Number}**.
12. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra.
13. A Hudson-irányítópulton kattintson a **Build most** gombra a **MyJob**futtatásához. Ellenőrizze a konzol kimenetét az állapothoz. Az Azure Storage-hoz tartozó állapotüzenetek a konzol kimenetében jelennek meg, amikor a Build utáni művelet elindítja a Build-összetevők feltöltését.
14. A feladatok sikeres befejezését követően a nyilvános blob megnyitásával ellenőrizheti a Build-összetevőket.
    
    a. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
    
    b. Kattintson a **tárterület**elemre.
    
    c. Kattintson a Hudson-hoz használt Storage-fiók nevére.
    
    d. Kattintson a **Tárolók** lehetőségre.
    
    e. Kattintson a **myjob**nevű tárolóra, amely a Hudson-feladatok létrehozásakor hozzárendelt feladatnév kisbetűs verziója. A tárolók neve és a blob neve kisbetűs (és kis-és nagybetűket megkülönböztető) az Azure Storage-ban. A **myjob** nevű tároló Blobok listáján belül a **Hello. txt** és a **Date. txt fájlt**kell látnia. Másolja az egyik elem URL-címét, és nyissa meg a böngészőben. Ekkor megjelenik a Build-összetevőként feltöltött szövegfájl.

Feladatokból csak egy, az Azure Blob Storage-ba feltöltés utáni művelet hozható létre. Az összetevők Azure Blob Storage-ba való feltöltésének egyetlen utólagos felépítési művelete különböző fájlokat (beleértve a helyettesítő karaktereket) és a fájlok elérési útját is megadhatja egy pontosvesszővel elválasztó karakterrel **feltöltött összetevők listáján** belül. Ha például a Hudson Build JAR-fájlokat és TXT-fájlokat hoz létre a munkaterület **Build** mappájában, és az Azure Blob Storage-ba szeretne feltölteni, a következőt kell használnia a **feltöltendő összetevők listájához** : **Build/\*. jar; Build/\*. txt**. A dupla kettőspontos szintaxist is használhatja a blob nevében használandó elérési út megadásához. Ha például azt szeretné, hogy az adattégelyek a blob elérési útján **bináris** fájlok használatával legyenek feltöltve, és a txt-fájlok a blob elérési útjában lévő **megjegyzésekkel** töltődnek fel, a következőt kell használnia a **feltöltési** értékre: **\*Build/.\*jar:: bináris; Build/. txt:: megjegyzések**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Az Azure Blob Storage-ból letöltött Build lépés létrehozása
A következő lépések bemutatják, hogyan konfigurálhat egy Build lépést az elemek Azure Blob Storage-ból való letöltéséhez. Ez akkor lehet hasznos, ha a buildben lévő elemeket is bele kívánja foglalni, például az Azure Blob Storage-ban megőrizni kívánt tégelyeket.

1. A feladatok konfigurációjának **build (létrehozás** ) részében kattintson a **Build lépés hozzáadása** elemre, és válassza a **Letöltés az Azure Blob Storage-ból**lehetőséget.
2. A **Storage-fiók neve**mezőben válassza ki a használni kívánt Storage-fiókot.
3. A **tároló neve**mezőben adja meg annak a tárolónak a nevét, amely a letölteni kívánt blobokat tartalmazni kívánja. Környezeti változókat is használhat.
4. A **blob neve**mezőben adja meg a blob nevét. Környezeti változókat is használhat. Azt is megteheti, hogy egy csillagot is használhat helyettesítő karakterként, miután megadta a blob nevének kezdeti betűjelét (ke) t. A **\\Project*** például minden olyan blobot megad, amelynek a neve a **projekttel**kezdődik.
5. Választható A **letöltési útvonal**mezőben határozza meg az elérési utat a Hudson-gépen, amelyen le szeretné tölteni a fájlokat az Azure Blob Storage-ból. A környezeti változók is használhatók. (Ha nem ad meg értéket a **letöltési útvonalhoz**, a rendszer letölti az Azure Blob Storage-ból származó fájlokat a feladatok munkaterületére.)

Ha további elemeket szeretne letölteni az Azure Blob Storage-ból, további létrehozási lépéseket is létrehozhat.

A Build futtatása után megtekintheti a létrehozási előzmények konzoljának kimenetét, vagy megtekintheti a letöltési helyet, és megtekintheti, hogy a várt Blobok letöltése sikeres volt-e.

## <a name="components-used-by-the-blob-service"></a>Az Blob service által használt összetevők
Az alábbi áttekintést nyújt a Blob service összetevőkről.

* **Storage-fiók**: az Azure Storage-hoz való összes hozzáférés egy Storage-fiókon keresztül történik. Ez a névtér legmagasabb szintje a Blobok eléréséhez. Egy fiók korlátlan számú tárolót tartalmazhat, feltéve, hogy a teljes méret 100 TB.
* **Tároló**: a tároló Blobok egy csoportját biztosítja. Az összes blobnak tárolóban kell lennie. Egy fiók korlátlan számú tárolót tartalmazhat. Egy tároló korlátlan számú blob tárolására használható.
* **Blob**: bármilyen típusú és méretű fájl. Az Azure Storage-ban két típusú blob tárolható: a blokk-és a Blobok. A legtöbb fájl blokkolja a blobokat. Egy blokkos blob mérete legfeljebb 200 GB lehet. Ez az oktatóanyag blokk-blobokat használ. Az oldal Blobok, egy másik blob típusa akár 1 TB méretű is lehet, és hatékonyabb, ha a fájlban lévő bájtok tartományait gyakran módosítják. A Blobokkal kapcsolatos további információkért lásd: [a Blobok, a blobok és a Blobok hozzáfűzésének ismertetése](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **URL-formátum**: a Blobok a következő URL-formátummal érhetők el:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (A fenti formátum a globális Azure-felhőre vonatkozik. Ha más Azure-felhőt használ, az URL-végpont meghatározásához használja a [Azure Portal](https://portal.azure.com) belüli végpontot.)
  
    A fenti `storageaccount` formátumban a Storage-fiók `container_name` nevét jelöli, a pedig a tároló nevét, valamint `blob_name` a blob nevét jelöli. A tároló neve alatt több elérési út is lehet, a továbbítási perjelekkel elválasztva **/**. Az oktatóanyagban szereplő példa **MyJob**volt, a közös virtuális elérési útra pedig a **$ {Build\_ID\_}/$ {Build Number}** lett használva, ami a blob URL-címét a következő formában adja meg:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>További lépések
* [A Hudson megfelel](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [Javához készült Azure Storage SDK](https://github.com/azure/azure-storage-java)
* [Azure Storage ügyféloldali SDK-referencia](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [Az Azure Storage-szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Az Azure Storage csapat blogja](https://blogs.msdn.com/b/windowsazurestorage/)

További információ: [Azure Java-fejlesztőknek](/java/azure).
