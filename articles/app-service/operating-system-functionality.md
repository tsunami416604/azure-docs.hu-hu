---
title: Az App Service szolgáltatásban – Azure operációs rendszer funkcionalitása
description: 'További tudnivalók: web apps, mobil-háttéralkalmazások és az Azure App Service API Apps-alkalmazások számára elérhető OS-funkciók'
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: bb17748968d0875cae132730a02f490eab966690
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53388981"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Az Azure App Service-ben az operációs rendszer funkcionalitása
Ez a cikk ismerteti a gyakori alapkonfiguráció operációs rendszer funkcionalitása futó összes Windows-alkalmazások számára elérhető [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Ez a funkció tartalmazza a fájl, hálózati, és az adatbázis eléréséhez, és a diagnosztikai naplók és események. 

> [!NOTE] 
> [A Linux-alkalmazások](containers/app-service-linux-intro.md) saját futtathatők App Service-ben. A gazdagép operációs rendszere nincs hozzáférése engedélyezett, rendelkezik legfelső szintű hozzáféréssel a tárolóhoz. Hasonlóképpen, a [Windows-tárolókban futó alkalmazások](app-service-web-get-started-windows-container.md), rendelkezik rendszergazdai hozzáféréssel a tárolóhoz, de a gazdagép operációs rendszeréhez sem fér hozzá. 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>Az App Service-csomagom
App Service-ben ügyfélalkalmazások egy több-bérlős üzemeltetési környezetben fut. A telepített alkalmazások a **ingyenes** és **megosztott** rétegek futás munkavégző folyamatok a megosztott virtuális gépeken telepített alkalmazások a **Standard** és **prémium**  rétegek kifejezetten az egyetlen ügyfél társított alkalmazások számára dedikált virtuális gép(ek) futhatnak.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Mivel az App Service támogatja a különböző rétegek közötti méretezési zökkenőmentesen, a biztonsági beállítások, az App Service-alkalmazás által kényszerített változatlan marad. Ez biztosítja, hogy alkalmazásokat nem hirtelen eltérően viselkednek, nem várt módon működik, amikor az App Service-csomag egyik rétegről a másikra vált.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Fejlesztési keretrendszerek
Az App Service-tarifacsomag irányítása a számítási erőforrások (CPU, lemezterület, memória és a kimenő hálózati adatforgalom) elérhető alkalmazásokra. Keretrendszer funkciók alkalmazások számára elérhető technológiai spektrumunk kihasználtságának növelését, függetlenül a méretezési réteg változatlan marad.

Az App Service többféle fejlesztési keretrendszerekkel, beleértve az ASP.NET, klasszikus ASP, node.js, PHP és Python - bővítmények belül IIS futtató összes támogatja. Egyszerűsítése és biztonsági konfiguráció normalizálása, App Service-alkalmazások futtatására általában a különböző fejlesztési keretrendszerekkel az alapértelmezett beállításokkal. Alkalmazások konfigurálása az egyik lehetőség lehetett volna API felület és minden egyes fejlesztési keretrendszer funkciók testreszabásához. App Service-ben több általános megközelítés inkább egy alkalmazást fejlesztői keretrendszer függetlenül operációs rendszer funkcionalitása közös alapvető engedélyezésével vesz igénybe.

A következő szakaszok összegzik az operációs rendszer funkcionalitása elérhető App Service-alkalmazások általános típusú.

<a id="FileAccess"></a>

## <a name="file-access"></a>Fájlhozzáférés
Különböző meghajtókon található App Service, beleértve a helyi és hálózati meghajtókat.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Helyi meghajtók
A core, az App Service szolgáltatás az Azure PaaS (szolgáltatásként nyújtott platformon) infrastruktúra felett futó szolgáltatásban. Ennek eredményeképpen a helyi meghajtókat "csatlakoztatott" virtuális gép olyan az azonos meghajtón bármely Azure-ban futó feldolgozói szerepkör számára elérhető. Az érintett műveletek közé tartoznak az alábbiak:

- Az operációsrendszer-meghajtó (a D:\ meghajtóra)
- Egy alkalmazás meghajtó, amely tartalmazza az Azure cspkg használt csomagfájlok kizárólag az App Service (és az ügyfelek számára nem érhető el)
- A "user" meghajtó (a C:\ meghajtóra), amelynek mérete a virtuális gép méretétől függően változik. 

Fontos a lemezhasználat monitorozása az alkalmazás növekedésével. Ha a lemez kvóta elérése az alkalmazásnak a negatív hatások rendelkezhet. Példa: 

- Az alkalmazás throw előfordulhat, hogy egy hibaüzenet nincs elég hely a lemezen.
- A Kudu konzolhoz böngészésekor lemezterületből fakadó hibák jelenhetnek meg.
- Az üzembe helyezés a vsts-ben vagy a Visual Studio meghiúsulhat `ERROR_NOT_ENOUGH_DISK_SPACE: Web deployment task failed. (Web Deploy detected insufficient space on disk)`.
- Az alkalmazás gyengülhet a teljesítmény lecsökkenését tapasztalhatja.

<a id="NetworkDrives"></a>

### <a name="network-drives-aka-unc-shares"></a>Hálózati meghajtók (más néven UNC fájlmegosztások)
Az App Service, amely az alkalmazás üzembe helyezése és karbantartása egyszerű egyedi aspektusai egyike, hogy minden felhasználó tartalomtárolást különböző UNC-megosztásokhoz. Ez a modell és a gyakori minta, tartalom, a üzemeltetési környezetek, amelyek több, elosztott terhelésű kiszolgálókat a helyi webes által felhasznált tárterület rendeli hozzá. 

App Service-ben belül létrehozott minden adatközpontban UNC-megosztásokhoz számos van. Minden adatközpontban minden ügyfél számára a felhasználói tartalom százalékában van lefoglalva minden egyes UNC megosztást. Továbbá a fájl számára egy egy ügyfél-előfizetés mindig el van helyezve az azonos UNC tartalom mindegyikét megosztani. 

Az Azure működése, mert az adott virtuális gép egy UNC megosztást üzemeltető felelős idővel változni fognak. Garantált, hogy különböző virtuális gépek által UNC-megosztásokhoz lesz csatlakoztatva, a normál működés során az Azure üzemeltetése során felfelé és lefelé vont. Ebből kifolyólag alkalmazásokat soha nem győződjön meg, hogy a fájl UNC elérési út adatai marad állandó idővel változtatható feltételezéseket. Akkor érdemes inkább a kényelmes *ál* abszolút elérési út **D:\home\site** , amelyek az App Service biztosítja. Ez ál abszolút elérési utat a saját alkalmazásra hivatkozó hordozható, és-felhasználó-alkalmazásfüggetlen módszert biztosít. Használatával **D:\home\site**, egy vihet át a megosztott app alkalmazást minden új abszolút elérési útnak konfigurálása nélkül.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Típusú fájl a hozzáférést egy alkalmazáshoz
Minden egyes ügyfél-előfizetés rendelkezik egy fenntartott könyvtárstruktúrát adatközponton belül meghatározott UNC-megosztáson található. Előfordulhat, hogy az ügyfél megosztani több alkalmazás létrehozott egy adott adatközponton belül, így az azonos UNC jönnek létre a könyvtárak egyetlen ügyfél-előfizetések alá tartozó összes. A megosztás lehet olyan címtárakat, például a tartalmat, a hiba és a diagnosztikai naplók és a korábbi verzióiban a verziókövetés által létrehozott alkalmazás. Megfelelően működik, a felhasználó alkalmazás könyvtárak érhetők el az olvasási és írási hozzáférés az alkalmazás alkalmazáskód által futásidőben.

A helyi meghajtókon csatolva a virtuális géphez, amely egy alkalmazást futtat az App Service fenntart egy szövegrészletet, alkalmazás-specifikus ideiglenes helyi tárolóhoz tartozó a C:\ meghajtón található helyet. Bár az alkalmazás a saját ideiglenes helyi tárhely teljes írási/olvasási hozzáféréssel rendelkezik, hogy a tárolási valóban használata nem javasolt közvetlenül az alkalmazás kódja által használható. A célja, biztosít ideiglenes fájlok tárolására az IIS és a webes alkalmazás-keretrendszerek. App Service-ben is elérhető minden alkalmazás megakadályozza, hogy egyes alkalmazások felhasználása a helyi fájl tárolási túlzott mennyiségű ideiglenes helyi tárhely mennyiségét korlátozza.

Hogyan használja az App Service-ben a helyi ideiglenes tárhely két példa az ideiglenes ASP.NET-fájlok a könyvtárban, és az IIS-címtárnak tömörített fájlok. Az ASP.NET-fordítási rendszer a "Ideiglenes ASP.NET-fájlok" könyvtárat használja egy ideiglenes fordítási gyorsítótár helyét. Az IIS tömörített válasz kimenetének tárolására a "IIS ideiglenes tömörített fájlok" könyvtárat használja. Mindkét említett típusú fájl használati (ahogy mások) rendszer újramegfeleltetése alatt App Service-ben alkalmazásonkénti ideiglenes helyi tárba. Az újbóli hozzárendelés biztosítja, hogy funkciói továbbra is a várt módon.

Minden alkalmazás az App Service-ben fut, egy véletlenszerű egyedi alacsony jogosultsági szintű munkavégző folyamat identitása, a "alkalmazáskészlet-identitás", továbbá az itt leírtak szerint nevű: [ https://www.iis.net/learn/manage/configuring-security/application-pool-identities ](https://www.iis.net/learn/manage/configuring-security/application-pool-identities). Alkalmazás kódja ezt az identitást használja az alapszintű csak olvasási hozzáféréssel az operációs rendszer meghajtójára (a D:\ meghajtóra). Ez azt jelenti, hogy alkalmazáskód közös könyvtárstruktúrák listában, és az operációs rendszer meghajtóján közös fájlok olvasását. Bár ez jelenhet meg kell a hozzáférést, az ugyanazon könyvtárak és fájlok némileg széles körben elérhetők, amikor üzembe helyez egy feldolgozói szerepkört az Azure-ban üzemeltetett szolgáltatás, és olvassa el a meghajtó tartalmát. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Az egyes példányok a fájlhozzáférés
A kezdőkönyvtár tartalmazza az alkalmazás tartalmát, és az alkalmazáskód írható-e. Ha egy alkalmazás több példánya fut, a kezdőkönyvtár között megosztott összes példányát, hogy az összes példány megtekintéséhez ugyanabban a címtárban. Tehát például ha egy alkalmazás feltöltött fájlokat a kezdőkönyvtárba menti, ezeket a fájlokat érhetők el azonnal az összes példányra. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Hálózati hozzáférés
Alkalmazás felhasználhat TCP/IP és UDP-alapú protokollokat, hogy az internetről hozzáférhető végpontokkal, amely a külső szolgáltatások a kimenő hálózati kapcsolatokat. Alkalmazások használhatják ezeket a protokollokat, az Azure-szolgáltatásokhoz&#151;például úgy, hogy az SQL Database-HTTPS-kapcsolatok létesítése.

Az alkalmazások egyetlen helyi visszacsatolási kapcsolat, és a egy alkalmazást figyelni, hogy helyi visszacsatolási szoftvercsatornán rendelkezik egy korlátozott funkciókkal is van. Ez a funkció elsősorban az alkalmazások, amelyek a helyi visszacsatolási sockets funkcióikkal részeként figyelik létezik. Minden alkalmazás "privát" visszacsatolási kapcsolat fog látni. "A" alkalmazás "B" alkalmazás által létrehozott helyi visszacsatolási szoftvercsatornához nem tudja figyelni.

Nevesített csövek egy másik alkalmazás együttesen futó folyamatok közötti folyamatok közti kommunikációja (IPC) mechanizmust is támogatottak. Az IIS FastCGI-modul például az egyes folyamatok PHP lapokat futtató nevesített csövek támaszkodik.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Végrehajtás, folyamatok és a memória
Korábban feljegyzett az alkalmazások egy véletlenszerű alkalmazáskészlet-identitás használatával alacsony jogosultsági szintű munkavégző folyamatok belül futnak. Alkalmazáskód hozzáfér a memória, a munkavégző folyamat, valamint a gyermek folyamatokat is lehet generálandó CGI-folyamatok és más alkalmazásokkal társított. Azonban egy alkalmazás nem fér hozzá a memória vagy az adatok egy másik alkalmazás akkor is, ha ugyanazon a virtuális gépen.

Alkalmazások futtathat szkripteket vagy a támogatott webfejlesztési keretrendszerekre írt lapok. App Service-ben nem konfigurálja minden olyan webes keretrendszer szűkebb módokat. App Service-ben futó ASP.NET-alkalmazások például futtassa a "teljes" megbízhatósági helyett egy szűkebb megbízhatósági módot. Webes keretrendszereket is a klasszikus ASP és az ASP.NET, meghívhatja a folyamaton belüli COM-komponensek (de nem kívüli folyamat COM-komponensek) például ADO (ActiveX Data Objects), amelyek a Windows operációs rendszeren alapértelmezés szerint.

Alkalmazások is tudott gyermekfolyamatként létrehozni, és tetszőleges kódot futtatni. Egy alkalmazás elvégzésére gyermekfolyamatként létrehozni egy parancs-rendszerhéj vagy egy PowerShell-szkript futtatása engedélyezett. Azonban Bár tetszőleges kódot és a folyamatok is létrehozta az alkalmazásokból, végrehajtható programokat és parancsfájlokat korlátozódnak továbbra is a szülő alkalmazáskészlethez jogosultsággal. Például egy alkalmazást is tudott gyermekfolyamatként létrehozni egy végrehajtható fájl, amely lehetővé teszi egy kimenő HTTP-hívással, azonban, hogy ugyanaz a végrehajtható fájl nem próbál meg a hálózati adapteren. a virtuális gép IP-címe a kötés megszüntetését Alacsony jogosultsági szintű kód egy kimenő hálózati hívás engedélyezett, de a kísérlet a hálózati beállítások a virtuális gép rendszergazdai jogosultságra van szükség.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Diagnosztikai naplók és események
Naplóadatok az adatokat, amelyek bizonyos alkalmazásokhoz való hozzáférés megkísérlése egy másik csoportja. A naplóadatok futó App Service-ben elérhető típusú diagnosztikai tartalmazza, és naplózza az információkat, hogy könnyen elérhető, az alkalmazás által generált. 

Például egy aktív alkalmazás által generált W3C HTTP-naplókat érhetők el vagy egy könyvtárat a létrehozott az alkalmazás vagy a blob storage szolgáltatásban elérhető, ha egy ügyfél úgy állította be W3C-naplózást, tárolási és hálózati megosztás helye. Az utóbbi lehetőséget lehetővé teszi nagy mennyiségű anélkül, hogy egy hálózati megosztásra a file storage korlátait meghaladó gyűjtendő naplók.

Egy hasonló tekintettel a .NET-alkalmazások valós idejű diagnosztikai adatait is naplózható használatával a .NET-nyomkövetési és diagnosztikai infrastruktúra, a beállításokkal a nyomkövetési adatokat írni az alkalmazás hálózati megosztásra, vagy másik lehetőségként egy blob tárolási helyére.

Diagnosztikai naplózás és nyomkövetés, amelyek nem érhetők el az alkalmazások a következő területekre Windows ETW-események és a gyakori Windows-eseménynaplók (például rendszer, alkalmazás és biztonsági eseménynaplók). ETW-nyomkövetési információkat lehet megtekinthető gépre kiterjedő (együtt jobb ACL-ek), mivel olvasási és írási hozzáférés ETW-események le vannak tiltva. A fejlesztők előfordulhat, hogy figyelje meg, hogy API-hívások olvasása és írása az ETW, események és a gyakori Windows-eseménynaplók úgy tűnik, hogy működik, de az App Service-ben a "faking" a hívások azért van, hogy azok megjelenhessenek sikeres. A valóságban ez az alkalmazás kódja nem rendelkezik hozzáféréssel az eseményadatokat.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Beállításjegyzék-hozzáférés
Alkalmazások rendelkeznek csak olvasható hozzáférést (bár nem: mind) a beállításjegyzék, a virtuális gép, amelyen futnak. A gyakorlatban ez azt jelenti, amelyek lehetővé teszik a helyi felhasználók csoport a csak olvasható hozzáférést beállításkulcsok alkalmazások által elérhető. Része a beállításjegyzékben, amely jelenleg nem támogatott olvasási vagy írási hozzáférése a HKEY\_aktuális\_felhasználói hive.

A beállításjegyzék írási hozzáféréssel le van tiltva, beleértve a felhasználónkénti beállításkulcsok való hozzáférést. Az alkalmazás szempontjából, a beállításjegyzéket írási hozzáféréssel kell soha nem lehet hivatkozni az Azure-környezetben, mivel az alkalmazások (és is) települnek át a különböző virtuális gépek között. A csak írható adattárolásra, amely képes lehet alárendelve, az alkalmazás által az alkalmazásonkénti tartalom könyvtárstruktúrát a az App Service UNC-megosztásokon tárolt. 

## <a name="more-information"></a>További információ

[Az Azure Web App védőfal](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) – a legfrissebb információkat az App Service a végrehajtási környezetben. Ezen a lapon közvetlenül az App Service fejlesztői csapat által karbantartott.

> [!NOTE]
> Ha az Azure App Service-t az Azure-fiók regisztrálása előtt szeretné kipróbálni, ugorjon [Az Azure App Service kipróbálása](https://azure.microsoft.com/try/app-service/) oldalra. Itt azonnal létrehozhat egy ideiglenes, kezdő szintű webalkalmazást az App Service szolgáltatásban. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 


