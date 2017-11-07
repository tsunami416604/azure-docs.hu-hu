---
title: "A Microsoft Azure Tártallózó (előzetes verzió) kibocsátási megjegyzései |} Microsoft Docs"
description: "Kibocsátási megjegyzések a Microsoft Azure Tártallózó (előzetes verzió)"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2017
ms.author: cawa
ms.openlocfilehash: cf077fef6df2fd21cf51f6b4fd4e26a4b5081247
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="microsoft-azure-storage-explorer-preview-release-notes"></a>Kibocsátási megjegyzések a Microsoft Azure Tártallózó (előzetes verzió)

A cikkben a kibocsátási megjegyzések a 0.9.2. Azure Tártallózó (előzetes verzió) kiadása, valamint a kibocsátási megjegyzések a korábbi verziók.

[A Microsoft Azure Tártallózó (előzetes verzió)](./vs-azure-tools-storage-manage-with-storage-explorer.md) egy különálló alkalmazás, amelynek segítségével egyszerűen dolgozhat Azure Storage-adatokkal Windows, a macOS és a Linux rendszeren.

## <a name="version-092"></a>0.9.2 verzió
11/01/2017

### <a name="download-azure-storage-explorer-092-preview"></a>Töltse le az Azure Tártallózó (előzetes verzió) 0.9.2
- [A Windows Azure Tártallózó (előzetes verzió) 0.9.2](https://go.microsoft.com/fwlink/?LinkId=708343)
- [A Mac Azure Tártallózó (előzetes verzió) 0.9.2](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Linux rendszerhez készült Azure Tártallózó (előzetes verzió) 0.9.2](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="hotfixes"></a>Gyorsjavítások
* Váratlan adatok módosítása volt lehetséges, attól függően, hogy a helyi időzónát táblaentitásokat Edm.DateTime értékeinek szerkesztése során. A szerkesztő mostantól használ a egyszerű szövegmező Edm.DateTime értékek pontos, egységes ad.
* Egy csoport nevét és a kulcs csatolt blobok feltöltése/letöltése nem indul el. A probléma javítását.
* Korábban Tártallózó csak felajánlja újból hitelesítésre a régi fiókot, ha egy vagy több a fiók-előfizetések választotta. Most Tártallózó kérni fogja, még akkor is, ha a fiók teljesen ki van szűrve.
* A végpontok Azure Amerikai Egyesült államokbeli kormányzati tartomány hibás volt. Rögzítettek.
* Az Alkalmaz gombra a fiókok kezelése panelen lett néha nehéz kattintson. Ez már nem megtörténik.

### <a name="new"></a>új
* Azure Cosmos DB Preview támogatása:
    * [Online dokumentáció](./cosmos-db/tutorial-documentdb-and-mongodb-in-storage-explorer.md)
    * Adatbázisok és a gyűjtemények létrehozása
    * Adatok kezelése
    * Lekérdezési, hozzon létre vagy dokumentumok törlése
    * A tárolt eljárások, felhasználó által megadott függvények és eseményindítók frissítése
    * Kapcsolati karakterláncok használatával csatlakozhat, és az adatbázisok kezelése
* Növeli a sok kisméretű blobok feltöltése/letöltés a teljesítményt.
* Bekerül egy "Próbálja meg újra All" művelet, ha egy blob feltöltése vagy blob letöltési csoport hibái.
* A Tártallózó most felfüggeszti iterációs blob feltöltése/letöltése során, ha azt észleli, a hálózati kapcsolat megszakadt. Ezután folytathatja a iterációs követően a hálózati kapcsolat helyreáll.
* Mostantól lehetőség van a "Bezárás All", "Bezárás mások" és a "Bezárás" lapon a helyi menü segítségével.
* Most már a Tártallózó natív párbeszédpanelek és natív helyi menük.
* Tártallózó alkalmazással akadálymentesített áll. Fejlesztések a következők:
    * Továbbfejlesztett képernyő olvasó, NVDA Windows rendszeren, és a támogatása VoiceOver Mac gépen
    * Továbbfejlesztett kontrasztos témák használatát
    * Kijavítja a bejárási és billentyűzet billentyűzetfókusz

### <a name="fixes"></a>Javítások
* Ha próbált megnyitni, vagy letöltheti egy blobot a Windows fájl neve érvénytelen, a művelet sikertelen lesz. A Tártallózó most ellenőrzés, hogy a blob neve érvénytelen, és kérje meg, ha szeretné kódolása, vagy hagyja ki a blob. A Tártallózó is észlelni fogja, ha a fájl nevét úgy tűnik, hogy kódolhatók, és megkérdezi, hogy ha kívánja dekódolni a feltöltés előtt.
* Blob feltöltése közben a cél blob tároló szerkesztője néha nem megfelelően frissíti. A probléma javítását.
* Több űrlap kapcsolati karakterláncok és a SAS URI-k támogatása közleményében szerepelt. Rendszer foglalkoztak minden ismert problémákat, de küldjön visszajelzést, ha hibát tapasztal további.
* A frissítési értesítés 0.9.0-s a felhasználók megszakadt. Ezt a problémát, és azok a hiba érinti, manuálisan töltse le a legújabb verzióját a Tártallózó [Itt](https://azure.microsoft.com/en-us/features/storage-explorer/).

### <a name="known-issues"></a>Ismert problémák
* A Tártallózó nem támogatja az AD FS-fiókokat.
* Billentyűparancsok a "Nézet Explorer" és "View Fiókkezelés" kell lennie a Ctrl vagy Cmd + Shift + E, és a Ctrl vagy Cmd + Shift + A kulcsattribútumokkal.
* Ha Azure verem célzó, bizonyos fájlokat, hozzáfűző blobok feltöltése meghiúsulhat.
* Után a "Mégse gombra" kattintva meg olyan feladatra, ez eltarthat egy kis ideig, megszakítja a tevékenység. Ennek oka az itt leírt Mégse szűrő megoldást használunk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd akkor kell biztosítani, hogy a döntési elfelejti Tártallózó újraindítására.
* A fiók beállítások panel jelenhet meg, hogy meg kell adnia a hitelesítő adatok előfizetések szűrése.
* Blobok (külön-külön vagy átnevezett blob tárolóhoz belül) átnevezése nem őrzi meg a pillanatképeket. Minden más tulajdonságok és metaadatok BLOB-, fájl-és entitások egy átnevezési megőriz.
* Bár az Azure-verem jelenleg nem támogatja a fájlmegosztásokat, fájlmegosztások csomópont továbbra is egy csatolt verem Azure storage-fiók alatt jelenik meg.
* A Tártallózó által használt elektronsugár rendszerhéj problémák léptek fel néhány (grafikus processzorral) GPU-hardveres gyorsítás rendelkezik. A Tártallózó (üres) üres fő ablak megjelenítése, ha megpróbálhatja Tártallózó indítása a parancssorból, és a GPU gyorsítás letiltása hozzáadásával a `--disable-gpu` váltani:
```
./StorageExplorer.exe --disable-gpu
```
* Ubuntu 14.04 felhasználójához, meg kell győződnie arról ÖET folyamatosan naprakész adatokat tartalmazzon – ezt megteheti a következő parancsok futtatásával, és indítsa újra a számítógépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a számítógépet erre:

    ```
    sudo apt-get install libgconf-2-4
    ```



## <a name="version-091--090-preview"></a>Verzió 0.9.1 / 0.9.0-s (előzetes verzió)
10/20/2017
### <a name="download-azure-storage-explorer-091-preview"></a>Töltse le az Azure Tártallózó (előzetes verzió) 0.9.1
* [Töltse le a Windows Azure Tártallózó (előzetes verzió) 0.9.1](https://go.microsoft.com/fwlink/?LinkId=809306)
* [Töltse le a Mac Azure Tártallózó (előzetes verzió) 0.9.1](https://go.microsoft.com/fwlink/?LinkId=809307)
* [Linux rendszerhez készült Azure Tártallózó (előzetes verzió) 0.9.1 letöltése](https://go.microsoft.com/fwlink/?LinkId=809308)

### <a name="new"></a>új
* Azure Cosmos DB Preview támogatása:
    * [Online dokumentáció](./cosmos-db/tutorial-documentdb-and-mongodb-in-storage-explorer.md)
    * Adatbázisok és a gyűjtemények létrehozása
    * Adatok kezelése
    * Lekérdezési, hozzon létre vagy dokumentumok törlése
    * A tárolt eljárások, felhasználó által megadott függvények és eseményindítók frissítése
    * Kapcsolati karakterláncok használatával csatlakozhat, és az adatbázisok kezelése
* Növeli a sok kisméretű blobok feltöltése/letöltés a teljesítményt.
* Bekerül egy "Próbálja meg újra All" művelet, ha egy blob feltöltése vagy blob letöltési csoport hibái.
* A Tártallózó most felfüggeszti iterációs blob feltöltése/letöltése során, ha azt észleli, a hálózati kapcsolat megszakadt. Ezután folytathatja a iterációs követően a hálózati kapcsolat helyreáll.
* Mostantól lehetőség van a "Bezárás All", "Bezárás mások" és a "Bezárás" lapon a helyi menü segítségével.
* Most már a Tártallózó natív párbeszédpanelek és natív helyi menük.
* Tártallózó alkalmazással akadálymentesített áll. Fejlesztések a következők:
    * Továbbfejlesztett képernyő olvasó, NVDA Windows rendszeren, és a támogatása VoiceOver Mac gépen
    * Továbbfejlesztett kontrasztos témák használatát
    * Kijavítja a bejárási és billentyűzet billentyűzetfókusz

### <a name="fixes"></a>Javítások
* Ha próbált megnyitni, vagy letöltheti egy blobot a Windows fájl neve érvénytelen, a művelet sikertelen lesz. A Tártallózó most ellenőrzés, hogy a blob neve érvénytelen, és kérje meg, ha szeretné kódolása, vagy hagyja ki a blob. A Tártallózó is észlelni fogja, ha a fájl nevét úgy tűnik, hogy kódolhatók, és megkérdezi, hogy ha kívánja dekódolni a feltöltés előtt.
* Blob feltöltése közben a cél blob tároló szerkesztője néha nem megfelelően frissíti. A probléma javítását.
* Több űrlap kapcsolati karakterláncok és a SAS URI-k támogatása közleményében szerepelt. Rendszer foglalkoztak minden ismert problémákat, de küldjön visszajelzést, ha hibát tapasztal további.
* A frissítési értesítés 0.9.0-s a felhasználók megszakadt. Ezt a problémát, és azok a hiba érinti, manuálisan töltse le a legújabb verzióját a Tártallózó [Itt](https://azure.microsoft.com/en-us/features/storage-explorer/)

### <a name="known-issues"></a>Ismert problémák
* A Tártallózó nem támogatja az AD FS-fiókokat.
* Billentyűparancsok a "Nézet Explorer" és "View Fiókkezelés" kell lennie a Ctrl vagy Cmd + Shift + E, és a Ctrl vagy Cmd + Shift + A kulcsattribútumokkal.
* Ha Azure verem célzó, bizonyos fájlokat, hozzáfűző blobok feltöltése meghiúsulhat.
* Után a "Mégse gombra" kattintva meg olyan feladatra, ez eltarthat egy kis ideig, megszakítja a tevékenység. Ennek oka az itt leírt Mégse szűrő megoldást használunk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd akkor kell biztosítani, hogy a döntési elfelejti Tártallózó újraindítására.
* A fiók beállítások panel jelenhet meg, hogy meg kell adnia a hitelesítő adatok előfizetések szűrése.
* Blobok (külön-külön vagy átnevezett blob tárolóhoz belül) átnevezése nem őrzi meg a pillanatképeket. Minden más tulajdonságok és metaadatok BLOB-, fájl-és entitások egy átnevezési megőriz.
* Bár az Azure-verem jelenleg nem támogatja a fájlmegosztásokat, fájlmegosztások csomópont továbbra is egy csatolt verem Azure storage-fiók alatt jelenik meg.
* A Tártallózó által használt elektronsugár rendszerhéj problémák léptek fel néhány (grafikus processzorral) GPU-hardveres gyorsítás rendelkezik. A Tártallózó (üres) üres fő ablak megjelenítése, ha megpróbálhatja Tártallózó indítása a parancssorból, és a GPU gyorsítás letiltása hozzáadásával a `--disable-gpu` váltani:
```
./StorageExplorer.exe --disable-gpu
```
* Ubuntu 14.04 felhasználójához, meg kell győződnie arról ÖET folyamatosan naprakész adatokat tartalmazzon – ezt megteheti a következő parancsok futtatásával, és indítsa újra a számítógépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a számítógépet erre:

    ```
    sudo apt-get install libgconf-2-4
    ```



## <a name="previous-releases"></a>Korábbi kiadások

* [0.8.16 verzió](#version-0816)
* [0.8.14 verzió](#version-0814)
* [0.8.13 verzió](#version-0813)
* [Verzió 0.8.12 / 0.8.11 / 0.8.10](#version-0812--0811--0810)
* [Verzió 0.8.9 / 0.8.8](#version-089--088)
* [0.8.7 verzió](#version-087)
* [0.8.6 verzió](#version-086)
* [0.8.5 verzió](#version-085)
* [0.8.4 verzió](#version-084)
* [0.8.3 verzió](#version-083)
* [0.8.2 verzió](#version-082)
* [0.8.0 verzió](#version-080)
* [0.7.20160509.0 verzió](#version-07201605090)
* [0.7.20160325.0 verzió](#version-07201603250)
* [0.7.20160129.1 verzió](#version-07201601291)
* [0.7.20160105.0 verzió](#version-07201601050)
* [0.7.20151116.0 verzió](#version-07201511160)

## <a name="version-0816"></a>0.8.16 verzió
8/21/2017

### <a name="new"></a>új
* Amikor megnyit egy blobot, Tártallózó kérni fogja a letöltött fájl feltöltéséhez, ha egy változást észlel
* Azure verem bejelentkezési élmény
* Feltöltése/sok kis fájlok letöltésével egy időben teljesítményének növelése


### <a name="fixes"></a>Javítások
* Néhány blob-típusok válassza a "cseréje" ütközést okozott feltöltés során néha eredményezne újraindítja a feltöltést.
* Verzióban 0.8.15 a feltöltések volna néha megrekedésének 99 %.
* Fájlok feltöltése egy fájlmegosztásba mentheti, ha úgy döntött, hogy egy könyvtárat, amely nem történt meg feltölteni, még létezik, amikor a feltöltés sikertelen lesz.
* Tártallózó helytelenül lett időbélyegeket közös hozzáférésű jogosultságkód valamint tábla létrehozásához.


### <a name="known-issues"></a>Ismert problémák
* Név és kulcs kapcsolati karakterlánc nem működik. A következő kiadás rögzítettek. Addig is használhat nevével és a kulcs csatolása.
* Nyisson meg egy fájlt egy Windows érvénytelen fájlnév próbál, ha a letöltés egy fájl nem található az hiba eredményez.
* Után a "Mégse gombra" kattintva meg olyan feladatra, ez eltarthat egy kis ideig, megszakítja a tevékenység. Ez az az Azure tárolás csomópont Library korlátozása.
* Egy blob feltöltése befejezése után a fülre, amely a feltöltés kezdeményezett frissül. Módosult az előző viselkedését, és is okozhatja, hogy a tároló, a legfelső szintű visszakerül.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd akkor kell biztosítani, hogy a döntési elfelejti Tártallózó újraindítására.
* A fiók beállítások panel jelenhet meg, hogy meg kell adnia a hitelesítő adatok előfizetések szűrése.
* Blobok (külön-külön vagy átnevezett blob tárolóhoz belül) átnevezése nem őrzi meg a pillanatképeket. Minden más tulajdonságok és metaadatok BLOB-, fájl-és entitások egy átnevezési megőriz.
* Bár az Azure-verem jelenleg nem támogatja a fájlmegosztásokat, fájlmegosztások csomópont továbbra is egy csatolt verem Azure storage-fiók alatt jelenik meg.
* Ubuntu 14.04 felhasználójához, meg kell győződnie arról ÖET folyamatosan naprakész adatokat tartalmazzon – ezt megteheti a következő parancsok futtatásával, és indítsa újra a számítógépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a számítógépet erre:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>0.8.14 verzió
06/22/2017

### <a name="new"></a>új

* 1.7.2 több fontos biztonsági frissítést kihasználása érdekében frissített elektronsugár verzióra
* Most már elérheti az online hibaelhárítási útmutató a Súgó menü
* A Tártallózó hibaelhárítási [útmutató][2]
* [Útmutatás] [ 3] a verem Azure-előfizetéshez csatlakozó

### <a name="known-issues"></a>Ismert problémák

* A mappa törlése megerősítő párbeszédpanelen levő nem regisztrálja a kattintások Linux rendszeren. Megkerülő megoldás, hogy használja az Enter billentyűt
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványának akkor szüksége lesz ahhoz, hogy a Tártallózó alkalmazással elfelejti újraindítása
* Mivel a fájlok feltöltése egy időben és blobokat több mint 3 csoport hibákat okozhat
* A fiók beállítások panel jelenhet meg, hogy meg kell adnia hitelesítő adatait ahhoz, hogy az előfizetések szűrése
* Blobok (külön-külön vagy átnevezett blob tárolóhoz belül) átnevezése nem őrzi meg a pillanatképeket. Minden más tulajdonságok és metaadatok BLOB-, fájl-és entitások egy átnevezési megőriz.
* Bár az Azure-verem jelenleg nem támogatja a fájlmegosztások, fájlmegosztások csomópont továbbra is egy csatolt verem Azure storage-fiók alatt jelenik meg.
* Ubuntu 14.04 telepítés kell ÖET verziójához vagy frissítése – frissítésének lépései alatt van:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>0.8.13 verzió
05/12/2017

#### <a name="new"></a>új

* A Tártallózó hibaelhárítási [útmutató][2]
* [Útmutatás] [ 3] a verem Azure-előfizetéshez csatlakozó

#### <a name="fixes"></a>Javítások

* Rögzített: A fájl feltöltése volna, amely egy kevés a memória, nagy valószínűséggel
* Rögzített: Most már bejelentkezhet PIN-kód/intelligens kártya
* Rögzített: Nyissa meg a portál most works Azure Kína, a Németországi Azure, a Azure Amerikai Egyesült államokbeli kormányzati és az Azure verem
* Rögzített: Egy mappát egy blob-tárolóba feltöltésekor "Szabálytalan műveletet" hiba néha lépne
* Rögzített: Az összes le lett tiltva, a pillanatképek felügyelete során
* Rögzített: Az alap blob metaadatait előfordulhat, hogy első felül után a pillanatképek tulajdonságainak megtekintése

#### <a name="known-issues"></a>Ismert problémák

* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványának akkor szüksége lesz ahhoz, hogy a Tártallózó alkalmazással elfelejti újraindítása
* Bejövő vagy kimenő nagyított, a nagyítási szint rövid ideig lehetséges, hogy alaphelyzetbe az alapértelmezett szint
* Mivel a fájlok feltöltése egy időben és blobokat több mint 3 csoport hibákat okozhat
* A fiók beállítások panel jelenhet meg, hogy meg kell adnia hitelesítő adatait ahhoz, hogy az előfizetések szűrése
* Blobok (külön-külön vagy átnevezett blob tárolóhoz belül) átnevezése nem őrzi meg a pillanatképeket. Minden más tulajdonságok és metaadatok BLOB-, fájl-és entitások egy átnevezési megőriz.
* Bár az Azure-verem jelenleg nem támogatja a fájlmegosztásokat, fájlmegosztások csomópont továbbra is egy csatolt verem Azure storage-fiók alatt jelenik meg.
* Ubuntu 14.04 telepítés kell ÖET verziójához vagy frissítése – frissítésének lépései alatt van:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812--0811--0810"></a>Verzió 0.8.12 / 0.8.11 / 0.8.10
04/07/2017

#### <a name="new"></a>új

* A Tártallózó automatikusan leáll a frissítési értesítés a frissítés telepítésekor
* Helyben gyors hozzáférést biztosít a fokozott élményt nyújtó a gyakran használt erőforrásokat használata
* A Blob-tároló-szerkesztőben, most már megtekintheti a bérelt blob tartozik virtual machine
* Most csukja össze a bal oldali panelen
* Most észlelést le egyszerre
* A Blob tároló, a fájlmegosztás és a tábla szerkesztők statisztika segítségével tekintse meg az erőforrás vagy a kijelölés mérete
* Most már bejelentkezhet az Azure Active Directory (AAD) alapú Azure verem fiókok is.
* Prémium szintű tárfiókokba történő most már feltöltheti archív fájlok 32 MB-nál nagyobb
* Továbbfejlesztett kisegítő támogatása
* Mostantól hozzáadhatja azok megbízható Base-64 kódolású X.509 SSL-tanúsítványok Szerkesztés – Ha&gt; SSL-tanúsítványok -&gt; tanúsítványok importálása

#### <a name="fixes"></a>Javítások

* Rögzített: után frissíteni egy fiók hitelesítő adatait, a fanézetben volna néha nem automatikus frissítése
* Rögzített: emulátor üzenetsorok és táblák SAS-kód generálása eredményeznének egy URL-cím érvénytelen
* Rögzített: prémium szintű storage-fiókok most bővíthetők a proxy be van kapcsolva
* Rögzített: az Alkalmaz gombra a fiókok kezelése lap volna nem működik, ha volt a kiválasztott 1 vagy 0-fiókok
* Rögzített: ütközés megoldások igénylő blobok feltöltése meghiúsulhat - 0.8.11 rögzített
* Rögzített: visszajelzés küldése lett feltörése 0.8.11 - 0.8.12 rögzített

#### <a name="known-issues"></a>Ismert problémák

* Alkalmazásra történő verziófrissítés utáni 0.8.10, szüksége lesz az összes a hitelesítő adatok frissítéséhez.
* Nagyított bejövő vagy kimenő, a előfordulhat, hogy rövid ideig a nagyítási szint visszaállítása alapértelmezett szintű.
* Mivel a fájlok feltöltése egy időben és blobokat több mint 3 csoport hibákat okozhat.
* A fiók beállítások panel jelenhet meg, hogy meg kell adnia hitelesítő adatait ahhoz, hogy az előfizetések szűrése.
* Blobok (külön-külön vagy átnevezett blob tárolóhoz belül) átnevezése nem őrzi meg a pillanatképeket. Minden más tulajdonságok és metaadatok BLOB-, fájl-és entitások egy átnevezési megőriz.
* Bár az Azure-verem jelenleg nem támogatja a fájlmegosztásokat, fájlmegosztások csomópont továbbra is egy csatolt verem Azure storage-fiók alatt jelenik meg.
* Ubuntu 14.04 telepítés kell ÖET verziójához vagy frissítése – frissítésének lépései alatt van:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089--088"></a>Verzió 0.8.9 / 0.8.8
02/23/2017

<iframe width="560" height="315" src="https://www.youtube.com/embed/R6gonK3cYAc?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/SrRPCm94mfE?ecver=1" frameborder="0" allowfullscreen></iframe>


#### <a name="new"></a>új

* A Tártallózó 0.8.9 automatikusan letölti a legújabb frissítések.
* A gyorsjavítás: a portál használatával létrehozott SAS URI-t a storage-fiók csatolása eredményezne hiba.
* Most létrehozására, kezelésére és blob pillanatképek lépteti elő.
* Most már bejelentkezhet Azure Kína, a Németországi Azure és a Azure Amerikai Egyesült államokbeli kormányzati fiókhoz.
* A nagyítási szint mostantól megváltoztatható. A Nézet menü Nagyítás, kicsinyítés és Nagyítás alaphelyzetbe állítja a beállításokat használják.
* Unicode-karaktereket mostantól támogatja a felhasználói blobok és a fájlok metaadatait.
* Kisegítő lehetőségek fejlesztései.
* A következő verzió kibocsátási megjegyzések a frissítési értesítés is megtekinthetők. Az aktuális kibocsátási megjegyzések a Súgó menüben is megtekintheti.

#### <a name="fixes"></a>Javítások

* Rögzített: a verziószám megfelelően megjelenik a Windows Vezérlőpult
* Rögzített: keresési már nincs legfeljebb 50 000 csomópontok
* Rögzített: töltse fel a fájlmegosztás végtelen hoz, ha a célkönyvtárat már nem létezik
* Rögzített: jobb stabilitás hosszú feltöltések és letöltések

#### <a name="known-issues"></a>Ismert problémák

* Nagyított bejövő vagy kimenő, a előfordulhat, hogy rövid ideig a nagyítási szint visszaállítása alapértelmezett szintű.
* A gyors hozzáférés csak olyan alapú előfizetés elemek működik. Ebben a kiadásban nem támogatottak a helyi erőforrások vagy a kulcs- vagy SAS-jogkivonat keresztül csatlakozó erőforrásokat.
* A gyors hozzáférés a keresse meg a cél erőforráson, attól függően, hogy hány erőforrásokhoz rendelkezik néhány másodpercet vehet igénybe.
* Mivel a fájlok feltöltése egy időben és blobokat több mint 3 csoport hibákat okozhat.

12/16/2016
### <a name="version-087"></a>0.8.7 verzió

<iframe width="560" height="315" src="https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>új

* Kiválaszthatja, hogyan egy frissítés, letölthető vagy másolási munkamenet elején a tevékenységek ablakban az ütközések feloldása
* A lapon, láthatja a tárolási erőforrások elérési útját mutat
* Ha egy lap gombra kattint, a bal oldali navigációs ablak helyére szinkronizálják

#### <a name="fixes"></a>Javítások

* Rögzített: Tártallózó mostantól a megbízható alkalmazások Mac gépen
* Rögzített: Ubuntu 14.04 támogatja
* Rögzített: Néha felhasználói felület Hozzáadás fiók tokenkódot előfizetések betöltésekor
* Rögzített: Néha nem minden tárolási erőforrások szereplő a bal oldali navigációs panelen
* Rögzített: A művelet ablaktáblában néha megjelenített üres műveletek
* Rögzített: Most megőrzi a legutóbbi lezárt munkamenetet a az ablak mérete
* Rögzített: Megnyithatja a helyi menü az azonos erőforrás több lap

#### <a name="known-issues"></a>Ismert problémák

* A gyors hozzáférés csak olyan alapú előfizetés elemek működik. Helyi erőforrások vagy a kulcs- vagy SAS-jogkivonat keresztül csatlakozó erőforrásokat nem támogatott ebben a kiadásban
* Keresse meg a cél erőforráson, attól függően, hogy hány erőforrásokhoz rendelkezik néhány másodpercet a is eltarthat, gyorsan elérheti őket
* Mivel a fájlok feltöltése egy időben és blobokat több mint 3 csoport hibákat okozhat
* Keresési kezeli, ezt követően a körülbelül 50 000 csomópontokon - keresés teljesítmény negatív hatással lehet, vagy nem kezelt kivételt okozhat
* MacOS Tártallózóval először kulcslánc eléréséhez felhasználói engedélyt kér a több kérdések merülhetnek fel. Javasoljuk, hogy mindig választja, az a kérdés nem jelenik meg újra

11/18/2016
### <a name="version-086"></a>0.8.6 verzió

#### <a name="new"></a>új

* PIN-kód a leggyakrabban használt szolgáltatások a gyors elérés egyszerű kezelhetőség lehet
* Több szerkesztők egyes lapjainak most már úgy is megnyithatja. Egyetlen kattintással megnyitni egy ideiglenes fülre. Kattintson duplán egy állandó lap megnyitásához. Az ideiglenes lapon abba, hogy egy állandó lapon is kattinthat
* Hajtottunk észlelhető teljesítménybeli és stabilitását érintő fejlesztések feltölti és tölti le, különösen olyan nagyméretű fájlok gyors gépeken
* Blob tárolók most hozhatók létre üres "virtuális" mappák
* Azt újra vezetett be az új kibővített substring keresés hatókörös keresésre, most két választási lehetősége van a keresés:
    * Globális keresés - csak adja meg a kívánt keresőkifejezést a keresési szövegmezőbe
    * Hatókörös keresésre - mellett egy csomópont Nagyítót ábrázoló ikonra, majd adja hozzá a kívánt keresőkifejezést, az elérési út végén, vagy kattintson a jobb gombbal és "keresési az itt"
* Jelentek meg különböző témák: ritka (alapértelmezett), sötét, kontrasztos fekete és kontrasztos fehér. Navigáljon a Szerkesztés -&gt; témák használatát igény szerint módosíthatja témák
* Módosíthatja a Blob és a fájl tulajdonságait
* Most már támogatott kódolt (base64) és kódolatlan üzenetek
* Linux, a 64 bites operációs rendszer mostantól szükséges. Ebben a kiadásban csak támogatott 64 bites Ubuntu 16.04.1 LTS
* Frissítettük az embléma!

#### <a name="fixes"></a>Javítások

* Rögzített: Képernyőn befagyasztási problémák
* Rögzített: Fokozott biztonság
* Rögzített: Néha duplikált csatolt fiókot fog megjelenni
* Rögzített: Nincs megadva content típusú blob tudta előállítani kivétel
* Rögzített: A program üres táblát a lekérdezés Panel megnyitása nem sikerült
* Rögzített: Változó a keresési hibák
* Rögzített: Nőtt az erőforrások száma betöltődnek 50 100 kattintva "Több betöltése"
* Rögzített: Első alkalommal történő futtatásakor, a fiók alá van írva, ha azt immár előfizetéseket fiók alapértelmezés szerint

#### <a name="known-issues"></a>Ismert problémák

* A Tártallózó jelen kiadása nem futtatható Ubuntu 14.04
* Az azonos erőforrás több lap megnyitásához nem folyamatosan kattintson az erőforrást. Kattintson az egy másik erőforrás és majd lépjen vissza, és kattintson az eredeti erőforrás-újra megnyitni egy másik lapon
* A gyors hozzáférés csak olyan alapú előfizetés elemek működik. Helyi erőforrások vagy a kulcs- vagy SAS-jogkivonat keresztül csatlakozó erőforrásokat nem támogatott ebben a kiadásban
* Keresse meg a cél erőforráson, attól függően, hogy hány erőforrásokhoz rendelkezik néhány másodpercet a is eltarthat, gyorsan elérheti őket
* Mivel a fájlok feltöltése egy időben és blobokat több mint 3 csoport hibákat okozhat
* Keresési kezeli, ezt követően a körülbelül 50 000 csomópontokon - keresés teljesítmény negatív hatással lehet, vagy nem kezelt kivételt okozhat

10/03/2016
### <a name="version-085"></a>0.8.5 verzió

#### <a name="new"></a>új

* Most már használhatja SAS portál által létrehozott kulcsok Tárfiókokhoz és erőforrások csatolása

#### <a name="fixes"></a>Javítások

* Rögzített: versenyhelyzet néha a keresés során okozott legyen, nem kibontható csomópontok
* Rögzített: "HTTP használata" nem működik, amikor csatlakozik a Storage-fiókok a fióknevet és kulcsot
* Rögzített: SAS-kulcsok (kifejezetten portál által létrehozott állók közül.) a hibaüzenetet adja vissza "záró perjelet"
* Rögzített: tábla importálása problémák
    * Egyes esetekben partíciókulcs- és sorkulcsa lettek visszaállítva
    * Nem lehet olvasni a "null" Partíciókulcsok

#### <a name="known-issues"></a>Ismert problémák

* Keresési kezeli a keresés - körülbelül 50 000-csomópont között, előfordulhat, hogy lehet a teljesítményre
* Azure verem jelenleg nem támogatja fájlok, a, a fájlok kibontása közben hiba fog megjelenítése

09/12/2016
### <a name="version-084"></a>0.8.4 verzió

<iframe width="560" height="315" src="https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>új

* Közvetlen hivatkozások mutatnak a storage-fiókok, tárolók, várólisták, táblák vagy megosztása a fájlmegosztások létrehozása és egyszerűen hozzáférhetnek a Windows és Mac OS - erőforrások támogatása
* Keresse meg a blobtárolók, táblák, üzenetsorok, fájlmegosztások vagy a keresőmezőbe a storage-fiókok
* Most csoportosíthatja záradékot a tábla Lekérdezésszerkesztőben
* Nevezze át, és a másolás/beillesztés blob tárolók, fájlmegosztások, táblák, blobok, blob mappákat, fájlokat és könyvtárakat az SAS-csatolású fiókok és a tárolók belül
* Átnevezése és lemásolná a blob-tárolók és a fájlmegosztások a tulajdonságok és metaadatok most megőrzése

#### <a name="fixes"></a>Javítások

* Rögzített: nem szerkeszthető táblaentitásokat tartalmazó logikai vagy bináris tulajdonság

#### <a name="known-issues"></a>Ismert problémák

* Keresési kezeli a keresés - körülbelül 50 000-csomópont között, előfordulhat, hogy lehet a teljesítményre

08/03/2016
### <a name="version-083"></a>0.8.3 verzió

<iframe width="560" height="315" src="https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>új

* Nevezze át a tárolók, táblák, fájlmegosztások
* Lekérdezés-szerkesztő fejlett élményt
* Mentse és lekérdezések betöltése
* Közvetlen storage-fiókok vagy a tárolókat, várólisták, táblákat mutató hivatkozásokat tartalmaz vagy fájlmegosztásokon megosztásához, és könnyen fér hozzá az erőforrások (csak Windows - macOS támogatása hamarosan elérhető!)
* Képes kezelni, és konfigurálja a CORS-szabályokat

#### <a name="fixes"></a>Javítások

* Rögzített: Microsoft Accounts ismételt hitelesítés szükséges 8 – 12 óránként

#### <a name="known-issues"></a>Ismert problémák

* Néha előfordulhat, hogy válaszol a felhasználói felület – az ablak maximalizálva segít a probléma megoldásához
* macOS telepítés emelt jogosultsági szintű lehet szükség.
* Fiók beállítások panel jelenhet meg, hogy meg kell adnia hitelesítő adatait ahhoz, hogy az előfizetések szűrése
* Fájlmegosztások, a blob-tárolók és a táblák átnevezése nem őrzi meg a metaadat- vagy más tulajdonságaiból a tárolóhoz, például a fájlmegosztási kvóta, a nyilvános hozzáférési szint vagy a hozzáférési házirendek
* Blobok (külön-külön vagy átnevezett blob tárolóhoz belül) átnevezése nem őrzi meg a pillanatképeket. Minden más tulajdonságok és metaadatok BLOB-, fájl-és entitások megőriz egy átnevezése
* Másolással vagy erőforrások átnevezése nem működik SAS-csatolású fiókok belül

07/07/2016
### <a name="version-082"></a>0.8.2 verzió

<iframe width="560" height="315" src="https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>új

* Storage-fiókok vannak csoportosítva, előfizetések; fejlesztési tárolás és a kulcs- vagy SAS keresztül csatlakozó erőforrásokat (helyi és kapcsolódó) csomópont alatt látható
* Jelentkezzen ki a "Azure-fiók beállítások" panelen fiókokhoz
* Proxy beállításainak engedélyezése és bejelentkezési kezelése
* Hozzon létre és blob címbérleteket megszüntetése
* Nyissa meg a blob-tárolók, várólisták, táblák és az egy kattintással fájlok

#### <a name="fixes"></a>Javítások

* Rögzített: a .NET vagy Java könyvtárak beszúrni üzenetek vannak nem megfelelően dekódolni a Base64 kódolású anyag
* Rögzített: $metrics táblák nem láthatók a Blob Storage-fiókok
* Rögzített: táblák csomópont nem működik a helyi (fejlesztés) tároláshoz

#### <a name="known-issues"></a>Ismert problémák

* macOS telepítés emelt jogosultsági szintű lehet szükség.

06/15/2016
### <a name="version-080"></a>0.8.0 verzió

<iframe width="560" height="315" src="https://www.youtube.com/embed/ycfQhKztSIY?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>új

* Megosztás-támogatás: megtekintése, feltöltése, letöltés, fájlok és könyvtárak, SAS URI-azonosítók másolására (létrehozása, és csatlakozzon)
* Növelt felhasználói élmény a SAS URI-azonosító vagy a fiók kulccsal rendelkező tárolóhoz való kapcsolódás során
* Tábla lekérdezési eredmények exportálása
* Tábla oszlop újra sorrendbe rendezés és a Testreszabás
* Blob tárolók $logs és $metrics táblák megtekintését Storage-fiókok engedélyezett metrikák
* Továbbfejlesztett exportálási és viselkedésének importálni, mostantól tartalmazza a tulajdonságérték-típus

#### <a name="fixes"></a>Javítások

* Rögzített: vagy nagy blobok feltöltése eredményezhet teljes feltöltések/letöltések
* Rögzített: Szerkesztés, hozzáadását és importálása egy numerikus karakterlánc-érték ("1") rendelkező entitás konvertálja a jelentést a kettős
* Rögzített: Nem sikerült a tábla csomópontot a helyi fejlesztési környezetben

#### <a name="known-issues"></a>Ismert problémák

* $metrics táblák nem láthatók a Blob Storage-fiókok
* Programozott módon hozzá üzenetsor-üzeneteket nem jeleníthető meg megfelelően, ha az üzenetek Base64 kódolás használatával kódolt

05/17/2016
### <a name="version-07201605090"></a>0.7.20160509.0 verzió

#### <a name="new"></a>új

* Jobb hiba történt az alkalmazás kezelése összeomlik

#### <a name="fixes"></a>Javítások

* Ahol információs sáv üzenetek néha nem jelennek meg, ha a bejelentkezési hitelesítő adatok volt szükség rögzített hiba

#### <a name="known-issues"></a>Ismert problémák

* Táblák: Hozzáadása, szerkesztése és importálása olyan entitás, amely egy tulajdonsága egy kétértelműen numerikus érték, például az "1" vagy "1.0" és a felhasználó megpróbálja elküldeni azt egy `Edm.String`, az érték lesz térjen vissza az ügyfél API-ja, mint egy Edm.Double keresztül

03/31/2016

### <a name="version-07201603250"></a>0.7.20160325.0 verzió

<iframe width="560" height="315" src="https://www.youtube.com/embed/imbgBRHX65A?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1" frameborder="0" allowfullscreen></iframe>


#### <a name="new"></a>új

* Táblázat a támogatási szolgálathoz: megtekintésre lekérdezése, importálása és exportálása CRUD műveletek az entitások
* Feldolgozási sor támogatási: megtekintését, hozzáadását, dequeueing üzenetek
* SAS URI-azonosítók Storage-fiókok létrehozása
* Kapcsolódás a SAS URI-azonosítók a Storage-fiókok
* A jövőbeli frissítések Tártallózó vonatkozó frissítési értesítések
* Frissített Megjelenés és működés

#### <a name="fixes"></a>Javítások

* Teljesítmény és megbízhatóság fejlesztései

### <a name="known-issues-amp-mitigations"></a>Ismert problémák &amp; megoldást

* A nagy blob fájlok letöltésének nem működik megfelelően – AzCopy használatát, amíg a hiba megoldása érdekében azt javasoljuk
* Fiók hitelesítő adatai nem fogja beolvasni nem gyorsítótárazott, ha a kezdőmappa nem található vagy nem lehet írni
* Ha azt vannak hozzáadása, szerkesztése és importálása olyan entitás, amely egy kétértelműen numerikus érték, például az "1" vagy "1.0" tulajdonsággal rendelkezik, és küldje el, mint a felhasználó megpróbál egy `Edm.String`, az érték lesz térjen vissza az ügyfél API-ja, mint egy Edm.Double keresztül
* Többsoros rekordot tartalmazó CSV-fájlok importálásakor a adatok előfordulhat, hogy darabolja vagy titkosított

02/03/2016

### <a name="version-07201601291"></a>0.7.20160129.1 verzió

#### <a name="fixes"></a>Javítások

* Jobb összesített teljesítményt biztosít, feltöltését, letöltése és a BLOB másolása

01/14/2016

### <a name="version-07201601050"></a>0.7.20160105.0 verzió

#### <a name="new"></a>új

* Linux-támogatás (OSX paritásos szolgáltatások)
* Blob tárolók hozzáadása a megosztott hozzáférési aláírásokkal (SAS-) kulcs
* Kínai Azure Storage-fiókok hozzáadása
* Egyéni végpontokat a Storage-fiókok hozzáadása
* Megnyithatja és megtekintheti a tartalmak szöveg és a kép blobok
* A blob tulajdonságai és metaadatok megtekintése és szerkesztése

#### <a name="fixes"></a>Javítások

* Rögzített: feltöltéssel vagy a letöltési nagyszámú BLOB (500 +) néha okozhat az alkalmazásnak egy fehér képernyő
* Rögzített: blob tároló nyilvános hozzáférési szint, az új értéknek nem frissül addig, amíg újra a fókuszt a tárolóra. Emellett a párbeszédpanelen mindig az alapértelmezett "Nem nyilvános hozzáférés", és nem tényleges aktuális értéke.
* Jobb általános billentyűzet/kisegítő és a felhasználói felület támogatja
* Ha a szóköz karakter hosszú fussa útkövetését előzmények szöveg
* SAS párbeszédpanel támogatja a bemenet-ellenőrzést
* Helyi tárolás továbbra is elérhetők, még akkor is, ha a felhasználó hitelesítő adatainak tanúsítványa lejárt
* Egy megnyitott blob-tároló törlésekor a blob Intéző jobb oldalán ki van zárva

#### <a name="known-issues"></a>Ismert problémák

* Linux-telepítés kell ÖET verziójához frissítése – frissítésének lépései kisebb vagy:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>0.7.20151116.0 verzió

#### <a name="new"></a>új

* macOS, és a Windows-verziók
* Jelentkezzen be a Storage-fiókok megtekintése, – használja a szervezeti fiók Microsoft Account, 2FA, stb.
* Helyi fejlesztési tárolási (storage emulatorral csak Windows)
* Az Azure Resource Manager és klasszikus erőforrás-támogatás
* Hozzon létre vagy töröljön a blobokat, üzenetsorokat vagy táblák
* Keresse meg az adott blobokat, üzenetsorokat vagy táblák
* Blob tárolók tartalmát felfedezés
* Megtekintheti és haladjon végig a könyvtárak
* Töltse fel, töltse le és törölje a blobok és mappák
* A blob tulajdonságai és metaadatok megtekintése és szerkesztése
* SAS-kulcsok létrehozása
* Kezelése és tárolása hozzáférési házirendek (SAP) létrehozása
* Keresés a blobokban előtag alapján
* Húzza az n feltölteni, vagy letöltheti a fájlokat

#### <a name="known-issues"></a>Ismert problémák

* Blob tároló nyilvános hozzáférési szint, az új értéknek nem frissül addig, amíg újra a fókuszt a tárolóban
* A nyilvános hozzáférési szint beállítása párbeszédpanel megnyitásához mindig mutat "Nem nyilvános hozzáférés", az alapértelmezett, és nem a tényleges jelenlegi értéke
* A letöltött blobokat nem nevezhető át.
* Rendszer néha "elakadnak" a egy folyamatban lévő tevékenységek naplóbejegyzések állapot, ha hiba lép fel, és nem jelenik meg a hiba
* Egyes esetekben összeomlik, vagy teljesen fehér kerül, amikor megpróbálja feltölteni, vagy töltse le a nagyszámú BLOB
* A másolási műveletek néha megszakítása nem működik
* Során hozza létre a tárolót (blob vagy sor vagy tábla), ha érvénytelen bemeneti, és létrehozhatja az egy másik tárolóhoz típusa egy másik, fókusza nem adható meg az új típus
* Nem hozható létre új mappát, vagy nem mappa átnevezése




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
