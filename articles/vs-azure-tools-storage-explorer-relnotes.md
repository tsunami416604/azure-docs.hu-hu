---
title: A Microsoft Azure Storage Explorer kibocsátási megjegyzései
description: A Microsoft Azure Storage Explorer kibocsátási megjegyzései
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2018
ms.author: cawa
ms.openlocfilehash: 0b2ffc00b6c96f2c31a4b711f618e7b87b6f69e0
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482097"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>A Microsoft Azure Storage Explorer kibocsátási megjegyzései

Ez a cikk a Azure Storage Explorer legújabb kibocsátási megjegyzéseit, valamint a korábbi verziók kibocsátási megjegyzéseit tartalmazza. 

A [Microsoft Azure Storage Explorer](./vs-azure-tools-storage-manage-with-storage-explorer.md) egy önálló alkalmazás, amely lehetővé teszi az Azure Storage-alapú adattárolást Windows, MacOS és Linux rendszeren.

A Storage Explorer korábbi verzióinak letöltéséhez látogasson el a GitHub-tárház [kiadási oldalára](https://github.com/microsoft/AzureStorageExplorer/releases) .

## <a name="version-1110"></a>1\.11.0 verziója
11/4/2019

### <a name="new"></a>Új
* A Blobok, ADLS Gen2 és Managed Disks műveleteit az integrált AzCopy használja. Pontosabban a következő műveletek hajthatók végre a AzCopy használatával:
   * Blobok
      * Megnyitás szerkesztésre + feltöltésre
      * Feltöltés, beleértve a drag & drop
      * Letöltés
      * & Beillesztési #1249 másolása
      * Törlés
   * Blobok ADLS Gen2
      * Feltöltés, beleértve a drag & drop
      * Letöltés
      * & Beillesztés másolása
      * Törlés, beleértve a mappa törlését
   * Felügyelt lemezek
      * Feltöltés
      * Letöltés
      * & Beillesztés másolása

   Emellett számos gyakran igényelt szolgáltatás lett hozzáadva az integrált AzCopy-felülethez:
   * Ütközési felbontás – a rendszer az átvitel során kéri az ütközések feloldásához. #1455
   * Feltöltés blobként – kiválaszthatja, hogy AzCopy-e a feltöltések. VHD-és a. vhdx-fájlok. #1164 és #1601
   * Konfigurálható AzCopy paraméterek – számos beállítás lett hozzáadva a AzCopy teljesítményének és erőforrás-használatának finomhangolásához. További részletekért lásd alább.

* A ADLS Gen2 és a Blobok több protokollhoz való hozzáférésének engedélyezéséhez, valamint a ADLS Gen2-funkciók további fejlesztéséhez a következő funkciókat adta hozzá a ADLS Gen2-fiókokhoz:
   * Keresés az ACL-engedélyek beállítása felhasználóbarát nevek használatával
   * Rejtett tárolók, például $logs és $web megtekintése
   * Tároló bérletének beolvasása és megszakítása
   * BLOB bérletének beolvasása és megszakítása #848
   * Tároló-hozzáférési házirendek kezelése
   * BLOB hozzáférési szintjeinek konfigurálása
   * & Másolási Blobok másolása

* Ebben a kiadásban 17 további nyelvet tekintünk meg. Átválthat a kívánt nyelvre a beállítások lapon az "alkalmazás" → "területi beállítások" → "nyelv (előzetes verzió)" alatt. Továbbra is keményen dolgozunk a további sztringek fordításán és a fordítási minőség javításán. Ha bármilyen visszajelzést szeretne kapni a fordítással kapcsolatban, vagy ha olyan karakterláncot észlel, amely még nincs lefordítva, [Nyisson meg egy problémát a githubon](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%F0%9F%8C%90%20localization&template=bug-report.md&title=).
* Minden kiadásban megpróbálunk bevezetni néhány beállítást, így lehetővé válik a megfelelő Storage Explorer. Ebben a kiadásban a AzCopy további konfigurálására, valamint a szolgáltatási csomópontok elrejtésére vonatkozó beállításokat adunk hozzá:
   * AzCopy sávszélesség-korlát – segít megszabni, hogy a hálózati AzCopy mekkora részét használja. Ez a beállítás a "Transfers" → "AzCopy" → "maximális adatátviteli sebesség" alatt található. #1099
   * AzCopy MD5-ellenőrzés-lehetővé teszi, hogy beállítsa, hogy mikor és hogyan szigorúan AzCopy az MD5-kivonatokat a letöltéskor. Ez a beállítás a "Transfers" → "AzCopy" → "MD5"-ben található.
   * A AzCopy Egyidejűség és a memória pufferének mérete – alapértelmezés szerint a AzCopy elemzi a gépet, hogy meghatározza a beállítások ésszerű alapértelmezett értékeit. Ha azonban teljesítményproblémák jelentkeznek, a speciális beállításokkal részletesebben is megszabhatja, hogy a AzCopy hogyan fusson a számítógépen. Ezeket a beállításokat a "Transfers" → "AzCopy" alatt találja. #994
   * Szolgáltatási csomópontok megjelenítése és elrejtése – ezekkel a beállításokkal megjelenítheti vagy elrejtheti az Storage Explorer által támogatott Azure-szolgáltatásokat. Ezeket a beállításokat a "szolgáltatások" szakaszban találja. #1877

* Egy felügyelt lemez pillanatképének létrehozásakor az alapértelmezett név van megadva. #1847
* Ha az Azure AD-hez csatol egy ADLS Gen2 BLOB-tárolót, akkor az "(ADLS Gen2)" jelenik meg a csomópont mellett. #1861

### <a name="fixes"></a>Javítások
* Nagyméretű lemezek másolása, feltöltése vagy letöltése során a Storage Explorer időnként nem fogja tudni visszavonni a műveletben részt vevő lemezekhez való hozzáférést. Ez megoldottuk. #2048
* A tábla statisztikái nem sikerült a partíciós kulcs lekérdezésének megtekintésekor. Ez megoldottuk. #1886

### <a name="known-issues"></a>Ismert problémák
* Storage Explorer 1.11.0 most egy elosztott fájlrendszerbeli végpontra (például "myaccount.dfs.core.windows.net") van szükség ahhoz, hogy ADLS Gen2 tárolóhoz lehessen csatolni. A Storage Explorer korábbi verziói lehetővé tették a blob-végpont használatát. Előfordulhat, hogy ezek a mellékletek a 1.11.0 való frissítés után már nem működnek. Ha ezt a problémát tapasztalja, csatlakoztassa újra az elosztott fájlrendszerbeli végpont használatával.
* A numerikus beállításokat a rendszer nem ellenőrzi, hogy érvényes tartományban vannak-e. #2140
* A blob-tárolók egyik Storage-fiókból a másikba való másolása a fanézetben sikertelen lehet. Kivizsgáljuk a problémát. #2124
* Az automatikus frissítés beállítás még nem befolyásolja a blob Explorer összes műveletét.
* A felügyelt lemezes szolgáltatások Azure Stack nem támogatottak.
* Ha egy lemez feltöltése vagy beillesztése meghiúsul, és a hiba előtt új lemez lett létrehozva, akkor Storage Explorer nem törli a lemezt.
* Attól függően, hogy mikor szakítja meg a lemez feltöltését vagy beillesztését, megsérült állapotban hagyhatja az új lemezt. Ha ez történik, törölnie kell az új lemezt, vagy manuálisan kell meghívnia a lemez API-jait a lemez tartalmának lecserélése érdekében, hogy az már ne legyen megsérült.
* A RBAC használatakor Storage Explorer a tárolási erőforrások eléréséhez bizonyos felügyeleti rétegre vonatkozó engedélyeket igényel. További információért tekintse meg a [hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez frissítse a a csoport csomópontot. Tekintse meg a #537 további információt.
* Ha VS használ a Mac számítógépen, és minden eddiginél hozott létre egyéni AAD-konfigurációt, előfordulhat, hogy nem tudja jelentkezik be. A probléma megkerüléséhez tartalmának törlése ~ /. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* Azurite még nem teljes körűen megvalósítva összes Storage API-t. Emiatt lehetséges, hogy nem várt hibák vagy viselkedés fejlesztési tárterülettel Azurite használatakor.
* Ritka esetekben a fa fókusz előfordulhat, hogy elakadnak a gyors hozzáférés. Ezt követően a fókuszt, frissítse az összes is.
* Feltöltés a OneDrive-mappából egy NodeJS hibája miatt nem működik. Kijavítva a hiba, de Electron még nincs integrálva. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Az Azure Stack nem támogatja a következő funkciókat. Ezek a szolgáltatások használata közben az Azure Stack használatával próbál erőforrások váratlan hibákhoz vezethet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
   * ADLS Gen2
   * Felügyelt lemezek
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linuxon futó Storage Explorer futtatásához bizonyos függőségeket először telepíteni kell. További információért tekintse meg a Storage Explorer [hibaelhárítási útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) .

## <a name="previous-releases"></a>Korábbi kiadások

* [1.10.1 verziója](#version-1101)
* [1.10.0 verziója](#version-1100)
* [1.9.0 verziója](#version-190)
* [1.8.1-es verzió](#version-181)
* [1.8.0-verzió](#version-180)
* [1.7.0 verziója](#version-170)
* [1.6.2-es verzió](#version-162)
* [1.6.1-es verzió](#version-161)
* [1.6.0 verziója](#version-160)
* [1.5.0-es verzió](#version-150)
* [1.4.4-es verzió](#version-144)
* [1.4.3 verziója](#version-143)
* [1.4.2-es verzió](#version-142)
* [1.4.1-es verzió](#version-141)
* [1.3.0 verziója](#version-130)
* [1.2.0 verziója](#version-120)
* [1.1.0-es verzió](#version-110)
* [A 1.0.0 verziója](#version-100)
* [0.9.6 verziója](#version-096)
* [0.9.5 verziója](#version-095)
* [0.9.4 és 0.9.3 verzió](#version-094-and-093)
* [0.9.2 verziója](#version-092)
* [0.9.1 és 0.9.0 verzió](#version-091-and-090)
* [0.8.16 verziója](#version-0816)
* [0.8.14 verziója](#version-0814)
* [0.8.13 verziója](#version-0813)
* [0.8.12-és 0.8.11-és 0.8.10-verziók](#version-0812-and-0811-and-0810)
* [0.8.9 és 0.8.8 verzió](#version-089-and-088)
* [0.8.7 verziója](#version-087)
* [0.8.6 verziója](#version-086)
* [0.8.5 verziója](#version-085)
* [0.8.4 verziója](#version-084)
* [0.8.3 verziója](#version-083)
* [0.8.2 verziója](#version-082)
* [0.8.0 verziója](#version-080)
* [0.7.20160509.0 verziója](#version-07201605090)
* [0.7.20160325.0 verziója](#version-07201603250)
* [0.7.20160129.1 verziója](#version-07201601291)
* [0.7.20160105.0 verziója](#version-07201601050)
* [0.7.20151116.0 verziója](#version-07201511160)

## <a name="version-1101"></a>1\.10.1 verziója
9/19/2019

### <a name="hotfix"></a>Gyorsjavítás
* Egyes felhasználók hibát észleltek a 1.10.0, miközben megpróbálták megtekinteni az ADLS 1. generációs fiókjaikat. Ez a hiba megakadályozta az Explorer panel megfelelő megjelenítését. Ez megoldottuk. #1853 #1865

### <a name="new"></a>Új
* Storage Explorer most egy dedikált beállítások felhasználói felülettel rendelkezik. A Szerkesztés → beállítások menüpontból vagy a bal oldali függőleges eszköztár beállítások ikonjára (a fogaskerékre) kattintva érheti el. Ez a funkció az első lépés, amellyel számos [felhasználó által kért beállítást](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate)biztosítunk. A jelen kiadástól kezdve a következő beállítások támogatottak:
  * Téma
  * Proxy
  * Kilépés kijelentkezés #6
  * Az eszköz kódjának bejelentkezett bejelentkezésének engedélyezése
  * Automatikus frissítés #1526
  * AzCopy engedélyezése
  * AzCopy SAS-időtartam, ha más profilsablonban szeretné látni, [Nyisson meg egy problémát a githubon](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=) , amely leírja a kívánt beállítást.
* A Storage Explorer mostantól támogatja a Managed Disks. A következőket teheti:
  * Helyszíni virtuális merevlemez feltöltése új lemezre
  * Lemez letöltése
  * Lemezek másolása és beillesztése erőforrás-csoportok és régiók között
  * Lemezek törlése
  * Pillanatkép készítése lemezről a lemezek feltöltése, letöltése és régiók közötti másolása a AzCopy V10-es verzióval történik.
* A Storage Explorer mostantól a Linux rendszerhez tartozó snap-tárolón keresztül is telepíthető. Ha az illesztési tárolón keresztül telepíti az alkalmazást, a rendszer minden függőséget telepít Önnek, beleértve a .NET Core-t is. Jelenleg ellenőrizte, hogy Storage Explorer jól fut az Ubuntu és a CentOS platformon. Ha más Linux-disztribúciókban lévő snap Store-ból való telepítéssel kapcsolatos problémákat tapasztal, [Nyisson meg egy problémát a githubon](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Ha többet szeretne megtudni az snap áruházból történő telepítésről, tekintse meg az [első lépéseket ismertető útmutatót](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux). #68
* Két jelentős módosítás történt a Azure Active Directory (Azure AD) csatlakoztatására, amelynek célja, hogy a funkció ADLS Gen2 felhasználók számára is hasznos legyen:
  * Most válassza ki azt a bérlőt, amelyhez az erőforrást csatlakoztatja. Ez azt jelenti, hogy már nem kell RBAC hozzáférni az erőforrás előfizetéséhez.
  * Ha ADLS Gen2 BLOB-tárolót csatlakoztat, most már csatolhatja a tároló egy adott elérési útját.
* ADLS Gen2 fájlokhoz és mappákhoz tartozó ACL-ek kezelésekor a Storage Explorer mostantól megjeleníti az ACL entitások rövid nevét. #957
* Ha OID-n keresztül ad hozzá egy ADLS Gen2 ACL-t, Storage Explorer ekkor ellenőrzi, hogy az OID a bérlő egy érvényes entitásához tartozik-e. #1603
* A lapfülek közötti navigáláshoz használt billentyűparancsok mostantól további szabványos billentyűkombinációkat használnak. #1018
* A lapon a középső gombra kattintva lezárul. #1348
* Ha egy AzCopy-átvitel kihagyásokat tartalmaz, és nincsenek hibák, Storage Explorer ekkor egy figyelmeztető ikon jelenik meg, amely kiemeli, hogy a kihagyások bekövetkeztek. #1490
* Az integrált AzCopy frissítve lett a 10.2.1 verzióra. Emellett most már megtekintheti a névjegy párbeszédablakban telepített AzCopy verzióját is. #1343

### <a name="fixes"></a>Javítások
* Számos felhasználó több "nem definiált verziója nem olvasható" vagy "nem definiált kapcsolat nem olvasható" hibát észlelt a csatlakoztatott Storage-fiókok használatakor. Bár továbbra is vizsgáljuk a probléma alapvető okát, a 1.10.0 javítottuk a csatlakoztatott Storage-fiókok betöltésével kapcsolatos hibákat. #1626, #985 és #1532
* Lehetséges, hogy az Explorer faszerkezete (bal oldali) olyan állapotba kerül, amelyben a fókusz többször is a legfelső csomópontra ugrik. Ez megoldottuk. #1596
* Egy blob pillanatképének kezelésekor a képernyőolvasó nem olvassa be a pillanatképhez társított időbélyeget. Ez megoldottuk. #1202
* A macOS-re vonatkozó proxybeállítások nem lettek beállítva időben a hitelesítési folyamat használatára. Ez megoldottuk. #1567
* Ha egy szuverén felhőben lévő Storage-fiók neve és kulcsa alapján lett csatolva, a AzCopy nem fog működni. Ez megoldottuk. #1544
* Ha egy kapcsolódási karakterláncon keresztül csatlakozik, Storage Explorer most eltávolítja a záró szóközöket. #1387

### <a name="known-issues"></a>Ismert problémák
* Az automatikus frissítés beállítás még nem befolyásolja a blob Explorer összes műveletét.
* A felügyelt lemezes szolgáltatások Azure Stack nem támogatottak.
* Ha egy lemez feltöltése vagy beillesztése meghiúsul, és a hiba előtt új lemez lett létrehozva, akkor Storage Explorer nem törli a lemezt.
* Attól függően, hogy mikor szakítja meg a lemez feltöltését vagy beillesztését, megsérült állapotban hagyhatja az új lemezt. Ha ez történik, törölnie kell az új lemezt, vagy manuálisan kell meghívnia a lemez API-jait a lemez tartalmának lecserélése érdekében, hogy az már ne legyen megsérült.
* Attól függően, hogy mikor szakítja meg a lemez feltöltését vagy beillesztését, megsérült állapotban hagyhatja az új lemezt. Ha ez történik, törölnie kell az új lemezt, vagy manuálisan kell meghívnia a lemez API-jait a lemez tartalmának lecserélése érdekében, hogy az már ne legyen megsérült.
* A nem AzCopy Blobok letöltésének végrehajtásakor a nagyméretű fájlok MD5-hitelesítése nem történik meg. Ezt a Storage SDK hibája okozza. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* A RBAC használatakor Storage Explorer a tárolási erőforrások eléréséhez bizonyos felügyeleti rétegre vonatkozó engedélyeket igényel. További információért tekintse meg a [hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez frissítse a a csoport csomópontot. Tekintse meg a #537 további információt.
* Ha VS használ a Mac számítógépen, és minden eddiginél hozott létre egyéni AAD-konfigurációt, előfordulhat, hogy nem tudja jelentkezik be. A probléma megkerüléséhez tartalmának törlése ~ /. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* Azurite még nem teljes körűen megvalósítva összes Storage API-t. Emiatt lehetséges, hogy nem várt hibák vagy viselkedés fejlesztési tárterülettel Azurite használatakor.
* Ritka esetekben a fa fókusz előfordulhat, hogy elakadnak a gyors hozzáférés. Ezt követően a fókuszt, frissítse az összes is.
* Feltöltés a OneDrive-mappából egy NodeJS hibája miatt nem működik. Kijavítva a hiba, de Electron még nincs integrálva. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Az Azure Stack nem támogatja a következő funkciókat. Ezek a szolgáltatások használata közben az Azure Stack használatával próbál erőforrások váratlan hibákhoz vezethet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
   * ADLS Gen2
   * Felügyelt lemezek
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linuxon futó Storage Explorer futtatásához bizonyos függőségeket először telepíteni kell. További információért tekintse meg a Storage Explorer [hibaelhárítási útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) .


## <a name="version-1100"></a>1\.10.0 verziója
9/12/2019

### <a name="new"></a>Új

* Storage Explorer most egy dedikált beállítások felhasználói felülettel rendelkezik. A Szerkesztés → beállítások menüpontból vagy a bal oldali függőleges eszköztár beállítások ikonjára (a fogaskerékre) kattintva érheti el. Ez a funkció az első lépés, amellyel számos [felhasználó által kért beállítást](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate)biztosítunk. A jelen kiadástól kezdve a következő beállítások támogatottak:
    * Téma
    * Proxy
    * Kilépés Kijelentkezés [#6](https://www.github.com/Microsoft/AzureStorageExplorer/issues/6)
    * Az eszköz kódjának bejelentkezett bejelentkezésének engedélyezése
    * Automatikus frissítés [#1526](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1526)
    * AzCopy engedélyezése
    * AzCopy SAS időtartama

    Ha további profilsablonban szeretné látni, [Nyisson meg egy problémát a githubon, amely leírja a kívánt beállítást](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=).
* A Storage Explorer mostantól támogatja a Managed Disks. A következőket teheti:
    * Helyszíni virtuális merevlemez feltöltése új lemezre
    * Lemez letöltése
    * Lemezek másolása és beillesztése erőforrás-csoportok és régiók között
    * Lemezek törlése
    * Lemez pillanatképének létrehozása

    A lemezek feltöltését, letöltését és régiók közötti másolását a AzCopy v10 látja el.
* A Storage Explorer mostantól a Linux rendszerhez tartozó snap-tárolón keresztül is telepíthető. Ha az illesztési tárolón keresztül telepíti az alkalmazást, a rendszer minden függőséget telepít Önnek, beleértve a .NET Core-t is. Jelenleg ellenőrizte, hogy Storage Explorer jól fut az Ubuntu és a CentOS platformon. Ha más Linux-disztribúciókban lévő snap Store-ból való telepítéssel kapcsolatos problémákat tapasztal, [Nyisson meg egy problémát a githubon](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Ha többet szeretne megtudni az snap áruházból történő telepítésről, tekintse meg az [első lépéseket ismertető útmutatót](https://aka.ms/storageexplorer/snapinformation). [#68](https://www.github.com/Microsoft/AzureStorageExplorer/issues/68)
* Két jelentős módosítás történt a Azure Active Directory (Azure AD) csatlakoztatására, amelynek célja, hogy a funkció ADLS Gen2 felhasználók számára is hasznos legyen: * Most válassza ki azt a bérlőt, amelyhez az erőforrást csatlakoztatja. Ez azt jelenti, hogy már nem kell RBAC hozzáférni az erőforrás előfizetéséhez.
        * Ha ADLS Gen2 BLOB-tárolót csatlakoztat, most már csatolhatja a tároló egy adott elérési útját.
* ADLS Gen2 fájlokhoz és mappákhoz tartozó ACL-ek kezelésekor a Storage Explorer mostantól megjeleníti az ACL entitások rövid nevét. [#957](https://www.github.com/Microsoft/AzureStorageExplorer/issues/957)
* Ha OID-n keresztül ad hozzá egy ADLS Gen2 ACL-t, Storage Explorer ekkor ellenőrzi, hogy az OID a bérlő egy érvényes entitásához tartozik-e. [#1603](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1603)
* A lapfülek közötti navigáláshoz használt billentyűparancsok mostantól további szabványos billentyűkombinációkat használnak. [#1018](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1018)
* A lapon a középső gombra kattintva lezárul. [#1348](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1348)
* Ha egy AzCopy-átvitel kihagyásokat tartalmaz, és nincsenek hibák, Storage Explorer ekkor egy figyelmeztető ikon jelenik meg, amely kiemeli, hogy a kihagyások bekövetkeztek. [#1490](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1490)
* Az integrált AzCopy frissítve lett a 10.2.1 verzióra. Emellett most már megtekintheti a névjegy párbeszédablakban telepített AzCopy verzióját is. [#1343](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1343)

### <a name="fixes"></a>Javítások

* Számos felhasználó több "nem definiált verziója nem olvasható" vagy "nem definiált kapcsolat nem olvasható" hibát észlelt a csatlakoztatott Storage-fiókok használatakor. Bár továbbra is vizsgáljuk a probléma alapvető okát, a 1.10.0 javítottuk a csatlakoztatott Storage-fiókok betöltésével kapcsolatos hibákat. [#1626](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1626), [#985](https://www.github.com/Microsoft/AzureStorageExplorer/issues/985)és [#1532](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1532)
* Lehetséges, hogy az Explorer faszerkezete (bal oldali) olyan állapotba kerül, amelyben a fókusz többször is a legfelső csomópontra ugrik. Ez megoldottuk. [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* Egy blob pillanatképének kezelésekor a képernyőolvasó nem olvassa be a pillanatképhez társított időbélyeget. Ez megoldottuk. [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* A macOS-re vonatkozó proxybeállítások nem lettek beállítva időben a hitelesítési folyamat használatára. Ez megoldottuk. [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* Ha egy szuverén felhőben lévő Storage-fiók neve és kulcsa alapján lett csatolva, a AzCopy nem fog működni. Ez megoldottuk. [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* Ha egy kapcsolódási karakterláncon keresztül csatlakozik, Storage Explorer most eltávolítja a záró szóközöket. [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>Ismert problémák

* Az automatikus frissítés beállítás még nem befolyásolja a blob Explorer összes műveletét.
* A felügyelt lemezes szolgáltatások Azure Stack nem támogatottak.
* Ha egy lemez feltöltése vagy beillesztése meghiúsul, és a hiba előtt új lemez lett létrehozva, akkor Storage Explorer nem törli a lemezt.
* Attól függően, hogy mikor szakítja meg a lemez feltöltését vagy beillesztését, megsérült állapotban hagyhatja az új lemezt. Ha ez történik, törölnie kell az új lemezt, vagy manuálisan kell meghívnia a lemez API-jait a lemez tartalmának lecserélése érdekében, hogy az már ne legyen megsérült.
* A nem AzCopy Blobok letöltésének végrehajtásakor a nagyméretű fájlok MD5-hitelesítése nem történik meg. Ezt a Storage SDK hibája okozza. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* A RBAC használatakor Storage Explorer a tárolási erőforrások eléréséhez bizonyos felügyeleti rétegre vonatkozó engedélyeket igényel. További információért tekintse meg a [hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez frissítse a a csoport csomópontot. Tekintse meg a #537 további információt.
* Ha VS használ a Mac számítógépen, és minden eddiginél hozott létre egyéni AAD-konfigurációt, előfordulhat, hogy nem tudja jelentkezik be. A probléma megkerüléséhez tartalmának törlése ~ /. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* Azurite még nem teljes körűen megvalósítva összes Storage API-t. Emiatt lehetséges, hogy nem várt hibák vagy viselkedés fejlesztési tárterülettel Azurite használatakor.
* Ritka esetekben a fa fókusz előfordulhat, hogy elakadnak a gyors hozzáférés. Ezt követően a fókuszt, frissítse az összes is.
* Feltöltés a OneDrive-mappából egy NodeJS hibája miatt nem működik. Kijavítva a hiba, de Electron még nincs integrálva. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Az Azure Stack nem támogatja a következő funkciókat. Ezek a szolgáltatások használata közben az Azure Stack használatával próbál erőforrások váratlan hibákhoz vezethet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
   * ADLS Gen2
   * Felügyelt lemezek
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linuxon futó Storage Explorer futtatásához bizonyos függőségeket először telepíteni kell. További információért tekintse meg a Storage Explorer [hibaelhárítási útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) .

## <a name="version-190"></a>1\.9.0 verziója
7/1/2019

### <a name="download-azure-storage-explorer-190"></a>Azure Storage Explorer 1.9.0 letöltése
- [A Windows 1.9.0 Azure Storage Explorer](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Mac Azure Storage Explorer 1.9.0](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.9.0 Linux rendszerhez](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Új

* A blob-tárolók mostantól az Azure AD-n keresztül is csatolhatók (RBAC vagy ACL-engedélyek). Ez a szolgáltatás segít a tárolókban hozzáférő felhasználók számára, de nem a tárolók tárolási fiókjaihoz. A szolgáltatással kapcsolatos további információkért tekintse meg a Első lépések útmutatót.
* A bérlet beolvasása és megszakítása mostantól a RBAC-mel is működik. [#1354](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1354)
* A hozzáférési házirendek kezelése és a nyilvános hozzáférési szint beállítása mostantól együttműködik a RBAC. [#1355](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1355)
* A blob-mappák törlése mostantól együttműködik a RBAC. [#1450](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1450)
* A blob hozzáférési rétegének módosítása mostantól a RBAC-mel működik. [#1446](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1446)
* Mostantól gyorsan visszaállíthatja a gyors elérést a "Súgó" → "alaphelyzetbe állítás" lehetőségen keresztül. [#1327](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1327)

### <a name="preview-features"></a>Előzetes verziójú funkciók

* Az eszköz kódjának bejelentkezési folyamata mostantól előzetes verzióként érhető el. Az engedélyezéshez nyissa meg az "Előnézet" → "az eszköz kódjának bejelentkezni" című részt. Javasoljuk, hogy minden olyan felhasználó számára, aki üres bejelentkezési Windows-problémával rendelkezett, próbálja ki ezt a funkciót, mivel a bejelentkezés megbízhatóbb formája lehet.
* A AzCopy szolgáltatással integrált Storage Explorer jelenleg előzetes verzióban érhető el. Az engedélyezéshez nyissa meg az "Előnézet" → "a AzCopy használata a blob-feltöltéshez és a letöltéshez" lehetőséget. Az AzCopy-mel végzett blob-átviteleknek gyorsabbnak és nagyobb teljesítménynek kell lenniük.

### <a name="fixes"></a>Javítások

* A rögzített nem tud több mint 50 előfizetést betölteni egy fiókhoz. [#1416](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1416)
* A "Bejelentkezés" gomb nem működik az információs sávon, amely akkor jelenik meg, ha egy közvetlen kapcsolat meghiúsul. [#1358](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1358)
* Rögzített nem az. app fájlok feltöltése macOS rendszeren. [#1119](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1119)
* Kijavítva: az összes újrapróbálkozási művelet nem működik a hibás blob átnevezéséhez. [#992](https://www.github.com/Microsoft/AzureStorageExplorer/issues/992)
* A rögzített "megszakítás" nem működik blob megnyitásakor. [#1464](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1464)
* Több helyesírási és elemleírási probléma javítva a termékben. Köszönjük, hogy mindenki, aki jelentett ezeket a problémákat! [#1303](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1303), [#1328](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1328), [#1329](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1329), [#1331](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1331), [#1336](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1336), [#1352](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1352), [#1368](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1368), [#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1395)

### <a name="known-issues"></a>Ismert problémák

* A nem AzCopy Blobok letöltésének végrehajtásakor a nagyméretű fájlok MD5-hitelesítése nem történik meg. Ezt a Storage SDK hibája okozza. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* A RBAC használatakor Storage Explorer a tárolási erőforrások eléréséhez bizonyos felügyeleti rétegre vonatkozó engedélyeket igényel. További információért tekintse meg a [hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Sikertelen volt a ADLS Gen2 Blobok elérésének megkísérlése a proxy mögött.
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez frissítse a a csoport csomópontot. Tekintse meg a #537 további információt.
* Ha VS használ a Mac számítógépen, és minden eddiginél hozott létre egyéni AAD-konfigurációt, előfordulhat, hogy nem tudja jelentkezik be. A probléma megkerüléséhez tartalmának törlése ~ /. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* Azurite még nem teljes körűen megvalósítva összes Storage API-t. Emiatt lehetséges, hogy nem várt hibák vagy viselkedés fejlesztési tárterülettel Azurite használatakor.
* Ritka esetekben a fa fókusz előfordulhat, hogy elakadnak a gyors hozzáférés. Ezt követően a fókuszt, frissítse az összes is.
* Feltöltés a OneDrive-mappából egy NodeJS hibája miatt nem működik. Kijavítva a hiba, de Electron még nincs integrálva. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Az Azure Stack nem támogatja a következő funkciókat. Ezek a szolgáltatások használata közben az Azure Stack használatával próbál erőforrások váratlan hibákhoz vezethet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
   * ADLS Gen2
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linuxon futó Storage Explorer futtatásához bizonyos függőségeket először telepíteni kell. További információért tekintse meg a Storage Explorer [hibaelhárítási útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) .

## <a name="version-181"></a>1\.8.1-es verzió
5/13/2019

### <a name="hotfixes"></a>Gyorsjavítások
* Bizonyos esetekben a "több betöltés" gombra kattintva az erőforrás szintjén nem fog visszaadni az erőforrások következő oldalára. Ez megoldottuk. [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* Windows rendszeren a AzCopy letöltése meghiúsul, ha egyetlen fájlt vagy mappát töltött le, és a fájl vagy mappa neve érvénytelen volt egy Windows elérési útra. Ez megoldottuk. [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* Rendkívül ritka esetekben, amikor egy fájlmegosztás átnevezését vagy egy fájlmegosztás átnevezését végzi, ha az átnevezéshez tartozó másolatok sikertelenek voltak, vagy ha a Storage Explore nem tudta megerősíteni a másolatok sikerességét az Azure-ban, lehetséges, hogy Storage Explorer törölni az o-t a másolás befejezése előtt riginal fájlokat. Ez megoldottuk.

### <a name="new"></a>Új

* Az integrált AzCopy verziója frissítve lett a 10.1.0 verzióra.
* Mostantól a CTRL + cmd + R használatával frissítheti a jelenleg fókuszált szerkesztőt. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* A Azure Stack Storage API verziója 2017-04-17-re változott.
* A ADLS Gen2 hozzáférésének kezelése párbeszédablak mostantól a többi POSIX-engedélyhez hasonló eszközhöz hasonlóan megtartja a maszk szinkronizálását. A felhasználói felület arra is figyelmezteti, ha olyan módosítás történik, amely egy felhasználó vagy csoport engedélyeit a maszk határainak túllépése miatt teszi lehetővé. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* A AzCopy-feltöltések esetében az MD5-kivonat kiszámítására és beállítására szolgáló jelző már engedélyezve van. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Előzetes verziójú funkciók

* Az eszköz kódjának bejelentkezési folyamata mostantól előzetes verzióként érhető el. Az engedélyezéshez nyissa meg az "Előnézet" → "az eszköz kódjának bejelentkezni" című részt. Javasoljuk, hogy minden olyan felhasználó számára, aki üres bejelentkezési Windows-problémával rendelkezett, próbálja ki ezt a funkciót, mivel a bejelentkezés megbízhatóbb formája lehet.
* A AzCopy szolgáltatással integrált Storage Explorer jelenleg előzetes verzióban érhető el. Az engedélyezéshez nyissa meg az "Előnézet" → "a AzCopy használata a blob-feltöltéshez és a letöltéshez" lehetőséget. Az AzCopy-mel végzett blob-átviteleknek gyorsabbnak és nagyobb teljesítménynek kell lenniük.

### <a name="fixes"></a>Javítások

* A hozzáférési házirendek párbeszédpanel nem fog lejárati dátumot beállítani a nem lejáró tárolási hozzáférési házirendekben. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Bizonyos módosítások történtek a SAS létrehozása párbeszédpanelen, hogy a tárolt hozzáférési szabályzatok megfelelően legyenek használatban SAS létrehozásakor. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Ha egy nem 512 bájtra igazított fájlt próbál feltölteni egy oldal Blobba, Storage Explorer a rendszer egy további releváns hibát tesz elérhetővé. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* A megjelenített nevet használó blob-tároló másolása sikertelen lesz. A blob-tároló tényleges neve most már használatban van. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Ha olyan műveleteket próbál végrehajtani egy ADLS Gen2 mappában, amelyeknek a neve Unicode karaktereket tartalmaz, a művelet sikertelen lesz. Ekkor minden műveletnek működnie kell. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Ismert problémák

* A nem AzCopy Blobok letöltésének végrehajtásakor a nagyméretű fájlok MD5-hitelesítése nem történik meg. Ezt a Storage SDK hibája okozza. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* A RBAC használatakor Storage Explorer a tárolási erőforrások eléréséhez bizonyos felügyeleti rétegre vonatkozó engedélyeket igényel. További információért tekintse meg a [hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Sikertelen volt a ADLS Gen2 Blobok elérésének megkísérlése a proxy mögött.
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez frissítse a a csoport csomópontot. Tekintse meg a #537 további információt.
* Ha VS használ a Mac számítógépen, és minden eddiginél hozott létre egyéni AAD-konfigurációt, előfordulhat, hogy nem tudja jelentkezik be. A probléma megkerüléséhez tartalmának törlése ~ /. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* Azurite még nem teljes körűen megvalósítva összes Storage API-t. Emiatt lehetséges, hogy nem várt hibák vagy viselkedés fejlesztési tárterülettel Azurite használatakor.
* Ritka esetekben a fa fókusz előfordulhat, hogy elakadnak a gyors hozzáférés. Ezt követően a fókuszt, frissítse az összes is.
* Feltöltés a OneDrive-mappából egy NodeJS hibája miatt nem működik. Kijavítva a hiba, de Electron még nincs integrálva. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Az Azure Stack nem támogatja a következő funkciókat. Ezek a szolgáltatások használata közben az Azure Stack használatával próbál erőforrások váratlan hibákhoz vezethet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
   * ADLS Gen2
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linuxon futó Storage Explorer futtatásához bizonyos függőségeket először telepíteni kell. További információért tekintse meg a Storage Explorer [hibaelhárítási útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) .

## <a name="version-180"></a>1\.8.0-verzió
5/1/2019

### <a name="new"></a>Új

* Az integrált AzCopy verziója frissítve lett a 10.1.0 verzióra.
* Mostantól a CTRL + cmd + R használatával frissítheti a jelenleg fókuszált szerkesztőt. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* A Azure Stack Storage API verziója 2017-04-17-re változott.
* A ADLS Gen2 hozzáférésének kezelése párbeszédablak mostantól a többi POSIX-engedélyhez hasonló eszközhöz hasonlóan megtartja a maszk szinkronizálását. A felhasználói felület arra is figyelmezteti, ha olyan módosítás történik, amely egy felhasználó vagy csoport engedélyeit a maszk határainak túllépése miatt teszi lehetővé. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* A AzCopy-feltöltések esetében az MD5-kivonat kiszámítására és beállítására szolgáló jelző már engedélyezve van. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Előzetes verziójú funkciók

* Az eszköz kódjának bejelentkezési folyamata mostantól előzetes verzióként érhető el. Az engedélyezéshez nyissa meg az "Előnézet" → "az eszköz kódjának bejelentkezni" című részt. Javasoljuk, hogy minden olyan felhasználó számára, aki üres bejelentkezési Windows-problémával rendelkezett, próbálja ki ezt a funkciót, mivel a bejelentkezés megbízhatóbb formája lehet.
* A AzCopy szolgáltatással integrált Storage Explorer jelenleg előzetes verzióban érhető el. Az engedélyezéshez nyissa meg az "Előnézet" → "a AzCopy használata a blob-feltöltéshez és a letöltéshez" lehetőséget. Az AzCopy-mel végzett blob-átviteleknek gyorsabbnak és nagyobb teljesítménynek kell lenniük.

### <a name="fixes"></a>Javítások

* A hozzáférési házirendek párbeszédpanel nem fog lejárati dátumot beállítani a nem lejáró tárolási hozzáférési házirendekben. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Bizonyos módosítások történtek a SAS létrehozása párbeszédpanelen, hogy a tárolt hozzáférési szabályzatok megfelelően legyenek használatban SAS létrehozásakor. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Ha egy nem 512 bájtra igazított fájlt próbál feltölteni egy oldal Blobba, Storage Explorer a rendszer egy további releváns hibát tesz elérhetővé. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* A megjelenített nevet használó blob-tároló másolása sikertelen lesz. A blob-tároló tényleges neve most már használatban van. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Ha olyan műveleteket próbál végrehajtani egy ADLS Gen2 mappában, amelyeknek a neve Unicode karaktereket tartalmaz, a művelet sikertelen lesz. Ekkor minden műveletnek működnie kell. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Ismert problémák

* A nem AzCopy Blobok letöltésének végrehajtásakor a nagyméretű fájlok MD5-hitelesítése nem történik meg. Ezt a Storage SDK hibája okozza. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* A RBAC használatakor Storage Explorer a tárolási erőforrások eléréséhez bizonyos felügyeleti rétegre vonatkozó engedélyeket igényel. További információért tekintse meg a [hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Sikertelen volt a ADLS Gen2 Blobok elérésének megkísérlése a proxy mögött.
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez frissítse a a csoport csomópontot. Tekintse meg a #537 további információt.
* Ha VS használ a Mac számítógépen, és minden eddiginél hozott létre egyéni AAD-konfigurációt, előfordulhat, hogy nem tudja jelentkezik be. A probléma megkerüléséhez tartalmának törlése ~ /. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* Azurite még nem teljes körűen megvalósítva összes Storage API-t. Emiatt lehetséges, hogy nem várt hibák vagy viselkedés fejlesztési tárterülettel Azurite használatakor.
* Ritka esetekben a fa fókusz előfordulhat, hogy elakadnak a gyors hozzáférés. Ezt követően a fókuszt, frissítse az összes is.
* Feltöltés a OneDrive-mappából egy NodeJS hibája miatt nem működik. Kijavítva a hiba, de Electron még nincs integrálva. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Az Azure Stack nem támogatja a következő funkciókat. Ezek a szolgáltatások használata közben az Azure Stack használatával próbál erőforrások váratlan hibákhoz vezethet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
   * ADLS Gen2
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linuxon futó Storage Explorer futtatásához bizonyos függőségeket először telepíteni kell. További információért tekintse meg a Storage Explorer [hibaelhárítási útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) .

## <a name="version-170"></a>1\.7.0-ás verzió
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>Azure Storage Explorer 1.7.0 letöltése
- [A Windows 1.7.0 Azure Storage Explorer](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Mac Azure Storage Explorer 1.7.0](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.7.0 Linux rendszerhez](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Új

* Most már megváltoztathatja a tulajdonos és a tulajdonos csoportot ADLS Gen2 tároló, fájl vagy mappa hozzáférésének kezelésekor.
* Windows rendszeren a terméken belüli Storage Explorer frissítése mostantól növekményes telepítés. Ennek eredményeképpen gyorsabb frissítési élményre lehet. Ha a tiszta telepítést részesíti előnyben, töltse le a [telepítőt](https://azure.microsoft.com/features/storage-explorer/) , majd telepítse manuálisan. #1089

### <a name="preview-features"></a>Előzetes verziójú funkciók

* Az eszköz kódjának bejelentkezési folyamata mostantól előzetes verzióként érhető el. Az engedélyezéshez nyissa meg az "Előnézet" → "az eszköz kódjának bejelentkezni" című részt. Javasoljuk, hogy minden olyan felhasználó számára, aki üres bejelentkezési Windows-problémával rendelkezett, próbálja ki ezt a funkciót, mivel a bejelentkezés megbízhatóbb formája lehet. #938
* A AzCopy szolgáltatással integrált Storage Explorer jelenleg előzetes verzióban érhető el. Az engedélyezéshez nyissa meg az "Előnézet" → "a AzCopy használata a blob-feltöltéshez és a letöltéshez" lehetőséget. Az AzCopy-mel végzett blob-átviteleknek gyorsabbnak és nagyobb teljesítménynek kell lenniük.

### <a name="fixes"></a>Javítások

* Most kiválaszthatja azt a blob-típust, amelyet a AzCopy engedélyezésekor feltölthet. #1111
* Korábban, ha engedélyezte a statikus webhelyeket egy ADLS Gen2 Storage-fiókhoz, és azt névvel és kulccsal csatolta, Storage Explorer nem észlelte, hogy a hierarchikus névtér engedélyezve lett. Ez megoldottuk. #1081
* A blob-szerkesztőben a rendezés a hátralévő megőrzési napok vagy az állapot megszakadásával történhet. Ez megoldottuk. #1106
* A 1.5.0 után Storage Explorer már nem várta a kiszolgálóoldali másolatok befejeződését, mielőtt az Átnevezés vagy a másolás & beillesztéskor befejeződik a sikeres jelentéskészítés. Ez megoldottuk. #976
* A kísérleti AzCopy funkció használatakor a Másolás parancs vágólapra való másolása után másolt parancs nem mindig a saját futtatható. Ekkor a rendszer az átvitel manuális futtatásához szükséges összes parancsot átmásolja. #1079
* Korábban ADLS Gen2 Blobok nem voltak elérhetők, ha a proxy mögött volt. Ezt a Storage SDK által használt új hálózati könyvtár hibája okozta. A 1.7.0-ben a probléma enyhítésére tett kísérlet történt, de egyes felhasználók továbbra is láthatják a problémákat. A teljes javítás egy jövőbeli frissítésben jelenik meg. #1090
* A 1.7.0-ben a fájl mentése párbeszédpanel helyesen emlékszik az utolsó olyan helyre, ahol a fájlt mentette. #16
* A Tulajdonságok panelen egy Storage-fiók SKU-szintje jelenik meg a fiók típusaként. Ez megoldottuk. #654
* Időnként előfordulhat, hogy nem sikerült megszüntetni egy blob bérletét, még akkor is, ha helyesen adta meg a blob nevét. Ez megoldottuk. #1070

### <a name="known-issues"></a>Ismert problémák

* A RBAC használatakor Storage Explorer a tárolási erőforrások eléréséhez bizonyos felügyeleti rétegre vonatkozó engedélyeket igényel. További információért tekintse meg a [hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Sikertelen volt a ADLS Gen2 Blobok elérésének megkísérlése a proxy mögött.
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez frissítse a a csoport csomópontot. Tekintse meg a #537 további információt.
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez frissítse a a csoport csomópontot. További információ: #537.
* Ha VS használ a Mac számítógépen, és minden eddiginél hozott létre egyéni AAD-konfigurációt, előfordulhat, hogy nem tudja jelentkezik be. A probléma megkerüléséhez tartalmának törlése ~ /. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* Azurite még nem teljes körűen megvalósítva összes Storage API-t. Emiatt lehetséges, hogy nem várt hibák vagy viselkedés fejlesztési tárterülettel Azurite használatakor.
* Ritka esetekben a fa fókusz előfordulhat, hogy elakadnak a gyors hozzáférés. Ezt követően a fókuszt, frissítse az összes is.
* Feltöltés a OneDrive-mappából egy NodeJS hibája miatt nem működik. Kijavítva a hiba, de Electron még nincs integrálva. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Az Azure Stack nem támogatja a következő funkciókat. Ezek a szolgáltatások használata közben az Azure Stack használatával próbál erőforrások váratlan hibákhoz vezethet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)-es frissítést.
* A felhasználók számára az Ubuntu 14.04, szüksége lesz annak biztosítása érdekében a GCC naprakész – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>1\.6.2-es verzió
1/9/2019

### <a name="hotfixes"></a>Gyorsjavítások
* A 1.6.1-ben a ObjectId által ADLS Gen2 ACL-ek számára hozzáadott entitások, amelyek nem a felhasználók csoportként lettek hozzáadva. Mostantól csak a csoportok lesznek hozzáadva csoportként, és az olyan entitások, mint például a vállalati alkalmazások, andService lesznek hozzáadva felhasználóként. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Ha egy ADLS Gen2 Storage-fiók nem rendelkezett tárolóval, és a név és a kulcs névvel lett csatolva, akkor Storage Explorer nem fogja észlelni, hogy a Storage-fiók ADLS Gen2 volt. Ez megoldottuk. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* A 1.6.0-ben a másolás és beillesztés során fellépő ütközések nem kérik fel a megoldást. Ehelyett az ütköző másolat egyszerűen meghiúsul. Az első ütközés után a rendszer megkérdezi, hogyan szeretné megoldani. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* Az API-korlátozások miatt a ObjectIds összes érvényesítése le van tiltva a hozzáférés kezelése párbeszédpanelen. Az érvényesítés most csak a felhasználói UPN-ket veszi fel. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* A ADLS Gen2 hozzáférés kezelése párbeszédpanelen a csoport engedélyei nem módosíthatók. Ez megoldottuk. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* A ADLS Gen2 szerkesztőhöz a fogd és vidd feltöltési támogatás hozzáadva. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* A ADLS Gen2 fájlok és mappák Tulajdonságok párbeszédpanelének URL-tulajdonsága néha hiányzik a következőhöz: "/". Ez megoldottuk. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Ha egy ADLS Gen2 tárolóhoz, fájlhoz vagy mappához tartozó jelenlegi engedélyek beolvasása meghiúsul, akkor a hiba mostantól a tevékenység naplójában jelenik meg. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* A fájlok megnyitására szolgáló ideiglenes elérési út lerövidítve lett, így csökkenthető az esélye annak, hogy az elérési út hosszabb, mint a Windows MAX_PATH. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* A csatlakozás párbeszédpanel helyesen jelenik meg, ha nincsenek bejelentkezett felhasználók, és nincsenek csatolva erőforrások. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* A 1.6.0-ben a nem HNS blobok és fájlok tulajdonságainak mentése az összes tulajdonság értékét kódolja. Ez szükségtelen kódolási értéket eredményezett, amely csak ASCII-karaktereket tartalmaz. Az értékek mostantól csak akkor lesznek kódolva, ha nem ASCII-karaktereket tartalmaznak. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* A mappa nem HNS blob-tárolóba való feltöltése sikertelen lesz, ha a SAS használatban van, és az SAS nem rendelkezik olvasási engedéllyel. Ez megoldottuk. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* A AzCopy-átvitel megszakítása nem működött. Ez megoldottuk. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* A AzCopy akkor sikertelen, amikor egy ADLS Gen2 blob-tárolóból próbál meg letölteni egy mappát, ha a mappában szerepel a neve. Ez megoldottuk. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* A CosmosDB-szerkesztő megszakadt a 1.6.0-ben. Most már kijavítva van. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Új

* Mostantól a Storage Explorer használatával férhet hozzá a blob-adataihoz a [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409)-on keresztül. Ha be van jelentkezve, és Storage Explorer nem tudja lekérni a Storage-fiók kulcsait, akkor a rendszer egy OAuth-tokent használ a hitelesítéshez az adataival való interakció során.
* A Storage Explorer mostantól támogatja a ADLS Gen2 Storage-fiókokat. Ha Storage Explorer észleli, hogy a hierarchikus névtér engedélyezve van egy Storage-fiókhoz, a Storage-fiók neve mellett "(ADLS Gen2 Preview)" jelenik meg. A Storage Explorer képes megállapítani, hogy a hierarchikus névtér engedélyezve van-e a bejelentkezéskor, vagy ha a Storage-fiókot a név és a kulcs használatával csatlakoztatta. ADLS Gen2 Storage-fiókok esetében a következőket használhatja Storage Explorer:
  * Tárolók létrehozása és törlése
  * Tároló tulajdonságainak és engedélyeinek kezelése (bal oldali)
  * A tárolókban tárolt adatmegjelenítés és-böngészés
  * Új mappák létrehozása
  * Fájlok és mappák feltöltése, letöltése, átnevezése és törlése
  * A fájl-és mappa tulajdonságainak és engedélyeinek (jobb oldali) kezelése.
    
    Más jellemző blob-funkciók, például a Soft delete és a pillanatképek jelenleg nem érhetők el. Az engedélyek kezelése csak akkor érhető el, ha bejelentkezett. Emellett, ha ADLS Gen2 Storage-fiókban dolgozik, Storage Explorer a AzCopy fogja használni az összes feltöltéshez és letöltéshez, valamint az alapértelmezett értéket, hogy az összes művelethez a név és a kulcs hitelesítő adatait használja, ha van ilyen.
* Az erős felhasználói visszajelzéseket követően a break bérlet többször is felhasználható több blob bérletének egyszerre történő megszakítására.

### <a name="known-issues"></a>Ismert problémák

* ADLS Gen2 Storage-fiókból való letöltéskor, ha az átvitt fájlok egyike már létezik, akkor a AzCopy időnként összeomlik. Ezt a rendszer egy közelgő gyorsjavításban rögzíti.
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez frissítse a a csoport csomópontot. További információ: #537.
* Ha VS használ a Mac számítógépen, és minden eddiginél hozott létre egyéni AAD-konfigurációt, előfordulhat, hogy nem tudja jelentkezik be. A probléma megkerüléséhez tartalmának törlése ~ /. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* Azurite még nem teljes körűen megvalósítva összes Storage API-t. Emiatt lehetséges, hogy nem várt hibák vagy viselkedés fejlesztési tárterülettel Azurite használatakor.
* Ritka esetekben a fa fókusz előfordulhat, hogy elakadnak a gyors hozzáférés. Ezt követően a fókuszt, frissítse az összes is.
* Feltöltés a OneDrive-mappából egy NodeJS hibája miatt nem működik. Kijavítva a hiba, de Electron még nincs integrálva. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Az Azure Stack nem támogatja a következő funkciókat. Ezek a szolgáltatások használata közben az Azure Stack használatával próbál erőforrások váratlan hibákhoz vezethet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)-es frissítést.
* A felhasználók számára az Ubuntu 14.04, szüksége lesz annak biztosítása érdekében a GCC naprakész – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>1\.6.1-es verzió
12/18/2018

### <a name="hotfixes"></a>Gyorsjavítások
* Az API-korlátozások miatt a ObjectIds összes érvényesítése le van tiltva a hozzáférés kezelése párbeszédpanelen. Az érvényesítés most csak a felhasználói UPN-ket veszi fel. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* A ADLS Gen2 hozzáférés kezelése párbeszédpanelen a csoport engedélyei nem módosíthatók. Ez megoldottuk. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* A ADLS Gen2 szerkesztőhöz a fogd és vidd feltöltési támogatás hozzáadva. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* A ADLS Gen2 fájlok és mappák Tulajdonságok párbeszédpanelének URL-tulajdonsága néha hiányzik a következőhöz: "/". Ez megoldottuk. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Ha egy ADLS Gen2 tárolóhoz, fájlhoz vagy mappához tartozó jelenlegi engedélyek beolvasása meghiúsul, akkor a hiba mostantól a tevékenység naplójában jelenik meg. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* A fájlok megnyitására szolgáló ideiglenes elérési út lerövidítve lett, így csökkenthető az esélye annak, hogy az elérési út hosszabb, mint a Windows MAX_PATH. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* A csatlakozás párbeszédpanel helyesen jelenik meg, ha nincsenek bejelentkezett felhasználók, és nincsenek csatolva erőforrások. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* A 1.6.0-ben a nem HNS blobok és fájlok tulajdonságainak mentése az összes tulajdonság értékét kódolja. Ez szükségtelen kódolási értéket eredményezett, amely csak ASCII-karaktereket tartalmaz. Az értékek mostantól csak akkor lesznek kódolva, ha nem ASCII-karaktereket tartalmaznak. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* A mappa nem HNS blob-tárolóba való feltöltése sikertelen lesz, ha a SAS használatban van, és az SAS nem rendelkezik olvasási engedéllyel. Ez megoldottuk. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* A AzCopy-átvitel megszakítása nem működött. Ez megoldottuk. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* A AzCopy akkor sikertelen, amikor egy ADLS Gen2 blob-tárolóból próbál meg letölteni egy mappát, ha a mappában szerepel a neve. Ez megoldottuk. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* A CosmosDB-szerkesztő megszakadt a 1.6.0-ben. Most már kijavítva van. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Új

* Mostantól a Storage Explorer használatával férhet hozzá a blob-adataihoz a [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409)-on keresztül. Ha be van jelentkezve, és Storage Explorer nem tudja lekérni a Storage-fiók kulcsait, akkor a rendszer egy OAuth-tokent használ a hitelesítéshez az adataival való interakció során.
* A Storage Explorer mostantól támogatja a ADLS Gen2 Storage-fiókokat. Ha Storage Explorer észleli, hogy a hierarchikus névtér engedélyezve van egy Storage-fiókhoz, a Storage-fiók neve mellett "(ADLS Gen2 Preview)" jelenik meg. A Storage Explorer képes megállapítani, hogy a hierarchikus névtér engedélyezve van-e a bejelentkezéskor, vagy ha a Storage-fiókot a név és a kulcs használatával csatlakoztatta. ADLS Gen2 Storage-fiókok esetében a következőket használhatja Storage Explorer:
  * Tárolók létrehozása és törlése
  * Tároló tulajdonságainak és engedélyeinek kezelése (bal oldali)
  * A tárolókban tárolt adatmegjelenítés és-böngészés
  * Új mappák létrehozása
  * Fájlok és mappák feltöltése, letöltése, átnevezése és törlése
  * A fájl-és mappa tulajdonságainak és engedélyeinek (jobb oldali) kezelése.
    
    Más jellemző blob-funkciók, például a Soft delete és a pillanatképek jelenleg nem érhetők el. Az engedélyek kezelése csak akkor érhető el, ha bejelentkezett. Emellett, ha ADLS Gen2 Storage-fiókban dolgozik, Storage Explorer a AzCopy fogja használni az összes feltöltéshez és letöltéshez, valamint az alapértelmezett értéket, hogy az összes művelethez a név és a kulcs hitelesítő adatait használja, ha van ilyen.
* Az erős felhasználói visszajelzéseket követően a break bérlet többször is felhasználható több blob bérletének egyszerre történő megszakítására.

### <a name="known-issues"></a>Ismert problémák

* ADLS Gen2 Storage-fiókból való letöltéskor, ha az átvitt fájlok egyike már létezik, akkor a AzCopy időnként összeomlik. Ezt a rendszer egy közelgő gyorsjavításban rögzíti.
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez frissítse a a csoport csomópontot. További információ: #537.
* Ha VS használ a Mac számítógépen, és minden eddiginél hozott létre egyéni AAD-konfigurációt, előfordulhat, hogy nem tudja jelentkezik be. A probléma megkerüléséhez tartalmának törlése ~ /. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* Azurite még nem teljes körűen megvalósítva összes Storage API-t. Emiatt lehetséges, hogy nem várt hibák vagy viselkedés fejlesztési tárterülettel Azurite használatakor.
* Ritka esetekben a fa fókusz előfordulhat, hogy elakadnak a gyors hozzáférés. Ezt követően a fókuszt, frissítse az összes is.
* Feltöltés a OneDrive-mappából egy NodeJS hibája miatt nem működik. Kijavítva a hiba, de Electron még nincs integrálva. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Az Azure Stack nem támogatja a következő funkciókat. Ezek a szolgáltatások használata közben az Azure Stack használatával próbál erőforrások váratlan hibákhoz vezethet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)-es frissítést.
* A felhasználók számára az Ubuntu 14.04, szüksége lesz annak biztosítása érdekében a GCC naprakész – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>1\.6.0 verziója
2018. 12. 05.

### <a name="new"></a>Új

* Mostantól a Storage Explorer használatával férhet hozzá a blob-adataihoz a [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409)-on keresztül. Ha be van jelentkezve, és Storage Explorer nem tudja lekérni a Storage-fiók kulcsait, akkor a rendszer egy OAuth-tokent használ a hitelesítéshez az adataival való interakció során.
* A Storage Explorer mostantól támogatja a ADLS Gen2 Storage-fiókokat. Ha Storage Explorer észleli, hogy a hierarchikus névtér engedélyezve van egy Storage-fiókhoz, a Storage-fiók neve mellett "(ADLS Gen2 Preview)" jelenik meg. A Storage Explorer képes megállapítani, hogy a hierarchikus névtér engedélyezve van-e a bejelentkezéskor, vagy ha a Storage-fiókot a név és a kulcs használatával csatlakoztatta. ADLS Gen2 Storage-fiókok esetében a következőket használhatja Storage Explorer:
  * Tárolók létrehozása és törlése
  * Tároló tulajdonságainak és engedélyeinek kezelése (bal oldali)
  * A tárolókban tárolt adatmegjelenítés és-böngészés
  * Új mappák létrehozása
  * Fájlok és mappák feltöltése, letöltése, átnevezése és törlése
  * A fájl-és mappa tulajdonságainak és engedélyeinek (jobb oldali) kezelése.
    
    Más jellemző blob-funkciók, például a Soft delete és a pillanatképek jelenleg nem érhetők el. Az engedélyek kezelése csak akkor érhető el, ha bejelentkezett. Emellett, ha ADLS Gen2 Storage-fiókban dolgozik, Storage Explorer a AzCopy fogja használni az összes feltöltéshez és letöltéshez, valamint az alapértelmezett értéket, hogy az összes művelethez a név és a kulcs hitelesítő adatait használja, ha van ilyen.
* Az erős felhasználói visszajelzéseket követően a break bérlet többször is felhasználható több blob bérletének egyszerre történő megszakítására.

### <a name="known-issues"></a>Ismert problémák

* ADLS Gen2 Storage-fiókból való letöltéskor, ha az átvitt fájlok egyike már létezik, akkor a AzCopy időnként összeomlik. Ezt a rendszer egy közelgő gyorsjavításban rögzíti.
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez frissítse a a csoport csomópontot. További információ: #537.
* Ha VS használ a Mac számítógépen, és minden eddiginél hozott létre egyéni AAD-konfigurációt, előfordulhat, hogy nem tudja jelentkezik be. A probléma megkerüléséhez tartalmának törlése ~ /. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* Azurite még nem teljes körűen megvalósítva összes Storage API-t. Emiatt lehetséges, hogy nem várt hibák vagy viselkedés fejlesztési tárterülettel Azurite használatakor.
* Ritka esetekben a fa fókusz előfordulhat, hogy elakadnak a gyors hozzáférés. Ezt követően a fókuszt, frissítse az összes is.
* Feltöltés a OneDrive-mappából egy NodeJS hibája miatt nem működik. Kijavítva a hiba, de Electron még nincs integrálva. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Az Azure Stack nem támogatja a következő funkciókat. Ezek a szolgáltatások használata közben az Azure Stack használatával próbál erőforrások váratlan hibákhoz vezethet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)-es frissítést.
* A felhasználók számára az Ubuntu 14.04, szüksége lesz annak biztosítása érdekében a GCC naprakész – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>Verzió 1.5.0
10/29 vagy 2018.

### <a name="new"></a>Új

* Most már használhatja a [AzCopy v10 (előzetes verzió)](https://github.com/Azure/azure-storage-azcopy) használatát a Blobok feltöltéséhez és letöltéséhez. Ez a funkció engedélyezéséhez válassza a "Kísérleti" menü, és kattintson a "Használata az AzCopy a továbbfejlesztett Blob feltöltése és letöltése". Ha engedélyezve van, az AzCopy fogja használni a következő esetekben:
   * Töltse fel a blob-tárolók, akár az eszköztár fájlokat és mappákat, vagy húzza.
   * Letölti a mappákat és fájlokat, vagy az eszköztár vagy helyi menüjében.

* Ezenkívül az AzCopy használata esetén:
   * A vágólapra az átadás végrehajtására szolgáló AzCopy-parancs másolásával. Egyszerűen kattintson a "Másolás AzCopy parancs vágólapra" a tevékenységnaplóban.
   * A blob-szerkesztő feltöltése után manuálisan frissítenie kell.
   * A fájlok hozzáfűzési blobokhoz való feltöltése nem támogatott, a VHD-fájlok pedig blobként lesznek feltöltve, és az összes többi fájl is fel lesz töltve.
   * A feltöltés vagy a letöltés során felmerülő hibák és ütközések addig nem lesznek felszínben, amíg a feltöltés vagy a letöltés be nem fejeződik.

Végül az AzCopy használata a fájlmegosztások támogatása hamarosan elérhető a jövőben.
* Storage Explorer Electron 2.0.11 verziót használ.
* Használhatatlanná tévő bérleteket is most már csak hajtható végre egy blob egy időben. Ezenkívül rendelkezik az a blob nevét, amelynek meg bontja bérleti meg. Ez a változás azért történt, hogy csökkentse a bérlet véletlen megszakításának valószínűségét, különösen a virtuális gépek esetében. #394
* Ha minden eddiginél bejelentkezési problémák, most megpróbálhat alaphelyzetbe állítása hitelesítési. Nyissa meg a "Súgó" menü, és kattintson az "Új" Ez a funkció eléréséhez. #419

### <a name="fix"></a>Javítás

* Erős felhasználói visszajelzéseket, miután az alapértelmezett emulátor csomópont újra engedélyezve lett. Továbbra is hozzáadhat további emulátor kapcsolatok keresztül a Csatlakozás párbeszédpanel, de ha az emulátor az alapértelmezett portok használatára van konfigurálva az "Emulátor * alapértelmezett portok" csomópont "Helyi és csatolt/Storage-fiókok" is használható. #669
* Storage Explorer többé nem tesztszabályzatokat állíthat a blob metaadatértékeket rendelkező kezdő vagy záró szóközt. #760
* A "Bejelentkezés" gomb mindig engedélyezték a Csatlakozás párbeszédpanel azonos oldalain. Most már tiltva, ha szükséges. #761
* A gyors hozzáférés már nem generál hibát a konzolon a gyors hozzáférés elemek hozzáadásakor.

### <a name="known-issues"></a>Ismert problémák

* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez frissítse a a csoport csomópontot. További információ: #537.
* Ha VS használ a Mac számítógépen, és minden eddiginél hozott létre egyéni AAD-konfigurációt, előfordulhat, hogy nem tudja jelentkezik be. A probléma megkerüléséhez tartalmának törlése ~ /. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* Azurite még nem teljes körűen megvalósítva összes Storage API-t. Emiatt lehetséges, hogy nem várt hibák vagy viselkedés fejlesztési tárterülettel Azurite használatakor.
* Ritka esetekben a fa fókusz előfordulhat, hogy elakadnak a gyors hozzáférés. Ezt követően a fókuszt, frissítse az összes is.
* Feltöltés a OneDrive-mappából egy NodeJS hibája miatt nem működik. Kijavítva a hiba, de Electron még nincs integrálva. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Az Azure Stack nem támogatja a következő funkciókat. Ezek a szolgáltatások használata közben az Azure Stack használatával próbál erőforrások váratlan hibákhoz vezethet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)-es frissítést.
* A felhasználók számára az Ubuntu 14.04, szüksége lesz annak biztosítása érdekében a GCC naprakész – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>Verzió 1.4.4
10/15/2018.

### <a name="hotfixes"></a>Gyorsjavítások
* Az Azure Resource Management API verziója vissza lett állítva az Azure US government-felhasználók blokkolásának feloldására. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Betöltés pörgettyű most már használ a CSS-animációk Rövidítse le a Storage Explorer által használt GPU. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Új
* Külső erőforrás-mellékleteket, például a SAS-kapcsolatok és emulátorok, jelentősen javult. Most a következőket teheti:
   * Testre szabhatja a csatolni kívánt erőforrás megjelenített neve. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Különböző portok használata több helyi emulátor csatolása. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Csatolt erőforrások hozzáadása a gyorselérési eszköztáron. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer mostantól támogatja a helyreállítható törlés. A következőket teheti:
   * A helyreállítható törlés házirend konfigurálásához kattintson a jobb gombbal a tárfiók Blob-tárolók csomóponton.
   * Helyreállíthatóan törölt megtekintése blobok a Blob-szerkesztőben kiválasztásával "aktív és a blobok törlése" mellett a navigációs sávban a legördülő listában.
   * Helyreállíthatóan törölt BLOB törlésének visszavonása.

### <a name="fixes"></a>Javítások
* A "Konfigurálja a CORS-beállítások" művelet már nem elérhető a Premium Storage-fiókok, mert a prémium szintű Storage-fiókok nem támogatják a CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Most már rendelkezésre áll egy SAS-hez csatolt szolgáltatások közös hozzáférésű Jogosultságkód tulajdonsága. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Az "Alapértelmezett hozzáférési szint beállítása" művelet már elérhető a Blob és a GPV2 Storage-fiókok, amely gyors hozzáférést van rögzítve. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Egyes esetekben a Storage Explorer megjelenítéséhez a klasszikus tárfiókok meghiúsul. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Ismert problémák
* Emulátory systému, például az Azure Storage Emulator vagy Azurite, használatakor kell figyelni a kapcsolatokat, az alapértelmezett porton őket. Ellenkező esetben Storage Explorer nem képes csatlakozni hozzájuk.
* Ha VS használ a Mac számítógépen, és minden eddiginél hozott létre egyéni AAD-konfigurációt, előfordulhat, hogy nem tudja jelentkezik be. A probléma megkerüléséhez tartalmának törlése ~ /. IdentityService/AadConfigurations. Ha ez nem oldja fel a tiltást, kérjük, véleményezze a [problémát](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite még nem teljes körűen megvalósítva összes Storage API-t. Emiatt lehetséges, hogy nem várt hibák vagy viselkedés fejlesztési tárterülettel Azurite használatakor.
* Ritka esetekben a fa fókusz előfordulhat, hogy elakadnak a gyors hozzáférés. Ezt követően a fókuszt, frissítse az összes is.
* Feltöltés a OneDrive-mappából egy NodeJS hibája miatt nem működik. Kijavítva a hiba, de Electron még nincs integrálva.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is egy csatolt Azure Stack tárfiókok alatt jelenik meg.
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)-es frissítést.
* A felhasználók számára az Ubuntu 14.04, szüksége lesz annak biztosítása érdekében a GCC naprakész – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>Verzió 1.4.3
10/11/2018.

### <a name="hotfixes"></a>Gyorsjavítások
* Az Azure Resource Management API verziója vissza lett állítva az Azure US government-felhasználók blokkolásának feloldására. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Betöltés pörgettyű most már használ a CSS-animációk Rövidítse le a Storage Explorer által használt GPU. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Új
* Külső erőforrás-mellékleteket, például a SAS-kapcsolatok és emulátorok, jelentősen javult. Most a következőket teheti:
   * Testre szabhatja a csatolni kívánt erőforrás megjelenített neve. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Különböző portok használata több helyi emulátor csatolása. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Csatolt erőforrások hozzáadása a gyorselérési eszköztáron. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer mostantól támogatja a helyreállítható törlés. A következőket teheti:
   * A helyreállítható törlés házirend konfigurálásához kattintson a jobb gombbal a tárfiók Blob-tárolók csomóponton.
   * Helyreállíthatóan törölt megtekintése blobok a Blob-szerkesztőben kiválasztásával "aktív és a blobok törlése" mellett a navigációs sávban a legördülő listában.
   * Helyreállíthatóan törölt BLOB törlésének visszavonása.

### <a name="fixes"></a>Javítások
* A "Konfigurálja a CORS-beállítások" művelet már nem elérhető a Premium Storage-fiókok, mert a prémium szintű Storage-fiókok nem támogatják a CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Most már rendelkezésre áll egy SAS-hez csatolt szolgáltatások közös hozzáférésű Jogosultságkód tulajdonsága. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Az "Alapértelmezett hozzáférési szint beállítása" művelet már elérhető a Blob és a GPV2 Storage-fiókok, amely gyors hozzáférést van rögzítve. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Egyes esetekben a Storage Explorer megjelenítéséhez a klasszikus tárfiókok meghiúsul. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Ismert problémák
* Emulátory systému, például az Azure Storage Emulator vagy Azurite, használatakor kell figyelni a kapcsolatokat, az alapértelmezett porton őket. Ellenkező esetben Storage Explorer nem képes csatlakozni hozzájuk.
* Ha VS használ a Mac számítógépen, és minden eddiginél hozott létre egyéni AAD-konfigurációt, előfordulhat, hogy nem tudja jelentkezik be. A probléma megkerüléséhez tartalmának törlése ~ /. IdentityService/AadConfigurations. Ha ez nem oldja fel a tiltást, kérjük, véleményezze a [problémát](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite még nem teljes körűen megvalósítva összes Storage API-t. Emiatt lehetséges, hogy nem várt hibák vagy viselkedés fejlesztési tárterülettel Azurite használatakor.
* Ritka esetekben a fa fókusz előfordulhat, hogy elakadnak a gyors hozzáférés. Ezt követően a fókuszt, frissítse az összes is.
* Feltöltés a OneDrive-mappából egy NodeJS hibája miatt nem működik. Kijavítva a hiba, de Electron még nincs integrálva.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is egy csatolt Azure Stack tárfiókok alatt jelenik meg.
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)-es frissítést.
* A felhasználók számára az Ubuntu 14.04, szüksége lesz annak biztosítása érdekében a GCC naprakész – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>1\.4.2 verziója
09/24/2018.

### <a name="hotfixes"></a>Gyorsjavítások
* Az Azure Resource Management API verziójának frissítése 2018-07-01-re az új Azure Storage-fiókok támogatásának hozzáadásához. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Új
* Külső erőforrás-mellékleteket, például a SAS-kapcsolatok és emulátorok, jelentősen javult. Most a következőket teheti:
   * Testre szabhatja a csatolni kívánt erőforrás megjelenített neve. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Különböző portok használata több helyi emulátor csatolása. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Csatolt erőforrások hozzáadása a gyorselérési eszköztáron. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer mostantól támogatja a helyreállítható törlés. A következőket teheti:
   * A helyreállítható törlés házirend konfigurálásához kattintson a jobb gombbal a tárfiók Blob-tárolók csomóponton.
   * Helyreállíthatóan törölt megtekintése blobok a Blob-szerkesztőben kiválasztásával "aktív és a blobok törlése" mellett a navigációs sávban a legördülő listában.
   * Helyreállíthatóan törölt BLOB törlésének visszavonása.

### <a name="fixes"></a>Javítások
* A "Konfigurálja a CORS-beállítások" művelet már nem elérhető a Premium Storage-fiókok, mert a prémium szintű Storage-fiókok nem támogatják a CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Most már rendelkezésre áll egy SAS-hez csatolt szolgáltatások közös hozzáférésű Jogosultságkód tulajdonsága. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Az "Alapértelmezett hozzáférési szint beállítása" művelet már elérhető a Blob és a GPV2 Storage-fiókok, amely gyors hozzáférést van rögzítve. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Egyes esetekben a Storage Explorer megjelenítéséhez a klasszikus tárfiókok meghiúsul. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Ismert problémák
* Emulátory systému, például az Azure Storage Emulator vagy Azurite, használatakor kell figyelni a kapcsolatokat, az alapértelmezett porton őket. Ellenkező esetben Storage Explorer nem képes csatlakozni hozzájuk.
* Ha VS használ a Mac számítógépen, és minden eddiginél hozott létre egyéni AAD-konfigurációt, előfordulhat, hogy nem tudja jelentkezik be. A probléma megkerüléséhez tartalmának törlése ~ /. IdentityService/AadConfigurations. Ha ez nem oldja fel a tiltást, kérjük, véleményezze a [problémát](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite még nem teljes körűen megvalósítva összes Storage API-t. Emiatt lehetséges, hogy nem várt hibák vagy viselkedés fejlesztési tárterülettel Azurite használatakor.
* Ritka esetekben a fa fókusz előfordulhat, hogy elakadnak a gyors hozzáférés. Ezt követően a fókuszt, frissítse az összes is.
* Feltöltés a OneDrive-mappából egy NodeJS hibája miatt nem működik. Kijavítva a hiba, de Electron még nincs integrálva.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is egy csatolt Azure Stack tárfiókok alatt jelenik meg.
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)-es frissítést.
* A felhasználók számára az Ubuntu 14.04, szüksége lesz annak biztosítása érdekében a GCC naprakész – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>1\.4.1 verzió
08/28/2018.

### <a name="hotfixes"></a>Gyorsjavítások
* Az alkalmazás első indításakor a Storage Explorer nem tudta létrehozni a bizalmas adatok titkosításához használt kulcs. Emiatt problémák gyors hozzáférés használata esetén és a kapcsolódó erőforrásokat. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Ha a fiók nem volt szükség az MFA az otthoni bérlő számára, de néhány más bérlők esetében tette, a Storage Explorer az előfizetések listája nem lehet lenne. Most az ilyen egy fiókkal történő bejelentkezés után a Tártallózó ekkor megkérdezi, írja be újra a hitelesítő adatait, és végezze el az MFA. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* Storage Explorer nem tudta csatolni az erőforrásokat az Azure Germany és Azure US Government. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Ha bejelentkezett, két fiókot, amely ugyanazt az e-mail címet rendelkezett, Storage Explorer időnként meghiúsul a fanézetben megtekintheti az erőforrások megjelenítéséhez. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* Lassabb Windows-gépeken a kezdőképernyőjén néha igénybe jelentős mennyiségű időt jelennek meg. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* A Csatlakozás párbeszédpanel tűnik, még akkor is, ha voltak a csatolt fiókhoz vagy szolgáltatáshoz. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Új
* Külső erőforrás-mellékleteket, például a SAS-kapcsolatok és emulátorok, jelentősen javult. Most a következőket teheti:
   * Testre szabhatja a csatolni kívánt erőforrás megjelenített neve. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Különböző portok használata több helyi emulátor csatolása. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Csatolt erőforrások hozzáadása a gyorselérési eszköztáron. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer mostantól támogatja a helyreállítható törlés. A következőket teheti:
   * A helyreállítható törlés házirend konfigurálásához kattintson a jobb gombbal a tárfiók Blob-tárolók csomóponton.
   * Helyreállíthatóan törölt megtekintése blobok a Blob-szerkesztőben kiválasztásával "aktív és a blobok törlése" mellett a navigációs sávban a legördülő listában.
   * Helyreállíthatóan törölt BLOB törlésének visszavonása.

### <a name="fixes"></a>Javítások
* A "Konfigurálja a CORS-beállítások" művelet már nem elérhető a Premium Storage-fiókok, mert a prémium szintű Storage-fiókok nem támogatják a CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Most már rendelkezésre áll egy SAS-hez csatolt szolgáltatások közös hozzáférésű Jogosultságkód tulajdonsága. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Az "Alapértelmezett hozzáférési szint beállítása" művelet már elérhető a Blob és a GPV2 Storage-fiókok, amely gyors hozzáférést van rögzítve. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Egyes esetekben a Storage Explorer megjelenítéséhez a klasszikus tárfiókok meghiúsul. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Ismert problémák
* Emulátory systému, például az Azure Storage Emulator vagy Azurite, használatakor kell figyelni a kapcsolatokat, az alapértelmezett porton őket. Ellenkező esetben Storage Explorer nem képes csatlakozni hozzájuk.
* Ha VS használ a Mac számítógépen, és minden eddiginél hozott létre egyéni AAD-konfigurációt, előfordulhat, hogy nem tudja jelentkezik be. A probléma megkerüléséhez tartalmának törlése ~ /. IdentityService/AadConfigurations. Ha ez nem oldja fel a tiltást, kérjük, véleményezze a [problémát](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite még nem teljes körűen megvalósítva összes Storage API-t. Emiatt lehetséges, hogy nem várt hibák vagy viselkedés fejlesztési tárterülettel Azurite használatakor.
* Ritka esetekben a fa fókusz előfordulhat, hogy elakadnak a gyors hozzáférés. Ezt követően a fókuszt, frissítse az összes is.
* Feltöltés a OneDrive-mappából egy NodeJS hibája miatt nem működik. Kijavítva a hiba, de Electron még nincs integrálva.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is egy csatolt Azure Stack tárfiókok alatt jelenik meg.
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)-es frissítést.
* A felhasználók számára az Ubuntu 14.04, szüksége lesz annak biztosítása érdekében a GCC naprakész – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>Verzió 1.3.0
07/09/2018.

### <a name="new"></a>Új
* Mostantól támogatott a statikus webhelyek által használt $web tárolók elérése. Ez lehetővé teszi, hogy könnyedén töltheti fel és kezelheti a fájlok és mappák, a webhely által használt. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* MacOS rendszeren alkalmazássávon az átszervezett már rendelkezik. A változások a következők: a Fájl menü, helyi legfontosabb változások és számos új parancsokat az alkalmazás menüjében. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* Az Azure US Governmentbe való bejelentkezéshez használt szolgáltatói végpont a következőre változott: https://login.microsoftonline.us/
* Kisegítő lehetőségek: Ha egy képernyőolvasó aktív, most már a billentyűzetnavigációt együttműködik elemek megjelenítéséhez a jobb oldali használt táblák. Használhatja a nyílbillentyűkkel navigálhat a sorok és oszlopok, adjon meg alapértelmezett műveletek, a helyi menü kulcs egy elem, a helyi menü megnyitásához és a Shift vagy a többszörös kijelölés vezérlőelem meghívásához. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Javítások
*  Az egyes gépek alárendelt indítása hosszú ideig is tart. Ez akkor fordulhat elő, amikor egy "gyermek folyamata nem tudott időben elindítani" hibát jelent. Indítsa el a gyermek a folyamat a várt időn most 20-ról értékre nőtt 90 másodperc alatt. Ha a probléma továbbra is érint, adjon fűzni a csatolt GitHub-problémát. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* Amely az adott nem rendelkezik olvasási engedéllyel a SAS használatával, amikor nem lehetett feltölteni a nagyméretű blobok. A feltöltés logika csapatmunkában vesz részt ebben a forgatókönyvben módosították. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* A nyilvános hozzáférés szintje beállítása egy tároló el kell távolítani az összes hozzáférési házirendeket, és ez fordítva is igaz. Nyilvános hozzáférés szintje és a hozzáférési szabályzatok megmaradnak, vagy a kettő beállításakor. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* A Tulajdonságok párbeszédpanelen "AccessTierChangeTime" byl zkrácen. Ez megoldottuk. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* A "Microsoft Azure Storage Explorer –" előtag hiányzott az új könyvtár létrehozása párbeszédpanel. Ez megoldottuk. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Kisegítő lehetőségek: Az entitás hozzáadása párbeszédpanelen keresse meg a VoiceOver használatakor bonyolult volt. Fejlesztések történtek-e. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Kisegítő lehetőségek: A háttérszíne, a műveletek és a Tulajdonságok ablak összecsukása és bontsa ki a gombjára a Kontrasztos fekete témában hasonló felhasználói felületi vezérlők inkonzisztens volt. A szín megváltozott. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Kisegítő lehetőségek: Kontrasztos fekete témában a fókuszt, ha az "X" gomb a Tulajdonságok párbeszédpanelen nem volt látható. Ez megoldottuk. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Kisegítő lehetőségek: A műveletek és a Tulajdonságok lapon lett(ek) subpar képernyő olvasó élmény szabványosította számos aria-értékét. A hiányzó aria-értékek hozzáadása megtörtént. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Kisegítő lehetőségek: Összecsukott fa csomópontjait a bal oldali nem folyamatban van a kapott egy aria-kibontva értéke hamis. Ez megoldottuk. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Ismert problémák
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez frissítse a a csoport csomópontot. További információért tekintse meg [ezt a problémát](https://github.com/Microsoft/AzureStorageExplorer/issues/537) .
* Ha VS használ a Mac számítógépen, és minden eddiginél hozott létre egyéni AAD-konfigurációt, előfordulhat, hogy nem tudja jelentkezik be. A probléma megkerüléséhez tartalmának törlése ~ /. IdentityService/AadConfigurations. Ha ez nem oldja fel a tiltást, kérjük, véleményezze a [problémát](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite még nem teljes körűen megvalósítva összes Storage API-t. Emiatt lehetséges, hogy nem várt hibák vagy viselkedés fejlesztési tárterülettel Azurite használatakor.
* Ritka esetekben a fa fókusz előfordulhat, hogy elakadnak a gyors hozzáférés. Ezt követően a fókuszt, frissítse az összes is.
* Feltöltés a OneDrive-mappából egy NodeJS hibája miatt nem működik. Kijavítva a hiba, de Electron még nincs integrálva.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Az Azure Stack nem támogatja a következő funkciókat, és a váratlan hibákat eredményezhet próbál használni őket az Azure Stack használata során:
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)-es frissítést.
* A felhasználók számára az Ubuntu 14.04, szüksége lesz annak biztosítása érdekében a GCC naprakész – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>1\.2.0-s vagy annál újabb verzió
06/12/2018.

### <a name="new"></a>Új
* Ha a Storage Explorer nem tudja betölteni az előfizetések a bérlők csak egy részhalmazát, majd sikeresen betöltve előfizetésekkel megjelenik együtt egy hibaüzenet kifejezetten a bérlők számára, amely nem sikerült a. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* Windows, a frissítés érhető el, ha most kiválaszthatja a "Frissítéséhez a Bezárás". Ezzel a beállítással kivétele történik, amikor a telepítő a frissítés a Storage Explorer bezárása után fog futni. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* A fájlmegosztás pillanatképének megtekintésekor a visszaállítási pillanatkép hozzá lett adva a fájlmegosztás-szerkesztő helyi menüjéhez. [#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* A várólista törlése gomb most mindig engedélyezve van. [#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* Az AD FS az Azure Stackhez bejelentkezés támogatása lett újra engedélyezni. Az Azure Stack 1804 vagy újabb verziójára szükség. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Javítások
* Ha, amelynek a neve lett egy másik fájlmegosztásra ugyanabban a tárfiókban lévő meg egy fájlmegosztás pillanatképekre vonatkozó, majd a fájlmegosztás pillanatképeinek is lesz feltüntetve. Ez a probléma megoldódott. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* Csatlakoztatott SAS-n keresztül, ha hiba történt a fájl visszaállítása a fájl megosztási pillanatképből eredményez. Ez a probléma megoldódott. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* Egy blob pillanatképének megtekintésekor a pillanatkép előléptetése művelet lett engedélyezve, amikor az alap blob és a egy egységes pillanatképet jelölt ki. A művelet csak engedélyezve van egy egységes pillanatképet választásakor. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Ha egy feladat (például blob letöltése) lett elindítva, és később nem sikerült, ez lenne nem automatikusan próbálja meg újra mindaddig, amíg egy másik, azonos típusú feladat indítása. Az összes feladat most kell automatikus újrapróbálkozás, függetlenül attól, hogy hány feladatok rendelkezik várólistára.
* Szerkesztők megnyitni az újonnan létrehozott blob-tárolók a GPV2 és Blob Storage-fiókok nem rendelkezett egy hozzáférési szint oszlop. Ez a probléma megoldódott. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* Egy hozzáférési szint oszlop néha nem jelenik meg, ha egy tárfiókot, vagy a blobtároló SAS-n keresztül lett csatolva. Az oszlopban mindig jelennek meg, de ha nincsenek hozzáférési szint beállítása üres értékkel. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* Az újonnan feltöltött blokkblob hozzáférési szintjének beállítását le lett tiltva. Ez a probléma megoldódott. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* A "Tartsa lapon nyissa meg" gombra a billentyűzet használatával lett meghívva, ha a billentyűzetfókusz elveszett lenne. A fókusz most, hogy megtartotta nyissa meg a lap mehet. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* Egy lekérdezést a Lekérdezésszerkesztő, a VoiceOver volt nem engedélyezi az aktuális operátor használható leírását. Most már több beszédes legyen. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* A rendszer nem leíró a tördelés hivatkozások a különböző szerkesztők. Lehet kifejezőbb megváltoztak. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* Entitás hozzáadása párbeszédpanelen VoiceOver volt nem bejelentése milyen oszlop a bemeneti elem része volt. Elem leírása most már szerepel az aktuális oszlop neve. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Választógombok és a jelölőnégyzetek nem volt látható a fókuszban lévő szegély. Ez a probléma megoldódott. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Ismert problémák
* Emulátory systému, például az Azure Storage Emulator vagy Azurite, használatakor kell figyelni a kapcsolatokat, az alapértelmezett porton őket. Ellenkező esetben Storage Explorer nem képes csatlakozni hozzájuk.
* Ha VS használ a Mac számítógépen, és minden eddiginél hozott létre egyéni AAD-konfigurációt, előfordulhat, hogy nem tudja jelentkezik be. A probléma megkerüléséhez tartalmának törlése ~ /. IdentityService/AadConfigurations. Ha ez nem oldja fel a tiltást, kérjük, véleményezze a [problémát](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite még nem teljes körűen megvalósítva összes Storage API-t. Emiatt lehetséges, hogy nem várt hibák vagy viselkedés fejlesztési tárterülettel Azurite használatakor.
* Ritka esetekben a fa fókusz előfordulhat, hogy elakadnak a gyors hozzáférés. Ezt követően a fókuszt, frissítse az összes is.
* Feltöltés a OneDrive-mappából egy NodeJS hibája miatt nem működik. Kijavítva a hiba, de Electron még nincs integrálva.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is egy csatolt Azure Stack tárfiókok alatt jelenik meg.
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)-es frissítést.
* A felhasználók számára az Ubuntu 14.04, szüksége lesz annak biztosítása érdekében a GCC naprakész – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>1\.1.0-s verzió
2018/05/09

### <a name="new"></a>Új
* Storage Explorer mostantól támogatja a Azurite. Megjegyzés: az alapértelmezett fejlesztési végpontjaira szoftveresen kötött áll a kapcsolat Azurite.
* Storage Explorer mostantól támogatja az elérési szint csak Blob és a GPV2-Tárfiókok esetében. További információ a hozzáférési szintekről [.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)
* Kezdési idő már nem szükséges, SAS készítésekor.

### <a name="fixes"></a>Javítások
* Az előfizetések US Government-fiókok beolvasása megszakadt. Ez a probléma megoldódott. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* Hozzáférési szabályzatok lejárati ideje nem lett megfelelően folyamatban van mentve. Ez a probléma megoldódott. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* Ha egy SAS URL-cím létrehozása egy elem a tárolóban, az elem nevét volt nem hozzáfűzésekor az URL-címre. Ez a probléma megoldódott. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* SAS létrehozása esetén, amelyek az elmúlt a lejárati idő néha lenne az alapértelmezett érték. Ez a Storage Explorer használata az elmúlt alapértékei kezdési és lejárati idő miatt volt. Most már minden alkalommal, amikor megnyitja az SAS párbeszédpanel, az alapértelmezett értékek új készlet jön létre. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* Másolás a Storage-fiókok között, ha egy 24 órás SAS jön létre. Ha a másolási tartott 24 óránál hosszabb, a Másolás meghiúsul. Megnöveltük az SAS által az elmúlt 1 hét meghiúsul egy lejárt SAS miatt másolatot esélyének csökkentése érdekében. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* Bizonyos tevékenységek a "Mégse"gombra kattintva nem minden esetben működik. Ez a probléma megoldódott. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* Bizonyos tevékenységek az átviteli sebesség hibás volt. Ez a probléma megoldódott. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* A Nézet menü "Előző" helyesírás-hibás volt. Ez most már megfelelően írta be. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* A Windows telepítő utolsó lapján kellett a Next (Tovább) gombra. A "Befejezés" gombra a megváltozott. [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* TAB billentyű fókusz nem látható a párbeszédpanelek gombjain, a hibrid kapcsolat fekete téma használata esetén. Most már látható. [#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* A kis-és az "Automatikus feloldása" műveletek a tevékenységnaplóban hibás volt. Már megfelelő. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* Egy táblából egy entitás törlésekor megerősítését kéri a párbeszédpanelen megjelenik a hibajelző ikon. A párbeszédpanel már használja egy figyelmeztető ikon. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Ismert problémák
* Ha VS használ a Mac számítógépen, és minden eddiginél hozott létre egyéni AAD-konfigurációt, előfordulhat, hogy nem tudja jelentkezik be. A probléma megkerüléséhez tartalmának törlése ~ /. IdentityService/AadConfigurations. Ha ez nem oldja fel a tiltást, kérjük, véleményezze a [problémát](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite még nem teljes körűen megvalósítva összes Storage API-t. Emiatt lehetséges, hogy nem várt hibák vagy viselkedés fejlesztési tárterülettel Azurite használatakor.
* Ritka esetekben a fa fókusz előfordulhat, hogy elakadnak a gyors hozzáférés. Ezt követően a fókuszt, frissítse az összes is.
* Feltöltés a OneDrive-mappából egy NodeJS hibája miatt nem működik. Kijavítva a hiba, de Electron még nincs integrálva.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is egy csatolt Azure Stack tárfiókok alatt jelenik meg.
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)-es frissítést.
* A felhasználók számára az Ubuntu 14.04, szüksége lesz annak biztosítása érdekében a GCC naprakész – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>1\.0.0-s verziójának
04/16 és 2018.

### <a name="new"></a>Új
* Lehetővé teszi, hogy a Storage Explorer az azonos fióktároló adatokként a Visual Studio 2017 fejlett hitelesítést. Ez a funkció használatához szüksége lesz a fiókok újra bejelentkezni, és újra beállítani a szűrt előfizetések.
* AAD által támogatott Azure Stack-fiókok esetében Storage Explorer-kezelő most beolvassa Azure Stack-előfizetést, amikor a "Cél Azure Stack" engedélyezve van. Már nincs szüksége egy egyéni bejelentkezési környezetet hozhat létre.
* Gyorsabb navigációt parancsikonokat bővült. Ezek közé tartoznak a különböző panelek váltását, és a szerkesztők közötti áthelyezése. Tekintse meg a Nézet menü További részletekért.
* Storage Explorer visszajelzés most lakik a Githubon. Ha a bal alsó sarokban található visszajelzés gombra kattint, a [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues)gombra kattintva elérheti a problémák oldalát. Kérdéseit elküldheti a javaslatokat, problémák jelentése, kérdéseket tehet fel, vagy hagyja üresen a visszajelzés bármilyen más formájában.
* Ha SSL-tanúsítványokkal kapcsolatos problémákba ütközik, és nem találja a jogsértő tanúsítványt, most már elindíthatja a Storage Explorert a parancssorból a `--ignore-certificate-errors` jelzővel. Ez a jelző indításakor Storage Explorer figyelmen kívül hagyja az SSL-tanúsítvánnyal kapcsolatos hiba.
* Már létezik egy "Letöltés" lehetőséget a helyi menüben a blobok és fájlok elemek.
* Javított kisegítő lehetőségek és képernyőolvasó támogatása. Ha a kisegítő lehetőségek valamelyikét használja, további információért tekintse meg a [kisegítő lehetőségek dokumentációját](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-accessibility) .
* Storage Explorer Electron 1.8.3 használja

### <a name="breaking-changes"></a>Kompatibilitástörő változások
* Storage Explorer egy új hitelesítési tár változott. A kapcsolót, hogy a szalagtár részeként kell a fiókok újra bejelentkezni, és újra beállítani a szűrt előfizetések
* A bizalmas adatok titkosításához használt módszer megváltozott. Emiatt előfordulhat, hogy egyes kellene újra hozzáadni a gyors hozzáférés elemeket, illetve, hogy kapcsolt erőforrások kellene csatolni.

### <a name="fixes"></a>Javítások
* Néhány felhasználó proxyk mögött kell csoport blob feltöltések vagy letöltések szakította meg egy "nem oldható fel a(z)" hibaüzenet jelenik meg. Ez a probléma megoldódott.
* Ha be volt szükség egy közvetlen hivatkozást, és kattintson a "Bejelentkezés" üzenet a használatával egy üres párbeszédpanelt kellene felugró közben. Ez a probléma megoldódott.
* Linuxon, ha a Storage Explorer nem tudja elindítani egy GPU folyamat összeomlása miatt most értesíteni fogjuk az összeomlás előtt a szolgáltatás használatához a "--disable-gpu" kapcsolót, és a Storage Explorer fogja automatikusan újraindítja a kapcsoló engedélyezve van.
* Érvénytelen hozzáférési szabályzatok a hozzáférési szabályzatok párbeszédpanelen identitás nehéz volt. Érvénytelen hozzáférési szabályzat azonosítói most leírt pirosat a láthatóságot.
* A tevékenységnapló néha kellene tevékenység különböző részei közötti szóköz nagy területéhez. Ez a probléma megoldódott.
* A tábla Lekérdezésszerkesztő egy időbélyegző-záradék érvénytelen állapotban marad, és ezután módosítását kísérelte meg egy másik záradékban, ha a szerkesztő szeretné rögzíteni. A szerkesztő most visszaállítja a timestamp záradék utolsó érvényes állapotát egy másik záradék változás észlelésekor.
* A fanézetben a keresési lekérdezésben gépelés közben szüneteltetésekor kezdik meg a Keresés és fókusz lenne ellophatják a szövegmezőben. Most meg kell explicit módon kezdeni a keresést az "Enter" billentyűt lenyomásával, vagy kattintson a start Keresés gombra.
* A "Get közös hozzáférésű Jogosultságkód" parancsot bővítés néha le lesz tiltva, ha a jobb gombbal kattintva a fájlmegosztásban lévő fájl. Ez a probléma megoldódott.
* Az erőforrás facsomópont az aktuális keresés közben lett szűrve, ha Ön nem sikerült a erőforrás fába lapon és a nyílbillentyűkkel navigálhat az erőforrás-fa. Most ha a célzott erőforrás facsomópont rejtett, az első csomópontot az erőforrás-fa lesz automatikusan fordítani.
* Az extra elválasztó néha lenne az eszköztáron látható. Ez a probléma megoldódott.
* A webhely-navigációs szövegmező néha túlcsordulást. Ez a probléma megoldódott.
* A Blob- és fájlmegosztás szerkesztők néha folyamatosan frissíti, egyszerre sok fájl feltöltésekor. Ez a probléma megoldódott.
* A "Mappa statisztika" funkció nincs szerepük volt a fájlmegosztás pillanatképeinek-kezelés nézetben. Most már le lett tiltva.
* A Linux a Fájl menü fejeződött nem jelennek meg. Ez a probléma megoldódott.
* Mappa feltöltése egy fájlmegosztásba alapértelmezés szerint, ha csak a mappa tartalmának feltöltve. Most az alapértelmezett viselkedést, hogy a mappa tartalmának feltöltése a fájlmegosztás a megfelelő mappájába.
* Az eredménykészlet sorrendjét a több párbeszédpanelek gombok kellett lettek fordítva. Ez a probléma megoldódott.
* A kapcsolódó különböző biztonsági javításokat.

### <a name="known-issues"></a>Ismert problémák
* Ritka esetekben a fa fókusz előfordulhat, hogy elakadnak a gyors hozzáférés. Ezt követően a fókuszt, frissítse az összes is.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is egy csatolt Azure Stack tárfiókok alatt jelenik meg.
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)-es frissítést.
* A felhasználók számára az Ubuntu 14.04, szüksége lesz annak biztosítása érdekében a GCC naprakész – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>Verzió 0.9.6
2018-02-28

### <a name="fixes"></a>Javítások
* Probléma miatt nem sikerült a várt blobok és fájlok a szerkesztőben listázását. Ez a probléma megoldódott.
* Problémát az okozza, pillanatképnézet helytelenül megjelenítendő elemek közötti váltáskor. Ez a probléma megoldódott.

### <a name="known-issues"></a>Ismert problémák
* Storage Explorer nem támogatja az AD FS-fiókokat.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* A fiók beállítások panel jelenhet meg, hogy szeretne-e az előfizetések szűrése hitelesítő adatok újbóli.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is egy csatolt Azure Stack tárfiókok alatt jelenik meg.
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A felhasználók számára az Ubuntu 14.04, szüksége lesz annak biztosítása érdekében a GCC naprakész – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>Verzió 0.9.5
2018/02/06

### <a name="new"></a>Új

* Fájlmegosztások pillanatképeinek támogatása:
    * Hozzon létre, és a fájlmegosztások pillanatképeinek kezelése.
    * Könnyedén nézetváltás a fájlmegosztások pillanatképeinek között is.
    * Állítsa vissza a fájlok korábbi verzióit.
* Az Azure Data Lake Store előzetes verziós támogatást:
    * Csatlakozzon az ADLS-erőforrások több fiókban.
    * Csatlakozhat, és megoszthatja az ADLS-erőforrások ADL URI-k használatával.
    * Hajtsa végre az alapszintű fájl vagy mappa műveletek rekurzív módon.
    * A gyors hozzáférés PIN-kód egyes mappák.
    * Mappa statisztikái jelennek meg.

### <a name="fixes"></a>Javítások
* Indítási teljesítménnyel kapcsolatos fejlesztések.
* Különböző hibajavítások.

### <a name="known-issues"></a>Ismert problémák
* Storage Explorer nem támogatja az AD FS-fiókokat.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* A fiók beállítások panel jelenhet meg, hogy szeretne-e az előfizetések szűrése hitelesítő adatok újbóli.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is egy csatolt Azure Stack tárfiókok alatt jelenik meg.
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A felhasználók számára az Ubuntu 14.04, szüksége lesz annak biztosítása érdekében a GCC naprakész – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>0\.9.4 és 0.9.3-as verzió
2018-01-21

### <a name="new"></a>Új
* A meglévő Storage Explorer ablak lesz újra felhasználni mikor:
    * A Storage Explorerben jön létre közvetlen hivatkozások megnyitásakor.
    * Storage Explorer megnyitása a portálról.
    * Nyissa meg a Storage Explorer az Azure Storage a VS Code-bővítményében (hamarosan).
* A hozzáadott lehetőség új ablak megnyitása a Storage Explorer a Storage Explorerben.
    * A Windows van egy "Új ablakban" lehetőséget a Fájl menü alatt, és a helyi menüben, a tálcán.
    * A Mac van egy "Új ablakban" lehetőséget az alkalmazás menüpont alatt.

### <a name="fixes"></a>Javítások
* Egy biztonsági javítva. Az Önnek legkorábbi, 0.9.4 frissítsen.
* Régi tevékenységek is nem megfelelő alatt lettek törölve. Ez az érintett hosszabb ideig futó feladatok teljesítményét. Akkor most folyamatban törlődnek megfelelően.
* Műveletek, például nagy mennyiségű fájlok és könyvtárak alkalmanként okozna befagyasztani a Storage Explorerben. Az Azure-fájlmegosztások kérelmek most szabályozott rendszer erőforrás-használat korlátozásához.

### <a name="known-issues"></a>Ismert problémák
* Storage Explorer nem támogatja az AD FS-fiókokat.
* Billentyűparancsok a "Nézet Explorer" és "Nézet felügyeletének" kell lennie a Ctrl / Cmd + Shift + E, és a Ctrl / Cmd + Shift + A jelölik.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* A fiók beállítások panel jelenhet meg, hogy szeretne-e az előfizetések szűrése hitelesítő adatok újbóli.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is egy csatolt Azure Stack tárfiókok alatt jelenik meg.
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer --disable-gpu
    ```

* A felhasználók számára az Ubuntu 14.04, szüksége lesz annak biztosítása érdekében a GCC naprakész – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>Verzió 0.9.2
11/01/2017

### <a name="hotfixes"></a>Gyorsjavítások
* Váratlan adatok változásának voltak, attól függően, a helyi időzónát táblaentitások Edm.DateTime értékeit szerkesztése során. A szerkesztő használja egy egyszerű szöveges mezőben Edm.DateTime értékek pontos, konzisztens ad.
* A blobok nevére és kulcsára csatolt csoport feltöltése/letöltése nem indul el. Ez a probléma megoldódott.
* Korábban lenne Storage Explorer csak kérni fogja, hogy hitelesítse magát újra egy elavult fiókot, ha egy, vagy a fiók-előfizetések több lett kiválasztva. Most már a Tártallózó felkéri, még akkor is, ha a fiók teljes kiszűrte.
* A végpontok tartományát az Azure US Government hibás volt. Megoldottuk.
* Az Alkalmaz gombra a fiókok kezelése panelen néha nehéz kattintson volt. Ez már nem történjen.

### <a name="new"></a>Új
* Azure Cosmos DB előzetes verziós támogatást:
    * [Online dokumentáció](./cosmos-db/storage-explorer.md)
    * Adatbázisok és gyűjtemények létrehozása
    * Adatok kezelése
    * Lekérdezés, hozzon létre vagy dokumentumok törlése
    * Frissítse a tárolt eljárások, felhasználó által definiált függvények és eseményindítók
    * Kapcsolati karakterláncok használatával csatlakozhat, és az adatbázisok kezelése
* Javult a teljesítmény a sok kis méretű BLOB feltöltése/letöltése.
* "Az összes újra" művelet hozzáadva, ha hibákat észlel egy blob feltöltése vagy a blob letöltése csoport.
* Storage Explorer most szüneteltetheti a ismétlés során a blob feltöltése/letöltése, ha észleli a hálózati kapcsolat megszakadt. Ismétlés majd folytatni, lett, hogy a hálózati kapcsolat újbóli létesítése után.
* Képes hozzáadni "Zárja be az összes", "Bezárás mások" és a "Bezárás" lapon helyi menüjében.
* Storage Explorer mostantól a natív párbeszédpanelek és nativního kontextu menüket használja.
* Storage Explorer mostantól könnyebben elérhetővé vált. Fejlesztések a következők:
    * Továbbfejlesztett képernyőolvasó támogatása, a Windows NVDA, valamint a VoiceOver Mac gépen
    * Továbbfejlesztett kontrasztos témája
    * A bejárási és billentyűzettel billentyűzetfókuszt javítások

### <a name="fixes"></a>Javítások
* Ha próbált megnyitni vagy letölt egy blobot egy érvénytelen Windows nevére, a művelet sikertelen lesz. Storage Explorer mostantól észleli, ha egy blob neve érvénytelen, és kérje meg, ha szeretne kódolni, vagy hagyja ki a blob. Storage Explorer is észlelni fogja, ha a fájl nevét úgy tűnik, hogy értéket kódolni kell, és kérje meg, ha kívánja dekódolni a feltöltés előtt.
* Blob feltöltésekor a blob céltárolón-szerkesztő lenne néha nem megfelelően frissülnek. Ez a probléma megoldódott.
* Kapcsolati karakterláncok és a SAS URI-k számos formája támogatása közleményében szerepelt. Az összes ismert problémák foglalkoztak egyelőre küldjön visszajelzést, ha problémák merülnek fel további.
* A frissítési értesítés: a 0.9.0 az egyes felhasználók megszakadt. Ezt a problémát megjavítottuk, és a hiba által érintettek számára [a Storage Explorer legújabb](https://azure.microsoft.com/features/storage-explorer/)verzióját manuálisan is letöltheti.

### <a name="known-issues"></a>Ismert problémák
* Storage Explorer nem támogatja az AD FS-fiókokat.
* Billentyűparancsok a "Nézet Explorer" és "Nézet felügyeletének" kell lennie a Ctrl / Cmd + Shift + E, és a Ctrl / Cmd + Shift + A jelölik.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* A fiók beállítások panel jelenhet meg, hogy szeretne-e az előfizetések szűrése hitelesítő adatok újbóli.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is egy csatolt Azure Stack tárfiókok alatt jelenik meg.
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer --disable-gpu
    ```

* A felhasználók számára az Ubuntu 14.04, szüksége lesz annak biztosítása érdekében a GCC naprakész – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>0\.9.1 és 0.9.0-s verziója
10/20/2017
### <a name="new"></a>Új
* Azure Cosmos DB előzetes verziós támogatást:
    * [Online dokumentáció](./cosmos-db/storage-explorer.md)
    * Adatbázisok és gyűjtemények létrehozása
    * Adatok kezelése
    * Lekérdezés, hozzon létre vagy dokumentumok törlése
    * Frissítse a tárolt eljárások, felhasználó által definiált függvények és eseményindítók
    * Kapcsolati karakterláncok használatával csatlakozhat, és az adatbázisok kezelése
* Javult a teljesítmény a sok kis méretű BLOB feltöltése/letöltése.
* "Az összes újra" művelet hozzáadva, ha hibákat észlel egy blob feltöltése vagy a blob letöltése csoport.
* Storage Explorer most szüneteltetheti a ismétlés során a blob feltöltése/letöltése, ha észleli a hálózati kapcsolat megszakadt. Ismétlés majd folytatni, lett, hogy a hálózati kapcsolat újbóli létesítése után.
* Képes hozzáadni "Zárja be az összes", "Bezárás mások" és a "Bezárás" lapon helyi menüjében.
* Storage Explorer mostantól a natív párbeszédpanelek és nativního kontextu menüket használja.
* Storage Explorer mostantól könnyebben elérhetővé vált. Fejlesztések a következők:
    * Továbbfejlesztett képernyőolvasó támogatása, a Windows NVDA, valamint a VoiceOver Mac gépen
    * Továbbfejlesztett kontrasztos témája
    * A bejárási és billentyűzettel billentyűzetfókuszt javítások

### <a name="fixes"></a>Javítások
* Ha próbált megnyitni vagy letölt egy blobot egy érvénytelen Windows nevére, a művelet sikertelen lesz. Storage Explorer mostantól észleli, ha egy blob neve érvénytelen, és kérje meg, ha szeretne kódolni, vagy hagyja ki a blob. Storage Explorer is észlelni fogja, ha a fájl nevét úgy tűnik, hogy értéket kódolni kell, és kérje meg, ha kívánja dekódolni a feltöltés előtt.
* Blob feltöltésekor a blob céltárolón-szerkesztő lenne néha nem megfelelően frissülnek. Ez a probléma megoldódott.
* Kapcsolati karakterláncok és a SAS URI-k számos formája támogatása közleményében szerepelt. Az összes ismert problémák foglalkoztak egyelőre küldjön visszajelzést, ha problémák merülnek fel további.
* A frissítési értesítés: a 0.9.0 az egyes felhasználók megszakadt. Ezt a problémát megjavítottuk, és a hiba által érintettek számára [a Storage Explorer legújabb](https://azure.microsoft.com/features/storage-explorer/) verzióját manuálisan is letöltheti.

### <a name="known-issues"></a>Ismert problémák
* Storage Explorer nem támogatja az AD FS-fiókokat.
* Billentyűparancsok a "Nézet Explorer" és "Nézet felügyeletének" kell lennie a Ctrl / Cmd + Shift + E, és a Ctrl / Cmd + Shift + A jelölik.
* Ha az Azure Stack célozza, bizonyos fájlokat, a hozzáfűző blobok feltöltése meghiúsulhat.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* A fiók beállítások panel jelenhet meg, hogy szeretne-e az előfizetések szűrése hitelesítő adatok újbóli.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is egy csatolt Azure Stack tárfiókok alatt jelenik meg.
* A Storage Explorer által használt Electron rendszerhéj rendelkezik néhány GPU (grafikai processzor) a hardveres gyorsítás ütközik. Ha a Storage Explorer üres (üres) főablakot jelenít meg, akkor a `--disable-gpu` kapcsoló hozzáadásával kipróbálhatja a parancssorból a Storage Explorer indítását, és letilthatja a GPU-gyorsítást:

    ```
    ./StorageExplorer --disable-gpu
    ```

* A felhasználók számára az Ubuntu 14.04, szüksége lesz annak biztosítása érdekében a GCC naprakész – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>Verzió 0.8.16
8/21/2017

### <a name="new"></a>Új
* Amikor megnyit egy blob, a Storage Explorer felkéri, hogy a letöltött fájl feltöltése, ha változás észlelésekor
* Azure Stack bejelentkezési élmény
* Feltöltése/letöltése sok kis méretű fájl egy időben teljesítményének növelése


### <a name="fixes"></a>Javítások
* Néhány blob esetében válassza a "replace" során egy feltöltési ütközés néha eredményez a feltöltés folyamatban van az újraindítása.
* 0\.8.15 verzióban feltöltések lenne néha – megrekedésének 99 %-ában.
* Ha a fájlok feltöltése egy fájlmegosztásba, ha úgy döntött, hogy egy könyvtárat, amely nem történt meg feltölteni, még létezik, a feltöltés sikertelen lesz.
* Storage Explorer helytelenül lett létrehozni a közös hozzáférésű jogosultságkódok és a tábla lekérdezések időbélyegzőket.


### <a name="known-issues"></a>Ismert problémák
* Név és kulcs kapcsolati karakterlánca nem működik. Ez a következő kiadás javítja. Addig is használhat csatolása nevére és kulcsára.
* Próbál nyissa meg a fájlt a Windows fájl neve érvénytelen, ha a letöltés egy fájl nem található az hibát eredményez.
* "Mégse gombra" kattint egy feladatot, miután ezt a feladatot megszakítja egy ideig is eltarthat. Ez az az Azure Storage-csomópont kódtár korlátozása.
* Miután befejezte a blob feltöltése, a lapon, ami a feltöltés kezdeményezett frissülnek. Ez előző viselkedés eltérő, és is eredményezheti, hogy biztosan megfeleljen az a tároló gyökérkönyvtárába visszalépteti.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt, majd szüksége lesz ahhoz, hogy a Storage Explorer felejtse el, hogy döntést kell újraindítani.
* A fiók beállítások panel jelenhet meg, hogy szeretne-e az előfizetések szűrése hitelesítő adatok újbóli.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is egy csatolt Azure Stack tárfiókok alatt jelenik meg.
* A felhasználók számára az Ubuntu 14.04, szüksége lesz annak biztosítása érdekében a GCC naprakész – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 felhasználójához GConf telepíteni kell, – a következő parancsok futtatásával, és indítsa újra a gép erre:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Verzió 0.8.14
06/22/2017

### <a name="new"></a>Új

* Számos fontos biztonsági frissítések kihasználása érdekében 1.7.2-es verzióra Electron
* Most már gyorsan elérheti a online – hibaelhárítási útmutató a Súgó menü
* Storage Explorer hibaelhárítási [útmutató][2]
* [Útmutató][3] Azure stack-előfizetéshez való csatlakozáshoz

### <a name="known-issues"></a>Ismert problémák

* Gombok a megerősítő párbeszédpanelen delete mappa nem regisztrálja a kattintásokat Linux rendszeren. megkerülő megoldásként használja az ENTER billentyűt.
* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt kell ahhoz, hogy rendelkezik a Storage Explorer elfelejtette a döntést újraindítása
* A fájlok feltöltése egy időben és blobok legfeljebb 3 csoportok kellene hibát okozhat
* A fiók beállítások panel jelenhet meg, hogy szeretne-e a hitelesítő adatok újbóli annak érdekében, hogy az előfizetések szűrése
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is egy csatolt Azure Stack tárfiókok alatt jelenik meg.
* Ubuntu 14.04-es telepítés van szüksége a gcc-verziójához, vagy frissített – frissítésére lépéseket alább:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Verzió 0.8.13
05/12/2017

#### <a name="new"></a>Új

* Storage Explorer hibaelhárítási [útmutató][2]
* [Útmutató][3] Azure stack-előfizetéshez való csatlakozáshoz

#### <a name="fixes"></a>Javítások

* Kijavítva: A fájl feltöltése kellett kívüli elég memória hiba okozza, nagy valószínűséggel
* Kijavítva: Is most jelentkezik be a PIN-kód/intelligens kártya
* Kijavítva: a Megnyitás a portálon mostantól működik az Azure China 21Vianet, az Azure Germany, az Azure US government és a Azure Stack
* Kijavítva: Mappa feltöltése a blob-tárolóba, során "Szabálytalan műveletet" hiba néha fel
* Kijavítva: Az összes kijelölése le lett tiltva a pillanatképek kezelése során
* Kijavítva: Az alap blob metaadatait előfordulhat, hogy első felül után annak pillanatképei tulajdonságainak megtekintése

#### <a name="known-issues"></a>Ismert problémák

* Ha úgy dönt, hogy a megfelelő PIN-kód/intelligens kártya tanúsítványt kell ahhoz, hogy rendelkezik a Storage Explorer elfelejtette a döntést újraindítása
* Vagy nagyítása, míg a nagyítási szint rövid ideig alaphelyzetbe állíthatja az alapértelmezett szintre
* A fájlok feltöltése egy időben és blobok legfeljebb 3 csoportok kellene hibát okozhat
* A fiók beállítások panel jelenhet meg, hogy szeretne-e a hitelesítő adatok újbóli annak érdekében, hogy az előfizetések szűrése
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is egy csatolt Azure Stack tárfiókok alatt jelenik meg.
* Ubuntu 14.04-es telepítés van szüksége a gcc-verziójához, vagy frissített – frissítésére lépéseket alább:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>0\.8.12 és 0.8.11 és 0.8.10 verzió
04/07/2017

#### <a name="new"></a>Új

* Storage Explorer automatikusan bezárul amikor frissítést telepít, a frissítési értesítés
* A gyors hozzáférés a helyben a gyakran használt erőforrásokat az használata hatékonyabb tartalmaz
* A Blob-tároló-szerkesztőben megtekintheti melyik virtuális gép tartozik egy bérelt blob
* Most már összecsukhatja a bal oldali panel
* Felderítés most le egyidejűleg fog futni
* A Blob-tárolóba, a fájlmegosztás és a tábla szerkesztők statisztika segítségével megtekintheti a erőforrás vagy a kiválasztott mérete
* Most jelentkezzen be az Azure Active Directory (AAD) alapú az Azure Stack-fiókokat is.
* Most már feltöltheti archív fájlokban több mint 32 MB méretű prémium szintű storage-fiókok
* Továbbfejlesztett kisegítő lehetőségek támogatása
* Mostantól megbízható alap-64 kódolású X. 509 SSL-tanúsítványokat is hozzáadhat a Szerkesztés –&gt; SSL-tanúsítványok –&gt; importálási tanúsítványok

#### <a name="fixes"></a>Javítások

* Kijavítva: egy fiók hitelesítő adatainak frissítése, miután a fanézetben lenne néha nem frissül automatikusan
* Kijavítva: SAS-emulátor üzenetsorok és táblák létrehozása eredményez olyan URL-címe érvénytelen
* Kijavítva: prémium szintű storage-fiókok mostantól kibővíthetők a proxy engedélyezve van
* Kijavítva: az Alkalmaz gombra a felügyeleti fiókok lapon nem működne Ha a kiválasztott fiók 1 vagy 0
* Kijavítva: ütközések feloldásának igénylő blobok feltöltése meghiúsulhat - 0.8.11 rögzített
* Kijavítva: küldjön visszajelzést volt működésképtelenné 0.8.11 - 0.8.12 rögzített

#### <a name="known-issues"></a>Ismert problémák

* 0\.8.10 termékcsaládba, szüksége lesz az összes a hitelesítő adatok frissítése.
* Vagy nagyítása, míg a nagyítási szint rövid ideig alaphelyzetbe állíthatja az alapértelmezett szintre.
* A fájlok feltöltése egy időben és blobok legfeljebb 3 csoportok kellene hibákat okozhat.
* A fiók beállítások panel jelenhet meg, hogy szeretne-e a hitelesítő adatok újbóli előfizetések szűrése érdekében.
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. Minden egyéb tulajdonságok és metaadatok a blobok, fájlok és entitások egy átnevezése közben megőrződnek.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is egy csatolt Azure Stack tárfiókok alatt jelenik meg.
* Ubuntu 14.04-es telepítés van szüksége a gcc-verziójához, vagy frissített – frissítésére lépéseket alább:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>0\.8.9 és 0.8.8 verzió
02/23/2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>Új

* Storage Explorer 0.8.9 automatikusan letöltheti a legújabb frissítések.
* A gyorsjavítás: a portál használatával létrehozott SAS URI-t egy tárfiók csatolása eredményez hiba.
* Most már létrehozása, kezelése és blobpillanatképeket előléptetése.
* Most már bejelentkezhet az Azure China 21Vianet, az Azure Germany és az USA Azure Government-fiókjaiba.
* Mostantól módosíthatja a nagyítási szint. A Nézet menü Nagyítás, kicsinyítés és Nagyítás alaphelyzetbe a beállításokat használják.
* Unicode-karaktereket felhasználói metaadatok a blobok és fájlok mostantól támogatottak.
* Javított kisegítő lehetőségek.
* A következő verzió kibocsátási megjegyzések a frissítési értesítés is megtekinthetők. A jelenlegi kiadási megjegyzések a Súgó menüben is megtekintheti.

#### <a name="fixes"></a>Javítások

* Kijavítva: a verziószám megfelelően megjelenik a Vezérlőpulton a Windows
* Kijavítva: keresés már nem legfeljebb 50 000 csomópontok
* Kijavítva: feltöltése egy fájlmegosztásba örökre is, ha a célkönyvtárban duplikátum már nem létezik
* Kijavítva: jobb stabilitás hosszú feltöltéseket és letöltések

#### <a name="known-issues"></a>Ismert problémák

* Vagy nagyítása, míg a nagyítási szint rövid ideig alaphelyzetbe állíthatja az alapértelmezett szintre.
* A gyors hozzáférés csak az előfizetés-alapú elemek működik. Ebben a kiadásban nem támogatottak a helyi erőforrások vagy kulccsal vagy SAS-jogkivonat keresztül csatlakoztatott erőforrásokhoz.
* A gyors hozzáférés a néhány másodperc alatt keresse meg a célként megadott erőforrás hány erőforrásokhoz rendelkezik függően vehet igénybe.
* A fájlok feltöltése egy időben és blobok legfeljebb 3 csoportok kellene hibákat okozhat.

12/16/2016
### <a name="version-087"></a>Verzió 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Új

* Kiválaszthatja, hogyan egy frissítés, a letöltési vagy a másolási munkamenet elején a tevékenységek ablak az ütközések feloldása
* A kurzort egy lapján megtekintheti a storage-erőforrások teljes elérési útja
* Egy fülre kattintva, szinkronizálja a hellyel, a bal oldali navigációs panelen

#### <a name="fixes"></a>Javítások

* Kijavítva: Storage Explorer jelenleg egy megbízható alkalmazások Mac gépen
* Kijavítva: Ubuntu 14.04 újra támogatott
* Kijavítva: Néha fiók felhasználói felület hozzáadása tokenkódot előfizetések betöltésekor
* Kijavítva: Néha nem minden tárolási erőforrások felsorolt a bal oldali navigációs panelen
* Kijavítva: A művelet ablaktáblában néha megjelenített üres műveletek
* Kijavítva: Most megőrzött a legutóbbi lezárt munkamenetet a az ablak mérete
* Kijavítva: Megnyithatja a helyi menü használatával ugyanarra az erőforrásra vonatkozó több lap

#### <a name="known-issues"></a>Ismert problémák

* A gyors hozzáférés csak az előfizetés-alapú elemek működik. Helyi erőforrások vagy kulcs- vagy SAS-jogkivonat összekapcsolnia erőforrások nem támogatottak ebben a kiadásban
* A gyors hozzáférés igénybe vehet, keresse meg a célként megadott erőforrás hány erőforrásokhoz rendelkezik függően néhány másodperccel
* A fájlok feltöltése egy időben és blobok legfeljebb 3 csoportok kellene hibát okozhat
* Ezt követően a körülbelül 50 000 csomópont - keresés keresési kezeli teljesítmény kimaradására vagy okozhat a nem kezelt kivétel
* Az első alkalommal a Storage Explorer használatával a MacOS-gépeken megjelenhet több utasításokat a felhasználó a kulcslánc hozzáférési engedélyt kér. Javasoljuk, hogy mindig választja, az így a rendszer nem jelennek meg újra

11/18/2016
### <a name="version-086"></a>Verzió 0.8.6

#### <a name="new"></a>Új

* Most már egyszerűen megtalálható a leggyakrabban használt szolgáltatásokat, a gyors hozzáférés PIN-kód
* Most megnyithatja több szerkesztők különböző lapokon. Egyetlen kattintással megnyitható egy ideiglenes lap; Kattintson ide duplán egy állandó lap megnyitásához. Az ideiglenes lapra is kattinthat, hogy az állandó lapot jelenítse meg
* Hajtottunk végre észlelhető teljesítménybeli, és a stabilitás tölt fel, és letölti, különösen a nagy méretű fájlok gyors gépeken
* Üres "virtuális" mappák most már létrehozhatja az blob-tárolók
* Újra bevezettük az új kibővített karakterláncrészletet keresés a hatókörös keresésre, így most már a keresés során két lehetősége van:
    * Globális keresés – csak adja meg egy keresett kifejezést a keresési szövegmezőbe
    * Hatókörös keresésre - kattintson a Nagyító ikont mellett a csomópontot, majd az elérési út végéhez adja hozzá a keresési kifejezés vagy kattintson a jobb gombbal és "Keresés a itt"
* Hozzáadtuk a különböző témákat: világos (alapértelmezett), sötét, kontrasztos fekete és kontrasztos fehér. Válassza a Szerkesztés –&gt; témák lehetőséget a rájuk vonatkozó beállítások módosításához
* Módosíthatja a Blobok és fájlok tulajdonságai
* Mostantól támogatjuk a kódolt (base64) és megmaradnak kódolatlan üzenetsorbeli üzenetek esetén
* Linuxon, egy 64 bites operációs rendszer már szükséges. Az ebben a kiadásban csak támogatja 16.04.1 64 bites Ubuntu LTS
* Frissítettük az embléma!

#### <a name="fixes"></a>Javítások

* Kijavítva: Képernyőn fagyasztási kapcsolatos problémák
* Kijavítva: Fokozott biztonság
* Kijavítva: Néha ismétlődő csatolt fiókok fog megjelenni
* Kijavítva: Egy nem definiált tartalomtípus rendelkező sikerült kivételt hoz létre
* Kijavítva: Az üres táblát a lekérdezés Panel megnyitása nem sikerült
* Kijavítva: Változik, a keresés hibák
* Kijavítva: Növelni az erőforrások számát betöltődnek 50 és 100 kattint a "Továbbiak betöltése"
* Kijavítva: Első futtatásakor, ha egy fiók bejelentkezett, most kiválasztjuk az adott fiók összes előfizetés alapértelmezés szerint

#### <a name="known-issues"></a>Ismert problémák

* Ebben a kiadásban a Storage Explorer nem fut az Ubuntu 14.04
* Ugyanarra az erőforrásra vonatkozó több lap megnyitásához, ne folyamatosan kattintson ugyanazon az erőforráson futó. Kattintson egy másik erőforrás, és ezután lépjen vissza, és kattintson a egy másik lapon újra megnyitni az eredeti erőforrás
* A gyors hozzáférés csak az előfizetés-alapú elemek működik. Helyi erőforrások vagy kulcs- vagy SAS-jogkivonat összekapcsolnia erőforrások nem támogatottak ebben a kiadásban
* A gyors hozzáférés igénybe vehet, keresse meg a célként megadott erőforrás hány erőforrásokhoz rendelkezik függően néhány másodperccel
* A fájlok feltöltése egy időben és blobok legfeljebb 3 csoportok kellene hibát okozhat
* Ezt követően a körülbelül 50 000 csomópont - keresés keresési kezeli teljesítmény kimaradására vagy okozhat a nem kezelt kivétel

10/03/2016
### <a name="version-085"></a>Verzió 0.8.5

#### <a name="new"></a>Új

* Használhatják a portál által létrehozott SAS-kulcsok, Tárfiókok és erőforrások csatolása

#### <a name="fixes"></a>Javítások

* Kijavítva: néha a keresés közben versenyhelyzet miatt csomópontok nem kibontható válik
* Kijavítva: "HTTP használata" nem működik a fiók nevére és kulcsára Tárfiókokban való csatlakozáskor
* Kijavítva: SAS-kulcsok (kifejezetten portál által létrehozott kapcsolatok) hibaüzenetet ad vissza "záró perjellel"
* Kijavítva: table-importálási hibák elhárítása
    * Néha partíciókulccsal és sorkulccsal lettek visszaállítva
    * Nem sikerült beolvasni a "null" Partíciókulcsok

#### <a name="known-issues"></a>Ismert problémák

* Ezt követően a körülbelül 50 000 csomópont - keresés keresési leírók legyen negatív hatással a teljesítményre
* Az Azure Stack jelenleg nem támogatja fájlok, így megpróbálja kibontani a fájlok megjelenítése hiba

09/12/2016
### <a name="version-084"></a>Verzió 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Új

* Tárfiókok, tárolók, üzenetsorok, táblák vagy megosztási fájlmegosztások közvetlen hivatkozások létrehozása és a Windows és Mac OS - erőforrásokhoz való könnyű hozzáférést támogatja
* Keresse meg a blobtárolók, táblák, üzenetsorok, fájlmegosztások vagy a keresőmezőbe a storage-fiókok
* Most már csoportosíthatók záradékot a tábla a Lekérdezésszerkesztő
* Nevezze át és blobtárolók, fájlmegosztások, táblák, blobok, blob mappákat, fájlokat és könyvtárakat az SAS-hez csatolt fiókok és a tárolók másolja és illessze be
* Blobtárolók átnevezése, valamint másolására, és most fájlmegosztások őrizni tulajdonságok és metaadatok

#### <a name="fixes"></a>Javítások

* Kijavítva: nem szerkeszthető táblaentitások tartalmazó logikai vagy bináris tulajdonságok

#### <a name="known-issues"></a>Ismert problémák

* Ezt követően a körülbelül 50 000 csomópont - keresés keresési leírók legyen negatív hatással a teljesítményre

08/03/2016
### <a name="version-083"></a>Verzió 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Új

* Nevezze át a tárolók, a táblákat, a fájlmegosztások
* Lekérdezés-szerkesztő továbbfejlesztett
* Mentés és a lekérdezések betöltése
* Közvetlen hivatkozásokat a storage-fiókok vagy tárolók, üzenetsorok, táblák, vagy a fájlmegosztások megosztásához, és könnyedén az erőforrásokhoz hozzáférő (csak Windows - macOS támogatása hamarosan elérhető!)
* Kezelheti és CORS-szabályok konfigurálása

#### <a name="fixes"></a>Javítások

* Kijavítva: A Microsoft Accounts újbóli hitelesítés szükséges 8 – 12 óránként

#### <a name="known-issues"></a>Ismert problémák

* Egyes esetekben előfordulhat, hogy válaszol a felhasználói felület – során az ablak maximális segít a probléma megoldásához
* telepítés macOS szükség lehet emelt jogosultsági szintű
* Fiók beállítások panel jelenhet meg, hogy szeretne-e a hitelesítő adatok újbóli annak érdekében, hogy az előfizetések szűrése
* Fájlmegosztások, blob-tárolók és táblák átnevezése nem őrzi meg a metaadatok vagy egyéb tulajdonságok, például a fájlmegosztás kvótájának, a nyilvános hozzáférés szintje vagy a hozzáférési házirendek a tárolón
* Blobok (külön-külön vagy átnevezett blob-tárolóban) átnevezése nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások minden egyéb tulajdonsága és metaadatai megmaradnak az Átnevezés során
* Másolása, illetve az erőforrás átnevezése nem működik a fiók SAS-hez csatolt belül

07/07/2016
### <a name="version-082"></a>Verzió 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Új

* Storage-fiókok előfizetések; szerint vannak csoportosítva fejlesztési tárterülettel és a kulcs vagy SAS-n keresztül csatlakoztatott erőforrásokhoz jelennek meg (helyi és csatolt) csomópont alatt
* Kijelentkezés a fiókok közül az "Azure-fiók beállításai" panelen
* Proxybeállítások engedélyezheti és kezelheti a bejelentkezés konfigurálása
* Hozzon létre és az blob bérleteket
* Nyissa meg a blob-tárolók, üzenetsorok, táblák és fájlok egyetlen kattintással

#### <a name="fixes"></a>Javítások

* Kijavítva: .NET vagy Java-kódtárakat beszúrt üzenetsorbeli üzenetek vannak se megfelelően dekódovat a Base64 kódolású anyag
* Kijavítva: $metrics táblákat nem jelennek meg a Blob Storage-fiókok
* Kijavítva: táblák csomópont nem működik a helyi (fejlesztés) tároló

#### <a name="known-issues"></a>Ismert problémák

* telepítés macOS szükség lehet emelt jogosultsági szintű

06/15/2016
### <a name="version-080"></a>Verzió 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Új

* A fájl megosztási támogatási: megtekintése, feltöltése, letöltése, másolja a fájlokat és könyvtárakat, SAS URI-k (létrehozás és csatlakozás)
* Növelt felhasználói élmény a SAS URI-k vagy a fiók a tárolóhoz való kapcsolódás
* Tábla lekérdezési eredmények exportálása
* Tábla oszlop újra sorrendbe rendezés és a Testreszabás
* Megtekintés $logs blob-tárolók és a $metrics táblákat Tárfiókok esetében engedélyezett metrikákkal
* Továbbfejlesztett exportálása és importálása viselkedés, mostantól tartalmazza a tulajdonságérték-típus

#### <a name="fixes"></a>Javítások

* Kijavítva: fel- vagy nagyméretű blobok letöltéséhez eredményezhet hiányos feltöltések vagy letöltések
* Kijavítva: szerkesztése, hozzáadásával vagy importálásával numerikus karakterlánc-érték ("1") rendelkező entitás konvertálja a dupla
* Kijavítva: Nem lehet a helyi fejlesztési környezetben a tábla a csomópont kibontása

#### <a name="known-issues"></a>Ismert problémák

* $metrics táblákat nem láthatók a Blob Storage-fiókok
* Programozott módon hozzáadva üzenetsorbeli üzenetek nem jeleníthető meg megfelelően, ha az üzenetek kódolt Base64 kódolással

05/17/2016
### <a name="version-07201605090"></a>Verzió 0.7.20160509.0

#### <a name="new"></a>Új

* Jobb hibakezelés alkalmazás összeomlik

#### <a name="fixes"></a>Javítások

* Kijavítva a hiba, információs sáv üzenetek néha nem jelennek meg, amikor szükség volt a bejelentkezési hitelesítő adataival

#### <a name="known-issues"></a>Ismert problémák

* Táblázatok: olyan entitás hozzáadása, szerkesztése vagy importálása, amely nem egyértelmű numerikus értékkel rendelkezik (például "1" vagy "1,0"), és a felhasználó `Edm.String`ként próbálja elküldeni, az érték pedig az ügyfél API-EDM fog visszatérni. Double

03/31/2016

### <a name="version-07201603250"></a>Verzió 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Új

* Táblázat a támogatási: megtekintése lekérdezése, importálása és exportálása az entitások CRUD-műveletek
* Várólista-támogatás: megtekintését, hozzáadását, dequeueing üzenetek
* Storage-fiókok SAS URI-k létrehozása
* Kapcsolódás a SAS URI-k a Storage-fiókok
* Frissítési értesítések, a Storage Explorer jövőbeli frissítései
* Frissített Megjelenés és működés

#### <a name="fixes"></a>Javítások

* Teljesítmény és megbízhatóság fejlesztései

### <a name="known-issues-amp-mitigations"></a>Ismert problémák &amp; enyhítések

* A nagy méretű blob fájlok letöltése nem működik megfelelően – azt javasoljuk, hogy a probléma megoldása az AzCopy használatával
* Fiók hitelesítő adatai nem fogja beolvasni és gyorsítótárazva, ha a kezdőmappa nem található vagy nem lehet írni
* Ha olyan entitást ad hozzá, szerkeszt vagy importál, amelynek tulajdonsága egyértelmű numerikus értékkel rendelkezik (például "1" vagy "1,0"), és a felhasználó megpróbálja `Edm.String`ként elküldeni, az érték visszakerül az ügyfél API-EDM. Double
* Többsoros rekordokat tartalmazó CSV-fájlok importálásakor az adatok lekérése darabolja esetleg betűtípus

02/03/2016

### <a name="version-07201601291"></a>Verzió 0.7.20160129.1

#### <a name="fixes"></a>Javítások

* Teljesítmény javítása általában amikor feltöltése, letöltése és a blobok másolása

01/14/2016

### <a name="version-07201601050"></a>Verzió 0.7.20160105.0

#### <a name="new"></a>Új

* Linux-támogatás (OSX szolgáltatások paritásos)
* Adja hozzá a blob-tárolók közös hozzáférésű Jogosultságkódok (SAS) kulccsal
* Storage-fiókok hozzáadása az Azure China 21Vianet
* Egyéni végpontokkal rendelkező Tárfiókok hozzáadása
* Megnyithatja és megtekintheti az szöveg és képek tartalmának blobok
* Blob tulajdonságai és metaadatainak megtekintése és szerkesztése

#### <a name="fixes"></a>Javítások

* Kijavítva: feltöltése vagy sok (500 +) blobok letöltése néha eredményezheti, hogy az alkalmazásnak, hogy rendelkezik egy fehér képernyő
* Kijavítva: blob tároló nyilvános hozzáférés szintje beállításakor az új érték nem frissül mindaddig, amíg újra a fókuszt a tárolón. Emellett a párbeszédpanelen mindig az alapértelmezett "Nincs nyilvános hozzáférés", és nem a tényleges aktuális érték.
* Jobb általános billentyűzet/kisegítő és a felhasználói felület támogatja
* Ha a fehér területtel hosszú fussa útkövetés előzmények szöveg
* SAS párbeszédpanel támogatja a bemenet-ellenőrzés
* Helyi tároló továbbra is elérhető akkor is, ha a felhasználói hitelesítő adatok lejártak.
* Egy megnyitott blob-tároló törlése esetén a jobb oldalon a blob explorer bezárása

#### <a name="known-issues"></a>Ismert problémák

* Telepítés Linux rendszeren van szüksége a gcc-verziójához, vagy frissített – frissítésére lépéseket alább:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>Verzió 0.7.20151116.0

#### <a name="new"></a>Új

* macOS és Windows-verziók
* Jelentkezzen be a Storage-fiókok nézet, – használja a szervezeti fiókot, a Microsoft Account, 2FA, stb.
* Helyi fejlesztési tárterülettel (storage emulatorral csak Windows)
* Az Azure Resource Manager és klasszikus erőforrások támogatása
* Hozzon létre vagy töröljön a blobok, üzenetsorok vagy táblák
* Keresse meg az adott blobok, üzenetsorok vagy táblák
* Fedezze fel a blob-tárolók tartalma
* Megtekintheti, és haladjon végig a könyvtárak
* Feltöltése, letöltése és törlése a blobok és mappák
* Blob tulajdonságai és metaadatainak megtekintése és szerkesztése
* SAS-kulcsok létrehozása
* Kezelheti, és a tárolt hozzáférési szabályzatok (SAP) létrehozása
* Keresés a blobokban előtag alapján
* Húzza az ajánlatból el fájlokat tölthetnek fel vagy le

#### <a name="known-issues"></a>Ismert problémák

* Blob tároló nyilvános hozzáférés szintje beállításakor az új érték nem frissül, amíg újra a fókuszt a tárolón
* A párbeszédpanelen állítsa be a nyilvános hozzáférés szintje megnyitásakor, mindig megjeleníti a "Nincs nyilvános hozzáférés" az alapértelmezett, és nem a tényleges aktuális érték
* A letöltött blobokat nem nevezhető át.
* Tevékenységnapló-bejegyzései fog néha "elakadnak a" egy folyamatban lévő állapot, amikor hiba történik, és a hibaüzenet nem jelenik meg
* Néha összeomlik, vagy teljesen fehér bekapcsolja a fel-vagy nagy számú blobok letöltése közben
* Néha az a másolási művelet megszakítása nem működik
* Során (a blob/üzenetsor vagy tábla) tároló létrehozása, ha a bemeneti neve érvénytelen, és a egy másik egy másik tárolóba típus alatt létrehozhatja nem állítható be fókusz új típus
* Nem hozható létre új mappát, vagy nem mappa átnevezése




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
