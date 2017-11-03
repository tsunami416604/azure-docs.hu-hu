---
title: "BizTalk Server EDI megoldások történő BizTalk szolgáltatások műszaki útmutatója |} Microsoft Docs"
description: "EDI át MABS; A Microsoft Azure BizTalk szolgáltatások"
services: biztalk-services
documentationcenter: na
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 61c179fa-3f37-495b-8016-dee7474fd3a6
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 1b70fc3d199d7f1521acb534dafec8fb3e69500e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="migrating-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>BizTalk Server EDI megoldások történő BizTalk szolgáltatás: technikai útmutató

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Szerző: Tim Wieman és Nitin Mehrotra

A felülvizsgálók: Karthik Bharthy

Használatával készítettek: Microsoft Azure BizTalk szolgáltatások – 2014. február kiadási.

## <a name="introduction"></a>Bevezetés
Elektronikus adatcsere (EDI) az egyik a legjellemzőbb azt jelenti, hogy mely vállalatok számára az exchange-adatok által elektronikus úton, vállalatok vagy B2B tranzakcióként is hívják. BizTalk Server volt-e a kezdeti BizTalk Server kiadása óta egy évtizedben keresztül támogatása EDI. BizTalk szolgáltatások, a Microsoft EDI-megoldások támogatása a Microsoft Azure platformon továbbra is. B2B tranzakciók többnyire külső a szervezetek számára, és így egyszerűbb, ha megtörtént a felhő platform is. A Microsoft Azure lehetővé teszi a BizTalk szolgáltatások segítségével.

Egyes ügyfelek BizTalk szolgáltatások új EDI-megoldások "greenfield" platformként tekinti meg, amíg a sok ügyfél rendelkezik, akkor előfordulhat, hogy szeretne áttérni az Azure aktuális BizTalk Server EDI-megoldások. BizTalk szolgáltatások EDI van tervezett mert alapuló BizTalk Server EDI-architektúra (kereskedelmi a partnerek, entitásokat, szerződések), mint az azonos kulcs entitásokat is lehet BizTalk Server EDI-összetevők át BizTalk szolgáltatások.

