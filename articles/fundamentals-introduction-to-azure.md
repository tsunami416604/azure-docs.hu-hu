---
title: "Bevezetés a Microsoft Azure |} Microsoft Docs"
description: "Új Microsoft Azure-bA? Első általános áttekintést szolgáltatást kínál kapcsolódó hogyan hasznosak."
services: " "
documentationcenter: .net
author: rboucher
manager: carolz
editor: 
ms.assetid: 6f47f711-2208-4c21-8c1d-826a54c05c29
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2015
ms.author: robb
ms.openlocfilehash: efcafa40c3d47ebfdd4520f98b65ad2c05b03c57
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="introducing-microsoft-azure"></a>Introducing Microsoft Azure
A Microsoft Azure a Microsoft alkalmazás platformja a nyilvános felhő.  Ez a cikk célja, hogy biztosítson alaprendszert az Azure-ban – alapok megértéséhez még akkor is, ha a felhő semmit nem tudja a számítástechnikai.

**Ez a cikk olvasása**

Azure folyamatosan nő, egyszerűen get túlterhelt.  Indítsa el a alapvető szolgáltatásokat, amelyek először a cikkben találhatók, és további szolgáltatásokat, majd nyissa meg. Amely a nem jelenti azt, csak a további szolgáltatások önmagában nem használhatók. De az alapvető szolgáltatások jött létre az Azure-ban futó alkalmazások részében.

**Visszajelzés küldése**

Visszajelzése fontos. Ez a cikk Azure hatékony áttekintést adjon meg. Ha nem, adja meg, a megjegyzéseket szakaszban az oldal alján. Adjon meg néhány részletek megjelenítéséhez várható és hogyan lehet fokozni a cikk.  

