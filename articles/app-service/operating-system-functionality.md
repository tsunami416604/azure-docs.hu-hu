---
title: Operációs rendszer funkcionalitása
description: Ismerje meg az operációs rendszer funkcióit a Windows Azure App Service szolgáltatásában. Megtudhatja, hogy milyen típusú fájl-, hálózati és beállításjegyzék-hozzáférést kap az alkalmazás.
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.topic: article
ms.date: 10/30/2018
ms.custom: seodec18
ms.openlocfilehash: ed84cb2b0cb8d98b12fe787e49c400ba47e4e38a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671611"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Az Azure App Service operációs rendszerének funkciói
Ez a cikk az [Azure App Service szolgáltatásban](https://go.microsoft.com/fwlink/?LinkId=529714)futó összes Windows-alkalmazás számára elérhető alapvető operációsrendszer-funkciókat ismerteti. Ez a funkció magában foglalja a fájl-, hálózati és beállításjegyzék-hozzáférést, valamint a diagnosztikai naplókat és eseményeket. 

> [!NOTE] 
> Az App Service [Linux-alkalmazásai](containers/app-service-linux-intro.md) saját tárolókban futnak. A gazdaoperációs rendszerhez nem lehet hozzáférni, root hozzáféréssel rendelkezik a tárolóhoz. Hasonlóképpen, a [Windows-tárolókban futó alkalmazások](app-service-web-get-started-windows-container.md)esetében rendszergazdai hozzáféréssel rendelkezik a tárolóhoz, de nem fér hozzá a gazdaoperációs rendszerhez. 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>App-szolgáltatási csomag-szintek
Az App Service több-bérlős üzemeltetési környezetben futtatja az ügyfélalkalmazásokat. Az **ingyenes** és **megosztott** szinteken telepített alkalmazások megosztott virtuális gépeken futó munkavégző folyamatokban futnak, míg a **standard** és **prémium** szinteken telepített alkalmazások a kifejezetten az egyetlen ügyfélhez társított alkalmazások számára kijelölt virtuális gépeken futnak.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Mivel az App Service támogatja a különböző szintek közötti zökkenőmentes skálázási élményt, az App Service-alkalmazások biztonsági konfigurációja ugyanaz marad. Ez biztosítja, hogy az alkalmazások ne viselkedjenek hirtelen másképp, váratlan módon nem megfelelően, amikor az App Service-csomag egyik szintről a másikra vált.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Fejlesztési keretek
Az App Service-díjszabási szintek szabályozzák az alkalmazások számára elérhető számítási erőforrások (PROCESSZOR, lemezes tárolás, memória és hálózati kimenő forgalom) mennyiségét. Azonban az alkalmazások számára elérhető keretrendszeri funkciók szélessége a skálázási szintektől függetlenül ugyanaz marad.

Az App Service számos fejlesztési keretrendszert támogat, beleértve a ASP.NET, a klasszikus ASP, a node.js, a PHP és a Python - amelyek mindegyike az IIS-en belüli bővítményekként fut. A biztonsági konfiguráció egyszerűsítése és normalizálása érdekében az App Service-alkalmazások általában az alapértelmezett beállításokkal futtatják a különböző fejlesztési keretrendszereket. Az alkalmazások konfigurálásának egyik módszere lehetett volna az API-felület és az egyes fejlesztési keretrendszerek funkcióinak testreszabása. Az App Service ehelyett általánosabb megközelítést alkalmaz az operációs rendszer funkcióinak közös alapkonfigurációjának engedélyezésével, függetlenül az alkalmazás fejlesztési keretrendszerétől.

A következő szakaszok összefoglalják az App Service-alkalmazások számára elérhető általános operációsrendszer-funkciókat.

<a id="FileAccess"></a>

## <a name="file-access"></a>Fájlhozzáférés
Az App Service-en belül különböző meghajtók léteznek, beleértve a helyi meghajtókat és a hálózati meghajtókat.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Helyi meghajtók
Az App Service egy szolgáltatás, amely az Azure PaaS (platform szolgáltatásként) infrastruktúrán fut. Ennek eredményeképpen a virtuális géphez "csatlakoztatott" helyi meghajtók ugyanazok a meghajtótípusok, amelyek az Azure-ban futó feldolgozói szerepkörszámára elérhetők. Az érintett műveletek közé tartoznak az alábbiak:

- Az operációs rendszer meghajtója (a D:\ meghajtó)
- Olyan alkalmazásmeghajtó, amely kizárólag az App Service által használt (és az ügyfelek számára elérhetetlen) Azure Package cspkg fájlokat tartalmaz
- A "felhasználó" meghajtó (a C:\ meghajtó), amelynek mérete a virtuális gép méretétől függ. 

Fontos, hogy az alkalmazás növekedésével figyelje a lemezkihasználtságot. Ha eléri a lemezkvótát, az káros hatással lehet az alkalmazásra. Példa: 

- Előfordulhat, hogy az alkalmazás hibát jelez, amely azt jelzi, hogy nincs elég hely a lemezen.
- A Kudu konzolon való böngészés során lemezhibák jelenhetnek meg.
- Az Azure DevOps vagy a `ERROR_NOT_ENOUGH_DISK_SPACE: Web deployment task failed. (Web Deploy detected insufficient space on disk)`Visual Studio központi telepítése sikertelen lehet a használatával.
- Az alkalmazás teljesítménye lassú lehet.

<a id="NetworkDrives"></a>

### <a name="network-drives-aka-unc-shares"></a>Hálózati meghajtók (más néven UNC megosztások)
Az App Service egyik egyedi aspektusa, amely egyszerűvé teszi az alkalmazások üzembe helyezését és karbantartását, hogy az összes felhasználói tartalom UNC-megosztásokon tárolódik. Ez a modell jól leképezi a több terheléselosztással rendelkező kiszolgálókat tartalmazó helyszíni webüzemeltetési környezetek által használt tartalomtárolás közös mintájához. 

Az App Service-en belül az egyes adatközpontokban létrehozott UNC-megosztások száma található. Az egyes adatközpontokban lévő összes ügyfél felhasználói tartalmának egy százaléka az egyes UNC-megosztásokhoz van hozzárendelve. Továbbá az egyetlen ügyfél előfizetésének összes fájltartalma mindig ugyanarra az UNC-megosztásra kerül. 

Az Azure-szolgáltatások működésének köszönhetően az UNC-megosztás üzemeltetéséért felelős virtuális gép idővel megváltozik. Garantált, hogy az UNC-megosztásokat különböző virtuális gépek fogják csatlakoztatni, mivel azok az Azure-műveletek szokásos folyamata során fel- és lekerülnek. Emiatt az alkalmazások soha nem szabad, hogy a kódolt feltételezéseket, hogy a gép adatait egy UNC fájl elérési útja stabil marad az idő múlásával. Ehelyett az App Service által biztosított kényelmes *faux* abszolút elérési utat kell **használnia.** Ez a faux abszolút elérési út egy hordozható, alkalmazás-és-felhasználó-agnosztikus módszert biztosít a saját alkalmazásra való hivatkozáshoz. A **D:\home\site**használatával a megosztott fájlokat anélkül vihetjük át az alkalmazásból az alkalmazásba, hogy minden egyes átvitelhez új abszolút elérési utat kellene konfigurálni.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Az alkalmazáshoz megadott fájlhozzáférés típusai
Minden ügyfél előfizetése egy adatközponton belül egy adott UNC-megosztáson foglalt könyvtárstruktúrával rendelkezik. Előfordulhat, hogy egy ügyfél egy adott adatközponton belül több alkalmazást hoz létre, így az egyetlen ügyfél-előfizetéshez tartozó könyvtárak ugyanazon az UNC-megosztáson jönnek létre. A megosztás tartalmazhat könyvtárakat, például a tartalom, a hiba- és diagnosztikai naplók, valamint a forrásellenőrzés sel létrehozott alkalmazás korábbi verziói. Ahogy az várható volt, az ügyfél alkalmazáskönyvtárak állnak rendelkezésre olvasási és írási hozzáférés futásidőben az alkalmazás kódja.

Az alkalmazást futtató virtuális géphez csatlakoztatott helyi meghajtókon az App Service egy darab helyet foglal a C:\ ideiglenes helyi tárolóhoz. Bár egy alkalmazás teljes olvasási/írási hozzáféréssel rendelkezik a saját ideiglenes helyi tárolójához, a tárolót valójában nem közvetlenül az alkalmazáskód használja. Ehelyett a szándék az, hogy ideiglenes fájltárolást biztosítson az IIS és a webalkalmazás-keretrendszerek számára. Az App Service az egyes alkalmazások számára rendelkezésre álló ideiglenes helyi tárhely mennyiségét is korlátozza, hogy megakadályozza, hogy az egyes alkalmazások túlzott mennyiségű helyi fájltárolást tegyenek.

Két példa arra, hogy az App Service hogyan használja az ideiglenes helyi tárolót az ideiglenes ASP.NET fájlok könyvtára és az IIS tömörített fájlok könyvtára. A ASP.NET fordítási rendszer a "Temporary ASP.NET Files" könyvtárat használja ideiglenes fordítási gyorsítótárhelyként. Az IIS az "IIS ideiglenes tömörített fájlok" könyvtárat használja a tömörített válaszkimenet tárolására. Mindkét típusú fájlhasználat (valamint mások) újra levan képezve az App Service alkalmazásonkénti ideiglenes helyi tároláshoz. Ez az újrahozzárendelés biztosítja, hogy a funkcionalitás a várt módon folytatódjon.

Az App Service minden egyes alkalmazása véletlenszerűen, egyedi, alacsony jogosultsági szintű munkaszintű munkavégző [https://www.iis.net/learn/manage/configuring-security/application-pool-identities](https://www.iis.net/learn/manage/configuring-security/application-pool-identities)folyamatidentitásként fut, amelyet "alkalmazáskészlet-identitásnak" neveznek, amelyet a következőképpen ismertetett: . Az alkalmazáskód ezt az identitást használja az operációs rendszer meghajtójának (d:\ meghajtó). Ez azt jelenti, hogy az alkalmazáskód felsorolhatja a közös könyvtárstruktúrákat, és olvashatja a gyakori fájlokat az operációs rendszer meghajtóján. Bár ez úgy tűnhet, hogy egy kissé széles szintű hozzáférést, ugyanazok at és fájlokérhetők el, ha egy feldolgozói szerepkör t egy Azure üzemeltetett szolgáltatás, és olvassa el a meghajtó tartalmát. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Fájlhozzáférés több példányban
A kezdőkönyvtár tartalmazza az alkalmazás tartalmát, és az alkalmazáskód írhat rá. Ha egy alkalmazás több példányon fut, a kezdőkönyvtár meg van osztva az összes példány között, így minden példány ugyanazt a könyvtárat látja. Így például, ha egy alkalmazás menti a feltöltött fájlokat a kezdőkönyvtárba, ezek a fájlok azonnal elérhetők az összes példány számára. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Hálózati hozzáférés
Az alkalmazáskód TCP/IP és UDP-alapú protokollok segítségével kimenő hálózati kapcsolatokat létesíthet a külső szolgáltatásokat elérhető, internetről elérhető végpontokkal. Az alkalmazások ugyanezeket a protokollokat használhatják az Azure-on belüli szolgáltatásokhoz való csatlakozáshoz, például HTTPS-kapcsolatok létrehozásával az SQL Database-hez.

Az alkalmazások korlátozott mértékben hozhatnak létre egy helyi visszacsatolási kapcsolatot, és rendelkeznek egy alkalmazásfigyeléssel az adott helyi visszacsatolási szoftvercsatornán. Ez a funkció elsősorban azért létezik, hogy a helyi visszacsatolási szoftvercsatornákon a funkcionalitásuk részeként figyelő alkalmazásokat engedélyezzen. Minden alkalmazás "privát" visszacsatolási kapcsolatot lát. Az "A" alkalmazás nem tudja meghallgatni a "B" alkalmazás által létrehozott helyi visszacsatolási szoftvercsatornát.

A névvel ellátott csövek et egy folyamatközi kommunikációs (IPC) mechanizmusként is támogatják az alkalmazást együttesen futó különböző folyamatok között. Az IIS FastCGI modul például elnevezett csövekre támaszkodik a PHP-oldalakat futtató egyes folyamatok koordinálásához.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Kódfuttatás, folyamatok és memória
Ahogy korábban említettük, az alkalmazások alacsony jogosultsági szintű munkavégző folyamatokon belül futnak egy véletlenszerű alkalmazáskészlet-identitás használatával. Az alkalmazáskód hozzáfér a munkavégző folyamathoz kapcsolódó memóriaterülethez, valamint a CGI-folyamatok vagy más alkalmazások által létrehozandó gyermekfolyamatokhoz. Azonban egy alkalmazás nem férhet hozzá egy másik alkalmazás memóriájához vagy adataihoz, még akkor sem, ha ugyanazon a virtuális gépen található.

Az alkalmazások futtathatnak parancsfájlokat vagy támogatott webfejlesztési keretrendszerekkel írt lapokat. Az App Service nem konfigurálja a webes keretrendszer beállításait korlátozottabb üzemmódokra. Például ASP.NET appon futó alkalmazások "teljes" megbízhatósági kapcsolatban futnak, szemben a korlátozottabb megbízhatósági módban. A webes keretrendszerek, beleértve a klasszikus ASP-t és a ASP.NET is, meghívhatják a folyamaton belül lévő COM-összetevőket (de nem a folyamaton kívüli COM-összetevőket), például az ADO (ActiveX Data Objects) rendszereket, amelyek alapértelmezés szerint regisztrálva vannak a Windows operációs rendszeren.

Az alkalmazások tetszőleges kódot futtathatnak és futtathatnak. Egy alkalmazás számára lehetővé teszi, hogy olyan műveleteket végez, mint a parancshéj és egy PowerShell-parancsfájl futtatása. Annak ellenére azonban, hogy tetszőleges kód és folyamatok hozhatnak létre egy alkalmazásból, a végrehajtható programok és parancsfájlok továbbra is a szülőalkalmazáskészlet számára biztosított jogosultságokra korlátozódnak. Például egy alkalmazás hozhat létre egy végrehajtható fájlt, amely kimenő HTTP-hívást hajt végre, de ugyanez a végrehajtható fájl nem kísérelheti meg a virtuális gép IP-címének lekötését a hálózati adapterről. Kimenő hálózati hívás esetén engedélyezett az alacsony jogosultsági szintű kód, de a hálózati beállítások újrakonfigurálása egy virtuális gépen rendszergazdai jogosultságokat igényel.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Diagnosztikai naplók és események
A naplóadatok egy másik adathalmaz, amelyhez egyes alkalmazások megpróbálnak hozzáférni. Az App Service-ben futó kód számára elérhető naplóadatok típusai közé tartoznak az alkalmazás által létrehozott diagnosztikai és naplóadatok, amelyek szintén könnyen hozzáférhetők az alkalmazás számára. 

Egy aktív alkalmazás által létrehozott W3C HTTP-naplók például elérhetők az alkalmazáshoz létrehozott hálózati megosztási hely naplókönyvtárában, vagy a blob storage-ban, ha az ügyfél beállította a W3C naplózást a tárolóba. Az utóbbi lehetőség lehetővé teszi a nagy mennyiségű naplók gyűjtése kockázata nélkül a fájl tárolási korlátok kapcsolódó hálózati megosztást.

Hasonló módon a .NET-alkalmazások valós idejű diagnosztikai információi is naplózhatók a .NET nyomkövetési és diagnosztikai infrastruktúrával, és lehetőség van a nyomkövetési adatok írására az alkalmazás hálózati megosztására, vagy egy blob tárolási helyére.

Az alkalmazások számára nem elérhető diagnosztikai naplózási és nyomkövetési területek a Windows ETW-események és a gyakori Windows-eseménynaplók (például Rendszer-, alkalmazás- és biztonsági eseménynaplók). Mivel az ETW nyomkövetési információ potenciálisan látható gépszerte (a megfelelő ACL-k), olvasási és írási hozzáférést ETW események blokkolva vannak. Előfordulhat, hogy a fejlesztők észreveszik, hogy az ETW-események és a gyakori Windows-eseménynaplók olvasására és írására irányuló API-hívások működnek, de ez azért van, mert az App Service "megjátssza" a hívásokat, hogy sikeresnek tűnjenek. A valóságban az alkalmazáskód nem fér hozzá az eseményadatokhoz.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Rendszerleíró adatbázis hoz való hozzáférés
Az alkalmazások csak olvasható hozzáféréssel rendelkeznek a virtuális gép rendszerleíró adatbázisának sok (bár nem az összes) eléréséhez. A gyakorlatban ez azt jelenti, hogy a rendszerleíró kulcsok, amelyek lehetővé teszik az írásvédett hozzáférést a helyi felhasználók csoport számára elérhetők az alkalmazások számára. A beállításjegyzék egyik területe, amely jelenleg nem támogatott olvasási\_vagy\_írási hozzáférésesetén, a HKEY CURRENT USER struktúra.

A rendszerleíró adatbázis írási hozzáférése le van tiltva, beleértve a felhasználónkénti beállításkulcsokhoz való hozzáférést is. Az alkalmazás szempontjából a beállításjegyzékhez való írási hozzáférés soha nem lehet hivatkozni az Azure-környezetben, mivel az alkalmazások különböző virtuális gépek között áttelepíthetők (és nem is telepíthetők). Az egyetlen állandó írható tároló, amely függhet egy alkalmazás az alkalmazásonkénti tartalom könyvtárszerkezet az App Service UNC-megosztások on tárolt. 

## <a name="remote-desktop-access"></a>Távoli asztali hozzáférés

Az App Service nem biztosít távoli asztali hozzáférést a virtuálisgép-példányokhoz.

## <a name="more-information"></a>További információ

[Azure App Service sandbox](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) – A legfrissebb információkat az App Service végrehajtási környezetében. Ezt az oldalt közvetlenül az App Service fejlesztői csapata tartja karban.

