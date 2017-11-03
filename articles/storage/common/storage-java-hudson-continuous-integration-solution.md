---
title: "A Blob storage Hudson használata |} Microsoft Docs"
description: "Ismerteti, hogyan használandó Hudson az Azure Blob storage szolgáltatással a tárházat az összeállítási összetevők."
services: storage
documentationcenter: java
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: 119becdd-72c4-4ade-a439-070233c1e1ac
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 02/28/2017
ms.author: seguler
ms.openlocfilehash: e54bedff5f744004288e132efbed8c3e7981f8a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Az Azure Storage szolgáltatás használata Hudson folyamatos integrációs megoldással
## <a name="overview"></a>Áttekintés
Az alábbi információk segítségével a Blob storage Hudson folyamatos integráció (CI) megoldás által létrehozott build műtermék tára, vagy letölthető fájlok forrásaként használható a létrehozási folyamat mutatja be. Egy Ha akkor is hasznos ez a forgatókönyv akkor, ha Ön most kódolási egy gyors fejlesztési környezetben (Java vagy más nyelvek) buildek futtat alapú folyamatos integrációt és van szüksége a tárházat a build összetevők, hogy sikerült, például más szervezet tagjaival, az ügyfelek megosztására vagy archiválhatja karbantartása.  Egy másik helyzet lehet, ha az összeállítási feladat maga szükséges egyéb fájlokat, például a függőségeket a bemeneti build részeként tölthető le.

Ebben az oktatóanyagban fog használni az Azure Storage beépülő modul a Microsoft által elérhetővé tett Hudson konfigurációelemhez.

## <a name="introduction-to-hudson"></a>Hudson bemutatása
Hudson lehetővé teszi, hogy a szoftver projekt folyamatos integrációt azáltal, hogy a fejlesztők könnyedén integrálható a kód módosítására, és buildek előállított automatikusan és gyakran, ezáltal termelékenységének a fejlesztők számára. Buildek verzióval ellátva, és build összetevők is feltölthetők a különböző tárházak találhatók. Ez a cikk ismerteti, hogyan Azure Blob storage használata a build műtermék tárházaként. Azt is megjelennek a függőségek letöltése Azure Blob Storage tárolóban.

