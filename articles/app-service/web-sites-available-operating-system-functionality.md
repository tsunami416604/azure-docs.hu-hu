---
title: "Operációs rendszer működőképességét Azure App Service"
description: "Az operációs rendszer funkció érhető el webes, mobil-háttéralkalmazások és Azure App Service API apps megismerése"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2016
ms.author: cephalin
ms.openlocfilehash: 6b5939341ad05fb8f80415c5335c24d216fc2555
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Operációs rendszer működőképességét Azure App Service
Ez a cikk összes futó alkalmazások számára elérhető közös eredeti operációs rendszer funkcióit mutatja be [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Ez a funkció tartalmazza a fájl, hálózati, és a beállításjegyzék elérése és diagnosztikai naplók és események. 

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>Az alkalmazásszolgáltatási csomag rétegek
App Service alkalmazások vevői futtat egy több-bérlős üzemeltetési környezet. Alkalmazások telepítése a **szabad** és **megosztott** rétegek futtatásához a munkavégző folyamatok megosztott virtuális gépeken, alkalmazások telepítésekor a a **szabványos** és **Premium**  rétegek futó virtuális gépek dedikált kifejezetten az egyetlen ügyfél társított alkalmazások.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Mivel az App Service különböző rétegek közötti méretezési gördülékenyen támogatja, a biztonsági beállítások, az App Service apps érvényes változatlan marad. Ez biztosítja, hogy alkalmazásokat nem hirtelen eltérően viselkednek, nem várt módon sikertelenek lesznek, amikor az App Service-csomag egyrétegű a másikra vált.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Fejlesztési keretrendszerek
App Service tarifacsomagok mértékének szabályozására a számítási erőforrások (CPU, lemezterület, memória és a kimenő hálózati forgalom) elérhető alkalmazásokra. Azonban a hardverekről keretrendszer funkció érhető el az alkalmazások változatlan marad, függetlenül a méretezési rétegek.

App Service számos fejlesztési keretrendszerek, beleértve az ASP.NET, a klasszikus ASP, node.js, PHP és Python -, amelyek mindegyikén kiterjesztéseket IIS belül. Egyszerűsítése és biztonsági konfigurációs optimalizálására, az App Service apps általában akkor futnak a különböző fejlesztési keretrendszerek az alapértelmezett beállításokkal. Alkalmazások konfigurálása az egyik módszer az API felület és minden egyes fejlesztési keretrendszer testreszabásához lehetett volna. App Service egy általánosabbá megközelítés helyette azáltal, hogy az operációs rendszer működőképességét, függetlenül az alkalmazás-fejlesztési keretrendszer közös alapterv vesz igénybe.

A következő szakaszok összegzik az általános típusú operációs rendszer funkció érhető el az App Service-alkalmazásokhoz.

<a id="FileAccess"></a>

## <a name="file-access"></a>Fájlhozzáférés
Különböző meghajtó belül App Service, beleértve a helyi és hálózati meghajtókat.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Helyi meghajtók
A a fő App Service az Azure PaaS (platformok) infrastruktúrán futó szolgáltatásban. Ennek eredményeképpen a helyi meghajtókra, "csatlakoztatott" virtuális gép olyan a azonos meghajtón bármely Azure-beli feldolgozói szerepkör számára elérhető. Ez magában foglalja az operációsrendszer-meghajtó (a D:\ meghajtóra), az alkalmazás Azure cspkg csomagfájlok kizárólag az App Service által használt (és az ügyfél számára nem érhető el) tartalmazó meghajtót és a "user" meghajtó (a C:\ meghajtóra), amelynek mérete változó attól függően, hogy a virtuális gép méretét . Fontos, a lemezhasználat figyelése, ahogy az alkalmazás növekszik. A lemezkvóta elérésekor az alkalmazás a negatív hatások rendelkezhet.

<a id="NetworkDrives"></a>

### <a name="network-drives-aka-unc-shares"></a>Hálózati meghajtók (más néven UNC közös)
Az App Service-alkalmazás telepítési és karbantartási egyszerű teszi egyedi vonatkozásait egyike, hogy minden felhasználó tartalom UNC-megosztásokhoz a megfelelő tárolja. Ez a modell nagyon szépen leképezve a megszokott mintát követi, a tartalom tárolási helyszíni webszolgáltatási környezetekben, melyekben még több terhelésű kiszolgálókat használja. 

App Service-ben belül vannak száma minden adatközpont létrehozott UNC-megosztásokhoz. A felhasználó tartalom az ügyfelek minden egyes adatközpont százalékos le van foglalva minden UNC-megosztás. Továbbá a tartalom, az egyetlen ügyfél-előfizetést mindig el van helyezve a azonos UNC-fájl osztoznak. 

Vegye figyelembe, hogy hogyan cloud services – munkahelyi miatt, UNC-megosztásnevet üzemeltető felelős az adott virtuális gép időbeli változik. A garantált, hogy különböző virtuális gépek UNC-megosztásokhoz lesz csatlakoztatva, a normális működés során felhő műveletek során felfelé és lefelé vont. Emiatt alkalmazásokat kell soha ne, hogy a gép adatait egy UNC elérési útja marad stabil időbeli kódolt feltételezéseket. Helyette használják inkább a a kényelmes *ál* abszolút elérési út **D:\home\site** , amely az App Service biztosít. Az ál abszolút elérési út lehetővé teszi a hordozható, alkalmazás-és-felhasználó-független saját alkalmazás hivatkozik. A **D:\home\site**, egy vihetők át megosztott fájlok app alkalmazást anélkül, hogy minden új abszolút elérési útnak konfigurálásához.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Fájl hozzáférést biztosít az alkalmazások típusai
Minden ügyfél-előfizetést egy fenntartott directory struktúrája adatközponton belül meghatározott UNC-megosztáson található. Előfordulhat, hogy az ügyfél megosztása több alkalmazás belül egy adott adatközpont jött létre, ezért a könyvtárakat egyetlen ügyfél-előfizetések alá tartozó összes jönnek létre, az azonos UNC. A megosztás könyvtárak, például a tartalmat, hiba és diagnosztikai naplók és az alkalmazás hozta létre a verziókövetési rendszerrel korábbi verzióinak is tartalmazhat. Elvárás ügyfél app könyvtárak érhetők el az olvasási és írási hozzáférés az alkalmazás alkalmazás kód futási időben.

A helyi meghajtókon csatolva a virtuális géphez, amely egy alkalmazást futtat az App Service fenntartja a rendszer az alkalmazás-specifikus ideiglenes helyi tárolására a C:\ meghajtón rendelkezésre álló. Habár egy alkalmazás teljes olvasási/írási hozzáférést a saját helyi ideiglenes tárhelyre, tárolási valóban nem célja, hogy közvetlenül az alkalmazás kódjának használják. Ehelyett a szándéka az, hogy ideiglenes fájlok tárolására biztosítanak az IIS és a webes alkalmazás-keretrendszerek számára. App Service is korlátozza ideiglenes helyi tárterület érhető el minden egyes alkalmazáshoz megakadályozhatja, hogy egyes alkalmazások a helyi fájl tárolási túl nagy mennyiségű fel.

Hogyan használja az App Service a helyi ideiglenes tárolási két többek között az ideiglenes ASP.NET-fájlok a könyvtárban, és az IIS a könyvtár a tömörített fájlok. Az ASP.NET-fordítási rendszer a "Temporary ASP.NET Files" könyvtárat használja, mint egy ideiglenes fordítási gyorsítótár helyét. Az IIS az "IIS ideiglenes tömörített fájlok" könyvtár a tömörített válasz kimenetének tárolására használja. Mindkét említett típusú fájl használati (valamint más) vannak újra társítva az App Service alkalmazásonkénti ideiglenes helyi tárterület. Az újbóli hozzárendelés biztosítja, hogy funkció továbbra is a várt módon.

Minden alkalmazás az App Service fut egy véletlenszerű egyedi alacsony jogosultsági szintű munkavégző folyamat identitásaként hívása a "alkalmazáskészlet-identitás", itt további leírt: [http://www.iis.net/learn/manage/configuring-security/application-pool-identities ](http://www.iis.net/learn/manage/configuring-security/application-pool-identities). Alkalmazáskód ezzel az identitással a rendszermeghajtó (a D:\ meghajtóra) alapvető csak olvasási hozzáféréssel használja. Ez azt jelenti, hogy alkalmazáskód közös könyvtárstruktúrák listában, és az operációs rendszer meghajtóján közös fájlok olvasását. Bár ez tűnhet némileg széles körre kiterjeszteni access, az ugyanazon könyvtárak és fájlok hozzáférhetők, amikor a feldolgozói szerepkör egy Azure-ban üzemeltetett szolgáltatás, és olvassa el a meghajtó tartalmát. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Fájlhozzáférés több példánya között
A kezdőkönyvtár az alkalmazás forráskódjának tartalom található, és alkalmazáskód írhat rá. Ha egy alkalmazás több példánya fut, a kezdőkönyvtár osztozik összes példányát, hogy minden példány könyvtárába. Így például ha egy alkalmazás a kezdőkönyvtár feltöltött fájlok mentése, ezeket a fájlokat érhetők el azonnal összes példányára. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Hálózati hozzáférés
Alkalmazáskód használhatja a TCP/IP, és hogy kimenő UDP-alapú protokollok hálózati Internet elérhető végpontok külső szolgáltatások elérhetővé tevő kapcsolat. Alkalmazások használhatják ezeket a protokollokat services, Azure &#151;belül, például az SQL Database HTTPS-kapcsolatok létrehozásával elérésére.

Egy korlátozott képesség alkalmazások között egy helyi visszacsatolási-kapcsolat létrehozásához, és figyelni, hogy helyi visszacsatolási szoftvercsatornán egy alkalmazás is van. Ez a funkció lehetővé teszi az alkalmazások, amelyek a helyi visszacsatolási sockets működés részeként figyelik a elsősorban létezik. Vegye figyelembe, hogy egyes alkalmazások látja-e a "privát" visszacsatolási kapcsolat; "A" App "B" alkalmazás által létrehozott helyi visszacsatolási szoftvercsatornához nem lehet figyelni.

A nevesített csöveket is támogatottak, amelyek együttesen az IOS-alkalmazásokat futtatnak, különböző folyamatok közötti folyamatok közti kommunikációja (IPC) módszerként. Az IIS FastCGI-modul például az egyes folyamatok PHP lapokat futtató nevesített csövek támaszkodik.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Programkód, folyamatok és memória
Ahogy azt korábban említettük, alkalmazások egy véletlenszerű alkalmazáskészlet-identitás használatával alacsony jogosultsági szintű munkavégző folyamatok belül fut. Alkalmazás kódjának a memória, a munkavégző folyamat, valamint a gyermek folyamatokat is lehet indított CGI folyamatok és más alkalmazásokkal társított hozzáférése van. Azonban egy alkalmazás nem fér hozzá a memória vagy egy másik alkalmazás adatok akkor is, ha az azonos virtuális gépen.

Alkalmazások parancsfájlokat, illetve a támogatott webes fejlesztési keretrendszerek írt lapok futtathatók. App Service nem konfigurálja a webes keretrendszer szűkebb módok. Például App Service futó ASP.NET alkalmazások figyelésekor egy szűkebb megbízhatósági mód "teljes" megbízhatósági szinten futtassa. Webes keretrendszerek, beleértve a klasszikus ASP és az ASP.NET, a folyamaton belüli COM-komponensek (de nem kívüli folyamat COM-komponensek) például ADO (ActiveX Data Objects) alapértelmezés szerint a Windows operációs rendszerben regisztrált elő tudják hívni.

Alkalmazások elindítanak és futtathatják. Egy olyan alkalmazáshoz, többek között a származtatása a parancssorba vagy egy PowerShell-parancsfájl futtatása engedélyezett legyen. Azonban annak ellenére, hogy tetszőleges kódot és a folyamatok tud indított az alkalmazásokból, végrehajtható programokat és parancsfájlokat korlátozódnak továbbra is a szülő alkalmazáskészlethez jogosultsággal. Például egy alkalmazás is elindítanak egy végrehajtható fájl, amely lehetővé teszi egy kimenő HTTP-hívás, azonban, hogy az azonos végrehajtható nem próbál szüntesse meg a virtuális gépek adapter IP-címe Alacsony jogosultsági szintű kód egy kimenő hálózati hívás engedélyezett, de a hálózati beállítások a virtuális gép próbált rendszergazdai jogosultságokra van szüksége.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Diagnosztikai naplók és események
Naplózási adatok egy másik adatok, amelyek bizonyos alkalmazásokban való hozzáférés megkísérlése egy. A naplózási információk érhetők el az App Service-ben futó típusú diagnosztikai tartalmazza, és egy alkalmazás, amely könnyen elérhető, az alkalmazás által generált adatok naplózására. 

Például a W3C HTTP egy aktív alkalmazás által generált érhetők el naplók vagy a hálózati megosztásnak a helyét, az alkalmazás vagy a blob Storage tárolóban elérhető létrehozása, ha az ügyfél Storage W3C-naplózás a naplókönyvtár. Az utóbbi lehetőséget lehetővé teszi, hogy a nagy mennyiségű naplók anélkül, hogy a meghaladó fájl tárolási egy hálózati megosztásra társított összegyűjteni.

Egy hasonló tekintettel a valós idejű diagnosztikai információkat, a .NET-alkalmazás is bejelentkezhet a .NET-nyomkövetés és diagnosztika infrastruktúra használata a beállítások a nyomkövetési adatokat írni az alkalmazás hálózati megosztást, vagy másik lehetőségként blob tárolóhelyre.

Diagnosztikai naplózás és nyomkövetés, amelyek nem érhetők el az alkalmazások területek a következők: Windows ETW-események és a gyakori Windows-Eseménynapló (pl. rendszer, az alkalmazás és a biztonság eseménynaplók). ETW-nyomkövetési adatokat is lehet megtekinthető gépre kiterjedő (az ACL-ek jobbra), mert az olvasási és írási hozzáférés ETW-események letiltva. A fejlesztők észrevette, hogy, hogy API hívások olvasási és írási ETW események és a Windows eseménynaplóiban keresse meg közös jelennek meg a munkahelyi, de, mivel az App Service van "faking" a hívásokat, hogy sikeres legyen. A valóságban az alkalmazás kódja nem rendelkezik hozzáféréssel az eseményadatok.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Beállításjegyzék elérésének
Az alkalmazások rendelkezzenek csak olvasási hozzáféréssel a legtöbb (azonban nem mindegyik) a virtuális gép a gazdagépen futnak a beállításjegyzék. A gyakorlatban ez azt jelenti, amelyek lehetővé teszik az olvasási hozzáférést a helyi felhasználók csoport beállításkulcsok érhetők el alkalmazások. A beállításjegyzékben, amely jelenleg nem támogatott olvasási vagy írási hozzáférés egy részén a HKEY\_aktuális\_felhasználói struktúra.

A beállításjegyzéket írási le van tiltva, beleértve a felhasználói beállításkulcsok való hozzáférést. Az alkalmazás szempontjából a beállításjegyzéket írási hozzáféréssel kell soha nem lehet hivatkozni az Azure környezetben óta alkalmazások (illetve használhat) települnek át a különböző virtuális gépek között. Az alkalmazás által kell függött csak állandó írható tárolóhoz az alkalmazáson belüli tartalom könyvtárstruktúrát a az App Service UNC-megosztásokon tárolt. 

## <a name="more-information"></a>További információ

[Az Azure Web App védőfal](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) -a végrehajtási környezet, az App Service a legfrissebb információkat. Ezen a lapon közvetlenül az App Service fejlesztői csapat tartja fenn.

> [!NOTE]
> Ha az Azure App Service-t az Azure-fiók regisztrálása előtt szeretné kipróbálni, ugorjon [Az Azure App Service kipróbálása](https://azure.microsoft.com/try/app-service/) oldalra. Itt azonnal létrehozhat egy ideiglenes, kezdő szintű webalkalmazást az App Service szolgáltatásban. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 


