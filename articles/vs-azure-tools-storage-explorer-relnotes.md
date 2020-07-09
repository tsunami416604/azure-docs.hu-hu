---
title: Microsoft Azure Storage Explorer kibocsátási megjegyzések
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
ms.openlocfilehash: 986da8980a569583ef454833957ace85dd1bfbb6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80351058"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Microsoft Azure Storage Explorer kibocsátási megjegyzések

Ez a cikk a Azure Storage Explorer legújabb kibocsátási megjegyzéseit, valamint a korábbi verziók kibocsátási megjegyzéseit tartalmazza. 

A [Microsoft Azure Storage Explorer](./vs-azure-tools-storage-manage-with-storage-explorer.md) egy önálló alkalmazás, amely lehetővé teszi az Azure Storage-alapú adattárolást Windows, MacOS és Linux rendszeren.

A Storage Explorer korábbi verzióinak letöltéséhez látogasson el a GitHub-tárház [kiadási oldalára](https://github.com/microsoft/AzureStorageExplorer/releases) .

## <a name="version-1110"></a>1.11.0 verziója
11/4/2019

### <a name="new"></a>Új
* A Blobok, ADLS Gen2 és Managed Disks műveleteit az integrált AzCopy használja. Pontosabban a következő műveletek hajthatók végre a AzCopy használatával:
   * Blobok
      * Megnyitás szerkesztésre + feltöltésre
      * Feltöltés, beleértve a drag & drop
      * Letöltés
      * & beillesztési #1249 másolása
      * Törlés
   * Blobok ADLS Gen2
      * Feltöltés, beleértve a drag & drop
      * Letöltés
      * & beillesztés másolása
      * Törlés, beleértve a mappa törlését
   * Managed Disks
      * Feltöltés
      * Letöltés
      * & beillesztés másolása

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
   * & másolási Blobok másolása

* Ebben a kiadásban 17 további nyelvet tekintünk meg. Átválthat a kívánt nyelvre a beállítások lapon az "alkalmazás" → "területi beállítások" → "nyelv (előzetes verzió)" alatt. Továbbra is keményen dolgozunk a további sztringek fordításán és a fordítási minőség javításán. Ha bármilyen visszajelzést szeretne kapni a fordítással kapcsolatban, vagy ha olyan karakterláncot észlel, amely még nincs lefordítva, [Nyisson meg egy problémát a githubon](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%F0%9F%8C%90%20localization&template=bug-report.md&title=).
* Minden kiadásban megpróbálunk bevezetni néhány beállítást, így lehetővé válik a megfelelő Storage Explorer. Ebben a kiadásban a AzCopy további konfigurálására, valamint a szolgáltatási csomópontok elrejtésére vonatkozó beállításokat adunk hozzá:
   * AzCopy sávszélesség-korlát – segít megszabni, hogy a hálózati AzCopy mekkora részét használja. Ez a beállítás a "Transfers" → "AzCopy" → "maximális adatátviteli sebesség" alatt található. #1099
   * AzCopy MD5-ellenőrzés-lehetővé teszi, hogy beállítsa, hogy mikor és hogyan szigorúan AzCopy az MD5-kivonatokat a letöltéskor. Ez a beállítás a "Transfers" → "AzCopy" → "MD5"-ben található.
   * A AzCopy Egyidejűség és a memória pufferének mérete – alapértelmezés szerint a AzCopy elemzi a gépet, hogy meghatározza a beállítások ésszerű alapértelmezett értékeit. Ha azonban teljesítményproblémák jelentkeznek, a speciális beállításokkal részletesebben is megszabhatja, hogy a AzCopy hogyan fusson a számítógépen. Ezeket a beállításokat a "Transfers" → "AzCopy" alatt találja. #994
   * Szolgáltatási csomópontok megjelenítése és elrejtése – ezekkel a beállításokkal megjelenítheti vagy elrejtheti az Storage Explorer által támogatott Azure-szolgáltatásokat. Ezeket a beállításokat a "szolgáltatások" szakaszban találja. #1877

* Egy felügyelt lemez pillanatképének létrehozásakor az alapértelmezett név van megadva. #1847
* Ha az Azure AD-hez csatol egy ADLS Gen2 BLOB-tárolót, akkor az "(ADLS Gen2)" jelenik meg a csomópont mellett. #1861

### <a name="fixes"></a>Javítások
* Nagyméretű lemezek másolása, feltöltése vagy letöltése során a Storage Explorer időnként nem fogja tudni visszavonni a műveletben részt vevő lemezekhez való hozzáférést. Ezt a problémát megoldottuk. #2048
* A tábla statisztikái nem sikerült a partíciós kulcs lekérdezésének megtekintésekor. Ezt a problémát megoldottuk. #1886

### <a name="known-issues"></a>Ismert problémák
* Storage Explorer 1.11.0 most egy elosztott fájlrendszerbeli végpontra (például "myaccount.dfs.core.windows.net") van szükség ahhoz, hogy ADLS Gen2 tárolóhoz lehessen csatolni. A Storage Explorer korábbi verziói lehetővé tették a blob-végpont használatát. Előfordulhat, hogy ezek a mellékletek a 1.11.0 való frissítés után már nem működnek. Ha ezt a problémát tapasztalja, csatlakoztassa újra az elosztott fájlrendszerbeli végpont használatával.
* A numerikus beállításokat a rendszer nem ellenőrzi, hogy érvényes tartományban vannak-e. #2140
* A blob-tárolók egyik Storage-fiókból a másikba való másolása a fanézetben sikertelen lehet. Kivizsgáljuk a problémát. #2124
* Az automatikus frissítés beállítás még nem befolyásolja a blob Explorer összes műveletét.
* A felügyelt lemezes szolgáltatások Azure Stack nem támogatottak.
* Ha egy lemez feltöltése vagy beillesztése meghiúsul, és a hiba előtt új lemez lett létrehozva, akkor Storage Explorer nem törli a lemezt.
* Attól függően, hogy mikor szakítja meg a lemez feltöltését vagy beillesztését, megsérült állapotban hagyhatja az új lemezt. Ha ez történik, törölnie kell az új lemezt, vagy manuálisan kell meghívnia a lemez API-jait a lemez tartalmának lecserélése érdekében, hogy az már ne legyen megsérült.
* A RBAC használatakor Storage Explorer a tárolási erőforrások eléréséhez bizonyos felügyeleti rétegre vonatkozó engedélyeket igényel. További információért tekintse meg a [hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez csak frissítse a csoport csomópontot. További információ: #537.
* Ha a VS for Mac-et használja, és korábban már létrehozott egy egyéni HRE-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma megkerüléséhez törölje a ~/. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* A Azurite még nem teljes mértékben implementálta az összes tárolási API-t. Emiatt előfordulhat, hogy a Azurite fejlesztési tárolóhoz való használata váratlan hibákat vagy viselkedést okoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza a gyors eléréssel is elakad. A fókusz kitöltéséhez frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba javítva lett, de még nincs integrálva az Elektronba. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* A Azure Stack nem támogatja a következő funkciókat: Ha a Azure Stack-erőforrások használata során megkísérli használni ezeket a funkciókat, a rendszer váratlan hibákat eredményezhet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
   * ADLS Gen2
   * Managed Disks
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

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
* [1.7.0-ás verzió](#version-170)
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

## <a name="version-1101"></a>1.10.1 verziója
9/19/2019

### <a name="hotfix"></a>Gyorsjavítás
* Egyes felhasználók hibát észleltek a 1.10.0, miközben megpróbálták megtekinteni az ADLS 1. generációs fiókjaikat. Ez a hiba megakadályozta az Explorer panel megfelelő megjelenítését. Ezt a problémát megoldottuk. #1853 #1865

### <a name="new"></a>Új
* Storage Explorer most egy dedikált beállítások felhasználói felülettel rendelkezik. A Szerkesztés → beállítások menüpontból vagy a bal oldali függőleges eszköztár beállítások ikonjára (a fogaskerékre) kattintva érheti el. Ez a funkció az első lépés, amellyel számos [felhasználó által kért beállítást](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate)biztosítunk. A jelen kiadástól kezdve a következő beállítások támogatottak:
  * Téma
  * Proxy
  * Kilépés kijelentkezés #6
  * Az eszköz kódjának bejelentkezett bejelentkezésének engedélyezése
  * Automatikus frissítés #1526
  * AzCopy engedélyezése
  * A AzCopy SAS időtartama, ha további beállításokkal szeretné látni a hozzáadást, [Nyisson meg egy problémát a githubon](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=) , amely leírja a kívánt beállítást.
* A Storage Explorer mostantól támogatja a Managed Disks. A következőket teheti:
  * Helyszíni virtuális merevlemez feltöltése új lemezre
  * Lemez letöltése
  * Lemezek másolása és beillesztése erőforrás-csoportok és régiók között
  * Lemezek törlése
  * Lemez pillanatképének létrehozása

A lemezek feltöltését, letöltését és régiók közötti másolását a AzCopy v10 látja el.
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
* Lehetséges, hogy az Explorer faszerkezete (bal oldali) olyan állapotba kerül, amelyben a fókusz többször is a legfelső csomópontra ugrik. Ezt a problémát megoldottuk. #1596
* A Blobok pillanatképének kezelésekor a képernyőolvasók nem fogják beolvasni a pillanatképhez társított időbélyeget. Ezt a problémát megoldottuk. #1202
* A macOS-re vonatkozó proxybeállítások nem lettek beállítva időben a hitelesítési folyamat használatára. Ezt a problémát megoldottuk. #1567
* Ha egy szuverén felhőben lévő Storage-fiók neve és kulcsa alapján lett csatolva, a AzCopy nem fog működni. Ezt a problémát megoldottuk. #1544
* Ha egy kapcsolódási karakterláncon keresztül csatlakozik, Storage Explorer most eltávolítja a záró szóközöket. #1387

### <a name="known-issues"></a>Ismert problémák
* Az automatikus frissítés beállítás még nem befolyásolja a blob Explorer összes műveletét.
* A felügyelt lemezes szolgáltatások Azure Stack nem támogatottak.
* Ha egy lemez feltöltése vagy beillesztése meghiúsul, és a hiba előtt új lemez lett létrehozva, akkor Storage Explorer nem törli a lemezt.
* Attól függően, hogy mikor szakítja meg a lemez feltöltését vagy beillesztését, megsérült állapotban hagyhatja az új lemezt. Ha ez történik, törölnie kell az új lemezt, vagy manuálisan kell meghívnia a lemez API-jait a lemez tartalmának lecserélése érdekében, hogy az már ne legyen megsérült.
* Attól függően, hogy mikor szakítja meg a lemez feltöltését vagy beillesztését, megsérült állapotban hagyhatja az új lemezt. Ha ez történik, törölnie kell az új lemezt, vagy manuálisan kell meghívnia a lemez API-jait a lemez tartalmának lecserélése érdekében, hogy az már ne legyen megsérült.
* A nem AzCopy Blobok letöltésének végrehajtásakor a nagyméretű fájlok MD5-hitelesítése nem történik meg. Ezt a Storage SDK hibája okozza. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* A RBAC használatakor Storage Explorer a tárolási erőforrások eléréséhez bizonyos felügyeleti rétegre vonatkozó engedélyeket igényel. További információért tekintse meg a [hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez csak frissítse a csoport csomópontot. További információ: #537.
* Ha a VS for Mac-et használja, és korábban már létrehozott egy egyéni HRE-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma megkerüléséhez törölje a ~/. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* A Azurite még nem teljes mértékben implementálta az összes tárolási API-t. Emiatt előfordulhat, hogy a Azurite fejlesztési tárolóhoz való használata váratlan hibákat vagy viselkedést okoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza a gyors eléréssel is elakad. A fókusz kitöltéséhez frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba javítva lett, de még nincs integrálva az Elektronba. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* A Azure Stack nem támogatja a következő funkciókat: Ha a Azure Stack-erőforrások használata során megkísérli használni ezeket a funkciókat, a rendszer váratlan hibákat eredményezhet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
   * ADLS Gen2
   * Managed Disks
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linuxon futó Storage Explorer futtatásához bizonyos függőségeket először telepíteni kell. További információért tekintse meg a Storage Explorer [hibaelhárítási útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) .


## <a name="version-1100"></a>1.10.0 verziója
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

    Ha további beállításokkal szeretné látni a hozzáadást, [Nyisson meg egy problémát a githubon, amely leírja a kívánt beállítást](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=).
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
* Lehetséges, hogy az Explorer faszerkezete (bal oldali) olyan állapotba kerül, amelyben a fókusz többször is a legfelső csomópontra ugrik. Ezt a problémát megoldottuk. [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* A Blobok pillanatképének kezelésekor a képernyőolvasók nem fogják beolvasni a pillanatképhez társított időbélyeget. Ezt a problémát megoldottuk. [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* A macOS-re vonatkozó proxybeállítások nem lettek beállítva időben a hitelesítési folyamat használatára. Ezt a problémát megoldottuk. [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* Ha egy szuverén felhőben lévő Storage-fiók neve és kulcsa alapján lett csatolva, a AzCopy nem fog működni. Ezt a problémát megoldottuk. [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* Ha egy kapcsolódási karakterláncon keresztül csatlakozik, Storage Explorer most eltávolítja a záró szóközöket. [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>Ismert problémák

* Az automatikus frissítés beállítás még nem befolyásolja a blob Explorer összes műveletét.
* A felügyelt lemezes szolgáltatások Azure Stack nem támogatottak.
* Ha egy lemez feltöltése vagy beillesztése meghiúsul, és a hiba előtt új lemez lett létrehozva, akkor Storage Explorer nem törli a lemezt.
* Attól függően, hogy mikor szakítja meg a lemez feltöltését vagy beillesztését, megsérült állapotban hagyhatja az új lemezt. Ha ez történik, törölnie kell az új lemezt, vagy manuálisan kell meghívnia a lemez API-jait a lemez tartalmának lecserélése érdekében, hogy az már ne legyen megsérült.
* A nem AzCopy Blobok letöltésének végrehajtásakor a nagyméretű fájlok MD5-hitelesítése nem történik meg. Ezt a Storage SDK hibája okozza. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* A RBAC használatakor Storage Explorer a tárolási erőforrások eléréséhez bizonyos felügyeleti rétegre vonatkozó engedélyeket igényel. További információért tekintse meg a [hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez csak frissítse a csoport csomópontot. További információ: #537.
* Ha a VS for Mac-et használja, és korábban már létrehozott egy egyéni HRE-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma megkerüléséhez törölje a ~/. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* A Azurite még nem teljes mértékben implementálta az összes tárolási API-t. Emiatt előfordulhat, hogy a Azurite fejlesztési tárolóhoz való használata váratlan hibákat vagy viselkedést okoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza a gyors eléréssel is elakad. A fókusz kitöltéséhez frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba javítva lett, de még nincs integrálva az Elektronba. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* A Azure Stack nem támogatja a következő funkciókat: Ha a Azure Stack-erőforrások használata során megkísérli használni ezeket a funkciókat, a rendszer váratlan hibákat eredményezhet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
   * ADLS Gen2
   * Managed Disks
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linuxon futó Storage Explorer futtatásához bizonyos függőségeket először telepíteni kell. További információért tekintse meg a Storage Explorer [hibaelhárítási útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) .

## <a name="version-190"></a>1.9.0 verziója
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
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez csak frissítse a csoport csomópontot. További információ: #537.
* Ha a VS for Mac-et használja, és korábban már létrehozott egy egyéni HRE-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma megkerüléséhez törölje a ~/. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* A Azurite még nem teljes mértékben implementálta az összes tárolási API-t. Emiatt előfordulhat, hogy a Azurite fejlesztési tárolóhoz való használata váratlan hibákat vagy viselkedést okoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza a gyors eléréssel is elakad. A fókusz kitöltéséhez frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba javítva lett, de még nincs integrálva az Elektronba. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* A Azure Stack nem támogatja a következő funkciókat: Ha a Azure Stack-erőforrások használata során megkísérli használni ezeket a funkciókat, a rendszer váratlan hibákat eredményezhet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
   * ADLS Gen2
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linuxon futó Storage Explorer futtatásához bizonyos függőségeket először telepíteni kell. További információért tekintse meg a Storage Explorer [hibaelhárítási útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) .

## <a name="version-181"></a>1.8.1-es verzió
5/13/2019

### <a name="hotfixes"></a>Gyorsjavítások
* Bizonyos esetekben a "több betöltés" gombra kattintva az erőforrás szintjén nem fog visszaadni az erőforrások következő oldalára. Ezt a problémát megoldottuk. [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* Windows rendszeren a AzCopy letöltése meghiúsul, ha egyetlen fájlt vagy mappát töltött le, és a fájl vagy mappa neve érvénytelen volt egy Windows elérési útra. Ezt a problémát megoldottuk. [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* Rendkívül ritka esetekben, amikor egy fájlmegosztás átnevezését vagy egy fájlmegosztás átnevezését végzi, ha az átnevezéshez tartozó másolatok sikertelenek voltak, vagy ha a Storage Explore nem tudta megerősíteni a másolatok sikerességét az Azure-ban, lehetséges, hogy Storage Explorer az eredeti fájlok törlését a másolás befejeződése előtt. Ezt a problémát megoldottuk.

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
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez csak frissítse a csoport csomópontot. További információ: #537.
* Ha a VS for Mac-et használja, és korábban már létrehozott egy egyéni HRE-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma megkerüléséhez törölje a ~/. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* A Azurite még nem teljes mértékben implementálta az összes tárolási API-t. Emiatt előfordulhat, hogy a Azurite fejlesztési tárolóhoz való használata váratlan hibákat vagy viselkedést okoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza a gyors eléréssel is elakad. A fókusz kitöltéséhez frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba javítva lett, de még nincs integrálva az Elektronba. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* A Azure Stack nem támogatja a következő funkciókat: Ha a Azure Stack-erőforrások használata során megkísérli használni ezeket a funkciókat, a rendszer váratlan hibákat eredményezhet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
   * ADLS Gen2
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linuxon futó Storage Explorer futtatásához bizonyos függőségeket először telepíteni kell. További információért tekintse meg a Storage Explorer [hibaelhárítási útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) .

## <a name="version-180"></a>1.8.0-verzió
2019. 05. 01.

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
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez csak frissítse a csoport csomópontot. További információ: #537.
* Ha a VS for Mac-et használja, és korábban már létrehozott egy egyéni HRE-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma megkerüléséhez törölje a ~/. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* A Azurite még nem teljes mértékben implementálta az összes tárolási API-t. Emiatt előfordulhat, hogy a Azurite fejlesztési tárolóhoz való használata váratlan hibákat vagy viselkedést okoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza a gyors eléréssel is elakad. A fókusz kitöltéséhez frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba javítva lett, de még nincs integrálva az Elektronba. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* A Azure Stack nem támogatja a következő funkciókat: Ha a Azure Stack-erőforrások használata során megkísérli használni ezeket a funkciókat, a rendszer váratlan hibákat eredményezhet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
   * ADLS Gen2
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linuxon futó Storage Explorer futtatásához bizonyos függőségeket először telepíteni kell. További információért tekintse meg a Storage Explorer [hibaelhárítási útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) .

## <a name="version-170"></a>1.7.0-ás verzió
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
* Korábban, ha engedélyezte a statikus webhelyeket egy ADLS Gen2 Storage-fiókhoz, és azt névvel és kulccsal csatolta, Storage Explorer nem észlelte, hogy a hierarchikus névtér engedélyezve lett. Ezt a problémát megoldottuk. #1081
* A blob-szerkesztőben a rendezés a hátralévő megőrzési napok vagy az állapot megszakadásával történhet. Ezt a problémát megoldottuk. #1106
* A 1.5.0 után Storage Explorer már nem várta a kiszolgálóoldali másolatok befejeződését, mielőtt az Átnevezés vagy a másolás & beillesztéskor befejeződik a sikeres jelentéskészítés. Ezt a problémát megoldottuk. #976
* A kísérleti AzCopy funkció használatakor a Másolás parancs vágólapra való másolása után másolt parancs nem mindig a saját futtatható. Ekkor a rendszer az átvitel manuális futtatásához szükséges összes parancsot átmásolja. #1079
* Korábban ADLS Gen2 Blobok nem voltak elérhetők, ha a proxy mögött volt. Ezt a Storage SDK által használt új hálózati könyvtár hibája okozta. A 1.7.0-ben a probléma enyhítésére tett kísérlet történt, de egyes felhasználók továbbra is láthatják a problémákat. A teljes javítás egy jövőbeli frissítésben jelenik meg. #1090
* A 1.7.0-ben a fájl mentése párbeszédpanel helyesen emlékszik az utolsó olyan helyre, ahol a fájlt mentette. #16
* A Tulajdonságok panelen egy Storage-fiók SKU-szintje jelenik meg a fiók típusaként. Ezt a problémát megoldottuk. #654
* Időnként előfordulhat, hogy nem sikerült megszüntetni egy blob bérletét, még akkor is, ha helyesen adta meg a blob nevét. Ezt a problémát megoldottuk. #1070

### <a name="known-issues"></a>Ismert problémák

* A RBAC használatakor Storage Explorer a tárolási erőforrások eléréséhez bizonyos felügyeleti rétegre vonatkozó engedélyeket igényel. További információért tekintse meg a [hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Sikertelen volt a ADLS Gen2 Blobok elérésének megkísérlése a proxy mögött.
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez csak frissítse a csoport csomópontot. További információ: #537.
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez csak frissítse a csoport csomópontot. További információ: #537.
* Ha a VS for Mac-et használja, és korábban már létrehozott egy egyéni HRE-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma megkerüléséhez törölje a ~/. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* A Azurite még nem teljes mértékben implementálta az összes tárolási API-t. Emiatt előfordulhat, hogy a Azurite fejlesztési tárolóhoz való használata váratlan hibákat vagy viselkedést okoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza a gyors eléréssel is elakad. A fókusz kitöltéséhez frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba javítva lett, de még nincs integrálva az Elektronba. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* A Azure Stack nem támogatja a következő funkciókat: Ha a Azure Stack-erőforrások használata során megkísérli használni ezeket a funkciókat, a rendszer váratlan hibákat eredményezhet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)-es frissítést.
* Az Ubuntu 14,04-es felhasználóknak biztosítania kell, hogy a GCC naprakész legyen – ez a következő parancsok futtatásával, majd a gép újraindításával végezhető el:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17,04 rendszerű felhasználók esetén telepítenie kell a GConf-ezt a következő parancsok futtatásával, majd a gép újraindításával:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>1.6.2-es verzió
1/9/2019

### <a name="hotfixes"></a>Gyorsjavítások
* A 1.6.1-ben a ObjectId által ADLS Gen2 ACL-ek számára hozzáadott entitások, amelyek nem a felhasználók csoportként lettek hozzáadva. Mostantól csak a csoportok lesznek hozzáadva csoportként, és az olyan entitások, mint például a vállalati alkalmazások, andService lesznek hozzáadva felhasználóként. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Ha egy ADLS Gen2 Storage-fiók nem rendelkezett tárolóval, és a név és a kulcs névvel lett csatolva, akkor Storage Explorer nem fogja észlelni, hogy a Storage-fiók ADLS Gen2 volt. Ezt a problémát megoldottuk. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* A 1.6.0-ben a másolás és beillesztés során fellépő ütközések nem kérik fel a megoldást. Ehelyett az ütköző másolat egyszerűen meghiúsul. Az első ütközés után a rendszer megkérdezi, hogyan szeretné megoldani. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* Az API-korlátozások miatt a ObjectIds összes érvényesítése le van tiltva a hozzáférés kezelése párbeszédpanelen. Az érvényesítés most csak a felhasználói UPN-ket veszi fel. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* A ADLS Gen2 hozzáférés kezelése párbeszédpanelen a csoport engedélyei nem módosíthatók. Ezt a problémát megoldottuk. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* A ADLS Gen2 szerkesztőhöz a fogd és vidd feltöltési támogatás hozzáadva. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* A ADLS Gen2 fájlok és mappák Tulajdonságok párbeszédpanelének URL-tulajdonsága néha hiányzik a következőhöz: "/". Ezt a problémát megoldottuk. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Ha egy ADLS Gen2 tárolóhoz, fájlhoz vagy mappához tartozó jelenlegi engedélyek beolvasása meghiúsul, akkor a hiba mostantól a tevékenység naplójában jelenik meg. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* A fájlok megnyitására szolgáló ideiglenes elérési út lerövidítve lett, így csökkenthető az esélye annak, hogy az elérési út hosszabb, mint a Windows MAX_PATH. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* A csatlakozás párbeszédpanel helyesen jelenik meg, ha nincsenek bejelentkezett felhasználók, és nincsenek csatolva erőforrások. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* A 1.6.0-ben a nem HNS blobok és fájlok tulajdonságainak mentése az összes tulajdonság értékét kódolja. Ez szükségtelen kódolási értéket eredményezett, amely csak ASCII-karaktereket tartalmaz. Az értékek mostantól csak akkor lesznek kódolva, ha nem ASCII-karaktereket tartalmaznak. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* A mappa nem HNS blob-tárolóba való feltöltése sikertelen lesz, ha a SAS használatban van, és az SAS nem rendelkezik olvasási engedéllyel. Ezt a problémát megoldottuk. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* A AzCopy-átvitel megszakítása nem működött. Ezt a problémát megoldottuk. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* A AzCopy akkor sikertelen, amikor egy ADLS Gen2 blob-tárolóból próbál meg letölteni egy mappát, ha a mappában szerepel a neve. Ezt a problémát megoldottuk. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
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
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez csak frissítse a csoport csomópontot. További információ: #537.
* Ha a VS for Mac-et használja, és korábban már létrehozott egy egyéni HRE-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma megkerüléséhez törölje a ~/. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* A Azurite még nem teljes mértékben implementálta az összes tárolási API-t. Emiatt előfordulhat, hogy a Azurite fejlesztési tárolóhoz való használata váratlan hibákat vagy viselkedést okoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza a gyors eléréssel is elakad. A fókusz kitöltéséhez frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba javítva lett, de még nincs integrálva az Elektronba. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* A Azure Stack nem támogatja a következő funkciókat: Ha a Azure Stack-erőforrások használata során megkísérli használni ezeket a funkciókat, a rendszer váratlan hibákat eredményezhet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)-es frissítést.
* Az Ubuntu 14,04-es felhasználóknak biztosítania kell, hogy a GCC naprakész legyen – ez a következő parancsok futtatásával, majd a gép újraindításával végezhető el:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17,04 rendszerű felhasználók esetén telepítenie kell a GConf-ezt a következő parancsok futtatásával, majd a gép újraindításával:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>1.6.1-es verzió
12/18/2018

### <a name="hotfixes"></a>Gyorsjavítások
* Az API-korlátozások miatt a ObjectIds összes érvényesítése le van tiltva a hozzáférés kezelése párbeszédpanelen. Az érvényesítés most csak a felhasználói UPN-ket veszi fel. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* A ADLS Gen2 hozzáférés kezelése párbeszédpanelen a csoport engedélyei nem módosíthatók. Ezt a problémát megoldottuk. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* A ADLS Gen2 szerkesztőhöz a fogd és vidd feltöltési támogatás hozzáadva. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* A ADLS Gen2 fájlok és mappák Tulajdonságok párbeszédpanelének URL-tulajdonsága néha hiányzik a következőhöz: "/". Ezt a problémát megoldottuk. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Ha egy ADLS Gen2 tárolóhoz, fájlhoz vagy mappához tartozó jelenlegi engedélyek beolvasása meghiúsul, akkor a hiba mostantól a tevékenység naplójában jelenik meg. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* A fájlok megnyitására szolgáló ideiglenes elérési út lerövidítve lett, így csökkenthető az esélye annak, hogy az elérési út hosszabb, mint a Windows MAX_PATH. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* A csatlakozás párbeszédpanel helyesen jelenik meg, ha nincsenek bejelentkezett felhasználók, és nincsenek csatolva erőforrások. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* A 1.6.0-ben a nem HNS blobok és fájlok tulajdonságainak mentése az összes tulajdonság értékét kódolja. Ez szükségtelen kódolási értéket eredményezett, amely csak ASCII-karaktereket tartalmaz. Az értékek mostantól csak akkor lesznek kódolva, ha nem ASCII-karaktereket tartalmaznak. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* A mappa nem HNS blob-tárolóba való feltöltése sikertelen lesz, ha a SAS használatban van, és az SAS nem rendelkezik olvasási engedéllyel. Ezt a problémát megoldottuk. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* A AzCopy-átvitel megszakítása nem működött. Ezt a problémát megoldottuk. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* A AzCopy akkor sikertelen, amikor egy ADLS Gen2 blob-tárolóból próbál meg letölteni egy mappát, ha a mappában szerepel a neve. Ezt a problémát megoldottuk. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
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
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez csak frissítse a csoport csomópontot. További információ: #537.
* Ha a VS for Mac-et használja, és korábban már létrehozott egy egyéni HRE-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma megkerüléséhez törölje a ~/. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* A Azurite még nem teljes mértékben implementálta az összes tárolási API-t. Emiatt előfordulhat, hogy a Azurite fejlesztési tárolóhoz való használata váratlan hibákat vagy viselkedést okoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza a gyors eléréssel is elakad. A fókusz kitöltéséhez frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba javítva lett, de még nincs integrálva az Elektronba. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* A Azure Stack nem támogatja a következő funkciókat: Ha a Azure Stack-erőforrások használata során megkísérli használni ezeket a funkciókat, a rendszer váratlan hibákat eredményezhet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)-es frissítést.
* Az Ubuntu 14,04-es felhasználóknak biztosítania kell, hogy a GCC naprakész legyen – ez a következő parancsok futtatásával, majd a gép újraindításával végezhető el:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17,04 rendszerű felhasználók esetén telepítenie kell a GConf-ezt a következő parancsok futtatásával, majd a gép újraindításával:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>1.6.0 verziója
2018.12.05.

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
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez csak frissítse a csoport csomópontot. További információ: #537.
* Ha a VS for Mac-et használja, és korábban már létrehozott egy egyéni HRE-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma megkerüléséhez törölje a ~/. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* A Azurite még nem teljes mértékben implementálta az összes tárolási API-t. Emiatt előfordulhat, hogy a Azurite fejlesztési tárolóhoz való használata váratlan hibákat vagy viselkedést okoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza a gyors eléréssel is elakad. A fókusz kitöltéséhez frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba javítva lett, de még nincs integrálva az Elektronba. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* A Azure Stack nem támogatja a következő funkciókat: Ha a Azure Stack-erőforrások használata során megkísérli használni ezeket a funkciókat, a rendszer váratlan hibákat eredményezhet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)-es frissítést.
* Az Ubuntu 14,04-es felhasználóknak biztosítania kell, hogy a GCC naprakész legyen – ez a következő parancsok futtatásával, majd a gép újraindításával végezhető el:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17,04 rendszerű felhasználók esetén telepítenie kell a GConf-ezt a következő parancsok futtatásával, majd a gép újraindításával:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>1.5.0-es verzió
10/29/2018

### <a name="new"></a>Új

* Most már használhatja a [AzCopy v10 (előzetes verzió)](https://github.com/Azure/azure-storage-azcopy) használatát a Blobok feltöltéséhez és letöltéséhez. A funkció engedélyezéséhez lépjen a "kísérleti" menüre, majd kattintson a "AzCopy használata a továbbfejlesztett blob-feltöltéshez és a letöltéshez" lehetőségre. Ha ez a beállítás engedélyezve van, a rendszer a következő helyzetekben fogja használni a AzCopy:
   * Mappák és fájlok feltöltése blob-tárolóba az eszköztáron vagy a drag and drop használatával.
   * Mappák és fájlok letöltése az eszköztár vagy a helyi menü segítségével.

* Emellett a AzCopy használata esetén:
   * A vágólapra történő átvitel végrehajtásához használt AzCopy-parancsot másolhatja. Egyszerűen kattintson a AzCopy parancs másolása a vágólapra elemre a tevékenység naplójában.
   * A feltöltés után manuálisan frissítenie kell a blob-szerkesztőt.
   * A fájlok hozzáfűzési blobokhoz való feltöltése nem támogatott, a VHD-fájlok pedig blobként lesznek feltöltve, és az összes többi fájl is fel lesz töltve.
   * A feltöltés vagy a letöltés során felmerülő hibák és ütközések addig nem lesznek felszínben, amíg a feltöltés vagy a letöltés be nem fejeződik.

Végezetül a AzCopy és a fájlmegosztás használatának támogatása a jövőben fog megjelenni.
* Storage Explorer mostantól az Electron 2.0.11-verziót használja.
* A megszakított bérletek mostantól egyszerre csak egy blobon hajthatók végre. Emellett meg kell adnia annak a blobnak a nevét, amelynek a bérletét megszakította. Ez a változás azért történt, hogy csökkentse a bérlet véletlen megszakításának valószínűségét, különösen a virtuális gépek esetében. #394
* Ha bármikor bejelentkezik a bejelentkezési problémákba, most már próbálkozhat a hitelesítés alaphelyzetbe állításával. Lépjen a "Súgó" menüre, és kattintson az "alaphelyzetbe állítás" gombra a funkció eléréséhez. #419

### <a name="fix"></a>Hibajavítás

* Az erős felhasználói visszajelzés után az emulátor alapértelmezett csomópontja újra engedélyezve lett. Továbbra is hozzáadhat további Emulator-kapcsolatokat a csatlakozás párbeszédpanelen, de ha az emulátor az alapértelmezett portok használatára van konfigurálva, akkor az "emulátor * alapértelmezett portok" csomópontot is használhatja a "helyi & csatolt/Storage-fiókok" területen. #669
* Storage Explorer többé nem teszi lehetővé a blob-metaadatok kezdő vagy záró szóközt tartalmazó értékének beállítását. #760
* A "Bejelentkezés" gomb mindig engedélyezve volt a csatlakozási párbeszédpanel ugyanazon oldalain. Ha szükséges, le van tiltva. #761
* A gyors hozzáférés többé nem eredményez hibát a konzolon, ha nem lettek hozzáadva gyors elérésű elemek.

### <a name="known-issues"></a>Ismert problémák

* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez csak frissítse a csoport csomópontot. További információ: #537.
* Ha a VS for Mac-et használja, és korábban már létrehozott egy egyéni HRE-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma megkerüléséhez törölje a ~/. IdentityService/AadConfigurations. Ha így tesz, azzal nem oldja fel a problémát.
* A Azurite még nem teljes mértékben implementálta az összes tárolási API-t. Emiatt előfordulhat, hogy a Azurite fejlesztési tárolóhoz való használata váratlan hibákat vagy viselkedést okoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza a gyors eléréssel is elakad. A fókusz kitöltéséhez frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba javítva lett, de még nincs integrálva az Elektronba. A probléma megoldásához blob-tárolóból történő feltöltéskor vagy letöltéskor használhatja a kísérleti AzCopy funkciót.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* A Azure Stack nem támogatja a következő funkciókat: Ha a Azure Stack-erőforrások használata során megkísérli használni ezeket a funkciókat, a rendszer váratlan hibákat eredményezhet.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)-es frissítést.
* Az Ubuntu 14,04-es felhasználóknak biztosítania kell, hogy a GCC naprakész legyen – ez a következő parancsok futtatásával, majd a gép újraindításával végezhető el:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17,04 rendszerű felhasználók esetén telepítenie kell a GConf-ezt a következő parancsok futtatásával, majd a gép újraindításával:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>1.4.4-es verzió
10/15/2018

### <a name="hotfixes"></a>Gyorsjavítások
* Az Azure Resource Management API verziója vissza lett állítva az Azure US government-felhasználók blokkolásának feloldására. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* A tárcsa betöltése mostantól a CSS-animációk használatával csökkenti az Storage Explorer által használt GPU mennyiségét. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Új
* A külső erőforrások mellékletei, például az SAS-kapcsolatok és az emulátorok, jelentősen javultak. Ezután:
   * Szabja testre a csatolni kívánt erőforrás megjelenítendő nevét. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Több helyi emulátorhoz is csatolhat különböző portokat használva. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Csatolt erőforrások hozzáadása a gyors eléréshez. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* A Storage Explorer mostantól támogatja a Soft delete használatát. A következőket teheti:
   * A helyreállítható törlési házirend konfigurálásához kattintson a jobb gombbal a Storage-fiók blob-tárolók csomópontjára.
   * A legördülő menüben kattintson az "aktív és törölt Blobok" elemre a navigációs sáv melletti legördülő listában.
   * Törölje a törölt Blobok törlését.

### <a name="fixes"></a>Javítások
* A "CORS-beállítások konfigurálása" művelet már nem érhető el Premium Storage fiókokon, mert Premium Storage fiókok nem támogatják a CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Már van egy közös hozzáférés-aláírási tulajdonság a SAS-hez csatolt szolgáltatásokhoz. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Az "alapértelmezett hozzáférési szintek beállítása" művelet mostantól elérhető a gyors hozzáféréshez rögzített blob-és GPV2-tároló fiókok esetében. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Időnként a Storage Explorer nem fogja tudni megjeleníteni a klasszikus Storage-fiókokat. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Ismert problémák
* Az emulátorok, például az Azure Storage Emulator vagy a Azurite használata esetén az alapértelmezett portokon figyelnie kell a kapcsolatokat. Ellenkező esetben Storage Explorer nem fog tudni csatlakozni hozzájuk.
* Ha a VS for Mac-et használja, és korábban már létrehozott egy egyéni HRE-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma megkerüléséhez törölje a ~/. IdentityService/AadConfigurations. Ha ez nem oldja fel a tiltást, kérjük, véleményezze a [problémát](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* A Azurite még nem teljes mértékben implementálta az összes tárolási API-t. Emiatt előfordulhat, hogy a Azurite fejlesztési tárolóhoz való használata váratlan hibákat vagy viselkedést okoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza a gyors eléréssel is elakad. A fókusz kitöltéséhez frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba javítva lett, de még nincs integrálva az Elektronba.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* Bár a Azure Stack jelenleg nem támogatja a fájlmegosztás használatát, egy fájlmegosztási csomópont továbbra is megjelenik egy csatolt Azure Stack Storage-fiókban.
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)-es frissítést.
* Az Ubuntu 14,04-es felhasználóknak biztosítania kell, hogy a GCC naprakész legyen – ez a következő parancsok futtatásával, majd a gép újraindításával végezhető el:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17,04 rendszerű felhasználók esetén telepítenie kell a GConf-ezt a következő parancsok futtatásával, majd a gép újraindításával:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>1.4.3 verziója
10/11/2018

### <a name="hotfixes"></a>Gyorsjavítások
* Az Azure Resource Management API verziója vissza lett állítva az Azure US government-felhasználók blokkolásának feloldására. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* A tárcsa betöltése mostantól a CSS-animációk használatával csökkenti az Storage Explorer által használt GPU mennyiségét. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Új
* A külső erőforrások mellékletei, például az SAS-kapcsolatok és az emulátorok, jelentősen javultak. Ezután:
   * Szabja testre a csatolni kívánt erőforrás megjelenítendő nevét. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Több helyi emulátorhoz is csatolhat különböző portokat használva. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Csatolt erőforrások hozzáadása a gyors eléréshez. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* A Storage Explorer mostantól támogatja a Soft delete használatát. A következőket teheti:
   * A helyreállítható törlési házirend konfigurálásához kattintson a jobb gombbal a Storage-fiók blob-tárolók csomópontjára.
   * A legördülő menüben kattintson az "aktív és törölt Blobok" elemre a navigációs sáv melletti legördülő listában.
   * Törölje a törölt Blobok törlését.

### <a name="fixes"></a>Javítások
* A "CORS-beállítások konfigurálása" művelet már nem érhető el Premium Storage fiókokon, mert Premium Storage fiókok nem támogatják a CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Már van egy közös hozzáférés-aláírási tulajdonság a SAS-hez csatolt szolgáltatásokhoz. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Az "alapértelmezett hozzáférési szintek beállítása" művelet mostantól elérhető a gyors hozzáféréshez rögzített blob-és GPV2-tároló fiókok esetében. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Időnként a Storage Explorer nem fogja tudni megjeleníteni a klasszikus Storage-fiókokat. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Ismert problémák
* Az emulátorok, például az Azure Storage Emulator vagy a Azurite használata esetén az alapértelmezett portokon figyelnie kell a kapcsolatokat. Ellenkező esetben Storage Explorer nem fog tudni csatlakozni hozzájuk.
* Ha a VS for Mac-et használja, és korábban már létrehozott egy egyéni HRE-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma megkerüléséhez törölje a ~/. IdentityService/AadConfigurations. Ha ez nem oldja fel a tiltást, kérjük, véleményezze a [problémát](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* A Azurite még nem teljes mértékben implementálta az összes tárolási API-t. Emiatt előfordulhat, hogy a Azurite fejlesztési tárolóhoz való használata váratlan hibákat vagy viselkedést okoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza a gyors eléréssel is elakad. A fókusz kitöltéséhez frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba javítva lett, de még nincs integrálva az Elektronba.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* Bár a Azure Stack jelenleg nem támogatja a fájlmegosztás használatát, egy fájlmegosztási csomópont továbbra is megjelenik egy csatolt Azure Stack Storage-fiókban.
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)-es frissítést.
* Az Ubuntu 14,04-es felhasználóknak biztosítania kell, hogy a GCC naprakész legyen – ez a következő parancsok futtatásával, majd a gép újraindításával végezhető el:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17,04 rendszerű felhasználók esetén telepítenie kell a GConf-ezt a következő parancsok futtatásával, majd a gép újraindításával:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>1.4.2-es verzió
09/24/2018

### <a name="hotfixes"></a>Gyorsjavítások
* Az Azure Resource Management API verziójának frissítése 2018-07-01-re az új Azure Storage-fiókok támogatásának hozzáadásához. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Új
* A külső erőforrások mellékletei, például az SAS-kapcsolatok és az emulátorok, jelentősen javultak. Ezután:
   * Szabja testre a csatolni kívánt erőforrás megjelenítendő nevét. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Több helyi emulátorhoz is csatolhat különböző portokat használva. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Csatolt erőforrások hozzáadása a gyors eléréshez. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* A Storage Explorer mostantól támogatja a Soft delete használatát. A következőket teheti:
   * A helyreállítható törlési házirend konfigurálásához kattintson a jobb gombbal a Storage-fiók blob-tárolók csomópontjára.
   * A legördülő menüben kattintson az "aktív és törölt Blobok" elemre a navigációs sáv melletti legördülő listában.
   * Törölje a törölt Blobok törlését.

### <a name="fixes"></a>Javítások
* A "CORS-beállítások konfigurálása" művelet már nem érhető el Premium Storage fiókokon, mert Premium Storage fiókok nem támogatják a CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Már van egy közös hozzáférés-aláírási tulajdonság a SAS-hez csatolt szolgáltatásokhoz. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Az "alapértelmezett hozzáférési szintek beállítása" művelet mostantól elérhető a gyors hozzáféréshez rögzített blob-és GPV2-tároló fiókok esetében. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Időnként a Storage Explorer nem fogja tudni megjeleníteni a klasszikus Storage-fiókokat. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Ismert problémák
* Az emulátorok, például az Azure Storage Emulator vagy a Azurite használata esetén az alapértelmezett portokon figyelnie kell a kapcsolatokat. Ellenkező esetben Storage Explorer nem fog tudni csatlakozni hozzájuk.
* Ha a VS for Mac-et használja, és korábban már létrehozott egy egyéni HRE-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma megkerüléséhez törölje a ~/. IdentityService/AadConfigurations. Ha ez nem oldja fel a tiltást, kérjük, véleményezze a [problémát](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* A Azurite még nem teljes mértékben implementálta az összes tárolási API-t. Emiatt előfordulhat, hogy a Azurite fejlesztési tárolóhoz való használata váratlan hibákat vagy viselkedést okoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza a gyors eléréssel is elakad. A fókusz kitöltéséhez frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba javítva lett, de még nincs integrálva az Elektronba.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* Bár a Azure Stack jelenleg nem támogatja a fájlmegosztás használatát, egy fájlmegosztási csomópont továbbra is megjelenik egy csatolt Azure Stack Storage-fiókban.
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)-es frissítést.
* Az Ubuntu 14,04-es felhasználóknak biztosítania kell, hogy a GCC naprakész legyen – ez a következő parancsok futtatásával, majd a gép újraindításával végezhető el:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17,04 rendszerű felhasználók esetén telepítenie kell a GConf-ezt a következő parancsok futtatásával, majd a gép újraindításával:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>1.4.1-es verzió
08/28/2018

### <a name="hotfixes"></a>Gyorsjavítások
* Az első indításkor a Storage Explorer nem tudta előállítani a bizalmas adatok titkosításához használt kulcsot. Ez problémát okozhat a gyors elérés és az erőforrások csatolása során. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Ha a fiókja nem igényelte az MFA használatát a saját bérlője számára, de más bérlők esetében nem, Storage Explorer nem fogja tudni listázni az előfizetéseket. A fiókkal való bejelentkezés után Storage Explorer kérni fogja, hogy adja meg újból a hitelesítő adatait, és végezzen el MFA-t. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* Storage Explorer nem tudott erőforrásokat csatlakoztatni az Azure Germany és az USA kormányzati szervei számára. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Ha két olyan fiókba jelentkezett be, amely ugyanazzal az e-mail-címmel rendelkezik, Storage Explorer időnként nem fogja tudni megjeleníteni az erőforrásokat a fanézetben. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* A lassabb Windowsos gépeken a splash-képernyő esetenként jelentős mennyiségű időt is igénybe veheti. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* A csatlakozási párbeszédpanel akkor is megjelenik, ha csatolva vannak fiókok vagy szolgáltatások. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Új
* A külső erőforrások mellékletei, például az SAS-kapcsolatok és az emulátorok, jelentősen javultak. Ezután:
   * Szabja testre a csatolni kívánt erőforrás megjelenítendő nevét. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Több helyi emulátorhoz is csatolhat különböző portokat használva. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Csatolt erőforrások hozzáadása a gyors eléréshez. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* A Storage Explorer mostantól támogatja a Soft delete használatát. A következőket teheti:
   * A helyreállítható törlési házirend konfigurálásához kattintson a jobb gombbal a Storage-fiók blob-tárolók csomópontjára.
   * A legördülő menüben kattintson az "aktív és törölt Blobok" elemre a navigációs sáv melletti legördülő listában.
   * Törölje a törölt Blobok törlését.

### <a name="fixes"></a>Javítások
* A "CORS-beállítások konfigurálása" művelet már nem érhető el Premium Storage fiókokon, mert Premium Storage fiókok nem támogatják a CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Már van egy közös hozzáférés-aláírási tulajdonság a SAS-hez csatolt szolgáltatásokhoz. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Az "alapértelmezett hozzáférési szintek beállítása" művelet mostantól elérhető a gyors hozzáféréshez rögzített blob-és GPV2-tároló fiókok esetében. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Időnként a Storage Explorer nem fogja tudni megjeleníteni a klasszikus Storage-fiókokat. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Ismert problémák
* Az emulátorok, például az Azure Storage Emulator vagy a Azurite használata esetén az alapértelmezett portokon figyelnie kell a kapcsolatokat. Ellenkező esetben Storage Explorer nem fog tudni csatlakozni hozzájuk.
* Ha a VS for Mac-et használja, és korábban már létrehozott egy egyéni HRE-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma megkerüléséhez törölje a ~/. IdentityService/AadConfigurations. Ha ez nem oldja fel a tiltást, kérjük, véleményezze a [problémát](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* A Azurite még nem teljes mértékben implementálta az összes tárolási API-t. Emiatt előfordulhat, hogy a Azurite fejlesztési tárolóhoz való használata váratlan hibákat vagy viselkedést okoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza a gyors eléréssel is elakad. A fókusz kitöltéséhez frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba javítva lett, de még nincs integrálva az Elektronba.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* Bár a Azure Stack jelenleg nem támogatja a fájlmegosztás használatát, egy fájlmegosztási csomópont továbbra is megjelenik egy csatolt Azure Stack Storage-fiókban.
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)-es frissítést.
* Az Ubuntu 14,04-es felhasználóknak biztosítania kell, hogy a GCC naprakész legyen – ez a következő parancsok futtatásával, majd a gép újraindításával végezhető el:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17,04 rendszerű felhasználók esetén telepítenie kell a GConf-ezt a következő parancsok futtatásával, majd a gép újraindításával:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>1.3.0 verziója
07/09/2018

### <a name="new"></a>Új
* A statikus webhelyek által használt $web tárolók elérése mostantól támogatott. Így könnyedén feltöltheti és kezelheti a webhely által használt fájlokat és mappákat. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* A macOS-ben lévő alkalmazás-sáv újra lett rendszerezve. A változtatások közé tartozik a Fájl menü, néhány billentyűparancs, valamint az alkalmazás menüjében számos új parancs. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* Az Azure US Governmentbe való bejelentkezéshez használt szolgáltatói végpont a következőre változott:https://login.microsoftonline.us/
* Kisegítő lehetőségek: Ha egy képernyőolvasó aktív, a billentyűzetes Navigálás már együttműködik a jobb oldalon található elemek megjelenítéséhez használt táblázatokkal. A nyílbillentyűk segítségével navigáljon a sorokhoz és az oszlopokhoz, és az alapértelmezett műveletek meghívásához adja meg a helyi menüt, és nyissa meg az elemhez tartozó helyi menüt, és váltson át vagy vezérelje a többkijelölést. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Javítások
*  Egyes gépeken a gyermek folyamatok elindításának ideje hosszabb időt vesz igénybe. Ha ez történne, a "gyermek folyamat nem indult el időben" hibaüzenet jelenik meg. Egy alárendelt folyamat elindításához megadott idő mostantól 20 és 90 másodperc közötti értékre nőtt. Ha a probléma továbbra is fennáll, akkor véleményezze a csatolt GitHub-problémát. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* Ha olyan SAS-t használ, amely nem rendelkezik olvasási engedéllyel, akkor nem lehetett feltölteni egy nagyméretű blobot. A feltöltés logikája úgy lett módosítva, hogy működjön ebben a forgatókönyvben. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* Egy tároló nyilvános hozzáférési szintjének beállítása az összes hozzáférési házirendet eltávolítja, és fordítva. Most a nyilvános hozzáférési szint és a hozzáférési szabályzatok megmaradnak a kettő beállításakor. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* A "AccessTierChangeTime" csonkolt a Tulajdonságok párbeszédpanelen. Ezt a problémát megoldottuk. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* A "Microsoft Azure Storage Explorer-" előtag hiányzik az új könyvtár létrehozása párbeszédpanelen. Ezt a problémát megoldottuk. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Kisegítő lehetőségek: az entitás hozzáadása párbeszédpanel nehezen navigálható a kommentár használatakor. Előrelépés történt. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Kisegítő lehetőségek: a műveletek és Tulajdonságok ablaktábla összecsukás/Kibontás gombjának háttérszíne inkonzisztens volt kontrasztos megjelenítés fekete téma hasonló felhasználói felületi vezérlőkkel. A szín megváltozott. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Kisegítő lehetőségek: kontrasztos megjelenítés fekete témában a Tulajdonságok párbeszédpanelen található X gomb fókusz stílusa nem látható. Ezt a problémát megoldottuk. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Kisegítő lehetőségek: a műveletek és tulajdonságok lapokon számos olyan ARIA-érték hiányzik, amely egy subpar képernyőolvasó élményt eredményezett. A hiányzó ARIA-értékek már hozzá lettek adva. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Kisegítő lehetőségek: a bal oldalon összeomlott facsomópontok nem kaptak ARIA-kibővített értéket hamis értékre. Ezt a problémát megoldottuk. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Ismert problémák
* A SAS URI-n keresztül csatolt erőforrásból (például blob-tárolóból) való leválasztás olyan hibát okozhat, amely megakadályozza, hogy a többi melléklet helyesen legyen megjelenítve. A probléma megkerüléséhez csak frissítse a csoport csomópontot. További információért tekintse meg [ezt a problémát](https://github.com/Microsoft/AzureStorageExplorer/issues/537) .
* Ha a VS for Mac-et használja, és korábban már létrehozott egy egyéni HRE-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma megkerüléséhez törölje a ~/. IdentityService/AadConfigurations. Ha ez nem oldja fel a tiltást, kérjük, véleményezze a [problémát](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* A Azurite még nem teljes mértékben implementálta az összes tárolási API-t. Emiatt előfordulhat, hogy a Azurite fejlesztési tárolóhoz való használata váratlan hibákat vagy viselkedést okoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza a gyors eléréssel is elakad. A fókusz kitöltéséhez frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba javítva lett, de még nincs integrálva az Elektronba.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* A Azure Stack nem támogatja a következő szolgáltatásokat, és a Azure Stack használata közben nem várt hibákhoz vezethet:
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)-es frissítést.
* Az Ubuntu 14,04-es felhasználóknak biztosítania kell, hogy a GCC naprakész legyen – ez a következő parancsok futtatásával, majd a gép újraindításával végezhető el:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17,04 rendszerű felhasználók esetén telepítenie kell a GConf-ezt a következő parancsok futtatásával, majd a gép újraindításával:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>1.2.0 verziója
06/12/2018

### <a name="new"></a>Új
* Ha Storage Explorer nem tudja betölteni az előfizetéseket a bérlők csak egy részhalmazára, akkor a sikeres betöltésű előfizetések a meghiúsult bérlők számára külön hibaüzenettel együtt jelennek meg. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* Windows rendszeren, ha egy frissítés elérhető, mostantól kiválaszthatja a "frissítés bezáráskor" lehetőséget. Ha ezt a lehetőséget választja, a frissítéshez tartozó telepítő a Storage Explorer bezárását követően fog futni. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* A fájlmegosztás pillanatképének megtekintésekor a visszaállítási pillanatkép hozzá lett adva a fájlmegosztás-szerkesztő helyi menüjéhez. [#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* A várólista törlése gomb most mindig engedélyezve van. [#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* Az ADFS-Azure Stack való bejelentkezés támogatása újra engedélyezve lett. A Azure Stack 1804-es vagy újabb verziójára van szükség. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Javítások
* Ha olyan fájlmegosztás pillanatképeit tekinti meg, amelynek a neve egy másik fájlmegosztás előtagja ugyanabban a Storage-fiókban, akkor a többi fájlmegosztás pillanatképei is szerepelni fognak. A probléma kijavítva. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* Ha SAS-n keresztül csatolja a fájlt, a fájlmegosztás pillanatképének visszaállítása hibát eredményezhet. A probléma kijavítva. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* Egy blob pillanatképének megtekintésekor a rendszer engedélyezte az előléptetési pillanatképet, amikor az alap blobot és egyetlen pillanatképet választott ki. A művelet mostantól csak akkor engedélyezett, ha egyetlen pillanatkép van kiválasztva. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Ha egyetlen feladat (például egy blob letöltése) elindult, és később sikertelen volt, akkor nem próbálja meg automatikusan újrapróbálkozni, amíg el nem indít egy ugyanilyen típusú feladatot. Az összes feladatnak most újra kell próbálkoznia, függetlenül attól, hogy hány feladat van a várólistán.
* Az újonnan létrehozott blob-tárolók számára megnyitott szerkesztők a GPV2-ben, és a Blob Storage fiókok nem rendelkeznek hozzáférési szintű oszloppal. A probléma kijavítva. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* A hozzáférési szintek oszlopa esetenként nem jelenik meg, ha a Storage-fiók vagy a blob-tároló SAS-n keresztül lett csatolva. Az oszlop ekkor mindig megjelenik, de üres értékkel, ha nincs hozzáférési szintű készlet. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* Az újonnan feltöltött blokk blob hozzáférési szintjeinek beállítása le lett tiltva. A probléma kijavítva. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Ha a "Folytatás lap megnyitása" gomb meghívása a billentyűzet használatával megtörtént, akkor a billentyűzet fókusza elvész. A fókusz ekkor a megnyitott lapra lép. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* A Lekérdezéskészítő lévő lekérdezések esetében a kommentár nem adta meg az aktuális operátor felhasználható leírását. Ez a leíró jellegű. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* A különböző szerkesztők tördelési hivatkozásai nem voltak leíró jellegűek. Úgy módosították, hogy jobban leíró jellegűek legyenek. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* Az entitás hozzáadása párbeszédpanelen a kommentár nem jelent meg, hogy a bemeneti elem melyik oszlop része volt. Az aktuális oszlop neve már szerepel az elem leírásában. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* A választógombok és a jelölőnégyzetek nem rendelkeznek látható szegéllyel a fókuszban. A probléma kijavítva. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Ismert problémák
* Az emulátorok, például az Azure Storage Emulator vagy a Azurite használata esetén az alapértelmezett portokon figyelnie kell a kapcsolatokat. Ellenkező esetben Storage Explorer nem fog tudni csatlakozni hozzájuk.
* Ha a VS for Mac-et használja, és korábban már létrehozott egy egyéni HRE-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma megkerüléséhez törölje a ~/. IdentityService/AadConfigurations. Ha ez nem oldja fel a tiltást, kérjük, véleményezze a [problémát](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* A Azurite még nem teljes mértékben implementálta az összes tárolási API-t. Emiatt előfordulhat, hogy a Azurite fejlesztési tárolóhoz való használata váratlan hibákat vagy viselkedést okoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza a gyors eléréssel is elakad. A fókusz kitöltéséhez frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba javítva lett, de még nincs integrálva az Elektronba.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* Bár a Azure Stack jelenleg nem támogatja a fájlmegosztás használatát, egy fájlmegosztási csomópont továbbra is megjelenik egy csatolt Azure Stack Storage-fiókban.
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)-es frissítést.
* Az Ubuntu 14,04-es felhasználóknak biztosítania kell, hogy a GCC naprakész legyen – ez a következő parancsok futtatásával, majd a gép újraindításával végezhető el:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17,04 rendszerű felhasználók esetén telepítenie kell a GConf-ezt a következő parancsok futtatásával, majd a gép újraindításával:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>1.1.0-es verzió
05/09/2018

### <a name="new"></a>Új
* A Storage Explorer mostantól támogatja a Azurite használatát. Megjegyzés: a Azurite-hez való kapcsolódás az alapértelmezett fejlesztési végpontok hardcoded.
* A Storage Explorer mostantól támogatja a csak a Blobokhoz és a GPV2 tartozó hozzáférési szinteket. További információ a hozzáférési szintekről [.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)
* SAS létrehozásakor már nincs szükség kezdő időpontra.

### <a name="fixes"></a>Javítások
* Az Egyesült államokbeli kormányzati fiókokra vonatkozó előfizetések beolvasása megszakadt. A probléma kijavítva. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* A hozzáférési házirendek lejárati ideje nem lett megfelelően mentve. A probléma kijavítva. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* Egy tárolóban lévő elem SAS URL-címének létrehozásakor az elem neve nem lett hozzáfűzve az URL-címhez. A probléma kijavítva. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* SAS létrehozásakor a lejárt lejárati idő esetenként az alapértelmezett érték. Ennek oka az volt, hogy Storage Explorer az utolsó felhasznált kezdési és lejárati időt használta alapértelmezett értékként. Most, hogy minden alkalommal, amikor megnyitja az SAS párbeszédpanelt, új alapértelmezett értékek jönnek létre. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* A Storage-fiókok közötti másoláskor a rendszer egy 24 órás SAS-t hoz létre. Ha a másolat 24 óránál hosszabb ideig tartott, akkor a másolás sikertelen lesz. Megnövelte az SAS-t az elmúlt 1 hétben, hogy csökkentse annak esélyét, hogy egy lejárt SAS miatt sikertelen volt a másolás. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* Egyes tevékenységek a "Mégse" gombra kattintva nem mindig működnek. A probléma kijavítva. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* Bizonyos tevékenységek esetében az átviteli sebesség helytelen volt. A probléma kijavítva. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* A Nézet menü "Previous" (előző) helyesírása helytelen volt. A szolgáltatás már helyesen van írva. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* A Windows Installer utolsó lapja "Next" (tovább) gombra mutat. Egy "Finish" (Befejezés) gombra változott. [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* A TAB billentyű a HC fekete téma használatakor nem látható a párbeszédpanelek gombjain. Most már látható. [#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* A tevékenység naplójában lévő műveletek "automatikus feloldása" burkolata helytelen. Most már helyes. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* Ha egy táblából töröl egy entitást, akkor a megerősítést kérő párbeszédpanel hibaüzenetet jelenít meg. A párbeszédpanel most egy figyelmeztető ikont használ. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Ismert problémák
* Ha a VS for Mac-et használja, és korábban már létrehozott egy egyéni HRE-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma megkerüléséhez törölje a ~/. IdentityService/AadConfigurations. Ha ez nem oldja fel a tiltást, kérjük, véleményezze a [problémát](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* A Azurite még nem teljes mértékben implementálta az összes tárolási API-t. Emiatt előfordulhat, hogy a Azurite fejlesztési tárolóhoz való használata váratlan hibákat vagy viselkedést okoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza a gyors eléréssel is elakad. A fókusz kitöltéséhez frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba javítva lett, de még nincs integrálva az Elektronba.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* Bár a Azure Stack jelenleg nem támogatja a fájlmegosztás használatát, egy fájlmegosztási csomópont továbbra is megjelenik egy csatolt Azure Stack Storage-fiókban.
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)-es frissítést.
* Az Ubuntu 14,04-es felhasználóknak biztosítania kell, hogy a GCC naprakész legyen – ez a következő parancsok futtatásával, majd a gép újraindításával végezhető el:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17,04 rendszerű felhasználók esetén telepítenie kell a GConf-ezt a következő parancsok futtatásával, majd a gép újraindításával:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>A 1.0.0 verziója
04/16/2018

### <a name="new"></a>Új
* Továbbfejlesztett hitelesítés, amely lehetővé teszi, hogy Storage Explorer ugyanazt a fiókot használja, mint a Visual Studio 2017. A szolgáltatás használatához újra be kell jelentkeznie a fiókjaiba, és újra be kell állítania a szűrt előfizetéseket.
* A HRE által támogatott Azure Stack fiókok esetében Storage Explorer most lekéri Azure Stack előfizetéseket, ha engedélyezve van a "Target Azure Stack". Már nem kell egyéni bejelentkezési környezetet létrehoznia.
* Több parancsikon lett hozzáadva a gyorsabb navigáláshoz. Ezek közé tartoznak a különböző panelek és a szerkesztők közötti váltás. További részletekért tekintse meg a Nézet menüt.
* Storage Explorer visszajelzés most a GitHubon él. A problémák oldalát a bal alsó sarokban található visszajelzés gombra kattintva vagy a következő címen érheti el: [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues) . Nyugodtan javaslatokat tehet, jelenthet problémákat, kérdéseket tehet fel, vagy bármilyen más visszajelzést is küldhet.
* Ha TLS/SSL-tanúsítványokkal kapcsolatos problémákba ütközik, és nem találja a jogsértő tanúsítványt, most már elindíthatja a Storage Explorert a parancssorból a `--ignore-certificate-errors` jelzővel. Ha ezzel a jelzővel indul el, Storage Explorer figyelmen kívül hagyja a TLS/SSL-tanúsítvány hibáit.
* A blob és a file items (helyi) menüjében már van egy "Letöltés" lehetőség.
* Továbbfejlesztett kisegítő lehetőségek és képernyőolvasók támogatása. Ha a kisegítő lehetőségek valamelyikét használja, további információért tekintse meg a [kisegítő lehetőségek dokumentációját](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-accessibility) .
* Storage Explorer mostantól az Electron 1.8.3-t használja

### <a name="breaking-changes"></a>Kompatibilitástörő változások
* Storage Explorer új hitelesítési tárba váltott. A könyvtárra váltás részeként újra be kell jelentkeznie a fiókjaiba, és újra be kell állítania a szűrt előfizetéseket
* A bizalmas adatok titkosítására használt módszer megváltozott. Ez azt eredményezheti, hogy a gyors elérésű elemeket újra hozzá kell adni, és/vagy néhány csatlakoztatott erőforrást újra kell csatolni.

### <a name="fixes"></a>Javítások
* Néhány proxy mögötti felhasználónak a "nem sikerült feloldani" hibaüzenet jelenik meg a csoport blobjának feltöltésével vagy a letöltések megszakításával. A probléma kijavítva.
* Ha bejelentkezésre van szükség a közvetlen hivatkozás használata során, a "Bejelentkezés" parancssorba való kattintáskor egy üres párbeszédpanel jelenik meg. A probléma kijavítva.
* Linux rendszeren, ha Storage Explorer nem tud elindítani egy GPU-folyamat összeomlása miatt, az összeomlásról tájékoztatni fogjuk, és a "--disable-GPU" kapcsolót kell használnia, és a Storage Explorer automatikusan újraindul, ha engedélyezve van a kapcsoló.
* Érvénytelen hozzáférési szabályzatok lettek megnehezítik a hozzáférési házirendek párbeszédablakban. A hozzáférési szabályzatok azonosítói mostantól a piros színnel jelennek meg a láthatóság érdekében.
* A műveletnapló esetenként nagy területtel rendelkezik a tevékenységek különböző részei között. A probléma kijavítva.
* Ha a tábla lekérdezési szerkesztőjében érvénytelen állapotba hagyott egy időbélyeg-záradékot, majd megpróbálta módosítani egy másik záradékot, a szerkesztő lefagy. A szerkesztő most visszaállítja az időbélyegző záradékot az utolsó érvényes állapotba, ha a rendszer egy másik záradékban változást észlel.
* Ha a keresési lekérdezés fanézetbe való beírásakor szünetelteti a keresést, a keresés megkezdődik és a fókuszt a szövegmezőből ellopja. Most explicit módon meg kell kezdenie a keresést a "Enter" billentyű lenyomásával, vagy a Keresés indítása gombra kattintva.
* A "Shared Access Signature" parancs esetenként le lesz tiltva, ha a jobb gombbal rákattint egy fájlmegosztás egyik fájljára. A probléma kijavítva.
* Ha a fókuszban lévő erőforrás-facsomópontot a keresés során kiszűrték, nem lehet fület beilleszteni az erőforrás-fába, és a nyílbillentyűk segítségével navigáljon az erőforrás-fában. Ha a fókuszban lévő erőforrás-fa csomópont el van rejtve, akkor az erőforrás-fa első csomópontja automatikusan fókuszál.
* Egy további elválasztó időnként a szerkesztő eszköztárán látható. A probléma kijavítva.
* A navigációs szövegmező időnként túlcsordulást eredményezne. A probléma kijavítva.
* A blob-és fájlmegosztás-szerkesztő időnként folyamatosan frissül, amikor sok fájlt tölt fel egyszerre. A probléma kijavítva.
* A "mappa statisztikája" szolgáltatásnak nincs célja a fájlmegosztás-Pillanatképek kezelése nézetben. A szolgáltatás már le van tiltva.
* Linux rendszeren a Fájl menü nem jelenik meg. A probléma kijavítva.
* Amikor egy mappát egy fájlmegosztás számára tölt fel, alapértelmezés szerint csak a mappa tartalma lett feltöltve. Most az alapértelmezett viselkedés a mappa tartalmának feltöltése a fájlmegosztás megfelelő mappájába.
* A gombok rendezése több párbeszédablakban megfordult. A probléma kijavítva.
* Különböző biztonsággal kapcsolatos javítások.

### <a name="known-issues"></a>Ismert problémák
* Ritka esetekben előfordulhat, hogy a fa fókusza a gyors eléréssel is elakad. A fókusz kitöltéséhez frissítheti az összeset.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* Bár a Azure Stack jelenleg nem támogatja a fájlmegosztás használatát, egy fájlmegosztási csomópont továbbra is megjelenik egy csatolt Azure Stack Storage-fiókban.
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-felhasználók esetén telepítenie kell a [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)-es frissítést.
* Az Ubuntu 14,04-es felhasználóknak biztosítania kell, hogy a GCC naprakész legyen – ez a következő parancsok futtatásával, majd a gép újraindításával végezhető el:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17,04 rendszerű felhasználók esetén telepítenie kell a GConf-ezt a következő parancsok futtatásával, majd a gép újraindításával:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>0.9.6 verziója
02/28/2018

### <a name="fixes"></a>Javítások
* Egy hiba miatt nem sikerült a várt Blobok vagy fájlok listázása a szerkesztőben. A probléma kijavítva.
* Hiba történt a pillanatkép-nézetek közötti váltás miatt, hogy az elemek helytelenek jelenjenek meg. A probléma kijavítva.

### <a name="known-issues"></a>Ismert problémák
* A Storage Explorer nem támogatja az ADFS-fiókokat.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* Előfordulhat, hogy a Fiókbeállítások panel megmutathatja, hogy újra meg kell adnia a hitelesítő adatokat az előfizetések szűréséhez.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* Bár a Azure Stack jelenleg nem támogatja a fájlmegosztás használatát, egy fájlmegosztási csomópont továbbra is megjelenik egy csatolt Azure Stack Storage-fiókban.
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Az Ubuntu 14,04-es felhasználóknak biztosítania kell, hogy a GCC naprakész legyen – ez a következő parancsok futtatásával, majd a gép újraindításával végezhető el:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17,04 rendszerű felhasználók esetén telepítenie kell a GConf-ezt a következő parancsok futtatásával, majd a gép újraindításával:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>0.9.5 verziója
02/06/2018

### <a name="new"></a>Új

* Fájlmegosztás-Pillanatképek támogatása:
    * Pillanatképek létrehozása és kezelése a fájlmegosztás számára.
    * A tallózással könnyedén válthat a fájlmegosztás pillanatképei között.
    * A fájlok korábbi verzióinak visszaállítása.
* A Azure Data Lake Store előzetes verziójának támogatása:
    * Több fiók között csatlakozhat a ADLS-erőforrásokhoz.
    * ADLS-erőforrások kapcsolódása és megosztása ADL URI-k használatával.
    * A fájl-és mappaengedélyek alapszintű műveleteit rekurzív módon végezze el.
    * Egyedi mappák rögzítése a gyors hozzáférés érdekében.
    * Mappa statisztikáinak megjelenítése

### <a name="fixes"></a>Javítások
* Az indítási teljesítmény fejlesztése.
* Különböző hibajavítások.

### <a name="known-issues"></a>Ismert problémák
* A Storage Explorer nem támogatja az ADFS-fiókokat.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* Előfordulhat, hogy a Fiókbeállítások panel megmutathatja, hogy újra meg kell adnia a hitelesítő adatokat az előfizetések szűréséhez.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* Bár a Azure Stack jelenleg nem támogatja a fájlmegosztás használatát, egy fájlmegosztási csomópont továbbra is megjelenik egy csatolt Azure Stack Storage-fiókban.
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Az Ubuntu 14,04-es felhasználóknak biztosítania kell, hogy a GCC naprakész legyen – ez a következő parancsok futtatásával, majd a gép újraindításával végezhető el:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17,04 rendszerű felhasználók esetén telepítenie kell a GConf-ezt a következő parancsok futtatásával, majd a gép újraindításával:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>0.9.4 és 0.9.3 verzió
01/21/2018

### <a name="new"></a>Új
* A meglévő Storage Explorer ablakát a rendszer újra felhasználja:
    * Storage Explorer-ben generált közvetlen hivatkozások megnyitása.
    * Storage Explorer megnyitása a portálról.
    * Storage Explorer megnyitása az Azure Storage VS Code bővítménnyel (hamarosan elérhető).
* Új Storage Explorer ablak megnyitásának lehetősége a Storage Explorer belülről.
    * A Windows esetében egy "új ablak" lehetőség található a Fájl menü és a feladat sávjának helyi menüjében.
    * Mac esetén az alkalmazás menüben az új ablak lehetőség van kiválasztva.

### <a name="fixes"></a>Javítások
* Kijavítva egy biztonsági problémát. A lehető leghamarabb frissítsen a 0.9.4.
* A régi tevékenységek nem lettek megfelelően törölve. Ez hatással volt a hosszú ideig futó feladatok teljesítményére. Mostantól megfelelően törlődnek.
* A nagy mennyiségű fájlt és könyvtárat érintő műveletek időnként Storage Explorer lefagyását okozzák. A fájlmegosztás Azure-ra irányuló kérései mostantól szabályozva vannak a rendszererőforrás-használat korlátozására.

### <a name="known-issues"></a>Ismert problémák
* A Storage Explorer nem támogatja az ADFS-fiókokat.
* A "View Explorer" és a "Fiókkezelés megtekintése" billentyűparancsoknak CTRL/cmd + SHIFT + E és CTRL/cmd + SHIFT + A billentyűkombinációnak kell lenniük.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* Előfordulhat, hogy a Fiókbeállítások panel megmutathatja, hogy újra meg kell adnia a hitelesítő adatokat az előfizetések szűréséhez.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* Bár a Azure Stack jelenleg nem támogatja a fájlmegosztás használatát, egy fájlmegosztási csomópont továbbra is megjelenik egy csatolt Azure Stack Storage-fiókban.
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer --disable-gpu
    ```

* Az Ubuntu 14,04-es felhasználóknak biztosítania kell, hogy a GCC naprakész legyen – ez a következő parancsok futtatásával, majd a gép újraindításával végezhető el:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17,04 rendszerű felhasználók esetén telepítenie kell a GConf-ezt a következő parancsok futtatásával, majd a gép újraindításával:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>0.9.2 verziója
11/01/2017

### <a name="hotfixes"></a>Gyorsjavítások
* Váratlan adatváltozások voltak lehetségesek, amikor a helyi időzónától függően EDM. dátum és idő értékeket szerkeszt. A szerkesztő mostantól egy egyszerű szövegmezőt használ, amely precíz, konzisztens vezérlést biztosít a EDM. DateTime értékekhez képest.
* Blobok csoportjának feltöltése/letöltése, ha a név és a kulcs csatolása nem indul el. A probléma kijavítva.
* Korábban Storage Explorer csak akkor kéri egy elavult fiók ismételt hitelesítését, ha egy vagy több fiók előfizetése ki van választva. Most Storage Explorer akkor is rákérdez, ha a fiók teljesen ki van szűrve.
* Az Azure US government végpontok tartománya helytelen. Megoldódott.
* Néha nehéz rákattintani a Manage accounts (fiókok kezelése) panel Apply (alkalmaz) gombjára. Ez már nem történhet meg.

### <a name="new"></a>Új
* A Azure Cosmos DB előzetes verziójának támogatása:
    * [Online dokumentáció](./cosmos-db/storage-explorer.md)
    * Adatbázisok és gyűjtemények létrehozása
    * Adatok módosítása
    * Dokumentumok lekérdezése, létrehozása vagy törlése
    * Tárolt eljárások, felhasználó által definiált függvények vagy eseményindítók frissítése
    * Kapcsolati karakterláncok használata az adatbázisok csatlakoztatásához és kezeléséhez
* Javítottuk a sok kis blob feltöltésének/letöltésének teljesítményét.
* Egy "újrapróbálkozás az összes művelettel" műveletet adott meg, ha hiba történt egy blob-feltöltési csoport vagy egy blob letöltési csoportjában.
* A Storage Explorer most szünetelteti az iterációt a blob feltöltése/letöltése során, ha az észleli, hogy a hálózati kapcsolat megszakadt. Ezután folytathatja az iterációt a hálózati kapcsolatok újbóli létrehozása után.
* Lehetőség van az "összes lezárása", a "Bezárás mások" és a "Bezárás" fülek hozzáadására a helyi menün keresztül.
* Storage Explorer mostantól natív párbeszédpaneleket és natív helyi menüket használ.
* A Storage Explorer mostantól elérhető. A tökéletesítések a következők:
    * Továbbfejlesztett képernyőolvasó-támogatás, a Windows rendszerhez készült NVDA, valamint a Mac-hez készült kommentár
    * Javított kontrasztos
    * A billentyűzet-tabbing és a billentyűzet fókuszának javításai

### <a name="fixes"></a>Javítások
* Ha egy érvénytelen Windows-fájlnévvel rendelkező blobot próbált megnyitni vagy letölteni, a művelet sikertelen lesz. A Storage Explorer mostantól felismeri, ha a blob neve érvénytelen, és megkérdezi, hogy szeretné-e kódolni vagy kihagyni a blobot. A Storage Explorer azt is felismeri, hogy a fájl neve kódolásra van-e kódolva, és megkérdezi, hogy szeretné-e dekódolni a feltöltés előtt.
* A blob feltöltése során a cél blob-tároló szerkesztője esetenként nem fog megfelelően frissülni. A probléma kijavítva.
* A kapcsolatok és SAS URI-k romlott különböző formáinak támogatása. Minden ismert problémát megoldottunk, de küldjön visszajelzést, ha további problémákba ütközik.
* A frissítési értesítés megszakadt a 0.9.0 egyes felhasználói számára. Ezt a problémát megjavítottuk, és a hiba által érintettek számára [a Storage Explorer legújabb](https://azure.microsoft.com/features/storage-explorer/)verzióját manuálisan is letöltheti.

### <a name="known-issues"></a>Ismert problémák
* A Storage Explorer nem támogatja az ADFS-fiókokat.
* A "View Explorer" és a "Fiókkezelés megtekintése" billentyűparancsoknak CTRL/cmd + SHIFT + E és CTRL/cmd + SHIFT + A billentyűkombinációnak kell lenniük.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* Előfordulhat, hogy a Fiókbeállítások panel megmutathatja, hogy újra meg kell adnia a hitelesítő adatokat az előfizetések szűréséhez.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* Bár a Azure Stack jelenleg nem támogatja a fájlmegosztás használatát, egy fájlmegosztási csomópont továbbra is megjelenik egy csatolt Azure Stack Storage-fiókban.
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer --disable-gpu
    ```

* Az Ubuntu 14,04-es felhasználóknak biztosítania kell, hogy a GCC naprakész legyen – ez a következő parancsok futtatásával, majd a gép újraindításával végezhető el:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17,04 rendszerű felhasználók esetén telepítenie kell a GConf-ezt a következő parancsok futtatásával, majd a gép újraindításával:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>0.9.1 és 0.9.0 verzió
10/20/2017
### <a name="new"></a>Új
* A Azure Cosmos DB előzetes verziójának támogatása:
    * [Online dokumentáció](./cosmos-db/storage-explorer.md)
    * Adatbázisok és gyűjtemények létrehozása
    * Adatok módosítása
    * Dokumentumok lekérdezése, létrehozása vagy törlése
    * Tárolt eljárások, felhasználó által definiált függvények vagy eseményindítók frissítése
    * Kapcsolati karakterláncok használata az adatbázisok csatlakoztatásához és kezeléséhez
* Javítottuk a sok kis blob feltöltésének/letöltésének teljesítményét.
* Egy "újrapróbálkozás az összes művelettel" műveletet adott meg, ha hiba történt egy blob-feltöltési csoport vagy egy blob letöltési csoportjában.
* A Storage Explorer most szünetelteti az iterációt a blob feltöltése/letöltése során, ha az észleli, hogy a hálózati kapcsolat megszakadt. Ezután folytathatja az iterációt a hálózati kapcsolatok újbóli létrehozása után.
* Lehetőség van az "összes lezárása", a "Bezárás mások" és a "Bezárás" fülek hozzáadására a helyi menün keresztül.
* Storage Explorer mostantól natív párbeszédpaneleket és natív helyi menüket használ.
* A Storage Explorer mostantól elérhető. A tökéletesítések a következők:
    * Továbbfejlesztett képernyőolvasó-támogatás, a Windows rendszerhez készült NVDA, valamint a Mac-hez készült kommentár
    * Javított kontrasztos
    * A billentyűzet-tabbing és a billentyűzet fókuszának javításai

### <a name="fixes"></a>Javítások
* Ha egy érvénytelen Windows-fájlnévvel rendelkező blobot próbált megnyitni vagy letölteni, a művelet sikertelen lesz. A Storage Explorer mostantól felismeri, ha a blob neve érvénytelen, és megkérdezi, hogy szeretné-e kódolni vagy kihagyni a blobot. A Storage Explorer azt is felismeri, hogy a fájl neve kódolásra van-e kódolva, és megkérdezi, hogy szeretné-e dekódolni a feltöltés előtt.
* A blob feltöltése során a cél blob-tároló szerkesztője esetenként nem fog megfelelően frissülni. A probléma kijavítva.
* A kapcsolatok és SAS URI-k romlott különböző formáinak támogatása. Minden ismert problémát megoldottunk, de küldjön visszajelzést, ha további problémákba ütközik.
* A frissítési értesítés megszakadt a 0.9.0 egyes felhasználói számára. Ezt a problémát megjavítottuk, és a hiba által érintettek számára [a Storage Explorer legújabb](https://azure.microsoft.com/features/storage-explorer/) verzióját manuálisan is letöltheti.

### <a name="known-issues"></a>Ismert problémák
* A Storage Explorer nem támogatja az ADFS-fiókokat.
* A "View Explorer" és a "Fiókkezelés megtekintése" billentyűparancsoknak CTRL/cmd + SHIFT + E és CTRL/cmd + SHIFT + A billentyűkombinációnak kell lenniük.
* A Azure Stack célzásakor előfordulhat, hogy bizonyos fájlokat a hozzáfűző Blobok feltöltésével nem sikerül feltölteni.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ennek az az oka, hogy az itt leírtak szerint az itt leírt megszakítási szűrőt használjuk.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* Előfordulhat, hogy a Fiókbeállítások panel megmutathatja, hogy újra meg kell adnia a hitelesítő adatokat az előfizetések szűréséhez.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* Bár a Azure Stack jelenleg nem támogatja a fájlmegosztás használatát, egy fájlmegosztási csomópont továbbra is megjelenik egy csatolt Azure Stack Storage-fiókban.
* Az Storage Explorer által használt elektron-rendszerhéj nem tud gondot okozni néhány GPU-s (grafikus feldolgozási egység) hardveres gyorsítással. Ha Storage Explorer egy üres (üres) főablakot jelenít meg, akkor próbálja meg elindítani a parancssorból a Storage Explorert, és tiltsa le a GPU-gyorsítást a kapcsoló hozzáadásával `--disable-gpu` :

    ```
    ./StorageExplorer --disable-gpu
    ```

* Az Ubuntu 14,04-es felhasználóknak biztosítania kell, hogy a GCC naprakész legyen – ez a következő parancsok futtatásával, majd a gép újraindításával végezhető el:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17,04 rendszerű felhasználók esetén telepítenie kell a GConf-ezt a következő parancsok futtatásával, majd a gép újraindításával:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>0.8.16 verziója
8/21/2017

### <a name="new"></a>Új
* Amikor megnyit egy blobot, Storage Explorer kéri a letöltött fájl feltöltését, ha a rendszer változást észlel
* Továbbfejlesztett Azure Stack bejelentkezési élmény
* Javítottuk a sok kis fájl feltöltésének vagy letöltésének teljesítményét egy időben


### <a name="fixes"></a>Javítások
* Egyes blob-típusok esetén a feltöltési ütközés során a "csere" gombra kattintva időnként a feltöltés újraindul.
* A 0.8.15 verzióban a feltöltések időnként 99%-nál is elakadnak.
* Ha fájlokat tölt fel egy fájlmegosztás számára, ha úgy döntött, hogy egy olyan könyvtárba tölti fel a fájlt, amely még nem létezik, akkor a feltöltés sikertelen lesz.
* A Storage Explorer helytelenül eredményezte a megosztott hozzáférési aláírások és a táblák lekérdezése időbélyegét.


### <a name="known-issues"></a>Ismert problémák
* A név és a kulcs típusú kapcsolatok karakterláncának használata jelenleg nem működik. A következő kiadásban lesz kijavítva. Addig is használhatja a csatolást a név és a kulcs használatával.
* Ha érvénytelen Windows-fájlnévvel próbál megnyitni egy fájlt, a letöltés nem található hibaüzenetet eredményez.
* Ha egy feladatban a "Mégse" gombra kattint, eltarthat egy ideig, amíg a feladat meg nem szakítja a műveletet. Ez az Azure Storage-csomópont függvénytárának korlátozása.
* A blob feltöltésének befejezése után a feltöltést kezdeményező lap frissül. Ez a korábbi viselkedés változása, és azt is eredményezi, hogy vissza kell térnie a tároló gyökeréhez.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, akkor újra kell indítania, hogy Storage Explorer elfelejteni a döntést.
* Előfordulhat, hogy a Fiókbeállítások panel megmutathatja, hogy újra meg kell adnia a hitelesítő adatokat az előfizetések szűréséhez.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* Bár a Azure Stack jelenleg nem támogatja a fájlmegosztás használatát, egy fájlmegosztási csomópont továbbra is megjelenik egy csatolt Azure Stack Storage-fiókban.
* Az Ubuntu 14,04-es felhasználóknak biztosítania kell, hogy a GCC naprakész legyen – ez a következő parancsok futtatásával, majd a gép újraindításával végezhető el:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17,04 rendszerű felhasználók esetén telepítenie kell a GConf-ezt a következő parancsok futtatásával, majd a gép újraindításával:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>0.8.14 verziója
06/22/2017

### <a name="new"></a>Új

* Frissítette az Electron-verziót a 1.7.2-be, hogy kihasználhassa több kritikus biztonsági frissítést
* Mostantól gyorsan elérheti az online hibaelhárítási útmutatót a Súgó menüből.
* Storage Explorer hibaelhárítási [útmutató][2]
* [Útmutató][3] Azure stack-előfizetéshez való csatlakozáshoz

### <a name="known-issues"></a>Ismert problémák

* A mappa törlése megerősítő párbeszédpanelen lévő gombok nem regisztrálják magukat a Linuxon futó egérkattintással. megkerülő megoldásként használja az ENTER billentyűt.
* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, újra kell indítania a döntést, hogy Storage Explorer elfelejteni a határozatot
* Ha a Blobok több mint 3 csoportja vagy a fájlok egy időben való feltöltésekor hibák fordulnak elő
* Előfordulhat, hogy a Fiókbeállítások panelen újra meg kell adnia a hitelesítő adatokat, hogy szűrni lehessen az előfizetéseket
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* Bár a Azure Stack jelenleg nem támogatja a fájlmegosztás használatát, a fájlmegosztás csomópont továbbra is megjelenik egy csatolt Azure Stack Storage-fiókban.
* Az Ubuntu 14,04 telepítésének szüksége van a GCC verziófrissítésére vagy frissítésére – a frissítés lépései a következők:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>0.8.13 verziója
05/12/2017

#### <a name="new"></a>Új

* Storage Explorer hibaelhárítási [útmutató][2]
* [Útmutató][3] Azure stack-előfizetéshez való csatlakozáshoz

#### <a name="fixes"></a>Javítások

* Kijavítva: a fájlfeltöltés nagy eséllyel okozta a memória hibáját.
* Kijavítva: mostantól PIN-kóddal vagy intelligens kártyával is bejelentkezhet
* Kijavítva: a Megnyitás a portálon mostantól működik az Azure China 21Vianet, az Azure Germany, az Azure US government és a Azure Stack
* Kijavítva: egy mappa blob-tárolóba való feltöltésekor előfordulhat, hogy egy "szabálytalan művelet" hiba fordul elő.
* Kijavítva: az összes kijelölése le lett tiltva a pillanatképek kezelése közben
* Kijavítva: a pillanatképek tulajdonságainak megtekintése után előfordulhat, hogy felülírja az alap blob metaadatait

#### <a name="known-issues"></a>Ismert problémák

* Ha nem megfelelő PIN-kódot vagy SmartCard-tanúsítványt választ, újra kell indítania a döntést, hogy Storage Explorer elfelejteni a határozatot
* Nagyítás vagy kicsinyítés közben a nagyítási szint az alapértelmezett szintre térhet vissza.
* Ha a Blobok több mint 3 csoportja vagy a fájlok egy időben való feltöltésekor hibák fordulnak elő
* Előfordulhat, hogy a Fiókbeállítások panelen újra meg kell adnia a hitelesítő adatokat, hogy szűrni lehessen az előfizetéseket
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* Bár a Azure Stack jelenleg nem támogatja a fájlmegosztás használatát, egy fájlmegosztási csomópont továbbra is megjelenik egy csatolt Azure Stack Storage-fiókban.
* Az Ubuntu 14,04 telepítésének szüksége van a GCC verziófrissítésére vagy frissítésére – a frissítés lépései a következők:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>0.8.12-és 0.8.11-és 0.8.10-verziók
04/07/2017

#### <a name="new"></a>Új

* A Storage Explorer mostantól automatikusan bezárul, amikor frissítést telepít a frissítési értesítésből
* A helyszíni gyors hozzáférés nagyobb élményt biztosít a gyakran használt erőforrások kezeléséhez
* A blob Container Editorban mostantól megtekintheti, hogy melyik virtuális gép tartalmaz egy bérelt blobot
* Most már összecsukhatja a bal oldali panelt
* A felderítés mostantól a letöltéssel megegyező időpontban fut
* Az erőforrás vagy a kijelölés méretének megtekintéséhez használja a blob tárolóban, a fájlmegosztásban és a táblák szerkesztőjében található statisztikát.
* Most már bejelentkezhet Azure Active Directory (HRE)-alapú Azure Stack-fiókokba.
* Most már feltöltheti az archív fájlokat a 32MB-ből a Premium Storage-fiókokba
* Továbbfejlesztett akadálymentesítési támogatás
* Mostantól hozzáadhat megbízható alap-64 kódolású X. 509 TLS/SSL-tanúsítványokat az Edit- &gt; SSL tanúsítványok – &gt; tanúsítványok importálása lehetőséggel

#### <a name="fixes"></a>Javítások

* Kijavítva: a fiók hitelesítő adatainak frissítése után a fanézet esetenként nem frissül automatikusan
* Kijavítva: SAS létrehozása az Emulator-várólistákhoz és-táblákhoz érvénytelen URL-címet eredményezne
* Kijavítva: a Premium Storage-fiókok mostantól kiterjeszthetők a proxy engedélyezésekor
* Kijavítva: az Apply (alkalmaz) gomb a fiókok kezelése lapon nem fog működni, ha 1 vagy 0 fiók van kiválasztva
* Kijavítva: az ütközési felbontást igénylő Blobok feltöltése a 0.8.11-ben sikertelen lehet – kijavítva
* Kijavítva: a visszajelzés küldése megszakadt a 0.8.11-ben – javítva a 0.8.12-ben

#### <a name="known-issues"></a>Ismert problémák

* A 0.8.10-re való frissítés után frissítenie kell az összes hitelesítő adatot.
* Nagyítás vagy kicsinyítés közben a nagyítási szint az alapértelmezett szintre térhet vissza.
* Ha a Blobok több mint 3 csoportja vagy egy időben feltöltött fájl is hibákat okoz.
* Előfordulhat, hogy a Fiókbeállítások panelen újra meg kell adnia a hitelesítő adatokat, hogy szűrni lehessen az előfizetéseket.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások összes többi tulajdonsága és metaadatai az Átnevezés során megmaradnak.
* Bár a Azure Stack jelenleg nem támogatja a fájlmegosztás használatát, egy fájlmegosztási csomópont továbbra is megjelenik egy csatolt Azure Stack Storage-fiókban.
* Az Ubuntu 14,04 telepítésének szüksége van a GCC verziófrissítésére vagy frissítésére – a frissítés lépései a következők:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>0.8.9 és 0.8.8 verzió
02/23/2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>Új

* Storage Explorer 0.8.9 automatikusan letölti a legújabb verziót a frissítésekhez.
* Gyorsjavítás: Ha egy portálon létrehozott SAS URI-t használ a Storage-fiók csatolásához, a rendszer hibát eredményezne.
* Most már létrehozhatja, kezelheti és előléptetheti a blob-pillanatképeket.
* Most már bejelentkezhet az Azure China 21Vianet, az Azure Germany és az USA Azure Government-fiókjaiba.
* Most már módosíthatja a nagyítási szintet. A nagyításhoz, a nagyításhoz és a nagyítás visszaállításához használja a Nézet menü beállításait.
* Az Unicode-karakterek mostantól a blobok és fájlok felhasználói metaadatainak esetében is támogatottak.
* A kisegítő lehetőségek fejlesztése.
* A következő verzió kibocsátási megjegyzései megtekinthetők a frissítési értesítésből. A Súgó menü aktuális kibocsátási megjegyzéseit is megtekintheti.

#### <a name="fixes"></a>Javítások

* Kijavítva: a verziószám mostantól helyesen jelenik meg a Vezérlőpulton a Windows rendszerben
* Kijavítva: a keresés már nem korlátozódik 50 000 csomópontra
* Kijavítva: a fájlmegosztás feltöltése örökre megpördült, ha a cél könyvtára még nem létezett.
* Javítva: továbbfejlesztett stabilitás a hosszú feltöltések és letöltések számára

#### <a name="known-issues"></a>Ismert problémák

* Nagyítás vagy kicsinyítés közben a nagyítási szint az alapértelmezett szintre térhet vissza.
* A gyors hozzáférés csak az előfizetés-alapú elemek esetében működik. Ebben a kiadásban nem támogatottak a helyi erőforrások vagy a kulcs vagy SAS-token használatával csatolt erőforrások.
* A cél erőforráshoz való belépéshez igénybe vehet néhány másodpercig, attól függően, hogy hány erőforrásra van szüksége.
* Ha a Blobok több mint 3 csoportja vagy egy időben feltöltött fájl is hibákat okoz.

12/16/2016
### <a name="version-087"></a>0.8.7 verziója

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Új

* Megadhatja, hogyan oldják fel az ütközéseket a frissítések elején, a letöltés vagy a másolás munkamenetet a tevékenységek ablakban.
* Vigye az egérmutatót egy lapra a tárolási erőforrás teljes elérési útjának megtekintéséhez
* Ha rákattint egy lapra, szinkronizálja a helyét a bal oldali navigációs panelen.

#### <a name="fixes"></a>Javítások

* Kijavítva: a Storage Explorer mostantól egy megbízható alkalmazás Mac gépen
* Javítva: az Ubuntu 14,04 ismét támogatott
* Kijavítva: időnként a fiók hozzáadása KEZELŐFELÜLET villan fel az előfizetések betöltésekor
* Kijavítva: néha nem minden tárolási erőforrás szerepel a bal oldali navigációs panelen.
* Kijavítva: a művelet ablaktábla időnként üres műveleteket jelenít meg
* Kijavítva: a rendszer megőrzi az utolsó lezárt munkamenet ablakának méretét.
* Kijavítva: több fület is megnyithat ugyanahhoz az erőforráshoz a helyi menü használatával.

#### <a name="known-issues"></a>Ismert problémák

* A gyors hozzáférés csak az előfizetés-alapú elemek esetében működik. Ebben a kiadásban nem támogatottak a helyi erőforrások vagy a kulcs vagy SAS-token használatával csatolt erőforrások.
* A cél erőforráshoz való belépéshez igénybe vehet néhány másodpercet, attól függően, hogy hány erőforrást tartalmaz
* Ha a Blobok több mint 3 csoportja vagy a fájlok egy időben való feltöltésekor hibák fordulnak elő
* A keresés a nagyjából 50 000 csomópontok közötti keresést végzi – ezt követően a teljesítmény hatással lehet vagy nem kezelt kivételt okozhat.
* Ha első alkalommal használja a Storage Explorer macOS rendszeren, több kérdés jelenhet meg, amely arra kéri a felhasználótól, hogy hozzáférjen a kulcstartóhoz. Javasoljuk, hogy válassza a mindig Engedélyezés lehetőséget, hogy a kérdés ne jelenjen meg újra

11/18/2016
### <a name="version-086"></a>0.8.6 verziója

#### <a name="new"></a>Új

* Most már rögzítheti a leggyakrabban használt szolgáltatásokat a gyors eléréshez az egyszerű navigálás érdekében
* Mostantól több szerkesztőt is megnyithat különböző lapokon. Egyetlen kattintással megnyitható egy ideiglenes lap; Kattintson ide duplán egy állandó lap megnyitásához. Az ideiglenes lapra is kattinthat, hogy az állandó lapot jelenítse meg
* Észrevehető teljesítmény-és stabilitási előrelépés történt a feltöltések és a letöltések esetében, különösen a gyors gépek nagy fájljai esetében
* Az üres "virtuális" mappák már létrehozhatók a blob-tárolókban.
* A hatókörön belüli keresést újra bevezetjük az új, továbbfejlesztett alsztring-kereséssel, így mostantól két lehetőség közül választhat:
    * Globális keresés – csak adja meg a keresési kifejezést a keresési szövegmezőbe
    * Hatókörön belüli keresés – kattintson a csomópont melletti nagyító ikonra, majd vegyen fel egy keresési kifejezést az elérési út végére, vagy kattintson a jobb gombbal, és válassza a "keresés innen" lehetőséget.
* A következő témákat adta hozzá: Light (alapértelmezett), sötét, kontrasztos megjelenítés Black és kontrasztos megjelenítés White. Lépjen a Szerkesztés – &gt; témák menüpontra a rájuk vonatkozó beállítások módosításához
* A blobok és a fájlok tulajdonságai módosíthatók
* Mostantól támogatjuk a kódolt (Base64) és a nem kódolt üzenetsor-üzeneteket
* Linux rendszeren a 64 bites operációs rendszer már szükséges. Ebben a kiadásban csak a 64 bites Ubuntu 16.04.1-t támogatjuk
* Frissítettük a logót!

#### <a name="fixes"></a>Javítások

* Kijavítva: képernyő-fagyasztási problémák
* Kijavítva: fokozott biztonság
* Kijavítva: néha duplikált csatolt fiókok jelenhetnek meg
* Kijavítva: A nem definiált tartalomtípusú Blobok kivételt hozhatnak létre.
* Kijavítva: a lekérdezési panel megnyitása üres táblán nem lehetséges
* Kijavítva: a keresési hibák változói
* Kijavítva: megnövelte a 50 és 100 között betöltött erőforrások számát, ha a "betöltés" gombra kattint.
* Kijavítva: az első futtatáskor, ha egy fiók be van jelentkezve, mostantól a fiókhoz tartozó összes előfizetést kiválasztjuk.

#### <a name="known-issues"></a>Ismert problémák

* A Storage Explorer ezen kiadása nem fut az Ubuntu 14,04
* Ha több lapot szeretne megnyitni ugyanahhoz az erőforráshoz, ne folyamatosan kattintson ugyanarra az erőforrásra. Kattintson egy másik erőforrásra, majd lépjen vissza, és kattintson az eredeti erőforrásra, és nyissa meg újra egy másik lapon
* A gyors hozzáférés csak az előfizetés-alapú elemek esetében működik. Ebben a kiadásban nem támogatottak a helyi erőforrások vagy a kulcs vagy SAS-token használatával csatolt erőforrások.
* A cél erőforráshoz való belépéshez igénybe vehet néhány másodpercet, attól függően, hogy hány erőforrást tartalmaz
* Ha a Blobok több mint 3 csoportja vagy a fájlok egy időben való feltöltésekor hibák fordulnak elő
* A keresés a nagyjából 50 000 csomópontok közötti keresést végzi – ezt követően a teljesítmény hatással lehet vagy nem kezelt kivételt okozhat.

10/03/2016
### <a name="version-085"></a>0.8.5 verziója

#### <a name="new"></a>Új

* Mostantól a portál által generált SAS-kulcsok használatával csatolhatók a Storage-fiókokhoz és-erőforrásokhoz

#### <a name="fixes"></a>Javítások

* Kijavítva: a keresés során előforduló versenyhelyzet miatt a csomópontok nem bővíthetővé válnak
* Kijavítva: a "HTTP használata" nem működik, ha a fiók nevével és kulcsával csatlakozik a Storage-fiókokhoz.
* Kijavítva: a SAS-kulcsok (a speciális portálon létrehozott) "záró perjel" hibát adnak vissza.
* Kijavítva: tábla importálásával kapcsolatos problémák
    * Néha a partíciós kulcs és a sor kulcsa fordított volt.
    * A "NULL" partíciós kulcsok nem olvashatók

#### <a name="known-issues"></a>Ismert problémák

* A keresés a nagyjából 50 000 csomópontok közötti keresést végzi – ezt követően a teljesítmény hatással lehet
* Azure Stack jelenleg nem támogatja a fájlokat, ezért a fájlok kibontására tett kísérlet hibát jelez

09/12/2016
### <a name="version-084"></a>0.8.4 verziója

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Új

* Közvetlen hivatkozásokat készíthet a Storage-fiókok, tárolók, várólisták, táblák vagy fájlmegosztás számára az erőforrások megosztásához és egyszerű eléréséhez – Windows és Mac OS támogatás
* Keresse meg a blob-tárolókat, a táblákat, a várólistákat, a fájlmegosztást vagy a Storage-fiókokat a keresőmezőbe.
* Most már csoportosíthatjuk a záradékokat a tábla lekérdezés-szerkesztőben
* BLOB-tárolók, fájlmegosztás, táblák, blobok, blob-mappák, fájlok és könyvtárak átnevezése és másolása/beillesztése a SAS-hez csatolt fiókok és tárolók között
* BLOB-tárolók és fájlmegosztás átnevezése és másolása most megőrzi a tulajdonságokat és a metaadatokat

#### <a name="fixes"></a>Javítások

* Kijavítva: a Table entitások nem szerkeszthetők, ha logikai vagy bináris tulajdonságokat tartalmaznak

#### <a name="known-issues"></a>Ismert problémák

* A keresés a nagyjából 50 000 csomópontok közötti keresést végzi – ezt követően a teljesítmény hatással lehet

08/03/2016
### <a name="version-083"></a>0.8.3 verziója

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Új

* Tárolók, táblák, fájlmegosztás átnevezése
* Továbbfejlesztett lekérdezés-szerkesztői élmény
* Lekérdezések mentésének és betöltésének lehetősége
* Közvetlen hivatkozások a Storage-fiókokra vagy-tárolóra, a várólistákra, a táblázatokra vagy a fájlmegosztás megosztására és az erőforrások egyszerű elérésére (csak Windows macOS-támogatás hamarosan elérhető)
* CORS-szabályok kezelése és konfigurálása

#### <a name="fixes"></a>Javítások

* Kijavítva: a Microsoft-fiókoknak minden 8-12 óránként újra kell hitelesíteniük magukat

#### <a name="known-issues"></a>Ismert problémák

* Időnként előfordulhat, hogy a felhasználói felület megfagyott – maximalizálja az ablakot a probléma megoldásához
* a macOS telepítéséhez emelt szintű engedélyekre lehet szükség
* Előfordulhat, hogy a Fiókbeállítások panelen újra meg kell adnia a hitelesítő adatokat, hogy szűrni lehessen az előfizetéseket
* A fájlmegosztás, a blob-tárolók és a táblák átnevezése nem őrzi meg a tárolók metaadatait vagy egyéb tulajdonságait, például a fájlmegosztás kvótáját, a nyilvános hozzáférési szintet vagy a hozzáférési házirendeket.
* A Blobok átnevezése (egyénileg vagy átnevezett blob-tárolón belül) nem őrzi meg a pillanatképeket. A Blobok, fájlok és entitások minden egyéb tulajdonsága és metaadatai megmaradnak az Átnevezés során
* Az erőforrások másolása vagy átnevezése nem működik SAS-vel csatlakoztatott fiókokon belül

07/07/2016
### <a name="version-082"></a>0.8.2 verziója

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Új

* A Storage-fiókok előfizetések szerint vannak csoportosítva; a (helyi és csatolt) csomópont alatt a kulcs-vagy SAS-n keresztül csatolt fejlesztői tárolók és erőforrások láthatók.
* Kijelentkezés a fiókok közül az "Azure-fiók beállításai" panelen
* Proxybeállítások konfigurálása a bejelentkezés engedélyezéséhez és kezeléséhez
* BLOB-bérletek létrehozása és megszakítása
* BLOB-tárolók,-várólisták,-táblák és-fájlok megnyitása egyetlen kattintással

#### <a name="fixes"></a>Javítások

* Kijavítva: a .NET-vagy Java-kódtárakkal beszúrt üzenetsor-üzenetek nem lettek megfelelően dekódolva a Base64-ből
* Kijavítva: $metrics táblák nem jelennek meg Blob Storage-fiókok esetében
* Kijavítva: a táblák csomópont nem működik a helyi (fejlesztői) tároláshoz

#### <a name="known-issues"></a>Ismert problémák

* a macOS telepítéséhez emelt szintű engedélyekre lehet szükség

06/15/2016
### <a name="version-080"></a>0.8.0 verziója

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Új

* Fájlmegosztás támogatása: fájlok és könyvtárak, SAS URI-k (létrehozás és kapcsolat) megtekintése, feltöltése, letöltése, másolása
* Továbbfejlesztett felhasználói élmény az SAS URI-k vagy a fiók kulcsainak tárolásához való csatlakozáshoz
* Tábla lekérdezési eredményeinek exportálása
* Táblázatos oszlopok átrendezése és testreszabása
* $logs blob-tárolók és $metrics táblák megtekintése az engedélyezett metrikákkal rendelkező Storage-fiókok számára
* Továbbfejlesztett exportálási és importálási viselkedés, mostantól tulajdonság-értéktípus is szerepel

#### <a name="fixes"></a>Javítások

* Kijavítva: a nagyméretű Blobok feltöltése vagy letöltése hiányos feltöltéseket vagy letöltéseket eredményezhet
* Kijavítva: numerikus karakterlánc-értékkel ("1") rendelkező entitások szerkesztése, hozzáadása vagy importálása dupla értékre
* Kijavítva: nem lehet kibontani a tábla csomópontot a helyi fejlesztési környezetben.

#### <a name="known-issues"></a>Ismert problémák

* Blob Storage-fiókok $metrics táblái nem láthatók
* A programozott módon hozzáadott üzenetsor-üzenetek nem jelennek meg megfelelően, ha az üzenetek Base64 kódolással vannak kódolva

05/17/2016
### <a name="version-07201605090"></a>0.7.20160509.0 verziója

#### <a name="new"></a>Új

* Jobb hibakezelés az alkalmazások összeomlásakor

#### <a name="fixes"></a>Javítások

* Kijavítva a hiba, ahol az információs sáv üzenetei időnként nem jelennek meg a bejelentkezési hitelesítő adatok megadásakor

#### <a name="known-issues"></a>Ismert problémák

* Táblázatok: olyan entitás hozzáadása, szerkesztése vagy importálása, amely nem egyértelmű numerikus értékkel rendelkezik (például "1" vagy "1,0"), és a felhasználó megpróbálja elküldeni azt `Edm.String` , az érték visszakerül az ügyfél API-EDM. Double

03/31/2016

### <a name="version-07201603250"></a>0.7.20160325.0 verziója

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Új

* Tábla támogatása: entitások megtekintése, lekérdezés, exportálás, Importálás és szifilisz-műveletek
* Üzenetsor-támogatás: üzenetek megtekintése, hozzáadása, üzenetsor-várólistára helyezése
* SAS URI-k létrehozása a Storage-fiókokhoz
* Csatlakozás a Storage-fiókokhoz SAS URI-k használatával
* A Storage Explorer jövőbeli frissítéseire vonatkozó értesítések frissítése
* Frissített megjelenés és működés

#### <a name="fixes"></a>Javítások

* Teljesítmény-és megbízhatósági változások

### <a name="known-issues-amp-mitigations"></a>Ismert problémák &amp; enyhítése

* A nagyméretű blob-fájlok letöltése nem működik megfelelően – a probléma megoldásához a AzCopy használatát javasoljuk.
* A fiók hitelesítő adatait nem lehet beolvasni és gyorsítótárazni, ha a kezdőmappa nem található vagy nem írható
* Ha olyan entitást ad hozzá, szerkeszt vagy importál, amelynek tulajdonsága nem egyértelmű numerikus értékkel rendelkezik (például "1" vagy "1,0"), és a felhasználó megpróbálja elküldeni azt `Edm.String` , akkor az érték az ügyfél API-EDM fog visszatérni. Double
* Többsoros rekordokkal rendelkező CSV-fájlok importálásakor előfordulhat, hogy az adatok fel vannak vágva vagy kódoltak

02/03/2016

### <a name="version-07201601291"></a>0.7.20160129.1 verziója

#### <a name="fixes"></a>Javítások

* Továbbfejlesztett általános teljesítmény a Blobok feltöltésekor, letöltésekor és másolásakor

01/14/2016

### <a name="version-07201601050"></a>0.7.20160105.0 verziója

#### <a name="new"></a>Új

* Linux-támogatás (paritásos funkciók az OSX-hez)
* BLOB-tárolók hozzáadása közös hozzáférésű aláírás (SAS) kulccsal
* Storage-fiókok hozzáadása az Azure China 21Vianet
* Egyéni végpontokkal rendelkező Storage-fiókok hozzáadása
* A tartalom szöveg-és képblobok megnyitása és megtekintése
* BLOB tulajdonságainak és metaadatainak megtekintése és szerkesztése

#### <a name="fixes"></a>Javítások

* Kijavítva: nagy mennyiségű blob feltöltése vagy letöltése (500 +) időnként előfordulhat, hogy az alkalmazás fehér képernyőt tartalmaz
* Kijavítva: a blob-tároló nyilvános hozzáférési szintjének beállításakor az új érték addig nem frissül, amíg újra be nem állítja a fókuszt a tárolóra. Emellett a párbeszédpanel mindig alapértelmezés szerint "nincs nyilvános hozzáférés", és nem a tényleges aktuális érték.
* Jobb általános billentyűzet/kisegítő lehetőségek és felhasználói felületi támogatás
* A Zsemlemorzsa-előzmények szövege, ha hosszú, szóközzel van ellátva
* SAS-párbeszédpanel támogatja a bemeneti ellenőrzést
* A helyi tárterület továbbra is elérhető marad, még akkor is, ha a felhasználói hitelesítő adatok lejártak
* A megnyitott blob-tároló törlése után a jobb oldalon található blob Explorer bezárul

#### <a name="known-issues"></a>Ismert problémák

* A Linux telepítéséhez a GCC frissített vagy frissített verziója szükséges – a frissítés lépései a következők:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>0.7.20151116.0 verziója

#### <a name="new"></a>Új

* macOS és Windows verziók
* Bejelentkezés a Storage-fiókok megtekintéséhez – szervezeti fiókja, Microsoft-fiókja, 2FA stb. használatával.
* Helyi fejlesztési tároló (a Storage Emulator használata, csak Windows)
* Azure Resource Manager és klasszikus erőforrás-támogatás
* Blobok, várólisták vagy táblák létrehozása és törlése
* Adott Blobok, várólisták vagy táblák keresése
* A blob-tárolók tartalmának megismerése
* Könyvtárak megtekintése és navigálása
* Blobok és mappák feltöltése, letöltése és törlése
* BLOB tulajdonságainak és metaadatainak megtekintése és szerkesztése
* SAS-kulcsok generálása
* Tárolt hozzáférési szabályzatok kezelése és létrehozása (SAP)
* Blobok keresése előtag alapján
* Húzza a fájlokat a feltöltéshez vagy a letöltéshez.

#### <a name="known-issues"></a>Ismert problémák

* A blob-tároló nyilvános hozzáférési szintjének beállításakor az új érték addig nem frissül, amíg újra be nem állítja a fókuszt a tárolóra.
* Amikor megnyitja a párbeszédpanelt a nyilvános hozzáférési szint megadásához, mindig a "nincs nyilvános hozzáférés" érték jelenik meg alapértelmezettként, nem pedig a tényleges aktuális érték.
* A letöltött Blobok nem nevezhetők át
* A műveletnapló bejegyzései időnként "ragadt" állapotba kerülnek, ha hiba történik, és a hiba nem jelenik meg
* A nagy mennyiségű blob feltöltésekor vagy letöltésekor időnként összeomlik vagy teljesen fehérre vált.
* A másolási művelet megszakítása néha nem működik
* Tároló létrehozásakor (blob/üzenetsor/tábla) Ha érvénytelen nevet adott meg, és egy másik típusú tárolóban folytatja a létrehozást, nem állítható be fókusz az új típusra.
* Nem lehet létrehozni új mappát vagy átnevezni a mappát




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
