---
title: BizTalk Server EDI-megoldások áttelepítése a BizTalk Services |} A Microsoft Docs
description: Ismerje meg, hogyan telepíthet át a Microsoft BizTalk Server EDI-megoldások a Microsoft Azure BizTalk Services (MABS)
services: biztalk-services
ms.service: biztalk-services
author: jonfancey
ms.author: jonfan
manager: jeconnoc
ms.topic: article
ms.date: 07/31/2018
ms.reviewer: jonfan, LADocs
ms.suite: integration
ms.openlocfilehash: 5543fd5ee2b86a57414a384df9d808e87b297a5e
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56983030"
---
# <a name="migrate-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>BizTalk Server EDI-megoldások áttelepítése a BizTalk Services: Műszaki útmutató

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Szerző: TIM Wieman és Nitin Mehrotra

Véleményezők: Karthik Bharthy

Használatával írt:  A Microsoft Azure BizTalk Services – 2014. februárban kiadás.

## <a name="introduction"></a>Bevezetés
Elektronikus adatcserére (EDI) az egyik a legelterjedtebb azt jelenti, hogy melyik vállalkozások exchange-adatok alapján elektronikus úton, vállalatok vagy B2B tranzakcióként is hívják. BizTalk Server EDI támogatása több mint egy évtizedes, mivel a kezdeti BizTalk Server-kiadás esetében. A BizTalk Services a Microsoft támogatást biztosít az EDI-megoldások a Microsoft Azure platformon továbbra is. B2B-tranzakciók többnyire egy szervezeten kívüli, és ezért az egyszerűbb, ha azt egy felhőalapú platformon lett megvalósítva. A Microsoft Azure BizTalk Services segítségével ezt a képességet biztosít.

Egyes ügyfeleink a BizTalk Services új EDI-megoldások "zöldmezős" platformként tekinti meg, míg számos ügyfelünk aktuális BizTalk Server EDI-megoldások, amelyek szeretnék áttelepíteni az Azure-bA. Mivel a BizTalk Services EDI tervezésnek lesz a azonos kulcsentitások (kereskedelmi partnerek, entitásokat, szerződések), a BizTalk Server EDI-architektúra alapján, lehetőség a BizTalk Services áttelepítése a BizTalk Server EDI-összetevők.

