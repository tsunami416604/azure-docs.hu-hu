---
title: "Azure Import/Export használatával történő adatátvitelhez számára és Azure Storage-ból |} Microsoft Docs"
description: "Megtudhatja, hogyan importálási létrehozni és exportálni a feladatokat az adatoknak az Azure Storage érkező vagy oda irányuló az Azure portálon."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 668f53f2-f5a4-48b5-9369-88ec5ea05eb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: muralikk
ms.openlocfilehash: ffcf0766b89cdab7c79c28dad6bf4c80275e33fc
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-azure-storage"></a>Az adatok átviteléhez az Azure Storage a Microsoft Azure Import/Export szolgáltatás használata
Ebben a cikkben azt részletes útmutatást nyújtanak az Azure Import/Export szolgáltatás használatával biztonságos átvitelére a nagy mennyiségű adatok Azure Blob storage és Azure fájlok által az Azure adatközpontba szállítási lemezmeghajtókat. Ez a szolgáltatás adatok átviteléhez az Azure storage merevlemez-meghajtók és a helyszíni helyek szállítás is használható. A SATA egyetlen lemezmeghajtó adatait vagy Azure Blob storage-vagy Azure fájlok importálhatók. 

> [!IMPORTANT] 
> A szolgáltatás csak fogadja a belső SATA merevlemez vagy SSD meghajtók csak. Nincs más eszköz használata támogatott. Nem küldenek külső HDD NAS-eszközökön, stb., akkor adja vissza, ha lehetséges, vagy más módon elvetett.
>
>

Kövesse az alábbi lépéseket, ha a lemezen lévő adatok Azure Storage importálható.
### <a name="step-1-prepare-the-drives-using-waimportexport-tool-and-generate-journal-files"></a>1. lépés: Készítse elő a meghajtó/s WAImportExport eszközzel, és napló fájl/s készítése.

1.  Az adatok Azure Storage importálható azonosításához. Ennek oka lehet egy helyi kiszolgálón vagy egy hálózati megosztásra önálló fájlok és könyvtárak.
2.  Attól függően, hogy az adatok teljes mérete be kell szereznie a szükséges számú 2,5 hüvelyk SSD vagy 2,5" vagy 3.5-ös" SATA II vagy III merevlemez-meghajtókat.
3.  Csatlakoztassa közvetlenül használatával SATA merevlemez-meghajtók vagy külső USB-adapterek egy windows-számítógépre.
4.  Hozzon létre egy NTFS-kötet minden merevlemez-meghajtón, és rendeljen meghajtóbetűjelet a köteten. Nincs csatlakozási pontok le.
5.  Engedélyezze a bit tároló titkosítást az NTFS-köteten. Kövesse az utasításokat a https://technet.microsoft.com/en-us/library/cc731549(v=ws.10).aspx to enable encryption on the windows machine.
6.  Másolás & Beillesztés húzza & dobja el vagy Robocopy, illetve bármely ilyen eszköz lemezeken ezen titkosított egyetlen kötetek adatok másolása teljesen.
7.  WAImportExport V1 letöltését https://www.microsoft.com/en-us/download/details.aspx?id=42659
8.  Csomagolja ki, hogy az alapértelmezett mappa waimportexportv1. Például C:\WaImportExportV1  
9.  Futtatás rendszergazdaként, és nyissa meg a PowerShell vagy a parancssorból, és módosítsa a könyvtárat a tömörítetlen mappába. Például cd C:\WaImportExportV1
10. Másolás az alábbi parancssort egy Jegyzettömb és szerkesztésére létrehozása a parancssorból végzi.
  ./WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session #1 /sk:***== /t:D /bk:*** /srcdir:D: \ /dstdir:ContainerName / /skipwrite
    
    egy fájl neve /j: napló .jrn kiterjesztésű nevezik. A napló-fájl jön létre minden meghajtó, és ezért javasoljuk, hogy a lemez sorozatszám használata a napló fájlnév.
    /SK: az azure Storage-fiók kulcsát. / t: a szállítási lemez meghajtóbetűjelet. Például D /bk: az a meghajtó /srcdir bit tároló kulcsa: a lemez szállítási meghajtóbetűjelet követ: \. Például D:\
    /dstdir: a neve, amelyre az adatokat importálni az Azure-tárolót.
    /skipwrite 
    
11. Az egyes szállítási lemezt ismételje meg a 10.
12. A parancssor minden Futtatás /j: paraméterrel megadott nevű napló fájl jön létre.

### <a name="step-2-create-an-import-job-on-azure-portal"></a>2. lépés: Az importálási feladat létrehozása Azure-portál.

1. Jelentkezzen be a https://portal.azure.com/ és a további szolgáltatások -> tároló -> "importálási/exportálási feladatok" kattintson **létrehozás importálási/exportálási feladatok**.

2. Alapvető beállítások területen válassza ki a "Importálása az Azure", adjon meg egy karakterláncot feladat nevét, válasszon egy előfizetést, adja meg vagy válasszon egy erőforráscsoportot. Adjon meg egy leíró nevet az importálási feladatnak. Vegye figyelembe, hogy a név is tartalmazhat, csak kisbetűket, számokat, kötőjeleket és aláhúzásjeleket tartalmazhat, betűvel kell kezdődnie, és nem tartalmazhat szóközt. Ön úgy dönt, hogy a feladatok követésének, amikor folyamatban van, és azok befejezése után nevét használja.

3. A feladat részletes adatait tartalmazó részben tölt fel a meghajtó napló beolvasott meghajtó előkészítési lépés során. Ha waimportexport.exe version1 használt, szüksége lesz az egyes előkészítette a meghajtót egy fájl feltöltéséhez. Válassza ki a tárfiók, amely az adatok Importálja a tárolási fiók "Importálása a cél" szakaszában. A gyűjtőtár hely adatok automatikusan kitöltődnek a kiválasztott tárfiók régió alapján.
   
   ![Hozzon létre importálási feladat - 3. lépés](./media/storage-import-export-service/import-job-03.png)
4. Ismét szállítási adatokra vonatkozó részt, válassza ki a szolgáltatói a legördülő listából, és adjon meg egy érvényes vivőjel-szám, amely adott szolgáltatónként hozott létre. A Microsoft ehhez a fiókhoz használandó küldje el azt a meghajtókat, az importálási feladat befejezése után. Adja meg a teljes és érvényes kapcsolattartójának a neve, telefon, e-mail, utca, házszám, város, zip, állam/proviince és ország vagy régió.
   
