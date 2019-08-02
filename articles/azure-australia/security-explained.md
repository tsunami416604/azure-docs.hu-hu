---
title: Az Azure Australia biztonságának ismertetése
description: Az ausztráliai régiókkal kapcsolatos információk, amelyek megfelelnek az ausztráliai kormányzati szabályzatok, rendeletek és jogszabályok konkrét követelményeinek.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 212d14e31c152e50c216f2f34fb225c29fe054b5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571431"
---
# <a name="azure-australia-security-explained"></a>Az Azure Australia biztonságának ismertetése

Ez a cikk az ausztráliai kormányzati szerveknek a Microsoft Azure Ausztráliában való vizsgálatával, tervezésével és üzembe helyezésével kapcsolatos gyakori kérdéseket és területeket tárgyalja.

## <a name="irap-and-certified-cloud-services-list-ccsl-documents"></a>IRAP és Certified Cloud Services List (CCSL) dokumentumok

Az ausztrál Cyber Security Center (ASCS) tanúsítási, minősítési jelentést és használati útmutatót biztosít a szolgáltatáshoz, amikor hozzáadja őket a CCSL.

A Microsoft jelenleg az Azure, az Office 365 és a Dynamics 365 CRM CCSL van felsorolva.

A Microsoft a [Microsoft szolgáltatás-megbízhatósági portál](https://aka.ms/au-irap)Ausztrália-specifikus oldalán elérhetővé teszi a naplózási, értékelési és ASCs minősítési dokumentumokat az ügyfelek és partnerek számára.

## <a name="dissemination-limiting-markers-dlm-and-protected-certification"></a>A terjesztési jelölők (DLM-EK) és a védett minősítés korlátozása

A kormányzati szervezetek általi használatra jóváhagyott rendszerek, beleértve a Cloud Servicest is, az ausztrál Cyber Security Center (ASCS) által létrehozott és közzétett [információs biztonsági kézikönyvben (ISM)](https://acsc.gov.au/infosec/ism/) határozzák meg. Az ausztrál Cyber Security Center (ASCS) a Cyber biztonsági és a Felhőbeli minősítésért felelős ASD-en belüli entitás.

A jóváhagyási folyamat két lépésből áll:

1. Biztonsági értékelés (IRAP) – olyan folyamat, amelyben a regisztrált szakemberek a rendszereket, szolgáltatásokat és megoldásokat értékelik az ISM-es műszaki ellenőrzéseken, és kiértékelik, hogy a vezérlők hatékonyan lettek-e implementálva. Az értékelés azt is meghatározza, hogy a jóváhagyó hatóság milyen kockázatokkal jár a jóváhagyás megkezdése előtt.

1. Jóváhagyás a működéshez – az a folyamat, amelyben egy kormányzati szerv vezető tisztviselője hivatalosan felismeri és elfogadja a rendszer fennmaradó kockázatát az informatikai folyamatok, a tárolók és a kommunikáció során.  A folyamat bemenete a biztonsági Értékelés.

Az Azure-szolgáltatások védett szinten való értékelése azt jelzi, hogy a védett és az alábbi adatmennyiségek tárolásához és feldolgozásához szükséges biztonsági ellenőrzések megvalósítását megerősítettük, és hatékonyan működnek.

## <a name="australian-data-classification-changes"></a>Ausztrál adatbesorolási változások

2018. október 1-jén a legfőbb ügyészi részleg nyilvánosan bejelentette a védelmi házirend keretrendszerének (PSPF) változását, amely konkrétan új, [bizalmas és minősített információs rendszer](https://www.protectivesecurity.gov.au/information/sensitive-classified-information/Pages/default.aspx).

![Módosított PSPF besorolások](media/pspf-classifications.png)

Ezek a változások hatással vannak az összes olyan ausztrál Ügynökségre és szervezetre, amely a PSPF alatt működik. Az aktuális IRAP/CCSL-minősítéseket érintő elsődleges változás a jelenlegi terjesztési korlátozási jelölések (DLM-EK) kivonása. A megjelölés **hivatalos: A** bizalmas adatok védelméhez használt különféle DLMs lecseréli. A módosítás három olyan adatkezelési jelölőt is bevezetett, amelyek az összes hivatalos információra alkalmazhatók az érzékenység és a besorolás szintjén. A **védett** besorolás változatlan marad.

A nem besorolt kifejezés el lett távolítva az új rendszerből, és a nemhivatalos kifejezés nem kormányzati információra van alkalmazva, bár nem igényel formális jelölést.

## <a name="choosing-an-azure-region-for-my-official-sensitive-and-protected-workloads"></a>A hivatalos Azure-régió kiválasztása: Bizalmas és védett munkaterhelések

Az Azure **hivatalos: A** bizalmas és **védett** minősítésű szolgáltatások mind a négy ausztráliai adatközpont-régióban (Kelet-Ausztrália, Délkelet-Ausztrália, Közép-Ausztrália és Ausztrália 2. középen) vannak üzembe helyezve, de a minősítési jelentés a következő: A ASCS javasolja, hogy a **védett** adatközpontok üzembe helyezése a Canberra Azure Government régióiban történjen, ha egy szolgáltatás elérhető. A **védett** tanúsítvánnyal rendelkező Azure-szolgáltatásokkal kapcsolatos részletesebb információk az [Australia oldaláról érhetők el az STP](https://aka.ms/au-irap)-ben.

>[!NOTE]
>A Microsoft javasolja, hogy az összes érzékenységgel és besorolással kapcsolatos kormányzati adatai üzembe helyezése az Ausztrália középső és Ausztrália középső régiójában 2 régióban történjen, mivel azokat kifejezetten a kormányzat igényeihez tervezték és üzemeltetik.

Az Azure Australian-régiók különleges jellegével kapcsolatos további információk a következő címen érhetők el: [Ausztrália középső Azure-régiói](https://azure.microsoft.com/global-infrastructure/australia/).

## <a name="how-microsoft-separates-classified-and-official-data"></a>Hogyan különíti el a Microsoft a besorolt és a hivatalos adatgyűjtést

A Microsoft az Azure-t és az Office 365-et Ausztráliában működteti, mintha az összes adat bizalmas és/vagy besorolt legyen, és a biztonsági ellenőrzéseket a nagy sávban is feldolgozza.

Az Azure-t támogató infrastruktúra több besorolást is kiszolgálhat.  A feltételezés szerint azonban az ügyféladatok besoroltak, ezért a megfelelő vezérlők is érvényben vannak. A Microsoft olyan alapszintű biztonsági testhelyzetet vezetett be a szolgáltatásainkhoz, amely megfelel a **védett** minősített információk tárolására és feldolgozására vonatkozó PSPF követelményeknek.

Annak biztosítása érdekében, hogy az egyik bérlő az Azure-ban ne legyen kitéve más bérlők számára, a Microsoft átfogó védelmi jellegű ellenőrzéseket hajt végre.

A Microsoft Azure platformon belül megvalósított képességek mellett további, az ügyfél által felügyelt kulcsokkal, a beágyazott virtualizálás és az igény szerinti rendszergazdai hozzáféréssel rendelkező ügyfelek által konfigurálható vezérlők is csökkenthetik a kockázatot. A Canberra-beli Azure Government Ausztrália régiójában a formális engedélyezési folyamat csak az ausztrál & új-zélandi kormánya és a nemzeti kritikus fontosságú infrastruktúra-szervezetek számára van érvényben. Ez a közösségi felhő további garanciát nyújt a többek között a bérlői kockázatokra érzékeny szervezeteknek.

A Microsoft Azure **védett** minősítési jelentés megerősíti, hogy ezek a vezérlők érvényesek a **védett** minősített adatmennyiségek tárolásához és feldolgozásához, valamint azok elkülönítéséhez.

## <a name="relevance-of-irapccsl-to-state-government-and-critical-infrastructure-providers"></a>Az IRAP/CCSL relevanciája az állami kormányzati és a kritikus fontosságú infrastruktúra-szolgáltatók számára

Számos állami kormányzati és kritikus fontosságú infrastruktúra-szolgáltató a szövetségi kormányzati követelményeket beépíti a biztonsági szabályzatba és a megbízhatósági keretrendszerbe. Ezek a szervezetek emelletta hivatalos **, a hivatalos: Bizalmas** és a **védett** besorolású adatok mennyisége a szövetségi kormánnyal vagy a saját jogon való interakció alapján.

Az ausztrál kormány egyre nagyobb hangsúlyt fektet a nem kormányzati adatvédelemre, ami alapvetően hatással van az ausztráliai biztonságára és gazdasági fellendülésére. Ennek megfelelően az Azure Australia-régiók és a CCSL-minősítés az összes említett iparágra vonatkozik.

![Kritikus infrastruktúra-ágazatok](media/nci-sectors.png)

A Microsoft minősítései azt mutatják be, hogy az Azure-szolgáltatások alapos, szigorú és formális értékelésre kerültek a megfelelő biztonsági védelemről, és ezeket a szigorúan bizalmas adatok kezelésére jóváhagyták.

## <a name="location-and-control-of-microsoft-data-centres"></a>A Microsoft adatközpontjainak helye és felügyelete

A kormányzati és a kritikus fontosságú infrastruktúra kötelező követelménye, hogy explicit módon tájékoztassa az adatközpont helyét és tulajdonjogát a Cloud Services számára az adatfeldolgozás során.  A Microsoft egyedülálló nagy kapacitású-szolgáltatóként, amely részletes információkat nyújt ezekről a helyekről és tulajdonosokról.

A Microsoft Azure Australia-régiói (Ausztrália középső és ausztráliai középső régiója 2) a CDC Datacentres eszközein belül működnek.  A CDC Datacentres tulajdonosa a nemzetközösségi járadék Corporation 48%-os tulajdonjoga, 48%-os tulajdonjoga a Infratil-től (egy NZ-alapú, kettős ausztráliai és új-zélandi Értéktőzsde, a hosszú távú infrastruktúra-eszköz alapja) és 4% ausztrál felügyelet.  

A CDC Datacentres-kezelésének szerződéses garanciái vannak, és az ausztrál kormány a tulajdonjog és a felügyelet jövőbeli átruházását korlátozza. Az ellátási lánc és a tulajdonjog a Microsoft által a CDC Datacentres való együttműködésen keresztüli átláthatósága összhangban van a [kormányzati üzemeltetési stratégia](https://www.dta.gov.au/our-projects/whole-government-hosting-strategy) és a **Certified szuverén adatközpontot**definíciójának alapelveivel.

## <a name="the-azure-services-that-are-included-in-the-current-ccsl-certification"></a>Az aktuális CCSL-minősítés részét képező Azure-szolgáltatások

2017 júniusában a ASCs Certified 41 Azure-szolgáltatások az adattároláshoz és-feldolgozáshoz **a nem besorolt módon: DLM** szint. A fenti szolgáltatások 24. április 2018-án a **védett** besorolású adatmennyiséget tanúsították.

A ASCS Certified Azure-szolgáltatások rendelkezésre állása az ausztráliai Azure-régiókban az alábbiak szerint történik (a félkövérrel **védett**):

|Azure Australia – középső régiók|Nem regionális szolgáltatások és más régiók|
|---|---|
|API Management, app Gateway, alkalmazásszolgáltatás, **Automation, Azure Portal, Backup, batch, Cloud Services**, Cosmos db, Event Hubs, **ExpressRoute**, HDInsight, **Key Vault**, Load Balancer, log Analytics, **multi-Factor Hitelesítés**, Redis cache, **erőforrás-kezelő, Service Bus, Service Fabric, Site Recovery, SQL Database, tárolás**, Traffic Manager, **Virtual Machines, Virtual Network, VPN Gateway**|**Azure Active Directory**, CDN, Data Catalog, **Import export, Information Protection, IOT hub**, Machine Learning, Media Services, **Notification Hubs**, Power bi, **biztonsági központ, ütemező**, keresés, Stream Analytics|
|

A Microsoft közzéteszi [Microsoft Azure megfelelőségének áttekintését](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/44/Microsoft%20Azure%20Compliance%20Offerings.pdf) , amely felsorolja a globális, kormányzati, iparági és regionális megfelelőségi és értékelési folyamatok összes, az Azure-ra kiterjedő szolgáltatását, beleértve a IRAP/CCSL.

## <a name="azure-service-not-listed-or-assessed-at-a-lower-level-than-i-need"></a>Az Azure-szolgáltatás nem jelenik meg vagy nem becsülhető meg alacsonyabb szinten, mint amire szükségem van

Nem tanúsított vagy **hivatalos minősítésű szolgáltatások: A** bizalmas, de nem **védett**adatok a **védett** adatokat üzemeltető megoldás részeként vagy a szolgáltatásokban is használhatók:
1. nem titkosított **védett** adattárolók tárolása vagy feldolgozása, vagy
1. elvégezte a kockázatértékelést, és jóváhagyta a szolgáltatást, hogy saját maga tárolja a **védett** adatot.

Ha olyan szolgáltatást szeretne használni, amely nem szerepel a CCSL a **hivatalos** adatokat tároló és feldolgozó eszközön, de az ISM-nek szüksége van arra, hogy írásban értesítse a ASCs, mielőtt megkezdi vagy megújítja a szerződést egy felhőalapú szolgáltatóval.

Az Ügynökség által a **védett** munkaterhelésekhez használt szolgáltatásokat továbbra is biztonsági értékeléssel kell elbírálni és jóváhagyni, összhangban az ISM-ben és az Ügynökség által felügyelt IRAP felmérések folyamatában az [DTA biztonságos felhő stratégiájában](https://www.dta.gov.au/files/cloud-strategy/secure-cloud-strategy.pdf)ismertetett folyamatokkal.

![DTA biztonságos felhőalapú stratégia minősítési folyamata](media/certification.png)

A Microsoft folyamatosan felméri a szolgáltatásainkat annak biztosítására, hogy a platform biztonságos és a célnak megfelelő legyen az ausztráliai kormányzat általi használatra, ezért a Microsofttal való kapcsolatfelvételhez olyan szolgáltatásra van szükség, amely jelenleg nem a **védett**CCSL van.

Mivel a Microsoft számos olyan szolgáltatást biztosít a CCSL, amely a nem **besorolt DLM** és a **védett** besorolások esetében egyaránt engedélyezett, az ISM-nek szüksége van arra, hogy legalább két évente IRAP értékelje a szolgáltatásainkat. A Microsoft éves értékelést készít, amely egyúttal a további szolgáltatások megfontolására is lehetőséget nyújt.

## <a name="certified-protected-gateway-in-azure"></a>Hitelesített védett átjáró az Azure-ban

A Microsoft nem működtet kormányzati tanúsítvánnyal rendelkező biztonságos internet-átjárót (SIG), mert az átjáró konszolidációs programja által megengedett SIGs száma korlátozott.  A SIG várt és szükséges képességei azonban Microsoft Azureon belül konfigurálhatók.

Az Azure-szolgáltatások **védett** tanúsítványán keresztül a ASCs konkrét javaslatokat tartalmaz az ügynökségeknek az Azure-hoz való csatlakozáshoz, valamint a hálózati szegmentálás megvalósításához a biztonsági tartományok között, például a **védett** és a Internet. Ezek a javaslatok a hálózati biztonsági csoportok, tűzfalak és virtuális magánhálózatok használatát foglalják magukban.  A ASCS egy virtuális átjáró berendezés használatát javasolja. Az Azure-ban több virtuális készülék is rendelkezésre áll, amelyek fizikai egyenértékűek az ASD kiértékelt termékeinek listájával, vagy kiértékelésre kerültek a közös feltételekkel foglalkozó portálon. Ezeket a termékeket az ASD kölcsönösen elismeri az általános feltételek felismerési megállapodásának (CCRA) aláírójának.

A Microsoft útmutatást hozott létre az Azure-alapú képességek megvalósításához, amelyek biztosítják a különböző biztonsági tartományok közötti határ védelméhez szükséges biztonsági funkciókat, amelyek együttes használata esetén a Certified SIG egyenértékűnek kell lennie. Számos partner tud segítséget nyújtani ezeknek a képességeknek a kialakításához és megvalósításához, valamint számos olyan partneri megoldáshoz, amely ugyanezt teszi elérhetővé.

## <a name="security-clearances-and-citizenship-of-microsoft-support-personnel"></a>A Microsoft támogatási szakemberének biztonsági felhatalmazása és állampolgársága

A Microsoft a szolgáltatásokat globálisan, a betanított és képzett biztonsági személyzettel működteti.  A Sydney-ben és Melbourne-ben található létesítményekhez nem megfelelő fizikai hozzáféréssel rendelkező munkatársak az ausztrál kormány alapkonfigurációjának biztonsági engedélyekkel rendelkeznek. Az Ausztrália középső és ausztráliai középső régiójának tagjai legalább negatív, 1 (NV1) (a titkos adatvédelemhez megfelelő) (az adott esetben a **titkos** ) területekre érvényesek. Ez további garanciát nyújt azon ügyfelek számára, akik az Azure-t üzemeltető adatközpontok személyzete nagyon megbízható.

A Microsoft az igény szerinti hozzáférési szabályzattal rendelkezik, és hozzáférést biztosít a szerepköralapú hozzáférés-vezérlésen alapuló, csak a megfelelő felügyelettel rendelkező rendszerekhez. Az esetek túlnyomó többségében a rendszergazdáknak nincs szükségük az ügyféladatok elérésére, illetve a szolgáltatással kapcsolatos hibák megoldására.  A távoli végrehajtáshoz szükséges feladatok nagy fokú automatizálása és parancsfájlkezelése cáfolja az ügyféladatok közvetlen elérésének szükségességét.

A legfőbb Minisztérium megerősítette, hogy az Azure-ban a Microsoft személyzeti biztonsági szabályzatai és eljárásai összhangban vannak azzal, hogy a PSPF az INFOSEC-9-ben az információkhoz való hozzáféréshez való hozzáférést biztosít.

## <a name="storing-international-traffic-of-arms-regulations-itar-or-export-administration-regulations-ear-data"></a>A Arms-szabályozások (ITAR-EK) nemzetközi forgalmának tárolása vagy az exportálási szabályok (EAR)

Az Azure-beli technikai vezérlők, amelyek segítik az ügyfeleknek az export-vezérelt adatkezelésre vonatkozó kötelezettségeiknek való megfelelést, globálisak az Azure-ban. Fontos megjegyezni, hogy nincs "ketyeg a ITAR/fül Box", mivel nincs formális értékelési és minősítési keretrendszer az export-vezérelt adattároláshoz.

A Azure Government és az Office 365 Amerikai Egyesült Államok védelmi Minisztériuma további szerződéses és feldolgozási intézkedéseket helyezett el, hogy támogassa az export-ellenőrzés alá eső ügyfeleket. A további szerződéses záradékok és az Azure-régiók garantált országos támogatása és felügyelete nem áll rendelkezésre Ausztráliában.

Ez nem jelenti azt, hogy Ausztráliában nem használható az Azure az ITAR/EAR számára, de világosan meg kell ismernie az exportálási licenccel kapcsolatos korlátozásokat, és további védelmi eszközöket kell megvalósítani, mielőtt az Azure-t tárolni kellene Ezek az adathalmazok. Előfordulhat például Azure Information Protection, hogy a Azure Active Directory-ként kell központilag létrehoznia az állampolgárságot, és a titkosítási szabályokat az adaton keresztül kell kikényszeríteni, és csak az Egyesült Államokban, illetve más nemzetiségű országokra korlátozza az exportálási licencet, Az Azure-ban való tárolás előtt titkosítsa az összes helyszíni adatfeldolgozást, vagy használja az ITAR adatait, és a lista továbbra is elérhető...

Mivel a ITAR nem formális minősítés, meg kell ismernie, hogy milyen korlátozások és korlátozások vonatkoznak az exportálási licencre, és hogy az Azure-ban elegendőan vannak-e a követelmények teljesítéséhez szükséges vezérlők. Ebben az esetben az egyik probléma, amely szerint a mérnökök hozzáférhetnek az exportálási licencen nem engedélyezett állampolgársághoz.

## <a name="next-steps"></a>További lépések

Tekintse át az Azure- [VPN Gateway](vpn-gateway.md) az Azure Australia-hez készült VPN-kapcsolatok ISM-kompatibilis konfigurációját és megvalósítását ismertető cikket.