Ez a dokumentum ismerteti a BizTalk Services áttelepítése a BizTalk Server EDI-összetevőkkel kapcsolatos eltérések képest. Ez a dokumentum a BizTalk Server EDI-feldolgozást és kereskedelmi partneri szerződéseket ismeretét feltételezi. A BizTalk Server EDI további információkért lásd: [Partner Management használatával BizTalk Server kereskedelmi](https://msdn.microsoft.com/library/bb259970.aspx).

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>BizTalk Server EDI-összetevők verziójának telepíthető át a BizTalk Services?
A BizTalk Server EDI modul jelentősen bővült a BizTalk Server 2010-hez, amikor a partnerek, profilok és szerződések újra lett modellezett. A BizTalk Services ugyanannak a modellnek a kereskedelmi partnerekkel és a kereskedelmi partnereknek üzleti osztályai rendszerezéséhez használja. Ennek eredményeképpen EDI-összetevők áttérés a BizTalk Server 2010 és újabb verziókban a BizTalk Services, van egy sokkal nagyon egyszerű folyamatot. Áttelepítése a BizTalk Server 2010 korábbi verziók társított EDI-összetevők, először frissítse a BizTalk Server 2010, és majd áttelepítése a BizTalk Services az EDI-összetevőkhöz.

## <a name="scenariosmessage-flow"></a>Forgatókönyvek és üzenet folyamat
Mint a BizTalk Serverrel, EDI-feldolgozás a BizTalk Services épül Trading Partner Management (TPM) megoldás. A TPM-megoldás a következő főbb összetevőket tartalmazza:

* Kereskedelmi partnerek, amely szervezetet képviseljék a B2B-tranzakciók.
* Profilok, amelyek tartalmazzák a kereskedelmi partnerek osztályai.
* Kereskedelmi partneri szerződéseket (vagy szerződések), amelyek két partnerek és profilok között létrejött üzleti megállapodás képviselik.

Az alábbi ábrán a hasonlóságokat, valamint a különbség a BizTalk Server EDI-megoldások és a BizTalk Services EDI-megoldások között:

![][EDImessageflow]

A fő különbségeket, és a egy EDI megoldási folyamat a BizTalk Server és a BizTalk Services döntésünket a következők:

* Ugyanúgy, mint a BizTalk Server EDI-üzenetben és a egy EDISend folyamat EDI-üzenetben kap egy EDIReceive folyamatot használ, a BizTalk Services egy híd EDI kap fogadásához és EDI küldése hidat, EDI-üzenetek küldése használ. A BizTalk Server a folyamatok rendelt megállapodás küldési használatával, vagy a portok kap. A BizTalk Services-magát a szerződés azt jelzi, hogy a küldés, vagy a híd kap.
* A BizTalk Server a EDIReceive folyamat feldolgozza az EDI-üzenetet, miután az üzenet kiírja az SQL Server-adatbázis. A EdiSend folyamat majd szerzi be az üzenetet, az SQL Server-adatbázisból, folyamatokat és és továbbítja őket a kereskedelmi partner.
  
    A BizTalk Services Miután megkapják az EDI híd feldolgozza az EDI-üzenetet, és egy külső folyamatban az üzenet irányítja. A külső folyamatot a Microsoft Azure és a helyszínen is futhat. A külső folyamat kell továbbítsa az üzenetet az EDI-küldési híd; a Küldés híd természetüknél fogva nem kér az üzenetet. Az üzenet feldolgozása után az EDI-küldési híd irányítja az üzenetet a kereskedelmi partner.

BizTalk Services modullal járulékos egy könnyen használható konfigurációs feladatok gyors létrehozása és üzembe helyezése egy B2B megállapodást a kereskedelmi partnerekkel bármely Microsoft Azure számítási példány (webes és feldolgozói szerepkörök), a bármely Microsoft Azure SQL Database-adatbázishoz vagy a konfigurálás nélkül A Microsoft Azure storage-fiókok. Az összetettebb esetekhez szükséges a munkafolyamatok vagy más szolgáltatás feldolgozási összeköthető "szélei" egy kereskedelmi partneri szerződés, előtt, vagy kereskedelmi partneri szerződés EDI híd feldolgozása után. Részletesen a következő eseménysorozat fordulhat elő, az EDI-üzenetet a BizTalk Services feldolgozása során.

1. EDI-üzenetet kapott kereskedelmi partner, a Fabrikam.  EDI-üzenetek fogadása a kereskedelmi partnerekkel, a BizTalk Services támogatja az átviteli protokollokat, például az FTP, illetve SFTP, AS2 és a HTTP/s
2. A kereskedelmi partneri szerződés fogadóoldali feldolgozás visszafejti az XML formátumú EDI-üzenetet.  (Az XML-formátumú) szétszerelt EDI-üzenetet a Service Bus-végpontokat, például egy Service Bus Relay-végpontot, a Service Bus-témakör, a Service Bus-üzenetsor vagy a BizTalk Services hidat irányíthatja.
3. A szétszerelt XML-üzenetek majd kapott a végpontot a további egyéni feldolgozáshoz.  A végpontok például egy helyszíni összetevő vagy egy további folyamat az üzenet egy Windows-munkafolyamat (WF) vagy a Windows Communication Foundation (WCF) szolgáltatásban, a Microsoft Azure Compute-példány sikerült feldolgozni.
4. A "küldési kiszolgálóoldali feldolgozást" a kereskedelmi partnerek szerződés majd az XML-üzenet tartalomkiszolgálójáról EDI formátumban, és elküldi kereskedelmi partner, a Contoso.  A kereskedelmi partnerek EDI-üzenetek küldését, a BizTalk Services támogatja ugyanazokat a protokollokat, EDI-üzenetek fogadásához használ.

Ez a dokumentum további fogalmi útmutatást nyújt a néhányat a különböző BizTalk Server EDI áttelepítése a BizTalk Services.

## <a name="sendreceive-ports-to-trading-partners"></a>Küldése/fogadása portok a kereskedelmi partnerek
A BizTalk Server-Fogadóhely és a fogadás portok az EDI/XML-üzenetek fogadása a kereskedelmi partnerek beállította, és az EDI/XML-üzeneteket küldeni a kereskedelmi partner küldési portok beállítása. Ezután lefoglalhatnak ezeket a portokat a kereskedelmipartner-egyezmény a BizTalk Server felügyeleti konzol használatával. A BizTalk Services, a helyeken, ahol a kereskedelmi partnerekkel üzeneteket fog kapni, és ahol küld e-kereskedelmi partnerek üzenetek vannak konfigurálva a kereskedelmi partneri szerződést (az átviteli beállítások részeként) magát a BizTalk Services portálon részeként.  Így nem igazán kell a "küldési port" és "helyek megkapta", fogalmát önmagában, a BizTalk Services. További információkért lásd: [szerződések létrehozása](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx).

## <a name="pipelines-bridges"></a>A folyamatok (hidat)
A BizTalk Server EDI a folyamatok olyan üzenet feldolgozása entitások, amelyek meghatározott feldolgozási képességei, az alkalmazás által kért egyéni logikát is használható. A BizTalk Services-megfelelője lenne egy EDI híd. Azonban a BizTalk Services egyelőre az EDI hidak "bezárul".  EDI-híd azt jelenti, nem adja hozzá a saját egyéni tevékenységeket. Bármilyen egyéni feldolgozási kell elvégezni kívül az EDI-hidat, az alkalmazás előtt vagy után az üzenetet a híd a kereskedelmi partneri szerződés részeként beállított kerül. EAI-hidak egyéni feldolgozási nem rendelkezik. Ha azt szeretné, hogy egyéni feldolgozási, EAI-hidak használhatja, előtt vagy után az üzenetet a EDI híd dolgoz fel. További információkért lásd: [egyéni kód belefoglalása a hidak hogyan](https://msdn.microsoft.com/library/azure/dn232389.aspx).

Egyéni kód és/vagy a Service Bus-üzenetkezelés üzenetsorokat és Üzenettémákat, mielőtt a kereskedelmi partneri szerződés fogadja az üzenetet, vagy ha a szerződés feldolgozza az üzenetet, és továbbítja azt a Service Bus-végpont használatával közzétételi és előfizetési folyamat beszúrásához.

Lásd: **forgatókönyvek/Üzenetáramlás** ebben a cikkben, az üzenet a folyamat minta.

## <a name="agreements"></a>Egyezmények
Ha ismeri a BizTalk Server 2010 Trading Partner kimutatáshoz alapul szolgáló licencszerződések EDI-feldolgozást, majd a BizTalk Services kereskedelmipartner-egyezmények ismerős nagyon. A szerződés beállításai a legtöbb azonosak, és ugyanazokat a kifejezéseket használni. Bizonyos esetekben a szerződés beállítások a következők jóval egyszerűbb ugyanazokat a beállításokat a BizTalk Server képest. A Microsoft Azure BizTalk Services által támogatott X12, EDIFACT és az AS2 átviteli.

A Microsoft Azure BizTalk Services is biztosít egy **TPM adatáttelepítés** eszközt, amellyel kereskedelmi partnerek és egyezmények a BizTalk Server kereskedelmi partneri modulból a BizTalk Services portáljára. A TPM adatáttelepítési eszköz érhető el egy eszközcsomag részeként, amely letölthető a a [MABS SDK](https://go.microsoft.com/fwlink/p/?LinkId=235057). A csomag egy információs fájl, amely útmutatást ad az eszköz használata az eszközt, és alapvető információkat is tartalmaz.

## <a name="schemas"></a>Sémák
BizTalk Services modullal járulékos EDI-sémák, amely használható a BizTalk Services-megoldásokban.  Emellett a BizTalk Server EDI-sémák is használható a BizTalk Services segítségével mivel a legfelső szintű csomópontja az EDI-sémák azonos között a BizTalk Server, valamint a BizTalk Services. Így lesz közvetlenül a BizTalk Server EDI-sémák és a BizTalk Services fejleszthet EDI-megoldások használni őket. Is letöltheti a sémák, a [MABS SDK](https://go.microsoft.com/fwlink/p/?LinkId=235057).

## <a name="maps-transforms"></a>Térképek (átalakító)
A BizTalk Server Maps a BizTalk Services átalakítások nevezik. Leképezések áttelepítése a BizTalk Server a BizTalk Services eléréséhez (a map összetettsége) függően az összetettebb feladatok egyike lehet. A BizTalk Services használt térképkészítő eszközével eltér a BizTalk-végpontleképező. Annak ellenére, hogy a teljesítményleképező ugyanúgy néz ki, főleg, az alapul szolgáló térkép formátuma eltér. A functoids (nevű **térkép Operations** a BizTalk Services) érhető el, hogy a felhasználókat, valamint nem ugyanaz.  Érvényben nem közvetlenül használható a BizTalk-térkép a BizTalk Services. Nem minden elérhető a BizTalk Server functoids is elérhető a BizTalk Services térkép műveleteket.

### <a name="new-transform-operations"></a>Új átalakítási műveletek
Tűnhet, hogy a BizTalk Server eseményleképező igen különböző elérhető átalakítási térkép műveletek listáját, amíg a BizTalk Services alakítja át kell új módszereket mutatnak konzoljával végezhetők el a feladatot. Ha például a BizTalk Services alakítja át kell **listázási műveletek** érhető el. Ez nem volt elérhető a BizTalk-végpontleképező.  A **listázási műveletek** engedélyezése hozhat létre és művelethez egy "List", ahol az listáját (más néven "sor") egy tételcsoport, és ahol minden egyes cikk rendelkezhet több tagot (más néven "oszlop").  Is rendezheti a listát, jelölje ki a cikkek alapján egy feltételt, és így tovább.

Egy másik példa a BizTalk Services alakítja át az új funkciók vannak a **hurok Operations**.  Beágyazott hurkok létrehozása a BizTalk Server leképezőjét nehézséget jelent.  Így a hurok térkép műveletek hozzáadása a BizTalk Services alakítja át a.

Még egy másik példa a **If-majd más** kifejezés térkép művelete.  Ennek során egy if-majd más művelet nem volt lehetőség a BizTalk eseményleképező, de ez több functoids látszólag egyszerű feladat elvégzéséhez szükséges.

### <a name="migrating-biztalk-server-maps"></a>A BizTalk-kiszolgáló áttelepítése térképek
A Microsoft Azure BizTalk Services biztosítja, hogy egy eszköz a BizTalk-kiszolgáló áttelepítése a BizTalk Services átalakítások képez le. A **BTMMigrationTool** elérhető része a **eszközök** megadott csomag a [BizTalk Services SDK letöltése](https://go.microsoft.com/fwlink/p/?LinkId=235057). Az eszközzel kapcsolatos további információkért lásd: [BizTalk térkép átalakítása a BizTalk Services átalakítása](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx).

Emellett tekintse meg a mintát Sandro Pereira, BizTalk MVP, amelyet szeretne [BizTalk Server leképezések áttelepítése a BizTalk Services átalakítások](https://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## <a name="orchestrations"></a>Vezénylések
Ha szeretné áttelepíteni a BizTalk Server vezénylési feldolgozása a Microsoft Azure-ba, a vezénylések kell írni, mert a Microsoft Azure nem támogatja a futó BizTalk Server vezénylések kellene.  A vezénylési funkciót a Windows Workflow Foundation 4.0 (WF4) szolgáltatásként sikerült újraírása.  Ez lehet egy teljes újraírási jelenleg nincs áttelepítése a BizTalk Server vezénylések WF4 nem. Íme néhány forrás, a Windows-munkafolyamat:

* [*A munkafolyamat WCF-szolgáltatások integrálása a Service Bus-üzenetsorok és témakörök* ](https://blogs.msdn.microsoft.com/paolos/2013/04/09/how-to-integrate-a-wcf-workflow-service-with-service-bus-queues-and-topics/) Paolo Salvatori szerint. 
* [*Alkalmazások a Windows Workflow Foundation és az Azure* munkamenet](https://go.microsoft.com/fwlink/p/?LinkId=237314) a Build 2011 konferenciáról.
* [*Windows folyamatkövető alaprendszer fejlesztői központja*](https://docs.microsoft.com/previous-versions/dotnet/articles/ee342461(v=msdn.10)).
* [*Windows munkafolyamat Foundation 4 (WF4) dokumentációja* ](https://msdn.microsoft.com/library/dd489441.aspx) az MSDN Webhelyén.

## <a name="other-considerations"></a>Egyéb szempontok
Az alábbiakban néhány megfontolandó szempont a BizTalk Services használata közben kell végrehajtania.

### <a name="fallback-agreements"></a>Tartalék szerződések
BizTalk Server EDI-feldolgozást tartalmaz "Tartalék szerződések" fogalma.  A BizTalk Services does **nem** , amennyiben rendelkezik egy tartalék szerződés fogalom.  A BizTalk-dokumentáció témakörök [feldolgozása EDI szerződések a a szerepkör](https://go.microsoft.com/fwlink/p/?LinkId=237317) és [globális konfigurálása vagy tartalék megállapodás tulajdonságai](https://msdn.microsoft.com/library/bb245981.aspx) információ a tartalék szerződések használata a BizTalk A kiszolgáló.

### <a name="routing-to-multiple-destinations"></a>Több célhelyre Útválasztás
A BizTalk Services-hidat, a jelenlegi állapotában nem támogatja a útválasztási üzenetek, több célra irányuló használatával közzétételi-feliratkozási modell. Ehelyett sikerült irányíthatja a Service Bus-témakörbe, majd az üzenetet, egynél több végponton több előfizetéssel is rendelkezhet, amely a BizTalk Services hidat üzeneteket.

## <a name="see-also"></a>Lásd még:
[LOB-megoldások az Azure-ban](https://azure.microsoft.com/solutions/lob-applications)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png
