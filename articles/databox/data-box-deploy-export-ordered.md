---
title: Oktatóanyag az adatok Azure Data Boxból való exportálásához | Microsoft Docs
description: Ismerje meg az üzembe helyezés előfeltételeit és az adatok exportálását Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 09/10/2020
ms.author: alkohli
ms.openlocfilehash: 5494c2dd57220888ad846aaf69fde2f7a59353e4
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2020
ms.locfileid: "90053054"
---
# <a name="tutorial-create-export-order-for-azure-data-box-preview"></a>Oktatóanyag: Azure Data Box exportálási sorrendjének létrehozása (előzetes verzió)

A Azure Data Box egy hibrid megoldás, amely lehetővé teszi az Azure-ba való adatáthelyezést a helyükre. Ez az oktatóanyag leírja, hogyan hozhat létre Azure Data Box exportálási sorrendjét. Az exportálási sorrend létrehozásának fő oka a vész-helyreállítás, abban az esetben, ha a helyszíni tárolás biztonságban van, és vissza kell állítani a biztonsági mentést.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Az Exportálás előfeltételei
> * Data Box megrendelése exportáláshoz
> * Az exportálási sorrend nyomon követése
> * Az exportálási sorrend megszakítása

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Előfeltételek

Az eszköz megrendelése előtt végezze el az alábbi konfigurációs előfeltételeket a Data Box szolgáltatáshoz és eszközhöz.

### <a name="for-service"></a>A szolgáltatás esetén

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* Győződjön meg arról, hogy rendelkezik egy meglévő erőforráscsoporthoz, amelyet a Azure Data Box használhat.

