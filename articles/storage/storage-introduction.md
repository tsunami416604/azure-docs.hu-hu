---
title: "Bevezetés az Azure Storage használatába | Microsoft Docs"
description: "Áttekintés az Azure Storage-ról, a Microsoft online, felhőalapú adattároló szolgáltatásáról. Ismerje meg, hogyan használhatja fel alkalmazásaihoz az elérhető legjobb felhőalapú tárolómegoldást."
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: a4a1bc58-ea14-4bf5-b040-f85114edc1f1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/02/2017
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 8e11b36ee927724d4736f8a235aa97848ee2d271
ms.openlocfilehash: d0647706ad18b3106de44496e43ea822896215ba


---
# <a name="introduction-to-microsoft-azure-storage"></a>A Microsoft Azure Storage bemutatása

## <a name="overview"></a>Áttekintés
Az Azure Storage az ügyfelek igényeihez alkalmazkodó, modern, a tartósságra, rendelkezésre állásra és méretezhetőségre tervezett alkalmazásokhoz legmegfelelőbb felhőalapú tárolómegoldás. A cikket olvasó fejlesztők, informatikai szakemberek és üzleti döntéshozók megtudhatják:

* Mi is az Azure Storage, és hogyan használható fel a felhőalapú, mobil-, kiszolgálói vagy asztali alkalmazásokhoz
* Milyen típusú adatok tárolhatók az Azure Storage szolgáltatásaiban: blobadatok (objektumadatok), NoSQL-táblázatok adatai, üzenetsorbeli üzenetek és fájlmegosztások.
* Hogyan történik az Azure Storage-adatok hozzáférésének kezelése
* Hogyan teszi tartóssá az Azure Storage-adatokat a redundancia és a replikáció
* Hol kezdhet neki az első Azure Storage-alkalmazás felépítésének

Az Azure Storage gyors üzembe helyezéséről lásd: [Az Azure Storage első lépéseinek rövid összefoglalása](storage-getting-started-guide.md).