Ez a dokumentum ismerteti a BizTalk szolgáltatások áttelepítése BizTalk Server EDI-összetevők szerepet játszó különbségek egy részét. Ez a dokumentum a BizTalk Server EDI-feldolgozás és Kereskedelmipartner-egyezmények ismeretét feltételezi. BizTalk Server EDI további információkért lásd: [Kereskedelmipartner-kezelés használatával BizTalk partnerkiszolgáló](https://msdn.microsoft.com/library/bb259970.aspx).

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>BizTalk Server EDI-összetevők verziójának BizTalk szolgáltatás telepíthető át?
A BizTalk Server EDI modul jelentősen bővült a BizTalk Server 2010, amikor a partnerek, profilok és megállapodások újra lett modellezett. BizTalk szolgáltatások ugyanannak a modellnek a kereskedelmi partnereknek és a vállalati részlegeinek a kereskedelmi partnereknek belül rendszerezéséhez használja. Ennek eredményeképpen EDI-összetevőket telepít BizTalk Server 2010 és újabb verziók BizTalk szolgáltatások, az egy sokkal több egyszerű folyamat. BizTalk Server 2010 korábbi verziók társított EDI-összetevők áttelepítéséhez először frissítsen a BizTalk Server 2010, és telepítse át a EDI-összetevők BizTalk szolgáltatások.

## <a name="scenariosmessage-flow"></a>Forgatókönyv/üzenet folyamata
Mint a BizTalk Server EDI-feldolgozás alatt álló BizTalk szolgáltatások épül kereskedelmi Partner (TPM) megoldás. A TPM-megoldás a következő fő részből áll:

* Kereskedelmi partnereknek, amelyek szervezetet képviseljék a B2B tranzakció.
* Profilok, amelyek megfelelnek a kereskedelmi partner osztályai.
* Kereskedelmipartner-egyezmények (vagy szerződések), amely képviseli az üzleti között létrejött, a két partner/profil.

Az alábbi ábra mutatja be, a szolgáltatások, valamint a a BizTalk Server EDI-megoldás és a BizTalk szolgáltatások EDI-megoldás közötti különbségek:

![][EDImessageflow]

A fontosabb különbségeket, és egy EDI megoldás folyamata a BizTalk Server és a BizTalk szolgáltatások Hasonlóságok a következők:

* BizTalk Server EDI-üzenetben és egy EDISend csővezeték EDI üzenet küldhető egy EDIReceive folyamat használja, ahogy a BizTalk szolgáltatások egy EDI-fogadási híd fogadásához és EDI küldése híd EDI-üzenetek küldése használja. BizTalk Server a folyamatok társított szerződés küldési használatával, vagy portok fogadására. BizTalk szolgáltatások a megállapodás azt jelzi, a Küldés vagy híd fogadni.
* BizTalk Server a EDIReceive folyamat folyamatok EDI üzenet után az üzenet kiírja az SQL Server-adatbázishoz. A EdiSend folyamat majd szerzi be az üzenetet az SQL Server-adatbázisból, folyamatokat engedélyez, és ezután elküldi policies.
  
    BizTalk szolgáltatások Miután megkapják az EDI híd feldolgozza a EDI-üzenetet, továbbítja az az üzenet egy külső folyamatban. A külső folyamat sikerült futtató Microsoft Azure vagy a helyszínen. A külső folyamat kell továbbítani az üzenetet a EDI-küldési híd; a Küldés híd eredendően kéri le az üzenetet. Az üzenet feldolgozása után a EDI-küldési híd irányítja az üzenet policies.

BizTalk szolgáltatások hozhat létre és központi telepítése egy kereskedelmi partnerek nélkül konfigurálása a Microsoft Azure számítás-példányok (webes vagy feldolgozói szerepkörök), a Microsoft Azure SQL-adatbázisok vagy a Microsoft Azure storage-fiókok B2B megállapodást könnyen használható konfigurációs élményt nyújt. Összetettebb forgatókönyveket szükséges a munkafolyamatok vagy más szolgáltatás-feldolgozás Ön "szélei körül" egy Kereskedelmipartner-egyezmény, ez azt jelenti, hogy előtt vagy után kereskedelmi Partner megállapodás EDI híd feldolgozása. Részletes a következő eseménysorozat elő egy BizTalk szolgáltatások EDI üzenetfeldolgozás során.

1. EDI-üzenetet kapott kereskedelmi partner, a Fabrikam.  BizTalk szolgáltatások támogatja például FTP SFTP, AS2 vagy HTTP/s átviteli protokollt EDI-üzenetek fogadása kereskedelmi partnereknek,
2. A kereskedelmi partner megállapodás fogadóoldali feldolgozás visszafejti a EDI üzenet XML-formátuma.  A Service Bus-végpontokat, például egy Service Bus Relay végpont, a Service Bus-témakörbe, a Service Bus-üzenetsorba vagy a BizTalk szolgáltatások hidat irányítani tudja a szétszerelt EDI-üzenet (XML formátumban).
3. A szétszerelt XML-üzenetek majd kapott a végpont további egyéni feldolgozásra.  Ezeket a végpontokat például egy helyszíni összetevő vagy egy Microsoft Azure számítás-példányt további feldolgozását a Windows Workflow (WF) vagy a Windows Communication Foundation (WCF) szolgáltatást, az üzenet lehetett feldolgozni.
4. A "küldési-oldalon feldolgozási" Policies a szerződés majd állítja össze az XML-üzenet EDI-formátumba, és elküldi kereskedelmi partner, Contoso.  Kereskedelmi partnereknek EDI üzenetküldésre, BizTalk szolgáltatások támogatja ugyanazokat a protokollokat, mint EDI-üzenetek fogadására.

Ez a dokumentum további elméleti útmutatást nyújt az áttelepítés néhányat a különböző BizTalk Server EDI BizTalk szolgáltatások.

## <a name="sendreceive-ports-to-trading-partners"></a>Küldési/fogadási portok kereskedelmi partnerek számára
BizTalk Server kap helyek és a fogadási portok EDI/XML-üzenetek fogadása kereskedelmi partnereknek beállította, és EDI/XML-üzeneteket küldhet kereskedelmi partner küldési portok beállítása. Majd lefoglalják ezeket a portokat a kereskedelmipartner-egyezmény a BizTalk Server felügyeleti konzol használatával. BizTalk szolgáltatások, a helyeken, ahol üzeneteket fogadhat a kereskedelmi partnerek és ahol küldünk a kereskedelmi partnerek üzenetek vannak konfigurálva a kereskedelmipartner-egyezmény (az átviteli beállítások részeként) magát a BizTalk szolgáltatások portálon részeként.  Ezért valóban nincs "küldési portok" és "fogadásához helyek" fogalmát önmagában, a BizTalk szolgáltatások. További információkért lásd: [létrehozása megállapodások](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx).

## <a name="pipelines-bridges"></a>Folyamatok (hidak)
BizTalk Server EDI, a folyamatok üzenet feldolgozása entitások egyedi feldolgozása képességeit, az alkalmazás által megkövetelt egyéni logikát is használható. BizTalk szolgáltatások egyenértékű egy EDI híd lenne. Azonban a BizTalk szolgáltatások most a EDI hidak "bezárul".  Ez azt jelenti, hogy a saját egyéni tevékenységeket nem lehet hozzáadni egy EDI híd. Minden egyéni feldolgozási elvégzendő kívül a EDI híd az alkalmazás előtt vagy után az üzenet kerül, a híd a Kereskedelmipartner-egyezmény tagja. EAI-Összekötők hidak van egyéni feldolgozás elvégzéséhez. Ha azt szeretné, hogy egyéni feldolgozási, a EAI-Összekötők hidak előtt vagy után a dolgozza fel a EDI híd használhatja. További információkért lásd: [közé tartoznak az egyéni kód a hidak hogyan](https://msdn.microsoft.com/library/azure/dn232389.aspx).

A közzététel/előfizetés áramlását egyéni kód és/vagy a Service Bus üzenetkezelés üzenetsorok és témakörök a kereskedelmipartner-egyezmény a üzenet előtt vagy után a szerződés feldolgozza az üzenetet, és továbbítja a Service Bus-végpont használatával is beszúrhat.

Lásd: **forgatókönyvek/üzenet folyamat** ebben a témakörben az üzenet folyamata szabályt.

## <a name="agreements"></a>Szerződés
Ha jártas a BizTalk Server 2010 Trading Partner megállapodások EDI feldolgozásához használt, BizTalk szolgáltatások kereskedelmipartner-egyezmények keresés tisztában. A szerződés beállításai a legtöbb azonosak, és használja ugyanazokat a kifejezéseket. Bizonyos esetekben a szerződés beállítások sokkal egyszerűbbek ugyanazokat a beállításokat a BizTalk Server képest. A Microsoft Azure BizTalk szolgáltatások által támogatott X12, EDIFACT és AS2 átviteli.

Microsoft Azure BizTalk szolgáltatásokat is biztosít egy **TPM adatáttelepítés** eszközt, amellyel kereskedelmi partnereknek és megállapodások BizTalk Server kereskedelmi Partner modulból BizTalk Services portálra. A TPM adatáttelepítési eszköz eszközök csomag részeként, amely letölthető a [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057). A csomag is tartalmazza a fontos, hogy az eszközt, és alapvető hibaelhárítási információkat az eszköz használatával.

## <a name="schemas"></a>Sémák
BizTalk szolgáltatások EDI-sémák, amely használható a BizTalk szolgáltatások megoldások biztosít.  Ezenkívül BizTalk Server EDI-sémák is használható a BizTalk szolgáltatások, mert a gyökércsomópont EDI-sémájának legyen, a BizTalk Serveren, valamint a BizTalk szolgáltatások között. Így lesz közvetlenül a BizTalk Server EDI-sémák és azok a EDI megoldásokat fejleszt BizTalk szolgáltatásokkal történő használatát. Emellett letöltheti a sémák a [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057).

## <a name="maps-transforms"></a>A Maps (átalakítások)
BizTalk Server Maps a BizTalk szolgáltatások átalakítások nevezik. Leképezések áttelepítése a BizTalk Server BizTalk szolgáltatások az egyik összetettebb feladat eléréséhez (attól függően, hogy térkép összetettsége) lehet. BizTalk szolgáltatások használható leképezés eszköz eltér a BizTalk leképező. Annak ellenére, hogy a leképező többnyire ugyanúgy, alapul szolgáló leképezési formátuma különböző. A functoids (nevű **térkép műveletek** a BizTalk szolgáltatások) számára elérhető, valamint különböző a felhasználók.  Érvényben nem közvetlenül használható BizTalk térképre BizTalk szolgáltatások. Nem minden, a BizTalk Server rendszerben elérhető functoids is elérhető BizTalk szolgáltatások térkép műveletei.

### <a name="new-transform-operations"></a>Új átalakítási műveletek
Tűnhet, hogy a rendelkezésre álló átalakító térkép műveletek listája a BizTalk Server hozzárendelő igen különböző, miközben új módokat parancsokról ugyanazokhoz a feladatokhoz BizTalk szolgáltatások átalakítja rendelkezik. Például a BizTalk szolgáltatások átalakítja rendelkezik **listázási műveletei** érhető el. Ez nem volt elérhető a BizTalk leképező.  A **listázási műveletei** engedélyezése hozhat létre, és működik a "List", ha a lista egy olyan elemek (más néven "sort"), és ha egyes elemek rendelkezhet több tagot (más néven "oszlop").  Rendezheti a listát, válassza ki a cikkek alapján olyan feltétel, stb.

Egy másik példa a BizTalk szolgáltatások átalakítja az új funkciók vannak a **hurok műveletek**.  Nehéz a BizTalk Server hozzárendelő beágyazott hurkok létrehozására is.  Ebből kifolyólag a hurok térkép műveletek hozzáadott a BizTalk szolgáltatások alakítja.

Még egy másik példa a **Ha-akkor más** kifejezés térkép művelete.  Mert az if-majd más műveletet végez korábban az a BizTalk hozzárendelő, de szükség több functoids látszólag egyszerű feladat végrehajtására.

### <a name="migrating-biztalk-server-maps"></a>A Maps BizTalk-kiszolgáló áttelepítése
Microsoft Azure BizTalk biztosít egy eszközt, amellyel a BizTalk Server BizTalk szolgáltatások átalakítások van leképezve. A **BTMMigrationTool** áll rendelkezésre, mert része a **eszközök** csomag megadott a [BizTalk szolgáltatások SDK letöltési](http://go.microsoft.com/fwlink/p/?LinkId=235057). Az eszközzel kapcsolatos további információkért lásd: [BizTalk térképre átalakítása BizTalk szolgáltatások átalakítási](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx).

Egy minta Sandro Pereira, BizTalk MVP által történő is megtekintheti [BizTalk Server leképezések áttelepítése BizTalk szolgáltatások átalakítások](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## <a name="orchestrations"></a>Álló üzenettípusok összehangolását
Ha szeretné áttelepíteni a BizTalk Server vezénylési feldolgozása a Microsoft Azure, a álló üzenettípusok összehangolását kellene kell írni, mert a Microsoft Azure nem támogatja a futó BizTalk Server álló üzenettípusok összehangolását.  Sikerült írja át a vezénylési funkcióit a Windows Workflow Foundation 4.0 (WF4) szolgáltatás.  Mivel jelenleg nem áttelepítés BizTalk Server álló üzenettípusok összehangolását WF4 lenne egy teljes átdolgozás. Íme néhány forrás, a Windows-munkafolyamat:

* [*A WCF-munkafolyamat szolgáltatás integrálása a Service Bus-üzenetsorok és témakörök* ](https://msdn.microsoft.com/library/azure/hh709041.aspx) Paolo Salvatori által. 
* [*A Windows Workflow Foundation és az Azure-alkalmazások építése* munkamenet](http://go.microsoft.com/fwlink/p/?LinkId=237314) a Build 2011 konferencia a.
* [*A Windows folyamatkövető alaprendszer fejlesztői központja* ](http://go.microsoft.com/fwlink/p/?LinkId=237315) az MSDN Webhelyén.
* [*Windows munkafolyamat Foundation 4 (WF4) dokumentációjában* ](https://msdn.microsoft.com/library/dd489441.aspx) az MSDN Webhelyén.

## <a name="other-considerations"></a>Egyéb szempontok
Az alábbiakban néhány olyan szempontot, amelyek meg kell nyitnia a BizTalk szolgáltatás használata során.

### <a name="fallback-agreements"></a>Tartalék megállapodások
BizTalk Server EDI feldolgozási rendelkezik "Tartalék megállapodások" fogalmát.  BizTalk szolgáltatások does **nem** egy tartalék megállapodás fogalom, amennyiben rendelkezik.  BizTalk dokumentáció témakörök [EDI feldolgozásakor szerződések a a szerepkör](http://go.microsoft.com/fwlink/p/?LinkId=237317) és [globális konfigurálása vagy tartalék megállapodás tulajdonságok](https://msdn.microsoft.com/library/bb245981.aspx) információ a tartalék megállapodások használata a BizTalk Server.

### <a name="routing-to-multiple-destinations"></a>A több célhoz rendelt Útválasztás
BizTalk szolgáltatások hidak, a jelenlegi állapotában nem támogatja a közzététel használatával több helyre üzenetek-modell előfizetés. Ehelyett sikerült továbbítani a Service Bus-témakörbe, amely majd több előfizetést az jelenik meg több végpont a BizTalk szolgáltatások hidat üzenetek.

## <a name="conclusion"></a>Összegzés
A Microsoft Azure BizTalk szolgáltatások rendszeres mérföldkövek további funkciók és képességek hozzáadása, frissül. Minden egyes frissítés Kíváncsian támogató BizTalk szolgáltatások és egyéb Azure technológiák segítségével végpontok közötti megoldások megkönnyítésére szolgáltatásokat.

## <a name="see-also"></a>Lásd még:
[Vállalati alkalmazások Azure programmal](https://msdn.microsoft.com/library/azure/hh674490.aspx)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png