* Győződjön meg arról, hogy az Azure Storage-fiókja, amelyről exportálni kívánja az adatait, az egyik támogatott Storage-fióktípus a [Data Box támogatott Storage](data-box-system-requirements.md#supported-storage-accounts)-fiókokkal.

### <a name="for-device"></a>Az eszköz esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Rendelkezik egy, az adatközponti hálózatra csatlakoztatott gazdagéppel. A Azure Data Box adatok másolása erre a számítógépre történik. A gazdagépnek egy támogatott operációs rendszert kell futtatnia az [Azure Data Box rendszerkövetelményeinél](data-box-system-requirements.md) leírtaknak megfelelően.

* Az adatközpontnak nagy sebességű hálózattal kell rendelkeznie. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. Ha egy 10 GbE-kapcsolat nem érhető el, akkor a rendszer egy GbE adatkapcsolatot használ, de a másolási sebesség hatással van.

## <a name="order-data-box-for-export"></a>Data Box rendelése exportáláshoz

Az eszköz megrendeléséhez hajtsa végre a következő lépéseket az Azure Portalon.

1. Microsoft Azure hitelesítő adatai használatával jelentkezzen be ezen az URL-címen: [https://portal.azure.com](https://portal.azure.com).

2. Válassza az **+ Erőforrás létrehozása** lehetőséget, és keressen rá az *Azure Data Box* kifejezésre. Válassza az **Azure Data Box** lehetőséget.

   ![Erőforrás létrehozása](media/data-box-deploy-export-ordered/azure-data-box-export-00b.png)

3. Kattintson a **Létrehozás** gombra.

   ![Azure Data Box létrehozása](media/data-box-deploy-export-ordered/azure-data-box-export-00c.png)

4. Ellenőrizze, hogy a Azure Data Box szolgáltatás elérhető-e a régiójában. Adja meg vagy válassza ki a következő információkat, majd válassza az **Alkalmaz** lehetőséget.

    |Beállítás  |Érték  |
    |---------|---------|
    |Átvitel típusa     | Válassza **az Exportálás az Azure-ba**lehetőséget.        |
    |Előfizetés     | Válasszon egy EA-, CSP- vagy Azure Sponsorship-előfizetést a Data Box szolgáltatáshoz. <br> Az előfizetés az Ön számlázási fiókjához lesz társítva.       |
    |Erőforráscsoport     |    Válasszon ki egy meglévő erőforráscsoportot. <br> Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója.         |
    |Forrás Azure-régió    |    Válassza ki azt az Azure-régiót, ahol az adatai jelenleg vannak.         |
    |Rendeltetési ország     |     Válassza ki azt az országot, ahová az eszközt el szeretné szállítani.        |

   ![Válassza ki a Data Box beállításait](media/data-box-deploy-export-ordered/azure-data-box-export-01.png)

5. Válassza a **Data Box** lehetőséget. Egy megrendelés maximális felhasználható kapacitása 80 TB. Nagyobb mennyiségű adat esetén több rendelést is létrehozhat.

   ![Data Box kapacitás kiválasztása](media/data-box-deploy-export-ordered/azure-data-box-export-02b.png)

6. A **sorrend**mezőben határozza meg az **alapszintű** rendelés részleteit. Adja meg vagy válassza ki a következő információkat, majd válassza a **Tovább** lehetőséget.

    |Beállítás  |Érték  |
    |---------|---------|
    |Előfizetés     | Az előfizetés automatikusan kitöltődik a korábbi kiválasztás alapján.|
    |Erőforráscsoport | A korábban kiválasztott erőforráscsoport. |
    |Exportálási rendelés neve     |  Adjon meg egy rövid nevet a megrendelés nyomon követéséhez. <br> A névnek 3-24 karakter hosszúságúnak kell lennie, és csak betűket, számokat és kötőjelet tartalmazhat. <br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.      |

    ![Exportálási sorrend alapjai](media/data-box-deploy-export-ordered/azure-data-box-export-03.png)

    Kattintson a Tovább gombra: a folytatáshoz válassza ki az **adatkijelölést** .

7. Az **adatkijelölés**területen válassza a **Storage-fiók hozzáadása és az Exportálás típusa**lehetőséget.

    ![Storage-fiók és exportálási típus hozzáadása](media/data-box-deploy-export-ordered/azure-data-box-export-03b.png)

8. Az **Exportálás kiválasztása beállításnál**adja meg az exportálási beállítás részleteit. Adja meg vagy válassza ki a következő adatokat, majd válassza a **Hozzáadás**lehetőséget.

    |Beállítás  |Érték  |
    |---------|---------|
    |Tárfiók     | Az Azure Storage-fiók, ahonnan exportálni kívánja az adatait. |
    |Exportálás típusa     | Meghatározza az **összes objektumból** exportálandó adatok típusát és az **XML-fájl használatát**.<ul><li> **Minden objektum** – azt adja meg, hogy a feladatsor az **átvitel beállításaitól**függően az összes értéket exportálja.</li><li> **XML-fájl használata** – olyan XML-fájlt ad meg, amely a Storage-fiókból exportálandó blobok és/vagy fájlok elérési útját és előtagokat tartalmazza. Az XML-fájlnak a kiválasztott Storage-fiók tárolójában kell lennie, és a fájlmegosztás lehetőség választása jelenleg nem támogatott. A fájlnak nem üres. XML formátumú fájlnak kell lennie.</li></ul>        |
    |Átvitel beállításai     |  Meghatározza az adatátviteli beállításokat az **összes kijelölése**, **az összes blob**és az **összes fájl**közül. <ul><li> **Az összes kijelölése** – megadja, hogy a rendszer minden blobot és Azure-fájlt exportál. Ha olyan Storage-fiókot használ, amely csak a blobokat támogatja (Blob Storage fiók), a **minden fájl** lehetőség nem lesz kiválasztható.</li><li> **Minden blob** – azt adja meg, hogy csak a blokk-és a Blobok legyenek exportálva.</li><li> **Minden fájl** – azt adja meg, hogy minden fájl exportálva legyen, kivéve a blobokat. Az Ön által létrehozott Storage-fiók típusa (GPv1 és GPv2, Premium Storage vagy blob Storage) meghatározza az exportálható adattípusokat. További információ: [támogatott Storage-fiókok exportáláshoz](../storage/common/storage-import-export-requirements.md#supported-storage-types).</li></ul>         |
    |Részletes napló belefoglalása     | Azt jelzi, hogy szeretne-e részletes naplófájlt használni, amely tartalmazza az összes sikeresen exportált fájl listáját.        |

    > [!NOTE]
    >
    > Ha a **XML-fájl használata** lehetőséget választja az **Exportálás típusa** beállításhoz, meg kell győződnie arról, hogy az XML érvényes elérési utakat és/vagy előtagokat tartalmaz. Hozza létre és adja meg az XML-fájlt.  Ha a fájl érvénytelen, vagy a megadott elérési utak nem felelnek meg, a megrendelés a részleges vagy a nem exportált adatértékekkel végződik.

    Ha szeretné megtudni, hogyan adhat hozzá XML-fájlt egy tárolóhoz, olvassa el az [Exportálás az XML-fájllal](data-box-deploy-export-ordered.md#export-order-using-xml-file)című témakört.

   ![Exportálási beállítás kiválasztása](media/data-box-deploy-export-ordered/azure-data-box-export-04b.png)

   Az XML-bemenet példájának megtekintéséhez lásd: [minta XML-bemenet](data-box-deploy-export-ordered.md#sample-xml-file)

9. Az **Adatválasztás**területen tekintse át a beállításokat, és válassza a **tovább: biztonsági>**.

   ![Kapcsolattartási adatok](media/data-box-deploy-export-ordered/azure-data-box-export-05.png)

1. Ha a **Biztonság**területen engedélyezni szeretné a szoftveres kettős titkosítást, válassza **a dupla titkosítás engedélyezése lehetőséget a rendeléshez**. 

   A szoftveres titkosítás a Data Box lévő adatok AES-256 bites titkosításán felül történik.

   > [!NOTE]
   > Ha engedélyezi ezt a beállítást, megteheti, hogy a rendelés feldolgozása és az Adatmásolás tovább tart. Ez a beállítás a megrendelés létrehozása után nem módosítható.

   ![Adatmező importálásának biztonsági képernyője, dupla titkosítás](media/data-box-deploy-export-ordered/azure-data-box-export-05b.png)

   A folytatáshoz válassza a **tovább lehetőséget: kapcsolattartási adatok** .

10. A **kapcsolattartási**adatok területen válassza a **+ szállítási címek hozzáadása** lehetőséget a szállítási információk megadásához.

    ![Szállítási címek hozzáadása](media/data-box-deploy-export-ordered/azure-data-box-export-06.png)

11. A **szállítási cím hozzáadása**területen adja meg a vállalat vezetéknevét, nevét és postacímét, valamint egy érvényes telefonszámot. Válassza az **Érvényesítés** lehetőséget. A rendszer ellenőrzi, hogy a szolgáltatás elérhető-e a megadott szállítási címen. Ha a szolgáltatás elérhető a megadott szállítási címen, értesítést fog kapni erről.

    ![Szállítási címek ellenőrzése](media/data-box-deploy-export-ordered/azure-data-box-export-07.png)

    Ha olyan régiót rendel, amelyben az önfelügyelt szállítás elérhető, ezt a lehetőséget választhatja. Az önfelügyelt szállítással kapcsolatos további információkért lásd: [saját üzemeltetésű szállítás használata](data-box-portal-customer-managed-shipping.md).

12. Válassza a **szállítási címek hozzáadása** lehetőséget a szállítási adatok sikeres ellenőrzése után.

13. A **kapcsolattartási adatok**területen tekintse át a szállítási címet és az e-mail-címét. A szolgáltatás e-mail-értesítést küld a megrendelés állapotának minden változásáról a megadott e-mail-címekre.

    Javasoljuk a csoportos e-mail-cím használatát, hogy az értesítéseket a rendszergazda távollétében is kézhez kaphassa.

    ![Rendelés részletei](media/data-box-deploy-export-ordered/azure-data-box-export-09.png)

14. Kattintson a **Next (tovább) gombra: Review + Order>**. El kell fogadnia a feltételeket és a kikötéseket a rendelés létrehozásával folytatva.

15. Válassza a **Megrendelés** lehetőséget. A megrendelés létrehozása néhány percet vesz igénybe.

    ![Véglegesítő sorrend](media/data-box-deploy-export-ordered/azure-data-box-export-10.png)

## <a name="export-order-using-xml-file"></a>Exportálási sorrend XML-fájl használatával

Ha **az XML-fájl használata**lehetőséget választja, megadhatja az exportálni kívánt tárolókat és blobokat (oldalt és blokkokat). Az XML formázásához a [minta XML-fájl táblázatának](#sample-xml-file) specifikációit kell követnie. Az alábbi lépések bemutatják, hogyan lehet XML-fájlt használni az adatai exportálásához:

1. Az **Exportálás típusa**beállításnál válassza az **XML-fájl használata**lehetőséget. Ezt az XML-fájlt adja meg az exportálni kívánt blobok és Azure-fájlok megadásához. Az XML-fájl hozzáadásához jelölje be az **XML-fájl kiválasztásához kattintson ide**.
     ![XML-fájl](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-01.png)

2. A tároló létrehozásához kattintson a **+ tároló** elemre.
    ![XML-fájl](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-02.png)

3. Az **új tároló** lapon, amely a Azure Portal jobb oldalán jelenik meg, adja meg a tároló nevét. A névnek kisebbnek kell lennie, és tartalmazhat számokat és kötőjeleket (-). Ezután válassza ki a **nyilvános hozzáférési szintet** a legördülő listából. Javasoljuk, hogy a **magánjellegű (névtelen hozzáférés)** beállítással megakadályozza, hogy mások hozzáférjenek az adatokhoz. A tárolók hozzáférési szintjeivel kapcsolatos további információkért lásd: [tárolók hozzáférési engedélyei](../storage/blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

   ![XML-fájl](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-04.png)

4. Kattintson a **Létrehozás** gombra.

   ![XML-fájl](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-07.png)

   Ha a tároló létrehozása sikeresen megtörtént, a következő üzenet jelenik meg:

   ![XML-fájl](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-09.png)

5. Válassza ki a létrehozott tárolót, és kattintson rá duplán.

   ![XML-fájl](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-08.png)

6. Ha duplán kattint a tárolóra, megjelenik a tároló tulajdonságai nézet. Most csatolnia kell (vagy meg kell keresnie) az XML-fájlt, amely tartalmazza az exportálni kívánt blobok és/vagy Azure-fájlok listáját. Válassza a **Feltöltés** lehetőséget.

   ![XML-fájl](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-10c.png)

7. Sikeresen felvette az XML-fájlt a tárolóba. Csak az ebben az XML-ben megadott blobok és Azure-fájlok lesznek exportálva.

   ![XML-fájl](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-12.png)

## <a name="track-the-order"></a>A megrendelés nyomon követése

Miután elküldte a megrendelését, annak állapotát az Azure Portalon követheti nyomon. Lépjen a Data Box sorrendbe, majd az állapot megtekintéséhez lépjen az **Áttekintés** gombra. A portálon a megrendelés **Megrendelve** állapotban látható.

Az eszköz előkészítésének befejezésekor a rendszer az adatok másolását a kiválasztott Storage-fiókoktól kezdi. A portálon látható, hogy az **Adatmásolás állapota folyamatban** állapotú.

![Data Box a feldolgozott exportálási sorrend](media/data-box-deploy-export-ordered/azure-data-box-export-15b.png)

Data Box adatok másolása a forrás Storage-fiók (ok) ból. Az Adatmásolás befejezése után Data Box zárolva van, és a portálon megjelenik a sorrend a **Másolás befejeződött** állapotban.

![Data Box az adatmásolások exportálása befejeződött](media/data-box-deploy-export-ordered/azure-data-box-export-15c.png)

Ha az eszköz nem érhető el, értesítést fog kapni. Ha van elérhető eszköz, a Microsoft kiválaszt egyet a szállításhoz, és előkészíti a csomagot. Az eszköz előkészítése során a következő műveletek végezhetők el:

* SMB-megosztások létrehozása az eszközzel társított mindegyik tárfiókhoz.
* Hozzáférési hitelesítő adatok (felhasználónév és jelszó) kiosztása mindegyik megosztáshoz.
* Az eszköz zárolva van, és csak az eszköz feloldási jelszava használatával érhető el. A jelszó lekéréséhez be kell jelentkeznie a Azure Portal-fiókjába, és ki kell választania az **eszköz adatait**.

A Microsoft ezután előkészíti és elküldi az eszközt egy regionális szolgáltatón keresztül. Az eszköz feladását követően Ön megkapja a fuvarlevélszámot. A portál a **Feladva** állapotot mutatja.

![Data Box exportálási sorrend elküldve](media/data-box-deploy-export-ordered/azure-data-box-export-16.png)

Ha az önfelügyelt szállítás be van jelölve, e-mailben értesítést fog kapni a következő lépésekről, amikor az eszköz készen áll az adatközpontból való kiválasztásra. Az önfelügyelt szállítással kapcsolatos további információkért lásd: [saját üzemeltetésű szállítás](data-box-portal-customer-managed-shipping.md).

![Az önfelügyelt szállítás készen áll a felvételre](media/data-box-deploy-export-ordered/azure-data-box-export-17.png)

## <a name="cancel-the-order"></a>A rendelés lemondása

A megrendelés megszakításához a Azure Portal lépjen az **Áttekintés** elemre, és válassza a **Mégse** lehetőséget a parancssáv alatt.

Egy megrendelés elhelyezése után bármikor lemondhatja azt, mielőtt a rendelés megkezdi a feldolgozást.

Egy megszakított megrendelés törléséhez lépjen az **Áttekintés** elemre, és válassza a **Törlés** elemet a parancssorból.

## <a name="sample-xml-file"></a>Minta XML-fájl

Az alábbi XML-ben egy példa látható a blob-nevekre, a blob-előtagokra, valamint az exportálási sorrend által használt XML-formátumban található Azure-fájlokra az **XML-fájl használata** lehetőség kiválasztásakor:

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!-- BlobList/prefix/Container list for Blob storage for export  -->
   <BlobList>
      <BlobPath>/8tbpageblob/8tbpageblob/8tbpageblob</BlobPath>
      <BlobPathPrefix>/blockblob4dot75tbdata/</BlobPathPrefix>
      <BlobPathPrefix>/1tbfilepageblob</BlobPathPrefix>
      <BlobPathPrefix>/1tbfile/</BlobPathPrefix>
      <BlobPathPrefix>/8mbfiles/</BlobPathPrefix>
      <BlobPathPrefix>/64mbfiles/</BlobPathPrefix>
   </BlobList>
   <!-- FileList/prefix/Share list for Azure File storage for export  -->
   <AzureFileList>
      <FilePathPrefix>/64mbfiles/</FilePathPrefix>
      <FilePathPrefix>/4mbfiles/prefix2/subprefix</FilePathPrefix>
      <FilePathPrefix>/1tbfile/prefix</FilePathPrefix>
   </AzureFileList>
```

Néhány fontos pont az XML-fájlokra vonatkozóan:

* Az XML-címkék megkülönböztetik a kis-és nagybetűket, és pontosan a fenti mintában megadott módon kell megegyezniük.
* A címkék megnyitásához és bezárásához egyeznie kell.
* Az XML-címkék helytelenek, vagy a formázás adatexportálási hibát eredményezhet.
* A rendszer nem exportálja az adatexportálást, ha a blob és/vagy a fájl előtagja érvénytelen.

### <a name="examples-of-valid-blob-paths"></a>Példák érvényes blob-elérési utakra

A következő táblázat példákat mutat be a Blobok érvényes elérési útjaira:

   | Szelektor | BLOB elérési útja | Description |
   | --- | --- | --- |
   | Ezzel kezdődik |/ |A Storage-fiókban lévő összes blob exportálása |
   | Ezzel kezdődik |/$root/ |A gyökér tárolóban lévő összes blob exportálása |
   | Ezzel kezdődik |/containers |Minden blob exportálása minden olyan tárolóban, amely előtag- **tárolókkal** kezdődik |
   | Ezzel kezdődik |/container-name/ |Az összes blob exportálása a tároló **-tárolóban – név** |
   | Ezzel kezdődik |/container-name/prefix |Az összes blob exportálása a tároló **-tárolóban –** az előtag- **előtaggal** kezdődő név |
   | Egyenlő |$root/logo.bmp |A blob **logo.bmp** exportálása a gyökér tárolóba |
   | Egyenlő |8tbpageblob/mydata.txt |BLOB **mydata.txt** exportálása a tároló **8tbpageblob** |

## <a name="sample-log-files"></a>Minta naplófájlok

Ez a szakasz az exportálás során létrehozott minta naplófájlokat tartalmazza. A rendszer automatikusan létrehozza a hibanapló-naplókat. A részletes naplófájl létrehozásához ki kell választania a **részletes napló Belefoglalása** Azure Portal az exportálási sorrend konfigurálásakor.
A másolással és a részletes naplókkal kapcsolatos további információkért lásd: [naplók másolása](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

<!-- ### Verbose log

The following log files show examples of verbose logging when you select **Include verbose log**:

```xml
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820"></File>
```

### Copy logs

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box). -->

<!-- The following xml shows an example of the copy log when the export is successful:

```xml
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
    <TotalFiles_Blobs>27</TotalFiles_Blobs>
    <FilesErrored>0</FilesErrored>
</CopyLog>
```

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

The following xml shows an example of the copy log when the export has errors:

```xml
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>
``` -->

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
>
> * Az Exportálás előfeltételei
> * Data Box megrendelése exportáláshoz
> * Az exportálási sorrend nyomon követése
> * Az exportálási sorrend megszakítása

A következő oktatóanyag a Data Box beállítását mutatja be.

> [!div class="nextstepaction"]
> [Az Azure Data Box beállítása](./data-box-deploy-set-up.md)