További információ a Hudson található [megfelelnek Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>A Blob szolgáltatás használatának előnyei
A gyors fejlesztés build összetevők futtatására a Blob szolgáltatás használatának előnyei a következők:

* Magas rendelkezésre állás a build összetevők és/vagy letölthető függőségek.
* Ha a Hudson CI megoldás feltölti a build összetevők teljesítményét.
* Ha az ügyfelek és partnerek töltse le a build összetevők teljesítményét.
* Felhasználói hozzáférési házirendeket, a névtelen hozzáférés, lejárati-alapú megosztott hozzáférési aláírást hozzáférés, saját közötti választás felett ellenőrzést hozzáférés, stb.

## <a name="prerequisites"></a>Előfeltételek
A Hudson CI-megoldás a Blob szolgáltatás használatához a következőkre lesz szüksége:

* A folyamatos integrációs Hudson megoldás.
  
    Jelenleg nem rendelkezik olyan Hudson CI megoldást, ha egy Hudson CI megoldást a következő eljárás segítségével is futtathatja:
  
  1. A Java-kompatibilis gépen töltse le a Hudson WAR a <http://hudson-ci.org/>.
  2. A parancssorba, amely meg van nyitva a Hudson WAR, futtassa a Hudson WAR tartalmazó mappát. Ha például verzió 3.1.2 letöltött:
     
      `java -jar hudson-3.1.2.war`

  3. A böngészőben nyissa meg a `http://localhost:8080/`. Ekkor megnyílik a Hudson irányítópult.
  4. Első használata Hudson, végezze el a kezdeti beállítás `http://localhost:8080/`.
  5. A kezdeti beállítás, szakítsa meg a Hudson WAR futó példányát, indítsa újra a Hudson WAR, és után nyissa meg újra a Hudson irányítópult `http://localhost:8080/`, amelyek telepítése és konfigurálása az Azure Storage beépülő modult használhatja.
     
      Amíg egy tipikus Hudson CI megoldás értékre van beállítva szolgáltatásként, a Hudson war fut a parancssorból futtassa a jelen oktatóanyag elegendő lesz.
* Egy Azure-fiók. Azure-fiókot, regisztrálhat <http://www.azure.com>.
* Egy Azure-tárfiók. Ha még nem rendelkezik egy tárfiókot, létrehozhat egyet a következő lépések [hozzon létre egy Tárfiókot](../common/storage-create-storage-account.md#create-a-storage-account).
* A tesztkörnyezet Hudson CI-megoldással ajánlott, de nem szükséges, a következő tartalmat használja egy egyszerű példa, a lépéseken, amikor a Blob szolgáltatás használata a tárház Hudson CI build összetevők.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>A Blob szolgáltatás használata Hudson CI
A Blob szolgáltatás használata Hudson, lesz szüksége az Azure Storage beépülő modul telepítését, a beépülő modul a tárfiók használatára konfigurálja, és hozzon létre egy utáni műveletet, amely a build összetevők feltölti a tárfiókhoz. A következő szakaszok ismertetik ezeket a lépéseket.

## <a name="how-to-install-the-azure-storage-plugin"></a>Az Azure Storage beépülő modul telepítése
1. Belül Hudson irányítópulton kattintson **kezelése Hudson**.
2. Az a **kezelése Hudson** kattintson **kezelése beépülő modulok**.
3. Kattintson a **elérhető** fülre.
4. Kattintson a **mások**.
5. Az a **összetevő Uploaders** szakaszban jelölje be **Microsoft Azure Storage beépülő modul**.
6. Kattintson az **Install** (Telepítés) gombra.
7. A telepítés befejezése után indítsa újra a Hudson.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>A tárfiók használatára az Azure Storage beépülő modul konfigurálása
1. Belül Hudson irányítópulton kattintson **kezelése Hudson**.
2. Az a **kezelése Hudson** kattintson **rendszer konfigurálása**.
3. Az a **Microsoft Azure Storage-fiók konfigurációja** szakasz:
   
    a. Adja meg a tárfiók neve, amely szerezhet be a [Azure Portal](https://portal.azure.com).
   
    b. Adja meg a tárfiók kulcsára, történő is szerezhető a [Azure Portal](https://portal.azure.com).
   
    c. Használja az alapértelmezett értéket a **Blob-Szolgáltatásvégpont URL-** használata a nyilvános Azure felhőbe. Ha egy másik Azure felhőbe használja, használja a végpontot a a [Azure Portal](https://portal.azure.com) a tárfiók.
   
    d. Kattintson a **tárolási fiók hitelesítő adatainak érvényesítéséhez** a tárfiók érvényesítése.
   
    e. [Választható] Ha további tárfiókok kívánt érhetik el a Hudson CI, kattintson a **adja hozzá a további tárfiókok**.
   
    f. Kattintson a **mentése** a beállítások mentéséhez.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Egy utáni műveletet, amely a build összetevők feltölti a tárfiók létrehozása
Utasítás célokra először azt kell hozzon létre egy feladatot, amely több-fájlok létrehozása, majd adja hozzá a tárfiókhoz a fájlok feltöltéséhez utáni műveletnél.

1. Belül Hudson irányítópulton kattintson **új feladat**.
2. A feladat neve **MyJob**, kattintson a **egy ingyenes stílusú szoftver feladat létrehozása**, és kattintson a **OK**.
3. A a **Build** szakasz a feladat-konfiguráció, kattintson a **Hozzáadás összeállítása lépés** válassza **hajtható végre Windows kötegelt parancs**.
4. A **parancs**, az alábbi parancsokat használja:

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. Az a **utáni műveletek** szakaszban kattintson a feladat-konfiguráció **összetevők feltöltése a Microsoft Azure Blob storage**.
6. A **Tárfióknév**, válassza ki a tárfiókot használni.
7. A **Tárolónév**, adja meg a tároló nevét. (A tároló jön létre, ha még nem létezik a build összetevők feltöltése során.) Környezeti változók használata, így ehhez a példához írja be **${JOB_NAME}** , a tároló neve.
   
    **Tipp**
   
    Alább a **parancs** egy parancsfájlt megadásánál szakasz **hajtható végre Windows kötegelt parancs** a környezeti változók ismeri fel Hudson mutató hivatkozás. Kattintson a hivatkozásra a környezeti változó neve és leírása. Vegye figyelembe, hogy környezeti változókat, amelyek tartalmazhat különleges karaktereket, például a **BUILD_URL** környezeti változó, a tároló neve vagy a közös virtuális elérési út nem tartalmazhat.
8. Kattintson a **új tároló alapértelmezés szerint nyilvánosságra** ehhez a példához. (Ha egy személyes tárolót használni kívánt, szüksége lesz egy közös hozzáférésű jogosultságkód hozzáférést létrehozásához. Amely már nem tartozik ez a cikk. A megosztott hozzáférési aláírásokkal kapcsolatos részletesebb [használatával megosztott hozzáférési aláírásokkal (SAS)](../storage-dotnet-shared-access-signature-part-1.md).)
9. [Választható] Kattintson a **feltöltés előtt tiszta tároló** Ha azt szeretné, hogy a tároló tartalmának törölni kell, mielőtt build összetevők feltöltése (hagyja bejelölve Ha nem szeretné, hogy a tároló tartalmának törlése).
10. A **összetevők lista feltöltéséhez**, adja meg  **szöveg /*.txt**.
11. A **közös virtuális elérési utat feltöltött összetevők**, adja meg **${BUILD\_azonosító} / ${BUILD\_szám}**.
12. Kattintson a **mentése** a beállítások mentéséhez.
13. Hudson irányítópultján kattintson **Build most** futtatásához **MyJob**. Vizsgálja meg a konzol kimeneti az állapotot. Az Azure Storage állapotüzenetek szerepelni fog a konzol kimeneti build összetevők feltölteni a létrehozás után végrehajtandó művelet indításakor.
14. A feladat sikeres befejezését követően a build összetevők a nyilvános blob megnyitásával ellenőrizheti.
    
    a. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
    
    b. Kattintson a **tárolási**.
    
    c. Kattintson a tárfiók nevét, amelyet Hudson használt.
    
    d. Kattintson a **tárolók**.
    
    e. Kattintson a tároló **myjob**, amely a feladat nevét, amely a Hudson feladat létrehozásakor rendelt kis verziója telepítve. A tároló nevének és a blob nevének, kisbetű (és kis-és nagybetűket) az Azure Storage. Blobok nevű tároló listáján **myjob** kell megjelennie **hello.txt** és **date.txt**. Az URL-Címének másolása ezek az elemek egyikét, majd nyissa meg a böngészőben. A build összetevő, látni fogja a feltöltött szövegfájl.

Az összetevők feltölti az Azure Blob storage csak egy utáni végrehajtandó feladat / hozhatók létre. Vegye figyelembe, hogy a egyetlen utáni műveletet az összetevők feltöltése az Azure Blob storage megadhat különböző fájlok (beleértve a helyettesítő karaktereket) és a megadott elérési utakon található fájl **összetevők lista feltöltéséhez** pontosvesszővel kell elválasztani őket egy használatával. Például ha a Hudson előállító JAR és TXT-fájlok a munkaterületen **build** mappa, és szeretne feltölteni mindkettőt Azure Blob Storage, használja a következő a **összetevők lista feltöltéséhez** érték: **build /\*.jar; build /\*.txt**. Dupla kettőspont szintaxis segítségével adjon meg egy elérési utat a blob nevének belül. Például, ha azt szeretné, hogy a JAR-fájlok kivételével az beszerzése feltöltött használatával **bináris** a blob elérési út és a TXT-fájlok használatával beolvasása feltöltött **értesítéseket** a blob elérési út a következőt használja a **összetevők lista feltöltéséhez** érték: **build /\*. jar::binaries; build /\*. txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>A build lépést, amely letölti a Azure Blob-tároló létrehozása
A következő lépések bemutatják egy build lépéssel töltheti le a cikkek az Azure Blob storage konfigurálása. Ez akkor hasznos, ha az elemek szerepeljenek a build, például JAR-fájlok kivételével, amely azt az Azure Blob Storage tárolóban tárolni szeretné.

1. A a **Build** szakaszban kattintson a feladat-konfiguráció **Hozzáadás összeállítása lépés** , és válassza **töltse le az Azure Blob storage**.
2. A **tárfióknév**, válassza ki a tárfiókot használni.
3. A **Tárolónév**, adja meg, amely rendelkezik a blobok szeretné letölteni a tároló nevét. Környezeti változókat is használhat.
4. A **Blob neve**, adja meg a blob neve. Környezeti változókat is használhat. Is használhatja a csillag karakter, a kezdeti betűjét vagy a blob nevének megadása után helyettesítő karakter. Például **projekt\***  kellene megadnia kezdődő összes BLOB **projekt**.
5. [Választható] A **letöltési mappa elérési útját**, az Azure Blob storage fájlok letöltésére ahová Hudson gépen elérési útjának megadása. Környezeti változók is használható. (Ha nem ad meg értéket **letöltési mappa elérési útját**, a feladatok munkaterületet letölti a fájlokat az Azure Blob-tárolóból.)

Ha le szeretné tölteni az Azure Blob storage további elemek, build további lépéseket is létrehozhat.

Miután lefuttatta a build, ellenőrizheti a build előzmények konzol kimeneti, vagy nézze meg a letöltési hely megtekintéséhez, hogy a várt blobok letöltése sikeresen megtörtént.

## <a name="components-used-by-the-blob-service"></a>A Blob szolgáltatás által használt összetevők
A következő áttekintést a Blob szolgáltatás-összetevők.

* **A tárfiók**: Azure Storage minden hozzáférés a storage-fiók segítségével történik. Ez a legmagasabb szintű névterét a blobok elérése. Egy fiók tartalmazhat egy korlátlan számú tárolót, mindaddig, amíg a teljes mérete 100 TB alatt.
* **Tároló**: A tárolók blobok blobkészletek csoportosítását biztosítja. Az összes blobnak tárolóban kell lennie. Egy fiók korlátlan számú tárolót tartalmazhat. Egy tároló korlátlan számú blob tárolására használható.
* **A BLOB**: bármilyen típusú és bármekkora méretű fájl. Az Azure Storage tárolható blobok két típusa van: blokkblobok és lapblobok. A legtöbb fájlok blokk blobokat. Egyetlen blokkblob legfeljebb 200 GB méretű lehet. Ez az oktatóanyag a blokkblobokhoz használja. Lapblobokat, egy másik blob típushoz, legfeljebb 1 TB méretű, és a hatékonyabb lehet, ha egy fájlban bájt tartományok gyakran módosítják. Blobok kapcsolatos további információkért lásd: [ismertetése Blokkblobokat, hozzáfűző blobokat és Lapblobokat](http://msdn.microsoft.com/library/azure/ee691964.aspx).
* **URL-formátum**: Blobok olyan a következő URL-formátummal:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (A fenti formátum a nyilvános Azure felhőben vonatkozik. Ha egy másik Azure felhőbe használ, használja a végpont belül a [Azure Portal](https://portal.azure.com) az URL-végpontjának meghatározására.)
  
    A fenti formátumban `storageaccount` a tárfiók nevét jelöli `container_name` jelenti. a tároló neve és `blob_name` rendre a blob nevét jelöli. A tároló neve belül, akkor is több elérési út, szóközzel elválasztva perjellel,  **/** . Ebben az oktatóanyagban példa tároló neve **MyJob**, és **${BUILD\_azonosító} / ${BUILD\_szám}** lett a közös virtuális elérési útja, ami azt eredményezi, hogy a blob, hogy egy URL-cím a következő:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Következő lépések
* [Hudson felel meg](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [Az Azure Storage Java SDK](https://github.com/azure/azure-storage-java)
* [Az Azure Storage ügyfél SDK-dokumentáció](http://dl.windowsazure.com/storage/javadoc/)
* [Az Azure Storage-szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Az Azure Storage csapat blogja](http://blogs.msdn.com/b/windowsazurestorage/)

További információ: [Azure Java-fejlesztőknek](/java/azure).