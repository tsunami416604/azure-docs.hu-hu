---
title: A Blob storage használata a Hudson |} A Microsoft Docs
description: Ismerteti, hogyan lehet használata a Hudson az Azure Blob storage a build-összetevőket.
services: storage
author: seguler
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 02/28/2017
ms.author: seguler
ms.component: common
ms.openlocfilehash: 86a14d12f6621524ef26e2a869d4be532105dc77
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45732346"
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Az Azure Storage szolgáltatás használata Hudson folyamatos integrációs megoldással
## <a name="overview"></a>Áttekintés
A következő adatokat használja a Blob storage vagy a Hudson folyamatos integrációs (CI) megoldás által létrehozott, vagy letölthető fájlok forrásként használható a létrehozási folyamat mutatja be. A forgatókönyvek, ahol Ön is hasznos ez egyik, kódolási használ (a Java vagy más nyelvek) Agilis fejlesztési környezetben, a folyamatos integrációs buildek alapján futnak, és a build-összetevőket, szüksége lesz egy adattár, hogy sikerült, például megoszthatók más szervezet tagjaival, az ügyfelek vagy karbantartása az archívumot.  Egy másik helyzet lehet, ha a fordítási feladatot magán szükséges egyéb fájlokat, például a build bemeneti részeként letöltéséhez függőségek.

Ebben az oktatóanyagban fog használni az Azure Storage beépülő modul a Microsoft által rendelkezésre bocsátott Hudson folyamatos integrációhoz.

## <a name="introduction-to-hudson"></a>Hudson bemutatása
Hudson lehetővé teszi, hogy a folyamatos integráció, a szoftver projekt azáltal, hogy fejlesztők könnyedén integrálhatják a kód módosításait, és buildek előállított automatikusan és gyakran, ezáltal a fejlesztők a termelékenység növelése. Buildek rendszerverzióval ellátott, illetve build-összetevőket is feltölthetők a különböző adattárak. Ebből a cikkből megtudhatja, a build-összetevőket a tárházat az Azure Blob storage használata. Azt is megjelennek a függőségek letöltése az Azure Blob storage-ból.