## <a name="the-components-of-azure"></a>Azure összetevői
Azure services csoportosítja az Azure portálon, és a különböző visual eszközökkel, például a [Mi az Azure Infographic](https://azure.microsoft.com/documentation/infographics/azure/). A [Azure-portálon](http://portal.azure.com) használata az Azure-szolgáltatások kezelésére.

Ebben a cikkben az egy **másik vállalatnál** hasonló függvény alapján szolgáltatások kapcsolatban, és nagyobb ők részét képező fontos alárendelt szolgáltatások hívásához.  

![Az Azure-összetevők](./media/fundamentals-introduction-to-azure/AzureComponentsIntroNew780.png)   
 *Ábra: Az Azure internetről elérhető, az Azure adatközpontjaiban futó alkalmazásszolgáltatások biztosít.*

## <a name="azure-portal"></a>Azure Portal
Azure rendelkezik nevű webes felületet a [Azure-portálon](http://portal.azure.com) , amely lehetővé teszi a rendszergazdák eléréséhez és Azure-szolgáltatások és szolgáltatások felügyeletéhez.  

## <a name="compute"></a>Számítás
A legalapvetőbb dolgot a felhőplatform does egyik alkalmazások végrehajtására. Az Azure számítási modellek mindegyiknek saját szerepet játszanak.

Ezek a technológiák külön használhatja, vagy az alkalmazás megfelelő foundation létrehozásához szükség szerint kombinálja azokat. A módszer függ, milyen problémákat próbál megoldani.

### <a name="azure-virtual-machines"></a>Azure-alapú virtuális gépek
![Az Azure virtuális gépek ROBBCSIART_TEST](./media/fundamentals-introduction-to-azure/mscsiart_VirtualMachinesIntroNew_12345.png)   
*. Ábra: Az Azure virtuális gépek lehetővé teszi a felhő virtuálisgép-példányok teljes ellenőrzést.*

A virtuális gép létrehozása az igény szerinti szabványos lemezkép akár egy ad meg, hogy is nagyon hasznos lehet. Ezt a módszert gyakran nevezik infrastruktúrák (IaaS), az Azure virtuális gépek biztosít. 2. ábra bemutatja, hogyan fut a virtuális gép (VM) kombinációja és egyet a virtuális merevlemez létrehozása.  

Hozzon létre egy virtuális Gépet, mely VHD-fájlt használja, és a Virtuálisgép-méretet adjon meg.  Majd kell fizetnie az ideje, hogy fut-e a virtuális gép. Után kell fizetnie perc, és csak akkor futása közben, bár egy minimális tárolási kell fizetni, hogy a virtuális merevlemez érhető el. Az Azure kínál készlet tartalmazó virtuális merevlemezeket (más néven "képek") egy rendszerindításra alkalmas operációs rendszer kiinduló gyűjteménye. Ezek közé tartozik a Microsoft és a partner beállítások, például a Windows Server és Linux, SQL Server, Oracle és sok más. Most a virtuális merevlemezek és a képek létrehozása, és majd feltölthetők saját maga. Töltse fel a VHD-k, melyek csak az adatokat és lehessen elérni a futó virtuális gépek is.

A virtuális Merevlemezt tartalmazza, ahol a virtuális gép futása közben végrehajtott módosítások tartósan tárolhatja. Virtuális gép létrehozása az adott virtuális merevlemezből, amikor legközelebb dolgot átvételéhez ahol abbahagyta. Azure Storage blobs, amelyek később döntésről bővebben a biztonsági másolatot a virtuális gépek virtuális merevlemezeket tárolja.  Ez azt jelenti, hogy a redundancia biztosításához a virtuális gépek eltűnnek a hardver- és lemez hibák miatt nem kap. Akkor is másolja az Azure-ból a módosított virtuális Merevlemezt, majd futtassa helyileg.

Az alkalmazás fut, egy vagy több virtuális gépekről, attól függően, hogy milyen módon létrehozta előtt, és úgy dönt, hogy létrehozza a teljesen most.

Ez a felhőalapú informatika általános megközelítése segítségével számos különböző problémákat.

**Virtuálisgép-forgatókönyvek esetén**

1. **Fejlesztési és tesztelési célú** -segítségével lehet őket hozzon létre egy alacsony költségű fejlesztési és tesztelési platform, amely leállíthat befejezése után használja azt. Előfordulhat, hogy hozzon létre, és bármilyen nyelvet és a könyvtárak tetszés használó alkalmazások futtatásához. Ezeket az alkalmazásokat az Azure biztosít, amely akkor is beállíthatja használjon az SQL Server vagy egy másik adatbázis-kezelő rendszer egy vagy több virtuális gépeken futó felügyeleti lehetőségeket használhatja.
2. **Helyezze át az Azure-ba (növekedési-és-shift) alkalmazások** -"Növekedési-és-shift" hivatkozik sokkal áthelyezése az alkalmazás, például egy áttekintésében szeretné használni egy nagy objektumot.  "Növekedési" a VHD-t a helyi adatközpontban, a és "az eltolás mértékét megadó" az Azure-ba, és nincs futtassa.  Általában akkor néhány a munkáját, távolítsa el a más rendszerekkel függőségeket. Ha túl sok, Ehelyett választhatja az 3. lehetőség.  
3. **Az Adatközpont kiterjesztése** -használata Azure futó virtuális gépeket a helyszíni adatközpont kiterjesztése SharePoint vagy más alkalmazásokat. Ezt támogatja, nem lehet létrehozni a Windows-tartományokban a felhőben futó Active Directory az Azure virtuális gépeken. Az Azure Virtual Network (később említett) segítségével összekötni a helyi hálózat és a hálózat az Azure-ban.

### <a name="web-apps"></a>Web Apps
![Azure Web Apps ROBBCSIART_TEST](./media/fundamentals-introduction-to-azure/mscsiart_AzureWebsitesIntroNew_12345.png)   
 *. Ábra: Az Azure Web Apps egy webhely alkalmazást futtat a felhőben anélkül, hogy az alapul szolgáló webkiszolgálókat kezelheti.*

A leggyakoribb dolog, amelyek személyek a felhőben fut webhelyekhez és webes alkalmazásokhoz. Ez lehetővé teszi az Azure virtuális gépek, de továbbra is hagyja meg az ezzel az egy vagy több virtuális gépek és az alapjául szolgáló operációs rendszerek felügyelete. Cloud services webes szerepkörök ehhez, de telepíteni és fenntartani, azokat továbbra is szükséges adminisztratív munka.  Mi történik, ha csak kívánt webhely Ha valaki másnak gondoskodik a felügyeleti feladatok meg?

Ez az pontosan, Web Apps biztosít. A számítási modellt kínál a felügyelt webes környezet az Azure-portálon, valamint a API-k használatával. Egy meglévő webhely alkalmazást áthelyezi változatlan Web Apps, vagy létrehozhat egy új közvetlenül a felhőben. Után egy webhelye fut-e, adja hozzá, vagy példányt eltávolítja, dinamikusan, Azure Web Apps kérések történő terheléselosztásához mindegyik hagyatkoznia. Az Azure Apps kínál egy megosztott lehetőséget, ha a webhely más helyekre rendelkező virtuális gép fut, és a egy normál beállítás, amely lehetővé teszi, hogy a hely a saját virtuális gép futtatásához. A normál beállítás lehetővé teszi a példányok (számítástechnikai power) méretének növelése.

Fejlesztési Web Apps támogatja a .NET, PHP, Node.js, Java és Python, valamint az SQL-adatbázis és az Azure-adatbázis a MySQL relációs tárolására. Azt is beépített támogatást nyújt több népszerű alkalmazások, beleértve a WordPress, a Joomla vagy a Drupal. A cél, hogy egy alacsony költségű, méretezhető és körben használható platformot biztosít webhelyek és webalkalmazások létrehozása a nyilvános felhőben.

**Web Apps Scenarios**

Webes alkalmazások hasznosak lehetnek a vállalatok, a fejlesztők és a webes tervezési szerveinek célja. A vállalatok egy könnyen kezelhető, méretezhető, rendkívül biztonságos és magas rendelkezésre állású megoldás futtatásához jelenléte webhelyek esetén. Állítson be egy webhelyet van szüksége, célszerű az Azure Web Apps indításához és a Cloud Services folytassa, ha egy szolgáltatás, amely nem érhető el kell. Tekintse meg a további hivatkozások, amelyek segítséget nyújtanak a beállítások között válassza a "Számítási" szakasz.

### <a name="cloud-services"></a>Cloud Services
![Azure Cloud Service](./media/fundamentals-introduction-to-azure/CloudServicesIntroNew.png)   
*Ábra: Azure Cloud Services Itt adható meg olyan platformon, mint egy Platformszolgáltatási környezetben kiválóan méretezhető egyéni kód futtatása*

Tegyük fel, hogy hozható létre egy felhő-alkalmazás, amely képes támogatni az egyidejű felhasználók sok nem igényel sok felügyeleti és soha nem működik. Egy meglévő szoftver gyártójához Előfordulhat például, hogy határozott vezessék be a szoftver (SaaS) szolgáltatás épület egy verziója, a felhőben az alkalmazások közül. Vagy előfordulhat, hogy a kezdeti egy fogyasztó alkalmazás várható méretének növelése gyors létrehozása. Ha Azure van építve, mely végrehajtási modell használja?

Az Azure Web Apps lehetővé teszi, hogy az ilyen webalkalmazás létrehozása, de néhány megkötések. Nem rendelkezik rendszergazdai hozzáféréssel, például, ami azt jelenti, hogy tetszőleges szoftver nem telepíthető. Az Azure virtuális gépek rugalmasan, beleértve a rendszergazdai hozzáférést biztosít. Hozhat létre egy jól méretezhető alkalmazás. Azonban saját kezűleg a megbízhatóság és felügyeleti számos aspektusát kezelésére. Mit szeretne olyan beállítás, amely lehetővé teszi az szükséges, de a megbízhatóság és a felügyeleti szükséges munka nagy részét is kezeli a vezérlő.

Ennek az az pontosan mit Azure Cloud Services biztosítja. Ez a technológia terveztek kifejezetten méretezhető, megbízható, és alacsony – a rendszergazdai alkalmazásokhoz, és milyen gyakran hívják Platform (PaaS) szolgáltatás egy példát. A használatához hozzon létre egy alkalmazást a technológia úgy dönt, például a C#, Java, PHP, Python, Node.js, vagy valami mással. A kód végrehajtja a virtuális gépek (néven példányok) Windows Server-verziót futtat.

De virtuális gépeken különbözőek a Azure virtuális gépek létrehozása állók közül. Az egyetlen művelet, Azure maga kezeli azokat, többek között a javítások operációs rendszer telepítése során, és automatikusan új terítésével lett képek. Ez azt jelenti, hogy az alkalmazás nem szabad állapotban webes vagy feldolgozói szerepkör példányát; kell helyette megmarad az Azure felügyeleti lehetőségeket a következő szakaszban leírt egyikében. Azure virtuális gépeken, újraindítása lehetséges, hogy sikertelen is méri. Beállíthatja a felhőszolgáltatások iránti igény miatt több vagy kevesebb példánnyal automatikus létrehozásához. Ez lehetővé teszi, és növekedő kihasználtság kezelésére, majd méretezése vissza, hogy annyi nem fizető kevesebb használat esetén.

Példány létrehozásakor választhat kétféle szerepkör van, mind a Windows Server alapján. A fő különbség a kettő között, hogy egy webes szerepkör példánya fut-e, az IIS, míg a feldolgozói szerepkör példánya nem. Mindkét kezelt ugyanúgy, azonban és gyakori, hogy az alkalmazás is. Például a webes szerepkör példánya lehet, hogy fogadja el a felhasználók által érkező kérések, majd adja meg azokat a feldolgozói szerepkör példánya feldolgozásra. Az alkalmazás felfelé vagy lefelé méretezéséhez kérheti, hogy Azure létrehozása vagy a szerepkör több példányát, vagy állítsa le a meglévő példányt. És hasonló az Azure virtuális gépek, akkor még többletfizetésre volna szükség csak az idő, hogy minden webes vagy feldolgozói szerepkör példánya fut.

**Cloud Services – forgatókönyvek**

Cloud Services csomag ideálisak jelentős mértékű ki kell több vezérlése a platformon, mint az Azure Web Apps által biztosított, de nem kell az operációs rendszer szabályozhatják támogatásához.

#### <a name="choosing-a-compute-model"></a>A számítási modellt kiválasztása
A lap [Azure Web Apps, a Cloud Services és a virtuális gépek összevetése](app-service/choose-web-site-cloud-service-vm.md) további információk a kiválasztása egy számítási modellt biztosít.

## <a name="data-management"></a>Adatkezelés
Alkalmazások adatokra van szükségük, és a különböző típusú alkalmazások kell különböző típusú adatok. Ebből kifolyólag Azure tárolására és kezelésére adatok több különböző módot is kínál. Azure számos tárolási lehetőség biztosítja, de minden nagyon tartós tárolására készültek.  A ezen lehetőségek egyikének esetén mindig az adatok szinkronban között egy Azure-adatközpontban – Ha engedélyezi a Azure-georedundancia használatára történő biztonsági mentés egy másik datacenter legalább 300 miles számítógépnél 6 3 másolatát.     

### <a name="in-virtual-machines"></a>A virtuális gépek
SQL Server vagy egy másik adatbázis-kezelő futtatását létrehozott Azure virtuális gépek virtuális gépen már már említett. Vegye figyelembe, hogy ez a beállítás nem korlátozódik relációs rendszerek; is szabadon NoSQL technológiák, például a MongoDB és Cassandra futtatásához. A saját adatbázis rendszert futtató egyszerű informatikai eljut mi el saját adatközpontjainkba használt-is van szükség, az adott adatbázis-kezelő felügyeleti kezelése.  Egyéb beállítások a Azure több vagy az összes, a felügyeleti kezeli.

Ebben az esetben állapotát, a virtuális gép és minden további adatlemezt létrehozása és feltöltése a blob storage (amely a döntésről bővebben később) által támogatott.  

### <a name="azure-sql-database"></a>Azure SQL Database
![Azure Storage SQL Database](./media/fundamentals-introduction-to-azure/StorageAzureSQLDatabaseIntroNew.png)   

*Ábra: Azure SQL Database biztosít a felhőben felügyelt relációs adatbázis-szolgáltatás.*

Relációs tárolási az Azure biztosít az SQL-adatbázis szolgáltatást. Ne legyen adatlopási elnevezési. Ez eltér a Windows Server felett futó SQL Server által biztosított tipikus SQL-adatbázis.  

Korábbi nevén SQL Azure, az Azure SQL Database biztosít minden a kulcsfontosságú szolgáltatásokat egy relációs adatbázis-kezelő rendszer, atomi tranzakciók egyidejű adatelérési adatok integritását, ANSI SQL-lekérdezések és a megszokott programozási egyszerre több felhasználó modell. Például az SQL Server, SQL-adatbázis segítségével férhetők el Entity Framework, ADO.NET, JDBC és más jól ismert adat-hozzáférési technológiák. A T-SQL nyelv, például az SQL Server Management Studio SQL Server-eszközök mellett a legtöbb is támogatja. Az, hogy ismeri a SQL Server (vagy egy másik relációs adatbázis) SQL-adatbázis használata egyszerű.

Azonban csak az SQL-adatbázis nem a felhő-Ez egy adatbázis-kezelő által egy PaaS-szolgáltatás. Az adatok továbbra is szabályozhatja, és ki férhet, de SQL-adatbázis gondoskodik a felügyeleti grunt munkaelem, például kezelheti a hardver-infrastruktúrát, és automatikusan az adatbázis és az operációs rendszer szoftver naprakészen tartása. SQL-adatbázis is magas rendelkezésre állást biztosít, az automatikus biztonsági mentésekhez, időpontban képességek visszaállítása, és másolja replikálhatja a földrajzi régiók között.  

**SQL-adatbázis forgatókönyvei**

Ha az Azure-alkalmazások (bármely a számítási modell használatával), amelyet a relációs tároló hoz létre, SQL-adatbázis jó választás lehet. A felhő kívül futó alkalmazások is használható ez a szolgáltatás azonban, a többi egyéb forgatókönyvek. Például különböző ügyfélrendszer, beleértve az asztali számítógépek, laptopok, táblagépek és telefonok SQL-adatbázisban tárolt adatok elérhető. És mivel replikáció útján magas rendelkezésre állást biztosít, SQL-adatbázis használata segíthet minimalizálják az állásidőt.

### <a name="tables"></a>Táblák
![Azure Storage Tables](./media/fundamentals-introduction-to-azure/StorageTablesIntroNew.png)  

*. Ábra: Azure-táblákban módszert kínál a strukturálatlan NoSQL adatainak tárolásához.*

Ez a szolgáltatás részeként az "Azure Storage" nevezett nagyobb szolgáltatással is nevezik különböző feltételeket. Ha "tábla", "Azure-táblákban" vagy "storage tables" című minden ugyanaz.  

Nem keverendő nevével és: Ez a technológia nem relációs tárhely biztosításához. Az valójában egy példa egy nosql-alapú módszer kulcs/érték tárolónak hívják. Azure-táblákban lehetővé teszik, hogy egy alkalmazás, például karakterláncok egész számok vagy dátumok különböző típusú tulajdonságok tárolásához. Egy alkalmazás majd lekérheti a csoport tulajdonságai egyedi kulcs biztosítva, hogy a csoport. Összetett műveletek során illesztések nem támogatottak, például táblák kínálnak típusos adatok gyors elérését. Is fontosságúak jól méretezhető, amennyire csak egy terabájt adatok tárolására képes egy táblát. És az egyszerűség kedvéért egyeztetéséhez, táblák általában kevésbé költséges, mint az SQL-adatbázis relációs tároló használatára.

**Táblák forgatókönyvei**

Tegyük fel, hogy szeretne létrehozni az Azure-alkalmazások gyors hozzáférést igénylő adta-e adatokat, lehet, hogy azt a nagyszámú, de nem szükséges összetett SQL-lekérdezések végrehajtásához ezeken az adatokon. Tegyük fel, hogy hoz létre, egy fogyasztó alkalmazás, amelyet a minden felhasználó felhasználói profil adatait tárolja. Az alkalmazás népszerű, lehet, hogy engedélyeznie kell a nagy mennyiségű adatot, de akkor nem nem sok és az adatok tárolásához, majd beolvasása egyszerű módon lesz. Ez az, hogy pontosan milyen típusú forgatókönyvet, ahol Azure táblák teljesen logikus.

### <a name="blobs"></a>Blobok
![Azure Storage Blobs](./media/fundamentals-introduction-to-azure/StorageBlobsIntroNew.png)    
*Ábra: Az Azure BLOB strukturálatlan bináris adatokat biztosít.*  

Azure-blobokat (újra a "Blob tároló" és "Storage Blobsba" csak olyan az ugyanaz) strukturálatlan bináris adatok tárolására szolgál. Például a táblákat Blobok alacsony költségű tárolására szolgál, és lehet, hogy egyetlen blob akkora, mint 1 TB-os (egy terabájtnál). Azure-alkalmazások Azure meghajtót, adja meg az állandó tároló-e csatlakoztatva egy Azure-példányt Windows fájlrendszer blobok módosítását is lehet használni. Az alkalmazás látja a szokásos Windows-fájlok, de a tartalom ténylegesen tárolódnak a blob.

A BLOB storage használják sok más Azure-szolgáltatások (beleértve a virtuális gépek), így az alapértékekkel kezelni tud a munkaterhelések túl.

**Blobok forgatókönyvei**

Videók, nagy fájlok, vagy más bináris tároló alkalmazás egyszerű, olcsó tárolási BLOB is használhat. Blobok általában is használhatók, például a Content Delivery Network, amely előadás később más szolgáltatásokkal együtt.  

### <a name="import--export"></a>Import / Export (Importálás és exportálás)
![Azure Import Export Service](./media/fundamentals-introduction-to-azure/ImportExportIntroNew.png)  

*Ábra: Az Azure Import / Export szállítási egy fizikai merevlemez-meghajtóról vagy az Azure-ral gyorsabb és olcsóbb tömeges adatok importálása vagy exportálása lehetőséget nyújt.*  

Néha szükség nagy mennyiségű adat áthelyezése az Azure. Amely ehhez hosszú ideig akár napokig eltarthat, és a nagy mennyiségű sávszélesség használata. Ebben az esetben használhatja az Azure Import/Export, amely lehetővé teszi, hogy küldje el a Bitlocker-titkosítású 3.5-ös "SATA merevlemez-meghajtók közvetlenül az Azure-adatközpont, ahol Microsoft lesz az adatok átviteléhez a blob-tároló meg.  A feltöltés befejezését követően a Microsoft azonban azt a meghajtó.  Nagy mennyiségű adat blobtárolóból exportált rögzített meghajtókra és visszaküldött mail keresztül is kérheti.

**Forgatókönyv-importálás / exportálás**

* **Nagy adatáttelepítés** -bármikor nagy mennyiségű adat (TB), amely az Azure-bA feltölteni kívánt rendelkezik, az Import/Export szolgáltatás nem gyakran sokkal gyorsabb és lehet, hogy olcsóbbak áthelyezte azt az interneten keresztül. Ha az adatokat a BLOB, például a Table storage vagy egy SQL-adatbázis más űrlapba tud feldolgozni.
* **Archivált adat-helyreállítás** -importálási/exportálási segítségével rendelkezik Microsoft átviteli nagy mennyiségű adat tárolja az Azure Blob Storage és a tárolóeszköz között küldött és rendelkezik eszköz kézbesítését a kívánt helyre. Ez némi időt vesz igénybe, mert nincs vész-helyreállítási jó választás. Érdemes az archivált adatok gyors eléréséhez nem szükséges.

### <a name="file-service"></a>File Service
![Azure File Service](./media/fundamentals-introduction-to-azure/FileServiceIntroNew.png)    
*Ábra: Az Azure a Fájlszolgáltatások olyan SMB \\ \\server\share elérési utak a felhőben futó alkalmazások.*

A helyszíni, gyakori, hogy elérhető a Server Message Block (SMB) protokoll használatával a file storage nagy mennyiségű egy \\ \\Server\share formátumban. Azure most már rendelkezik egy szolgáltatás, amely lehetővé teszi a protokoll használatát a felhőben. Az Azure-ban futó alkalmazások segítségével fájlokat megosztani a jól ismert fájlrendszer API-k például ReadFile és WriteFile használatával virtuális gépek között. Emellett a fájlokat is elérhető, egyszerre egy REST-felület, amely lehetővé teszi a virtuális hálózat is beállításakor a megosztások elérésére a helyszíni keresztül. Az Azure Files a blob szolgáltatás épül, ezért a azonos rendelkezésre állási, tartóssága, méretezhetőség és georedundancia Azure Storage épített örökli.

**Az Azure Files forgatókönyvei**

* **Meglévő alkalmazások áttelepítése a felhőbe** -a könnyebben áttelepíteni a helyszíni az alkalmazás részei közötti adatmegosztásra fájlmegosztások használó alkalmazások a felhőbe. Minden virtuális gép csatlakozik a fájlmegosztáshoz és majd az olvasási és írási fájljait, ahogy azt elleni egy helyi fájl megosztásához.
* **Megosztott Alkalmazásbeállítások** -elosztott alkalmazás általános felépítését, hogy a konfigurációs fájlok egy központi helyen, ha azok elérhetők a sok különböző virtuális gépekről. Ezeket a konfigurációs fájlokat egy Azure-fájlmegosztáson tárolódnak, és minden alkalmazáspéldányok olvasni. A beállítások a REST-felület, amely lehetővé teszi a konfigurációs fájlokat a világszerte hozzáférést keresztül is kezelhetők.
* **Diagnosztikai megosztás** - mentheti, és diagnosztikai fájlmegosztásra például naplók, metrikák és összeomlási memóriaképek. Ezeket a fájlokat az SMB- és a REST-felületen keresztül elérhető segítségével alkalmazások legyen használva elemzésére szolgáló eszközöket különböző feldolgozása és a diagnosztikai adatok elemzése.
* **Fejlesztési/tesztelési/Debug** – amikor a fejlesztők és rendszergazdák dolgozunk a virtuális gépek a felhőben, gyakran kell eszközök vagy segédprogramok készlete. Telepítése és terjesztése ezeket a segédprogramokat mindegyik virtuális gépre sok időt vesz igénybe. Az Azure Files a fejlesztők és a rendszergazda tárolhatnak a kedvenc eszközök fájlmegosztáson, és minden virtuális gép csatlakozni hozzájuk.

## <a name="networking"></a>Hálózat
Azure ma elosztva a világ számos adatközpontokban futtatja. Az alkalmazás futtatásakor, vagy az adatok tárolásához, kiválaszthatja egy vagy több ezek adatközpontok használatára. Ezek a szolgáltatások az alábbi különböző módokon adatközpontokban is kapcsolódhat.

### <a name="virtual-network"></a>Virtual Network
![VirtualNetwork](./media/fundamentals-introduction-to-azure/VirtualNetworkIntroNew.png)   

*Ábra: Virtuális hálózatok biztosít egy magánhálózaton a felhőben így különböző szolgáltatások egymáshoz működik, vagy a helyszíni erőforrások Ha úgy konfigurálja a VPN létesítmények közötti kapcsolat.*  

Egy hasznos használata a nyilvános felhők módja kezeli a saját adatközpont kiterjesztése.

Virtuális gépek igény szerint hozhat létre, mert távolítsa el őket (és fizető leállítása) azok már nincs szükség, ha csak akkor, ha azt szeretné, a számítástechnikai power lehet. Azure virtuális gépek lehetővé teszi, hogy a SharePoint, az Active Directory és más jól ismert helyszíni szoftver futó virtuális gépeket létrehozni, mert ez a megközelítés segítségével és az alkalmazások már rendelkeznek.

Ahhoz, hogy ez valóban hasznos, azonban a felhasználók kell kerülnie tudja kezelni az alkalmazásokat, mintha a saját adatközpont futtatnák. Ennek az az pontosan Azure Virtual Network lehetővé teszi. Egy VPN-átjáró eszköz használatával, a rendszergazda állíthat be a virtuális magánhálózat (VPN) a helyi hálózat és a virtuális hálózat az Azure-ban telepített virtuális gépek között. A felhő virtuális gépek a saját IP-v4-címek hozzárendelése, mert úgy tűnik, mintha a saját hálózaton. A szervezeti felhasználók férhetnek hozzá az alkalmazások virtuális gépek, mintha a helyi futtatnák tartalmaz.

Tervezési és a virtuális hálózat, amely megfelelő létrehozásával kapcsolatos további információkért lásd: [virtuális hálózati](virtual-network/virtual-networks-overview.md).

### <a name="express-route"></a>Express Route
![ExpressRoute](./media/fundamentals-introduction-to-azure/ExpressRouteIntroNew.png)   

*Ábra: ExpressRoute egy Azure virtuális hálózatot használ, de kapcsolatok gyorsabban dedikált sorok helyett a nyilvános interneten keresztül irányítja.*  

Ha további sávszélesség vagy biztonságos, mint a kapcsolat biztosíthat egy Azure virtuális hálózatra van szüksége, megtekintheti az ExpressRoute. Bizonyos esetekben ExpressRoute is mentheti, pénzt. Továbbra is szüksége lesz egy virtuális hálózatot az Azure-ban, de a hely és az Azure közötti kapcsolat, amely nem halad át a nyilvános internethez dedikált kapcsolatot használ. Ez a szolgáltatás használatához szüksége lesz a hálózati szolgáltatóhoz, vagy exchange szolgáltató kötött.

Beállítás egy ExpressRoute-kapcsolat több időt igényel, és tervezési, ezért érdemes indítsa el a telephelyek közötti VPN, majd telepítse át az ExpressRoute-kapcsolatot.

ExpressRoute kapcsolatos további információkért lásd: [ExpressRoute műszaki áttekintés](expressroute/expressroute-introduction.md).

### <a name="traffic-manager"></a>Traffic Manager
![TrafficManager](./media/fundamentals-introduction-to-azure/TrafficManagerIntroNew.png)   

*Ábra: Az Azure Traffic Manager teszi globális forgalom továbbításához a szolgáltatás intelligens szabályok alapján.*

Ha több adatközpontot az Azure alkalmazás fut, a felhasználók által érkező kérések intelligens módon irányítja az alkalmazás példányai között használhatja Azure Traffic Manager. Is irányíthatja a forgalmat a szolgáltatás nem fut az Azure-ban, amíg az interneten.  

Csak egy részét a világ felhasználóival az Azure-alkalmazások futtatása előfordulhat, hogy csak egy Azure-adatközpontban. Az alkalmazás a felhasználók a világ többi részén Elszórva azonban valószínűbb futtatható több adatközpontot, talán még az összes. A második esetben probléma szembesülhetnek: hogyan tegye intelligens módon közvetlen alkalmazáspéldányok felhasználók? A legtöbb esetben érdemes a legközelebbi, datacenter elérésére, mivel így azokat a legjobb válaszidő minden felhasználóhoz. De mi történik, ha az az alkalmazás ezen példányát túlterhelt vagy nem érhető el? Ebben az esetben célszerű át tudja irányítani a kérelmet automatikusan egy másik datacenter. Ez az pontosan, mi történik az Azure Traffic Manager által.

Az alkalmazás tulajdonosa adja meg, hogyan legyenek irányítva felhasználók által érkező kérések adatközpontok szabályokat határozza meg, majd támaszkodik a Traffic Manager hajthat végre ezeket a szabályokat. Felhasználók például előfordulhat, hogy szokásos módon átirányítja a legközelebbi Azure-adatközpontban, de beolvasása küldhet egy másikat, ha a válaszidőt azok alapértelmezett datacenter meghaladja a más adatközpontok válaszidejét. Számos felhasználóval rendelkező globálisan elosztott alkalmazásokhoz hogy egy beépített szolgáltatás nem tudja kezelni a fenti problémák akkor hasznos.

A TRAFFIC manager Directory szolgáltatás (DNS) használja a útvonal felhasználóknak, hogy végpontok, de további forgalom nem halad keresztül Traffic Manager a kapcsolat létrehozása után. Így a Traffic Manager szűk keresztmetszet, amely a szolgáltatás kommunikációs lelassíthatják nem.

## <a name="developer-services"></a>Fejlesztői szolgáltatások
Azure számos eszközök segítségével a fejlesztők és informatikai szakemberek számára hozzon létre és kezelheti az alkalmazásokat a felhőben.  

### <a name="azure-sdk"></a>Azure SDK
Vissza a 2008 a legelső előzetes verzióját Azure csak a .NET development támogatott. Napjainkban azonban hozhat létre Azure-alkalmazások lényegében bármilyen nyelven. A Microsoft jelenleg biztosít nyelvspecifikus SDK-k .NET, Java, PHP, Node.js, Ruby és Python. Egy általános Azure SDK-t, amely alapszintű támogatást nyújt bármely nyelvhez, például a C++ is van.  

Ezek az SDK-k segítségével létrehozása, telepítése és kezelése az Azure-alkalmazások. Azok elérhetők vagy a [www.microsoftazure.com](https://azure.microsoft.com/downloads/) vagy GitHub, és a Visual Studio és az eclipse-ben használható. Azure parancssori eszközöket, amelyekkel a fejlesztők bármely szerkesztő vagy a fejlesztői környezetet, beleértve a Linux és a Macintosh-rendszerekből Azure-alkalmazások telepítése a eszközök is biztosít.

Segít az Azure-alkalmazások létrehozását, valamint ezek SDK-k is nyújt, amelyek segítenek ügyfélkódtáraival létre szoftver, amely Azure-szolgáltatásokat használ. Például akkor lehet, hogy létre egy alkalmazás, amely beolvassa és az Azure BLOB, vagy hozzon létre olyan eszköz, amely telepíti az Azure-alkalmazások az Azure felügyeleti felületen keresztül.

### <a name="visual-studio-team-services"></a>Visual Studio Team Services
A Visual Studio Team Services alhálózatnév marketing kiterjedő egy szám szolgáltatásokat, amelyek segítenek az Azure-alkalmazások fejlesztéséhez.

-A félreértések elkerülése érdekében nem meg a Visual Studio központi vagy a Web-alapú verzióját. A Visual Studio helyi futó példányát kell. De számos más eszközt, amely nagyon hasznos lehet.

Az üzemeltetett forrás ellenőrző rendszer verziókezelést és a munka elem követési biztosít a Team Foundation Service nevű tartalmazza.  Akkor is használható Git verziókezelést Ha jobban szeret. És a projekt által használt adatforrás vezérlő rendszer eltérőek lehetnek. Létrehozhat korlátlan titkos csapatprojektek érhető el a bárhol a világon.  

A Visual Studio Team Services egy terheléselosztási tesztelési szolgáltatást biztosít. Ön is végrehajthatja a terhelés tesztek létrehozása a Visual Studio, a felhőben található virtuális gépeken. Megadhatja, hogy a vizsgálat során, és a Visual Studio Team Services automatikusan betölteni kívánt felhasználók teljes száma határozza meg, hány ügynök van szükség, a szükséges virtuális gépek léptetési és a betöltés tesztek végrehajtása. Ha az MSDN-előfizető, szabad felhasználói perc minden hónap tesztelés terhelés ezer kap.

A Visual Studio Team Services is biztosít, szolgáltatásokat, például a folyamatos integrációt alkot, Kanban modulok és virtuális team helyiségekben gyors fejlesztési támogatása.

**Visual Studio Team Services Scenarios**

A Visual Studio Team Services rendszer vállalatok közötti együttműködés világszerte, és nem jó választás az infrastruktúra már rendelkezik az ehhez az. A telepítő percben beolvasása, válassza ki a forrásrendszerben vezérlő, és indítsa programozás és felépítése adott napon.  A team eszközök olyan helyen nyújtanak koordinációs, és együttműködés és további eszközöket biztosít az elemzés tesztelésére és hangolására gyorsan az alkalmazás szükséges.

De egy a helyszíni rendszer már rendelkező szervezeteknek meg, hogy hatékonyabb a Visual Studio Team Services új projektek tesztelheti.   

### <a name="application-insights"></a>Application Insights
![Application Insights](./media/fundamentals-introduction-to-azure/ApplicationInsights.png)  

*Ábra: Az Application Insights figyelők teljesítmény- és használati az élő web vagy az eszköz alkalmazás.*

Ha az alkalmazás - tette közzé, hogy a mobileszközökön, az asztali számítógépek és a webböngésző - fut az Application Insights megtudhatja, teljesítményét és a felhasználók tevékenységeit vele. Az összeomlásokat és a lassú válasz számát tartja, riasztás, ha a ábra kereszt-elfogadhatatlan küszöbértékeket, és segítséget nyújtanak a problémák diagnosztizálásához.

Egy új szolgáltatás fejlesztésekor tervezze meg a felhasználók a sikeresség felméréséhez. Használati minták elemzésével, mi működik a legjobban az ügyfeleknek megértéséhez, és javíthatja az alkalmazás minden fejlesztési ciklusban.

Bár az Azure-ban található, az Application Insights-alkalmazások esetén is be- és kikapcsolását Azure széles és az egyre növekvő számos működik. J2EE és az ASP.NET web apps ismertetnek, valamint iOS, Android, os x és Windows-alkalmazások. Telemetriai adatok elemzése és az Application Insights szolgáltatás az Azure-ban jelenik meg az alkalmazás-val készült SDK küldi.

Ha azt szeretné, további speciális elemzés, exportálja a telemetriai adatok adatfolyam adatbázishoz, vagy a Power bi-ban, vagy bármely más eszközök.

**Application Insights-forgatókönyvek**

Ha az alkalmazások fejlesztéséhez. Előfordulhat, hogy a webes alkalmazás vagy egy eszköz-alkalmazást, vagy egy eszköz alkalmazást a webes háttérből.

* Az alkalmazás a teljesítmény hangolására, hogy közzététele után, vagy amíg módban betölteni tesztelése.  Az Application Insights telemetria összesíti az összes telepített példányát, és diagramok válaszidejét, kérelem kivételek száma, függőség válaszidejét és más teljesítménymutatók nyújt. Ezek segítenek az alkalmazás teljesítmény hangolására. Több jelentésének kód beszúrásához rendszerspecifikus adatokat, ha esetleg szükség lenne rá.
* Észleli, és az élő alkalmazásban problémák diagnosztizálásához. E-mailben is riasztásokat kaphat, ha teljesítménymutatók telephelyek közötti elfogadható küszöbértékeket. Használatával megvizsgálhatja a felhasználói munkamenetek, például a kérelemhez, amely kivételt okozott a megjelenítéséhez.
* Annak ellenőrzéséhez, a sikeres egyes új szolgáltatások használatának nyomon követése. Amikor egy új felhasználó szövegegység, tervezze meg mennyi szolgál, és hogy felhasználók várható céljaik. Az Application Insights lehetővé teszi az alapvető használati adatok, például a weblap nézetek, és nyomon követheti a felhasználói élmény részletesebben kódot is beszúrhat.

### <a name="automation"></a>Automatizálás
Nem szeret vesztegessen ennek során manuális ugyanazzal az eljárással újra és újra időt. Azure Automation szolgáltatásbeli létrehozása, figyeléséhez, kezeléséhez és központi telepítése az Azure környezetben erőforrások egy megoldást kínál.  

Automatizálási "runbook", amely használja a Windows PowerShell-munkafolyamatok (és csak rendszeres PowerShell) a színfalak használja. Runbookok úgy van kialakítva, hogy a felhasználó beavatkozása nélkül hajtható végre. PowerShell-munkafolyamatok lehetővé teszi, hogy egy parancsfájl menet pontokon menteni az állapotát. Ha hiba lép fel, nem kell végrehajtania az elejétől parancsfájl futtatásához. Később is újraindíthatja azt az utolsó ellenőrzőpont. Ezáltal a parancsfájl minden lehetséges hibakezeléshez próbált munka nagy.

**Automation-forgatókönyvek**

Azure Automation szolgáltatásbeli érdemes az Azure-ban a manuális, hosszan futó, hibákhoz vezethet, és gyakran ismétlődő feladatok automatizálására.

### <a name="api-management"></a>API Management
Létrehozása és közzététele az internetes alkalmazás-programozó felületek (API) módja a közös szolgáltatások alkalmazásokhoz. Ha ezek a szolgáltatások resellable (például időjárási adatokat), a szervezetek lehetővé tehetik a más harmadik felek díj ellenében azonos szolgáltatások eléréséhez. Mivel további partnereknek méretezni, általában szüksége optimalizálása és hozzáférés szabályozása.  Egyes partnerek még akkor is előfordulhat, hogy kell az adatokat, más formátumban.

Az Azure API Management megkönnyíti a szervezetek biztonságosan és léptékű partnerek, az alkalmazottak és a külső fejlesztők API-k közzétenni. Egy másik API-végpont biztosít, és a tényleges végpontja hívására ugyanakkor biztosítható a szolgáltatásokat, mint a gyorsítótár, transformation, sávszélesség-szabályozás, hozzáférés-vezérlés és analytics összesítési proxyként funkcionál.

**API-kezelési forgatókönyveket**

Tegyük fel, a vállalat rendelkezik-e azon eszközök, hogy minden szeretne-e ezen a számon egy központi szolgáltatás adatok – például egy olyan szállítási vállalat rendelkezik olyan eszközökkel, a minden teherautó útközben.  Biztosan a vállalat hozzon létre a rendszer külön teherautók nyomon, hogy azok megbízható előrejelzése és kézbesítési idejének szeretne. Azt tudja hány teherautók rendelkezik, és tervezze meg megfelelően.  Minden egyes teherautó visszahívja egy központi helyen az Elhelyezés és sebességét, adatok, és lehet, hogy több eszköz szükséges.

A szállítási vállalat ügyfél valószínűleg akkor is előnyös a pozicionáló adatok.  Az ügyfelek használhatják azt tudja, milyen távolságban termékek van továbbítani, ahol azok elakadnak, mennyi azok bizonyos útvonalon fizető (Ha mi fizetett szállítási együtt). A szállítási vállalati már összesíti az adatokat, ha sok ügyfél lehet, hogy díj ellenében azt.  De majd a szállítási vállalati lehetővé teszik az adatokat biztosít. Miután az ügyfél számára hozzáférést nyújtanak, azokat nem lehet szabályozhatják, hogy az adatok milyen gyakran le kell kérdezni. Milyen adatok való hozzáférés szabályaik biztosítása érdekében rendelkeznek. Ezek a szabályok összes kellene beépíthető a külső API. Ez azért, ahol az API Management segítségével.  

## <a name="identity-and-access"></a>Identitás és hozzáférés
Identitás használata a legtöbb alkalmazás részét képezi. Egy felhasználó ismerete lehetővé teszi, hogy a döntse el, hogyan azt kell működjön együtt, hogy a felhasználó egy alkalmazást. Azure-szolgáltatások nyomon követéséhez, valamint a már használhat identitástárolók integrálva identitás biztosítja.

### <a name="active-directory"></a>Active Directory
A legtöbb címtárszolgáltatások, például a Azure Active Directory-felhasználók és a szervezetek tartoznak kapcsolatos információkat tárolja. Lehetővé teszi a felhasználóknak a bejelentkezéshez, majd úgy is dönthetnek, alkalmazások kell igazolnia az identitását jogkivonatokkal látott el. Lehetővé teszi a Windows Server Active Directoryval a helyszínen a helyi hálózaton futó felhasználói adatok szinkronizálása. A mechanizmusok és az Azure Active Directory által használt adatok formátumok nem azonos a Windows Server Active Directory, a függvény végrehajtása is nagyon hasonló.

Fontos tudni, hogy Azure Active Directory által a felhőalapú alkalmazásokhoz elsősorban használatra szolgál. Lehetséges például, vagy más felhőalapú platformokon a Azure-ban futó alkalmazások használhatják. Microsoft saját felhőalapú alkalmazásokhoz, például az Office 365-ben is használják. Ha azt szeretné, az Adatközpont kiterjeszti a felhőbe Azure virtuális gépek és az Azure Virtual Network, azonban Azure Active Directoryban nem a megfelelő választás. Ehelyett érdemes futtatni a Windows Server Active Directory virtuális gépeken.

Ahhoz, hogy a hozzáférést a benne található alkalmazások, Azure Active Directory egy RESTful API hívása az Azure Active Directory Graph biztosít. Ez az API lehetővé teszi, hogy bármely platformhoz hozzáférés directory-objektumok és a közöttük lévő kapcsolatok futó alkalmazások.  Például egy engedéllyel rendelkező alkalmazás előfordulhat, hogy segítségével az API többet tudhat meg a felhasználó, a csoportok tagja, és más információkat. Alkalmazások is láthatja, felhasználók a társadalombiztosítási graph-bérbeadása, azok több intelligens módon dolgozni személyek közötti kapcsolatok közötti kapcsolatokat.

Ez a szolgáltatás Azure Active Directory hozzáférés-vezérlés, egy másik képessége megkönnyíti a azonosító adatokat a Facebook, Google, a Windows Live ID és egyéb népszerű identitás-szolgáltatóktól fogadásához az alkalmazáshoz. Ahelyett, hogy az alkalmazás különböző adatok formátumok és ezek a szolgáltatók által használt protokollt igénylő, hozzáférés-vezérlés az eszköz az összes olyan egyetlen közös formátumra. Azt is lehetővé teszi, hogy egy alkalmazás egy vagy több Active Directory-tartományok bejelentkezések fogadja el. Például az egy SaaS-alkalmazáshoz biztosító szállító előfordulhat, hogy használja az Azure Active Directory hozzáférés-vezérlés lehetőséget nyújt a felhasználóknak minden az ügyfelei egyszeri bejelentkezést az alkalmazás.

Directory szolgáltatások egy alapvető megerősítő a helyszíni számítási. Nem lehet meglepő, hogy azok viselkedése is fontos a felhőben.

### <a name="multi-factor-authentication"></a>Többtényezős hitelesítés
![Azure Multi-Factor Authentication](./media/fundamentals-introduction-to-azure/MFAIntroNew.png)   

*. Ábra: A multi-factor Authentication azonosítási egynél több űrlap ellenőrzése az alkalmazás a funkcionalitást biztosítja*

Biztonsági mindig fontos. Többtényezős hitelesítés (MFA) segítségével biztosítja, hogy csak azok a felhasználók maguk hozzáférési fiókjukat. Többtényezős Hitelesítést (más néven a kéttényezős hitelesítés vagy "2FA") esetén a felhasználóknak megadniuk a két ezeket a felhasználói bejelentkezéseket és tranzakciókat identitás ellenőrzése mindhárom módszerét.

* Valami tudja (általában jelszót)
* Valami (megbízható eszközzel rendelkezik, amely nem egyszerű ismétlődik, például a telefon)
* Valami áll (biometria)

Ezért amikor a felhasználó bejelentkezik, is szüksége van rájuk is igazolják az identitásukat egy mobilalkalmazás, telefonhívást vagy SMS, valamint a jelszavát, hogy. Alapértelmezés szerint a Azure Active Directory támogatja a felhasználói bejelentkezések a egyetlen hitelesítési módszerként a jelszavak használatát. MFA együtt az Azure AD vagy az egyéni alkalmazások és könyvtárak is használhatja a multi-factor Authentication SDK használatával. Használhatja azt a helyszíni alkalmazások együtt multi-factor Authentication kiszolgáló használatával.

**MFA-forgatókönyvek**

Bejelentkezés a védelmet a bizalmas fiókokat vehetnek célba például banki bejelentkezések és ahol jogosulatlan bejegyzés rendelkezhetnek a költségeket, magas pénzügyi vagy szellemi tulajdonság forráskódjához hozzáfér.   

## <a name="mobile"></a>Mobiltelefon
A mobileszközön lévő alkalmazások létrehozásakor Azure segíthet a felhőben tárolt adatok hitelesíti a felhasználókat, és leküldéses értesítések küldéséhez nagyfokú egyéni kód írása nélkül.

A virtuális gépek, a Cloud Services és a Web Apps mobilalkalmazások a háttérrendszer alapértékekkel hozhat létre, amíg tölthet sokkal kevesebb időt írása az alapul szolgáló szolgáltatás-összetevők Azure-szolgáltatások használatával.

### <a name="mobile-apps"></a>Mobile Apps
![Mobile Apps](./media/fundamentals-introduction-to-azure/MobileServicesIntroNew.png)

*. Ábra: A mobilalkalmazások általában alkalmazások, amelyek kommunikáljon a mobileszközök által igényelt funkciókat biztosítja.*

Az Azure Mobile Apps biztosít számos hasznos funkciót, amely takaríthat meg időben a mobilalkalmazás háttérkiszolgálón felépítése közben. Ez lehetővé teszi, hogy egyszerű kiépítés és az SQL-adatbázisban tárolt adatok kezelését. Kiszolgálóoldali kód könnyen használhatja például a blob storage vagy a MongoDB további adatok tárolási lehetőségek. Mobile Apps támogatja az értesítéseket, abban az esetben, ha bizonyos esetekben használhatja a Notification Hubs alább leírtak szerint.  A szolgáltatás egy REST API-t, amely a mobilalkalmazás munkavégzésben telefonon is rendelkezik. Mobile Apps elvégezhessék a hitelesítést a Microsoft és az Active Directory – felhasználók és más jól ismert Identitásszolgáltatók, például a Facebook, a Twitter és a Google is biztosít.   

Egyéb Azure-szolgáltatásokat, mint a Service Bus és feldolgozói szerepkörök használja, és a helyszíni rendszerekhez történő csatlakozáshoz. 3. fél bővítményei az Azure-áruházból (például az e-mailek SendGrid) nyújt további szolgáltatásokat is felhasználhat.

Az Android, iOS, HTML/JavaScript, Windows Phone és Windows Store natív ügyfél függvénytárainak könnyebben minden nagyobb mobilplatform az alkalmazások fejlesztéséhez. A REST API lehetővé teszi a Mobile Services adatok és hitelesítési funkcióinak használatát az alkalmazások különböző platformokon. Egyetlen mobilszolgáltatás biztonsági több ügyfél alkalmazásokat, egy egységes felhasználói élményt biztosíthat az eszközön.

Azure már támogatja a jelentős mértékű, mert kezelheti a forgalmat, amint az az alkalmazás népszerűbbnek válik.  Figyelés és naplózás a problémák elhárításához, és a teljesítmény kezelése érdekében támogatottak.

### <a name="notification-hubs"></a>Notification Hubs
![NotificationHubs](./media/fundamentals-introduction-to-azure/NotificationHubsIntroNew.png)  

*. Ábra: A Notification Hubs gyakran alkalmazások, amelyek kommunikáljon a mobileszközök által igényelt funkciókat biztosítja.*

Értesítések az Azure Mobile Apps kódjában írhat, amíg a Notification Hubs arra optimalizálták, hogy nagy mértékben testre szabott leküldéses értesítések több millió szórás percen belül.  Nincs foglalkoznia az adatait, például a mobil szolgáltatónként vagy a készülék gyártójához. Egy vagy több millió felhasználó egyetlen API-hívással Megcélozhat.

Notification hubs használatával bármilyen háttérrendszerből együttműködve célja. Azure Mobile Apps, a felhőben futó bármely szolgáltató használható egyéni háttérrendszer vagy egy helyszíni háttér is használhatja.

**Értesítési központ forgatókönyvek** ahol játékosok tartott viszont mobil játék írna, ha szeretne értesíteni player 2 adott player 1 befejezte a saját kapcsolja. Ha ez mindössze annyit kell tennie, használhatja a Mobile Apps csak. De ha 100 000 felhasználó kellett játék a, és elküldi egy időpontot, bizalmas a különleges ajánlat mindenki, a Notification Hubs a megfelelőbb választás.

Legfrissebb hírek küldhet, eseményeket és a termék bejelentési értesítések több millió felhasználónak sport és kis késésű. Vállalatok értesítheti új idő-és nagybetűket indított kommunikációval kapcsolatban, például az értékesítési érdeklődők az alkalmazottak számára, az alkalmazottaknak nem kell e-mail vagy más alkalmazásokhoz, és azonnal tájékozódjon, folyamatosan kereséséhez. Egy-egyszer használatos jelszavak a többtényezős hitelesítés is küldhet.

## <a name="back-up"></a>Biztonsági mentés
Minden vállalati kell biztonsági mentése és visszaállítása az adatokat. Használhatja az Azure biztonsági mentése és visszaállítása az alkalmazás a felhőben, vagy a helyszínen. Azure biztonsági mentés típusától függően különböző lehetőséget kínál.

### <a name="site-recovery"></a>Site Recovery
Az Azure Site Recovery (korábbi nevén Hyper-V helyreállítás-kezelő) segítségével védi a fontos alkalmazások a replikáció és a helyreállítási összehangolása a különböző helyek között. A Site Recovery lehetővé teszi a Hyper-v, VMWare vagy SAN-alapú saját másodlagos helyre, egy tárhelyszolgáltató helyre vagy az Azure-alkalmazások védelmét, és elkerülheti a költségeket és összetett létrehozása és kezelése a saját másodlagos helyre. Azure titkosítja az adatokat, és a kommunikációs és rendelkezik a túl engedélyezheti a titkosítást az adatokat nyugalmi lehetőséget.

Folyamatosan figyeli a szolgáltatás állapotát, és segítségével automatizálhatja a rendezett helyreállítási szolgáltatások, az elsődleges adatközpont egy hely leállás esetén. A virtuális gépek már előkészítve indíthatók el a szolgáltatás mielőbbi helyreállítása érdekében, akár összetett, többrétegű munkaterhelések esetében is.

A Site Recovery működik együtt a meglévő technológiák, például a Hyper-V replika, a System Center és SQL Server Always On. Tekintse meg [Azure Site Recovery áttekintését](site-recovery/site-recovery-overview.md) további részleteket.

### <a name="azure-backup"></a>Azure Backup
![Azure Backup](./media/fundamentals-introduction-to-azure/AzureBackupIntroNew.png)  

*. Ábra: Az Azure Backup lemezein lévő adatokat a helyszíni Windows Server kiszolgálókról kiterjeszti a felhőbe.*  

Azure biztonsági mentés kiterjeszti a felhőbe a Windows Server rendszert futtató helyszíni kiszolgálók lemezein lévő adatokat. Kezelheti a biztonsági másolatokat közvetlenül a Windows Server 2012, Windows Server 2012 Essentials vagy a System Center 2012 – Data Protection Manager biztonsági mentési eszközök. Másik megoldásként egy speciális biztonságimásolat-készítő ügynök is használhatja.

Adatok azért biztonságosabb, mert a biztonsági mentések átvitele előtt titkosított és tárolt titkosított az Azure és a feltöltött tanúsítvány által védett. A szolgáltatás ugyanazon redundáns és magas rendelkezésre állású adatvédelmi található az Azure Storage használja.  Is biztonsági másolatot készíteni fájlok és mappák rendszeres ütemezés szerint vagy azonnal, vagy teljes vagy növekményes biztonsági mentések futtatása. Adatok biztonsági mentése a felhőbe, után jogosult felhasználók egyszerűen helyre lehet állítani kiszolgálói biztonsági másolatok. Is biztosít, konfigurálható az adatmegőrzési házirendek, az adattömörítés és az adatok átvitele sávszélesség-szabályozás, kezelheti a költség, tárolására és az adatok átviteléhez.

**A forgatókönyvek az Azure Backup szolgáltatásra**

Ha már használja a Windows Server vagy a System Center, Azure biztonsági mentés egy természetes megoldás a kiszolgálók fájlrendszer, a virtuális gépek és az SQL Server-adatbázisok biztonsági mentése.  A titkosított, ritka és tömörített fájlok működik. Nincsenek bizonyos korlátozások mellett, így akkor [az Azure biztonsági mentés szükséges előfeltételek ellenőrzése](http://technet.microsoft.com/library/dn296608.aspx) első.

## <a name="messaging-and-integration"></a>Üzenettovábbítás és integráció
Függetlenül attól, milyen műveletet a kód gyakran kell más kód kommunikálni.  Bizonyos helyzetekben szükség van csak a alapvető aszinkron üzenetkezelési. Más esetekben összetettebb kapcsolati szükség. Azure biztosít az ilyen problémák megoldására fejlesztésében többféle formában. 5. ábra a lehetőségeit szemlélteti.

### <a name="queues"></a>Üzenetsorok
![Azure Service Bus Relay](./media/fundamentals-introduction-to-azure/QueuesIntroNew.png)

*. Ábra: A várólisták laza alkalmazás részei között csatolási engedélyezése, és lehetővé teszi a méretezés.*  

Egy egyszerű ötlet Queuing: egy alkalmazás egy üzenet helyezi a sorhoz, és az üzenet egy másik alkalmazás végül olvasható. Ha az alkalmazásnak csak az egyszerű szolgáltatás, Azure várólisták lehet a legjobb választás.

Az Azure nőtt időbeli módjával Azure tárolási sorok és a Service Bus-üzenetsorok szolgáltatásokhoz hasonló Üzenetsor-kezelés. A lehetnek, miért kellene használni kívánt közül történő ismertetnek a viszonylag műszaki papír [Azure várólisták és a Service Bus-üzenetsorok - az képest és ellentétben](http://msdn.microsoft.com/library/azure/hh767287.aspx).  Sok esetben vagy működik.

**Várólista forgatókönyvek**

Egy közös várólisták ma használata, hogy a webes szerepkör példánya egy feldolgozói szerepkör példánya az azonos Felhőszolgáltatások alkalmazásban folytatott kommunikációhoz.

Tegyük fel, hogy megosztható videó az Azure alkalmazás létrehozása. Az alkalmazás egy webes szerepkör, amely lehetővé teszi, hogy a felhasználók feltöltés és figyelési videók, a C# nyelven íródtak, amely a különböző formátumokba feltöltött videó megvalósított feldolgozói szerepkörrel együtt a futó PHP-kód áll.

A webes szerepkör példánya új videó lekérdezi egy felhasználó, amikor azt a videó tárolása egy blobot, majd üzenetet küldeni a feldolgozói szerepkör keresztül szólítja fel, akkor ez a videó új megkeresése. A feldolgozói szerepkör példány-it nem számít, mely egy lesz majd olvassa el az üzenetet az üzenetsorból és hajthat végre a szükséges videó fordítások a háttérben.

Az aszinkron feldolgozás szerkezetének kialakítása egy alkalmazás, így lehetővé teszi, és azt is megkönnyíti az alkalmazás skála óta webes szerepkörpéldányokat és a szerepkör feldolgozópéldányok száma egymástól függetlenül eltérőek lehetnek. Használhatja a várólista mérete kiindulópontként méretezési feldolgozói szerepkörök növekszik és csökken. Túl magas, és a további szerepkörök hozzáadásához. Ha annak mérete kisebb, csökkentheti a futó szerepkörök kevesebbet költeni számát.  

Használhatja a ugyanilyen mintájú az alkalmazás számos különböző részei között, még akkor is, ha a webes és feldolgozói szerepkörök nem használnak.  Ez lehetővé teszi, hogy a kijelzők mindkét oldalon a várólista növekszik és csökken, igény szerint méretezheti és feldolgozási időt igényel.

### <a name="service-bus"></a>Service Bus
Futtatja a felhőben, az adatközpont, a mobil eszköz vagy valahol máshol, hogy alkalmazásokat kell interakciót. Azure Service Bus célja, hogy bárhonnan adatcserében lényegében futó alkalmazások.

A korábban ismertetett várólisták (egy az egyhez típusú), valamint a Service Bus is biztosít további kommunikációs módszerek.

#### <a name="service-bus-relay"></a>Service Bus Relay
![Azure Service Bus Relay](./media/fundamentals-introduction-to-azure/ServiceBusRelayIntroNew.png)

*Ábra: Service Bus Relay lehetővé teszi, hogy egy tűzfal másik oldalon található alkalmazások közötti kommunikációt.*

A Service Bus lehetővé teszi, hogy a továbbítási szolgáltatáshoz, a közvetlen kommunikáció biztosítása biztonságosan együttműködhet tűzfalon keresztül. Service Bus-továbbítók lehetővé teszik az alkalmazások üzenetváltásokban a felhőben ahelyett, hogy helyileg üzemeltetett végpont keresztül kommunikálnak.

**Service Bus Relay forgatókönyvek**

Alkalmazások Service Buson keresztül kommunikáló lehet Azure alkalmazásokat és szoftvereket. néhány más felhőalapú platformot. Azonban a felhő kívül futó alkalmazások is lehetnek. Például egy légitársaság foglalási szolgáltatások saját adatközponton belüli számítógépek megvalósító gondol. A légitársaság sok ügyfél bejelentkezési pultokról repülőtereken foglalás ügynök terminálok, beleértve a szolgáltatások közzétételét, és lehet, hogy az ügyfelek telefonokon is kell. Használhatja a Service Bus ehhez a különböző alkalmazások közötti lazán összekapcsolt kapcsolat létrehozása.

#### <a name="service-bus-topics-and-subscriptions"></a>Service Bus-üzenettémák és -előfizetések
![Az Azure Service Bus-Üzenettémák](./media/fundamentals-introduction-to-azure/ServiceBusTopicsSubsIntroNew.png)   
 *. Ábra: Service Bus-üzenettémakörök lehetővé teszi, hogy több alkalmazás visszaküldeni az üzenetek és a más alkalmazásokban való előfizetés egy adott feltételeknek megfelelő üzenetek fogadására.*

A Service Bus témakörök és előfizetések közzététel és előfizetés mechanizmust biztosít. Publish-subscribe, az alkalmazás közben más alkalmazásokat hozhat létre ehhez a témakörhöz előfizetések üzeneteket küldhetnek a témakör. Ez lehetővé teszi, hogy egy-a-többhöz kommunikáció egy adott alkalmazáscsoportra, ha engedélyezi, hogy ugyanaz az üzenet több címzettnek olvashatják között.

**Service Bus-Üzenettémák és előfizetések forgatókönyvek**

Bármikor hoz létre Ha sok üzeneteket, amelyek fontos, de különböző alárendelt rendszereket csak kell azokat kommunikációs, a Service Bus-témakörbe eltérő részhalmaza figyelésére és előfizetések jó választás.

### <a name="biztalk-services"></a>BizTalk Services
![BizTalk Services](./media/fundamentals-introduction-to-azure/BizTalkServicesIntroNew.png)   
 *Ábra: BizTalk szolgáltatás lehetővé teszi a átalakítására XML-üzenetek formátumainak a felhőben.*

Egyes esetekben kell kapcsolni rendszerek, amelyek segítségével különböző üzenetkezelési formátumok kommunikálnak. Esetében gyakori, a vállalatok különböző adatbázis sémák és XML messaging formátumok, akkor is, ha egy általános szabvány érhető el. Helyett a egyéni kódot ír, használhatja a helyszíni BizTalk Server különböző rendszerek integrálására.  Az Azure BizTalk biztosít az azonos típusú szolgáltatást, de a felhőben. Is kell fizetnie csak mi használ, és nem kell foglalkoznia méretezési, mint a helyszíni kellene lennie.

**BizTalk Services Scenarios**

Üzleti vállalatközi (B2B) kapcsolati gyakran igényelnek fordítási az ilyen típusú.  Például egy vállalat repülőgépek felépítése kell rendelés részeit különböző részeit beszállítói. Sok részből szállítók lesz.  A szállítók rendszerekbe közvetlenül a repülőgép szerkesztők rendszerekből go rendeléseket érdemes automatizálni.  A core rendszerek és üzenetformátumok módosítani szeretné sem üzleti, és nem nagyon valószínű, hogy ezek a formátumok megegyeznek. BizTalk szolgáltatások üzenetek igénybe, és állomásneveket, az új formátum mindkét irányba között. Vagy repülőgép szállítójához teheti meg lefordítani a munkahelyi vagy az egyes szállítók is, attól függően ki szeretne rendelni a további ellenőrzési és a szükséges fordítási mennyisége.     

## <a name="compute-assistance"></a>Számítási támogatás
Azure Services, folyamatosan futtatásához nincs szükség támogatást nyújt.  

### <a name="scheduler"></a>Scheduler
![Azure Scheduler](./media/fundamentals-introduction-to-azure/SchedulerIntroNew.png)   
*Ábra: Azure Schedulerrel módszert kínál a feladatok ütemezése a megadott idő az adott időpontban.*

Más alkalmazások csak futtatnia kell egy bizonyos időpontban. A Azure-ban költségtakarékosabb munkavégzésben az ilyen típusú alkalmazások ahelyett, hogy egy alkalmazás csak tovább futnak 24 x 7-Várakozás az adatok feldolgozásához. Azure Schedulerrel segítségével meghatározhatja, hogy mikor egy alkalmazás vagy a naptárban időközönkénti alapján futni. Megbízható, és ellenőrzi, hogy egy folyamat fut-e akkor is, ha a hálózati, a gép és a data center hibákat észlel. A Feladatütemező REST API segítségével kezelheti ezeket a műveleteket.

Ütemezett riasztás akkor fordul elő, amikor a Feladatütemező HTTP vagy HTTPS-üzeneteket küld egy adott végponti, vagy egy üzenet lehet helyezni a tároló várólista.  Így kell az alkalmazást, vagy egy elérhető végpontot, vagy azt a tároló várólista figyelése. Majd az üzenetet kap, miután műveleteket hajthat végre azt programozott intézkedéseket.

**A Feladatütemező forgatókönyvek**

* Ismétlődő alkalmazás műveletek: Tegyük fel, a szolgáltatás előfordulhat, hogy rendszeres időközönként adatokat lekérni a twitter és az adatgyűjtést rendszeres adatcsatornára be.
* A napi karbantartás: napló dolgoz fel, vagy csatlakozási, elvégzése biztonsági mentések és más időnként feladatok ütemezése.
* Feladatok, amelyek éjszaka futtatni.
* Webes alkalmazások feladatokat, például napi törlése a naplókat, a biztonsági mentés, és egyéb karbantartási műveleteket. A rendszergazda dönthetnek úgy, hogy biztonsági mentését az adatbázis minden nap a következő 9 hónapig 1 órakor.

A Feladatütemező API létrehozása, frissítése, törlése, megtekintése és feladatgyűjteményei és ütemezett feladatok programozott kezelését teszi lehetővé.

## <a name="performance"></a>Teljesítmény
Az alkalmazás mindig fontos teljesítményét. Az egyes alkalmazások és újra ugyanazon adatokat érik el. Egy a teljesítmény javítása módja az alkalmazáshoz, ennek lekéréséhez szükséges idő minimalizálása közelebb adatok tartani. Azure ezzel különböző szolgáltatásokat biztosít.

### <a name="azure-caching"></a>Azure Caching
![Azure Caching](./media/fundamentals-introduction-to-azure/AzureCacheIntroNew.png)   
 **Ábra: Is az Azure-alkalmazások gyorsítótárazza az adatokat a memóriában, és még ossza sok feldolgozói szerepkörök között**

Bármely Azure adatkezelés szolgáltatások-SQL-adatbázis, a táblák vagy a Blobok tárolt adatok elérése-meglehetősen gyorsan elvégezhető. Még a memóriában tárolt adatok elérése még gyorsabb. Ebből kifolyólag tartja a gyakran használt adatokhoz egy memórián belüli másolatot javíthatja az alkalmazások teljesítményének. Ehhez használhatja Azure memórián belüli gyorsítótárazáshoz.

A Cloud Services alkalmazás adatok tárolása a gyorsítótár, majd közvetlenül anélkül állandó tároló elérésére lekéréséhez. A gyorsítótár az alkalmazás virtuális gépeken belül is kezelhetők, vagy a virtuális gépeket, amely kizárólag gyorsítótárazását ellátni. Mindkét esetben a gyorsítótár terjeszthető, az adatok tartalmaz terjedésének egy Azure-adatközpontban több virtuális gépek között.

Azure számos különböző gyorsítótár technológia, amely rendelkezik vette idővel rendelkezik. Ahhoz, azok lett bevezetve, van egy megosztott szerepköralapú, felügyelt és a Redis gyorsítótár. A megosztott gyorsítótárazás régebbi technológia, és ne hozzon létre új megvalósítások vele. A felügyelt gyorsítótár a In szerepköralapú gyorsítótár, de az Azure-portálon kívül felügyelt szolgáltatásként azonos funkciókat tartalmaz. A Redis-megvalósítás a funkciók legnagyobb számú, és ajánlott, ha új gyorsítótárazási kódot ír.

**Az Azure Cache forgatókönyvek**

Termékkatalógus ismételten olvasó alkalmazás előfordulhat, hogy az ilyen gyorsítótárazás előnye, például az adatok óta kell lesz elérhető gyorsabban. A technológia is támogatja a zárolás, ha engedélyezi, hogy az olvasási/írási, valamint a csak olvasható adatok használható. És az ASP.NET-alkalmazások a szolgáltatás használatával tárolja a munkamenet csak egy konfiguráció módosítását.

### <a name="content-delivery-network"></a>Content Delivery Network
![Azure CDN](./media/fundamentals-introduction-to-azure/CDNIntroNew.png)   
 **Ábra: Blob másolatát a világ különböző helyeken gyorsítótárazható.**

Tegyük fel kell tárolni a világ különböző felhasználók által használt blobadatokat. Lehet, hogy a legújabb világbajnokság match, például vagy illesztőprogram-frissítést, vagy egy népszerű e-könyv videó. Az adatok másolatát tárolja az Azure adatközpontjaiban több segít, de az számos felhasználók, esetén valószínűleg nem elegendő. A még jobb teljesítmény érdekében az Azure CDN is használhatja.

A CDN a világ minden egyes kompatibilis Azure BLOB tárolásának helyek több tucatnyi rendelkezik. A világ egyes részei a felhasználó hozzáfér egy adott blob, először adatoktól átmásolva egy Azure-adatközpontban adott földrajzi helyi CDN tárba. Ezt követően a világ része a hozzáférések fogja használni a blob másolási gyorsítótárazza a CDN-egészen a legközelebbi Azure-adatközpontban Ugrás nem szükséges. Eredménye gyorsabb hozzáférés a felhasználók bárhol a világon ritkábban használt adatokhoz.

**CDN-forgatókönyvek**

Közös CDN használata a Media Services videó világszerte továbbítására is. Videó általában nagy, és nagy mennyiségű sávszélesség szükséges.  A Media Services rendszer túlmutatnak máshol ezen a lapon.

## <a name="big-data-and-big-compute"></a>Big Data típusú adatok és nagy számítási
### <a name="hdinsight-hadoop"></a>HDInsight (Hadoop)
![HDInsight](./media/fundamentals-introduction-to-azure/HDInsightIntroNew.png)   
 **. Ábra: A tömeges feldolgozási túl nagy mennyiségű adatok segítségével HDInsight**

Sok éve adatelemzés tömeges még nem lett végrehajtva egy relációs adatbázis-kezelő építését adatraktárban tárolt relációs adatok. Az ilyen üzleti analytics továbbra is fontos, és újra megjelenik a hosszú ideig lesz. De mi történik, ha a kívánt adatok elemzése nagy, hogy relációs adatbázisok csak nem tudja kezelni azt? És tegyük fel, hogy az adatok nem relációs? Előfordulhat, hogy kiszolgálónaplókban egy adatközpontban, például vagy korábbi eseményadatokat az érzékelők, illetve valami mással. Ilyen esetben van big Data típusú adatok probléma ún. Egy másik módszer van szüksége.

A meghatározó technológia ma a big Data típusú adatok elemzésére szolgáló Hadoop. Az Apache nyissa meg a forrás-projektet, ez a technológia tárolja az adatokat a Hadoop elosztott fájlrendszerrel (HDFS) használatával, és lehetővé teszi, hogy a fejlesztők számára, hogy az adatok elemzéséhez MapReduce-feladatok létrehozása. HDFS adatok több kiszolgálóra terjed ki, majd az egyes, ha engedélyezi, hogy a big Data típusú adatok a MapReduce feladatot futtat adattömböket írnak párhuzamosan kell.

A HDInsight az Azure Apache Hadoop-alapú szolgáltatás neve. A HDInsight lehetővé teszi, hogy a HDFS a fürtön adatokat tárolhatnak, és szét a több virtuális géphez. Virtuális gépek között a MapReduce-feladatok logikát is terjed. Ugyanúgy, mint a helyszíni Hadoop-, az adatok feldolgozott helyileg a logikai és az adatokat, hogy működik-e az az ugyanazon virtuálisgép- és a jobb teljesítmény párhuzamosan. HDInsight is tárolhatja az adatokat az Azure tárolási tárolóban (ASV), amely blobokat használ.  ASV használatával teszi költségtakarékosabb munkavégzésben, mert törlése a HDInsight-fürthöz, ha nincsenek használatban, de továbbra is tárolni az adatait a felhőben.

HDinsight más összetevők, valamint a Hadoop ökoszisztémájának támogatja, beleértve a Hive és a Pig. A Microsoft is létrehozott összetevők, amelyek megkönnyítik a HDInsight által előállított adatok hagyományos Üzletiintelligencia-eszközök, például a HiveODBC adapter használatával, és együttműködik az Excel adatok Explorer.

### <a name="high-performance-computing-big-compute"></a>Nagy teljesítményű számítástechnikai (nagy számítási)
A felhőplatform használatának legtöbb vonzó módjai egyik nagy teljesítményű számítástechnikai (HPC) és más "Nagy számítási" alkalmazások futtatásához. Például a szabványos Message Passing Interface (MPI), valamint a úgynevezett embarrassingly párhuzamos alkalmazások, például a pénzügyi kockázat modellek használandó beépített speciális műszaki alkalmazásokhoz.

Nagy számítási lényege végrehajtja az kód sok számítógépre egy időben. Az Azure Ez azt jelenti, hogy számos futó virtuális gépek egyidejűleg, minden egyes probléma megoldására párhuzamosan működik. Ez bármilyen módon erőforrásokhoz és alkalmazásokhoz, azaz ütemezése, a munkahelyi szét ezek a példányok igényel. A Microsoft szabad HPC Pack és a számítási fürt megoldásait elvégezhetnek is Azure, kihasználni a Azure számítási és infrastruktúra-szolgáltatások igény kapacitás hozzáadása egy a helyszíni számítási fürt vagy futtatási nagy számítási alkalmazások teljesen a felhőben .

Azure biztosít egy virtuális gép mag, memória, lemez kapacitását, és egyéb jellemzőket is lehet elégíteni a különböző alkalmazások különböző konfigurációkkal rendelkező példány mérete. A közelmúltban bevezetett A8 és A9 példányok tevékenységeket is számos számítási igényes munkaterhelések és párhuzamos MPI alkalmazások különösen fontos, mivel nagy sebességű, multicore processzort és nagy mennyiségű memóriával rendelkeznek. Egyes konfigurációk a példányok kihasználása egy alacsony késésű és nagy átviteli alkalmazás hálózati a felhőben, amely tartalmazza a távoli közvetlen memória-hozzáférés (RDMA) technológia a maximális hatékonyság párhuzamos MPI-alkalmazások.

Az Azure is kínál alkalmazásfejlesztők nagy számítási és a partnerek számítási képességekre, szolgáltatások, architektúra lehetőségek és fejlesztői eszközök teljes készletét. Azure speciális adatok munkafolyamatok érintő egyéni nagy számítási munkafolyamatok támogatja, és a feladat- és ütemezés mintáról olvashat, amelyek több ezer méretezhető számítási magok.

## <a name="media"></a>Média
![Az Azure Media Services](./media/fundamentals-introduction-to-azure/MediaServicesIntroNew.png)   
 **. Ábra: A Media Services szolgáltatás olyan platform, amelyet az ügyfelek számára a világ különböző képeket és más alkalmazásokat.**

Videó ma teszi ki internetes forgalmat nagy része, és százalékosan holnap még nagyobb lesz. Még nem biztosít a weben nem egyszerű. Az számos változók, például a kódolási algoritmus és a felhasználó képernyő felbontásának. Videó általában is rendelkezik felszakadásáig igény szerint, például egy szombat éjszakai csúcs, amikor több személlyel mellett dönt, hogy azok egy online movie figyelni kívánja.

Megadott annak időben népszerűvé vált, akkor egy biztonságos tipp, hogy számos új alkalmazás jön létre a videoeszközök használata. Még az összes kell megoldani, az azonos problémák és a saját elérhetővé válnak a problémák megoldásában mindegyiknél elvégzése nem értelmezhető. Jobb megoldást jelenthet, ha sok alkalmazások is használhatnak közös megoldásokat biztosít platformot. És ezen a platformon, a felhő felépítése néhány egyszerű előnyeit. Széles körben elérhető, használatalapú fizetési alapon lehet, és az igény szerinti, amelyek a video-alkalmazások gyakran szembesülhetnek variancia is kezelni tud.

Az Azure Media Services kezeli ezt a problémát. Felhő összetevők megkönnyítő élettartama egyszerűbb létrehozását és a futó alkalmazások video- és egyéb adathordozó használatával biztosít.

Az ábrán az látható, a Media Services összetevők a video- és egyéb adathordozó együtt használható alkalmazások biztosít. Például az tartalmaz egy adathordozó feldolgozó összetevő videó feltöltése a Media Services (helyén az Azure-Blobokkal), egy kódolási összetevője, amely támogatja a különböző video- és formátumok, a tartalomvédelem összetevője, amely digitális tartalomvédelmi, egy összetevő ads beszúrni a video-adatfolyamot, az adatfolyamként történő összetevők és egyebek. Microsoft-partnerek is is adja meg az összetevők a platform, majd, hogy a Microsoft ezek az összetevők terjesztése és a nevében kiszámlázni.

Ezen a platformon használó alkalmazások az Azure-on vagy máshol futtathatók. Például egy asztali alkalmazás számára a videó éles house előfordulhat, hogy lehetővé teszik a videó feltöltése a Media Services szolgáltatásban a felhasználók majd feldolgozni azt különböző módokon. Azt is megteheti egy Azure-on futó tartalomkezelési felhő alapú szolgáltatás a Media Services feldolgozni, és a videó terjesztése előfordulhat, hogy támaszkodnak. Ahol csak akkor fut, és bármilyen kezeli, minden egyes alkalmazás úgy dönt, összetevőinek kell alkalmaznia, ezek a RESTful-felületeket általi eléréséhez szükséges.

Terjesztéséhez, akkor hozza létre, az alkalmazás az Azure CDN, egy másik CDN használhatja, vagy csak küldéséhez a bits közvetlenül a felhasználók számára. Azonban amikor megkapja van, a Media Services használatával létrehozott videó használni képes különböző ügyfél Systems, Windows, Macintosh, HTML 5, iOS, Android, Windows Phone, Flash és a Silverlight. A cél, hogy könnyebben modern media alkalmazásokat hozhat létre.

**Hivatkozások**

Több megjeleníti a Media Services működésével, töltse le a [Azure Media Services poszter][Azure Media Services Poster].

## <a name="commerce"></a>Kereskedelmi
Szolgáltatott szoftver általában a rendszer átalakítása hogyan létrehozhatunk alkalmazások. Is átalakítása hogyan adunk el alkalmazások. Egy SaaS-alkalmazáshoz él, a felhőben, mert az így, amely a lehetséges ügyfeleket kell keresnie az online megoldások. És ez a változás az adatokat, valamint hogy az alkalmazások vonatkozik. Miért nem szabad személyek keresse meg a felhőbe kereskedelmi forgalomban kapható adatkészletek esetében? Microsoft-címek is, a problémák a [Azure piactér](https://azure.microsoft.com/marketplace/).

![Az Azure kereskedelmi](./media/fundamentals-introduction-to-azure/CommerceIntroNew.png)   
 **Ábra: Az Azure piactér és az Azure-tároló lehetővé teszik, hogy megkeresheti és Azure-alkalmazások és a kereskedelmi adatkészletek vásárolni, azokat az Azure-alkalmazások részeként.**

A kettő közötti különbség, hogy az Azure-portálon kívül esik a piactér, de a áruházban érhetők el a portálon. A lehetséges ügyfeleket találhatók az Azure-alkalmazások, amelyek megfelelnek az igényeinek kereshet. Az ügyfelek például demográfiai, pénzügyi adatokat, földrajzi adatok és egyéb, valamint a kereskedelmi adatkészletek kereshet. Ha találnak valamit, például akkor, eléréséhez, vagy a szállítótól közvetlenül a piactér vagy tároló webhelyeken vagy bizonyos esetekben az Azure-portálon. Alkalmazások is használhatják a Bing keresési API-t a piactéren, és adjon hozzáférést a webkiszolgáló eredményeit keresztül.

**Kereskedelmi forgatókönyvek**

SendGrid egy alkalmazás az Azure-tárolóban, amely lehetővé teszi az e-mail üzenetek küldéséhez. További funkciók, például a megbízható kézbesítési és a statisztika kínál.  Akkor is az alkalmazás és a kapcsolódó szolgáltatások megvásárlása ahelyett saját kezűleg ilyen infrastruktúra kialakítása során.  

## <a name="getting-started"></a>Első lépések
Most, hogy a nagy vonalakban tekinti, a következő lépés az első Azure-alkalmazások írásával. Válassza ki azt a nyelvet, [a megfelelő SDK beszerzése](https://azure.microsoft.com/en-us/downloads/), és válassza ki azt. Felhőalapú informatika az új alapértelmezett – első lépések megkezdése.

[Azure Media Services Poster]: http://azure.microsoft.com/documentation/infographics/media-services/
