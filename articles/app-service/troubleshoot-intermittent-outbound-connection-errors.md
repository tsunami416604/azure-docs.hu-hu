---
title: Időszakos kimenő csatlakozási hibák elhárítása az Azure App Service-ben
description: Az Azure App Service időszakos csatlakozási hibáinak és a kapcsolódó teljesítményproblémáknak a elhárítása
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 03/24/2020
ms.author: ramakoni
ms.custom: security-recommendations
ms.openlocfilehash: 028ddccdb989d35710e387081b08a3b973d75bdc
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80367550"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Időszakos kimenő csatlakozási hibák elhárítása az Azure App Service-ben

Ez a cikk segítséget nyújt az azure App Service időszakos csatlakozási hibáinak és a kapcsolódó teljesítményproblémáknak a [elhárításában.](https://docs.microsoft.com/azure/app-service/overview) Ez a témakör további információkat és hibaelhárítási módszereket tartalmaz a forráscím-hálózati fordítási (SNAT) portok kimerüléséről. Ha további segítségre van szüksége a cikk bármely pontján, forduljon az Azure szakértőihez az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Másik lehetőségként nyújtson be egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**

## <a name="symptoms"></a>Probléma

Az Azure App Service szolgáltatásban üzemeltetett alkalmazások és funkciók az alábbi jelenségek közül egyet vagy többet mutathatnak:

* Lassú válaszidő a szolgáltatási csomag összes vagy néhány példányán.
* Időszakos 5xx vagy **hibás átjáró** hibák
* Időeltetési hibaüzenetek
* Nem lehet csatlakozni külső végpontokhoz (például SQLDB, Service Fabric, egyéb alkalmazásszolgáltatások stb.)

## <a name="cause"></a>Ok

A jelenségek egyik fő oka, hogy az alkalmazáspéldány nem tud új kapcsolatot nyitni a külső végponthoz, mert elérte az alábbi korlátok egyikét:

* TCP-kapcsolatok: A kimenő kapcsolatok száma korlátozva van. Ez a használt dolgozó méretéhez van társítva.
* SNAT-portok: Az [Azure-beli kimenő kapcsolatokban](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)tárgyalt módon az Azure a forráshálózati címfordítást (SNAT) és a terheléselosztót (az ügyfelek számára nem elérhető) használja az Azure-on kívüli végpontokkal való kommunikációhoz a nyilvános IP-címtérben. Az Azure App-szolgáltatás minden példánya kezdetben **128** SNAT-port előre lefoglalt száma. Ez a korlát hatással van az azonos állomás- és portkombinációhoz való kapcsolatok megnyitására. Ha az alkalmazás kapcsolatokat hoz létre a cím- és portkombinációk keverékével, akkor nem használja fel az SNAT-portokat. Az SNAT-portok akkor használatosak, ha ugyanazt a címet és portkombinációt többször is meghívja. A port felszabadítása után a port szükség szerint újra felhasználható. Az Azure Network terheléselosztó csak 4 perc várakozás után szerzi vissza az SNAT-portot a lezárt kapcsolatokról.

Amikor az alkalmazások vagy funkciók gyorsan új kapcsolatot nyitnak meg, gyorsan kimeríthetik a 128 port előre lefoglalt kvótáját. Ezután le van tiltva, amíg egy új SNAT-port nem válik elérhetővé, akár további SNAT-portok dinamikus kiosztásával, akár egy visszanyert SNAT-port újrafelhasználásával. Azok az alkalmazások vagy függvények, amelyek azért vannak blokkolva, mert nem képesek új kapcsolatokat létrehozni, a cikk **Jelenségei** című szakaszban ismertetett egy vagy több problémát tapasztalnak.

## <a name="avoiding-the-problem"></a>A probléma elkerülése

Az SNAT-portproblémájának elkerülése azt jelenti, hogy elkerüljük az ugyanazon állomáshoz és porthoz ismétlődően új kapcsolatok létrehozását.

Az SNAT-portok kimerülésének csökkentésére vonatkozó általános stratégiákat az Azure **dokumentációjának kimenő kapcsolatainak** [Problémamegoldó szakaszában](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#problemsolving) tárgyaljuk. Ezek közül a stratégiák közül az alábbiak az Azure App-szolgáltatásban üzemeltetett alkalmazásokra és funkciókra vonatkoznak.

### <a name="modify-the-application-to-use-connection-pooling"></a>Az alkalmazás módosítása kapcsolatkészletezés használatára

* Http-kapcsolatok készletezéséhez tekintse át [a Http-kapcsolatokat a HttpClientFactory segítségével.](https://docs.microsoft.com/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory)
* Az SQL Server kapcsolatkészletezésről az [SQL Server kapcsolatkészletezés (ADO.NET) című, a kataista kiszolgálói kapcsolatkészletezés című oldalán talál.](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
* Az entitáskeretrendszer-alkalmazásokkal való összevonás megvalósításához tekintse át a [DbContext-készletezést.](https://docs.microsoft.com/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)

Itt van egy linkgyűjtemény a különböző megoldásverem kapcsolatkészletezésének megvalósításához.

#### <a name="node"></a>Csomópont

Alapértelmezés szerint a NodeJS-kapcsolatok nem maradnak életben. Az alábbiakban a népszerű adatbázisok és csomagok kapcsolat készletezés, amelyek példákat tartalmaznak, hogyan lehet megvalósítani őket.

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP Életben maradni

* [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [Node.js v13.9.0 Dokumentáció](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

Az alábbiakban a JDBC-kapcsolatkészlethez használt népszerű tárak találhatók, amelyek példákat tartalmaznak ezek megvalósítására: JDBC kapcsolatkészletezés.

* [Tomcat, 8.](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

HTTP-kapcsolat készletezése

* [Apache kapcsolatkezelés](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [OsztálykészletezésHttpClientConnectionManager](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

Bár a PHP nem támogatja a kapcsolatkészletezést, megpróbálhat állandó adatbázis-kapcsolatokat használni a háttérkiszolgálóval.
 
* MySQL szerver

   * [MySQLi kapcsolatok](https://www.php.net/manual/mysqli.quickstart.connections.php) újabb verziókhoz
   * [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php) a PHP régebbi verzióihoz

* Egyéb adatforrások

   * [PHP kapcsolatkezelés](https://www.php.net/manual/en/pdo.connections.php)

#### <a name="python"></a>Python

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools) (MEGJEGYZÉS: AZ SQLAlchemy a MicrosoftSQL Server en kívül más adatbázisokkal is használható)
* [HTTP Keep-alive](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)(A Keep-Alive automatikus, ha [munkamenet-objektumokat](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)használ).

Más környezetekben tekintse át a szolgáltatót vagy az illesztőprogram-specifikus dokumentumokat a kapcsolatkészletezés alkalmazásba való bevezetéséhez.

### <a name="modify-the-application-to-reuse-connections"></a>Az alkalmazás módosítása a kapcsolatok újrafelhasználásához

*  Az Azure-függvények kapcsolatainak kezelésével kapcsolatos további mutatókért és példákért tekintse meg [a Kapcsolatok kezelése az Azure Functions ben](https://docs.microsoft.com/azure/azure-functions/manage-connections).

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>Az alkalmazás módosítása kevésbé agresszív újrapróbálkozási logika használatára

* További útmutatásért és példákért tekintse át [az Újraminta mintát.](https://docs.microsoft.com/azure/architecture/patterns/retry)

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>Keepalives használatával alaphelyzetbe állíthatja a kimenő tétlen időoutot

* A Node.js alkalmazások keepalives alkalmazásának megvalósításához tekintse át [a Csomópont alkalmazás túlzott kimenő hívásokat.](https://docs.microsoft.com/azure/app-service/app-service-web-nodejs-best-practices-and-troubleshoot-guide#my-node-application-is-making-excessive-outbound-calls)

### <a name="additional-guidance-specific-to-app-service"></a>Az App Service-re vonatkozó további útmutatás:

* A [terhelési tesztnek](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test) valós adatokat kell szimulálnia állandó adagolási sebességmellett. Az alkalmazások és funkciók valós stressz alatt való tesztelése előre azonosíthatja és megoldhatja a SNAT-port kimerülésével kapcsolatos problémákat.
* Győződjön meg arról, hogy a háttérszolgáltatások gyorsan vissza tudják adni a válaszokat. Az Azure SQL-adatbázissal kapcsolatos teljesítményproblémák elhárításához tekintse át [az Azure SQL Database teljesítményhibáinak elhárítását az Intelligent Insights segítségével.](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance#recommended-troubleshooting-flow)
* Az App Service-csomag horizontális felskálázása további példányokra. A méretezésről további információt az [Alkalmazás méretezése az Azure App Service-ben című témakörben talál.](https://docs.microsoft.com/azure/app-service/manage-scale-up) Az alkalmazásszolgáltatási csomag minden egyes munkavégző példánya több SNAT-portot kap. Ha a használatot több példányközött osztja el, előfordulhat, hogy az SNAT-port használat példányonkénti használata az ajánlott 100 kimenő kapcsolatra vonatkozó korlát alatt van, egyedi távoli végpontonként.
* Fontolja meg az [App Service-környezet (ASE)](https://docs.microsoft.com/azure/app-service/environment/using-an-ase), ahol van rendelve egy kimenő IP-címet, és a korlátok a kapcsolatok és az SNAT-portok sokkal magasabbak.

A kimenő TCP-korlátok elkerülése könnyebben megoldható, mivel a korlátokat a dolgozó mérete határozza meg. A korlátokat a [Sandbox Cross VM numerikus korlátok – TCP-kapcsolatok](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits) mezőben láthatja.

|Korlát neve|Leírás|Kicsi (A1)|Közepes (A2)|Nagy (A3)|Elkülönített szint (ASE)|
|---|---|---|---|---|---|
|Kapcsolatok|Kapcsolatok száma a teljes virtuális gép között|1920|3968|8064|16000|

A kimenő TCP-korlátok elkerülése érdekében növelheti a dolgozók méretét, vagy horizontálisan horizontálisan skálázhatja.

## <a name="troubleshooting"></a>Hibaelhárítás

A kimenő kapcsolati korlátok két típusának és az alkalmazás által végzett lehetőségeknek a ismerete megkönnyíti a hibaelhárítást. Ha tudja, hogy az alkalmazás sok hívást kezdeményez ugyanarra a tárfiókra, sNAT-korlátra gyanakodhat. Ha az alkalmazás létrehoz egy nagy számú hívások végpontok az interneten, azt gyanítja, hogy eléri a virtuális gép korlátját.

Ha nem ismeri eléggé az alkalmazás viselkedését az ok gyors meghatározásához, az App Service-ben elérhető néhány eszköz és technika segít a meghatározásban.

### <a name="find-snat-port-allocation-information"></a>SNAT-portfoglalási adatok keresése

[Az App Service Diagnosztika](https://docs.microsoft.com/azure/app-service/overview-diagnostics) segítségével megkeresheti az SNAT-portok foglalási adatait, és megfigyelheti az App Service-webhelyek SNAT-portok foglalási metrikáját. Az SNAT-portfoglalási információk megkereséséhez kövesse az alábbi lépéseket:

1. Az App Service-diagnosztika eléréséhez keresse meg az App Service webappját vagy az App Service-környezetet az [Azure Portalon.](https://portal.azure.com/) A bal oldali navigációs sávon válassza **a Problémák diagnosztizálása és megoldása**lehetőséget.
2. Elérhetőségi és teljesítménykategória kiválasztása
3. Válassza az SNAT port kimerülése csempét a kategóriában elérhető csempék listájában. A gyakorlat az, hogy tartsa 128 alatt.
Ha szüksége van rá, továbbra is megnyithat egy támogatási jegyet, és a támogatási mérnök megkapja a metrikát a háttérrendszerből.

Vegye figyelembe, hogy mivel az SNAT-port használat nem érhető el metrikaként, nem lehet automatikus skálázás alapján SNAT port használat, vagy konfigurálni automatikus méretezés alapján SNAT portok foglalási metrika alapján.

### <a name="tcp-connections-and-snat-ports"></a>TCP-kapcsolatok és SNAT-portok

A TCP-kapcsolatok és az SNAT-portok nem kapcsolódnak közvetlenül egymáshoz. A TCP-kapcsolatok használati érzékelője megtalálható bármely App Service-webhely Problémák diagnosztizálása és megoldása panelen. Keresse meg a "TCP-kapcsolatok" kifejezést a kereséséhez.

* Az SNAT-portok csak külső hálózati folyamatokhoz használatosak, míg a teljes TCP-kapcsolatok helyi visszacsatolási kapcsolatokat tartalmaznak.
* Az SNAT-portokat különböző folyamatok is megoszthatják, ha a folyamatok protokollban, IP-címben vagy portban eltérőek. A TCP-kapcsolatok metrika minden TCP-kapcsolatot megszámol.
* A TCP-kapcsolatok korlátja a munkavégző példány szintjén történik. Az Azure Network kimenő terheléselosztás nem használja a TCP-kapcsolatok metrika SNAT-port korlátozása.
* A TCP-kapcsolatok korlátait a [Sandbox Cross VM numerikus korlátok – TCP-kapcsolatok](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits) ismertetik.

|Korlát neve|Leírás|Kicsi (A1)|Közepes (A2)|Nagy (A3)|Elkülönített szint (ASE)|
|---|---|---|---|---|---|
|Kapcsolatok|Kapcsolatok száma a teljes virtuális gép között|1920|3968|8064|16000|

### <a name="webjobs-and-database-connections"></a>WebJobs és adatbázis-kapcsolatok
 
Ha az SNAT-portok kimerültek, ahol a WebJobs nem tud csatlakozni az Azure SQL-adatbázishoz, nincs metrika, amely megmutatja, hogy az egyes webalkalmazás-folyamatok hány kapcsolatot nyitnak meg. A problémás WebJob megkereséséhez helyezzen át több WebJobs-ot egy másik App Service-csomagba, hogy megtudja, javul-e a helyzet, vagy hogy probléma áll-e még fel a csomagok egyikében. Ismételje meg a folyamatot, amíg meg nem találja a problémás WebJob.Repeat the process until you find the problematikus WebJob.

### <a name="using-snat-ports-sooner"></a>SNAT-portok használata

Nem módosíthatja az Azure-beállításokat a használt SNAT-portok hamarabbi felszabadításához, mivel az összes SNAT-port az alábbi feltételek nek megfelel, és a viselkedés szándékosan jelenik meg.
 
* Ha bármelyik kiszolgáló vagy az ügyfél FINACK-ot küld, az [SNAT-port](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#tcp-snat-port-release) 240 másodperc elteltével felszabadul.
* Ha rst látható, az SNAT-port 15 másodperc elteltével feloldódik.
* Ha elérte az tétlen időtúllépés t, a port fel szabadul.
 
## <a name="additional-information"></a>További információ

* [SNAT appszolgáltatással](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Lassú alkalmazásteljesítménnyel kapcsolatos problémák elhárítása az Azure App Service-ben](https://docs.microsoft.com/azure/app-service/troubleshoot-performance-degradation)