További információ a Hudson található [megfelelnek Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>A Blob szolgáltatás használatának előnyei
A Blob service használata az Agilis fejlesztési build-összetevők futtatásához előnyei a következők:

* Magas rendelkezésre állás, a build-összetevőket és/vagy letölthető függőségek.
* Teljesítmény, ha a Hudson CI-megoldás a build-összetevőket tölt fel.
* Amikor az ügyfelek és partnerek töltse le a build-összetevők teljesítményét.
* Felhasználói hozzáférési házirendeket, a névtelen hozzáférés, lejárati-alapú közös hozzáférésű jogosultságkód hozzáférést, privát választhat felett ellenőrzést hozzáférés, stb.

## <a name="prerequisites"></a>Előfeltételek
A Blob service használata a Hudson CI-megoldás a következő lesz szüksége:

* A Hudson folyamatos integrációs megoldással.
  
    Ha jelenleg nem rendelkezik a Hudson CI-megoldás, futtassa az alábbi eljárással használata Hudson CI megoldást:
  
  1. Egy Java-kompatibilis gépen töltse le a Hudson HÁBORÚ <http://hudson-ci.org/>.
  2. Egy a mappába, amely tartalmazza a Hudson WAR megnyitott parancssorban futtassa a Hudson WAR. Ha például 3.1.2 verzió letöltése:
     
      `java -jar hudson-3.1.2.war`

  3. A böngészőben nyissa meg a `http://localhost:8080/`. Ekkor megnyílik a Hudson irányítópultján.
  4. Hudson első használatakor, végezze el a kezdeti beállítás, `http://localhost:8080/`.
  5. Után végezze el a kezdeti beállítását, megszakítja a futó példányát, a Hudson WAR, indítsa újra a Hudson WAR, és nyissa meg újra a Hudson irányítópult `http://localhost:8080/`, mert ezzel fogja telepítése és konfigurálása az Azure Storage beépülő modul.
     
      Bár egy tipikus Hudson CI-megoldást szeretne úgy, hogy a szolgáltatás, a Hudson war futtatása a parancssorból futtassa a jelen oktatóanyag elegendő lesz.
* Egy Azure-fiók. Iratkozzon fel a következő Azure-fiókot <http://www.azure.com>.
* Egy Azure-tárfiók. Ha a tárfiók már nem rendelkezik, létrehozhat egyet a következő lépések használatával [hozzon létre egy Tárfiókot](../common/storage-quickstart-create-account.md).
* A Hudson CI megoldás ismeretét ajánlott, de nem szükséges, az alábbi tartalommal fogja használni egy alapszintű példa mutatni, ha a Blob service használata Hudson CI vagy a szükséges lépéseket build-összetevőket.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>A Blob szolgáltatás használata Hudson CI-vel
A Blob service használata Hudson, szüksége lesz az Azure Storage beépülő modul telepítéséhez, a storage-fiók használata a beépülő modul konfigurálásához és majd a felépítés utáni művelet, amely a build-összetevőket tölt fel a tárfiók létrehozása. A következő szakaszok ezeket a lépéseket ismerteti.

## <a name="how-to-install-the-azure-storage-plugin"></a>Az Azure Storage beépülő modul telepítése
1. Kattintson a Hudson irányítópulton **kezelése Hudson**.
2. Az a **kezelése Hudson** kattintson **beépülő modulok kezelése**.
3. Kattintson a **elérhető** fülre.
4. Kattintson a **mások**.
5. Az a **összetevő Feltöltők** szakaszban jelölje be **a Microsoft Azure Storage beépülő modul**.
6. Kattintson az **Install** (Telepítés) gombra.
7. A telepítés befejezése után indítsa újra a Hudson.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>A tárfiók használata az Azure Storage beépülő modul konfigurálása
1. Kattintson a Hudson irányítópulton **kezelése Hudson**.
2. Az a **kezelése Hudson** kattintson **rendszer konfigurálása**.
3. Az a **a Microsoft Azure Storage-fiók konfigurációja** szakaszban:
   
    a. Adja meg a tárfiók nevét, amely szerezhet be a [az Azure Portal](https://portal.azure.com).
   
    b. Adja meg a tárfiók kulcsára, is megrendelhető az [az Azure Portal](https://portal.azure.com).
   
    c. Használja az alapértelmezett értéket a **Blob Service-végpont URL-címe** használata a nyilvános Azure-felhőben. Ha egy másik Azure-felhőben használja, használja a végponthoz megadott a [az Azure Portal](https://portal.azure.com) a tárfiók számára.
   
    d. Kattintson a **tároló hitelesítő adatainak ellenőrzésére** ellenőrzése a storage-fiókjában.
   
    e. [Opcionális] Ha további tárfiókokat, amelyeket szeretne elérhetővé tett a Hudson CI, kattintson a **további tárfiókok hozzáadása**.
   
    f. Kattintson a **mentése** a beállítások mentéséhez.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>A felépítés utáni művelet, amely a build-összetevőket tölt fel a tárfiók létrehozása
Utasítás céljából először azt kell hozzon létre egy feladatot, amely több, a fájl létrehozásához, majd adja hozzá a felépítés utáni műveletben, a fájlok feltöltése a storage-fiók.

1. Kattintson a Hudson irányítópulton **új feladat**.
2. Nevezze el a feladat **MyJob**, kattintson a **hozhat létre egy ingyenes stílusú szoftverfrissítési feladat**, és kattintson a **OK**.
3. Az a **hozhat létre** szakaszban kattintson a feladat-konfiguráció **felépítési lépés hozzáadása** , és válassza **végrehajtása Windows kötegparanccsal**.
4. A **parancs**, használja a következő parancsokat:

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. Az a **felépítés utáni műveletekben** szakaszban kattintson a feladat-konfiguráció **összetevők feltöltése a Microsoft Azure Blob storage**.
6. A **Tárfióknevet**, válassza ki a használandó tárfiókot.
7. A **Tárolónév**, adja meg a tároló nevét. (A tároló fog hozható létre, ha ezt még nem létezik a build-összetevőket feltöltésekor.) Környezeti változók használata is, így ebben a példában adja meg **${JOB_NAME}** a tároló neve.
   
    **Tipp**
   
    Alább a **parancs** szkript megadásánál szakasz **végrehajtása Windows kötegparanccsal** a környezeti változók ismeri fel a Hudson mutató hivatkozás. Kattintson a hivatkozásra a környezeti változók neve és leírása. Vegye figyelembe, hogy környezeti változókat, amelyek tartalmazzák a speciális karakterek, mint például a **BUILD_URL** környezeti változót, a tároló neve vagy a közös virtuális elérési út nem engedélyezettek.
8. Kattintson a **alapértelmezés szerint az új tároló nyilvánosságra** ebben a példában. (Ha szeretne egy privát tároló használatára, szüksége való hozzáférés engedélyezése a közös hozzáférésű jogosultságkód létrehozása. Ez a cikk nem foglalkozik. További információ a közös hozzáférésű jogosultságkódok [használatával a közös hozzáférésű Jogosultságkódok (SAS)](../storage-dotnet-shared-access-signature-part-1.md).)
9. [Opcionális] Kattintson a **tiszta tároló feltöltése előtt** Ha azt szeretné, hogy a tároló tartalmát törölni kell, mielőtt feltöltésekor a rendszer a build-összetevőket (ne jelölje ezt, ha nem szeretné, hogy a tároló tartalmának törlése).
10. A **összetevők lista feltöltéséhez**, adja meg **szöveg/*.txt**.
11. A **feltöltött összetevők közös virtuális elérési út**, adja meg **${hozhat létre\_azonosító} / ${hozhat létre\_NUMBER}**.
12. Kattintson a **mentése** a beállítások mentéséhez.
13. A Hudson irányítópultján kattintson **hozhat létre most** futtatásához **MyJob**. Vizsgálja meg a konzol kimenete az állapotát. Az Azure Storage-állapotüzenetek fog szerepelni a konzol kimenete feltölteni a build-összetevőket a felépítés utáni művelet indításakor.
14. A feladat sikeres telepítést vizsgálja meg a build-összetevőket a nyilvános blob megnyitásával.
    
    a. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
    
    b. Kattintson a **tárolási**.
    
    c. Kattintson a Hudson használt tárfiók nevét.
    
    d. Kattintson a **tárolók**.
    
    e. Kattintson a tároló nevű **myjob**, amely az, hogy a feladat neve, a Hudson feladat létrehozásakor hozzárendelt kis verziója. A tároló nevének és a blob nevét is kisbetű (és a kis-és nagybetűket) az Azure Storage-ban. A tárolót a blobok listáját belül **myjob** kell megjelennie **hello.txt** és **date.txt**. Ezek az elemek egyikét másolja az URL-címet, és nyissa meg a böngészőben. Szöveges fájlt töltött fel, mint a buildösszetevőt jelenik meg.

Csak egy felépítés utáni művelet, amely feltölti az összetevők az Azure Blob storage-feladat hozható létre. Vegye figyelembe, hogy az egyetlen felépítés utáni művelet összetevők feltöltése az Azure Blob storage-adhatja meg (beleértve a helyettesítő karaktereket) a különböző fájlok és a belül fájlok elérési útjait **összetevők lista feltöltéséhez** pontosvesszővel az elválasztó használatával. Például, ha a Hudson hozhat létre és hoz létre JAR-fájlok TXT-fájlokat a munkaterületen **hozhat létre** mappát, és a feltöltése az Azure Blob storage egyaránt, használja a következőt szeretné az **összetevők lista feltöltéséhez** érték: **összeállítása /\*.jar; build /\*.txt**. Dupla kettőspont szintaxis segítségével adja meg egy elérési útját használja a blob neveként belül. Például, ha azt szeretné, hogy a JAR-fájlok kivételével az első feltöltött használatával **bináris** a blob elérési útja és a TXT-fájlokat feltölteni lekérése használatával **hirdetmények** a blob elérési útja, használja a következő a **listán az összetevők a feltöltendő** érték: **hozhat létre /\*. jar::binaries; build /\*. txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Hogyan hozhat létre, amely az Azure Blob storage-ból letöltött buildelési lépést
A következő lépések bemutatják, hogyan konfigurálhatja egy lépést, amely elemek letöltése az Azure Blob storage-ból. Ez akkor lehet hasznos, ha az elemek felvétele a build, például JAR-fájlok kivételével az Azure Blob storage-ban tartó a szeretné.

1. Az a **hozhat létre** szakaszban kattintson a feladat-konfiguráció **felépítési lépés hozzáadása** , és válassza **töltse le az Azure Blob storage-ból**.
2. A **tárfióknevet**, válassza ki a használandó tárfiókot.
3. A **Tárolónév**, adja meg a tároló, amelyben a blobokat letölteni kívánt nevét. Környezeti változókat is használhat.
4. A **blobnév**, adja meg a blob nevét. Környezeti változókat is használhat. Is használhatja egy csillagot helyettesítő karakter a kezdeti betűjét a blob nevének megadása után. Ha például **projekt\***  kellene megadnia kezdődő összes BLOB **projekt**.
5. [Opcionális] A **letöltési mappa elérési útját**, adja meg az elérési utat a Hudson számítógépen, ahol szeretné tölteni a fájlokat az Azure Blob storage-ból. A környezeti változók is használható. (Ha nem ad meg értéket **letöltési mappa elérési útját**, a feladatok munkaterület a fájlok az Azure Blob storage-ból letöltött.)

Ha további elemek szeretné tölteni az Azure Blob storage-ból, létrehozhat további létrehozási lépések.

Miután lefuttatta a build, ellenőrizze a korábbi konzol kimenete, vagy tekintse meg a letöltési helyét, megtekintheti, hogy a várt blobok letöltése sikeresen megtörtént.

## <a name="components-used-by-the-blob-service"></a>A Blob szolgáltatás által használt összetevők
A következő a Blob szolgáltatás-összetevők áttekintést nyújt.

* **Storage-fiók**: minden, az Azure Storage-hozzáférés tárfiókon keresztül történik. Ez az a névtér a blobok elérése a legmagasabb szintű. Egy fiók korlátlan számú tárolók, tartalmazhat, mindaddig, amíg azok teljes mérete 100 TB-os alatt.
* **Tároló**: tároló blobokat áll csoportosítását biztosítja. Az összes blobnak tárolóban kell lennie. Egy fiók korlátlan számú tárolót tartalmazhat. Egy tároló korlátlan számú blob tárolására használható.
* **BLOB**: bármilyen típusú és méretű fájl. A blobok Azure Storage-ban tárolt két típusa van: blokkblobok és lapblobok. Fájlok a legtöbb esetben blokkblobok. Egyetlen blokkblob akár 200 GB méretű lehet. Ebben az oktatóanyagban a blokkblobok használatát támogatják. Lapblobok és a egy másik blob típusú tárfiók, akár 1 TB-ig a mérete, és kiváló hatékonyabb lehet, ha egy fájlban bájtos címtartományok gyakran módosítják. Blobok kapcsolatos további információkért lásd: [Understanding Block Blobs, hozzáfűző blobokat és Lapblobokat](http://msdn.microsoft.com/library/azure/ee691964.aspx).
* **URL-formátum**: Blobok legyenek a következő URL-formátummal:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (A fenti formátum a nyilvános Azure felhő vonatkozik. Egy másik Azure-felhőben használja, ha a végpont belül használja a [az Azure Portal](https://portal.azure.com) meghatározni az URL-végpontot.)
  
    A fenti formátumban `storageaccount` pedig a tárfiók nevét `container_name` a tároló nevét jelöli, és `blob_name` rendre a blob nevét jelöli. A tároló nevének belül használhat több elérési útnak perjellel elválasztva **/**. A példa tároló neve ebben az oktatóanyagban **MyJob**, és **${hozhat létre\_azonosító} / ${hozhat létre\_szám}** használták a közös virtuális elérési utat, a blob URL-Címének kellene eredményez a a következő képernyőn:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>További lépések
* [Hudson felel meg](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [Az Azure Storage-hoz készült Java SDK](https://github.com/azure/azure-storage-java)
* [Azure Storage ügyféloldali SDK-referencia](http://dl.windowsazure.com/storage/javadoc/)
* [Az Azure Storage-szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Az Azure Storage csapat blogja](http://blogs.msdn.com/b/windowsazurestorage/)

További információ: [Azure Java-fejlesztőknek](/java/azure).