5. Összegzési területen a szállítási cím Azure DataCenter Azure DC lemezek szállítási használandó valósul meg. Győződjön meg arról, hogy a feladat neve és a teljes címe szerepelnek a szállítási címkén. 

6. Kattintson az OK gombra az Összegzés lapon importálási feladat létrehozásának befejezéséhez.

### <a name="step-3-ship-the-drives-to-the-azure-datacenter-shipping-address-provided-in-step-2"></a>3. lépés: A meghajtó/s a szállítási cím 2. lépésben megadott Azure adatközpontba szállítási.
FedEx, UPS vagy DHL küldje el a csomagot a Azure DC használható.

### <a name="step-4-update-the-job-created-in-step2-with-tracking-number-of-the-shipment"></a>4. lépés: A feladat, a 2. lépés a szállítási számú követési létre frissítése.
A lemezek szállítási, után térjen vissza a **Import/Export** oldalon, és az alábbi lépéseket követve azonosítószám, a) keresse meg az Azure portálon, majd kattintson az importálás a sikertelen feladat-b) kattintson a **feladat állapotát és a nyomkövetési információ frissítése Miután mellékeltük a meghajtók**. c) jelölje be a jelölőnégyzetet "Megjelölése rendszerrel szállított" d) adja meg a szállító és követési számát.
A nyomon követési számot a rendszer nem frissíti a feladat létrehozása két héten belül, ha a feladat lejár. A feladat előrehaladását a portál irányítópultján követhető nyomon. Tekintse meg, mi az előző szakaszban minden feladat állapota: által [a feladat állapotának megtekintése](#viewing-your-job-status).

## <a name="when-should-i-use-the-azure-importexport-service"></a>Mikor kell használnom az Azure Import/Export szolgáltatást?
Azure Import/Export szolgáltatás esetekben érdemes feltöltése vagy adatok letöltése a hálózaton keresztül túl lassú, vagy további hálózati sávszélesség első megfizethetetlenné.

Forgatókönyvek például használhatja ezt a szolgáltatást:

* Adatok áttelepítése a felhőbe: nagy mennyiségű adatok gyors áthelyezése az Azure-ba, és hatékonyan költség.
* Tartalomterjesztést: a felhasználói helyek gyorsan adatküldéshez.
* Biztonsági mentés: A helyszíni adatok biztonsági mentések tárolására az Azure Storage igénybe vehet.
* Adat-helyreállítás: nagy mennyiségű storage-ban tárolt adatok helyreállításához, és annak a helyszíni helyre kézbesíteni.

## <a name="prerequisites"></a>Előfeltételek
Az itt látható a szolgáltatás használatához szükséges előfeltételeket. Olvassa el őket figyelmesen a meghajtók, mielőtt.

### <a name="storage-account"></a>Tárfiók
Meglévő Azure-előfizetés és az Import/Export szolgáltatás használata egy vagy több storage-fiókokat kell rendelkeznie. Minden feladat adatátvitel vagy a csak egy tárfiókot is használható. Más szóval egy egyetlen importálási/exportálási feladatok nem terjedhetnek ki több tárfiókok között. Új tárfiók létrehozásával kapcsolatos további információkért lásd: [a Storage-fiók létrehozása](storage-create-storage-account.md#create-a-storage-account).

### <a name="data-types"></a>Adattípusok
Azure Import/Export szolgáltatás segítségével az adatok másolása **blokk** blobokat, **lap** blobot, vagy **fájlok**. Viszont csak akkor exportálható **blokk** blobokat, **lap** blobok vagy **Append** BLOB az Azure storage-ban Ez a szolgáltatás. A szolgáltatás támogatja az Azure storage Azure fájlok csak importálása. Azure-fájlok exportálása jelenleg nem támogatott.

### <a name="job"></a>Feladat
A megkezdéséhez importálása vagy exportálása a tárolási, először feladatot hoz létre. Egy feladat lehet, az importálási feladat vagy exportálási feladat:

* Hozzon létre egy importálási feladat, ha a kívánt Azure-tárfiókot kell a helyszíni adatok átviteléhez.
* Exportálási feladat létrehozása, ha azt szeretné, a merevlemez-meghajtókat, amelyek a számunkra mellékeltük a tárfiók jelenleg tárolt adatok átviteléhez. Feladatot hoz létre, amikor Ön értesítést az Import/Export szolgáltatás, hogy Ön lesz kell szállítási legalább egy merevlemez-meghajtók egy Azure adatközpontba.

* Az importálási feladat meg lesz kell szállítási az adatokat tartalmazó merevlemez-meghajtókat.
* Az exportálási feladat akkor lesz kell szállítási üres merevlemez-meghajtókat.
* Legfeljebb 10 merevlemez-meghajtók száma feladat elküldhet.

Az importálás létrehozhat vagy az Azure portál használatával feladat exportálása vagy az [Azure Storage Import/Export REST API felülete](/rest/api/storageimportexport).

### <a name="waimportexport-tool"></a>WAImportExport eszköz
A létrehozásának első lépése egy **importálása** közzéteendő meghajtó előkészítése az importálási folyamat. Készítse elő a meghajtók, csatlakoztassa a helyi kiszolgálón, és futtassa a WAImportExport eszközt a helyi kiszolgálón. Ez WAImportExport az eszköz lehetővé teszi az adatok másolását a meghajtóra, a meghajtón a BitLocker, az adatok titkosítása és a meghajtó Adatbázisnapló-fájlok generálása.

Az adatbázisnapló-fájlok a feladat és a meghajtó meghajtó sorozatszáma és a tárfiók neve például alapvető adatainak tárolására. A napló fájl nem található a meghajtón. Importálási feladat létrehozásakor használható. Feladat létrehozása részletes adatait a cikk későbbi részében találhatók.

A WAImportExport eszköze csak 64 bites Windows operációs rendszerrel kompatibilis. Tekintse meg a [operációs rendszer](#operating-system) az adott operációsrendszer-verziók támogatottak területén.

Töltse le a legújabb verzióját a [WAImportExport eszköz](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExportV2.zip). A WAImportExport eszközzel kapcsolatos további tudnivalókért tekintse meg a [a WAImportExport eszközzel](storage-import-export-tool-how-to.md).

>[!NOTE]
>**Előző verzió:** is [WAImportExpot V1 letöltése](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip) az eszköz verzióját, majd tekintse át [WAImportExpot V1 használati útmutató](storage-import-export-tool-how-to-v1.md). Az eszköz WAImportExpot V1-es verzióját támogatást nyújt az **lemezek előkészítése során az adatok már előre írása a lemezre**. Akkor is, WAImportExpot V1-es eszköz használata, ha a rendelkezésre álló csak kulcs SAS-kulcs.

>

### <a name="hard-disk-drives"></a>Merevlemez-meghajtók
2,5 hüvelykes csak SSD vagy 2,5" vagy 3.5-ös" SATA II vagy III belső HDD támogatottak az Import/Export szolgáltatás való használatra. Egy egyetlen importálási/exportálási feladatok lehet egy legfeljebb 10 HDD/SSD-k és minden egyes HDD/SSD tetszőleges méretű lehet. Nagy számú meghajtókat is elosztva több feladat és a nem hozható létre feladatok száma korlátozza. 

Az importálási feladatok csak az első adatmennyiség a meghajtón dolgoz fel. Az adatmennyiség NTFS fájlrendszerrel kell formázni.

> [!IMPORTANT]
> Külső merevlemez-meghajtók egy beépített USB-adapterrel járó nem támogatja ezt a szolgáltatást. Emellett a kis-és nagybetűhasználat egy külső HDD belül a lemez nem használható; Ne küldjön külső HDD.
> 
> 

Alább van az adatok másolása belső HDD használt külső USB-adapterek listáját. Anker 68UPSATAA - 02BU Anker 68UPSHHDS-BU Startech SATADOCK22UE Orico 6628SUS3-C-fekete (6628 sorozat) Thermaltake BlacX gyakran használt adatok-csere SATA külső merevlemez meghajtó rögzített állomás (USB 2.0-s & eSATA)

### <a name="encryption"></a>Titkosítás
A meghajtón található adatokat titkosítani kell a BitLocker meghajtótitkosítás segítségével. Ez védi az adatokat, amíg az átvitel során.

Az importálási feladatok, két módja van a titkosítás végrehajtásához. Az első módja adja meg a beállítást, a WAImportExport eszköz futtatásakor meghajtó előkészítése során dataset CSV-fájl használata esetén. A második módja engedélyezze manuálisan a meghajtón a BitLocker-titkosítást, és adja meg a fürt megosztott kötetei szolgáltatás driveset a titkosítási kulcs WAImportExport eszköz parancssori meghajtó előkészítése során futtatásakor.

Az exportálási feladatok a meghajtók, az adatok másolását követően a szolgáltatás titkosítja a meghajtón a BitLocker használatával, mielőtt azt vissza. A titkosítási kulcsot az Azure-portál Ön számára biztosított.  

### <a name="operating-system"></a>Operációs rendszer
A merevlemez-meghajtót a meghajtó az Azure-ba, mielőtt a WAImportExport eszközzel készítse elő a következő 64 bites operációs rendszerek egyikét használhatja:

Windows 7 Enterprise, Windows 7 Ultimate, Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise, Windows 10<sup>1</sup>, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 rendszerben. Mindegyik említett operációs rendszerektől támogatja, a BitLocker meghajtótitkosítás.

### <a name="locations"></a>Helyek
Az Azure Import/Export szolgáltatás támogatja az adatok másolását, és az összes nyilvános Azure storage-fiók. A következő helyek egyikére merevlemez-meghajtók elküldhet. Ha a tárfiók egy nyilvános Azure helyre, amely nincs megadva itt, egy másik szállítási helyre lesz kell megadni, ha a feladatot az Azure-portálon vagy az Import/Export REST API használatával hoz létre.

Szállítási helyek támogatottak:

* USA keleti régiója
* USA nyugati régiója
* USA 2. keleti régiója
* USA nyugati régiója, 2.
* USA középső régiója
* USA északi középső régiója
* USA déli középső régiója
* USA nyugati középső régiója
* Észak-Európa
* Nyugat-Európa
* Kelet-Ázsia
* Délkelet-Ázsia
* Kelet-Ausztrália
* Délkelet-Ausztrália
* Nyugat-Japán
* Kelet-Japán
* Közép-India
* Dél-India
* Nyugat-India
* Közép-Kanada
* Kelet-Kanada
* Dél-Brazília
* Korea középső régiója
* USA-beli államigazgatás – Virginia
* USA-beli államigazgatás – Iowa
* US DoD – Kelet
* US DoD – Középső régió
* Kelet-Kína
* Észak-Kína
* Az Egyesült Királyság déli régiója
* Közép-Németország
* Északkelet-Németország

### <a name="shipping"></a>Szállítási
**Az Adatközpont meghajtók szállítási:**

Az importálási vagy exportálási feladat létrehozásakor, adja meg a szállítási cím szállítási a meghajtók támogatott helyek közül az egyik. A szállítási cím megadott függ a tárfiók helyét, azonban nem lehet ugyanaz, mint a tárfiókhely.

FedEx, UPS vagy DHL küldje el a meghajtókat, a szállítási cím használható.

**Az adatközpontból meghajtók szállítási:**

Az importálási vagy exportálási feladat létrehozásakor meg kell adnia egy címet a Microsoft akkor használja, ha a meghajtók szállítási vissza a feladat befejezése után. Ellenőrizze, hogy megadta a feldolgozási késedelmeket elkerülése érdekében egy érvényes címet.

A szolgáltatói rendelkeznie kell a megfelelő nyomon követi azokat a felügyeleti lánc fenntartása. Meg kell adnia egy érvényes FedEx, UPS vagy DHL szolgáltatónként számú szállítási a meghajtók vissza a Microsoft által használandó fiók. Egy FedEx, UPS vagy DHL szám meghajtók szállítási vissza a helyekről amerikai és Európai szükség. Egy DHL szám meghajtók szállítási újból az Ázsia és a Ausztrália szükség. Létrehozhat egy [FedEx](http://www.fedex.com/us/oadr/) (az amerikai és európai) vagy [DHL](http://www.dhl.com/) (ázsiai és Ausztrália) szolgáltatónként a fiókot, ha még nem rendelkezik ilyennel. Ha már rendelkezik egy vivőjel-szám, győződjön meg arról, hogy legyen érvényes.

A szállítási a csomagok, hajtsa végre a következő feltételek [Microsoft Azure szolgáltatási feltételek](https://azure.microsoft.com/support/legal/services-terms/).

> [!IMPORTANT]
> Vegye figyelembe, hogy valóban a fizikai adathordozókat esetleg kereszt-nemzetközi határokon. Biztos, hogy a fizikai adathordozó és adatok vannak importálva és/vagy az alkalmazandó jogszabályok betartása exportált biztosításáért felelős. Még a szállítás előtt a fizikai adathordozó egyeztetni a tanácsadók annak ellenőrzésére, hogy az adathordozót, és adatok jogilag szállítani a azonosított adatközpontba. Ez segít annak érdekében, hogy időben Microsoft eléri. Bármelyik csomag nemzetközi határokon túl fogja keresztezi például kereskedelmi számla (kivéve ha meghaladó határokon belül Európai Unió) csomag, amelyben van szüksége. A kereskedelmi számla szolgáltatónként webhelyéről töltött másolatának sikerült kinyomtatása. Példa kereskedelmi számlák [DHL kereskedelmi számla](http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf) és [FedEx kereskedelmi számla](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf). Győződjön meg arról, hogy a Microsoft nem lett tüntetve ennek az az exportáló.
> 
> 

## <a name="how-does-the-azure-importexport-service-work"></a>Hogyan működik az Azure Import/Export szolgáltatás?
A helyszíni hely és az Azure storage-ban az Azure Import/Export szolgáltatás létrehozása feladatokat és merevlemez-meghajtók egy Azure adatközpontba szállítási közötti küldhetnek adatokat. Minden szállított merevlemez-meghajtó nem tartozik egyetlen feladat. Minden feladat tartozik egy tárfiókot. Tekintse át a [szükséges előfeltételek szakasz](#pre-requisites) gondosan további tudnivalók a mintaadatokról ezt a szolgáltatást, például a támogatott adattípusok, a lemez a típusok, a helyek és a szállítási.

Ez a szakasz azt ismerteti magas szintű lépései importálni és exportálni a feladatokat. Későbbi részében a [gyors üzembe helyezés szakasz](#quick-start), az importálás létrehozni és exportálni a feladat részletes útmutatást nyújtanak az azt.

### <a name="inside-an-import-job"></a>Az importálási feladat belül
Magas szinten az importálási feladat a következő lépéseket foglalja magában:

* Az importálandó adatokat, és hány meghajtót kell meghatározni.
* Adja meg az adatok az Azure storage cél blob vagy a fájl helyét.
* A WAImportExport eszközzel másolja át az adatokat egy vagy több merevlemezéről, és a Bitlockerrel titkosítani.
* Az importálási feladat létrehozása a céloldali tárfiók az Azure-portálon vagy az Import/Export REST API használatával. Ha az Azure portál használatával, a meghajtó napló fájlok feltöltése.
* Adja meg a címet és a vivőjel fiók száma a meghajtók szállítási vissza szeretné használni.
* Küldje el a merevlemez-meghajtók feladat létrehozásakor megadott szállítási címre.
* A nyomon követési száma az importálási feladat részleteit a kézbesítési frissítése, és küldje el az importálási feladat.
* Fogadott és az Azure-adatközpont feldolgozni.
* Meghajtók mellékeltük a vivőjel-fiókkal a található az importálási feladat Válaszcím.
  
    ![Ábra 1:Import feladat folyamata](./media/storage-import-export-service/importjob.png)

### <a name="inside-an-export-job"></a>Exportálási feladat belül
> [!IMPORTANT]
> A szolgáltatás csak támogatja az Azure BLOB exportálása és nem támogatja az Azure-fájlok exportálása...
> 
>

Magas szinten exportálási feladat a következő lépéseket foglalja magában:

* Az exportálandó adatokat, és hány meghajtót kell meghatározni.
* Azonosítsa a forrás blobokkal vagy a tároló elérési az adatok a Blob Storage tárolóban.
* Exportálási feladat létrehozása az Azure-portálon vagy az Import/Export REST API használatával forrás tárfiókját.
* Adja meg a forrás blobok vagy a tároló adatait az exportálási feladat.
* Adja meg a címet és szolgáltatónként fiók száma azt a meghajtók szállítási használandó.
* Küldje el a merevlemez-meghajtók feladat létrehozásakor megadott szállítási címre.
* Frissítse a kézbesítési követési szám a exportálási feladat részletei, valamint az exportálási feladat elküldéséhez.
* A fogadott és az Azure-adatközpont feldolgozni.
* A meghajtók titkosítása a BitLocker; a kulcsok elérhetők az Azure-portálon.  
* A meghajtók mellékeltük a vivőjel-fiókkal a található az importálási feladat Válaszcím.
  
    ![Ábra 2:Export feladat folyamata](./media/storage-import-export-service/exportjob.png)

### <a name="viewing-your-job-and-drive-status"></a>A feladat és a meghajtó állapotának megtekintése
Az importálás állapotának nyomon követése, vagy exportálni a feladatokat az Azure portálról. Kattintson a **Import/Export** fülre. A feladatok listája megjelenik a lap.

![Feladat állapotának megtekintése](./media/storage-import-export-service/jobstate.png)

A következő feladatállapotok attól függően, hogy a meghajtó a folyamat során az egyik jelenik meg.

| Feladat állapota | Leírás |
|:--- |:--- |
| Létrehozás | A feladat létrehozása után létrehozása állapotában van beállítva. A feladat létrehozása állapotban van, amíg az Import/Export szolgáltatás azt feltételezi, hogy rendelkezik a meghajtók nem lett kiadva az adatközpontban. Egy feladat két héttel, amely után a rendszer automatikusan törli a szolgáltatás létrehozása állapotban maradhat. |
| Szállítási | Miután a csomag küldje el, frissítenie kell a nyomkövetési információk az Azure portálon.  Ez a feladat ikonná "Szállítási". A feladat két héttel a szállítási állapotban marad. 
| Érkezett | Miután az összes meghajtó az adatközpontban a fogadott, a feladat állapotát állítja be a fogadott. |
| Átvitele | Miután legalább egy meghajtó már megkezdődött a feldolgozás, a feladat állapotát állítja be a átadása. Című rész a meghajtó állapota alatt részletes információkat. |
| Csomag | Után az összes meghajtó feldolgozása befejeződött, a feladat kerülnek csomagolás állapota mindaddig, amíg a meghajtók szállítják vissza. |
| Befejezve | Miután az összes meghajtó még szállított vissza az ügyfél számára, ha a feladat befejezése nem jelenik meg hibaüzenet, majd a kész állapot úgy lesz beállítva, a feladat. A feladat automatikusan törli 90 nap után befejezve állapotban. |
| Lezárva | Miután az összes meghajtó még szállított vissza az ügyfél a, ha történt hiba a feladat feldolgozása során, majd a feladat fog szerepelni a lezárt állapotában. A feladat automatikusan törli 90 nap után a lezárt állapotban. |

Az alábbi táblázat ismerteti a meghajtók egyéni életciklusát, akkor átkerül egy importálási vagy exportálási feladat keresztül. Minden olyan meghajtó egy feladat jelenlegi állapota most látható Azure-portálról.
A következő táblázat ismerteti az egyes állapot esetében egy feladat minden olyan meghajtó továbbítása is.

| Meghajtó állapotát | Leírás |
|:--- |:--- |
| A megadott | Az importálási feladatnak a feladat létrehozásakor az Azure-portálon, a kezdeti egy meghajtó állapota a megadott állapot. Exportálási feladat, a meghajtó nem adható meg a feladat jön létre, mert a kezdeti meghajtó állapota fogadott állapotát. |
| Érkezett | A meghajtó tér át a kapott állapota, akkor az Import/Export szolgáltatás operátor feldolgozta-e a meghajtókat, hogy az importálás a szállítási vállalati érkezett. Az exportálási feladat kezdeti meghajtó állapota fogadott állapotát. |
| NeverReceived | A meghajtó NeverReceived állapotát helyezi át, ha a csomag feladat megérkeznek, de a csomag nem tartalmaz a meghajtó. A meghajtó is áthelyezheti a állapotba, ha két héten lett, mert a szolgáltatás a szállítási adatokat kapott, de a csomag még nem érkezett meg az Adatközpont. |
| Átvitele | A meghajtó átadása állapotát helyezi át, ha a szolgáltatás megkezdi az adatok átvitele a meghajtó Windows Azure Storage. |
| Befejezve | A meghajtó helyezi át a kész állapot, ha a szolgáltatás sikeresen átadta a hibátlan az adatokat.
| CompletedMoreInfo | A meghajtó CompletedMoreInfo állapotát helyezi át, ha a szolgáltatás észlelt kapcsolatos néhány problémát ismertetünk az adatok másolásának származó, vagy a meghajtóra. Az információk között szerepelhet hibák, figyelmeztetések és információs üzenetek blobok felülírására.
| ShippedBack | A meghajtó helyezi át a ShippedBack állapot, amikor azt teljesítették data center hátsó visszatérési címre. |

Az Azure-portálon a lemezkép egy példa feladat meghajtó állapotát jeleníti meg:

![Meghajtó állapotának megtekintése](./media/storage-import-export-service/drivestate.png)

A következő táblázat ismerteti a meghajtó hiba állapotok és az egyes állapotokhoz végrehajtott műveleteket.

| Meghajtó állapotát | Esemény | Megoldás / a következő lépés |
|:--- |:--- |:--- |
| NeverReceived | A meghajtó, amely NeverReceived (mert nem érkezett a feladat szállítási részeként) érkezik egy másik szállítási van megjelölve. | A műveleti csapata a meghajtó helyezi át a kapott állapotát. |
| N/A | A meghajtó, amely nem része semmilyen feladatot megérkezik az Adatközpont egy másik feladat részeként. | A meghajtó egy extra meghajtóként lesz megjelölve, és visszatér az ügyfél az eredeti csomagot kapcsolódó feladat végrehajtását. |

### <a name="time-to-process-job"></a>Feldolgozási feladatot az idő
Mennyi ideig tart a folyamat, például a szállítási idő különböző tényezőktől függően változik importálási/exportálási feladatok feladat típusa, típusa és másolásának adatok méretétől és a megadott lemez méretét. Az Import/Export szolgáltatás nem rendelkezik egy SLA-t, de a lemezek fogadása után a szolgáltatás nagy hangsúlyt fektet fejezze be a másolási 7 – 10 nap múlva. A REST API használatával hatékonyabban nyomon követheti a feladat előrehaladását. A lista feladatok művelet, amely arra utal, a másolási folyamat százalékban kifejezett teljes paramétere van. Érheti el, ha egy ideje kritikus importálási/exportálási feladatok befejezéséhez becsült van szüksége.

### <a name="pricing"></a>Díjszabás
**Meghajtó díj kezelése**

Minden meghajtó, az importálás részeként feldolgozott meghajtó kezelési díj ellenében történik, vagy exportálja a feladatot. A részletek a [Azure Import/Export szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Szállítási költségek**

Amikor Ön Azure-meghajtóval, a szállítási szolgáltatói fizetett szállítási költsége. Amikor Microsoft, a meghajtók visszatér, a vivőjel-fiók, amely a feladat létrehozásakor, a megadott díjfizetéssel szállítási költsége.

**Tranzakciós költségek**

Nincsenek nem tranzakciós költségek adatok importálása az Azure Storage. A szabványos kilépő költségek is alkalmazható, ha adatait a Blob-tároló exportálja. Tranzakciós költségek a további részletekért lásd: [adatátviteli díjszabás.](https://azure.microsoft.com/pricing/details/data-transfers/)



## <a name="how-to-import-data-into-azure-file-storage-using-internal-sata-hdds-and-ssds"></a>Adatok importálása az Azure File Storage belső SATA merevlemezeket és SSD-k hogyan?
Kövesse az alábbi lépéseket az Azure File Storage importálható a lemezen lévő adatok esetén.
Az első lépés az Azure Import/Export szolgáltatás használatával az adatok importálása a esetén a WAImportExport eszközzel meghajtó előkészítése. A meghajtók előkészítése az alábbi lépésekkel.

1. Az Azure File Storage importálható azon adatok meghatározásában. Ennek oka lehet a helyi kiszolgálón vagy egy hálózati megosztásra önálló fájlok és könyvtárak.  
2. Szüksége lesz, attól függően, hogy az adatok teljes mérete meghajtók számának meghatározásához. Szerzik be a szükséges számú 2,5 hüvelyk SSD vagy 2,5" vagy 3.5-ös" SATA II vagy III merevlemez-meghajtókat.
4. Határozza meg, a könyvtárak és/vagy az önálló fájlok, amelyet a program minden merevlemez-meghajtón.
5. A DataSet adatkészlet és driveset a CSV-fájlok létrehozása.
    
  Az alábbiakban a következő egy minta dataset CSV fájl például adatimportálási Azure-fájlok formájában:
  
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\50M_original\100M_1.csv.txt","fileshare/100M_1.csv.txt",file,rename,"None",None
    "F:\50M_original\","fileshare/",file,rename,"None",None 
    ```
   A fenti példában a "fájlmegosztási" gyökerébe 100M_1.csv.txt kerülnek. Ha a "fájlmegosztási" nem létezik, jön létre. Minden fájl és mappa alatti 50M_original rekurzív módon másolja a fájlmegosztási lesz. Gyökérmappa-szerkezetében maradnak.

    További információ [a dataset CSV-fájl előkészítése](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    


    **Driveset CSV-fájl**

    A driveset jelző értéke, amelyhez a meghajtó-betűjelek ahhoz, hogy az eszköz megfelelően válassza ki a lemezek listáját készüljön leképezve lemezek listáját tartalmazó CSV-fájlból. 

    A példában driveset CSV-fájl az alábbiakban látható:
    
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    H,Format,SilentMode,Encrypt,
    ```

    A fenti példában feltételezzük, hogy két lemezek vannak csatolva hozzá, és alapvető NTFS típusú kötetek G:\ kötet levelek és H:\ létrejött. Az eszköz formázása és titkosíthatja a lemezt, amely H:\ futtatja, és nem formázása vagy titkosíthatja a lemezt üzemeltető G:\ kötet.

    További információ [driveset CSV-fájl előkészítése](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file).

6.  Használja a [WAImportExport eszköz](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) másolja az adatokat legalább egy merevlemez-meghajtókat.
7.  A titkosítási mezője drivset BitLocker-titkosítást a merevlemez-meghajtó engedélyezése a fürt megosztott kötetei szolgáltatás "Titkosítás" is megadhat. Azt is megteheti sikerült is engedélyezheti a BitLocker titkosítást manuálisan a merevlemez-meghajtóról, és adja meg a "AlreadyEncrypted", majd adja meg a fürt megosztott kötetei szolgáltatás driveset a kulcsot az eszköz futtatása során.

8. Ne módosítsa a merevlemez-meghajtók vagy a napló fájlban lévő adatok lemezét befejezése után.

> [!IMPORTANT]
> Minden egyes merevlemez-meghajtó előkészíti a naplófájl eredményez. Ha az importálás az Azure portál használatával hoz létre, a meghajtók, hogy importálási feladat részét képező Adatbázisnapló-fájlok kell feltöltenie. -Meghajtók nélkül napló fájlokat nem fogja feldolgozni.
> 
>

Az alábbiakban a parancsok és példák WAImportExport eszközzel a merevlemez-meghajtó előkészítése.

WAImportExport eszköz PrepImport parancs az első másolás munkamenet könyvtárak és/vagy egy új példány munkamenet-fájlok másolása:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**1. példa importálása**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Annak érdekében, hogy **további meghajtók hozzáadása**, egy hozzon létre egy új driveset fájlt, és futtassa a parancsot az alábbi. Későbbi másolási munkamenetek a különböző lemezmeghajtók megadottnál InitialDriveset csv-fájlban adjon meg egy új driveset CSV-fájlt, és a "AdditionalDriveSet" paraméter értékeként adja meg. Használja a **azonos naplófájl** nevet, és adjon meg egy **új munkamenet-azonosító**. Ugyanaz, mint a InitialDriveSet formátum AdditionalDriveset CSV-fájl formátuma.

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

**Importálja a 2. példa**
```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```

Ahhoz, hogy további adatok hozzáadása a azonos driveset, WAImportExport eszköz PrepImport parancs hívható későbbi másolási munkamenetek másolása további fájlokat vagy könyvtár: az azonos merevlemez-meghajtókra InitialDriveset .csv fájlban megadott munkamenetek későbbi másolása, adja meg a **azonos naplófájl** nevet, és adja meg egy **új munkamenet-azonosító**; nincs szükség arra, hogy a tárfiók kulcsára.

```
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

**Importálja a 3. példa**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

A WAImportExport eszközzel kapcsolatos további részletek megtekintéséhez [merevlemezek előkészítése az importálási](storage-import-export-tool-preparing-hard-drives-import.md).

Emellett tekintse meg a [minta munkafolyamat merevlemezek előkészítése az importálási feladat](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md) részletesebb lépésenkénti leírását.  



## <a name="create-an-export-job"></a>Exportálási feladat létrehozása
Értesítse az Import/Export szolgáltatásról, hogy Ön lesz kell szállítási egy vagy több üres meghajtókat, az adatközpont, hogy adatokat importáljon a tárfiókba exportált a és a meghajtókat, majd kapnia exportálási feladat létrehozása.

### <a name="prepare-your-drives"></a>Készítse elő a meghajtók
A meghajtók előkészítése exportálási feladat következő előzetes ellenőrzése ajánlott:

1. Ellenőrizze a WAImportExport eszköz PreviewExport paranccsal szükséges lemezek számát. További információkért lásd: [meghajtó használata Előnézet egy exportálása feladat](https://msdn.microsoft.com/library/azure/dn722414.aspx). A program segít előzetes meghajtóinak használatát választotta, a BLOB-alapú a használni kívánt meghajtók mérete.
2. Ellenőrizze, hogy akkor is olvasására vagy írására a Exportálás feladathoz szállítják merevlemez-meghajtóról.

### <a name="create-the-export-job"></a>Az exportálási feladat létrehozása
1. Exportálási feladat létrehozásához keresse meg a további szolgáltatások -> tároló -> "importálási/exportálási feladatok" az Azure portálon. Kattintson a **létrehozás importálási/exportálási feladatok**.
2. Lépés 1 alapjait, az "Exportálás az Azure", adjon meg egy karakterláncot feladat nevét, válasszon egy előfizetést, adja meg vagy válasszon egy erőforráscsoportot. Adjon meg egy leíró nevet az importálási feladatnak. Vegye figyelembe, hogy a név is tartalmazhat, csak kisbetűket, számokat, kötőjeleket és aláhúzásjeleket tartalmazhat, betűvel kell kezdődnie, és nem tartalmazhat szóközt. Ön úgy dönt, hogy a feladatok követésének, amikor folyamatban van, és azok befejezése után nevét használja. Adja meg az exportálási feladat felelős személy kapcsolattartási adatait. 

3. Lépés 2 feladat részleteit válassza ki a tárfiók, amely az adatok exportálja a tárolási fiók szakaszában. A gyűjtőtár hely rendszer lehet automatikusan beállítja a kiválasztott tárfiók régió alapján. Adja meg, melyik blobadatokat importáljon a tárfiókba exportálja az üres meghajtó vagy a meghajtók kíván. Ha szeretné, exportálja a tárfiókban lévő összes blob adatokat, vagy is megadhat, amely blobok vagy beállítja a bináris objektumok exportálása.
   
   Adja meg az exportálandó blob, a **Equal To** választó, és adja meg a relatív elérési útját a blob, a tároló neve kezdve. Használjon *$root* adhatja meg a gyökérszintű tárolóban.
   
   Előtaggal kezdődő összes BLOB megadásához használja a **kezdődik** választó, és adja meg az előtagot, kezdve a perjel '/'. Az előtag lehet, hogy az előtag a tároló neve, a teljes tároló neve vagy a teljes tároló neve követi a blob nevének előtagja.
   
   Az alábbi táblázat példákat érvényes blob elérési utak közül:
   
   | Kiválasztó | BLOB elérési út | Leírás |
   | --- | --- | --- |
   | Kezdődik |/ |Exportálja a tárfiókban lévő összes BLOB |
   | Kezdődik |/$root / |A gyökérszintű tárolóban lévő összes blobok exportálása |
   | Kezdődik |/Book |Exportálja az összes BLOB a tárolóban előtaggal kezdődő **könyv** |
   | Kezdődik |/Music/ |Exportálja a tárolóban lévő blobok összes **zene** |
   | Kezdődik |/ zene/szerelem |Exportálja a tárolóban lévő összes blobok **zene** előtaggal rendelkező karaktersorral **kedvelt** |
   | Egyenlő |$root/logo.bmp |Exportálja a blob **logo.bmp** a gyökérszintű tárolóban |
   | Egyenlő |Videos/Story.mp4 |Exportálja a blob **story.mp4** tárolóban **videók** |
   
   Ezen a képernyőfelvételen látható módon meg kell adnia az érvényes formátum a feldolgozás során hibák elkerülése érdekében blob elérési utakat.
   
   ![Hozzon létre exportálási feladat - 3. lépés](./media/storage-import-export-service/export-job-03.png)

4. A lépés a 3 vissza szállítási adatait a legördülő listából válassza ki a szolgáltatói, és adja meg egy érvényes szolgáltatója, amely adott szolgáltatónként hozott létre. A Microsoft ehhez a fiókhoz használandó küldje el azt a meghajtókat, az importálási feladat befejezése után. Adja meg a teljes és érvényes kapcsolattartójának a neve, telefon, e-mail, utca, házszám, város, zip, állam/proviince és ország vagy régió...
   
 5. Az összefoglalás lapon Azure DC lemezek szállítási használt Azure DataCenter szállítási cím valósul meg. Győződjön meg arról, hogy a feladat neve és a teljes címe szerepelnek a szállítási címkén. 

6. Kattintson az OK gombra az Összegzés lapon importálási feladat létrehozásának befejezéséhez

7. A lemezek szállítási, után térjen vissza a **Import/Export** lap az Azure-portálon a) keresse meg és kattintson az importálás a b) kattintson a **frissítési feladat állapotát és a nyomkövetési információ, miután mellékeltük a meghajtók**. 
     c) jelölje be a jelölőnégyzetet "Megjelölése rendszerrel szállított" d) adja meg a szállító és követési számát.
    
   A nyomon követési számot a rendszer nem frissíti a feladat létrehozása két héten belül, ha a feladat lejár.
   
8. A feladat előrehaladását a portál irányítópultján követheti nyomon. Tekintse meg, mi az előző szakaszban minden feladat állapota: által [a feladat állapotának megtekintése](#viewing-your-job-status).

   > [!NOTE]
   > Ha exportálni a blob másolása merevlemezre időpontjában használatban van, Azure Import/Export szolgáltatás pillanatkép készítése a blob, és másolja a pillanatkép.
   > 
   > 
 
9. Miután megkapta a meghajtók az exportált adatok, megtekintheti, és másolja a meghajtó a szolgáltatás által létrehozott BitLocker-kulcsok. Nyissa meg az Azure portálon feladat exportálása, majd kattintson az Import/Export fülre. Az exportálási feladat válasszon a listából, majd kattintson a BitLocker-kulcsok lehetőséget. A BitLocker-kulcsok az alábbi módon jelenik meg:
   
   ![BitLocker-kulcsok exportálási feladat megtekintése](./media/storage-import-export-service/export-job-bitlocker-keys.png)

Nyissa meg a gyakran feltett alatt keresztül, az ügyfelek a szolgáltatás használata során felmerülő leggyakoribb kérdések magában foglalja.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Másolhatja az Azure Import/Export szolgáltatás használata Azure File storage?**

Igen, az Azure Import/Export szolgáltatás támogatja az Azure fájl Storge importálás. Azure-fájlok exportálása jelenleg nem támogatja.

**Az Azure Import/Export szolgáltatás érhető el a CSP-előfizetések?**

Az Azure Import/Export szolgáltatás támogatja a kriptográfiai Szolgáltató előfizetések.

**I kihagyhatja a meghajtó előkészítése az importálási feladat vagy szeretnék előkészítheti egy meghajtó másolása nélkül?**

Bármely meghajtóra, szállítási adatimportálási elő kell készíteni az Azure WAImportExport eszközzel. Adatok másolása a meghajtó a WAImportExport eszközt kell használnia.

**Van bármilyen lemez előkészítése exportálási feladat létrehozásakor elvégzéséhez?**

Nem, de néhány előzetes ellenőrzése használata ajánlott. Ellenőrizze a WAImportExport eszköz PreviewExport paranccsal szükséges lemezek számát. További információkért lásd: [meghajtó használata Előnézet egy exportálása feladat](https://msdn.microsoft.com/library/azure/dn722414.aspx). A program segít előzetes meghajtóinak használatát választotta, a BLOB-alapú a használni kívánt meghajtók mérete. Is ellenőrizheti, hogy olvasni és írni a Exportálás feladathoz szállítják merevlemez-meghajtóról.

**Mi történik, ha véletlenül küldése egy HDD, amely nem felel meg a támogatott követelmények?**

Az Azure-adatközpont visszatér a meghajtó nem felel meg a támogatott követelményeinek Önnek. Ha csak néhány a meghajtót a csomag megfelel a támogatási követelmények, ezek a meghajtók dolgoz fel, és Önnek visszaadott a meghajtókat, amelyek nem felelnek meg a követelményeknek.

**Lehet megszakítani a feladatot?**

Megszakíthatja a feladat állapotának létrehozásakor, illetve szállítási.

**Mennyi ideig lehet befejezett feladatok állapotának megtekintése az Azure-portálon?**

Legfeljebb 90 napig megtekintheti a befejezett feladatokhoz állapotát. Befejezett feladatokhoz 90 nap után törlődnek.

**Mi a teendő, ha szeretnék, importálása és exportálása a 10-nél több meghajtó?**

Egy importálási vagy exportálási feladat az Import/Export szolgáltatás egyetlen feladatban csak 10 meghajtók hivatkozhat. Ha több mint 10 meghajtók szállítani kívánt, több feladat is létrehozhat. Ugyanazt a feladatot társított meghajtók együtt szállítani kell egy csomagban található.
Microsoft nyújt útmutatást, és segítséget, ha az adatok kapacitás több lemezre kiterjedő importálási feladatok. Lépjen kapcsolatba a bulkimport@microsoft.com további információ

**Nem a szolgáltatás formázása előtt azok a meghajtók?**

Nem. Az összes meghajtó bitlockerrel titkosított.

**Lehet az importálási/exportálási feladatok meghajtókat is vásárolni a Microsoft?**

Nem. Szüksége lesz, küldje el saját meghajtókat is importálni és exportálni a feladatokat.

** Hogyan hozzáférhet a szolgáltatás ** importált adatok

Az Azure storage-fiókjában az adatok az Azure portálon keresztül is elérhetők, vagy egy önálló eszközzel hívják meg a Tártallózó alkalmazással. https://docs.microsoft.com/Azure/VS-Azure-Tools-Storage-Manage-with-Storage-Explorer 

**Az importálási feladat befejezése után mi lesz a adatok néz a tárfiókban lévő? A könyvtár-hierarchia megőrzi?**

Ha a merevlemez-meghajtó előkészítése az importálási feladat, a cél a fürt megosztott kötetei szolgáltatás adatkészlet DstBlobPathOrPrefix mező szerint van megadva. Ez az a cél tároló a storage-fiókot, amelyhez a merevlemez-meghajtóról az adatok másolásakor. A cél tárolóban virtuális könyvtárak mappák a merevlemez-meghajtóról jön létre, és blobok fájlok jönnek létre. 

**Ha a meghajtó már megtalálható a tárfiókban lévő fájlok, a szolgáltatás felülírja meglévő blobokkal vagy a fájlok a storage-fiókom?**

Amikor előkészíti a meghajtót, vagy adhat meg hogy felülírható-e a cél fájlok törlése figyelmen kívül hagyott használatát a mező dataset CSV-fájl neve: < átnevezése |} nem írható felül |} felülírása >. Alapértelmezés szerint a szolgáltatás fogja az új fájlok átnevezése helyett felülírja a meglévő blobokkal vagy a fájlokat.

**Egy 32 bites operációs rendszerekkel kompatibilis a WAImportExport eszköz?**
Nem. A WAImportExport eszköze csak 64 bites Windows operációs rendszerekkel kompatibilis. Tekintse meg az operációs rendszerek szakasza a [szükséges előfeltételek](#pre-requisites) támogatott operációsrendszer-verziók teljes listáját.

**I bele kell foglalni a merevlemez-meghajtóról csakis a csomagot?**

Csak a merevlemez-meghajtók adjon szolgáltatástól. Ne adjon meg például a kiemelt tápkábelek vagy USB-kábelt.

**Küldje el a meghajtókat FedEx vagy DHL kell?**

Az Adatközpont használatával FedEx, DHL, UPS vagy Velünk postai bármely ismert vivőjel-meghajtóval elküldhet. Azonban a meghajtók vissza az adatközpontból szállítási, meg kell adnia egy FedEx fiók az Amerikai Egyesült Államokban és Európa, vagy DHL fiók szám Ázsia és a Ausztrália régiókban.

**Vannak-e a nemzetközi a meghajtó szállítási korlátozások?**

Vegye figyelembe, hogy valóban a fizikai adathordozókat esetleg kereszt-nemzetközi határokon. Biztos, hogy a fizikai adathordozó és adatok vannak importálva és/vagy az alkalmazandó jogszabályok betartása exportált biztosításáért felelős. Még a szállítás előtt a fizikai adathordozó egyeztetni a tanácsadók annak ellenőrzésére, hogy az adathordozót, és adatok jogilag szállítani a azonosított adatközpontba. Ez segít annak érdekében, hogy időben Microsoft eléri.

**Feladat létrehozása a szállítási cím esetén egy helyet, amely eltér a tárfiókhely. Mit tegyek?**

Néhány fiók tárolóhelyek szállítási másodlagos helyek vannak leképezve. Korábban elérhető szállítási helyről is ideiglenesen rendelhetők más helyekre. Mindig ellenőrizze a megadott feladat létrehozásakor, a meghajtók, mielőtt szállítási címe.

**Ha a meghajtó szállítási, a szolgáltatói a data center címét és telefonszámát a telefonszám kér. Mit kell nyújtania?**

A telefonszám és a tartományvezérlő cím valósul meg a projekt létrehozásának részeként.

**Az Azure Import/Export szolgáltatás segítségével csendes-óceáni TÉLI postaládákhoz és SharePoint-adatok másolása az o365-re?**

Tekintse meg [importálási csendes-óceáni TÉLI fájlok vagy az Office 365 SharePoint-adatok](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**Az Azure Import/Export szolgáltatás követve másolja át a biztonsági másolatok offline állapotba az Azure Backup szolgáltatás?**

Tekintse meg [Offline biztonsági másolat munkafolyamat Azure backup](../../backup/backup-azure-backup-import-export.md).

**Mi az a HDD maximális számát egy részletben?**

Tetszőleges számú HDD lehet egy szállítási, és amennyiben több feladat tartozik a lemezek javasoljuk, hogy a) a megfelelő feladat nevekkel feliratú lemezekkel rendelkeznek. b) a feladatok frissítése követési számnak -1, a utótaggal-2 stb.
  
**Mi az a maximális Blokkblob és lemez Import/Export támogatott Blob oldalméret?**

Maximális Blokkblob mérete körülbelül 4.768TB vagy 5,000,000 MB.
Az oldalakra vonatkozó Blob maximális mérete 1TB.

**Támogatja a lemez Import/Export AES 256 titkosítás?**

Az Azure Import/Export szolgáltatás alapértelmezés szerint titkosítja a bitlocker titkosítás AES-128, de ez növelhető AES 256 manuálisan titkosítása a bitlocker előtt az adatok másolásakor. 

Ha használ [WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), az alábbiakban van egy minta parancs
```
WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
```
Ha használ [WAImportExport eszköz](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) "AlreadyEncrypted" adja meg, és adja meg a fürt megosztott kötetei szolgáltatás driveset a kulcsot.
```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
```
## <a name="next-steps"></a>Következő lépések

* [A WAImportExport eszköz beállítása](storage-import-export-tool-how-to.md)
* [Adatátvitel az AzCopy parancssori segédprogrammal](storage-use-azcopy.md)
* [Az Azure importálási exportálása REST API minta](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