Az Azure Storage használatához szükséges eszközökkel, kódtárakkal és egyéb forrásokkal kapcsolatban további információt talál a cikk [Következő lépések](#next-steps) szakaszában.

## <a name="what-is-azure-storage"></a>Mi az Azure Storage?
A felhőalapú számítógép-használat új alkalmazási helyzeteket tesz lehetővé azon alkalmazások számára, amelyek adataihoz méretezhető, tartós és nagy rendelkezésre állású tárolóra van szükség – a Microsoft épp ezért fejlesztette ki az Azure Storage szolgáltatást. Az Azure Storage lehetővé teszi a fejlesztőknek az új forgatókönyvekhez szükséges nagyméretű alkalmazások létrehozását, emellett tárolási alapokat is biztosít az Azure Virtual Machines szolgáltatáshoz, ami újabb bizonyíték a szolgáltatás megbízhatóságára.

Az Azure Storage nagymértékben méretezhető, így a tudományos és üzleti elemzések, vagy a médiaalkalmazások big data forgatókönyvéhez szükséges több száz terabájtos adatkészleteket is tárolhatja és feldolgozhatja. Ugyanakkor a kisvállalkozások webhelyeihez szükséges kis mennyiségű adatokat is tárolhatja itt. Ha csökkennek az igényei, csak a tárolt adatmennyiség után kell fizetnie. Az Azure Storage jelenleg több tízbillió egyéni ügyfélobjektumot tárol, és másodpercenként átlagosan több millió lekérést szolgál ki.

Az Azure Storage rugalmas, így hatalmas, globális közönségek számára is tervezhet alkalmazásokat, majd az igényeknek megfelelően méretezheti őket, a tárolt adatok méretéhez és a lekérések számához igazodva. Csak a valóban használt funkciókért kell fizetnie, és csak akkor, amikor használja őket.

Az Azure Storage automatikus particionálási rendszert használ, amely a forgalomtól függően automatikusan kiegyenlíti az adatterhelést. Ez azt jelenti, hogy ahogy nő az alkalmazás iránti igény, az Azure Storage automatikusan lefoglalja számára a megfelelő erőforrásokat.

Az Azure Storage a világon bárhol, bármilyen alkalmazásból elérhető, függetlenül attól, hogy a felhőben, az asztalon, egy helyszíni kiszolgálón vagy egy mobileszközön fut. Az Azure Storage olyan mobil forgatókönyveknél is használható, ahol az alkalmazás az adatok egy részét az eszközön tárolja, és ezt a felhőben tárolt teljes adatkészlettel szinkronizálja.

Az Azure Storage ügyfelei a kényelmes fejlesztés érdekében számos operációs rendszert (beleértve a Windows és a Linux rendszereket is) és programnyelvet (többek között a .NET, a Java, a Node.js, a Python, a Ruby, a PHP, a C++ és a mobil programnyelveket) támogatnak. Az Azure Storage egyszerű REST API-kon keresztül is közzétesz adatforrásokat, amelyek minden, HTTP/HTTPS kapcsolaton adatot küldeni és fogadni képes ügyféllel elérhetők.

A prémium szintű Storage nagy teljesítményű, kis késleltetésű lemeztámogatást biztosít az Azure virtuális gépeken futtatott, nagy adatátviteli teljesítményt igénylő számítási feladatokhoz. A prémium szintű Azure Storage használatával több állandó adatlemezt csatlakoztathat egy virtuális géphez, és beállíthatja őket úgy, hogy alkalmazkodjanak a teljesítménykövetelményekhez. A maximális adatátviteli teljesítményt a minden adatlemezről elkészült, SSD-n tárolt biztonsági másolat biztosítja a prémium szintű Azure Storage-ban. A Premium Storage részletesebb áttekintéséért lásd: [Premium Storage: High-Performance Storage for Azure Virtual Machine Workloads](storage-premium-storage.md) (Premium Storage: Nagy teljesítményű tárterület az Azure virtuális gépek számítási feladataihoz).

## <a name="introducing-the-azure-storage-services"></a>Az Azure Storage szolgáltatásainak bemutatása
Az Azure Storage a következő négy szolgáltatást biztosítja: Blob Storage, Table Storage, Queue Storage és File storage.

* A Blob Storage a strukturálatlan objektumadatokat tárolja. Egy blob állhat bármilyen szövegből vagy bináris adatból, lehet például egy dokumentum, egy médiafájl vagy egy alkalmazástelepítő. A Blob Storage más néven Objektumtárnak is hívható.
* A Table Storage a strukturált adatkészleteket tárolja. A Table Storage a NoSQL-kulcsattribútumok adattára, amely gyors fejlesztési lehetőségeket és nagy adatmennyiségek gyors elérését biztosítja.
* A Queue Storage megbízható üzenetküldést biztosít a munkafolyamat-feldolgozáshoz és a felhőszolgáltatás összetevői közötti kommunikációhoz.
* A File Storage közös tárterületet biztosít a szabványos SMB-protokollt használó örökölt alkalmazások számára. Az Azure virtuális gépek és a felhőszolgáltatás csatlakoztatott megosztásokon keresztül adatokat oszthatnak meg az alkalmazások összetevői között, a helyszíni alkalmazások pedig a fájlszolgáltatás REST API-ján keresztül hozzáférhetnek a megosztott fájladatokhoz.

Az Azure-tárfiók olyan biztonságos fiók, amely hozzáférést biztosít az Azure Storage szolgáltatásaihoz. A tárfiók biztosítja az egyedi névteret a tárterület erőforrásainak. Az alábbi képen láthatók az Azure Storage erőforrásainak kapcsolatai egy tárfiókon belül:

![Azure Storage-erőforrások](./media/storage-introduction/storage-concepts.png)

[!INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

[!INCLUDE [storage-versions-include](../../includes/storage-versions-include.md)]

## <a name="blob-storage"></a>Blob Storage
A nagy mennyiségű, strukturálatlan objektumadatot a felhőben tárolni kívánó felhasználók számára a Blob storage költséghatékony, méretezhető megoldást kínál. A Blob Storage többek között az alábbi tartalmak tárolására használható:

* Dokumentumok
* Közösségi adatok (fényképek, videók, zene és blogok)
* Fájlok, számítógépek, adatbázisok és eszközök biztonsági másolatai
* Webalkalmazásokhoz tartozó képek és szövegek
* Konfigurációs adatok a felhőalapú alkalmazásokhoz
* Big data (naplók és egyéb nagy adatkészletek)

Minden blob egy tárolóba van rendezve. A tárolók nagy előnye, hogy az objektumok csoportjaihoz biztonsági házirendeket lehet rendelni. Egy tárfiókban korlátlan számú tárolót tárolhat, és egy tároló korlátlan számú blobot tartalmazhat a tárfiók 500 TB-os kapacitásán belül.

A Blob Storage háromféle blobot biztosít: blokkblobokat, hozzáfűző blobokat és lapblobokat.

* A blokkblobok a felhőbeli objektumok streamelésére és tárolására vannak optimalizálva, és kiválóan alkalmasak a dokumentumok, médiafájlok, biztonsági másolatok stb. tárolására.
* A hozzáfűző blobok a blokkblobokhoz hasonló, de a műveletek hozzáfűzésére optimalizált blobok. A naplóíró blobok csak a végükhöz hozzáadott új blokkal frissíthetők. A naplóíró blobok olyan forgatókönyvekhez felelnek meg leginkább, mint például a naplózás, ahol az új adatokat csak a blob végéhez kell hozzáírni.
* A lapblobok az infrastruktúra-szolgáltatási lemezek megjelenítésére és a véletlenszerű írások támogatására optimalizált blobok, amelyek akár az 1 TB-os méretet is elérhetik. Egy Azure virtuálisgép-hálózathoz csatlakoztatott infrastruktúra-szolgáltatási lemez tulajdonképpen egy lapblobként tárolt VHD.

A nagy adatkészleteknél, ahol a hálózati korlátok miatt irreális lenne az adatok vezetékes le- vagy feltöltése a Blob Storage-ba, elküldhet a Microsoftnak egy merevlemezt, hogy az adatokat közvetlenül az adatközpontból importálják vagy oda exportálják. Lásd: [Use the Microsoft Azure Import/Export Service to Transfer Data to Blob Storage](storage-import-export-service.md) (A Microsoft Azure Import/Export szolgáltatás használata az adatok átviteléhez a Blob Storage-ba).

## <a name="table-storage"></a>Table Storage
A modern alkalmazásokhoz gyakran az előző generációs szoftvereknél jobban méretezhető és rugalmasabb adattárolók szükségesek. A Table Storage magas rendelkezésre állású, nagymértékben méretezhető tárolót kínál, így alkalmazása a felhasználói igények függvényében automatikusan átméretezheti magát. A Table Storage a Microsoft NoSQL kulcs-/attribútumtára, amely séma nélküli kivitelezésében különbözik a hagyományos relációs adatbázisoktól. A séma nélküli adattárral az adatok könnyedén alkalmazkodnak az alkalmazás igényeinek fejlődéséhez. A Table Storage könnyen használható, így a fejlesztők gyorsan létrehozhatják benne alkalmazásaikat. Az adatok hozzáférése gyors és költséghatékony, bármilyen alkalmazásról legyen is szó.  Hasonló adatmennyiséggel számolva a Table Storage általában határozottan kevesebb költséggel jár, mint egy hagyományos SQL.

A Table Storage egy kulcs-/attribútumtár, ami azt jelenti, hogy a táblázatok minden értékét egy típusos tulajdonságnévvel tárolja. A tulajdonságnév felhasználható szűréshez vagy a kiválasztási feltételek megadásához is. A tulajdonságok és értékeik gyűjteménye egy entitást alkot. Mivel a Table Storage séma nélküli, egy tábla két entitása a tulajdonságok különböző gyűjteményeit tartalmazhatja, és ezek a tulajdonságok különböző típusúak lehetnek.

A Table Storage segítségével rugalmas adatkészleteket tárolhat, például webalkalmazások felhasználói adatait, címtárakat, eszközadatokat és bármilyen egyéb metaadatot, amelyre a szolgáltatásnak szüksége van.  Egy táblán korlátlan számú entitást tárolhat, és egy tárfiók a kapacitásán belül korlátlan számú táblát tartalmazhat.

A fejlesztők a Blob és a Queue szolgáltatáshoz hasonlóan a szabványos REST-protokollal kezelhetik és érhetik el a Table Storage szolgáltatást, azonban a Table Storage támogatja az OData protokoll egy részét is, így leegyszerűsíti a speciális lekérdezési képességeket, valamint lehetővé teszi a JSON és az AtomPub (XML-alapú) formátumok használatát.

A mai internetalapú alkalmazásoknál a Table Storage-hoz hasonló NoSQL-adatbázisok a hagyományos, relációs adatbázisok népszerű alternatíváját kínálják.

## <a name="queue-storage"></a>Queue Storage
A méretezhető alkalmazások tervezésekor az alkalmazás összetevői gyakran le vannak választva, hogy egymástól függetlenül lehessen őket méretezni. A Queue Storage megbízható üzenetkezelési megoldást kínál az alkalmazás összetevő közötti aszinkron kommunikációhoz, függetlenül attól, hogy az összetevők a felhőben, asztali gépen, egy helyszíni kiszolgálón vagy egy mobileszközön futnak. A Queue Storage támogatja az aszinkron feladatok kezelését és a feldolgozási munkafolyamatok kialakítását is.

Egy tárfiók tetszőleges számú üzenetsort tartalmazhat. Egy üzenetsor a tárfiók kapacitásán belül korlátlan számú üzenetet tartalmazhat. Az egyes üzenetek akár a 64 KB-os méretet is elérhetik.

## <a name="file-storage"></a>File Storage
Az Azure File Storage felhőalapú SMB-fájlmegosztásokat kínál, így költséges újraírások nélkül, gyorsan megoldható a fájlmegosztásra támaszkodó örökölt alkalmazások áttelepítése az Azure-ra. Az Azure File Storage szolgáltatással az Azure virtuális gépeken vagy felhőszolgáltatásában üzemelő alkalmazások fájlmegosztást csatlakoztathatnak a felhőben. Ez a megosztás hasonló ahhoz a csatlakoztatott SMB-megosztáshoz, amelyet az asztali alkalmazások használnak. Ezután bármennyi alkalmazás-összetevő egyszerre csatlakoztathatja a File Storage-megosztást, és hozzá is férhet.

Mivel a File Storage-megosztás egy szabványos SMB-fájlmegosztás, az Azure-ban futó alkalmazások a fájlrendszer adatátviteli API-jain keresztül férhetnek hozzá a megosztás adataihoz. A fejlesztők épp ezért meglévő kódjaik és képességeik felhasználásával áttelepíthetik az alkalmazásokat. Az informatikai szakemberek az Azure-alkalmazások rendszergazdai feladatainak részeként PowerShell-parancsmagokkal hozhatják létre, csatlakoztathatják és kezelhetik a File Storage-megosztásokat.

Az egyéb Azure Storage-szolgáltatásokhoz hasonlóan a File Storage is közzétesz egy REST API-t egy megosztás adatainak eléréséhez. A helyszíni alkalmazások hívhatják a File Storage REST API-jának meghívásával érhetik el egy fájlmegosztás adatait. Épp ezért egy vállalat dönthet úgy, hogy néhány örökölt alkalmazást áttelepít az Azure-ra, míg más alkalmazásokat továbbra is a szervezeten belül futtat. Vegye figyelembe, hogy a fájlmegosztások csatlakoztatása csak az Azure-ban futó alkalmazások számára lehetséges. A helyszíni alkalmazások csak a REST API-n keresztül férhetnek hozzá a fájlmegosztáshoz.

Az elosztott alkalmazások is használhatják a File Storage szolgáltatást a hasznos alkalmazásadatok, valamint a fejlesztési és teszteszközök tárolására és megosztására. Egy alkalmazás például tárolhat konfigurációs fájlokat és diagnosztikai adatokat (naplókat, metrikákat és összeomlási memóriaképeket) egy File Storage-megosztásban, hogy több virtuális gép vagy szerepkör is elérhesse őket. A fejlesztők és rendszergazdák az alkalmazások felépítéséhez és kezeléséhez szükséges segédprogramokat tárolhatnak a minden összetevő számára elérhető File Storage-megosztásban, így nem szükséges minden virtuális gépre és szerepkörpéldányra telepíteni őket.

## <a name="access-to-blob-table-queue-and-file-resources"></a>Hozzáférés a Blob-, Table-, Queue- és File-erőforrásokhoz
Alapértelmezés szerint csak a tárfiók tulajdonosa férhet hozzá a tárfiók erőforrásaihoz. Az adatok biztonsága érdekében a fiók erőforrásai felé irányuló összes kérelmet hitelesíteni kell. A hitelesítés megosztott kulcsos modellen alapszik. A blobokat beállíthatja úgy is, hogy támogassák a névtelen hitelesítést.

A tárfiókhoz a létrehozáskor két titkos hívóbetű jár, amelyek a hitelesítéshez használhatók. A két kulcs biztosítja, hogy az alkalmazás akkor is elérhető legyen, ha a biztonsági kulcsok megszokott felügyeleti gyakorlata szerint rendszeresen újragenerálja a kulcsokat.

Ha felügyelt hozzáférést kell biztosítani a felhasználóknak a tároló erőforrásaihoz, létrehozhat egy közös hozzáférésű jogosultságkódot. A közös hozzáférésű jogosultságkódok (SAS) hozzáfűzhetők egy URL-címhez, amely meghatalmazott hozzáférést biztosít a tároló egyik erőforrásához. A kód birtokában az érvényesség ideje alatt bárki hozzáférhet az URL-címen elérhető erőforráshoz a kódban megadott engedélyekkel. A 2015-04-05-ös verzió óta az Azure Storage két közös hozzáférésű jogosultságkód-típust támogat: a szolgáltatásalapú SAS-t és a fiókalapú SAS-t.

A szolgáltatásalapú SAS csak egy társzolgáltatás (a Blob, a Queue, a Table vagy a File szolgáltatás) egy erőforrásához biztosít hozzáférést.

A fiókalapú SAS egy vagy több tárolószolgáltatás erőforrásaihoz nyújt hozzáférést. Olyan szolgáltatásszintű műveletekhez is adhat hozzáférést, amelyek szolgáltatásalapú SAS kóddal nem érhetők el. A blobtárolók, táblák, üzenetsorok és fájlmegosztások olvasási, írási és törlési műveleteihez is hozzáférést biztosíthat, amelyeket a szolgáltatásalapú SAS nem engedélyez.

Végezetül pedig meghatározhatja, hogy egy tároló a blobjai, illetve egy adott blob nyilvánosan elérhető-e. Ha egy tárolót vagy blobot nyilvánosként jelöl meg, bárki névtelenül, hitelesítés nélkül megtekintheti.  A nyilvános tárolók és blobok az olyan erőforrások közzétételénél hasznosak, mint például a webhelyeken tárolt médiafájlok és dokumentumok.  Ha egy globális közönség számára csökkenteni kívánja a hálózati késleltetést, az Azure CDN használatával gyorsítótárazhatja a webhelyek által használt blobadatokat.

További információ a közös hozzáférésű jogosultságkódokról: [Using Shared Access Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md) (Közös hozzáférésű jogosultságkódok (SAS) használata). További információ a tárfiók biztonságos hozzáféréséről: [Manage anonymous read access to containers and blobs](storage-manage-access-to-resources.md) (Tárolók és blobok névtelen olvasási hozzáférésének kezelése) és [Az Azure Storage szolgáltatásainak hitelesítése](https://msdn.microsoft.com/library/azure/dd179428.aspx).

## <a name="replication-for-durability-and-high-availability"></a>Replikáció a tartósság és magas rendelkezésre állás érdekében
A Microsoft Azure tárfiók tartalmát mindig replikáljuk, így biztosítva az adatok tartósságát és magas rendelkezésre állását. A replikáció akár egyazon adatközponton belül, akár egy második adatközpontban készít másolatot az adatokról, a replikáció beállítástól függően. A replikáció óvja az adatokat és biztosítja az alkalmazás üzemidejét egy átmeneti hardverhiba esetén. Ha az adatok másolata egy második adatközpontba kerül, akkor a replikáció az elsődleges helyszínen bekövetkező katasztrofális hibával szemben is védelmet nyújt.

A replikáció biztosítja, hogy a tárfiók még hibák esetén is teljesíti a [Storage szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/storage/) feltételeit. Tekintse át az Azure Storage tartóssági és rendelkezésre állási garanciáit a szolgáltatói szerződésben .

Tárfiók létrehozásakor választhat a következő replikációs lehetőségek közül:

* **Helyileg redundáns tárolás (LRS)**. A helyileg redundáns tárolás három másolatot tart fenn adatairól. A rendszer háromszor replikálja az LRS-t egy régió egyetlen adatközpontján belül. Az LRS megvédi adatait a normál hardverhibáktól, de nem nyújt védelmet az adatközpontot érintő hibák ellen.

    Az LRS kedvezményes áron vásárolható meg. A maximális tartósság érdekében az alábbiakban ismertetett georedundáns tárolás használata javasolt.
* **Zónaredundáns tárolás (ZRS)**. A zónaredundáns tárolás három másolatot tart fenn adatairól. A ZRS során a rendszer két vagy három intézményben replikálja az adatokat, amelyek egy vagy két régióban is lehetnek, így a ZRS nagyobb tartósságot biztosít, mint az LRS. A ZRS biztosítja az adatok tartósságát egyetlen régión belül.

    A ZRS az LRS-nél nagyobb tartósságot biztosít, azonban a maximális tartósság érdekében az alábbiakban ismertetett georedundáns tárolás használata javasolt.

  > [!NOTE]
  > A ZRS jelenleg csak a blokkblobokhoz érhető el, és csak a 2014-02-14-es és frissebb verziókban támogatott.
  >
  > Miután létrehozott egy tárfiókot és kiválasztotta a ZRS-t, már nem módosíthatja a replikáció típusát más típusra, és ugyanez fordítva is igaz.
  >
  >
* **Georedundáns tárolás (GRS)**. A GRS hat másolatot tart fenn adatairól. A GRS tárolással a rendszer háromszor replikálja az adatokat az elsődleges régióban, valamint még háromszor replikálja őket egy másodlagos régióban, az elsődleges régiótól több száz kilométer távolságban, így a legnagyobb szintű tartósságot biztosítja. Ha az elsődleges régióban hiba történne, az Azure Storage feladatátvételt hajt végre a másodlagos régióba. A GRS biztosítja az adatok tartósságát két külön régióban.

    További információ az elsődleges és másodlagos régiók párosításairól: [Az Azure régiói](https://azure.microsoft.com/regions/).
* **Írásvédett georedundáns tárolás (RA-GRS)**. Az írásvédett georedundáns tárolás az adatokat egy másodlagos földrajzi helyre replikálja, és az adatokhoz a másodlagos helyszínen is írásvédett hozzáférést biztosít. Az írásvédett georedundáns tárolással az elsődleges vagy a másodlagos helyszínről is hozzáférhet az adatokhoz, ha valamely helyszín elérhetetlenné válik. Tárfiók létrehozásakor alapértelmezés szerint az írásvédett georedundáns tárolás van beállítva.

  > [!IMPORTANT]
  > A tárfiók létrehozása után módosíthatja az adatok replikálási módját, kivéve ha a fiók létrehozásakor a ZRS módot választotta. Azonban ügyeljen arra, hogy ha az  LRS-ről  GRS-re vagy RA- GRS-re vált, akkor lehetséges, hogy egyszeri adatátviteli díjat kell fizetnie.
  >
  >

További információ az Azure Storage replikációs beállításairól: [Azure Storage replication](storage-redundancy.md) (Azure Storage replikációja).

A tárfiók replikációjának díjszabásáról itt tájékozódhat: [Az Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/). Tekintse meg [Az Azure régiói](https://azure.microsoft.com/regions/#services) című lapot azzal kapcsolatban, hogy az egyes régiókban mely szolgáltatások érhetőek el.

További információ az Azure Storage tartósságának szerkezeti részleteiről: [SOSP Paper - Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx) (SOSP tanulmány – Azure Storage: Egy magas rendelkezésre állású, felhőalapú szolgáltatás erős konzisztenciával).

## <a name="transferring-data-to-and-from-azure-storage"></a>Adatok áthelyezése az Azure Storage-ba és az Azure Storage-ból
Az AzCopy parancssori segédprogrammal blob-, fájl- és táblaadatokat másolhat egy tárfiókon belül vagy tárfiókok között. További információ: [Transfer data with the AzCopy Command-Line Utility](storage-use-azcopy.md) (Adatátvitel az AzCopy parancssori segédprogrammal).

Az AzCopy az [Azure Adatátviteli könyvtárra](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/) épül, amelynek jelenleg az előzetes verziója érhető el.

Az Azure Import/Export szolgáltatás lehetőséget biztosít, hogy az Azure-adatközpontba postán elküldött merevlemez-meghajtó segítségével blobadatokat importáljon a tárfiókba, vagy exportálja őket onnan. További információ az Import/Export szolgáltatásról: [Use the Microsoft Azure Import/Export Service to Transfer Data to Blob Storage](storage-import-export-service.md) (A Microsoft Azure Import/Export szolgáltatás használata az adatok átviteléhez a Blob Storage-ba).

## <a name="pricing"></a>Díjszabás
[!INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

## <a name="storage-apis-libraries-and-tools"></a>Storage API-k, kódtárak és eszközök
Az Azure Storage-erőforrások bármely olyan nyelvvel hozzáférhetők, amelyekkel HTTP/HTTPS kérelmek indíthatók. Ezenfelül az Azure Storage számos népszerű nyelvhez biztosít programozási kódtárakat. Ezek a kódtárak sok szempontból leegyszerűsítik az Azure Storage használatát, mivel számos részletet kezelnek (például a szinkron és aszinkron hívás, műveletek kötegelése, kivételek kezelése, automatikus újrapróbálkozások, működési viselkedés stb.). Jelenleg a következő nyelvekhez és platformokhoz érhetők el kódtárak (a továbbiak összeállítása folyamatban):

### <a name="azure-storage-data-services"></a>Azure Storage-adatszolgáltatások
* [A Storage szolgáltatások REST API-ja](http://msdn.microsoft.com/library/azure/dd179355.aspx)
* [A Storage ügyféloldali kódtára a .NET-keretrendszerhez, a Windows Phone-hoz és a Windows-futtatókörnyezethez](https://www.nuget.org/packages/WindowsAzure.Storage/)
* [A Storage ügyféloldali kódtára a C++ programnyelvhez](https://github.com/Azure/azure-storage-cpp)
* [A Storage ügyféloldali kódtára a Javához/Androidhoz](/develop/java/)
* [A Storage ügyféloldali kódtára a Node.js-hez](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [A Storage ügyféloldali kódtára a PHP-hez](/develop/php/)
* [A Storage ügyféloldali kódtára a Rubyhoz](/develop/ruby/)
* [A Storage ügyféloldali kódtára a Pythonhoz](/develop/python/)
* [Storage-parancsmagok a PowerShell 1.0-hoz](https://msdn.microsoft.com/library/azure/mt269418.aspx)

### <a name="azure-storage-management-services"></a>Azure Storage kezelési szolgáltatások
* [A Storage erőforrás-szolgáltató REST API-ja – referencia](https://msdn.microsoft.com/library/azure/mt163683.aspx)
* [Storage erőforrás-szolgáltató ügyfél a .NET-hez](https://msdn.microsoft.com/library/azure/mt131037.aspx)
* [A Storage erőforrás-szolgáltató parancsmagjai a PowerShell 1.0-hoz](https://msdn.microsoft.com/library/azure/mt607151.aspx)
* [A Storage szolgáltatásfelügyelet REST API-ja](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-services"></a>Azure Storage adatátviteli szolgáltatások
* [A Storage Import/Export szolgáltatás REST API-ja](https://msdn.microsoft.com/library/azure/dn529096.aspx)
* [A Storage adatátviteli ügyféloldali kódtára a .NET-hez](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### <a name="tools-and-utilities"></a>Eszközök és segédprogramok
* [Azure Storage Explorer](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Azure Storage-ügyféleszközök](storage-explorers.md)
* [Azure SDK-k és eszközök](https://azure.microsoft.com/tools/)
* [Azure Storage Emulator](http://www.microsoft.com/download/details.aspx?id=43709)
* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [AzCopy parancssori segédprogram](http://aka.ms/downloadazcopy)

## <a name="next-steps"></a>Következő lépések
Az alábbi forrásokból többet is megtudhat az Azure Storage-ról:

### <a name="documentation"></a>Dokumentáció
* [Az Azure Storage dokumentációja](https://azure.microsoft.com/documentation/services/storage/)
* [Tárfiók létrehozása](storage-create-storage-account.md)
* [Az Azure Storage első lépéseinek rövid összefoglalása](storage-getting-started-guide.md)

### <a name="for-administrators"></a>Rendszergazdáknak
* [Using Azure PowerShell with Azure Storage (Az Azure PowerShell és az Azure Storage együttes használata)](storage-powershell-guide-full.md)
* [Using the Azure CLI with Azure Storage (Az Azure CLI és az Azure Storage együttes használata)](storage-azure-cli.md)

### <a name="for-net-developers"></a>.NET-fejlesztőknek
* [Az Azure Blob Storage használatának első lépései a .NET-keretrendszerrel](storage-dotnet-how-to-use-blobs.md)
* [Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel](storage-dotnet-how-to-use-tables.md)
* [Az Azure Queue Storage használatának első lépései a .NET-keretrendszerrel](storage-dotnet-how-to-use-queues.md)
* [Ismerkedés a Windowshoz készült Azure File Storage szolgáltatással](storage-dotnet-how-to-use-files.md)

### <a name="for-javaandroid-developers"></a>Java/Android-fejlesztőknek
* [How to use Blob storage from Java (A Blob Storage használata Javával)](storage-java-how-to-use-blob-storage.md)
* [How to use Table storage from Java (A Table Storage használata Javával)](storage-java-how-to-use-table-storage.md)
* [How to use Queue Storage from Java (A Queue Storage használata Javával)](storage-java-how-to-use-queue-storage.md)
* [How to use File Storage from Java (A File Storage használata Javával)](storage-java-how-to-use-file-storage.md)

### <a name="for-nodejs-developers"></a>Node.js-fejlesztőknek
* [How to use Blob storage from Node.js (A Blob Storage használata Node.js-sel)](storage-nodejs-how-to-use-blob-storage.md)
* [How to use Table storage from Node.js (A Table Storage használata Node.js-sel)](storage-nodejs-how-to-use-table-storage.md)
* [How to use Queue storage from Node.js (A Queue Storage használata Node.js-sel)](storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>PHP-fejlesztőknek
* [How to use Blob storage from PHP (A Blob Storage használata PHP-val)](storage-php-how-to-use-blobs.md)
* [How to use Table storage from PHP (A Table Storage használata PHP-val)](storage-php-how-to-use-table-storage.md)
* [How to use Queue storage from PHP (A Queue Storage használata PHP-val)](storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>Ruby-fejlesztőknek
* [How to use Blob storage from Ruby (A Blob Storage használata Rubyval)](storage-ruby-how-to-use-blob-storage.md)
* [How to use Table storage from Ruby (A Table Storage használata Rubyval)](storage-ruby-how-to-use-table-storage.md)
* [How to use Queue storage from Ruby (A Queue Storage használata Rubyval)](storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Python-fejlesztőknek
* [How to use Blob storage from Pythonnal (A Blob Storage használata Pythonnal)](storage-python-how-to-use-blob-storage.md)
* [How to use Table storage from Python (A Table Storage használata Pythonnal)](storage-python-how-to-use-table-storage.md)
* [How to use Queue storage from Python (A Queue Storage használata Pythonnal)](storage-python-how-to-use-queue-storage.md)
* [How to use File Storage from Python (A File Storage használata Pythonnal)](storage-python-how-to-use-file-storage.md)



<!--HONumber=Feb17_HO1-->


