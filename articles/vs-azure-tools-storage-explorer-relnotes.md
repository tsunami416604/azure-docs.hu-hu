---
title: A Microsoft Azure Storage Explorer kiadási megjegyzések
description: Kibocsátási megjegyzések a Microsoft Azure Storage Explorer programhoz
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
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351058"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>A Microsoft Azure Storage Explorer kiadási megjegyzések

Ez a cikk az Azure Storage Explorer legújabb kiadási megjegyzéseket, valamint a korábbi verziók kiadási megjegyzéseket tartalmazza. 

[A Microsoft Azure Storage Explorer](./vs-azure-tools-storage-manage-with-storage-explorer.md) egy önálló alkalmazás, amely lehetővé teszi, hogy könnyedén dolgozzon az Azure Storage-adatokkal Windows, macOS és Linux rendszeren.

A Storage Explorer korábbi verzióinak letöltéséhez látogasson el a GitHub-tárház [Releases oldalára.](https://github.com/microsoft/AzureStorageExplorer/releases)

## <a name="version-1110"></a>1.11.0-s verzió
11/4/2019

### <a name="new"></a>Új
* A blobok, az ADLS Gen2 és a felügyelt lemezek műveletei az integrált AzCopy-t használják. Pontosabban a következő műveletek et az AzCopy használatával végezzük:
   * Blobok
      * Megnyitás szerkesztésre + Feltöltés
      * Feltöltés, beleértve & húzást
      * Letöltés
      * & beillesztési #1249 másolása
      * Törlés
   * ADLS Gen2 blobok
      * Feltöltés, beleértve & húzást
      * Letöltés
      * Beillesztés & beillesztése
      * Törlés, beleértve a mappa törlését is
   * Felügyelt lemezek
      * Feltöltés
      * Letöltés
      * Beillesztés & beillesztése

   Emellett számos gyakran kért funkcióval bővült az integrált AzCopy-élmény:
   * Ütközésfeloldások – az átvitelek során az ütközések feloldására kéri a rendszer. #1455
   * Feltöltés lapblobként – kiválaszthatja, hogy az AzCopy feltölti-e a .vhd és .vhdx fájlokat lapblobként. #1164 és #1601
   * Konfigurálható AzCopy paraméterek – Az AzCopy teljesítményének és erőforrás-felhasználásának beállításához számos beállítás került hozzáadásra. További részleteket alább talál.

* Az ADLS Gen2 és a Blobs többprotokollos hozzáférés engedélyezéséhez és az ADLS Gen2 felhasználói élményének további javításához az ADLS Gen2-fiókok következő funkcióit adtuk hozzá:
   * Keresés rövid nevek használatával az ACL-engedélyek beállításához
   * Rejtett tárolók, például $logs és $web megtekintése
   * Konténerbérlet beszerzése és megszakítása
   * Blob-címbérlet #848 beszerzése és megszakítása
   * Tárolóhozzáférési házirendek kezelése
   * Blob-hozzáférési szintek konfigurálása
   * Blobok másolása & beillesztése

* Ebben a kiadásban további 17 nyelvet tekintünk meg. Az "Alkalmazás" → "Területi beállítások" → "Nyelv (előnézet)" alatt a beállítások oldalon átválthat egy általad választott nyelvre. Még mindig keményen dolgozunk a további húrok fordításán és a fordítás minőségének javításán. Ha bármilyen visszajelzést a fordítás, vagy ha azt veszi észre, egy string, amely még nem fordították le, kérjük, [nyisson meg egy kérdést a GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%F0%9F%8C%90%20localization&template=bug-report.md&title=).
* Minden kiadásban megpróbálunk néhány beállítást berakni, hogy lehetővé tegyék a Storage Explorer finom elfordítását. Ebben a kiadásban az AzCopy további konfigurálásához, valamint a szolgáltatáscsomópontok elrejtéséhez beállításokat adtunk hozzá:
   * AzCopy sávszélesség-korlát - segít szabályozni, hogy az AzCopy mekkora hálózatot használ. Ezt a beállítást a "Transfers" → "AzCopy" → "Maximális átviteli sebesség" oldalon találja. #1099
   * AzCopy MD5 check - lehetővé teszi annak konfigurálását, hogy az AzCopy ellenőrzi-e az MD5-ös kimérőket a letöltéskor. Ezt a beállítást a "Transfers" → "AzCopy" → "Check MD5" (Átvitelek) oldalon találja.
   * AzCopy egyidejűség és memóriapuffer mérete - alapértelmezés szerint az AzCopy elemzi a gépet, hogy ésszerű alapértelmezett értékeket határozson meg ezekhez a beállításokhoz. Ha azonban teljesítményproblémákba ütközik, ezek a speciális beállítások segítségével tovább szabhatja az AzCopy futtatását a számítógépen. Ezeket a beállításokat a "Transzferek" → "AzCopy" menüalatt találja. #994
   * A szolgáltatáscsomópontok megjelenítése és elrejtése – ezek a beállítások lehetőséget biztosítanak a Storage Explorer által támogatott Azure-szolgáltatások megjelenítésére vagy elrejtésére. Ezeket a beállításokat a "Szolgáltatások" részben találja. #1877

* Felügyelt lemez pillanatképének létrehozásakor most egy alapértelmezett név is meglesz adva. #1847
* Az Azure AD-hez való csatoláskor, ha egy ADLS Gen2 Blob-tárolót csatol, majd a "(ADLS Gen2)" a csomópont mellett jelenik meg. #1861

### <a name="fixes"></a>Javítások
* Nagy méretű lemezek másolása, feltöltése vagy letöltése közben a Storage Explorer néha nem vonja vissza a műveletben részt vevő lemezekhez való hozzáférést. Ezt a problémát megoldottuk. #2048
* A táblastatisztika nem sikerült partíciókulcs-lekérdezés megtekintésekor. Ezt a problémát megoldottuk. #1886

### <a name="known-issues"></a>Ismert problémák
* A Storage Explorer 1.11.0-s telepítéséhez most egy DFS-végpontra (például "myaccount.dfs.core.windows.net") van szükség az ADLS Gen2 tárolókhoz való csatoláshoz. A Storage Explorer korábbi verziói lehetővé tették a blobvégpont használatát. Előfordulhat, hogy ezek a mellékletek az 1.11.0-s verzióra való frissítés után már nem működnek. Ha ez a probléma merül fel, csatlakoztassa újra a DFS-végpontot.
* A numerikus beállítások nem ellenőrzik, hogy érvényes tartományban vannak-e.#2140
* Blob-tárolók másolása egyik tárfiókból a másikba a fa nézetben sikertelen lehet. A problémát vizsgáljuk.#2124
* Az Automatikus frissítés beállítás még nem befolyásolja a Blob Explorer összes műveletét.
* A felügyelt lemez funkciói nem támogatottak az Azure Stackben.
* Ha a lemez feltöltése vagy beillesztése sikertelen, és a hiba előtt új lemez jött létre, a Storage Explorer nem törli a lemezt.
* Attól függően, hogy mikor szakítja meg a lemezfeltöltést vagy a beillesztést, az új lemez tromba állapotban hagyhatja. Ebben az esetben törölnie kell az új lemezt, vagy manuálisan meg kell hívnia a lemez API-kat, hogy lecserélje a lemez tartalmát, hogy az már ne legyen sérült.
* Az RBAC használatakor a Storage Explorer bizonyos felügyeleti réteg-engedélyeket igényel a tárolási erőforrások eléréséhez. További információt a [hibaelhárítási útmutatóban](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) talál.
* A SAS URI-n keresztül csatlakoztatott erőforrásról, például egy blobtárolóról való leválasztás olyan hibát okozhat, amely megakadályozza, hogy más mellékletek megfelelően jelenjenek meg. A probléma kerülő megoldásához frissítse a csoportcsomópontot. További információt a #537 című témakörben talál.
* Ha a VS for Mac-et használja, és valaha is létrehozott egy egyéni AAD-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma kerülő megoldásához törölje a ~/. IdentityService/AadConfigurations. Ha ez nem oldja fel a letiltást, véleményezze a problémát.
* Azurite még nem valósította meg teljes mértékben az összes tárolási API-t. Emiatt előfordulhat, hogy váratlan hibák vagy viselkedés használata kor Azurite fejlesztési tároláshoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza elakad a gyorselérésen. A fókusz leoldásához frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba van rögzített, de még nem egységbe rendezett -ba Elektron. A probléma kerülő megoldásához blobtárolóba való feltöltéskor vagy onnan való letöltéskor használhatja a kísérleti AzCopy funkciót.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ennek az az oka, hogy az itt leírt törlési szűrőt használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Az Azure Stack nem támogatja a következő funkciókat. Ha az Azure Stack-erőforrásokkal való munka során megpróbálja használni ezeket a funkciókat, az váratlan hibákat okozhat.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
   * ADLS Gen2
   * Felügyelt lemezek
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Storage Explorer Linuxon való futtatásához először bizonyos függőségeket kell telepíteni. További információt a Tárolókezelő [hibaelhárítási útmutatójában](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) talál.

## <a name="previous-releases"></a>Korábbi kiadások

* [1.10.1-es verzió](#version-1101)
* [1.10.0-s verzió](#version-1100)
* [1.9.0-s verzió](#version-190)
* [1.8.1-es verzió](#version-181)
* [1.8.0-s verzió](#version-180)
* [1.7.0-ás verzió](#version-170)
* [1.6.2-es verzió](#version-162)
* [1.6.1-es verzió](#version-161)
* [1.6.0-s verzió](#version-160)
* [1.5.0-s verzió](#version-150)
* [1.4.4-es verzió](#version-144)
* [1.4.3-as verzió](#version-143)
* [1.4.2-es verzió](#version-142)
* [1.4.1-es verzió](#version-141)
* [1.3.0-s verzió](#version-130)
* [1.2.0-s verzió](#version-120)
* [1.1.0-s verzió](#version-110)
* [1.0.0-s verzió](#version-100)
* [0.9.6-os verzió](#version-096)
* [0.9.5-ös verzió](#version-095)
* [0.9.4-es és 0.9.3-as verzió](#version-094-and-093)
* [0.9.2-es verzió](#version-092)
* [0.9.1-es és 0.9.0-s verzió](#version-091-and-090)
* [0.8.16-os verzió](#version-0816)
* [0.8.14-es verzió](#version-0814)
* [0.8.13-as verzió](#version-0813)
* [0.8.12-es és 0.8.11-es és 0.8.10-es verzió](#version-0812-and-0811-and-0810)
* [0.8.9-es és 0.8.8-as verzió](#version-089-and-088)
* [0.8.7-es verzió](#version-087)
* [0.8.6-os verzió](#version-086)
* [0.8.5-ös verzió](#version-085)
* [0.8.4-es verzió](#version-084)
* [0.8.3-as verzió](#version-083)
* [0.8.2-es verzió](#version-082)
* [0.8.0-s verzió](#version-080)
* [0.7.20160509.0-s verzió](#version-07201605090)
* [0.7.20160325.0-s verzió](#version-07201603250)
* [0.7.20160129.1 verzió](#version-07201601291)
* [0.7.20160105.0-s verzió](#version-07201601050)
* [20151116.0-s verzió](#version-07201511160)

## <a name="version-1101"></a>1.10.1-es verzió
9/19/2019

### <a name="hotfix"></a>Gyorsjavítás
* Egyes felhasználók hibát észleltek az 1.10.0-s ablakban, miközben megpróbálták megtekinteni az adataikat az ADLS Gen 1-fiókjukban. Ez a hiba megakadályozta, hogy a felfedező panel megfelelően rendereljön. Ezt a problémát megoldottuk. #1853 #1865

### <a name="new"></a>Új
* A Storage Explorer most már rendelkezik egy dedikált Beállítások felhasználói felületével. Elérheti akár szerkesztése → Beállítások, vagy kattintson a Beállítások ikonra (a fogaskerék) a bal oldali függőleges eszköztáron. Ez a funkció az első lépés, amit a [felhasználók által kért beállítások](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate)biztosítása felé teszünk. Ebben a kiadásban a következő beállítások támogatottak:
  * Téma
  * Proxy
  * Kijelentkezés a kilépési #6
  * Eszközkód-beírás engedélyezése
  * Automatikus frissítés #1526
  * AzCopy engedélyezése
  * AzCopy SAS időtartama Ha vannak más beállítások, amelyeket szeretne hozzáadni, nyisson meg egy problémát a [GitHubon,](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=) amely leírja a kívánt beállítást.
* A Storage Explorer mostantól támogatja a felügyelt lemezeket. A következőket teheti:
  * Helyszíni virtuális merevlemez feltöltése új lemezre
  * Lemez letöltése
  * Lemezek másolása és beillesztése erőforráscsoportok és -régiók között
  * Lemezek törlése
  * Lemez pillanatképének létrehozása

A lemezek feltöltése, letöltése és régióközi másolása az AzCopy v10-en történik.
* A Storage Explorer most már telepíthető a Snap áruházon keresztül Linuxon. Amikor a Snap áruházon keresztül telepíti az összes függőséget, a .NET Core! Jelenleg ellenőriztük, hogy a Storage Explorer jól fut az Ubuntu és a CentOS rendszeren. Ha problémákat tapasztal a Snap áruházból más Linux disztribúciókon történő telepítésével kapcsolatban, kérjük, [nyisson meg egy problémát a GitHubon.](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=) Ha többet szeretne megtudni a Snap áruházból történő telepítésről, olvassa el [az első lépések útmutatót.](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux) #68
* Az Azure Active Directory (Azure AD) két fő módosítása történt, amelyek célja, hogy a szolgáltatás még hasznosabbá tegye az ADLS Gen2-felhasználók számára:
  * Most válassza ki a bérlőt, amelyben a hozzácsatolja az erőforrást. Ez azt jelenti, hogy már nem kell RBAC-hozzáféréssel rendelkeznie az erőforrás előfizetéséhez.
  * Ha egy ADLS Gen2 blob tárolót csatol, most már csatolhat egy adott elérési utat a tárolóban.
* Az ADLS Gen2-fájlok és -mappák ACL-ek kezelése során a Storage Explorer ekkor az ACL-ben szereplő entitások rövid neveit jeleníti meg. #957
* Oid-on keresztül egy ADLS Gen2 ACL hozzáadásakor a Storage Explorer most ellenőrzi, hogy az OID a bérlő egy érvényes entitáshoz tartozik-e. #1603
* A lapok közötti navigáláshoz használható billentyűparancsok mostantól szabványosabb billentyűkombinációkat használnak. #1018
* Középső kattintva egy lapon most zárja be. #1348
* Ha az AzCopy-átvitel ugrásokat tartalmaz, és nincsenek hibák, a Storage Explorer ekkor megjelenik egy figyelmeztető ikon, amely kiemeli az átugrásokat. #1490
* Az integrált AzCopy frissítve lett a 10.2.1-es verzióra. Ezenkívül most már megtekintheti az AzCopy telepített verzióját a Betekintés párbeszédpanelen. #1343

### <a name="fixes"></a>Javítások
* Sok felhasználó különböző "nem tudja olvasni a nem definiált verzióját" vagy "nem tudja olvasni a nem definiált kapcsolat" hibákat, amikor csatlakoztatott tárfiókokkal dolgozik. Bár továbbra is vizsgálja a probléma kiváltó okát, az 1.10.0-ban javítottuk a csatlakoztatott tárfiókok betöltésével kapcsolatos hibakezelést. #1626, #985 és #1532
* Lehetséges volt, hogy a felfedező fa (bal oldali) olyan állapotba kerüljön, ahol a fókusz többször a legfelső csomópontra ugrik. Ezt a problémát megoldottuk. #1596
* Egy blob pillanatképeikezelése során a képernyőolvasók nem olvassa el a pillanatképhez társított időbélyeget. Ezt a problémát megoldottuk. #1202
* A macOS proxybeállítása nem volt beállítva időben ahhoz, hogy a hitelesítési folyamat használni szeretné őket. Ezt a problémát megoldottuk. #1567
* Ha egy szuverén felhőben lévő tárfiók név vel és kulccsal lett csatolva, az AzCopy nem működik. Ezt a problémát megoldottuk. #1544
* Kapcsolati karakterláncon keresztül történő csatoláskor a Storage Explorer eltávolítja a záró helyeket. #1387

### <a name="known-issues"></a>Ismert problémák
* Az Automatikus frissítés beállítás még nem befolyásolja a Blob Explorer összes műveletét.
* A felügyelt lemez funkciói nem támogatottak az Azure Stackben.
* Ha a lemez feltöltése vagy beillesztése sikertelen, és a hiba előtt új lemez jött létre, a Storage Explorer nem törli a lemezt.
* Attól függően, hogy mikor szakítja meg a lemezfeltöltést vagy a beillesztést, az új lemez tromba állapotban hagyhatja. Ebben az esetben törölnie kell az új lemezt, vagy manuálisan meg kell hívnia a lemez API-kat, hogy lecserélje a lemez tartalmát, hogy az már ne legyen sérült.
* Attól függően, hogy mikor szakítja meg a lemezfeltöltést vagy a beillesztést, az új lemez tromba állapotban hagyhatja. Ebben az esetben törölnie kell az új lemezt, vagy manuálisan meg kell hívnia a lemez API-kat, hogy lecserélje a lemez tartalmát, hogy az már ne legyen sérült.
* Nem AzCopy Blob letöltésvégrehajtásakor a nagyfájlok md5-je nem lesz ellenőrizve. Ez a storage SDK-ban található hiba miatt van. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Az RBAC használatakor a Storage Explorer bizonyos felügyeleti réteg-engedélyeket igényel a tárolási erőforrások eléréséhez. További információt a [hibaelhárítási útmutatóban](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) talál.
* A SAS URI-n keresztül csatlakoztatott erőforrásról, például egy blobtárolóról való leválasztás olyan hibát okozhat, amely megakadályozza, hogy más mellékletek megfelelően jelenjenek meg. A probléma kerülő megoldásához frissítse a csoportcsomópontot. További információt a #537 című témakörben talál.
* Ha a VS for Mac-et használja, és valaha is létrehozott egy egyéni AAD-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma kerülő megoldásához törölje a ~/. IdentityService/AadConfigurations. Ha ez nem oldja fel a letiltást, véleményezze a problémát.
* Azurite még nem valósította meg teljes mértékben az összes tárolási API-t. Emiatt előfordulhat, hogy váratlan hibák vagy viselkedés használata kor Azurite fejlesztési tároláshoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza elakad a gyorselérésen. A fókusz leoldásához frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba van rögzített, de még nem egységbe rendezett -ba Elektron. A probléma kerülő megoldásához blobtárolóba való feltöltéskor vagy onnan való letöltéskor használhatja a kísérleti AzCopy funkciót.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ennek az az oka, hogy az itt leírt törlési szűrőt használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Az Azure Stack nem támogatja a következő funkciókat. Ha az Azure Stack-erőforrásokkal való munka során megpróbálja használni ezeket a funkciókat, az váratlan hibákat okozhat.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
   * ADLS Gen2
   * Felügyelt lemezek
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Storage Explorer Linuxon való futtatásához először bizonyos függőségeket kell telepíteni. További információt a Tárolókezelő [hibaelhárítási útmutatójában](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) talál.


## <a name="version-1100"></a>1.10.0-s verzió
9/12/2019

### <a name="new"></a>Új

* A Storage Explorer most már rendelkezik egy dedikált Beállítások felhasználói felületével. Elérheti akár szerkesztése → Beállítások, vagy kattintson a Beállítások ikonra (a fogaskerék) a bal oldali függőleges eszköztáron. Ez a funkció az első lépés, amit a [felhasználók által kért beállítások](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate)biztosítása felé teszünk. Ebben a kiadásban a következő beállítások támogatottak:
    * Téma
    * Proxy
    * Kijelentkezés a [kilépési #6](https://www.github.com/Microsoft/AzureStorageExplorer/issues/6)
    * Eszközkód-beírás engedélyezése
    * Automatikus frissítés [#1526](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1526)
    * AzCopy engedélyezése
    * AzCopy SAS időtartama

    Ha más beállításokat is szeretne hozzáadni, [nyisson meg egy problémát a GitHubon, amely leírja a kívánt beállítást.](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=)
* A Storage Explorer mostantól támogatja a felügyelt lemezeket. A következőket teheti:
    * Helyszíni virtuális merevlemez feltöltése új lemezre
    * Lemez letöltése
    * Lemezek másolása és beillesztése erőforráscsoportok és -régiók között
    * Lemezek törlése
    * Lemez pillanatképének létrehozása

    A lemezek feltöltése, letöltése és régióközi másolása az AzCopy v10-en történik.
* A Storage Explorer most már telepíthető a Snap áruházon keresztül Linuxon. Amikor a Snap áruházon keresztül telepíti az összes függőséget, a .NET Core! Jelenleg ellenőriztük, hogy a Storage Explorer jól fut az Ubuntu és a CentOS rendszeren. Ha problémákat tapasztal a Snap áruházból más Linux disztribúciókon történő telepítésével kapcsolatban, kérjük, [nyisson meg egy problémát a GitHubon.](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=) Ha többet szeretne megtudni a Snap áruházból történő telepítésről, olvassa el [az első lépések útmutatót.](https://aka.ms/storageexplorer/snapinformation) [#68](https://www.github.com/Microsoft/AzureStorageExplorer/issues/68)
* Két fő változtatástörtént az Azure Active Directory (Azure AD) csatlakoztatása érdekében, amelyek célja, hogy a szolgáltatás hasznosabbá tegye az ADLS Gen2 felhasználók számára: * Most válassza ki azt a bérlőt, amelyben a csatolja az erőforrást. Ez azt jelenti, hogy már nem kell RBAC-hozzáféréssel rendelkeznie az erőforrás előfizetéséhez.
        * Ha egy ADLS Gen2 Blob Container csatlakoztatása, most már csatolhatja egy adott elérési út a tárolóban.
* Az ADLS Gen2-fájlok és -mappák ACL-ek kezelése során a Storage Explorer ekkor az ACL-ben szereplő entitások rövid neveit jeleníti meg. [#957](https://www.github.com/Microsoft/AzureStorageExplorer/issues/957)
* Oid-on keresztül egy ADLS Gen2 ACL hozzáadásakor a Storage Explorer most ellenőrzi, hogy az OID a bérlő egy érvényes entitáshoz tartozik-e. [#1603](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1603)
* A lapok közötti navigáláshoz használható billentyűparancsok mostantól szabványosabb billentyűkombinációkat használnak. [#1018](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1018)
* Középső kattintva egy lapon most zárja be. [#1348](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1348)
* Ha az AzCopy-átvitel ugrásokat tartalmaz, és nincsenek hibák, a Storage Explorer ekkor megjelenik egy figyelmeztető ikon, amely kiemeli az átugrásokat. [#1490](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1490)
* Az integrált AzCopy frissítve lett a 10.2.1-es verzióra. Ezenkívül most már megtekintheti az AzCopy telepített verzióját a Betekintés párbeszédpanelen. [#1343](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1343)

### <a name="fixes"></a>Javítások

* Sok felhasználó különböző "nem tudja olvasni a nem definiált verzióját" vagy "nem tudja olvasni a nem definiált kapcsolat" hibákat, amikor csatlakoztatott tárfiókokkal dolgozik. Bár továbbra is vizsgálja a probléma kiváltó okát, az 1.10.0-ban javítottuk a csatlakoztatott tárfiókok betöltésével kapcsolatos hibakezelést. [#1626,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1626) [#985](https://www.github.com/Microsoft/AzureStorageExplorer/issues/985)és [#1532](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1532)
* Lehetséges volt, hogy a felfedező fa (bal oldali) olyan állapotba kerüljön, ahol a fókusz többször a legfelső csomópontra ugrik. Ezt a problémát megoldottuk. [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* Egy blob pillanatképeikezelése során a képernyőolvasók nem olvassa el a pillanatképhez társított időbélyeget. Ezt a problémát megoldottuk. [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* A macOS proxybeállítása nem volt beállítva időben ahhoz, hogy a hitelesítési folyamat használni szeretné őket. Ezt a problémát megoldottuk. [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* Ha egy szuverén felhőben lévő tárfiók név vel és kulccsal lett csatolva, az AzCopy nem működik. Ezt a problémát megoldottuk. [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* Kapcsolati karakterláncon keresztül történő csatoláskor a Storage Explorer eltávolítja a záró helyeket. [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>Ismert problémák

* Az Automatikus frissítés beállítás még nem befolyásolja a Blob Explorer összes műveletét.
* A felügyelt lemez funkciói nem támogatottak az Azure Stackben.
* Ha a lemez feltöltése vagy beillesztése sikertelen, és a hiba előtt új lemez jött létre, a Storage Explorer nem törli a lemezt.
* Attól függően, hogy mikor szakítja meg a lemezfeltöltést vagy a beillesztést, az új lemez tromba állapotban hagyhatja. Ebben az esetben törölnie kell az új lemezt, vagy manuálisan meg kell hívnia a lemez API-kat, hogy lecserélje a lemez tartalmát, hogy az már ne legyen sérült.
* Nem AzCopy Blob letöltésvégrehajtásakor a nagyfájlok md5-je nem lesz ellenőrizve. Ez a storage SDK-ban található hiba miatt van. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Az RBAC használatakor a Storage Explorer bizonyos felügyeleti réteg-engedélyeket igényel a tárolási erőforrások eléréséhez. További információt a [hibaelhárítási útmutatóban](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) talál.
* A SAS URI-n keresztül csatlakoztatott erőforrásról, például egy blobtárolóról való leválasztás olyan hibát okozhat, amely megakadályozza, hogy más mellékletek megfelelően jelenjenek meg. A probléma kerülő megoldásához frissítse a csoportcsomópontot. További információt a #537 című témakörben talál.
* Ha a VS for Mac-et használja, és valaha is létrehozott egy egyéni AAD-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma kerülő megoldásához törölje a ~/. IdentityService/AadConfigurations. Ha ez nem oldja fel a letiltást, véleményezze a problémát.
* Azurite még nem valósította meg teljes mértékben az összes tárolási API-t. Emiatt előfordulhat, hogy váratlan hibák vagy viselkedés használata kor Azurite fejlesztési tároláshoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza elakad a gyorselérésen. A fókusz leoldásához frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba van rögzített, de még nem egységbe rendezett -ba Elektron. A probléma kerülő megoldásához blobtárolóba való feltöltéskor vagy onnan való letöltéskor használhatja a kísérleti AzCopy funkciót.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ennek az az oka, hogy az itt leírt törlési szűrőt használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Az Azure Stack nem támogatja a következő funkciókat. Ha az Azure Stack-erőforrásokkal való munka során megpróbálja használni ezeket a funkciókat, az váratlan hibákat okozhat.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
   * ADLS Gen2
   * Felügyelt lemezek
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Storage Explorer Linuxon való futtatásához először bizonyos függőségeket kell telepíteni. További információt a Tárolókezelő [hibaelhárítási útmutatójában](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) talál.

## <a name="version-190"></a>1.9.0-s verzió
7/1/2019

### <a name="download-azure-storage-explorer-190"></a>Az Azure Storage Explorer 1.9.0 letöltése
- [Azure Storage Explorer 1.9.0 Windows rendszerhez](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer 1.9.0 Mac hez](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.9.0 Linuxhoz](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Új

* Most már csatolhatja blob tárolók keresztül Azure AD (RBAC vagy ACL engedélyeket). Ez a funkció célja, hogy segítse a felhasználókat, akik hozzáférhetnek a tárolók, de nem a storage-fiókok, amelyek a tárolók vannak. Az első lépésekről szóló útmutatóban további információt talál erről a funkcióról.
* A bérlet beszerzése és megszakítása mostantól az RBAC-mal működik. [#1354](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1354)
* A hozzáférési házirendek kezelése és a nyilvános hozzáférési szint beállítása mostantól működik az RBAC-kal. [#1355](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1355)
* A blobmappák törlése mostantól az RBAC-kal működik. [#1450](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1450)
* A blob hozzáférési szintjének módosítása mostantól az RBAC-kal működik. [#1446](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1446)
* Most már gyorsan visszaállíthatja a gyors hozzáférést a "Súgó" → "Reset" segítségével. [#1327](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1327)

### <a name="preview-features"></a>Előzetes verziójú funkciók

* Az eszközkód-bejelentkezés mostmár előzetes verzióban is megtekinthető. Az engedélyezéshez válassza az "Előzetes verzió" → "Device Code Flow bejelentkezés használata" lehetőséget. Azt javasoljuk, hogy minden olyan felhasználó, akinek problémái voltak az üres bejelentkezési ablakokkal, próbálja ki ezt a funkciót, mivel megbízhatóbb bejelentkezési formának bizonyulhat.
* Az AzCopy-szal integrált Storage Explorer jelenleg előzetes verzióban érhető el. Az engedélyezéshez lépjen az "Előzetes verzió" → "Az AzCopy használata a továbbfejlesztett blob feltöltéshez és letöltéshez". Blob-átvitelek az AzCopy-nal befejeződött gyorsabbnak és hatékonyabbnak kell lennie.

### <a name="fixes"></a>Javítások

* Javítva, hogy nem tudott 50-nél több előfizetést betölteni egy fiókhoz. [#1416](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1416)
* Javítva, hogy a "Bejelentkezés" gomb nem működik az információs sávon, amely akkor jelenik meg, ha a közvetlen kapcsolat sikertelen. [#1358](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1358)
* Javítva, hogy nem feltölteni .app fájlokat macOS. [#1119](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1119)
* Javítottuk, hogy az "Újrapróbálkozás" nem működik egy sikertelen blobátnevezéshez. [#992](https://www.github.com/Microsoft/AzureStorageExplorer/issues/992)
* Javítottuk, hogy a "Mégse" nem működik egy blob megnyitásakor. [#1464](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1464)
* A termék ben több helyesírási és elemleírási problémát javítottunk. Sok köszönet mindazoknak, akik jelentették ezeket a kérdéseket! [#1303,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1303) [#1328 #1329,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1328) [#1329,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1329)#1336 [#1331,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1331) [#1352,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1336) [#1368](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1368) [#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1395) [#1352](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1352)

### <a name="known-issues"></a>Ismert problémák

* Nem AzCopy Blob letöltésvégrehajtásakor a nagyfájlok md5-je nem lesz ellenőrizve. Ez a storage SDK-ban található hiba miatt van. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Az RBAC használatakor a Storage Explorer bizonyos felügyeleti réteg-engedélyeket igényel a tárolási erőforrások eléréséhez. További információt a [hibaelhárítási útmutatóban](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) talál.
* Kísérlet az ADLS Gen2 blobok elérésére, ha egy proxy mögött sikertelen lehet.
* A SAS URI-n keresztül csatlakoztatott erőforrásról, például egy blobtárolóról való leválasztás olyan hibát okozhat, amely megakadályozza, hogy más mellékletek megfelelően jelenjenek meg. A probléma kerülő megoldásához frissítse a csoportcsomópontot. További információt a #537 című témakörben talál.
* Ha a VS for Mac-et használja, és valaha is létrehozott egy egyéni AAD-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma kerülő megoldásához törölje a ~/. IdentityService/AadConfigurations. Ha ez nem oldja fel a letiltást, véleményezze a problémát.
* Azurite még nem valósította meg teljes mértékben az összes tárolási API-t. Emiatt előfordulhat, hogy váratlan hibák vagy viselkedés használata kor Azurite fejlesztési tároláshoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza elakad a gyorselérésen. A fókusz leoldásához frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba van rögzített, de még nem egységbe rendezett -ba Elektron. A probléma kerülő megoldásához blobtárolóba való feltöltéskor vagy onnan való letöltéskor használhatja a kísérleti AzCopy funkciót.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ennek az az oka, hogy az itt leírt törlési szűrőt használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Az Azure Stack nem támogatja a következő funkciókat. Ha az Azure Stack-erőforrásokkal való munka során megpróbálja használni ezeket a funkciókat, az váratlan hibákat okozhat.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
   * ADLS Gen2
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Storage Explorer Linuxon való futtatásához először bizonyos függőségeket kell telepíteni. További információt a Tárolókezelő [hibaelhárítási útmutatójában](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) talál.

## <a name="version-181"></a>1.8.1-es verzió
5/13/2019

### <a name="hotfixes"></a>Gyorsjavítások
* Bizonyos esetekben a "Több betöltése" elemre kattintva az erőforrás szintjén nem adja vissza az erőforrások következő oldalát. Ezt a problémát megoldottuk. [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* Windows rendszerben az AzCopy-letöltések sikertelenek lennének, ha egyetlen fájlt vagy mappát töltenék le, és a fájl vagy mappa neve érvénytelen volt egy Windows elérési úthoz. Ezt a problémát megoldottuk. [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* Rendkívül ritka esetekben, amikor egy fájlmegosztás vagy egy fájlmegosztási átnevezés tanoncát hajtott végre, ha az átnevezés másolatai nem sikerültek, vagy ha a Storage Explore nem tudta megerősíteni a másolatok sikeressét az Azure-ral, a Storage Explorer törölheti a eredeti fájlokat, mielőtt a másolat befejeződött volna. Ezt a problémát megoldottuk.

### <a name="new"></a>Új

* Az integrált AzCopy verzió frissítve lett a 10.1.0-s verzióra.
* A Ctrl/Cmd+R segítségével frissítheti az aktuálisan fókuszált szerkesztőt. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Az Azure Stack Storage API-verziója 2017-04-17-re módosult.
* Az ADLS Gen2 Hozzáférés kezelése párbeszédpanele a maszk szinkronizálását a többi POSIX engedélyhez hasonló módon fogja szinkronban tartani. A felhasználói felület akkor is figyelmezteti, ha olyan módosítás történik, amely miatt egy felhasználó vagy csoport engedélyei túllépik a maszk határait. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Az AzCopy feltöltések esetén az MD5-kivonat kiszámításának és beállításának jelzője most antól engedélyezve van. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Előzetes verziójú funkciók

* Az eszközkód-bejelentkezés mostmár előzetes verzióban is megtekinthető. Az engedélyezéshez válassza az "Előzetes verzió" → "Device Code Flow bejelentkezés használata" lehetőséget. Azt javasoljuk, hogy minden olyan felhasználó, akinek problémái voltak az üres bejelentkezési ablakokkal, próbálja ki ezt a funkciót, mivel megbízhatóbb bejelentkezési formának bizonyulhat.
* Az AzCopy-szal integrált Storage Explorer jelenleg előzetes verzióban érhető el. Az engedélyezéshez lépjen az "Előzetes verzió" → "Az AzCopy használata a továbbfejlesztett blob feltöltéshez és letöltéshez". Blob-átvitelek az AzCopy-nal befejeződött gyorsabbnak és hatékonyabbnak kell lennie.

### <a name="fixes"></a>Javítások

* A Hozzáférési házirendek párbeszédpanel a továbbiakban nem állít be lejárati dátumot a nem lejárati szintű tároló-hozzáférési házirendeken. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Néhány módosítás történt a SAS létrehozása párbeszédpanelen, hogy a Tárolt hozzáférési házirendek megfelelően legyenek használva a SAS létrehozásakor. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Amikor nem 512 bájtos, egymáshoz igazított fájlt próbál feltölteni egy lapblobba, a Storage Explorer most egy relevánsabb hibát jelenít meg. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* A megjelenítendő nevet használó Blob-tároló másolása sikertelen lenne. Most a blob tároló tényleges nevét használja a rendszer. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Ha bizonyos műveleteket próbálna végrehajtani egy Olyan ADLS Gen2 mappán, amelynek nevében unicode karakterek voltak, az sikertelen lenne. Mostantól minden műveletnek működnie kell. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Ismert problémák

* Nem AzCopy Blob letöltésvégrehajtásakor a nagyfájlok md5-je nem lesz ellenőrizve. Ez a storage SDK-ban található hiba miatt van. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Az RBAC használatakor a Storage Explorer bizonyos felügyeleti réteg-engedélyeket igényel a tárolási erőforrások eléréséhez. További információt a [hibaelhárítási útmutatóban](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) talál.
* Kísérlet az ADLS Gen2 blobok elérésére, ha egy proxy mögött sikertelen lehet.
* A SAS URI-n keresztül csatlakoztatott erőforrásról, például egy blobtárolóról való leválasztás olyan hibát okozhat, amely megakadályozza, hogy más mellékletek megfelelően jelenjenek meg. A probléma kerülő megoldásához frissítse a csoportcsomópontot. További információt a #537 című témakörben talál.
* Ha a VS for Mac-et használja, és valaha is létrehozott egy egyéni AAD-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma kerülő megoldásához törölje a ~/. IdentityService/AadConfigurations. Ha ez nem oldja fel a letiltást, véleményezze a problémát.
* Azurite még nem valósította meg teljes mértékben az összes tárolási API-t. Emiatt előfordulhat, hogy váratlan hibák vagy viselkedés használata kor Azurite fejlesztési tároláshoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza elakad a gyorselérésen. A fókusz leoldásához frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba van rögzített, de még nem egységbe rendezett -ba Elektron. A probléma kerülő megoldásához blobtárolóba való feltöltéskor vagy onnan való letöltéskor használhatja a kísérleti AzCopy funkciót.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ennek az az oka, hogy az itt leírt törlési szűrőt használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Az Azure Stack nem támogatja a következő funkciókat. Ha az Azure Stack-erőforrásokkal való munka során megpróbálja használni ezeket a funkciókat, az váratlan hibákat okozhat.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
   * ADLS Gen2
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Storage Explorer Linuxon való futtatásához először bizonyos függőségeket kell telepíteni. További információt a Tárolókezelő [hibaelhárítási útmutatójában](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) talál.

## <a name="version-180"></a>1.8.0-s verzió
2019. 05. 01.

### <a name="new"></a>Új

* Az integrált AzCopy verzió frissítve lett a 10.1.0-s verzióra.
* A Ctrl/Cmd+R segítségével frissítheti az aktuálisan fókuszált szerkesztőt. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Az Azure Stack Storage API-verziója 2017-04-17-re módosult.
* Az ADLS Gen2 Hozzáférés kezelése párbeszédpanele a maszk szinkronizálását a többi POSIX engedélyhez hasonló módon fogja szinkronban tartani. A felhasználói felület akkor is figyelmezteti, ha olyan módosítás történik, amely miatt egy felhasználó vagy csoport engedélyei túllépik a maszk határait. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Az AzCopy feltöltések esetén az MD5-kivonat kiszámításának és beállításának jelzője most antól engedélyezve van. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Előzetes verziójú funkciók

* Az eszközkód-bejelentkezés mostmár előzetes verzióban is megtekinthető. Az engedélyezéshez válassza az "Előzetes verzió" → "Device Code Flow bejelentkezés használata" lehetőséget. Azt javasoljuk, hogy minden olyan felhasználó, akinek problémái voltak az üres bejelentkezési ablakokkal, próbálja ki ezt a funkciót, mivel megbízhatóbb bejelentkezési formának bizonyulhat.
* Az AzCopy-szal integrált Storage Explorer jelenleg előzetes verzióban érhető el. Az engedélyezéshez lépjen az "Előzetes verzió" → "Az AzCopy használata a továbbfejlesztett blob feltöltéshez és letöltéshez". Blob-átvitelek az AzCopy-nal befejeződött gyorsabbnak és hatékonyabbnak kell lennie.

### <a name="fixes"></a>Javítások

* A Hozzáférési házirendek párbeszédpanel a továbbiakban nem állít be lejárati dátumot a nem lejárati szintű tároló-hozzáférési házirendeken. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Néhány módosítás történt a SAS létrehozása párbeszédpanelen, hogy a Tárolt hozzáférési házirendek megfelelően legyenek használva a SAS létrehozásakor. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Amikor nem 512 bájtos, egymáshoz igazított fájlt próbál feltölteni egy lapblobba, a Storage Explorer most egy relevánsabb hibát jelenít meg. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* A megjelenítendő nevet használó Blob-tároló másolása sikertelen lenne. Most a blob tároló tényleges nevét használja a rendszer. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Ha bizonyos műveleteket próbálna végrehajtani egy Olyan ADLS Gen2 mappán, amelynek nevében unicode karakterek voltak, az sikertelen lenne. Mostantól minden műveletnek működnie kell. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Ismert problémák

* Nem AzCopy Blob letöltésvégrehajtásakor a nagyfájlok md5-je nem lesz ellenőrizve. Ez a storage SDK-ban található hiba miatt van. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Az RBAC használatakor a Storage Explorer bizonyos felügyeleti réteg-engedélyeket igényel a tárolási erőforrások eléréséhez. További információt a [hibaelhárítási útmutatóban](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) talál.
* Kísérlet az ADLS Gen2 blobok elérésére, ha egy proxy mögött sikertelen lehet.
* A SAS URI-n keresztül csatlakoztatott erőforrásról, például egy blobtárolóról való leválasztás olyan hibát okozhat, amely megakadályozza, hogy más mellékletek megfelelően jelenjenek meg. A probléma kerülő megoldásához frissítse a csoportcsomópontot. További információt a #537 című témakörben talál.
* Ha a VS for Mac-et használja, és valaha is létrehozott egy egyéni AAD-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma kerülő megoldásához törölje a ~/. IdentityService/AadConfigurations. Ha ez nem oldja fel a letiltást, véleményezze a problémát.
* Azurite még nem valósította meg teljes mértékben az összes tárolási API-t. Emiatt előfordulhat, hogy váratlan hibák vagy viselkedés használata kor Azurite fejlesztési tároláshoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza elakad a gyorselérésen. A fókusz leoldásához frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba van rögzített, de még nem egységbe rendezett -ba Elektron. A probléma kerülő megoldásához blobtárolóba való feltöltéskor vagy onnan való letöltéskor használhatja a kísérleti AzCopy funkciót.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ennek az az oka, hogy az itt leírt törlési szűrőt használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Az Azure Stack nem támogatja a következő funkciókat. Ha az Azure Stack-erőforrásokkal való munka során megpróbálja használni ezeket a funkciókat, az váratlan hibákat okozhat.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
   * ADLS Gen2
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Storage Explorer Linuxon való futtatásához először bizonyos függőségeket kell telepíteni. További információt a Tárolókezelő [hibaelhárítási útmutatójában](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) talál.

## <a name="version-170"></a>1.7.0-ás verzió
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>Az Azure Storage Explorer 1.7.0 letöltése
- [Azure Storage Explorer 1.7.0 Windows rendszerhez](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer 1.7.0 Mac hez](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.7.0 Linuxhoz](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Új

* Az ADLS Gen2 tároló, fájl vagy mappa hozzáférésének kezelésekor módosíthatja a tulajdonost és a tulajdonos csoportot.
* A Windows rendszerben a Storage Explorer frissítése a terméken belülről mostantól növekményes telepítés. Ez gyorsabb frissítési élményt eredményez. Ha inkább egy tiszta telepítés, akkor töltse le a [telepítőt](https://azure.microsoft.com/features/storage-explorer/) magad, majd telepítse manuálisan. #1089

### <a name="preview-features"></a>Előzetes verziójú funkciók

* Az eszközkód-bejelentkezés mostmár előzetes verzióban is megtekinthető. Az engedélyezéshez válassza az "Előzetes verzió" → "Device Code Flow bejelentkezés használata" lehetőséget. Azt javasoljuk, hogy minden olyan felhasználó, akinek problémái voltak az üres bejelentkezési ablakokkal, próbálja ki ezt a funkciót, mivel megbízhatóbb bejelentkezési formának bizonyulhat. #938
* Az AzCopy-szal integrált Storage Explorer jelenleg előzetes verzióban érhető el. Az engedélyezéshez lépjen az "Előzetes verzió" → "Az AzCopy használata a továbbfejlesztett blob feltöltéshez és letöltéshez". Blob-átvitelek az AzCopy-nal befejeződött gyorsabbnak és hatékonyabbnak kell lennie.

### <a name="fixes"></a>Javítások

* Most már kiválaszthatja a feltölteni kívánt blobtípust, mint amikor az AzCopy engedélyezve van. #1111
* Korábban, ha engedélyezte a statikus webhelyeket egy ADLS Gen2 storage-fiókhoz, majd névvel és kulccsal csatolta, a Storage Explorer nem észlelte, hogy a hierarchikus névtér engedélyezve van. Ezt a problémát megoldottuk. #1081
* A blob szerkesztőben a fennmaradó megőrzési napok vagy állapot szerinti rendezés megszakadt. Ezt a problémát megoldottuk. #1106
* Az 1.5.0 után a Storage Explorer már nem várta meg, hogy a kiszolgálóoldali másolatok befejeződjenek, mielőtt az átnevezés vagy & beillesztés során sikeresnek bizonyult volna. Ezt a problémát megoldottuk. #976
* A kísérleti AzCopy szolgáltatás használatakor a "Parancs másolása a vágólapra" gombra kattintva másolt parancs önmagában nem mindig futtatható. Most az átvitel manuális futtatásához szükséges összes parancs másolásra kerül. #1079
* Korábban az ADLS Gen2 blobok nem érhetők el, ha proxy mögött van. Ennek oka a Storage SDK által használt új hálózati könyvtár hibája volt. Az 1.7.0-s számban kísérlet történt a probléma enyhítésére, de néhányan továbbra is problémákat tapasztalhatnak. A teljes javítás egy későbbi frissítésben jelenik meg. #1090
* Az 1.7.0-s verzióban a fájl mentési párbeszédpanelje helyesen emlékszik arra a helyre, ahhoz, ahhoz, ahamikor a fájlt utoljára mentette. #16
* A tulajdonságok panelen a tárfiók termékváltozat-szintje a fiók megfelelőként jelenik meg. Ezt a problémát megoldottuk. #654
* Néha lehetetlen volt megszakítani egy blob bérletét, még akkor is, ha helyesen adta meg a blob nevét. Ezt a problémát megoldottuk. #1070

### <a name="known-issues"></a>Ismert problémák

* Az RBAC használatakor a Storage Explorer bizonyos felügyeleti réteg-engedélyeket igényel a tárolási erőforrások eléréséhez. További információt a [hibaelhárítási útmutatóban](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) talál.
* Kísérlet az ADLS Gen2 blobok elérésére, ha egy proxy mögött sikertelen lehet.
* A SAS URI-n keresztül csatlakoztatott erőforrásról, például egy blobtárolóról való leválasztás olyan hibát okozhat, amely megakadályozza, hogy más mellékletek megfelelően jelenjenek meg. A probléma kerülő megoldásához frissítse a csoportcsomópontot. További információt a #537 című témakörben talál.
* A SAS URI-n keresztül csatlakoztatott erőforrásról, például egy blobtárolóról való leválasztás olyan hibát okozhat, amely megakadályozza, hogy más mellékletek megfelelően jelenjenek meg. A probléma kerülő megoldásához frissítse a csoportcsomópontot. További információ: #537.
* Ha a VS for Mac-et használja, és valaha is létrehozott egy egyéni AAD-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma kerülő megoldásához törölje a ~/. IdentityService/AadConfigurations. Ha ez nem oldja fel a letiltást, véleményezze a problémát.
* Azurite még nem valósította meg teljes mértékben az összes tárolási API-t. Emiatt előfordulhat, hogy váratlan hibák vagy viselkedés használata kor Azurite fejlesztési tároláshoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza elakad a gyorselérésen. A fókusz leoldásához frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba van rögzített, de még nem egységbe rendezett -ba Elektron. A probléma kerülő megoldásához blobtárolóba való feltöltéskor vagy onnan való letöltéskor használhatja a kísérleti AzCopy funkciót.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ennek az az oka, hogy az itt leírt törlési szűrőt használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Az Azure Stack nem támogatja a következő funkciókat. Ha az Azure Stack-erőforrásokkal való munka során megpróbálja használni ezeket a funkciókat, az váratlan hibákat okozhat.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linux-felhasználók számára telepítenie kell a [.NET Core 2.0-t.](https://dotnet.microsoft.com/download/dotnet-core/2.0)
* Az Ubuntu 14.04 felhasználói számára biztosítania kell, hogy a GCC naprakész legyen - ezt a következő parancsok futtatásával teheti meg, majd újra kell indítania a gépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Az Ubuntu 17.04 felhasználói számára telepítenie kell a GConf-ot - ezt a következő parancsok futtatásával teheti meg, majd újrakell indítania a gépet:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>1.6.2-es verzió
1/9/2019

### <a name="hotfixes"></a>Gyorsjavítások
* Az 1.6.1-ben az ObjectId által az ADLS Gen2 ACL-okhoz hozzáadott entitások, amelyek nem felhasználók, mindig csoportként kerülnek hozzáadásra. Most már csak a csoportok vannak hozzáadva csoportokként, és az entitások, például az Enterprise Applications ésaszolgáltatás-egyszerű felhasználók. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Ha egy ADLS Gen2 storage-fiók nem rendelkezett tárolókkal, és névvel és kulccsal volt csatolva, majd a Storage Explorer nem észleli, hogy a tárfiók ADLS Gen2. Ezt a problémát megoldottuk. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* Az 1.6.0-s bekezdésben a másolás és beillesztés során fellépő ütközések nem kérnek megoldást. Ehelyett az ütköző példány egyszerűen sikertelen lesz. Most, az első konfliktus, meg fogják kérdezni, hogyan szeretné, hogy megoldódott. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* Az API-korlátozások miatt az ObjectIds minden érvényesítése le van tiltva a Hozzáférés kezelése párbeszédpanelen. Az érvényesítés most antól csak a felhasználói upn-ek esetében történik meg. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* Az ADLS Gen2 Manage Access párbeszédpanelen a csoport engedélyeit nem lehetett módosítani. Ezt a problémát megoldottuk. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Hozzáadott drag and drop feltöltési támogatást az ADLS Gen2 szerkesztő. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* Az ADLS Gen2 fájlok és mappák tulajdonságpárbeszédpanelén lévő URL-tulajdonságból néha hiányzott egy "/". Ezt a problémát megoldottuk. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Ha egy ADLS Gen2 tároló, fájl vagy mappa aktuális engedélyei sikertelenek, akkor a hiba mostantól helyesen jelenik meg a tevékenységnaplóban. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* A fájlok megnyitásához létrehozott ideiglenes elérési utat lerövidítették, hogy csökkentsék a Windows rendszeren MAX_PATH hosszabb elérési út létrehozásának esélyét. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* A Csatlakozás párbeszédpanel most már helyesen jelenik meg, ha nincsenek bejelentkezve a felhasználók, és nincsenek erőforrások csatolva. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* Az 1.6.0-s számban a nem HNS blobok és fájlok tulajdonságainak mentése minden tulajdonság értékét kódolja. Ez szükségtelen kódoló értékeket eredményezett, amelyek csak ASCII karaktereket tartalmaztak. Most antól az értékek csak akkor lesznek kódolva, ha nem ASCII karaktereket tartalmaznak. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Egy mappát feltölteni egy nem HNS Blob tároló sikertelen lenne, ha egy SAS-t használnak, és a SAS nem rendelkezik olvasási engedéllyel. Ezt a problémát megoldottuk. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Az AzCopy-átvitel visszavonása nem működött. Ezt a problémát megoldottuk. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy sikertelen lenne, amikor megpróbál letölteni egy mappát egy ADLS Gen2 Blob tárolóból, ha a mappa nevében szóközök találhatók. Ezt a problémát megoldottuk. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* A CosmosDB szerkesztője 1.6.0-ban tört meg. Most már meg van javítva. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Új

* Most már használhatja Storage Explorer eléréséhez a Blob adatok [rbac](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409)keresztül. Ha be van jelentkezve, és a Storage Explorer nem tudja beolvasni a kulcsokat a storage-fiók, majd egy OAuth token hitelesítésére lesz használva, amikor az adatokkal való interakció.
* A Storage Explorer mostantól támogatja az ADLS Gen2 Storage-fiókokat. Amikor a Storage Explorer észleli, hogy a hierarchikus névtér engedélyezve van egy storage-fiók, a storage-fiók neve mellett a "(ADLS Gen2 Preview)" jelenik meg. A Storage Explorer képes észlelni, hogy a hierarchikus névtér engedélyezve van-e, amikor be van jelentkezve, vagy ha a tárfiókot névvel és kulccsal csatolta. ADLS Gen2 Storage-fiókok esetén a Storage Explorer segítségével:
  * Tárolók létrehozása és törlése
  * Tároló tulajdonságainak és engedélyeinek kezelése (bal oldali)
  * Tárolókban lévő adatok megtekintése és navigálása
  * Új mappák létrehozása
  * Fájlok és mappák feltöltése, letöltése, átnevezése és törlése
  * A fájl- és mappatulajdonságok és -engedélyek kezelése (jobb oldali).
    
    Más tipikus Blob-funkciók, például a helyreállítható törlés és a pillanatképek jelenleg nem érhetők el. Az engedélyek kezelése is csak akkor érhető el, ha be van jelentkezve. Emellett ha Egy ADLS Gen2 Storage-fiókban dolgozik, a Storage Explorer az AzCopy programot fogja használni az összes feltöltéshez és letöltéshez, és alapértelmezés szerint név- és kulcshitelesítő adatokat használ az összes művelethez, ha elérhető.
* Erős felhasználói visszajelzések után a megszakítási bérlet ismét használható több blob bérletek megszakítására egyszerre.

### <a name="known-issues"></a>Ismert problémák

* Ha egy ADLS Gen2 Storage-fiókból tölt le, ha az átvitt fájlok egyike már létezik, akkor az AzCopy néha összeomlik. Ezt egy közelgő gyorsjavítás javítja.
* A SAS URI-n keresztül csatlakoztatott erőforrásról, például egy blobtárolóról való leválasztás olyan hibát okozhat, amely megakadályozza, hogy más mellékletek megfelelően jelenjenek meg. A probléma kerülő megoldásához frissítse a csoportcsomópontot. További információ: #537.
* Ha a VS for Mac-et használja, és valaha is létrehozott egy egyéni AAD-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma kerülő megoldásához törölje a ~/. IdentityService/AadConfigurations. Ha ez nem oldja fel a letiltást, véleményezze a problémát.
* Azurite még nem valósította meg teljes mértékben az összes tárolási API-t. Emiatt előfordulhat, hogy váratlan hibák vagy viselkedés használata kor Azurite fejlesztési tároláshoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza elakad a gyorselérésen. A fókusz leoldásához frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba van rögzített, de még nem egységbe rendezett -ba Elektron. A probléma kerülő megoldásához blobtárolóba való feltöltéskor vagy onnan való letöltéskor használhatja a kísérleti AzCopy funkciót.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ennek az az oka, hogy az itt leírt törlési szűrőt használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Az Azure Stack nem támogatja a következő funkciókat. Ha az Azure Stack-erőforrásokkal való munka során megpróbálja használni ezeket a funkciókat, az váratlan hibákat okozhat.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linux-felhasználók számára telepítenie kell a [.NET Core 2.0-t.](https://dotnet.microsoft.com/download/dotnet-core/2.0)
* Az Ubuntu 14.04 felhasználói számára biztosítania kell, hogy a GCC naprakész legyen - ezt a következő parancsok futtatásával teheti meg, majd újra kell indítania a gépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Az Ubuntu 17.04 felhasználói számára telepítenie kell a GConf-ot - ezt a következő parancsok futtatásával teheti meg, majd újrakell indítania a gépet:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>1.6.1-es verzió
12/18/2018

### <a name="hotfixes"></a>Gyorsjavítások
* Az API-korlátozások miatt az ObjectIds minden érvényesítése le van tiltva a Hozzáférés kezelése párbeszédpanelen. Az érvényesítés most antól csak a felhasználói upn-ek esetében történik meg. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* Az ADLS Gen2 Manage Access párbeszédpanelen a csoport engedélyeit nem lehetett módosítani. Ezt a problémát megoldottuk. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Hozzáadott drag and drop feltöltési támogatást az ADLS Gen2 szerkesztő. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* Az ADLS Gen2 fájlok és mappák tulajdonságpárbeszédpanelén lévő URL-tulajdonságból néha hiányzott egy "/". Ezt a problémát megoldottuk. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Ha egy ADLS Gen2 tároló, fájl vagy mappa aktuális engedélyei sikertelenek, akkor a hiba mostantól helyesen jelenik meg a tevékenységnaplóban. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* A fájlok megnyitásához létrehozott ideiglenes elérési utat lerövidítették, hogy csökkentsék a Windows rendszeren MAX_PATH hosszabb elérési út létrehozásának esélyét. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* A Csatlakozás párbeszédpanel most már helyesen jelenik meg, ha nincsenek bejelentkezve a felhasználók, és nincsenek erőforrások csatolva. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* Az 1.6.0-s számban a nem HNS blobok és fájlok tulajdonságainak mentése minden tulajdonság értékét kódolja. Ez szükségtelen kódoló értékeket eredményezett, amelyek csak ASCII karaktereket tartalmaztak. Most antól az értékek csak akkor lesznek kódolva, ha nem ASCII karaktereket tartalmaznak. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Egy mappát feltölteni egy nem HNS Blob tároló sikertelen lenne, ha egy SAS-t használnak, és a SAS nem rendelkezik olvasási engedéllyel. Ezt a problémát megoldottuk. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Az AzCopy-átvitel visszavonása nem működött. Ezt a problémát megoldottuk. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy sikertelen lenne, amikor megpróbál letölteni egy mappát egy ADLS Gen2 Blob tárolóból, ha a mappa nevében szóközök találhatók. Ezt a problémát megoldottuk. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* A CosmosDB szerkesztője 1.6.0-ban tört meg. Most már meg van javítva. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Új

* Most már használhatja Storage Explorer eléréséhez a Blob adatok [rbac](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409)keresztül. Ha be van jelentkezve, és a Storage Explorer nem tudja beolvasni a kulcsokat a storage-fiók, majd egy OAuth token hitelesítésére lesz használva, amikor az adatokkal való interakció.
* A Storage Explorer mostantól támogatja az ADLS Gen2 Storage-fiókokat. Amikor a Storage Explorer észleli, hogy a hierarchikus névtér engedélyezve van egy storage-fiók, a storage-fiók neve mellett a "(ADLS Gen2 Preview)" jelenik meg. A Storage Explorer képes észlelni, hogy a hierarchikus névtér engedélyezve van-e, amikor be van jelentkezve, vagy ha a tárfiókot névvel és kulccsal csatolta. ADLS Gen2 Storage-fiókok esetén a Storage Explorer segítségével:
  * Tárolók létrehozása és törlése
  * Tároló tulajdonságainak és engedélyeinek kezelése (bal oldali)
  * Tárolókban lévő adatok megtekintése és navigálása
  * Új mappák létrehozása
  * Fájlok és mappák feltöltése, letöltése, átnevezése és törlése
  * A fájl- és mappatulajdonságok és -engedélyek kezelése (jobb oldali).
    
    Más tipikus Blob-funkciók, például a helyreállítható törlés és a pillanatképek jelenleg nem érhetők el. Az engedélyek kezelése is csak akkor érhető el, ha be van jelentkezve. Emellett ha Egy ADLS Gen2 Storage-fiókban dolgozik, a Storage Explorer az AzCopy programot fogja használni az összes feltöltéshez és letöltéshez, és alapértelmezés szerint név- és kulcshitelesítő adatokat használ az összes művelethez, ha elérhető.
* Erős felhasználói visszajelzések után a megszakítási bérlet ismét használható több blob bérletek megszakítására egyszerre.

### <a name="known-issues"></a>Ismert problémák

* Ha egy ADLS Gen2 Storage-fiókból tölt le, ha az átvitt fájlok egyike már létezik, akkor az AzCopy néha összeomlik. Ezt egy közelgő gyorsjavítás javítja.
* A SAS URI-n keresztül csatlakoztatott erőforrásról, például egy blobtárolóról való leválasztás olyan hibát okozhat, amely megakadályozza, hogy más mellékletek megfelelően jelenjenek meg. A probléma kerülő megoldásához frissítse a csoportcsomópontot. További információ: #537.
* Ha a VS for Mac-et használja, és valaha is létrehozott egy egyéni AAD-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma kerülő megoldásához törölje a ~/. IdentityService/AadConfigurations. Ha ez nem oldja fel a letiltást, véleményezze a problémát.
* Azurite még nem valósította meg teljes mértékben az összes tárolási API-t. Emiatt előfordulhat, hogy váratlan hibák vagy viselkedés használata kor Azurite fejlesztési tároláshoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza elakad a gyorselérésen. A fókusz leoldásához frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba van rögzített, de még nem egységbe rendezett -ba Elektron. A probléma kerülő megoldásához blobtárolóba való feltöltéskor vagy onnan való letöltéskor használhatja a kísérleti AzCopy funkciót.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ennek az az oka, hogy az itt leírt törlési szűrőt használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Az Azure Stack nem támogatja a következő funkciókat. Ha az Azure Stack-erőforrásokkal való munka során megpróbálja használni ezeket a funkciókat, az váratlan hibákat okozhat.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linux-felhasználók számára telepítenie kell a [.NET Core 2.0-t.](https://dotnet.microsoft.com/download/dotnet-core/2.0)
* Az Ubuntu 14.04 felhasználói számára biztosítania kell, hogy a GCC naprakész legyen - ezt a következő parancsok futtatásával teheti meg, majd újra kell indítania a gépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Az Ubuntu 17.04 felhasználói számára telepítenie kell a GConf-ot - ezt a következő parancsok futtatásával teheti meg, majd újrakell indítania a gépet:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>1.6.0-s verzió
2018.12.05.

### <a name="new"></a>Új

* Most már használhatja Storage Explorer eléréséhez a Blob adatok [rbac](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409)keresztül. Ha be van jelentkezve, és a Storage Explorer nem tudja beolvasni a kulcsokat a storage-fiók, majd egy OAuth token hitelesítésére lesz használva, amikor az adatokkal való interakció.
* A Storage Explorer mostantól támogatja az ADLS Gen2 Storage-fiókokat. Amikor a Storage Explorer észleli, hogy a hierarchikus névtér engedélyezve van egy storage-fiók, a storage-fiók neve mellett a "(ADLS Gen2 Preview)" jelenik meg. A Storage Explorer képes észlelni, hogy a hierarchikus névtér engedélyezve van-e, amikor be van jelentkezve, vagy ha a tárfiókot névvel és kulccsal csatolta. ADLS Gen2 Storage-fiókok esetén a Storage Explorer segítségével:
  * Tárolók létrehozása és törlése
  * Tároló tulajdonságainak és engedélyeinek kezelése (bal oldali)
  * Tárolókban lévő adatok megtekintése és navigálása
  * Új mappák létrehozása
  * Fájlok és mappák feltöltése, letöltése, átnevezése és törlése
  * A fájl- és mappatulajdonságok és -engedélyek kezelése (jobb oldali).
    
    Más tipikus Blob-funkciók, például a helyreállítható törlés és a pillanatképek jelenleg nem érhetők el. Az engedélyek kezelése is csak akkor érhető el, ha be van jelentkezve. Emellett ha Egy ADLS Gen2 Storage-fiókban dolgozik, a Storage Explorer az AzCopy programot fogja használni az összes feltöltéshez és letöltéshez, és alapértelmezés szerint név- és kulcshitelesítő adatokat használ az összes művelethez, ha elérhető.
* Erős felhasználói visszajelzések után a megszakítási bérlet ismét használható több blob bérletek megszakítására egyszerre.

### <a name="known-issues"></a>Ismert problémák

* Ha egy ADLS Gen2 Storage-fiókból tölt le, ha az átvitt fájlok egyike már létezik, akkor az AzCopy néha összeomlik. Ezt egy közelgő gyorsjavítás javítja.
* A SAS URI-n keresztül csatlakoztatott erőforrásról, például egy blobtárolóról való leválasztás olyan hibát okozhat, amely megakadályozza, hogy más mellékletek megfelelően jelenjenek meg. A probléma kerülő megoldásához frissítse a csoportcsomópontot. További információ: #537.
* Ha a VS for Mac-et használja, és valaha is létrehozott egy egyéni AAD-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma kerülő megoldásához törölje a ~/. IdentityService/AadConfigurations. Ha ez nem oldja fel a letiltást, véleményezze a problémát.
* Azurite még nem valósította meg teljes mértékben az összes tárolási API-t. Emiatt előfordulhat, hogy váratlan hibák vagy viselkedés használata kor Azurite fejlesztési tároláshoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza elakad a gyorselérésen. A fókusz leoldásához frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba van rögzített, de még nem egységbe rendezett -ba Elektron. A probléma kerülő megoldásához blobtárolóba való feltöltéskor vagy onnan való letöltéskor használhatja a kísérleti AzCopy funkciót.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ennek az az oka, hogy az itt leírt törlési szűrőt használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Az Azure Stack nem támogatja a következő funkciókat. Ha az Azure Stack-erőforrásokkal való munka során megpróbálja használni ezeket a funkciókat, az váratlan hibákat okozhat.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linux-felhasználók számára telepítenie kell a [.NET Core 2.0-t.](https://dotnet.microsoft.com/download/dotnet-core/2.0)
* Az Ubuntu 14.04 felhasználói számára biztosítania kell, hogy a GCC naprakész legyen - ezt a következő parancsok futtatásával teheti meg, majd újra kell indítania a gépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Az Ubuntu 17.04 felhasználói számára telepítenie kell a GConf-ot - ezt a következő parancsok futtatásával teheti meg, majd újrakell indítania a gépet:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>1.5.0-s verzió
10/29/2018

### <a name="new"></a>Új

* Most már [használhatja az AzCopy v10 (Előzetes verzió)](https://github.com/Azure/azure-storage-azcopy) a blobok feltöltéséhez és letöltéséhez. A funkció engedélyezéséhez lépjen a "Kísérleti" menüre, majd kattintson az "AzCopy használata a továbbfejlesztett blob feltöltéshez és letöltéshez" parancsra. Ha engedélyezve van, az AzCopy a következő esetekben lesz használva:
   * Mappák és fájlok feltöltése blobtárolókba az eszköztáron keresztül vagy húzással.
   * Mappák és fájlok letöltése az eszköztáron vagy a helyi menün keresztül.

* Továbbá, ha az AzCopy:
   * Az átvitel vágólapra történő végrehajtásához használt AzCopy parancs másolható. Egyszerűen kattintson a tevékenységnapló "AzCopy parancs másolása a vágólapra" lehetőségre.
   * A blobszerkesztőt manuálisan kell frissítenie a feltöltés után.
   * Fájlok feltöltése hozzáfűző blobok nem támogatott, és a vhd fájlokat kell feltölteni a lap blobok, és az összes többi fájl lesz feltöltve blokk blobok.
   * A feltöltés vagy letöltés során előforduló hibák és ütközések csak a feltöltés vagy a letöltés befejezése után jelennek meg.

Végül az AzCopy fájlmegosztásokkal való használatának támogatása a jövőben érkezik.
* A Storage Explorer most antól az Electron 2.0.11-es verzióját használja.
* A bérletek feltörése mostantól egyszerre csak egy blobon hajtható végre. Emellett meg kell adnia annak a blobnak a nevét, amelynek bérletét megszakítja. Ez a módosítás a bérlet véletlen feltörésének valószínűségének csökkentése érdekében történt, különösen a virtuális gépek esetében. #394
* Ha valaha is bejelentkezési problémákkal szembesül, most megpróbálhatja alaphelyzetbe állítása hitelesítés. Lépjen a "Súgó" menüre, és kattintson a "Visszaállítás" gombra a funkció eléréséhez. #419

### <a name="fix"></a>Hibajavítás

* Erős felhasználói visszajelzés után az alapértelmezett emulátorcsomópont újra engedélyezve van. A Csatlakozás párbeszédpanelen továbbra is hozzáadhat további emulátorkapcsolatokat, de ha az emulátor az alapértelmezett portok használatára van konfigurálva, akkor a "Helyi & csatolt/tárfiókok" alatt is használhatja az "Emulátor * Alapértelmezett portok" csomópontot. #669
* A Storage Explorer a továbbiakban nem teszi lehetővé a kezdő vagy záró szóközt tartalmazó blob metaadat-értékek beállítását. #760
* A "Bejelentkezés" gomb mindig engedélyezve volt a Csatlakozás párbeszédpanel ugyanazon oldalain. Most már le van tiltva, ha szükséges. #761
* A gyorselérés a továbbiakban nem okoz hibát a konzolon, ha nem adott hozzá gyorselérési elemeket.

### <a name="known-issues"></a>Ismert problémák

* A SAS URI-n keresztül csatlakoztatott erőforrásról, például egy blobtárolóról való leválasztás olyan hibát okozhat, amely megakadályozza, hogy más mellékletek megfelelően jelenjenek meg. A probléma kerülő megoldásához frissítse a csoportcsomópontot. További információ: #537.
* Ha a VS for Mac-et használja, és valaha is létrehozott egy egyéni AAD-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma kerülő megoldásához törölje a ~/. IdentityService/AadConfigurations. Ha ez nem oldja fel a letiltást, véleményezze a problémát.
* Azurite még nem valósította meg teljes mértékben az összes tárolási API-t. Emiatt előfordulhat, hogy váratlan hibák vagy viselkedés használata kor Azurite fejlesztési tároláshoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza elakad a gyorselérésen. A fókusz leoldásához frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba van rögzített, de még nem egységbe rendezett -ba Elektron. A probléma kerülő megoldásához blobtárolóba való feltöltéskor vagy onnan való letöltéskor használhatja a kísérleti AzCopy funkciót.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ennek az az oka, hogy az itt leírt törlési szűrőt használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Az Azure Stack nem támogatja a következő funkciókat. Ha az Azure Stack-erőforrásokkal való munka során megpróbálja használni ezeket a funkciókat, az váratlan hibákat okozhat.
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linux-felhasználók számára telepítenie kell a [.NET Core 2.0-t.](https://dotnet.microsoft.com/download/dotnet-core/2.0)
* Az Ubuntu 14.04 felhasználói számára biztosítania kell, hogy a GCC naprakész legyen - ezt a következő parancsok futtatásával teheti meg, majd újra kell indítania a gépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Az Ubuntu 17.04 felhasználói számára telepítenie kell a GConf-ot - ezt a következő parancsok futtatásával teheti meg, majd újrakell indítania a gépet:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>1.4.4-es verzió
10/15/2018

### <a name="hotfixes"></a>Gyorsjavítások
* Az Azure Resource Management API-verziója vissza lett állítva az Azure US Government felhasználóinak blokkolásának feloldásához. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* A spinnerek betöltése most CSS-animációkat használ a Storage Explorer által használt GPU mennyiségének csökkentésére. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Új
* A külső erőforrás-mellékletek, például a SAS-kapcsolatok és emulátorok esetében jelentősen javultak. Ezután:
   * A csatolni kívánt erőforrás megjelenítendő nevének testreszabása. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Több helyi emulátorhoz csatolhat különböző portokat. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Csatolt erőforrások hozzáadása a gyorseléréshez. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* A Storage Explorer mostantól támogatja a soft delete parancsot. A következőket teheti:
   * Konfigurálja a soft delete házirendet a jobb gombbal a Blob Containers csomóponta a storage-fiók.
   * Tekintse meg a helyreállíthatóan törölt blobok a Blob Editor kiválasztásával "Aktív és törölt blobok" a legördülő menüben a navigációs sáv mellett.
   * A helyreállíthatóan törölt blobok törlésének törlése.

### <a name="fixes"></a>Javítások
* A "Cors-beállítások konfigurálása" művelet már nem érhető el a prémium szintű storage-fiókok, mert a prémium szintű storage-fiókok nem támogatják a CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Most már létezik egy megosztott hozzáférésű aláírás tulajdonság a SAS csatolt szolgáltatásokhoz. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Az "Alapértelmezett hozzáférési szint beállítása" művelet már elérhető a blob és a GPV2 storage-fiókok, amelyek a gyors eléréshez rögzített. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Néha a Storage Explorer nem tudta megjeleníteni a Klasszikus tárfiókokat. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Ismert problémák
* Emulátorok, például az Azure Storage-emulátor vagy az Azurite használataesetén az alapértelmezett portokon lévő kapcsolatokfigyelésével kell rendelkeznie. Ellenkező esetben a Storage Explorer nem tud csatlakozni hozzájuk.
* Ha a VS for Mac-et használja, és valaha is létrehozott egy egyéni AAD-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma kerülő megoldásához törölje a ~/. IdentityService/AadConfigurations. Ha ez nem feloldása van, kérjük, véleményezze ezt a [kérdést](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite még nem valósította meg teljes mértékben az összes tárolási API-t. Emiatt előfordulhat, hogy váratlan hibák vagy viselkedés használata kor Azurite fejlesztési tároláshoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza elakad a gyorselérésen. A fókusz leoldásához frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba van rögzített, de még nem egységbe rendezett -ba Elektron.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ez azért van, mert az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt szűrőszűrési munkát használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is megjelenik egy csatolt Azure Stack tárfiók alatt.
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linux-felhasználók számára telepítenie kell a [.NET Core 2.0-t.](https://dotnet.microsoft.com/download/dotnet-core/2.0)
* Az Ubuntu 14.04 felhasználói számára biztosítania kell, hogy a GCC naprakész legyen - ezt a következő parancsok futtatásával teheti meg, majd újra kell indítania a gépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Az Ubuntu 17.04 felhasználói számára telepítenie kell a GConf-ot - ezt a következő parancsok futtatásával teheti meg, majd újrakell indítania a gépet:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>1.4.3-as verzió
10/11/2018

### <a name="hotfixes"></a>Gyorsjavítások
* Az Azure Resource Management API-verziója vissza lett állítva az Azure US Government felhasználóinak blokkolásának feloldásához. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* A spinnerek betöltése most CSS-animációkat használ a Storage Explorer által használt GPU mennyiségének csökkentésére. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Új
* A külső erőforrás-mellékletek, például a SAS-kapcsolatok és emulátorok esetében jelentősen javultak. Ezután:
   * A csatolni kívánt erőforrás megjelenítendő nevének testreszabása. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Több helyi emulátorhoz csatolhat különböző portokat. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Csatolt erőforrások hozzáadása a gyorseléréshez. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* A Storage Explorer mostantól támogatja a soft delete parancsot. A következőket teheti:
   * Konfigurálja a soft delete házirendet a jobb gombbal a Blob Containers csomóponta a storage-fiók.
   * Tekintse meg a helyreállíthatóan törölt blobok a Blob Editor kiválasztásával "Aktív és törölt blobok" a legördülő menüben a navigációs sáv mellett.
   * A helyreállíthatóan törölt blobok törlésének törlése.

### <a name="fixes"></a>Javítások
* A "Cors-beállítások konfigurálása" művelet már nem érhető el a prémium szintű storage-fiókok, mert a prémium szintű storage-fiókok nem támogatják a CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Most már létezik egy megosztott hozzáférésű aláírás tulajdonság a SAS csatolt szolgáltatásokhoz. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Az "Alapértelmezett hozzáférési szint beállítása" művelet már elérhető a blob és a GPV2 storage-fiókok, amelyek a gyors eléréshez rögzített. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Néha a Storage Explorer nem tudta megjeleníteni a Klasszikus tárfiókokat. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Ismert problémák
* Emulátorok, például az Azure Storage-emulátor vagy az Azurite használataesetén az alapértelmezett portokon lévő kapcsolatokfigyelésével kell rendelkeznie. Ellenkező esetben a Storage Explorer nem tud csatlakozni hozzájuk.
* Ha a VS for Mac-et használja, és valaha is létrehozott egy egyéni AAD-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma kerülő megoldásához törölje a ~/. IdentityService/AadConfigurations. Ha ez nem feloldása van, kérjük, véleményezze ezt a [kérdést](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite még nem valósította meg teljes mértékben az összes tárolási API-t. Emiatt előfordulhat, hogy váratlan hibák vagy viselkedés használata kor Azurite fejlesztési tároláshoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza elakad a gyorselérésen. A fókusz leoldásához frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba van rögzített, de még nem egységbe rendezett -ba Elektron.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ez azért van, mert az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt szűrőszűrési munkát használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is megjelenik egy csatolt Azure Stack tárfiók alatt.
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linux-felhasználók számára telepítenie kell a [.NET Core 2.0-t.](https://dotnet.microsoft.com/download/dotnet-core/2.0)
* Az Ubuntu 14.04 felhasználói számára biztosítania kell, hogy a GCC naprakész legyen - ezt a következő parancsok futtatásával teheti meg, majd újra kell indítania a gépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Az Ubuntu 17.04 felhasználói számára telepítenie kell a GConf-ot - ezt a következő parancsok futtatásával teheti meg, majd újrakell indítania a gépet:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>1.4.2-es verzió
09/24/2018

### <a name="hotfixes"></a>Gyorsjavítások
* Frissítse az Azure Resource Management API-verziója 2018-07-01-re az új Azure Storage-fiók-típusú verziók támogatásának hozzáadásához. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Új
* A külső erőforrás-mellékletek, például a SAS-kapcsolatok és emulátorok esetében jelentősen javultak. Ezután:
   * A csatolni kívánt erőforrás megjelenítendő nevének testreszabása. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Több helyi emulátorhoz csatolhat különböző portokat. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Csatolt erőforrások hozzáadása a gyorseléréshez. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* A Storage Explorer mostantól támogatja a soft delete parancsot. A következőket teheti:
   * Konfigurálja a soft delete házirendet a jobb gombbal a Blob Containers csomóponta a storage-fiók.
   * Tekintse meg a helyreállíthatóan törölt blobok a Blob Editor kiválasztásával "Aktív és törölt blobok" a legördülő menüben a navigációs sáv mellett.
   * A helyreállíthatóan törölt blobok törlésének törlése.

### <a name="fixes"></a>Javítások
* A "Cors-beállítások konfigurálása" művelet már nem érhető el a prémium szintű storage-fiókok, mert a prémium szintű storage-fiókok nem támogatják a CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Most már létezik egy megosztott hozzáférésű aláírás tulajdonság a SAS csatolt szolgáltatásokhoz. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Az "Alapértelmezett hozzáférési szint beállítása" művelet már elérhető a blob és a GPV2 storage-fiókok, amelyek a gyors eléréshez rögzített. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Néha a Storage Explorer nem tudta megjeleníteni a Klasszikus tárfiókokat. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Ismert problémák
* Emulátorok, például az Azure Storage-emulátor vagy az Azurite használataesetén az alapértelmezett portokon lévő kapcsolatokfigyelésével kell rendelkeznie. Ellenkező esetben a Storage Explorer nem tud csatlakozni hozzájuk.
* Ha a VS for Mac-et használja, és valaha is létrehozott egy egyéni AAD-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma kerülő megoldásához törölje a ~/. IdentityService/AadConfigurations. Ha ez nem feloldása van, kérjük, véleményezze ezt a [kérdést](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite még nem valósította meg teljes mértékben az összes tárolási API-t. Emiatt előfordulhat, hogy váratlan hibák vagy viselkedés használata kor Azurite fejlesztési tároláshoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza elakad a gyorselérésen. A fókusz leoldásához frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba van rögzített, de még nem egységbe rendezett -ba Elektron.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ez azért van, mert az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt szűrőszűrési munkát használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is megjelenik egy csatolt Azure Stack tárfiók alatt.
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linux-felhasználók számára telepítenie kell a [.NET Core 2.0-t.](https://dotnet.microsoft.com/download/dotnet-core/2.0)
* Az Ubuntu 14.04 felhasználói számára biztosítania kell, hogy a GCC naprakész legyen - ezt a következő parancsok futtatásával teheti meg, majd újra kell indítania a gépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Az Ubuntu 17.04 felhasználói számára telepítenie kell a GConf-ot - ezt a következő parancsok futtatásával teheti meg, majd újrakell indítania a gépet:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>1.4.1-es verzió
08/28/2018

### <a name="hotfixes"></a>Gyorsjavítások
* Az első indításkor a Storage Explorer nem tudta létrehozni a bizalmas adatok titkosításához használt kulcsot. Ez problémákat okozna a gyorselérés és az erőforrások csatolása során. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Ha a fiók nem igényel többéves korig az otthoni bérlő, de néhány más bérlők, Storage Explorer nem lenne képes felsorolni az előfizetéseket. Most, miután bejelentkezett egy ilyen fiókkal, a Storage Explorer kérni fogja, hogy adja meg újra a hitelesítő adatait, és végre MFA. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* A Storage Explorer nem tudott erőforrásokat csatolni az Azure Germany és az Azure US Government. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Ha két olyan fiókba jelentkezett be, amelyeknek ugyanaz az e-mail címe, a Storage Explorer néha nem jeleníti meg az erőforrásokat a fanézetben. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* Lassabb Windows-gépeken a kezdőkép néha jelentős időt vesz igénybe. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* A csatlakozás párbeszédpanel akkor is megjelenik, ha csatolt fiókok vagy szolgáltatások vannak. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Új
* A külső erőforrás-mellékletek, például a SAS-kapcsolatok és emulátorok esetében jelentősen javultak. Ezután:
   * A csatolni kívánt erőforrás megjelenítendő nevének testreszabása. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Több helyi emulátorhoz csatolhat különböző portokat. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Csatolt erőforrások hozzáadása a gyorseléréshez. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* A Storage Explorer mostantól támogatja a soft delete parancsot. A következőket teheti:
   * Konfigurálja a soft delete házirendet a jobb gombbal a Blob Containers csomóponta a storage-fiók.
   * Tekintse meg a helyreállíthatóan törölt blobok a Blob Editor kiválasztásával "Aktív és törölt blobok" a legördülő menüben a navigációs sáv mellett.
   * A helyreállíthatóan törölt blobok törlésének törlése.

### <a name="fixes"></a>Javítások
* A "Cors-beállítások konfigurálása" művelet már nem érhető el a prémium szintű storage-fiókok, mert a prémium szintű storage-fiókok nem támogatják a CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Most már létezik egy megosztott hozzáférésű aláírás tulajdonság a SAS csatolt szolgáltatásokhoz. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Az "Alapértelmezett hozzáférési szint beállítása" művelet már elérhető a blob és a GPV2 storage-fiókok, amelyek a gyors eléréshez rögzített. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Néha a Storage Explorer nem tudta megjeleníteni a Klasszikus tárfiókokat. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Ismert problémák
* Emulátorok, például az Azure Storage-emulátor vagy az Azurite használataesetén az alapértelmezett portokon lévő kapcsolatokfigyelésével kell rendelkeznie. Ellenkező esetben a Storage Explorer nem tud csatlakozni hozzájuk.
* Ha a VS for Mac-et használja, és valaha is létrehozott egy egyéni AAD-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma kerülő megoldásához törölje a ~/. IdentityService/AadConfigurations. Ha ez nem feloldása van, kérjük, véleményezze ezt a [kérdést](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite még nem valósította meg teljes mértékben az összes tárolási API-t. Emiatt előfordulhat, hogy váratlan hibák vagy viselkedés használata kor Azurite fejlesztési tároláshoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza elakad a gyorselérésen. A fókusz leoldásához frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba van rögzített, de még nem egységbe rendezett -ba Elektron.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ez azért van, mert az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt szűrőszűrési munkát használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is megjelenik egy csatolt Azure Stack tárfiók alatt.
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linux-felhasználók számára telepítenie kell a [.NET Core 2.0-t.](https://dotnet.microsoft.com/download/dotnet-core/2.0)
* Az Ubuntu 14.04 felhasználói számára biztosítania kell, hogy a GCC naprakész legyen - ezt a következő parancsok futtatásával teheti meg, majd újra kell indítania a gépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Az Ubuntu 17.04 felhasználói számára telepítenie kell a GConf-ot - ezt a következő parancsok futtatásával teheti meg, majd újrakell indítania a gépet:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>1.3.0-s verzió
07/09/2018

### <a name="new"></a>Új
* A static websites által használt $web tárolók elérése már támogatott. Ez lehetővé teszi, hogy könnyen feltölteni és kezelni a fájlokat és mappákat által használt webhely. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* A macOS alkalmazássávja átlett szervezve. A módosítások közé tartozik a Fájl menü, néhány billentyűparancs-módosítás és számos új parancs az alkalmazás menüalatt. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* Az Azure US Government-be való bejelentkezés hatósági végpontjahttps://login.microsoftonline.us/
* Kisegítő lehetőségek: Ha a képernyőolvasó aktív, a billentyűzetnavigáció mostantól az elemek jobb oldalon való megjelenítéséhez használt táblázatokkal működik. A nyílbillentyűkkel navigálhat a sorokban és oszlopokban, az Enter billentyűvel az alapértelmezett műveletek meghívásához, a helyi menübillentyűvel megnyithatja egy elem helyi menüjét, a Shift vagy a Control billentyűkombinációt pedig a többszörös kijelöléshez. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Javítások
*  Egyes gépeken a gyermekfolyamatok elindulása hosszú időt vett igénybe. Ha ez megtörténik, megjelenik egy "gyermekfolyamat nem indult el időben" hibaüzenet jelenik meg. A gyermekfolyamat indításához rendelkezésre rendelt idő 20 másodpercről 90 másodpercre nőtt. Ha továbbra is érinti a probléma, kérjük, véleményezze a kapcsolódó GitHub-problémát. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* Olvasási engedéllyel nem rendelkező SAS használata esetén nem lehetett nagy blobot feltölteni. A feltöltés logikája módosult, hogy ebben a forgatókönyvben működjön. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* A tároló nyilvános hozzáférési szintjének beállítása eltávolítaná az összes hozzáférési házirendet, és fordítva. Most, nyilvános hozzáférési szint és a hozzáférési házirendek megmaradnak, ha a kettő közül bármelyik. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* Az "AccessTierChangeTime" csonkolva lett a Tulajdonságok párbeszédpanelen. Ezt a problémát megoldottuk. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* A "Microsoft Azure Storage Explorer -" előtag hiányzott az Új könyvtár létrehozása párbeszédpanelről. Ezt a problémát megoldottuk. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Kisegítő lehetőségek: Az Entitás hozzáadása párbeszédpanelen nehéz volt navigálni a VoiceOver használatakor. Fejlesztések történtek. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Kisegítő lehetőségek: A Műveletek és tulajdonságok ablaktábla összecsukás/kibontásgombjának háttérszíne nem volt összhangban a Kontrasztos fekete téma hasonló felhasználói felületi vezérlőivel. A színe megváltozott. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Kisegítő lehetőségek: A Kontrasztos fekete téma esetében a Tulajdonságok párbeszédpanel "X" gombjának fókuszstílusa nem volt látható. Ezt a problémát megoldottuk. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Hozzáférhetőség: A Műveletek és tulajdonságok fülek számos áriaértékből hiányoztak, ami subpar képernyőolvasó élményt eredményezett. A hiányzó áriaértékek most már hozzá. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Hozzáférhetőség: A bal oldalon lévő összecsukott facsomópontok nem kaptak hamis áriával bővített értéket. Ezt a problémát megoldottuk. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Ismert problémák
* A SAS URI-n keresztül csatlakoztatott erőforrásról, például egy blobtárolóról való leválasztás olyan hibát okozhat, amely megakadályozza, hogy más mellékletek megfelelően jelenjenek meg. A probléma kerülő megoldásához frissítse a csoportcsomópontot. További információt ebben a [témakörben](https://github.com/Microsoft/AzureStorageExplorer/issues/537) talál.
* Ha a VS for Mac-et használja, és valaha is létrehozott egy egyéni AAD-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma kerülő megoldásához törölje a ~/. IdentityService/AadConfigurations. Ha ez nem feloldása van, kérjük, véleményezze ezt a [kérdést](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite még nem valósította meg teljes mértékben az összes tárolási API-t. Emiatt előfordulhat, hogy váratlan hibák vagy viselkedés használata kor Azurite fejlesztési tároláshoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza elakad a gyorselérésen. A fókusz leoldásához frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba van rögzített, de még nem egységbe rendezett -ba Elektron.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ez azért van, mert az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt szűrőszűrési munkát használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Az Azure Stack nem támogatja a következő funkciókat, és ha az Azure Stackkel való munka során megpróbálja használni őket, az váratlan hibákat okozhat:
   * Fájlmegosztások
   * Hozzáférési szintek
   * Helyreállítható törlés
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linux-felhasználók számára telepítenie kell a [.NET Core 2.0-t.](https://dotnet.microsoft.com/download/dotnet-core/2.0)
* Az Ubuntu 14.04 felhasználói számára biztosítania kell, hogy a GCC naprakész legyen - ezt a következő parancsok futtatásával teheti meg, majd újra kell indítania a gépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Az Ubuntu 17.04 felhasználói számára telepítenie kell a GConf-ot - ezt a következő parancsok futtatásával teheti meg, majd újrakell indítania a gépet:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>1.2.0-s verzió
06/12/2018

### <a name="new"></a>Új
* Ha a Storage Explorer nem tudja betölteni az előfizetéseket a bérlők nek csak egy részhalmazából, akkor a sikeresen betöltött előfizetések egy hibaüzenettel együtt jelennek meg, amely kifejezetten a sikertelen bérlők számára jelenik meg. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* Windows rendszerben, ha egy frissítés elérhetővé válik, most már választhatja a "Frissítés bezáráskor" lehetőséget. Ha ezt a beállítást választja, a frissítés telepítője a Storage Explorer bezárása után futni fog. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* A fájlmegosztás pillanatképének megtekintésekor a visszaállítási pillanatkép hozzá lett adva a fájlmegosztás-szerkesztő helyi menüjéhez. [#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* A Várólista törlése gomb mostantól mindig engedélyezve van. [#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* Az ADFS Azure Stackbe való bejelentkezés támogatása újra engedélyezve van. Az Azure Stack 1804-es vagy újabb verziója szükséges. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Javítások
* Ha megtekintette egy fájlmegosztás pillanatképeit, amelynek neve ugyanazon tárfiókegy másik fájlmegosztáselőtagja volt, akkor a másik fájlmegosztás pillanatképei is megjelennek. A probléma ki lett javítva. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* Ha SAS-on keresztül csatlakozik, a fájl visszaállítása egy fájlmegosztás pillanatképe hiba esetén. A probléma ki lett javítva. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* Egy blob pillanatképeinek megtekintésekor a Snapshot előléptetése művelet engedélyezve volt, amikor az alap blob és egyetlen pillanatkép lett kiválasztva. A művelet mostantól csak akkor engedélyezett, ha egyetlen pillanatkép van kiválasztva. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Ha egyetlen feladat (például egy blob letöltése) indult el, és később nem sikerült, akkor nem automatikusan újra, amíg el nem kezdett egy másik feladat az azonos típusú. Az összes feladatnak automatikusan újra kell próbálkoznia, függetlenül attól, hogy hány feladat várakoztatott.
* A GPV2-ben újonnan létrehozott blobtárolók számára megnyitott szerkesztők nem rendelkeztek Access Tier oszloppal. A probléma ki lett javítva. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* Az Access Tier oszlop néha nem jelenik meg, ha egy storage-fiók vagy blob tároló sas-on keresztül lett csatolva. Az oszlop most antól mindig megjelenik, de üres értékkel, ha nincs hozzáférési szint beállítva. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* Az újonnan feltöltött blokkblob hozzáférési szintjének beállítása le van tiltva. A probléma ki lett javítva. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Ha a "Tab ultúra nyitva tartása" gombot billentyűzettel hívták meg, akkor a billentyűzet fókusza elvész. Most, a hangsúly fog lépni rá a lapot, hogy nyitva tartották. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* A Lekérdezésszerkesztőben lévő lekérdezések esetében a VoiceOver nem adott használható leírást az aktuális operátorról. Ez most már leíróbb. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* A különböző szerkesztők oldalszámozási hivatkozásai nem voltak leíró jellegűek. Ezek megváltoztak, hogy több leíró. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* Az Entitás hozzáadása párbeszédpanelen a VoiceOver nem jelentette be, hogy egy bemeneti elem melyik oszlopban volt része. Az aktuális oszlop neve most már szerepel az elem leírásában. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* A választógombok és a jelölőnégyzetek fókuszban nem rendelkeztek látható kerettel. A probléma ki lett javítva. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Ismert problémák
* Emulátorok, például az Azure Storage-emulátor vagy az Azurite használataesetén az alapértelmezett portokon lévő kapcsolatokfigyelésével kell rendelkeznie. Ellenkező esetben a Storage Explorer nem tud csatlakozni hozzájuk.
* Ha a VS for Mac-et használja, és valaha is létrehozott egy egyéni AAD-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma kerülő megoldásához törölje a ~/. IdentityService/AadConfigurations. Ha ez nem feloldása van, kérjük, véleményezze ezt a [kérdést](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite még nem valósította meg teljes mértékben az összes tárolási API-t. Emiatt előfordulhat, hogy váratlan hibák vagy viselkedés használata kor Azurite fejlesztési tároláshoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza elakad a gyorselérésen. A fókusz leoldásához frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba van rögzített, de még nem egységbe rendezett -ba Elektron.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ez azért van, mert az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt szűrőszűrési munkát használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is megjelenik egy csatolt Azure Stack tárfiók alatt.
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linux-felhasználók számára telepítenie kell a [.NET Core 2.0-t.](https://dotnet.microsoft.com/download/dotnet-core/2.0)
* Az Ubuntu 14.04 felhasználói számára biztosítania kell, hogy a GCC naprakész legyen - ezt a következő parancsok futtatásával teheti meg, majd újra kell indítania a gépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Az Ubuntu 17.04 felhasználói számára telepítenie kell a GConf-ot - ezt a következő parancsok futtatásával teheti meg, majd újrakell indítania a gépet:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>1.1.0-s verzió
05/09/2018

### <a name="new"></a>Új
* A Storage Explorer mostantól támogatja az Azurite használatát. Megjegyzés: az Azurite-hoz való kapcsolat az alapértelmezett fejlesztési végpontokra van kódolva.
* A Storage Explorer mostantól támogatja a Csak blobhoz és a GPV2 storage-fiókokhoz tartozó hozzáférési szinteket. Az Access-csomagokról [itt](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)olvashat bővebben.
* A SAS létrehozásakor már nincs szükség kezdési időre.

### <a name="fixes"></a>Javítások
* Az Egyesült Államok kormányzati fiókjaira vonatkozó előfizetések lekérése megszakadt. A probléma ki lett javítva. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* A hozzáférési házirendek lejárati ideje nem lett megfelelően mentve. A probléma ki lett javítva. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* Amikor egy tárolóban lévő elem SAS-URL-címét hozlétre, az elem neve nem lett hozzáfűzve az URL-címhez. A probléma ki lett javítva. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* SAS létrehozásakor a múltbeli lejárati idők néha az alapértelmezett értékek. Ennek az volt az oka, hogy a Storage Explorer az utoljára használt kezdési és lejárati időt használta alapértelmezett értékként. Mostantól minden alkalommal, amikor megnyitja a SAS-párbeszédpanelt, új alapértelmezett értékek jönnek létre. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* A storage-fiókok közötti másoláskor egy 24 órás SAS jön létre. Ha a másolat több mint 24 órán át tartott, akkor a másolat sikertelen lesz. Növeltük az SAS-k az elmúlt 1 hét, hogy csökkentsék annak az esélyét, egy példány nem lejárt SAS miatt. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* Egyes tevékenységek kattintva "Mégse" nem mindig működik. A probléma ki lett javítva. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* Egyes tevékenységek esetében az átviteli sebesség rossz volt. A probléma ki lett javítva. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* A Nézet menü "Előző" parancsának helyesírása hibás volt. Most már helyesen van beírva. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* Az utolsó oldalon a Windows telepítő volt a "Tovább" gombot. "Befejezés" gombra változott. [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* A tabulátorfókusz nem volt látható a párbeszédpanelekgombjainál a HC Fekete téma használatakor. Most már látható. [#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* A tevékenységnaplóban lévő műveletekhez az "Auto-Resolve" burkolata hibás volt. Most már helyes. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* Amikor egy entitást egy táblából tetszően ad ki, a megerősítést kérő párbeszédpanel hibaikont jelenít meg. A párbeszéd most egy figyelmeztető ikont használ. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Ismert problémák
* Ha a VS for Mac-et használja, és valaha is létrehozott egy egyéni AAD-konfigurációt, előfordulhat, hogy nem tud bejelentkezni. A probléma kerülő megoldásához törölje a ~/. IdentityService/AadConfigurations. Ha ez nem feloldása van, kérjük, véleményezze ezt a [kérdést](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite még nem valósította meg teljes mértékben az összes tárolási API-t. Emiatt előfordulhat, hogy váratlan hibák vagy viselkedés használata kor Azurite fejlesztési tároláshoz.
* Ritka esetekben előfordulhat, hogy a fa fókusza elakad a gyorselérésen. A fókusz leoldásához frissítheti az összeset.
* A OneDrive mappából való feltöltés nem működik a NodeJS hibája miatt. A hiba van rögzített, de még nem egységbe rendezett -ba Elektron.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ez azért van, mert az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt szűrőszűrési munkát használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is megjelenik egy csatolt Azure Stack tárfiók alatt.
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linux-felhasználók számára telepítenie kell a [.NET Core 2.0-t.](https://dotnet.microsoft.com/download/dotnet-core/2.0)
* Az Ubuntu 14.04 felhasználói számára biztosítania kell, hogy a GCC naprakész legyen - ezt a következő parancsok futtatásával teheti meg, majd újra kell indítania a gépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Az Ubuntu 17.04 felhasználói számára telepítenie kell a GConf-ot - ezt a következő parancsok futtatásával teheti meg, majd újrakell indítania a gépet:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>1.0.0-s verzió
04/16/2018

### <a name="new"></a>Új
* Továbbfejlesztett hitelesítés, amely lehetővé teszi, hogy a Storage Explorer ugyanazt a fióktárolót használja, mint a Visual Studio 2017. A funkció használatához újra be kell jelentkeznie a fiókokba, és újra be kell állítania a szűrt előfizetéseket.
* Az AAD által támogatott Azure Stack-fiókok esetében a Storage Explorer mostantól lekéri az Azure Stack-előfizetéseket, ha a "Cél Azure Stack" engedélyezve van. Már nem kell egyéni bejelentkezési környezetet létrehoznia.
* A gyorsabb navigáció érdekében számos parancsikont adtak hozzá. Ezek közé tartozik a különböző panelek váltása és a szerkesztők közötti mozgás. További részleteket a Nézet menüben talál.
* A Storage Explorer visszajelzései mostantól a GitHubon maradnak. A problémák oldalt a bal alsó sarokban található Visszajelzés gombra kattintva érheti el, vagy a lehetőségre [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues)kattintva. Nyugodtan tegyen javaslatokat, jelentse a problémákat, tegyen fel kérdéseket, vagy hagyjon bármilyen más visszajelzést.
* Ha tls/SSL-tanúsítvánnyal kapcsolatos problémákba ütközik, és nem találja a jogsértő tanúsítványt, `--ignore-certificate-errors` most már elindíthatja a Storage Explorer t a parancssorból a jelzővel. Ha ezzel a jelzővel indítja el, a Storage Explorer figyelmen kívül hagyja a TLS/SSL tanúsítványhibákat.
* Most már van egy "Letöltés" lehetőség a helyi menüben a blob és a fájl elemeket.
* Továbbfejlesztett kisegítő lehetőségek és képernyőolvasó-támogatás. Ha a kisegítő lehetőségekre támaszkodik, további információt a [kisegítő lehetőségekről szóló dokumentációban](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-accessibility) talál.
* A Storage Explorer mostantól az Electron 1.8.3-at használja

### <a name="breaking-changes"></a>Kompatibilitástörő változások
* A Storage Explorer új hitelesítési könyvtárra váltott. A könyvtárra való váltás részeként újra be kell jelentkeznie a fiókokba, és újra be kell állítania a szűrt előfizetéseket
* A bizalmas adatok titkosítására használt módszer megváltozott. Ez azt eredményezheti, hogy a gyorselérési elemek egy részét újra hozzá kell adni, és/vagy néhány hozzá csatlakoztatott erőforrást újra kell csatolni.

### <a name="fixes"></a>Javítások
* Egyes proxyk mögött álló felhasználók egy "Nem oldható meg" hibaüzenet teljében megszakított a csoportblob-feltöltéseket vagy -letöltéseket. A probléma ki lett javítva.
* Ha közvetlen hivatkozás használata közben bejelentkezésre volt szükség, a "Bejelentkezés" üzenetre kattintva megjelenik egy üres párbeszédpanel. A probléma ki lett javítva.
* Linuxon, ha a Storage Explorer nem tud elindulni egy GPU-folyamat összeomlása miatt, akkor most értesítést kap az összeomlásról, azt mondta, hogy használja a "--disable-gpu" kapcsolót, és a Storage Explorer automatikusan újraindul a kapcsoló engedélyezve.
* Az érvénytelen hozzáférési házirendeket nehéz volt azonosulni a Hozzáférési házirendek párbeszédpanelen. Az érvénytelen hozzáférési házirend-azonosítók mostantól piros sal vannak eltikva a jobb láthatóság érdekében.
* A tevékenységnapló néha nagy területű szóközökkel rendelkezik a tevékenység különböző részei között. A probléma ki lett javítva.
* Ha a táblalekérdezés-szerkesztőben érvénytelen állapotban hagyott egy időbélyeg-záradékot, majd egy másik záradékot próbált módosítani, a szerkesztő lefagy. A szerkesztő most visszaállítja az időbélyeg-záradékot az utolsó érvényes állapotába, amikor egy másik záradék módosítása észlel.
* Ha a keresési lekérdezés fanézetben történő beírása közben szünetelt, a keresés megkezdődik, és a fókusz tlopódisa lesz a szövegmezőből. Most explicit módon meg kell kezdenie a keresést az "Enter" gomb megnyomásával, vagy a keresés indítása gombra kattintva.
* A "Közös hozzáférésű aláírás betöltése" parancs néha le van tiltva, ha a fájlmegosztásban lévő fájlra kattint. A probléma ki lett javítva.
* Ha a keresés során kiszűrte a fókuszú erőforrásfa csomópontot, akkor nem tudott az erőforrásfába lapozni, és a nyílbillentyűkkel navigálhat az erőforrásfán. Most, ha a fókuszált erőforrásfa csomópont rejtett, az erőforrásfa első csomópontja automatikusan fókuszálódik.
* Egy extra elválasztó néha látható a szerkesztő eszköztáron. A probléma ki lett javítva.
* A zsemlemorzsa szövegdoboz néha túlcsordul. A probléma ki lett javítva.
* A Blob és a Fájlmegosztás szerkesztők néha folyamatosan frissíti, amikor egyszerre sok fájlt tölt fel. A probléma ki lett javítva.
* A "Mappastatisztika" szolgáltatásnak nem volt célja a Fájlmegosztás pillanatképeinek kezelése nézetben. Most már le van tiltva.
* Linux on a Fájl menü nem jelent meg. A probléma ki lett javítva.
* Amikor egy mappát fájlmegosztásra tölt fel, alapértelmezés szerint csak a mappa tartalma volt feltöltve. Most az alapértelmezett viselkedés az, hogy feltölti a mappa tartalmát a Fájlmegosztás egyező mappájába.
* A gombok sorrendje több párbeszédablakban megfordult. A probléma ki lett javítva.
* Különböző biztonsági kapcsolatos javítások.

### <a name="known-issues"></a>Ismert problémák
* Ritka esetekben előfordulhat, hogy a fa fókusza elakad a gyorselérésen. A fókusz leoldásához frissítheti az összeset.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ennek az az oka, hogy az itt leírt törlési szűrőt használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is megjelenik egy csatolt Azure Stack tárfiók alatt.
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* A Linux-felhasználók számára telepítenie kell a [.NET Core 2.0-t.](https://dotnet.microsoft.com/download/dotnet-core/2.0)
* Az Ubuntu 14.04 felhasználói számára biztosítania kell, hogy a GCC naprakész legyen - ezt a következő parancsok futtatásával teheti meg, majd újra kell indítania a gépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Az Ubuntu 17.04 felhasználói számára telepítenie kell a GConf-ot - ezt a következő parancsok futtatásával teheti meg, majd újrakell indítania a gépet:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>0.9.6-os verzió
02/28/2018

### <a name="fixes"></a>Javítások
* Egy probléma megakadályozta, hogy a várt blobok/fájlok megjelenjenek a szerkesztőben. A probléma ki lett javítva.
* Egy probléma miatt a pillanatképnézetek közötti váltás helytelenül jeleníti meg az elemeket. A probléma ki lett javítva.

### <a name="known-issues"></a>Ismert problémák
* A Storage Explorer nem támogatja az ADFS-fiókokat.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ez azért van, mert az [itt](https://github.com/Azure/azure-storage-node/issues/317)leírt szűrőszűrési munkát használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* A fiókbeállítások panelen megjelenhet, hogy újra meg kell adnia a hitelesítő adatokat az előfizetések szűréséhez.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is megjelenik egy csatolt Azure Stack tárfiók alatt.
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Az Ubuntu 14.04 felhasználói számára biztosítania kell, hogy a GCC naprakész legyen - ezt a következő parancsok futtatásával teheti meg, majd újra kell indítania a gépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Az Ubuntu 17.04 felhasználói számára telepítenie kell a GConf-ot - ezt a következő parancsok futtatásával teheti meg, majd újrakell indítania a gépet:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>0.9.5-ös verzió
02/06/2018

### <a name="new"></a>Új

* Fájlmegosztási pillanatképek támogatása:
    * Pillanatképek létrehozása és kezelése a fájlmegosztásokhoz.
    * A katakítás közben könnyedén válthat a fájlmegosztások pillanatképei között.
    * A fájlok korábbi verzióinak visszaállítása.
* Az Azure Data Lake Store előzetes verziójú támogatása:
    * Csatlakozzon az ADLS-erőforrásokhoz több fiókban.
    * Csatlakozzon és osszon meg ADLS-erőforrásokat ADL URI-k használatával.
    * Az alapvető fájl-/mappaműveletek rekurzív végrehajtása.
    * Rögzítse az egyes mappákat a Gyorselérés funkcióhoz.
    * Mappastatisztikák megjelenítése.

### <a name="fixes"></a>Javítások
* Indítási teljesítmény javítása.
* Különböző hibajavítások.

### <a name="known-issues"></a>Ismert problémák
* A Storage Explorer nem támogatja az ADFS-fiókokat.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ennek az az oka, hogy az itt leírt törlési szűrőt használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* A fiókbeállítások panelen megjelenhet, hogy újra meg kell adnia a hitelesítő adatokat az előfizetések szűréséhez.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is megjelenik egy csatolt Azure Stack tárfiók alatt.
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Az Ubuntu 14.04 felhasználói számára biztosítania kell, hogy a GCC naprakész legyen - ezt a következő parancsok futtatásával teheti meg, majd újra kell indítania a gépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Az Ubuntu 17.04 felhasználói számára telepítenie kell a GConf-ot - ezt a következő parancsok futtatásával teheti meg, majd újrakell indítania a gépet:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>0.9.4-es és 0.9.3-as verzió
01/21/2018

### <a name="new"></a>Új
* A meglévő Storage Explorer ablak újra használatba kerül, ha:
    * A Storage Explorer ben létrehozott közvetlen kapcsolatok megnyitása.
    * A Tárolókezelő megnyitása a portálról.
    * A Storage Explorer megnyitása az Azure Storage VS Code bővítményből (hamarosan).
* Hozzáadott képes megnyitni egy új Storage Explorer ablak belül Storage Explorer.
    * A Windows rendszerben a Fájl menü és a tálcán található helyi menüben található egy "Új ablak" lehetőség.
    * Mac esetén az Alkalmazásmenü ben található egy "Új ablak" lehetőség.

### <a name="fixes"></a>Javítások
* Kijavítottunk egy biztonsági problémát. Kérjük, frissítsen 0.9.4-re a lehető leghamarabb.
* A régi tevékenységeket nem megfelelően takarították fel. Ez hatással volt a hosszú ideig futó feladatok teljesítményére. Most már helyesen tisztítják őket.
* A nagy számú fájlt és könyvtárat érintő műveletek esetenként a Storage Explorer lefagyását eredményeznék. Az Azure fájlmegosztások most antól szabályozva, hogy korlátozza a rendszer erőforrás-használat.

### <a name="known-issues"></a>Ismert problémák
* A Storage Explorer nem támogatja az ADFS-fiókokat.
* A "View Explorer" és a "View Account Management" billentyűparancsainak a Ctrl/Cmd+Shift+E, illetve a Ctrl/Cmd+Shift+A billentyűparancsnak kell lenniük.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ennek az az oka, hogy az itt leírt törlési szűrőt használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* A fiókbeállítások panelen megjelenhet, hogy újra meg kell adnia a hitelesítő adatokat az előfizetések szűréséhez.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is megjelenik egy csatolt Azure Stack tárfiók alatt.
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Az Ubuntu 14.04 felhasználói számára biztosítania kell, hogy a GCC naprakész legyen - ezt a következő parancsok futtatásával teheti meg, majd újra kell indítania a gépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Az Ubuntu 17.04 felhasználói számára telepítenie kell a GConf-ot - ezt a következő parancsok futtatásával teheti meg, majd újrakell indítania a gépet:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>0.9.2-es verzió
11/01/2017

### <a name="hotfixes"></a>Gyorsjavítások
* A helyi időzónától függően nem várt adatmódosítások rakhatók le a táblaentitások Edm.DateTime értékeinek szerkesztésekor. A szerkesztő mostantól egy egyszerű szövegdobozt használ, amely pontos, egységes ellenőrzést biztosít az Edm.DateTime értékek felett.
* A blobok egy csoportjának feltöltése/letöltése, ha névvel és kulccsal van csatolva, nem indul el. A probléma ki lett javítva.
* Korábban a Storage Explorer csak akkor kéri az elavult fiók újbóli hitelesítését, ha a fiók egy vagy több előfizetése van kiválasztva. Most a Storage Explorer akkor is megkérdezi, ha a fiók teljesen ki van szűrve.
* Az Azure US Government végponttartománya hibás volt. Megjavították.
* A Fiókok kezelése panelen lévő Apply gombra néha nehéz volt kattintani. Ennek nem szabadna megtörténnie.

### <a name="new"></a>Új
* Előzetes verzió támogatása az Azure Cosmos DB-hez:
    * [Online dokumentáció](./cosmos-db/storage-explorer.md)
    * Adatbázisok és gyűjtemények létrehozása
    * Adatok módosítása
    * Dokumentumok lekérdezése, létrehozása és törlése
    * Tárolt eljárások, felhasználó által definiált függvények vagy eseményindítók frissítése
    * Kapcsolati karakterláncok használata adatbázisokhoz való csatlakozáshoz és -kezeléshez
* Számos kis blob feltöltésének/letöltésének teljesítményén javult.
* Hozzáadott egy "Újra az összes" műveletet, ha hibák vannak egy blob feltöltési csoportban vagy blob letöltési csoportban.
* A Storage Explorer mostantól szünetelteti az ismétlést a blob feltöltése/letöltése során, ha azt észleli, hogy a hálózati kapcsolat megszakadt. Ezután folytathatja az ismétlést, miután a hálózati kapcsolat helyreállt.
* A helyi menün keresztül hozzáadva a "Minden bezárása", a "Mások bezárása" és a "Bezárás" lapok lehetőségét.
* A Storage Explorer mostantól natív párbeszédeket és natív helyi menüket használ.
* A Storage Explorer mostantól könnyebben elérhető. A fejlesztések a következők:
    * Továbbfejlesztett képernyőolvasó-támogatás, NVDA Windows rendszeren és VoiceOver Mac-en
    * Továbbfejlesztett kontrasztos téma
    * A billentyűzet be- és billentyűzetfókusz-javításai

### <a name="fixes"></a>Javítások
* Ha érvénytelen Windows-fájlnévvel rendelkező blobot próbált megnyitni vagy letölteni, a művelet sikertelen lesz. A Storage Explorer mostantól észleli, ha egy blob neve érvénytelen, és megkérdezi, hogy szeretné-e kódolni, vagy kihagyni a blobot. A Storage Explorer azt is észleli, hogy a fájlnév kódolva jelenik-e meg, és megkérdezi, hogy szeretné-e dekódolni a feltöltés előtt.
* Blob feltöltése során a célblob-tároló szerkesztője néha nem megfelelően frissül. A probléma ki lett javítva.
* A kapcsolati karakterláncok és a SAS URI-k több formájának támogatása visszaesett. Minden ismert problémát kikezeltünk, de kérjük, küldjön visszajelzést, ha további problémákba ütközik.
* A frissítési értesítés megszakadt néhány felhasználó számára a 0.9.0-s területen. Ezt a problémát kijavították, és a hiba által érintettek számára manuálisan letöltheti a Storage Explorer legújabb verzióját [itt](https://azure.microsoft.com/features/storage-explorer/).

### <a name="known-issues"></a>Ismert problémák
* A Storage Explorer nem támogatja az ADFS-fiókokat.
* A "View Explorer" és a "View Account Management" billentyűparancsainak a Ctrl/Cmd+Shift+E, illetve a Ctrl/Cmd+Shift+A billentyűparancsnak kell lenniük.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ennek az az oka, hogy az itt leírt törlési szűrőt használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* A fiókbeállítások panelen megjelenhet, hogy újra meg kell adnia a hitelesítő adatokat az előfizetések szűréséhez.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is megjelenik egy csatolt Azure Stack tárfiók alatt.
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Az Ubuntu 14.04 felhasználói számára biztosítania kell, hogy a GCC naprakész legyen - ezt a következő parancsok futtatásával teheti meg, majd újra kell indítania a gépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Az Ubuntu 17.04 felhasználói számára telepítenie kell a GConf-ot - ezt a következő parancsok futtatásával teheti meg, majd újrakell indítania a gépet:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>0.9.1-es és 0.9.0-s verzió
10/20/2017
### <a name="new"></a>Új
* Előzetes verzió támogatása az Azure Cosmos DB-hez:
    * [Online dokumentáció](./cosmos-db/storage-explorer.md)
    * Adatbázisok és gyűjtemények létrehozása
    * Adatok módosítása
    * Dokumentumok lekérdezése, létrehozása és törlése
    * Tárolt eljárások, felhasználó által definiált függvények vagy eseményindítók frissítése
    * Kapcsolati karakterláncok használata adatbázisokhoz való csatlakozáshoz és -kezeléshez
* Számos kis blob feltöltésének/letöltésének teljesítményén javult.
* Hozzáadott egy "Újra az összes" műveletet, ha hibák vannak egy blob feltöltési csoportban vagy blob letöltési csoportban.
* A Storage Explorer mostantól szünetelteti az ismétlést a blob feltöltése/letöltése során, ha azt észleli, hogy a hálózati kapcsolat megszakadt. Ezután folytathatja az ismétlést, miután a hálózati kapcsolat helyreállt.
* A helyi menün keresztül hozzáadva a "Minden bezárása", a "Mások bezárása" és a "Bezárás" lapok lehetőségét.
* A Storage Explorer mostantól natív párbeszédeket és natív helyi menüket használ.
* A Storage Explorer mostantól könnyebben elérhető. A fejlesztések a következők:
    * Továbbfejlesztett képernyőolvasó-támogatás, NVDA Windows rendszeren és VoiceOver Mac-en
    * Továbbfejlesztett kontrasztos téma
    * A billentyűzet be- és billentyűzetfókusz-javításai

### <a name="fixes"></a>Javítások
* Ha érvénytelen Windows-fájlnévvel rendelkező blobot próbált megnyitni vagy letölteni, a művelet sikertelen lesz. A Storage Explorer mostantól észleli, ha egy blob neve érvénytelen, és megkérdezi, hogy szeretné-e kódolni, vagy kihagyni a blobot. A Storage Explorer azt is észleli, hogy a fájlnév kódolva jelenik-e meg, és megkérdezi, hogy szeretné-e dekódolni a feltöltés előtt.
* Blob feltöltése során a célblob-tároló szerkesztője néha nem megfelelően frissül. A probléma ki lett javítva.
* A kapcsolati karakterláncok és a SAS URI-k több formájának támogatása visszaesett. Minden ismert problémát kikezeltünk, de kérjük, küldjön visszajelzést, ha további problémákba ütközik.
* A frissítési értesítés megszakadt néhány felhasználó számára a 0.9.0-s területen. Ezt a problémát kijavítottuk, és a hiba által érintettek számára manuálisan letöltheti a Storage Explorer legújabb verzióját [itt](https://azure.microsoft.com/features/storage-explorer/)

### <a name="known-issues"></a>Ismert problémák
* A Storage Explorer nem támogatja az ADFS-fiókokat.
* A "View Explorer" és a "View Account Management" billentyűparancsainak a Ctrl/Cmd+Shift+E, illetve a Ctrl/Cmd+Shift+A billentyűparancsnak kell lenniük.
* Az Azure Stack célzásakor bizonyos fájlok hozzáfűző blobok feltöltése sikertelen lehet.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ennek az az oka, hogy az itt leírt törlési szűrőt használjuk.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* A fiókbeállítások panelen megjelenhet, hogy újra meg kell adnia a hitelesítő adatokat az előfizetések szűréséhez.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is megjelenik egy csatolt Azure Stack tárfiók alatt.
* A Storage Explorer által használt Electron héj nak problémája van néhány GPU (grafikus feldolgozó egység) hardveres gyorsítással. Ha a Storage Explorer üres (üres) főablakot jelenít meg, a kapcsoló hozzáadásával megpróbálhatja elindítani a Storage Explorert a parancssorból, és letiltani a `--disable-gpu` GPU-gyorsítást:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Az Ubuntu 14.04 felhasználói számára biztosítania kell, hogy a GCC naprakész legyen - ezt a következő parancsok futtatásával teheti meg, majd újra kell indítania a gépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Az Ubuntu 17.04 felhasználói számára telepítenie kell a GConf-ot - ezt a következő parancsok futtatásával teheti meg, majd újrakell indítania a gépet:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>0.8.16-os verzió
8/21/2017

### <a name="new"></a>Új
* Blob megnyitásakor a Storage Explorer kérni fogja a letöltött fájl feltöltését, ha változást észlel
* Továbbfejlesztett Azure Stack bejelentkezési élmény
* Több kis fájl egyidejű feltöltésének/letöltésének teljesítményének javítása


### <a name="fixes"></a>Javítások
* Egyes blobtípusok esetén a feltöltési ütközések során a "csere" kiválasztása néha a feltöltés újraindítását eredményezi.
* A 0.8.15-ös verzióban a feltöltések néha 99%-on állnak.
* Amikor fájlokat tölt fel egy fájlmegosztásba, ha úgy dönt, hogy feltölt egy olyan könyvtárba, amely még nem létezett, a feltöltés sikertelen lesz.
* A Storage Explorer helytelenül generált időbélyegzőket a megosztott hozzáférésű aláírásokhoz és a táblalekérdezésekhez.


### <a name="known-issues"></a>Ismert problémák
* A név és a kulcskapcsolati karakterlánc használata jelenleg nem működik. A következő kiadásban lesz rögzítve. Addig használhatja csatolja a nevét és kulcsát.
* Ha érvénytelen Windows-fájlnévvel rendelkező fájlt próbál megnyitni, a letöltés hibát eredményez.
* Miután egy feladat "Mégse" gombbal kattintott, eltarthat egy ideig, amíg a feladat megszakad. Ez az Azure Storage-csomópontkönyvtár korlátozása.
* A blob feltöltése befejezése után a feltöltést kezdeményező lap frissül. Ez a korábbi viselkedéshez képest változás, és azt is eredményezi, hogy visszakerül a tároló gyökerébe, amelyben van.
* Ha nem a megfelelő PIN/Smartcard tanúsítványt választja, akkor újra kell indítania, hogy a Storage Explorer elfelejtse ezt a döntést.
* A fiókbeállítások panelen megjelenhet, hogy újra meg kell adnia a hitelesítő adatokat az előfizetések szűréséhez.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is megjelenik egy csatolt Azure Stack tárfiók alatt.
* Az Ubuntu 14.04 felhasználói számára biztosítania kell, hogy a GCC naprakész legyen - ezt a következő parancsok futtatásával teheti meg, majd újra kell indítania a gépet:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Az Ubuntu 17.04 felhasználói számára telepítenie kell a GConf-ot - ezt a következő parancsok futtatásával teheti meg, majd újrakell indítania a gépet:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>0.8.14-es verzió
06/22/2017

### <a name="new"></a>Új

* Frissített Electron verzió 1.7.2 annak érdekében, hogy kihasználják a több kritikus biztonsági frissítések
* Most már gyorsan elérheti az online hibaelhárítási útmutatót a súgó menüből
* Útmutató [a][2] Tároláskezelő hibaelhárításához
* [Az][3] Azure Stack-előfizetéshez való csatlakozásra vonatkozó utasítások

### <a name="known-issues"></a>Ismert problémák

* A törlési mappa megerősítő párbeszédpanelén lévő gombok nem regisztrálnak a Linuxon lévő egérkattintásokkal. a munka körül, hogy használja az Enter gombot
* Ha rossz PIN/Smartcard tanúsítványt választ, akkor újra kell indítania, hogy a Storage Explorer elfelejtse a döntést
* Ha egyszerre több mint 3 blobcsoport vagy fájl feltöltése van, az hibákat okozhat
* A fiókbeállítások panelen megjelenhet, hogy újra meg kell adnia a hitelesítő adatokat az előfizetések szűréséhez.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is megjelenik egy csatolt Azure Stack tárfiók alatt.
* Ubuntu 14.04 telepítés szükséges gcc verzió frissítve vagy frissítve - lépéseket frissíteni az alábbiakban:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>0.8.13-as verzió
05/12/2017

#### <a name="new"></a>Új

* Útmutató [a][2] Tároláskezelő hibaelhárításához
* [Az][3] Azure Stack-előfizetéshez való csatlakozásra vonatkozó utasítások

#### <a name="fixes"></a>Javítások

* Javítva: A fájlfeltöltésnek nagy esélye volt arra, hogy memóriazavart okozzon
* Javítva: Most már betud jelentkezni PIN/Smartcard kártyával
* Javítva: Az Open in Portal mostantól együttműködik az Azure China 21Vianet, az Azure Germany, az Azure US Government és az Azure Stack használatával
* Javítva: Egy mappa blobtárolóba való feltöltése közben néha "Érvénytelen művelet" hiba lép fel
* Javítva: Az összes kijelölése le lett tiltva a pillanatképek kezelése közben
* Javítva: Az alapblob metaadatai felülíródhatnak a pillanatképek tulajdonságainak megtekintése után

#### <a name="known-issues"></a>Ismert problémák

* Ha rossz PIN/Smartcard tanúsítványt választ, akkor újra kell indítania, hogy a Storage Explorer elfelejtse a döntést
* Nagyítás vagy kicsinyítés közben a nagyítási szint egy pillanatra visszaállhat az alapértelmezett szintre
* Ha egyszerre több mint 3 blobcsoport vagy fájl feltöltése van, az hibákat okozhat
* A fiókbeállítások panelen megjelenhet, hogy újra meg kell adnia a hitelesítő adatokat az előfizetések szűréséhez.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is megjelenik egy csatolt Azure Stack tárfiók alatt.
* Ubuntu 14.04 telepítés szükséges gcc verzió frissítve vagy frissítve - lépéseket frissíteni az alábbiakban:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>0.8.12-es és 0.8.11-es és 0.8.10-es verzió
04/07/2017

#### <a name="new"></a>Új

* A Storage Explorer automatikusan bezáródik, amikor frissítést telepít a frissítési értesítésből
* A helyben elérhető gyors hozzáférés továbbfejlesztett élményt nyújt a gyakran használt erőforrásokkal való munkához
* A Blob Container szerkesztőben most már láthatja, hogy egy bérelt blob melyik virtuális géphez tartozik
* Most már összecsukhatja a bal oldali panelt
* A felderítés most a letöltéssel egy időben fut
* A Blob Container, a Fájlmegosztás és a Táblázatszerkesztő statisztikáinak használatával megtekintheti az erőforrás vagy a kijelölés méretét
* Most már bejelentkezhet az Azure Active Directory (AAD) alapú Azure Stack-fiókokba.
* Most már 32 MB-on keresztül is feltöltheti az archív fájlokat a Prémium tárfiókokba
* Továbbfejlesztett kisegítő lehetőségek támogatása
* Most már hozzáadhat megbízható Base-64 kódolású X.509 TLS/SSL tanúsítványokat a Szerkesztés&gt; - SSL tanúsítványok -&gt; Tanúsítványok importálása

#### <a name="fixes"></a>Javítások

* Javítva: a fiók hitelesítő adatainak frissítése után a fanézet néha nem frissül automatikusan
* Javítva: az emulátorvárólisták és táblák SAS-ének létrehozása érvénytelen URL-címet eredményezne
* Javítva: a prémium szintű tárfiókok mostantól kibonthatók, miközben a proxy engedélyezve van
* Javítva: az apply gomb a fiókkezelési oldalon nem működne, ha 1 vagy 0 fiók van kiválasztva
* Javítva: az ütközésfeloldást igénylő blobok feltöltése sikertelen lehet - 0.8.11-ben javítva
* Javítva: a visszajelzés küldése 0.8.11-ben megszakadt - 0.8.12-ben rögzítve

#### <a name="known-issues"></a>Ismert problémák

* A 0.8.10-re való frissítés után frissítenie kell az összes hitelesítő adatot.
* Nagyítás vagy kicsinyítés közben a nagyítási szint egy pillanatra visszaállhat az alapértelmezett szintre.
* Ha egyszerre több mint 3 blobcsoportot vagy fájlt tölt fel, az hibákat okozhat.
* A fiókbeállítások panelen megjelenhet, hogy újra meg kell adnia a hitelesítő adatokat az előfizetések szűréséhez.
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások minden egyéb tulajdonsága és metaadata megmarad az átnevezés során.
* Bár az Azure Stack jelenleg nem támogatja a fájlmegosztások, a fájlmegosztások csomópont továbbra is megjelenik egy csatolt Azure Stack tárfiók alatt.
* Ubuntu 14.04 telepítés szükséges gcc verzió frissítve vagy frissítve - lépéseket frissíteni az alábbiakban:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>0.8.9-es és 0.8.8-as verzió
02/23/2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>Új

* A Storage Explorer 0.8.9 automatikusan letölti a legújabb verziót a frissítésekhez.
* Gyorsjavítás: egy portál által generált SAS URI-tárfiók csatolásához hibát eredményez.
* Most már létrehozhat, kezelhet és előléptethet blob-pillanatképeket.
* Most már bejelentkezhet az Azure China 21Vianet, az Azure Germany és az Azure US Government-fiókokba.
* Most már módosíthatja a nagyítási szintet. A Nézet menü beállításaival nagyíthat, kicsinyíthet és visszaállíthatja a nagyítást.
* A Unicode karakterek et a blobok és fájlok felhasználói metaadatai is támogatják.
* Hozzáférhetőség javítása.
* A következő verzió kiadási jegyzetei megtekinthetők a frissítési értesítésből. Az aktuális kibocsátási megjegyzéseket a Súgó menüből is megtekintheti.

#### <a name="fixes"></a>Javítások

* Javítva: a verziószám most már helyesen jelenik meg a Windows Vezérlőpultján
* Rögzített: keresés már nem korlátozódik 50.000 csomópontok
* Rögzített: feltölteni egy fájlmegosztásra megpördült örökre, ha a cél könyvtár nem létezik már létezik
* Rögzített: jobb stabilitás a hosszú feltöltések és letöltések

#### <a name="known-issues"></a>Ismert problémák

* Nagyítás vagy kicsinyítés közben a nagyítási szint egy pillanatra visszaállhat az alapértelmezett szintre.
* A Gyorselérés csak előfizetésalapú elemekkel működik. A kulcson vagy sas-jogkivonaton keresztül csatlakoztatott helyi erőforrások at vagy SAS-jogkivonatot ez a kiadás nem támogatja.
* A gyors elérésnek néhány másodpercig is eltarthat, amíg a célerőforráshoz navigál, attól függően, hogy hány erőforrással rendelkezik.
* Ha egyszerre több mint 3 blobcsoportot vagy fájlt tölt fel, az hibákat okozhat.

12/16/2016
### <a name="version-087"></a>0.8.7-es verzió

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Új

* Az ütközések feloldásának módját a frissítések elején, a Tevékenységek ablakban töltheti le vagy másolhatja a munkamenetet.
* Mutasson egy lapra a tárolási erőforrás teljes elérési útjának megtekintéséhez
* Amikor rákattint egy fülre, szinkronizálja a helyét a bal oldali navigációs ablakban

#### <a name="fixes"></a>Javítások

* Javítva: A Storage Explorer mostantól megbízható alkalmazás Macen
* Javítva: Ubuntu 14.04 ismét támogatott
* Javítva: Néha villog az add account ui előfizetések betöltésekor
* Javítva: Néha nem minden tárolási erőforrás szerepel a bal oldali navigációs ablakban
* Javítva: A műveletpanelen néha üres műveletek jelennek meg
* Javítva: Az utolsó zárt munkamenet ablakmérete megmarad
* Javítva: Ugyanahhoz az erőforráshoz több lapot is megnyithat a helyi menüvel

#### <a name="known-issues"></a>Ismert problémák

* A Gyorselérés csak előfizetésalapú elemekkel működik. A kulcson vagy SAS-jogkivonaton keresztül csatolt helyi erőforrásokat vagy erőforrásokat ez a kiadás nem támogatja.
* A gyors elérésnéhány másodpercet vehet igénybe a célerőforráshoz való navigáláshoz, attól függően, hogy hány erőforrással rendelkezik
* Ha egyszerre több mint 3 blobcsoport vagy fájl feltöltése van, az hibákat okozhat
* Körülbelül 50 000 csomópontot kereső keresési fogópontok – ezt követően a teljesítmény hatással lehet a teljesítményre, vagy nem kezelt kivételt okozhat
* A macOS Storage Explorer használatával először több kérdés is megjelenhet, amelyek a felhasználó engedélyét kérik a kulcskarika eléréséhez. Javasoljuk, hogy válassza a Mindig engedélyezés lehetőséget, hogy a kérdés ne jelenjen meg újra

11/18/2016
### <a name="version-086"></a>0.8.6-os verzió

#### <a name="new"></a>Új

* Most már rögzítheti a leggyakrabban használt szolgáltatásokat a Gyors elérés hez az egyszerű
* Most már több szerkesztőt is megnyithat különböző lapokon. Egyetlen kattintással megnyithat egy ideiglenes lapot; dupla kattintással megnyithat egy állandó lapot. Azt is kattintson az ideiglenes lapon, hogy ez egy állandó lapot
* A feltöltések és a letöltések esetében észrevehető teljesítmény- és stabilitási fejlesztéseket hajtottunk végre, különösen a gyors gépeken lévő nagy fájlok esetében
* Üres "virtuális" mappák most már létre blob konténerek
* Az új továbbfejlesztett karakterlánc-részhalmazoskereséssel újra bevezettük a hatókörrel végzett keresést, így most két lehetősége van a keresésre:
    * Globális keresés - csak írjon be egy keresési kifejezést a keresőmezőbe
    * Hatóköralapú keresés – kattintson a csomópont melletti nagyító ikonra, majd adjon hozzá egy keresési kifejezést az elérési út végéhez, vagy kattintson a jobb gombbal, és válassza a "Keresés innen" parancsot.
* Van hozzá különböző témák: Világos (alapértelmezett), Sötét, Nagy kontrasztú fekete, és a Kontrasztos fehér. Ugrás a Szerkesztés&gt; - Témák a témabeállítás módosításához
* A Blob és a fájltulajdonságok módosíthatók
* Most már támogatja kódolt (base64) és unencoded várólista üzenetek
* Linuxon most egy 64 bites operációs rendszer re van szükség. Ebben a kiadásban csak a 64 bites Ubuntu 16.04.1 LTS-t támogatjuk
* Frissítettük a logónkat!

#### <a name="fixes"></a>Javítások

* Javítva: Képernyő fagyasztási problémák
* Javítva: Fokozott biztonság
* Javítva: Néha ismétlődő csatolt fiókok jelenhetnek meg
* Javítva: Egy nem definiált tartalomtípussal rendelkező blob kivételt hozhat létre
* Javítva: A lekérdezéspanel megnyitása üres táblán nem lehetséges
* Javítva: Változik a hibák a keresésben
* Rögzített: Nőtt a betöltve erőforrások száma 50-100 kattintva "Load More"
* Javítva: Első futtatáskor, ha egy fiók be van jelentkezve, alapértelmezés szerint kiválasztjuk az adott fiók összes előfizetését.

#### <a name="known-issues"></a>Ismert problémák

* Ez a kiadás a Storage Explorer nem fut ubuntu 14.04
* Ha több lapot szeretne megnyitni ugyanahhoz az erőforráshoz, ne kattintson folyamatosan ugyanarra az erőforrásra. Kattintson egy másik erőforrásra, majd menjen vissza, majd kattintson az eredeti erőforrásra, hogy újra megnyissa egy másik lapon
* A Gyorselérés csak előfizetésalapú elemekkel működik. A kulcson vagy SAS-jogkivonaton keresztül csatolt helyi erőforrásokat vagy erőforrásokat ez a kiadás nem támogatja.
* A gyors elérésnéhány másodpercet vehet igénybe a célerőforráshoz való navigáláshoz, attól függően, hogy hány erőforrással rendelkezik
* Ha egyszerre több mint 3 blobcsoport vagy fájl feltöltése van, az hibákat okozhat
* Körülbelül 50 000 csomópontot kereső keresési fogópontok – ezt követően a teljesítmény hatással lehet a teljesítményre, vagy nem kezelt kivételt okozhat

10/03/2016
### <a name="version-085"></a>0.8.5-ös verzió

#### <a name="new"></a>Új

* Most már használhatja a portál által generált SAS-kulcsokat a tárfiókokhoz és az erőforrásokhoz való csatoláshoz

#### <a name="fixes"></a>Javítások

* Javítva: a keresés során a versenyállapot néha a csomópontok nem bővíthetővé vált
* Javítva: A "HTTP használata" nem működik, ha fióknévvel és kulccsal rendelkező tárfiókokhoz csatlakozik
* Javítva: A SAS-kulcsok (különösen a portál által generáltak) "záró perjel" hibát adnak vissza
* Javítva: táblaimportálási problémák
    * Néha a partíciós kulcs és a sorkulcs megfordult
    * Nem olvasható a "null" partíciókulcsok

#### <a name="known-issues"></a>Ismert problémák

* Körülbelül 50 000 csomóponton keresőket kereső kereső kezelőkezelő fogantyúk – ezt követően a teljesítmény hatással lehet a
* Az Azure Stack jelenleg nem támogatja a Fájlok szolgáltatást, ezért a Fájlok kibontása hibaüzenetet jelenít meg

09/12/2016
### <a name="version-084"></a>0.8.4-es verzió

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Új

* Közvetlen hivatkozások létrehozása tárfiókokhoz, tárolókhoz, várólistákhoz, táblákhoz vagy fájlmegosztásokhoz az erőforrások megosztásához és az erőforrásokhoz való könnyű hozzáféréshez – Windows és Mac OS támogatás
* Blob-tárolók, táblák, várólisták, fájlmegosztások vagy tárfiókok keresése a keresőmezőből
* Most már csoportosíthatja a záradékokat a táblalekérdezés-szerkesztőben.
* Blobtárolók, fájlmegosztások, táblák, blobok, blobmappák, fájlok és könyvtárak átnevezése és másolása/beillesztése a SAS-hez csatlakoztatott fiókokból és tárolókból
* A blobtárolók és fájlmegosztások átnevezése és másolása mostantól megőrzi a tulajdonságokat és a metaadatokat

#### <a name="fixes"></a>Javítások

* Javítva: nem szerkeszthetők a táblaentitások, ha logikai vagy bináris tulajdonságokat tartalmaznak

#### <a name="known-issues"></a>Ismert problémák

* Körülbelül 50 000 csomóponton keresőket kereső kereső kezelőkezelő fogantyúk – ezt követően a teljesítmény hatással lehet a

08/03/2016
### <a name="version-083"></a>0.8.3-as verzió

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Új

* Tárolók, táblák, fájlmegosztások átnevezése
* Továbbfejlesztett Lekérdezésszerkesztő-élmény
* A lekérdezések mentése és betöltése
* Közvetlen hivatkozások tárfiókokhoz vagy tárolókhoz, várólistákhoz, táblákhoz vagy fájlmegosztásokhoz az erőforrások megosztásához és egyszerű eléréséhez (csak Windows - macOS támogatás hamarosan!)
* A CORS-szabályok kezelésének és konfigurálásának képessége

#### <a name="fixes"></a>Javítások

* Javítva: A Microsoft-fiókok 8-12 óránként újrahitelesítést igényelnek

#### <a name="known-issues"></a>Ismert problémák

* Néha a felhasználói felület lefagyottnak tűnhet - az ablak maximalizálása segít megoldani ezt a problémát
* A macOS telepítéséhez emelt szintű engedélyekre lehet szükség
* A Fiókbeállítások panelen megjelenhet, hogy újra meg kell adnia a hitelesítő adatokat az előfizetések szűréséhez
* A fájlmegosztások, a blobtárolók és a táblák átnevezése nem őrzi meg a tároló metaadatait vagy más tulajdonságait, például a fájlmegosztási kvótát, a nyilvános hozzáférési szintet vagy a hozzáférési házirendeket
* Blobok átnevezése (egyénileg vagy egy átnevezett blob tárolón belül) nem őrzi meg a pillanatképek. A blobok, fájlok és entitások összes többi tulajdonsága és metaadata megmarad az átnevezés során
* Az erőforrások másolása vagy átnevezése nem működik a SAS-hez csatlakoztatott fiókokban

07/07/2016
### <a name="version-082"></a>0.8.2-es verzió

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Új

* A tárfiókok előfizetések szerint vannak csoportosítva; a (helyi és csatolt) csomópontban a (helyi és csatolt) csomópontban, a (Helyi és csatolt)
* Kijelentkezés a fiókokból az "Azure-fiók beállításai" panelen
* Proxybeállítások konfigurálása a bejelentkezés engedélyezéséhez és kezeléséhez
* Blob-bérletek létrehozása és megszakítása
* Blob-tárolók, várólisták, táblák és fájlok megnyitása egyetlen kattintással

#### <a name="fixes"></a>Javítások

* Javítva: a .NET vagy Java tárakkal beszúrt várólista-üzenetek nincsenek megfelelően dekódolva a base64-ből
* Javítva: $metrics táblák nem jelennek meg a Blob Storage-fiókokban
* Javítva: a tables csomópont nem működik helyi (fejlesztési) tárolóhoz

#### <a name="known-issues"></a>Ismert problémák

* A macOS telepítéséhez emelt szintű engedélyekre lehet szükség

06/15/2016
### <a name="version-080"></a>0.8.0-s verzió

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Új

* Fájlmegosztás támogatása: megtekintés, feltöltés, letöltés, fájlok és könyvtárak másolása, SAS URI-k (létrehozás és csatlakozás)
* Továbbfejlesztett felhasználói élmény a SAS URI-kkal vagy fiókkulcsokkal rendelkező storage-hoz való csatlakozáshoz
* Táblalekérdezés eredményeinek exportálása
* Táblázatoszlop átrendezése és testreszabása
* $logs blobtárolók és $metrics táblák megtekintése az engedélyezett metrikákkal rendelkező tárfiókokhoz
* Továbbfejlesztett exportálási és importálási viselkedés, mostantól tartalmazza a tulajdonságérték típusát

#### <a name="fixes"></a>Javítások

* Javítva: nagy blobok feltöltése vagy letöltése hiányos feltöltéseket/letöltéseket eredményezhet
* Rögzített: numerikus karakterlánc-értékkel ("1") rendelkező entitás szerkesztése, hozzáadása vagy importálása
* Javítva: Nem lehet kibontani a táblacsomópontot a helyi fejlesztési környezetben

#### <a name="known-issues"></a>Ismert problémák

* $metrics táblák nem láthatók a Blob Storage-fiókokban
* Előfordulhat, hogy a programozott módon hozzáadott várólistaüzenetek nem jelennek meg megfelelően, ha az üzenetek base64 kódolással vannak kódolva

05/17/2016
### <a name="version-07201605090"></a>0.7.20160509.0-s verzió

#### <a name="new"></a>Új

* Jobb hibakezelés az alkalmazások összeomlásához

#### <a name="fixes"></a>Javítások

* Javítottuk azt a hibát, amely miatt az InfoBar-üzenetek néha nem jelennek meg, amikor bejelentkezési hitelesítő adatokra volt szükség

#### <a name="known-issues"></a>Ismert problémák

* Táblázatok: Olyan entitás hozzáadása, szerkesztése vagy importálása, amelynek egy tulajdonsága nem egyértelműen numerikus értékkel rendelkezik,például `Edm.String`"1" vagy "1.0", és a felhasználó megpróbálja elküldeni, az érték az ügyfél API-n keresztül Edm.Double-ként fog visszajönni az ügyfél API-n keresztül.

03/31/2016

### <a name="version-07201603250"></a>0.7.20160325.0-s verzió

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Új

* Táblatámogatás: entitások megtekintése, lekérdezése, exportálása, importálása és CRUD-műveletei
* Várólista-támogatás: üzenetek megtekintése, hozzáadása, várólistájának törlése
* SAS URI-k létrehozása tárfiókokhoz
* Csatlakozás sas-URI-kkal rendelkező tárfiókokhoz
* Értesítések frissítése a Storage Explorer jövőbeli frissítéseiről
* Frissített megjelenés és megtapintás

#### <a name="fixes"></a>Javítások

* Teljesítmény- és megbízhatósági fejlesztések

### <a name="known-issues-amp-mitigations"></a>Ismert &amp; problémák mérséklése

* A nagy méretű blobfájlok letöltése nem működik megfelelően - javasoljuk az AzCopy használatát a probléma megoldása közben
* A fiók hitelesítő adatai nem lesznek beolvasva és gyorsítótárazva, ha a kezdőmappa nem található, vagy nem írható
* Ha olyan entitást veszünk fel, szerkesztünk vagy importálunk, amelynek egy tulajdonsága nem egyértelműen numerikus értékkel rendelkezik, `Edm.String`például "1" vagy "1.0", és a felhasználó megpróbálja elküldeni, az érték az ügyfél API-n keresztül Edm.Double-ként fog visszajönni az ügyfél API-n keresztül.
* A többsoros rekordokkal rendelkező CSV-fájlok importálásakor az adatok aprítás vagy a kódolt

02/03/2016

### <a name="version-07201601291"></a>0.7.20160129.1 verzió

#### <a name="fixes"></a>Javítások

* A blobok feltöltése, letöltése és másolása során nyújtott általános teljesítmény javítása

01/14/2016

### <a name="version-07201601050"></a>0.7.20160105.0-s verzió

#### <a name="new"></a>Új

* Linux-támogatás (paritásfunkciók az OSX-hez)
* Blob-tárolók hozzáadása SAS-kulccsal
* Tárfiókok hozzáadása az Azure China 21Vianethez
* Tárfiókok hozzáadása egyéni végpontokkal
* A szöveg- és képblobok tartalmának megnyitása és megtekintése
* Blob-tulajdonságok és metaadatok megtekintése és szerkesztése

#### <a name="fixes"></a>Javítások

* Javítva: nagyszámú blob feltöltése vagy letöltése (500+) néha fehér képernyőt okozhat az alkalmazásnak
* Javítva: blob tároló nyilvános hozzáférési szint beállításakor az új érték nem frissül, amíg újra be nem állítja a fókuszt a tárolóra. Emellett a párbeszédpanel alapértelmezés szerint "Nincs nyilvános hozzáférés", és nem a tényleges aktuális érték.
* Jobb általános billentyűzet / hozzáférhetőség és a felhasználói felület támogatása
* A zsemlemorzsa előzményszövege immára tördelődik, ha hosszú, és szóközvan
* A SAS-párbeszédpanel támogatja a bemeneti ellenőrzést
* A helyi tárhely akkor is elérhető, ha a felhasználói hitelesítő adatok lejártak
* Egy megnyitott blobtároló törlésekor a jobb oldalon lévő blob-kezelő be zárul

#### <a name="known-issues"></a>Ismert problémák

* A Linux telepítéséhez frissíteni vagy frissíteni kell a gcc verziót – a frissítés lépései az alábbiak:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>20151116.0-s verzió

#### <a name="new"></a>Új

* macOS és Windows verziók
* Jelentkezzen be a tárfiókok megtekintéséhez – használja a szervezeti fiókot, a Microsoft-fiókot, a 2FA-t stb.
* Helyi fejlesztési tároló (tárolóemulátor használata, csak Windows)
* Az Azure Resource Manager és a klasszikus erőforrás-támogatás
* Blobok, várólisták és táblák létrehozása és törlése
* Adott blobok, várólisták és táblák keresése
* A blobtárolók tartalmának megismerése
* Könyvtárak megtekintése és navigálás
* Blobok és mappák feltöltése, letöltése és törlése
* Blob-tulajdonságok és metaadatok megtekintése és szerkesztése
* SAS-kulcsok létrehozása
* Tárolt hozzáférési házirendek kezelése és létrehozása (SAP)
* Blobok keresése előtag gal
* A feltöltéshez vagy letöltéshez húzza a fájlok at

#### <a name="known-issues"></a>Ismert problémák

* Blob container nyilvános hozzáférési szint beállításakor az új érték nem frissül, amíg újra be nem állítja a fókuszt a tárolóra
* Amikor megnyitja a párbeszédpanelt a nyilvános hozzáférési szint beállításához, mindig a "Nincs nyilvános hozzáférés" jelenik meg alapértelmezettként, nem pedig a tényleges aktuális érték.
* A letöltött blobok nem nevezhetők át
* A tevékenységnapló-bejegyzések néha "beragadnak" egy folyamatban lévő állapotban, amikor hiba történik, és a hiba nem jelenik meg
* Néha összeomlik, vagy teljesen fehérre vált, amikor nagyszámú blobot próbál feltölteni vagy letölteni
* Néha nem működik a másolási művelet megszakítása
* Tároló (blob/várólista/tábla) létrehozásakor, ha érvénytelen nevet ad meg, és egy másik atakat hoz létre egy másik tárolótípus alatt, akkor nem állíthatja be a fókuszt az új típusra.
* Nem lehet új mappát létrehozni vagy átnevezni a mappát




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
